## Content Delivery > CDN > API v2.0 가이드

NHN Cloud CDN에서 제공하는 Public API v2.0을 설명합니다.

## API 공통 정보

### 도메인

| 이름              | 도메인                                   |
| --------------- | ------------------------------------- |
| CDN Public API 도메인 | https://kr1-cdn.api.nhncloudservice.com |

### 사전 준비

API를 사용하려면 앱 키(Appkey)와 보안 키(SecretKey)가 필요합니다.
앱 키와 보안 키는 콘솔 오른쪽 위의 **URL & Appkey** 메뉴에서 확인할 수 있습니다.

### 요청 공통 정보

#### 요청 헤더

| 이름            | 설명                        |
| ------------- | ------------------------- |
| Authorization | 콘솔에서 발급받은 보안 키(SecretKey) |

#### Path 파라미터

모든 API는 앱 키를 path 파라미터로 지정해야 합니다.
* 예) /v2.0/appKeys/**{appKey}**/distributions

| 이름     | 설명                    |
| ------ | --------------------- |
| appKey | 콘솔에서 발급받은 앱 키(Appkey) |

### 응답 공통 정보

#### 헤더

모든 API 요청에 대해서 **200 OK**로 응답합니다. 자세한 응답 결과는 다음의 예와 같이 응답 본문의 헤더를 참고합니다.

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Success"
    }
}
```


[필드]

| 필드                   | 타입      | 설명     |
| -------------------- | ------- | ------ |
| header               | Object  | 헤더 영역  |
| header.isSuccessful  | Boolean | 성공 여부  |
| header.resultCode    | Integer | 결과 코드  |
| header.resultMessage | String  | 결과 메시지 |

#### CDN 상태 코드

다음은 CDN 서비스 상태를 나타내는 상태 코드로, 서비스 조회 시 서비스 상태를 확인할 수 있습니다.

| 값         | 설명                     |
| ---------- | ------------------------ |
| OPENING    | 서비스 시작 중           |
| OPEN       | 서비스 중                |
| MODIFYING  | 수정 중                  |
| RESUME     | 시작                     |
| SUSPENDING | 정지 진행 중             |
| SUSPEND    | 정지                     |
| CLOSING    | 사용 종료 중             |
| CLOSE      | 사용 종료                |
| ERROR      | 서비스 생성 중 오류 발생 |

#### 인증서 발급 상태 코드

다음은 도메인의 인증서 발급 상태를 나타내는 상태 코드로, 인증서 조회 시 발급 상태를 확인할 수 있습니다.

| 값         | 설명                     |
| ---------- | ------------------------ |
| PENDING_NEW        | 인증서 신규 발급이 요청되어 처리 대기 중   |
| PENDING_CANCEL     | 인증서 발급이 취소 요청되어 도메인 검증 취소 처리 대기 중   |
| PENDING_DELETE     | 발급된 인증서가 삭제 요청되어 처리 대기 중  |
| PENDING_EXPIRE     | 발급된 인증서가 만료되어 만료 처리 대기 중  |
| VALIDATED          | 도메인 검증 완료                     |
| DEPLOYED           | 인증서 배포 완료                     |
| WAITING_VALIDATION | 도메인 검증 대기 중                  |
| CANCELED           | 도메인 검증 취소 완료                 |
| DELETED            | 도메인 인증서 삭제 완료               |
| EXPIRED            | 도메인 인증서 만료                   |


## 서비스 API

### 서비스 생성

#### 요청


[URI]

| 메서드  | URI                                  |
| ---- | ------------------------------------ |
| POST | /v2.0/appKeys/{appKey}/distributions |


[요청 본문]

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

[필드]

| 이름                                                                                    | 타입      | 필수 여부 | 기본값         | 유효 범위                                                                 | 설명                                                                                                                        |
|---------------------------------------------------------------------------------------|---------|-------|-------------|-----------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| distributions                                                                         | List    | 필수    |             |                                                                       | 생성할 CDN의 오브젝트 목록                                                                                                          |
| distributions[0].useOriginHttpProtocolDowngrade                                       | Boolean | 필수    | false       | true/false                                                            | 원본 서버가 HTTP 응답만 가능한 경우, CDN 서버에서 원본 서버로 요청 시 HTTPS 요청을 HTTP 요청으로 다운그레이드 하기 위한 설정 사용 여부                                     |
| distributions[0].forwardHostHeader                                                    | String  | 필수    |             | ORIGIN_HOSTNAME<br/>REQUEST_HOST_HEADER                               | CDN 서버가 원본 서버로 콘텐츠 요청 시 전달할 호스트 헤더 설정("ORIGIN_HOSTNAME": 원본 서버의 호스트 이름으로 설정, "REQUEST_HOST_HEADER": 클라이언트 요청의 호스트 헤더로 설정) |
| distributions[0].useOriginCacheControl                                                | Boolean | 선택    |             | true/false                                                            | 캐시 만료 설정(true: 원본 서버 설정 사용, false: 사용자 설정 사용). useOriginCacheControl이나 cacheType 중 하나는 반드시 입력해야 합니다.                      |
| distributions[0].cacheType                                                            | String  | 선택    |             | BYPASS, NO_STORE                                                      | 캐시 타입 설정. useOriginCacheControl이나 cacheType 중 하나는 반드시 입력해야 합니다.                                                           |
| distributions[0].referrerType                                                         | String  | 필수    |             | BLACKLIST/WHITELIST                                                   | 리퍼러 접근 관리("BLACKLIST": 블랙리스트, "WHITELIST": 화이트리스트)                                                                        |
| distributions[0].referrers                                                            | List    | 선택    |             |                                                                       | 정규 표현식 형태의 리퍼러 헤더 목록                                                                                                      |
| distributions[0].isAllowWhenEmptyReferrer                                             | Boolean | 선택    | true        | true/false                                                            | 리퍼러 헤더가 없는 경우 콘텐츠 접근 허용(true)/거부(false) 여부                                                                                |
| distributions[0].isAllowPost                                                          | Boolean | 선택    | false       | true/false                                                            | POST 메서드 허용(true)/거부(false) 여부                                                                                            |
| distributions[0].isAllowPut                                                           | Boolean | 선택    | false       | true/false                                                            | PUT 메서드 허용(true)/거부(false) 여부                                                                                             |
| distributions[0].isAllowPatch                                                         | Boolean | 선택    | false       | true/false                                                            | PATCH 메서드 허용(true)/거부(false) 여부                                                                                           |
| distributions[0].isAllowDelete                                                        | Boolean | 선택    | false       | true/false                                                            | DELETE 메서드 허용(true)/거부(false) 여부                                                                                          |
| distributions[0].useLargeFileOptimization                                             | Boolean | 선택    | false       | true/false                                                            | 대용량 파일 최적화 설정 사용 여부                                                                                                       |
| distributions[0].description                                                          | String  | 선택    |             | 최대 255자                                                               | 설명                                                                                                                        |
| distributions[0].domainAlias                                                          | List    | 선택    |             |                                                                       | 도메인 별칭 목록(개인 혹은 회사가 소유한 도메인 사용)                                                                                           |
| distributions[0].defaultMaxAge                                                        | Integer | 선택    | 0           | 0~2,147,483,647                                                       | 캐시 만료 시간(초), 기본값 0은 604,800초입니다.                                                                                          |
| distributions[0].cacheKeyQueryParam                                                   | String  | 선택    | INCLUDE_ALL | INCLUDE_ALL/EXCLUDE_ALL                                               | 캐시 키에 요청 쿼리 문자열 포함 여부 설정("INCLUDE_ALL": 전체 포함, "EXCLUDE_ALL": 전체 미포함)                                                     |
| distributions[0].origins                                                              | List    | 필수    |             |                                                                       | 원본 서버 오브젝트 목록                                                                                                             |
| distributions[0].origins[0].origin                                                    | String  | 필수    |             | 최대 255자                                                               | 원본 서버(도메인 또는 IP)                                                                                                          |
| distributions[0].origins[0].originPath                                                | String  | 선택    |             | 최대 8192자                                                              | 원본 서버 하위 경로(/를 포함한 경로로 입력해 주세요.)                                                                                          |
| distributions[0].origins[0].httpPort                                                  | Integer | 선택    |             | [콘솔 사용 가이드 > 원본 서버](./console-guide/#_2)의 '[표 2] 사용 가능한 원본 서버 포트 번호' 참고 | 원본 서버 HTTP 프로토콜 포트(origins[0].httpPort와 origins[0].httpsPort 중 하나는 반드시 입력해야 합니다.)                                         |
| distributions[0].origins[0].httpsPort                                                 | Integer | 선택    |             | [콘솔 사용 가이드 > 원본 서버](./console-guide/#_2)의 '[표 2] 사용 가능한 원본 서버 포트 번호' 참고 | 원본 서버 HTTPS 프로토콜 포트(origins[0].httpPort와 origins[0].httpsPort 중 하나는 반드시 입력해야 합니다.)                                        |
| distributions[0].rootPathAccessControl                                                | Object  | 선택    |             |                                                                       | CDN 서비스의 루트 경로에 대한 접근 제어 설정                                                                                               | 
| distributions[0].rootPathAccessControl.enable                                         | Boolean | 필수    | true        | true/false                                                            | 루트 경로에 대한 접근 제어 사용(true)/미사용(false) 여부                                                                                    |
| distributions[0].rootPathAccessControl.controlType                                    | String  | 선택    |             | DENY, REDIRECT                                                        | enable이 true일 경우 필수 입력. 루트 경로에 대한 접근 제어 방식("DENY": 접근 거부, "REDIRECT": 지정한 경로로 리다이렉트)                                      | 
| distributions[0].rootPathAccessControl.redirectPath                                   | String  | 선택    |             |                                                                       | controlType이 "REDIRECT"일 경우 필수 입력. 루트 경로에 대한 요청을 리다이렉트할 경로(/를 포함한 경로로 입력해 주세요.)                                           |
| distributions[0].rootPathAccessControl.redirectStatusCode                             | Integer | 선택    |             | 301, 302, 303, 307                                                    | controlType이 "REDIRECT"일 경우 필수 입력. 리다이렉트시 전달되는 HTTP 응답 코드                                                                 |
| distributions[0].modifyOutgoingResponseHeaderControl                                  | Object  | 선택    |             |                                                                       | CDN에서 응답하는 HTTP 헤더를 추가/변경/삭제하는 설정                                                                                         |
| distributions[0].modifyOutgoingResponseHeaderControl.enable                           | Boolean | 필수    | true        | true/false                                                            | HTTP 응답 헤더를 추가/변경/삭제하는 설정 사용(true)/미사용(false) 여부                                                                          |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList                       | List    | 선택    |         |                                                                       | HTTP 응답 헤더 목록                                                                                                             |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].action             | String  | 선택    |         | ADD, MODIFY, DELETE                                                   | HTTP 응답 헤더 변경 방식                                                                                                          |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].standardHeaderName | String  | 선택    |         | ACCESS_CONTROL_ALLOW_CREDENTIALS<br/>ACCESS_CONTROL_ALLOW_HEADERS<br/>ACCESS_CONTROL_ALLOW_METHODS<br/>ACCESS_CONTROL_ALLOW_ORIGIN<br/>ACCESS_CONTROL_EXPOSE_HEADERS<br/>ACCESS_CONTROL_MAX_AGE<br/>CACHE_CONTROL<br/>CONTENT_DISPOSITION<br/>CONTENT_TYPE<br/>P3P<br/>PRAGMA<br/>OTHER | 일반 HTTP 응답 헤더 이름                                                                                                          |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].customHeaderName   | String  | 선택    |         |                                                      | standardHeaderName이 "OTHER"일 경우 필수 입력. 사용자 정의 HTTP 응답 헤더 이름                                                               |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].headerValue        | String  | 필수    |         |                                                      | HTTP 응답 헤더 값                                                                                                              |
| distributions[0].callback                                                             | Object  | 선택    |             |                                                                       | CDN 생성 처리 결과를 통보받을 콜백 URL(콜백 설정은 선택 입력입니다.)                                                                               |
| distributions[0].callback.httpMethod                                                  | String  | 필수    |             | GET/POST/PUT                                                          | 콜백의 HTTP 메서드                                                                                                              |
| distributions[0].callback.url                                                         | String  | 필수    |             | 최대 1024자                                                              | 콜백 URL                                                                                                                    |

- forwardHostHeader의 기본값은 domainAlias를 설정한 경우 REQUEST_HOST_HEADER이고, 미설정하면 ORIGIN_HOSTNAME입니다. 



#### 응답


[응답 본문]

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


[필드]

| 필드                                   | 타입    | 설명                                                       |
| -------------------------------------- | ------- | ---------------------------------------------------------- |
| header                                 | Object  | 헤더 영역                                                  |
| header.isSuccessful                    | Boolean | 성공 여부                                                  |
| header.resultCode                      | Integer | 결과 코드                                                  |
| header.resultMessage                   | String  | 결과 메시지                                                |
| distributions                          | List    | 생성된 CDN 오브젝트 목록                                 |
| distributions[0].domain                | String  | 생성된 도메인(서비스) 이름                                 |
| distributions[0].domainAlias           | List    | 도메인 별칭 목록(개인 혹은 회사가 소유한 도메인 사용)            |
| distributions[0].region                | String  | 서비스 지역("GLOBAL": 글로벌)          |
| distributions[0].description           | String  | 설명                                                       |
| distributions[0].status                | String  | CDN 상태 코드([표] CDN 상태 코드 참고)                               |
| distributions[0].defaultMaxAge         | Integer | 캐시 만료 시간(초)                                         |
| distributions[0].cacheKeyQueryParam    | String  | 캐시 키에 요청 쿼리 문자열 포함 여부 설정("INCLUDE_ALL": 전체 포함, "EXCLUDE_ALL": 전체 미포함) |
| distributions[0].referrerType          | String  | 리퍼러 접근 관리("BLACKLIST": 블랙리스트, "WHITELIST": 화이트리스트) |
| distributions[0].referrers             | List    | 정규 표현식 형태의 리퍼러 헤더 목록                                |
| distributions[0].isAllowWhenEmptyReferrer | Boolean | 리퍼러 헤더가 없는 경우 콘텐츠 접근 허용(true)/거부(false) 여부 |
| distributions[0].isAllowPost | Boolean | POST 메서드 허용(true)/거부(false) 여부           |
| distributions[0].isAllowPut | Boolean | PUT 메서드 허용(true)/거부(false) 여부           |
| distributions[0].isAllowPatch | Boolean | PATCH 메서드 허용(true)/거부(false) 여부           |
| distributions[0].isAllowDelete | Boolean | DELETE 메서드 허용(true)/거부(false) 여부           |
| distributions[0].useLargeFileOptimization | Boolean | 대용량 파일 최적화 설정 사용 여부   |
| distributions[0].useOriginCacheControl | Boolean | 원본 서버 설정 사용 여부(true: 원본 서버 설정 사용, false: 사용자 설정 사용) |
| distributions[0].cacheType             | String  | 캐시 타입 설정                                        |
| distributions[0].origins               | List    | 원본 서버 오브젝트 목록                                    |
| distributions[0].origins[0].origin     | String  | 원본 서버(도메인 또는 IP)                                    |
| distributions[0].origins[0].originPath | String  | 원본 서버 하위 경로                                        |
| distributions[0].origins[0].httpPort   | Integer | 원본 서버 HTTP 프로토콜 포트                                             |
| distributions[0].origins[0].httpsPort  | Integer | 원본 서버 HTTPS 프로토콜 포트                                             |
| distributions[0].useOriginHttpProtocolDowngrade | Boolean | 원본 서버가 HTTP 응답만 가능한 경우, CDN 서버에서 원본 서버로 요청 시 HTTPS 요청을 HTTP 요청으로 다운그레이드 하기 위한 설정 사용 여부 |
| distributions[0].forwardHostHeader     | String  | CDN 서버가 원본 서버로 콘텐츠 요청 시 전달 할 호스트 헤더 설정("ORIGIN_HOSTNAME": 원본 서버의 호스트 이름으로 설정, "REQUEST_HOST_HEADER": 클라이언트 요청의 호스트헤더로 설정 |
| distributions[0].rootPathAccessControl  | Object  | CDN 서비스의 루트 경로에 대한 접근 제어 설정 | 
| distributions[0].rootPathAccessControl.enable | Boolean | 루트 경로에 대한 접근 제어 사용(true)/미사용(false) 여부        |
| distributions[0].rootPathAccessControl.controlType  | String  | enable이 true일 경우 필수 입력. 루트 경로에 대한 접근 제어 방식("DENY": 접근 거부, "REDIRECT": 지정한 경로로 리다이렉트) | 
| distributions[0].rootPathAccessControl.redirectPath | String | controlType이 "REDIRECT"일 경우 필수 입력. 루트 경로에 대한 요청을 리다이렉트할 경로(/를 포함한 경로로 입력해 주세요.)      |
| distributions[0].rootPathAccessControl.redirectStatusCode | Integer | controlType이 "REDIRECT"일 경우 필수 입력. 리다이렉트시 전달되는 HTTP 응답 코드        |
| distributions[0].modifyOutgoingResponseHeaderControl                                  | Object  | CDN에서 응답하는 HTTP 헤더를 추가/변경/삭제하는 설정  |
| distributions[0].modifyOutgoingResponseHeaderControl.enable                           | Boolean | HTTP 응답 헤더를 추가/변경/삭제하는 설정 사용(true)/미사용(false) 여부  |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList                       | List    | HTTP 응답 헤더 목록 |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].action             | String  | HTTP 응답 헤더 변경 방식 |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].standardHeaderName | String  | 일반 HTTP 응답 헤더 이름 |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].customHeaderName   | String  | standardHeaderName이 "OTHER"일 경우 필수 입력. 사용자 정의 HTTP 응답 헤더 이름 |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].headerValue        | String  | HTTP 응답 헤더 값 |
| distributions[0].callback              | Object  | 서비스 생성 처리 결과를 통보받을 콜백                      |
| distributions[0].callback.httpMethod   | String  | 콜백의 HTTP 메서드                                         |
| distributions[0].callback.url          | String  | 콜백 URL                                                   |



### 서비스 조회

#### 요청


[URI]

| 메서드  | URI                                  |
| ---- | ------------------------------------ |
| GET  | /v2.0/appKeys/{appKey}/distributions |


[파라미터]

| 이름   | 타입   | 필수 여부 | 유효 범위     | 설명                         |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| domain | String | 선택      | 최대 255자    | 조회할 도메인(서비스 이름)   |
| status | String | 선택      | CDN 상태 코드 | CDN 상태 코드([표] CDN 상태 코드 참고) |

[예]
```
curl -X GET "https://kr1-cdn.api.nhncloudservice.com/v2.0/appKeys/{appKey}/distributions?domain={domain}" \
 -H "Authorization: {secretKey}" \
 -H "Content-Type: application/json"
```

#### 응답


[응답 본문]

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


[필드]

| 필드                                   | 타입    | 설명                                                         |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | 헤더 영역                                                    |
| header.isSuccessful                    | Boolean | 성공 여부                                                    |
| header.resultCode                      | Integer | 결과 코드                                                    |
| header.resultMessage                   | String  | 결과 메시지                                                  |
| distributions                          | List    | 생성된 CDN 오브젝트 목록                                     |
| distributions[0].domain                | String  | 도메인 이름(서비스 이름)                                     |
| distributions[0].domainAlias           | List  | 도메인 별칭 목록(개인 혹은 회사가 소유한 도메인 사용)                                                  |
| distributions[0].region                | String  | 서비스 지역("GLOBAL": 글로벌)             |
| distributions[0].status                | String  | CDN 상태 코드([표] CDN 상태 코드 참고)                                 |
| distributions[0].defaultMaxAge         | Integer  | 캐시 만료 시간(초)                                           |
| distributions[0].cacheKeyQueryParam    | String  | 캐시 키에 요청 쿼리 문자열 포함 여부 설정("INCLUDE_ALL": 전체 포함, "EXCLUDE_ALL": 전체 미포함) |
| distributions[0].referrerType          | String  | 리퍼러 접근 관리("BLACKLIST": 블랙리스트, "WHITELIST": 화이트리스트) |
| distributions[0].referrers             | List    | 정규 표현식 형태의 리퍼러 헤더 목록                                 |
| distributions[0].isAllowWhenEmptyReferrer | Boolean | 리퍼러 헤더가 없는 경우 콘텐츠 접근 허용(true)/거부(false) 여부 |
| distributions[0].isAllowPost          | Boolean | POST 메서드 허용(true)/거부(false) 여부             |
| distributions[0].isAllowPut           | Boolean | PUT 메서드 허용(true)/거부(false) 여부             |
| distributions[0].isAllowPatch         | Boolean | PATCH 메서드 허용(true)/거부(false) 여부             |
| distributions[0].isAllowDelete        | Boolean | DELETE 메서드 허용(true)/거부(false) 여부             |
| distributions[0].useLargeFileOptimization | Boolean | 대용량 파일 최적화 설정 사용 여부     |
| distributions[0].useOriginCacheControl | Boolean | 원본 서버 설정 사용 여부(true: 원본 서버 설정 사용, false: 사용자 설정 사용) |
| distributions[0].cacheType             | String  | 캐시 타입 설정                                          |
| distributions[0].origins               | List    | 원본 서버 오브젝트 목록                                      |
| distributions[0].origins[0].origin     | String  | 원본 서버(도메인 또는 IP)                                      |
| distributions[0].origins[0].originPath | String  | 원본 서버 하위 경로                                          |
| distributions[0].origins[0].httpPort   | Integer | 원본 서버 HTTP 프로토콜 포트                                  |
| distributions[0].origins[0].httpsPort  | Integer | 원본 서버 HTTPS 프로토콜 포트                                 |
| distributions[0].forwardHostHeader     | String  | 서비스 배포 처리 결과를 통보받을 콜백                        |
| distributions[0].useOriginHttpProtocolDowngrade | Boolean | 원본 서버가 HTTP 응답만 가능한 경우, CDN 서버에서 원본 서버로 요청 시 HTTPS 요청을 HTTP 요청으로 다운그레이드 하기 위한 설정 사용 여부 |
| distributions[0].forwardHostHeader     | String  | CDN 서버가 원본 서버로 콘텐츠 요청 시 전달 할 호스트 헤더 설정("ORIGIN_HOSTNAME": 원본 서버의 호스트 이름으로 설정, "REQUEST_HOST_HEADER": 클라이언트 요청의 호스트헤더로 설정 |
| distributions[0].rootPathAccessControl  | Object  | CDN 서비스의 루트 경로에 대한 접근 제어 설정 | 
| distributions[0].rootPathAccessControl.enable | Boolean | 루트 경로에 대한 접근 제어 사용(true)/미사용(false) 여부          |
| distributions[0].rootPathAccessControl.controlType  | String  | enable이 true일 경우 필수 입력. 루트 경로에 대한 접근 제어 방식("DENY": 접근 거부, "REDIRECT": 지정한 경로로 리다이렉트) | 
| distributions[0].rootPathAccessControl.redirectPath | String | controlType이 "REDIRECT"일 경우 필수 입력. 루트 경로에 대한 요청을 리다이렉트할 경로(/를 포함한 경로로 입력해 주세요.)        |
| distributions[0].rootPathAccessControl.redirectStatusCode | Integer | controlType이 "REDIRECT"일 경우 필수 입력. 리다이렉트시 전달되는 HTTP 응답 코드          |
| distributions[0].modifyOutgoingResponseHeaderControl                                  | Object  | CDN에서 응답하는 HTTP 헤더를 추가/변경/삭제하는 설정  |
| distributions[0].modifyOutgoingResponseHeaderControl.enable                           | Boolean | HTTP 응답 헤더를 추가/변경/삭제하는 설정 사용(true)/미사용(false) 여부  |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList                       | List    | HTTP 응답 헤더 목록 |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].action             | String  | HTTP 응답 헤더 변경 방식 |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].standardHeaderName | String  | 일반 HTTP 응답 헤더 이름 |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].customHeaderName   | String  | standardHeaderName이 "OTHER"일 경우 필수 입력. 사용자 정의 HTTP 응답 헤더 이름 |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].headerValue        | String  | HTTP 응답 헤더 값 |
| distributions[0].callback              | Object  | 서비스 배포 처리 결과를 통보받을 콜백                        |
| distributions[0].callback.httpMethod   | String  | 콜백의 HTTP 메서드                                           |
| distributions[0].callback.url          | String  | 콜백 URL                                                     |


### 서비스 수정

#### 요청


[URI]

| 메서드  | URI                                  |
| ---- | ------------------------------------ |
| PUT  | /v2.0/appKeys/{appKey}/distributions |


[요청 본문]

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


[필드]

| 이름                  | 타입    | 필수 여부 | 기본값 | 유효 범위                                                    | 설명                                                         |
| --------------------- | ------- | --------- | ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| domain                | String  | 필수      |        | 최대 255자                                                   | 수정할 도메인(서비스 이름)                                   |
| useOriginCacheControl | Boolean | 선택      |        | true/false                                                        | 캐시 만료 설정(true: 원본 서버 설정 사용, false: 사용자 설정 사용). useOriginCacheControl이나 cacheType 중 하나는 반드시 입력해야 합니다.      |
| cacheType             | String  | 선택      |        | BYPASS, NO_STORE            | 캐시 타입 설정. useOriginCacheControl이나 cacheType 중 하나는 반드시 입력해야 합니다.                                          |
| referrerType          | String  | 필수      |        | BLACKLIST/WHITELIST                                          | 리퍼러 접근 관리("BLACKLIST": 블랙리스트, "WHITELIST": 화이트리스트) |
| referrers             | List    | 선택      |        |                                                              | 정규 표현식 형태의 리퍼러 헤더 목록 |
| isAllowWhenEmptyReferrer | Boolean | 선택      | true      | true/false             | 리퍼러 헤더가 없는 경우 콘텐츠 접근 허용(true)/거부(false) 여부             |
| isAllowPost           | Boolean | 선택      | false      | true/false             | POST 메서드 허용(true)/거부(false) 여부             |
| isAllowPut            | Boolean | 선택      | false      | true/false             | PUT 메서드 허용(true)/거부(false) 여부             |
| isAllowPatch          | Boolean | 선택      | false      | true/false             | PATCH 메서드 허용(true)/거부(false) 여부             |
| isAllowDelete         | Boolean | 선택      | false      | true/false             | DELETE 메서드 허용(true)/거부(false) 여부             |
| useLargeFileOptimization | Boolean | 선택   | false      | true/false             | 대용량 파일 최적화 설정 사용 여부     |
| description           | String  | 선택      |        | 최대 255자                                                   | 설명                                                         |
| domainAlias           | List    | 선택      |        | 최대 255자                                                   | 도메인 별칭(개인 혹은 회사가 소유한 도메인 사용) |
| defaultMaxAge         | Integer | 선택      | 0      | 0~2,147,483,647                                            | 캐시 만료 시간(초), 기본값 0은 604,800초입니다.              |
| cacheKeyQueryParam    | String  | 선택      | INCLUDE_ALL | INCLUDE_ALL/EXCLUDE_ALL                               | 캐시 키에 요청 쿼리 문자열 포함 여부 설정("INCLUDE_ALL": 전체 포함, "EXCLUDE_ALL": 전체 미포함) |
| origins               | List    | 필수      |        |                                                              | 원본 서버                                                    |
| origins[0].origin     | String  | 필수      |        | 최대 255자                                                   | 원본 서버(도메인 또는 IP)                                      |
| origins[0].originPath | String  | 선택      |        | 최대 8192자                                                  | 원본 서버 하위 경로                                          |
| origins[0].httpPort   | Integer  | 선택      |        |[콘솔 사용 가이드 > 원본 서버](./console-guide/#_2)의 '[표 2] 사용 가능한 원본 서버 포트 번호' 참고| 원본 서버 HTTP 프로토콜 포트(origins[0].httpPort와 origins[0].httpsPort 중 하나는 반드시 입력해야 합니다.)  |
| origins[0].httpsPort  | Integer  | 선택      |        |[콘솔 사용 가이드 > 원본 서버](./console-guide/#_2)의 '[표 2] 사용 가능한 원본 서버 포트 번호' 참고 | 원본 서버 HTTPS 프로토콜 포트(origins[0].httpPort와 origins[0].httpsPort 중 하나는 반드시 입력해야 합니다.) |
| useOriginHttpProtocolDowngrade | Boolean  | 필수     | false       | true/false         | 원본 서버가 HTTP 응답만 가능한 경우, CDN 서버에서 원본 서버로 요청 시 HTTPS 요청을 HTTP 요청으로 다운그레이드 하기 위한 설정 사용 여부 |
| forwardHostHeader     | String  | 필수      |        | ORIGIN_HOSTNAME<br/>REQUEST_HOST_HEADER   | CDN 서버가 원본 서버로 콘텐츠 요청 시 전달할 호스트 헤더 설정("ORIGIN_HOSTNAME": 원본 서버의 호스트 이름으로 설정, "REQUEST_HOST_HEADER": 클라이언트 요청의 호스트 헤더로 설정)|
| useOrigin             | String  | 필수      |        | Y/N                                                          | 캐시 만료 설정(Y: 원본 설정 사용, "N":사용자 설정 사용)      |
| rootPathAccessControl  | Object  | 선택 |  |  | CDN 서비스의 루트 경로에 대한 접근 제어 설정 | 
| rootPathAccessControl.enable | Boolean | 필수 | false | true/false | 루트 경로에 대한 접근 제어 사용(true)/미사용(false) 여부          |
| rootPathAccessControl.controlType  | String  | 선택 |  | DENY, REDIRECT | enable이 true일 경우 필수 입력. 루트 경로에 대한 접근 제어 방식("DENY": 접근 거부, "REDIRECT": 지정한 경로로 리다이렉트) | 
| rootPathAccessControl.redirectPath | String | 선택 |  | | controlType이 "REDIRECT"일 경우 필수 입력. 루트 경로에 대한 요청을 리다이렉트할 경로(/를 포함한 경로로 입력해 주세요.)        |
| rootPathAccessControl.redirectStatusCode | Integer | 선택 | | 301, 302, 303, 307 |controlType이 "REDIRECT"일 경우 필수 입력. 리다이렉트시 전달되는 HTTP 응답 코드          |
| modifyOutgoingResponseHeaderControl                                  | Object  | 선택    |             |                                                                       | CDN에서 응답하는 HTTP 헤더를 추가/변경/삭제하는 설정                                                                                         |
| modifyOutgoingResponseHeaderControl.enable                           | Boolean | 필수    | true        | true/false                                                            | HTTP 응답 헤더를 추가/변경/삭제하는 설정 사용(true)/미사용(false) 여부                                                                          |
| modifyOutgoingResponseHeaderControl.headerList                       | List    | 선택    |         |                                                                       | HTTP 응답 헤더 목록                                                                                                             |
| modifyOutgoingResponseHeaderControl.headerList[0].action             | String  | 선택    |         | ADD, MODIFY, DELETE                                                   | HTTP 응답 헤더 변경 방식                                                                                                          |
| modifyOutgoingResponseHeaderControl.headerList[0].standardHeaderName | String  | 선택    |         | ACCESS_CONTROL_ALLOW_CREDENTIALS<br/>ACCESS_CONTROL_ALLOW_HEADERS<br/>ACCESS_CONTROL_ALLOW_METHODS<br/>ACCESS_CONTROL_ALLOW_ORIGIN<br/>ACCESS_CONTROL_EXPOSE_HEADERS<br/>ACCESS_CONTROL_MAX_AGE<br/>CACHE_CONTROL<br/>CONTENT_DISPOSITION<br/>CONTENT_TYPE<br/>P3P<br/>PRAGMA<br/>OTHER | 일반 HTTP 응답 헤더 이름                                                                                                          |
| modifyOutgoingResponseHeaderControl.headerList[0].customHeaderName   | String  | 선택    |         |                                                      | standardHeaderName이 "OTHER"일 경우 필수 입력. 사용자 정의 HTTP 응답 헤더 이름                                                               |
| modifyOutgoingResponseHeaderControl.headerList[0].headerValue        | String  | 필수    |         |                                                      | HTTP 응답 헤더 값                                                                                                              |
| callback              | Object  | 선택      |        | CDN 서비스 배포 결과를 통보받을 콜백 URL(콜백 설정은 선택 입력입니다.) |                                                              |
| callback.httpMethod   | String  | 필수      |        | GET/POST/PUT                                                 | 콜백의 HTTP 메서드                                           |
| callback.url          | String  | 필수      |        | 최대 1024자                                                  | 콜백 URL                                                     |

- forwardHostHeader의 기본값은 domainAlias를 설정한 경우 REQUEST_HOST_HEADER이고, 미설정하면 ORIGIN_HOSTNAME입니다. 

#### 응답


[응답 본문]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    }
}
```


[필드]

| 필드                   | 타입      | 설명     |
| -------------------- | ------- | ------ |
| header               | Object  | 헤더 영역  |
| header.isSuccessful  | Boolean | 성공 여부  |
| header.resultCode    | Integer | 결과 코드  |
| header.resultMessage | String  | 결과 메시지 |

### 서비스 삭제

#### 요청


[URI]

| 메서드    | URI                                  |
| ------ | ------------------------------------ |
| DELETE | /v2.0/appKeys/{appKey}/distributions |


[요청 본문]

```json
{
    "domains" : [
        "lhcsxuo0.toastcdn.net"
    ]
}
```


[필드]

| 이름      | 타입     | 필수 여부 | 기본값  | 유효 범위 | 설명                    |
| ------- | ------ | ----- | ---- | ----- | --------------------- |
| domains | String | 필수    |      |       | 삭제할 도메인, 여러 도메인 입력 가능 |

**\* 여러 도메인 입력 시, 해당하는 서비스는 모두 종료됩니다.**

#### 응답


[응답 본문]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    }
}
```


[필드]

| 필드                   | 타입      | 설명     |
| -------------------- | ------- | ------ |
| header               | Object  | 헤더 영역  |
| header.isSuccessful  | Boolean | 성공 여부  |
| header.resultCode    | Integer | 결과 코드  |
| header.resultMessage | String  | 결과 메시지 |


## Auth Token API

### Auth Token 생성

#### 요청

[URI]

| 메서드  | URI                           |
| ---- | ----------------------------- |
| POST | /v2.0/appKeys/{appKey}/auth-token |


[요청 본문]

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


[필드]

| 이름      | 타입   | 필수 여부 | 기본값 | 유효 범위             | 설명                                                         |
| --------- | ------ | --------- | ------ | --------------------- | ------------------------------------------------------------ |
| encryptKey    | String | 필수   |        |             | NHN Cloud CDN 콘솔에 표시된 Auth Token 인증 제어 관리 > 토큰 암호화 키 |
| durationSeconds | Integer | 필수 |        | 0~2,147,483,647 | 생성된 토큰이 유효한 시간(초) |
| singlePath      | String | 선택 |        |             | 생성된 토큰을 이용하여 접근할 단일 경로 |
| singleWildcardPath | String | 선택 |     |             | 생성된 토큰을 이용하여 접근할 단일 와일드카드 경로 |
| multipleWildcardPath | String | 선택 |   |             | 생성된 토큰을 이용하여 접근할 여러 개의 와일드카드 경로 |
| sessionId |           String | 선택 |    |  문자열 길이 최대 36바이트           | 단일 접근 요청에 대해 sessionId를 포함하여 토큰을 생성 |

* singlePath, singleWildcardPath, multipleWildcardPath 중 하나 이상의 값이 필수로 존재해야 합니다.
* 토큰 생성 및 사용에 대한 상세한 내용은 [콘솔 사용 가이드 > Auth Token 인증 접근 관리 > 2. 토큰 생성](./console-guide/#auth-token)을 참고하시기 바랍니다.


#### 응답

[응답 본문]

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


[필드]

| 필드                   | 타입      | 설명        |
| -------------------- | ------- | --------- |
| header               | Object  | 헤더 영역     |
| header.isSuccessful  | Boolean | 성공 여부     |
| header.resultCode    | Integer | 결과 코드     |
| header.resultMessage | String  | 결과 메시지    |
| authToken             | Object    | 생성된 Auth Token 오브젝트 |
| authToken.singlePathToken | String    | 단일 경로에 접근할 수 있도록 생성된 인증 토큰                                 |
| authToken.singleWildcardPathToken | String    | 단일 와일드카드 경로에 접근할 수 있도록 생성된 인증 토큰                 |
| authToken.multipleWildcardPathToken | String  | 여러 개의 와일드카드 경로에 접근할 수 있도록 생성된 인증 토큰             |



## 캐시 재배포 API

### 캐시 재배포(Purge) -  ITEM(특정 파일 타입)

#### 요청

[URI]

| 메서드  | URI                           |
| ---- | ----------------------------- |
| POST | /v2.0/appKeys/{appKey}/purge/item |


[요청 본문]

```json
{
	"domain": "sample.toastcdn.net",
	"purgeList":["http://sample.toastcdn.net/img_01.png",
  "http://sample.toastcdn.net/img_02.png"]
}
```


[필드]

| 이름      | 타입   | 필수 여부 | 기본값 | 유효 범위             | 설명                                                         |
| --------- | ------ | --------- | ------ | --------------------- | ------------------------------------------------------------ |
| domain    | String | 필수      |        | 최대 255자            | 재배포할 도메인(서비스) 이름                                 |
| purgeList | List | 필수      |        |                       | 재배포 대상 URL 목록 |

#### 응답

[응답 본문]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    }
}
```


[필드]

| 필드                   | 타입      | 설명        |
| -------------------- | ------- | --------- |
| header               | Object  | 헤더 영역     |
| header.isSuccessful  | Boolean | 성공 여부     |
| header.resultCode    | Integer | 결과 코드     |
| header.resultMessage | String  | 결과 메시지    |

### 캐시 재배포(Purge) -  ALL(전체 파일 타입)

#### 요청

[URI]

| 메서드  | URI                           |
| ---- | ----------------------------- |
| POST | /v2.0/appKeys/{appKey}/purge/all |


[요청 본문]

```json
{
	"domain": "sample.toastcdn.net"
}
```


[필드]

| 이름      | 타입   | 필수 여부 | 기본값 | 유효 범위             | 설명                                                         |
| --------- | ------ | --------- | ------ | --------------------- | ------------------------------------------------------------ |
| domain    | String | 필수      |        | 최대 255자            | 재배포할 도메인(서비스) 이름                                 |

#### 응답

[응답 본문]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    }
}
```


[필드]

| 필드                   | 타입      | 설명        |
| -------------------- | ------- | --------- |
| header               | Object  | 헤더 영역     |
| header.isSuccessful  | Boolean | 성공 여부     |
| header.resultCode    | Integer | 결과 코드     |
| header.resultMessage | String  | 결과 메시지    |

- CDN 서비스를 신규로 생성한 후 약 1시간 이내에는 캐시 재배포 요청이 실패할 수 있습니다. 이후에도 실패가 계속되면 고객 센터로 문의해 주시기 바랍니다.
- 퍼지 API 사용량 제한 정책이 있습니다. 자세한 내용은 [콘솔 사용 가이드 > CDN 캐시 재배포](./console-guide/#cdn-purge)의 캐시 재배포 사용량 제한] 내용을 확인해주세요.

### 캐시 재배포(Purge) 조회
- API v2.0을 통한 캐시 재배포 시, 고속 캐시 재배포가 수행되어 요청 후 수 초 이내에 완료되므로 캐시 재배포 상태를 조회하는 API가 별도로 제공되지 않습니다.

## 인증서 API
### 신규 인증서 발급
#### 요청

[URI]

| 메서드  | URI                           |
| ---- | ----------------------------- |
| POST | /v2.0/appKeys/{appKey}/certificates|


[요청 본문]

```json
{
    "certificateDomain": "example.domain.com",
    "callbackHttpMethod": "POST",
    "callbackUrl": "http://test.callback.com/cdn-certificate?appKey={appKey}&status={status}&domain={domain}"   
}
```


[필드]

| 이름      | 타입   | 필수 여부 | 기본값 | 유효 범위             | 설명                                                         |
| --------- | ------ | --------- | ------ | --------------------- | ------------------------------------------------------------ |
| certificateDomain    | String | 필수      |        | 최대 255자            | 신규 인증서를 발급하고자 하는 도메인(전체 도메인 주소 형식으로 입력)|
| callbackHttpMethod  | String | 선택      |        | GET/POST/PUT        | 인증서 생성 처리 결과를 통보받을 콜백의 HTTP 메서드 |
| callbackUrl         | String | 선택      |        | 최대 1024자           | 인증서 생성 처리 결과를 통보받을 콜백 URL       |

* 인증서 발급에 대한 상세한 내용은 [콘솔 사용 가이드 > 인증서 관리 > 신규 인증서 발급](./console-guide/#_7)을 참고하시기 바랍니다.

#### 응답

[응답 본문]

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


[필드]

| 필드                   | 타입      | 설명        |
| -------------------- | ------- | --------- |
| header               | Object  | 헤더 영역     |
| header.isSuccessful  | Boolean | 성공 여부     |
| header.resultCode    | Integer | 결과 코드     |
| header.resultMessage | String  | 결과 메시지    |
| certificates         | List    | 발급된 인증서 목록 |
| certificates[0].sanDnsId | String | 인증서 ID    |
| certificates[0].dnsName  | String | 인증서 도메인  |
| certificates[0].dnsStatus | String | 인증서 발급 상태 코드([표] 인증서 발급 상태 코드 참고) |
| certificates[0].callbackHttpMethod | String | 인증서 생성 처리 결과를 통보받을 콜백의 HTTP 메서드 |
| certificates[0].callbackUrl | String | 인증서 생성 처리 결과를 통보받을 콜백 URL |
| certificates[0].createDatetime | DateTime | 인증서 생성 일시 |
| certificates[0].updateDatetime | DateTime | 인증서 변경 일시 |
| certificates[0].hasCname | Boolean | CNAME 레코드 설정 여부 |
| certificates[0].hasDistributionDomain | Boolean | CDN 서비스 연동 여부 |
| certificates[0].renewalStartDate | DateTime | 인증서 갱신 시작 일시 |
| certificates[0].renewalEndDate | DateTime | 인증서 갱신 종료 일시 |

### 인증서 목록 조회
#### 요청

[URI]

| 메서드  | URI                           |
| ---- | ----------------------------- |
| GET | /v2.0/appKeys/{appKey}/certificates|


#### 응답

[응답 본문]

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


[필드]

| 필드                   | 타입      | 설명        |
| -------------------- | ------- | --------- |
| header               | Object  | 헤더 영역     |
| header.isSuccessful  | Boolean | 성공 여부     |
| header.resultCode    | Integer | 결과 코드     |
| header.resultMessage | String  | 결과 메시지    |
| certificates         | List    | 발급된 인증서 목록 |
| certificates[0].sanDnsId | String | 인증서 ID    |
| certificates[0].dnsName  | String | 인증서 도메인  |
| certificates[0].dnsStatus | String | 인증서 발급 상태 코드([표] 인증서 발급 상태 코드 참고) |
| certificates[0].callbackHttpMethod | String | 인증서 생성 처리 결과를 통보받을 콜백의 HTTP 메서드 |
| certificates[0].callbackUrl | String | 인증서 생성 처리 결과를 통보받을 콜백 URL |
| certificates[0].createDatetime | DateTime | 인증서 생성 일시 |
| certificates[0].updateDatetime | DateTime | 인증서 변경 일시 |
| certificates[0].hasCname | Boolean | CNAME 레코드 설정 여부 |
| certificates[0].hasDistributionDomain | Boolean | CDN 서비스 연동 여부 |
| certificates[0].renewalStartDate | DateTime | 인증서 갱신 시작 일시 |
| certificates[0].renewalEndDate | DateTime | 인증서 갱신 종료 일시 |

### 인증서 삭제
#### 요청

[URI]

| 메서드  | URI                           |
| ---- | ----------------------------- |
| DELETE | /v2.0/appKeys/{appKey}/certificates|


[파라미터]

| 이름   | 타입   | 필수 여부 | 유효 범위     | 설명                         |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| dnsIdList | String | 필수      |     | 삭제할 인증서 ID(sanDnsId) 목록(,로 연결된 인증서 ID 목록)   |

[예]
```
curl -X GET "https://kr1-cdn.api.nhncloudservice.com/v2.0/appKeys/{appKey}/certificates?dnsIdList={dnsIdList}" \
 -H "Authorization: {secretKey}" \
 -H "Content-Type: application/json"
```

#### 응답

[응답 본문]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    }
}
```


[필드]

| 필드                   | 타입      | 설명        |
| -------------------- | ------- | --------- |
| header               | Object  | 헤더 영역     |
| header.isSuccessful  | Boolean | 성공 여부     |
| header.resultCode    | Integer | 결과 코드     |
| header.resultMessage | String  | 결과 메시지    |

## 통계 API
### 트래픽 통계 조회
#### 요청

[URI]

| 메서드  | URI                           |
| ---- | ----------------------------- |
| GET | /v2.0/appKeys/{appKey}/statistics/traffic|


[파라미터]

| 이름   | 타입   | 필수 여부 | 유효 범위     | 설명                         |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| domain | String | 필수      | 최대 255자    | 조회할 도메인(서비스 이름)   |
| fromDate | DateTime | 필수      |  | 통계 조회 시작 일시 |
| toDate | DateTime | 필수      |  | 통계 조회 종료 일시 |

- fromDate, toDate 필드의 조회 기간은 최근 90일까지 조회할 수 있습니다.
- fromDate, toDate 필드는 ISO 8601 형식의 날짜 문자열 형식으로 입력합니다.
  - UTC 표기: yyyy-MM-dd'T'HH:mm:ssZ
  - UTC 기준 타임 오프셋 표기: yyyy-MM-dd'T'HH:mm:ss±hh:mm

[예]
```
curl -X GET "https://kr1-cdn.api.nhncloudservice.com/v2.0/appKeys/{appKey}/statistics/traffic?domain={domain}&fromDate={fromDate}&toDate={toDate}" \
 -H "Authorization: {secretKey}" \
 -H "Content-Type: application/json"
```

#### 응답

[응답 본문]

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


[필드]

| 필드                   | 타입      | 설명        |
| -------------------- | ------- | --------- |
| header               | Object  | 헤더 영역     |
| header.isSuccessful  | Boolean | 성공 여부     |
| header.resultCode    | Integer | 결과 코드     |
| header.resultMessage | String  | 결과 메시지    |
| statistics         | List    | 트래픽 통계 데이터 목록 |
| statistics[0].dateTime | DateTime | 통계 시간    |
| statistics[0].bandwidth  | String | 통계 시간의 대역폭(Mbps)  |
| statistics[0].transferred | String | 통계 시간의 전송량(bytes) |

### HTTP 상태 코드별 통계 조회
#### 요청

[URI]

| 메서드  | URI                           |
| ---- | ----------------------------- |
| GET | /v2.0/appKeys/{appKey}/statistics/http|


[파라미터]

| 이름   | 타입   | 필수 여부 | 유효 범위     | 설명                         |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| domain | String | 필수      | 최대 255자    | 조회할 도메인(서비스 이름)   |
| fromDate | DateTime | 필수      |  | 통계 조회 시작 일시 |
| toDate | DateTime | 필수      |  | 통계 조회 종료 일시 |

- fromDate, toDate 필드의 조회 기간은 최근 90일까지 조회할 수 있습니다.
- fromDate, toDate 필드는 ISO 8601 형식의 날짜 문자열 형식으로 입력합니다.
  - UTC 표기: yyyy-MM-dd'T'HH:mm:ssZ
  - UTC 기준 타임 오프셋 표기: yyyy-MM-dd'T'HH:mm:ss±hh:mm

[예]
```
curl -X GET "https://kr1-cdn.api.nhncloudservice.com/v2.0/appKeys/{appKey}/statistics/http?domain={domain}&fromDate={fromDate}&toDate={toDate}" \
 -H "Authorization: {secretKey}" \
 -H "Content-Type: application/json"
```

#### 응답

[응답 본문]

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


[필드]

| 필드                   | 타입      | 설명        |
| -------------------- | ------- | --------- |
| header               | Object  | 헤더 영역     |
| header.isSuccessful  | Boolean | 성공 여부     |
| header.resultCode    | Integer | 결과 코드     |
| header.resultMessage | String  | 결과 메시지    |
| statistics         | List    | 트래픽 통계 데이터 목록 |
| statistics[0].dateTime | DateTime | 통계 시간    |
| statistics[0].successHits  | Long | 응답 HTTP 상태 코드가 2xx인 호출 수  |
| statistics[0].notModifiedHits | Long | 응답 HTTP 상태 코드가 304인 호출 수 |
| statistics[0].redirectsHits | Long | 응답 HTTP 상태 코드가 301, 302인 호출 수 |
| statistics[0].notFoundHits | Long | 응답 HTTP 상태 코드가 404인 호출 수 |
| statistics[0].permissionHits | Long | 응답 HTTP 상태 코드가 401, 403, 415인 호출 수 |
| statistics[0].serverErrorHits | Long | 응답 HTTP 상태 코드가 5xx인 호출 수 |
| statistics[0].etcHits | Long | 2xx, 3xx, 4xx, 5xx 외 응답 HTTP 상태 코드 API 호출 수 |

### 다운로드가 가장 많은 콘텐츠의 순위 통계
#### 요청

[URI]

| 메서드  | URI                           |
| ---- | ----------------------------- |
| GET | /v2.0/appKeys/{appKey}/statistics/topcontent|


[파라미터]

| 이름   | 타입   | 필수 여부 | 유효 범위     | 설명                         |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| domain | String | 필수      | 최대 255자    | 조회할 도메인(서비스 이름)   |
| fromDate | DateTime | 필수      |  | 통계 조회 시작 일시 |
| toDate | DateTime | 필수      |  | 통계 조회 종료 일시 |

- fromDate, toDate 필드의 조회 기간은 최근 90일까지 조회할 수 있습니다.
- fromDate, toDate 필드는 ISO 8601 형식의 날짜 문자열 형식으로 입력합니다.
  - UTC 표기: yyyy-MM-dd'T'HH:mm:ssZ
  - UTC 기준 타임 오프셋 표기: yyyy-MM-dd'T'HH:mm:ss±hh:mm

[예]
```
curl -X GET "https://kr1-cdn.api.nhncloudservice.com/v2.0/appKeys/{appKey}/statistics/topcontent?domain={domain}&fromDate={fromDate}&toDate={toDate}" \
 -H "Authorization: {secretKey}" \
 -H "Content-Type: application/json"
```

#### 응답

[응답 본문]

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


[필드]

| 필드                   | 타입      | 설명        |
| -------------------- | ------- | --------- |
| header               | Object  | 헤더 영역     |
| header.isSuccessful  | Boolean | 성공 여부     |
| header.resultCode    | Integer | 결과 코드     |
| header.resultMessage | String  | 결과 메시지    |
| statistics         | List    | 트래픽 통계 데이터 목록 |
| statistics[0].rank | Integer | 통계 시간    |
| statistics[0].successHits  | Long | 응답 HTTP 상태 코드가 2xx인 호출 수  |
| statistics[0].succDataTransferred  | Long | 응답 HTTP 상태 코드가 2xx인 호출 트래픽 전송량(MBytes)  |



## 콜백 응답
### CDN 서비스
CDN 서비스에 콜백 기능이 설정된 경우, 생성, 수정, 일시 정지, 재개, 삭제 변경이 완료되면 설정된 콜백 URL을 호출합니다.
콜백 호출 시 요청 본문에는 다음과 같은 CDN 서비스 설정 정보가 포함됩니다.

[응답 본문]
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

[필드]

| 필드                                   | 타입    | 설명                                                         |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | 헤더 영역                                                    |
| header.isSuccessful                    | Boolean | 성공 여부                                                    |
| header.resultCode                      | Integer | 결과 코드                                                    |
| header.resultMessage                   | String  | 결과 메시지                                                  |
| distribution                          | Object    | 변경 작업이 완료된 CDN 오브젝트                                   |
| distribution.appKey                   | String    | 앱키                                  |
| distribution.domain                | String  | 도메인 이름(서비스 이름)                                     |
| distribution.domainAlias           | List  | 도메인 별칭 목록(개인 혹은 회사가 소유한 도메인 사용)                                 |
| distribution.region                | String  | 서비스 지역("GLOBAL": 글로벌)             |
| distribution.status                | String  | CDN 상태 코드([표] CDN 상태 코드 참고)                                 |
| distribution.defaultMaxAge         | Integer  | 캐시 만료 시간(초)                                           |
| distribution.cacheKeyQueryParam    | String  | 캐시 키에 요청 쿼리 문자열 포함 여부 설정("INCLUDE_ALL": 전체 포함, "EXCLUDE_ALL": 전체 미포함) |
| distribution.referrerType          | String  | 리퍼러 접근 관리("BLACKLIST": 블랙리스트, "WHITELIST": 화이트리스트) |
| distribution.referrers             | List    | 정규 표현식 형태의 리퍼러 헤더 목록                                 |
| distribution.useOriginCacheControl | Boolean | 원본 서버 설정 사용 여부(true: 원본 서버 설정 사용, false: 사용자 설정 사용) |
| distribution.createTime            | DateTime | 생성 일시                                         |
| distribution.deleteTime            | DateTime | 삭제 일시                                         |
| distribution.origins               | List    | 원본 서버 오브젝트 목록                                      |
| distribution.origins[0].origin     | String  | 원본 서버(도메인 또는 IP)                                      |
| distribution.origins[0].originPath | String  | 원본 서버 하위 경로                                          |
| distribution.origins[0].httpPort   | Integer | 원본 서버 HTTP 프로토콜 포트                                               |
| distribution.origins[0].httpsPort  | Integer | 원본 서버 HTTPS 프로토콜 포트                                               |
| distribution.useOriginHttpProtocolDowngrade | Boolean | 원본 서버가 HTTP 응답만 가능한 경우, CDN 서버에서 원본 서버로 요청 시 HTTPS 요청을 HTTP 요청으로 다운그레이드 하기 위한 설정 사용 여부 |
| distribution.forwardHostHeader     | String  | CDN 서버가 원본 서버로 콘텐츠 요청 시 전달 할 호스트 헤더 설정("ORIGIN_HOSTNAME": 원본 서버의 호스트 이름으로 설정, "REQUEST_HOST_HEADER": 클라이언트 요청의 호스트헤더로 설정 |
| distribution.rootPathAccessControl  | Object  | CDN 서비스의 루트 경로에 대한 접근 제어 설정 | 
| distribution.rootPathAccessControl.enable | Boolean | 루트 경로에 대한 접근 제어 사용(true)/미사용(false) 여부          |
| distribution.rootPathAccessControl.controlType  | String  | enable이 true일 경우 필수 입력. 루트 경로에 대한 접근 제어 방식("DENY": 접근 거부, "REDIRECT": 지정한 경로로 리다이렉트) | 
| distribution.rootPathAccessControl.redirectPath | String | controlType이 "REDIRECT"일 경우 필수 입력. 루트 경로에 대한 요청을 리다이렉트할 경로(/를 포함한 경로로 입력해 주세요.)        |
| distribution.rootPathAccessControl.redirectStatusCode | Integer | controlType이 "REDIRECT"일 경우 필수 입력. 리다이렉트시 전달되는 HTTP 응답 코드         |
| distribution.modifyOutgoingResponseHeaderControl                      | Object  | CDN에서 응답하는 HTTP 헤더를 추가/변경/삭제하는 설정  |
| distribution.modifyOutgoingResponseHeaderControl.enable               | Boolean | HTTP 응답 헤더를 추가/변경/삭제하는 설정 사용(true)/미사용(false) 여부  |
| distribution.modifyOutgoingResponseHeaderControl.headerList           | List    | HTTP 응답 헤더 목록 |
| distribution.modifyOutgoingResponseHeaderControl.headerList[0].action | String  | HTTP 응답 헤더 변경 방식 |
| distribution.modifyOutgoingResponseHeaderControl.headerList[0].standardHeaderName | String  | 일반 HTTP 응답 헤더 이름 |
| distribution.modifyOutgoingResponseHeaderControl.headerList[0].customHeaderName | String  | standardHeaderName이 "OTHER"일 경우 필수 입력. 사용자 정의 HTTP 응답 헤더 이름 |
| distribution.modifyOutgoingResponseHeaderControl.headerList[0].headerValue | String  | HTTP 응답 헤더 값 |
| distribution.callback              | Object  | 서비스 배포 처리 결과를 통보받을 콜백                        |
| distribution.callback.httpMethod   | String  | 콜백의 HTTP 메서드                                           |
| distribution.callback.url          | String  | 콜백 URL                                                     |

### 인증서
인증서 발급 요청 시 콜백 정보가 설정된 경우, 도메인 검증/도메인 검증 완료/인증서 발급 완료로 상태 변경이 완료되면 설정된 콜백 URL을 호출합니다.
콜백 호출 시 요청 본문에는 다음과 같은 인증서 설정 정보가 포함됩니다.

[응답 본문]
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

[필드]

| 필드                                   | 타입    | 설명                                                         |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | 헤더 영역                                                    |
| header.isSuccessful                    | Boolean | 성공 여부                                                    |
| header.resultCode                      | Integer | 결과 코드                                                    |
| header.resultMessage                   | String  | 결과 메시지                                                  |
| certificate                          | Object    | 변경 작업이 완료된 인증서 오브젝트                                  |
| certificate.sanDnsId                   | String    | 인증서 ID                                  |
| certificate.distributionSeq                   | String    | 연동된 CDN 서비스 ID                                  |
| certificate.dnsName  | String | 인증서 도메인  |
| certificate.dnsStatus | String | 인증서 발급 상태 코드([표] 인증서 발급 상태 코드 참고) |
| certificate.validationDnsRecordName | String | 도메인 검증 정보(DNS TXT 레코드 추가 방식의 레코드 이름)  |
| certificate.validationDnsToken | String | 도메인 검증 정보(DNS TXT 레코드 추가 방식의 레코드 값)  |
| certificate.validationHtmlUrl | String | 도메인 검증 정보(HTTP 페이지 추가 방식의 HTTP 페이지 URL)  |
| certificate.validationHtmlToken | String | 도메인 검증 정보(HTTP 페이지 추가 방식의 HTTP 페이지 본문 콘텐츠 값)  |
| certificate.validationExpireDatetime | DateTime | 도메인 검증 만료 일시  |
| certificate.createDatetime | DateTime | 인증서 생성 일시 |
| certificate.updateDatetime | DateTime | 인증서 변경 일시 |
| certificate.deleteDatetime | DateTime | 인증서 삭제 일시 |
| certificate.callbackHttpMethod | String | 인증서 생성 처리 결과를 통보받을 콜백의 HTTP 메서드 |
| certificate.callbackUrl | String | 인증서 생성 처리 결과를 통보받을 콜백 URL |
