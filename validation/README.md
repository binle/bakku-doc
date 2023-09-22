# 1 Install & Use

## 1.1 Install

&emsp;&emsp; `npm install @bakku/validation`

## 1.2 Use

##### Recommended use `asyncValidate` function for all validation.

_Return an `error` if it is failed the validation, return `correct value of data` if it passed the validation_

Refer: [use asyncValidate](./doc/validate.md)

```
asyncValidate = async <T = any>(
  schema?: ISchemaCore<T>,
  data?: T,
  name?: string
): Promise<IValidationError | TypeOptional<T>> => {
```

### 1.2.1 asyncValidateString

_Return an `error` if it is failed the validation, return `string` (as inputted data) if it passed the validation_
Refer: [use asyncValidateString](./doc/validate-string.md)

```
asyncValidateString = async (
  schema: IStringSchema,
  data?: any,
  name?: string
): Promise<IValidationError | string>
```

#### 1.2.2 asyncValidateNumber

_Return an `error` if it is failed the validation, return `number` (as inputted data or converted data) if it passed the validation_
Refer: [use asyncValidateNumber](./doc/validate-number.md)

```
asyncValidateNumber = async (
  schema: INumberSchema,
  data?: any,
  name?: string
): Promise<IValidationError | number> => {
```

#### 1.2.3 asyncValidateInteger

_Return an `error` if it is failed the validation, return `integer` (as inputted data or converted data) if it passed the validation_
Refer: [use asyncValidateInteger](./doc/validate-integer.md)

```
asyncValidateInteger = async (
  schema: IIntegerSchema,
  data?: any,
  name?: string
): Promise<IValidationError | number> => {
```

#### 1.2.4 asyncValidateFile

Note: file type is `Express.Multer.File`
_Return an `error` if it is failed the validation, return `file` (as inputted data) if it passed the validation_
Refer: [use asyncValidateFile](./doc/validate-file.md)

```
asyncValidateFile = async (
  schema: IFileSchema,
  data?: any,
  name?: string
): Promise<IValidationError | Express.Multer.File> => {
```

#### 1.2.5 asyncValidateDate

_Return an `error` if it is failed the validation, return `Date` (as inputted data or converted data) if it passed the validation_
Refer: [use asyncValidateDate](./doc/validate-date.md)

**Note: we convert to moment data for validation, so the format base on moment rules.**

```
asyncValidateDate = async (
  schema: IDateSchema,
  data?: any,
  name?: string
): Promise<IValidationError | Date> => {
```

#### 1.2.6 asyncValidateBoolean

_Return an `error` if it is failed the validation, return `Boolean` (as inputted data or converted data) if it passed the validation_
Refer: [use asyncValidateBoolean](./doc/validate-boolean.md)

```
asyncValidateBoolean = async (
  schema: IBooleanSchema,
  data?: any,
  name?: string
): Promise<IValidationError | boolean> => {
```

#### 1.2.7 asyncValidateEnum

_Return an `error` if it is failed the validation, return `Enum Value` (as inputted data) if it passed the validation_
Refer: [use asyncValidateEnum](./doc/validate-enum.md)

```
asyncValidateEnum = async (
  schema: IEnumSchema,
  data?: any,
  name?: string
): Promise<IValidationError | any> => {
```

#### 1.2.8 asyncValidateArray

_Return an `error` if it is failed the validation, return `Array` (as inputted data with corrected items ) if it passed the validation_
Refer: [use asyncValidateArray](./doc/validate-array.md)

```
asyncValidateArray = async <T>(
  schema: IArraySchema<T>,
  data?: any,
  name?: string
): Promise<IValidationError | Array<T>> => {
```

#### 1.2.9 asyncValidateObject

_Return an `error` if it is failed the validation, return `Object` (as inputted data with corrected properties ) if it passed the validation_
Refer: [use asyncValidateObject](./doc/validate-object.md)

```
asyncValidateObject = async <T = unknown>(
  schema: IObjectSchema<T>,
  data?: T,
  name?: string
): Promise<IValidationError | TypeOptional<T>> => {
```

# 2 Schemas

`ISchemaGeneral` is a common type, It can be understand as all schema has in the library.

```
export type ISchemaGeneral =  ISchemaCore | IBooleanSchema | IStringSchema | INumberSchema | IIntegerSchema | IFileSchema | IDateSchema | IEnumSchema | IArraySchema | IObjectSchema;
```

## 2.1 ISchemaCore.

Core schema with validation is extends [IValidationCore](#32-ivalidationCore)

| Property     | Type                                                                     | Required | Default          | Description                                                |
| :----------- | :----------------------------------------------------------------------- | -------- | ---------------- | ---------------------------------------------------------- |
| type         | enum: [SchemaType](#31-schematype)                                       |          | default `object` | type of schema which we base on that to validate           |
| propertyType | Class name, Function object name                                         |          |                  | use for validation an object class (type is `object`)      |
| description  | String                                                                   |          |                  | Description for schema                                     |
| validation   | Object: Instance of object extend [IValidationCore](#32-ivalidationcore) |          |                  | Detail the validation rules, custom the validation if need |

## 2.2 IStringSchema.

String schema extend ISchemaCore with validation is [IValidationString](#33-ivalidationstring)

## 2.3 INumberSchema.

Number schema extend ISchemaCore with validation is [IValidationNumber](#34-ivalidationnumber)

## 2.4 IIntegerSchema.

Integer schema extend ISchemaCore with validation is [IValidationInteger](#35-ivalidationinteger)

## 2.5 IFileSchema.

File schema extend ISchemaCore with validation is [IValidationFile](#36-ivalidationfile)

## 2.6 IDateSchema.

Array schema extend ISchemaCore with validation is [IValidationDate](#37-ivalidationdate) with extra properties:

| Property            | Type                        | Required | Default | Description                                |
| :------------------ | :-------------------------- | -------- | ------- | ------------------------------------------ |
| format              | string                      |          |         | moment format                              |
| convertToDateMoment | (originData: any) => Moment |          |         | convert data to moment date for validation |

```
let dateSchema: IDateSchema = {
  type: 'date',
  dateFormat: 'YYYY-MM-DD',
  validation: {...},
};
// **Note: when you use convertToDateMoment, dateFormat will be ignored.**
dateSchema = {
  type: 'date',
  convertToDateMoment: (date) => moment(date, 'YYYY-MM-DD'),
  validation: {...},
};

// convert to local timezone
dateSchema = {
  type: 'date',
  convertToDateMoment: (data: string) => {
    const currentOffset = moment().utcOffset();
    return moment.utc(data, 'YYYY-MM-DD').utcOffset(currentOffset);
  },
  validation: {...},
};
```

## 2.6 IEnumSchema.

Enum schema extend ISchemaCore with validation is [IValidationEnum](#38-ivalidationenum) with extra properties:

| Property   | Type                  | Required | Default | Description               |
| :--------- | :-------------------- | -------- | ------- | ------------------------- |
| enumValues | (string or number) [] | true     |         | all data can have of enum |
| enumName   | string                |          |         | name of enum              |

```
enum TempEnum {
  active = 'active',
  inactive = 'inactive',
  normal = 'normal',
}
const enumSchema: IEnumSchema = {
  type: 'enum',
  enumValues: getEnumValues(TempEnum),
  enumName: 'TempEnum'
  validation: {...},
};
```

## 2.6 IArraySchema.

Array schema extend ISchemaCore with validation is [IValidationArray](#39-ivalidationarray) with extra properties:

| Property   | Type                         | Required | Default | Description                                         |
| :--------- | :--------------------------- | -------- | ------- | --------------------------------------------------- |
| itemSchema | [ISchemaGeneral](#2-schemas) |          |         | schema use to verify(if needed) each item in array, |

## 2.6 IObjectSchema.

Object schema extend ISchemaCore with validation is [IValidationObject](#310-ivalidationobject) with extra properties:

| Property   | Type                                  | Required | Default | Description                                                          |
| :--------- | :------------------------------------ | -------- | ------- | -------------------------------------------------------------------- |
| properties | {[key]: [ISchemaGeneral](#2-schemas)} |          |         | each property will have a schema which use for validation it's value |

#3 . Data

## 3.1 SchemaType

```

Types of schema which library support for validation:
'boolean' |'string' |'number' |'integer' |'enum' |'date' |'file' |'array' |'object'

```

## 3.2 IValidationCore

| Property   | Type     | Required | Default | Description                                                        |
| :--------- | :------- | -------- | ------- | ------------------------------------------------------------------ |
| isRequired | Boolean  |          | false   | mark for validation, item is required or not, default is `false`   |
| validation | Function |          |         | custom the added validation with data of item and item's path name |

NOTE:
`validation: (data, pathName) => (IValidationError | undefined) | Promise<IValidationError | undefined>`

## 3.3 IValidationString

is extended **IValidationCore** with extra properties:

| Property       | Type               | Required | Default | Description                         |
| :------------- | :----------------- | -------- | ------- | ----------------------------------- |
| format         | Regex or 'isEmail' |          |         | isEmail is a supported Regex        |
| minLength      | integer            |          |         | min length of string for validation |
| maxLength      | integer            |          |         | max length of string for validation |
| byteLength     | Object             |          |         | check length of string in byte size |
| byteLength.min | integer            |          |         | min byte after convert              |
| byteLength.max | integer            |          |         | max byte after convert              |

## 3.4 IValidationNumber

is extended **IValidationCore** with extra properties:

| Property | Type    | Required | Default | Description              |
| :------- | :------ | -------- | ------- | ------------------------ |
| min      | integer |          |         | min value for validation |
| max      | integer |          |         | max value for validation |

## 3.5 IValidationInteger

is the same with **IValidationNumber**

## 3.6 IValidationFile

is extended **IValidationCore** with extra properties:

| Property | Type    | Required | Default | Description                     |
| :------- | :------ | -------- | ------- | ------------------------------- |
| minSize  | integer |          |         | min size of file for validation |
| maxSize  | integer |          |         | max size of file for validation |
| mineType | string  |          |         | type of file for validation     |

## 3.7 IValidationDate

is extended **IValidationCore** with extra properties:

| Property | Type | Required | Default | Description             |
| :------- | :--- | -------- | ------- | ----------------------- |
| min      | Date |          |         | min date for validation |
| max      | Date |          |         | max date for validation |

## 3.8 IValidationEnum

is extended **IValidationNumber** with no extra properties.

## 3.9 IValidationArray

is extended **IValidationCore** with extra properties:

| Property  | Type    | Required | Default | Description                        |
| :-------- | :------ | -------- | ------- | ---------------------------------- |
| minLength | integer |          |         | min length of array for validation |
| maxLength | integer |          |         | max length of array for validation |

## 3.10 IValidationObject

is extended **IValidationCore** with extra properties:

| Property               | Type    | Required | Default | Description                                                                                                    |
| :--------------------- | :------ | -------- | ------- | -------------------------------------------------------------------------------------------------------------- |
| isNotAlowExtProperties | boolean |          |         | allow the other properties which is not declared in object or not, true=> not alow, false (undefined) => allow |
