```
import { asyncValidate, ISchemaGeneral, ValidationError } from '../dist/src';

const passwordSchema: ISchemaGeneral = {
  type: 'string',
  validation: {
    isRequired: true,
    validate: (data: string, pathName?: string) => {
      if (data.length < 6 && data.length > 32) {
        return new ValidationError(
          pathName || '',
          `"${pathName}" are not correct password length. It should be in rang [6-32]`
        );
      }
    },
  },
};

const passwordResult = await asyncValidate(passwordSchema, 'abc', 'password');
console.log(passwordResult); // ==>Error: `"password" are not correct password length. It should be in rang [6-32]`
const bodySchema: ISchemaGeneral = {
  type: 'object',
  properties: {
    password: passwordSchema,
  },
};
const bodyResult = await asyncValidate(bodySchema, { password: 'abc' }, 'body');
console.log(bodyResult); // ==>Error: `"body.password" are not correct password length. It should be in rang [6-32]`
```
