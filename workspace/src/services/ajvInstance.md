
```javascript
import Ajv from "ajv";
import ajvErrors from "ajv-errors";

const ajvInstance = new Ajv({ allErrors: true }); // options can be passed, e.g. {allErrors: true}
ajvErrors(ajvInstance);

export default ajvInstance;
```