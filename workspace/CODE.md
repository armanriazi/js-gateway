# Codes

* [Download Zip File of Codes](./code)

In the code documentation section, there are two methods available: one can access the embedded code through the following list and insert documentation, which in this case means the documentation is created outside of the code project, i.e., it is done in a separate project.
The other recommended method is to execute the command below. By running this command, the `jsdocs` library will create an output or a folder in the project workspace. This documentation is written by the comment-codes present in the main code, and jsdocs utilizes those comments.
Therefore, currently, there are two templates prepared for documentation, which can be used based on the need or change in documentation strategy, or they can contribute to further completing this section.

```bash
npm run docs
```

## Code Index

* [HOME](../SUMMARY.md)
* [Database](./db/CODE_DB.md)
* [ORM](./db.md)
  * [Drop](./db/drop.md)
  * [Init](./db/init.md)
  * [SeederData](./db/seederData/seederData.md)
    * [user](./db/seederData/user.md)
    * [serviceIds](./db/seederData/serviceIds.md)
  * [Migrations](./db/migrations/migrations.md)
    * [addFieldToServices](./test/migrations/20231016130445-addFieldToServices.md)
    * [apiKey](./src/migrations/20221016074453-create-apiKey.md)
    * [create-service](./src/migrations/20221016101259-create-service.md)
    * [create-apis](./src/migrations/20221016101637-create-apis.md)
    * [create-user](./src/migrations/20220729150928-create-user.md)
    * [create-gatwayLog](./src/migrations/20221016103549-create-gatwayLog.md)
    * [create-project](./src/migrations/20221016082149-create-project.md)
  * [Seeders](./db/seeders/seeders.md)
    * [apiKey](./db/seeders/20230103133848-apiKey.md)
    * [service](./db/seeders/20221119084109-service.md)
    * [apiKey](./db/seeders/20230103133848-apiKey.md)
    * [project](./db/seeders/20221119083421-project.md)
    * [api](./db/seeders/20230103143015-api.md)
    * [project](./db/seeders/20221119083421-project.md)
    * [user](./db/seeders/20220824103847-user.md)
    * [api](./db/seeders/20230103143015-api.md)
  * [Factory](./db/factory/factory.md)
    * [projectFactory](./db/factory/projectFactory.md)
    * [apiKeyFactory](./db/factory/apiKeyFactory.md)
    * [apiFactory](./db/factory/apiFactory.md)
    * [serviceFactory](./db/factory/serviceFactory.md)
    * [apiLogFactory](./db/factory/apiLogFactory.md)
    * [userFactory](./db/factory/userFactory.md)
* [SourceCode](./src/CODE_SRC.md)
  * [Index](./src/index.md)
  * [App](./src/app.md)
  * [Config](./src/config.md)
  * [database](./src/config/database.md)
  * [redis](./src/config/redis.md)
  * [Services](./src/services.md)
  * [controllerBase](./src/services/controllerBase.md)
  * [multerUploader](./src/services/multerUploader.md)
  * [backendLogger](./src/services/backendLogger.md)
  * [broker](./src/services/broker.md)
  * [ajvInstance](./src/services/ajvInstance.md)
  * [busboyUploader](./src/services/busboyUploader.md)
  * [utils](./src/services/utils.md)
  * [auth](./src/services/auth.md)
  * [logger](./src/services/logger.md)
  * [serviceBase](./src/services/serviceBase.md)
  * [inputValidation](./src/services/inputValidation.md)
  * [GRPC](./src/grpc.md)
  * [grpcHandler](./src/grpc/grpcHandler.md)
  * [Websocket](./src/websocket.md)
  * [websocketHandler](./src/websocket/websocketHandler.md)
  * [Modules](./src/modules.md)
  * [models](./src/modules/models.md)
  * [Modules_index](./src/modules/index.md)
  * [Apis](./src/modules/apis.md)
    * [model](./src/modules/apis/apis.model.md)
    * [route](./src/modules/apis/apis.route.md)
    * [service](./src/modules/apis/apis.service.md)
    * [controller](./src/modules/apis/apis.controller.md)
    * [validator](./src/modules/apis/apis.validator.md)
  * [AiServices](./src/modules/aiServices.md)
    * [controller](./src/modules/aiServices/aiServices.controller.md)
    * [route](./src/modules/aiServices/aiServices.route.md)
    * [service](./src/modules/aiServices/aiServices.service.md)
  * [Projects](./src/modules/projects.md)
    * [service](./src/modules/projects/projects.service.md)
    * [model](./src/modules/projects/projects.model.md)
    * [route](./src/modules/projects/projects.route.md)
    * [validator](./src/modules/projects/projects.validator.md)
    * [controller](./src/modules/projects/projects.controller.md)
  * [Users](./src/modules/users.md)
    * [route](./src/modules/users/users.route.md)
    * [service](./src/modules/users/users.service.md)
    * [validator](./src/modules/users/users.validator.md)
    * [model](./src/modules/users/users.model.md)
    * [controller](./src/modules/users/users.controller.md)
  * [ApiKeys](./src/modules/apiKeys.md)
    * [model](./src/modules/apiKeys/apiKeys.model.md)
    * [service](./src/modules/apiKeys/apiKeys.service.md)
    * [validator](./src/modules/apiKeys/apiKeys.validator.md)
    * [route](./src/modules/apiKeys/apiKeys.route.md)
    * [controller](./src/modules/apiKeys/apiKeys.controller.md)
  * [GatewayLogs](./src/modules/gatewayLogs.md)
    * [validator](./src/modules/gatewayLogs/gatewayLogs.validator.md)
    * [model](./src/modules/gatewayLogs/gatewayLogs.model.md)
    * [service](./src/modules/gatewayLogs/gatewayLogs.service.md)
    * [route](./src/modules/gatewayLogs/gatewayLogs.route.md)
    * [controller](./src/modules/gatewayLogs/gatewayLogs.controller.md)
  * [Services](./src/modules/services.md)
    * [route](./src/modules/services/services.route.md)
    * [controller](./src/modules/services/services.controller.md)
    * [service](./src/modules/services/services.service.md)
    * [model](./src/modules/services/services.model.md)
    * [validator](./src/modules/services/services.validator.md)
  * [Common](./src/common.md)
  * [ENV](./src/common/env.md)
  * [messages](./src/common/messages.md)
  * [responseMessage](./src/common/responseMessage.md)
  * [Helper](./src/common/helper.md)
  * [bcrypt](./src/common/helper/bcrypt.md)
  * [require](./src/common/helper/require.md)
  * [winston](./src/common/helper/winston.md)
  * [axios](./src/common/helper/axios.md)
  * [utility](./src/common/helper/utility.md)
  * [rateLimiter](./src/common/helper/rateLimiter.md)
  * [jwt](./src/common/helper/jwt.md)
  * [Middlewares](./src/common/middlewares.md)
  * [generalValidator](./src/common/middlewares/generalValidator.md)
  * [public](./src/common/middlewares/public.md)
  * [storeLog](./src/common/middlewares/storeLog.md)
  * [errorHandler](./src/common/middlewares/errorHandler.md)
  * [policy](./src/common/middlewares/policy.md)
  * [paginationHandler](./src/common/middlewares/paginationHandler.md)
  * [ajvValidator](./src/common/middlewares/ajvValidator.md)
* [Tests](./test/CODE_TEST.md)
  * [Tests_Index](./test/testindex.test.md)
  * [mock](./test/mock.py.md)
  * [Unit Test](./test/unit_test.md)
  * [Helper](./test/unit_test/helper.md)
    * [Bcrypt](./test/unit_test/helper/bcrypt.test.md)
    * [Axios](./test/unit_test/helper/axios.test.md)
    * [jwt](./test/unit_test/helper/jwt.test.md)
  * [Services](./test/unit_test/services.md)
    * [utils](./test/unit_test/services/utils.test.md)
    * [busboy](./test/unit_test/services/busboy.test.md)
    * [broker](./test/unit_test/services/broker.test.md)
    * [auht](./test/unit_test/services/auth.test.md)
  * [Middleware](./test/unit_test/middleware.md)
    * [paginationHandler](./test/unit_test/middleware/paginationHandler.test.md)
    * [ajvValidator](./test/unit_test/middleware/ajvValidator.test.md)
    * [storeLog](./test/unit_test/middleware/storeLog.test.md)
    * [policy](./test/unit_test/middleware/policy.test.md)
  * [Integration_test](./test/integration_test.md)
    * [Apis](./test/integration_test/apis.md)
    * [getApis](./test/integration_test/apis/getApis.test.md)
    * [deleteApis](./test/integration_test/apis/deleteApis.test.md)
    * [createApis](./test/integration_test/apis/createApis.test.md)
    * [updateApia](./test/integration_test/apis/updateApis.test.md)
    * [indexApi](./test/integration_test/apis/indexApi.test.md)
    * [Projects](./test/integration_test/projects.md)
    * [getProject](./test/integration_test/projects/getProject.test.md)
    * [indexProject](./test/integration_test/projects/indexProject.test.md)
    * [updateProject](./test/integration_test/projects/updateProject.test.md)
    * [deleteProject](./test/integration_test/projects/deleteProject.test.md)
    * [createProject](./test/integration_test/projects/createProject.test.md)
    * [Users](./test/integration_test/users.md)
    * [getUser](./test/integration_test/users/getUser.test.md)
    * [updateUser](./test/integration_test/users/updateUser.test.md)
    * [indexUser](./test/integration_test/users/indexUser.test.md)
    * [updateUserPass](./test/integration_test/users/updateUserPass.test.md)
    * [createUser](./test/integration_test/users/createUser.test.md)
    * [login](./test/integration_test/users/login.test.md)
    * [deleteUser](./test/integration_test/users/deleteUser.test.md)
    * [ApiKeys](./test/integration_test/apiKeys.md)
    * [getApiKey](./test/integration_test/apiKeys/getApiKey.test.md)
    * [deleteApiKey](./test/integration_test/apiKeys/deleteApiKey.test.md)
    * [updateApiKey](./test/integration_test/apiKeys/updateApiKey.test.md)
    * [indexApiKey](./test/integration_test/apiKeys/indexApiKey.test.md)
    * [createApiKey](./test/integration_test/apiKeys/createApiKey.test.md)
    * [Services](./test/integration_test/services.md)
    * [createService](./test/integration_test/services/createService.test.md)
    * [serviceIndex](./test/integration_test/services/serviceIndex.test.md)
    * [updateService](./test/integration_test/services/updateService.test.md)
    * [deleteService](./test/integration_test/services/deleteService.test.md)
    * [getService](./test/integration_test/services/getService.test.md)
    * [Data](./test/integration_test/data.md)
    * [user](./test/integration_test/data/user.md)
    * [apikey](./test/integration_test/data/apikey.md)
    * [service](./test/integration_test/data/service.md)
    * [project](./test/integration_test/data/project.md)
    * [api](./test/integration_test/data/api.md)
    * [RequiredTest](./test/integration_test/requiredTest.md)
    * [apiService](./test/integration_test/requiredTest/apiService.md)
    * [userService](./test/integration_test/requiredTest/userService.md)
    * [servicesService](./test/integration_test/requiredTest/servicesService.md)
    * [projectService](./test/integration_test/requiredTest/projectService.md)
    * [apiKeyService](./test/integration_test/requiredTest/apiKeyService.md)
  * [mockAI](./test/mockAI.md)
    * [mockCardRecognition](./test/mockAI/mockCardRecognition.md)
    * [mockAIPos](./test/mockAI/mockAIPos.md)
    * [mockAiFaceCartoonize](./test/mockAI/mockAiFaceCartoonize.md)
    * [mockAINationalCard](./test/mockAI/mockAINationalCard.md)
    * [mockAISummarization](./test/mockAI/mockAISummarization.md)
    * [mockAIObject](./test/mockAI/mockAIObject.md)
    * [mockAIUsernameConverter](./test/mockAI/mockAIUsernameConverter.md)
    * [mockAIVehicleIDRecog](./test/mockAI/mockAIVehicleIDRecog.md)
    * [mockSummarizationHC](./test/mockAI/mockSummarizationHC.md)
    * [mockAiSpeechRecognition](./test/mockAI/mockAiSpeechRecognition.md)
    * [mockAINationalCardHC](./test/mockAI/mockAINationalCardHC.md)
    * [mockAiFaceDetection](./test/mockAI/mockAiFaceDetection.md)
    * [mockAiFaceVerification](./test/mockAI/mockAiFaceVerification.md)
* [GLOSSARY](../GLOSSARY.md)
