# 

# Credit Card Microservices Demo Project Guide

## System Requirements

1.  **Docker Desktop**: Required to run Docker containers.

2.  **Postman**: For testing the APIs by importing and running
    collections.

## Steps to Run the Project

1.  **Download the Project:**

-   Clone the project from GitHub using this link:

> [**https://github.com/yoramnag/credit-card-microservices-demo.git**](https://github.com/yoramnag/credit-card-microservices-demo.git)

2.  **Set Up Docker:**

-   Open **Docker Desktop** and ensure the Docker engine is running.

3.  **Navigate to Project Folder:**

-   Open **PowerShell** or **IntelliJ IDEA** and navigate to the folder
    where the docker-compose.yml file is located.

-   You can use the following PowerShell command to change the
    directory:

> cd path\\to\\your\\project

4.  **Start Docker Containers:**

-   In the project folder, run the following command to start the Docker
    containers:

> docker compose up -d

-   After the command executes successfully, go to **Docker Desktop**
    and verify that the necessary containers are running.

![](media/image1.png){width="7.879166666666666in"
height="4.247916666666667in"}

## Testing the APIs with Postman

1.  **Import the Postman Collection:**

-   Open **Postman**.

-   Go to **File \> Import**, then select the credit card microservices
    demo.postman_collection.json file from your project directory. This
    file contains all the API requests required to interact with your
    services.

2.  **Run the Save Transaction Request:**

-   In Postman, locate the request named
    **transactions-service-saveTransaction-NoAuth** from the imported
    collection.

-   ![](media/image2.png){width="7.652777777777778in"
    height="5.003472222222222in"}Run the request. You will receive a
    **401 Unauthorized** response. This is expected.

3.  **Run the Request with Authorization:**

-   Now, try to run the request named
    **transactions-service-saveTransaction-cc**. This request includes
    **OAuth 2.0 authorization**.

-   ![](media/image3.png){width="7.944444444444445in"
    height="5.204861111111111in"}You will again receive a **401
    Unauthorized** response because the client-id
    creditCard-callcenter-cc doesn't exist in Keycloak.

## Set Up Client in Keycloak

1.  **Access Keycloak Admin Console:**

Go to: <http://localhost:7080/admin/master/console/#/master/clients>

Login with the following credentials:

> **Username**: admin
>
> ![](media/image4.png){width="7.902083333333334in"
> height="4.076388888888889in"}**Password**: admin

2.  **Create a New Client:**

-   In the **left sidebar**, select **Clients** and then click on
    **Create Client**.

-   Enter creditCard-callcenter-cc as the **Client ID** and click
    **Next**.

-   Enable **Client Authentication** and check only **Service Accounts
    Roles**.

-   ![](media/image5.png){width="7.8902777777777775in"
    height="4.083333333333333in"}**Click Next.**

![](media/image6.png){width="7.719444444444444in"
height="3.9930555555555554in"}

![](media/image7.png){width="8.03888888888889in"
height="4.166666666666667in"}

3.  **Generate Client Credentials:**

-   Click on **Credentials**.

-   ![](media/image8.png){width="7.873611111111111in"
    height="4.076388888888889in"}**Copy the Client Secret** and save it.

![](media/image9.png){width="7.899305555555555in"
height="4.083333333333333in"}

4.  **Update Postman with the Client Secret:**

-   Go back to Postman.

-   ![](media/image10.png){width="7.86875in"
    height="5.173611111111111in"}In the
    **transactions-service-saveTransaction-cc** request, update the
    **Authorization** header with the **client secret** you just copied.

## Obtain Access Token in Postman

1.  **Get New Access Token:**

-   In Postman, click **Get New Access Token**.

-   Once the token is generated, click **Use Token**.

2.  **Run the Request Again:**

-   Try running the transactions-service-saveTransaction-cc request
    again. You should receive a **403 Forbidden** response.

![](media/image11.png){width="7.770833333333333in"
height="5.1090277777777775in"}

![](media/image12.png){width="7.520138888888889in"
height="4.458333333333333in"}![](media/image13.png){width="7.598611111111111in"
height="4.868055555555555in"}

## Assign Roles to Client in Keycloak

1.  **Create New Roles in Keycloak:**

-   In Keycloak, click **Realm Roles** in the left sidebar.

-   Click **Create Role** and create the following roles (uppercase
    required):

> TRANSACTIONS
>
> BLACK-LIST![](media/image14.png){width="7.930555555555555in"
> height="4.105555555555555in"}

![](media/image15.png){width="7.635416666666667in"
height="3.951388888888889in"}

![](media/image16.png){width="7.809027777777778in"
height="4.034722222222222in"}

![](media/image17.png){width="7.820833333333334in"
height="4.069444444444445in"}

> 2\. **Assign Roles to the Client:**

-   In the left sidebar, click **Clients**, and select the
    creditCard-callcenter-cc client.

-   Go to **Service Account Roles** and click **Assign Role**.

-   ![](media/image18.png){width="7.920138888888889in"
    height="4.111111111111111in"}Assign both the **TRANSACTIONS** and
    **BLACK-LIST** roles.

![](media/image19.png){width="7.983333333333333in"
height="4.125in"}![](media/image20.png){width="7.847222222222222in"
height="4.047916666666667in"}

![](media/image21.png){width="8.029166666666667in"
height="4.159722222222222in"}

3.  **Reattempt to Get Access Token:**

-   In Postman, click on **Get New Access Token**.

-   ![](media/image11.png){width="7.722222222222222in"
    height="5.077083333333333in"}Click **Use Token**.

![](media/image12.png){width="7.701388888888889in"
height="4.565277777777778in"}

4.  **Run the Request Again:**

-   ![](media/image22.png){width="7.7652777777777775in"
    height="4.951388888888889in"}Now, you should receive a **201
    Created** response, indicating that the transaction was successfully
    saved.

## Grafana

1.  **Open the Application:**

-   Open your browser and go to: <http://localhost:3000/>

-   ![](media/image23.png){width="7.9527777777777775in"
    height="4.298611111111111in"}Once the page loads, navigate to the
    **Explore** section and select **Loki** to explore the logs
    generated by the services running in the Docker containers.

2.  **Set up Tempo**

<!-- -->

1.  **Access Logs:**

-   Open one of the logs generated by the application.

-   Copy the **traceID** from the log entry.

2.  **Search in Tempo:**

-   Navigate to **Tempo**

-   In Tempo\'s search bar, **paste the traceID** you copied. This will
    allow you to trace the request across various microservices
    involved.

3.  **View Trace Details:**

-   ![](media/image24.png){width="8.04861111111111in"
    height="4.338888888888889in"}Once the traceID is entered, Tempo will
    display a detailed trace showing the microservices that participated
    in the request, allowing you to track its flow across the system.

![](media/image25.png){width="7.836805555555555in"
height="4.229166666666667in"}

4.  **Access Tempo from Loki:**

-   Tempo can also be accessed directly from **Loki**.

-   While viewing a log in Loki, look for the **Tempo button** (usually
    located next to the log entry).

-   ![](media/image26.png){width="7.882638888888889in"
    height="4.25in"}Click on the **Tempo button** to automatically open
    the trace associated with that request and view the trace details.

> ![](media/image27.png){width="7.896527777777778in" height="4.25in"}

5.  Set Up Prometheus Dashboard:

-   In Grafana, go to **Dashboards** and click **New \> Import**.

-   Use the following link to import the **JVM Micrometer** dashboard:

> <https://grafana.com/grafana/dashboards/4701-jvm-micrometer/>

-   Select **Prometheus** as the data source.

![](media/image28.png){width="7.334027777777778in"
height="3.7916666666666665in"}

![](media/image29.png){width="7.597916666666666in"
height="3.6527777777777777in"}

![](media/image30.png){width="7.611111111111111in"
height="3.7354166666666666in"}

![](media/image31.png){width="7.877083333333333in"
height="4.079861111111111in"}
