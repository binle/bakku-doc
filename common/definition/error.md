# Define error common type for using in platform

## 1. IBakkuHttpError

`Error type for checking in request, response`

```
export interface IBakkuHttpError extends Error {
  status: number;
  code?: string;
  data?: { [key: string]: any };
}
```

| name   | Required    | Type | Description                                         |
| :----- | :---------- | :--- | :-------------------------------------------------- |
| status | number      | x    | The status code of http request                     |
| code   | string      |      | The code of error for matching error from BE and FE |
| data   | map, object |      | The external data for clear error's meaning         |

EX:

- {status: 400, code: 'login_failed_wrong_username_password'}
- {status: 400, code: 'register_member_error', data: {phone:"invalid phone number", email: 'email was used!'}}

## 2. BakkuHttpError

`implement of IBakkuHttpError`

```
class BakkuHttpError extends Error implements IBakkuHttpError
```

## 3. BakkuHttpErrorOption

`Error option type for creating new error`

```
export interface BakkuHttpErrorOption {
  message?: string;
  code?: string;
  data?: { [key: string]: any };
}
```

| name    | Required    | Type | Description                                         |
| :------ | :---------- | :--- | :-------------------------------------------------- |
| message | string      | x    | Message of error                                    |
| code    | string      |      | The code of error for matching error from BE and FE |
| data    | map, object |      | The external data for clear error's meaning         |

## 4. newBakkuHttpError

`function for create new IBakkuHttpError`

`newBakkuHttpError(opts: {message: string, status: number, code?: string}, extOpt: BakkuHttpErrorOption)`

| name         | Required             | Type | Description                                          |
| :----------- | :------------------- | :--- | :--------------------------------------------------- |
| opts.message | string               | x    | Message of error                                     |
| opts.status  | number               | x    | The status code of http request                      |
| opts.code    | string               |      | The code of error for matching error from BE and FE  |
| extOpt       | BakkuHttpErrorOption |      | extra options:                                       |
|              |                      |      | **extOpt.message** is high priority than opt.message |
|              |                      |      | **extOpt.code** is high priority than opt.code       |

EX:

- newBakkuHttpError({code: 400, message: 'bad request'})
- newBakkuHttpError({code: 400, message: 'bad request', code: 'login_failed'})
- newBakkuHttpError({code: 400, message: 'bad request', code: 'register_member_error', data: {phone:"invalid phone number", email: 'email was used!'}})

## 5. HttpCodeStatuses

```
  100: { status: 100, message: 'Continue' },
  101: { status: 101, message: 'Switching Protocols' },
  102: { status: 102, message: 'Processing' },
  200: { status: 200, message: 'OK' },
  201: { status: 201, message: 'Created' },
  202: { status: 202, message: 'Accepted' },
  203: { status: 203, message: 'Non Authoritative Information' },
  204: { status: 204, message: 'No Content' },
  205: { status: 205, message: 'Reset Content' },
  206: { status: 206, message: 'Partial Content' },
  207: { status: 207, message: 'Multi-Status' },
  300: { status: 300, message: 'Multiple Choices' },
  301: { status: 301, message: 'Moved Permanently' },
  302: { status: 302, message: 'Moved Temporarily' },
  303: { status: 303, message: 'See Other' },
  304: { status: 304, message: 'Not Modified' },
  305: { status: 305, message: 'Use Proxy' },
  307: { status: 307, message: 'Temporary Redirect' },
  308: { status: 308, message: 'Permanent Redirect' },
  400: { status: 400, message: 'Bad Request' },
  401: { status: 401, message: 'Unauthorized' },
  402: { status: 402, message: 'Payment Required' },
  403: { status: 403, message: 'Forbidden' },
  404: { status: 404, message: 'Not Found' },
  405: { status: 405, message: 'Method Not Allowed' },
  406: { status: 406, message: 'Not Acceptable' },
  407: { status: 407, message: 'Proxy Authentication Required' },
  408: { status: 408, message: 'Request Timeout' },
  409: { status: 409, message: 'Conflict' },
  410: { status: 410, message: 'Gone' },
  411: { status: 411, message: 'Length Required' },
  412: { status: 412, message: 'Precondition Failed' },
  413: { status: 413, message: 'Request Entity Too Large' },
  414: { status: 414, message: 'Request-URI Too Long' },
  415: { status: 415, message: 'Unsupported Media Type' },
  416: { status: 416, message: 'Requested Range Not Satisfiable' },
  417: { status: 417, message: 'Expectation Failed' },
  418: { status: 418, message: "I'm a teapot" },
  419: { status: 419, message: 'Insufficient Space on Resource' },
  420: { status: 420, message: 'Method Failure' },
  422: { status: 422, message: 'Unprocessable Entity' },
  423: { status: 423, message: 'Locked' },
  424: { status: 424, message: 'Failed Dependency' },
  428: { status: 428, message: 'Precondition Required' },
  429: { status: 429, message: 'Too Many Requests' },
  431: { status: 431, message: 'Request Header Fields Too Large' },
  451: { status: 451, message: 'Unavailable For Legal Reasons' },
  500: { status: 500, message: 'Internal Server Error' },
  501: { status: 501, message: 'Not Implemented' },
  502: { status: 502, message: 'Bad Gateway' },
  503: { status: 503, message: 'Service Unavailable' },
  504: { status: 504, message: 'Gateway Timeout' },
  505: { status: 505, message: 'HTTP Version Not Supported' },
  507: { status: 507, message: 'Insufficient Storage' },
  511: { status: 511, message: 'Network Authentication Required' },
```

EX:

- newBakkuHttpError(HttpCodeStatuses[400])
- newBakkuHttpError(HttpCodeStatuses[400], {message : 'Can not save data', code: 'invalid_phone_number'})

## 6. HttpStatusCodes

```
   CONTINUE: { status: 100, message: 'Continue' },
  SWITCHING_PROTOCOLS: { status: 101, message: 'Switching Protocols' },
  PROCESSING: { status: 102, message: 'Processing' },
  OK: { status: 200, message: 'OK' },
  CREATED: { status: 201, message: 'Created' },
  ACCEPTED: { status: 202, message: 'Accepted' },
  NON_AUTHORITATIVE_INFORMATION: { status: 203, message: 'Non Authoritative Information' },
  NO_CONTENT: { status: 204, message: 'No Content' },
  RESET_CONTENT: { status: 205, message: 'Reset Content' },
  PARTIAL_CONTENT: { status: 206, message: 'Partial Content' },
  MULTI_STATUS: { status: 207, message: 'Multi-Status' },
  MULTIPLE_CHOICES: { status: 300, message: 'Multiple Choices' },
  MOVED_PERMANENTLY: { status: 301, message: 'Moved Permanently' },
  MOVED_TEMPORARILY: { status: 302, message: 'Moved Temporarily' },
  SEE_OTHER: { status: 303, message: 'See Other' },
  NOT_MODIFIED: { status: 304, message: 'Not Modified' },
  USE_PROXY: { status: 305, message: 'Use Proxy' },
  TEMPORARY_REDIRECT: { status: 307, message: 'Temporary Redirect' },
  PERMANENT_REDIRECT: { status: 308, message: 'Permanent Redirect' },
  BAD_REQUEST: { status: 400, message: 'Bad Request' },
  UNAUTHORIZED: { status: 401, message: 'Unauthorized' },
  PAYMENT_REQUIRED: { status: 402, message: 'Payment Required' },
  FORBIDDEN: { status: 403, message: 'Forbidden' },
  NOT_FOUND: { status: 404, message: 'Not Found' },
  METHOD_NOT_ALLOWED: { status: 405, message: 'Method Not Allowed' },
  NOT_ACCEPTABLE: { status: 406, message: 'Not Acceptable' },
  PROXY_AUTHENTICATION_REQUIRED: { status: 407, message: 'Proxy Authentication Required' },
  REQUEST_TIMEOUT: { status: 408, message: 'Request Timeout' },
  CONFLICT: { status: 409, message: 'Conflict' },
  GONE: { status: 410, message: 'Gone' },
  LENGTH_REQUIRED: { status: 411, message: 'Length Required' },
  PRECONDITION_FAILED: { status: 412, message: 'Precondition Failed' },
  REQUEST_ENTITY_TOO_LARGE: { status: 413, message: 'Request Entity Too Large' },
  REQUEST_URI_TOO_LONG: { status: 414, message: 'Request-URI Too Long' },
  UNSUPPORTED_MEDIA_TYPE: { status: 415, message: 'Unsupported Media Type' },
  REQUESTED_RANGE_NOT_SATISFIABLE: { status: 416, message: 'Requested Range Not Satisfiable' },
  EXPECTATION_FAILED: { status: 417, message: 'Expectation Failed' },
  IM_A_TEAPOT: { status: 418, message: "I'm a teapot" },
  INSUFFICIENT_SPACE_ON_RESOURCE: { status: 419, message: 'Insufficient Space on Resource' },
  METHOD_FAILURE: { status: 420, message: 'Method Failure' },
  UNPROCESSABLE_ENTITY: { status: 422, message: 'Unprocessable Entity' },
  LOCKED: { status: 423, message: 'Locked' },
  FAILED_DEPENDENCY: { status: 424, message: 'Failed Dependency' },
  PRECONDITION_REQUIRED: { status: 428, message: 'Precondition Required' },
  TOO_MANY_REQUESTS: { status: 429, message: 'Too Many Requests' },
  REQUEST_HEADER_FIELDS_TOO_LARGE: { status: 431, message: 'Request Header Fields Too Large' },
  UNAVAILABLE_FOR_LEGAL_REASONS: { status: 451, message: 'Unavailable For Legal Reasons' },
  INTERNAL_SERVER_ERROR: { status: 500, message: 'Internal Server Error' },
  NOT_IMPLEMENTED: { status: 501, message: 'Not Implemented' },
  BAD_GATEWAY: { status: 502, message: 'Bad Gateway' },
  SERVICE_UNAVAILABLE: { status: 503, message: 'Service Unavailable' },
  GATEWAY_TIMEOUT: { status: 504, message: 'Gateway Timeout' },
  HTTP_VERSION_NOT_SUPPORTED: { status: 505, message: 'HTTP Version Not Supported' },
  INSUFFICIENT_STORAGE: { status: 507, message: 'Insufficient Storage' },
  NETWORK_AUTHENTICATION_REQUIRED: { status: 511, message: 'Network Authentication Required' },
```

EX:

- newBinHttpError(HttpStatusCodes.BAD_REQUEST);
- newBakkuHttpError(HttpStatusCodes.BAD_REQUEST, {message : 'Can not save data', code: 'invalid_phone_number'})
