
# ABFuzz分布式模糊测试引擎
主要负责测试相关的任务的调度和Fuzzing整个流程的管理
[TOC]

## 整体说明
1.	字符串都为utf8格式;
1.	HTTP Headers:
	1.	Content-Type设置为：application/json
1.	DataTime格式参考RFC3339标准

## 错误处理
错误的具体信息将在error字段中返回。

### 错误码示例
```json
{
    "code": "400",
    "message": "Param Error"
}
```


### 状态码列表
| 状态码 | 说明 |
|---|---|
| 200 | 返回正常 |
| 400 | 参数错误 |
| 401 | 无access<br> key或key无效 |
| 500 | 服务器内部错误 |


## java agent 注册

### 请求路径
```http
POST /abfuzz/v1/agent/register
```


### 请求参数

#### Body 请求对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `packageName` | `string` |  | N |  |
| `remote` | `string` |  | N |  |
| `createTime` | `string` | `Timestamp` | N |  |  |
| `updateTime` | `string` | `Timestamp` | N |  |  |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `packageName` | `string` |  | N |  |
| `remote` | `string` |  | N |  |
| `createTime` | `string` | `Timestamp` | N |  |  |
| `updateTime` | `string` | `Timestamp` | N |  |  |


## 

### 请求路径
```http
GET /abfuzz/v1/agents/status
```


### 请求参数

#### Query 参数
| 参数名 | 参数类型 | 格式类型 | 是否必须 | 默认值 | 说明 |
|---|---|---|---|---|---|
| `page_size` | `integer` | `Int32` | 否 |  |  |
| `page_token` | `string` |  | 否 |  |  |
| `skip` | `integer` | `Int32` | 否 |  |  |


### 返回值

#### 返回对象
| type | description |
|---|---|
| `Array<abfuzz.AgentStatus>` |  |


#### `abfuzz.AgentStatus`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `name` | `string` |  | N |  |
| `status` | `string` |  | N |  |
| `projectId` | `string` |  | N |  |
| `cpuset` | `string` |  | N |  |
| `taskId` | `string` |  | N |  |
| `agentStartTime` | `string` | `Timestamp` | N |  |  |
| `createTime` | `string` | `Timestamp` | N |  |  |


## 将openapi entities转化为ContextRequest

### 请求路径
```http
PUT /abfuzz/v1/openapi/entities
```


### 请求参数

#### Body 请求对象
| type | description |
|---|---|
| `Array<abfuzz.Entity>` |  |


#### `abfuzz.Entity`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `stepType` | `string` |  | N |  |
| `interfaces` | `Array<abfuzz.Entity.Interface>` |  | N |  |


#### `abfuzz.Entity.Interface`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `type` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | N |  |
| `pathParams` | `Map<string, string>` |  | N |  |
| `queryParams` | `Map<string, string>` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `response` | `mojo.http.Response` |  | N |  | Response represents the response from an HTTP request. |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


#### `mojo.http.Request`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `method` | `string` |  | N |  | method specifies the HTTP method (GET, POST, PUT, etc.).For client requests, an empty string means GET. |
| `url` | `string` | `Url` | N |  | url specifies either the URI being requested (for serverrequests) or the URL to access (for client requests). |
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Response`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `status` | `mojo.http.Status` |  | N |  |  |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Status`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `integer` | `Int32` | N |  |
| `reason` | `string` |  | N |  |


#### `mojo.http.Version`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `major` | `integer` | `Int32` | N |  |
| `minor` | `integer` | `Int32` | N |  |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `setup` | `abfuzz.ContextSetup` |  | N |  | 全局设置，在cases之前执行 |
| `cases` | `Array<abfuzz.ContextCase>` |  | N |  | OpenAPI里的请求(用path和method来对应)，包含在cases里的优先执行，没有包含在cases里的请求，每个请求自动生成一个case，在cases之后依次自行 |
| `teardown` | `abfuzz.ContextTeardown` |  | N |  | 在cases之后执行的请求 |


#### `abfuzz.CaseStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `disable` | `array` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.ContextCase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `steps` | `Array<abfuzz.CaseStep>` |  | N |  |


#### `abfuzz.ContextSetup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.SetupStep>` |  | N |  |


#### `abfuzz.ContextTeardown`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.TeardownStep>` |  | N |  |


#### `abfuzz.SetupStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | 更新上下文时发起的请求，不要求包含在OpenAPI中 |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |
| `filter` | `Array<string>` |  | N |  |
| `order` | `string` |  | N |  |


#### `abfuzz.TeardownStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


#### `mojo.http.Request`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `method` | `string` |  | N |  | method specifies the HTTP method (GET, POST, PUT, etc.).For client requests, an empty string means GET. |
| `url` | `string` | `Url` | N |  | url specifies either the URI being requested (for serverrequests) or the URL to access (for client requests). |
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Version`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `major` | `integer` | `Int32` | N |  |
| `minor` | `integer` | `Int32` | N |  |


## 根据openapi文档输出接口的entities

### 请求路径
```http
POST /abfuzz/v1/openapi/entities
```


### 请求参数

#### Query 参数
| 参数名 | 参数类型 | 格式类型 | 是否必须 | 默认值 | 说明 |
|---|---|---|---|---|---|
| `key` | `string` |  | 否 |  |  |
| `page_size` | `integer` | `Int32` | 否 |  |  |
| `page_token` | `string` |  | 否 |  |  |
| `skip` | `integer` | `Int32` | 否 |  |  |


### 返回值

#### 返回对象
| type | description |
|---|---|
| `Array<abfuzz.Entity>` |  |


#### `abfuzz.Entity`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `stepType` | `string` |  | N |  |
| `interfaces` | `Array<abfuzz.Entity.Interface>` |  | N |  |


#### `abfuzz.Entity.Interface`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `type` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | N |  |
| `pathParams` | `Map<string, string>` |  | N |  |
| `queryParams` | `Map<string, string>` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `response` | `mojo.http.Response` |  | N |  | Response represents the response from an HTTP request. |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


#### `mojo.http.Request`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `method` | `string` |  | N |  | method specifies the HTTP method (GET, POST, PUT, etc.).For client requests, an empty string means GET. |
| `url` | `string` | `Url` | N |  | url specifies either the URI being requested (for serverrequests) or the URL to access (for client requests). |
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Response`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `status` | `mojo.http.Status` |  | N |  |  |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Status`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `integer` | `Int32` | N |  |
| `reason` | `string` |  | N |  |


#### `mojo.http.Version`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `major` | `integer` | `Int32` | N |  |
| `minor` | `integer` | `Int32` | N |  |


## 校验openapi文档 TODO to be removed

### 请求路径
```http
POST /abfuzz/v1/openapis/lint
```


### 请求参数

#### Query 参数
| 参数名 | 参数类型 | 格式类型 | 是否必须 | 默认值 | 说明 |
|---|---|---|---|---|---|
| `key` | `string` |  | 否 |  |  |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `valid` | `boolean` |  | N |  |
| `operations` | `Array<abfuzz.OpenAPILintOperationResult>` |  | N |  |


#### `abfuzz.OpenAPILintOperationResult`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `path` | `string` |  | N |  | url path |
| `method` | `string` |  | N |  |
| `valid` | `boolean` |  | N |  |
| `description` | `string` |  | N |  |
| `howToFix` | `string` |  | N |  |
| `line` | `integer` | `Int32` | N |  |


## 查询项目列表

### 请求路径
```http
GET /abfuzz/v1/projects
```


### 请求参数

#### Query 参数
| 参数名 | 参数类型 | 格式类型 | 是否必须 | 默认值 | 说明 |
|---|---|---|---|---|---|
| `page_size` | `integer` | `Int32` | 否 |  |  |
| `page_token` | `string` |  | 否 |  |  |
| `skip` | `integer` | `Int32` | 否 |  |  |


### 返回值

#### 返回对象
| type | description |
|---|---|
| `Array<abfuzz.Project>` |  |


#### `abfuzz.CaseStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `disable` | `array` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.ContextCase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `steps` | `Array<abfuzz.CaseStep>` |  | N |  |


#### `abfuzz.ContextRequest`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `setup` | `abfuzz.ContextSetup` |  | N |  | 全局设置，在cases之前执行 |
| `cases` | `Array<abfuzz.ContextCase>` |  | N |  | OpenAPI里的请求(用path和method来对应)，包含在cases里的优先执行，没有包含在cases里的请求，每个请求自动生成一个case，在cases之后依次自行 |
| `teardown` | `abfuzz.ContextTeardown` |  | N |  | 在cases之后执行的请求 |


#### `abfuzz.ContextSetup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.SetupStep>` |  | N |  |


#### `abfuzz.ContextTeardown`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.TeardownStep>` |  | N |  |


#### `abfuzz.CorpusConfig`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `includings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `excludings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `httpBaseUrl` | `string` | `Url` | N |  | URl type<br>Uniform Resource Identifier (URI) is a string of characters used to identify a resource.<br>A Uniform Resource Name (URN) may be compared to a person's name,while a Uniform Resource Locator (URL) may be compared to their street address.In other words, a URN identifies an item and a URL provides a method for finding it.<br> |
| `httpMappingPort` | `integer` | `Int32` | N |  |
| `httpMappingHost` | `string` |  | N |  |
| `httpStaticValues` | `mojo.core.Object` |  | N |  | Object type |
| `httpCorpuses` | `Array<abfuzz.HttpCorpus>` |  | N |  |
| `combinedApiPath` | `string` |  | N |  |
| `asyncApi` | `boolean` |  | N |  |


#### `abfuzz.EndPoint`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` |  | N |  |
| `http` | `abfuzz.EndPoint.Http` |  | N |  |  |
| `priority` | `integer` | `Int32` | N |  |


#### `abfuzz.EndPoint.Http`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `path` | `string` |  | N |  |
| `methods` | `Array<string>` |  | N |  |


#### `abfuzz.Engine`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the engine |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.Entity`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `stepType` | `string` |  | N |  |
| `interfaces` | `Array<abfuzz.Entity.Interface>` |  | N |  |


#### `abfuzz.Entity.Interface`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `type` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | N |  |
| `pathParams` | `Map<string, string>` |  | N |  |
| `queryParams` | `Map<string, string>` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `response` | `mojo.http.Response` |  | N |  | Response represents the response from an HTTP request. |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |


#### `abfuzz.Fixup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.Fuzzer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | Y |  | the fuzzer name |
| `group` | `string` |  | N |  | the group id for the fuzzers |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `fuzzerPackage` | `abfuzz.FuzzerPackage` |  | N |  | the fuzzer package of the fuzzer |
| `executablePath` | `string` |  | N |  | Fuzzer's main executable path, relative to root. |
| `executableArguments` | `Array<string>` |  | N |  | the input arguments for the executable, it will be skipped when launcher_script is set. |
| `executableEnvironment` | `Map<string, string>` |  | N |  | the environment setting for the executable |
| `launcherScript` | `string` |  | N |  | Custom script that should be used to launch executable for this fuzzer.It will not try to launch the target if "@service-auto-start" set. |
| `timeout` | `integer` | `Int64` | N |  | Fuzzing timeout. |
| `testcaseTimeoutMillis` | `integer` | `Int64` | N |  | Testcase timeout. |
| `autoStop` | `boolean` |  | N |  | Fuzzing automatic stop. |
| `autoHarness` | `boolean` |  | N |  | auto generate harness |
| `customMutatorPlugin` | `string` |  | N |  | custom mutator plugin for Fuzz Engine |
| `customAllowTargets` | `Array<string>` |  | N |  | custom file name & function name targets for instruments and coverage, like white list. |
| `customDenyTargets` | `Array<string>` |  | N |  | custom targets for instruments and coverage, like white list. |
| `context` | `abfuzz.ContextRequest` |  | N |  | context request |
| `corpusPath` | `string` |  | N |  | the special corpus path |
| `preprocessScript` | `string` |  | N |  | the preprocess script info |
| `onlyFunctionalTest` | `boolean` |  | N |  | is only fuzzing user corpus |
| `autoHarnessCommand` | `string` |  | N |  | execute the command before run auto harness generation |
| `autoHarnessScript` | `string` |  | N |  | execute the script before run auto harness generation if the command is empty |
| `autoHarnessArguments` | `Array<string>` |  | N |  | the arguments when to run the harness generation, usually for including path setting |
| `autoHarnessBlacklist` | `Array<string>` |  | N |  | the blacklist files to run the harness generation, these files will be skipped. |
| `autoHarnessWebServer` | `boolean` |  | N |  | determine if the java auto harness server is a web server |
| `metadata` | `Map<string, mojo.core.Value>` |  | N |  | the meta information for the fuzzing |
| `corpus` | `abfuzz.CorpusConfig` |  | N |  |  |
| `entities` | `Array<abfuzz.Entity>` |  | N |  |


#### `abfuzz.FuzzerPackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `type` | `string` |  | N |  | the type of the source package, oneof the whitebox, greybox, blackbox |
| `filename` | `string` |  | N |  | Filename for the custom binary. |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |


#### `abfuzz.HttpCorpus`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | Y |  |
| `staticValues` | `mojo.core.Object` |  | N |  | Object type |
| `fixups` | `Array<abfuzz.Fixup>` |  | N |  |
| `relations` | `Array<abfuzz.Relation>` |  | N |  |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `abfuzz.Project`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | project id |
| `name` | `string` |  | N |  | project name, normally the same with the custom's main source repo |
| `description` | `string` |  | N |  | project description |
| `sourcePackage` | `abfuzz.SourcePackage` |  | N |  | the source package for the project, only used in the create project |
| `language` | `string` |  | N |  | the program languages for the project，如果有多种语言，那么fuzzer中就需要指定语言 |
| `userId` | `string` |  | N |  | 用户相关的Project的用户ID |
| `externalId` | `string` |  | N |  | 第三方系统对应project的id |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | the sanitizers will be used for the project |
| `engines` | `Array<abfuzz.Engine>` |  | Y |  | the fazz engine name |
| `platforms` | `Array<string>` |  | N |  | The platform that this job can run on. |
| `langVms` | `Array<abfuzz.LangVM>` |  | N |  | The lang_vm platform that this job can run on if the language is Java. |
| `fuzzers` | `Array<abfuzz.Fuzzer>` |  | Y |  | the fuzzers in the project |
| `createTime` | `string` | `Timestamp` | N |  | the create time of the project |
| `updateTime` | `string` | `Timestamp` | N |  | the update time of the project |
| `deleteTime` | `string` | `Timestamp` | N |  | the delete time of the project, for the soft delete |


#### `abfuzz.Relation`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.Repository`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `url` | `string` | `Url` | N |  | the git url for the source package |
| `branch` | `string` |  | N |  | the git branch for the source package |
| `tag` | `string` |  | N |  | the git tag for the source package |
| `commitTime` | `string` | `Timestamp` | N |  | the used commit time for the source package |
| `commitKey` | `string` |  | N |  | the used commit key for the source package, it is hash key for git |


#### `abfuzz.Sanitizer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the sanitizer |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.SetupStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | 更新上下文时发起的请求，不要求包含在OpenAPI中 |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |
| `filter` | `Array<string>` |  | N |  |
| `order` | `string` |  | N |  |


#### `abfuzz.SourcePackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the uuid for the source package |
| `projectId` | `string` |  | N |  | the project id which including the source package |
| `projectName` | `string` |  | N |  | the project name which including the source package |
| `projectVersion` | `string` |  | N |  | the project version |
| `type` | `string` |  | N |  | the type of the source packagewhitebox, graybox, blackbox |
| `name` | `string` |  | N |  | the name of the module of the source package |
| `fuzzRepo` | `abfuzz.Repository` |  | N |  | the repository information for the fuzz code reposisity in the source package |
| `sourceRepo` | `abfuzz.Repository` |  | N |  | the repository information for the main code reposisity of custom in the source package |
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `fileSet` | `Array<abfuzz.StorageFile>` |  | N |  | the file set for the source package |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary or raw source code package. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |
| `customVersion` | `string` |  | N |  | more version info for the custom system |
| `codeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `createTime` | `string` | `Timestamp` | N |  | create time of the source code package |
| `updateTime` | `string` | `Timestamp` | N |  | update time of the source code package |


#### `abfuzz.StorageFile`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  | 文件在minio上的 地址 |
| `checksum` | `string` | `Checksum` | N |  | 文件的hash校验码 |
| `type` | `string` |  | N |  | 文件类型，比如： `corpus`, `harness`, `source`, `build-script`, `openapi`, `jar` |
| `group` | `string` |  | N |  | 文件属于某个组，可以是某个fuzzer |


#### `abfuzz.TeardownStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


#### `mojo.http.Request`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `method` | `string` |  | N |  | method specifies the HTTP method (GET, POST, PUT, etc.).For client requests, an empty string means GET. |
| `url` | `string` | `Url` | N |  | url specifies either the URI being requested (for serverrequests) or the URL to access (for client requests). |
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Response`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |


#### `mojo.http.Status`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `integer` | `Int32` | N |  |
| `reason` | `string` |  | N |  |


#### `mojo.http.Version`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `major` | `integer` | `Int32` | N |  |
| `minor` | `integer` | `Int32` | N |  |


## 创建项目

### 请求路径
```http
POST /abfuzz/v1/projects
```


### 请求参数

#### Body 请求对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | project id |
| `name` | `string` |  | N |  | project name, normally the same with the custom's main source repo |
| `description` | `string` |  | N |  | project description |
| `sourcePackage` | `abfuzz.SourcePackage` |  | N |  | the source package for the project, only used in the create project |
| `language` | `string` |  | N |  | the program languages for the project，如果有多种语言，那么fuzzer中就需要指定语言 |
| `userId` | `string` |  | N |  | 用户相关的Project的用户ID |
| `externalId` | `string` |  | N |  | 第三方系统对应project的id |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | the sanitizers will be used for the project |
| `engines` | `Array<abfuzz.Engine>` |  | Y |  | the fazz engine name |
| `platforms` | `Array<string>` |  | N |  | The platform that this job can run on. |
| `langVms` | `Array<abfuzz.LangVM>` |  | N |  | The lang_vm platform that this job can run on if the language is Java. |
| `fuzzers` | `Array<abfuzz.Fuzzer>` |  | Y |  | the fuzzers in the project |
| `createTime` | `string` | `Timestamp` | N |  | the create time of the project |
| `updateTime` | `string` | `Timestamp` | N |  | the update time of the project |
| `deleteTime` | `string` | `Timestamp` | N |  | the delete time of the project, for the soft delete |


#### `abfuzz.CaseStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `disable` | `array` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.ContextCase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `steps` | `Array<abfuzz.CaseStep>` |  | N |  |


#### `abfuzz.ContextRequest`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `setup` | `abfuzz.ContextSetup` |  | N |  | 全局设置，在cases之前执行 |
| `cases` | `Array<abfuzz.ContextCase>` |  | N |  | OpenAPI里的请求(用path和method来对应)，包含在cases里的优先执行，没有包含在cases里的请求，每个请求自动生成一个case，在cases之后依次自行 |
| `teardown` | `abfuzz.ContextTeardown` |  | N |  | 在cases之后执行的请求 |


#### `abfuzz.ContextSetup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.SetupStep>` |  | N |  |


#### `abfuzz.ContextTeardown`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.TeardownStep>` |  | N |  |


#### `abfuzz.CorpusConfig`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `includings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `excludings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `httpBaseUrl` | `string` | `Url` | N |  | URl type<br>Uniform Resource Identifier (URI) is a string of characters used to identify a resource.<br>A Uniform Resource Name (URN) may be compared to a person's name,while a Uniform Resource Locator (URL) may be compared to their street address.In other words, a URN identifies an item and a URL provides a method for finding it.<br> |
| `httpMappingPort` | `integer` | `Int32` | N |  |
| `httpMappingHost` | `string` |  | N |  |
| `httpStaticValues` | `mojo.core.Object` |  | N |  | Object type |
| `httpCorpuses` | `Array<abfuzz.HttpCorpus>` |  | N |  |
| `combinedApiPath` | `string` |  | N |  |
| `asyncApi` | `boolean` |  | N |  |


#### `abfuzz.EndPoint`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` |  | N |  |
| `http` | `abfuzz.EndPoint.Http` |  | N |  |  |
| `priority` | `integer` | `Int32` | N |  |


#### `abfuzz.EndPoint.Http`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `path` | `string` |  | N |  |
| `methods` | `Array<string>` |  | N |  |


#### `abfuzz.Engine`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the engine |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.Entity`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `stepType` | `string` |  | N |  |
| `interfaces` | `Array<abfuzz.Entity.Interface>` |  | N |  |


#### `abfuzz.Entity.Interface`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `type` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | N |  |
| `pathParams` | `Map<string, string>` |  | N |  |
| `queryParams` | `Map<string, string>` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `response` | `mojo.http.Response` |  | N |  | Response represents the response from an HTTP request. |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |


#### `abfuzz.Fixup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.Fuzzer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | Y |  | the fuzzer name |
| `group` | `string` |  | N |  | the group id for the fuzzers |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `fuzzerPackage` | `abfuzz.FuzzerPackage` |  | N |  | the fuzzer package of the fuzzer |
| `executablePath` | `string` |  | N |  | Fuzzer's main executable path, relative to root. |
| `executableArguments` | `Array<string>` |  | N |  | the input arguments for the executable, it will be skipped when launcher_script is set. |
| `executableEnvironment` | `Map<string, string>` |  | N |  | the environment setting for the executable |
| `launcherScript` | `string` |  | N |  | Custom script that should be used to launch executable for this fuzzer.It will not try to launch the target if "@service-auto-start" set. |
| `timeout` | `integer` | `Int64` | N |  | Fuzzing timeout. |
| `testcaseTimeoutMillis` | `integer` | `Int64` | N |  | Testcase timeout. |
| `autoStop` | `boolean` |  | N |  | Fuzzing automatic stop. |
| `autoHarness` | `boolean` |  | N |  | auto generate harness |
| `customMutatorPlugin` | `string` |  | N |  | custom mutator plugin for Fuzz Engine |
| `customAllowTargets` | `Array<string>` |  | N |  | custom file name & function name targets for instruments and coverage, like white list. |
| `customDenyTargets` | `Array<string>` |  | N |  | custom targets for instruments and coverage, like white list. |
| `context` | `abfuzz.ContextRequest` |  | N |  | context request |
| `corpusPath` | `string` |  | N |  | the special corpus path |
| `preprocessScript` | `string` |  | N |  | the preprocess script info |
| `onlyFunctionalTest` | `boolean` |  | N |  | is only fuzzing user corpus |
| `autoHarnessCommand` | `string` |  | N |  | execute the command before run auto harness generation |
| `autoHarnessScript` | `string` |  | N |  | execute the script before run auto harness generation if the command is empty |
| `autoHarnessArguments` | `Array<string>` |  | N |  | the arguments when to run the harness generation, usually for including path setting |
| `autoHarnessBlacklist` | `Array<string>` |  | N |  | the blacklist files to run the harness generation, these files will be skipped. |
| `autoHarnessWebServer` | `boolean` |  | N |  | determine if the java auto harness server is a web server |
| `metadata` | `Map<string, mojo.core.Value>` |  | N |  | the meta information for the fuzzing |
| `corpus` | `abfuzz.CorpusConfig` |  | N |  |  |
| `entities` | `Array<abfuzz.Entity>` |  | N |  |


#### `abfuzz.FuzzerPackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `type` | `string` |  | N |  | the type of the source package, oneof the whitebox, greybox, blackbox |
| `filename` | `string` |  | N |  | Filename for the custom binary. |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |


#### `abfuzz.HttpCorpus`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | Y |  |
| `staticValues` | `mojo.core.Object` |  | N |  | Object type |
| `fixups` | `Array<abfuzz.Fixup>` |  | N |  |
| `relations` | `Array<abfuzz.Relation>` |  | N |  |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `abfuzz.Relation`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.Repository`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `url` | `string` | `Url` | N |  | the git url for the source package |
| `branch` | `string` |  | N |  | the git branch for the source package |
| `tag` | `string` |  | N |  | the git tag for the source package |
| `commitTime` | `string` | `Timestamp` | N |  | the used commit time for the source package |
| `commitKey` | `string` |  | N |  | the used commit key for the source package, it is hash key for git |


#### `abfuzz.Sanitizer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the sanitizer |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.SetupStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | 更新上下文时发起的请求，不要求包含在OpenAPI中 |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |
| `filter` | `Array<string>` |  | N |  |
| `order` | `string` |  | N |  |


#### `abfuzz.SourcePackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the uuid for the source package |
| `projectId` | `string` |  | N |  | the project id which including the source package |
| `projectName` | `string` |  | N |  | the project name which including the source package |
| `projectVersion` | `string` |  | N |  | the project version |
| `type` | `string` |  | N |  | the type of the source packagewhitebox, graybox, blackbox |
| `name` | `string` |  | N |  | the name of the module of the source package |
| `fuzzRepo` | `abfuzz.Repository` |  | N |  | the repository information for the fuzz code reposisity in the source package |
| `sourceRepo` | `abfuzz.Repository` |  | N |  | the repository information for the main code reposisity of custom in the source package |
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `fileSet` | `Array<abfuzz.StorageFile>` |  | N |  | the file set for the source package |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary or raw source code package. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |
| `customVersion` | `string` |  | N |  | more version info for the custom system |
| `codeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `createTime` | `string` | `Timestamp` | N |  | create time of the source code package |
| `updateTime` | `string` | `Timestamp` | N |  | update time of the source code package |


#### `abfuzz.StorageFile`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  | 文件在minio上的 地址 |
| `checksum` | `string` | `Checksum` | N |  | 文件的hash校验码 |
| `type` | `string` |  | N |  | 文件类型，比如： `corpus`, `harness`, `source`, `build-script`, `openapi`, `jar` |
| `group` | `string` |  | N |  | 文件属于某个组，可以是某个fuzzer |


#### `abfuzz.TeardownStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


#### `mojo.http.Request`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `method` | `string` |  | N |  | method specifies the HTTP method (GET, POST, PUT, etc.).For client requests, an empty string means GET. |
| `url` | `string` | `Url` | N |  | url specifies either the URI being requested (for serverrequests) or the URL to access (for client requests). |
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Response`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |


#### `mojo.http.Status`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `integer` | `Int32` | N |  |
| `reason` | `string` |  | N |  |


#### `mojo.http.Version`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `major` | `integer` | `Int32` | N |  |
| `minor` | `integer` | `Int32` | N |  |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | project id |
| `name` | `string` |  | N |  | project name, normally the same with the custom's main source repo |
| `description` | `string` |  | N |  | project description |
| `sourcePackage` | `abfuzz.SourcePackage` |  | N |  | the source package for the project, only used in the create project |
| `language` | `string` |  | N |  | the program languages for the project，如果有多种语言，那么fuzzer中就需要指定语言 |
| `userId` | `string` |  | N |  | 用户相关的Project的用户ID |
| `externalId` | `string` |  | N |  | 第三方系统对应project的id |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | the sanitizers will be used for the project |
| `engines` | `Array<abfuzz.Engine>` |  | Y |  | the fazz engine name |
| `platforms` | `Array<string>` |  | N |  | The platform that this job can run on. |
| `langVms` | `Array<abfuzz.LangVM>` |  | N |  | The lang_vm platform that this job can run on if the language is Java. |
| `fuzzers` | `Array<abfuzz.Fuzzer>` |  | Y |  | the fuzzers in the project |
| `createTime` | `string` | `Timestamp` | N |  | the create time of the project |
| `updateTime` | `string` | `Timestamp` | N |  | the update time of the project |
| `deleteTime` | `string` | `Timestamp` | N |  | the delete time of the project, for the soft delete |


#### `abfuzz.CaseStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `disable` | `array` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.ContextCase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `steps` | `Array<abfuzz.CaseStep>` |  | N |  |


#### `abfuzz.ContextRequest`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `setup` | `abfuzz.ContextSetup` |  | N |  | 全局设置，在cases之前执行 |
| `cases` | `Array<abfuzz.ContextCase>` |  | N |  | OpenAPI里的请求(用path和method来对应)，包含在cases里的优先执行，没有包含在cases里的请求，每个请求自动生成一个case，在cases之后依次自行 |
| `teardown` | `abfuzz.ContextTeardown` |  | N |  | 在cases之后执行的请求 |


#### `abfuzz.ContextSetup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.SetupStep>` |  | N |  |


#### `abfuzz.ContextTeardown`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.TeardownStep>` |  | N |  |


#### `abfuzz.CorpusConfig`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `includings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `excludings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `httpBaseUrl` | `string` | `Url` | N |  | URl type<br>Uniform Resource Identifier (URI) is a string of characters used to identify a resource.<br>A Uniform Resource Name (URN) may be compared to a person's name,while a Uniform Resource Locator (URL) may be compared to their street address.In other words, a URN identifies an item and a URL provides a method for finding it.<br> |
| `httpMappingPort` | `integer` | `Int32` | N |  |
| `httpMappingHost` | `string` |  | N |  |
| `httpStaticValues` | `mojo.core.Object` |  | N |  | Object type |
| `httpCorpuses` | `Array<abfuzz.HttpCorpus>` |  | N |  |
| `combinedApiPath` | `string` |  | N |  |
| `asyncApi` | `boolean` |  | N |  |


#### `abfuzz.EndPoint`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` |  | N |  |
| `http` | `abfuzz.EndPoint.Http` |  | N |  |  |
| `priority` | `integer` | `Int32` | N |  |


#### `abfuzz.EndPoint.Http`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `path` | `string` |  | N |  |
| `methods` | `Array<string>` |  | N |  |


#### `abfuzz.Engine`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the engine |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.Entity`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `stepType` | `string` |  | N |  |
| `interfaces` | `Array<abfuzz.Entity.Interface>` |  | N |  |


#### `abfuzz.Entity.Interface`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `type` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | N |  |
| `pathParams` | `Map<string, string>` |  | N |  |
| `queryParams` | `Map<string, string>` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `response` | `mojo.http.Response` |  | N |  | Response represents the response from an HTTP request. |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |


#### `abfuzz.Fixup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.Fuzzer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | Y |  | the fuzzer name |
| `group` | `string` |  | N |  | the group id for the fuzzers |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `fuzzerPackage` | `abfuzz.FuzzerPackage` |  | N |  | the fuzzer package of the fuzzer |
| `executablePath` | `string` |  | N |  | Fuzzer's main executable path, relative to root. |
| `executableArguments` | `Array<string>` |  | N |  | the input arguments for the executable, it will be skipped when launcher_script is set. |
| `executableEnvironment` | `Map<string, string>` |  | N |  | the environment setting for the executable |
| `launcherScript` | `string` |  | N |  | Custom script that should be used to launch executable for this fuzzer.It will not try to launch the target if "@service-auto-start" set. |
| `timeout` | `integer` | `Int64` | N |  | Fuzzing timeout. |
| `testcaseTimeoutMillis` | `integer` | `Int64` | N |  | Testcase timeout. |
| `autoStop` | `boolean` |  | N |  | Fuzzing automatic stop. |
| `autoHarness` | `boolean` |  | N |  | auto generate harness |
| `customMutatorPlugin` | `string` |  | N |  | custom mutator plugin for Fuzz Engine |
| `customAllowTargets` | `Array<string>` |  | N |  | custom file name & function name targets for instruments and coverage, like white list. |
| `customDenyTargets` | `Array<string>` |  | N |  | custom targets for instruments and coverage, like white list. |
| `context` | `abfuzz.ContextRequest` |  | N |  | context request |
| `corpusPath` | `string` |  | N |  | the special corpus path |
| `preprocessScript` | `string` |  | N |  | the preprocess script info |
| `onlyFunctionalTest` | `boolean` |  | N |  | is only fuzzing user corpus |
| `autoHarnessCommand` | `string` |  | N |  | execute the command before run auto harness generation |
| `autoHarnessScript` | `string` |  | N |  | execute the script before run auto harness generation if the command is empty |
| `autoHarnessArguments` | `Array<string>` |  | N |  | the arguments when to run the harness generation, usually for including path setting |
| `autoHarnessBlacklist` | `Array<string>` |  | N |  | the blacklist files to run the harness generation, these files will be skipped. |
| `autoHarnessWebServer` | `boolean` |  | N |  | determine if the java auto harness server is a web server |
| `metadata` | `Map<string, mojo.core.Value>` |  | N |  | the meta information for the fuzzing |
| `corpus` | `abfuzz.CorpusConfig` |  | N |  |  |
| `entities` | `Array<abfuzz.Entity>` |  | N |  |


#### `abfuzz.FuzzerPackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `type` | `string` |  | N |  | the type of the source package, oneof the whitebox, greybox, blackbox |
| `filename` | `string` |  | N |  | Filename for the custom binary. |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |


#### `abfuzz.HttpCorpus`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | Y |  |
| `staticValues` | `mojo.core.Object` |  | N |  | Object type |
| `fixups` | `Array<abfuzz.Fixup>` |  | N |  |
| `relations` | `Array<abfuzz.Relation>` |  | N |  |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `abfuzz.Relation`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.Repository`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `url` | `string` | `Url` | N |  | the git url for the source package |
| `branch` | `string` |  | N |  | the git branch for the source package |
| `tag` | `string` |  | N |  | the git tag for the source package |
| `commitTime` | `string` | `Timestamp` | N |  | the used commit time for the source package |
| `commitKey` | `string` |  | N |  | the used commit key for the source package, it is hash key for git |


#### `abfuzz.Sanitizer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the sanitizer |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.SetupStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | 更新上下文时发起的请求，不要求包含在OpenAPI中 |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |
| `filter` | `Array<string>` |  | N |  |
| `order` | `string` |  | N |  |


#### `abfuzz.SourcePackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the uuid for the source package |
| `projectId` | `string` |  | N |  | the project id which including the source package |
| `projectName` | `string` |  | N |  | the project name which including the source package |
| `projectVersion` | `string` |  | N |  | the project version |
| `type` | `string` |  | N |  | the type of the source packagewhitebox, graybox, blackbox |
| `name` | `string` |  | N |  | the name of the module of the source package |
| `fuzzRepo` | `abfuzz.Repository` |  | N |  | the repository information for the fuzz code reposisity in the source package |
| `sourceRepo` | `abfuzz.Repository` |  | N |  | the repository information for the main code reposisity of custom in the source package |
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `fileSet` | `Array<abfuzz.StorageFile>` |  | N |  | the file set for the source package |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary or raw source code package. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |
| `customVersion` | `string` |  | N |  | more version info for the custom system |
| `codeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `createTime` | `string` | `Timestamp` | N |  | create time of the source code package |
| `updateTime` | `string` | `Timestamp` | N |  | update time of the source code package |


#### `abfuzz.StorageFile`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  | 文件在minio上的 地址 |
| `checksum` | `string` | `Checksum` | N |  | 文件的hash校验码 |
| `type` | `string` |  | N |  | 文件类型，比如： `corpus`, `harness`, `source`, `build-script`, `openapi`, `jar` |
| `group` | `string` |  | N |  | 文件属于某个组，可以是某个fuzzer |


#### `abfuzz.TeardownStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


#### `mojo.http.Request`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `method` | `string` |  | N |  | method specifies the HTTP method (GET, POST, PUT, etc.).For client requests, an empty string means GET. |
| `url` | `string` | `Url` | N |  | url specifies either the URI being requested (for serverrequests) or the URL to access (for client requests). |
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Response`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `status` | `mojo.http.Status` |  | N |  |  |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Status`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `integer` | `Int32` | N |  |
| `reason` | `string` |  | N |  |


#### `mojo.http.Version`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `major` | `integer` | `Int32` | N |  |
| `minor` | `integer` | `Int32` | N |  |


## 创建 Fuzzing

### 请求路径
```http
POST /abfuzz/v1/projects/{fuzzing_project_id}/fuzzings
```


### 请求参数

#### Body 请求对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | Additionally allows '.' and '@' over NAME_CHECK_REGEX.VALID_NAME_REGEX: re.compile(r'^[a-zA-Z0-9_@.-]+$') |
| `name` | `string` |  | N |  | Fuzzer Name. |
| `group` | `string` |  | N |  | the group id for the fuzzers |
| `projectId` | `string` |  | N |  | Project id |
| `projectName` | `string` |  | N |  | project name of the fuzzing |
| `state` | `string` |  | N |  | the state of the Fuzzing |
| `buildOperationName` | `string` |  | N |  | the build operation name |
| `buildId` | `string` |  | N |  | the build id |
| `operationName` | `string` |  | N |  | the operation name of the fuzzing |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | the sanitizers will be used for the project |
| `engine` | `abfuzz.Engine` |  | N |  | the fuzz engine name |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `sourcePackageId` | `string` |  | N |  | the source package id for the fuzzer |
| `sourcePackageCodeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `sourcePackageVersion` | `string` |  | N |  | 源代码的版本信息 |
| `sourcePackage` | `abfuzz.SourcePackage` |  | N |  | the source package for the fuzzer |
| `fuzzerPackage` | `abfuzz.FuzzerPackage` |  | N |  | the fuzzer package of the fuzzer |
| `coveragePackage` | `abfuzz.CoveragePackage` |  | N |  | the fuzzer package of the fuzzer |
| `executablePath` | `string` |  | N |  | Fuzzer's main executable path, relative to root. |
| `executableArguments` | `Array<string>` |  | N |  | the input arguments for the executable, it will be skipped when launcher_script is set. |
| `executableEnvironment` | `Map<string, string>` |  | N |  | the environment setting for the executable |
| `launcherScript` | `string` |  | N |  | Custom script that should be used to launch executable for this fuzzer.It will not try to launch the target if "@service-auto-start" set. |
| `timeout` | `integer` | `Int64` | N |  | Fuzzing timeout. |
| `testcaseTimeoutMillis` | `integer` | `Int64` | N |  | Testcase timeout. |
| `maxTestcases` | `integer` | `Int64` | N |  | Max testcases to generate for this fuzzer. |
| `additionalEnvironmentString` | `string` |  | N |  | Additional environment variables that need to be set for this fuzzer. |
| `statsColumns` | `string` |  | N |  | Column specification for stats. |
| `statsColumnDescriptions` | `string` |  | N |  | Helpful descriptions for the stats_columns. In a yaml format. |
| `builtin` | `boolean` |  | N |  | Whether this is a builtin fuzzer. |
| `autoStop` | `boolean` |  | N |  | Fuzzing automatic stop. |
| `autoHarness` | `boolean` |  | N |  | auto generate harness |
| `customMutatorPlugin` | `string` |  | N |  | custom mutator plugin for Fuzz Engine |
| `customAllowTargets` | `Array<string>` |  | N |  | custom file name & function name targets for instruments and coverage, like white list. |
| `customDenyTargets` | `Array<string>` |  | N |  | custom targets for instruments and coverage, like white list. |
| `context` | `abfuzz.ContextRequest` |  | N |  | context request |
| `dockerImage` | `string` |  | N |  | the specific docker image for the fuzzing |
| `corpusPath` | `string` |  | N |  | the special corpus path |
| `result` | `string` |  | N |  | Result from the last fuzzer run showing the number of testcases generated. |
| `resultUpdateTime` | `string` | `Timestamp` | N |  | Last result update timestamp. |
| `returnCode` | `integer` | `Int64` | N |  | Return code from last fuzzer run. |
| `consoleOutput` | `string` |  | N |  | Console output from last fuzzer run. |
| `sampleTestcase` | `abfuzz.StorageFile` |  | N |  | storage file for the sample testcase generated by the fuzzer. |
| `testcases` | `Array<abfuzz.Testcase>` |  | N |  | generated test cases |
| `coverages` | `Array<abfuzz.Coverage>` |  | N |  | 覆盖率信息 |
| `coverageUrl` | `string` |  | N |  | 覆盖率信息的url |
| `onlyFunctionTest` | `boolean` |  | N |  | 是否只进行功能测试 |
| `coverageMaps` | `Array<abfuzz.CoverageMap>` |  | N |  | 覆盖率图谱 |
| `fuzzStats` | `abfuzz.FuzzStats` |  | N |  | AFL相关的统计 |
| `inputPackage` | `string` | `Url` | N |  | 对coverage有提升的输入集合包 |
| `preprocessScript` | `string` |  | N |  | preprocess script ,process before aboroxy do reuqest |
| `durationSeconds` | `integer` | `Int64` | N |  | the duration of the fuzzing real running in seconds |
| `metadata` | `Map<string, mojo.core.Value>` |  | N |  | the meta information for the fuzzing |
| `createTime` | `string` | `Timestamp` | N |  | the created time of the fuzzing |
| `updateTime` | `string` | `Timestamp` | N |  | Last update time. |
| `corpus` | `mojo.core.Object` |  | N |  | Object type |


#### `abfuzz.CaseStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `disable` | `array` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.ContextCase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `steps` | `Array<abfuzz.CaseStep>` |  | N |  |


#### `abfuzz.ContextRequest`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `setup` | `abfuzz.ContextSetup` |  | N |  | 全局设置，在cases之前执行 |
| `cases` | `Array<abfuzz.ContextCase>` |  | N |  | OpenAPI里的请求(用path和method来对应)，包含在cases里的优先执行，没有包含在cases里的请求，每个请求自动生成一个case，在cases之后依次自行 |
| `teardown` | `abfuzz.ContextTeardown` |  | N |  | 在cases之后执行的请求 |


#### `abfuzz.ContextSetup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.SetupStep>` |  | N |  |


#### `abfuzz.ContextTeardown`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.TeardownStep>` |  | N |  |


#### `abfuzz.Coverage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `element` | `string` |  | N |  | 目标的路径，可以是包、文件、类、函数 |
| `name` | `string` |  | N |  | 目标的名称，不包含路径信息，如果是函数，只是函数名；如果是类，只是类名 |
| `kind` | `string` |  | N |  | 目标的类型: package, file, class, function |
| `hash` | `integer` | `Int64` | N |  | 指定目标的hash值 |
| `lines` | `abfuzz.Coverage.Count` |  | N |  | 行覆盖率，只要本行有一条指令被执行，则本行则被标记为被执行。 |
| `functions` | `abfuzz.Coverage.Count` |  | N |  | 方法覆盖率，任何非抽象的方法，只要有一条指令被执行，则该方法就会被计为被执行。 |
| `classes` | `abfuzz.Coverage.Count` |  | N |  | 类覆盖率，所有类，包括接口，只要其中有一个方法被执行，则标记为被执行。注意：构造函数和静态初始化块也算作方法。 |
| `instructions` | `abfuzz.Coverage.Count` |  | N |  | Jacoco计算的最小单位就是字节码指令。指令覆盖率表明了在所有的指令中，哪些被指令过以及哪些没有被执行。这项指数完全独立于源码格式并且在任何情况下有效，不需要类文件的调试信息。 |
| `branches` | `abfuzz.Coverage.Count` |  | N |  | 对所有的if和switch指令计算了分支覆盖率。这项指标会统计所有的分支数量，并同时支出哪些分支被执行，哪些分支没有被执行。这项指标也在任何情况都有效。异常处理不考虑在分支范围内。 |
| `cxty` | `abfuzz.Coverage.Count` |  | N |  | Cyclomatic Complexity): 圈复杂度会为每一个非抽象方法计算圈复杂度，并为类，包以及组（groups）计算复杂度。圈复杂度简单地说就是为了覆盖所有路径，所需要执行单元测试数量，圈复杂度大说明程序代码可能质量低且难于测试和维护。 |
| `edges` | `abfuzz.Coverage.Count` |  | N |  | 边覆盖 |
| `linesInfo` | `abfuzz.Coverage.LinesInfo` |  | N |  | 在源文件内详细的行覆盖信息，在kind为file时才会有 |
| `branchInfoes` | `Array<abfuzz.Coverage.BranchInfo>` |  | N |  | 在源文件内详细的分支覆盖信息，在kind为file时才会有 |
| `functionInfoes` | `Array<abfuzz.Coverage.FunctionInfo>` |  | N |  | 在源文件内详细的函数覆盖信息，在kind为file时才会有 |
| `classInfoes` | `Array<abfuzz.Coverage.ClassInfo>` |  | N |  | 在源文件内详细的类覆盖信息，在kind为file时才会有 |


#### `abfuzz.Coverage.BranchInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `startLine` | `integer` | `Int32` | N |  | 分支起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 分支起始列 |
| `destLine` | `integer` | `Int32` | N |  | 分支跳转至的行 |
| `attribute` | `integer` | `Int32` | N |  | 分支属性 |
| `executedCount` | `integer` | `Int32` | N |  | 分支的执行数 |


#### `abfuzz.Coverage.ClassInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | 类名称 |
| `startLine` | `integer` | `Int32` | N |  | 类的起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 类的起始列 |
| `endLine` | `integer` | `Int32` | N |  | 类结束行 |
| `endColumn` | `integer` | `Int32` | N |  | 类结束行 |
| `attribute` | `integer` | `Int32` | N |  | 类属性 |
| `executedCount` | `integer` | `Int32` | N |  | 类执行数 |


#### `abfuzz.Coverage.Count`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `hit` | `integer` | `Int64` | N |  | 覆盖到的元素中的计数 |
| `total` | `integer` | `Int64` | N |  | 元素中的总计数值 |


#### `abfuzz.Coverage.FunctionInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | 函数名称 |
| `startLine` | `integer` | `Int32` | N |  | 函数的起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 函数的起始列 |
| `endLine` | `integer` | `Int32` | N |  | 函数结束行 |
| `endColumn` | `integer` | `Int32` | N |  | 函数结束行 |
| `attribute` | `integer` | `Int32` | N |  | 函数属性 |
| `executedCount` | `integer` | `Int32` | N |  | 函数执行数 |


#### `abfuzz.Coverage.LinesInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `start` | `integer` | `Int32` | N |  | 起始行 |
| `attributes` | `Array<integer>` |  | N |  | 每行的属性值 |
| `executedCounts` | `Array<integer>` |  | N |  | 每行的执行数 |


#### `abfuzz.CoverageMap`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `node` | `string` |  | N |  |
| `childNode` | `string` |  | N |  |
| `uri` | `string` |  | N |  |


#### `abfuzz.CoveragePackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |


#### `abfuzz.CpuStat`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `number` | `integer` | `Int32` | N |  | CPU编号 |
| `state` | `string` |  | N |  | CPU的状态 AVAILABLE， CAUTION |


#### `abfuzz.Engine`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the engine |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.FuzzStats`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `projectId` | `string` |  | N |  |
| `sourcePackageId` | `string` |  | N |  |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `buildId` | `string` |  | N |  | the build id |
| `operationName` | `string` |  | N |  | the operation name of the fuzzing |
| `fuzzingId` | `string` |  | N |  |
| `fuzzerName` | `string` |  | N |  |
| `cyclesDone` | `integer` | `Int32` | N |  |
| `execsDone` | `integer` | `Int32` | N |  | 总共完成的执行数 |
| `execsSpeed` | `number` | `Float32` | N |  | 每秒的执行次数 |
| `cpuStats` | `Array<abfuzz.CpuStat>` |  | N |  | CPU的状态 |
| `pathsTotal` | `integer` | `Int32` | N |  | 执行边的总数 |
| `pathsFavored` | `integer` | `Int32` | N |  |
| `pathsFound` | `integer` | `Int32` | N |  | 发现的边的总数 |
| `pathsImported` | `integer` | `Int32` | N |  |
| `pathsMaxDepth` | `integer` | `Int32` | N |  |
| `pathsPendingFavored` | `integer` | `Int32` | N |  |
| `pathsPendingTotal` | `integer` | `Int32` | N |  |
| `pathsVariable` | `integer` | `Int32` | N |  |
| `pathsStability` | `number` | `Float32` | N |  |
| `bitmapCoverage` | `number` | `Float32` | N |  | bitmap覆盖率 |
| `uniqueCrashes` | `integer` | `Int32` | N |  | 独立的crash的总数 |
| `uniqueHangs` | `integer` | `Int32` | N |  | 独立的hang的总数 |
| `lastNewPath` | `string` | `Duration` | N |  | 最近一次新path的持续时间 |
| `lastUniqueCrash` | `string` | `Duration` | N |  | 最近一次发现独立crash的持续时间 |
| `lastUniqueHang` | `string` | `Duration` | N |  | 最近一次发现独立hang的持续时间 |
| `execsSinceCrash` | `integer` | `Int32` | N |  | 从上次unique的crash的执行数 |
| `execTimeout` | `integer` | `Int32` | N |  |
| `createTime` | `string` | `Timestamp` | N |  |  |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.FuzzerPackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `type` | `string` |  | N |  | the type of the source package, oneof the whitebox, greybox, blackbox |
| `filename` | `string` |  | N |  | Filename for the custom binary. |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |


#### `abfuzz.Impact`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `versionIndices` | `Array<string>` |  | N |  | The impacted version indices (including beta, stable and extended_stable). |
| `extendedStableVersion` | `string` |  | N |  | The impacted extended stable version. |
| `extendedStableVersionIndices` | `Array<string>` |  | N |  | The impacted extended stable version indices. |
| `extendedStableVersionLikely` | `boolean` |  | N |  | The impacted extended stable version is merely probable (not definite).See the comment on stable_version_likely. |
| `stableVersion` | `string` |  | N |  | The impacted stable version. |
| `stableVersionIndices` | `Array<string>` |  | N |  | The impacted stable version indices. |
| `stableVersionLikely` | `boolean` |  | N |  | The impacted stable version is merely probable (not definite). Becausefor a non-asan build, we don't have a stable/beta build. Therefore, wemake an intelligent guess on the version. |
| `betaVersion` | `string` |  | N |  | The impacted beta version. |
| `betaVersionIndices` | `Array<string>` |  | N |  | The impacted beta version indices. |
| `betaVersionLikely` | `boolean` |  | N |  | The impacted beta version is merely probable (not definite). See thecomment on stable_version_likely. |
| `headVersion` | `string` |  | N |  | The impacted 'head' version |
| `headVersionIndices` | `Array<string>` |  | N |  | The impacted head version indices. |
| `headVersionLikely` | `boolean` |  | N |  | The impacted head version is likely. |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `abfuzz.Repository`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `url` | `string` | `Url` | N |  | the git url for the source package |
| `branch` | `string` |  | N |  | the git branch for the source package |
| `tag` | `string` |  | N |  | the git tag for the source package |
| `commitTime` | `string` | `Timestamp` | N |  | the used commit time for the source package |
| `commitKey` | `string` |  | N |  | the used commit key for the source package, it is hash key for git |


#### `abfuzz.Sanitizer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the sanitizer |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.SetupStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | 更新上下文时发起的请求，不要求包含在OpenAPI中 |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |
| `filter` | `Array<string>` |  | N |  |
| `order` | `string` |  | N |  |


#### `abfuzz.SourcePackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the uuid for the source package |
| `projectId` | `string` |  | N |  | the project id which including the source package |
| `projectName` | `string` |  | N |  | the project name which including the source package |
| `projectVersion` | `string` |  | N |  | the project version |
| `type` | `string` |  | N |  | the type of the source packagewhitebox, graybox, blackbox |
| `name` | `string` |  | N |  | the name of the module of the source package |
| `fuzzRepo` | `abfuzz.Repository` |  | N |  | the repository information for the fuzz code reposisity in the source package |
| `sourceRepo` | `abfuzz.Repository` |  | N |  | the repository information for the main code reposisity of custom in the source package |
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `fileSet` | `Array<abfuzz.StorageFile>` |  | N |  | the file set for the source package |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary or raw source code package. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |
| `customVersion` | `string` |  | N |  | more version info for the custom system |
| `codeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `createTime` | `string` | `Timestamp` | N |  | create time of the source code package |
| `updateTime` | `string` | `Timestamp` | N |  | update time of the source code package |


#### `abfuzz.StorageFile`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  | 文件在minio上的 地址 |
| `checksum` | `string` | `Checksum` | N |  | 文件的hash校验码 |
| `type` | `string` |  | N |  | 文件类型，比如： `corpus`, `harness`, `source`, `build-script`, `openapi`, `jar` |
| `group` | `string` |  | N |  | 文件属于某个组，可以是某个fuzzer |


#### `abfuzz.TeardownStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.Testcase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | id for the testcase |
| `description` | `string` |  | N |  | description for the testcase |
| `keywords` | `Array<string>` |  | N |  | keywords is used for searching. |
| `securityFlag` | `boolean` |  | N |  | Is it a security bug ? |
| `securitySeverity` | `integer` | `Int64` | N |  | Security severity of the bug. |
| `oneTimeCrasherFlag` | `boolean` |  | N |  | Did the bug only reproduced once ? |
| `comments` | `string` |  | N |  | Any additional comments. |
| `projectId` | `string` |  | N |  | Project id associated with this test case. |
| `projectName` | `string` |  | N |  | Project name associated with this test case. |
| `sourcePackageId` | `string` |  | N |  | the source package id for the testcase |
| `fuzzerName` | `string` |  | N |  | Name of the fuzzer used to generate this testcase. |
| `fuzzerNameIndices` | `Array<string>` |  | N |  | Fuzzer name indices |
| `overriddenFuzzerName` | `string` |  | N |  | Overridden fuzzer name because actual fuzzer name can be different in manyscenarios (libfuzzer, afl, etc). |
| `fuzzingId` | `string` |  | N |  | the fuzzing which is the testcase generated from |
| `buildOperationName` | `string` |  | N |  | the build operation name of the testcase |
| `operationName` | `string` |  | N |  | the operation name of the testcase when fuzzing |
| `buildId` | `string` |  | N |  | the build id |
| `platform` | `string` | `Platform` | N |  | Platforms (e.g. windows, linux, android). |
| `language` | `string` |  | N |  | the program language for the fuzzing |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `otherPlatforms` | `Array<string>` |  | N |  | Platforms (e.g. windows, linux, android). |
| `otherLangVms` | `Array<abfuzz.LangVM>` |  | N |  | 指定的JDK，如果是聚合后的testcase，会包含多个jdk版本 |
| `engines` | `Array<abfuzz.Engine>` |  | N |  | engine names for the testcase |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | 指定的sanitizers |
| `fuzzedIds` | `Array<string>` |  | N |  | Blobstore keys for various things like original testcase, minimizedtestcase, etc. |
| `minimizedIds` | `Array<string>` |  | N |  |
| `minidumpIds` | `Array<string>` |  | N |  |
| `status` | `string` |  | N |  | Status of this testcase (pending, processed, unreproducible, etc). |
| `open` | `boolean` |  | N |  | Whether testcase is open. |
| `duplicateOf` | `integer` | `Int64` | N |  | Id of the testcase that this is marked as a duplicate of. |
| `groupId` | `integer` | `Int64` | N |  | Id for this testcase's associated group. |
| `groupBugInformation` | `integer` | `Int64` | N |  | Tracking issue tracker bug for this testcase group. |
| `isLeader` | `boolean` |  | N |  | Whether or not a testcase is the leader of its group.If the testcase is not in a group, it's the leader of a group of 1.The default is false because we prefer not to show crashes until we aresure. And group_task will correctly set the value within 30 minutes. |
| `bugInformation` | `string` |  | N |  | Tracking issue tracker bug. One bug number per line (future extension). |
| `isADuplicateFlag` | `boolean` |  | N |  | Whether or not a testcase is a duplicate of other testcase. |
| `hasBugFlag` | `boolean` |  | N |  | Whether testcase has a bug (either bug_information orgroup_bug_information). |
| `bugIndices` | `Array<string>` |  | N |  | Indices for bug_information and group_bug_information. |
| `triaged` | `boolean` |  | N |  | Boolean attribute indicating if cleanup triage needs to be done. |
| `additionalMetadata` | `mojo.core.Object` |  | N |  | Additional metadata stored as a JSON object. This should be used forproperties that are not commonly accessed and do not need to be indexed. |
| `crashType` | `string` |  | N |  | Crash on an invalid read/write. |
| `crashDescription` | `string` |  | N |  | Crash description for runtime error |
| `crashAddress` | `string` |  | N |  | Crashing address. |
| `crashState` | `string` |  | N |  | First x stack frames. |
| `crashStacktrace` | `string` |  | N |  | Complete stacktrace. |
| `lastTestedCrashStacktrace` | `string` |  | N |  | Last tested crash stacktrace using the latest revision. |
| `crashRevision` | `integer` | `Int64` | N |  | Revision that we discovered the crash in. |
| `crashSourceFileName` | `string` |  | N |  | The source file name, may be omitted if debug symbols are not available. |
| `crashSourceLine` | `integer` | `Int32` | N |  | The (1-based) source line number, may be omitted if debug symbols arenot available. |
| `crashFunctionName` | `string` |  | N |  | The function name, may be omitted if debug symbols are not available. |
| `symbolized` | `boolean` |  | N |  | Flag indicating whether or not the testcase has been symbolized. |
| `input` | `abfuzz.Testcase.Input` |  | N |  | The input for the testcase crashing |
| `targetServerLogPath` | `string` |  | N |  | The target server log path with java testcase |
| `absolutePath` | `string` |  | N |  | The file on the agent that generated the testcase. |
| `binaryFlag` | `boolean` |  | N |  | Is this a binary file? |
| `flakyStack` | `boolean` |  | N |  | Does the testcase crash stack vary b/w crashes ? |
| `httpFlag` | `boolean` |  | N |  | Do we need to test this testcase using an HTTP/HTTPS server? |
| `gestures` | `Array<string>` |  | N |  | Fake user interaction sequences like key clicks, mouse movements, etc. |
| `redzone` | `integer` | `Int64` | N |  | ASAN redzone size in bytes. |
| `disableUbsan` | `boolean` |  | N |  | Flag indicating if UBSan detection should be disabled. This is needed forcases when ASan and UBSan are bundled in the same build configurationand we need to disable UBSan in some runs to find the potentially moreinteresting ASan bugs. |
| `timeoutMultiplier` | `number` | `Float64` | N |  | Adjusts timeout based on multiplier value. |
| `regression` | `string` |  | N |  | Regression range. |
| `fixed` | `string` |  | N |  | Revisions where this issue has been fixed. |
| `impactIndices` | `Array<string>` |  | N |  | Impact indices for searching. |
| `impact` | `abfuzz.Impact` |  | N |  | 用例影响源代码的版本信息 |
| `isImpactSetFlag` | `boolean` |  | N |  | Whether or not impact task has been run on this testcase. |
| `uploaderEmail` | `string` |  | N |  | Uploader email address. |
| `createTime` | `string` | `Timestamp` | N |  | Timestamp. |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.Testcase.Input`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  | binaray, file, json |
| `content` | `mojo.core.Value` |  | N |  | the input content |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


#### `mojo.http.Request`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `method` | `string` |  | N |  | method specifies the HTTP method (GET, POST, PUT, etc.).For client requests, an empty string means GET. |
| `url` | `string` | `Url` | N |  | url specifies either the URI being requested (for serverrequests) or the URL to access (for client requests). |
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Version`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `major` | `integer` | `Int32` | N |  |
| `minor` | `integer` | `Int32` | N |  |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | Additionally allows '.' and '@' over NAME_CHECK_REGEX.VALID_NAME_REGEX: re.compile(r'^[a-zA-Z0-9_@.-]+$') |
| `name` | `string` |  | N |  | Fuzzer Name. |
| `group` | `string` |  | N |  | the group id for the fuzzers |
| `projectId` | `string` |  | N |  | Project id |
| `projectName` | `string` |  | N |  | project name of the fuzzing |
| `state` | `string` |  | N |  | the state of the Fuzzing |
| `buildOperationName` | `string` |  | N |  | the build operation name |
| `buildId` | `string` |  | N |  | the build id |
| `operationName` | `string` |  | N |  | the operation name of the fuzzing |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | the sanitizers will be used for the project |
| `engine` | `abfuzz.Engine` |  | N |  | the fuzz engine name |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `sourcePackageId` | `string` |  | N |  | the source package id for the fuzzer |
| `sourcePackageCodeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `sourcePackageVersion` | `string` |  | N |  | 源代码的版本信息 |
| `sourcePackage` | `abfuzz.SourcePackage` |  | N |  | the source package for the fuzzer |
| `fuzzerPackage` | `abfuzz.FuzzerPackage` |  | N |  | the fuzzer package of the fuzzer |
| `coveragePackage` | `abfuzz.CoveragePackage` |  | N |  | the fuzzer package of the fuzzer |
| `executablePath` | `string` |  | N |  | Fuzzer's main executable path, relative to root. |
| `executableArguments` | `Array<string>` |  | N |  | the input arguments for the executable, it will be skipped when launcher_script is set. |
| `executableEnvironment` | `Map<string, string>` |  | N |  | the environment setting for the executable |
| `launcherScript` | `string` |  | N |  | Custom script that should be used to launch executable for this fuzzer.It will not try to launch the target if "@service-auto-start" set. |
| `timeout` | `integer` | `Int64` | N |  | Fuzzing timeout. |
| `testcaseTimeoutMillis` | `integer` | `Int64` | N |  | Testcase timeout. |
| `maxTestcases` | `integer` | `Int64` | N |  | Max testcases to generate for this fuzzer. |
| `additionalEnvironmentString` | `string` |  | N |  | Additional environment variables that need to be set for this fuzzer. |
| `statsColumns` | `string` |  | N |  | Column specification for stats. |
| `statsColumnDescriptions` | `string` |  | N |  | Helpful descriptions for the stats_columns. In a yaml format. |
| `builtin` | `boolean` |  | N |  | Whether this is a builtin fuzzer. |
| `autoStop` | `boolean` |  | N |  | Fuzzing automatic stop. |
| `autoHarness` | `boolean` |  | N |  | auto generate harness |
| `customMutatorPlugin` | `string` |  | N |  | custom mutator plugin for Fuzz Engine |
| `customAllowTargets` | `Array<string>` |  | N |  | custom file name & function name targets for instruments and coverage, like white list. |
| `customDenyTargets` | `Array<string>` |  | N |  | custom targets for instruments and coverage, like white list. |
| `context` | `abfuzz.ContextRequest` |  | N |  | context request |
| `dockerImage` | `string` |  | N |  | the specific docker image for the fuzzing |
| `corpusPath` | `string` |  | N |  | the special corpus path |
| `result` | `string` |  | N |  | Result from the last fuzzer run showing the number of testcases generated. |
| `resultUpdateTime` | `string` | `Timestamp` | N |  | Last result update timestamp. |
| `returnCode` | `integer` | `Int64` | N |  | Return code from last fuzzer run. |
| `consoleOutput` | `string` |  | N |  | Console output from last fuzzer run. |
| `sampleTestcase` | `abfuzz.StorageFile` |  | N |  | storage file for the sample testcase generated by the fuzzer. |
| `testcases` | `Array<abfuzz.Testcase>` |  | N |  | generated test cases |
| `coverages` | `Array<abfuzz.Coverage>` |  | N |  | 覆盖率信息 |
| `coverageUrl` | `string` |  | N |  | 覆盖率信息的url |
| `onlyFunctionTest` | `boolean` |  | N |  | 是否只进行功能测试 |
| `coverageMaps` | `Array<abfuzz.CoverageMap>` |  | N |  | 覆盖率图谱 |
| `fuzzStats` | `abfuzz.FuzzStats` |  | N |  | AFL相关的统计 |
| `inputPackage` | `string` | `Url` | N |  | 对coverage有提升的输入集合包 |
| `preprocessScript` | `string` |  | N |  | preprocess script ,process before aboroxy do reuqest |
| `durationSeconds` | `integer` | `Int64` | N |  | the duration of the fuzzing real running in seconds |
| `metadata` | `Map<string, mojo.core.Value>` |  | N |  | the meta information for the fuzzing |
| `createTime` | `string` | `Timestamp` | N |  | the created time of the fuzzing |
| `updateTime` | `string` | `Timestamp` | N |  | Last update time. |
| `corpus` | `mojo.core.Object` |  | N |  | Object type |


#### `abfuzz.CaseStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `disable` | `array` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.ContextCase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `steps` | `Array<abfuzz.CaseStep>` |  | N |  |


#### `abfuzz.ContextRequest`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `setup` | `abfuzz.ContextSetup` |  | N |  | 全局设置，在cases之前执行 |
| `cases` | `Array<abfuzz.ContextCase>` |  | N |  | OpenAPI里的请求(用path和method来对应)，包含在cases里的优先执行，没有包含在cases里的请求，每个请求自动生成一个case，在cases之后依次自行 |
| `teardown` | `abfuzz.ContextTeardown` |  | N |  | 在cases之后执行的请求 |


#### `abfuzz.ContextSetup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.SetupStep>` |  | N |  |


#### `abfuzz.ContextTeardown`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.TeardownStep>` |  | N |  |


#### `abfuzz.Coverage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `element` | `string` |  | N |  | 目标的路径，可以是包、文件、类、函数 |
| `name` | `string` |  | N |  | 目标的名称，不包含路径信息，如果是函数，只是函数名；如果是类，只是类名 |
| `kind` | `string` |  | N |  | 目标的类型: package, file, class, function |
| `hash` | `integer` | `Int64` | N |  | 指定目标的hash值 |
| `lines` | `abfuzz.Coverage.Count` |  | N |  | 行覆盖率，只要本行有一条指令被执行，则本行则被标记为被执行。 |
| `functions` | `abfuzz.Coverage.Count` |  | N |  | 方法覆盖率，任何非抽象的方法，只要有一条指令被执行，则该方法就会被计为被执行。 |
| `classes` | `abfuzz.Coverage.Count` |  | N |  | 类覆盖率，所有类，包括接口，只要其中有一个方法被执行，则标记为被执行。注意：构造函数和静态初始化块也算作方法。 |
| `instructions` | `abfuzz.Coverage.Count` |  | N |  | Jacoco计算的最小单位就是字节码指令。指令覆盖率表明了在所有的指令中，哪些被指令过以及哪些没有被执行。这项指数完全独立于源码格式并且在任何情况下有效，不需要类文件的调试信息。 |
| `branches` | `abfuzz.Coverage.Count` |  | N |  | 对所有的if和switch指令计算了分支覆盖率。这项指标会统计所有的分支数量，并同时支出哪些分支被执行，哪些分支没有被执行。这项指标也在任何情况都有效。异常处理不考虑在分支范围内。 |
| `cxty` | `abfuzz.Coverage.Count` |  | N |  | Cyclomatic Complexity): 圈复杂度会为每一个非抽象方法计算圈复杂度，并为类，包以及组（groups）计算复杂度。圈复杂度简单地说就是为了覆盖所有路径，所需要执行单元测试数量，圈复杂度大说明程序代码可能质量低且难于测试和维护。 |
| `edges` | `abfuzz.Coverage.Count` |  | N |  | 边覆盖 |
| `linesInfo` | `abfuzz.Coverage.LinesInfo` |  | N |  | 在源文件内详细的行覆盖信息，在kind为file时才会有 |
| `branchInfoes` | `Array<abfuzz.Coverage.BranchInfo>` |  | N |  | 在源文件内详细的分支覆盖信息，在kind为file时才会有 |
| `functionInfoes` | `Array<abfuzz.Coverage.FunctionInfo>` |  | N |  | 在源文件内详细的函数覆盖信息，在kind为file时才会有 |
| `classInfoes` | `Array<abfuzz.Coverage.ClassInfo>` |  | N |  | 在源文件内详细的类覆盖信息，在kind为file时才会有 |


#### `abfuzz.Coverage.BranchInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `startLine` | `integer` | `Int32` | N |  | 分支起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 分支起始列 |
| `destLine` | `integer` | `Int32` | N |  | 分支跳转至的行 |
| `attribute` | `integer` | `Int32` | N |  | 分支属性 |
| `executedCount` | `integer` | `Int32` | N |  | 分支的执行数 |


#### `abfuzz.Coverage.ClassInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | 类名称 |
| `startLine` | `integer` | `Int32` | N |  | 类的起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 类的起始列 |
| `endLine` | `integer` | `Int32` | N |  | 类结束行 |
| `endColumn` | `integer` | `Int32` | N |  | 类结束行 |
| `attribute` | `integer` | `Int32` | N |  | 类属性 |
| `executedCount` | `integer` | `Int32` | N |  | 类执行数 |


#### `abfuzz.Coverage.Count`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `hit` | `integer` | `Int64` | N |  | 覆盖到的元素中的计数 |
| `total` | `integer` | `Int64` | N |  | 元素中的总计数值 |


#### `abfuzz.Coverage.FunctionInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | 函数名称 |
| `startLine` | `integer` | `Int32` | N |  | 函数的起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 函数的起始列 |
| `endLine` | `integer` | `Int32` | N |  | 函数结束行 |
| `endColumn` | `integer` | `Int32` | N |  | 函数结束行 |
| `attribute` | `integer` | `Int32` | N |  | 函数属性 |
| `executedCount` | `integer` | `Int32` | N |  | 函数执行数 |


#### `abfuzz.Coverage.LinesInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `start` | `integer` | `Int32` | N |  | 起始行 |
| `attributes` | `Array<integer>` |  | N |  | 每行的属性值 |
| `executedCounts` | `Array<integer>` |  | N |  | 每行的执行数 |


#### `abfuzz.CoverageMap`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `node` | `string` |  | N |  |
| `childNode` | `string` |  | N |  |
| `uri` | `string` |  | N |  |


#### `abfuzz.CoveragePackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |


#### `abfuzz.CpuStat`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `number` | `integer` | `Int32` | N |  | CPU编号 |
| `state` | `string` |  | N |  | CPU的状态 AVAILABLE， CAUTION |


#### `abfuzz.Engine`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the engine |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.FuzzStats`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `projectId` | `string` |  | N |  |
| `sourcePackageId` | `string` |  | N |  |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `buildId` | `string` |  | N |  | the build id |
| `operationName` | `string` |  | N |  | the operation name of the fuzzing |
| `fuzzingId` | `string` |  | N |  |
| `fuzzerName` | `string` |  | N |  |
| `cyclesDone` | `integer` | `Int32` | N |  |
| `execsDone` | `integer` | `Int32` | N |  | 总共完成的执行数 |
| `execsSpeed` | `number` | `Float32` | N |  | 每秒的执行次数 |
| `cpuStats` | `Array<abfuzz.CpuStat>` |  | N |  | CPU的状态 |
| `pathsTotal` | `integer` | `Int32` | N |  | 执行边的总数 |
| `pathsFavored` | `integer` | `Int32` | N |  |
| `pathsFound` | `integer` | `Int32` | N |  | 发现的边的总数 |
| `pathsImported` | `integer` | `Int32` | N |  |
| `pathsMaxDepth` | `integer` | `Int32` | N |  |
| `pathsPendingFavored` | `integer` | `Int32` | N |  |
| `pathsPendingTotal` | `integer` | `Int32` | N |  |
| `pathsVariable` | `integer` | `Int32` | N |  |
| `pathsStability` | `number` | `Float32` | N |  |
| `bitmapCoverage` | `number` | `Float32` | N |  | bitmap覆盖率 |
| `uniqueCrashes` | `integer` | `Int32` | N |  | 独立的crash的总数 |
| `uniqueHangs` | `integer` | `Int32` | N |  | 独立的hang的总数 |
| `lastNewPath` | `string` | `Duration` | N |  | 最近一次新path的持续时间 |
| `lastUniqueCrash` | `string` | `Duration` | N |  | 最近一次发现独立crash的持续时间 |
| `lastUniqueHang` | `string` | `Duration` | N |  | 最近一次发现独立hang的持续时间 |
| `execsSinceCrash` | `integer` | `Int32` | N |  | 从上次unique的crash的执行数 |
| `execTimeout` | `integer` | `Int32` | N |  |
| `createTime` | `string` | `Timestamp` | N |  |  |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.FuzzerPackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `type` | `string` |  | N |  | the type of the source package, oneof the whitebox, greybox, blackbox |
| `filename` | `string` |  | N |  | Filename for the custom binary. |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |


#### `abfuzz.Impact`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `versionIndices` | `Array<string>` |  | N |  | The impacted version indices (including beta, stable and extended_stable). |
| `extendedStableVersion` | `string` |  | N |  | The impacted extended stable version. |
| `extendedStableVersionIndices` | `Array<string>` |  | N |  | The impacted extended stable version indices. |
| `extendedStableVersionLikely` | `boolean` |  | N |  | The impacted extended stable version is merely probable (not definite).See the comment on stable_version_likely. |
| `stableVersion` | `string` |  | N |  | The impacted stable version. |
| `stableVersionIndices` | `Array<string>` |  | N |  | The impacted stable version indices. |
| `stableVersionLikely` | `boolean` |  | N |  | The impacted stable version is merely probable (not definite). Becausefor a non-asan build, we don't have a stable/beta build. Therefore, wemake an intelligent guess on the version. |
| `betaVersion` | `string` |  | N |  | The impacted beta version. |
| `betaVersionIndices` | `Array<string>` |  | N |  | The impacted beta version indices. |
| `betaVersionLikely` | `boolean` |  | N |  | The impacted beta version is merely probable (not definite). See thecomment on stable_version_likely. |
| `headVersion` | `string` |  | N |  | The impacted 'head' version |
| `headVersionIndices` | `Array<string>` |  | N |  | The impacted head version indices. |
| `headVersionLikely` | `boolean` |  | N |  | The impacted head version is likely. |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `abfuzz.Repository`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `url` | `string` | `Url` | N |  | the git url for the source package |
| `branch` | `string` |  | N |  | the git branch for the source package |
| `tag` | `string` |  | N |  | the git tag for the source package |
| `commitTime` | `string` | `Timestamp` | N |  | the used commit time for the source package |
| `commitKey` | `string` |  | N |  | the used commit key for the source package, it is hash key for git |


#### `abfuzz.Sanitizer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the sanitizer |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.SetupStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | 更新上下文时发起的请求，不要求包含在OpenAPI中 |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |
| `filter` | `Array<string>` |  | N |  |
| `order` | `string` |  | N |  |


#### `abfuzz.SourcePackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the uuid for the source package |
| `projectId` | `string` |  | N |  | the project id which including the source package |
| `projectName` | `string` |  | N |  | the project name which including the source package |
| `projectVersion` | `string` |  | N |  | the project version |
| `type` | `string` |  | N |  | the type of the source packagewhitebox, graybox, blackbox |
| `name` | `string` |  | N |  | the name of the module of the source package |
| `fuzzRepo` | `abfuzz.Repository` |  | N |  | the repository information for the fuzz code reposisity in the source package |
| `sourceRepo` | `abfuzz.Repository` |  | N |  | the repository information for the main code reposisity of custom in the source package |
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `fileSet` | `Array<abfuzz.StorageFile>` |  | N |  | the file set for the source package |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary or raw source code package. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |
| `customVersion` | `string` |  | N |  | more version info for the custom system |
| `codeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `createTime` | `string` | `Timestamp` | N |  | create time of the source code package |
| `updateTime` | `string` | `Timestamp` | N |  | update time of the source code package |


#### `abfuzz.StorageFile`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  | 文件在minio上的 地址 |
| `checksum` | `string` | `Checksum` | N |  | 文件的hash校验码 |
| `type` | `string` |  | N |  | 文件类型，比如： `corpus`, `harness`, `source`, `build-script`, `openapi`, `jar` |
| `group` | `string` |  | N |  | 文件属于某个组，可以是某个fuzzer |


#### `abfuzz.TeardownStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.Testcase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | id for the testcase |
| `description` | `string` |  | N |  | description for the testcase |
| `keywords` | `Array<string>` |  | N |  | keywords is used for searching. |
| `securityFlag` | `boolean` |  | N |  | Is it a security bug ? |
| `securitySeverity` | `integer` | `Int64` | N |  | Security severity of the bug. |
| `oneTimeCrasherFlag` | `boolean` |  | N |  | Did the bug only reproduced once ? |
| `comments` | `string` |  | N |  | Any additional comments. |
| `projectId` | `string` |  | N |  | Project id associated with this test case. |
| `projectName` | `string` |  | N |  | Project name associated with this test case. |
| `sourcePackageId` | `string` |  | N |  | the source package id for the testcase |
| `fuzzerName` | `string` |  | N |  | Name of the fuzzer used to generate this testcase. |
| `fuzzerNameIndices` | `Array<string>` |  | N |  | Fuzzer name indices |
| `overriddenFuzzerName` | `string` |  | N |  | Overridden fuzzer name because actual fuzzer name can be different in manyscenarios (libfuzzer, afl, etc). |
| `fuzzingId` | `string` |  | N |  | the fuzzing which is the testcase generated from |
| `buildOperationName` | `string` |  | N |  | the build operation name of the testcase |
| `operationName` | `string` |  | N |  | the operation name of the testcase when fuzzing |
| `buildId` | `string` |  | N |  | the build id |
| `platform` | `string` | `Platform` | N |  | Platforms (e.g. windows, linux, android). |
| `language` | `string` |  | N |  | the program language for the fuzzing |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `otherPlatforms` | `Array<string>` |  | N |  | Platforms (e.g. windows, linux, android). |
| `otherLangVms` | `Array<abfuzz.LangVM>` |  | N |  | 指定的JDK，如果是聚合后的testcase，会包含多个jdk版本 |
| `engines` | `Array<abfuzz.Engine>` |  | N |  | engine names for the testcase |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | 指定的sanitizers |
| `fuzzedIds` | `Array<string>` |  | N |  | Blobstore keys for various things like original testcase, minimizedtestcase, etc. |
| `minimizedIds` | `Array<string>` |  | N |  |
| `minidumpIds` | `Array<string>` |  | N |  |
| `status` | `string` |  | N |  | Status of this testcase (pending, processed, unreproducible, etc). |
| `open` | `boolean` |  | N |  | Whether testcase is open. |
| `duplicateOf` | `integer` | `Int64` | N |  | Id of the testcase that this is marked as a duplicate of. |
| `groupId` | `integer` | `Int64` | N |  | Id for this testcase's associated group. |
| `groupBugInformation` | `integer` | `Int64` | N |  | Tracking issue tracker bug for this testcase group. |
| `isLeader` | `boolean` |  | N |  | Whether or not a testcase is the leader of its group.If the testcase is not in a group, it's the leader of a group of 1.The default is false because we prefer not to show crashes until we aresure. And group_task will correctly set the value within 30 minutes. |
| `bugInformation` | `string` |  | N |  | Tracking issue tracker bug. One bug number per line (future extension). |
| `isADuplicateFlag` | `boolean` |  | N |  | Whether or not a testcase is a duplicate of other testcase. |
| `hasBugFlag` | `boolean` |  | N |  | Whether testcase has a bug (either bug_information orgroup_bug_information). |
| `bugIndices` | `Array<string>` |  | N |  | Indices for bug_information and group_bug_information. |
| `triaged` | `boolean` |  | N |  | Boolean attribute indicating if cleanup triage needs to be done. |
| `additionalMetadata` | `mojo.core.Object` |  | N |  | Additional metadata stored as a JSON object. This should be used forproperties that are not commonly accessed and do not need to be indexed. |
| `crashType` | `string` |  | N |  | Crash on an invalid read/write. |
| `crashDescription` | `string` |  | N |  | Crash description for runtime error |
| `crashAddress` | `string` |  | N |  | Crashing address. |
| `crashState` | `string` |  | N |  | First x stack frames. |
| `crashStacktrace` | `string` |  | N |  | Complete stacktrace. |
| `lastTestedCrashStacktrace` | `string` |  | N |  | Last tested crash stacktrace using the latest revision. |
| `crashRevision` | `integer` | `Int64` | N |  | Revision that we discovered the crash in. |
| `crashSourceFileName` | `string` |  | N |  | The source file name, may be omitted if debug symbols are not available. |
| `crashSourceLine` | `integer` | `Int32` | N |  | The (1-based) source line number, may be omitted if debug symbols arenot available. |
| `crashFunctionName` | `string` |  | N |  | The function name, may be omitted if debug symbols are not available. |
| `symbolized` | `boolean` |  | N |  | Flag indicating whether or not the testcase has been symbolized. |
| `input` | `abfuzz.Testcase.Input` |  | N |  | The input for the testcase crashing |
| `targetServerLogPath` | `string` |  | N |  | The target server log path with java testcase |
| `absolutePath` | `string` |  | N |  | The file on the agent that generated the testcase. |
| `binaryFlag` | `boolean` |  | N |  | Is this a binary file? |
| `flakyStack` | `boolean` |  | N |  | Does the testcase crash stack vary b/w crashes ? |
| `httpFlag` | `boolean` |  | N |  | Do we need to test this testcase using an HTTP/HTTPS server? |
| `gestures` | `Array<string>` |  | N |  | Fake user interaction sequences like key clicks, mouse movements, etc. |
| `redzone` | `integer` | `Int64` | N |  | ASAN redzone size in bytes. |
| `disableUbsan` | `boolean` |  | N |  | Flag indicating if UBSan detection should be disabled. This is needed forcases when ASan and UBSan are bundled in the same build configurationand we need to disable UBSan in some runs to find the potentially moreinteresting ASan bugs. |
| `timeoutMultiplier` | `number` | `Float64` | N |  | Adjusts timeout based on multiplier value. |
| `regression` | `string` |  | N |  | Regression range. |
| `fixed` | `string` |  | N |  | Revisions where this issue has been fixed. |
| `impactIndices` | `Array<string>` |  | N |  | Impact indices for searching. |
| `impact` | `abfuzz.Impact` |  | N |  | 用例影响源代码的版本信息 |
| `isImpactSetFlag` | `boolean` |  | N |  | Whether or not impact task has been run on this testcase. |
| `uploaderEmail` | `string` |  | N |  | Uploader email address. |
| `createTime` | `string` | `Timestamp` | N |  | Timestamp. |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.Testcase.Input`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  | binaray, file, json |
| `content` | `mojo.core.Value` |  | N |  | the input content |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


#### `mojo.http.Request`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `method` | `string` |  | N |  | method specifies the HTTP method (GET, POST, PUT, etc.).For client requests, an empty string means GET. |
| `url` | `string` | `Url` | N |  | url specifies either the URI being requested (for serverrequests) or the URL to access (for client requests). |
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Version`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `major` | `integer` | `Int32` | N |  |
| `minor` | `integer` | `Int32` | N |  |


## 更新 Fuzzing

### 请求路径
```http
PUT /abfuzz/v1/projects/{fuzzing_project_id}/fuzzings/{fuzzing_id}
```


### 请求参数

#### Body 请求对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | Additionally allows '.' and '@' over NAME_CHECK_REGEX.VALID_NAME_REGEX: re.compile(r'^[a-zA-Z0-9_@.-]+$') |
| `name` | `string` |  | N |  | Fuzzer Name. |
| `group` | `string` |  | N |  | the group id for the fuzzers |
| `projectId` | `string` |  | N |  | Project id |
| `projectName` | `string` |  | N |  | project name of the fuzzing |
| `state` | `string` |  | N |  | the state of the Fuzzing |
| `buildOperationName` | `string` |  | N |  | the build operation name |
| `buildId` | `string` |  | N |  | the build id |
| `operationName` | `string` |  | N |  | the operation name of the fuzzing |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | the sanitizers will be used for the project |
| `engine` | `abfuzz.Engine` |  | N |  | the fuzz engine name |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `sourcePackageId` | `string` |  | N |  | the source package id for the fuzzer |
| `sourcePackageCodeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `sourcePackageVersion` | `string` |  | N |  | 源代码的版本信息 |
| `sourcePackage` | `abfuzz.SourcePackage` |  | N |  | the source package for the fuzzer |
| `fuzzerPackage` | `abfuzz.FuzzerPackage` |  | N |  | the fuzzer package of the fuzzer |
| `coveragePackage` | `abfuzz.CoveragePackage` |  | N |  | the fuzzer package of the fuzzer |
| `executablePath` | `string` |  | N |  | Fuzzer's main executable path, relative to root. |
| `executableArguments` | `Array<string>` |  | N |  | the input arguments for the executable, it will be skipped when launcher_script is set. |
| `executableEnvironment` | `Map<string, string>` |  | N |  | the environment setting for the executable |
| `launcherScript` | `string` |  | N |  | Custom script that should be used to launch executable for this fuzzer.It will not try to launch the target if "@service-auto-start" set. |
| `timeout` | `integer` | `Int64` | N |  | Fuzzing timeout. |
| `testcaseTimeoutMillis` | `integer` | `Int64` | N |  | Testcase timeout. |
| `maxTestcases` | `integer` | `Int64` | N |  | Max testcases to generate for this fuzzer. |
| `additionalEnvironmentString` | `string` |  | N |  | Additional environment variables that need to be set for this fuzzer. |
| `statsColumns` | `string` |  | N |  | Column specification for stats. |
| `statsColumnDescriptions` | `string` |  | N |  | Helpful descriptions for the stats_columns. In a yaml format. |
| `builtin` | `boolean` |  | N |  | Whether this is a builtin fuzzer. |
| `autoStop` | `boolean` |  | N |  | Fuzzing automatic stop. |
| `autoHarness` | `boolean` |  | N |  | auto generate harness |
| `customMutatorPlugin` | `string` |  | N |  | custom mutator plugin for Fuzz Engine |
| `customAllowTargets` | `Array<string>` |  | N |  | custom file name & function name targets for instruments and coverage, like white list. |
| `customDenyTargets` | `Array<string>` |  | N |  | custom targets for instruments and coverage, like white list. |
| `context` | `abfuzz.ContextRequest` |  | N |  | context request |
| `dockerImage` | `string` |  | N |  | the specific docker image for the fuzzing |
| `corpusPath` | `string` |  | N |  | the special corpus path |
| `result` | `string` |  | N |  | Result from the last fuzzer run showing the number of testcases generated. |
| `resultUpdateTime` | `string` | `Timestamp` | N |  | Last result update timestamp. |
| `returnCode` | `integer` | `Int64` | N |  | Return code from last fuzzer run. |
| `consoleOutput` | `string` |  | N |  | Console output from last fuzzer run. |
| `sampleTestcase` | `abfuzz.StorageFile` |  | N |  | storage file for the sample testcase generated by the fuzzer. |
| `testcases` | `Array<abfuzz.Testcase>` |  | N |  | generated test cases |
| `coverages` | `Array<abfuzz.Coverage>` |  | N |  | 覆盖率信息 |
| `coverageUrl` | `string` |  | N |  | 覆盖率信息的url |
| `onlyFunctionTest` | `boolean` |  | N |  | 是否只进行功能测试 |
| `coverageMaps` | `Array<abfuzz.CoverageMap>` |  | N |  | 覆盖率图谱 |
| `fuzzStats` | `abfuzz.FuzzStats` |  | N |  | AFL相关的统计 |
| `inputPackage` | `string` | `Url` | N |  | 对coverage有提升的输入集合包 |
| `preprocessScript` | `string` |  | N |  | preprocess script ,process before aboroxy do reuqest |
| `durationSeconds` | `integer` | `Int64` | N |  | the duration of the fuzzing real running in seconds |
| `metadata` | `Map<string, mojo.core.Value>` |  | N |  | the meta information for the fuzzing |
| `createTime` | `string` | `Timestamp` | N |  | the created time of the fuzzing |
| `updateTime` | `string` | `Timestamp` | N |  | Last update time. |
| `corpus` | `mojo.core.Object` |  | N |  | Object type |


#### `abfuzz.CaseStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `disable` | `array` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.ContextCase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `steps` | `Array<abfuzz.CaseStep>` |  | N |  |


#### `abfuzz.ContextRequest`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `setup` | `abfuzz.ContextSetup` |  | N |  | 全局设置，在cases之前执行 |
| `cases` | `Array<abfuzz.ContextCase>` |  | N |  | OpenAPI里的请求(用path和method来对应)，包含在cases里的优先执行，没有包含在cases里的请求，每个请求自动生成一个case，在cases之后依次自行 |
| `teardown` | `abfuzz.ContextTeardown` |  | N |  | 在cases之后执行的请求 |


#### `abfuzz.ContextSetup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.SetupStep>` |  | N |  |


#### `abfuzz.ContextTeardown`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.TeardownStep>` |  | N |  |


#### `abfuzz.Coverage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `element` | `string` |  | N |  | 目标的路径，可以是包、文件、类、函数 |
| `name` | `string` |  | N |  | 目标的名称，不包含路径信息，如果是函数，只是函数名；如果是类，只是类名 |
| `kind` | `string` |  | N |  | 目标的类型: package, file, class, function |
| `hash` | `integer` | `Int64` | N |  | 指定目标的hash值 |
| `lines` | `abfuzz.Coverage.Count` |  | N |  | 行覆盖率，只要本行有一条指令被执行，则本行则被标记为被执行。 |
| `functions` | `abfuzz.Coverage.Count` |  | N |  | 方法覆盖率，任何非抽象的方法，只要有一条指令被执行，则该方法就会被计为被执行。 |
| `classes` | `abfuzz.Coverage.Count` |  | N |  | 类覆盖率，所有类，包括接口，只要其中有一个方法被执行，则标记为被执行。注意：构造函数和静态初始化块也算作方法。 |
| `instructions` | `abfuzz.Coverage.Count` |  | N |  | Jacoco计算的最小单位就是字节码指令。指令覆盖率表明了在所有的指令中，哪些被指令过以及哪些没有被执行。这项指数完全独立于源码格式并且在任何情况下有效，不需要类文件的调试信息。 |
| `branches` | `abfuzz.Coverage.Count` |  | N |  | 对所有的if和switch指令计算了分支覆盖率。这项指标会统计所有的分支数量，并同时支出哪些分支被执行，哪些分支没有被执行。这项指标也在任何情况都有效。异常处理不考虑在分支范围内。 |
| `cxty` | `abfuzz.Coverage.Count` |  | N |  | Cyclomatic Complexity): 圈复杂度会为每一个非抽象方法计算圈复杂度，并为类，包以及组（groups）计算复杂度。圈复杂度简单地说就是为了覆盖所有路径，所需要执行单元测试数量，圈复杂度大说明程序代码可能质量低且难于测试和维护。 |
| `edges` | `abfuzz.Coverage.Count` |  | N |  | 边覆盖 |
| `linesInfo` | `abfuzz.Coverage.LinesInfo` |  | N |  | 在源文件内详细的行覆盖信息，在kind为file时才会有 |
| `branchInfoes` | `Array<abfuzz.Coverage.BranchInfo>` |  | N |  | 在源文件内详细的分支覆盖信息，在kind为file时才会有 |
| `functionInfoes` | `Array<abfuzz.Coverage.FunctionInfo>` |  | N |  | 在源文件内详细的函数覆盖信息，在kind为file时才会有 |
| `classInfoes` | `Array<abfuzz.Coverage.ClassInfo>` |  | N |  | 在源文件内详细的类覆盖信息，在kind为file时才会有 |


#### `abfuzz.Coverage.BranchInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `startLine` | `integer` | `Int32` | N |  | 分支起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 分支起始列 |
| `destLine` | `integer` | `Int32` | N |  | 分支跳转至的行 |
| `attribute` | `integer` | `Int32` | N |  | 分支属性 |
| `executedCount` | `integer` | `Int32` | N |  | 分支的执行数 |


#### `abfuzz.Coverage.ClassInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | 类名称 |
| `startLine` | `integer` | `Int32` | N |  | 类的起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 类的起始列 |
| `endLine` | `integer` | `Int32` | N |  | 类结束行 |
| `endColumn` | `integer` | `Int32` | N |  | 类结束行 |
| `attribute` | `integer` | `Int32` | N |  | 类属性 |
| `executedCount` | `integer` | `Int32` | N |  | 类执行数 |


#### `abfuzz.Coverage.Count`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `hit` | `integer` | `Int64` | N |  | 覆盖到的元素中的计数 |
| `total` | `integer` | `Int64` | N |  | 元素中的总计数值 |


#### `abfuzz.Coverage.FunctionInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | 函数名称 |
| `startLine` | `integer` | `Int32` | N |  | 函数的起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 函数的起始列 |
| `endLine` | `integer` | `Int32` | N |  | 函数结束行 |
| `endColumn` | `integer` | `Int32` | N |  | 函数结束行 |
| `attribute` | `integer` | `Int32` | N |  | 函数属性 |
| `executedCount` | `integer` | `Int32` | N |  | 函数执行数 |


#### `abfuzz.Coverage.LinesInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `start` | `integer` | `Int32` | N |  | 起始行 |
| `attributes` | `Array<integer>` |  | N |  | 每行的属性值 |
| `executedCounts` | `Array<integer>` |  | N |  | 每行的执行数 |


#### `abfuzz.CoverageMap`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `node` | `string` |  | N |  |
| `childNode` | `string` |  | N |  |
| `uri` | `string` |  | N |  |


#### `abfuzz.CoveragePackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |


#### `abfuzz.CpuStat`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `number` | `integer` | `Int32` | N |  | CPU编号 |
| `state` | `string` |  | N |  | CPU的状态 AVAILABLE， CAUTION |


#### `abfuzz.Engine`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the engine |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.FuzzStats`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `projectId` | `string` |  | N |  |
| `sourcePackageId` | `string` |  | N |  |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `buildId` | `string` |  | N |  | the build id |
| `operationName` | `string` |  | N |  | the operation name of the fuzzing |
| `fuzzingId` | `string` |  | N |  |
| `fuzzerName` | `string` |  | N |  |
| `cyclesDone` | `integer` | `Int32` | N |  |
| `execsDone` | `integer` | `Int32` | N |  | 总共完成的执行数 |
| `execsSpeed` | `number` | `Float32` | N |  | 每秒的执行次数 |
| `cpuStats` | `Array<abfuzz.CpuStat>` |  | N |  | CPU的状态 |
| `pathsTotal` | `integer` | `Int32` | N |  | 执行边的总数 |
| `pathsFavored` | `integer` | `Int32` | N |  |
| `pathsFound` | `integer` | `Int32` | N |  | 发现的边的总数 |
| `pathsImported` | `integer` | `Int32` | N |  |
| `pathsMaxDepth` | `integer` | `Int32` | N |  |
| `pathsPendingFavored` | `integer` | `Int32` | N |  |
| `pathsPendingTotal` | `integer` | `Int32` | N |  |
| `pathsVariable` | `integer` | `Int32` | N |  |
| `pathsStability` | `number` | `Float32` | N |  |
| `bitmapCoverage` | `number` | `Float32` | N |  | bitmap覆盖率 |
| `uniqueCrashes` | `integer` | `Int32` | N |  | 独立的crash的总数 |
| `uniqueHangs` | `integer` | `Int32` | N |  | 独立的hang的总数 |
| `lastNewPath` | `string` | `Duration` | N |  | 最近一次新path的持续时间 |
| `lastUniqueCrash` | `string` | `Duration` | N |  | 最近一次发现独立crash的持续时间 |
| `lastUniqueHang` | `string` | `Duration` | N |  | 最近一次发现独立hang的持续时间 |
| `execsSinceCrash` | `integer` | `Int32` | N |  | 从上次unique的crash的执行数 |
| `execTimeout` | `integer` | `Int32` | N |  |
| `createTime` | `string` | `Timestamp` | N |  |  |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.FuzzerPackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `type` | `string` |  | N |  | the type of the source package, oneof the whitebox, greybox, blackbox |
| `filename` | `string` |  | N |  | Filename for the custom binary. |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |


#### `abfuzz.Impact`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `versionIndices` | `Array<string>` |  | N |  | The impacted version indices (including beta, stable and extended_stable). |
| `extendedStableVersion` | `string` |  | N |  | The impacted extended stable version. |
| `extendedStableVersionIndices` | `Array<string>` |  | N |  | The impacted extended stable version indices. |
| `extendedStableVersionLikely` | `boolean` |  | N |  | The impacted extended stable version is merely probable (not definite).See the comment on stable_version_likely. |
| `stableVersion` | `string` |  | N |  | The impacted stable version. |
| `stableVersionIndices` | `Array<string>` |  | N |  | The impacted stable version indices. |
| `stableVersionLikely` | `boolean` |  | N |  | The impacted stable version is merely probable (not definite). Becausefor a non-asan build, we don't have a stable/beta build. Therefore, wemake an intelligent guess on the version. |
| `betaVersion` | `string` |  | N |  | The impacted beta version. |
| `betaVersionIndices` | `Array<string>` |  | N |  | The impacted beta version indices. |
| `betaVersionLikely` | `boolean` |  | N |  | The impacted beta version is merely probable (not definite). See thecomment on stable_version_likely. |
| `headVersion` | `string` |  | N |  | The impacted 'head' version |
| `headVersionIndices` | `Array<string>` |  | N |  | The impacted head version indices. |
| `headVersionLikely` | `boolean` |  | N |  | The impacted head version is likely. |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `abfuzz.Repository`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `url` | `string` | `Url` | N |  | the git url for the source package |
| `branch` | `string` |  | N |  | the git branch for the source package |
| `tag` | `string` |  | N |  | the git tag for the source package |
| `commitTime` | `string` | `Timestamp` | N |  | the used commit time for the source package |
| `commitKey` | `string` |  | N |  | the used commit key for the source package, it is hash key for git |


#### `abfuzz.Sanitizer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the sanitizer |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.SetupStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | 更新上下文时发起的请求，不要求包含在OpenAPI中 |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |
| `filter` | `Array<string>` |  | N |  |
| `order` | `string` |  | N |  |


#### `abfuzz.SourcePackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the uuid for the source package |
| `projectId` | `string` |  | N |  | the project id which including the source package |
| `projectName` | `string` |  | N |  | the project name which including the source package |
| `projectVersion` | `string` |  | N |  | the project version |
| `type` | `string` |  | N |  | the type of the source packagewhitebox, graybox, blackbox |
| `name` | `string` |  | N |  | the name of the module of the source package |
| `fuzzRepo` | `abfuzz.Repository` |  | N |  | the repository information for the fuzz code reposisity in the source package |
| `sourceRepo` | `abfuzz.Repository` |  | N |  | the repository information for the main code reposisity of custom in the source package |
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `fileSet` | `Array<abfuzz.StorageFile>` |  | N |  | the file set for the source package |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary or raw source code package. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |
| `customVersion` | `string` |  | N |  | more version info for the custom system |
| `codeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `createTime` | `string` | `Timestamp` | N |  | create time of the source code package |
| `updateTime` | `string` | `Timestamp` | N |  | update time of the source code package |


#### `abfuzz.StorageFile`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  | 文件在minio上的 地址 |
| `checksum` | `string` | `Checksum` | N |  | 文件的hash校验码 |
| `type` | `string` |  | N |  | 文件类型，比如： `corpus`, `harness`, `source`, `build-script`, `openapi`, `jar` |
| `group` | `string` |  | N |  | 文件属于某个组，可以是某个fuzzer |


#### `abfuzz.TeardownStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.Testcase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | id for the testcase |
| `description` | `string` |  | N |  | description for the testcase |
| `keywords` | `Array<string>` |  | N |  | keywords is used for searching. |
| `securityFlag` | `boolean` |  | N |  | Is it a security bug ? |
| `securitySeverity` | `integer` | `Int64` | N |  | Security severity of the bug. |
| `oneTimeCrasherFlag` | `boolean` |  | N |  | Did the bug only reproduced once ? |
| `comments` | `string` |  | N |  | Any additional comments. |
| `projectId` | `string` |  | N |  | Project id associated with this test case. |
| `projectName` | `string` |  | N |  | Project name associated with this test case. |
| `sourcePackageId` | `string` |  | N |  | the source package id for the testcase |
| `fuzzerName` | `string` |  | N |  | Name of the fuzzer used to generate this testcase. |
| `fuzzerNameIndices` | `Array<string>` |  | N |  | Fuzzer name indices |
| `overriddenFuzzerName` | `string` |  | N |  | Overridden fuzzer name because actual fuzzer name can be different in manyscenarios (libfuzzer, afl, etc). |
| `fuzzingId` | `string` |  | N |  | the fuzzing which is the testcase generated from |
| `buildOperationName` | `string` |  | N |  | the build operation name of the testcase |
| `operationName` | `string` |  | N |  | the operation name of the testcase when fuzzing |
| `buildId` | `string` |  | N |  | the build id |
| `platform` | `string` | `Platform` | N |  | Platforms (e.g. windows, linux, android). |
| `language` | `string` |  | N |  | the program language for the fuzzing |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `otherPlatforms` | `Array<string>` |  | N |  | Platforms (e.g. windows, linux, android). |
| `otherLangVms` | `Array<abfuzz.LangVM>` |  | N |  | 指定的JDK，如果是聚合后的testcase，会包含多个jdk版本 |
| `engines` | `Array<abfuzz.Engine>` |  | N |  | engine names for the testcase |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | 指定的sanitizers |
| `fuzzedIds` | `Array<string>` |  | N |  | Blobstore keys for various things like original testcase, minimizedtestcase, etc. |
| `minimizedIds` | `Array<string>` |  | N |  |
| `minidumpIds` | `Array<string>` |  | N |  |
| `status` | `string` |  | N |  | Status of this testcase (pending, processed, unreproducible, etc). |
| `open` | `boolean` |  | N |  | Whether testcase is open. |
| `duplicateOf` | `integer` | `Int64` | N |  | Id of the testcase that this is marked as a duplicate of. |
| `groupId` | `integer` | `Int64` | N |  | Id for this testcase's associated group. |
| `groupBugInformation` | `integer` | `Int64` | N |  | Tracking issue tracker bug for this testcase group. |
| `isLeader` | `boolean` |  | N |  | Whether or not a testcase is the leader of its group.If the testcase is not in a group, it's the leader of a group of 1.The default is false because we prefer not to show crashes until we aresure. And group_task will correctly set the value within 30 minutes. |
| `bugInformation` | `string` |  | N |  | Tracking issue tracker bug. One bug number per line (future extension). |
| `isADuplicateFlag` | `boolean` |  | N |  | Whether or not a testcase is a duplicate of other testcase. |
| `hasBugFlag` | `boolean` |  | N |  | Whether testcase has a bug (either bug_information orgroup_bug_information). |
| `bugIndices` | `Array<string>` |  | N |  | Indices for bug_information and group_bug_information. |
| `triaged` | `boolean` |  | N |  | Boolean attribute indicating if cleanup triage needs to be done. |
| `additionalMetadata` | `mojo.core.Object` |  | N |  | Additional metadata stored as a JSON object. This should be used forproperties that are not commonly accessed and do not need to be indexed. |
| `crashType` | `string` |  | N |  | Crash on an invalid read/write. |
| `crashDescription` | `string` |  | N |  | Crash description for runtime error |
| `crashAddress` | `string` |  | N |  | Crashing address. |
| `crashState` | `string` |  | N |  | First x stack frames. |
| `crashStacktrace` | `string` |  | N |  | Complete stacktrace. |
| `lastTestedCrashStacktrace` | `string` |  | N |  | Last tested crash stacktrace using the latest revision. |
| `crashRevision` | `integer` | `Int64` | N |  | Revision that we discovered the crash in. |
| `crashSourceFileName` | `string` |  | N |  | The source file name, may be omitted if debug symbols are not available. |
| `crashSourceLine` | `integer` | `Int32` | N |  | The (1-based) source line number, may be omitted if debug symbols arenot available. |
| `crashFunctionName` | `string` |  | N |  | The function name, may be omitted if debug symbols are not available. |
| `symbolized` | `boolean` |  | N |  | Flag indicating whether or not the testcase has been symbolized. |
| `input` | `abfuzz.Testcase.Input` |  | N |  | The input for the testcase crashing |
| `targetServerLogPath` | `string` |  | N |  | The target server log path with java testcase |
| `absolutePath` | `string` |  | N |  | The file on the agent that generated the testcase. |
| `binaryFlag` | `boolean` |  | N |  | Is this a binary file? |
| `flakyStack` | `boolean` |  | N |  | Does the testcase crash stack vary b/w crashes ? |
| `httpFlag` | `boolean` |  | N |  | Do we need to test this testcase using an HTTP/HTTPS server? |
| `gestures` | `Array<string>` |  | N |  | Fake user interaction sequences like key clicks, mouse movements, etc. |
| `redzone` | `integer` | `Int64` | N |  | ASAN redzone size in bytes. |
| `disableUbsan` | `boolean` |  | N |  | Flag indicating if UBSan detection should be disabled. This is needed forcases when ASan and UBSan are bundled in the same build configurationand we need to disable UBSan in some runs to find the potentially moreinteresting ASan bugs. |
| `timeoutMultiplier` | `number` | `Float64` | N |  | Adjusts timeout based on multiplier value. |
| `regression` | `string` |  | N |  | Regression range. |
| `fixed` | `string` |  | N |  | Revisions where this issue has been fixed. |
| `impactIndices` | `Array<string>` |  | N |  | Impact indices for searching. |
| `impact` | `abfuzz.Impact` |  | N |  | 用例影响源代码的版本信息 |
| `isImpactSetFlag` | `boolean` |  | N |  | Whether or not impact task has been run on this testcase. |
| `uploaderEmail` | `string` |  | N |  | Uploader email address. |
| `createTime` | `string` | `Timestamp` | N |  | Timestamp. |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.Testcase.Input`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  | binaray, file, json |
| `content` | `mojo.core.Value` |  | N |  | the input content |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


#### `mojo.http.Request`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `method` | `string` |  | N |  | method specifies the HTTP method (GET, POST, PUT, etc.).For client requests, an empty string means GET. |
| `url` | `string` | `Url` | N |  | url specifies either the URI being requested (for serverrequests) or the URL to access (for client requests). |
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Version`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `major` | `integer` | `Int32` | N |  |
| `minor` | `integer` | `Int32` | N |  |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | Additionally allows '.' and '@' over NAME_CHECK_REGEX.VALID_NAME_REGEX: re.compile(r'^[a-zA-Z0-9_@.-]+$') |
| `name` | `string` |  | N |  | Fuzzer Name. |
| `group` | `string` |  | N |  | the group id for the fuzzers |
| `projectId` | `string` |  | N |  | Project id |
| `projectName` | `string` |  | N |  | project name of the fuzzing |
| `state` | `string` |  | N |  | the state of the Fuzzing |
| `buildOperationName` | `string` |  | N |  | the build operation name |
| `buildId` | `string` |  | N |  | the build id |
| `operationName` | `string` |  | N |  | the operation name of the fuzzing |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | the sanitizers will be used for the project |
| `engine` | `abfuzz.Engine` |  | N |  | the fuzz engine name |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `sourcePackageId` | `string` |  | N |  | the source package id for the fuzzer |
| `sourcePackageCodeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `sourcePackageVersion` | `string` |  | N |  | 源代码的版本信息 |
| `sourcePackage` | `abfuzz.SourcePackage` |  | N |  | the source package for the fuzzer |
| `fuzzerPackage` | `abfuzz.FuzzerPackage` |  | N |  | the fuzzer package of the fuzzer |
| `coveragePackage` | `abfuzz.CoveragePackage` |  | N |  | the fuzzer package of the fuzzer |
| `executablePath` | `string` |  | N |  | Fuzzer's main executable path, relative to root. |
| `executableArguments` | `Array<string>` |  | N |  | the input arguments for the executable, it will be skipped when launcher_script is set. |
| `executableEnvironment` | `Map<string, string>` |  | N |  | the environment setting for the executable |
| `launcherScript` | `string` |  | N |  | Custom script that should be used to launch executable for this fuzzer.It will not try to launch the target if "@service-auto-start" set. |
| `timeout` | `integer` | `Int64` | N |  | Fuzzing timeout. |
| `testcaseTimeoutMillis` | `integer` | `Int64` | N |  | Testcase timeout. |
| `maxTestcases` | `integer` | `Int64` | N |  | Max testcases to generate for this fuzzer. |
| `additionalEnvironmentString` | `string` |  | N |  | Additional environment variables that need to be set for this fuzzer. |
| `statsColumns` | `string` |  | N |  | Column specification for stats. |
| `statsColumnDescriptions` | `string` |  | N |  | Helpful descriptions for the stats_columns. In a yaml format. |
| `builtin` | `boolean` |  | N |  | Whether this is a builtin fuzzer. |
| `autoStop` | `boolean` |  | N |  | Fuzzing automatic stop. |
| `autoHarness` | `boolean` |  | N |  | auto generate harness |
| `customMutatorPlugin` | `string` |  | N |  | custom mutator plugin for Fuzz Engine |
| `customAllowTargets` | `Array<string>` |  | N |  | custom file name & function name targets for instruments and coverage, like white list. |
| `customDenyTargets` | `Array<string>` |  | N |  | custom targets for instruments and coverage, like white list. |
| `context` | `abfuzz.ContextRequest` |  | N |  | context request |
| `dockerImage` | `string` |  | N |  | the specific docker image for the fuzzing |
| `corpusPath` | `string` |  | N |  | the special corpus path |
| `result` | `string` |  | N |  | Result from the last fuzzer run showing the number of testcases generated. |
| `resultUpdateTime` | `string` | `Timestamp` | N |  | Last result update timestamp. |
| `returnCode` | `integer` | `Int64` | N |  | Return code from last fuzzer run. |
| `consoleOutput` | `string` |  | N |  | Console output from last fuzzer run. |
| `sampleTestcase` | `abfuzz.StorageFile` |  | N |  | storage file for the sample testcase generated by the fuzzer. |
| `testcases` | `Array<abfuzz.Testcase>` |  | N |  | generated test cases |
| `coverages` | `Array<abfuzz.Coverage>` |  | N |  | 覆盖率信息 |
| `coverageUrl` | `string` |  | N |  | 覆盖率信息的url |
| `onlyFunctionTest` | `boolean` |  | N |  | 是否只进行功能测试 |
| `coverageMaps` | `Array<abfuzz.CoverageMap>` |  | N |  | 覆盖率图谱 |
| `fuzzStats` | `abfuzz.FuzzStats` |  | N |  | AFL相关的统计 |
| `inputPackage` | `string` | `Url` | N |  | 对coverage有提升的输入集合包 |
| `preprocessScript` | `string` |  | N |  | preprocess script ,process before aboroxy do reuqest |
| `durationSeconds` | `integer` | `Int64` | N |  | the duration of the fuzzing real running in seconds |
| `metadata` | `Map<string, mojo.core.Value>` |  | N |  | the meta information for the fuzzing |
| `createTime` | `string` | `Timestamp` | N |  | the created time of the fuzzing |
| `updateTime` | `string` | `Timestamp` | N |  | Last update time. |
| `corpus` | `mojo.core.Object` |  | N |  | Object type |


#### `abfuzz.CaseStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `disable` | `array` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.ContextCase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `steps` | `Array<abfuzz.CaseStep>` |  | N |  |


#### `abfuzz.ContextRequest`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `setup` | `abfuzz.ContextSetup` |  | N |  | 全局设置，在cases之前执行 |
| `cases` | `Array<abfuzz.ContextCase>` |  | N |  | OpenAPI里的请求(用path和method来对应)，包含在cases里的优先执行，没有包含在cases里的请求，每个请求自动生成一个case，在cases之后依次自行 |
| `teardown` | `abfuzz.ContextTeardown` |  | N |  | 在cases之后执行的请求 |


#### `abfuzz.ContextSetup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.SetupStep>` |  | N |  |


#### `abfuzz.ContextTeardown`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.TeardownStep>` |  | N |  |


#### `abfuzz.Coverage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `element` | `string` |  | N |  | 目标的路径，可以是包、文件、类、函数 |
| `name` | `string` |  | N |  | 目标的名称，不包含路径信息，如果是函数，只是函数名；如果是类，只是类名 |
| `kind` | `string` |  | N |  | 目标的类型: package, file, class, function |
| `hash` | `integer` | `Int64` | N |  | 指定目标的hash值 |
| `lines` | `abfuzz.Coverage.Count` |  | N |  | 行覆盖率，只要本行有一条指令被执行，则本行则被标记为被执行。 |
| `functions` | `abfuzz.Coverage.Count` |  | N |  | 方法覆盖率，任何非抽象的方法，只要有一条指令被执行，则该方法就会被计为被执行。 |
| `classes` | `abfuzz.Coverage.Count` |  | N |  | 类覆盖率，所有类，包括接口，只要其中有一个方法被执行，则标记为被执行。注意：构造函数和静态初始化块也算作方法。 |
| `instructions` | `abfuzz.Coverage.Count` |  | N |  | Jacoco计算的最小单位就是字节码指令。指令覆盖率表明了在所有的指令中，哪些被指令过以及哪些没有被执行。这项指数完全独立于源码格式并且在任何情况下有效，不需要类文件的调试信息。 |
| `branches` | `abfuzz.Coverage.Count` |  | N |  | 对所有的if和switch指令计算了分支覆盖率。这项指标会统计所有的分支数量，并同时支出哪些分支被执行，哪些分支没有被执行。这项指标也在任何情况都有效。异常处理不考虑在分支范围内。 |
| `cxty` | `abfuzz.Coverage.Count` |  | N |  | Cyclomatic Complexity): 圈复杂度会为每一个非抽象方法计算圈复杂度，并为类，包以及组（groups）计算复杂度。圈复杂度简单地说就是为了覆盖所有路径，所需要执行单元测试数量，圈复杂度大说明程序代码可能质量低且难于测试和维护。 |
| `edges` | `abfuzz.Coverage.Count` |  | N |  | 边覆盖 |
| `linesInfo` | `abfuzz.Coverage.LinesInfo` |  | N |  | 在源文件内详细的行覆盖信息，在kind为file时才会有 |
| `branchInfoes` | `Array<abfuzz.Coverage.BranchInfo>` |  | N |  | 在源文件内详细的分支覆盖信息，在kind为file时才会有 |
| `functionInfoes` | `Array<abfuzz.Coverage.FunctionInfo>` |  | N |  | 在源文件内详细的函数覆盖信息，在kind为file时才会有 |
| `classInfoes` | `Array<abfuzz.Coverage.ClassInfo>` |  | N |  | 在源文件内详细的类覆盖信息，在kind为file时才会有 |


#### `abfuzz.Coverage.BranchInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `startLine` | `integer` | `Int32` | N |  | 分支起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 分支起始列 |
| `destLine` | `integer` | `Int32` | N |  | 分支跳转至的行 |
| `attribute` | `integer` | `Int32` | N |  | 分支属性 |
| `executedCount` | `integer` | `Int32` | N |  | 分支的执行数 |


#### `abfuzz.Coverage.ClassInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | 类名称 |
| `startLine` | `integer` | `Int32` | N |  | 类的起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 类的起始列 |
| `endLine` | `integer` | `Int32` | N |  | 类结束行 |
| `endColumn` | `integer` | `Int32` | N |  | 类结束行 |
| `attribute` | `integer` | `Int32` | N |  | 类属性 |
| `executedCount` | `integer` | `Int32` | N |  | 类执行数 |


#### `abfuzz.Coverage.Count`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `hit` | `integer` | `Int64` | N |  | 覆盖到的元素中的计数 |
| `total` | `integer` | `Int64` | N |  | 元素中的总计数值 |


#### `abfuzz.Coverage.FunctionInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | 函数名称 |
| `startLine` | `integer` | `Int32` | N |  | 函数的起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 函数的起始列 |
| `endLine` | `integer` | `Int32` | N |  | 函数结束行 |
| `endColumn` | `integer` | `Int32` | N |  | 函数结束行 |
| `attribute` | `integer` | `Int32` | N |  | 函数属性 |
| `executedCount` | `integer` | `Int32` | N |  | 函数执行数 |


#### `abfuzz.Coverage.LinesInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `start` | `integer` | `Int32` | N |  | 起始行 |
| `attributes` | `Array<integer>` |  | N |  | 每行的属性值 |
| `executedCounts` | `Array<integer>` |  | N |  | 每行的执行数 |


#### `abfuzz.CoverageMap`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `node` | `string` |  | N |  |
| `childNode` | `string` |  | N |  |
| `uri` | `string` |  | N |  |


#### `abfuzz.CoveragePackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |


#### `abfuzz.CpuStat`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `number` | `integer` | `Int32` | N |  | CPU编号 |
| `state` | `string` |  | N |  | CPU的状态 AVAILABLE， CAUTION |


#### `abfuzz.Engine`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the engine |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.FuzzStats`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `projectId` | `string` |  | N |  |
| `sourcePackageId` | `string` |  | N |  |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `buildId` | `string` |  | N |  | the build id |
| `operationName` | `string` |  | N |  | the operation name of the fuzzing |
| `fuzzingId` | `string` |  | N |  |
| `fuzzerName` | `string` |  | N |  |
| `cyclesDone` | `integer` | `Int32` | N |  |
| `execsDone` | `integer` | `Int32` | N |  | 总共完成的执行数 |
| `execsSpeed` | `number` | `Float32` | N |  | 每秒的执行次数 |
| `cpuStats` | `Array<abfuzz.CpuStat>` |  | N |  | CPU的状态 |
| `pathsTotal` | `integer` | `Int32` | N |  | 执行边的总数 |
| `pathsFavored` | `integer` | `Int32` | N |  |
| `pathsFound` | `integer` | `Int32` | N |  | 发现的边的总数 |
| `pathsImported` | `integer` | `Int32` | N |  |
| `pathsMaxDepth` | `integer` | `Int32` | N |  |
| `pathsPendingFavored` | `integer` | `Int32` | N |  |
| `pathsPendingTotal` | `integer` | `Int32` | N |  |
| `pathsVariable` | `integer` | `Int32` | N |  |
| `pathsStability` | `number` | `Float32` | N |  |
| `bitmapCoverage` | `number` | `Float32` | N |  | bitmap覆盖率 |
| `uniqueCrashes` | `integer` | `Int32` | N |  | 独立的crash的总数 |
| `uniqueHangs` | `integer` | `Int32` | N |  | 独立的hang的总数 |
| `lastNewPath` | `string` | `Duration` | N |  | 最近一次新path的持续时间 |
| `lastUniqueCrash` | `string` | `Duration` | N |  | 最近一次发现独立crash的持续时间 |
| `lastUniqueHang` | `string` | `Duration` | N |  | 最近一次发现独立hang的持续时间 |
| `execsSinceCrash` | `integer` | `Int32` | N |  | 从上次unique的crash的执行数 |
| `execTimeout` | `integer` | `Int32` | N |  |
| `createTime` | `string` | `Timestamp` | N |  |  |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.FuzzerPackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `type` | `string` |  | N |  | the type of the source package, oneof the whitebox, greybox, blackbox |
| `filename` | `string` |  | N |  | Filename for the custom binary. |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |


#### `abfuzz.Impact`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `versionIndices` | `Array<string>` |  | N |  | The impacted version indices (including beta, stable and extended_stable). |
| `extendedStableVersion` | `string` |  | N |  | The impacted extended stable version. |
| `extendedStableVersionIndices` | `Array<string>` |  | N |  | The impacted extended stable version indices. |
| `extendedStableVersionLikely` | `boolean` |  | N |  | The impacted extended stable version is merely probable (not definite).See the comment on stable_version_likely. |
| `stableVersion` | `string` |  | N |  | The impacted stable version. |
| `stableVersionIndices` | `Array<string>` |  | N |  | The impacted stable version indices. |
| `stableVersionLikely` | `boolean` |  | N |  | The impacted stable version is merely probable (not definite). Becausefor a non-asan build, we don't have a stable/beta build. Therefore, wemake an intelligent guess on the version. |
| `betaVersion` | `string` |  | N |  | The impacted beta version. |
| `betaVersionIndices` | `Array<string>` |  | N |  | The impacted beta version indices. |
| `betaVersionLikely` | `boolean` |  | N |  | The impacted beta version is merely probable (not definite). See thecomment on stable_version_likely. |
| `headVersion` | `string` |  | N |  | The impacted 'head' version |
| `headVersionIndices` | `Array<string>` |  | N |  | The impacted head version indices. |
| `headVersionLikely` | `boolean` |  | N |  | The impacted head version is likely. |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `abfuzz.Repository`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `url` | `string` | `Url` | N |  | the git url for the source package |
| `branch` | `string` |  | N |  | the git branch for the source package |
| `tag` | `string` |  | N |  | the git tag for the source package |
| `commitTime` | `string` | `Timestamp` | N |  | the used commit time for the source package |
| `commitKey` | `string` |  | N |  | the used commit key for the source package, it is hash key for git |


#### `abfuzz.Sanitizer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the sanitizer |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.SetupStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | 更新上下文时发起的请求，不要求包含在OpenAPI中 |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |
| `filter` | `Array<string>` |  | N |  |
| `order` | `string` |  | N |  |


#### `abfuzz.SourcePackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the uuid for the source package |
| `projectId` | `string` |  | N |  | the project id which including the source package |
| `projectName` | `string` |  | N |  | the project name which including the source package |
| `projectVersion` | `string` |  | N |  | the project version |
| `type` | `string` |  | N |  | the type of the source packagewhitebox, graybox, blackbox |
| `name` | `string` |  | N |  | the name of the module of the source package |
| `fuzzRepo` | `abfuzz.Repository` |  | N |  | the repository information for the fuzz code reposisity in the source package |
| `sourceRepo` | `abfuzz.Repository` |  | N |  | the repository information for the main code reposisity of custom in the source package |
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `fileSet` | `Array<abfuzz.StorageFile>` |  | N |  | the file set for the source package |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary or raw source code package. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |
| `customVersion` | `string` |  | N |  | more version info for the custom system |
| `codeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `createTime` | `string` | `Timestamp` | N |  | create time of the source code package |
| `updateTime` | `string` | `Timestamp` | N |  | update time of the source code package |


#### `abfuzz.StorageFile`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  | 文件在minio上的 地址 |
| `checksum` | `string` | `Checksum` | N |  | 文件的hash校验码 |
| `type` | `string` |  | N |  | 文件类型，比如： `corpus`, `harness`, `source`, `build-script`, `openapi`, `jar` |
| `group` | `string` |  | N |  | 文件属于某个组，可以是某个fuzzer |


#### `abfuzz.TeardownStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.Testcase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | id for the testcase |
| `description` | `string` |  | N |  | description for the testcase |
| `keywords` | `Array<string>` |  | N |  | keywords is used for searching. |
| `securityFlag` | `boolean` |  | N |  | Is it a security bug ? |
| `securitySeverity` | `integer` | `Int64` | N |  | Security severity of the bug. |
| `oneTimeCrasherFlag` | `boolean` |  | N |  | Did the bug only reproduced once ? |
| `comments` | `string` |  | N |  | Any additional comments. |
| `projectId` | `string` |  | N |  | Project id associated with this test case. |
| `projectName` | `string` |  | N |  | Project name associated with this test case. |
| `sourcePackageId` | `string` |  | N |  | the source package id for the testcase |
| `fuzzerName` | `string` |  | N |  | Name of the fuzzer used to generate this testcase. |
| `fuzzerNameIndices` | `Array<string>` |  | N |  | Fuzzer name indices |
| `overriddenFuzzerName` | `string` |  | N |  | Overridden fuzzer name because actual fuzzer name can be different in manyscenarios (libfuzzer, afl, etc). |
| `fuzzingId` | `string` |  | N |  | the fuzzing which is the testcase generated from |
| `buildOperationName` | `string` |  | N |  | the build operation name of the testcase |
| `operationName` | `string` |  | N |  | the operation name of the testcase when fuzzing |
| `buildId` | `string` |  | N |  | the build id |
| `platform` | `string` | `Platform` | N |  | Platforms (e.g. windows, linux, android). |
| `language` | `string` |  | N |  | the program language for the fuzzing |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `otherPlatforms` | `Array<string>` |  | N |  | Platforms (e.g. windows, linux, android). |
| `otherLangVms` | `Array<abfuzz.LangVM>` |  | N |  | 指定的JDK，如果是聚合后的testcase，会包含多个jdk版本 |
| `engines` | `Array<abfuzz.Engine>` |  | N |  | engine names for the testcase |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | 指定的sanitizers |
| `fuzzedIds` | `Array<string>` |  | N |  | Blobstore keys for various things like original testcase, minimizedtestcase, etc. |
| `minimizedIds` | `Array<string>` |  | N |  |
| `minidumpIds` | `Array<string>` |  | N |  |
| `status` | `string` |  | N |  | Status of this testcase (pending, processed, unreproducible, etc). |
| `open` | `boolean` |  | N |  | Whether testcase is open. |
| `duplicateOf` | `integer` | `Int64` | N |  | Id of the testcase that this is marked as a duplicate of. |
| `groupId` | `integer` | `Int64` | N |  | Id for this testcase's associated group. |
| `groupBugInformation` | `integer` | `Int64` | N |  | Tracking issue tracker bug for this testcase group. |
| `isLeader` | `boolean` |  | N |  | Whether or not a testcase is the leader of its group.If the testcase is not in a group, it's the leader of a group of 1.The default is false because we prefer not to show crashes until we aresure. And group_task will correctly set the value within 30 minutes. |
| `bugInformation` | `string` |  | N |  | Tracking issue tracker bug. One bug number per line (future extension). |
| `isADuplicateFlag` | `boolean` |  | N |  | Whether or not a testcase is a duplicate of other testcase. |
| `hasBugFlag` | `boolean` |  | N |  | Whether testcase has a bug (either bug_information orgroup_bug_information). |
| `bugIndices` | `Array<string>` |  | N |  | Indices for bug_information and group_bug_information. |
| `triaged` | `boolean` |  | N |  | Boolean attribute indicating if cleanup triage needs to be done. |
| `additionalMetadata` | `mojo.core.Object` |  | N |  | Additional metadata stored as a JSON object. This should be used forproperties that are not commonly accessed and do not need to be indexed. |
| `crashType` | `string` |  | N |  | Crash on an invalid read/write. |
| `crashDescription` | `string` |  | N |  | Crash description for runtime error |
| `crashAddress` | `string` |  | N |  | Crashing address. |
| `crashState` | `string` |  | N |  | First x stack frames. |
| `crashStacktrace` | `string` |  | N |  | Complete stacktrace. |
| `lastTestedCrashStacktrace` | `string` |  | N |  | Last tested crash stacktrace using the latest revision. |
| `crashRevision` | `integer` | `Int64` | N |  | Revision that we discovered the crash in. |
| `crashSourceFileName` | `string` |  | N |  | The source file name, may be omitted if debug symbols are not available. |
| `crashSourceLine` | `integer` | `Int32` | N |  | The (1-based) source line number, may be omitted if debug symbols arenot available. |
| `crashFunctionName` | `string` |  | N |  | The function name, may be omitted if debug symbols are not available. |
| `symbolized` | `boolean` |  | N |  | Flag indicating whether or not the testcase has been symbolized. |
| `input` | `abfuzz.Testcase.Input` |  | N |  | The input for the testcase crashing |
| `targetServerLogPath` | `string` |  | N |  | The target server log path with java testcase |
| `absolutePath` | `string` |  | N |  | The file on the agent that generated the testcase. |
| `binaryFlag` | `boolean` |  | N |  | Is this a binary file? |
| `flakyStack` | `boolean` |  | N |  | Does the testcase crash stack vary b/w crashes ? |
| `httpFlag` | `boolean` |  | N |  | Do we need to test this testcase using an HTTP/HTTPS server? |
| `gestures` | `Array<string>` |  | N |  | Fake user interaction sequences like key clicks, mouse movements, etc. |
| `redzone` | `integer` | `Int64` | N |  | ASAN redzone size in bytes. |
| `disableUbsan` | `boolean` |  | N |  | Flag indicating if UBSan detection should be disabled. This is needed forcases when ASan and UBSan are bundled in the same build configurationand we need to disable UBSan in some runs to find the potentially moreinteresting ASan bugs. |
| `timeoutMultiplier` | `number` | `Float64` | N |  | Adjusts timeout based on multiplier value. |
| `regression` | `string` |  | N |  | Regression range. |
| `fixed` | `string` |  | N |  | Revisions where this issue has been fixed. |
| `impactIndices` | `Array<string>` |  | N |  | Impact indices for searching. |
| `impact` | `abfuzz.Impact` |  | N |  | 用例影响源代码的版本信息 |
| `isImpactSetFlag` | `boolean` |  | N |  | Whether or not impact task has been run on this testcase. |
| `uploaderEmail` | `string` |  | N |  | Uploader email address. |
| `createTime` | `string` | `Timestamp` | N |  | Timestamp. |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.Testcase.Input`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  | binaray, file, json |
| `content` | `mojo.core.Value` |  | N |  | the input content |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


#### `mojo.http.Request`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `method` | `string` |  | N |  | method specifies the HTTP method (GET, POST, PUT, etc.).For client requests, an empty string means GET. |
| `url` | `string` | `Url` | N |  | url specifies either the URI being requested (for serverrequests) or the URL to access (for client requests). |
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Version`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `major` | `integer` | `Int32` | N |  |
| `minor` | `integer` | `Int32` | N |  |


## 获取项目

### 请求路径
```http
GET /abfuzz/v1/projects/{id}
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `id` | `string` |  |  |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | project id |
| `name` | `string` |  | N |  | project name, normally the same with the custom's main source repo |
| `description` | `string` |  | N |  | project description |
| `sourcePackage` | `abfuzz.SourcePackage` |  | N |  | the source package for the project, only used in the create project |
| `language` | `string` |  | N |  | the program languages for the project，如果有多种语言，那么fuzzer中就需要指定语言 |
| `userId` | `string` |  | N |  | 用户相关的Project的用户ID |
| `externalId` | `string` |  | N |  | 第三方系统对应project的id |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | the sanitizers will be used for the project |
| `engines` | `Array<abfuzz.Engine>` |  | Y |  | the fazz engine name |
| `platforms` | `Array<string>` |  | N |  | The platform that this job can run on. |
| `langVms` | `Array<abfuzz.LangVM>` |  | N |  | The lang_vm platform that this job can run on if the language is Java. |
| `fuzzers` | `Array<abfuzz.Fuzzer>` |  | Y |  | the fuzzers in the project |
| `createTime` | `string` | `Timestamp` | N |  | the create time of the project |
| `updateTime` | `string` | `Timestamp` | N |  | the update time of the project |
| `deleteTime` | `string` | `Timestamp` | N |  | the delete time of the project, for the soft delete |


#### `abfuzz.CaseStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `disable` | `array` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.ContextCase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `steps` | `Array<abfuzz.CaseStep>` |  | N |  |


#### `abfuzz.ContextRequest`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `setup` | `abfuzz.ContextSetup` |  | N |  | 全局设置，在cases之前执行 |
| `cases` | `Array<abfuzz.ContextCase>` |  | N |  | OpenAPI里的请求(用path和method来对应)，包含在cases里的优先执行，没有包含在cases里的请求，每个请求自动生成一个case，在cases之后依次自行 |
| `teardown` | `abfuzz.ContextTeardown` |  | N |  | 在cases之后执行的请求 |


#### `abfuzz.ContextSetup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.SetupStep>` |  | N |  |


#### `abfuzz.ContextTeardown`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.TeardownStep>` |  | N |  |


#### `abfuzz.CorpusConfig`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `includings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `excludings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `httpBaseUrl` | `string` | `Url` | N |  | URl type<br>Uniform Resource Identifier (URI) is a string of characters used to identify a resource.<br>A Uniform Resource Name (URN) may be compared to a person's name,while a Uniform Resource Locator (URL) may be compared to their street address.In other words, a URN identifies an item and a URL provides a method for finding it.<br> |
| `httpMappingPort` | `integer` | `Int32` | N |  |
| `httpMappingHost` | `string` |  | N |  |
| `httpStaticValues` | `mojo.core.Object` |  | N |  | Object type |
| `httpCorpuses` | `Array<abfuzz.HttpCorpus>` |  | N |  |
| `combinedApiPath` | `string` |  | N |  |
| `asyncApi` | `boolean` |  | N |  |


#### `abfuzz.EndPoint`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` |  | N |  |
| `http` | `abfuzz.EndPoint.Http` |  | N |  |  |
| `priority` | `integer` | `Int32` | N |  |


#### `abfuzz.EndPoint.Http`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `path` | `string` |  | N |  |
| `methods` | `Array<string>` |  | N |  |


#### `abfuzz.Engine`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the engine |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.Entity`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `stepType` | `string` |  | N |  |
| `interfaces` | `Array<abfuzz.Entity.Interface>` |  | N |  |


#### `abfuzz.Entity.Interface`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `type` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | N |  |
| `pathParams` | `Map<string, string>` |  | N |  |
| `queryParams` | `Map<string, string>` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `response` | `mojo.http.Response` |  | N |  | Response represents the response from an HTTP request. |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |


#### `abfuzz.Fixup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.Fuzzer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | Y |  | the fuzzer name |
| `group` | `string` |  | N |  | the group id for the fuzzers |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `fuzzerPackage` | `abfuzz.FuzzerPackage` |  | N |  | the fuzzer package of the fuzzer |
| `executablePath` | `string` |  | N |  | Fuzzer's main executable path, relative to root. |
| `executableArguments` | `Array<string>` |  | N |  | the input arguments for the executable, it will be skipped when launcher_script is set. |
| `executableEnvironment` | `Map<string, string>` |  | N |  | the environment setting for the executable |
| `launcherScript` | `string` |  | N |  | Custom script that should be used to launch executable for this fuzzer.It will not try to launch the target if "@service-auto-start" set. |
| `timeout` | `integer` | `Int64` | N |  | Fuzzing timeout. |
| `testcaseTimeoutMillis` | `integer` | `Int64` | N |  | Testcase timeout. |
| `autoStop` | `boolean` |  | N |  | Fuzzing automatic stop. |
| `autoHarness` | `boolean` |  | N |  | auto generate harness |
| `customMutatorPlugin` | `string` |  | N |  | custom mutator plugin for Fuzz Engine |
| `customAllowTargets` | `Array<string>` |  | N |  | custom file name & function name targets for instruments and coverage, like white list. |
| `customDenyTargets` | `Array<string>` |  | N |  | custom targets for instruments and coverage, like white list. |
| `context` | `abfuzz.ContextRequest` |  | N |  | context request |
| `corpusPath` | `string` |  | N |  | the special corpus path |
| `preprocessScript` | `string` |  | N |  | the preprocess script info |
| `onlyFunctionalTest` | `boolean` |  | N |  | is only fuzzing user corpus |
| `autoHarnessCommand` | `string` |  | N |  | execute the command before run auto harness generation |
| `autoHarnessScript` | `string` |  | N |  | execute the script before run auto harness generation if the command is empty |
| `autoHarnessArguments` | `Array<string>` |  | N |  | the arguments when to run the harness generation, usually for including path setting |
| `autoHarnessBlacklist` | `Array<string>` |  | N |  | the blacklist files to run the harness generation, these files will be skipped. |
| `autoHarnessWebServer` | `boolean` |  | N |  | determine if the java auto harness server is a web server |
| `metadata` | `Map<string, mojo.core.Value>` |  | N |  | the meta information for the fuzzing |
| `corpus` | `abfuzz.CorpusConfig` |  | N |  |  |
| `entities` | `Array<abfuzz.Entity>` |  | N |  |


#### `abfuzz.FuzzerPackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `type` | `string` |  | N |  | the type of the source package, oneof the whitebox, greybox, blackbox |
| `filename` | `string` |  | N |  | Filename for the custom binary. |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |


#### `abfuzz.HttpCorpus`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | Y |  |
| `staticValues` | `mojo.core.Object` |  | N |  | Object type |
| `fixups` | `Array<abfuzz.Fixup>` |  | N |  |
| `relations` | `Array<abfuzz.Relation>` |  | N |  |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `abfuzz.Relation`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.Repository`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `url` | `string` | `Url` | N |  | the git url for the source package |
| `branch` | `string` |  | N |  | the git branch for the source package |
| `tag` | `string` |  | N |  | the git tag for the source package |
| `commitTime` | `string` | `Timestamp` | N |  | the used commit time for the source package |
| `commitKey` | `string` |  | N |  | the used commit key for the source package, it is hash key for git |


#### `abfuzz.Sanitizer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the sanitizer |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.SetupStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | 更新上下文时发起的请求，不要求包含在OpenAPI中 |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |
| `filter` | `Array<string>` |  | N |  |
| `order` | `string` |  | N |  |


#### `abfuzz.SourcePackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the uuid for the source package |
| `projectId` | `string` |  | N |  | the project id which including the source package |
| `projectName` | `string` |  | N |  | the project name which including the source package |
| `projectVersion` | `string` |  | N |  | the project version |
| `type` | `string` |  | N |  | the type of the source packagewhitebox, graybox, blackbox |
| `name` | `string` |  | N |  | the name of the module of the source package |
| `fuzzRepo` | `abfuzz.Repository` |  | N |  | the repository information for the fuzz code reposisity in the source package |
| `sourceRepo` | `abfuzz.Repository` |  | N |  | the repository information for the main code reposisity of custom in the source package |
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `fileSet` | `Array<abfuzz.StorageFile>` |  | N |  | the file set for the source package |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary or raw source code package. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |
| `customVersion` | `string` |  | N |  | more version info for the custom system |
| `codeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `createTime` | `string` | `Timestamp` | N |  | create time of the source code package |
| `updateTime` | `string` | `Timestamp` | N |  | update time of the source code package |


#### `abfuzz.StorageFile`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  | 文件在minio上的 地址 |
| `checksum` | `string` | `Checksum` | N |  | 文件的hash校验码 |
| `type` | `string` |  | N |  | 文件类型，比如： `corpus`, `harness`, `source`, `build-script`, `openapi`, `jar` |
| `group` | `string` |  | N |  | 文件属于某个组，可以是某个fuzzer |


#### `abfuzz.TeardownStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


#### `mojo.http.Request`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `method` | `string` |  | N |  | method specifies the HTTP method (GET, POST, PUT, etc.).For client requests, an empty string means GET. |
| `url` | `string` | `Url` | N |  | url specifies either the URI being requested (for serverrequests) or the URL to access (for client requests). |
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Response`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |


#### `mojo.http.Status`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `integer` | `Int32` | N |  |
| `reason` | `string` |  | N |  |


#### `mojo.http.Version`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `major` | `integer` | `Int32` | N |  |
| `minor` | `integer` | `Int32` | N |  |


## 删除项目

### 请求路径
```http
DELETE /abfuzz/v1/projects/{id}
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `id` | `string` |  |  |


### 返回值

#### 返回对象
对象为空

## 构建项目

### 请求路径
```http
POST /abfuzz/v1/projects/{id}:build
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `id` | `string` |  | 指定项目ID |


#### Query 参数
| 参数名 | 参数类型 | 格式类型 | 是否必须 | 默认值 | 说明 |
|---|---|---|---|---|---|
| `source_package_id` | `string` |  | 否 |  | 指定源码包ID，指定特定的版本及分支 |
| `platform` | `string` | `Platform` | 否 |  | 指定某个特定的平台 |
| `language` | `string` |  | 否 |  | 指定某个特定的语言 |
| `lang_vm` | `abfuzz.LangVM` |  | 否 |  | 指定某个特定的语言虚拟机 |
| `fuzzers` | `Array<string>` |  | 否 |  | 特别指定fuzzers去运行模糊测试，项目中的其他fuzzer不运行 |
| `fuzzing` | `boolean` |  | 否 |  | 是否编译完成后直接运行fuzzing |
| `fuzzing_timeout` | `integer` | `Int64` | 否 |  | 指定fuzzing的timeout |
| `reproduce` | `boolean` |  | 否 |  | 是否编译完成后直接运行reproduce |


#### Body 请求对象
| type | description |
|---|---|
| `Array<string>` |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|


#### `mojo.core.Any`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `@type` | `string` |  | Y |  |


#### `mojo.core.Error`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` | `ErrorCode` | N |  | The error code |
| `message` | `string` |  | N |  | A developer-facing error message, which should be in English. Anyuser-facing error message should be localized and sent in the[google.rpc.Status.details][google.rpc.Status.details] field, or localized by the client. |
| `details` | `Array<mojo.core.Any>` |  | N |  | A list of messages that carry the error details.  There is a common set ofmessage types for APIs to use. |


## 继续项目中被手动停止的fuzzer进行模糊测试

### 请求路径
```http
POST /abfuzz/v1/projects/{id}:continue
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `id` | `string` |  | 指定项目ID |


#### Query 参数
| 参数名 | 参数类型 | 格式类型 | 是否必须 | 默认值 | 说明 |
|---|---|---|---|---|---|
| `source_package_id` | `string` |  | 是 |  | 指定源码包ID，指定特定的版本及分支 |
| `build_operation_name` | `string` |  | 是 |  | 指定是那次build后的fuzzer进行fuzz，该id为build_project返回的operation_name |
| `platform` | `string` | `Platform` | 否 |  | 指定某个特定的平台 |
| `language` | `string` |  | 否 |  | 指定某个特定的语言 |
| `lang_vm` | `abfuzz.LangVM` |  | 否 |  | 指定某个特定的语言虚拟机 |
| `fuzzers` | `Array<string>` |  | 否 |  | 特别指定fuzzers去继续进行模糊测试，项目中的其他fuzzer不运行 |
| `fuzzing_timeout` | `integer` | `Int64` | 否 |  | 指定fuzzing的timeout |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|


#### `mojo.core.Any`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `@type` | `string` |  | Y |  |


#### `mojo.core.Error`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` | `ErrorCode` | N |  | The error code |
| `message` | `string` |  | N |  | A developer-facing error message, which should be in English. Anyuser-facing error message should be localized and sent in the[google.rpc.Status.details][google.rpc.Status.details] field, or localized by the client. |
| `details` | `Array<mojo.core.Any>` |  | N |  | A list of messages that carry the error details.  There is a common set ofmessage types for APIs to use. |


## 对项目进行模糊测试

### 请求路径
```http
POST /abfuzz/v1/projects/{id}:fuzzing
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `id` | `string` |  | 指定项目ID |


#### Query 参数
| 参数名 | 参数类型 | 格式类型 | 是否必须 | 默认值 | 说明 |
|---|---|---|---|---|---|
| `source_package_id` | `string` |  | 是 |  | 指定源码包ID，指定特定的版本及分支 |
| `build_operation_name` | `string` |  | 是 |  | 指定是那次build后的fuzzer进行fuzz，该id为build_project返回的operation_name |
| `build_id` | `string` |  | 否 |  | 指定某个特定的build |
| `platform` | `string` | `Platform` | 否 |  | 指定某个特定的平台 |
| `language` | `string` |  | 否 |  | 指定某个特定的语言 |
| `lang_vm` | `abfuzz.LangVM` |  | 否 |  | 指定某个特定的语言虚拟机 |
| `fuzzers` | `Array<string>` |  | 否 |  | 特别指定fuzzers去运行模糊测试，项目中的其他fuzzer不运行 |
| `fuzzing_timeout` | `integer` | `Int64` | 否 |  | 指定fuzzing的timeout |
| `reproduce` | `boolean` |  | 否 |  | 是否在fuzz之前运行reproduce |


#### Body 请求对象
| type | description |
|---|---|
| `Array<string>` |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|


#### `mojo.core.Any`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `@type` | `string` |  | Y |  |


#### `mojo.core.Error`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` | `ErrorCode` | N |  | The error code |
| `message` | `string` |  | N |  | A developer-facing error message, which should be in English. Anyuser-facing error message should be localized and sent in the[google.rpc.Status.details][google.rpc.Status.details] field, or localized by the client. |
| `details` | `Array<mojo.core.Any>` |  | N |  | A list of messages that carry the error details.  There is a common set ofmessage types for APIs to use. |


## 重现项目的bug

### 请求路径
```http
POST /abfuzz/v1/projects/{id}:reproduce
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `id` | `string` |  | 项目ID |


#### Query 参数
| 参数名 | 参数类型 | 格式类型 | 是否必须 | 默认值 | 说明 |
|---|---|---|---|---|---|
| `source_package_id` | `string` |  | 是 |  | 源码包ID |
| `build_operation_name` | `string` |  | 是 |  | 指定是那次build后的fuzzer进行fuzz，该id为build_project返回的operation_name |
| `build_id` | `string` |  | 否 |  | 指定某个特定的build |
| `platform` | `string` | `Platform` | 否 |  | 指定某个特定的平台 |
| `language` | `string` |  | 否 |  | 指定某个特定的语言 |
| `lang_vm` | `abfuzz.LangVM` |  | 否 |  | 指定某个特定的语言虚拟机 |
| `fuzzers` | `Array<string>` |  | 否 |  | 特别指定fuzzers去进行重现，项目中的其他fuzzer不运行 |


#### Body 请求对象
| type | description |
|---|---|
| `Array<string>` |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|


#### `mojo.core.Any`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `@type` | `string` |  | Y |  |


#### `mojo.core.Error`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` | `ErrorCode` | N |  | The error code |
| `message` | `string` |  | N |  | A developer-facing error message, which should be in English. Anyuser-facing error message should be localized and sent in the[google.rpc.Status.details][google.rpc.Status.details] field, or localized by the client. |
| `details` | `Array<mojo.core.Any>` |  | N |  | A list of messages that carry the error details.  There is a common set ofmessage types for APIs to use. |


## 停止项目进行中的模糊测试

### 请求路径
```http
POST /abfuzz/v1/projects/{id}:stop
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `id` | `string` |  | 指定项目ID |


#### Query 参数
| 参数名 | 参数类型 | 格式类型 | 是否必须 | 默认值 | 说明 |
|---|---|---|---|---|---|
| `source_package_id` | `string` |  | 是 |  | 指定源码包ID，指定特定的版本及分支 |
| `operation_name` | `string` |  | 是 |  | 每次启动执行时返回的operation name |
| `platform` | `string` | `Platform` | 否 |  | 指定某个特定的平台 |
| `language` | `string` |  | 否 |  | 指定某个特定的语言 |
| `lang_vm` | `abfuzz.LangVM` |  | 否 |  | 指定某个特定的语言虚拟机 |
| `fuzzers` | `Array<string>` |  | 否 |  | 特别指定fuzzers停止正在执行的模糊测试，项目中的其他fuzzer不停止 |


### 返回值

#### 返回对象
对象为空

## 停止项目进行中的模糊测试

### 请求路径
```http
POST /abfuzz/v1/projects/{id}:stop-fuzzing
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `id` | `string` |  | 指定项目ID |


#### Query 参数
| 参数名 | 参数类型 | 格式类型 | 是否必须 | 默认值 | 说明 |
|---|---|---|---|---|---|
| `source_package_id` | `string` |  | 是 |  | 指定源码包ID，指定特定的版本及分支 |
| `operation_name` | `string` |  | 是 |  | 每次启动执行时返回的operation name |
| `platform` | `string` | `Platform` | 否 |  | 指定某个特定的平台 |
| `language` | `string` |  | 否 |  | 指定某个特定的语言 |
| `lang_vm` | `abfuzz.LangVM` |  | 否 |  | 指定某个特定的语言虚拟机 |
| `fuzzers` | `Array<string>` |  | 否 |  | 特别指定fuzzers停止正在执行的模糊测试，项目中的其他fuzzer不停止 |


### 返回值

#### 返回对象
对象为空

## 更新项目

### 请求路径
```http
PUT /abfuzz/v1/projects/{project_id}
```


### 请求参数

#### Body 请求对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | project id |
| `name` | `string` |  | N |  | project name, normally the same with the custom's main source repo |
| `description` | `string` |  | N |  | project description |
| `sourcePackage` | `abfuzz.SourcePackage` |  | N |  | the source package for the project, only used in the create project |
| `language` | `string` |  | N |  | the program languages for the project，如果有多种语言，那么fuzzer中就需要指定语言 |
| `userId` | `string` |  | N |  | 用户相关的Project的用户ID |
| `externalId` | `string` |  | N |  | 第三方系统对应project的id |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | the sanitizers will be used for the project |
| `engines` | `Array<abfuzz.Engine>` |  | Y |  | the fazz engine name |
| `platforms` | `Array<string>` |  | N |  | The platform that this job can run on. |
| `langVms` | `Array<abfuzz.LangVM>` |  | N |  | The lang_vm platform that this job can run on if the language is Java. |
| `fuzzers` | `Array<abfuzz.Fuzzer>` |  | Y |  | the fuzzers in the project |
| `createTime` | `string` | `Timestamp` | N |  | the create time of the project |
| `updateTime` | `string` | `Timestamp` | N |  | the update time of the project |
| `deleteTime` | `string` | `Timestamp` | N |  | the delete time of the project, for the soft delete |


#### `abfuzz.CaseStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `disable` | `array` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.ContextCase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `steps` | `Array<abfuzz.CaseStep>` |  | N |  |


#### `abfuzz.ContextRequest`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `setup` | `abfuzz.ContextSetup` |  | N |  | 全局设置，在cases之前执行 |
| `cases` | `Array<abfuzz.ContextCase>` |  | N |  | OpenAPI里的请求(用path和method来对应)，包含在cases里的优先执行，没有包含在cases里的请求，每个请求自动生成一个case，在cases之后依次自行 |
| `teardown` | `abfuzz.ContextTeardown` |  | N |  | 在cases之后执行的请求 |


#### `abfuzz.ContextSetup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.SetupStep>` |  | N |  |


#### `abfuzz.ContextTeardown`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.TeardownStep>` |  | N |  |


#### `abfuzz.CorpusConfig`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `includings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `excludings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `httpBaseUrl` | `string` | `Url` | N |  | URl type<br>Uniform Resource Identifier (URI) is a string of characters used to identify a resource.<br>A Uniform Resource Name (URN) may be compared to a person's name,while a Uniform Resource Locator (URL) may be compared to their street address.In other words, a URN identifies an item and a URL provides a method for finding it.<br> |
| `httpMappingPort` | `integer` | `Int32` | N |  |
| `httpMappingHost` | `string` |  | N |  |
| `httpStaticValues` | `mojo.core.Object` |  | N |  | Object type |
| `httpCorpuses` | `Array<abfuzz.HttpCorpus>` |  | N |  |
| `combinedApiPath` | `string` |  | N |  |
| `asyncApi` | `boolean` |  | N |  |


#### `abfuzz.EndPoint`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` |  | N |  |
| `http` | `abfuzz.EndPoint.Http` |  | N |  |  |
| `priority` | `integer` | `Int32` | N |  |


#### `abfuzz.EndPoint.Http`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `path` | `string` |  | N |  |
| `methods` | `Array<string>` |  | N |  |


#### `abfuzz.Engine`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the engine |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.Entity`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `stepType` | `string` |  | N |  |
| `interfaces` | `Array<abfuzz.Entity.Interface>` |  | N |  |


#### `abfuzz.Entity.Interface`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `type` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | N |  |
| `pathParams` | `Map<string, string>` |  | N |  |
| `queryParams` | `Map<string, string>` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `response` | `mojo.http.Response` |  | N |  | Response represents the response from an HTTP request. |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |


#### `abfuzz.Fixup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.Fuzzer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | Y |  | the fuzzer name |
| `group` | `string` |  | N |  | the group id for the fuzzers |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `fuzzerPackage` | `abfuzz.FuzzerPackage` |  | N |  | the fuzzer package of the fuzzer |
| `executablePath` | `string` |  | N |  | Fuzzer's main executable path, relative to root. |
| `executableArguments` | `Array<string>` |  | N |  | the input arguments for the executable, it will be skipped when launcher_script is set. |
| `executableEnvironment` | `Map<string, string>` |  | N |  | the environment setting for the executable |
| `launcherScript` | `string` |  | N |  | Custom script that should be used to launch executable for this fuzzer.It will not try to launch the target if "@service-auto-start" set. |
| `timeout` | `integer` | `Int64` | N |  | Fuzzing timeout. |
| `testcaseTimeoutMillis` | `integer` | `Int64` | N |  | Testcase timeout. |
| `autoStop` | `boolean` |  | N |  | Fuzzing automatic stop. |
| `autoHarness` | `boolean` |  | N |  | auto generate harness |
| `customMutatorPlugin` | `string` |  | N |  | custom mutator plugin for Fuzz Engine |
| `customAllowTargets` | `Array<string>` |  | N |  | custom file name & function name targets for instruments and coverage, like white list. |
| `customDenyTargets` | `Array<string>` |  | N |  | custom targets for instruments and coverage, like white list. |
| `context` | `abfuzz.ContextRequest` |  | N |  | context request |
| `corpusPath` | `string` |  | N |  | the special corpus path |
| `preprocessScript` | `string` |  | N |  | the preprocess script info |
| `onlyFunctionalTest` | `boolean` |  | N |  | is only fuzzing user corpus |
| `autoHarnessCommand` | `string` |  | N |  | execute the command before run auto harness generation |
| `autoHarnessScript` | `string` |  | N |  | execute the script before run auto harness generation if the command is empty |
| `autoHarnessArguments` | `Array<string>` |  | N |  | the arguments when to run the harness generation, usually for including path setting |
| `autoHarnessBlacklist` | `Array<string>` |  | N |  | the blacklist files to run the harness generation, these files will be skipped. |
| `autoHarnessWebServer` | `boolean` |  | N |  | determine if the java auto harness server is a web server |
| `metadata` | `Map<string, mojo.core.Value>` |  | N |  | the meta information for the fuzzing |
| `corpus` | `abfuzz.CorpusConfig` |  | N |  |  |
| `entities` | `Array<abfuzz.Entity>` |  | N |  |


#### `abfuzz.FuzzerPackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `type` | `string` |  | N |  | the type of the source package, oneof the whitebox, greybox, blackbox |
| `filename` | `string` |  | N |  | Filename for the custom binary. |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |


#### `abfuzz.HttpCorpus`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | Y |  |
| `staticValues` | `mojo.core.Object` |  | N |  | Object type |
| `fixups` | `Array<abfuzz.Fixup>` |  | N |  |
| `relations` | `Array<abfuzz.Relation>` |  | N |  |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `abfuzz.Relation`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.Repository`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `url` | `string` | `Url` | N |  | the git url for the source package |
| `branch` | `string` |  | N |  | the git branch for the source package |
| `tag` | `string` |  | N |  | the git tag for the source package |
| `commitTime` | `string` | `Timestamp` | N |  | the used commit time for the source package |
| `commitKey` | `string` |  | N |  | the used commit key for the source package, it is hash key for git |


#### `abfuzz.Sanitizer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the sanitizer |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.SetupStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | 更新上下文时发起的请求，不要求包含在OpenAPI中 |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |
| `filter` | `Array<string>` |  | N |  |
| `order` | `string` |  | N |  |


#### `abfuzz.SourcePackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the uuid for the source package |
| `projectId` | `string` |  | N |  | the project id which including the source package |
| `projectName` | `string` |  | N |  | the project name which including the source package |
| `projectVersion` | `string` |  | N |  | the project version |
| `type` | `string` |  | N |  | the type of the source packagewhitebox, graybox, blackbox |
| `name` | `string` |  | N |  | the name of the module of the source package |
| `fuzzRepo` | `abfuzz.Repository` |  | N |  | the repository information for the fuzz code reposisity in the source package |
| `sourceRepo` | `abfuzz.Repository` |  | N |  | the repository information for the main code reposisity of custom in the source package |
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `fileSet` | `Array<abfuzz.StorageFile>` |  | N |  | the file set for the source package |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary or raw source code package. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |
| `customVersion` | `string` |  | N |  | more version info for the custom system |
| `codeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `createTime` | `string` | `Timestamp` | N |  | create time of the source code package |
| `updateTime` | `string` | `Timestamp` | N |  | update time of the source code package |


#### `abfuzz.StorageFile`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  | 文件在minio上的 地址 |
| `checksum` | `string` | `Checksum` | N |  | 文件的hash校验码 |
| `type` | `string` |  | N |  | 文件类型，比如： `corpus`, `harness`, `source`, `build-script`, `openapi`, `jar` |
| `group` | `string` |  | N |  | 文件属于某个组，可以是某个fuzzer |


#### `abfuzz.TeardownStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


#### `mojo.http.Request`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `method` | `string` |  | N |  | method specifies the HTTP method (GET, POST, PUT, etc.).For client requests, an empty string means GET. |
| `url` | `string` | `Url` | N |  | url specifies either the URI being requested (for serverrequests) or the URL to access (for client requests). |
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Response`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |


#### `mojo.http.Status`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `integer` | `Int32` | N |  |
| `reason` | `string` |  | N |  |


#### `mojo.http.Version`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `major` | `integer` | `Int32` | N |  |
| `minor` | `integer` | `Int32` | N |  |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | project id |
| `name` | `string` |  | N |  | project name, normally the same with the custom's main source repo |
| `description` | `string` |  | N |  | project description |
| `sourcePackage` | `abfuzz.SourcePackage` |  | N |  | the source package for the project, only used in the create project |
| `language` | `string` |  | N |  | the program languages for the project，如果有多种语言，那么fuzzer中就需要指定语言 |
| `userId` | `string` |  | N |  | 用户相关的Project的用户ID |
| `externalId` | `string` |  | N |  | 第三方系统对应project的id |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | the sanitizers will be used for the project |
| `engines` | `Array<abfuzz.Engine>` |  | Y |  | the fazz engine name |
| `platforms` | `Array<string>` |  | N |  | The platform that this job can run on. |
| `langVms` | `Array<abfuzz.LangVM>` |  | N |  | The lang_vm platform that this job can run on if the language is Java. |
| `fuzzers` | `Array<abfuzz.Fuzzer>` |  | Y |  | the fuzzers in the project |
| `createTime` | `string` | `Timestamp` | N |  | the create time of the project |
| `updateTime` | `string` | `Timestamp` | N |  | the update time of the project |
| `deleteTime` | `string` | `Timestamp` | N |  | the delete time of the project, for the soft delete |


#### `abfuzz.CaseStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `disable` | `array` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.ContextCase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `steps` | `Array<abfuzz.CaseStep>` |  | N |  |


#### `abfuzz.ContextRequest`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `setup` | `abfuzz.ContextSetup` |  | N |  | 全局设置，在cases之前执行 |
| `cases` | `Array<abfuzz.ContextCase>` |  | N |  | OpenAPI里的请求(用path和method来对应)，包含在cases里的优先执行，没有包含在cases里的请求，每个请求自动生成一个case，在cases之后依次自行 |
| `teardown` | `abfuzz.ContextTeardown` |  | N |  | 在cases之后执行的请求 |


#### `abfuzz.ContextSetup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.SetupStep>` |  | N |  |


#### `abfuzz.ContextTeardown`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.TeardownStep>` |  | N |  |


#### `abfuzz.CorpusConfig`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `includings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `excludings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `httpBaseUrl` | `string` | `Url` | N |  | URl type<br>Uniform Resource Identifier (URI) is a string of characters used to identify a resource.<br>A Uniform Resource Name (URN) may be compared to a person's name,while a Uniform Resource Locator (URL) may be compared to their street address.In other words, a URN identifies an item and a URL provides a method for finding it.<br> |
| `httpMappingPort` | `integer` | `Int32` | N |  |
| `httpMappingHost` | `string` |  | N |  |
| `httpStaticValues` | `mojo.core.Object` |  | N |  | Object type |
| `httpCorpuses` | `Array<abfuzz.HttpCorpus>` |  | N |  |
| `combinedApiPath` | `string` |  | N |  |
| `asyncApi` | `boolean` |  | N |  |


#### `abfuzz.EndPoint`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` |  | N |  |
| `http` | `abfuzz.EndPoint.Http` |  | N |  |  |
| `priority` | `integer` | `Int32` | N |  |


#### `abfuzz.EndPoint.Http`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `path` | `string` |  | N |  |
| `methods` | `Array<string>` |  | N |  |


#### `abfuzz.Engine`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the engine |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.Entity`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `stepType` | `string` |  | N |  |
| `interfaces` | `Array<abfuzz.Entity.Interface>` |  | N |  |


#### `abfuzz.Entity.Interface`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `type` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | N |  |
| `pathParams` | `Map<string, string>` |  | N |  |
| `queryParams` | `Map<string, string>` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `response` | `mojo.http.Response` |  | N |  | Response represents the response from an HTTP request. |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |


#### `abfuzz.Fixup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.Fuzzer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | Y |  | the fuzzer name |
| `group` | `string` |  | N |  | the group id for the fuzzers |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `fuzzerPackage` | `abfuzz.FuzzerPackage` |  | N |  | the fuzzer package of the fuzzer |
| `executablePath` | `string` |  | N |  | Fuzzer's main executable path, relative to root. |
| `executableArguments` | `Array<string>` |  | N |  | the input arguments for the executable, it will be skipped when launcher_script is set. |
| `executableEnvironment` | `Map<string, string>` |  | N |  | the environment setting for the executable |
| `launcherScript` | `string` |  | N |  | Custom script that should be used to launch executable for this fuzzer.It will not try to launch the target if "@service-auto-start" set. |
| `timeout` | `integer` | `Int64` | N |  | Fuzzing timeout. |
| `testcaseTimeoutMillis` | `integer` | `Int64` | N |  | Testcase timeout. |
| `autoStop` | `boolean` |  | N |  | Fuzzing automatic stop. |
| `autoHarness` | `boolean` |  | N |  | auto generate harness |
| `customMutatorPlugin` | `string` |  | N |  | custom mutator plugin for Fuzz Engine |
| `customAllowTargets` | `Array<string>` |  | N |  | custom file name & function name targets for instruments and coverage, like white list. |
| `customDenyTargets` | `Array<string>` |  | N |  | custom targets for instruments and coverage, like white list. |
| `context` | `abfuzz.ContextRequest` |  | N |  | context request |
| `corpusPath` | `string` |  | N |  | the special corpus path |
| `preprocessScript` | `string` |  | N |  | the preprocess script info |
| `onlyFunctionalTest` | `boolean` |  | N |  | is only fuzzing user corpus |
| `autoHarnessCommand` | `string` |  | N |  | execute the command before run auto harness generation |
| `autoHarnessScript` | `string` |  | N |  | execute the script before run auto harness generation if the command is empty |
| `autoHarnessArguments` | `Array<string>` |  | N |  | the arguments when to run the harness generation, usually for including path setting |
| `autoHarnessBlacklist` | `Array<string>` |  | N |  | the blacklist files to run the harness generation, these files will be skipped. |
| `autoHarnessWebServer` | `boolean` |  | N |  | determine if the java auto harness server is a web server |
| `metadata` | `Map<string, mojo.core.Value>` |  | N |  | the meta information for the fuzzing |
| `corpus` | `abfuzz.CorpusConfig` |  | N |  |  |
| `entities` | `Array<abfuzz.Entity>` |  | N |  |


#### `abfuzz.FuzzerPackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `type` | `string` |  | N |  | the type of the source package, oneof the whitebox, greybox, blackbox |
| `filename` | `string` |  | N |  | Filename for the custom binary. |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |


#### `abfuzz.HttpCorpus`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | Y |  |
| `staticValues` | `mojo.core.Object` |  | N |  | Object type |
| `fixups` | `Array<abfuzz.Fixup>` |  | N |  |
| `relations` | `Array<abfuzz.Relation>` |  | N |  |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `abfuzz.Relation`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.Repository`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `url` | `string` | `Url` | N |  | the git url for the source package |
| `branch` | `string` |  | N |  | the git branch for the source package |
| `tag` | `string` |  | N |  | the git tag for the source package |
| `commitTime` | `string` | `Timestamp` | N |  | the used commit time for the source package |
| `commitKey` | `string` |  | N |  | the used commit key for the source package, it is hash key for git |


#### `abfuzz.Sanitizer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the sanitizer |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.SetupStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | 更新上下文时发起的请求，不要求包含在OpenAPI中 |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |
| `filter` | `Array<string>` |  | N |  |
| `order` | `string` |  | N |  |


#### `abfuzz.SourcePackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the uuid for the source package |
| `projectId` | `string` |  | N |  | the project id which including the source package |
| `projectName` | `string` |  | N |  | the project name which including the source package |
| `projectVersion` | `string` |  | N |  | the project version |
| `type` | `string` |  | N |  | the type of the source packagewhitebox, graybox, blackbox |
| `name` | `string` |  | N |  | the name of the module of the source package |
| `fuzzRepo` | `abfuzz.Repository` |  | N |  | the repository information for the fuzz code reposisity in the source package |
| `sourceRepo` | `abfuzz.Repository` |  | N |  | the repository information for the main code reposisity of custom in the source package |
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `fileSet` | `Array<abfuzz.StorageFile>` |  | N |  | the file set for the source package |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary or raw source code package. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |
| `customVersion` | `string` |  | N |  | more version info for the custom system |
| `codeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `createTime` | `string` | `Timestamp` | N |  | create time of the source code package |
| `updateTime` | `string` | `Timestamp` | N |  | update time of the source code package |


#### `abfuzz.StorageFile`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  | 文件在minio上的 地址 |
| `checksum` | `string` | `Checksum` | N |  | 文件的hash校验码 |
| `type` | `string` |  | N |  | 文件类型，比如： `corpus`, `harness`, `source`, `build-script`, `openapi`, `jar` |
| `group` | `string` |  | N |  | 文件属于某个组，可以是某个fuzzer |


#### `abfuzz.TeardownStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


#### `mojo.http.Request`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `method` | `string` |  | N |  | method specifies the HTTP method (GET, POST, PUT, etc.).For client requests, an empty string means GET. |
| `url` | `string` | `Url` | N |  | url specifies either the URI being requested (for serverrequests) or the URL to access (for client requests). |
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Response`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `status` | `mojo.http.Status` |  | N |  |  |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Status`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `integer` | `Int32` | N |  |
| `reason` | `string` |  | N |  |


#### `mojo.http.Version`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `major` | `integer` | `Int32` | N |  |
| `minor` | `integer` | `Int32` | N |  |


## 查询指定项目下的build列表

### 请求路径
```http
GET /abfuzz/v1/projects/{project_id}/builds
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  | 项目的ID |


#### Query 参数
| 参数名 | 参数类型 | 格式类型 | 是否必须 | 默认值 | 说明 |
|---|---|---|---|---|---|
| `source_package_id` | `string` |  | 否 |  | 指定源码包ID，指定特定的版本及分支 |
| `operation_name` | `string` |  | 否 |  | 进行build操作后的operation_name |
| `state` | `string` |  | 否 |  | 指定build的状态 |
| `page_size` | `integer` | `Int32` | 否 |  |  |
| `page_token` | `string` |  | 否 |  |  |
| `skip` | `integer` | `Int32` | 否 |  |  |


### 返回值

#### 返回对象
| type | description |
|---|---|
| `Array<abfuzz.Build>` |  |


#### `abfuzz.Build`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `projectId` | `string` |  | N |  |
| `sourcePackageId` | `string` |  | N |  |
| `operationName` | `string` |  | N |  |
| `platform` | `string` | `Platform` | N |  | 指定的平台 |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的语言虚拟机，JDK for java |
| `engine` | `abfuzz.Engine` |  | N |  | 指定的引擎 |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | 指定的sanitizer |
| `fuzzers` | `Array<abfuzz.Fuzzer>` |  | N |  | 指定输出的fuzzer |
| `state` | `string` |  | N |  | finished, failed |
| `error` | `mojo.core.Error` |  | N |  | build失败后的错误信息 |
| `log` | `string` |  | N |  |
| `logType` | `string` |  | N |  | text, url, binary (std base64) |
| `createTime` | `string` | `Timestamp` | N |  |  |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.CaseStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `disable` | `array` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.ContextCase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `steps` | `Array<abfuzz.CaseStep>` |  | N |  |


#### `abfuzz.ContextRequest`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `setup` | `abfuzz.ContextSetup` |  | N |  | 全局设置，在cases之前执行 |
| `cases` | `Array<abfuzz.ContextCase>` |  | N |  | OpenAPI里的请求(用path和method来对应)，包含在cases里的优先执行，没有包含在cases里的请求，每个请求自动生成一个case，在cases之后依次自行 |
| `teardown` | `abfuzz.ContextTeardown` |  | N |  | 在cases之后执行的请求 |


#### `abfuzz.ContextSetup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.SetupStep>` |  | N |  |


#### `abfuzz.ContextTeardown`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.TeardownStep>` |  | N |  |


#### `abfuzz.CorpusConfig`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `includings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `excludings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `httpBaseUrl` | `string` | `Url` | N |  | URl type<br>Uniform Resource Identifier (URI) is a string of characters used to identify a resource.<br>A Uniform Resource Name (URN) may be compared to a person's name,while a Uniform Resource Locator (URL) may be compared to their street address.In other words, a URN identifies an item and a URL provides a method for finding it.<br> |
| `httpMappingPort` | `integer` | `Int32` | N |  |
| `httpMappingHost` | `string` |  | N |  |
| `httpStaticValues` | `mojo.core.Object` |  | N |  | Object type |
| `httpCorpuses` | `Array<abfuzz.HttpCorpus>` |  | N |  |
| `combinedApiPath` | `string` |  | N |  |
| `asyncApi` | `boolean` |  | N |  |


#### `abfuzz.EndPoint`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` |  | N |  |
| `http` | `abfuzz.EndPoint.Http` |  | N |  |  |
| `priority` | `integer` | `Int32` | N |  |


#### `abfuzz.EndPoint.Http`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `path` | `string` |  | N |  |
| `methods` | `Array<string>` |  | N |  |


#### `abfuzz.Engine`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the engine |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.Entity`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `stepType` | `string` |  | N |  |
| `interfaces` | `Array<abfuzz.Entity.Interface>` |  | N |  |


#### `abfuzz.Entity.Interface`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `type` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | N |  |
| `pathParams` | `Map<string, string>` |  | N |  |
| `queryParams` | `Map<string, string>` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `response` | `mojo.http.Response` |  | N |  | Response represents the response from an HTTP request. |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |


#### `abfuzz.Fixup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.Fuzzer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | Y |  | the fuzzer name |
| `group` | `string` |  | N |  | the group id for the fuzzers |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `fuzzerPackage` | `abfuzz.FuzzerPackage` |  | N |  | the fuzzer package of the fuzzer |
| `executablePath` | `string` |  | N |  | Fuzzer's main executable path, relative to root. |
| `executableArguments` | `Array<string>` |  | N |  | the input arguments for the executable, it will be skipped when launcher_script is set. |
| `executableEnvironment` | `Map<string, string>` |  | N |  | the environment setting for the executable |
| `launcherScript` | `string` |  | N |  | Custom script that should be used to launch executable for this fuzzer.It will not try to launch the target if "@service-auto-start" set. |
| `timeout` | `integer` | `Int64` | N |  | Fuzzing timeout. |
| `testcaseTimeoutMillis` | `integer` | `Int64` | N |  | Testcase timeout. |
| `autoStop` | `boolean` |  | N |  | Fuzzing automatic stop. |
| `autoHarness` | `boolean` |  | N |  | auto generate harness |
| `customMutatorPlugin` | `string` |  | N |  | custom mutator plugin for Fuzz Engine |
| `customAllowTargets` | `Array<string>` |  | N |  | custom file name & function name targets for instruments and coverage, like white list. |
| `customDenyTargets` | `Array<string>` |  | N |  | custom targets for instruments and coverage, like white list. |
| `context` | `abfuzz.ContextRequest` |  | N |  | context request |
| `corpusPath` | `string` |  | N |  | the special corpus path |
| `preprocessScript` | `string` |  | N |  | the preprocess script info |
| `onlyFunctionalTest` | `boolean` |  | N |  | is only fuzzing user corpus |
| `autoHarnessCommand` | `string` |  | N |  | execute the command before run auto harness generation |
| `autoHarnessScript` | `string` |  | N |  | execute the script before run auto harness generation if the command is empty |
| `autoHarnessArguments` | `Array<string>` |  | N |  | the arguments when to run the harness generation, usually for including path setting |
| `autoHarnessBlacklist` | `Array<string>` |  | N |  | the blacklist files to run the harness generation, these files will be skipped. |
| `autoHarnessWebServer` | `boolean` |  | N |  | determine if the java auto harness server is a web server |
| `metadata` | `Map<string, mojo.core.Value>` |  | N |  | the meta information for the fuzzing |
| `corpus` | `abfuzz.CorpusConfig` |  | N |  |  |
| `entities` | `Array<abfuzz.Entity>` |  | N |  |


#### `abfuzz.FuzzerPackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `type` | `string` |  | N |  | the type of the source package, oneof the whitebox, greybox, blackbox |
| `filename` | `string` |  | N |  | Filename for the custom binary. |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |


#### `abfuzz.HttpCorpus`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | Y |  |
| `staticValues` | `mojo.core.Object` |  | N |  | Object type |
| `fixups` | `Array<abfuzz.Fixup>` |  | N |  |
| `relations` | `Array<abfuzz.Relation>` |  | N |  |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `abfuzz.Relation`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.Sanitizer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the sanitizer |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.SetupStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | 更新上下文时发起的请求，不要求包含在OpenAPI中 |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |
| `filter` | `Array<string>` |  | N |  |
| `order` | `string` |  | N |  |


#### `abfuzz.TeardownStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `mojo.core.Any`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `@type` | `string` |  | Y |  |


#### `mojo.core.Error`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` | `ErrorCode` | N |  | The error code |
| `message` | `string` |  | N |  | A developer-facing error message, which should be in English. Anyuser-facing error message should be localized and sent in the[google.rpc.Status.details][google.rpc.Status.details] field, or localized by the client. |
| `details` | `Array<mojo.core.Any>` |  | N |  | A list of messages that carry the error details.  There is a common set ofmessage types for APIs to use. |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


#### `mojo.http.Request`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `method` | `string` |  | N |  | method specifies the HTTP method (GET, POST, PUT, etc.).For client requests, an empty string means GET. |
| `url` | `string` | `Url` | N |  | url specifies either the URI being requested (for serverrequests) or the URL to access (for client requests). |
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Response`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |


#### `mojo.http.Status`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `integer` | `Int32` | N |  |
| `reason` | `string` |  | N |  |


#### `mojo.http.Version`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `major` | `integer` | `Int32` | N |  |
| `minor` | `integer` | `Int32` | N |  |


## 获取指定的build信息

### 请求路径
```http
GET /abfuzz/v1/projects/{project_id}/builds/{id}
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |
| `id` | `string` |  |  |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `projectId` | `string` |  | N |  |
| `sourcePackageId` | `string` |  | N |  |
| `operationName` | `string` |  | N |  |
| `platform` | `string` | `Platform` | N |  | 指定的平台 |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的语言虚拟机，JDK for java |
| `engine` | `abfuzz.Engine` |  | N |  | 指定的引擎 |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | 指定的sanitizer |
| `fuzzers` | `Array<abfuzz.Fuzzer>` |  | N |  | 指定输出的fuzzer |
| `state` | `string` |  | N |  | finished, failed |
| `error` | `mojo.core.Error` |  | N |  | build失败后的错误信息 |
| `log` | `string` |  | N |  |
| `logType` | `string` |  | N |  | text, url, binary (std base64) |
| `createTime` | `string` | `Timestamp` | N |  |  |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.CaseStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `disable` | `array` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.ContextCase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `steps` | `Array<abfuzz.CaseStep>` |  | N |  |


#### `abfuzz.ContextRequest`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `setup` | `abfuzz.ContextSetup` |  | N |  | 全局设置，在cases之前执行 |
| `cases` | `Array<abfuzz.ContextCase>` |  | N |  | OpenAPI里的请求(用path和method来对应)，包含在cases里的优先执行，没有包含在cases里的请求，每个请求自动生成一个case，在cases之后依次自行 |
| `teardown` | `abfuzz.ContextTeardown` |  | N |  | 在cases之后执行的请求 |


#### `abfuzz.ContextSetup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.SetupStep>` |  | N |  |


#### `abfuzz.ContextTeardown`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.TeardownStep>` |  | N |  |


#### `abfuzz.CorpusConfig`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `includings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `excludings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `httpBaseUrl` | `string` | `Url` | N |  | URl type<br>Uniform Resource Identifier (URI) is a string of characters used to identify a resource.<br>A Uniform Resource Name (URN) may be compared to a person's name,while a Uniform Resource Locator (URL) may be compared to their street address.In other words, a URN identifies an item and a URL provides a method for finding it.<br> |
| `httpMappingPort` | `integer` | `Int32` | N |  |
| `httpMappingHost` | `string` |  | N |  |
| `httpStaticValues` | `mojo.core.Object` |  | N |  | Object type |
| `httpCorpuses` | `Array<abfuzz.HttpCorpus>` |  | N |  |
| `combinedApiPath` | `string` |  | N |  |
| `asyncApi` | `boolean` |  | N |  |


#### `abfuzz.EndPoint`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` |  | N |  |
| `http` | `abfuzz.EndPoint.Http` |  | N |  |  |
| `priority` | `integer` | `Int32` | N |  |


#### `abfuzz.EndPoint.Http`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `path` | `string` |  | N |  |
| `methods` | `Array<string>` |  | N |  |


#### `abfuzz.Engine`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the engine |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.Entity`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `stepType` | `string` |  | N |  |
| `interfaces` | `Array<abfuzz.Entity.Interface>` |  | N |  |


#### `abfuzz.Entity.Interface`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `type` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | N |  |
| `pathParams` | `Map<string, string>` |  | N |  |
| `queryParams` | `Map<string, string>` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `response` | `mojo.http.Response` |  | N |  | Response represents the response from an HTTP request. |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |


#### `abfuzz.Fixup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.Fuzzer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | Y |  | the fuzzer name |
| `group` | `string` |  | N |  | the group id for the fuzzers |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `fuzzerPackage` | `abfuzz.FuzzerPackage` |  | N |  | the fuzzer package of the fuzzer |
| `executablePath` | `string` |  | N |  | Fuzzer's main executable path, relative to root. |
| `executableArguments` | `Array<string>` |  | N |  | the input arguments for the executable, it will be skipped when launcher_script is set. |
| `executableEnvironment` | `Map<string, string>` |  | N |  | the environment setting for the executable |
| `launcherScript` | `string` |  | N |  | Custom script that should be used to launch executable for this fuzzer.It will not try to launch the target if "@service-auto-start" set. |
| `timeout` | `integer` | `Int64` | N |  | Fuzzing timeout. |
| `testcaseTimeoutMillis` | `integer` | `Int64` | N |  | Testcase timeout. |
| `autoStop` | `boolean` |  | N |  | Fuzzing automatic stop. |
| `autoHarness` | `boolean` |  | N |  | auto generate harness |
| `customMutatorPlugin` | `string` |  | N |  | custom mutator plugin for Fuzz Engine |
| `customAllowTargets` | `Array<string>` |  | N |  | custom file name & function name targets for instruments and coverage, like white list. |
| `customDenyTargets` | `Array<string>` |  | N |  | custom targets for instruments and coverage, like white list. |
| `context` | `abfuzz.ContextRequest` |  | N |  | context request |
| `corpusPath` | `string` |  | N |  | the special corpus path |
| `preprocessScript` | `string` |  | N |  | the preprocess script info |
| `onlyFunctionalTest` | `boolean` |  | N |  | is only fuzzing user corpus |
| `autoHarnessCommand` | `string` |  | N |  | execute the command before run auto harness generation |
| `autoHarnessScript` | `string` |  | N |  | execute the script before run auto harness generation if the command is empty |
| `autoHarnessArguments` | `Array<string>` |  | N |  | the arguments when to run the harness generation, usually for including path setting |
| `autoHarnessBlacklist` | `Array<string>` |  | N |  | the blacklist files to run the harness generation, these files will be skipped. |
| `autoHarnessWebServer` | `boolean` |  | N |  | determine if the java auto harness server is a web server |
| `metadata` | `Map<string, mojo.core.Value>` |  | N |  | the meta information for the fuzzing |
| `corpus` | `abfuzz.CorpusConfig` |  | N |  |  |
| `entities` | `Array<abfuzz.Entity>` |  | N |  |


#### `abfuzz.FuzzerPackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `type` | `string` |  | N |  | the type of the source package, oneof the whitebox, greybox, blackbox |
| `filename` | `string` |  | N |  | Filename for the custom binary. |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |


#### `abfuzz.HttpCorpus`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | Y |  |
| `staticValues` | `mojo.core.Object` |  | N |  | Object type |
| `fixups` | `Array<abfuzz.Fixup>` |  | N |  |
| `relations` | `Array<abfuzz.Relation>` |  | N |  |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `abfuzz.Relation`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.Sanitizer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the sanitizer |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.SetupStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | 更新上下文时发起的请求，不要求包含在OpenAPI中 |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |
| `filter` | `Array<string>` |  | N |  |
| `order` | `string` |  | N |  |


#### `abfuzz.TeardownStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `mojo.core.Any`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `@type` | `string` |  | Y |  |


#### `mojo.core.Error`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` | `ErrorCode` | N |  | The error code |
| `message` | `string` |  | N |  | A developer-facing error message, which should be in English. Anyuser-facing error message should be localized and sent in the[google.rpc.Status.details][google.rpc.Status.details] field, or localized by the client. |
| `details` | `Array<mojo.core.Any>` |  | N |  | A list of messages that carry the error details.  There is a common set ofmessage types for APIs to use. |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


#### `mojo.http.Request`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `method` | `string` |  | N |  | method specifies the HTTP method (GET, POST, PUT, etc.).For client requests, an empty string means GET. |
| `url` | `string` | `Url` | N |  | url specifies either the URI being requested (for serverrequests) or the URL to access (for client requests). |
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Response`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |


#### `mojo.http.Status`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `integer` | `Int32` | N |  |
| `reason` | `string` |  | N |  |


#### `mojo.http.Version`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `major` | `integer` | `Int32` | N |  |
| `minor` | `integer` | `Int32` | N |  |


## 获取指定build的日志

### 请求路径
```http
GET /abfuzz/v1/projects/{project_id}/builds/{id}/log
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |
| `id` | `string` |  |  |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `contentType` | `mojo.core.MediaType` |  | N |  | A standard MIME type describing the format of the object data. |
| `content` | `string` | `Bytes` | N |  |


#### `mojo.core.MediaType`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `subtype` | `string` |  | N |  |
| `parameter` | `mojo.core.MediaType.Parameter` |  | N |  |  |


#### `mojo.core.MediaType.Parameter`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  |
| `value` | `mojo.core.Value` |  | N |  |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


## 更新指定项目内指定的fuzzer配置
如果指定的fuzzer不存在，则会进行新增

### 请求路径
```http
PUT /abfuzz/v1/projects/{project_id}/fuzzers/{fuzzer_name}
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |


#### Body 请求对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | Y |  | the fuzzer name |
| `group` | `string` |  | N |  | the group id for the fuzzers |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `fuzzerPackage` | `abfuzz.FuzzerPackage` |  | N |  | the fuzzer package of the fuzzer |
| `executablePath` | `string` |  | N |  | Fuzzer's main executable path, relative to root. |
| `executableArguments` | `Array<string>` |  | N |  | the input arguments for the executable, it will be skipped when launcher_script is set. |
| `executableEnvironment` | `Map<string, string>` |  | N |  | the environment setting for the executable |
| `launcherScript` | `string` |  | N |  | Custom script that should be used to launch executable for this fuzzer.It will not try to launch the target if "@service-auto-start" set. |
| `timeout` | `integer` | `Int64` | N |  | Fuzzing timeout. |
| `testcaseTimeoutMillis` | `integer` | `Int64` | N |  | Testcase timeout. |
| `autoStop` | `boolean` |  | N |  | Fuzzing automatic stop. |
| `autoHarness` | `boolean` |  | N |  | auto generate harness |
| `customMutatorPlugin` | `string` |  | N |  | custom mutator plugin for Fuzz Engine |
| `customAllowTargets` | `Array<string>` |  | N |  | custom file name & function name targets for instruments and coverage, like white list. |
| `customDenyTargets` | `Array<string>` |  | N |  | custom targets for instruments and coverage, like white list. |
| `context` | `abfuzz.ContextRequest` |  | N |  | context request |
| `corpusPath` | `string` |  | N |  | the special corpus path |
| `preprocessScript` | `string` |  | N |  | the preprocess script info |
| `onlyFunctionalTest` | `boolean` |  | N |  | is only fuzzing user corpus |
| `autoHarnessCommand` | `string` |  | N |  | execute the command before run auto harness generation |
| `autoHarnessScript` | `string` |  | N |  | execute the script before run auto harness generation if the command is empty |
| `autoHarnessArguments` | `Array<string>` |  | N |  | the arguments when to run the harness generation, usually for including path setting |
| `autoHarnessBlacklist` | `Array<string>` |  | N |  | the blacklist files to run the harness generation, these files will be skipped. |
| `autoHarnessWebServer` | `boolean` |  | N |  | determine if the java auto harness server is a web server |
| `metadata` | `Map<string, mojo.core.Value>` |  | N |  | the meta information for the fuzzing |
| `corpus` | `abfuzz.CorpusConfig` |  | N |  |  |
| `entities` | `Array<abfuzz.Entity>` |  | N |  |


#### `abfuzz.CaseStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `disable` | `array` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.ContextCase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `steps` | `Array<abfuzz.CaseStep>` |  | N |  |


#### `abfuzz.ContextRequest`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `setup` | `abfuzz.ContextSetup` |  | N |  | 全局设置，在cases之前执行 |
| `cases` | `Array<abfuzz.ContextCase>` |  | N |  | OpenAPI里的请求(用path和method来对应)，包含在cases里的优先执行，没有包含在cases里的请求，每个请求自动生成一个case，在cases之后依次自行 |
| `teardown` | `abfuzz.ContextTeardown` |  | N |  | 在cases之后执行的请求 |


#### `abfuzz.ContextSetup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.SetupStep>` |  | N |  |


#### `abfuzz.ContextTeardown`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.TeardownStep>` |  | N |  |


#### `abfuzz.CorpusConfig`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `includings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `excludings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `httpBaseUrl` | `string` | `Url` | N |  | URl type<br>Uniform Resource Identifier (URI) is a string of characters used to identify a resource.<br>A Uniform Resource Name (URN) may be compared to a person's name,while a Uniform Resource Locator (URL) may be compared to their street address.In other words, a URN identifies an item and a URL provides a method for finding it.<br> |
| `httpMappingPort` | `integer` | `Int32` | N |  |
| `httpMappingHost` | `string` |  | N |  |
| `httpStaticValues` | `mojo.core.Object` |  | N |  | Object type |
| `httpCorpuses` | `Array<abfuzz.HttpCorpus>` |  | N |  |
| `combinedApiPath` | `string` |  | N |  |
| `asyncApi` | `boolean` |  | N |  |


#### `abfuzz.EndPoint`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` |  | N |  |
| `http` | `abfuzz.EndPoint.Http` |  | N |  |  |
| `priority` | `integer` | `Int32` | N |  |


#### `abfuzz.EndPoint.Http`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `path` | `string` |  | N |  |
| `methods` | `Array<string>` |  | N |  |


#### `abfuzz.Entity`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `stepType` | `string` |  | N |  |
| `interfaces` | `Array<abfuzz.Entity.Interface>` |  | N |  |


#### `abfuzz.Entity.Interface`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `type` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | N |  |
| `pathParams` | `Map<string, string>` |  | N |  |
| `queryParams` | `Map<string, string>` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `response` | `mojo.http.Response` |  | N |  | Response represents the response from an HTTP request. |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |


#### `abfuzz.Fixup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.FuzzerPackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `type` | `string` |  | N |  | the type of the source package, oneof the whitebox, greybox, blackbox |
| `filename` | `string` |  | N |  | Filename for the custom binary. |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |


#### `abfuzz.HttpCorpus`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | Y |  |
| `staticValues` | `mojo.core.Object` |  | N |  | Object type |
| `fixups` | `Array<abfuzz.Fixup>` |  | N |  |
| `relations` | `Array<abfuzz.Relation>` |  | N |  |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `abfuzz.Relation`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.SetupStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | 更新上下文时发起的请求，不要求包含在OpenAPI中 |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |
| `filter` | `Array<string>` |  | N |  |
| `order` | `string` |  | N |  |


#### `abfuzz.TeardownStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


#### `mojo.http.Request`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `method` | `string` |  | N |  | method specifies the HTTP method (GET, POST, PUT, etc.).For client requests, an empty string means GET. |
| `url` | `string` | `Url` | N |  | url specifies either the URI being requested (for serverrequests) or the URL to access (for client requests). |
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Response`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `status` | `mojo.http.Status` |  | N |  |  |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Status`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `integer` | `Int32` | N |  |
| `reason` | `string` |  | N |  |


#### `mojo.http.Version`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `major` | `integer` | `Int32` | N |  |
| `minor` | `integer` | `Int32` | N |  |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | Y |  | the fuzzer name |
| `group` | `string` |  | N |  | the group id for the fuzzers |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `fuzzerPackage` | `abfuzz.FuzzerPackage` |  | N |  | the fuzzer package of the fuzzer |
| `executablePath` | `string` |  | N |  | Fuzzer's main executable path, relative to root. |
| `executableArguments` | `Array<string>` |  | N |  | the input arguments for the executable, it will be skipped when launcher_script is set. |
| `executableEnvironment` | `Map<string, string>` |  | N |  | the environment setting for the executable |
| `launcherScript` | `string` |  | N |  | Custom script that should be used to launch executable for this fuzzer.It will not try to launch the target if "@service-auto-start" set. |
| `timeout` | `integer` | `Int64` | N |  | Fuzzing timeout. |
| `testcaseTimeoutMillis` | `integer` | `Int64` | N |  | Testcase timeout. |
| `autoStop` | `boolean` |  | N |  | Fuzzing automatic stop. |
| `autoHarness` | `boolean` |  | N |  | auto generate harness |
| `customMutatorPlugin` | `string` |  | N |  | custom mutator plugin for Fuzz Engine |
| `customAllowTargets` | `Array<string>` |  | N |  | custom file name & function name targets for instruments and coverage, like white list. |
| `customDenyTargets` | `Array<string>` |  | N |  | custom targets for instruments and coverage, like white list. |
| `context` | `abfuzz.ContextRequest` |  | N |  | context request |
| `corpusPath` | `string` |  | N |  | the special corpus path |
| `preprocessScript` | `string` |  | N |  | the preprocess script info |
| `onlyFunctionalTest` | `boolean` |  | N |  | is only fuzzing user corpus |
| `autoHarnessCommand` | `string` |  | N |  | execute the command before run auto harness generation |
| `autoHarnessScript` | `string` |  | N |  | execute the script before run auto harness generation if the command is empty |
| `autoHarnessArguments` | `Array<string>` |  | N |  | the arguments when to run the harness generation, usually for including path setting |
| `autoHarnessBlacklist` | `Array<string>` |  | N |  | the blacklist files to run the harness generation, these files will be skipped. |
| `autoHarnessWebServer` | `boolean` |  | N |  | determine if the java auto harness server is a web server |
| `metadata` | `Map<string, mojo.core.Value>` |  | N |  | the meta information for the fuzzing |
| `corpus` | `abfuzz.CorpusConfig` |  | N |  |  |
| `entities` | `Array<abfuzz.Entity>` |  | N |  |


#### `abfuzz.CaseStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `disable` | `array` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.ContextCase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `steps` | `Array<abfuzz.CaseStep>` |  | N |  |


#### `abfuzz.ContextRequest`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `setup` | `abfuzz.ContextSetup` |  | N |  | 全局设置，在cases之前执行 |
| `cases` | `Array<abfuzz.ContextCase>` |  | N |  | OpenAPI里的请求(用path和method来对应)，包含在cases里的优先执行，没有包含在cases里的请求，每个请求自动生成一个case，在cases之后依次自行 |
| `teardown` | `abfuzz.ContextTeardown` |  | N |  | 在cases之后执行的请求 |


#### `abfuzz.ContextSetup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.SetupStep>` |  | N |  |


#### `abfuzz.ContextTeardown`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.TeardownStep>` |  | N |  |


#### `abfuzz.CorpusConfig`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `includings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `excludings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `httpBaseUrl` | `string` | `Url` | N |  | URl type<br>Uniform Resource Identifier (URI) is a string of characters used to identify a resource.<br>A Uniform Resource Name (URN) may be compared to a person's name,while a Uniform Resource Locator (URL) may be compared to their street address.In other words, a URN identifies an item and a URL provides a method for finding it.<br> |
| `httpMappingPort` | `integer` | `Int32` | N |  |
| `httpMappingHost` | `string` |  | N |  |
| `httpStaticValues` | `mojo.core.Object` |  | N |  | Object type |
| `httpCorpuses` | `Array<abfuzz.HttpCorpus>` |  | N |  |
| `combinedApiPath` | `string` |  | N |  |
| `asyncApi` | `boolean` |  | N |  |


#### `abfuzz.EndPoint`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` |  | N |  |
| `http` | `abfuzz.EndPoint.Http` |  | N |  |  |
| `priority` | `integer` | `Int32` | N |  |


#### `abfuzz.EndPoint.Http`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `path` | `string` |  | N |  |
| `methods` | `Array<string>` |  | N |  |


#### `abfuzz.Entity`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `stepType` | `string` |  | N |  |
| `interfaces` | `Array<abfuzz.Entity.Interface>` |  | N |  |


#### `abfuzz.Entity.Interface`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `type` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | N |  |
| `pathParams` | `Map<string, string>` |  | N |  |
| `queryParams` | `Map<string, string>` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `response` | `mojo.http.Response` |  | N |  | Response represents the response from an HTTP request. |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |


#### `abfuzz.Fixup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.FuzzerPackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `type` | `string` |  | N |  | the type of the source package, oneof the whitebox, greybox, blackbox |
| `filename` | `string` |  | N |  | Filename for the custom binary. |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |


#### `abfuzz.HttpCorpus`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | Y |  |
| `staticValues` | `mojo.core.Object` |  | N |  | Object type |
| `fixups` | `Array<abfuzz.Fixup>` |  | N |  |
| `relations` | `Array<abfuzz.Relation>` |  | N |  |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `abfuzz.Relation`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.SetupStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | 更新上下文时发起的请求，不要求包含在OpenAPI中 |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |
| `filter` | `Array<string>` |  | N |  |
| `order` | `string` |  | N |  |


#### `abfuzz.TeardownStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


#### `mojo.http.Request`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `method` | `string` |  | N |  | method specifies the HTTP method (GET, POST, PUT, etc.).For client requests, an empty string means GET. |
| `url` | `string` | `Url` | N |  | url specifies either the URI being requested (for serverrequests) or the URL to access (for client requests). |
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Response`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `status` | `mojo.http.Status` |  | N |  |  |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Status`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `integer` | `Int32` | N |  |
| `reason` | `string` |  | N |  |


#### `mojo.http.Version`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `major` | `integer` | `Int32` | N |  |
| `minor` | `integer` | `Int32` | N |  |


## 批量删除指定项目内指定的fuzzer

### 请求路径
```http
POST /abfuzz/v1/projects/{project_id}/fuzzers/{names}:batch-delete
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |
| `names` | `Array<string>` |  |  |


### 返回值

#### 返回对象
对象为空

## 删除指定项目内指定的fuzzer

### 请求路径
```http
DELETE /abfuzz/v1/projects/{project_id}/fuzzers/{name}
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |
| `name` | `string` |  |  |


#### Query 参数
| 参数名 | 参数类型 | 格式类型 | 是否必须 | 默认值 | 说明 |
|---|---|---|---|---|---|
| `operation_name` | `string` |  | 否 |  | 每次启动执行时返回的operation name |


### 返回值

#### 返回对象
对象为空

## 批量更新指定项目内指定的fuzzer配置
如果指定的fuzzer不存在，则会进行新增

### 请求路径
```http
POST /abfuzz/v1/projects/{project_id}/fuzzers:batch-update
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |


#### Body 请求对象
| type | description |
|---|---|
| `Array<abfuzz.Fuzzer>` |  |


#### `abfuzz.CaseStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `disable` | `array` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.ContextCase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `steps` | `Array<abfuzz.CaseStep>` |  | N |  |


#### `abfuzz.ContextRequest`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `setup` | `abfuzz.ContextSetup` |  | N |  | 全局设置，在cases之前执行 |
| `cases` | `Array<abfuzz.ContextCase>` |  | N |  | OpenAPI里的请求(用path和method来对应)，包含在cases里的优先执行，没有包含在cases里的请求，每个请求自动生成一个case，在cases之后依次自行 |
| `teardown` | `abfuzz.ContextTeardown` |  | N |  | 在cases之后执行的请求 |


#### `abfuzz.ContextSetup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.SetupStep>` |  | N |  |


#### `abfuzz.ContextTeardown`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.TeardownStep>` |  | N |  |


#### `abfuzz.CorpusConfig`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `includings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `excludings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `httpBaseUrl` | `string` | `Url` | N |  | URl type<br>Uniform Resource Identifier (URI) is a string of characters used to identify a resource.<br>A Uniform Resource Name (URN) may be compared to a person's name,while a Uniform Resource Locator (URL) may be compared to their street address.In other words, a URN identifies an item and a URL provides a method for finding it.<br> |
| `httpMappingPort` | `integer` | `Int32` | N |  |
| `httpMappingHost` | `string` |  | N |  |
| `httpStaticValues` | `mojo.core.Object` |  | N |  | Object type |
| `httpCorpuses` | `Array<abfuzz.HttpCorpus>` |  | N |  |
| `combinedApiPath` | `string` |  | N |  |
| `asyncApi` | `boolean` |  | N |  |


#### `abfuzz.EndPoint`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` |  | N |  |
| `http` | `abfuzz.EndPoint.Http` |  | N |  |  |
| `priority` | `integer` | `Int32` | N |  |


#### `abfuzz.EndPoint.Http`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `path` | `string` |  | N |  |
| `methods` | `Array<string>` |  | N |  |


#### `abfuzz.Entity`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `stepType` | `string` |  | N |  |
| `interfaces` | `Array<abfuzz.Entity.Interface>` |  | N |  |


#### `abfuzz.Entity.Interface`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `type` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | N |  |
| `pathParams` | `Map<string, string>` |  | N |  |
| `queryParams` | `Map<string, string>` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `response` | `mojo.http.Response` |  | N |  | Response represents the response from an HTTP request. |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |


#### `abfuzz.Fixup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.Fuzzer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | Y |  | the fuzzer name |
| `group` | `string` |  | N |  | the group id for the fuzzers |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `fuzzerPackage` | `abfuzz.FuzzerPackage` |  | N |  | the fuzzer package of the fuzzer |
| `executablePath` | `string` |  | N |  | Fuzzer's main executable path, relative to root. |
| `executableArguments` | `Array<string>` |  | N |  | the input arguments for the executable, it will be skipped when launcher_script is set. |
| `executableEnvironment` | `Map<string, string>` |  | N |  | the environment setting for the executable |
| `launcherScript` | `string` |  | N |  | Custom script that should be used to launch executable for this fuzzer.It will not try to launch the target if "@service-auto-start" set. |
| `timeout` | `integer` | `Int64` | N |  | Fuzzing timeout. |
| `testcaseTimeoutMillis` | `integer` | `Int64` | N |  | Testcase timeout. |
| `autoStop` | `boolean` |  | N |  | Fuzzing automatic stop. |
| `autoHarness` | `boolean` |  | N |  | auto generate harness |
| `customMutatorPlugin` | `string` |  | N |  | custom mutator plugin for Fuzz Engine |
| `customAllowTargets` | `Array<string>` |  | N |  | custom file name & function name targets for instruments and coverage, like white list. |
| `customDenyTargets` | `Array<string>` |  | N |  | custom targets for instruments and coverage, like white list. |
| `context` | `abfuzz.ContextRequest` |  | N |  | context request |
| `corpusPath` | `string` |  | N |  | the special corpus path |
| `preprocessScript` | `string` |  | N |  | the preprocess script info |
| `onlyFunctionalTest` | `boolean` |  | N |  | is only fuzzing user corpus |
| `autoHarnessCommand` | `string` |  | N |  | execute the command before run auto harness generation |
| `autoHarnessScript` | `string` |  | N |  | execute the script before run auto harness generation if the command is empty |
| `autoHarnessArguments` | `Array<string>` |  | N |  | the arguments when to run the harness generation, usually for including path setting |
| `autoHarnessBlacklist` | `Array<string>` |  | N |  | the blacklist files to run the harness generation, these files will be skipped. |
| `autoHarnessWebServer` | `boolean` |  | N |  | determine if the java auto harness server is a web server |
| `metadata` | `Map<string, mojo.core.Value>` |  | N |  | the meta information for the fuzzing |
| `corpus` | `abfuzz.CorpusConfig` |  | N |  |  |
| `entities` | `Array<abfuzz.Entity>` |  | N |  |


#### `abfuzz.FuzzerPackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `type` | `string` |  | N |  | the type of the source package, oneof the whitebox, greybox, blackbox |
| `filename` | `string` |  | N |  | Filename for the custom binary. |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |


#### `abfuzz.HttpCorpus`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | Y |  |
| `staticValues` | `mojo.core.Object` |  | N |  | Object type |
| `fixups` | `Array<abfuzz.Fixup>` |  | N |  |
| `relations` | `Array<abfuzz.Relation>` |  | N |  |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `abfuzz.Relation`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.SetupStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | 更新上下文时发起的请求，不要求包含在OpenAPI中 |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |
| `filter` | `Array<string>` |  | N |  |
| `order` | `string` |  | N |  |


#### `abfuzz.TeardownStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


#### `mojo.http.Request`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `method` | `string` |  | N |  | method specifies the HTTP method (GET, POST, PUT, etc.).For client requests, an empty string means GET. |
| `url` | `string` | `Url` | N |  | url specifies either the URI being requested (for serverrequests) or the URL to access (for client requests). |
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Response`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `status` | `mojo.http.Status` |  | N |  |  |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Status`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `integer` | `Int32` | N |  |
| `reason` | `string` |  | N |  |


#### `mojo.http.Version`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `major` | `integer` | `Int32` | N |  |
| `minor` | `integer` | `Int32` | N |  |


### 返回值

#### 返回对象
| type | description |
|---|---|
| `Array<abfuzz.Fuzzer>` |  |


#### `abfuzz.CaseStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `disable` | `array` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.ContextCase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `steps` | `Array<abfuzz.CaseStep>` |  | N |  |


#### `abfuzz.ContextRequest`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `setup` | `abfuzz.ContextSetup` |  | N |  | 全局设置，在cases之前执行 |
| `cases` | `Array<abfuzz.ContextCase>` |  | N |  | OpenAPI里的请求(用path和method来对应)，包含在cases里的优先执行，没有包含在cases里的请求，每个请求自动生成一个case，在cases之后依次自行 |
| `teardown` | `abfuzz.ContextTeardown` |  | N |  | 在cases之后执行的请求 |


#### `abfuzz.ContextSetup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.SetupStep>` |  | N |  |


#### `abfuzz.ContextTeardown`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.TeardownStep>` |  | N |  |


#### `abfuzz.CorpusConfig`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `includings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `excludings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `httpBaseUrl` | `string` | `Url` | N |  | URl type<br>Uniform Resource Identifier (URI) is a string of characters used to identify a resource.<br>A Uniform Resource Name (URN) may be compared to a person's name,while a Uniform Resource Locator (URL) may be compared to their street address.In other words, a URN identifies an item and a URL provides a method for finding it.<br> |
| `httpMappingPort` | `integer` | `Int32` | N |  |
| `httpMappingHost` | `string` |  | N |  |
| `httpStaticValues` | `mojo.core.Object` |  | N |  | Object type |
| `httpCorpuses` | `Array<abfuzz.HttpCorpus>` |  | N |  |
| `combinedApiPath` | `string` |  | N |  |
| `asyncApi` | `boolean` |  | N |  |


#### `abfuzz.EndPoint`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` |  | N |  |
| `http` | `abfuzz.EndPoint.Http` |  | N |  |  |
| `priority` | `integer` | `Int32` | N |  |


#### `abfuzz.EndPoint.Http`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `path` | `string` |  | N |  |
| `methods` | `Array<string>` |  | N |  |


#### `abfuzz.Entity`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `stepType` | `string` |  | N |  |
| `interfaces` | `Array<abfuzz.Entity.Interface>` |  | N |  |


#### `abfuzz.Entity.Interface`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `type` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | N |  |
| `pathParams` | `Map<string, string>` |  | N |  |
| `queryParams` | `Map<string, string>` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `response` | `mojo.http.Response` |  | N |  | Response represents the response from an HTTP request. |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |


#### `abfuzz.Fixup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.Fuzzer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | Y |  | the fuzzer name |
| `group` | `string` |  | N |  | the group id for the fuzzers |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `fuzzerPackage` | `abfuzz.FuzzerPackage` |  | N |  | the fuzzer package of the fuzzer |
| `executablePath` | `string` |  | N |  | Fuzzer's main executable path, relative to root. |
| `executableArguments` | `Array<string>` |  | N |  | the input arguments for the executable, it will be skipped when launcher_script is set. |
| `executableEnvironment` | `Map<string, string>` |  | N |  | the environment setting for the executable |
| `launcherScript` | `string` |  | N |  | Custom script that should be used to launch executable for this fuzzer.It will not try to launch the target if "@service-auto-start" set. |
| `timeout` | `integer` | `Int64` | N |  | Fuzzing timeout. |
| `testcaseTimeoutMillis` | `integer` | `Int64` | N |  | Testcase timeout. |
| `autoStop` | `boolean` |  | N |  | Fuzzing automatic stop. |
| `autoHarness` | `boolean` |  | N |  | auto generate harness |
| `customMutatorPlugin` | `string` |  | N |  | custom mutator plugin for Fuzz Engine |
| `customAllowTargets` | `Array<string>` |  | N |  | custom file name & function name targets for instruments and coverage, like white list. |
| `customDenyTargets` | `Array<string>` |  | N |  | custom targets for instruments and coverage, like white list. |
| `context` | `abfuzz.ContextRequest` |  | N |  | context request |
| `corpusPath` | `string` |  | N |  | the special corpus path |
| `preprocessScript` | `string` |  | N |  | the preprocess script info |
| `onlyFunctionalTest` | `boolean` |  | N |  | is only fuzzing user corpus |
| `autoHarnessCommand` | `string` |  | N |  | execute the command before run auto harness generation |
| `autoHarnessScript` | `string` |  | N |  | execute the script before run auto harness generation if the command is empty |
| `autoHarnessArguments` | `Array<string>` |  | N |  | the arguments when to run the harness generation, usually for including path setting |
| `autoHarnessBlacklist` | `Array<string>` |  | N |  | the blacklist files to run the harness generation, these files will be skipped. |
| `autoHarnessWebServer` | `boolean` |  | N |  | determine if the java auto harness server is a web server |
| `metadata` | `Map<string, mojo.core.Value>` |  | N |  | the meta information for the fuzzing |
| `corpus` | `abfuzz.CorpusConfig` |  | N |  |  |
| `entities` | `Array<abfuzz.Entity>` |  | N |  |


#### `abfuzz.FuzzerPackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `type` | `string` |  | N |  | the type of the source package, oneof the whitebox, greybox, blackbox |
| `filename` | `string` |  | N |  | Filename for the custom binary. |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |


#### `abfuzz.HttpCorpus`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | Y |  |
| `staticValues` | `mojo.core.Object` |  | N |  | Object type |
| `fixups` | `Array<abfuzz.Fixup>` |  | N |  |
| `relations` | `Array<abfuzz.Relation>` |  | N |  |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `abfuzz.Relation`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.SetupStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | 更新上下文时发起的请求，不要求包含在OpenAPI中 |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |
| `filter` | `Array<string>` |  | N |  |
| `order` | `string` |  | N |  |


#### `abfuzz.TeardownStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


#### `mojo.http.Request`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `method` | `string` |  | N |  | method specifies the HTTP method (GET, POST, PUT, etc.).For client requests, an empty string means GET. |
| `url` | `string` | `Url` | N |  | url specifies either the URI being requested (for serverrequests) or the URL to access (for client requests). |
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Response`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `status` | `mojo.http.Status` |  | N |  |  |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Status`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `integer` | `Int32` | N |  |
| `reason` | `string` |  | N |  |


#### `mojo.http.Version`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `major` | `integer` | `Int32` | N |  |
| `minor` | `integer` | `Int32` | N |  |


## 查询模糊测试列表

### 请求路径
```http
GET /abfuzz/v1/projects/{project_id}/fuzzings
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  | 指定的项目ID |


#### Query 参数
| 参数名 | 参数类型 | 格式类型 | 是否必须 | 默认值 | 说明 |
|---|---|---|---|---|---|
| `source_package_id` | `string` |  | 否 |  | 指定的源代码包ID |
| `build_operation_name` | `string` |  | 否 |  | 指定的build的operation name |
| `operation_name` | `string` |  | 否 |  | 指定的operation name |
| `build_id` | `string` |  | 否 |  | 指定的 build id |
| `fuzzer_names` | `Array<string>` |  | 否 |  | 指定的fuzzer名称 |
| `page_size` | `integer` | `Int32` | 否 |  |  |
| `page_token` | `string` |  | 否 |  |  |
| `skip` | `integer` | `Int32` | 否 |  |  |


### 返回值

#### 返回对象
| type | description |
|---|---|
| `Array<abfuzz.Fuzzing>` |  |


#### `abfuzz.CaseStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `disable` | `array` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.ContextCase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `steps` | `Array<abfuzz.CaseStep>` |  | N |  |


#### `abfuzz.ContextRequest`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `setup` | `abfuzz.ContextSetup` |  | N |  | 全局设置，在cases之前执行 |
| `cases` | `Array<abfuzz.ContextCase>` |  | N |  | OpenAPI里的请求(用path和method来对应)，包含在cases里的优先执行，没有包含在cases里的请求，每个请求自动生成一个case，在cases之后依次自行 |
| `teardown` | `abfuzz.ContextTeardown` |  | N |  | 在cases之后执行的请求 |


#### `abfuzz.ContextSetup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.SetupStep>` |  | N |  |


#### `abfuzz.ContextTeardown`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.TeardownStep>` |  | N |  |


#### `abfuzz.Coverage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `element` | `string` |  | N |  | 目标的路径，可以是包、文件、类、函数 |
| `name` | `string` |  | N |  | 目标的名称，不包含路径信息，如果是函数，只是函数名；如果是类，只是类名 |
| `kind` | `string` |  | N |  | 目标的类型: package, file, class, function |
| `hash` | `integer` | `Int64` | N |  | 指定目标的hash值 |
| `lines` | `abfuzz.Coverage.Count` |  | N |  | 行覆盖率，只要本行有一条指令被执行，则本行则被标记为被执行。 |
| `functions` | `abfuzz.Coverage.Count` |  | N |  | 方法覆盖率，任何非抽象的方法，只要有一条指令被执行，则该方法就会被计为被执行。 |
| `classes` | `abfuzz.Coverage.Count` |  | N |  | 类覆盖率，所有类，包括接口，只要其中有一个方法被执行，则标记为被执行。注意：构造函数和静态初始化块也算作方法。 |
| `instructions` | `abfuzz.Coverage.Count` |  | N |  | Jacoco计算的最小单位就是字节码指令。指令覆盖率表明了在所有的指令中，哪些被指令过以及哪些没有被执行。这项指数完全独立于源码格式并且在任何情况下有效，不需要类文件的调试信息。 |
| `branches` | `abfuzz.Coverage.Count` |  | N |  | 对所有的if和switch指令计算了分支覆盖率。这项指标会统计所有的分支数量，并同时支出哪些分支被执行，哪些分支没有被执行。这项指标也在任何情况都有效。异常处理不考虑在分支范围内。 |
| `cxty` | `abfuzz.Coverage.Count` |  | N |  | Cyclomatic Complexity): 圈复杂度会为每一个非抽象方法计算圈复杂度，并为类，包以及组（groups）计算复杂度。圈复杂度简单地说就是为了覆盖所有路径，所需要执行单元测试数量，圈复杂度大说明程序代码可能质量低且难于测试和维护。 |
| `edges` | `abfuzz.Coverage.Count` |  | N |  | 边覆盖 |
| `linesInfo` | `abfuzz.Coverage.LinesInfo` |  | N |  | 在源文件内详细的行覆盖信息，在kind为file时才会有 |
| `branchInfoes` | `Array<abfuzz.Coverage.BranchInfo>` |  | N |  | 在源文件内详细的分支覆盖信息，在kind为file时才会有 |
| `functionInfoes` | `Array<abfuzz.Coverage.FunctionInfo>` |  | N |  | 在源文件内详细的函数覆盖信息，在kind为file时才会有 |
| `classInfoes` | `Array<abfuzz.Coverage.ClassInfo>` |  | N |  | 在源文件内详细的类覆盖信息，在kind为file时才会有 |


#### `abfuzz.Coverage.BranchInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `startLine` | `integer` | `Int32` | N |  | 分支起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 分支起始列 |
| `destLine` | `integer` | `Int32` | N |  | 分支跳转至的行 |
| `attribute` | `integer` | `Int32` | N |  | 分支属性 |
| `executedCount` | `integer` | `Int32` | N |  | 分支的执行数 |


#### `abfuzz.Coverage.ClassInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | 类名称 |
| `startLine` | `integer` | `Int32` | N |  | 类的起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 类的起始列 |
| `endLine` | `integer` | `Int32` | N |  | 类结束行 |
| `endColumn` | `integer` | `Int32` | N |  | 类结束行 |
| `attribute` | `integer` | `Int32` | N |  | 类属性 |
| `executedCount` | `integer` | `Int32` | N |  | 类执行数 |


#### `abfuzz.Coverage.Count`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `hit` | `integer` | `Int64` | N |  | 覆盖到的元素中的计数 |
| `total` | `integer` | `Int64` | N |  | 元素中的总计数值 |


#### `abfuzz.Coverage.FunctionInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | 函数名称 |
| `startLine` | `integer` | `Int32` | N |  | 函数的起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 函数的起始列 |
| `endLine` | `integer` | `Int32` | N |  | 函数结束行 |
| `endColumn` | `integer` | `Int32` | N |  | 函数结束行 |
| `attribute` | `integer` | `Int32` | N |  | 函数属性 |
| `executedCount` | `integer` | `Int32` | N |  | 函数执行数 |


#### `abfuzz.Coverage.LinesInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `start` | `integer` | `Int32` | N |  | 起始行 |
| `attributes` | `Array<integer>` |  | N |  | 每行的属性值 |
| `executedCounts` | `Array<integer>` |  | N |  | 每行的执行数 |


#### `abfuzz.CoverageMap`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `node` | `string` |  | N |  |
| `childNode` | `string` |  | N |  |
| `uri` | `string` |  | N |  |


#### `abfuzz.CoveragePackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |


#### `abfuzz.CpuStat`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `number` | `integer` | `Int32` | N |  | CPU编号 |
| `state` | `string` |  | N |  | CPU的状态 AVAILABLE， CAUTION |


#### `abfuzz.Engine`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the engine |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.FuzzStats`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `projectId` | `string` |  | N |  |
| `sourcePackageId` | `string` |  | N |  |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `buildId` | `string` |  | N |  | the build id |
| `operationName` | `string` |  | N |  | the operation name of the fuzzing |
| `fuzzingId` | `string` |  | N |  |
| `fuzzerName` | `string` |  | N |  |
| `cyclesDone` | `integer` | `Int32` | N |  |
| `execsDone` | `integer` | `Int32` | N |  | 总共完成的执行数 |
| `execsSpeed` | `number` | `Float32` | N |  | 每秒的执行次数 |
| `cpuStats` | `Array<abfuzz.CpuStat>` |  | N |  | CPU的状态 |
| `pathsTotal` | `integer` | `Int32` | N |  | 执行边的总数 |
| `pathsFavored` | `integer` | `Int32` | N |  |
| `pathsFound` | `integer` | `Int32` | N |  | 发现的边的总数 |
| `pathsImported` | `integer` | `Int32` | N |  |
| `pathsMaxDepth` | `integer` | `Int32` | N |  |
| `pathsPendingFavored` | `integer` | `Int32` | N |  |
| `pathsPendingTotal` | `integer` | `Int32` | N |  |
| `pathsVariable` | `integer` | `Int32` | N |  |
| `pathsStability` | `number` | `Float32` | N |  |
| `bitmapCoverage` | `number` | `Float32` | N |  | bitmap覆盖率 |
| `uniqueCrashes` | `integer` | `Int32` | N |  | 独立的crash的总数 |
| `uniqueHangs` | `integer` | `Int32` | N |  | 独立的hang的总数 |
| `lastNewPath` | `string` | `Duration` | N |  | 最近一次新path的持续时间 |
| `lastUniqueCrash` | `string` | `Duration` | N |  | 最近一次发现独立crash的持续时间 |
| `lastUniqueHang` | `string` | `Duration` | N |  | 最近一次发现独立hang的持续时间 |
| `execsSinceCrash` | `integer` | `Int32` | N |  | 从上次unique的crash的执行数 |
| `execTimeout` | `integer` | `Int32` | N |  |
| `createTime` | `string` | `Timestamp` | N |  |  |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.FuzzerPackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `type` | `string` |  | N |  | the type of the source package, oneof the whitebox, greybox, blackbox |
| `filename` | `string` |  | N |  | Filename for the custom binary. |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |


#### `abfuzz.Fuzzing`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | Additionally allows '.' and '@' over NAME_CHECK_REGEX.VALID_NAME_REGEX: re.compile(r'^[a-zA-Z0-9_@.-]+$') |
| `name` | `string` |  | N |  | Fuzzer Name. |
| `group` | `string` |  | N |  | the group id for the fuzzers |
| `projectId` | `string` |  | N |  | Project id |
| `projectName` | `string` |  | N |  | project name of the fuzzing |
| `state` | `string` |  | N |  | the state of the Fuzzing |
| `buildOperationName` | `string` |  | N |  | the build operation name |
| `buildId` | `string` |  | N |  | the build id |
| `operationName` | `string` |  | N |  | the operation name of the fuzzing |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | the sanitizers will be used for the project |
| `engine` | `abfuzz.Engine` |  | N |  | the fuzz engine name |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `sourcePackageId` | `string` |  | N |  | the source package id for the fuzzer |
| `sourcePackageCodeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `sourcePackageVersion` | `string` |  | N |  | 源代码的版本信息 |
| `sourcePackage` | `abfuzz.SourcePackage` |  | N |  | the source package for the fuzzer |
| `fuzzerPackage` | `abfuzz.FuzzerPackage` |  | N |  | the fuzzer package of the fuzzer |
| `coveragePackage` | `abfuzz.CoveragePackage` |  | N |  | the fuzzer package of the fuzzer |
| `executablePath` | `string` |  | N |  | Fuzzer's main executable path, relative to root. |
| `executableArguments` | `Array<string>` |  | N |  | the input arguments for the executable, it will be skipped when launcher_script is set. |
| `executableEnvironment` | `Map<string, string>` |  | N |  | the environment setting for the executable |
| `launcherScript` | `string` |  | N |  | Custom script that should be used to launch executable for this fuzzer.It will not try to launch the target if "@service-auto-start" set. |
| `timeout` | `integer` | `Int64` | N |  | Fuzzing timeout. |
| `testcaseTimeoutMillis` | `integer` | `Int64` | N |  | Testcase timeout. |
| `maxTestcases` | `integer` | `Int64` | N |  | Max testcases to generate for this fuzzer. |
| `additionalEnvironmentString` | `string` |  | N |  | Additional environment variables that need to be set for this fuzzer. |
| `statsColumns` | `string` |  | N |  | Column specification for stats. |
| `statsColumnDescriptions` | `string` |  | N |  | Helpful descriptions for the stats_columns. In a yaml format. |
| `builtin` | `boolean` |  | N |  | Whether this is a builtin fuzzer. |
| `autoStop` | `boolean` |  | N |  | Fuzzing automatic stop. |
| `autoHarness` | `boolean` |  | N |  | auto generate harness |
| `customMutatorPlugin` | `string` |  | N |  | custom mutator plugin for Fuzz Engine |
| `customAllowTargets` | `Array<string>` |  | N |  | custom file name & function name targets for instruments and coverage, like white list. |
| `customDenyTargets` | `Array<string>` |  | N |  | custom targets for instruments and coverage, like white list. |
| `context` | `abfuzz.ContextRequest` |  | N |  | context request |
| `dockerImage` | `string` |  | N |  | the specific docker image for the fuzzing |
| `corpusPath` | `string` |  | N |  | the special corpus path |
| `result` | `string` |  | N |  | Result from the last fuzzer run showing the number of testcases generated. |
| `resultUpdateTime` | `string` | `Timestamp` | N |  | Last result update timestamp. |
| `returnCode` | `integer` | `Int64` | N |  | Return code from last fuzzer run. |
| `consoleOutput` | `string` |  | N |  | Console output from last fuzzer run. |
| `sampleTestcase` | `abfuzz.StorageFile` |  | N |  | storage file for the sample testcase generated by the fuzzer. |
| `testcases` | `Array<abfuzz.Testcase>` |  | N |  | generated test cases |
| `coverages` | `Array<abfuzz.Coverage>` |  | N |  | 覆盖率信息 |
| `coverageUrl` | `string` |  | N |  | 覆盖率信息的url |
| `onlyFunctionTest` | `boolean` |  | N |  | 是否只进行功能测试 |
| `coverageMaps` | `Array<abfuzz.CoverageMap>` |  | N |  | 覆盖率图谱 |
| `fuzzStats` | `abfuzz.FuzzStats` |  | N |  | AFL相关的统计 |
| `inputPackage` | `string` | `Url` | N |  | 对coverage有提升的输入集合包 |
| `preprocessScript` | `string` |  | N |  | preprocess script ,process before aboroxy do reuqest |
| `durationSeconds` | `integer` | `Int64` | N |  | the duration of the fuzzing real running in seconds |
| `metadata` | `Map<string, mojo.core.Value>` |  | N |  | the meta information for the fuzzing |
| `createTime` | `string` | `Timestamp` | N |  | the created time of the fuzzing |
| `updateTime` | `string` | `Timestamp` | N |  | Last update time. |
| `corpus` | `mojo.core.Object` |  | N |  | Object type |


#### `abfuzz.Impact`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `versionIndices` | `Array<string>` |  | N |  | The impacted version indices (including beta, stable and extended_stable). |
| `extendedStableVersion` | `string` |  | N |  | The impacted extended stable version. |
| `extendedStableVersionIndices` | `Array<string>` |  | N |  | The impacted extended stable version indices. |
| `extendedStableVersionLikely` | `boolean` |  | N |  | The impacted extended stable version is merely probable (not definite).See the comment on stable_version_likely. |
| `stableVersion` | `string` |  | N |  | The impacted stable version. |
| `stableVersionIndices` | `Array<string>` |  | N |  | The impacted stable version indices. |
| `stableVersionLikely` | `boolean` |  | N |  | The impacted stable version is merely probable (not definite). Becausefor a non-asan build, we don't have a stable/beta build. Therefore, wemake an intelligent guess on the version. |
| `betaVersion` | `string` |  | N |  | The impacted beta version. |
| `betaVersionIndices` | `Array<string>` |  | N |  | The impacted beta version indices. |
| `betaVersionLikely` | `boolean` |  | N |  | The impacted beta version is merely probable (not definite). See thecomment on stable_version_likely. |
| `headVersion` | `string` |  | N |  | The impacted 'head' version |
| `headVersionIndices` | `Array<string>` |  | N |  | The impacted head version indices. |
| `headVersionLikely` | `boolean` |  | N |  | The impacted head version is likely. |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `abfuzz.Repository`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `url` | `string` | `Url` | N |  | the git url for the source package |
| `branch` | `string` |  | N |  | the git branch for the source package |
| `tag` | `string` |  | N |  | the git tag for the source package |
| `commitTime` | `string` | `Timestamp` | N |  | the used commit time for the source package |
| `commitKey` | `string` |  | N |  | the used commit key for the source package, it is hash key for git |


#### `abfuzz.Sanitizer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the sanitizer |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.SetupStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | 更新上下文时发起的请求，不要求包含在OpenAPI中 |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |
| `filter` | `Array<string>` |  | N |  |
| `order` | `string` |  | N |  |


#### `abfuzz.SourcePackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the uuid for the source package |
| `projectId` | `string` |  | N |  | the project id which including the source package |
| `projectName` | `string` |  | N |  | the project name which including the source package |
| `projectVersion` | `string` |  | N |  | the project version |
| `type` | `string` |  | N |  | the type of the source packagewhitebox, graybox, blackbox |
| `name` | `string` |  | N |  | the name of the module of the source package |
| `fuzzRepo` | `abfuzz.Repository` |  | N |  | the repository information for the fuzz code reposisity in the source package |
| `sourceRepo` | `abfuzz.Repository` |  | N |  | the repository information for the main code reposisity of custom in the source package |
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `fileSet` | `Array<abfuzz.StorageFile>` |  | N |  | the file set for the source package |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary or raw source code package. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |
| `customVersion` | `string` |  | N |  | more version info for the custom system |
| `codeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `createTime` | `string` | `Timestamp` | N |  | create time of the source code package |
| `updateTime` | `string` | `Timestamp` | N |  | update time of the source code package |


#### `abfuzz.StorageFile`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  | 文件在minio上的 地址 |
| `checksum` | `string` | `Checksum` | N |  | 文件的hash校验码 |
| `type` | `string` |  | N |  | 文件类型，比如： `corpus`, `harness`, `source`, `build-script`, `openapi`, `jar` |
| `group` | `string` |  | N |  | 文件属于某个组，可以是某个fuzzer |


#### `abfuzz.TeardownStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.Testcase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | id for the testcase |
| `description` | `string` |  | N |  | description for the testcase |
| `keywords` | `Array<string>` |  | N |  | keywords is used for searching. |
| `securityFlag` | `boolean` |  | N |  | Is it a security bug ? |
| `securitySeverity` | `integer` | `Int64` | N |  | Security severity of the bug. |
| `oneTimeCrasherFlag` | `boolean` |  | N |  | Did the bug only reproduced once ? |
| `comments` | `string` |  | N |  | Any additional comments. |
| `projectId` | `string` |  | N |  | Project id associated with this test case. |
| `projectName` | `string` |  | N |  | Project name associated with this test case. |
| `sourcePackageId` | `string` |  | N |  | the source package id for the testcase |
| `fuzzerName` | `string` |  | N |  | Name of the fuzzer used to generate this testcase. |
| `fuzzerNameIndices` | `Array<string>` |  | N |  | Fuzzer name indices |
| `overriddenFuzzerName` | `string` |  | N |  | Overridden fuzzer name because actual fuzzer name can be different in manyscenarios (libfuzzer, afl, etc). |
| `fuzzingId` | `string` |  | N |  | the fuzzing which is the testcase generated from |
| `buildOperationName` | `string` |  | N |  | the build operation name of the testcase |
| `operationName` | `string` |  | N |  | the operation name of the testcase when fuzzing |
| `buildId` | `string` |  | N |  | the build id |
| `platform` | `string` | `Platform` | N |  | Platforms (e.g. windows, linux, android). |
| `language` | `string` |  | N |  | the program language for the fuzzing |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `otherPlatforms` | `Array<string>` |  | N |  | Platforms (e.g. windows, linux, android). |
| `otherLangVms` | `Array<abfuzz.LangVM>` |  | N |  | 指定的JDK，如果是聚合后的testcase，会包含多个jdk版本 |
| `engines` | `Array<abfuzz.Engine>` |  | N |  | engine names for the testcase |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | 指定的sanitizers |
| `fuzzedIds` | `Array<string>` |  | N |  | Blobstore keys for various things like original testcase, minimizedtestcase, etc. |
| `minimizedIds` | `Array<string>` |  | N |  |
| `minidumpIds` | `Array<string>` |  | N |  |
| `status` | `string` |  | N |  | Status of this testcase (pending, processed, unreproducible, etc). |
| `open` | `boolean` |  | N |  | Whether testcase is open. |
| `duplicateOf` | `integer` | `Int64` | N |  | Id of the testcase that this is marked as a duplicate of. |
| `groupId` | `integer` | `Int64` | N |  | Id for this testcase's associated group. |
| `groupBugInformation` | `integer` | `Int64` | N |  | Tracking issue tracker bug for this testcase group. |
| `isLeader` | `boolean` |  | N |  | Whether or not a testcase is the leader of its group.If the testcase is not in a group, it's the leader of a group of 1.The default is false because we prefer not to show crashes until we aresure. And group_task will correctly set the value within 30 minutes. |
| `bugInformation` | `string` |  | N |  | Tracking issue tracker bug. One bug number per line (future extension). |
| `isADuplicateFlag` | `boolean` |  | N |  | Whether or not a testcase is a duplicate of other testcase. |
| `hasBugFlag` | `boolean` |  | N |  | Whether testcase has a bug (either bug_information orgroup_bug_information). |
| `bugIndices` | `Array<string>` |  | N |  | Indices for bug_information and group_bug_information. |
| `triaged` | `boolean` |  | N |  | Boolean attribute indicating if cleanup triage needs to be done. |
| `additionalMetadata` | `mojo.core.Object` |  | N |  | Additional metadata stored as a JSON object. This should be used forproperties that are not commonly accessed and do not need to be indexed. |
| `crashType` | `string` |  | N |  | Crash on an invalid read/write. |
| `crashDescription` | `string` |  | N |  | Crash description for runtime error |
| `crashAddress` | `string` |  | N |  | Crashing address. |
| `crashState` | `string` |  | N |  | First x stack frames. |
| `crashStacktrace` | `string` |  | N |  | Complete stacktrace. |
| `lastTestedCrashStacktrace` | `string` |  | N |  | Last tested crash stacktrace using the latest revision. |
| `crashRevision` | `integer` | `Int64` | N |  | Revision that we discovered the crash in. |
| `crashSourceFileName` | `string` |  | N |  | The source file name, may be omitted if debug symbols are not available. |
| `crashSourceLine` | `integer` | `Int32` | N |  | The (1-based) source line number, may be omitted if debug symbols arenot available. |
| `crashFunctionName` | `string` |  | N |  | The function name, may be omitted if debug symbols are not available. |
| `symbolized` | `boolean` |  | N |  | Flag indicating whether or not the testcase has been symbolized. |
| `input` | `abfuzz.Testcase.Input` |  | N |  | The input for the testcase crashing |
| `targetServerLogPath` | `string` |  | N |  | The target server log path with java testcase |
| `absolutePath` | `string` |  | N |  | The file on the agent that generated the testcase. |
| `binaryFlag` | `boolean` |  | N |  | Is this a binary file? |
| `flakyStack` | `boolean` |  | N |  | Does the testcase crash stack vary b/w crashes ? |
| `httpFlag` | `boolean` |  | N |  | Do we need to test this testcase using an HTTP/HTTPS server? |
| `gestures` | `Array<string>` |  | N |  | Fake user interaction sequences like key clicks, mouse movements, etc. |
| `redzone` | `integer` | `Int64` | N |  | ASAN redzone size in bytes. |
| `disableUbsan` | `boolean` |  | N |  | Flag indicating if UBSan detection should be disabled. This is needed forcases when ASan and UBSan are bundled in the same build configurationand we need to disable UBSan in some runs to find the potentially moreinteresting ASan bugs. |
| `timeoutMultiplier` | `number` | `Float64` | N |  | Adjusts timeout based on multiplier value. |
| `regression` | `string` |  | N |  | Regression range. |
| `fixed` | `string` |  | N |  | Revisions where this issue has been fixed. |
| `impactIndices` | `Array<string>` |  | N |  | Impact indices for searching. |
| `impact` | `abfuzz.Impact` |  | N |  | 用例影响源代码的版本信息 |
| `isImpactSetFlag` | `boolean` |  | N |  | Whether or not impact task has been run on this testcase. |
| `uploaderEmail` | `string` |  | N |  | Uploader email address. |
| `createTime` | `string` | `Timestamp` | N |  | Timestamp. |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.Testcase.Input`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  | binaray, file, json |
| `content` | `mojo.core.Value` |  | N |  | the input content |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


#### `mojo.http.Request`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `method` | `string` |  | N |  | method specifies the HTTP method (GET, POST, PUT, etc.).For client requests, an empty string means GET. |
| `url` | `string` | `Url` | N |  | url specifies either the URI being requested (for serverrequests) or the URL to access (for client requests). |
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Version`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `major` | `integer` | `Int32` | N |  |
| `minor` | `integer` | `Int32` | N |  |


## 获取指定模糊测试

### 请求路径
```http
GET /abfuzz/v1/projects/{project_id}/fuzzings/{id}
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |
| `id` | `string` |  |  |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | Additionally allows '.' and '@' over NAME_CHECK_REGEX.VALID_NAME_REGEX: re.compile(r'^[a-zA-Z0-9_@.-]+$') |
| `name` | `string` |  | N |  | Fuzzer Name. |
| `group` | `string` |  | N |  | the group id for the fuzzers |
| `projectId` | `string` |  | N |  | Project id |
| `projectName` | `string` |  | N |  | project name of the fuzzing |
| `state` | `string` |  | N |  | the state of the Fuzzing |
| `buildOperationName` | `string` |  | N |  | the build operation name |
| `buildId` | `string` |  | N |  | the build id |
| `operationName` | `string` |  | N |  | the operation name of the fuzzing |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | the sanitizers will be used for the project |
| `engine` | `abfuzz.Engine` |  | N |  | the fuzz engine name |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `sourcePackageId` | `string` |  | N |  | the source package id for the fuzzer |
| `sourcePackageCodeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `sourcePackageVersion` | `string` |  | N |  | 源代码的版本信息 |
| `sourcePackage` | `abfuzz.SourcePackage` |  | N |  | the source package for the fuzzer |
| `fuzzerPackage` | `abfuzz.FuzzerPackage` |  | N |  | the fuzzer package of the fuzzer |
| `coveragePackage` | `abfuzz.CoveragePackage` |  | N |  | the fuzzer package of the fuzzer |
| `executablePath` | `string` |  | N |  | Fuzzer's main executable path, relative to root. |
| `executableArguments` | `Array<string>` |  | N |  | the input arguments for the executable, it will be skipped when launcher_script is set. |
| `executableEnvironment` | `Map<string, string>` |  | N |  | the environment setting for the executable |
| `launcherScript` | `string` |  | N |  | Custom script that should be used to launch executable for this fuzzer.It will not try to launch the target if "@service-auto-start" set. |
| `timeout` | `integer` | `Int64` | N |  | Fuzzing timeout. |
| `testcaseTimeoutMillis` | `integer` | `Int64` | N |  | Testcase timeout. |
| `maxTestcases` | `integer` | `Int64` | N |  | Max testcases to generate for this fuzzer. |
| `additionalEnvironmentString` | `string` |  | N |  | Additional environment variables that need to be set for this fuzzer. |
| `statsColumns` | `string` |  | N |  | Column specification for stats. |
| `statsColumnDescriptions` | `string` |  | N |  | Helpful descriptions for the stats_columns. In a yaml format. |
| `builtin` | `boolean` |  | N |  | Whether this is a builtin fuzzer. |
| `autoStop` | `boolean` |  | N |  | Fuzzing automatic stop. |
| `autoHarness` | `boolean` |  | N |  | auto generate harness |
| `customMutatorPlugin` | `string` |  | N |  | custom mutator plugin for Fuzz Engine |
| `customAllowTargets` | `Array<string>` |  | N |  | custom file name & function name targets for instruments and coverage, like white list. |
| `customDenyTargets` | `Array<string>` |  | N |  | custom targets for instruments and coverage, like white list. |
| `context` | `abfuzz.ContextRequest` |  | N |  | context request |
| `dockerImage` | `string` |  | N |  | the specific docker image for the fuzzing |
| `corpusPath` | `string` |  | N |  | the special corpus path |
| `result` | `string` |  | N |  | Result from the last fuzzer run showing the number of testcases generated. |
| `resultUpdateTime` | `string` | `Timestamp` | N |  | Last result update timestamp. |
| `returnCode` | `integer` | `Int64` | N |  | Return code from last fuzzer run. |
| `consoleOutput` | `string` |  | N |  | Console output from last fuzzer run. |
| `sampleTestcase` | `abfuzz.StorageFile` |  | N |  | storage file for the sample testcase generated by the fuzzer. |
| `testcases` | `Array<abfuzz.Testcase>` |  | N |  | generated test cases |
| `coverages` | `Array<abfuzz.Coverage>` |  | N |  | 覆盖率信息 |
| `coverageUrl` | `string` |  | N |  | 覆盖率信息的url |
| `onlyFunctionTest` | `boolean` |  | N |  | 是否只进行功能测试 |
| `coverageMaps` | `Array<abfuzz.CoverageMap>` |  | N |  | 覆盖率图谱 |
| `fuzzStats` | `abfuzz.FuzzStats` |  | N |  | AFL相关的统计 |
| `inputPackage` | `string` | `Url` | N |  | 对coverage有提升的输入集合包 |
| `preprocessScript` | `string` |  | N |  | preprocess script ,process before aboroxy do reuqest |
| `durationSeconds` | `integer` | `Int64` | N |  | the duration of the fuzzing real running in seconds |
| `metadata` | `Map<string, mojo.core.Value>` |  | N |  | the meta information for the fuzzing |
| `createTime` | `string` | `Timestamp` | N |  | the created time of the fuzzing |
| `updateTime` | `string` | `Timestamp` | N |  | Last update time. |
| `corpus` | `mojo.core.Object` |  | N |  | Object type |


#### `abfuzz.CaseStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `disable` | `array` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.ContextCase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `steps` | `Array<abfuzz.CaseStep>` |  | N |  |


#### `abfuzz.ContextRequest`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `setup` | `abfuzz.ContextSetup` |  | N |  | 全局设置，在cases之前执行 |
| `cases` | `Array<abfuzz.ContextCase>` |  | N |  | OpenAPI里的请求(用path和method来对应)，包含在cases里的优先执行，没有包含在cases里的请求，每个请求自动生成一个case，在cases之后依次自行 |
| `teardown` | `abfuzz.ContextTeardown` |  | N |  | 在cases之后执行的请求 |


#### `abfuzz.ContextSetup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.SetupStep>` |  | N |  |


#### `abfuzz.ContextTeardown`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.TeardownStep>` |  | N |  |


#### `abfuzz.Coverage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `element` | `string` |  | N |  | 目标的路径，可以是包、文件、类、函数 |
| `name` | `string` |  | N |  | 目标的名称，不包含路径信息，如果是函数，只是函数名；如果是类，只是类名 |
| `kind` | `string` |  | N |  | 目标的类型: package, file, class, function |
| `hash` | `integer` | `Int64` | N |  | 指定目标的hash值 |
| `lines` | `abfuzz.Coverage.Count` |  | N |  | 行覆盖率，只要本行有一条指令被执行，则本行则被标记为被执行。 |
| `functions` | `abfuzz.Coverage.Count` |  | N |  | 方法覆盖率，任何非抽象的方法，只要有一条指令被执行，则该方法就会被计为被执行。 |
| `classes` | `abfuzz.Coverage.Count` |  | N |  | 类覆盖率，所有类，包括接口，只要其中有一个方法被执行，则标记为被执行。注意：构造函数和静态初始化块也算作方法。 |
| `instructions` | `abfuzz.Coverage.Count` |  | N |  | Jacoco计算的最小单位就是字节码指令。指令覆盖率表明了在所有的指令中，哪些被指令过以及哪些没有被执行。这项指数完全独立于源码格式并且在任何情况下有效，不需要类文件的调试信息。 |
| `branches` | `abfuzz.Coverage.Count` |  | N |  | 对所有的if和switch指令计算了分支覆盖率。这项指标会统计所有的分支数量，并同时支出哪些分支被执行，哪些分支没有被执行。这项指标也在任何情况都有效。异常处理不考虑在分支范围内。 |
| `cxty` | `abfuzz.Coverage.Count` |  | N |  | Cyclomatic Complexity): 圈复杂度会为每一个非抽象方法计算圈复杂度，并为类，包以及组（groups）计算复杂度。圈复杂度简单地说就是为了覆盖所有路径，所需要执行单元测试数量，圈复杂度大说明程序代码可能质量低且难于测试和维护。 |
| `edges` | `abfuzz.Coverage.Count` |  | N |  | 边覆盖 |
| `linesInfo` | `abfuzz.Coverage.LinesInfo` |  | N |  | 在源文件内详细的行覆盖信息，在kind为file时才会有 |
| `branchInfoes` | `Array<abfuzz.Coverage.BranchInfo>` |  | N |  | 在源文件内详细的分支覆盖信息，在kind为file时才会有 |
| `functionInfoes` | `Array<abfuzz.Coverage.FunctionInfo>` |  | N |  | 在源文件内详细的函数覆盖信息，在kind为file时才会有 |
| `classInfoes` | `Array<abfuzz.Coverage.ClassInfo>` |  | N |  | 在源文件内详细的类覆盖信息，在kind为file时才会有 |


#### `abfuzz.Coverage.BranchInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `startLine` | `integer` | `Int32` | N |  | 分支起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 分支起始列 |
| `destLine` | `integer` | `Int32` | N |  | 分支跳转至的行 |
| `attribute` | `integer` | `Int32` | N |  | 分支属性 |
| `executedCount` | `integer` | `Int32` | N |  | 分支的执行数 |


#### `abfuzz.Coverage.ClassInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | 类名称 |
| `startLine` | `integer` | `Int32` | N |  | 类的起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 类的起始列 |
| `endLine` | `integer` | `Int32` | N |  | 类结束行 |
| `endColumn` | `integer` | `Int32` | N |  | 类结束行 |
| `attribute` | `integer` | `Int32` | N |  | 类属性 |
| `executedCount` | `integer` | `Int32` | N |  | 类执行数 |


#### `abfuzz.Coverage.Count`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `hit` | `integer` | `Int64` | N |  | 覆盖到的元素中的计数 |
| `total` | `integer` | `Int64` | N |  | 元素中的总计数值 |


#### `abfuzz.Coverage.FunctionInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | 函数名称 |
| `startLine` | `integer` | `Int32` | N |  | 函数的起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 函数的起始列 |
| `endLine` | `integer` | `Int32` | N |  | 函数结束行 |
| `endColumn` | `integer` | `Int32` | N |  | 函数结束行 |
| `attribute` | `integer` | `Int32` | N |  | 函数属性 |
| `executedCount` | `integer` | `Int32` | N |  | 函数执行数 |


#### `abfuzz.Coverage.LinesInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `start` | `integer` | `Int32` | N |  | 起始行 |
| `attributes` | `Array<integer>` |  | N |  | 每行的属性值 |
| `executedCounts` | `Array<integer>` |  | N |  | 每行的执行数 |


#### `abfuzz.CoverageMap`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `node` | `string` |  | N |  |
| `childNode` | `string` |  | N |  |
| `uri` | `string` |  | N |  |


#### `abfuzz.CoveragePackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |


#### `abfuzz.CpuStat`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `number` | `integer` | `Int32` | N |  | CPU编号 |
| `state` | `string` |  | N |  | CPU的状态 AVAILABLE， CAUTION |


#### `abfuzz.Engine`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the engine |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.FuzzStats`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `projectId` | `string` |  | N |  |
| `sourcePackageId` | `string` |  | N |  |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `buildId` | `string` |  | N |  | the build id |
| `operationName` | `string` |  | N |  | the operation name of the fuzzing |
| `fuzzingId` | `string` |  | N |  |
| `fuzzerName` | `string` |  | N |  |
| `cyclesDone` | `integer` | `Int32` | N |  |
| `execsDone` | `integer` | `Int32` | N |  | 总共完成的执行数 |
| `execsSpeed` | `number` | `Float32` | N |  | 每秒的执行次数 |
| `cpuStats` | `Array<abfuzz.CpuStat>` |  | N |  | CPU的状态 |
| `pathsTotal` | `integer` | `Int32` | N |  | 执行边的总数 |
| `pathsFavored` | `integer` | `Int32` | N |  |
| `pathsFound` | `integer` | `Int32` | N |  | 发现的边的总数 |
| `pathsImported` | `integer` | `Int32` | N |  |
| `pathsMaxDepth` | `integer` | `Int32` | N |  |
| `pathsPendingFavored` | `integer` | `Int32` | N |  |
| `pathsPendingTotal` | `integer` | `Int32` | N |  |
| `pathsVariable` | `integer` | `Int32` | N |  |
| `pathsStability` | `number` | `Float32` | N |  |
| `bitmapCoverage` | `number` | `Float32` | N |  | bitmap覆盖率 |
| `uniqueCrashes` | `integer` | `Int32` | N |  | 独立的crash的总数 |
| `uniqueHangs` | `integer` | `Int32` | N |  | 独立的hang的总数 |
| `lastNewPath` | `string` | `Duration` | N |  | 最近一次新path的持续时间 |
| `lastUniqueCrash` | `string` | `Duration` | N |  | 最近一次发现独立crash的持续时间 |
| `lastUniqueHang` | `string` | `Duration` | N |  | 最近一次发现独立hang的持续时间 |
| `execsSinceCrash` | `integer` | `Int32` | N |  | 从上次unique的crash的执行数 |
| `execTimeout` | `integer` | `Int32` | N |  |
| `createTime` | `string` | `Timestamp` | N |  |  |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.FuzzerPackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `type` | `string` |  | N |  | the type of the source package, oneof the whitebox, greybox, blackbox |
| `filename` | `string` |  | N |  | Filename for the custom binary. |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |


#### `abfuzz.Impact`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `versionIndices` | `Array<string>` |  | N |  | The impacted version indices (including beta, stable and extended_stable). |
| `extendedStableVersion` | `string` |  | N |  | The impacted extended stable version. |
| `extendedStableVersionIndices` | `Array<string>` |  | N |  | The impacted extended stable version indices. |
| `extendedStableVersionLikely` | `boolean` |  | N |  | The impacted extended stable version is merely probable (not definite).See the comment on stable_version_likely. |
| `stableVersion` | `string` |  | N |  | The impacted stable version. |
| `stableVersionIndices` | `Array<string>` |  | N |  | The impacted stable version indices. |
| `stableVersionLikely` | `boolean` |  | N |  | The impacted stable version is merely probable (not definite). Becausefor a non-asan build, we don't have a stable/beta build. Therefore, wemake an intelligent guess on the version. |
| `betaVersion` | `string` |  | N |  | The impacted beta version. |
| `betaVersionIndices` | `Array<string>` |  | N |  | The impacted beta version indices. |
| `betaVersionLikely` | `boolean` |  | N |  | The impacted beta version is merely probable (not definite). See thecomment on stable_version_likely. |
| `headVersion` | `string` |  | N |  | The impacted 'head' version |
| `headVersionIndices` | `Array<string>` |  | N |  | The impacted head version indices. |
| `headVersionLikely` | `boolean` |  | N |  | The impacted head version is likely. |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `abfuzz.Repository`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `url` | `string` | `Url` | N |  | the git url for the source package |
| `branch` | `string` |  | N |  | the git branch for the source package |
| `tag` | `string` |  | N |  | the git tag for the source package |
| `commitTime` | `string` | `Timestamp` | N |  | the used commit time for the source package |
| `commitKey` | `string` |  | N |  | the used commit key for the source package, it is hash key for git |


#### `abfuzz.Sanitizer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the sanitizer |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.SetupStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | 更新上下文时发起的请求，不要求包含在OpenAPI中 |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |
| `filter` | `Array<string>` |  | N |  |
| `order` | `string` |  | N |  |


#### `abfuzz.SourcePackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the uuid for the source package |
| `projectId` | `string` |  | N |  | the project id which including the source package |
| `projectName` | `string` |  | N |  | the project name which including the source package |
| `projectVersion` | `string` |  | N |  | the project version |
| `type` | `string` |  | N |  | the type of the source packagewhitebox, graybox, blackbox |
| `name` | `string` |  | N |  | the name of the module of the source package |
| `fuzzRepo` | `abfuzz.Repository` |  | N |  | the repository information for the fuzz code reposisity in the source package |
| `sourceRepo` | `abfuzz.Repository` |  | N |  | the repository information for the main code reposisity of custom in the source package |
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `fileSet` | `Array<abfuzz.StorageFile>` |  | N |  | the file set for the source package |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary or raw source code package. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |
| `customVersion` | `string` |  | N |  | more version info for the custom system |
| `codeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `createTime` | `string` | `Timestamp` | N |  | create time of the source code package |
| `updateTime` | `string` | `Timestamp` | N |  | update time of the source code package |


#### `abfuzz.StorageFile`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  | 文件在minio上的 地址 |
| `checksum` | `string` | `Checksum` | N |  | 文件的hash校验码 |
| `type` | `string` |  | N |  | 文件类型，比如： `corpus`, `harness`, `source`, `build-script`, `openapi`, `jar` |
| `group` | `string` |  | N |  | 文件属于某个组，可以是某个fuzzer |


#### `abfuzz.TeardownStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.Testcase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | id for the testcase |
| `description` | `string` |  | N |  | description for the testcase |
| `keywords` | `Array<string>` |  | N |  | keywords is used for searching. |
| `securityFlag` | `boolean` |  | N |  | Is it a security bug ? |
| `securitySeverity` | `integer` | `Int64` | N |  | Security severity of the bug. |
| `oneTimeCrasherFlag` | `boolean` |  | N |  | Did the bug only reproduced once ? |
| `comments` | `string` |  | N |  | Any additional comments. |
| `projectId` | `string` |  | N |  | Project id associated with this test case. |
| `projectName` | `string` |  | N |  | Project name associated with this test case. |
| `sourcePackageId` | `string` |  | N |  | the source package id for the testcase |
| `fuzzerName` | `string` |  | N |  | Name of the fuzzer used to generate this testcase. |
| `fuzzerNameIndices` | `Array<string>` |  | N |  | Fuzzer name indices |
| `overriddenFuzzerName` | `string` |  | N |  | Overridden fuzzer name because actual fuzzer name can be different in manyscenarios (libfuzzer, afl, etc). |
| `fuzzingId` | `string` |  | N |  | the fuzzing which is the testcase generated from |
| `buildOperationName` | `string` |  | N |  | the build operation name of the testcase |
| `operationName` | `string` |  | N |  | the operation name of the testcase when fuzzing |
| `buildId` | `string` |  | N |  | the build id |
| `platform` | `string` | `Platform` | N |  | Platforms (e.g. windows, linux, android). |
| `language` | `string` |  | N |  | the program language for the fuzzing |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `otherPlatforms` | `Array<string>` |  | N |  | Platforms (e.g. windows, linux, android). |
| `otherLangVms` | `Array<abfuzz.LangVM>` |  | N |  | 指定的JDK，如果是聚合后的testcase，会包含多个jdk版本 |
| `engines` | `Array<abfuzz.Engine>` |  | N |  | engine names for the testcase |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | 指定的sanitizers |
| `fuzzedIds` | `Array<string>` |  | N |  | Blobstore keys for various things like original testcase, minimizedtestcase, etc. |
| `minimizedIds` | `Array<string>` |  | N |  |
| `minidumpIds` | `Array<string>` |  | N |  |
| `status` | `string` |  | N |  | Status of this testcase (pending, processed, unreproducible, etc). |
| `open` | `boolean` |  | N |  | Whether testcase is open. |
| `duplicateOf` | `integer` | `Int64` | N |  | Id of the testcase that this is marked as a duplicate of. |
| `groupId` | `integer` | `Int64` | N |  | Id for this testcase's associated group. |
| `groupBugInformation` | `integer` | `Int64` | N |  | Tracking issue tracker bug for this testcase group. |
| `isLeader` | `boolean` |  | N |  | Whether or not a testcase is the leader of its group.If the testcase is not in a group, it's the leader of a group of 1.The default is false because we prefer not to show crashes until we aresure. And group_task will correctly set the value within 30 minutes. |
| `bugInformation` | `string` |  | N |  | Tracking issue tracker bug. One bug number per line (future extension). |
| `isADuplicateFlag` | `boolean` |  | N |  | Whether or not a testcase is a duplicate of other testcase. |
| `hasBugFlag` | `boolean` |  | N |  | Whether testcase has a bug (either bug_information orgroup_bug_information). |
| `bugIndices` | `Array<string>` |  | N |  | Indices for bug_information and group_bug_information. |
| `triaged` | `boolean` |  | N |  | Boolean attribute indicating if cleanup triage needs to be done. |
| `additionalMetadata` | `mojo.core.Object` |  | N |  | Additional metadata stored as a JSON object. This should be used forproperties that are not commonly accessed and do not need to be indexed. |
| `crashType` | `string` |  | N |  | Crash on an invalid read/write. |
| `crashDescription` | `string` |  | N |  | Crash description for runtime error |
| `crashAddress` | `string` |  | N |  | Crashing address. |
| `crashState` | `string` |  | N |  | First x stack frames. |
| `crashStacktrace` | `string` |  | N |  | Complete stacktrace. |
| `lastTestedCrashStacktrace` | `string` |  | N |  | Last tested crash stacktrace using the latest revision. |
| `crashRevision` | `integer` | `Int64` | N |  | Revision that we discovered the crash in. |
| `crashSourceFileName` | `string` |  | N |  | The source file name, may be omitted if debug symbols are not available. |
| `crashSourceLine` | `integer` | `Int32` | N |  | The (1-based) source line number, may be omitted if debug symbols arenot available. |
| `crashFunctionName` | `string` |  | N |  | The function name, may be omitted if debug symbols are not available. |
| `symbolized` | `boolean` |  | N |  | Flag indicating whether or not the testcase has been symbolized. |
| `input` | `abfuzz.Testcase.Input` |  | N |  | The input for the testcase crashing |
| `targetServerLogPath` | `string` |  | N |  | The target server log path with java testcase |
| `absolutePath` | `string` |  | N |  | The file on the agent that generated the testcase. |
| `binaryFlag` | `boolean` |  | N |  | Is this a binary file? |
| `flakyStack` | `boolean` |  | N |  | Does the testcase crash stack vary b/w crashes ? |
| `httpFlag` | `boolean` |  | N |  | Do we need to test this testcase using an HTTP/HTTPS server? |
| `gestures` | `Array<string>` |  | N |  | Fake user interaction sequences like key clicks, mouse movements, etc. |
| `redzone` | `integer` | `Int64` | N |  | ASAN redzone size in bytes. |
| `disableUbsan` | `boolean` |  | N |  | Flag indicating if UBSan detection should be disabled. This is needed forcases when ASan and UBSan are bundled in the same build configurationand we need to disable UBSan in some runs to find the potentially moreinteresting ASan bugs. |
| `timeoutMultiplier` | `number` | `Float64` | N |  | Adjusts timeout based on multiplier value. |
| `regression` | `string` |  | N |  | Regression range. |
| `fixed` | `string` |  | N |  | Revisions where this issue has been fixed. |
| `impactIndices` | `Array<string>` |  | N |  | Impact indices for searching. |
| `impact` | `abfuzz.Impact` |  | N |  | 用例影响源代码的版本信息 |
| `isImpactSetFlag` | `boolean` |  | N |  | Whether or not impact task has been run on this testcase. |
| `uploaderEmail` | `string` |  | N |  | Uploader email address. |
| `createTime` | `string` | `Timestamp` | N |  | Timestamp. |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.Testcase.Input`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  | binaray, file, json |
| `content` | `mojo.core.Value` |  | N |  | the input content |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


#### `mojo.http.Request`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `method` | `string` |  | N |  | method specifies the HTTP method (GET, POST, PUT, etc.).For client requests, an empty string means GET. |
| `url` | `string` | `Url` | N |  | url specifies either the URI being requested (for serverrequests) or the URL to access (for client requests). |
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Version`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `major` | `integer` | `Int32` | N |  |
| `minor` | `integer` | `Int32` | N |  |


## 删除指定模糊测试

### 请求路径
```http
DELETE /abfuzz/v1/projects/{project_id}/fuzzings/{id}
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |
| `id` | `string` |  |  |


### 返回值

#### 返回对象
对象为空

## 查询模糊测试接口测试详情

### 请求路径
```http
GET /abfuzz/v1/projects/{project_id}/fuzzings/{id}/apis
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  | 指定的项目ID |
| `id` | `string` |  | 指定的fuzzingID |


#### Query 参数
| 参数名 | 参数类型 | 格式类型 | 是否必须 | 默认值 | 说明 |
|---|---|---|---|---|---|
| `fuzzer_name` | `string` |  | 否 |  | 指定的fuzzerName,不填写时即不指定多引擎的fuzz |
| `method` | `string` |  | 否 |  |  |
| `url` | `string` |  | 否 |  |  |
| `page` | `integer` | `Int64` | 否 |  | 页数 |
| `page_limit` | `integer` | `Int64` | 否 |  | 当前页的大小 |
| `page_size` | `integer` | `Int32` | 否 |  |  |
| `page_token` | `string` |  | 否 |  |  |
| `skip` | `integer` | `Int32` | 否 |  |  |


### 返回值

#### 返回对象
| type | description |
|---|---|
| `Array<abfuzz.FuzzApi>` |  |


#### `abfuzz.FuzzApi`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `projectId` | `string` |  | N |  |
| `sourcePackageId` | `string` |  | N |  |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `buildId` | `string` |  | N |  | the build id |
| `operationName` | `string` |  | N |  | the operation name of the fuzzing |
| `fuzzingId` | `string` |  | N |  |
| `fuzzerName` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `url` | `string` |  | N |  |
| `execsTotal` | `integer` | `Int32` | N |  | 请求的总数 |
| `bugsTotal` | `integer` | `Int32` | N |  | bug的总数 |
| `createTime` | `string` | `Timestamp` | N |  |  |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


## 获取指定模糊测试的详细覆盖率

### 请求路径
```http
GET /abfuzz/v1/projects/{project_id}/fuzzings/{id}/coverages
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |
| `id` | `string` |  |  |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `contentType` | `mojo.core.MediaType` |  | N |  | A standard MIME type describing the format of the object data. |
| `content` | `string` | `Bytes` | N |  |


#### `mojo.core.MediaType`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `subtype` | `string` |  | N |  |
| `parameter` | `mojo.core.MediaType.Parameter` |  | N |  |  |


#### `mojo.core.MediaType.Parameter`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  |
| `value` | `mojo.core.Value` |  | N |  |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


## 获取指定模糊测试的输入包

### 请求路径
```http
GET /abfuzz/v1/projects/{project_id}/fuzzings/{id}/input_package.zip
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |
| `id` | `string` |  |  |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `contentType` | `mojo.core.MediaType` |  | N |  | A standard MIME type describing the format of the object data. |
| `content` | `string` | `Bytes` | N |  |


#### `mojo.core.MediaType`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `subtype` | `string` |  | N |  |
| `parameter` | `mojo.core.MediaType.Parameter` |  | N |  |  |


#### `mojo.core.MediaType.Parameter`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  |
| `value` | `mojo.core.Value` |  | N |  |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


## 获取单条请求覆盖率的整体统计信息

### 请求路径
```http
GET /abfuzz/v1/projects/{project_id}/fuzzings/{id}/single-coverages
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |


#### Query 参数
| 参数名 | 参数类型 | 格式类型 | 是否必须 | 默认值 | 说明 |
|---|---|---|---|---|---|
| `fuzzing_id` | `string` |  | 否 |  |  |
| `url` | `string` |  | 否 |  |  |
| `limit` | `integer` | `Int64` | 否 |  | 最大返回的条数，默认100 |
| `start` | `string` |  | 否 |  | 查询的开始时间，默认1h前，格式为unixNano |
| `end` | `string` |  | 否 |  | 查询的结束时间，默认为现在，格式为unixNano |
| `interval` | `integer` | `Int64` | 否 |  | 指定间隔返回条数，单位为秒 |
| `step` | `integer` | `Int64` | 否 |  | 查询步骤的宽度，单位为[0-9]+[smhdwy]，例如5m |
| `direction` | `string` |  | 否 |  | 排序方向，默认backward，其它参数forward |
| `page_size` | `integer` | `Int32` | 否 |  |  |
| `page_token` | `string` |  | 否 |  |  |
| `skip` | `integer` | `Int32` | 否 |  |  |


### 返回值

#### 返回对象
| type | description |
|---|---|
| `Array<abfuzz.Coverage>` |  |


#### `abfuzz.Coverage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `element` | `string` |  | N |  | 目标的路径，可以是包、文件、类、函数 |
| `name` | `string` |  | N |  | 目标的名称，不包含路径信息，如果是函数，只是函数名；如果是类，只是类名 |
| `kind` | `string` |  | N |  | 目标的类型: package, file, class, function |
| `hash` | `integer` | `Int64` | N |  | 指定目标的hash值 |
| `lines` | `abfuzz.Coverage.Count` |  | N |  | 行覆盖率，只要本行有一条指令被执行，则本行则被标记为被执行。 |
| `functions` | `abfuzz.Coverage.Count` |  | N |  | 方法覆盖率，任何非抽象的方法，只要有一条指令被执行，则该方法就会被计为被执行。 |
| `classes` | `abfuzz.Coverage.Count` |  | N |  | 类覆盖率，所有类，包括接口，只要其中有一个方法被执行，则标记为被执行。注意：构造函数和静态初始化块也算作方法。 |
| `instructions` | `abfuzz.Coverage.Count` |  | N |  | Jacoco计算的最小单位就是字节码指令。指令覆盖率表明了在所有的指令中，哪些被指令过以及哪些没有被执行。这项指数完全独立于源码格式并且在任何情况下有效，不需要类文件的调试信息。 |
| `branches` | `abfuzz.Coverage.Count` |  | N |  | 对所有的if和switch指令计算了分支覆盖率。这项指标会统计所有的分支数量，并同时支出哪些分支被执行，哪些分支没有被执行。这项指标也在任何情况都有效。异常处理不考虑在分支范围内。 |
| `cxty` | `abfuzz.Coverage.Count` |  | N |  | Cyclomatic Complexity): 圈复杂度会为每一个非抽象方法计算圈复杂度，并为类，包以及组（groups）计算复杂度。圈复杂度简单地说就是为了覆盖所有路径，所需要执行单元测试数量，圈复杂度大说明程序代码可能质量低且难于测试和维护。 |
| `edges` | `abfuzz.Coverage.Count` |  | N |  | 边覆盖 |
| `linesInfo` | `abfuzz.Coverage.LinesInfo` |  | N |  | 在源文件内详细的行覆盖信息，在kind为file时才会有 |
| `branchInfoes` | `Array<abfuzz.Coverage.BranchInfo>` |  | N |  | 在源文件内详细的分支覆盖信息，在kind为file时才会有 |
| `functionInfoes` | `Array<abfuzz.Coverage.FunctionInfo>` |  | N |  | 在源文件内详细的函数覆盖信息，在kind为file时才会有 |
| `classInfoes` | `Array<abfuzz.Coverage.ClassInfo>` |  | N |  | 在源文件内详细的类覆盖信息，在kind为file时才会有 |


#### `abfuzz.Coverage.BranchInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `startLine` | `integer` | `Int32` | N |  | 分支起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 分支起始列 |
| `destLine` | `integer` | `Int32` | N |  | 分支跳转至的行 |
| `attribute` | `integer` | `Int32` | N |  | 分支属性 |
| `executedCount` | `integer` | `Int32` | N |  | 分支的执行数 |


#### `abfuzz.Coverage.ClassInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | 类名称 |
| `startLine` | `integer` | `Int32` | N |  | 类的起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 类的起始列 |
| `endLine` | `integer` | `Int32` | N |  | 类结束行 |
| `endColumn` | `integer` | `Int32` | N |  | 类结束行 |
| `attribute` | `integer` | `Int32` | N |  | 类属性 |
| `executedCount` | `integer` | `Int32` | N |  | 类执行数 |


#### `abfuzz.Coverage.Count`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `hit` | `integer` | `Int64` | N |  | 覆盖到的元素中的计数 |
| `total` | `integer` | `Int64` | N |  | 元素中的总计数值 |


#### `abfuzz.Coverage.FunctionInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | 函数名称 |
| `startLine` | `integer` | `Int32` | N |  | 函数的起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 函数的起始列 |
| `endLine` | `integer` | `Int32` | N |  | 函数结束行 |
| `endColumn` | `integer` | `Int32` | N |  | 函数结束行 |
| `attribute` | `integer` | `Int32` | N |  | 函数属性 |
| `executedCount` | `integer` | `Int32` | N |  | 函数执行数 |


#### `abfuzz.Coverage.LinesInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `start` | `integer` | `Int32` | N |  | 起始行 |
| `attributes` | `Array<integer>` |  | N |  | 每行的属性值 |
| `executedCounts` | `Array<integer>` |  | N |  | 每行的执行数 |


## 开始执行指定的模糊测试

### 请求路径
```http
POST /abfuzz/v1/projects/{project_id}/fuzzings/{id}:start
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |
| `id` | `string` |  |  |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|


#### `mojo.core.Any`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `@type` | `string` |  | Y |  |


#### `mojo.core.Error`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` | `ErrorCode` | N |  | The error code |
| `message` | `string` |  | N |  | A developer-facing error message, which should be in English. Anyuser-facing error message should be localized and sent in the[google.rpc.Status.details][google.rpc.Status.details] field, or localized by the client. |
| `details` | `Array<mojo.core.Any>` |  | N |  | A list of messages that carry the error details.  There is a common set ofmessage types for APIs to use. |


## 停止指定的模糊测试

### 请求路径
```http
POST /abfuzz/v1/projects/{project_id}/fuzzings/{id}:stop
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |
| `id` | `string` |  |  |


### 返回值

#### 返回对象
对象为空

## 获取项目的日志(timeline)

### 请求路径
```http
GET /abfuzz/v1/projects/{project_id}/logs
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |


#### Query 参数
| 参数名 | 参数类型 | 格式类型 | 是否必须 | 默认值 | 说明 |
|---|---|---|---|---|---|
| `page_size` | `integer` | `Int32` | 否 |  |  |
| `page_token` | `string` |  | 否 |  |  |
| `skip` | `integer` | `Int32` | 否 |  |  |


### 返回值

#### 返回对象
| type | description |
|---|---|
| `Array<abfuzz.ProjectLog>` |  |


#### `abfuzz.ProjectLog`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | log id |
| `projectId` | `string` |  | N |  | 项目id |
| `createTime` | `string` | `Timestamp` | N |  | 创建时间 |
| `level` | `string` |  | N |  | 日志等级 |
| `caller` | `string` |  | N |  | 写日志的文件及行号 |
| `log` | `string` |  | N |  | 日志内容 |
| `data` | `Map<string, mojo.core.Value>` |  | N |  | 相关数据 |
| `tid` | `string` |  | N |  | trace id |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


## 查询指定项目下的reproduce列表

### 请求路径
```http
GET /abfuzz/v1/projects/{project_id}/reproduces
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  | 项目的ID |


#### Query 参数
| 参数名 | 参数类型 | 格式类型 | 是否必须 | 默认值 | 说明 |
|---|---|---|---|---|---|
| `source_package_id` | `string` |  | 否 |  | 指定源码包ID，指定特定的版本及分支 |
| `build_operation_name` | `string` |  | 否 |  | 进行build操作后的operation_name |
| `operation_name` | `string` |  | 否 |  | 单独进行reproduce操作后的operation_name |
| `page_size` | `integer` | `Int32` | 否 |  |  |
| `page_token` | `string` |  | 否 |  |  |
| `skip` | `integer` | `Int32` | 否 |  |  |


### 返回值

#### 返回对象
| type | description |
|---|---|
| `Array<abfuzz.Reproduce>` |  |


#### `abfuzz.Engine`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the engine |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.Impact`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `versionIndices` | `Array<string>` |  | N |  | The impacted version indices (including beta, stable and extended_stable). |
| `extendedStableVersion` | `string` |  | N |  | The impacted extended stable version. |
| `extendedStableVersionIndices` | `Array<string>` |  | N |  | The impacted extended stable version indices. |
| `extendedStableVersionLikely` | `boolean` |  | N |  | The impacted extended stable version is merely probable (not definite).See the comment on stable_version_likely. |
| `stableVersion` | `string` |  | N |  | The impacted stable version. |
| `stableVersionIndices` | `Array<string>` |  | N |  | The impacted stable version indices. |
| `stableVersionLikely` | `boolean` |  | N |  | The impacted stable version is merely probable (not definite). Becausefor a non-asan build, we don't have a stable/beta build. Therefore, wemake an intelligent guess on the version. |
| `betaVersion` | `string` |  | N |  | The impacted beta version. |
| `betaVersionIndices` | `Array<string>` |  | N |  | The impacted beta version indices. |
| `betaVersionLikely` | `boolean` |  | N |  | The impacted beta version is merely probable (not definite). See thecomment on stable_version_likely. |
| `headVersion` | `string` |  | N |  | The impacted 'head' version |
| `headVersionIndices` | `Array<string>` |  | N |  | The impacted head version indices. |
| `headVersionLikely` | `boolean` |  | N |  | The impacted head version is likely. |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `abfuzz.Reproduce`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `fuzzerName` | `string` |  | N |  | Fuzzer Name. |
| `projectId` | `string` |  | N |  | Project id |
| `projectName` | `string` |  | N |  | project name of the fuzzing |
| `sourcePackageId` | `string` |  | N |  | the source package id for the fuzzer |
| `buildOperationName` | `string` |  | N |  | the build operation name |
| `buildId` | `string` |  | N |  | the build id |
| `operationName` | `string` |  | N |  | the operation name of the fuzzing |
| `platform` | `string` | `Platform` | N |  | 指定的平台 |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `engine` | `abfuzz.Engine` |  | N |  | 指定的引擎 |
| `testcases` | `Array<abfuzz.Testcase>` |  | N |  | 指定需要验证的Testcase |
| `state` | `string` |  | N |  |
| `createTime` | `string` | `Timestamp` | N |  |  |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.Sanitizer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the sanitizer |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.Testcase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | id for the testcase |
| `description` | `string` |  | N |  | description for the testcase |
| `keywords` | `Array<string>` |  | N |  | keywords is used for searching. |
| `securityFlag` | `boolean` |  | N |  | Is it a security bug ? |
| `securitySeverity` | `integer` | `Int64` | N |  | Security severity of the bug. |
| `oneTimeCrasherFlag` | `boolean` |  | N |  | Did the bug only reproduced once ? |
| `comments` | `string` |  | N |  | Any additional comments. |
| `projectId` | `string` |  | N |  | Project id associated with this test case. |
| `projectName` | `string` |  | N |  | Project name associated with this test case. |
| `sourcePackageId` | `string` |  | N |  | the source package id for the testcase |
| `fuzzerName` | `string` |  | N |  | Name of the fuzzer used to generate this testcase. |
| `fuzzerNameIndices` | `Array<string>` |  | N |  | Fuzzer name indices |
| `overriddenFuzzerName` | `string` |  | N |  | Overridden fuzzer name because actual fuzzer name can be different in manyscenarios (libfuzzer, afl, etc). |
| `fuzzingId` | `string` |  | N |  | the fuzzing which is the testcase generated from |
| `buildOperationName` | `string` |  | N |  | the build operation name of the testcase |
| `operationName` | `string` |  | N |  | the operation name of the testcase when fuzzing |
| `buildId` | `string` |  | N |  | the build id |
| `platform` | `string` | `Platform` | N |  | Platforms (e.g. windows, linux, android). |
| `language` | `string` |  | N |  | the program language for the fuzzing |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `otherPlatforms` | `Array<string>` |  | N |  | Platforms (e.g. windows, linux, android). |
| `otherLangVms` | `Array<abfuzz.LangVM>` |  | N |  | 指定的JDK，如果是聚合后的testcase，会包含多个jdk版本 |
| `engines` | `Array<abfuzz.Engine>` |  | N |  | engine names for the testcase |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | 指定的sanitizers |
| `fuzzedIds` | `Array<string>` |  | N |  | Blobstore keys for various things like original testcase, minimizedtestcase, etc. |
| `minimizedIds` | `Array<string>` |  | N |  |
| `minidumpIds` | `Array<string>` |  | N |  |
| `status` | `string` |  | N |  | Status of this testcase (pending, processed, unreproducible, etc). |
| `open` | `boolean` |  | N |  | Whether testcase is open. |
| `duplicateOf` | `integer` | `Int64` | N |  | Id of the testcase that this is marked as a duplicate of. |
| `groupId` | `integer` | `Int64` | N |  | Id for this testcase's associated group. |
| `groupBugInformation` | `integer` | `Int64` | N |  | Tracking issue tracker bug for this testcase group. |
| `isLeader` | `boolean` |  | N |  | Whether or not a testcase is the leader of its group.If the testcase is not in a group, it's the leader of a group of 1.The default is false because we prefer not to show crashes until we aresure. And group_task will correctly set the value within 30 minutes. |
| `bugInformation` | `string` |  | N |  | Tracking issue tracker bug. One bug number per line (future extension). |
| `isADuplicateFlag` | `boolean` |  | N |  | Whether or not a testcase is a duplicate of other testcase. |
| `hasBugFlag` | `boolean` |  | N |  | Whether testcase has a bug (either bug_information orgroup_bug_information). |
| `bugIndices` | `Array<string>` |  | N |  | Indices for bug_information and group_bug_information. |
| `triaged` | `boolean` |  | N |  | Boolean attribute indicating if cleanup triage needs to be done. |
| `additionalMetadata` | `mojo.core.Object` |  | N |  | Additional metadata stored as a JSON object. This should be used forproperties that are not commonly accessed and do not need to be indexed. |
| `crashType` | `string` |  | N |  | Crash on an invalid read/write. |
| `crashDescription` | `string` |  | N |  | Crash description for runtime error |
| `crashAddress` | `string` |  | N |  | Crashing address. |
| `crashState` | `string` |  | N |  | First x stack frames. |
| `crashStacktrace` | `string` |  | N |  | Complete stacktrace. |
| `lastTestedCrashStacktrace` | `string` |  | N |  | Last tested crash stacktrace using the latest revision. |
| `crashRevision` | `integer` | `Int64` | N |  | Revision that we discovered the crash in. |
| `crashSourceFileName` | `string` |  | N |  | The source file name, may be omitted if debug symbols are not available. |
| `crashSourceLine` | `integer` | `Int32` | N |  | The (1-based) source line number, may be omitted if debug symbols arenot available. |
| `crashFunctionName` | `string` |  | N |  | The function name, may be omitted if debug symbols are not available. |
| `symbolized` | `boolean` |  | N |  | Flag indicating whether or not the testcase has been symbolized. |
| `input` | `abfuzz.Testcase.Input` |  | N |  | The input for the testcase crashing |
| `targetServerLogPath` | `string` |  | N |  | The target server log path with java testcase |
| `absolutePath` | `string` |  | N |  | The file on the agent that generated the testcase. |
| `binaryFlag` | `boolean` |  | N |  | Is this a binary file? |
| `flakyStack` | `boolean` |  | N |  | Does the testcase crash stack vary b/w crashes ? |
| `httpFlag` | `boolean` |  | N |  | Do we need to test this testcase using an HTTP/HTTPS server? |
| `gestures` | `Array<string>` |  | N |  | Fake user interaction sequences like key clicks, mouse movements, etc. |
| `redzone` | `integer` | `Int64` | N |  | ASAN redzone size in bytes. |
| `disableUbsan` | `boolean` |  | N |  | Flag indicating if UBSan detection should be disabled. This is needed forcases when ASan and UBSan are bundled in the same build configurationand we need to disable UBSan in some runs to find the potentially moreinteresting ASan bugs. |
| `timeoutMultiplier` | `number` | `Float64` | N |  | Adjusts timeout based on multiplier value. |
| `regression` | `string` |  | N |  | Regression range. |
| `fixed` | `string` |  | N |  | Revisions where this issue has been fixed. |
| `impactIndices` | `Array<string>` |  | N |  | Impact indices for searching. |
| `impact` | `abfuzz.Impact` |  | N |  | 用例影响源代码的版本信息 |
| `isImpactSetFlag` | `boolean` |  | N |  | Whether or not impact task has been run on this testcase. |
| `uploaderEmail` | `string` |  | N |  | Uploader email address. |
| `createTime` | `string` | `Timestamp` | N |  | Timestamp. |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.Testcase.Input`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  | binaray, file, json |
| `content` | `mojo.core.Value` |  | N |  | the input content |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


## 获取指定的reproduce信息

### 请求路径
```http
GET /abfuzz/v1/projects/{project_id}/reproduces/{id}
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |
| `id` | `string` |  |  |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `fuzzerName` | `string` |  | N |  | Fuzzer Name. |
| `projectId` | `string` |  | N |  | Project id |
| `projectName` | `string` |  | N |  | project name of the fuzzing |
| `sourcePackageId` | `string` |  | N |  | the source package id for the fuzzer |
| `buildOperationName` | `string` |  | N |  | the build operation name |
| `buildId` | `string` |  | N |  | the build id |
| `operationName` | `string` |  | N |  | the operation name of the fuzzing |
| `platform` | `string` | `Platform` | N |  | 指定的平台 |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `engine` | `abfuzz.Engine` |  | N |  | 指定的引擎 |
| `testcases` | `Array<abfuzz.Testcase>` |  | N |  | 指定需要验证的Testcase |
| `state` | `string` |  | N |  |
| `createTime` | `string` | `Timestamp` | N |  |  |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.Engine`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the engine |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.Impact`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `versionIndices` | `Array<string>` |  | N |  | The impacted version indices (including beta, stable and extended_stable). |
| `extendedStableVersion` | `string` |  | N |  | The impacted extended stable version. |
| `extendedStableVersionIndices` | `Array<string>` |  | N |  | The impacted extended stable version indices. |
| `extendedStableVersionLikely` | `boolean` |  | N |  | The impacted extended stable version is merely probable (not definite).See the comment on stable_version_likely. |
| `stableVersion` | `string` |  | N |  | The impacted stable version. |
| `stableVersionIndices` | `Array<string>` |  | N |  | The impacted stable version indices. |
| `stableVersionLikely` | `boolean` |  | N |  | The impacted stable version is merely probable (not definite). Becausefor a non-asan build, we don't have a stable/beta build. Therefore, wemake an intelligent guess on the version. |
| `betaVersion` | `string` |  | N |  | The impacted beta version. |
| `betaVersionIndices` | `Array<string>` |  | N |  | The impacted beta version indices. |
| `betaVersionLikely` | `boolean` |  | N |  | The impacted beta version is merely probable (not definite). See thecomment on stable_version_likely. |
| `headVersion` | `string` |  | N |  | The impacted 'head' version |
| `headVersionIndices` | `Array<string>` |  | N |  | The impacted head version indices. |
| `headVersionLikely` | `boolean` |  | N |  | The impacted head version is likely. |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `abfuzz.Sanitizer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the sanitizer |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.Testcase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | id for the testcase |
| `description` | `string` |  | N |  | description for the testcase |
| `keywords` | `Array<string>` |  | N |  | keywords is used for searching. |
| `securityFlag` | `boolean` |  | N |  | Is it a security bug ? |
| `securitySeverity` | `integer` | `Int64` | N |  | Security severity of the bug. |
| `oneTimeCrasherFlag` | `boolean` |  | N |  | Did the bug only reproduced once ? |
| `comments` | `string` |  | N |  | Any additional comments. |
| `projectId` | `string` |  | N |  | Project id associated with this test case. |
| `projectName` | `string` |  | N |  | Project name associated with this test case. |
| `sourcePackageId` | `string` |  | N |  | the source package id for the testcase |
| `fuzzerName` | `string` |  | N |  | Name of the fuzzer used to generate this testcase. |
| `fuzzerNameIndices` | `Array<string>` |  | N |  | Fuzzer name indices |
| `overriddenFuzzerName` | `string` |  | N |  | Overridden fuzzer name because actual fuzzer name can be different in manyscenarios (libfuzzer, afl, etc). |
| `fuzzingId` | `string` |  | N |  | the fuzzing which is the testcase generated from |
| `buildOperationName` | `string` |  | N |  | the build operation name of the testcase |
| `operationName` | `string` |  | N |  | the operation name of the testcase when fuzzing |
| `buildId` | `string` |  | N |  | the build id |
| `platform` | `string` | `Platform` | N |  | Platforms (e.g. windows, linux, android). |
| `language` | `string` |  | N |  | the program language for the fuzzing |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `otherPlatforms` | `Array<string>` |  | N |  | Platforms (e.g. windows, linux, android). |
| `otherLangVms` | `Array<abfuzz.LangVM>` |  | N |  | 指定的JDK，如果是聚合后的testcase，会包含多个jdk版本 |
| `engines` | `Array<abfuzz.Engine>` |  | N |  | engine names for the testcase |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | 指定的sanitizers |
| `fuzzedIds` | `Array<string>` |  | N |  | Blobstore keys for various things like original testcase, minimizedtestcase, etc. |
| `minimizedIds` | `Array<string>` |  | N |  |
| `minidumpIds` | `Array<string>` |  | N |  |
| `status` | `string` |  | N |  | Status of this testcase (pending, processed, unreproducible, etc). |
| `open` | `boolean` |  | N |  | Whether testcase is open. |
| `duplicateOf` | `integer` | `Int64` | N |  | Id of the testcase that this is marked as a duplicate of. |
| `groupId` | `integer` | `Int64` | N |  | Id for this testcase's associated group. |
| `groupBugInformation` | `integer` | `Int64` | N |  | Tracking issue tracker bug for this testcase group. |
| `isLeader` | `boolean` |  | N |  | Whether or not a testcase is the leader of its group.If the testcase is not in a group, it's the leader of a group of 1.The default is false because we prefer not to show crashes until we aresure. And group_task will correctly set the value within 30 minutes. |
| `bugInformation` | `string` |  | N |  | Tracking issue tracker bug. One bug number per line (future extension). |
| `isADuplicateFlag` | `boolean` |  | N |  | Whether or not a testcase is a duplicate of other testcase. |
| `hasBugFlag` | `boolean` |  | N |  | Whether testcase has a bug (either bug_information orgroup_bug_information). |
| `bugIndices` | `Array<string>` |  | N |  | Indices for bug_information and group_bug_information. |
| `triaged` | `boolean` |  | N |  | Boolean attribute indicating if cleanup triage needs to be done. |
| `additionalMetadata` | `mojo.core.Object` |  | N |  | Additional metadata stored as a JSON object. This should be used forproperties that are not commonly accessed and do not need to be indexed. |
| `crashType` | `string` |  | N |  | Crash on an invalid read/write. |
| `crashDescription` | `string` |  | N |  | Crash description for runtime error |
| `crashAddress` | `string` |  | N |  | Crashing address. |
| `crashState` | `string` |  | N |  | First x stack frames. |
| `crashStacktrace` | `string` |  | N |  | Complete stacktrace. |
| `lastTestedCrashStacktrace` | `string` |  | N |  | Last tested crash stacktrace using the latest revision. |
| `crashRevision` | `integer` | `Int64` | N |  | Revision that we discovered the crash in. |
| `crashSourceFileName` | `string` |  | N |  | The source file name, may be omitted if debug symbols are not available. |
| `crashSourceLine` | `integer` | `Int32` | N |  | The (1-based) source line number, may be omitted if debug symbols arenot available. |
| `crashFunctionName` | `string` |  | N |  | The function name, may be omitted if debug symbols are not available. |
| `symbolized` | `boolean` |  | N |  | Flag indicating whether or not the testcase has been symbolized. |
| `input` | `abfuzz.Testcase.Input` |  | N |  | The input for the testcase crashing |
| `targetServerLogPath` | `string` |  | N |  | The target server log path with java testcase |
| `absolutePath` | `string` |  | N |  | The file on the agent that generated the testcase. |
| `binaryFlag` | `boolean` |  | N |  | Is this a binary file? |
| `flakyStack` | `boolean` |  | N |  | Does the testcase crash stack vary b/w crashes ? |
| `httpFlag` | `boolean` |  | N |  | Do we need to test this testcase using an HTTP/HTTPS server? |
| `gestures` | `Array<string>` |  | N |  | Fake user interaction sequences like key clicks, mouse movements, etc. |
| `redzone` | `integer` | `Int64` | N |  | ASAN redzone size in bytes. |
| `disableUbsan` | `boolean` |  | N |  | Flag indicating if UBSan detection should be disabled. This is needed forcases when ASan and UBSan are bundled in the same build configurationand we need to disable UBSan in some runs to find the potentially moreinteresting ASan bugs. |
| `timeoutMultiplier` | `number` | `Float64` | N |  | Adjusts timeout based on multiplier value. |
| `regression` | `string` |  | N |  | Regression range. |
| `fixed` | `string` |  | N |  | Revisions where this issue has been fixed. |
| `impactIndices` | `Array<string>` |  | N |  | Impact indices for searching. |
| `impact` | `abfuzz.Impact` |  | N |  | 用例影响源代码的版本信息 |
| `isImpactSetFlag` | `boolean` |  | N |  | Whether or not impact task has been run on this testcase. |
| `uploaderEmail` | `string` |  | N |  | Uploader email address. |
| `createTime` | `string` | `Timestamp` | N |  | Timestamp. |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.Testcase.Input`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  | binaray, file, json |
| `content` | `mojo.core.Value` |  | N |  | the input content |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


## 获取项目更新过的源代码包列表

### 请求路径
```http
GET /abfuzz/v1/projects/{project_id}/source-packages
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |


#### Query 参数
| 参数名 | 参数类型 | 格式类型 | 是否必须 | 默认值 | 说明 |
|---|---|---|---|---|---|
| `page_size` | `integer` | `Int32` | 否 |  |  |
| `page_token` | `string` |  | 否 |  |  |
| `skip` | `integer` | `Int32` | 否 |  |  |


### 返回值

#### 返回对象
| type | description |
|---|---|
| `Array<abfuzz.SourcePackage>` |  |


#### `abfuzz.Repository`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `url` | `string` | `Url` | N |  | the git url for the source package |
| `branch` | `string` |  | N |  | the git branch for the source package |
| `tag` | `string` |  | N |  | the git tag for the source package |
| `commitTime` | `string` | `Timestamp` | N |  | the used commit time for the source package |
| `commitKey` | `string` |  | N |  | the used commit key for the source package, it is hash key for git |


#### `abfuzz.SourcePackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the uuid for the source package |
| `projectId` | `string` |  | N |  | the project id which including the source package |
| `projectName` | `string` |  | N |  | the project name which including the source package |
| `projectVersion` | `string` |  | N |  | the project version |
| `type` | `string` |  | N |  | the type of the source packagewhitebox, graybox, blackbox |
| `name` | `string` |  | N |  | the name of the module of the source package |
| `fuzzRepo` | `abfuzz.Repository` |  | N |  | the repository information for the fuzz code reposisity in the source package |
| `sourceRepo` | `abfuzz.Repository` |  | N |  | the repository information for the main code reposisity of custom in the source package |
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `fileSet` | `Array<abfuzz.StorageFile>` |  | N |  | the file set for the source package |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary or raw source code package. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |
| `customVersion` | `string` |  | N |  | more version info for the custom system |
| `codeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `createTime` | `string` | `Timestamp` | N |  | create time of the source code package |
| `updateTime` | `string` | `Timestamp` | N |  | update time of the source code package |


#### `abfuzz.StorageFile`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  | 文件在minio上的 地址 |
| `checksum` | `string` | `Checksum` | N |  | 文件的hash校验码 |
| `type` | `string` |  | N |  | 文件类型，比如： `corpus`, `harness`, `source`, `build-script`, `openapi`, `jar` |
| `group` | `string` |  | N |  | 文件属于某个组，可以是某个fuzzer |


## 删除源代码包

### 请求路径
```http
DELETE /abfuzz/v1/projects/{project_id}/source-packages/{id}
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |
| `id` | `string` |  |  |


### 返回值

#### 返回对象
对象为空

## 获取项目指定的源代码包

### 请求路径
```http
GET /abfuzz/v1/projects/{project_id}/source-packages/{source_package_id}
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |
| `source_package_id` | `string` |  |  |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the uuid for the source package |
| `projectId` | `string` |  | N |  | the project id which including the source package |
| `projectName` | `string` |  | N |  | the project name which including the source package |
| `projectVersion` | `string` |  | N |  | the project version |
| `type` | `string` |  | N |  | the type of the source packagewhitebox, graybox, blackbox |
| `name` | `string` |  | N |  | the name of the module of the source package |
| `fuzzRepo` | `abfuzz.Repository` |  | N |  | the repository information for the fuzz code reposisity in the source package |
| `sourceRepo` | `abfuzz.Repository` |  | N |  | the repository information for the main code reposisity of custom in the source package |
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `fileSet` | `Array<abfuzz.StorageFile>` |  | N |  | the file set for the source package |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary or raw source code package. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |
| `customVersion` | `string` |  | N |  | more version info for the custom system |
| `codeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `createTime` | `string` | `Timestamp` | N |  | create time of the source code package |
| `updateTime` | `string` | `Timestamp` | N |  | update time of the source code package |


#### `abfuzz.Repository`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `url` | `string` | `Url` | N |  | the git url for the source package |
| `branch` | `string` |  | N |  | the git branch for the source package |
| `tag` | `string` |  | N |  | the git tag for the source package |
| `commitTime` | `string` | `Timestamp` | N |  | the used commit time for the source package |
| `commitKey` | `string` |  | N |  | the used commit key for the source package, it is hash key for git |


#### `abfuzz.StorageFile`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  | 文件在minio上的 地址 |
| `checksum` | `string` | `Checksum` | N |  | 文件的hash校验码 |
| `type` | `string` |  | N |  | 文件类型，比如： `corpus`, `harness`, `source`, `build-script`, `openapi`, `jar` |
| `group` | `string` |  | N |  | 文件属于某个组，可以是某个fuzzer |


## 获取项目指定的源代码包内的源代码文件

### 请求路径
```http
GET /abfuzz/v1/projects/{project_id}/source-packages/{source_package_id}/code-files
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |
| `source_package_id` | `string` |  |  |


#### Query 参数
| 参数名 | 参数类型 | 格式类型 | 是否必须 | 默认值 | 说明 |
|---|---|---|---|---|---|
| `name` | `string` |  | 否 |  |  |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `contentType` | `mojo.core.MediaType` |  | N |  | A standard MIME type describing the format of the object data. |
| `content` | `string` | `Bytes` | N |  |


#### `mojo.core.MediaType`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `subtype` | `string` |  | N |  |
| `parameter` | `mojo.core.MediaType.Parameter` |  | N |  |  |


#### `mojo.core.MediaType.Parameter`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  |
| `value` | `mojo.core.Value` |  | N |  |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


## 获取项目指定的源代码包内的源代码文件

### 请求路径
```http
GET /abfuzz/v1/projects/{project_id}/source-packages/{source_package_id}/code-map.json
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |
| `source_package_id` | `string` |  |  |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `contentType` | `mojo.core.MediaType` |  | N |  | A standard MIME type describing the format of the object data. |
| `content` | `string` | `Bytes` | N |  |


#### `mojo.core.MediaType`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `subtype` | `string` |  | N |  |
| `parameter` | `mojo.core.MediaType.Parameter` |  | N |  |  |


#### `mojo.core.MediaType.Parameter`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  |
| `value` | `mojo.core.Value` |  | N |  |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


## 更新指定源代码包的文件组
如果不指定group，则会将file set合并至原有的file set中；如果指定group，则会清除原有group下的文件，重新更新

### 请求路径
```http
PUT /abfuzz/v1/projects/{project_id}/source-packages/{source_package_id}/file-set
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |
| `source_package_id` | `string` |  |  |


#### Query 参数
| 参数名 | 参数类型 | 格式类型 | 是否必须 | 默认值 | 说明 |
|---|---|---|---|---|---|
| `group` | `string` |  | 否 |  |  |


#### Body 请求对象
| type | description |
|---|---|
| `Array<abfuzz.StorageFile>` |  |


#### `abfuzz.StorageFile`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  | 文件在minio上的 地址 |
| `checksum` | `string` | `Checksum` | N |  | 文件的hash校验码 |
| `type` | `string` |  | N |  | 文件类型，比如： `corpus`, `harness`, `source`, `build-script`, `openapi`, `jar` |
| `group` | `string` |  | N |  | 文件属于某个组，可以是某个fuzzer |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the uuid for the source package |
| `projectId` | `string` |  | N |  | the project id which including the source package |
| `projectName` | `string` |  | N |  | the project name which including the source package |
| `projectVersion` | `string` |  | N |  | the project version |
| `type` | `string` |  | N |  | the type of the source packagewhitebox, graybox, blackbox |
| `name` | `string` |  | N |  | the name of the module of the source package |
| `fuzzRepo` | `abfuzz.Repository` |  | N |  | the repository information for the fuzz code reposisity in the source package |
| `sourceRepo` | `abfuzz.Repository` |  | N |  | the repository information for the main code reposisity of custom in the source package |
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `fileSet` | `Array<abfuzz.StorageFile>` |  | N |  | the file set for the source package |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary or raw source code package. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |
| `customVersion` | `string` |  | N |  | more version info for the custom system |
| `codeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `createTime` | `string` | `Timestamp` | N |  | create time of the source code package |
| `updateTime` | `string` | `Timestamp` | N |  | update time of the source code package |


#### `abfuzz.Repository`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `url` | `string` | `Url` | N |  | the git url for the source package |
| `branch` | `string` |  | N |  | the git branch for the source package |
| `tag` | `string` |  | N |  | the git tag for the source package |
| `commitTime` | `string` | `Timestamp` | N |  | the used commit time for the source package |
| `commitKey` | `string` |  | N |  | the used commit key for the source package, it is hash key for git |


#### `abfuzz.StorageFile`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  | 文件在minio上的 地址 |
| `checksum` | `string` | `Checksum` | N |  | 文件的hash校验码 |
| `type` | `string` |  | N |  | 文件类型，比如： `corpus`, `harness`, `source`, `build-script`, `openapi`, `jar` |
| `group` | `string` |  | N |  | 文件属于某个组，可以是某个fuzzer |


## 删除指定源代码包的文件组中指定group中文件，如果没有指定group，则清空file set

### 请求路径
```http
DELETE /abfuzz/v1/projects/{project_id}/source-packages/{source_package_id}/file-set
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |
| `source_package_id` | `string` |  |  |


#### Query 参数
| 参数名 | 参数类型 | 格式类型 | 是否必须 | 默认值 | 说明 |
|---|---|---|---|---|---|
| `group` | `string` |  | 否 |  |  |


### 返回值

#### 返回对象
对象为空

## 获取项目指定的源代码包内的AutoHarness的元信息文件

### 请求路径
```http
GET /abfuzz/v1/projects/{project_id}/source-packages/{source_package_id}/harness.json
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |
| `source_package_id` | `string` |  |  |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `contentType` | `mojo.core.MediaType` |  | N |  | A standard MIME type describing the format of the object data. |
| `content` | `string` | `Bytes` | N |  |


#### `mojo.core.MediaType`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `subtype` | `string` |  | N |  |
| `parameter` | `mojo.core.MediaType.Parameter` |  | N |  |  |


#### `mojo.core.MediaType.Parameter`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  |
| `value` | `mojo.core.Value` |  | N |  |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


## 获取项目指定的源代码包内的OPENAPI文件

### 请求路径
```http
GET /abfuzz/v1/projects/{project_id}/source-packages/{source_package_id}/openapi.json
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |
| `source_package_id` | `string` |  |  |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `contentType` | `mojo.core.MediaType` |  | N |  | A standard MIME type describing the format of the object data. |
| `content` | `string` | `Bytes` | N |  |


#### `mojo.core.MediaType`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `subtype` | `string` |  | N |  |
| `parameter` | `mojo.core.MediaType.Parameter` |  | N |  |  |


#### `mojo.core.MediaType.Parameter`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  |
| `value` | `mojo.core.Value` |  | N |  |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


## 获取项目在fuzzing的指定的status信息

### 请求路径
```http
GET /abfuzz/v1/projects/{project_id}/status/{id}
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  | 指定项目ID |
| `id` | `string` |  | 状态ID，如果获取最新状态，则使用：latest |


#### Query 参数
| 参数名 | 参数类型 | 格式类型 | 是否必须 | 默认值 | 说明 |
|---|---|---|---|---|---|
| `source_package_id` | `string` |  | 是 |  | 指定源码包ID，指定特定的版本及分支 |
| `operation_name` | `string` |  | 是 |  | 每次启动执行时返回的operation name |
| `platform` | `string` | `Platform` | 否 |  | 指定某个特定的平台 |
| `language` | `string` |  | 否 |  | 指定某个特定的语言 |
| `lang_vm` | `abfuzz.LangVM` |  | 否 |  | 指定某个特定的语言虚拟机 |
| `fuzzer` | `string` |  | 否 |  | 特别指定fuzzer |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | status id |
| `state` | `string` |  | N |  | state of the project, "build.created", "build.running", "fuzz.created", "reproduce" |
| `error` | `mojo.core.Error` |  | N |  | @lang('zh-CN') |
| `projectId` | `string` |  | N |  | project id |
| `projectName` | `string` |  | N |  | project name, normally the same with the costom's main source repo |
| `sourcePackageId` | `string` |  | N |  | project source package |
| `operationName` | `string` |  | N |  | the operation name of the project status |
| `buildOperationName` | `string` |  | N |  | the build operation name of the project |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `fuzzerName` | `string` |  | N |  | 所有的信息是否在同一个指定的fuzzer之下 |
| `tasks` | `Array<abfuzz.Task>` |  | N |  | the running tasks for process the project |
| `builds` | `Array<abfuzz.Build>` |  | N |  | the build result of the project |
| `reproduces` | `Array<abfuzz.Reproduce>` |  | N |  | the reproduces result of the project |
| `fuzzings` | `Array<abfuzz.Fuzzing>` |  | N |  | the fuzzings' status for the project |
| `createTime` | `string` | `Timestamp` | N |  | the create time of the project |
| `updateTime` | `string` | `Timestamp` | N |  | the update time of the project |


#### `abfuzz.Build`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `projectId` | `string` |  | N |  |
| `sourcePackageId` | `string` |  | N |  |
| `operationName` | `string` |  | N |  |
| `platform` | `string` | `Platform` | N |  | 指定的平台 |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的语言虚拟机，JDK for java |
| `engine` | `abfuzz.Engine` |  | N |  | 指定的引擎 |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | 指定的sanitizer |
| `fuzzers` | `Array<abfuzz.Fuzzer>` |  | N |  | 指定输出的fuzzer |
| `state` | `string` |  | N |  | finished, failed |
| `error` | `mojo.core.Error` |  | N |  | build失败后的错误信息 |
| `log` | `string` |  | N |  |
| `logType` | `string` |  | N |  | text, url, binary (std base64) |
| `createTime` | `string` | `Timestamp` | N |  |  |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.CaseStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `disable` | `array` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.Container`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the id of the container |
| `name` | `string` |  | N |  | the name of the container |


#### `abfuzz.ContextCase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `steps` | `Array<abfuzz.CaseStep>` |  | N |  |


#### `abfuzz.ContextRequest`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `setup` | `abfuzz.ContextSetup` |  | N |  | 全局设置，在cases之前执行 |
| `cases` | `Array<abfuzz.ContextCase>` |  | N |  | OpenAPI里的请求(用path和method来对应)，包含在cases里的优先执行，没有包含在cases里的请求，每个请求自动生成一个case，在cases之后依次自行 |
| `teardown` | `abfuzz.ContextTeardown` |  | N |  | 在cases之后执行的请求 |


#### `abfuzz.ContextSetup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.SetupStep>` |  | N |  |


#### `abfuzz.ContextTeardown`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.TeardownStep>` |  | N |  |


#### `abfuzz.CorpusConfig`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `includings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `excludings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `httpBaseUrl` | `string` | `Url` | N |  | URl type<br>Uniform Resource Identifier (URI) is a string of characters used to identify a resource.<br>A Uniform Resource Name (URN) may be compared to a person's name,while a Uniform Resource Locator (URL) may be compared to their street address.In other words, a URN identifies an item and a URL provides a method for finding it.<br> |
| `httpMappingPort` | `integer` | `Int32` | N |  |
| `httpMappingHost` | `string` |  | N |  |
| `httpStaticValues` | `mojo.core.Object` |  | N |  | Object type |
| `httpCorpuses` | `Array<abfuzz.HttpCorpus>` |  | N |  |
| `combinedApiPath` | `string` |  | N |  |
| `asyncApi` | `boolean` |  | N |  |


#### `abfuzz.Coverage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `element` | `string` |  | N |  | 目标的路径，可以是包、文件、类、函数 |
| `name` | `string` |  | N |  | 目标的名称，不包含路径信息，如果是函数，只是函数名；如果是类，只是类名 |
| `kind` | `string` |  | N |  | 目标的类型: package, file, class, function |
| `hash` | `integer` | `Int64` | N |  | 指定目标的hash值 |
| `lines` | `abfuzz.Coverage.Count` |  | N |  | 行覆盖率，只要本行有一条指令被执行，则本行则被标记为被执行。 |
| `functions` | `abfuzz.Coverage.Count` |  | N |  | 方法覆盖率，任何非抽象的方法，只要有一条指令被执行，则该方法就会被计为被执行。 |
| `classes` | `abfuzz.Coverage.Count` |  | N |  | 类覆盖率，所有类，包括接口，只要其中有一个方法被执行，则标记为被执行。注意：构造函数和静态初始化块也算作方法。 |
| `instructions` | `abfuzz.Coverage.Count` |  | N |  | Jacoco计算的最小单位就是字节码指令。指令覆盖率表明了在所有的指令中，哪些被指令过以及哪些没有被执行。这项指数完全独立于源码格式并且在任何情况下有效，不需要类文件的调试信息。 |
| `branches` | `abfuzz.Coverage.Count` |  | N |  | 对所有的if和switch指令计算了分支覆盖率。这项指标会统计所有的分支数量，并同时支出哪些分支被执行，哪些分支没有被执行。这项指标也在任何情况都有效。异常处理不考虑在分支范围内。 |
| `cxty` | `abfuzz.Coverage.Count` |  | N |  | Cyclomatic Complexity): 圈复杂度会为每一个非抽象方法计算圈复杂度，并为类，包以及组（groups）计算复杂度。圈复杂度简单地说就是为了覆盖所有路径，所需要执行单元测试数量，圈复杂度大说明程序代码可能质量低且难于测试和维护。 |
| `edges` | `abfuzz.Coverage.Count` |  | N |  | 边覆盖 |
| `linesInfo` | `abfuzz.Coverage.LinesInfo` |  | N |  | 在源文件内详细的行覆盖信息，在kind为file时才会有 |
| `branchInfoes` | `Array<abfuzz.Coverage.BranchInfo>` |  | N |  | 在源文件内详细的分支覆盖信息，在kind为file时才会有 |
| `functionInfoes` | `Array<abfuzz.Coverage.FunctionInfo>` |  | N |  | 在源文件内详细的函数覆盖信息，在kind为file时才会有 |
| `classInfoes` | `Array<abfuzz.Coverage.ClassInfo>` |  | N |  | 在源文件内详细的类覆盖信息，在kind为file时才会有 |


#### `abfuzz.Coverage.BranchInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `startLine` | `integer` | `Int32` | N |  | 分支起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 分支起始列 |
| `destLine` | `integer` | `Int32` | N |  | 分支跳转至的行 |
| `attribute` | `integer` | `Int32` | N |  | 分支属性 |
| `executedCount` | `integer` | `Int32` | N |  | 分支的执行数 |


#### `abfuzz.Coverage.ClassInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | 类名称 |
| `startLine` | `integer` | `Int32` | N |  | 类的起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 类的起始列 |
| `endLine` | `integer` | `Int32` | N |  | 类结束行 |
| `endColumn` | `integer` | `Int32` | N |  | 类结束行 |
| `attribute` | `integer` | `Int32` | N |  | 类属性 |
| `executedCount` | `integer` | `Int32` | N |  | 类执行数 |


#### `abfuzz.Coverage.Count`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `hit` | `integer` | `Int64` | N |  | 覆盖到的元素中的计数 |
| `total` | `integer` | `Int64` | N |  | 元素中的总计数值 |


#### `abfuzz.Coverage.FunctionInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | 函数名称 |
| `startLine` | `integer` | `Int32` | N |  | 函数的起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 函数的起始列 |
| `endLine` | `integer` | `Int32` | N |  | 函数结束行 |
| `endColumn` | `integer` | `Int32` | N |  | 函数结束行 |
| `attribute` | `integer` | `Int32` | N |  | 函数属性 |
| `executedCount` | `integer` | `Int32` | N |  | 函数执行数 |


#### `abfuzz.Coverage.LinesInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `start` | `integer` | `Int32` | N |  | 起始行 |
| `attributes` | `Array<integer>` |  | N |  | 每行的属性值 |
| `executedCounts` | `Array<integer>` |  | N |  | 每行的执行数 |


#### `abfuzz.CoverageMap`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `node` | `string` |  | N |  |
| `childNode` | `string` |  | N |  |
| `uri` | `string` |  | N |  |


#### `abfuzz.CoveragePackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |


#### `abfuzz.CpuStat`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `number` | `integer` | `Int32` | N |  | CPU编号 |
| `state` | `string` |  | N |  | CPU的状态 AVAILABLE， CAUTION |


#### `abfuzz.EndPoint`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` |  | N |  |
| `http` | `abfuzz.EndPoint.Http` |  | N |  |  |
| `priority` | `integer` | `Int32` | N |  |


#### `abfuzz.EndPoint.Http`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `path` | `string` |  | N |  |
| `methods` | `Array<string>` |  | N |  |


#### `abfuzz.Engine`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the engine |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.Entity`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `stepType` | `string` |  | N |  |
| `interfaces` | `Array<abfuzz.Entity.Interface>` |  | N |  |


#### `abfuzz.Entity.Interface`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `type` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | N |  |
| `pathParams` | `Map<string, string>` |  | N |  |
| `queryParams` | `Map<string, string>` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `response` | `mojo.http.Response` |  | N |  | Response represents the response from an HTTP request. |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |


#### `abfuzz.Fixup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.FuzzStats`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `projectId` | `string` |  | N |  |
| `sourcePackageId` | `string` |  | N |  |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `buildId` | `string` |  | N |  | the build id |
| `operationName` | `string` |  | N |  | the operation name of the fuzzing |
| `fuzzingId` | `string` |  | N |  |
| `fuzzerName` | `string` |  | N |  |
| `cyclesDone` | `integer` | `Int32` | N |  |
| `execsDone` | `integer` | `Int32` | N |  | 总共完成的执行数 |
| `execsSpeed` | `number` | `Float32` | N |  | 每秒的执行次数 |
| `cpuStats` | `Array<abfuzz.CpuStat>` |  | N |  | CPU的状态 |
| `pathsTotal` | `integer` | `Int32` | N |  | 执行边的总数 |
| `pathsFavored` | `integer` | `Int32` | N |  |
| `pathsFound` | `integer` | `Int32` | N |  | 发现的边的总数 |
| `pathsImported` | `integer` | `Int32` | N |  |
| `pathsMaxDepth` | `integer` | `Int32` | N |  |
| `pathsPendingFavored` | `integer` | `Int32` | N |  |
| `pathsPendingTotal` | `integer` | `Int32` | N |  |
| `pathsVariable` | `integer` | `Int32` | N |  |
| `pathsStability` | `number` | `Float32` | N |  |
| `bitmapCoverage` | `number` | `Float32` | N |  | bitmap覆盖率 |
| `uniqueCrashes` | `integer` | `Int32` | N |  | 独立的crash的总数 |
| `uniqueHangs` | `integer` | `Int32` | N |  | 独立的hang的总数 |
| `lastNewPath` | `string` | `Duration` | N |  | 最近一次新path的持续时间 |
| `lastUniqueCrash` | `string` | `Duration` | N |  | 最近一次发现独立crash的持续时间 |
| `lastUniqueHang` | `string` | `Duration` | N |  | 最近一次发现独立hang的持续时间 |
| `execsSinceCrash` | `integer` | `Int32` | N |  | 从上次unique的crash的执行数 |
| `execTimeout` | `integer` | `Int32` | N |  |
| `createTime` | `string` | `Timestamp` | N |  |  |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.Fuzzer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | Y |  | the fuzzer name |
| `group` | `string` |  | N |  | the group id for the fuzzers |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `fuzzerPackage` | `abfuzz.FuzzerPackage` |  | N |  | the fuzzer package of the fuzzer |
| `executablePath` | `string` |  | N |  | Fuzzer's main executable path, relative to root. |
| `executableArguments` | `Array<string>` |  | N |  | the input arguments for the executable, it will be skipped when launcher_script is set. |
| `executableEnvironment` | `Map<string, string>` |  | N |  | the environment setting for the executable |
| `launcherScript` | `string` |  | N |  | Custom script that should be used to launch executable for this fuzzer.It will not try to launch the target if "@service-auto-start" set. |
| `timeout` | `integer` | `Int64` | N |  | Fuzzing timeout. |
| `testcaseTimeoutMillis` | `integer` | `Int64` | N |  | Testcase timeout. |
| `autoStop` | `boolean` |  | N |  | Fuzzing automatic stop. |
| `autoHarness` | `boolean` |  | N |  | auto generate harness |
| `customMutatorPlugin` | `string` |  | N |  | custom mutator plugin for Fuzz Engine |
| `customAllowTargets` | `Array<string>` |  | N |  | custom file name & function name targets for instruments and coverage, like white list. |
| `customDenyTargets` | `Array<string>` |  | N |  | custom targets for instruments and coverage, like white list. |
| `context` | `abfuzz.ContextRequest` |  | N |  | context request |
| `corpusPath` | `string` |  | N |  | the special corpus path |
| `preprocessScript` | `string` |  | N |  | the preprocess script info |
| `onlyFunctionalTest` | `boolean` |  | N |  | is only fuzzing user corpus |
| `autoHarnessCommand` | `string` |  | N |  | execute the command before run auto harness generation |
| `autoHarnessScript` | `string` |  | N |  | execute the script before run auto harness generation if the command is empty |
| `autoHarnessArguments` | `Array<string>` |  | N |  | the arguments when to run the harness generation, usually for including path setting |
| `autoHarnessBlacklist` | `Array<string>` |  | N |  | the blacklist files to run the harness generation, these files will be skipped. |
| `autoHarnessWebServer` | `boolean` |  | N |  | determine if the java auto harness server is a web server |
| `metadata` | `Map<string, mojo.core.Value>` |  | N |  | the meta information for the fuzzing |
| `corpus` | `abfuzz.CorpusConfig` |  | N |  |  |
| `entities` | `Array<abfuzz.Entity>` |  | N |  |


#### `abfuzz.FuzzerPackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `type` | `string` |  | N |  | the type of the source package, oneof the whitebox, greybox, blackbox |
| `filename` | `string` |  | N |  | Filename for the custom binary. |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |


#### `abfuzz.Fuzzing`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | Additionally allows '.' and '@' over NAME_CHECK_REGEX.VALID_NAME_REGEX: re.compile(r'^[a-zA-Z0-9_@.-]+$') |
| `name` | `string` |  | N |  | Fuzzer Name. |
| `group` | `string` |  | N |  | the group id for the fuzzers |
| `projectId` | `string` |  | N |  | Project id |
| `projectName` | `string` |  | N |  | project name of the fuzzing |
| `state` | `string` |  | N |  | the state of the Fuzzing |
| `buildOperationName` | `string` |  | N |  | the build operation name |
| `buildId` | `string` |  | N |  | the build id |
| `operationName` | `string` |  | N |  | the operation name of the fuzzing |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | the sanitizers will be used for the project |
| `engine` | `abfuzz.Engine` |  | N |  | the fuzz engine name |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `sourcePackageId` | `string` |  | N |  | the source package id for the fuzzer |
| `sourcePackageCodeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `sourcePackageVersion` | `string` |  | N |  | 源代码的版本信息 |
| `sourcePackage` | `abfuzz.SourcePackage` |  | N |  | the source package for the fuzzer |
| `fuzzerPackage` | `abfuzz.FuzzerPackage` |  | N |  | the fuzzer package of the fuzzer |
| `coveragePackage` | `abfuzz.CoveragePackage` |  | N |  | the fuzzer package of the fuzzer |
| `executablePath` | `string` |  | N |  | Fuzzer's main executable path, relative to root. |
| `executableArguments` | `Array<string>` |  | N |  | the input arguments for the executable, it will be skipped when launcher_script is set. |
| `executableEnvironment` | `Map<string, string>` |  | N |  | the environment setting for the executable |
| `launcherScript` | `string` |  | N |  | Custom script that should be used to launch executable for this fuzzer.It will not try to launch the target if "@service-auto-start" set. |
| `timeout` | `integer` | `Int64` | N |  | Fuzzing timeout. |
| `testcaseTimeoutMillis` | `integer` | `Int64` | N |  | Testcase timeout. |
| `maxTestcases` | `integer` | `Int64` | N |  | Max testcases to generate for this fuzzer. |
| `additionalEnvironmentString` | `string` |  | N |  | Additional environment variables that need to be set for this fuzzer. |
| `statsColumns` | `string` |  | N |  | Column specification for stats. |
| `statsColumnDescriptions` | `string` |  | N |  | Helpful descriptions for the stats_columns. In a yaml format. |
| `builtin` | `boolean` |  | N |  | Whether this is a builtin fuzzer. |
| `autoStop` | `boolean` |  | N |  | Fuzzing automatic stop. |
| `autoHarness` | `boolean` |  | N |  | auto generate harness |
| `customMutatorPlugin` | `string` |  | N |  | custom mutator plugin for Fuzz Engine |
| `customAllowTargets` | `Array<string>` |  | N |  | custom file name & function name targets for instruments and coverage, like white list. |
| `customDenyTargets` | `Array<string>` |  | N |  | custom targets for instruments and coverage, like white list. |
| `context` | `abfuzz.ContextRequest` |  | N |  | context request |
| `dockerImage` | `string` |  | N |  | the specific docker image for the fuzzing |
| `corpusPath` | `string` |  | N |  | the special corpus path |
| `result` | `string` |  | N |  | Result from the last fuzzer run showing the number of testcases generated. |
| `resultUpdateTime` | `string` | `Timestamp` | N |  | Last result update timestamp. |
| `returnCode` | `integer` | `Int64` | N |  | Return code from last fuzzer run. |
| `consoleOutput` | `string` |  | N |  | Console output from last fuzzer run. |
| `sampleTestcase` | `abfuzz.StorageFile` |  | N |  | storage file for the sample testcase generated by the fuzzer. |
| `testcases` | `Array<abfuzz.Testcase>` |  | N |  | generated test cases |
| `coverages` | `Array<abfuzz.Coverage>` |  | N |  | 覆盖率信息 |
| `coverageUrl` | `string` |  | N |  | 覆盖率信息的url |
| `onlyFunctionTest` | `boolean` |  | N |  | 是否只进行功能测试 |
| `coverageMaps` | `Array<abfuzz.CoverageMap>` |  | N |  | 覆盖率图谱 |
| `fuzzStats` | `abfuzz.FuzzStats` |  | N |  | AFL相关的统计 |
| `inputPackage` | `string` | `Url` | N |  | 对coverage有提升的输入集合包 |
| `preprocessScript` | `string` |  | N |  | preprocess script ,process before aboroxy do reuqest |
| `durationSeconds` | `integer` | `Int64` | N |  | the duration of the fuzzing real running in seconds |
| `metadata` | `Map<string, mojo.core.Value>` |  | N |  | the meta information for the fuzzing |
| `createTime` | `string` | `Timestamp` | N |  | the created time of the fuzzing |
| `updateTime` | `string` | `Timestamp` | N |  | Last update time. |
| `corpus` | `mojo.core.Object` |  | N |  | Object type |


#### `abfuzz.HttpCorpus`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | Y |  |
| `staticValues` | `mojo.core.Object` |  | N |  | Object type |
| `fixups` | `Array<abfuzz.Fixup>` |  | N |  |
| `relations` | `Array<abfuzz.Relation>` |  | N |  |


#### `abfuzz.Impact`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `versionIndices` | `Array<string>` |  | N |  | The impacted version indices (including beta, stable and extended_stable). |
| `extendedStableVersion` | `string` |  | N |  | The impacted extended stable version. |
| `extendedStableVersionIndices` | `Array<string>` |  | N |  | The impacted extended stable version indices. |
| `extendedStableVersionLikely` | `boolean` |  | N |  | The impacted extended stable version is merely probable (not definite).See the comment on stable_version_likely. |
| `stableVersion` | `string` |  | N |  | The impacted stable version. |
| `stableVersionIndices` | `Array<string>` |  | N |  | The impacted stable version indices. |
| `stableVersionLikely` | `boolean` |  | N |  | The impacted stable version is merely probable (not definite). Becausefor a non-asan build, we don't have a stable/beta build. Therefore, wemake an intelligent guess on the version. |
| `betaVersion` | `string` |  | N |  | The impacted beta version. |
| `betaVersionIndices` | `Array<string>` |  | N |  | The impacted beta version indices. |
| `betaVersionLikely` | `boolean` |  | N |  | The impacted beta version is merely probable (not definite). See thecomment on stable_version_likely. |
| `headVersion` | `string` |  | N |  | The impacted 'head' version |
| `headVersionIndices` | `Array<string>` |  | N |  | The impacted head version indices. |
| `headVersionLikely` | `boolean` |  | N |  | The impacted head version is likely. |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `abfuzz.Relation`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.Repository`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `url` | `string` | `Url` | N |  | the git url for the source package |
| `branch` | `string` |  | N |  | the git branch for the source package |
| `tag` | `string` |  | N |  | the git tag for the source package |
| `commitTime` | `string` | `Timestamp` | N |  | the used commit time for the source package |
| `commitKey` | `string` |  | N |  | the used commit key for the source package, it is hash key for git |


#### `abfuzz.Reproduce`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `fuzzerName` | `string` |  | N |  | Fuzzer Name. |
| `projectId` | `string` |  | N |  | Project id |
| `projectName` | `string` |  | N |  | project name of the fuzzing |
| `sourcePackageId` | `string` |  | N |  | the source package id for the fuzzer |
| `buildOperationName` | `string` |  | N |  | the build operation name |
| `buildId` | `string` |  | N |  | the build id |
| `operationName` | `string` |  | N |  | the operation name of the fuzzing |
| `platform` | `string` | `Platform` | N |  | 指定的平台 |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `engine` | `abfuzz.Engine` |  | N |  | 指定的引擎 |
| `testcases` | `Array<abfuzz.Testcase>` |  | N |  | 指定需要验证的Testcase |
| `state` | `string` |  | N |  |
| `createTime` | `string` | `Timestamp` | N |  |  |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.Sanitizer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the sanitizer |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.SetupStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | 更新上下文时发起的请求，不要求包含在OpenAPI中 |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |
| `filter` | `Array<string>` |  | N |  |
| `order` | `string` |  | N |  |


#### `abfuzz.SourcePackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the uuid for the source package |
| `projectId` | `string` |  | N |  | the project id which including the source package |
| `projectName` | `string` |  | N |  | the project name which including the source package |
| `projectVersion` | `string` |  | N |  | the project version |
| `type` | `string` |  | N |  | the type of the source packagewhitebox, graybox, blackbox |
| `name` | `string` |  | N |  | the name of the module of the source package |
| `fuzzRepo` | `abfuzz.Repository` |  | N |  | the repository information for the fuzz code reposisity in the source package |
| `sourceRepo` | `abfuzz.Repository` |  | N |  | the repository information for the main code reposisity of custom in the source package |
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `fileSet` | `Array<abfuzz.StorageFile>` |  | N |  | the file set for the source package |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary or raw source code package. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |
| `customVersion` | `string` |  | N |  | more version info for the custom system |
| `codeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `createTime` | `string` | `Timestamp` | N |  | create time of the source code package |
| `updateTime` | `string` | `Timestamp` | N |  | update time of the source code package |


#### `abfuzz.StorageFile`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  | 文件在minio上的 地址 |
| `checksum` | `string` | `Checksum` | N |  | 文件的hash校验码 |
| `type` | `string` |  | N |  | 文件类型，比如： `corpus`, `harness`, `source`, `build-script`, `openapi`, `jar` |
| `group` | `string` |  | N |  | 文件属于某个组，可以是某个fuzzer |


#### `abfuzz.Task`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | 任务ID |
| `name` | `string` |  | N |  | 任务名称，对应不同的任务执行器，只能是以下之一：'fuzz', 'variant', 'minimize', 'regression', 'impact', 'blame', 'progression' |
| `projectId` | `string` |  | N |  | 任务所属项目ID |
| `projectName` | `string` |  | N |  | 任务所属项目名称 |
| `sourcePackageId` | `string` |  | N |  | 源码包ID |
| `buildOperationName` | `string` |  | N |  | 对应build操作的Operation name |
| `buildId` | `string` |  | N |  | 对应building的ID |
| `fuzzerName` | `string` |  | N |  | 任务所属模糊器（Fuzzer）名称 |
| `fuzzingId` | `string` |  | N |  | 任务所属Fuzzing ID，当任务对象是Project的时候，该值为空 |
| `operationName` | `string` |  | N |  | the operation name of the project, will be assigned by the longrunning.Operation id |
| `platform` | `string` | `Platform` | N |  | 指定的平台 |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `argument` | `mojo.core.Object` |  | N |  | 任务执行所需的参数 |
| `timeout` | `string` | `Duration` | N |  | 任务最长超时时间 |
| `isCommandOverride` | `boolean` |  | N |  | 命令能否被覆盖 |
| `highEnd` | `boolean` |  | N |  | 是否是高优先级任务 |
| `agentName` | `string` |  | N |  | 执行任务的agent name. |
| `state` | `string` |  | N |  | 任务的状态，只能是以下之一：created, running, stopped, finished, failed |
| `error` | `mojo.core.Error` |  | N |  | 任务失败后的错误信息 |
| `containers` | `Array<abfuzz.Container>` |  | N |  | 任务过程中产生的容器信息 |
| `createTime` | `string` | `Timestamp` | N |  | 任务的创建时间 |
| `updateTime` | `string` | `Timestamp` | N |  | 任务的更新时间 |


#### `abfuzz.TeardownStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.Testcase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | id for the testcase |
| `description` | `string` |  | N |  | description for the testcase |
| `keywords` | `Array<string>` |  | N |  | keywords is used for searching. |
| `securityFlag` | `boolean` |  | N |  | Is it a security bug ? |
| `securitySeverity` | `integer` | `Int64` | N |  | Security severity of the bug. |
| `oneTimeCrasherFlag` | `boolean` |  | N |  | Did the bug only reproduced once ? |
| `comments` | `string` |  | N |  | Any additional comments. |
| `projectId` | `string` |  | N |  | Project id associated with this test case. |
| `projectName` | `string` |  | N |  | Project name associated with this test case. |
| `sourcePackageId` | `string` |  | N |  | the source package id for the testcase |
| `fuzzerName` | `string` |  | N |  | Name of the fuzzer used to generate this testcase. |
| `fuzzerNameIndices` | `Array<string>` |  | N |  | Fuzzer name indices |
| `overriddenFuzzerName` | `string` |  | N |  | Overridden fuzzer name because actual fuzzer name can be different in manyscenarios (libfuzzer, afl, etc). |
| `fuzzingId` | `string` |  | N |  | the fuzzing which is the testcase generated from |
| `buildOperationName` | `string` |  | N |  | the build operation name of the testcase |
| `operationName` | `string` |  | N |  | the operation name of the testcase when fuzzing |
| `buildId` | `string` |  | N |  | the build id |
| `platform` | `string` | `Platform` | N |  | Platforms (e.g. windows, linux, android). |
| `language` | `string` |  | N |  | the program language for the fuzzing |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `otherPlatforms` | `Array<string>` |  | N |  | Platforms (e.g. windows, linux, android). |
| `otherLangVms` | `Array<abfuzz.LangVM>` |  | N |  | 指定的JDK，如果是聚合后的testcase，会包含多个jdk版本 |
| `engines` | `Array<abfuzz.Engine>` |  | N |  | engine names for the testcase |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | 指定的sanitizers |
| `fuzzedIds` | `Array<string>` |  | N |  | Blobstore keys for various things like original testcase, minimizedtestcase, etc. |
| `minimizedIds` | `Array<string>` |  | N |  |
| `minidumpIds` | `Array<string>` |  | N |  |
| `status` | `string` |  | N |  | Status of this testcase (pending, processed, unreproducible, etc). |
| `open` | `boolean` |  | N |  | Whether testcase is open. |
| `duplicateOf` | `integer` | `Int64` | N |  | Id of the testcase that this is marked as a duplicate of. |
| `groupId` | `integer` | `Int64` | N |  | Id for this testcase's associated group. |
| `groupBugInformation` | `integer` | `Int64` | N |  | Tracking issue tracker bug for this testcase group. |
| `isLeader` | `boolean` |  | N |  | Whether or not a testcase is the leader of its group.If the testcase is not in a group, it's the leader of a group of 1.The default is false because we prefer not to show crashes until we aresure. And group_task will correctly set the value within 30 minutes. |
| `bugInformation` | `string` |  | N |  | Tracking issue tracker bug. One bug number per line (future extension). |
| `isADuplicateFlag` | `boolean` |  | N |  | Whether or not a testcase is a duplicate of other testcase. |
| `hasBugFlag` | `boolean` |  | N |  | Whether testcase has a bug (either bug_information orgroup_bug_information). |
| `bugIndices` | `Array<string>` |  | N |  | Indices for bug_information and group_bug_information. |
| `triaged` | `boolean` |  | N |  | Boolean attribute indicating if cleanup triage needs to be done. |
| `additionalMetadata` | `mojo.core.Object` |  | N |  | Additional metadata stored as a JSON object. This should be used forproperties that are not commonly accessed and do not need to be indexed. |
| `crashType` | `string` |  | N |  | Crash on an invalid read/write. |
| `crashDescription` | `string` |  | N |  | Crash description for runtime error |
| `crashAddress` | `string` |  | N |  | Crashing address. |
| `crashState` | `string` |  | N |  | First x stack frames. |
| `crashStacktrace` | `string` |  | N |  | Complete stacktrace. |
| `lastTestedCrashStacktrace` | `string` |  | N |  | Last tested crash stacktrace using the latest revision. |
| `crashRevision` | `integer` | `Int64` | N |  | Revision that we discovered the crash in. |
| `crashSourceFileName` | `string` |  | N |  | The source file name, may be omitted if debug symbols are not available. |
| `crashSourceLine` | `integer` | `Int32` | N |  | The (1-based) source line number, may be omitted if debug symbols arenot available. |
| `crashFunctionName` | `string` |  | N |  | The function name, may be omitted if debug symbols are not available. |
| `symbolized` | `boolean` |  | N |  | Flag indicating whether or not the testcase has been symbolized. |
| `input` | `abfuzz.Testcase.Input` |  | N |  | The input for the testcase crashing |
| `targetServerLogPath` | `string` |  | N |  | The target server log path with java testcase |
| `absolutePath` | `string` |  | N |  | The file on the agent that generated the testcase. |
| `binaryFlag` | `boolean` |  | N |  | Is this a binary file? |
| `flakyStack` | `boolean` |  | N |  | Does the testcase crash stack vary b/w crashes ? |
| `httpFlag` | `boolean` |  | N |  | Do we need to test this testcase using an HTTP/HTTPS server? |
| `gestures` | `Array<string>` |  | N |  | Fake user interaction sequences like key clicks, mouse movements, etc. |
| `redzone` | `integer` | `Int64` | N |  | ASAN redzone size in bytes. |
| `disableUbsan` | `boolean` |  | N |  | Flag indicating if UBSan detection should be disabled. This is needed forcases when ASan and UBSan are bundled in the same build configurationand we need to disable UBSan in some runs to find the potentially moreinteresting ASan bugs. |
| `timeoutMultiplier` | `number` | `Float64` | N |  | Adjusts timeout based on multiplier value. |
| `regression` | `string` |  | N |  | Regression range. |
| `fixed` | `string` |  | N |  | Revisions where this issue has been fixed. |
| `impactIndices` | `Array<string>` |  | N |  | Impact indices for searching. |
| `impact` | `abfuzz.Impact` |  | N |  | 用例影响源代码的版本信息 |
| `isImpactSetFlag` | `boolean` |  | N |  | Whether or not impact task has been run on this testcase. |
| `uploaderEmail` | `string` |  | N |  | Uploader email address. |
| `createTime` | `string` | `Timestamp` | N |  | Timestamp. |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.Testcase.Input`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  | binaray, file, json |
| `content` | `mojo.core.Value` |  | N |  | the input content |


#### `mojo.core.Any`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `@type` | `string` |  | Y |  |


#### `mojo.core.Error`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` | `ErrorCode` | N |  | The error code |
| `message` | `string` |  | N |  | A developer-facing error message, which should be in English. Anyuser-facing error message should be localized and sent in the[google.rpc.Status.details][google.rpc.Status.details] field, or localized by the client. |
| `details` | `Array<mojo.core.Any>` |  | N |  | A list of messages that carry the error details.  There is a common set ofmessage types for APIs to use. |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


#### `mojo.http.Request`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `method` | `string` |  | N |  | method specifies the HTTP method (GET, POST, PUT, etc.).For client requests, an empty string means GET. |
| `url` | `string` | `Url` | N |  | url specifies either the URI being requested (for serverrequests) or the URL to access (for client requests). |
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Response`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `status` | `mojo.http.Status` |  | N |  |  |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Status`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `integer` | `Int32` | N |  |
| `reason` | `string` |  | N |  |


#### `mojo.http.Version`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `major` | `integer` | `Int32` | N |  |
| `minor` | `integer` | `Int32` | N |  |


## 获取项目在fuzzing过程中的status列表信息

### 请求路径
```http
GET /abfuzz/v1/projects/{project_id}/statuses
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  | 指定项目ID |


#### Query 参数
| 参数名 | 参数类型 | 格式类型 | 是否必须 | 默认值 | 说明 |
|---|---|---|---|---|---|
| `source_package_id` | `string` |  | 是 |  | 指定源码包ID，指定特定的版本及分支 |
| `operation_name` | `string` |  | 是 |  | 每次启动执行时返回的operation name |
| `platform` | `string` | `Platform` | 否 |  | 指定某个特定的平台 |
| `language` | `string` |  | 否 |  | 指定某个特定的语言 |
| `lang_vm` | `abfuzz.LangVM` |  | 否 |  | 指定某个特定的语言虚拟机 |
| `fuzzer` | `string` |  | 否 |  | 特别指定fuzzer |
| `state` | `string` |  | 否 |  | 指定某个state，支持 build 以及 build.created |
| `unique` | `boolean` |  | 否 |  | 是否对相同的状态进行去重，一般来说fuzzing.running会存在多个相同的状态 |
| `page_size` | `integer` | `Int32` | 否 |  |  |
| `page_token` | `string` |  | 否 |  |  |
| `skip` | `integer` | `Int32` | 否 |  |  |


### 返回值

#### 返回对象
| type | description |
|---|---|
| `Array<abfuzz.ProjectStatus>` |  |


#### `abfuzz.Build`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `projectId` | `string` |  | N |  |
| `sourcePackageId` | `string` |  | N |  |
| `operationName` | `string` |  | N |  |
| `platform` | `string` | `Platform` | N |  | 指定的平台 |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的语言虚拟机，JDK for java |
| `engine` | `abfuzz.Engine` |  | N |  | 指定的引擎 |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | 指定的sanitizer |
| `fuzzers` | `Array<abfuzz.Fuzzer>` |  | N |  | 指定输出的fuzzer |
| `state` | `string` |  | N |  | finished, failed |
| `error` | `mojo.core.Error` |  | N |  | build失败后的错误信息 |
| `log` | `string` |  | N |  |
| `logType` | `string` |  | N |  | text, url, binary (std base64) |
| `createTime` | `string` | `Timestamp` | N |  |  |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.CaseStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `disable` | `array` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.Container`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the id of the container |
| `name` | `string` |  | N |  | the name of the container |


#### `abfuzz.ContextCase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `steps` | `Array<abfuzz.CaseStep>` |  | N |  |


#### `abfuzz.ContextRequest`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `setup` | `abfuzz.ContextSetup` |  | N |  | 全局设置，在cases之前执行 |
| `cases` | `Array<abfuzz.ContextCase>` |  | N |  | OpenAPI里的请求(用path和method来对应)，包含在cases里的优先执行，没有包含在cases里的请求，每个请求自动生成一个case，在cases之后依次自行 |
| `teardown` | `abfuzz.ContextTeardown` |  | N |  | 在cases之后执行的请求 |


#### `abfuzz.ContextSetup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.SetupStep>` |  | N |  |


#### `abfuzz.ContextTeardown`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.TeardownStep>` |  | N |  |


#### `abfuzz.CorpusConfig`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `includings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `excludings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `httpBaseUrl` | `string` | `Url` | N |  | URl type<br>Uniform Resource Identifier (URI) is a string of characters used to identify a resource.<br>A Uniform Resource Name (URN) may be compared to a person's name,while a Uniform Resource Locator (URL) may be compared to their street address.In other words, a URN identifies an item and a URL provides a method for finding it.<br> |
| `httpMappingPort` | `integer` | `Int32` | N |  |
| `httpMappingHost` | `string` |  | N |  |
| `httpStaticValues` | `mojo.core.Object` |  | N |  | Object type |
| `httpCorpuses` | `Array<abfuzz.HttpCorpus>` |  | N |  |
| `combinedApiPath` | `string` |  | N |  |
| `asyncApi` | `boolean` |  | N |  |


#### `abfuzz.Coverage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `element` | `string` |  | N |  | 目标的路径，可以是包、文件、类、函数 |
| `name` | `string` |  | N |  | 目标的名称，不包含路径信息，如果是函数，只是函数名；如果是类，只是类名 |
| `kind` | `string` |  | N |  | 目标的类型: package, file, class, function |
| `hash` | `integer` | `Int64` | N |  | 指定目标的hash值 |
| `lines` | `abfuzz.Coverage.Count` |  | N |  | 行覆盖率，只要本行有一条指令被执行，则本行则被标记为被执行。 |
| `functions` | `abfuzz.Coverage.Count` |  | N |  | 方法覆盖率，任何非抽象的方法，只要有一条指令被执行，则该方法就会被计为被执行。 |
| `classes` | `abfuzz.Coverage.Count` |  | N |  | 类覆盖率，所有类，包括接口，只要其中有一个方法被执行，则标记为被执行。注意：构造函数和静态初始化块也算作方法。 |
| `instructions` | `abfuzz.Coverage.Count` |  | N |  | Jacoco计算的最小单位就是字节码指令。指令覆盖率表明了在所有的指令中，哪些被指令过以及哪些没有被执行。这项指数完全独立于源码格式并且在任何情况下有效，不需要类文件的调试信息。 |
| `branches` | `abfuzz.Coverage.Count` |  | N |  | 对所有的if和switch指令计算了分支覆盖率。这项指标会统计所有的分支数量，并同时支出哪些分支被执行，哪些分支没有被执行。这项指标也在任何情况都有效。异常处理不考虑在分支范围内。 |
| `cxty` | `abfuzz.Coverage.Count` |  | N |  | Cyclomatic Complexity): 圈复杂度会为每一个非抽象方法计算圈复杂度，并为类，包以及组（groups）计算复杂度。圈复杂度简单地说就是为了覆盖所有路径，所需要执行单元测试数量，圈复杂度大说明程序代码可能质量低且难于测试和维护。 |
| `edges` | `abfuzz.Coverage.Count` |  | N |  | 边覆盖 |
| `linesInfo` | `abfuzz.Coverage.LinesInfo` |  | N |  | 在源文件内详细的行覆盖信息，在kind为file时才会有 |
| `branchInfoes` | `Array<abfuzz.Coverage.BranchInfo>` |  | N |  | 在源文件内详细的分支覆盖信息，在kind为file时才会有 |
| `functionInfoes` | `Array<abfuzz.Coverage.FunctionInfo>` |  | N |  | 在源文件内详细的函数覆盖信息，在kind为file时才会有 |
| `classInfoes` | `Array<abfuzz.Coverage.ClassInfo>` |  | N |  | 在源文件内详细的类覆盖信息，在kind为file时才会有 |


#### `abfuzz.Coverage.BranchInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `startLine` | `integer` | `Int32` | N |  | 分支起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 分支起始列 |
| `destLine` | `integer` | `Int32` | N |  | 分支跳转至的行 |
| `attribute` | `integer` | `Int32` | N |  | 分支属性 |
| `executedCount` | `integer` | `Int32` | N |  | 分支的执行数 |


#### `abfuzz.Coverage.ClassInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | 类名称 |
| `startLine` | `integer` | `Int32` | N |  | 类的起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 类的起始列 |
| `endLine` | `integer` | `Int32` | N |  | 类结束行 |
| `endColumn` | `integer` | `Int32` | N |  | 类结束行 |
| `attribute` | `integer` | `Int32` | N |  | 类属性 |
| `executedCount` | `integer` | `Int32` | N |  | 类执行数 |


#### `abfuzz.Coverage.Count`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `hit` | `integer` | `Int64` | N |  | 覆盖到的元素中的计数 |
| `total` | `integer` | `Int64` | N |  | 元素中的总计数值 |


#### `abfuzz.Coverage.FunctionInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | 函数名称 |
| `startLine` | `integer` | `Int32` | N |  | 函数的起始行 |
| `startColumn` | `integer` | `Int32` | N |  | 函数的起始列 |
| `endLine` | `integer` | `Int32` | N |  | 函数结束行 |
| `endColumn` | `integer` | `Int32` | N |  | 函数结束行 |
| `attribute` | `integer` | `Int32` | N |  | 函数属性 |
| `executedCount` | `integer` | `Int32` | N |  | 函数执行数 |


#### `abfuzz.Coverage.LinesInfo`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `start` | `integer` | `Int32` | N |  | 起始行 |
| `attributes` | `Array<integer>` |  | N |  | 每行的属性值 |
| `executedCounts` | `Array<integer>` |  | N |  | 每行的执行数 |


#### `abfuzz.CoverageMap`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `node` | `string` |  | N |  |
| `childNode` | `string` |  | N |  |
| `uri` | `string` |  | N |  |


#### `abfuzz.CoveragePackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |


#### `abfuzz.CpuStat`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `number` | `integer` | `Int32` | N |  | CPU编号 |
| `state` | `string` |  | N |  | CPU的状态 AVAILABLE， CAUTION |


#### `abfuzz.EndPoint`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` |  | N |  |
| `http` | `abfuzz.EndPoint.Http` |  | N |  |  |
| `priority` | `integer` | `Int32` | N |  |


#### `abfuzz.EndPoint.Http`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `path` | `string` |  | N |  |
| `methods` | `Array<string>` |  | N |  |


#### `abfuzz.Engine`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the engine |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.Entity`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `stepType` | `string` |  | N |  |
| `interfaces` | `Array<abfuzz.Entity.Interface>` |  | N |  |


#### `abfuzz.Entity.Interface`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `type` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | N |  |
| `pathParams` | `Map<string, string>` |  | N |  |
| `queryParams` | `Map<string, string>` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `response` | `mojo.http.Response` |  | N |  | Response represents the response from an HTTP request. |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |


#### `abfuzz.Fixup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.FuzzStats`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `projectId` | `string` |  | N |  |
| `sourcePackageId` | `string` |  | N |  |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `buildId` | `string` |  | N |  | the build id |
| `operationName` | `string` |  | N |  | the operation name of the fuzzing |
| `fuzzingId` | `string` |  | N |  |
| `fuzzerName` | `string` |  | N |  |
| `cyclesDone` | `integer` | `Int32` | N |  |
| `execsDone` | `integer` | `Int32` | N |  | 总共完成的执行数 |
| `execsSpeed` | `number` | `Float32` | N |  | 每秒的执行次数 |
| `cpuStats` | `Array<abfuzz.CpuStat>` |  | N |  | CPU的状态 |
| `pathsTotal` | `integer` | `Int32` | N |  | 执行边的总数 |
| `pathsFavored` | `integer` | `Int32` | N |  |
| `pathsFound` | `integer` | `Int32` | N |  | 发现的边的总数 |
| `pathsImported` | `integer` | `Int32` | N |  |
| `pathsMaxDepth` | `integer` | `Int32` | N |  |
| `pathsPendingFavored` | `integer` | `Int32` | N |  |
| `pathsPendingTotal` | `integer` | `Int32` | N |  |
| `pathsVariable` | `integer` | `Int32` | N |  |
| `pathsStability` | `number` | `Float32` | N |  |
| `bitmapCoverage` | `number` | `Float32` | N |  | bitmap覆盖率 |
| `uniqueCrashes` | `integer` | `Int32` | N |  | 独立的crash的总数 |
| `uniqueHangs` | `integer` | `Int32` | N |  | 独立的hang的总数 |
| `lastNewPath` | `string` | `Duration` | N |  | 最近一次新path的持续时间 |
| `lastUniqueCrash` | `string` | `Duration` | N |  | 最近一次发现独立crash的持续时间 |
| `lastUniqueHang` | `string` | `Duration` | N |  | 最近一次发现独立hang的持续时间 |
| `execsSinceCrash` | `integer` | `Int32` | N |  | 从上次unique的crash的执行数 |
| `execTimeout` | `integer` | `Int32` | N |  |
| `createTime` | `string` | `Timestamp` | N |  |  |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.Fuzzer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | Y |  | the fuzzer name |
| `group` | `string` |  | N |  | the group id for the fuzzers |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `fuzzerPackage` | `abfuzz.FuzzerPackage` |  | N |  | the fuzzer package of the fuzzer |
| `executablePath` | `string` |  | N |  | Fuzzer's main executable path, relative to root. |
| `executableArguments` | `Array<string>` |  | N |  | the input arguments for the executable, it will be skipped when launcher_script is set. |
| `executableEnvironment` | `Map<string, string>` |  | N |  | the environment setting for the executable |
| `launcherScript` | `string` |  | N |  | Custom script that should be used to launch executable for this fuzzer.It will not try to launch the target if "@service-auto-start" set. |
| `timeout` | `integer` | `Int64` | N |  | Fuzzing timeout. |
| `testcaseTimeoutMillis` | `integer` | `Int64` | N |  | Testcase timeout. |
| `autoStop` | `boolean` |  | N |  | Fuzzing automatic stop. |
| `autoHarness` | `boolean` |  | N |  | auto generate harness |
| `customMutatorPlugin` | `string` |  | N |  | custom mutator plugin for Fuzz Engine |
| `customAllowTargets` | `Array<string>` |  | N |  | custom file name & function name targets for instruments and coverage, like white list. |
| `customDenyTargets` | `Array<string>` |  | N |  | custom targets for instruments and coverage, like white list. |
| `context` | `abfuzz.ContextRequest` |  | N |  | context request |
| `corpusPath` | `string` |  | N |  | the special corpus path |
| `preprocessScript` | `string` |  | N |  | the preprocess script info |
| `onlyFunctionalTest` | `boolean` |  | N |  | is only fuzzing user corpus |
| `autoHarnessCommand` | `string` |  | N |  | execute the command before run auto harness generation |
| `autoHarnessScript` | `string` |  | N |  | execute the script before run auto harness generation if the command is empty |
| `autoHarnessArguments` | `Array<string>` |  | N |  | the arguments when to run the harness generation, usually for including path setting |
| `autoHarnessBlacklist` | `Array<string>` |  | N |  | the blacklist files to run the harness generation, these files will be skipped. |
| `autoHarnessWebServer` | `boolean` |  | N |  | determine if the java auto harness server is a web server |
| `metadata` | `Map<string, mojo.core.Value>` |  | N |  | the meta information for the fuzzing |
| `corpus` | `abfuzz.CorpusConfig` |  | N |  |  |
| `entities` | `Array<abfuzz.Entity>` |  | N |  |


#### `abfuzz.FuzzerPackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `type` | `string` |  | N |  | the type of the source package, oneof the whitebox, greybox, blackbox |
| `filename` | `string` |  | N |  | Filename for the custom binary. |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |


#### `abfuzz.Fuzzing`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | Additionally allows '.' and '@' over NAME_CHECK_REGEX.VALID_NAME_REGEX: re.compile(r'^[a-zA-Z0-9_@.-]+$') |
| `name` | `string` |  | N |  | Fuzzer Name. |
| `group` | `string` |  | N |  | the group id for the fuzzers |
| `projectId` | `string` |  | N |  | Project id |
| `projectName` | `string` |  | N |  | project name of the fuzzing |
| `state` | `string` |  | N |  | the state of the Fuzzing |
| `buildOperationName` | `string` |  | N |  | the build operation name |
| `buildId` | `string` |  | N |  | the build id |
| `operationName` | `string` |  | N |  | the operation name of the fuzzing |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | the sanitizers will be used for the project |
| `engine` | `abfuzz.Engine` |  | N |  | the fuzz engine name |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `sourcePackageId` | `string` |  | N |  | the source package id for the fuzzer |
| `sourcePackageCodeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `sourcePackageVersion` | `string` |  | N |  | 源代码的版本信息 |
| `sourcePackage` | `abfuzz.SourcePackage` |  | N |  | the source package for the fuzzer |
| `fuzzerPackage` | `abfuzz.FuzzerPackage` |  | N |  | the fuzzer package of the fuzzer |
| `coveragePackage` | `abfuzz.CoveragePackage` |  | N |  | the fuzzer package of the fuzzer |
| `executablePath` | `string` |  | N |  | Fuzzer's main executable path, relative to root. |
| `executableArguments` | `Array<string>` |  | N |  | the input arguments for the executable, it will be skipped when launcher_script is set. |
| `executableEnvironment` | `Map<string, string>` |  | N |  | the environment setting for the executable |
| `launcherScript` | `string` |  | N |  | Custom script that should be used to launch executable for this fuzzer.It will not try to launch the target if "@service-auto-start" set. |
| `timeout` | `integer` | `Int64` | N |  | Fuzzing timeout. |
| `testcaseTimeoutMillis` | `integer` | `Int64` | N |  | Testcase timeout. |
| `maxTestcases` | `integer` | `Int64` | N |  | Max testcases to generate for this fuzzer. |
| `additionalEnvironmentString` | `string` |  | N |  | Additional environment variables that need to be set for this fuzzer. |
| `statsColumns` | `string` |  | N |  | Column specification for stats. |
| `statsColumnDescriptions` | `string` |  | N |  | Helpful descriptions for the stats_columns. In a yaml format. |
| `builtin` | `boolean` |  | N |  | Whether this is a builtin fuzzer. |
| `autoStop` | `boolean` |  | N |  | Fuzzing automatic stop. |
| `autoHarness` | `boolean` |  | N |  | auto generate harness |
| `customMutatorPlugin` | `string` |  | N |  | custom mutator plugin for Fuzz Engine |
| `customAllowTargets` | `Array<string>` |  | N |  | custom file name & function name targets for instruments and coverage, like white list. |
| `customDenyTargets` | `Array<string>` |  | N |  | custom targets for instruments and coverage, like white list. |
| `context` | `abfuzz.ContextRequest` |  | N |  | context request |
| `dockerImage` | `string` |  | N |  | the specific docker image for the fuzzing |
| `corpusPath` | `string` |  | N |  | the special corpus path |
| `result` | `string` |  | N |  | Result from the last fuzzer run showing the number of testcases generated. |
| `resultUpdateTime` | `string` | `Timestamp` | N |  | Last result update timestamp. |
| `returnCode` | `integer` | `Int64` | N |  | Return code from last fuzzer run. |
| `consoleOutput` | `string` |  | N |  | Console output from last fuzzer run. |
| `sampleTestcase` | `abfuzz.StorageFile` |  | N |  | storage file for the sample testcase generated by the fuzzer. |
| `testcases` | `Array<abfuzz.Testcase>` |  | N |  | generated test cases |
| `coverages` | `Array<abfuzz.Coverage>` |  | N |  | 覆盖率信息 |
| `coverageUrl` | `string` |  | N |  | 覆盖率信息的url |
| `onlyFunctionTest` | `boolean` |  | N |  | 是否只进行功能测试 |
| `coverageMaps` | `Array<abfuzz.CoverageMap>` |  | N |  | 覆盖率图谱 |
| `fuzzStats` | `abfuzz.FuzzStats` |  | N |  | AFL相关的统计 |
| `inputPackage` | `string` | `Url` | N |  | 对coverage有提升的输入集合包 |
| `preprocessScript` | `string` |  | N |  | preprocess script ,process before aboroxy do reuqest |
| `durationSeconds` | `integer` | `Int64` | N |  | the duration of the fuzzing real running in seconds |
| `metadata` | `Map<string, mojo.core.Value>` |  | N |  | the meta information for the fuzzing |
| `createTime` | `string` | `Timestamp` | N |  | the created time of the fuzzing |
| `updateTime` | `string` | `Timestamp` | N |  | Last update time. |
| `corpus` | `mojo.core.Object` |  | N |  | Object type |


#### `abfuzz.HttpCorpus`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | Y |  |
| `staticValues` | `mojo.core.Object` |  | N |  | Object type |
| `fixups` | `Array<abfuzz.Fixup>` |  | N |  |
| `relations` | `Array<abfuzz.Relation>` |  | N |  |


#### `abfuzz.Impact`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `versionIndices` | `Array<string>` |  | N |  | The impacted version indices (including beta, stable and extended_stable). |
| `extendedStableVersion` | `string` |  | N |  | The impacted extended stable version. |
| `extendedStableVersionIndices` | `Array<string>` |  | N |  | The impacted extended stable version indices. |
| `extendedStableVersionLikely` | `boolean` |  | N |  | The impacted extended stable version is merely probable (not definite).See the comment on stable_version_likely. |
| `stableVersion` | `string` |  | N |  | The impacted stable version. |
| `stableVersionIndices` | `Array<string>` |  | N |  | The impacted stable version indices. |
| `stableVersionLikely` | `boolean` |  | N |  | The impacted stable version is merely probable (not definite). Becausefor a non-asan build, we don't have a stable/beta build. Therefore, wemake an intelligent guess on the version. |
| `betaVersion` | `string` |  | N |  | The impacted beta version. |
| `betaVersionIndices` | `Array<string>` |  | N |  | The impacted beta version indices. |
| `betaVersionLikely` | `boolean` |  | N |  | The impacted beta version is merely probable (not definite). See thecomment on stable_version_likely. |
| `headVersion` | `string` |  | N |  | The impacted 'head' version |
| `headVersionIndices` | `Array<string>` |  | N |  | The impacted head version indices. |
| `headVersionLikely` | `boolean` |  | N |  | The impacted head version is likely. |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `abfuzz.ProjectStatus`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | status id |
| `state` | `string` |  | N |  | state of the project, "build.created", "build.running", "fuzz.created", "reproduce" |
| `error` | `mojo.core.Error` |  | N |  | @lang('zh-CN') |
| `projectId` | `string` |  | N |  | project id |
| `projectName` | `string` |  | N |  | project name, normally the same with the costom's main source repo |
| `sourcePackageId` | `string` |  | N |  | project source package |
| `operationName` | `string` |  | N |  | the operation name of the project status |
| `buildOperationName` | `string` |  | N |  | the build operation name of the project |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `fuzzerName` | `string` |  | N |  | 所有的信息是否在同一个指定的fuzzer之下 |
| `tasks` | `Array<abfuzz.Task>` |  | N |  | the running tasks for process the project |
| `builds` | `Array<abfuzz.Build>` |  | N |  | the build result of the project |
| `reproduces` | `Array<abfuzz.Reproduce>` |  | N |  | the reproduces result of the project |
| `fuzzings` | `Array<abfuzz.Fuzzing>` |  | N |  | the fuzzings' status for the project |
| `createTime` | `string` | `Timestamp` | N |  | the create time of the project |
| `updateTime` | `string` | `Timestamp` | N |  | the update time of the project |


#### `abfuzz.Relation`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.Repository`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `url` | `string` | `Url` | N |  | the git url for the source package |
| `branch` | `string` |  | N |  | the git branch for the source package |
| `tag` | `string` |  | N |  | the git tag for the source package |
| `commitTime` | `string` | `Timestamp` | N |  | the used commit time for the source package |
| `commitKey` | `string` |  | N |  | the used commit key for the source package, it is hash key for git |


#### `abfuzz.Reproduce`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `fuzzerName` | `string` |  | N |  | Fuzzer Name. |
| `projectId` | `string` |  | N |  | Project id |
| `projectName` | `string` |  | N |  | project name of the fuzzing |
| `sourcePackageId` | `string` |  | N |  | the source package id for the fuzzer |
| `buildOperationName` | `string` |  | N |  | the build operation name |
| `buildId` | `string` |  | N |  | the build id |
| `operationName` | `string` |  | N |  | the operation name of the fuzzing |
| `platform` | `string` | `Platform` | N |  | 指定的平台 |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `engine` | `abfuzz.Engine` |  | N |  | 指定的引擎 |
| `testcases` | `Array<abfuzz.Testcase>` |  | N |  | 指定需要验证的Testcase |
| `state` | `string` |  | N |  |
| `createTime` | `string` | `Timestamp` | N |  |  |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.Sanitizer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the sanitizer |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.SetupStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | 更新上下文时发起的请求，不要求包含在OpenAPI中 |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |
| `filter` | `Array<string>` |  | N |  |
| `order` | `string` |  | N |  |


#### `abfuzz.SourcePackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the uuid for the source package |
| `projectId` | `string` |  | N |  | the project id which including the source package |
| `projectName` | `string` |  | N |  | the project name which including the source package |
| `projectVersion` | `string` |  | N |  | the project version |
| `type` | `string` |  | N |  | the type of the source packagewhitebox, graybox, blackbox |
| `name` | `string` |  | N |  | the name of the module of the source package |
| `fuzzRepo` | `abfuzz.Repository` |  | N |  | the repository information for the fuzz code reposisity in the source package |
| `sourceRepo` | `abfuzz.Repository` |  | N |  | the repository information for the main code reposisity of custom in the source package |
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `fileSet` | `Array<abfuzz.StorageFile>` |  | N |  | the file set for the source package |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary or raw source code package. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |
| `customVersion` | `string` |  | N |  | more version info for the custom system |
| `codeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `createTime` | `string` | `Timestamp` | N |  | create time of the source code package |
| `updateTime` | `string` | `Timestamp` | N |  | update time of the source code package |


#### `abfuzz.StorageFile`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  | 文件在minio上的 地址 |
| `checksum` | `string` | `Checksum` | N |  | 文件的hash校验码 |
| `type` | `string` |  | N |  | 文件类型，比如： `corpus`, `harness`, `source`, `build-script`, `openapi`, `jar` |
| `group` | `string` |  | N |  | 文件属于某个组，可以是某个fuzzer |


#### `abfuzz.Task`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | 任务ID |
| `name` | `string` |  | N |  | 任务名称，对应不同的任务执行器，只能是以下之一：'fuzz', 'variant', 'minimize', 'regression', 'impact', 'blame', 'progression' |
| `projectId` | `string` |  | N |  | 任务所属项目ID |
| `projectName` | `string` |  | N |  | 任务所属项目名称 |
| `sourcePackageId` | `string` |  | N |  | 源码包ID |
| `buildOperationName` | `string` |  | N |  | 对应build操作的Operation name |
| `buildId` | `string` |  | N |  | 对应building的ID |
| `fuzzerName` | `string` |  | N |  | 任务所属模糊器（Fuzzer）名称 |
| `fuzzingId` | `string` |  | N |  | 任务所属Fuzzing ID，当任务对象是Project的时候，该值为空 |
| `operationName` | `string` |  | N |  | the operation name of the project, will be assigned by the longrunning.Operation id |
| `platform` | `string` | `Platform` | N |  | 指定的平台 |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `argument` | `mojo.core.Object` |  | N |  | 任务执行所需的参数 |
| `timeout` | `string` | `Duration` | N |  | 任务最长超时时间 |
| `isCommandOverride` | `boolean` |  | N |  | 命令能否被覆盖 |
| `highEnd` | `boolean` |  | N |  | 是否是高优先级任务 |
| `agentName` | `string` |  | N |  | 执行任务的agent name. |
| `state` | `string` |  | N |  | 任务的状态，只能是以下之一：created, running, stopped, finished, failed |
| `error` | `mojo.core.Error` |  | N |  | 任务失败后的错误信息 |
| `containers` | `Array<abfuzz.Container>` |  | N |  | 任务过程中产生的容器信息 |
| `createTime` | `string` | `Timestamp` | N |  | 任务的创建时间 |
| `updateTime` | `string` | `Timestamp` | N |  | 任务的更新时间 |


#### `abfuzz.TeardownStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.Testcase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | id for the testcase |
| `description` | `string` |  | N |  | description for the testcase |
| `keywords` | `Array<string>` |  | N |  | keywords is used for searching. |
| `securityFlag` | `boolean` |  | N |  | Is it a security bug ? |
| `securitySeverity` | `integer` | `Int64` | N |  | Security severity of the bug. |
| `oneTimeCrasherFlag` | `boolean` |  | N |  | Did the bug only reproduced once ? |
| `comments` | `string` |  | N |  | Any additional comments. |
| `projectId` | `string` |  | N |  | Project id associated with this test case. |
| `projectName` | `string` |  | N |  | Project name associated with this test case. |
| `sourcePackageId` | `string` |  | N |  | the source package id for the testcase |
| `fuzzerName` | `string` |  | N |  | Name of the fuzzer used to generate this testcase. |
| `fuzzerNameIndices` | `Array<string>` |  | N |  | Fuzzer name indices |
| `overriddenFuzzerName` | `string` |  | N |  | Overridden fuzzer name because actual fuzzer name can be different in manyscenarios (libfuzzer, afl, etc). |
| `fuzzingId` | `string` |  | N |  | the fuzzing which is the testcase generated from |
| `buildOperationName` | `string` |  | N |  | the build operation name of the testcase |
| `operationName` | `string` |  | N |  | the operation name of the testcase when fuzzing |
| `buildId` | `string` |  | N |  | the build id |
| `platform` | `string` | `Platform` | N |  | Platforms (e.g. windows, linux, android). |
| `language` | `string` |  | N |  | the program language for the fuzzing |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `otherPlatforms` | `Array<string>` |  | N |  | Platforms (e.g. windows, linux, android). |
| `otherLangVms` | `Array<abfuzz.LangVM>` |  | N |  | 指定的JDK，如果是聚合后的testcase，会包含多个jdk版本 |
| `engines` | `Array<abfuzz.Engine>` |  | N |  | engine names for the testcase |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | 指定的sanitizers |
| `fuzzedIds` | `Array<string>` |  | N |  | Blobstore keys for various things like original testcase, minimizedtestcase, etc. |
| `minimizedIds` | `Array<string>` |  | N |  |
| `minidumpIds` | `Array<string>` |  | N |  |
| `status` | `string` |  | N |  | Status of this testcase (pending, processed, unreproducible, etc). |
| `open` | `boolean` |  | N |  | Whether testcase is open. |
| `duplicateOf` | `integer` | `Int64` | N |  | Id of the testcase that this is marked as a duplicate of. |
| `groupId` | `integer` | `Int64` | N |  | Id for this testcase's associated group. |
| `groupBugInformation` | `integer` | `Int64` | N |  | Tracking issue tracker bug for this testcase group. |
| `isLeader` | `boolean` |  | N |  | Whether or not a testcase is the leader of its group.If the testcase is not in a group, it's the leader of a group of 1.The default is false because we prefer not to show crashes until we aresure. And group_task will correctly set the value within 30 minutes. |
| `bugInformation` | `string` |  | N |  | Tracking issue tracker bug. One bug number per line (future extension). |
| `isADuplicateFlag` | `boolean` |  | N |  | Whether or not a testcase is a duplicate of other testcase. |
| `hasBugFlag` | `boolean` |  | N |  | Whether testcase has a bug (either bug_information orgroup_bug_information). |
| `bugIndices` | `Array<string>` |  | N |  | Indices for bug_information and group_bug_information. |
| `triaged` | `boolean` |  | N |  | Boolean attribute indicating if cleanup triage needs to be done. |
| `additionalMetadata` | `mojo.core.Object` |  | N |  | Additional metadata stored as a JSON object. This should be used forproperties that are not commonly accessed and do not need to be indexed. |
| `crashType` | `string` |  | N |  | Crash on an invalid read/write. |
| `crashDescription` | `string` |  | N |  | Crash description for runtime error |
| `crashAddress` | `string` |  | N |  | Crashing address. |
| `crashState` | `string` |  | N |  | First x stack frames. |
| `crashStacktrace` | `string` |  | N |  | Complete stacktrace. |
| `lastTestedCrashStacktrace` | `string` |  | N |  | Last tested crash stacktrace using the latest revision. |
| `crashRevision` | `integer` | `Int64` | N |  | Revision that we discovered the crash in. |
| `crashSourceFileName` | `string` |  | N |  | The source file name, may be omitted if debug symbols are not available. |
| `crashSourceLine` | `integer` | `Int32` | N |  | The (1-based) source line number, may be omitted if debug symbols arenot available. |
| `crashFunctionName` | `string` |  | N |  | The function name, may be omitted if debug symbols are not available. |
| `symbolized` | `boolean` |  | N |  | Flag indicating whether or not the testcase has been symbolized. |
| `input` | `abfuzz.Testcase.Input` |  | N |  | The input for the testcase crashing |
| `targetServerLogPath` | `string` |  | N |  | The target server log path with java testcase |
| `absolutePath` | `string` |  | N |  | The file on the agent that generated the testcase. |
| `binaryFlag` | `boolean` |  | N |  | Is this a binary file? |
| `flakyStack` | `boolean` |  | N |  | Does the testcase crash stack vary b/w crashes ? |
| `httpFlag` | `boolean` |  | N |  | Do we need to test this testcase using an HTTP/HTTPS server? |
| `gestures` | `Array<string>` |  | N |  | Fake user interaction sequences like key clicks, mouse movements, etc. |
| `redzone` | `integer` | `Int64` | N |  | ASAN redzone size in bytes. |
| `disableUbsan` | `boolean` |  | N |  | Flag indicating if UBSan detection should be disabled. This is needed forcases when ASan and UBSan are bundled in the same build configurationand we need to disable UBSan in some runs to find the potentially moreinteresting ASan bugs. |
| `timeoutMultiplier` | `number` | `Float64` | N |  | Adjusts timeout based on multiplier value. |
| `regression` | `string` |  | N |  | Regression range. |
| `fixed` | `string` |  | N |  | Revisions where this issue has been fixed. |
| `impactIndices` | `Array<string>` |  | N |  | Impact indices for searching. |
| `impact` | `abfuzz.Impact` |  | N |  | 用例影响源代码的版本信息 |
| `isImpactSetFlag` | `boolean` |  | N |  | Whether or not impact task has been run on this testcase. |
| `uploaderEmail` | `string` |  | N |  | Uploader email address. |
| `createTime` | `string` | `Timestamp` | N |  | Timestamp. |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.Testcase.Input`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  | binaray, file, json |
| `content` | `mojo.core.Value` |  | N |  | the input content |


#### `mojo.core.Any`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `@type` | `string` |  | Y |  |


#### `mojo.core.Error`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` | `ErrorCode` | N |  | The error code |
| `message` | `string` |  | N |  | A developer-facing error message, which should be in English. Anyuser-facing error message should be localized and sent in the[google.rpc.Status.details][google.rpc.Status.details] field, or localized by the client. |
| `details` | `Array<mojo.core.Any>` |  | N |  | A list of messages that carry the error details.  There is a common set ofmessage types for APIs to use. |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


#### `mojo.http.Request`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `method` | `string` |  | N |  | method specifies the HTTP method (GET, POST, PUT, etc.).For client requests, an empty string means GET. |
| `url` | `string` | `Url` | N |  | url specifies either the URI being requested (for serverrequests) or the URL to access (for client requests). |
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Response`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `status` | `mojo.http.Status` |  | N |  |  |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Status`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `integer` | `Int32` | N |  |
| `reason` | `string` |  | N |  |


#### `mojo.http.Version`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `major` | `integer` | `Int32` | N |  |
| `minor` | `integer` | `Int32` | N |  |


## 查询指定项目下的任务列表

### 请求路径
```http
GET /abfuzz/v1/projects/{project_id}/tasks
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |


#### Query 参数
| 参数名 | 参数类型 | 格式类型 | 是否必须 | 默认值 | 说明 |
|---|---|---|---|---|---|
| `operation_name` | `string` |  | 否 |  |  |
| `build_operation_name` | `string` |  | 否 |  |  |
| `done` | `boolean` |  | 否 |  |  |
| `page_size` | `integer` | `Int32` | 否 |  |  |
| `page_token` | `string` |  | 否 |  |  |
| `skip` | `integer` | `Int32` | 否 |  |  |


### 返回值

#### 返回对象
| type | description |
|---|---|
| `Array<abfuzz.Task>` |  |


#### `abfuzz.Container`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the id of the container |
| `name` | `string` |  | N |  | the name of the container |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `abfuzz.Task`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | 任务ID |
| `name` | `string` |  | N |  | 任务名称，对应不同的任务执行器，只能是以下之一：'fuzz', 'variant', 'minimize', 'regression', 'impact', 'blame', 'progression' |
| `projectId` | `string` |  | N |  | 任务所属项目ID |
| `projectName` | `string` |  | N |  | 任务所属项目名称 |
| `sourcePackageId` | `string` |  | N |  | 源码包ID |
| `buildOperationName` | `string` |  | N |  | 对应build操作的Operation name |
| `buildId` | `string` |  | N |  | 对应building的ID |
| `fuzzerName` | `string` |  | N |  | 任务所属模糊器（Fuzzer）名称 |
| `fuzzingId` | `string` |  | N |  | 任务所属Fuzzing ID，当任务对象是Project的时候，该值为空 |
| `operationName` | `string` |  | N |  | the operation name of the project, will be assigned by the longrunning.Operation id |
| `platform` | `string` | `Platform` | N |  | 指定的平台 |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `argument` | `mojo.core.Object` |  | N |  | 任务执行所需的参数 |
| `timeout` | `string` | `Duration` | N |  | 任务最长超时时间 |
| `isCommandOverride` | `boolean` |  | N |  | 命令能否被覆盖 |
| `highEnd` | `boolean` |  | N |  | 是否是高优先级任务 |
| `agentName` | `string` |  | N |  | 执行任务的agent name. |
| `state` | `string` |  | N |  | 任务的状态，只能是以下之一：created, running, stopped, finished, failed |
| `error` | `mojo.core.Error` |  | N |  | 任务失败后的错误信息 |
| `containers` | `Array<abfuzz.Container>` |  | N |  | 任务过程中产生的容器信息 |
| `createTime` | `string` | `Timestamp` | N |  | 任务的创建时间 |
| `updateTime` | `string` | `Timestamp` | N |  | 任务的更新时间 |


#### `mojo.core.Any`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `@type` | `string` |  | Y |  |


#### `mojo.core.Error`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` | `ErrorCode` | N |  | The error code |
| `message` | `string` |  | N |  | A developer-facing error message, which should be in English. Anyuser-facing error message should be localized and sent in the[google.rpc.Status.details][google.rpc.Status.details] field, or localized by the client. |
| `details` | `Array<mojo.core.Any>` |  | N |  | A list of messages that carry the error details.  There is a common set ofmessage types for APIs to use. |


## 获取指定任务信息

### 请求路径
```http
GET /abfuzz/v1/projects/{project_id}/tasks/{id}
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |
| `id` | `string` |  |  |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | 任务ID |
| `name` | `string` |  | N |  | 任务名称，对应不同的任务执行器，只能是以下之一：'fuzz', 'variant', 'minimize', 'regression', 'impact', 'blame', 'progression' |
| `projectId` | `string` |  | N |  | 任务所属项目ID |
| `projectName` | `string` |  | N |  | 任务所属项目名称 |
| `sourcePackageId` | `string` |  | N |  | 源码包ID |
| `buildOperationName` | `string` |  | N |  | 对应build操作的Operation name |
| `buildId` | `string` |  | N |  | 对应building的ID |
| `fuzzerName` | `string` |  | N |  | 任务所属模糊器（Fuzzer）名称 |
| `fuzzingId` | `string` |  | N |  | 任务所属Fuzzing ID，当任务对象是Project的时候，该值为空 |
| `operationName` | `string` |  | N |  | the operation name of the project, will be assigned by the longrunning.Operation id |
| `platform` | `string` | `Platform` | N |  | 指定的平台 |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `argument` | `mojo.core.Object` |  | N |  | 任务执行所需的参数 |
| `timeout` | `string` | `Duration` | N |  | 任务最长超时时间 |
| `isCommandOverride` | `boolean` |  | N |  | 命令能否被覆盖 |
| `highEnd` | `boolean` |  | N |  | 是否是高优先级任务 |
| `agentName` | `string` |  | N |  | 执行任务的agent name. |
| `state` | `string` |  | N |  | 任务的状态，只能是以下之一：created, running, stopped, finished, failed |
| `error` | `mojo.core.Error` |  | N |  | 任务失败后的错误信息 |
| `containers` | `Array<abfuzz.Container>` |  | N |  | 任务过程中产生的容器信息 |
| `createTime` | `string` | `Timestamp` | N |  | 任务的创建时间 |
| `updateTime` | `string` | `Timestamp` | N |  | 任务的更新时间 |


#### `abfuzz.Container`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the id of the container |
| `name` | `string` |  | N |  | the name of the container |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `mojo.core.Any`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `@type` | `string` |  | Y |  |


#### `mojo.core.Error`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` | `ErrorCode` | N |  | The error code |
| `message` | `string` |  | N |  | A developer-facing error message, which should be in English. Anyuser-facing error message should be localized and sent in the[google.rpc.Status.details][google.rpc.Status.details] field, or localized by the client. |
| `details` | `Array<mojo.core.Any>` |  | N |  | A list of messages that carry the error details.  There is a common set ofmessage types for APIs to use. |


## 查询测试用例列表

### 请求路径
```http
GET /abfuzz/v1/projects/{project_id}/testcases
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |


#### Query 参数
| 参数名 | 参数类型 | 格式类型 | 是否必须 | 默认值 | 说明 |
|---|---|---|---|---|---|
| `source_package_id` | `string` |  | 否 |  |  |
| `operation_name` | `string` |  | 否 |  |  |
| `fuzzing_id` | `string` |  | 否 |  |  |
| `page_size` | `integer` | `Int32` | 否 |  |  |
| `page_token` | `string` |  | 否 |  |  |
| `skip` | `integer` | `Int32` | 否 |  |  |


### 返回值

#### 返回对象
| type | description |
|---|---|
| `Array<abfuzz.Testcase>` |  |


#### `abfuzz.Engine`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the engine |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.Impact`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `versionIndices` | `Array<string>` |  | N |  | The impacted version indices (including beta, stable and extended_stable). |
| `extendedStableVersion` | `string` |  | N |  | The impacted extended stable version. |
| `extendedStableVersionIndices` | `Array<string>` |  | N |  | The impacted extended stable version indices. |
| `extendedStableVersionLikely` | `boolean` |  | N |  | The impacted extended stable version is merely probable (not definite).See the comment on stable_version_likely. |
| `stableVersion` | `string` |  | N |  | The impacted stable version. |
| `stableVersionIndices` | `Array<string>` |  | N |  | The impacted stable version indices. |
| `stableVersionLikely` | `boolean` |  | N |  | The impacted stable version is merely probable (not definite). Becausefor a non-asan build, we don't have a stable/beta build. Therefore, wemake an intelligent guess on the version. |
| `betaVersion` | `string` |  | N |  | The impacted beta version. |
| `betaVersionIndices` | `Array<string>` |  | N |  | The impacted beta version indices. |
| `betaVersionLikely` | `boolean` |  | N |  | The impacted beta version is merely probable (not definite). See thecomment on stable_version_likely. |
| `headVersion` | `string` |  | N |  | The impacted 'head' version |
| `headVersionIndices` | `Array<string>` |  | N |  | The impacted head version indices. |
| `headVersionLikely` | `boolean` |  | N |  | The impacted head version is likely. |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `abfuzz.Sanitizer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the sanitizer |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.Testcase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | id for the testcase |
| `description` | `string` |  | N |  | description for the testcase |
| `keywords` | `Array<string>` |  | N |  | keywords is used for searching. |
| `securityFlag` | `boolean` |  | N |  | Is it a security bug ? |
| `securitySeverity` | `integer` | `Int64` | N |  | Security severity of the bug. |
| `oneTimeCrasherFlag` | `boolean` |  | N |  | Did the bug only reproduced once ? |
| `comments` | `string` |  | N |  | Any additional comments. |
| `projectId` | `string` |  | N |  | Project id associated with this test case. |
| `projectName` | `string` |  | N |  | Project name associated with this test case. |
| `sourcePackageId` | `string` |  | N |  | the source package id for the testcase |
| `fuzzerName` | `string` |  | N |  | Name of the fuzzer used to generate this testcase. |
| `fuzzerNameIndices` | `Array<string>` |  | N |  | Fuzzer name indices |
| `overriddenFuzzerName` | `string` |  | N |  | Overridden fuzzer name because actual fuzzer name can be different in manyscenarios (libfuzzer, afl, etc). |
| `fuzzingId` | `string` |  | N |  | the fuzzing which is the testcase generated from |
| `buildOperationName` | `string` |  | N |  | the build operation name of the testcase |
| `operationName` | `string` |  | N |  | the operation name of the testcase when fuzzing |
| `buildId` | `string` |  | N |  | the build id |
| `platform` | `string` | `Platform` | N |  | Platforms (e.g. windows, linux, android). |
| `language` | `string` |  | N |  | the program language for the fuzzing |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `otherPlatforms` | `Array<string>` |  | N |  | Platforms (e.g. windows, linux, android). |
| `otherLangVms` | `Array<abfuzz.LangVM>` |  | N |  | 指定的JDK，如果是聚合后的testcase，会包含多个jdk版本 |
| `engines` | `Array<abfuzz.Engine>` |  | N |  | engine names for the testcase |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | 指定的sanitizers |
| `fuzzedIds` | `Array<string>` |  | N |  | Blobstore keys for various things like original testcase, minimizedtestcase, etc. |
| `minimizedIds` | `Array<string>` |  | N |  |
| `minidumpIds` | `Array<string>` |  | N |  |
| `status` | `string` |  | N |  | Status of this testcase (pending, processed, unreproducible, etc). |
| `open` | `boolean` |  | N |  | Whether testcase is open. |
| `duplicateOf` | `integer` | `Int64` | N |  | Id of the testcase that this is marked as a duplicate of. |
| `groupId` | `integer` | `Int64` | N |  | Id for this testcase's associated group. |
| `groupBugInformation` | `integer` | `Int64` | N |  | Tracking issue tracker bug for this testcase group. |
| `isLeader` | `boolean` |  | N |  | Whether or not a testcase is the leader of its group.If the testcase is not in a group, it's the leader of a group of 1.The default is false because we prefer not to show crashes until we aresure. And group_task will correctly set the value within 30 minutes. |
| `bugInformation` | `string` |  | N |  | Tracking issue tracker bug. One bug number per line (future extension). |
| `isADuplicateFlag` | `boolean` |  | N |  | Whether or not a testcase is a duplicate of other testcase. |
| `hasBugFlag` | `boolean` |  | N |  | Whether testcase has a bug (either bug_information orgroup_bug_information). |
| `bugIndices` | `Array<string>` |  | N |  | Indices for bug_information and group_bug_information. |
| `triaged` | `boolean` |  | N |  | Boolean attribute indicating if cleanup triage needs to be done. |
| `additionalMetadata` | `mojo.core.Object` |  | N |  | Additional metadata stored as a JSON object. This should be used forproperties that are not commonly accessed and do not need to be indexed. |
| `crashType` | `string` |  | N |  | Crash on an invalid read/write. |
| `crashDescription` | `string` |  | N |  | Crash description for runtime error |
| `crashAddress` | `string` |  | N |  | Crashing address. |
| `crashState` | `string` |  | N |  | First x stack frames. |
| `crashStacktrace` | `string` |  | N |  | Complete stacktrace. |
| `lastTestedCrashStacktrace` | `string` |  | N |  | Last tested crash stacktrace using the latest revision. |
| `crashRevision` | `integer` | `Int64` | N |  | Revision that we discovered the crash in. |
| `crashSourceFileName` | `string` |  | N |  | The source file name, may be omitted if debug symbols are not available. |
| `crashSourceLine` | `integer` | `Int32` | N |  | The (1-based) source line number, may be omitted if debug symbols arenot available. |
| `crashFunctionName` | `string` |  | N |  | The function name, may be omitted if debug symbols are not available. |
| `symbolized` | `boolean` |  | N |  | Flag indicating whether or not the testcase has been symbolized. |
| `input` | `abfuzz.Testcase.Input` |  | N |  | The input for the testcase crashing |
| `targetServerLogPath` | `string` |  | N |  | The target server log path with java testcase |
| `absolutePath` | `string` |  | N |  | The file on the agent that generated the testcase. |
| `binaryFlag` | `boolean` |  | N |  | Is this a binary file? |
| `flakyStack` | `boolean` |  | N |  | Does the testcase crash stack vary b/w crashes ? |
| `httpFlag` | `boolean` |  | N |  | Do we need to test this testcase using an HTTP/HTTPS server? |
| `gestures` | `Array<string>` |  | N |  | Fake user interaction sequences like key clicks, mouse movements, etc. |
| `redzone` | `integer` | `Int64` | N |  | ASAN redzone size in bytes. |
| `disableUbsan` | `boolean` |  | N |  | Flag indicating if UBSan detection should be disabled. This is needed forcases when ASan and UBSan are bundled in the same build configurationand we need to disable UBSan in some runs to find the potentially moreinteresting ASan bugs. |
| `timeoutMultiplier` | `number` | `Float64` | N |  | Adjusts timeout based on multiplier value. |
| `regression` | `string` |  | N |  | Regression range. |
| `fixed` | `string` |  | N |  | Revisions where this issue has been fixed. |
| `impactIndices` | `Array<string>` |  | N |  | Impact indices for searching. |
| `impact` | `abfuzz.Impact` |  | N |  | 用例影响源代码的版本信息 |
| `isImpactSetFlag` | `boolean` |  | N |  | Whether or not impact task has been run on this testcase. |
| `uploaderEmail` | `string` |  | N |  | Uploader email address. |
| `createTime` | `string` | `Timestamp` | N |  | Timestamp. |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.Testcase.Input`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  | binaray, file, json |
| `content` | `mojo.core.Value` |  | N |  | the input content |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


## 获取指定的测试用例

### 请求路径
```http
GET /abfuzz/v1/projects/{project_id}/testcases/{id}
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |
| `id` | `string` |  |  |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | id for the testcase |
| `description` | `string` |  | N |  | description for the testcase |
| `keywords` | `Array<string>` |  | N |  | keywords is used for searching. |
| `securityFlag` | `boolean` |  | N |  | Is it a security bug ? |
| `securitySeverity` | `integer` | `Int64` | N |  | Security severity of the bug. |
| `oneTimeCrasherFlag` | `boolean` |  | N |  | Did the bug only reproduced once ? |
| `comments` | `string` |  | N |  | Any additional comments. |
| `projectId` | `string` |  | N |  | Project id associated with this test case. |
| `projectName` | `string` |  | N |  | Project name associated with this test case. |
| `sourcePackageId` | `string` |  | N |  | the source package id for the testcase |
| `fuzzerName` | `string` |  | N |  | Name of the fuzzer used to generate this testcase. |
| `fuzzerNameIndices` | `Array<string>` |  | N |  | Fuzzer name indices |
| `overriddenFuzzerName` | `string` |  | N |  | Overridden fuzzer name because actual fuzzer name can be different in manyscenarios (libfuzzer, afl, etc). |
| `fuzzingId` | `string` |  | N |  | the fuzzing which is the testcase generated from |
| `buildOperationName` | `string` |  | N |  | the build operation name of the testcase |
| `operationName` | `string` |  | N |  | the operation name of the testcase when fuzzing |
| `buildId` | `string` |  | N |  | the build id |
| `platform` | `string` | `Platform` | N |  | Platforms (e.g. windows, linux, android). |
| `language` | `string` |  | N |  | the program language for the fuzzing |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `otherPlatforms` | `Array<string>` |  | N |  | Platforms (e.g. windows, linux, android). |
| `otherLangVms` | `Array<abfuzz.LangVM>` |  | N |  | 指定的JDK，如果是聚合后的testcase，会包含多个jdk版本 |
| `engines` | `Array<abfuzz.Engine>` |  | N |  | engine names for the testcase |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | 指定的sanitizers |
| `fuzzedIds` | `Array<string>` |  | N |  | Blobstore keys for various things like original testcase, minimizedtestcase, etc. |
| `minimizedIds` | `Array<string>` |  | N |  |
| `minidumpIds` | `Array<string>` |  | N |  |
| `status` | `string` |  | N |  | Status of this testcase (pending, processed, unreproducible, etc). |
| `open` | `boolean` |  | N |  | Whether testcase is open. |
| `duplicateOf` | `integer` | `Int64` | N |  | Id of the testcase that this is marked as a duplicate of. |
| `groupId` | `integer` | `Int64` | N |  | Id for this testcase's associated group. |
| `groupBugInformation` | `integer` | `Int64` | N |  | Tracking issue tracker bug for this testcase group. |
| `isLeader` | `boolean` |  | N |  | Whether or not a testcase is the leader of its group.If the testcase is not in a group, it's the leader of a group of 1.The default is false because we prefer not to show crashes until we aresure. And group_task will correctly set the value within 30 minutes. |
| `bugInformation` | `string` |  | N |  | Tracking issue tracker bug. One bug number per line (future extension). |
| `isADuplicateFlag` | `boolean` |  | N |  | Whether or not a testcase is a duplicate of other testcase. |
| `hasBugFlag` | `boolean` |  | N |  | Whether testcase has a bug (either bug_information orgroup_bug_information). |
| `bugIndices` | `Array<string>` |  | N |  | Indices for bug_information and group_bug_information. |
| `triaged` | `boolean` |  | N |  | Boolean attribute indicating if cleanup triage needs to be done. |
| `additionalMetadata` | `mojo.core.Object` |  | N |  | Additional metadata stored as a JSON object. This should be used forproperties that are not commonly accessed and do not need to be indexed. |
| `crashType` | `string` |  | N |  | Crash on an invalid read/write. |
| `crashDescription` | `string` |  | N |  | Crash description for runtime error |
| `crashAddress` | `string` |  | N |  | Crashing address. |
| `crashState` | `string` |  | N |  | First x stack frames. |
| `crashStacktrace` | `string` |  | N |  | Complete stacktrace. |
| `lastTestedCrashStacktrace` | `string` |  | N |  | Last tested crash stacktrace using the latest revision. |
| `crashRevision` | `integer` | `Int64` | N |  | Revision that we discovered the crash in. |
| `crashSourceFileName` | `string` |  | N |  | The source file name, may be omitted if debug symbols are not available. |
| `crashSourceLine` | `integer` | `Int32` | N |  | The (1-based) source line number, may be omitted if debug symbols arenot available. |
| `crashFunctionName` | `string` |  | N |  | The function name, may be omitted if debug symbols are not available. |
| `symbolized` | `boolean` |  | N |  | Flag indicating whether or not the testcase has been symbolized. |
| `input` | `abfuzz.Testcase.Input` |  | N |  | The input for the testcase crashing |
| `targetServerLogPath` | `string` |  | N |  | The target server log path with java testcase |
| `absolutePath` | `string` |  | N |  | The file on the agent that generated the testcase. |
| `binaryFlag` | `boolean` |  | N |  | Is this a binary file? |
| `flakyStack` | `boolean` |  | N |  | Does the testcase crash stack vary b/w crashes ? |
| `httpFlag` | `boolean` |  | N |  | Do we need to test this testcase using an HTTP/HTTPS server? |
| `gestures` | `Array<string>` |  | N |  | Fake user interaction sequences like key clicks, mouse movements, etc. |
| `redzone` | `integer` | `Int64` | N |  | ASAN redzone size in bytes. |
| `disableUbsan` | `boolean` |  | N |  | Flag indicating if UBSan detection should be disabled. This is needed forcases when ASan and UBSan are bundled in the same build configurationand we need to disable UBSan in some runs to find the potentially moreinteresting ASan bugs. |
| `timeoutMultiplier` | `number` | `Float64` | N |  | Adjusts timeout based on multiplier value. |
| `regression` | `string` |  | N |  | Regression range. |
| `fixed` | `string` |  | N |  | Revisions where this issue has been fixed. |
| `impactIndices` | `Array<string>` |  | N |  | Impact indices for searching. |
| `impact` | `abfuzz.Impact` |  | N |  | 用例影响源代码的版本信息 |
| `isImpactSetFlag` | `boolean` |  | N |  | Whether or not impact task has been run on this testcase. |
| `uploaderEmail` | `string` |  | N |  | Uploader email address. |
| `createTime` | `string` | `Timestamp` | N |  | Timestamp. |
| `updateTime` | `string` | `Timestamp` | N |  |  |


#### `abfuzz.Engine`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the engine |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.Impact`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `versionIndices` | `Array<string>` |  | N |  | The impacted version indices (including beta, stable and extended_stable). |
| `extendedStableVersion` | `string` |  | N |  | The impacted extended stable version. |
| `extendedStableVersionIndices` | `Array<string>` |  | N |  | The impacted extended stable version indices. |
| `extendedStableVersionLikely` | `boolean` |  | N |  | The impacted extended stable version is merely probable (not definite).See the comment on stable_version_likely. |
| `stableVersion` | `string` |  | N |  | The impacted stable version. |
| `stableVersionIndices` | `Array<string>` |  | N |  | The impacted stable version indices. |
| `stableVersionLikely` | `boolean` |  | N |  | The impacted stable version is merely probable (not definite). Becausefor a non-asan build, we don't have a stable/beta build. Therefore, wemake an intelligent guess on the version. |
| `betaVersion` | `string` |  | N |  | The impacted beta version. |
| `betaVersionIndices` | `Array<string>` |  | N |  | The impacted beta version indices. |
| `betaVersionLikely` | `boolean` |  | N |  | The impacted beta version is merely probable (not definite). See thecomment on stable_version_likely. |
| `headVersion` | `string` |  | N |  | The impacted 'head' version |
| `headVersionIndices` | `Array<string>` |  | N |  | The impacted head version indices. |
| `headVersionLikely` | `boolean` |  | N |  | The impacted head version is likely. |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `abfuzz.Sanitizer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the sanitizer |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.Testcase.Input`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  | binaray, file, json |
| `content` | `mojo.core.Value` |  | N |  | the input content |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


## 获取指定的测试用例的input引用的文件

### 请求路径
```http
GET /abfuzz/v1/projects/{project_id}/testcases/{testcase_id}/inputs/{id}
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |
| `testcase_id` | `string` |  |  |
| `id` | `string` |  |  |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `contentType` | `mojo.core.MediaType` |  | N |  | A standard MIME type describing the format of the object data. |
| `content` | `string` | `Bytes` | N |  |


#### `mojo.core.MediaType`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `subtype` | `string` |  | N |  |
| `parameter` | `mojo.core.MediaType.Parameter` |  | N |  |  |


#### `mojo.core.MediaType.Parameter`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  |
| `value` | `mojo.core.Value` |  | N |  |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


## 获取测试用例对应的java目标程序日志文件

### 请求路径
```http
GET /abfuzz/v1/projects/{project_id}/testcases/{testcase_id}/target_server_log
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `project_id` | `string` |  |  |
| `testcase_id` | `string` |  |  |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `contentType` | `mojo.core.MediaType` |  | N |  | A standard MIME type describing the format of the object data. |
| `content` | `string` | `Bytes` | N |  |


#### `mojo.core.MediaType`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `subtype` | `string` |  | N |  |
| `parameter` | `mojo.core.MediaType.Parameter` |  | N |  |  |


#### `mojo.core.MediaType.Parameter`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  |
| `value` | `mojo.core.Value` |  | N |  |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


## 创建项目的源代码包

### 请求路径
```http
POST /abfuzz/v1/projects/{source_package_project_id}/source-packages
```


### 请求参数

#### Body 请求对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the uuid for the source package |
| `projectId` | `string` |  | N |  | the project id which including the source package |
| `projectName` | `string` |  | N |  | the project name which including the source package |
| `projectVersion` | `string` |  | N |  | the project version |
| `type` | `string` |  | N |  | the type of the source packagewhitebox, graybox, blackbox |
| `name` | `string` |  | N |  | the name of the module of the source package |
| `fuzzRepo` | `abfuzz.Repository` |  | N |  | the repository information for the fuzz code reposisity in the source package |
| `sourceRepo` | `abfuzz.Repository` |  | N |  | the repository information for the main code reposisity of custom in the source package |
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `fileSet` | `Array<abfuzz.StorageFile>` |  | N |  | the file set for the source package |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary or raw source code package. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |
| `customVersion` | `string` |  | N |  | more version info for the custom system |
| `codeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `createTime` | `string` | `Timestamp` | N |  | create time of the source code package |
| `updateTime` | `string` | `Timestamp` | N |  | update time of the source code package |


#### `abfuzz.Repository`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `url` | `string` | `Url` | N |  | the git url for the source package |
| `branch` | `string` |  | N |  | the git branch for the source package |
| `tag` | `string` |  | N |  | the git tag for the source package |
| `commitTime` | `string` | `Timestamp` | N |  | the used commit time for the source package |
| `commitKey` | `string` |  | N |  | the used commit key for the source package, it is hash key for git |


#### `abfuzz.StorageFile`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  | 文件在minio上的 地址 |
| `checksum` | `string` | `Checksum` | N |  | 文件的hash校验码 |
| `type` | `string` |  | N |  | 文件类型，比如： `corpus`, `harness`, `source`, `build-script`, `openapi`, `jar` |
| `group` | `string` |  | N |  | 文件属于某个组，可以是某个fuzzer |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the uuid for the source package |
| `projectId` | `string` |  | N |  | the project id which including the source package |
| `projectName` | `string` |  | N |  | the project name which including the source package |
| `projectVersion` | `string` |  | N |  | the project version |
| `type` | `string` |  | N |  | the type of the source packagewhitebox, graybox, blackbox |
| `name` | `string` |  | N |  | the name of the module of the source package |
| `fuzzRepo` | `abfuzz.Repository` |  | N |  | the repository information for the fuzz code reposisity in the source package |
| `sourceRepo` | `abfuzz.Repository` |  | N |  | the repository information for the main code reposisity of custom in the source package |
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `fileSet` | `Array<abfuzz.StorageFile>` |  | N |  | the file set for the source package |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary or raw source code package. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |
| `customVersion` | `string` |  | N |  | more version info for the custom system |
| `codeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `createTime` | `string` | `Timestamp` | N |  | create time of the source code package |
| `updateTime` | `string` | `Timestamp` | N |  | update time of the source code package |


#### `abfuzz.Repository`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `url` | `string` | `Url` | N |  | the git url for the source package |
| `branch` | `string` |  | N |  | the git branch for the source package |
| `tag` | `string` |  | N |  | the git tag for the source package |
| `commitTime` | `string` | `Timestamp` | N |  | the used commit time for the source package |
| `commitKey` | `string` |  | N |  | the used commit key for the source package, it is hash key for git |


#### `abfuzz.StorageFile`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  | 文件在minio上的 地址 |
| `checksum` | `string` | `Checksum` | N |  | 文件的hash校验码 |
| `type` | `string` |  | N |  | 文件类型，比如： `corpus`, `harness`, `source`, `build-script`, `openapi`, `jar` |
| `group` | `string` |  | N |  | 文件属于某个组，可以是某个fuzzer |


## 更新指定源代码包

### 请求路径
```http
PUT /abfuzz/v1/projects/{source_package_project_id}/source-packages/{source_package_id}
```


### 请求参数

#### Body 请求对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the uuid for the source package |
| `projectId` | `string` |  | N |  | the project id which including the source package |
| `projectName` | `string` |  | N |  | the project name which including the source package |
| `projectVersion` | `string` |  | N |  | the project version |
| `type` | `string` |  | N |  | the type of the source packagewhitebox, graybox, blackbox |
| `name` | `string` |  | N |  | the name of the module of the source package |
| `fuzzRepo` | `abfuzz.Repository` |  | N |  | the repository information for the fuzz code reposisity in the source package |
| `sourceRepo` | `abfuzz.Repository` |  | N |  | the repository information for the main code reposisity of custom in the source package |
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `fileSet` | `Array<abfuzz.StorageFile>` |  | N |  | the file set for the source package |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary or raw source code package. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |
| `customVersion` | `string` |  | N |  | more version info for the custom system |
| `codeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `createTime` | `string` | `Timestamp` | N |  | create time of the source code package |
| `updateTime` | `string` | `Timestamp` | N |  | update time of the source code package |


#### `abfuzz.Repository`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `url` | `string` | `Url` | N |  | the git url for the source package |
| `branch` | `string` |  | N |  | the git branch for the source package |
| `tag` | `string` |  | N |  | the git tag for the source package |
| `commitTime` | `string` | `Timestamp` | N |  | the used commit time for the source package |
| `commitKey` | `string` |  | N |  | the used commit key for the source package, it is hash key for git |


#### `abfuzz.StorageFile`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  | 文件在minio上的 地址 |
| `checksum` | `string` | `Checksum` | N |  | 文件的hash校验码 |
| `type` | `string` |  | N |  | 文件类型，比如： `corpus`, `harness`, `source`, `build-script`, `openapi`, `jar` |
| `group` | `string` |  | N |  | 文件属于某个组，可以是某个fuzzer |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the uuid for the source package |
| `projectId` | `string` |  | N |  | the project id which including the source package |
| `projectName` | `string` |  | N |  | the project name which including the source package |
| `projectVersion` | `string` |  | N |  | the project version |
| `type` | `string` |  | N |  | the type of the source packagewhitebox, graybox, blackbox |
| `name` | `string` |  | N |  | the name of the module of the source package |
| `fuzzRepo` | `abfuzz.Repository` |  | N |  | the repository information for the fuzz code reposisity in the source package |
| `sourceRepo` | `abfuzz.Repository` |  | N |  | the repository information for the main code reposisity of custom in the source package |
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `fileSet` | `Array<abfuzz.StorageFile>` |  | N |  | the file set for the source package |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary or raw source code package. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |
| `customVersion` | `string` |  | N |  | more version info for the custom system |
| `codeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `createTime` | `string` | `Timestamp` | N |  | create time of the source code package |
| `updateTime` | `string` | `Timestamp` | N |  | update time of the source code package |


#### `abfuzz.Repository`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `url` | `string` | `Url` | N |  | the git url for the source package |
| `branch` | `string` |  | N |  | the git branch for the source package |
| `tag` | `string` |  | N |  | the git tag for the source package |
| `commitTime` | `string` | `Timestamp` | N |  | the used commit time for the source package |
| `commitKey` | `string` |  | N |  | the used commit key for the source package, it is hash key for git |


#### `abfuzz.StorageFile`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  | 文件在minio上的 地址 |
| `checksum` | `string` | `Checksum` | N |  | 文件的hash校验码 |
| `type` | `string` |  | N |  | 文件类型，比如： `corpus`, `harness`, `source`, `build-script`, `openapi`, `jar` |
| `group` | `string` |  | N |  | 文件属于某个组，可以是某个fuzzer |


## 批量获取项目

### 请求路径
```http
GET /abfuzz/v1/projects:batch-get
```


### 请求参数

#### Query 参数
| 参数名 | 参数类型 | 格式类型 | 是否必须 | 默认值 | 说明 |
|---|---|---|---|---|---|
| `ids` | `Array<string>` |  | 否 |  |  |


### 返回值

#### 返回对象
| type | description |
|---|---|
| `Array<abfuzz.Project>` |  |


#### `abfuzz.CaseStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `disable` | `array` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `abfuzz.ContextCase`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `steps` | `Array<abfuzz.CaseStep>` |  | N |  |


#### `abfuzz.ContextRequest`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `setup` | `abfuzz.ContextSetup` |  | N |  | 全局设置，在cases之前执行 |
| `cases` | `Array<abfuzz.ContextCase>` |  | N |  | OpenAPI里的请求(用path和method来对应)，包含在cases里的优先执行，没有包含在cases里的请求，每个请求自动生成一个case，在cases之后依次自行 |
| `teardown` | `abfuzz.ContextTeardown` |  | N |  | 在cases之后执行的请求 |


#### `abfuzz.ContextSetup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.SetupStep>` |  | N |  |


#### `abfuzz.ContextTeardown`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `steps` | `Array<abfuzz.TeardownStep>` |  | N |  |


#### `abfuzz.CorpusConfig`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `includings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `excludings` | `Array<abfuzz.EndPoint>` |  | N |  |
| `httpBaseUrl` | `string` | `Url` | N |  | URl type<br>Uniform Resource Identifier (URI) is a string of characters used to identify a resource.<br>A Uniform Resource Name (URN) may be compared to a person's name,while a Uniform Resource Locator (URL) may be compared to their street address.In other words, a URN identifies an item and a URL provides a method for finding it.<br> |
| `httpMappingPort` | `integer` | `Int32` | N |  |
| `httpMappingHost` | `string` |  | N |  |
| `httpStaticValues` | `mojo.core.Object` |  | N |  | Object type |
| `httpCorpuses` | `Array<abfuzz.HttpCorpus>` |  | N |  |
| `combinedApiPath` | `string` |  | N |  |
| `asyncApi` | `boolean` |  | N |  |


#### `abfuzz.EndPoint`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` |  | N |  |
| `http` | `abfuzz.EndPoint.Http` |  | N |  |  |
| `priority` | `integer` | `Int32` | N |  |


#### `abfuzz.EndPoint.Http`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `path` | `string` |  | N |  |
| `methods` | `Array<string>` |  | N |  |


#### `abfuzz.Engine`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the engine |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.Entity`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  |
| `stepType` | `string` |  | N |  |
| `interfaces` | `Array<abfuzz.Entity.Interface>` |  | N |  |


#### `abfuzz.Entity.Interface`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  |
| `type` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | N |  |
| `pathParams` | `Map<string, string>` |  | N |  |
| `queryParams` | `Map<string, string>` |  | N |  |
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `response` | `mojo.http.Response` |  | N |  | Response represents the response from an HTTP request. |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |


#### `abfuzz.Fixup`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.Fuzzer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | Y |  | the fuzzer name |
| `group` | `string` |  | N |  | the group id for the fuzzers |
| `platform` | `string` | `Platform` | N |  | The platform that this job can run on. |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `fuzzerPackage` | `abfuzz.FuzzerPackage` |  | N |  | the fuzzer package of the fuzzer |
| `executablePath` | `string` |  | N |  | Fuzzer's main executable path, relative to root. |
| `executableArguments` | `Array<string>` |  | N |  | the input arguments for the executable, it will be skipped when launcher_script is set. |
| `executableEnvironment` | `Map<string, string>` |  | N |  | the environment setting for the executable |
| `launcherScript` | `string` |  | N |  | Custom script that should be used to launch executable for this fuzzer.It will not try to launch the target if "@service-auto-start" set. |
| `timeout` | `integer` | `Int64` | N |  | Fuzzing timeout. |
| `testcaseTimeoutMillis` | `integer` | `Int64` | N |  | Testcase timeout. |
| `autoStop` | `boolean` |  | N |  | Fuzzing automatic stop. |
| `autoHarness` | `boolean` |  | N |  | auto generate harness |
| `customMutatorPlugin` | `string` |  | N |  | custom mutator plugin for Fuzz Engine |
| `customAllowTargets` | `Array<string>` |  | N |  | custom file name & function name targets for instruments and coverage, like white list. |
| `customDenyTargets` | `Array<string>` |  | N |  | custom targets for instruments and coverage, like white list. |
| `context` | `abfuzz.ContextRequest` |  | N |  | context request |
| `corpusPath` | `string` |  | N |  | the special corpus path |
| `preprocessScript` | `string` |  | N |  | the preprocess script info |
| `onlyFunctionalTest` | `boolean` |  | N |  | is only fuzzing user corpus |
| `autoHarnessCommand` | `string` |  | N |  | execute the command before run auto harness generation |
| `autoHarnessScript` | `string` |  | N |  | execute the script before run auto harness generation if the command is empty |
| `autoHarnessArguments` | `Array<string>` |  | N |  | the arguments when to run the harness generation, usually for including path setting |
| `autoHarnessBlacklist` | `Array<string>` |  | N |  | the blacklist files to run the harness generation, these files will be skipped. |
| `autoHarnessWebServer` | `boolean` |  | N |  | determine if the java auto harness server is a web server |
| `metadata` | `Map<string, mojo.core.Value>` |  | N |  | the meta information for the fuzzing |
| `corpus` | `abfuzz.CorpusConfig` |  | N |  |  |
| `entities` | `Array<abfuzz.Entity>` |  | N |  |


#### `abfuzz.FuzzerPackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `type` | `string` |  | N |  | the type of the source package, oneof the whitebox, greybox, blackbox |
| `filename` | `string` |  | N |  | Filename for the custom binary. |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |


#### `abfuzz.HttpCorpus`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  |
| `method` | `string` |  | N |  |
| `path` | `string` |  | Y |  |
| `staticValues` | `mojo.core.Object` |  | N |  | Object type |
| `fixups` | `Array<abfuzz.Fixup>` |  | N |  |
| `relations` | `Array<abfuzz.Relation>` |  | N |  |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `abfuzz.Project`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | project id |
| `name` | `string` |  | N |  | project name, normally the same with the custom's main source repo |
| `description` | `string` |  | N |  | project description |
| `sourcePackage` | `abfuzz.SourcePackage` |  | N |  | the source package for the project, only used in the create project |
| `language` | `string` |  | N |  | the program languages for the project，如果有多种语言，那么fuzzer中就需要指定语言 |
| `userId` | `string` |  | N |  | 用户相关的Project的用户ID |
| `externalId` | `string` |  | N |  | 第三方系统对应project的id |
| `sanitizers` | `Array<abfuzz.Sanitizer>` |  | N |  | the sanitizers will be used for the project |
| `engines` | `Array<abfuzz.Engine>` |  | Y |  | the fazz engine name |
| `platforms` | `Array<string>` |  | N |  | The platform that this job can run on. |
| `langVms` | `Array<abfuzz.LangVM>` |  | N |  | The lang_vm platform that this job can run on if the language is Java. |
| `fuzzers` | `Array<abfuzz.Fuzzer>` |  | Y |  | the fuzzers in the project |
| `createTime` | `string` | `Timestamp` | N |  | the create time of the project |
| `updateTime` | `string` | `Timestamp` | N |  | the update time of the project |
| `deleteTime` | `string` | `Timestamp` | N |  | the delete time of the project, for the soft delete |


#### `abfuzz.Relation`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `type` | `string` |  | N |  |
| `source` | `string` |  | N |  |
| `target` | `string` |  | N |  |


#### `abfuzz.Repository`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `url` | `string` | `Url` | N |  | the git url for the source package |
| `branch` | `string` |  | N |  | the git branch for the source package |
| `tag` | `string` |  | N |  | the git tag for the source package |
| `commitTime` | `string` | `Timestamp` | N |  | the used commit time for the source package |
| `commitKey` | `string` |  | N |  | the used commit key for the source package, it is hash key for git |


#### `abfuzz.Sanitizer`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the sanitizer |
| `properties` | `mojo.core.Object` |  | N |  | the properties of the engine |


#### `abfuzz.SetupStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | 更新上下文时发起的请求，不要求包含在OpenAPI中 |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |
| `linkValues` | `mojo.core.Object` |  | N |  | 后续所有case里的请求都会带上的变量 |
| `refreshIntervalSeconds` | `integer` | `Int64` | N |  | 刷新linkValues的周期(秒) |
| `refreshCount` | `integer` | `Int64` | N |  | 刷新linkValues的请求次数，如果同时设置了refreshIntervalSeconds和refreshCount，以refreshIntervalSeconds为准 |
| `filter` | `Array<string>` |  | N |  |
| `order` | `string` |  | N |  |


#### `abfuzz.SourcePackage`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the uuid for the source package |
| `projectId` | `string` |  | N |  | the project id which including the source package |
| `projectName` | `string` |  | N |  | the project name which including the source package |
| `projectVersion` | `string` |  | N |  | the project version |
| `type` | `string` |  | N |  | the type of the source packagewhitebox, graybox, blackbox |
| `name` | `string` |  | N |  | the name of the module of the source package |
| `fuzzRepo` | `abfuzz.Repository` |  | N |  | the repository information for the fuzz code reposisity in the source package |
| `sourceRepo` | `abfuzz.Repository` |  | N |  | the repository information for the main code reposisity of custom in the source package |
| `storageKey` | `string` |  | N |  | object store key of the source package |
| `storageChecksum` | `string` | `Checksum` | N |  | the object store checksum of the source package |
| `fileSet` | `Array<abfuzz.StorageFile>` |  | N |  | the file set for the source package |
| `revision` | `integer` | `Int64` | N |  | Revision of the custom binary or raw source code package. |
| `version` | `string` |  | N |  | version of the custom binary or raw source code package. |
| `customVersion` | `string` |  | N |  | more version info for the custom system |
| `codeMap` | `string` | `Url` | N |  | 源代码文件映射的JSON URL |
| `createTime` | `string` | `Timestamp` | N |  | create time of the source code package |
| `updateTime` | `string` | `Timestamp` | N |  | update time of the source code package |


#### `abfuzz.StorageFile`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `key` | `string` |  | N |  | 文件在minio上的 地址 |
| `checksum` | `string` | `Checksum` | N |  | 文件的hash校验码 |
| `type` | `string` |  | N |  | 文件类型，比如： `corpus`, `harness`, `source`, `build-script`, `openapi`, `jar` |
| `group` | `string` |  | N |  | 文件属于某个组，可以是某个fuzzer |


#### `abfuzz.TeardownStep`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `request` | `mojo.http.Request` |  | N |  | A Request represents an HTTP request received by a serveror to be sent by a client. |
| `variables` | `mojo.core.Object` |  | N |  | 定义变量，从response中取得的值都可以保存到变量里 |


#### `mojo.core.Value`
| type | format | description |
|---|---|---|
| `null` |  |  |
| `boolean` |  |  |
| `string` |  |  |
| `string` | `Bytes` | the format is: `b64.{base64 encoded bytes}` |
| `integer` | `Int64` |  |
| `number` | `Float64` |  |
| `mojo.core.Object` |  |  |
| `array` |  |  |


#### `mojo.http.Request`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `method` | `string` |  | N |  | method specifies the HTTP method (GET, POST, PUT, etc.).For client requests, an empty string means GET. |
| `url` | `string` | `Url` | N |  | url specifies either the URI being requested (for serverrequests) or the URL to access (for client requests). |
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Response`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `version` | `mojo.http.Version` |  | N |  | The protocol version for incoming server requests. |
| `status` | `mojo.http.Status` |  | N |  |  |
| `headers` | `Map<string, Array<string>>` |  | N |  | Headers contains the request header fields either receivedby the server or to be sent by the client. |
| `body` | `mojo.core.Value` |  | N |  | body is the request's body, which ban be raw bytes or JSON object |


#### `mojo.http.Status`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `integer` | `Int32` | N |  |
| `reason` | `string` |  | N |  |


#### `mojo.http.Version`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `major` | `integer` | `Int32` | N |  |
| `minor` | `integer` | `Int32` | N |  |


## 调度任务

### 请求路径
```http
POST /abfuzz/v1/tasks/{id}:schedule
```


### 请求参数

#### Body 请求对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | 任务ID |
| `name` | `string` |  | N |  | 任务名称，对应不同的任务执行器，只能是以下之一：'fuzz', 'variant', 'minimize', 'regression', 'impact', 'blame', 'progression' |
| `projectId` | `string` |  | N |  | 任务所属项目ID |
| `projectName` | `string` |  | N |  | 任务所属项目名称 |
| `sourcePackageId` | `string` |  | N |  | 源码包ID |
| `buildOperationName` | `string` |  | N |  | 对应build操作的Operation name |
| `buildId` | `string` |  | N |  | 对应building的ID |
| `fuzzerName` | `string` |  | N |  | 任务所属模糊器（Fuzzer）名称 |
| `fuzzingId` | `string` |  | N |  | 任务所属Fuzzing ID，当任务对象是Project的时候，该值为空 |
| `operationName` | `string` |  | N |  | the operation name of the project, will be assigned by the longrunning.Operation id |
| `platform` | `string` | `Platform` | N |  | 指定的平台 |
| `language` | `string` |  | N |  | the program language for the project |
| `langVm` | `abfuzz.LangVM` |  | N |  | 指定的JDK |
| `argument` | `mojo.core.Object` |  | N |  | 任务执行所需的参数 |
| `timeout` | `string` | `Duration` | N |  | 任务最长超时时间 |
| `isCommandOverride` | `boolean` |  | N |  | 命令能否被覆盖 |
| `highEnd` | `boolean` |  | N |  | 是否是高优先级任务 |
| `agentName` | `string` |  | N |  | 执行任务的agent name. |
| `state` | `string` |  | N |  | 任务的状态，只能是以下之一：created, running, stopped, finished, failed |
| `error` | `mojo.core.Error` |  | N |  | 任务失败后的错误信息 |
| `containers` | `Array<abfuzz.Container>` |  | N |  | 任务过程中产生的容器信息 |
| `createTime` | `string` | `Timestamp` | N |  | 任务的创建时间 |
| `updateTime` | `string` | `Timestamp` | N |  | 任务的更新时间 |


#### `abfuzz.Container`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `id` | `string` |  | N |  | the id of the container |
| `name` | `string` |  | N |  | the name of the container |


#### `abfuzz.LangVM`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | the name of the LangVM, lang_vm for java |
| `version` | `string` |  | N |  | the version of the LangVM |


#### `mojo.core.Any`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `@type` | `string` |  | Y |  |


#### `mojo.core.Error`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` | `ErrorCode` | N |  | The error code |
| `message` | `string` |  | N |  | A developer-facing error message, which should be in English. Anyuser-facing error message should be localized and sent in the[google.rpc.Status.details][google.rpc.Status.details] field, or localized by the client. |
| `details` | `Array<mojo.core.Any>` |  | N |  | A list of messages that carry the error details.  There is a common set ofmessage types for APIs to use. |


### 返回值

#### 返回对象
对象为空

## 查看异步执行的状态

### 请求路径
```http
GET /operation/v1/abfuzz/operations/{name}
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `name` | `string` |  | The name of the operation resource. |


### 返回值

#### 返回对象
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `name` | `string` |  | N |  | The server-assigned name, which is only unique within the same service thatoriginally returns it. If you use the default HTTP mapping, the`name` should be a resource name ending with `operations/{unique_id}`. |
| `metadata` | `mojo.core.Any` |  | N |  | Service-specific metadata associated with the operation.  It typicallycontains progress information and common metadata such as create time.Some services might not provide such metadata.  Any method that returns along-running operation should document the metadata type, if any. |
| `done` | `boolean` |  | N |  | If the value is `false`, it means the operation is still in progress.If `true`, the operation is completed, and either `error` or `response` isavailable. |
| `error` | `mojo.core.Error` |  | N |  | The operation result, which can be either an `error` or a valid `response`.If `done` == `false`, neither `error` nor `response` is set.If `done` == `true`, exactly one of `error` or `response` is set.The error result of the operation in case of failure or cancellation. |
| `response` | `mojo.core.Any` |  | N |  | The normal response of the operation in case of success.  If the originalmethod returns no data on success, such as `Delete`, the response is`google.protobuf.Empty`.  If the original method is standard`Get`/`Create`/`Update`, the response should be the resource.  For othermethods, the response should have the type `XxxResponse`, where `Xxx`is the original method name.  For example, if the original method nameis `TakeSnapshot()`, the inferred response type is`TakeSnapshotResponse`. |
| `createTime` | `string` | `Timestamp` | N |  | the create timestamp for the operation created. |
| `updateTime` | `string` | `Timestamp` | N |  | the updated timestamp for the operation when update the progression information. |


#### `mojo.core.Any`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `@type` | `string` |  | Y |  |


#### `mojo.core.Error`
| field | type | format | required | default | description |
|---|---|---|---|---|---|
| `code` | `string` | `ErrorCode` | N |  | The error code |
| `message` | `string` |  | N |  | A developer-facing error message, which should be in English. Anyuser-facing error message should be localized and sent in the[google.rpc.Status.details][google.rpc.Status.details] field, or localized by the client. |
| `details` | `Array<mojo.core.Any>` |  | N |  | A list of messages that carry the error details.  There is a common set ofmessage types for APIs to use. |


## 取消异步执行的任务

### 请求路径
```http
POST /operation/v1/abfuzz/operations/{name}:cancel
```


### 请求参数

#### Path 参数
| 参数名 | 参数类型 | 格式类型 | 说明 |
|---|---|---|---|
| `name` | `string` |  | The name of the operation resource to be cancelled. |


### 返回值

#### 返回对象
对象为空
