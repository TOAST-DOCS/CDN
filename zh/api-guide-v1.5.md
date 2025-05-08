## Content Delivery > CDN > API v1.5 Guide

This document describes Public API v1.5 provided by NHN Cloud CDN.

## Common API Information

### Domain

| Name         | Domain                             |
| --------------- | ------------------------------------- |
| Public API CDN Domain | https://kr1-cdn.api.nhncloudservice.com |

### Prerequisites

Requires Appkey and SecretKey API, which are available in **URL & Appkey** on top right of the console.

### Common Request Information

#### Request Header

| Name          | Description                   |
| ------------- | ----------------------------- |
| Authorization | SecretKey issued on a console |

#### Path Parameter

In all APIs, the appKey must be specified in the path parameter.
* e.g.) /v1.5/appKeys/**{appKey}**/distributions

| Name   | Description                |
| ------ | -------------------------- |
| appKey | Appkey issued on a console |

### Common Response Information

#### Header

Respond with **200 OK** to all API requests. For more details, see the header at the response body as below.

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Success"
    }
}
```


[Fields]

| Field                | Type    | Description       |
| -------------------- | ------- | ----------------- |
| header               | Object  | Header area       |
| header.isSuccessful  | Boolean | Successful or not |
| header.resultCode    | Integer | Result code       |
| header.resultMessage | String  | Result message    |

#### CDN Status Codes

Below shows the status codes of CDN service, which are available at the query of service.

| Value      | Description                           |
| ---------- | ------------------------------------- |
| OPENING    | Service is starting                   |
| OPEN       | In service                            |
| MODIFYING  | Under modification                    |
| RESUME     | Resumed                               |
| SUSPENDING | Under suspension                      |
| SUSPEND    | Suspended                             |
| CLOSING    | Closing                               |
| CLOSE      | Closed                                |
| ERROR      | Error occurred while creating service |


## Service API

### Create

#### Request


[URI]

| Method | URI                                  |
| ------ | ------------------------------------ |
| POST   | /v1.5/appKeys/{appKey}/distributions |


[Request Body]

```json
{
   "distributions":[
       {
			"region": "GLOBAL",
            "useOriginHttpProtocolDowngrade": false,
            "forwardHostHeader": "ORIGIN_HOSTNAME",
			"useOrigin" : "N",
			"referrerType" : "BLACKLIST",
			"description" : "sample-cdn",
			"maxAge": 86400,
			"referrers" : "cloud.nhn.com",
            "isAllowWhenEmptyReferrer" : true,
			"origins" : [
				{
					"origin" : "static.origin.com",
					"port" : 80,
					"originPath" : "/resources"
				}
			],
            "callback": {
                "httpMethod": "GET",
                "url": "http://test.callback.com/cdn?=appKey={appKey}&status={status}&domain={domain}"
            }
		}
	]
}
```

[Field]

| Name                                   | Type    | Required | Default | Valid Range                                  | Description                                                  |
| -------------------------------------- | ------- | -------- | ------- | -------------------------------------------- | ------------------------------------------------------------ |
| distributions                          | List    | Required |         |                                              | List of CDN objects to create                                |
| distributions[0].region                | String  | Required      |        | GLOBAL                | Service Region ("GLOBAL": Global service)           |
| distributions[0].useOriginHttpProtocolDowngrade | Boolean  | Required     |        | true/false         | Whether to enable settings to downgrade a request from HTTPS to HTTP when the request is made to origin server from CDN server, if the origin server can respond only via HTTP  |
| distributions[0].forwardHostHeader     | String  | Required      |        | ORIGIN_HOSTNAME/REQUEST_HOST_HEADER   | Setting host header to be delivered when CDN server requests content to origin server  ("ORIGIN_HOSTNAME": Set as host name for origin server, "REQUEST_HOST_HEADER": Set as host header for client requests)|
| distributions[0].useOrigin             | String  | Required |         | Y/N                                          | Cache expiration setting ("Y": Original setting, "N":User-configured) |
| distributions[0].referrerType          | String  | Required |         | BLACKLIST/WHITELIST                          | Referrer access management ("BLACKLIST": Blacklist, "WHITELIST": Whitelist) |
| distributions[0].description           | String  | Optional |         | Up to  255 characters                        | Description                                                  |
| distributions[0].domainAlias           | String  | Optional |         | Up to 255 characters                         | Domain alias (Use personal or company-owned domains; delimit by \n tokens.) |
| distributions[0].maxAge                | Integer | Optional | 0       | 0~2,147,483,647                              | Cache expiration time (second); default 0 refers to 604,800 seconds. |
| distributions[0].referrers             | String  | Optional |         | Up to 1024 characters, including '\n' tokens | Referrers (Delimit by \n tokens.)                            |
| distributions[0].isAllowWhenEmptyReferrer | Boolean | Optional      | true      | true/false             | True/False for Content Access if Referer Header is Unavailable             |
| distributions[0].origins               | List    | Required |         |                                              | List of origin server objects                                |
| distributions[0].origins[0].origin     | String  | Required |         | Up to 255 characters                         | Origin server (domain or IP)                                 |
| distributions[0].origins[0].port       | Integer  | Optional      |        |See [Console User Guide] > [[Table 2] Port number of available origin server of [Origin Server](./console-guide/#_2)] | HTTP Protocol Port for Origin Server <br>(Do not enter origins[0].httpPort and origins[0].httpsPort when origins[0].port is set.)|
| distributions[0].origins[0].httpPort   | Integer  | Optional      |        |See [Console User Guide] > [[Table 2] Port number of available origin server of [Origin Server](./console-guide/#_2)]| HTTP Protocol Port for Origin Server <br>(Must enter either origins[0].httpPort or origins[0].httpsPort when origins[0].port is not set.)  |
| distributions[0].origins[0].httpsPort  | Integer  | Optional      |        |See [Console User Guide] > [[Table 2] Port number of available origin server of [Origin Server](./console-guide/#_2)] | HTTP Protocol Port for Origin Server <br>(Must enter either origins[0].httpPort or origins[0].httpsPort when origins[0].port is not set.) |
| distributions[0].origins[0].originPath | String  | Optional |         | Up to 8192 characters                        | Lower paths of origin server (path must include /.)          |
| distributions[0].callback              | Object  | Optional |         |                                              | Callback URL to receive processing result of CDN creation (callback setting is optional.) |
| distributions[0].callback.httpMethod   | String  | Required |         | GET/POST/PUT                                 | HTTP method of callback                                      |
| distributions[0].callback.url          | String  | Required |         | Up to 1024 characters                        | Callback URL                                                 |

- The default value of forwardHostHeader is REQUEST_HOST_HEADER if domainAlias is set, or ORIGIN_HOSTNAME otherwise.


#### Response


[Response Body]

```json
{
    "header": {
        "resultCode": Integer,
        "resultMessage": String,
        "isSuccessful": Boolean
    },
    "distributions": [
        {
            "domain" : "lhcsxuo0.toastcdn.net"
            "region" :  "GLOBAL",
            "description" :  "api test pad",
            "status" : "OPENING",
            "useOrigin" :  "N",
            "domainAlias" :  "test.domain.com",
            "referrerType" :  "BLACKLIST",
            "referrers" :  "test.com",
            "maxAge" :  100,
            "origins" : [
                {
                    "origin" :  "cloud.nhn.com",
                    "port" :  80
                }
            ],
            "callback": {
                "httpMethod": "GET",
                "url": "http://test.callback.com/cdn?=appKey={appKey}&status={status}&domain={domain}"
            }
        }
    ]
}
```


[Field]

| Field                                  | Type    | Description                                                  |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | Header area                                                  |
| header.isSuccessful                    | Boolean | Successful or not                                            |
| header.resultCode                      | Integer | Result code                                                  |
| header.resultMessage                   | String  | Result message                                               |
| distributions                          | List    | List of created CDN objects                                  |
| distributions[0].domain                | String  | Created domain (service) name                                |
| distributions[0].domainAlias           | String  | List of domain alias (personal or company-owned domains)               |
| distributions[0].region                | String  | Service region ("GLOBAL": Global service)            |
| distributions[0].description           | String  | Description                                                  |
| distributions[0].status                | String  | CDN status code (see CDN status codes in [Table])            |
| distributions[0].createTime            | String  | Date and time of creation                                    |
| distributions[0].useOrigin             | String  | Whether to set origin server <br />("Y": Origin server setting, "N": User-configured) |
| distributions[0].maxAge                | String  | Cache expiration time (second)                               |
| distributions[0].referrerType          | String  | Referrer access control ("BLACKLIST": Blacklist, "WHITELIST": Whitelist) |
| distributions[0].referrers             | String  | List of referrers                                            |
| distributions[0].origins               | List    | List of origin server objects                                |
| distributions[0].origins[0].origin     | String  | Origin server (domain or IP)                                 |
| distributions[0].origins[0].originPath | String  | Lower paths of origin server                                 |
| distributions[0].origins[0].port       | Integer | Origin server port                                           |
| distributions[0].callback              | Object  | Callback to receive service processing results               |
| distributions[0].callback.httpMethod   | String  | HTTP method of callback                                      |
| distributions[0].callback.url          | String  | Callback URL                                                 |


### Get

#### Request


[URI]

| Method | URI                                  |
| ------ | ------------------------------------ |
| GET    | /v1.5/appKeys/{appKey}/distributions |


[Parameter]

| Name | Type | Required | Valid Range | Description  |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| domain | String | Optional | Up to 255 characters | Domain (service name) to get |
| status | String | Optional | CDN status codes | CDN status code (see CDN status codes in [Table]) |

[Example]
```
curl -X GET "https://kr1-cdn.api.nhncloudservice.com/v1.5/appKeys/{appKey}/distributions?domain={domain}" \
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
    "domainAlias" :  "test.domain.com",
    "region" :  "GLOBAL",
    "description" :  "api test pad",
    "status" :  "OPENING",
    "useOrigin" :  "N",
    "maxAge" :  "100",
    "referrerType" :  "BLACKLIST",
    "referrers" :  "test.com",
    "origins" : [
        {
            "origin" :  "static.resource.com",
            "port" :  80
        }
    ],
    "callback": {
        "httpMethod": "GET",
        "url": "http://test.callback.com/cdn?=appKey={appKey}&status={status}&domain={domain}"
    }
}
```


[Field]

| Field                                  | Type    | Description                                                  |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | Header area                                                  |
| header.isSuccessful                    | Boolean | Successful or not                                            |
| header.resultCode                      | Integer | Result code                                                  |
| header.resultMessage                   | String  | Result message                                               |
| distributions                          | List    | List of created CDN objects                                  |
| distributions[0].domain                | String  | Domain (service) name                                        |
| distributions[0].domainAlias           | String  | List of domain alias (personal or company-owned domains)                   |
| distributions[0].region                | String  | Service region ("GLOBAL": Global service)                                    |
| distributions[0].description           | String  | Description                                                  |
| distributions[0].status                | String  | CDN status code (see CDN status codes in [Table])            |
| distributions[0].createTime            | String  | Date and time of creation                                    |
| distributions[0].useOrigin             | String  | Whether to use origin server setting <br />("Y": Origin server setting, "N": User-configured) |
| distributions[0].maxAge                | String  | Cache expiration time (second)                               |
| distributions[0].referrerType          | String  | Referrer access management ("BLACKLIST": Blacklist, "WHITELIST": Whitelist) |
| distributions[0].referrers             | String  | List of referrers                                            |
| distributions[0].origins               | List    | List of origin server objects                                |
| distributions[0].origins[0].origin     | String  | Origin server (domain or IP)                                 |
| distributions[0].origins[0].originPath | String  | Lower paths of origin server                                 |
| distributions[0].origins[0].port       | Integer | Origin server port                                           |
| distributions[0].callback              | Object  | Callback to receive service deployment results               |
| distributions[0].callback.httpMethod   | String  | HTTP method of callback                                      |
| distributions[0].callback.url          | String  | Callback URL                                                 |


### Modify

#### Request


[URI]

| Method | URI                                  |
| ------ | ------------------------------------ |
| PUT    | /v1.5/appKeys/{appKey}/distributions |


[Request Body]

```json
{
        "domain" : "sample.toastcdn.net",
        "useOriginHttpProtocolDowngrade": false,
        "forwardHostHeader": "ORIGIN_HOSTNAME",
        "useOrigin" : "N",
        "maxAge": 86400,
        "referrerType" : "BLACKLIST",
        "referrers" : "test.com",
        "origins" : [
            {
                "origin" : "static.resource.com",
                "port" : 80,
                "originPath" : "/latest/resources"
            }
        ],
        "callback": {
            "httpMethod": "GET",
            "url": "http://test.callback.com/cdn?=appKey={appKey}&status={status}&domain={domain}"
        },
        "description" : "change contents"
}
```

[Field]

| Name                  | Type    | Required | Default | Valid Range                                                  | Description                                                  |
| --------------------- | ------- | -------- | ------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| domain                | String  | Required |         | Up to 255 characters                                         | Domain (service name) to modify                              |
| useOriginHttpProtocolDowngrade | Boolean  | Required     |        | true/false         |  Whether to enable settings to downgrade a request from HTTPS to HTTP when the request is made to origin server from CDN server, if the origin server can respond only via HTTP |
| forwardHostHeader     | String  | Required      |        | ORIGIN_HOSTNAME/REQUEST_HOST_HEADER   | Setting host header to be delivered when CDN server requests content to origin server  ("ORIGIN_HOSTNAME": Set as host name for origin server, "REQUEST_HOST_HEADER": Set as host header for client requests) |
| useOrigin             | String  | Required |         | Y/N                                                          | Cache expiration setting (Y: Original setting, "N": User-configured) |
| referrerType          | String  | Required |         | BLACKLIST/WHITELIST                                          | Referrer access management ("BLACKLIST": Blacklist, "WHITELIST": Whitelist) |
| description           | String  | Optional |         | Up to 255 characters                                         | Description                                                  |
| domainAlias           | String  | Optional |         | Up to 255 characters                                         | Domain alias (Personal or company-owned domains; delimit by \n tokens.) |
| maxAge                | Integer | Optional | 0       | 0 ~ 2,147,483,647                                            | Cache expiration time (second); default 0 refers to 604,800 seconds. |
| referrers             | String  | Optional |         | Up to 1024 characters, including '\n\' tokens                | Referrers (delimit by \n tokens. )                           |
| isAllowWhenEmptyReferrer | Boolean | Optional      | true      | true/false             | True/False for Content Access if Referer Header is Unavailable             |
| origins               | List    | Required |         |                                                              | Origin server                                                |
| origins[0].origin     | String  | Required |         | Up to 255 characters                                         | Origin server (domain or IP)                                 |
| origins[0].port       | Integer  | Optional      |        |See [Console User Guide] > [[Table 2] Port number of available origin server of [Origin Server](./console-guide/#_2)] | HTTP Protocol Port for Origin Server <br>(Do not enter origins[0].httpPort and origins[0].httpsPort when origins[0].port is set.)|
| origins[0].httpPort   | Integer  | Optional      |        |See [Console User Guide] > [[Table 2] Port number of available origin server of [Origin Server](./console-guide/#_2)]| HTTP Protocol Port for Origin Server <br>(Must enter either origins[0].httpPort or origins[0].httpsPort when origins[0].port is not set.)  |
| origins[0].httpsPort  | Integer  | Optional      |        |See [Console User Guide] > [[Table 2] Port number of available origin server of [Origin Server](./console-guide/#_2)]| HTTP Protocol Port for Origin Server <br>(Must enter either origins[0].httpPort or origins[0].httpsPort when origins[0].port is not set. |
| origins[0].originPath | String  | Optional |         | Up to 8192 characters                                        | Lower paths of origin server                                 |
| callback              | Object  | Optional |         | Callback URL to receive CDN service deployment results (callback setting is optional.) |                                    |
| callback.httpMethod   | String  | Required |         | GET/POST/PUT                                                 | HTTP method of callback                                      |
| callback.url          | String  | Required |         | Up to 1024 characters                                        | Callback URL                                                 |

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

| Field                | Type    | Description       |
| -------------------- | ------- | ----------------- |
| header               | Object  | Header area       |
| header.isSuccessful  | Boolean | Successful or not |
| header.resultCode    | Integer | Result code       |
| header.resultMessage | String  | Result message    |


### Patch

Apply partial modification API to change a part of the service setting.

#### Request


[URI]

| Method | URI                                  |
| ------ | ------------------------------------ |
| PATCH  | /v1.5/appKeys/{appKey}/distributions |


[Request Body]

```json
{
        "domain" : "sample.toastcdn.net",
        "useOrigin" : "N",
        "maxAge": 86400,
        "referrerType" : "BLACKLIST",
        "referrers" : "test.com",
        "origins" : [
            {
                "origin" : "static.resource.com",
                "port" : 80,
                "originPath" : "/latest/resources"
            }
        ],
        "callback": {
            "httpMethod": "GET",
            "url": "http://test.callback.com/cdn?=appKey={appKey}&status={status}&domain={domain}"
        },
        "description" : "change contents"
}
```


[Field]

| Name                  | Type    | Required | Default | Valid Range                                                  | Description                                                  |
| --------------------- | ------- | -------- | ------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| domain                | String  | Required |         | Up to 255 characters                                         | Domain (service name) to modify                              |
| useOriginHttpProtocolDowngrade | Boolean  | Optional     |        | true/false         | Whether to enable settings to downgrade a request from HTTPS to HTTP when the request is made to origin server from CDN server, if the origin server can respond only via HTTP |
| forwardHostHeader     | String  | Optional      |        | ORIGIN_HOSTNAME/REQUEST_HOST_HEADER   |Setting host header to be delivered when CDN server requests content to origin server  ("ORIGIN_HOSTNAME": Set as host name for origin server, "REQUEST_HOST_HEADER": Set as host header for client requests) |
| useOrigin             | String  | Optional |         | Y/N                                                          | Cache expiration setting (Y: Original setting, N: User-configured) |
| referrerType          | String  | Optional |         | BLACKLIST / WHITELIST                                        | Referrer access management ("BLACKLIST": Blacklist, "WHITELIST": Whitelist) |
| description           | String  | Optional |         | Up to 255 characters                                         | Description                                                  |
| domainAlias           | String  | Optional |         | Up to 255 characters                                         | Domain alias (personal or company-owned domains; delimit by \n tokens.) |
| maxAge                | Integer | Optional | 0       | 0~2,147,483,647                                              | Cache expiration time (second); default 0 refers to 604,800 seconds. |
| referrers             | String  | Optional |         | Up to 1024 characters, including '\n' tokens                 | Referrers (delimit by \n tokens. )                           |
| isAllowWhenEmptyReferrer | Boolean | Optional      | true      | true/false             | True/False for Content Access if Referer Header is Unavailable             |
| origins               | List    | Optional |         |                                                              | Origin server                                                |
| origins[0].origin     | String  | Optional |         | Up to 255 characters                                         | Origin server (domain or IP)                                 |
| origins[0].port       | Integer | Optional      |        |See [Console User Guide] > [[Table 2] Port number of available origin server of [Origin Server](./console-guide/#_2)] | HTTP Protocol Port for Origin Server <br>(Do not enter origins[0].httpPort and origins[0].httpsPort when origins[0].port is set.)|
| origins[0].httpPort   | Integer  | Optional      |        |See [Console User Guide] > [[Table 2] Port number of available origin server of [Origin Server](./console-guide/#_2)]| HTTP Protocol Port for Origin Server <br>(Must enter either origins[0].httpPort or origins[0].httpsPort when origins[0].port is not set.)   |
| origins[0].httpsPort  | Integer  | Optional      |        |See [Console User Guide] > [[Table 2] Port number of available origin server of [Origin Server](./console-guide/#_2)]| HTTP Protocol Port for Origin Server <br>(Must enter either origins[0].httpPort or origins[0].httpsPort when origins[0].port is not set.)  |
| origins[0].originPath | String  | Optional |         | Up to 8192 characters                                        | Lower paths of origin server                                 |
| callback              | Object  | Optional |         | Callback URL to receive CDN service deployment results (callback setting is optional.) |                                    |
| callback.httpMethod   | String  | Optional |         | GET/POST/PUT                                                 | HTTP method of callback                                      |
| callback.url          | String  | Optional |         | Up to 1024 characters                                        | Callback URL                                                 |

- To set the origins field, origin and originPath fields are required, and either port, httpPort, or httpsPort field must be included.
- To set the callback field, httpMethod and url fields are required.

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

| Field                | Type    | Description       |
| -------------------- | ------- | ----------------- |
| header               | Object  | Header area       |
| header.isSuccessful  | Boolean | Successful or not |
| header.resultCode    | Integer | Result code       |
| header.resultMessage | String  | Result message    |


### Delete

#### Request


[URI]

| Method | URI                                  |
| ------ | ------------------------------------ |
| DELETE | /v1.5/appKeys/{appKey}/distributions |


[Request Body]

```json
{
    "domains" : [
        "lhcsxuo0.toastcdn.net"
    ]
}
```


[Field]

| Name    | Type   | Required | Default | Valid Range | Description                                 |
| ------- | ------ | -------- | ------- | ----------- | ------------------------------------------- |
| domains | String | Y        |         |             | Domains to delete; multiple domains allowed |

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

| Field                | Type    | Description       |
| -------------------- | ------- | ----------------- |
| header               | Object  | Header area       |
| header.isSuccessful  | Boolean | Successful or not |
| header.resultCode    | Integer | Result code       |
| header.resultMessage | String  | Result message    |

## Cache Purge API

### Purge

#### Request

[URI]

| Method | URI                           |
| ------ | ----------------------------- |
| POST   | /v1.5/appKeys/{appKey}/purges |


[Request Body]

```json
{
	"domain": "sample.toastcdn.net",
	"purgeType": "ITEM",
	"purgeList":"/img_01.png"
}
```


[Field]

| Name      | Type   | Required | Default | Valid Range           | Description                                                  |
| --------- | ------ | -------- | ------- | --------------------- | ------------------------------------------------------------ |
| domain    | String | Required |         | Up to 255 characters  | Domain (service) name to purge                               |
| purgeType | List   | Required |         | ITEM / ALL | Purge type ("ITEM", or "ALL")                    |
| purgeList | String | Optional |         |                       | List of items to purge (delimit by \n tokens; not required, if the purge type is ALL.) |

#### Response

[Response Body]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    },
    "purgeSeq" : 1
}
```


[Field]

| Field                | Type    | Description             |
| -------------------- | ------- | ----------------------- |
| header               | Object  | Header area             |
| header.isSuccessful  | Boolean | Successful or not       |
| header.resultCode    | Integer | Result code             |
| header.resultMessage | String  | Result message          |
| purgeSeq             | Integer | Purge requesting number |

- Cache redeployment may fail within about an hour after CDN service is newly created. If it still fails afterwards, contact Customer Center.
- Purge API has usage restriction policy. For more details, go to [Console User Guide > CDN Cache Redeployment](./console-guide/#purging-cdn-cache) and check 'Cache Redeployment Usage Restriction'.
- ITEM type is restricted in the number of purge paths per request. When it is requested in excess of the number, purge is divided and requested as much as the number of purge paths per request. In such case, only the redeployment request number of the initial purge request is delivered as response.

### Get Cache Purges

#### Request


[URI]

| Method | URI                           |
| ------ | ----------------------------- |
| GET    | /v1.5/appKeys/{appKey}/purges |


[Parameter]

| Name         | Type    | Required | Default | Valid Range                                                  | Description                         |
| ------------ | ------- | -------- | ------- | ------------------------------------------------------------ | ----------------------------------- |
| domain       | String  | Required |         | Up to 255 characters                                         | Domain (service) name               |
| page         | Integer | Optional |         |                                                              | Page number                         |
| itemsPerPage | Integer | Optional |         |                                                              | Number of purged items on each page |
| startTime    | String  | Optional |         | "yyyy-MM-ddTHH:mm:ss.SSSZ" e.g.)"2018-02-22T09:00:00.000Z"(UTC) | Search of period                    |
| endTime      | String  | Optional |         | "yyyy-MM-ddTHH:mm:ss.SSSZ" e.g.)"2018-02-22T09:00:00.000Z"(UTC) | Search of period                    |


```
curl -X GET "https://kr1-cdn.api.nhncloudservice.com/v1.5/appKeys/{appKey}/purges?domain={domain}" \
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
    "totalItems" :  2,
    "purges" : [
        {
            "seq" :  593,
            "distributionSeq" :  8445,
            "purgeId" :  552256938,
            "progress" :  0,
            "purgeTime" :  1496979544701,
            "lastCheckTime" :  1496979559013,
            "type" :  "ITEM",
            "path" :  "/prod_apigateway/img_plugin_cors_1.png"
        },
        {
            "seq" :  589,
            "distributionSeq" :  8445,
            "purgeId" :  498528821,
            "progress" :  100,
            "purgeTime" :  1482385936413,
            "lastCheckTime" :  1482385997077,
            "type" :  "ALL",
            "path" :  ""
        }
    ]
}
```


[Field]

| Field                 | Type  | Description                     |
| ----------------------- | ------- | --------------------------------- |
| header                  | Object  | Header area                  |
| header.isSuccessful     | Boolean | Successful or not        |
| header.resultCode       | Integer | Result code                  |
| header.resultMessage    | String  | Result message             |
| totalItems              | Integer | Total number of purges  |
| purges                  | List    | List of purged items   |
| purges[0].seq           | Integer | Purge requesting number |
| purges[0].progress      | Integer | Purge progress rate |
| purges[0].purgeTime     | Long    | Purge requesting time |
| purges[0].lastCheckTime | Long    | Last confirmed purge time |
| purges[0].type          | String  | Purge type ("ITEM" or "ALL") |
| purges[0].path          | String  | Requested purge items |

## Callback Response
With callback enabled for CDN service, when tasks are completed, such as Create/Modify/Suspend/Resume/Delete, response values are delivered to callback URL as below.

[Response Body]
``` json
{
  "header" : {
    "resultCode" :  0,
    "resultMessage" :  "SUCCESS",
    "isSuccessful" :  true
  },
  "distribution":{
      "appKey": "String",
      "domain" : "lhcsxuo0.toastcdn.net",
      "domainAlias" : "test.domain.com",
      "region" : "GLOBAL",
      "description" : "api test pad",
      "status" : "OPENING",
      "createTime" : 1498613094692,
      "useOrigin" : "N",
      "maxAge" : "100",
      "referrerType" : "BLACKLIST",
      "referrers" : "test.com",
      "deleteTime": "DateTime",
      "origins":[
         {
            "origin": "static.resource.com",
            "originPath": "/path",
            "port": "80"
         }
      ],
      "callback":{
         "httpMethod": "GET",
         "url": "http://test.callback.com/cdn?=appKey={appKey}&status={status}&domain={domain}"
      }
  }
}
```

[Field]

| Field                                   | Type    | Description                                                         |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | Header area                                                    |
| header.isSuccessful                    | Boolean | Successful or not                                                 |
| header.resultCode                      | Integer | Result code                                                    |
| header.resultMessage                   | String  | Result message                                                  |
| distribution                          | Object    | CDN object completed with changes                                      |
| distribution.appKey                   | String    | Appkey                                  |
| distribution.domain                | String  | Domain name (service name)                                     |
| distribution.domainAlias           | String  | Owned domain                                                  |
| distribution.region                | String  | Service region ("GLOBAL": Global service)             |
| distribution.description           | String  | Description                                                         |
| distribution.status                | String  | CDN status code ([Table] See CDN Status Code)                                 |
| distribution.createTime            | String  | Date and time of creation                                                    |
| distribution.deleteTime            | String  | Date and time of deletion                                                    |
| distribution.useOrigin             | String  | Whether to use origin server setting ("Y": Enable origin server setting, "N": User-configured) |
| distribution.maxAge                | String  | Cache expiration time (seconds)                                           |
| distribution.referrerType          | String  | Referrer access control ("BLACKLIST": Blacklist, "WHITELIST": Whitelist) |
| distribution.referrers             | String  | List of referrers                                                  |
| distribution.origins               | List    | List of origin server objects                                      |
| distribution.origins[0].origin     | String  | Origin server (domain or IP)                                      |
| distribution.origins[0].originPath | String  | Lower path of origin server                                          |
| distribution.origins[0].port       | Integer | Origin server port                                               |
| distribution.callback              | Object  | Callback to be notified on processing results of service deployment               |
| distribution.callback.httpMethod   | String  | Callback HTTP Method                                           |
| distribution.callback.url          | String  | Callback URL                                                     |
