
```javascript



"use strict";
import { randomUUID } from "crypto";
import { faker } from "@faker-js/faker";
import servicesService from "../../src/modules/services/services.service.js";

const create = async () => {
  for (let index = 0; index < 1000; index++) {
    const service = {
      id: randomUUID(),
      ProjectId: "99326aab-eb37-4152-b549-f5558f69ff29",
      name: faker.random.alpha(12),
      validation: {},
      maxCount: 10,
      queue: faker.random.word(),
      apis: {},
      created_at: new Date().toISOString(),
      updated_at: new Date().toISOString(),
    };
    await servicesService.create(service);
  }
};

create();
```