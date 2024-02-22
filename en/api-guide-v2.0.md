## Content Delivery > CDN > API v2.0 Guide

This document describes Public API v2.0 of NHN Cloud CDN.

## Common API Information

### Domain

| Name              | Domain                                   |
| --------------- | ------------------------------------- |
| CDN Public API Domain | https://kr1-cdn.api.nhncloudservice.com |

### Prerequisites

To use the API, you need Appkey and SecretKey.
The Appkey and SecretKey can be found in the **URL & Appkey** menu in the upper right corner of the console.

### Common Request Information

#### Request Header

| Name            | Description                        |
| ------------- | ------------------------- |
| Authorization | SecretKey issued from the console |

#### Path Parameter

In all APIs, the appKey must be specified in the path parameter.
* Example: /v2.0/appKeys/**{appKey}**/distributions

| Name     | Description                    |
| ------ | --------------------- |
| appKey | Appkey issued from the console |

### Common Response Information

#### Header

The API responds with **200 OK** to all API requests. For more details, see the header at the response body as below:

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

The following shows the status codes of CDN service, which are available at the query of service.

| Value         | Description                     |
| ---------- | ------------------------ |
| OPENING    | Starting service           |
| OPEN       | In service                |
| MODIFYING  | Under modification                  |
| RESUME     | Resumed                     |
| SUSPENDING | Under suspension             |
| SUSPEND    | Suspended                     |
| CLOSING    | Closing service             |
| CLOSE      | Closed                |
| ERROR      | Error occurred while creating service |

#### Certificate Issuance Status Codes

The following are status codes that indicate the certificate issuance status of the domain. You can check the issuance status when querying the certificate.

| Value         | Description                     |
| ---------- | ------------------------ |
| PENDING_NEW        | Issuance of a new certificate has been requested, and processing is pending   |
| PENDING_CANCEL     | The issuance of a certificate has been requested to be cancelled, and domain validation cancellation is pending   |
| PENDING_DELETE     | The issued certificate has been requested to be deleted, and processing is pending  |
| PENDING_EXPIRE     | The issued certificate has expired, and expiration is pending  |
| VALIDATED          | Domain validated                     |
| DEPLOYED           | Certificate deployed                     |
| WAITING_VALIDATION | Waiting for domain validation                  |
| CANCELED           | Domain validation canceled                 |
| DELETED            | Domain certificate deleted               |
| EXPIRED            | Domain certificate expired                   |


## Service API

### Create a Service

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
      "cacheType": "BYPASS",    
      "defaultMaxAge": 86400,
      "cacheKeyQueryParam": "INCLUDE_ALL",
      "referrerType" : "BLACKLIST",     
      "referrers" : ["cloud.nhn.com"],
      "isAllowWhenEmptyReferrer" : true,
      "isAllowPost" : true,
      "isAllowPut" : false,
      "isAllowPatch" : true,
      "isAllowDelete" : false,
      "useLargeFileOptimization" : false,
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
      "modifyOutgoingResponseHeaderControl" : {
          "enable": true,
          "headerList": [
              {
                  "action": "ADD",
                  "standardHeaderName": "OTHER",
                  "customHeaderName": "custom-header-name",
                  "headerValue": "custom-header-value"
              },
              {
                  "action": "MODIFY",
                  "standardHeaderName": "ACCESS_CONTROL_ALLOW_ORIGIN",
                  "headerValue": "*"
              }            
          ]          
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

| Name                                                                                    | Type      | Required | Default         | Valid Range                                                                 | Description                                                                                                                        |
|---------------------------------------------------------------------------------------|---------|-------|-------------|-----------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| distributions                                                                         | List    | Required    |             |                                                                       | List of CDN objects to create                                                                                                          |
| distributions[0].useOriginHttpProtocolDowngrade                                       | Boolean | Required    | false       | true/false                                                            | Whether to enable settings to downgrade a request from HTTPS to HTTP when the request is made to origin server from CDN server, if the origin server can respond only via HTTP                                     |
| distributions[0].forwardHostHeader                                                    | String  | Required    |             | ORIGIN_HOSTNAME<br/>REQUEST_HOST_HEADER                               | Set the host header to be forwarded by the CDN server when requesting content to the origin server ("ORIGIN_HOSTNAME": Set to the host name of the origin server, "REQUEST_HOST_HEADER": Set to the host header of the client request) |
| distributions[0].useOriginCacheControl                                                | Boolean | Optional    |             | true/false                                                            | Set cache expiration (true: use origin server settings, false: use user settings). One of useOriginCacheControl or cacheType must be entered.                      |
| distributions[0].cacheType                                                            | String  | Optional    |             | BYPASS, NO_STORE                                                      | Set cache type. One of useOriginCacheControl or cacheType must be entered.                                                           |
| distributions[0].referrerType                                                         | String  | Required    |             | BLACKLIST/WHITELIST                                                   | Referrer access management ("BLACKLIST": Blacklist, "WHITELIST": Whitelist)                                                                        |
| distributions[0].referrers                                                            | List    | Optional    |             |                                                                       | List of regex referrer headers                                                                                                      |
| distributions[0].isAllowWhenEmptyReferrer                                             | Boolean | Optional    | true        | true/false                                                            | Whether to allow (true) or deny (false) access to content when there is no referer header                                                                                |
| distributions[0].isAllowPost                                                          | Boolean | Optional    | false       | true/false                                                            | Whether to allow (true)/deny (false) POST method                                                                                            |
| distributions[0].isAllowPut                                                           | Boolean | Optional    | false       | true/false                                                            | Whether to allow (true)/deny (false) PUT method                                                                                             |
| distributions[0].isAllowPatch                                                         | Boolean | Optional    | false       | true/false                                                            | Whether to allow (true)/deny (false) PATCH method                                                                                           |
| distributions[0].isAllowDelete                                                        | Boolean | Optional    | false       | true/false                                                            | Whether to allow (true)/deny (false) DELETE method                                                                                          |
| distributions[0].useLargeFileOptimization                                             | Boolean | Optional    | false       | true/false                                                            | Whether to use the large file optimization setting                                                                                                       |
| distributions[0].description                                                          | String  | Optional    |             | Up to 255 characters                                                               | Description                                                                                                                        |
| distributions[0].domainAlias                                                          | List    | Optional    |             |                                                                       | List of domain aliases (using domains owned by individuals or companies)                                                                                           |
| distributions[0].defaultMaxAge                                                        | Integer | Optional    | 0           | 0~2,147,483,647                                                       | Cache expiration time (seconds), the default value 0 is 604,800 seconds.                                                                                          |
| distributions[0].cacheKeyQueryParam                                                   | String  | Optional    | INCLUDE_ALL | INCLUDE_ALL/EXCLUDE_ALL                                               | Set whether to include the request query string in the cache key ("INCLUDE_ALL": Include all, "EXCLUDE_ALL": Exclude all)                                                     |
| distributions[0].origins                                                              | List    | Required    |             |                                                                       | List of origin server objects                                                                                                             |
| distributions[0].origins[0].origin                                                    | String  | Required    |             | Up to 255 characters                                                               | Origin server (domain or IP)                                                                                                          |
| distributions[0].origins[0].originPath                                                | String  | Optional    |             | Up to 8192 characters                                                              | Sub-path of the origin server (Enter a path including /.)                                                                                          |
| distributions[0].origins[0].httpPort                                                  | Integer | Optional    |             | See '[Table 2] Available Origin Server Port Numbers' of [Console User Guide > Origin Server](./console-guide/#origin-server) | HTTP protocol port of the origin server (one of origins[0].httpPort and origins[0].httpsPort must be entered.)                                         |
| distributions[0].origins[0].httpsPort                                                 | Integer | Optional    |             | See '[Table 2] Available Origin Server Port Numbers' of [Console User Guide > Origin Server](./console-guide/#origin-server) | HTTPS protocol port of the origin server (one of origins[0].httpPort and origins[0].httpsPort must be entered.)                                        |
| distributions[0].rootPathAccessControl                                                | Object  | Optional    |             |                                                                       | Set the access control for the CDN service root path                                                                                               | 
| distributions[0].rootPathAccessControl.enable                                         | Boolean | Required    | true        | true/false                                                            | Whether the access control for the root path is enabled (true) or disabled (false)                                                                                    |
| distributions[0].rootPathAccessControl.controlType                                    | String  | Optional    |             | DENY, REDIRECT                                                        | Required if enable is true. The access control method for the root path. ("DENY": deny access, "REDIRECT": redirect to the specified path)                                      | 
| distributions[0].rootPathAccessControl.redirectPath                                   | String  | Optional    |             |                                                                       | Required if controlType is "REDIRECT". The path to redirect requests for the root path to. (Enter a path including /.)                                           |
| distributions[0].rootPathAccessControl.redirectStatusCode                             | Integer | Optional    |             | 301, 302, 303, 307                                                    | Required when controlType is "REDIRECT". The HTTP response code sent when redirecting.                                                                 |
| distributions[0].modifyOutgoingResponseHeaderControl                                  | Object  | Optional    |             |                                                                       | Setting to add, modify, and delete HTTP response header from CDN                                                                                         |
| distributions[0].modifyOutgoingResponseHeaderControl.enable                           | Boolean | Required    | true        | true/false                                                            | Whether to use the settings that add/change/delete HTTP response headers                                                                          |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList                       | List    | Optional    |         |                                                                       | HTTP response header list                                                                                                             |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].action             | String  | Optional    |         | ADD, MODIFY, DELETE                                                   | HTTP response header change methods                                                                                                          |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].standardHeaderName | String  | Optional    |         | ACCESS_CONTROL_ALLOW_CREDENTIALS<br/>ACCESS_CONTROL_ALLOW_HEADERS<br/>ACCESS_CONTROL_ALLOW_METHODS<br/>ACCESS_CONTROL_ALLOW_ORIGIN<br/>ACCESS_CONTROL_EXPOSE_HEADERS<br/>ACCESS_CONTROL_MAX_AGE<br/>CACHE_CONTROL<br/>CONTENT_DISPOSITION<br/>CONTENT_TYPE<br/>P3P<br/>PRAGMA<br/>OTHER | General HTTP response header name                                                                                                          |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].customHeaderName   | String  | Optional    |         |                                                      | Required if standardHeaderName is "OTHER". Custom HTTP response header name                                                               |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].headerValue        | String  | Required    |         |                                                      | HTTP response header value                                                                                                              |
| distributions[0].callback                                                             | Object  | Optional    |             |                                                                       | Callback URL to receive the processing result of CDN creation (callback setting is optional.)                                                                               |
| distributions[0].callback.httpMethod                                                  | String  | Required    |             | GET/POST/PUT                                                          | HTTP method of callback                                                                                                              |
| distributions[0].callback.url                                                         | String  | Required    |             | Up to 1024 characters                                                              | Callback URL                                                                                                                    |

- The default value of forwardHostHeader is REQUEST_HOST_HEADER if domainAlias is set, or ORIGIN_HOSTNAME otherwise. 



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
            "cacheKeyQueryParam": "INCLUDE_ALL",
            "referrerType": "BLACKLIST",
            "referrers": [
                "cloud.nhn.com"
            ],
            "isAllowWhenEmptyReferrer" : true,
            "isAllowPost" : true,
            "isAllowPut" : false,
            "isAllowPatch" : true,
            "isAllowDelete" : false,
            "useLargeFileOptimization" : false,
            "useOriginCacheControl": true,
            "cacheType": "BYPASS",
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
            "modifyOutgoingResponseHeaderControl": {
                "enable": true,
                "headerList": [
                    {
                        "action": "ADD",
                        "standardHeaderName": "OTHER",
                        "customHeaderName": "custom-header-name",
                        "headerValue": "custom-header-value"
                    },
                    {
                        "action": "MODIFY",
                        "standardHeaderName": "ACCESS_CONTROL_ALLOW_ORIGIN",
                        "headerValue": "*"
                    }
                ]
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

| Field                                   | Type    | Description                                                       |
| -------------------------------------- | ------- | ---------------------------------------------------------- |
| header                                 | Object  | Header area                                                  |
| header.isSuccessful                    | Boolean | Successful or not                                                  |
| header.resultCode                      | Integer | Result code                                                  |
| header.resultMessage                   | String  | Result message                                                |
| distributions                          | List    | List of created CDN objects                                 |
| distributions[0].domain                | String  | Name of the created domain (service)                                 |
| distributions[0].domainAlias           | List    | List of domain aliases (using domains owned by individuals or companies)            |
| distributions[0].region                | String  | Service region ("GLOBAL": Global service)          |
| distributions[0].description           | String  | Description                                                       |
| distributions[0].status                | String  | CDN status code (See [Table] CDN Status Codes)                               |
| distributions[0].defaultMaxAge         | Integer | Cache expiration time (seconds)                                         |
| distributions[0].cacheKeyQueryParam    | String  | Set whether to include the request query string in the cache key ("INCLUDE_ALL": Include all, "EXCLUDE_ALL": Exclude all) |
| distributions[0].referrerType          | String  | Referrer access management ("BLACKLIST": Blacklist, "WHITELIST": Whitelist) |
| distributions[0].referrers             | List    | List of regex referrer headers                                |
| distributions[0].isAllowWhenEmptyReferrer | Boolean | Whether to allow (true) or deny (false) access to content when there is no referer header |
| distributions[0].isAllowPost | Boolean | Whether to allow (true)/deny (false) POST method           |
| distributions[0].isAllowPut | Boolean | Whether to allow (true)/deny (false) PUT method           |
| distributions[0].isAllowPatch | Boolean | Whether to allow (true)/deny (false) PATCH method           |
| distributions[0].isAllowDelete | Boolean | Whether to allow (true)/deny (false) DELETE method           |
| distributions[0].useLargeFileOptimization | Boolean | Whether to use the large file optimization setting   |
| distributions[0].useOriginCacheControl | Boolean | Whether to use origin server setting or not (true: Use the origin server setting, false: User-configured setting) |
| distributions[0].cacheType             | String  | Cache type settings                                        |
| distributions[0].origins               | List    | List of origin server objects                                    |
| distributions[0].origins[0].origin     | String  | Origin server (domain or IP)                                    |
| distributions[0].origins[0].originPath | String  | Sub-path of origin server                                        |
| distributions[0].origins[0].httpPort   | Integer | HTTP protocol port of origin server                                             |
| distributions[0].origins[0].httpsPort  | Integer | HTTPS protocol port of origin server                                             |
| distributions[0].useOriginHttpProtocolDowngrade | Boolean | Whether to enable settings to downgrade a request from HTTPS to HTTP when the request is made to origin server from CDN server, if the origin server can respond only via HTTP |
| distributions[0].forwardHostHeader     | String  | Set the host header to be forwarded by the CDN server when requesting content to the origin server ("ORIGIN_HOSTNAME": Set to the host name of the origin server, "REQUEST_HOST_HEADER": Set to the host header of the client request) |
| distributions[0].rootPathAccessControl  | Object  | Set the access control for the CDN service root path | 
| distributions[0].rootPathAccessControl.enable | Boolean | Whether the access control for the root path is enabled (true) or disabled (false)        |
| distributions[0].rootPathAccessControl.controlType  | String  | Required if enable is true. The access control method for the root path. ("DENY": deny access, "REDIRECT": redirect to the specified path) | 
| distributions[0].rootPathAccessControl.redirectPath | String | Required if controlType is "REDIRECT". The path to redirect requests for the root path to. (Enter a path including /.)      |
| distributions[0].rootPathAccessControl.redirectStatusCode | Integer | Required when controlType is "REDIRECT". The HTTP response code sent when redirecting.        |
| distributions[0].modifyOutgoingResponseHeaderControl                                  | Object  | Setting to add, modify, and delete HTTP response header from CDN  |
| distributions[0].modifyOutgoingResponseHeaderControl.enable                           | Boolean | Whether to use the settings that add/change/delete HTTP response headers  |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList                       | List    | HTTP response header list |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].action             | String  | HTTP response header change methods |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].standardHeaderName | String  | General HTTP response header name |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].customHeaderName   | String  | Required if standardHeaderName is "OTHER". Custom HTTP response header name |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].headerValue        | String  | HTTP response header value |
| distributions[0].callback              | Object  | Callback to receive service creation result                      |
| distributions[0].callback.httpMethod   | String  | HTTP method of callback                                         |
| distributions[0].callback.url          | String  | Callback URL                                                   |



### Query a Service

#### Request


[URI]

| Method  | URI                                  |
| ---- | ------------------------------------ |
| GET  | /v2.0/appKeys/{appKey}/distributions |


[Parameter]

| Name   | Type   | Required | Valid Range     | Description                         |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| domain | String | Optional      | Up to 255 characters    | Domain to query (service name)   |
| status | String | Optional      | CDN Status Codes | CDN status code (See [Table] CDN Status Codes) |

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
    "defaultMaxAge" : 86400,
    "cacheKeyQueryParam": "INCLUDE_ALL",
    "status" :  "OPENING",
    "referrerType" :  "BLACKLIST",
    "referrers" :  ["test.com"],
    "isAllowWhenEmptyReferrer" : true,
    "isAllowPost" : true,
    "isAllowPut" : false,
    "isAllowPatch" : true,
    "isAllowDelete" : false,
    "useLargeFileOptimization" : false,
    "useOriginCacheControl" :  false,
    "cacheType": "NO_STORE",
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
    "modifyOutgoingResponseHeaderControl" : {
        "enable": true,
        "headerList": [
            {
                "action": "ADD",
                "standardHeaderName": "OTHER",
                "customHeaderName": "custom-header-name",
                "headerValue": "custom-header-value"
            },
            {
                "action": "MODIFY",
                "standardHeaderName": "ACCESS_CONTROL_ALLOW_ORIGIN",
                "headerValue": "*"
            }
        ]
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
| header.isSuccessful                    | Boolean | Successful or not                                                    |
| header.resultCode                      | Integer | Result code                                                    |
| header.resultMessage                   | String  | Result message                                                  |
| distributions                          | List    | List of created CDN objects                                     |
| distributions[0].domain                | String  | Domain name (service name)                                     |
| distributions[0].domainAlias           | List  | List of domain aliases (using domains owned by individuals or companies)                                                  |
| distributions[0].region                | String  | Service region ("GLOBAL": Global service)             |
| distributions[0].status                | String  | CDN status code (See [Table] CDN Status Codes)                                 |
| distributions[0].defaultMaxAge         | Integer  | Cache expiration time (seconds)                                           |
| distributions[0].cacheKeyQueryParam    | String  | Set whether to include the request query string in the cache key ("INCLUDE_ALL": Include all, "EXCLUDE_ALL": Exclude all) |
| distributions[0].referrerType          | String  | Referrer access management ("BLACKLIST": Blacklist, "WHITELIST": Whitelist) |
| distributions[0].referrers             | List    | List of regex referrer headers                                 |
| distributions[0].isAllowWhenEmptyReferrer | Boolean | Whether to allow (true) or deny (false) access to content when there is no referer header |
| distributions[0].isAllowPost          | Boolean | Whether to allow (true)/deny (false) POST method             |
| distributions[0].isAllowPut           | Boolean | Whether to allow (true)/deny (false) PUT method             |
| distributions[0].isAllowPatch         | Boolean | Whether to allow (true)/deny (false) PATCH method             |
| distributions[0].isAllowDelete        | Boolean | Whether to allow (true)/deny (false) DELETE method             |
| distributions[0].useLargeFileOptimization | Boolean | Whether to use the large file optimization setting     |
| distributions[0].useOriginCacheControl | Boolean | Whether to use origin server setting or not (true: Use the origin server setting, false: User-configured setting) |
| distributions[0].cacheType             | String  | Cache type settings                                          |
| distributions[0].origins               | List    | List of origin server objects                                      |
| distributions[0].origins[0].origin     | String  | Origin server (domain or IP)                                      |
| distributions[0].origins[0].originPath | String  | Sub-path of origin server                                          |
| distributions[0].origins[0].httpPort   | Integer | HTTP protocol port of origin server                                  |
| distributions[0].origins[0].httpsPort  | Integer | HTTPS protocol port of origin server                                 |
| distributions[0].forwardHostHeader     | String  | Callback to receive service deployment result                        |
| distributions[0].useOriginHttpProtocolDowngrade | Boolean | Whether to enable settings to downgrade a request from HTTPS to HTTP when the request is made to origin server from CDN server, if the origin server can respond only via HTTP |
| distributions[0].forwardHostHeader     | String  | Set the host header to be forwarded by the CDN server when requesting content to the origin server ("ORIGIN_HOSTNAME": Set to the host name of the origin server, "REQUEST_HOST_HEADER": Set to the host header of the client request) |
| distributions[0].rootPathAccessControl  | Object  | Set the access control for the CDN service root path | 
| distributions[0].rootPathAccessControl.enable | Boolean | Whether the access control for the root path is enabled (true) or disabled (false)          |
| distributions[0].rootPathAccessControl.controlType  | String  | Required if enable is true. The access control method for the root path. ("DENY": deny access, "REDIRECT": redirect to the specified path) | 
| distributions[0].rootPathAccessControl.redirectPath | String | Required if controlType is "REDIRECT". The path to redirect requests for the root path to. (Enter a path including /.)        |
| distributions[0].rootPathAccessControl.redirectStatusCode | Integer | Required when controlType is "REDIRECT". The HTTP response code sent when redirecting.          |
| distributions[0].modifyOutgoingResponseHeaderControl                                  | Object  | Setting to add, modify, and delete HTTP response header from CDN  |
| distributions[0].modifyOutgoingResponseHeaderControl.enable                           | Boolean | Whether to use the settings that add/change/delete HTTP response headers  |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList                       | List    | HTTP response header list |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].action             | String  | HTTP response header change methods |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].standardHeaderName | String  | General HTTP response header name |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].customHeaderName   | String  | Required if standardHeaderName is "OTHER". Custom HTTP response header name |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].headerValue        | String  | HTTP response header value |
| distributions[0].callback              | Object  | Callback to receive service deployment result                        |
| distributions[0].callback.httpMethod   | String  | HTTP method of callback                                           |
| distributions[0].callback.url          | String  | Callback URL                                                     |


### Modify a Service

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
      "cacheType": "BYPASS",
      "defaultMaxAge": 86400,
      "cacheKeyQueryParam": "INCLUDE_ALL",
      "referrerType" : "BLACKLIST",
      "referrers" : ["test.com"],
      "isAllowWhenEmptyReferrer" : true,
      "isAllowPost" : true,
      "isAllowPut" : false,
      "isAllowPatch" : true,
      "isAllowDelete" : false,
      "useLargeFileOptimization" : true,
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
      "modifyOutgoingResponseHeaderControl" : {
          "enable": true,
          "headerList": [
              {
                  "action": "ADD",
                  "standardHeaderName": "OTHER",
                  "customHeaderName": "custom-header-name",
                  "headerValue": "custom-header-value"
              },
              {
                  "action": "MODIFY",
                  "standardHeaderName": "ACCESS_CONTROL_ALLOW_ORIGIN",
                  "headerValue": "*"
              }
          ]
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

| Name                  | Type    | Required | Default | Valid Range                                                    | Description                                                         |
| --------------------- | ------- | --------- | ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| domain                | String  | Required      |        | Up to 255 characters                                                   | Domain (service name) to modify                                   |
| useOriginCacheControl | Boolean | Optional      |        | true/false                                                        | Set cache expiration (true: use origin server settings, false: use user settings). One of useOriginCacheControl or cacheType must be entered.      |
| cacheType             | String  | Optional      |        | BYPASS, NO_STORE            | Set cache type. One of useOriginCacheControl or cacheType must be entered.                                          |
| referrerType          | String  | Required      |        | BLACKLIST/WHITELIST                                          | Referrer access management ("BLACKLIST": Blacklist, "WHITELIST": Whitelist) |
| referrers             | List    | Optional      |        |                                                              | List of regex referrer headers |
| isAllowWhenEmptyReferrer | Boolean | Optional      | true      | true/false             | Whether to allow (true) or deny (false) access to content when there is no referer header             |
| isAllowPost           | Boolean | Optional      | false      | true/false             | Whether to allow (true)/deny (false) POST method             |
| isAllowPut            | Boolean | Optional      | false      | true/false             | Whether to allow (true)/deny (false) PUT method             |
| isAllowPatch          | Boolean | Optional      | false      | true/false             | Whether to allow (true)/deny (false) PATCH method             |
| isAllowDelete         | Boolean | Optional      | false      | true/false             | Whether to allow (true)/deny (false) DELETE method             |
| useLargeFileOptimization | Boolean | Optional   | false      | true/false             | Whether to use the large file optimization setting     |
| description           | String  | Optional      |        | Up to 255 characters                                                   | Description                                                         |
| domainAlias           | List    | Optional      |        | Up to 255 characters                                                   | Domain alias (using a domain owned by individuals or companies) |
| defaultMaxAge         | Integer | Optional      | 0      | 0~2,147,483,647                                            | Cache expiration time (seconds), the default value 0 is 604,800 seconds.              |
| cacheKeyQueryParam    | String  | Optional      | INCLUDE_ALL | INCLUDE_ALL/EXCLUDE_ALL                               | Set whether to include the request query string in the cache key ("INCLUDE_ALL": Include all, "EXCLUDE_ALL": Exclude all) |
| origins               | List    | Required      |        |                                                              | Origin server                                                    |
| origins[0].origin     | String  | Required      |        | Up to 255 characters                                                   | Origin server (domain or IP)                                      |
| origins[0].originPath | String  | Optional      |        | Up to 8192 characters                                                  | Sub-path of origin server                                          |
| origins[0].httpPort   | Integer  | Optional      |        |See '[Table 2] Available Origin Server Port Numbers' of [Console User Guide > Origin Server](./console-guide/#origin-server)| HTTP protocol port of the origin server (one of origins[0].httpPort and origins[0].httpsPort must be entered.)  |
| origins[0].httpsPort  | Integer  | Optional      |        |See '[Table 2] Available Origin Server Port Numbers' of [Console User Guide > Origin Server](./console-guide/#origin-server) | HTTPS protocol port of the origin server (one of origins[0].httpPort and origins[0].httpsPort must be entered.) |
| useOriginHttpProtocolDowngrade | Boolean  | Required     | false       | true/false         | Whether to enable settings to downgrade a request from HTTPS to HTTP when the request is made to origin server from CDN server, if the origin server can respond only via HTTP |
| forwardHostHeader     | String  | Required      |        | ORIGIN_HOSTNAME<br/>REQUEST_HOST_HEADER   | Set the host header to be forwarded by the CDN server when requesting content to the origin server ("ORIGIN_HOSTNAME": Set to the host name of the origin server, "REQUEST_HOST_HEADER": Set to the host header of the client request)|
| useOrigin             | String  | Required      |        | Y/N                                                          | Cache expiration setting (Y: Use the original setting, "N": Use the user-configured setting)      |
| rootPathAccessControl  | Object  | Optional |  |  | Set the access control for the CDN service root path | 
| rootPathAccessControl.enable | Boolean | Required | false | true/false | Whether the access control for the root path is enabled (true) or disabled (false)          |
| rootPathAccessControl.controlType  | String  | Optional |  | DENY, REDIRECT | Required if enable is true. The access control method for the root path. ("DENY": deny access, "REDIRECT": redirect to the specified path) | 
| rootPathAccessControl.redirectPath | String | Optional |  | | Required if controlType is "REDIRECT". The path to redirect requests for the root path to. (Enter a path including /.)        |
| rootPathAccessControl.redirectStatusCode | Integer | Optional | | 301, 302, 303, 307 |Required when controlType is "REDIRECT". The HTTP response code sent when redirecting.          |
| modifyOutgoingResponseHeaderControl                                  | Object  | Optional    |             |                                                                       | Setting to add, modify, and delete HTTP response header from CDN                                                                                         |
| modifyOutgoingResponseHeaderControl.enable                           | Boolean | Required    | true        | true/false                                                            | Whether to use the settings that add/change/delete HTTP response headers                                                                          |
| modifyOutgoingResponseHeaderControl.headerList                       | List    | Optional    |         |                                                                       | HTTP response header list                                                                                                             |
| modifyOutgoingResponseHeaderControl.headerList[0].action             | String  | Optional    |         | ADD, MODIFY, DELETE                                                   | HTTP response header change methods                                                                                                          |
| modifyOutgoingResponseHeaderControl.headerList[0].standardHeaderName | String  | Optional    |         | ACCESS_CONTROL_ALLOW_CREDENTIALS<br/>ACCESS_CONTROL_ALLOW_HEADERS<br/>ACCESS_CONTROL_ALLOW_METHODS<br/>ACCESS_CONTROL_ALLOW_ORIGIN<br/>ACCESS_CONTROL_EXPOSE_HEADERS<br/>ACCESS_CONTROL_MAX_AGE<br/>CACHE_CONTROL<br/>CONTENT_DISPOSITION<br/>CONTENT_TYPE<br/>P3P<br/>PRAGMA<br/>OTHER | General HTTP response header name                                                                                                          |
| modifyOutgoingResponseHeaderControl.headerList[0].customHeaderName   | String  | Optional    |         |                                                      | Required if standardHeaderName is "OTHER". Custom HTTP response header name                                                               |
| modifyOutgoingResponseHeaderControl.headerList[0].headerValue        | String  | Required    |         |                                                      | HTTP response header value                                                                                                              |
| callback              | Object  | Optional      |        | Callback URL to receive CDN service deployment result (Callback setting is optional.) |                                                              |
| callback.httpMethod   | String  | Required      |        | GET/POST/PUT                                                 | HTTP method of callback                                           |
| callback.url          | String  | Required      |        | Up to 1024 characters                                                  | Callback URL                                                     |

- The default value of forwardHostHeader is REQUEST_HOST_HEADER if domainAlias is set, or ORIGIN_HOSTNAME otherwise. 

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

### Delete a Service

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
| domains | String | Required    |      |       | Domains to delete; multiple domains allowed |

**\* When multiple domains are provided as input, all corresponding services are closed.**

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


## Auth Token API

### Create an Auth Token

#### Request

[URI]

| Method  | URI                           |
| ---- | ----------------------------- |
| POST | /v2.0/appKeys/{appKey}/auth-token |


[Request Body]

```json
{
  "encryptKey" : "AUTH_TOKEN_ENCRYPT_KEY",
  "durationSeconds": 3600,
  "singlePath": "/sample.png",
  "singleWildcardPath": "/dir/*",
  "multipleWildcardPath": ["/dir/*", "/dir2/*"],
  "sessionId": "sampleSessionId"
}
```


[Field]

| Name      | Type   | Required | Default | Valid Range             | Description                                                         |
| --------- | ------ | --------- | ------ | --------------------- | ------------------------------------------------------------ |
| encryptKey    | String | Required   |        |             | Access Control for Auth Token Authentication > Token Encryption Key displayed on NHN Cloud CDN console  |
| durationSeconds | Integer | Required |        | 0~2,147,483,647 | Duration, in seconds, for which the generated token is valid |
| singlePath      | String | Optional |        |             | Single path to access using the generated token |
| singleWildcardPath | String | Optional |     |             | Single wildcard path to access using generated token |
| multipleWildcardPath | String | Optional |   |             | Multiple wildcard paths to access using the generated token |
| sessionId |           String | Optional |    |  String length is up to 36 bytes           | Generates token including sessionId for a single access request |

* At least one of singlePath, singleWildcardPath, or multipleWildcardPath must exist.
* For details on creating and using tokens, refer to [Console User Guide > Access Control for Auth Token Authentication > 2. Create a Token](./console-guide/#access-control-for-auth-token-authentication).


#### Response

[Response Body]

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "authToken": {
        "singlePathToken": "exp=1652247396~id=fjdklfjklsdfjklsdjflksdjfkls~hmac=c743fcdb2c35c7c97455c18f6d354eef89743f556d3b82df3861ef9cb67eec94",
        "singleWildcardPathToken": "exp=1652247396~acl=%2fdir%2f*~id=fjdklfjklsdfjklsdjflksdjfkls~hmac=160acb24795daf63a7b0628420f8d7f4a37f014c01b73ad388ee5efaca17d663",
        "multipleWildcardPathToken": "exp=1652247396~acl=%2fdir%2f*~id=fjdklfjklsdfjklsdjflksdjfkls~hmac=160acb24795daf63a7b0628420f8d7f4a37f014c01b73ad388ee5efaca17d663"
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
| authToken             | Object    | Created Auth Token object |
| authToken.singlePathToken | String    | Auth token generated to allow access to a single path                                 |
| authToken.singleWildcardPathToken | String    | Auth token generated to allow access to a single wildcard path                 |
| authToken.multipleWildcardPathToken | String  | Auth token generated to access multiple wildcard paths             |



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

| Name      | Type   | Required | Default | Valid Range             | Description                                                         |
| --------- | ------ | --------- | ------ | --------------------- | ------------------------------------------------------------ |
| domain    | String | Required      |        | Up to 255 characters            | Domain (service) name to be purged                                 |
| purgeList | List | Required      |        |                       | List of purge target URLs |

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

| Name      | Type   | Required | Default | Valid Range             | Description                                                         |
| --------- | ------ | --------- | ------ | --------------------- | ------------------------------------------------------------ |
| domain    | String | Required      |        | Up to 255 characters            | Domain (service) name to be purged                                 |

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

- Cache purge requests may fail within an hour after the CDN service is newly created. If the failure continues, contact Customer Center.
- A usage limit policy exists for Purge APIs. For more details, see the cache purge usage limit of [Console User Guide > Purging CDN Cache](./console-guide/#cdn-purge).

### Query Cache Purge
- In case of purging cache through API v2.0, high-speed cache purge is performed and completed within a few seconds after request, so an API to query cache purge status is not provided separately.

## Certificate API
### Issue New Certificates
#### Request

[URI]

| Method  | URI                           |
| ---- | ----------------------------- |
| POST | /v2.0/appKeys/{appKey}/certificates|


[Request Body]

```json
{
    "certificateDomain": "example.domain.com",
    "callbackHttpMethod": "POST",
    "callbackUrl": "http://test.callback.com/cdn-certificate?appKey={appKey}&status={status}&domain={domain}"   
}
```


[Field]

| Name      | Type   | Required | Default | Valid Range             | Description                                                         |
| --------- | ------ | --------- | ------ | --------------------- | ------------------------------------------------------------ |
| certificateDomain    | String | Required      |        | Up to 255 characters            | Domain for which you want to issue a new certificate (enter in full domain address format)|
| callbackHttpMethod  | String | Optional      |        | GET/POST/PUT        | HTTP method of callback to be notified of certificate generation processing result |
| callbackUrl         | String | Optional      |        | Up to 1024 characters           | Callback URL to be notified of certificate generation processing result       |

* For details on issuing a certificate, refer to [Console User Guide > Certificate Management > Issue New Certificates](./console-guide/#_7).

#### Response

[Response Body]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    },
    "certificates": [
        {
            "sanDnsId": "628bb15d-fe0a-46cf-9b63-8cdba80cbc1a",
            "dnsName": "example.domain.com",        
            "dnsStatus": "PENDING_NEW",
            "callbackHttpMethod": "POST",
            "callbackUrl": "http://test.callback.com/cdn-certificate?appKey={appKey}&status={status}&domain={domain}",
            "createDatetime": "2022-06-07T16:51:32.000+09:00",
            "updateDatetime": "2022-06-07T16:51:32.000+09:00",
            "hasCname": false,
            "hasDistributionDomain": false,
            "renewalStartDate": "2022-08-26T00:00:00.000+09:00",
            "renewalEndDate": "2022-08-30T00:00:00.000+09:00"            
        }
    ]
}
```


[Field]

| Field                   | Type      | Description        |
| -------------------- | ------- | --------- |
| header               | Object  | Header area     |
| header.isSuccessful  | Boolean | Successful or not     |
| header.resultCode    | Integer | Result code     |
| header.resultMessage | String  | Result message    |
| certificates         | List    | List of issued certificates |
| certificates[0].sanDnsId | String | Certificate ID    |
| certificates[0].dnsName  | String | Certificate domain  |
| certificates[0].dnsStatus | String | Certificate issuance status codes (Refer to [Table] Certificate Issuance Status Codes) |
| certificates[0].callbackHttpMethod | String | HTTP method of callback to be notified of certificate generation processing result |
| certificates[0].callbackUrl | String | Callback URL to be notified of certificate generation processing result |
| certificates[0].createDatetime | DateTime | Certificate creation date |
| certificates[0].updateDatetime | DateTime | Certificate update date |
| certificates[0].hasCname | Boolean | Whether to set up a CNAME record |
| certificates[0].hasDistributionDomain | Boolean | Whether to integrate with the CDN service |
| certificates[0].renewalStartDate | DateTime | Certificate renewal start date |
| certificates[0].renewalEndDate | DateTime | Certificate renewal end date |

### List Certificates
#### Request

[URI]

| Method  | URI                           |
| ---- | ----------------------------- |
| GET | /v2.0/appKeys/{appKey}/certificates|


#### Response

[Response Body]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    },
    "certificates": [
        {
            "sanDnsId": "628bb15d-fe0a-46cf-9b63-8cdba80cbc1a",
            "dnsName": "example.domain.com",        
            "dnsStatus": "PENDING_NEW",
            "callbackHttpMethod": "POST",
            "callbackUrl": "http://test.callback.com/cdn-certificate?appKey={appKey}&status={status}&domain={domain}",
            "createDatetime": "2022-06-07T16:51:32.000+09:00",
            "updateDatetime": "2022-06-07T16:51:32.000+09:00",
            "hasCname": false,
            "hasDistributionDomain": false,
            "renewalStartDate": "2022-08-26T00:00:00.000+09:00",
            "renewalEndDate": "2022-08-30T00:00:00.000+09:00"            
        }
    ]
}
```


[Field]

| Field                   | Type      | Description        |
| -------------------- | ------- | --------- |
| header               | Object  | Header area     |
| header.isSuccessful  | Boolean | Successful or not     |
| header.resultCode    | Integer | Result code     |
| header.resultMessage | String  | Result message    |
| certificates         | List    | List of issued certificates |
| certificates[0].sanDnsId | String | Certificate ID    |
| certificates[0].dnsName  | String | Certificate domain  |
| certificates[0].dnsStatus | String | Certificate issuance status codes (Refer to [Table] Certificate Issuance Status Codes) |
| certificates[0].callbackHttpMethod | String | HTTP method of callback to be notified of certificate generation processing result |
| certificates[0].callbackUrl | String | Callback URL to be notified of certificate generation processing result |
| certificates[0].createDatetime | DateTime | Certificate creation date |
| certificates[0].updateDatetime | DateTime | Certificate update date |
| certificates[0].hasCname | Boolean | Whether to set up a CNAME record |
| certificates[0].hasDistributionDomain | Boolean | Whether to integrate with the CDN service |
| certificates[0].renewalStartDate | DateTime | Certificate renewal start date |
| certificates[0].renewalEndDate | DateTime | Certificate renewal end date |

### Delete Certificates
#### Request

[URI]

| Method  | URI                           |
| ---- | ----------------------------- |
| DELETE | /v2.0/appKeys/{appKey}/certificates|


[Parameter]

| Name   | Type   | Required | Valid Range     | Description                         |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| dnsIdList | String | Required      |     | List of IDs (sanDnsId) of certificates to delete (list of certificate IDs concatenated by ,)   |

[Example]
```
curl -X GET "https://kr1-cdn.api.nhncloudservice.com/v2.0/appKeys/{appKey}/certificates?dnsIdList={dnsIdList}" \
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

## Statistics API
### Query Traffic Statistics
#### Request

[URI]

| Method  | URI                           |
| ---- | ----------------------------- |
| GET | /v2.0/appKeys/{appKey}/statistics/traffic|


[Parameter]

| Name   | Type   | Required | Valid Range     | Description                         |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| domain | String | Required      | Up to 255 characters    | Domain to query (service name)   |
| fromDate | DateTime | Required      |  | Start date and time of statistics query |
| toDate | DateTime | Required      |  | End date and time of statistics query |

- The search period set by the fromDate and toDate fields must be within the last 90 days.
- The fromDate and toDate fields must be entered in ISO 8601 format date string format.
  - UTC notation: yyyy-MM-dd'T'HH:mm:ssZ
  - UTC time offset notation: yyyy-MM-dd'T'HH:mm:ss±hh:mm

[Example]
```
curl -X GET "https://kr1-cdn.api.nhncloudservice.com/v2.0/appKeys/{appKey}/statistics/traffic?domain={domain}&fromDate={fromDate}&toDate={toDate}" \
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
    "statistics": [
        {
            "dateTime": "2022-05-01T09:00:00.000+09:00",
            "bandwidth": 0.0,
            "transferred": 0.0
        }
    ]
}
```


[Field]

| Field                   | Type      | Description        |
| -------------------- | ------- | --------- |
| header               | Object  | Header area     |
| header.isSuccessful  | Boolean | Successful or not     |
| header.resultCode    | Integer | Result code     |
| header.resultMessage | String  | Result message    |
| statistics         | List    | List of traffic statistics data |
| statistics[0].dateTime | DateTime | Statistics time    |
| statistics[0].bandwidth  | String | Bandwidth of statistics time (Mbps)  |
| statistics[0].transferred | String | Transfer volume of statistics time (bytes) |

### Query Statistics by HTTP Status Code
#### Request

[URI]

| Method  | URI                           |
| ---- | ----------------------------- |
| GET | /v2.0/appKeys/{appKey}/statistics/http|


[Parameter]

| Name   | Type   | Required | Valid Range     | Description                         |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| domain | String | Required      | Up to 255 characters    | Domain to query (service name)   |
| fromDate | DateTime | Required      |  | Start date and time of statistics query |
| toDate | DateTime | Required      |  | End date and time of statistics query |

- The search period set by the fromDate and toDate fields must be within the last 90 days.
- The fromDate and toDate fields must be entered in ISO 8601 format date string format.
  - UTC notation: yyyy-MM-dd'T'HH:mm:ssZ
  - UTC time offset notation: yyyy-MM-dd'T'HH:mm:ss±hh:mm

[Example]
```
curl -X GET "https://kr1-cdn.api.nhncloudservice.com/v2.0/appKeys/{appKey}/statistics/http?domain={domain}&fromDate={fromDate}&toDate={toDate}" \
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
    "statistics": [
        {
            "dateTime": "2022-05-01T09:00:00.000+09:00",
            "successHits": 10,
            "notModifiedHits": 2,
            "redirectsHits": 0,
            "notFoundHits": 5,
            "permissionHits": 0,
            "serverErrorHits": 0,
            "etcHits": 0
        }
    ]
}
```


[Field]

| Field                   | Type      | Description        |
| -------------------- | ------- | --------- |
| header               | Object  | Header area     |
| header.isSuccessful  | Boolean | Successful or not     |
| header.resultCode    | Integer | Result code     |
| header.resultMessage | String  | Result message    |
| statistics         | List    | List of traffic statistics data |
| statistics[0].dateTime | DateTime | Statistics time    |
| statistics[0].successHits  | Long | Number of calls with response HTTP status code 2xx  |
| statistics[0].notModifiedHits | Long | Number of calls with response HTTP status code 304 |
| statistics[0].redirectsHits | Long | Number of calls with response HTTP status codes 301, 302 |
| statistics[0].notFoundHits | Long | Number of calls with response HTTP status code 404 |
| statistics[0].permissionHits | Long | Number of calls with response HTTP status codes 401, 403, 415 |
| statistics[0].serverErrorHits | Long | Number of calls with response HTTP status code 5xx |
| statistics[0].etcHits | Long | Number of API calls with response HTTP status code other than 2xx, 3xx, 4xx, 5xx |

### Query Ranking Statistics for Content with the Most Downloads
#### Request

[URI]

| Method  | URI                           |
| ---- | ----------------------------- |
| GET | /v2.0/appKeys/{appKey}/statistics/topcontent|


[Parameter]

| Name   | Type   | Required | Valid Range     | Description                         |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| domain | String | Required      | Up to 255 characters    | Domain to query (service name)   |
| fromDate | DateTime | Required      |  | Start date and time of statistics query |
| toDate | DateTime | Required      |  | End date and time of statistics query |

- The search period set by the fromDate and toDate fields must be within the last 90 days.
- The fromDate and toDate fields must be entered in ISO 8601 format date string format.
  - UTC notation: yyyy-MM-dd'T'HH:mm:ssZ
  - UTC time offset notation: yyyy-MM-dd'T'HH:mm:ss±hh:mm

[Example]
```
curl -X GET "https://kr1-cdn.api.nhncloudservice.com/v2.0/appKeys/{appKey}/statistics/topcontent?domain={domain}&fromDate={fromDate}&toDate={toDate}" \
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
    "statistics": [
        {
            "rank": 1,
            "contentName": "top.png",
            "successHits": 700,
            "succDataTransferred": 4696.546738176
        }
    ]
}
```


[Field]

| Field                   | Type      | Description        |
| -------------------- | ------- | --------- |
| header               | Object  | Header area     |
| header.isSuccessful  | Boolean | Successful or not     |
| header.resultCode    | Integer | Result code     |
| header.resultMessage | String  | Result message    |
| statistics         | List    | List of traffic statistics data |
| statistics[0].rank | Integer | Statistics time    |
| statistics[0].successHits  | Long | Number of calls with response HTTP status code 2xx  |
| statistics[0].succDataTransferred  | Long | Traffic transfer volume of calls with response HTTP status code 2xx (MBytes)  |



## Callback Response
### CDN Service
If the callback function is set in the CDN service, the configured callback URL is called when creation, modification, pause, resume, deletion change is completed.
When the callback is called, the request body contains the following CDN service settings information.

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
      "cacheKeyQueryParam": "INCLUDE_ALL",
      "status" :  "OPENING",
      "referrerType" :  "BLACKLIST",
      "referrers" :  ["test.com"],    
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
      "modifyOutgoingResponseHeaderControl" : {
          "enable": true,
          "headerList": [
              {
                  "action": "ADD",
                  "standardHeaderName": "OTHER",
                  "customHeaderName": "custom-header-name",
                  "headerValue": "custom-header-value"
              },
              {
                  "action": "MODIFY",
                  "standardHeaderName": "ACCESS_CONTROL_ALLOW_ORIGIN",
                  "headerValue": "*"
              }
          ]
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
| distribution.domain                | String  | Domain name (service name)                                     |
| distribution.domainAlias           | List  | List of domain aliases (using domains owned by individuals or companies)                                 |
| distribution.region                | String  | Service region ("GLOBAL": Global service)             |
| distribution.status                | String  | CDN status code (See [Table] CDN Status Codes)                                 |
| distribution.defaultMaxAge         | Integer  | Cache expiration time (seconds)                                           |
| distribution.cacheKeyQueryParam    | String  | Set whether to include the request query string in the cache key ("INCLUDE_ALL": Include all, "EXCLUDE_ALL": Exclude all) |
| distribution.referrerType          | String  | Referrer access management ("BLACKLIST": Blacklist, "WHITELIST": Whitelist) |
| distribution.referrers             | List    | List of regex referrer headers                                 |
| distribution.useOriginCacheControl | Boolean | Whether to use origin server setting or not (true: Use the origin server setting, false: User-configured setting) |
| distribution.createTime            | DateTime | Date and time of creation                                         |
| distribution.deleteTime            | DateTime | Date and time of deletion                                         |
| distribution.origins               | List    | List of origin server objects                                      |
| distribution.origins[0].origin     | String  | Origin server (domain or IP)                                      |
| distribution.origins[0].originPath | String  | Sub-path of origin server                                          |
| distribution.origins[0].httpPort   | Integer | HTTP protocol port of origin server                                               |
| distribution.origins[0].httpsPort  | Integer | HTTPS protocol port of origin server                                               |
| distribution.useOriginHttpProtocolDowngrade | Boolean | Whether to enable settings to downgrade a request from HTTPS to HTTP when the request is made to origin server from CDN server, if the origin server can respond only via HTTP |
| distribution.forwardHostHeader     | String  | Set the host header to be forwarded by the CDN server when requesting content to the origin server ("ORIGIN_HOSTNAME": Set to the host name of the origin server, "REQUEST_HOST_HEADER": Set to the host header of the client request) |
| distribution.rootPathAccessControl  | Object  | Set the access control for the CDN service root path | 
| distribution.rootPathAccessControl.enable | Boolean | Whether the access control for the root path is enabled (true) or disabled (false)          |
| distribution.rootPathAccessControl.controlType  | String  | Required if enable is true. The access control method for the root path. ("DENY": deny access, "REDIRECT": redirect to the specified path) | 
| distribution.rootPathAccessControl.redirectPath | String | Required if controlType is "REDIRECT". The path to redirect requests for the root path to. (Enter a path including /.)        |
| distribution.rootPathAccessControl.redirectStatusCode | Integer | Required when controlType is "REDIRECT". The HTTP response code sent when redirecting.         |
| distribution.modifyOutgoingResponseHeaderControl                      | Object  | Setting to add, modify, and delete HTTP response header from CDN  |
| distribution.modifyOutgoingResponseHeaderControl.enable               | Boolean | Whether to use the settings that add/change/delete HTTP response headers  |
| distribution.modifyOutgoingResponseHeaderControl.headerList           | List    | HTTP response header list |
| distribution.modifyOutgoingResponseHeaderControl.headerList[0].action | String  | HTTP response header change methods |
| distribution.modifyOutgoingResponseHeaderControl.headerList[0].standardHeaderName | String  | General HTTP response header name |
| distribution.modifyOutgoingResponseHeaderControl.headerList[0].customHeaderName | String  | Required if standardHeaderName is "OTHER". Custom HTTP response header name |
| distribution.modifyOutgoingResponseHeaderControl.headerList[0].headerValue | String  | HTTP response header value |
| distribution.callback              | Object  | Callback to receive service deployment result                        |
| distribution.callback.httpMethod   | String  | HTTP method of callback                                           |
| distribution.callback.url          | String  | Callback URL                                                     |

### Certificate
If callback information is set when requesting issuance of a certificate, the configured callback URL is called when the status changes to domain validation, domain validated, or certificate issued.
When the callback is called, the request body contains the following certificate settings information.

[Response Body]
```json
{
  "header" : {
    "resultCode" :  0,
    "resultMessage" :  "SUCCESS",
    "isSuccessful" :  true
  },
  "certificate": {
      "sanDnsId": "628bb15d-fe0a-46cf-9b63-8cdba80cbc1a",
      "distributionSeq": null,
      "dnsName": "example.domain.com",
      "dnsStatus": "WAITING_VALIDATION",
      "validationDnsRecordName": "_acme-challenge.example.domain.com.",
      "validationDnsToken": "16WKuUX7ebmYEREEU1CqnPWx0I7wY04EvtF-QL2n-lU",
      "validationHtmlUrl": "http://example.domain.com/.well-known/acme-challenge/NDUxotnSnKAIJQrhDOUp1s3AC4zjyU1i_BEvLI3wmvg",
      "validationHtmlToken": "NDUxotnSnKAIJQrhDOUp1s3AC4zjyU1i_BEvLI3wmvg.tL4C5fu32Q5A81pbFTAgUeNiv9rorD-rUQYb7kQJvHc",
      "validationExpireDatetime": null,
      "createDatetime": 1654588292000,
      "updateDatetime": 1654588758056,
      "deleteDatetime": null,
      "callbackHttpMethod": "POST",
      "callbackUrl": "http://test.callback.com/cdn-certificate?appKey={appKey}&status={status}&domain={domain}"
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
| certificate                          | Object    | Certificate object for which changes have been completed                                  |
| certificate.sanDnsId                   | String    | Certificate ID                                  |
| certificate.distributionSeq                   | String    | Integrated CDN service ID                                  |
| certificate.dnsName  | String | Certificate domain  |
| certificate.dnsStatus | String | Certificate issuance status codes (Refer to [Table] Certificate Issuance Status Codes) |
| certificate.validationDnsRecordName | String | Domain validation information (record name for the method of adding DNS TXT records)  |
| certificate.validationDnsToken | String | Domain validation information (record value for the method of adding DNS TXT records)  |
| certificate.validationHtmlUrl | String | Domain validation information (HTTP page URL for the method of adding HTTP page)  |
| certificate.validationHtmlToken | String | Domain validation information (HTTP page body content value for the method of adding HTTP page)  |
| certificate.validationExpireDatetime | DateTime | Domain validation expiration date  |
| certificate.createDatetime | DateTime | Certificate creation date |
| certificate.updateDatetime | DateTime | Certificate update date |
| certificate.deleteDatetime | DateTime | Certificate deletion date |
| certificate.callbackHttpMethod | String | HTTP method of callback to be notified of certificate generation processing result |
| certificate.callbackUrl | String | Callback URL to be notified of certificate generation processing result |
