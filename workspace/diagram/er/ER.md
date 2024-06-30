![image](../../assets/uploads/c1e00097a61988649bcb613e256c6ba8/image.png)


**Users:**

*Description:* Represents the users of system. such as: sgw

*Relationships:*
- One user can have multiple apikeys.


**ApiKeys:**

*Description:* Represents the API keys generated for users.

*Relationships:*

- Belongs to a user (user_id).
- Can be assigned to a service (service_id) in apis table.


**Projects:**

*Description*: Represents the projects in the system.


*Relationships:*
- Contains multiple services.


**Services:**

*Description:* Represents the services within projects.


*Relationships:*
- Belongs to a project (project_id).
- Can be assigned an API key(apikeyId) in apis table.



**APIs:**

*Description:* Represents the APIs within services.

*Relationships:*
- Belongs to a service (service_id) and an apikey.



**gatewayLogs:**

*Description:* Represents the logs of API responses.
