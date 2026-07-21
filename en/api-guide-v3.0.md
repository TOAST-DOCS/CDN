<!-- pre-align:aligned sig=73fb7cfa0df0 -->

## Content Delivery > CDN > API v3.0 Guide

This document describes Public API v3.0 of NHN Cloud CDN.

<a id="api"></a>

## API Common Information

<a id="api-1"></a>

### Domain

| Name              | Domain                                 |
| --------------- | ----------------------------------- |
| CDN Public API domain | https://cdn.api.nhncloudservice.com |

<a id="api-2"></a>

### Authentication and Authorization
CDN API v3.0 supports Appkey and User Access Key token for API authentication call and authentication.

An Appkey is a unique authentication key issued for each NHN Cloud service, used to identify the service and validate API requests.<br>The User Access Key token is a temporary, Bearer-type access token issued from a User Access Key.
For more information on how to check and use each authentication method, see [Appkey](/en/nhncloud/en/public-api/appkey/) and [User Access Key Token](/en/nhncloud/en/public-api/user-access-key-token/), respectively.

The issued token must be included in the request header.

| Name | Type | Format | Required | Description |
|---|---|---|---|---|
| X-NHN-AUTHORIZATION | Header | String | O | Bearer type token issued by the Public API |

<a id="api-3"></a>

### Response Common Information

- '200 OK' is returned for all API requests. For details on response results, refer to the header of each response.

[Success Response Body]

```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    }
}
```

<a id="cdn"></a>

#### CDN Status Code

The following shows the status codes of CDN service, which are available at the query of service:

| Value         | Description                     |
| ---------- | ------------------------ |
| OPENING    | Starting service           |
| OPEN       | In service                |
| MODIFYING  | Modifying                  |
| RESUME     | Resumed                     |
| SUSPENDING | Suspending             |
| SUSPEND    | Suspended                     |
| CLOSING    | Closing             |
| CLOSE      | Closed                |
| ERROR      | Error occurred while creating service |

<a id="api-3-1"></a>

#### Certificate Issuance Status Codes

The following are status codes that indicate the certificate issuance status of the domain. You can check the issuance status when querying the certificate.

| Value         | Description                     |
| ---------- | ------------------------ |
| PENDING_NEW        | Issuance of a new certificate has been requested, and processing is pending   |
| PENDING_CANCEL     | The issuance of a certificate has been requested to be cancelled, and domain validation cancellation is pending   |
| PENDING_DELETE     | Deletion of the issued certificate has been requested, and processing is pending  |
| PENDING_EXPIRE     | The issued certificate has expired, and expiration processing is pending  |
| VALIDATED          | Domain validation completed                     |
| DEPLOYED           | Certificate deployment completed                     |
| WAITING_VALIDATION | Waiting for domain validation                  |
| CANCELED           | Domain validation cancellation completed                 |
| DELETED            | Domain certificate deletion completed               |
| EXPIRED            | Domain certificate expired                   |


<a id="api-4"></a>

## Service API

<a id="api-4-1"></a>

### Create Service

<a id="api-4-1-1"></a>

#### Request


[URI]

| Method  | URI                                  |
| ---- |--------------------------------------|
| POST | /v3.0/appKeys/{appKey}/distributions |


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
        "url": "http://test.callback.com/cdn?appKey={appKey}&status={status}&domain={domain}"
      }
    }
  ]
}
```

[Field]

| Name                                                                                    | Type      | Required | Default Value         | Valid Range                                                                 | Description                                                                                                                        |
|---------------------------------------------------------------------------------------|---------|-------|-------------|-----------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| distributions                                                                         | List    | Required    |             |                                                                       | List of CDN objects to create                                                                                                          |
| distributions[0].useOriginHttpProtocolDowngrade                                       | Boolean | Required    | false       | true/false                                                            | Whether to enable settings to downgrade a request from HTTPS to HTTP when the request is made to the origin server from the CDN server, if the origin server can respond only via HTTP                                     |
| distributions[0].forwardHostHeader                                                    | String  | Required    |             | ORIGIN_HOSTNAME<br/>REQUEST_HOST_HEADER                               | Host header setting to forward when the CDN server requests content from the origin server ("ORIGIN_HOSTNAME": set to the host name of the origin server, "REQUEST_HOST_HEADER": set to the host header of the client request) |
| distributions[0].useOriginCacheControl                                                | Boolean | Optional    |             | true/false                                                            | Set the cache expiration (true: use origin server settings, false: use user settings). One of useOriginCacheControl or cacheType must be entered.                      |
| distributions[0].cacheType                                                            | String  | Optional    |             | BYPASS, NO_STORE                                                      | Set the cache type. One of useOriginCacheControl or cacheType must be entered.                                                           |
| distributions[0].referrerType                                                         | String  | Required    |             | BLACKLIST/WHITELIST                                                   | Referrer access management ("BLACKLIST": blacklist, "WHITELIST": whitelist)                                                                        |
| distributions[0].referrers                                                            | List    | Optional    |             |                                                                       | List of referrer headers in regular expression format                                                                                                      |
| distributions[0].isAllowWhenEmptyReferrer                                             | Boolean | Optional    | true        | true/false                                                            | Whether to allow (true) or deny (false) content access when there is no referrer header                                                                                |
| distributions[0].isAllowPost                                                          | Boolean | Optional    | false       | true/false                                                            | Whether to allow (true) or deny (false) the POST method                                                                                            |
| distributions[0].isAllowPut                                                           | Boolean | Optional    | false       | true/false                                                            | Whether to allow (true) or deny (false) the PUT method                                                                                             |
| distributions[0].isAllowPatch                                                         | Boolean | Optional    | false       | true/false                                                            | Whether to allow (true) or deny (false) the PATCH method                                                                                           |
| distributions[0].isAllowDelete                                                        | Boolean | Optional    | false       | true/false                                                            | Whether to allow (true) or deny (false) the DELETE method                                                                                          |
| distributions[0].useLargeFileOptimization                                             | Boolean | Optional    | false       | true/false                                                            | Whether to use the large file optimization setting                                                                                                       |
| distributions[0].description                                                          | String  | Optional    |             | max 255 characters                                                               | Description                                                                                                                        |
| distributions[0].domainAlias                                                          | List    | Optional    |             |                                                                       | List of domain aliases (using domains owned by individuals or companies)                                                                                           |
| distributions[0].defaultMaxAge                                                        | Integer | Optional    | 0           | 0~2,147,483,647                                                       | Cache expiration time (seconds), the default value 0 is 604,800 seconds.                                                                                          |
| distributions[0].cacheKeyQueryParam                                                   | String  | Optional    | INCLUDE_ALL | INCLUDE_ALL/EXCLUDE_ALL                                               | Whether to include the request query string in the cache key ("INCLUDE_ALL": include all, "EXCLUDE_ALL": exclude all)                                                     |
| distributions[0].origins                                                              | List    | Required    |             |                                                                       | List of origin server objects                                                                                                             |
| distributions[0].origins[0].origin                                                    | String  | Required    |             | max 255 characters                                                               | Origin server (domain or IP)                                                                                                          |
| distributions[0].origins[0].originPath                                                | String  | Optional    |             | max 8192 characters                                                              | Origin server subpath (enter a path that includes /)                                                                                          |
| distributions[0].origins[0].httpPort                                                  | Integer | Optional    |             | See "[Table 2] Available Origin Server Port Numbers" in [Console User Guide > Origin Server](./console-guide/#origin-server-port) | Origin server HTTP protocol port (one of origins[0].httpPort or origins[0].httpsPort must be entered)                                         |
| distributions[0].origins[0].httpsPort                                                 | Integer | Optional    |             | See "[Table 2] Available Origin Server Port Numbers" in [Console User Guide > Origin Server](./console-guide/#origin-server-port) | Origin server HTTPS protocol port (one of origins[0].httpPort or origins[0].httpsPort must be entered)                                        |
| distributions[0].rootPathAccessControl                                                | Object  | Optional    |             |                                                                       | Access control setting for the root path of the CDN service                                                                                               | 
| distributions[0].rootPathAccessControl.enable                                         | Boolean | Required    | true        | true/false                                                            | Whether to use (true) or not use (false) access control for the root path                                                                                    |
| distributions[0].rootPathAccessControl.controlType                                    | String  | Optional    |             | DENY, REDIRECT                                                        | Required if enable is true. Access control method for the root path ("DENY": deny access, "REDIRECT": redirect to the specified path)                                      | 
| distributions[0].rootPathAccessControl.redirectPath                                   | String  | Optional    |             |                                                                       | Required if controlType is "REDIRECT". Path to redirect requests for the root path to (enter a path that includes /)                                           |
| distributions[0].rootPathAccessControl.redirectStatusCode                             | Integer | Optional    |             | 301, 302, 303, 307                                                    | Required if controlType is "REDIRECT". HTTP response code returned during the redirect                                                                 |
| distributions[0].modifyOutgoingResponseHeaderControl                                  | Object  | Optional    |             |                                                                       | Setting to add, modify, and delete HTTP response header from CDN                                                                                         |
| distributions[0].modifyOutgoingResponseHeaderControl.enable                           | Boolean | Required    | true        | true/false                                                            | Whether to use the settings that add/change/delete HTTP response headers                                                                          |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList                       | List    | Optional    |         |                                                                       | List of HTTP response headers                                                                                                             |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].action             | String  | Optional    |         | ADD, MODIFY, DELETE                                                   | HTTP response header change method                                                                                                          |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].standardHeaderName | String  | Optional    |         | ACCESS_CONTROL_ALLOW_CREDENTIALS<br/>ACCESS_CONTROL_ALLOW_HEADERS<br/>ACCESS_CONTROL_ALLOW_METHODS<br/>ACCESS_CONTROL_ALLOW_ORIGIN<br/>ACCESS_CONTROL_EXPOSE_HEADERS<br/>ACCESS_CONTROL_MAX_AGE<br/>CACHE_CONTROL<br/>CONTENT_DISPOSITION<br/>CONTENT_TYPE<br/>P3P<br/>PRAGMA<br/>OTHER | General HTTP response header name                                                                                                          |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].customHeaderName   | String  | Optional    |         |                                                      | Required if standardHeaderName is "OTHER". Custom HTTP response header name                                                               |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].headerValue        | String  | Required    |         |                                                      | HTTP response header value                                                                                                              |
| distributions[0].callback                                                             | Object  | Optional    |             |                                                                       | Callback URL to receive the result of the CDN creation process (the callback setting is optional)                                                                               |
| distributions[0].callback.httpMethod                                                  | String  | Required    |             | GET/POST/PUT                                                          | HTTP method of callback                                                                                                              |
| distributions[0].callback.url                                                         | String  | Required    |             | max 1024 characters                                                              | Callback URL                                                                                                                    |

- The default value of `forwardHostHeader` is `REQUEST_HOST_HEADER` if `domainAlias` is set, and `ORIGIN_HOSTNAME` if it is not set.



<a id="api-4-1-2"></a>

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
                "url": "http://test.callback.com/cdn?appKey={appKey}&status={status}&domain={domain}"
            }
        }
    ]
}
```


[Field]

| Field                                   | Type    | Description                                                       |
| -------------------------------------- | ------- | ---------------------------------------------------------- |
| header                                 | Object  | Header area                                                  |
| header.isSuccessful                    | Boolean | Whether the request succeeded                                                  |
| header.resultCode                      | Integer | Result code                                                  |
| header.resultMessage                   | String  | Result message                                                |
| distributions                          | List    | List of created CDN objects                                 |
| distributions[0].domain                | String  | Created domain (service name)                                 |
| distributions[0].domainAlias           | List    | List of domain aliases (using a domain owned by an individual or company)            |
| distributions[0].region                | String  | Service region ("GLOBAL": global)          |
| distributions[0].description           | String  | Description                                                       |
| distributions[0].status                | String  | CDN status code (see [Table] CDN Status Code)                               |
| distributions[0].defaultMaxAge         | Integer | Cache expiration time (seconds)                                         |
| distributions[0].cacheKeyQueryParam    | String  | Whether to include the request query string in the cache key ("INCLUDE_ALL": include all, "EXCLUDE_ALL": exclude all) |
| distributions[0].referrerType          | String  | Referrer access management ("BLACKLIST": blacklist, "WHITELIST": whitelist) |
| distributions[0].referrers             | List    | List of referrer headers in regular expression format                                |
| distributions[0].isAllowWhenEmptyReferrer | Boolean | Whether to allow (true) or deny (false) content access when there is no referrer header |
| distributions[0].isAllowPost | Boolean | Whether to allow (true) or deny (false) the POST method           |
| distributions[0].isAllowPut | Boolean | Whether to allow (true) or deny (false) the PUT method           |
| distributions[0].isAllowPatch | Boolean | Whether to allow (true) or deny (false) the PATCH method           |
| distributions[0].isAllowDelete | Boolean | Whether to allow (true) or deny (false) the DELETE method           |
| distributions[0].useLargeFileOptimization | Boolean | Whether to use the large file optimization setting   |
| distributions[0].useOriginCacheControl | Boolean | Whether to use origin server settings (true: use origin server settings, false: use user settings) |
| distributions[0].cacheType             | String  | Cache type setting                                        |
| distributions[0].origins               | List    | List of origin server objects                                    |
| distributions[0].origins[0].origin     | String  | Origin server (domain or IP)                                    |
| distributions[0].origins[0].originPath | String  | Origin server subpath                                        |
| distributions[0].origins[0].httpPort   | Integer | Origin server HTTP protocol port                                             |
| distributions[0].origins[0].httpsPort  | Integer | Origin server HTTPS protocol port                                             |
| distributions[0].useOriginHttpProtocolDowngrade | Boolean | Whether to enable settings to downgrade a request from HTTPS to HTTP when the request is made to the origin server from the CDN server, if the origin server can respond only via HTTP |
| distributions[0].forwardHostHeader     | String  | Host header setting to forward when the CDN server requests content from the origin server ("ORIGIN_HOSTNAME": set to the host name of the origin server, "REQUEST_HOST_HEADER": set to the host header of the client request) |
| distributions[0].rootPathAccessControl  | Object  | Access control setting for the root path of the CDN service | 
| distributions[0].rootPathAccessControl.enable | Boolean | Whether to use (true) or not use (false) access control for the root path        |
| distributions[0].rootPathAccessControl.controlType  | String  | Required if enable is true. Access control method for the root path ("DENY": deny access, "REDIRECT": redirect to the specified path) | 
| distributions[0].rootPathAccessControl.redirectPath | String | Required if controlType is "REDIRECT". Path to redirect requests for the root path to (enter a path that includes /)      |
| distributions[0].rootPathAccessControl.redirectStatusCode | Integer | Required if controlType is "REDIRECT". HTTP response code returned during the redirect        |
| distributions[0].modifyOutgoingResponseHeaderControl                                  | Object  | Setting to add, modify, and delete HTTP response header from CDN  |
| distributions[0].modifyOutgoingResponseHeaderControl.enable                           | Boolean | Whether to use the settings that add/change/delete HTTP response headers  |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList                       | List    | List of HTTP response headers |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].action             | String  | HTTP response header change method |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].standardHeaderName | String  | General HTTP response header name |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].customHeaderName   | String  | Required if standardHeaderName is "OTHER". Custom HTTP response header name |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].headerValue        | String  | HTTP response header value |
| distributions[0].callback              | Object  | Callback to receive the result of the service creation process                      |
| distributions[0].callback.httpMethod   | String  | HTTP method of callback                                         |
| distributions[0].callback.url          | String  | Callback URL                                                   |



<a id="api-4-2"></a>

### Retrieve Service

<a id="api-4-2-1"></a>

#### Request


[URI]

| Method  | URI                                  |
| ---- | ------------------------------------ |
| GET  | /v3.0/appKeys/{appKey}/distributions |


[Parameter]

| Name   | Type   | Required | Valid Range     | Description                         |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| domain | String | Optional      | max 255 characters    | Domain to retrieve (service name)   |
| status | String | Optional      | CDN status code | CDN status code (see [Table] CDN Status Code) |

[Example]
```
curl -X GET "https://cdn.api.nhncloudservice.com/v3.0/appKeys/{appKey}/distributions?domain={domain}" \
 -H "X-NHN-AUTHORIZATION: {secretKey}" \
 -H "Content-Type: application/json"
```

<a id="api-4-2-2"></a>

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
    "cacheKeyQueryParam": "INCLUDE_ALL",
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
        "url": "http://test.callback.com/cdn?appKey={appKey}&status={status}&domain={domain}"
    }
}
```


[Field]

| Field                                   | Type    | Description                                                         |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | Header area                                                    |
| header.isSuccessful                    | Boolean | Whether the request succeeded                                                    |
| header.resultCode                      | Integer | Result code                                                    |
| header.resultMessage                   | String  | Result message                                                  |
| distributions                          | List    | List of created CDN objects                                     |
| distributions[0].domain                | String  | Domain (service name)                                     |
| distributions[0].domainAlias           | List  | List of domain aliases (using a domain owned by an individual or company)                                                  |
| distributions[0].region                | String  | Service region ("GLOBAL": global)             |
| distributions[0].status                | String  | CDN status code (see [Table] CDN Status Code)                                 |
| distributions[0].defaultMaxAge         | Integer  | Cache expiration time (seconds)                                           |
| distributions[0].cacheKeyQueryParam    | String  | Whether to include the request query string in the cache key ("INCLUDE_ALL": include all, "EXCLUDE_ALL": exclude all) |
| distributions[0].referrerType          | String  | Referrer access management ("BLACKLIST": blacklist, "WHITELIST": whitelist) |
| distributions[0].referrers             | List    | List of referrer headers in regular expression format                                 |
| distributions[0].isAllowWhenEmptyReferrer | Boolean | Whether to allow (true) or deny (false) content access when there is no referrer header |
| distributions[0].isAllowPost          | Boolean | Whether to allow (true) or deny (false) the POST method             |
| distributions[0].isAllowPut           | Boolean | Whether to allow (true) or deny (false) the PUT method             |
| distributions[0].isAllowPatch         | Boolean | Whether to allow (true) or deny (false) the PATCH method             |
| distributions[0].isAllowDelete        | Boolean | Whether to allow (true) or deny (false) the DELETE method             |
| distributions[0].useLargeFileOptimization | Boolean | Whether to use the large file optimization setting     |
| distributions[0].useOriginCacheControl | Boolean | Whether to use origin server settings (true: use origin server settings, false: use user settings) |
| distributions[0].cacheType             | String  | Cache type setting                                          |
| distributions[0].origins               | List    | List of origin server objects                                      |
| distributions[0].origins[0].origin     | String  | Origin server (domain or IP)                                      |
| distributions[0].origins[0].originPath | String  | Origin server subpath                                          |
| distributions[0].origins[0].httpPort   | Integer | Origin server HTTP protocol port                                  |
| distributions[0].origins[0].httpsPort  | Integer | Origin server HTTPS protocol port                                 |
| distributions[0].useOriginHttpProtocolDowngrade | Boolean | Whether to enable settings to downgrade a request from HTTPS to HTTP when the request is made to the origin server from the CDN server, if the origin server can respond only via HTTP |
| distributions[0].forwardHostHeader     | String  | Host header setting to forward when the CDN server requests content from the origin server ("ORIGIN_HOSTNAME": set to the host name of the origin server, "REQUEST_HOST_HEADER": set to the host header of the client request) |
| distributions[0].rootPathAccessControl  | Object  | Access control setting for the root path of the CDN service | 
| distributions[0].rootPathAccessControl.enable | Boolean | Whether to use (true) or not use (false) access control for the root path          |
| distributions[0].rootPathAccessControl.controlType  | String  | Required if enable is true. Access control method for the root path ("DENY": deny access, "REDIRECT": redirect to the specified path) | 
| distributions[0].rootPathAccessControl.redirectPath | String | Required if controlType is "REDIRECT". Path to redirect requests for the root path to (enter a path that includes /)        |
| distributions[0].rootPathAccessControl.redirectStatusCode | Integer | Required if controlType is "REDIRECT". HTTP response code returned during the redirect          |
| distributions[0].modifyOutgoingResponseHeaderControl                                  | Object  | Setting to add, modify, and delete HTTP response header from CDN  |
| distributions[0].modifyOutgoingResponseHeaderControl.enable                           | Boolean | Whether to use the settings that add/change/delete HTTP response headers  |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList                       | List    | List of HTTP response headers |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].action             | String  | HTTP response header change method |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].standardHeaderName | String  | General HTTP response header name |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].customHeaderName   | String  | Required if standardHeaderName is "OTHER". Custom HTTP response header name |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].headerValue        | String  | HTTP response header value |
| distributions[0].callback              | Object  | Callback to receive service deployment result                        |
| distributions[0].callback.httpMethod   | String  | HTTP method of callback                                           |
| distributions[0].callback.url          | String  | Callback URL                                                     |


<a id="api-4-3"></a>

### Modify Service

<a id="api-4-3-1"></a>

#### Request


[URI]

| Method  | URI                                  |
| ---- | ------------------------------------ |
| PUT  | /v3.0/appKeys/{appKey}/distributions |


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
          "url": "http://test.callback.com/cdn?appKey={appKey}&status={status}&domain={domain}"
      },
      "description" : "change contents"        
    }
  ]
}
```


[Field]

| Name                  | Type    | Required | Default Value | Valid Range                                                    | Description                                                         |
| --------------------- | ------- | --------- | ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| domain                | String  | Required      |        | max 255 characters                                                   | Domain to modify (service name)                                   |
| useOriginCacheControl | Boolean | Optional      |        | true/false                                                        | Set the cache expiration (true: use origin server settings, false: use user settings). One of useOriginCacheControl or cacheType must be entered.      |
| cacheType             | String  | Optional      |        | BYPASS, NO_STORE            | Set the cache type. One of useOriginCacheControl or cacheType must be entered.                                          |
| referrerType          | String  | Required      |        | BLACKLIST/WHITELIST                                          | Referrer access management ("BLACKLIST": blacklist, "WHITELIST": whitelist) |
| referrers             | List    | Optional      |        |                                                              | List of referrer headers in regular expression format |
| isAllowWhenEmptyReferrer | Boolean | Optional      | true      | true/false             | Whether to allow (true) or deny (false) content access when there is no referrer header             |
| isAllowPost           | Boolean | Optional      | false      | true/false             | Whether to allow (true) or deny (false) the POST method             |
| isAllowPut            | Boolean | Optional      | false      | true/false             | Whether to allow (true) or deny (false) the PUT method             |
| isAllowPatch          | Boolean | Optional      | false      | true/false             | Whether to allow (true) or deny (false) the PATCH method             |
| isAllowDelete         | Boolean | Optional      | false      | true/false             | Whether to allow (true) or deny (false) the DELETE method             |
| useLargeFileOptimization | Boolean | Optional   | false      | true/false             | Whether to use the large file optimization setting     |
| description           | String  | Optional      |        | max 255 characters                                                   | Description                                                         |
| domainAlias           | List    | Optional      |        | max 255 characters                                                   | Domain alias (using a domain owned by an individual or company) |
| defaultMaxAge         | Integer | Optional      | 0      | 0~2,147,483,647                                            | Cache expiration time (seconds). The default value 0 is 604,800 seconds.              |
| cacheKeyQueryParam    | String  | Optional      | INCLUDE_ALL | INCLUDE_ALL/EXCLUDE_ALL                               | Whether to include the request query string in the cache key ("INCLUDE_ALL": include all, "EXCLUDE_ALL": exclude all) |
| origins               | List    | Required      |        |                                                              | Origin server                                                    |
| origins[0].origin     | String  | Required      |        | max 255 characters                                                   | Origin server (domain or IP)                                      |
| origins[0].originPath | String  | Optional      |        | max 8192 characters                                                  | Origin server subpath                                          |
| origins[0].httpPort   | Integer  | Optional      |        |See "[Table 2] Available Origin Server Port Numbers" in [Console User Guide > Origin Server](./console-guide/#origin-server-port)| Origin server HTTP protocol port (one of origins[0].httpPort or origins[0].httpsPort must be entered)  |
| origins[0].httpsPort  | Integer  | Optional      |        |See "[Table 2] Available Origin Server Port Numbers" in [Console User Guide > Origin Server](./console-guide/#origin-server-port) | Origin server HTTPS protocol port (one of origins[0].httpPort or origins[0].httpsPort must be entered) |
| useOriginHttpProtocolDowngrade | Boolean  | Required     | false       | true/false         | Whether to enable settings to downgrade a request from HTTPS to HTTP when the request is made to the origin server from the CDN server, if the origin server can respond only via HTTP |
| forwardHostHeader     | String  | Required      |        | ORIGIN_HOSTNAME<br/>REQUEST_HOST_HEADER   | Host header setting to forward when the CDN server requests content from the origin server ("ORIGIN_HOSTNAME": set to the host name of the origin server, "REQUEST_HOST_HEADER": set to the host header of the client request)|
| rootPathAccessControl  | Object  | Optional |  |  | Access control setting for the root path of the CDN service | 
| rootPathAccessControl.enable | Boolean | Required | false | true/false | Whether to use (true) or not use (false) access control for the root path          |
| rootPathAccessControl.controlType  | String  | Optional |  | DENY, REDIRECT | Required if enable is true. Access control method for the root path ("DENY": deny access, "REDIRECT": redirect to the specified path) | 
| rootPathAccessControl.redirectPath | String | Optional |  | | Required if controlType is "REDIRECT". Path to redirect requests for the root path to (enter a path that includes /)        |
| rootPathAccessControl.redirectStatusCode | Integer | Optional | | 301, 302, 303, 307 |Required if controlType is "REDIRECT". HTTP response code returned during the redirect          |
| modifyOutgoingResponseHeaderControl                                  | Object  | Optional    |             |                                                                       | Setting to add, modify, and delete HTTP response header from CDN                                                                                         |
| modifyOutgoingResponseHeaderControl.enable                           | Boolean | Required    | true        | true/false                                                            | Whether to use the settings that add/change/delete HTTP response headers                                                                          |
| modifyOutgoingResponseHeaderControl.headerList                       | List    | Optional    |         |                                                                       | List of HTTP response headers                                                                                                             |
| modifyOutgoingResponseHeaderControl.headerList[0].action             | String  | Optional    |         | ADD, MODIFY, DELETE                                                   | HTTP response header change method                                                                                                          |
| modifyOutgoingResponseHeaderControl.headerList[0].standardHeaderName | String  | Optional    |         | ACCESS_CONTROL_ALLOW_CREDENTIALS<br/>ACCESS_CONTROL_ALLOW_HEADERS<br/>ACCESS_CONTROL_ALLOW_METHODS<br/>ACCESS_CONTROL_ALLOW_ORIGIN<br/>ACCESS_CONTROL_EXPOSE_HEADERS<br/>ACCESS_CONTROL_MAX_AGE<br/>CACHE_CONTROL<br/>CONTENT_DISPOSITION<br/>CONTENT_TYPE<br/>P3P<br/>PRAGMA<br/>OTHER | General HTTP response header name                                                                                                          |
| modifyOutgoingResponseHeaderControl.headerList[0].customHeaderName   | String  | Optional    |         |                                                      | Required if standardHeaderName is "OTHER". Custom HTTP response header name                                                               |
| modifyOutgoingResponseHeaderControl.headerList[0].headerValue        | String  | Required    |         |                                                      | HTTP response header value                                                                                                              |
| callback              | Object  | Optional      |        |                                                              | Callback URL to receive the CDN service deployment result (the callback setting is optional) |
| callback.httpMethod   | String  | Required      |        | GET/POST/PUT                                                 | HTTP method of callback                                           |
| callback.url          | String  | Required      |        | max 1024 characters                                                  | Callback URL                                                     |

- The default value of `forwardHostHeader` is `REQUEST_HOST_HEADER` if `domainAlias` is set, and `ORIGIN_HOSTNAME` if it is not set.

<a id="api-4-3-2"></a>

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
| header.isSuccessful  | Boolean | Whether the request succeeded  |
| header.resultCode    | Integer | Result code  |
| header.resultMessage | String  | Result message |

<a id="api-4-4"></a>

### Delete Service

<a id="api-4-4-1"></a>

#### Request


[URI]

| Method    | URI                                  |
| ------ | ------------------------------------ |
| DELETE | /v3.0/appKeys/{appKey}/distributions |


[Request Body]

```json
{
    "domains" : [
        "lhcsxuo0.toastcdn.net"
    ]
}
```


[Field]

| Name      | Type     | Required | Default Value  | Valid Range | Description                    |
| ------- | ------ | ----- | ---- | ----- | --------------------- |
| domains | String | Required    |      |       | Domain to delete. Multiple domains can be entered |

> [Caution] If you enter multiple domains, all corresponding services are closed.

<a id="api-4-4-2"></a>

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
| header.isSuccessful  | Boolean | Whether the request succeeded  |
| header.resultCode    | Integer | Result code  |
| header.resultMessage | String  | Result message |


<a id="auth-token-api"></a>

## Auth Token API

<a id="auth-token"></a>

### Create Auth Token

<a id="auth-token-1"></a>

#### Request

[URI]

| Method  | URI                           |
| ---- | ----------------------------- |
| POST | /v3.0/appKeys/{appKey}/auth-token |


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

| Name      | Type   | Required | Default Value | Valid Range             | Description                                                         |
| --------- | ------ | --------- | ------ | --------------------- | ------------------------------------------------------------ |
| encryptKey    | String | Required   |        |             | Token Encryption Key under Auth Token Authentication Access Control, displayed on the NHN Cloud CDN console |
| durationSeconds | Integer | Required |        | 0~2,147,483,647 | Duration for which the generated token is valid (seconds) |
| singlePath      | String | Optional |        |             | Single path to access using the generated token |
| singleWildcardPath | String | Optional |     |             | Single wildcard path to access using the generated token |
| multipleWildcardPath | String | Optional |   |             | Multiple wildcard paths to access using the generated token |
| sessionId |           String | Optional |    |  max string length: 36 bytes           | Generate a token that includes sessionId for a single access request |

* At least one of `singlePath`, `singleWildcardPath`, or `multipleWildcardPath` must be provided.
* For more information on generating and using tokens, see [Console User Guide > Auth Token Authentication Access Control > 2. Generate Token](./console-guide/#create-a-token).


<a id="auth-token-2"></a>

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
| header.isSuccessful  | Boolean | Whether the request succeeded     |
| header.resultCode    | Integer | Result code     |
| header.resultMessage | String  | Result message    |
| authToken             | Object    | Created Auth Token object |
| authToken.singlePathToken | String    | Authentication token generated to access a single path                                 |
| authToken.singleWildcardPathToken | String    | Authentication token generated to access a single wildcard path                 |
| authToken.multipleWildcardPathToken | String  | Authentication token generated to access multiple wildcard paths             |



<a id="api-5"></a>

## 	Purge Cache API

<a id="purge---item"></a>

### Purge Cache - ITEM (particular file type)

<a id="purge---item-1"></a>

#### Request

[URI]

| Method  | URI                           |
| ---- | ----------------------------- |
| POST | /v3.0/appKeys/{appKey}/purge/item |


[Request Body]

```json
{
	"domain": "sample.toastcdn.net",
	"purgeList":["http://sample.toastcdn.net/img_01.png",
  "http://sample.toastcdn.net/img_02.png"]
}
```


[Field]

| Name      | Type   | Required | Default Value | Valid Range             | Description                                                         |
| --------- | ------ | --------- | ------ | --------------------- | ------------------------------------------------------------ |
| domain    | String | Required      |        | max 255 characters            | Domain to purge (service name)                                 |
| purgeList | List | Required      |        |                       | List of URLs to purge |

<a id="purge---item-2"></a>

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
| header.isSuccessful  | Boolean | Whether the request succeeded     |
| header.resultCode    | Integer | Result code     |
| header.resultMessage | String  | Result message    |

<a id="purge---all"></a>

### Purge Cache - ALL (All file types)

<a id="purge---all-1"></a>

#### Request

[URI]

| Method  | URI                           |
| ---- | ----------------------------- |
| POST | /v3.0/appKeys/{appKey}/purge/all |


[Request Body]

```json
{
	"domain": "sample.toastcdn.net"
}
```


[Field]

| Name      | Type   | Required | Default Value | Valid Range             | Description                                                         |
| --------- | ------ | --------- | ------ | --------------------- | ------------------------------------------------------------ |
| domain    | String | Required      |        | max 255 characters            | Domain to purge (service name)                                 |

<a id="purge---all-2"></a>

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
| header.isSuccessful  | Boolean | Whether the request succeeded     |
| header.resultCode    | Integer | Result code     |
| header.resultMessage | String  | Result message    |

- A cache purge request may fail within approximately one hour after a CDN service is newly created. If the failure continues afterward, contact customer support.
- There is a usage limit policy for the Purge API. For more information, see "Cache Purge Usage Limit" in [Console User Guide > CDN Cache Purge](./console-guide/#purge).

<a id="purge"></a>

### Retrieve Purge Status
- When you purge the cache through API v3.0, high-speed cache purging is performed and completed within a few seconds after the request, so no separate API is provided to check the cache purge status.

<a id="alias_domain_api"></a>
## Domain Alias API

<a id="alias_domain_api-1"></a>

### Register Domain Alias

<a id="alias_domain_api-1-1"></a>

#### Request

[URI]

| Method  | URI                                          |
| ---- | -------------------------------------------- |
| POST | /v3.0/appKeys/{appKey}/alias-domains         |


[Request Body]

```json
{
    "domain": "cdn.example.com"
}
```

[Field]

| Name              | Type     | Required | Default Value  | Valid Range                   | Description                                                                                     |
| --------------- | ------ | ----- | ---- | ----------------------- | -------------------------------------------------------------------------------------- |
| domain          | String | Required    |      | FQDN format, min 4 characters, max 253 characters | Domain to register (enter in full domain address format; the toastcdn.net domain cannot be used)                                    |

<a id="alias_domain_api-1-2"></a>

#### Response

[Response Body]

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "domain": {
        "aliasDomainDomSeq": 1,
        "domain": "cdn.example.com",
        "validationStatus": "REQUEST_ACCEPTED",
        "validationTxtName": "_acme-challenge.cdn.example.com.",
        "validationTxtValue": "16WKuUX7ebmYEREEU1CqnPWx0I7wY04EvtF-QL2n-lU",
        "validationHttpPath": "http://cdn.example.com/.well-known/acme-challenge/exampleToken",
        "validationHttpContent": "exampleToken.exampleContent",
        "validationHttpRedirectFrom": "http://cdn.example.com/.well-known/acme-challenge/exampleToken",
        "validationHttpRedirectTo": "http://dcv.akamai.com/.well-known/acme-challenge/exampleToken",
        "validationExpireDatetime": "2025-05-01T00:00:00.000+09:00",
        "validationCompleteDatetime": null,
        "createdAt": "2025-04-17T10:30:00.000+09:00",
        "updatedAt": "2025-04-17T10:30:00.000+09:00"
    }
}
```


[Field]

| Field                                  | Type      | Description                                                                 |
| ----------------------------------- | ------- | ------------------------------------------------------------------ |
| header                              | Object  | Header area                                                              |
| header.isSuccessful                 | Boolean | Whether the request succeeded                                                              |
| header.resultCode                   | Integer | Result code                                                              |
| header.resultMessage                | String  | Result message                                                             |
| domain                              | Object  | Registered domain alias object                                                    |
| domain.aliasDomainDomSeq            | Integer | Domain alias ID                                                          |
| domain.domain                       | String  | Registered domain                                                            |
| domain.validationStatus             | String  | Validation status code (see [Table] Domain Alias Validation Status Codes)                                   |
| domain.validationScope              | String  | Validation scope                                  |
| domain.validationTxtName            | String  | Record name for the DNS TXT record addition method                                          |
| domain.validationTxtValue           | String  | Record value for the DNS TXT record addition method                                            |
| domain.validationHttpPath           | String  | HTTP page URL for the HTTP file validation method                                        |
| domain.validationHttpContent        | String  | Page content value for the HTTP file validation method                                           |
| domain.validationHttpRedirectFrom   | String  | Redirect source URL for the HTTP redirect validation method                                     |
| domain.validationHttpRedirectTo     | String  | Redirect target URL for the HTTP redirect validation method                                     |
| domain.validationExpireDatetime     | DateTime | Validation token expiration date and time                                                        |
| domain.validationCompleteDatetime   | DateTime | Validation completion date and time                                                            |
| domain.distributionSeq              | Integer | ID of the linked CDN service                                                      |
| domain.distribution                 | Object  | Information of the linked CDN service                                                      |
| domain.distribution.domain          | String  | CDN service domain                                                         |
| domain.distribution.status          | String  | CDN service status code (see [Table] CDN Status Code)                                     |
| domain.createdAt                    | DateTime | Creation date and time                                                              |
| domain.updatedAt                    | DateTime | Modification date and time                                                              |


<a id="alias_domain_api-2"></a>

### List Domain Aliases

<a id="alias_domain_api-2-1"></a>

#### Request

[URI]

| Method | URI                                          |
| --- | -------------------------------------------- |
| GET | /v3.0/appKeys/{appKey}/alias-domains         |


[Parameter]

| Name     | Type      | Required | Valid Range                                                                       | Description                                       |
| ------ | ------- | ----- | --------------------------------------------------------------------------- | ---------------------------------------- |
| domain | String  | Optional    | max 253 characters                                                                     | Domain to retrieve                                  |
| status | String  | Optional    | REQUEST_ACCEPTED, VALIDATION_IN_PROGRESS, VALIDATED, TOKEN_EXPIRED | Validation status code (multiple statuses can be entered, separated by commas)                |
| page   | Integer | Optional    | Default: 1                                                                      | Page number                                   |
| limit  | Integer | Optional    | Default: 10, maximum: 1,000                                                           | Number of items retrieved per page                               |

[Example]
```
curl -X GET "https://cdn.api.nhncloudservice.com/v3.0/appKeys/{appKey}/alias-domains?status=VALIDATED&page=1&limit=10" \
 -H "X-NHN-AUTHORIZATION: {secretKey}" \
 -H "Content-Type: application/json"
```

<a id="alias_domain_api-2-2"></a>

#### Response

[Response Body]

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "paging": {
        "page": 1,
        "limit": 10,
        "totalCount": 1
    },
    "domains": [
        {
            "aliasDomainDomSeq": 1,
            "domain": "cdn.example.com",
            "validationStatus": "VALIDATED",
            "validationTxtName": "_acme-challenge.cdn.example.com.",
            "validationTxtValue": "16WKuUX7ebmYEREEU1CqnPWx0I7wY04EvtF-QL2n-lU",
            "validationHttpPath": "http://cdn.example.com/.well-known/acme-challenge/exampleToken",
            "validationHttpContent": "exampleToken.exampleContent",
            "validationHttpRedirectFrom": "http://cdn.example.com/.well-known/acme-challenge/exampleToken",
            "validationHttpRedirectTo": "http://dcv.akamai.com/.well-known/acme-challenge/exampleToken",
            "validationExpireDatetime": "2025-05-01T00:00:00.000+09:00",
            "validationCompleteDatetime": "2025-04-18T12:00:00.000+09:00",
            "distributionSeq": null,
            "distribution": null,
            "createdAt": "2025-04-17T10:30:00.000+09:00",
            "updatedAt": "2025-04-18T12:00:00.000+09:00"
        }
    ]
}
```


[Field]

| Field                                    | Type       | Description                                                                 |
| ------------------------------------- | -------- | ------------------------------------------------------------------ |
| header                                | Object   | Header area                                                              |
| header.isSuccessful                   | Boolean  | Whether the request succeeded                                                              |
| header.resultCode                     | Integer  | Result code                                                              |
| header.resultMessage                  | String   | Result message                                                             |
| paging                                | Object   | Paging area                                                             |
| paging.page                           | Integer  | Page number                                                             |
| paging.limit                          | Integer  | Number of items retrieved per page                                                         |
| paging.totalCount                     | Integer  | Total count                                                              |
| domains                               | List     | List of domain alias objects                                                     |
| domains[0].aliasDomainDomSeq          | Integer  | Domain alias ID                                                          |
| domains[0].domain                     | String   | Registered domain                                                            |
| domains[0].validationStatus           | String   | Validation status code (see [Table] Domain Alias Validation Status Codes)                                   |
| domains[0].validationTxtName          | String   | Record name for the DNS TXT record addition method                                          |
| domains[0].validationTxtValue         | String   | Record value for the DNS TXT record addition method                                            |
| domains[0].validationHttpPath         | String   | HTTP page URL for the HTTP file validation method                                        |
| domains[0].validationHttpContent      | String   | Page content value for the HTTP file validation method                                           |
| domains[0].validationHttpRedirectFrom | String   | Redirect source URL for the HTTP redirect validation method                                     |
| domains[0].validationHttpRedirectTo   | String   | Redirect target URL for the HTTP redirect validation method                                     |
| domains[0].validationExpireDatetime   | DateTime | Validation token expiration date and time                                                        |
| domains[0].validationCompleteDatetime | DateTime | Validation completion date and time                                                            |
| domains[0].distributionSeq            | Integer  | ID of the linked CDN service                                                      |
| domains[0].distribution               | Object   | Information of the linked CDN service                                                      |
| domains[0].distribution.domain        | String   | CDN service domain                                                         |
| domains[0].distribution.status        | String   | CDN service status code (see [Table] CDN Status Code)                                     |
| domains[0].createdAt                  | DateTime | Creation date and time                                                              |
| domains[0].updatedAt                  | DateTime | Modification date and time                                                              |


<a id="alias_domain_api-3"></a>

### Delete Domain Alias

<a id="alias_domain_api-3-1"></a>

#### Request

[URI]

| Method    | URI                                                        |
| ------ | ---------------------------------------------------------- |
| DELETE | /v3.0/appKeys/{appKey}/alias-domains/{aliasDomainDomSeq}   |


[Parameter]

| Name                | Type      | Required | Valid Range | Description          |
| ----------------- | ------- | ----- | ----- | ----------- |
| aliasDomainDomSeq | Integer | Required    |       | Domain alias ID  |


[Example]
```
curl -X DELETE "https://cdn.api.nhncloudservice.com/v3.0/appKeys/{appKey}/alias-domains/{aliasDomainDomSeq}" \
 -H "X-NHN-AUTHORIZATION: {secretKey}" \
 -H "Content-Type: application/json"
```

<a id="alias_domain_api-3-2"></a>

#### Response

[Response Body]

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    }
}
```


[Field]

| Field                   | Type      | Description     |
| -------------------- | ------- | ------ |
| header               | Object  | Header area  |
| header.isSuccessful  | Boolean | Whether the request succeeded  |
| header.resultCode    | Integer | Result code  |
| header.resultMessage | String  | Result message |

- A domain linked to a CDN service cannot be deleted. Unlink the domain alias from the CDN service before deleting it.


<a id="alias_domain_api-4"></a>

### Run Domain Validation

<a id="alias_domain_api-4-1"></a>

#### Request

[URI]

| Method  | URI                                                                     |
| ---- |-------------------------------------------------------------------------|
| POST | /v3.0/appKeys/{appKey}/alias-domains/{aliasDomainDomSeq}/token/validate |


[Request Body]

```json
{
    "validationMethod": "DNS_TXT"
}
```

[Field]

| Name               | Type     | Required | Default Value | Valid Range          | Description                                                            |
| ---------------- | ------ | ----- | --- | -------------- | ------------------------------------------------------------- |
| validationMethod | String | Required    |     | DNS_TXT, HTTP  | Validation method ("DNS_TXT": DNS TXT record addition method, "HTTP": HTTP file or redirect validation method) |


<a id="alias_domain_api-4-2"></a>

#### Response

[Response Body]

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "domain": {
        "aliasDomainDomSeq": 1,
        "domain": "cdn.example.com",
        "validationStatus": "VALIDATION_IN_PROGRESS",
        "validationTxtName": "_acme-challenge.cdn.example.com.",
        "validationTxtValue": "16WKuUX7ebmYEREEU1CqnPWx0I7wY04EvtF-QL2n-lU",
        "validationHttpPath": "http://cdn.example.com/.well-known/acme-challenge/exampleToken",
        "validationHttpContent": "exampleToken.exampleContent",
        "validationHttpRedirectFrom": "http://cdn.example.com/.well-known/acme-challenge/exampleToken",
        "validationHttpRedirectTo": "http://dcv.akamai.com/.well-known/acme-challenge/exampleToken",
        "validationExpireDatetime": "2025-05-01T00:00:00.000+09:00",
        "validationCompleteDatetime": null,
        "distributionSeq": null,
        "distribution": null,
        "createdAt": "2025-04-17T10:30:00.000+09:00",
        "updatedAt": "2025-04-17T14:00:00.000+09:00"
    }
}
```

[Field]

| Field                                  | Type      | Description                                                                 |
| ----------------------------------- | ------- | ------------------------------------------------------------------ |
| header                              | Object  | Header area                                                              |
| header.isSuccessful                 | Boolean | Whether the request succeeded                                                              |
| header.resultCode                   | Integer | Result code                                                              |
| header.resultMessage                | String  | Result message                                                             |
| domain                              | Object  | Domain alias object                                                        |
| domain.aliasDomainDomSeq            | Integer | Domain alias ID                                                          |
| domain.domain                       | String  | Registered domain                                                            |
| domain.validationStatus             | String  | Validation status code (see [Table] Domain Alias Validation Status Codes)                                   |
| domain.validationScope              | String  | Validation scope                                  |
| domain.validationTxtName            | String  | Record name for the DNS TXT record addition method                                          |
| domain.validationTxtValue           | String  | Record value for the DNS TXT record addition method                                            |
| domain.validationHttpPath           | String  | HTTP page URL for the HTTP file validation method                                        |
| domain.validationHttpContent        | String  | Page content value for the HTTP file validation method                                           |
| domain.validationHttpRedirectFrom   | String  | Redirect source URL for the HTTP redirect validation method                                     |
| domain.validationHttpRedirectTo     | String  | Redirect target URL for the HTTP redirect validation method                                     |
| domain.validationExpireDatetime     | DateTime | Validation token expiration date and time                                                        |
| domain.validationCompleteDatetime   | DateTime | Validation completion date and time                                                            |
| domain.distributionSeq              | Integer | ID of the linked CDN service                                                      |
| domain.distribution                 | Object  | Information of the linked CDN service                                                      |
| domain.distribution.domain          | String  | CDN service domain                                                         |
| domain.distribution.status          | String  | CDN service status code (see [Table] CDN Status Code)                                     |
| domain.createdAt                    | DateTime | Creation date and time                                                              |
| domain.updatedAt                    | DateTime | Modification date and time                                                              |

- Before running domain validation, you must first complete the DNS TXT record addition or HTTP file/redirect configuration.
- If the validation token has expired, you cannot run validation. Obtain a new token through the token reissue API, and then run validation again.


<a id="alias_domain_api-5"></a>

### Refresh Domain Validation Status

<a id="alias_domain_api-5-1"></a>

#### Request

[URI]

| Method  | URI                                                                    |
| ---- |------------------------------------------------------------------------|
| POST | /v3.0/appKeys/{appKey}/alias-domains/{aliasDomainDomSeq}/token/refresh |


[Example]
```
curl -X POST "https://cdn.api.nhncloudservice.com/v3.0/appKeys/{appKey}/alias-domains/{aliasDomainDomSeq}/token/refresh" \
 -H "X-NHN-AUTHORIZATION: {secretKey}" \
 -H "Content-Type: application/json"
```

<a id="alias_domain_api-5-2"></a>

#### Response

[Response Body]

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "domain": {
        "aliasDomainDomSeq": 1,
        "domain": "cdn.example.com",
        "validationStatus": "VALIDATED",
        "validationTxtName": "_acme-challenge.cdn.example.com.",
        "validationTxtValue": "16WKuUX7ebmYEREEU1CqnPWx0I7wY04EvtF-QL2n-lU",
        "validationHttpPath": null,
        "validationHttpContent": null,
        "validationHttpRedirectFrom": null,
        "validationHttpRedirectTo": null,
        "validationExpireDatetime": "2025-05-01T00:00:00.000+09:00",
        "validationCompleteDatetime": "2025-04-18T12:00:00.000+09:00",
        "distributionSeq": null,
        "distribution": null,
        "createdAt": "2025-04-17T10:30:00.000+09:00",
        "updatedAt": "2025-04-18T12:00:00.000+09:00"
    }
}
```

[Field]

| Field                                  | Type      | Description                                                                 |
| ----------------------------------- | ------- | ------------------------------------------------------------------ |
| header                              | Object  | Header area                                                              |
| header.isSuccessful                 | Boolean | Whether the request succeeded                                                              |
| header.resultCode                   | Integer | Result code                                                              |
| header.resultMessage                | String  | Result message                                                             |
| domain                              | Object  | Domain alias object                                                        |
| domain.aliasDomainDomSeq            | Integer | Domain alias ID                                                          |
| domain.domain                       | String  | Registered domain                                                            |
| domain.validationStatus             | String  | Validation status code (see [Table] Domain Alias Validation Status Codes)                                   |
| domain.validationScope              | String  | Validation scope                                  |
| domain.validationTxtName            | String  | Record name for the DNS TXT record addition method                                          |
| domain.validationTxtValue           | String  | Record value for the DNS TXT record addition method                                            |
| domain.validationHttpPath           | String  | HTTP page URL for the HTTP file validation method                                        |
| domain.validationHttpContent        | String  | Page content value for the HTTP file validation method                                           |
| domain.validationHttpRedirectFrom   | String  | Redirect source URL for the HTTP redirect validation method                                     |
| domain.validationHttpRedirectTo     | String  | Redirect target URL for the HTTP redirect validation method                                     |
| domain.validationExpireDatetime     | DateTime | Validation token expiration date and time                                                        |
| domain.validationCompleteDatetime   | DateTime | Validation completion date and time                                                            |
| domain.distributionSeq              | Integer | ID of the linked CDN service                                                      |
| domain.distribution                 | Object  | Information of the linked CDN service                                                      |
| domain.distribution.domain          | String  | CDN service domain                                                         |
| domain.distribution.status          | String  | CDN service status code (see [Table] CDN Status Code)                                     |
| domain.createdAt                    | DateTime | Creation date and time                                                              |
| domain.updatedAt                    | DateTime | Modification date and time                                                              |


<a id="alias_domain_api-6"></a>

### Reissue Validation Token

<a id="alias_domain_api-6-1"></a>

#### Request

[URI]

| Method  | URI                                                                    |
| ---- |------------------------------------------------------------------------|
| POST | /v3.0/appKeys/{appKey}/alias-domains/{aliasDomainDomSeq}/token/reissue |


[Example]
```
curl -X POST "https://cdn.api.nhncloudservice.com/v3.0/appKeys/{appKey}/alias-domains/{aliasDomainDomSeq}/token/reissue" \
 -H "X-NHN-AUTHORIZATION: {secretKey}" \
 -H "Content-Type: application/json"
```

<a id="alias_domain_api-6-2"></a>

#### Response

[Response Body]

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "domain": {
        "aliasDomainDomSeq": 1,
        "domain": "cdn.example.com",
        "validationStatus": "REQUEST_ACCEPTED",
        "validationTxtName": "_acme-challenge.cdn.example.com.",
        "validationTxtValue": "newReissuedTokenValue",
        "validationHttpPath": "http://cdn.example.com/.well-known/acme-challenge/newToken",
        "validationHttpContent": "newToken.newContent",
        "validationHttpRedirectFrom": "http://cdn.example.com/.well-known/acme-challenge/newToken",
        "validationHttpRedirectTo": "http://dcv.akamai.com/.well-known/acme-challenge/newToken",
        "validationExpireDatetime": "2025-05-15T00:00:00.000+09:00",
        "validationCompleteDatetime": null,
        "distributionSeq": null,
        "distribution": null,
        "createdAt": "2025-04-17T10:30:00.000+09:00",
        "updatedAt": "2025-05-01T10:00:00.000+09:00"
    }
}
```

[Field]

| Field                                  | Type      | Description                                                                 |
| ----------------------------------- | ------- | ------------------------------------------------------------------ |
| header                              | Object  | Header area                                                              |
| header.isSuccessful                 | Boolean | Whether the request succeeded                                                              |
| header.resultCode                   | Integer | Result code                                                              |
| header.resultMessage                | String  | Result message                                                             |
| domain                              | Object  | Domain alias object                                                        |
| domain.aliasDomainDomSeq            | Integer | Domain alias ID                                                          |
| domain.domain                       | String  | Registered domain                                                            |
| domain.validationStatus             | String  | Validation status code (see [Table] Domain Alias Validation Status Codes)                                   |
| domain.validationScope              | String  | Validation scope                                  |
| domain.validationTxtName            | String  | Record name for the DNS TXT record addition method                                          |
| domain.validationTxtValue           | String  | Record value for the DNS TXT record addition method                                            |
| domain.validationHttpPath           | String  | HTTP page URL for the HTTP file validation method                                        |
| domain.validationHttpContent        | String  | Page content value for the HTTP file validation method                                           |
| domain.validationHttpRedirectFrom   | String  | Redirect source URL for the HTTP redirect validation method                                     |
| domain.validationHttpRedirectTo     | String  | Redirect target URL for the HTTP redirect validation method                                     |
| domain.validationExpireDatetime     | DateTime | Validation token expiration date and time                                                        |
| domain.validationCompleteDatetime   | DateTime | Validation completion date and time                                                            |
| domain.distributionSeq              | Integer | ID of the linked CDN service                                                      |
| domain.distribution                 | Object  | Information of the linked CDN service                                                      |
| domain.distribution.domain          | String  | CDN service domain                                                         |
| domain.distribution.status          | String  | CDN service status code (see [Table] CDN Status Code)                                     |
| domain.createdAt                    | DateTime | Creation date and time                                                              |
| domain.updatedAt                    | DateTime | Modification date and time                                                              |

- When the token is reissued, the previous validation information is reset, and you must run validation again with the new token information.
- If the validation token has expired (`TOKEN_EXPIRED`), you can call this API to obtain a new token.

<a id="alias_domain_api-6-3"></a>

#### Domain Alias Validation Status Codes

The following are status codes that indicate the validation status of a domain alias. You can check the validation status when retrieving domain aliases.

| Value                      | Description                               |
| ---------------------- | -------------------------------- |
| REQUEST_ACCEPTED       | The domain has been registered, and validation is pending                |
| VALIDATION_IN_PROGRESS | Domain ownership validation is in progress                 |
| VALIDATED              | Domain ownership validation completed; the domain can be linked to a CDN service     |
| TOKEN_EXPIRED          | Validation token expired; reissue the token and validate again     |


<a id="api-6"></a>

## Certificate API
<a id="api-6-1"></a>

### Issue New Certificate
<a id="api-6-1-1"></a>

#### Request

[URI]

| Method  | URI                           |
| ---- | ----------------------------- |
| POST | /v3.0/appKeys/{appKey}/certificates|


[Request Body]

```json
{
    "certificateDomain": "example.domain.com",
    "callbackHttpMethod": "POST",
    "callbackUrl": "http://test.callback.com/cdn-certificate?appKey={appKey}&status={status}&domain={domain}"   
}
```


[Field]

| Name      | Type   | Required | Default Value | Valid Range             | Description                                                         |
| --------- | ------ | --------- | ------ | --------------------- | ------------------------------------------------------------ |
| certificateDomain    | String | Required      |        | max 255 characters            | Domain to issue a new certificate for (enter in full domain address format)|
| callbackHttpMethod  | String | Optional      |        | GET/POST/PUT        | HTTP method of the callback to receive the certificate creation result |
| callbackUrl         | String | Optional      |        | max 1024 characters           | Callback URL to receive the certificate creation result       |

* For more information on issuing certificates, see [Console User Guide > Certificate Management > Issue New Certificate](./console-guide/#issue-new-certificates).

<a id="api-6-1-2"></a>

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
| header.isSuccessful  | Boolean | Whether the request succeeded     |
| header.resultCode    | Integer | Result code     |
| header.resultMessage | String  | Result message    |
| certificates         | List    | List of issued certificates |
| certificates[0].sanDnsId | String | Certificate ID    |
| certificates[0].dnsName  | String | Certificate domain  |
| certificates[0].dnsStatus | String | Certificate issuance status code (see [Table] Certificate Issuance Status Codes) |
| certificates[0].callbackHttpMethod | String | HTTP method of the callback to receive the certificate creation result |
| certificates[0].callbackUrl | String | Callback URL to receive the certificate creation result |
| certificates[0].createDatetime | DateTime | Certificate creation date and time |
| certificates[0].updateDatetime | DateTime | Certificate modification date and time |
| certificates[0].hasCname | Boolean | Whether the CNAME record is set |
| certificates[0].hasDistributionDomain | Boolean | Whether the CDN service is linked |
| certificates[0].renewalStartDate | DateTime | Certificate renewal start date and time |
| certificates[0].renewalEndDate | DateTime | Certificate renewal end date and time |

<a id="api-6-2"></a>

### List Certificates
<a id="api-6-2-1"></a>

#### Request

[URI]

| Method  | URI                           |
| ---- | ----------------------------- |
| GET | /v3.0/appKeys/{appKey}/certificates|


<a id="api-6-2-2"></a>

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
| header.isSuccessful  | Boolean | Whether the request succeeded     |
| header.resultCode    | Integer | Result code     |
| header.resultMessage | String  | Result message    |
| certificates         | List    | List of issued certificates |
| certificates[0].sanDnsId | String | Certificate ID    |
| certificates[0].dnsName  | String | Certificate domain  |
| certificates[0].dnsStatus | String | Certificate issuance status code (see [Table] Certificate Issuance Status Codes) |
| certificates[0].callbackHttpMethod | String | HTTP method of the callback to receive the certificate creation result |
| certificates[0].callbackUrl | String | Callback URL to receive the certificate creation result |
| certificates[0].createDatetime | DateTime | Certificate creation date and time |
| certificates[0].updateDatetime | DateTime | Certificate modification date and time |
| certificates[0].hasCname | Boolean | Whether the CNAME record is set |
| certificates[0].hasDistributionDomain | Boolean | Whether the CDN service is linked |
| certificates[0].renewalStartDate | DateTime | Certificate renewal start date and time |
| certificates[0].renewalEndDate | DateTime | Certificate renewal end date and time |

<a id="api-6-3"></a>

### Delete Certificate
<a id="api-6-3-1"></a>

#### Request

[URI]

| Method  | URI                           |
| ---- | ----------------------------- |
| DELETE | /v3.0/appKeys/{appKey}/certificates|


[Parameter]

| Name   | Type   | Required | Valid Range     | Description                         |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| dnsIdList | String | Required      |     | List of certificate IDs (sanDnsId) to delete (certificate IDs separated by commas)   |

[Example]
```
curl -X DELETE "https://cdn.api.nhncloudservice.com/v3.0/appKeys/{appKey}/certificates?dnsIdList={dnsIdList}" \
 -H "X-NHN-AUTHORIZATION: {secretKey}" \
 -H "Content-Type: application/json"
```

<a id="api-6-3-2"></a>

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
| header.isSuccessful  | Boolean | Whether the request succeeded     |
| header.resultCode    | Integer | Result code     |
| header.resultMessage | String  | Result message    |


<a id="section-1"></a>

## Callback Response
<a id="cdn-2"></a>

### CDN Service
If the callback feature is configured for the CDN service, the configured callback URL is called when a create, modify, suspend, resume, or delete change is completed.
When the callback is called, the request body contains the following CDN service configuration information.

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
| header.isSuccessful                    | Boolean | Whether the request succeeded                                                    |
| header.resultCode                      | Integer | Result code                                                    |
| header.resultMessage                   | String  | Result message                                                  |
| distribution                          | Object    | CDN object for which the change has been completed                                   |
| distribution.appKey                   | String    | App key                                  |
| distribution.domain                | String  | Domain (service name)                                     |
| distribution.domainAlias           | List  | List of domain aliases (using a domain owned by an individual or company)                                 |
| distribution.region                | String  | Service region ("GLOBAL": global)             |
| distribution.status                | String  | CDN status code (see [Table] CDN Status Code)                                 |
| distribution.defaultMaxAge         | Integer  | Cache expiration time (seconds)                                           |
| distribution.cacheKeyQueryParam    | String  | Whether to include the request query string in the cache key ("INCLUDE_ALL": include all, "EXCLUDE_ALL": exclude all) |
| distribution.referrerType          | String  | Referrer access management ("BLACKLIST": blacklist, "WHITELIST": whitelist) |
| distribution.referrers             | List    | List of referrer headers in regular expression format                                 |
| distribution.useOriginCacheControl | Boolean | Whether to use origin server settings (true: use origin server settings, false: use user settings) |
| distribution.createTime            | DateTime | Creation date and time                                         |
| distribution.deleteTime            | DateTime | Deletion date and time                                         |
| distribution.origins               | List    | List of origin server objects                                      |
| distribution.origins[0].origin     | String  | Origin server (domain or IP)                                      |
| distribution.origins[0].originPath | String  | Origin server subpath                                          |
| distribution.origins[0].httpPort   | Integer | Origin server HTTP protocol port                                               |
| distribution.origins[0].httpsPort  | Integer | Origin server HTTPS protocol port                                               |
| distribution.useOriginHttpProtocolDowngrade | Boolean | Whether to enable settings to downgrade a request from HTTPS to HTTP when the request is made to the origin server from the CDN server, if the origin server can respond only via HTTP |
| distribution.forwardHostHeader     | String  | Host header setting to forward when the CDN server requests content from the origin server ("ORIGIN_HOSTNAME": set to the host name of the origin server, "REQUEST_HOST_HEADER": set to the host header of the client request) |
| distribution.rootPathAccessControl  | Object  | Access control setting for the root path of the CDN service | 
| distribution.rootPathAccessControl.enable | Boolean | Whether to use (true) or not use (false) access control for the root path          |
| distribution.rootPathAccessControl.controlType  | String  | Required if enable is true. Access control method for the root path ("DENY": deny access, "REDIRECT": redirect to the specified path) | 
| distribution.rootPathAccessControl.redirectPath | String | Required if controlType is "REDIRECT". Path to redirect requests for the root path to (enter a path that includes /)        |
| distribution.rootPathAccessControl.redirectStatusCode | Integer | Required if controlType is "REDIRECT". HTTP response code returned during the redirect         |
| distribution.modifyOutgoingResponseHeaderControl                      | Object  | Setting to add, modify, and delete HTTP response header from CDN  |
| distribution.modifyOutgoingResponseHeaderControl.enable               | Boolean | Whether to use the settings that add/change/delete HTTP response headers  |
| distribution.modifyOutgoingResponseHeaderControl.headerList           | List    | List of HTTP response headers |
| distribution.modifyOutgoingResponseHeaderControl.headerList[0].action | String  | HTTP response header change method |
| distribution.modifyOutgoingResponseHeaderControl.headerList[0].standardHeaderName | String  | General HTTP response header name |
| distribution.modifyOutgoingResponseHeaderControl.headerList[0].customHeaderName | String  | Required if standardHeaderName is "OTHER". Custom HTTP response header name |
| distribution.modifyOutgoingResponseHeaderControl.headerList[0].headerValue | String  | HTTP response header value |
| distribution.callback              | Object  | Callback to receive service deployment result                        |
| distribution.callback.httpMethod   | String  | HTTP method of callback                                           |
| distribution.callback.url          | String  | Callback URL                                                     |

<a id="section-1-1"></a>

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
| header.isSuccessful                    | Boolean | Whether the request succeeded                                                    |
| header.resultCode                      | Integer | Result code                                                    |
| header.resultMessage                   | String  | Result message                                                  |
| certificate                          | Object    | Certificate object for which the change has been completed                                  |
| certificate.sanDnsId                   | String    | Certificate ID                                  |
| certificate.distributionSeq                   | String    | ID of the linked CDN service                                  |
| certificate.dnsName  | String | Certificate domain  |
| certificate.dnsStatus | String | Certificate issuance status code (see [Table] Certificate Issuance Status Codes) |
| certificate.validationDnsRecordName | String | Domain validation information (record name for the DNS TXT record addition method)  |
| certificate.validationDnsToken | String | Domain validation information (record value for the DNS TXT record addition method)  |
| certificate.validationHtmlUrl | String | Domain validation information (HTTP page URL for the HTTP page addition method)  |
| certificate.validationHtmlToken | String | Domain validation information (HTTP page body content value for the HTTP page addition method)  |
| certificate.validationExpireDatetime | DateTime | Domain validation expiration date and time  |
| certificate.createDatetime | DateTime | Certificate creation date and time |
| certificate.updateDatetime | DateTime | Certificate modification date and time |
| certificate.deleteDatetime | DateTime | Certificate deletion date and time |
| certificate.callbackHttpMethod | String | HTTP method of the callback to receive the certificate creation result |
| certificate.callbackUrl | String | Callback URL to receive the certificate creation result |
