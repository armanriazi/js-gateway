```javascript
import { randomUUID } from "crypto";
import { faker } from "@faker-js/faker";
import apiKeysService from "../../src/modules/apiKeys/apiKeys.service.js";
const create = async () => {
  for (let index = 0; index < 1000; index++) {
    const apiKey = {
      id: randomUUID(),
      value: faker.random.alphaNumeric(64),
      UserId: "1130595e-1c6c-4855-a3a6-263a4b31e311",
      isValid: true,
      maxCount: 11,
      expireDate: "2023-11-08 12:12:44",
      created_at: new Date().toISOString(),
      updated_at: new Date().toISOString(),
    };
    await apiKeysService.create(apiKey);
  }
  process.exit();
};

create();
```