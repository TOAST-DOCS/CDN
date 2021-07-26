## Content Delivery > CDN > API v2.0 Guide 

This document describes Public API v2.0 of NHN Cloud CDN. 


## Common API Information 

### Domain

| Name              | Domain                                   |
| --------------- | ------------------------------------- |
| CDN Public API Domain | https://kr1-cdn.api.nhncloudservice.com |

### Prerequisites

Requires Appkey and Secretkey API, which are available in **URL & Appkey** on top right of the console.

### Common Request Information

#### Request Header

| Name            | Description                        |
| ------------- | ------------------------- |
| Authorization | SecretKey issued on a console |

#### Path Parameter 

In all APIs, the appKey must be specified in the path parameter.
* e.g.) /v2.0/appKeys/**{appKey}**/distributions

| Name     | Description                    |
| ------ | --------------------- |
| appKey | Appkey issued on console |

### Common Response Information 

#### Header 

Respond with **200 OK** to all API requests. For more details, see the header at the response body as below

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Success"
    }
}
```


[Field]

| Field                   | Type      | Description     |
| -------------------- | ------- | ------ |
| header               | Object  | Header area  |
| header.isSuccessful  | Boolean | Successful or not  |
| header.resultCode    | Integer | Result code  |
| header.resultMessage | String  | Result message |

#### CDN Status Codes

Below shows the status codes of CDN service, which are available at the query of service.

| Value         | Description                     |
| ---------- | ------------------------ |
| OPENING    | Starting service            |
| OPEN       | In service                 |
| MODIFYING  | Under modification         |
| RESUME     | Resumed                     |
| SUSPENDING | Under suspension            |
| SUSPEND    | Suspended                   |
| CLOSING    | Closing service              |
| CLOSE      | Closed                 |
| ERROR      | Error occurred while creating service  |


## Service API

### Create

#### Request


[URI]

| Method  | URI                                  |
| ---- | ------------------------------------ |
| POST | /v2.0/appKeys/{appKey}/distributions |


[Request Body]

```json
{
    "distributions" : [
    {
      "useOriginHttpProtocolDowngrade": false,
      "forwardHostHeader": "ORIGIN_HOSTNAME",
      "domainAlias": ["alias.test.net"],
      "description" : "sample-cdn",
      "useOriginCacheControl" : false,      
      "defaultMaxAge": 86400,
      "referrerType" : "BLACKLIST",      
      "referrers" : ["cloud.nhn.com"],
      "isAllowWhenEmptyReferrer" : true, 
      "origins" : [
        {
          "origin" : "static.origin.com",
          "originPath" : "/resources",       
          "httpPort": 80,
          "httpsPort": 443
        }
      ],
      "rootPathAccessControl" : {
          "enable": true,
          "controlType": "REDIRECT",
          "redirectPath": "/default.png",
          "redirectStatusCode": 302
      },
      "callback": {
          "httpMethod": "GET",
          "url": "http://test.callback.com/cdn?=appKey={appKey}&status={status}&domain={domain}"
      }
    }
  ]
}
```

[Field]

| Name                                   | Type    | Required | Default | Valid Range                   | Description                                                         |
| -------------------------------------- | ------- | --------- | ------ | --------------------------- | ------------------------------------------------------------ |
| distributions                          | List    | Required      |        |                              | List of CDN objects to create                                   |
| distributions[0].useOriginHttpProtocolDowngrade | Boolean  | Required     | false       | true/false         | Whether to enable settings to downgrade a request from HTTPS to HTTP when the request is made to origin server from CDN server, if the origin server can respond only via HTTP|
| distributions[0].forwardHostHeader     | String  | Required      | When domain alias setting is enabled: REQUEST_HOST_HEADER, When domain alias setting is not enabled: ORIGIN_HOSTNAME       | ORIGIN_HOSTNAME/REQUEST_HOST_HEADER   | Set host header to be delivered when CDN server requests content to origin server ("ORIGIN_HOSTNAME": Set as host name for origin server, "REQUEST_HOST_HEADER": Set as host header for client requests)| 
| distributions[0].useOriginCacheControl | Boolean | Required      |        | true/false                  | Cache expiration setting (true: Original setting, false: User-configured)   |
| distributions[0].referrerType          | String  | Required      |        | BLACKLIST/WHITELIST         | Referrer access management ("BLACKLIST": Blacklist, "WHITELIST": Whitelist) |
| distributions[0].referrers             | List    | Optional      |        |                           | List of regex referrer headers   |
| distributions[0].isAllowWhenEmptyReferrer | Boolean | Optional      | true      | true/false             | True/False for Content Access if Referer Header is Unavailable             |
| distributions[0].description           | String  | Optional      |        | Up to 255 characters                  | Description                                                         |
| distributions[0].domainAlias           | List    | Optional      |        |                           | List of domain aliases (Use personal or company-owned domain) |
| distributions[0].defaultMaxAge         | Integer | Optional      | 0      | 0~2,147,483,647             | Cache expiration time (seconds); default is 0 with 604,800 seconds.             |
| distributions[0].origins               | List    | Required      |        |                             | List of origin server objects                                      |
| distributions[0].origins[0].origin     | String  | Required      |        | Up to 255 characters                  | Origin server (domain or IP)                                     |
| distributions[0].origins[0].originPath | String  | Optional      |        | Up to 8192 characters                 | Lower paths of origin server (path must include /.)        |
| distributions[0].origins[0].httpPort   | Integer  | Optional      |        | See '[Table 2] Available Origin Server Port Number' of [Console User Guide > Origin Server](./console-guide/#_2) | HTTP protocol port of origin server(must enter either origins[0].httpPort or origins[0].httpsPort.)  |
| distributions[0].origins[0].httpsPort  | Integer  | Optional      |        | See '[Table 2] Available Origin Server Port Number' of[Console User Guide > Origin Server](./console-guide/#_2) | HTTPS protocol port of origin server (must enter either origins[0].httpPort or origins[0].httpsPort.) |
| distributions[0].rootPathAccessControl  | Object  | Optional      |        |                             | Setting the access control for the CDN service root path | 
| distributions[0].rootPathAccessControl.enable | Boolean | Required      | true      | true/false             | Whether the access control for the root path is abled (true)/disabled (false)           |
| distributions[0].rootPathAccessControl.controlType  | String  | Optional      |        | DENY, REDIRECT    | If enable is true, entry is required. The method of access control for root path ("DENY": reject approach, "REDIRECT": redirect to specified path)) | 
| distributions[0].rootPathAccessControl.redirectPath | String | Optional      |       |       |   If the controlType is “REDIRECT,” entry is required.( Please enter the path) including the redirection path for requesting root path.        |
| distributions[0].rootPathAccessControl.redirectStatusCode | Integer | Optional      |       | 301, 302, 303, 307             |  If the controlType is "REDIRECT,” entry is required. The HTTP Response Code transferred when redirecting           |
| distributions[0].callback              | Object  | Optional      |        |                             | Callback URL to receive processing result of CDN creation (callback setting is optional.) |
| distributions[0].callback.httpMethod   | String  | Required      |        | GET/POST/PUT                | HTTP method of callback                                           |
| distributions[0].callback.url          | String  | Required      |        | Up to 1024 characters                 | Callback URL                                                     |




#### Response


[Response Body]

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "distributions": [
        {
            "domain": "djwbjvqa.toastcdn.net",
            "domainAlias": [
                "alias.test1.net"
            ],
            "region": "GLOBAL",
            "description": "sample-cdn",
            "status": "OPENING",
            "defaultMaxAge": 0,
            "referrerType": "BLACKLIST",
            "referrers": [
                "cloud.nhn.com"
            ],
            "isAllowWhenEmptyReferrer": true,
            "useOriginCacheControl": true,
            "origins": [
                {
                    "origin": "static.origin.com",
                    "originPath": "/resources",
                    "httpPort": 80,
                    "httpsPort": 443
                }
            ],
            "forwardHostHeader": "ORIGIN_HOSTNAME",
            "useOriginHttpProtocolDowngrade": false,
            "rootPathAccessControl" : {
                "enable": true,
                "controlType": "REDIRECT",
                "redirectPath": "/default.png",
                "redirectStatusCode": 302
            },
            "callback": {
                "httpMethod": "GET",
                "url": "http://test.callback.com/cdn?=appKey={appKey}&status={status}&domain={domain}"
            }
        }
    ]
}
```


[Field]

| Field                                   | Type    | Description                                                         |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | Header area                                                   |
| header.isSuccessful                    | Boolean | Successful or not                                                    |
| header.resultCode                      | Integer | Result code                                                    |
| header.resultMessage                   | String  | Result message                                                  |
| distributions                          | List    | List of created CDN objects                                    |
| distributions[0].domain                | String  | Name of created domain (service)                               |
| distributions[0].domainAlias           | List    | List of domain aliases (use personal or company-owned domain)              |
| distributions[0].region                | String  | Service region ("GLOBAL": Global service)            |
| distributions[0].description           | String  | Description                                                     |
| distributions[0].status                | String  | CDN status code (see [Table] CDN Status Code)                                 |
| distributions[0].defaultMaxAge         | Integer | Cache expiration time (seconds)                                           |
| distributions[0].referrerType          | String  | Referrer access management ("BLACKLIST": Blacklist, "WHITELIST": Whitelist) |
| distributions[0].referrers             | List    | List of regex referrer headers                                   |
| distributions[0].isAllowWhenEmptyReferrer | Boolean | True/False for Content Access if Referer Header is Unavailable |
| distributions[0].useOriginCacheControl | Boolean  | Whether to use origin server setting or not (true: Enable origin server setting, false: User-configured setting) |
| distributions[0].origins               | List    | List of origin server objects                                      |
| distributions[0].origins[0].origin     | String  | Origin server (domain or IP)                                      |
| distributions[0].origins[0].originPath | String  | Lower paths of origin server                                           |
| distributions[0].origins[0].httpPort   | Integer | HTTP protocol port of origin server                                               |
| distributions[0].origins[0].httpsPort  | Integer | HTTPS protocol port of origin server                                              |
| distributions[0].useOriginHttpProtocolDowngrade | Boolean |Whether to enable settings to downgrade a request from HTTPS to HTTP when the request is made to origin server from CDN server, if the origin server can respond only via HTTP  |
| distributions[0].forwardHostHeader     | String  | Set host header to be delivered when CDN server requests content to origin server ("ORIGIN_HOSTNAME": Set as host name for origin server, "REQUEST_HOST_HEADER": Set as host header for client requests)|
| distributions[0].rootPathAccessControl  | Object  | Setting the access control for the CDN service root path | 
| distributions[0].rootPathAccessControl.enable | Boolean | Whether the access control for the root path is abled (true)/disabled (false)          |
| distributions[0].rootPathAccessControl.controlType  | String  | If enable is true, entry is required. The method of access control for root path ("DENY": reject approach, "REDIRECT": redirect to specified path) | 
| distributions[0].rootPathAccessControl.redirectPath | String | If the controlType is "REDIRECT,” entry is required. (Please enter the path ) including the redirection path for requesting root path.        |
| distributions[0].rootPathAccessControl.redirectStatusCode | Integer | If the controlType is "REDIRECT,” entry is required. The HTTP Response Code transferred when redirecting          |
| distributions[0].callback              | Object  | Callback to receive service creation result                 |
| distributions[0].callback.httpMethod   | String  | HTTP method of callback                                           |
| distributions[0].callback.url          | String  | Callbak URL                                                     |



### Get

#### Request


[URI]

| Method  | URI                                  |
| ---- | ------------------------------------ |
| GET  | /v2.0/appKeys/{appKey}/distributions |


[Parameter]

| Name   | Type   | Required | Valid Range     | Description                         |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| domain | String | Optional      | Up to 255 characters    | Domain to get (service name)   |
| status | String | Optional      | CDN status code | CDN Status Code (see [Table] CDN Status Code) |

[Example]
```
curl -X GET "https://kr1-cdn.api.nhncloudservice.com/v2.0/appKeys/{appKey}/distributions?domain={domain}" \
 -H "Authorization: {secretKey}" \
 -H "Content-Type: application/json"
```

#### Response


[Response Body]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
      "isSuccessful" :  true
    },
    "domain" :  "lhcsxuo0.toastcdn.net",
    "domainAlias" :  ["test.domain.com"],
    "region" :  "GLOBAL",
    "status" : "OPEN",
    "defaultMaxAge" : 86400,
    "status" :  "OPENING",
    "referrerType" :  "BLACKLIST",
    "referrers" :  ["test.com"],    
    "isAllowWhenEmptyReferrer": true,
    "useOriginCacheControl" :  false,
    "origins" : [
        {
            "origin" :  "static.resource.com",
            "httpPort" :  80,
            "httpsPort" : 443
        }
    ],
    "forwardHostHeader": "ORIGIN_HOSTNAME",
    "useOriginHttpProtocolDowngrade": false,    
    "rootPathAccessControl" : {
        "enable": true,
        "controlType": "REDIRECT",
        "redirectPath": "/default.png",
        "redirectStatusCode": 302
    }, 
    "callback": {
        "httpMethod": "GET",
        "url": "http://test.callback.com/cdn?=appKey={appKey}&status={status}&domain={domain}"
    }
}
```


[Field]

| Field                                   | Type    | Description                                                         |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | Header area                                                    |
| header.isSuccessful                    | Boolean | Successful or not                                                   |
| header.resultCode                      | Integer | Result code                                                   |
| header.resultMessage                   | String  | Result message                                                  |
| distributions                          | List    | List of created CDN objects                                     |
| distributions[0].domain                | String  | Domain name (service name)                                     |
| distributions[0].domainAlias           | List  | List of domain aliases (use personal or company-owned domain)                                                  |
| distributions[0].region                | String  | Service region ("GLOBAL": Global service)             |
| distributions[0].status                | String  | CDN status code (See [Table] CDN Status Code)                                 |
| distributions[0].defaultMaxAge         | Integer  | Cache expiration time (seconds)                                          |
| distributions[0].referrerType          | String  | Referrer access management ("BLACKLIST": Blacklist, "WHITELIST": Whitelist) |
| distributions[0].referrers             | List    | List of regex referrer headers                                 |
| distributions[0].isAllowWhenEmptyReferrer | Boolean | True/False for Content Access if Referer Header is Unavailable |
| distributions[0].useOriginCacheControl | Boolean | Whether to enable origin server setting or not (true: Enable origin server setting, false: User-configured) |
| distributions[0].origins               | List    | List of origin server objects                                      |
| distributions[0].origins[0].origin     | String  | Origin server (domain or IP)                                      |
| distributions[0].origins[0].originPath | String  | Lower paths of origin server                                          |
| distributions[0].origins[0].httpPort   | Integer | HTTP protocol port of origin server                                  |
| distributions[0].origins[0].httpsPort  | Integer | HTTPS protocol port of origi server                                 |
| distributions[0].forwardHostHeader     | String  | Callback to receive service deployment result                         |
| distributions[0].useOriginHttpProtocolDowngrade | Boolean | Whether to enable settings to downgrade a request from HTTPS to HTTP when the request is made to origin server from CDN server, if the origin server can respond only via HTTP |
| distributions[0].forwardHostHeader     | String  | Set host header to be delivered when CDN server requests content to origin server ("ORIGIN_HOSTNAME": Set as host name for origin server, "REQUEST_HOST_HEADER": Set as host header for client requests) |
| distributions[0].rootPathAccessControl  | Object  | Setting the access control for the CDN service root path | 
| distributions[0].rootPathAccessControl.enable | Boolean | Whether the access control for the root path is abled (true)/disabled (false)           |
| distributions[0].rootPathAccessControl.controlType  | String  | If enable is true, entry is required. The method of access control for root path ("DENY": reject approach, "REDIRECT": redirect to specified path) | 
| distributions[0].rootPathAccessControl.redirectPath | String |  If the controlType is "REDIRECT,” entry is required. (Please enter the path) including the redirection path for requesting root path.        |
| distributions[0].rootPathAccessControl.redirectStatusCode | Integer | If the controlType is "REDIRECT,” entry is required. The HTTP Response Code transferred when redirectin          |
| distributions[0].callback              | Object  | Callback to receive service deployment result                         |
| distributions[0].callback.httpMethod   | String  | HTTP method of callback                                           |
| distributions[0].callback.url          | String  | Callback URL                                                     |


### Modify

#### Request


[URI]

| Method  | URI                                  |
| ---- | ------------------------------------ |
| PUT  | /v2.0/appKeys/{appKey}/distributions |


[Request Body]

```json
{
    "distributions" : [
    {
      "domain" : "sample.toastcdn.net",
      "useOriginCacheControl" : false,
      "defaultMaxAge": 86400,
      "referrerType" : "BLACKLIST",
      "referrers" : ["test.com"],
      "isAllowWhenEmptyReferrer": true,
      "origins" : [
          {
              "origin" : "static.resource.com",
              "httpPort" : 80,
              "httpsPort" : 443,
              "originPath" : "/latest/resources"
          }
      ],
      "useOriginHttpProtocolDowngrade": false,
      "forwardHostHeader": "ORIGIN_HOSTNAME",
      "rootPathAccessControl" : {
          "enable": true,
          "controlType": "REDIRECT",
          "redirectPath": "/default.png",
          "redirectStatusCode": 302
      },
      "callback": {
          "httpMethod": "GET",
          "url": "http://test.callback.com/cdn?=appKey={appKey}&status={status}&domain={domain}"
      },
      "description" : "change contents"        
    }
  ]
}
```


[Field]

| Name                  | Type    | Required | Default | Valid Range                                                    | Description                                                        |
| --------------------- | ------- | --------- | ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| domain                | String  | Required |        | Up to 255 characters                              | Domain (service name) to modify                       |
| useOriginCacheControl | Boolean | Required      |        | true/false                              | Cache expiration setting (true: Enable origin server setting, false: User-configured)      |
| referrerType          | String  | Required      |        | BLACKLIST/WHITELIST                     | Referrer access management ("BLACKLIST": Blacklist, "WHITELIST": Whitelist) |
| referrers             | List    | Optional    |        |                                           | List of regex referrer headers |
| isAllowWhenEmptyReferrer | Boolean | Optional      | true      | true/false             | True/False for Content Access if Referer Header is Unavailable             |
| description           | String  | Optional    |        | Up to 255 characters                      | Description                                                         |
| domainAlias           | List    | Optional    |        | Up to 255 characters                      | Domain alias (Use personal or company-owned domain) |
| defaultMaxAge         | Integer | Optional      | 0      | 0~2,147,483,647                         | Cache expiration time (seconds), Default is 0 with 604,800 seconds.              |
| origins               | List    | Required      |        |                                         | Origin server                                                   |
| origins[0].origin     | String  | Required     |        | Up to 255 characters                     | Origin server (domain or IP)                                      |
| origins[0].originPath | String  | Optional      |        | UP to 8192 characters                   | Lower paths of origin server                                          |
| origins[0].httpPort   | Integer  | Optional      |       |See '[Table 2] Available Origin Server Port Number' of [Console User Guide > Origin Server](./console-guide/#_2) | HTTP protocol port of origin server(must enter either origins[0].httpPort or origins[0].httpsPort.) |
| origins[0].httpsPort  | Integer  | Optional      |        |See '[Table 2] Available Origin Server Port Number' of[Console User Guide > Origin Server](./console-guide/#_2) | HTTPS protocol port of origin server (must enter either origins[0].httpPort or origins[0].httpsPort.)  |
| useOriginHttpProtocolDowngrade | Boolean  | Required     | false       | true/false         | Whether to enable settings to downgrade a request from HTTPS to HTTP when the request is made to origin server from CDN server, if the origin server can respond only via HTTP |
| forwardHostHeader     | String  | Required      | When domain alias setting is enabled: REQUEST_HOST_HEADER, When domain alias setting is not enabled: ORIGIN_HOSTNAME       | ORIGIN_HOSTNAME/REQUEST_HOST_HEADER   | Set host header to be delivered when CDN server requests content to origin server ("ORIGIN_HOSTNAME": Set as host name for origin server, "REQUEST_HOST_HEADER": Set as host header for client requests)|
| useOrigin             | String  | Required      |        | Y/N                                                          | Cache expiration setting (Y: Enable original setting, "N":User-configured)      |
| rootPathAccessControl  | Object  | Optional |  |  | Setting the access control for the CDN service root path | 
| rootPathAccessControl.enable | Boolean | Required | false | true/false | Whether the access control for the root path is abled (true)/disabled (false)          |
| rootPathAccessControl.controlType  | String  | Optional |  | DENY, REDIRECT | If enable is true, entry is required. The method of access control for root path ("DENY": reject approach, "REDIRECT": redirect to specified path) | 
| rootPathAccessControl.redirectPath | String | Optional |  | | If the controlType is "REDIRECT,” entry is required. (Please enter the path) including the redirection path for requesting root path.        |
| rootPathAccessControl.redirectStatusCode | Integer | Optional | | 301, 302, 303, 307 |If the controlType is "REDIRECT,” entry is required. The HTTP Response Code transferred when redirecting          |
| callback              | Object  | Optional      |        | Callback URL to receive CDN service deployment result (Callback setting is optional.) |                                                              |
| callback.httpMethod   | String  | Required      |        | GET/POST/PUT                                                 | HTTP method of callback                                           |
| callback.url          | String  | Required      |        | Up to 1024 characters                                       | Callback URL                                                     |

- Default of forwardHostHeader is REQUEST_HOST_HEADER, if domainAlias is enabled; or REQUEST_HOST_HEADER, if not enabled.  

#### Response


[Rseponse Body]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    }
}
```


[Field]

| Field                   | Type      | Description     |
| -------------------- | ------- | ------ |
| header               | Object  | Header area  |
| header.isSuccessful  | Boolean | Successful or not  |
| header.resultCode    | Integer | Result code  |
| header.resultMessage | String  | Result message |

### Delete

#### Request


[URI]

| Method    | URI                                  |
| ------ | ------------------------------------ |
| DELETE | /v2.0/appKeys/{appKey}/distributions |


[Request Body]

```json
{
    "domains" : [
        "lhcsxuo0.toastcdn.net"
    ]
}
```


[Field]

| Name      | Type     | Required | Default  | Valid Range | Description                    |
| ------- | ------ | ----- | ---- | ----- | --------------------- |
| domains | String | Required    |      |       | Domains to delete; many domains allowed  |

**\* With the input of many domains, all corresponding services are closed.**

#### Response


[Response Body]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    }
}
```


[Field]

| Field                   | Type      | Description     |
| -------------------- | ------- | ------ |
| header               | Object  | Header area  |
| header.isSuccessful  | Boolean | Successful or not  |
| header.resultCode    | Integer | Result code  |
| header.resultMessage | String  | Result message |


## Purge Cache API

### Purge Cache -  ITEM (particular file type)

#### Request

[URI]

| Method  | URI                           |
| ---- | ----------------------------- |
| POST | /v2.0/appKeys/{appKey}/purge/item |


[Request Body]

```json
{
	"domain": "sample.toastcdn.net",
	"purgeList":["http://sample.toastcdn.net/img_01.png",
  "http://sample.toastcdn.net/img_02.png"]
}
```


[Field]

| Name     | Type   | Required | Default | Valid Range            | Description                                                  |
| --------- | ------ | --------- | ------ | --------------------- | ------------------------------------------------------------ |
| domain    | String | Required      |        | Up to 255 characters     | Domain (service) name to be redeployed                      |
| purgeList | List | Required     |        |                       | List o URL for redeployment |

#### Response

[Response Body]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    }
}
```


[Field]

| Field                   | Type      | Description        |
| -------------------- | ------- | --------- |
| header               | Object  | Header area     |
| header.isSuccessful  | Boolean | Successful or not     |
| header.resultCode    | Integer | Result code     |
| header.resultMessage | String  | Result message    |

### Purge Cache -  ALL (All file types)

#### Request

[URI]

| Method  | URI                           |
| ---- | ----------------------------- |
| POST | /v2.0/appKeys/{appKey}/purge/all |


[Request Body]

```json
{
	"domain": "sample.toastcdn.net"
}
```


[Field]

| Name      | Type  | Required | Default | Valid Range             | Description                                                       |
| --------- | ------ | --------- | ------ | --------------------- | ------------------------------------------------------------ |
| domain    | String | Required      |        | Up to 255 characters            | Domain (service) name to be redeployed         |

#### Response

[Response Body]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    }
}
```


[Field]

| Field                   | Type      | Description        |
| -------------------- | ------- | --------- |
| header               | Object  | Header area     |
| header.isSuccessful  | Boolean | Successful or not     |
| header.resultCode    | Integer | Result code     |
| header.resultMessage | String  | Result message    |

- Request for cache redeployment may fail within an hour after CDN service is newly created. If failure continues, contact Customer Center. 
- Capacity restriction policy exists for Purge APIs. For more details, see Restriction of Cache Redeployment Capacity of [Console User Guide > CDN Cache Redeployment](./console-guide/#cdn_4)].

### Get Purge Cache
- Purge of cache via API v2.0 shall be executed in high speed, allowing a request to be completed within seconds; hence, get cache redeployment status API shall not be provided. 

## Callback Response
If CDN service has a callback function, configured callback URL is called, once a request to Create, Modify, Suspend, Resume, Delete, or Change is completed. 
When a callback is called, the request body includes CDN service setting information as follows: 

[Response Body]
```json
{
  "header" : {
    "resultCode" :  0,
    "resultMessage" :  "SUCCESS",
    "isSuccessful" :  true
  },
  "distribution":{
      "appKey": "wXDdIjJRcZDtY9F7",
      "domain" :  "lhcsxuo0.toastcdn.net",
      "domainAlias" :  ["test.domain.com"],
      "region" :  "GLOBAL",
      "status" : "OPEN",
      "defaultMaxAge" : 86400,
      "status" :  "OPENING",
      "referrerType" :  "BLACKLIST",
      "referrers" :  ["test.com"],    
      "isAllowWhenEmptyReferrer" : true,
      "useOriginCacheControl" :  false,
      "createTime" : 1498613094692,
      "deleteTime": 1498613094692,
      "origins" : [
          {
              "origin" :  "static.resource.com",
              "httpPort" :  80,
              "httpsPort" : 443
          }
      ],
      "forwardHostHeader": "ORIGIN_HOSTNAME",
      "useOriginHttpProtocolDowngrade": false, 
      "rootPathAccessControl" : {
          "enable": true,
          "controlType": "REDIRECT",
          "redirectPath": "/default.png",
          "redirectStatusCode": 302
      },
      "callback": {
          "httpMethod": "GET",
          "url": "http"
      }
  }
}
```

[Field]

| Field                                   | Type    | Description                                                         |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | Header area                                                    |
| header.isSuccessful                    | Boolean | Successful or not                                                    |
| header.resultCode                      | Integer | Result code                                                    |
| header.resultMessage                   | String  | Result message                                                  |
| distribution                          | Object    | CDN objects completed with changes                                   |
| distribution.appKey                   | String    | Appkey                                  |
| distribution.domain                | String  | Domain name (Service name)                                     |
| distribution.domainAlias           | List  | List of domain aliases (use personal or company-owned domain)                                 |
| distribution.region                | String  | Service region ("GLOBAL": Global service)             |
| distribution.status                | String  | CDN status code (See [Table] CDN Status Code)                                 |
| distribution.defaultMaxAge         | Integer  | Cache expiration time (seconds)                                           |
| distribution.referrerType          | String  | Referrer access management ("BLACKLIST": Blacklist, "WHITELIST": Whitelist) |
| distribution.referrers             | List    | List of regex referrer headers                                 |
| distributions.isAllowWhenEmptyReferrer | Boolean | True/False for Content Access if Referer Header is Unavailable |
| distribution.useOriginCacheControl | Boolean | Whether to enable origin server setting (true: Enable origin server setting, false: User-configured) |
| distribution.createTime            | DateTime | Date and time of creation                                         |
| distribution.deleteTime            | DateTime | Date and time of deletion                                         |
| distribution.origins               | List    | List of origin server objects                                      |
| distribution.origins[0].origin     | String  | Origin server (domain or IP)                                      |
| distribution.origins[0].originPath | String  | Lower paths of origin server                                          |
| distribution.origins[0].httpPort   | Integer | HTTP protocol port of origin server                                       |
| distribution.origins[0].httpsPort  | Integer | HTTPS protocol port of origin server                                      |
| distribution.forwardHostHeader     | String  | Callback to receive service deployment results                        |
| distribution.useOriginHttpProtocolDowngrade | Boolean | Whether to enable settings to downgrade a request from HTTPS to HTTP when the request is made to origin server from CDN server, if the origin server can respond only via HTTP |
| distribution.forwardHostHeader     | String  | Set host header to be delivered when CDN server requests content to origin server ("ORIGIN_HOSTNAME": Set as host name for origin server, "REQUEST_HOST_HEADER": Set as host header for client requests) |
| distribution.rootPathAccessControl  | Object  | Setting the access control for the CDN service root path | 
| distribution.rootPathAccessControl.enable | Boolean | Whether the access control for the root path is abled (true)/disabled (false)          |
| distribution.rootPathAccessControl.controlType  | String  | If enable is true, entry is required. The method of access control for root path ("DENY": reject approach, "REDIRECT": redirect to specified path) | 
| distribution.rootPathAccessControl.redirectPath | String | If the controlType is "REDIRECT,” entry is required. (Please enter the path) including the redirection path for requesting root path.        |
| distribution.rootPathAccessControl.redirectStatusCode | Integer | If the controlType is "REDIRECT,” entry is required. The HTTP Response Code transferred when redirecting         |
| distribution.callback              | Object  | Callback to receive service deployment result                        |
| distribution.callback.httpMethod   | String  | HTTP method of callback                                           |
| distribution.callback.url          | String  | Callback URL                                                     |
