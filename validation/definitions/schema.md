# 1.Schema

`Schema use for validation`

## 1.1.SchemaType

`Type of data will be defined for validation`

```
  | 'boolean'
  | 'string'
  | 'number'
  | 'integer'
  | 'enum'
  | 'date'
  | 'file'
  | 'array'
  | 'object'
  | undefined;
```

## 1.2.ISchemaCore

`Schema core use for validation any data`

```
export interface ISchemaCore<T = any, V extends IValidationCore<T> = IValidationCore> {
  type?: SchemaType;
  description?: string;
  validation?: V;
    /**
   * propertyType only should be defined in item schema of array schema
   */
  propertyType?: TypeConstructor<T>;
  circularType?: (target: T) => any;
}
```

Properties:

| name         | Type                 | Required | Description                          |
| :----------- | :------------------- | :------- | :----------------------------------- |
| type         | SchemaType           |          | type of schema (#1.1)                |
| description  | Class or Constructor |          | description of schema                |
| validation   | V extend IValidation |          | validation data                      |
| propertyType | Class or Constructor |          | property type in Object Class //TODO |
| circularType | Function             |          | circular type in Object Class //TODO |

## 1.3. IBooleanSchema

`Schema core use for validation boolean variable`

```
export type IBooleanSchema = ISchemaCore<boolean, IValidationBoolean>;
```

## 1.4. IStringSchema

`Schema core use for validation string variable`

```
export type IStringSchema = ISchemaCore<string, IValidationString>;
```

## 1.5. INumberSchema

`Schema core use for validation boolean variable`

```
export type INumberSchema = ISchemaCore<number, IValidationNumber>;
```

## 1.6. IIntegerSchema

`Schema core use for validation integer variable`

```
export type IIntegerSchema = IIntegerSchema;
```

## 1.7. IFileSchema

`Schema core use for validation file variable`

```
export type IFileSchema = ISchemaCore<Express.Multer.File | any, IValidationFile>;
```

## 1.8. IDateSchema

`Schema core use for validation Date variable, use moment for checking`

```
export interface IDateSchema extends ISchemaCore<Date, IValidationDate> {
  /**
   * date format will be ignored if convertToDateMoment is defined
   */
  dateFormat?: string;
  /**
   * use this function, the dateFormat will be ignored
   */
  convertToDateMoment?: (originData: any) => Moment;
}
```

Properties extend ISchemaCore's properties and :

| name                | Type     | Required | Description                                               |
| :------------------ | :------- | :------- | :-------------------------------------------------------- |
| dateFormat          | String   |          | format of Date, use for convert date when use moment      |
| convertToDateMoment | Function |          | convert the data to moment date instead of use dateFormat |

## 1.9. IEnumSchema

`Schema core use for validation Enum variable`

```
export interface IEnumSchema extends ISchemaCore<any, IValidationEnum> {
  enumValues: any[];
  enumName?: string;
}
```

Properties extend ISchemaCore's properties and :

| name       | Type   | Required | Description                                                     |
| :--------- | :----- | :------- | :-------------------------------------------------------------- |
| enumValues | any    |          | list of value in enum which can be used                         |
| enumName   | string |          | enumName, use for sync the enum name between BE, FE, services.. |

## 1.10 IArraySchema

`Schema core use for validation Array variable`

```
export interface IArraySchema<I = any> extends ISchemaCore<Array<I>, IValidationArray<I>> {
  itemSchema?: ISchemaGeneral;
}
```

Properties extend ISchemaCore's properties and :

| name       | Type | Required | Description                                  |
| :--------- | :--- | :------- | :------------------------------------------- |
| itemSchema | any  |          | Schema use for validation each item of array |

## 1.11 IObjectSchema

`Schema core use for validation Object variable`

```
export interface IObjectSchema<T = any> extends ISchemaCore<T, IValidationObject<T>> {
  /**
   * validate each property with corresponding schema, this field is higher priority
   */
  properties?: { [key in keyof T]: ISchemaGeneral };
  /**
   * this schema will be use to validate all item in object,  this field is lower priority
   */
  generalSchema?: ISchemaGeneral;
}
```

Properties extend ISchemaCore's properties and :

| name          | Type | Required | Description                                          |
| :------------ | :--- | :------- | :--------------------------------------------------- |
| properties    | any  |          | Map each property of object and it's schema          |
| generalSchema | any  |          | schema use for validate for all properties of object |

## 1.12 ISchemaGeneral

```
export type ISchemaGeneral =
  | ISchemaCore
  | IBooleanSchema
  | IStringSchema
  | INumberSchema
  | IIntegerSchema
  | IFileSchema
  | IDateSchema
  | IEnumSchema
  | IArraySchema
  | IObjectSchema;
```

# 2.Common schema

- BooleanSchema
- StringSchema
- NumberSchema
- IntegerSchema
- DateSchema
- FileSchema
- EnumSchema
- ArraySchema
- ObjectSchema
- BooleanRequireSchema
- StringRequireSchema
- NumberRequireSchema
- IntegerRequireSchema
- DateRequireSchema
- FileRequireSchema
- EnumRequireSchema
- ArrayRequireSchema
- ObjectRequireSchema
- BakkuHttpErrorSchema
