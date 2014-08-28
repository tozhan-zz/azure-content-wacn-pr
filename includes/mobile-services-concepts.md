## <a name="what-is"></a>What is Mobile Services

Mobile Services is an Azure service offering designed to make it easy to create highly-functional mobile apps using Azure. Mobile Services brings together a set of Azure services that enable backend capabilities for your apps. Mobile Services provides the following backend capabilities in Azure to support your apps: 

+ Simple provisioning and management of tables for storing app data. 
+ Integration with notification services to deliver push notifications to your app.
+ Integration with well-known identity providers for authentication.
+ Granular control for authorizing access to tables.
+ Custom business logic on the server.
+ Integration with other cloud services.
+ Supports the ability to scale a mobile service instance.
+ Service monitoring and logging.

Mobile Services provides a client library for each supported platform, which makes it even easier to develop apps on your platform that consume Mobile Services.

## <a name="concepts"> </a>Concepts

The following are important functionalities and concepts in the Mobile Services:

<!--![1][1]-->

+ **Application key:** A unique value that is generated by Mobile Services, distributed with your app, and presented in client-generated requests. While useful for limiting access to your mobile service from random clients, this key is not secure and should not be used to authenticate users of your app.    

+ **Authentication token:** The acccess token that is generated by Mobile Services after a user is authenticated.

+ **Dynamic schema:** The ability of Mobile Services to automatically add a column to a table based on the fields of the JSON object sent in an insert or update request. Dynamic schema should be disabled when your app goes into production. 

+ **Identity provider:** External service, such as Facebook, Twitter, Google, or Microsoft Account, that is used to authenticate users of your mobile service.

+ **Permission:** The minimum authentication level needed to invoke a table operation.  

+ **Push notification:** Service-initiated message that is sent to a registered device or user.

+ **Scale:** The ability to add, for an additional cost, more processing power, performance, and storage as your app becomes more popular.

+ **Scheduled job:** Server script code that is run either on a pre-determined schedule or on-demand.

+ **Server script:** JavaScript code for custom business logic that is stored on the server. This code is registered to a table operation (read, insert, update, delete) or a scheduled job. 

+ **Table:** User data is stored in tables in a SQL Database. You can create these tables in the Management Portal.

+ **Table operation:** An HTTP request received from the client to read, insert, update, or delete table data.


<!-- Images. -->


  