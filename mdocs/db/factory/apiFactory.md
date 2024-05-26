


```javascript



import { randomUUID } from "crypto";
import apisService from "../../src/modules/apis/apis.service.js";
import servicesService from "../../src/modules/services/services.service.js";
import apiKeysService from "../../src/modules/apiKeys/apiKeys.service.js";

const create = async () => {
  const services = await servicesService.find({ where: { apis: {} } });
  const apiKeys = await apiKeysService.find({ where: { isValid: true } });
  for (let indexS = 0; indexS < services.length; indexS++) {
    for (let indexA = 0; indexA < apiKeys.length; indexA++) {
      const api = {
        id: randomUUID(),
        ApiKeyId: apiKeys[indexS].id,
        ServiceId: services[indexA].id,
        api: {},
        TPM: 11,
        maxCount: 100,
        policies: {},
        created_at: new Date().toISOString(),
        updated_at: new Date().toISOString(),
      };

      await apisService.create(api);
    }
  }
};

create();
```