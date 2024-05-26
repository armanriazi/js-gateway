
```javascript



import { randomUUID } from "crypto";
import { faker } from "@faker-js/faker";
import apisService from "../../src/modules/apis/apis.service.js";
import gatewayLogsService from "../../src/modules/gatewayLogs/gatewayLogs.service.js";

const create = async () => {
  const apis = await apisService.find({ where: { TPM: 11 } });
  const number = apis.length;
  for (let mainIndex = 0; mainIndex < 1000000; mainIndex++) {
    const apiRandom = [];
    for (let index = 0; index < 100; index++) {
      apiRandom.push(Math.floor(Math.random() * number));
    }
    for (let indexS = 0; indexS < apiRandom.length; indexS++) {
      const indexApi = Math.floor(Math.random() * 100);
      for (
        let addIndex = 0;
        addIndex < Math.floor(Math.random() * 11 + 1);
        addIndex++
      ) {
        const gatewayLog = {
          id: randomUUID(),
          ApiId: apis[apiRandom[indexApi]].id,
          data: [
            JSON.parse(faker.datatype.json()),
            JSON.parse(faker.datatype.json()),
          ],
          created_at: new Date().toISOString(),
        };
        await gatewayLogsService.create(gatewayLog);
      }
    }
  }
};

create();
```