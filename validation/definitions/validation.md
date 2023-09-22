# 1.Validation Error

## 1.1.IValidationError

IValidationError => [IBakkuHttpError](./../../common/definition/error.md)

## 1.2.ValidationError

`implement of IValidationError`

```
export class ValidationError extends BakkuHttpError {
  constructor(dataName: string, message: string, code?: string) {
    super(message, 400);
    this.code = code || ValiDationCode.common;
    this.data = { params: dataName };
  }
}
```

## 1.3. Validated codes

`Code use in error which is returned from validation`

```
{
  common: 'validation_error',
  StringSchema: {
    string_schema_required: 'string_schema_required',
    string_schema_not_string: 'string_schema_not_string',
    string_schema_length_invalid: 'string_schema_length_invalid',
    string_schema_byte_length_invalid: 'string_schema_byte_length_invalid',
    string_schema_email_format_invalid: 'string_schema_email_format_invalid',
    string_schema_format_invalid: 'string_schema_format_invalid',
  },
  NumberSchema: {
    number_schema_required: 'number_schema_required',
    number_schema_not_number: 'number_schema_not_number',
    number_schema_range_invalid: 'number_schema_range_invalid',
  },
  IntegerSchema: {
    integer_schema_required: 'integer_schema_required',
    integer_schema_not_number: 'integer_schema_not_number',
    integer_schema_not_integer: 'integer_schema_not_integer',
    integer_schema_range_invalid: 'integer_schema_range_invalid',
  },
  FileSchema: {
    file_schema_required: 'file_schema_required',
    file_schema_size_invalid: 'file_schema_size_invalid',
    file_schema_type_invalid: 'file_schema_type_invalid',
  },
  DateSchema: {
    date_schema_required: 'date_schema_required',
    date_schema_not_date: 'date_schema_not_date',
    date_schema_range_invalid: 'date_schema_range_invalid',
  },
  BooleanSchema: {
    boolean_schema_required: 'boolean_schema_required',
    boolean_schema_not_boolean: 'boolean_schema_not_boolean',
  },
  EnumSchema: {
    enum_schema_required: 'enum_schema_required',
    enum_schema_not_enum_value: 'enum_schema_not_enum_value',
  },
  ArraySchema: {
    array_schema_required: 'array_schema_required',
    array_schema_not_array: 'array_schema_not_array',
    array_schema_length_invalid: 'array_schema_length_invalid',
  },
  ObjectSchema: {
    object_schema_required: 'object_schema_required',
    object_schema_not_object: 'object_schema_not_object',
    object_schema_key_not_allow_invalid: 'object_schema_key_not_allow_invalid',
  },
}
```

# 2. Validation interface

## 2.1.IValidationCore

`Interface use to validate any variable`

```
export interface IValidationCore<T = any> {
  isRequired?: boolean;
  validate?: (data?: T, pathName?: string) => (IValidationError | undefined) | Promise<IValidationError | undefined>;
  customError?: (error: IValidationError) => ValidationError | Promise<ValidationError>;
}
```

Properties:

| name        | Type     | Required | Description                                                                                                                  |
| :---------- | :------- | :------- | :--------------------------------------------------------------------------------------------------------------------------- |
| isRequired  | boolean  |          | the object validate is required or not                                                                                       |
| validate    | Function |          | function execute the validation base on business of variable, if failed return IValidationError or Promise<IValidationError> |
| customError | Function |          | custom the error when validate failed, use to convert the error from default validation                                      |

EX:

```
const validator: IValidationCore = {
  isRequire: true,
  validate?: (data: any) => {
    if (data.person) {
      return new ValidationError('data', 'data error', 'error_code_data_failed')
    }
  }
}
```

## 2.2.IValidationBoolean

`Interface use to validate the boolean variable`
IValidationBoolean = IValidationCore

## 2.3.IValidationString

`Interface use to validate the string variable`

```
export interface IValidationString extends IValidationCore<string> {
  minLength?: number;
  maxLength?: number;
  format?: SupportedStringFormat;
  byteLength?: {
    encode?: BufferEncoding;
    min?: number;
    max?: number;
  };
}
```

Properties extend IValidationCore's properties and:

| name       | Type                | Required | Description                                                                                                        |
| :--------- | :------------------ | :------- | :----------------------------------------------------------------------------------------------------------------- |
| minLength  | number              |          | min length of string                                                                                               |
| maxLength  | number              |          | max length of string                                                                                               |
| format     | 'isEmail' or RegExp |          | format of string, can be check it is email or any regular expression                                               |
| byteLength | Object              |          | validate the string in byte size (min, max, encode in byte data), can be use to validate Chinese before save to DB |

EX:

```
const validator: IValidationString = {
  isRequire: true,
  minLength: 8,
  validate?: (data: string) => {
    if (data ==='special_string') {
      return new ValidationError('data', 'data error special string', 'error_code_data_special')
    }
  }
}
```

```
const validatorChinese: IValidationString = {
  isRequire: true,
  minLength: 6,
  byteLength: {
    max: 256,
    format: 'UTF8'
  },
  validate?: (data: string) => {
    if (data ==='special_string') {
      return new ValidationError('data', 'data error special string', 'error_code_data_special')
    }
  }
}
```

- encodes which is used in byteLength:
  ```
  | 'ascii'
  | 'utf8'
  | 'utf-8'
  | 'utf16le'
  | 'ucs2'
  | 'ucs-2'
  | 'base64'
  | 'latin1'
  | 'binary'
  | 'hex';
  ```

## 2.4.IValidationNumber

`Interface use to validate the number variable`

```
export interface IValidationNumber extends IValidationCore<number> {
  min?: number;
  max?: number;
}
```

Properties extend IValidationCore's properties and:

| name | Type   | Required | Description          |
| :--- | :----- | :------- | :------------------- |
| min  | number |          | min length of number |
| max  | number |          | max length of number |

## 2.4.IValidationInteger

`Interface use to validate the integer variable`

IValidationInteger = IValidationNumber

## 2.5.IValidationFile

`Interface use to validate the file variable, base on multer.File`

```
export interface IValidationFile extends IValidationCore<Express.Multer.File | any> {
  mimetype?: string;
  minSize?: number;
  maxSize?: number;
}
```

Properties extend IValidationCore's properties and:

| name     | Type   | Required | Description      |
| :------- | :----- | :------- | :--------------- |
| minSize  | number |          | min size of file |
| maxSize  | number |          | max size of file |
| mimetype | string |          | type of file     |

## 2.6.IValidationDate

`Interface use to validate the date variable`

```
export interface IValidationDate extends IValidationCore<Date> {
  min?: Date | Moment;
  max?: Date | Moment;
}
```

Properties extend IValidationCore's properties and:

| name | Type | Required | Description |
| :--- | :--- | :------- | :---------- |
| min  | Date |          | min date    |
| max  | Date |          | max date    |

## 2.7.IValidationEnum

`Interface use to validate the enum variable`
IValidationEnum = IValidationCore

## 2.8.IValidationArray

`Interface use to validate the array variable `

```
export interface IValidationArray<I> extends IValidationCore<I[]> {
  minLength?: number;
  maxLength?: number;
}
```

Properties extend IValidationCore's properties and:

| name      | Type   | Required | Description         |
| :-------- | :----- | :------- | :------------------ |
| minLength | number |          | min length of array |
| maxLength | number |          | max length of array |

Note: with each Item of array, we can validate it base of generic type <I>

## 2.9 IValidationObject

`Interface use to validate the object variable `

```
export interface IValidationObject<T = any> extends IValidationCore<T> {
  isNotAlowExtProperties?: boolean;
}
```

Properties extend IValidationCore's properties and:

| name                   | Type    | Required | Description                                                                                    |
| :--------------------- | :------ | :------- | :--------------------------------------------------------------------------------------------- |
| isNotAlowExtProperties | boolean |          | true => don't allow object have properties which is not defined before in Object 's properties |

Note: with each Item of array, we can validate it base of generic type <I>
