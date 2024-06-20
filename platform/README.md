# 1 Install & Use

## 1.1 Install

&emsp;&emsp;npm: `npm install @bakku/platform`
&emsp;&emsp;yarn: `yarn install @bakku/platform`

## 1.2 Use

refer [Sample Project](https://github.com/binle/bakku/tree/main/sample)

### startServer

```
startServer(
  appOptions?: TypeServerOptions,
  createExpressApp?: () => Express,
  ssl?: https.ServerOptions
)
```

| name             | type                | required | default     | description                                                                                            |
| :--------------- | :------------------ | :------- | :---------- | :----------------------------------------------------------------------------------------------------- |
| appOptions       | TypeServerOptions   | NO       |             | input information for setting app                                                                      |
| createExpressApp | () => Express       | NO       | `createApp` | custom create express instance, base on that for create APIs. Default `createApp` function in platform |
| ssl              | https.ServerOptions | NO       |             | case use directive SSL for server                                                                      |

### TypeServerOptions:

| name                | type                            | required           | default | description                                                                          |
| :------------------ | :------------------------------ | :----------------- | :------ | :----------------------------------------------------------------------------------- |
| host                | string                          | NO                 |         | host which server will be used, should be 0.0.0.0                                    |
| port                | number                          | NO                 | 8080    | port which server will be used                                                       |
| apiPrefix           | string                          | NO                 |         | prefix for all API => https://domain/[prefix]/[controller.paht]/[controller.apiPath] |
| logger              | log object                      | NO                 | console | custom logging                                                                       |
| appHandlers         | ExRequestHandler[]              | NO                 |         | Array handler for express application, EX: CORS, userMiddleware...                   |
| staticHandlers      | [key: string]: ExRequestHandler | ExRequestHandler[] | NO      | static handlers, often use for static file                                           |
| documentOptions     | TypeDocumentHandlers            | NO                 |         | config for display APIs document                                                     |
| documentOptions     | TypeDocumentHandlers            | NO                 |         | config for display APIs document                                                     |
| errorHandlerOptions | TypeErrorHandlers               | NO                 |         | Handle error default                                                                 |
| bodyJsonSetting     | bodyParser.OptionsJson          | NO                 |         | config for parse json data in express: express.json(..)                              |
| appSettings         | [key: string]: any              | NO                 |         | config for app use any setting. EX: convert date format, json...                     |

Example:

```
  const options: TypeServerOptions = {
    apiPrefix: 'api', // => all your APis will be have prefix : /api/...
    logger: global.applicationContexts.logger,

    documentOptions: {
      docPath: 'doc', // => API document page
    },
  };
  startServer(options);
```

# 2 Detail

## 2.1 Controller

```
@Controller({
  name: string | symbol,
  path: string,
  description?: string
})
```

EX:

```
@Controller({
  name: 'HomeController',
  path: 'home',
})
export class HomeController {
 ....
}
```

## 2.2 API methods

### CommonType

> @API(option: { type: TypeRequestMethod;
> path: string;
> description?: string;
> bodyContentType?: string;
> }): _define any API_

Note: TypeRequestMethod: 'get' | 'post' | 'put' | 'patch' | 'delete' | 'all'

### DetailType:

> **@Get(path: string, description?: string)**: _define Get API_

> **@Delete(path: string, description?: string)**: _define Delete API_

> **@Post(path: string, description?: string, bodyContentType?: string)**: _define Post API_

> **@Put(path: string, description?: string, bodyContentType?: string)**: _define Put API_

> **@Patch(path: string, description?: string, bodyContentType?: string)**: _define Patch API_

> **@UseAll(path: string, description?: string, bodyContentType?: string)**: _define UseAll API_

## 2.3 Middleware

### 2.3.1 **@Middleware(ExRequestHandler | ExRequestHandler[])**: _define middleware for each API_

Note: ExRequestHandler is RequestHandler - (req, res, next) : void
EX:

```
const requireLoggedInMiddleWare = async ( req: ExRequest, _res: ExResponse, next: ExNextFunction) => {
  let user;
  // check user from header token
  //....
  if (!user) throw new Error('You need to login first!');
  req.user = user;
  next();
};

@Controller({
  name: 'HomeController',
  path: 'home',
})
export class HomeController {
  @Get('user')
  @Middleware(requireLoggedInMiddleWare)
  doSomething(@Req() req: ExRequest) {
    return req.user;
  }
}
```

### 2.3.2 **@MiddlewareCustom( () => ExRequestHandler)**: _define middleware for each API_

```
const customRequireAdminInMiddleWare =() => {
  // get roles
  const adminRoles = ...// do somethings.
  return async ( req: ExRequest, \_res: ExResponse, next: ExNextFunction) => {
    let user;
    // check user from header token
    //....
    if (!user || !user.role || !adminRole.includes(user.role)) throw new Error('You are not allow process this feature!');
    req.user = user;
    next();
  }
};

@Controller({
name: 'HomeController',
path: 'home',
})
export class HomeController {
  @Get('admin')
  @MiddlewareCustom(customRequireAdminInMiddleWare)
  doSomething(@Req() req: ExRequest) {
    return req.user;
  }
}

```

## 2.4 Data from request

### 2.4.1 Data from header `@Headers()`

```
@Controller({
name: 'HomeController',
path: 'home',
})
export class HomeController {
  @Get('admin')
  doSomething(
    @Headers() headers: any,
  ) {
    return {headers};
  }
}
```

### 2.4.2 Data from queries `@Queries()`

```
@Controller({
name: 'HomeController',
path: 'home',
})
export class HomeController {
  @Post('test')
  doSomething(
    @Queries() queries: any,
  ) {
    return {queries};
  }
}
```

### 2.4.3 Data from params `@Params()`

```
@Controller({
name: 'HomeController',
path: 'home',
})
export class HomeController {
  @Post('test')
  doSomething(
    @Params() params: any,
  ) {
    return {params};
  }
}
```

### 2.4.4 Data from body

`@Body()`

```
@Controller({
name: 'HomeController',
path: 'home',
})
export class HomeController {
  @Post('test')
  doSomething(
    @Body() body: any,
  ) {
    return {body};
  }
}
```

### 2.4.5 get all request

`@Req()`

```
@Controller({
name: 'HomeController',
path: 'home',
})
export class HomeController {
  @Post('test')
  doSomething(
    @Req() req: ExRequest,
  ) {
    console.log(req.header, req.body, ...)
    return {ok: true};
  }
}
```

### 2.4.5 get all response

`@Res()`

```
@Controller({
name: 'HomeController',
path: 'home',
})
export class HomeController {
  @Post('test')
  doSomething(
    @Res() res: ExResponse,
  ) {
    console.log(res.header,, ...)
    return {ok: true};
  }
}
```

## 2.4 Data for response

`In normal when request success, the response will received the returned data from controller method and use it as body data.`

I
`Error case:`

```

  Response:
    header:
      status: statusCode
    body:
      error: {
        status: statusCode,
        code: errorCode, //base on requirement
        message: errorMessage //base on requirement
      }
```

# 3. Validation

refer [@bakku/validation](https://www.npmjs.com/package/@bakku/validation)

Validation base on schema when getting [data from request](#24-data-from-request).

## 3.1 Use schema for any data

For validation auto run, pass the corresponding validation schema.

EX:

```
const HeaderSecretDataSchema: IObjectSchema = {
  type: 'object',
  validation: { isRequired: true },
  properties: {
    'apikey': StringRequireSchema,
  },
};

@Controller({
name: 'HomeController',
path: 'home',
})
export class HomeController {
  @Get('admin')
  doSomething(
    @Headers(HeaderSecretDataSchema) headers: any,
  ) {
    // header.apikey will be validate as required, if not it will throw error
    return {headers};
  }
}

=====================
=> response:
  header: 400
  body: {
    error: {
      status: 400,
      code: ...
      message: 'headers.apikey' are required
    }
  }

```

**Note: the same for other data:**

@Body(schema) body: any

@Queries(schema) body: any

@Params(schema) body: any

## 3.2 Use class with DI

@[DI](options: IDataPropertyInjectedParams)

**_IDataPropertyInjectedParams_** is extends data from ISchemaGeneral of [@bakku/validation](https://www.npmjs.com/package/@bakku/validation) with:

&emsp;&emsp; options.sample // sample data for displaying in document api

&emsp;&emsp; options.description //// description data for displaying in document api

### 3.2.1 Common data

&emsp;&emsp; @DataProperty - require data

&emsp;&emsp; @DataPropertyOptional - not require data

EX:

```
class CreateAccountRequestBodyDto {
  @DataProperty({ validation: { format: 'isEmail' } })
  email: string;

  @DataProperty({ validation: { format: new RegExp('^(?=.*?[0-9])(?=.*[!@#$%^&*])(?=.*[a-z])(?=.*[A-Z]).{8,}$') } })
  password: string;
}

//==> Result:
....
@Body() body: CreateAccountRequestBodyDto,
...
```

### 3.2.2 Data is enum

&emsp;&emsp; @DataEnumProperty -

&emsp;&emsp; @DataEnumPropertyOptional

EX:

```
enum AccountType {
  USER = 'USER',
  MANAGER = 'MANAGER',
}

class CreateAccountRequestBodyDto {
  ...
  @DataEnumProperty({ enumData: getEnumData(AccountType) })
  accountType: AccountType;
  ...
}

//==> Result:
....
@Body() body: CreateAccountRequestBodyDto,
...
```

### 3.2.3 Data is array

&emsp;&emsp; @DataArrayProperty -

&emsp;&emsp; @DataArrayPropertyOptional

EX:

```

class Person {
  @DataProperty()
  name:
}
const  StringSchema: IStringSchema = { type: 'string' };
class RequestBodyDto {
  ...
  @DataArrayProperty({ itemSchema: { propertyType: Person } })
  listPerson: Person[];

  @DataArrayProperty({ itemSchema: StringSchema })
  listMessages: string[];
  ...
}

//==> Result:
....
@Body() body: RequestBodyDto,
...
```

3.2.5 Data is file upload

&emsp;&emsp; @DataFileProperty -

&emsp;&emsp; @DataFilePropertyOptional

# 4 Special cases

## 4.1 Self handle app error:

```
  const options: TypeServerOptions = {
    apiPrefix: 'api',
    errorHandlerOptions: {
      isSelfHandleError: true,
    },
  };
  const { app } = startServer(options);
  routeAppError(app, options);
```

## 4.2 Using swagger document API.

using with [swagger-ui-express](https://www.npmjs.com/package/swagger-ui-express)

Note: because default handle error will force all APIs which is defined after start server is not found APIs.
So for prevent it, and define API for swagger, should use combine with custom handle error above.

```
  ....
  import swaggerUi from 'swagger-ui-express';
  ....
  const options: TypeServerOptions = {
    apiPrefix: 'api',
    errorHandlerOptions: {
      isSelfHandleError: true,
    },
  };
  const { app, apiData } = startServer(options);
  const swaggerJson = convertToSwaggerJson(apiData);
  app.use('/swagger', swaggerUi.serve, swaggerUi.setup(swaggerJson));
  routeAppError(app, options);
  ...
```

## 4.3 Custom data response.
