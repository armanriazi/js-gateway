


```javascript



import { randomUUID } from "crypto";
import { faker } from "@faker-js/faker";
import usersService from "../../src/modules/users/users.service.js";

const create = async () => {
  for (let index = 0; index < 1000; index++) {
    const user = {
      id: randomUUID(),
      username: faker.internet.userName(),
      password: "$2a$12$kvR8XOiI7b6Vxi96PJz4.O0VAbPJxNAFA066X2UfEVGUa2M6h/YEG",
      role: "admin",
      profile: JSON.parse(faker.datatype.json()),
      created_at: new Date().toISOString(),
      updated_at: new Date().toISOString(),
    };
    await usersService.create(user);
  }
  process.exit();
};

create();
```