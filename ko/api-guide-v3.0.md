<!-- pre-align:aligned sig=9f8de2304f9d -->

<a id="content-delivery-cdn-api-v30-guide"></a>
## Content Delivery > CDN > API v3.0 가이드 { #content-delivery-cdn-api-v30-guide }

NHN Cloud CDN에서 제공하는 Public API v3.0을 설명합니다.

<a id="api"></a>
## API 공통 정보 { #api }

<a id="domain"></a>
### 도메인 { #domain }

| 이름              | 도메인                                 |
| --------------- | ----------------------------------- |
| CDN Public API 도메인 | https://cdn.api.nhncloudservice.com |

<a id="authentication-and-authorization"></a>
### 인증 및 권한 { #authentication-and-authorization }
CDN API v3.0은 API 인증 호출 및 인증을 위해 Appkey와 User Access Key 토큰을 지원합니다.

Appkey는 NHN Cloud의 각 서비스별로 발급되는 고유 인증 키로 API 요청 시 서비스 식별과 유효성 검증에 사용됩니다.<br>User Access Key 토큰은 User Access Key를 기반으로 발급되는 Bearer 타입의 일시적 액세스 토큰입니다.
각 인증 방법의 확인 및 사용에 대한 자세한 내용은 각각 [Appkey](/nhncloud/ko/public-api/appkey/)와 [User Access Key 토큰](/nhncloud/ko/public-api/user-access-key-token/)을 참고하세요.

발급 받은 토큰은 요청 Header에 포함해야 합니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| X-NHN-AUTHORIZATION | Header | String | O | Public API로 발급 받은 Bearer 유형 토큰 |

<a id="response-common-information"></a>
### 응답 공통 정보 { #response-common-information }

- 모든 API 요청에 '200 OK'로 응답합니다. 자세한 응답 결과는 응답 본문의 헤더를 참고하세요.

[성공 응답 본문]

```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    }
}
```

<a id="response-common-information-cdn-status-code"></a>
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

<a id="response-common-information-certificate-issuance-status-codes"></a>
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


<a id="service-api"></a>
## 서비스 API { #service-api }

<a id="create-service"></a>
### 서비스 생성 { #create-service }

<a id="create-service-request"></a>
#### 요청


[URI]

| 메서드  | URI                                  |
| ---- |--------------------------------------|
| POST | /v3.0/appKeys/{appKey}/distributions |


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
        "url": "http://test.callback.com/cdn?appKey={appKey}&status={status}&domain={domain}"
      }
    }
  ]
}
```

[필드]

| 이름                                                                                    | 타입      | 필수 여부 | 기본값         | 유효 범위                                                                 | 설명                                                                                                                        |
|---------------------------------------------------------------------------------------|---------|-------|-------------|-----------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| distributions                                                                         | List    | 필수    |             |                                                                       | 생성할 CDN의 오브젝트 목록                                                                                                          |
| distributions[0].useOriginHttpProtocolDowngrade                                       | Boolean | 필수    | false       | true/false                                                            | 원본 서버가 HTTP 응답만 가능한 경우, CDN 서버에서 원본 서버로 요청 시 HTTPS 요청을 HTTP 요청으로 다운그레이드하기 위한 설정 사용 여부                                     |
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
| distributions[0].domainAlias                                                          | List    | 선택    |             |                                                                       | 도메인 별칭 목록(개인 또는 회사가 소유한 도메인 사용)                                                                                           |
| distributions[0].defaultMaxAge                                                        | Integer | 선택    | 0           | 0~2,147,483,647                                                       | 캐시 만료 시간(초), 기본값 0은 604,800초입니다.                                                                                          |
| distributions[0].cacheKeyQueryParam                                                   | String  | 선택    | INCLUDE_ALL | INCLUDE_ALL/EXCLUDE_ALL                                               | 캐시 키에 요청 쿼리 문자열 포함 여부 설정("INCLUDE_ALL": 전체 포함, "EXCLUDE_ALL": 전체 미포함)                                                     |
| distributions[0].origins                                                              | List    | 필수    |             |                                                                       | 원본 서버 오브젝트 목록                                                                                                             |
| distributions[0].origins[0].origin                                                    | String  | 필수    |             | 최대 255자                                                               | 원본 서버(도메인 또는 IP)                                                                                                          |
| distributions[0].origins[0].originPath                                                | String  | 선택    |             | 최대 8192자                                                              | 원본 서버 하위 경로(/를 포함한 경로로 입력합니다.)                                                                                          |
| distributions[0].origins[0].httpPort                                                  | Integer | 선택    |             | [콘솔 사용 가이드 > 원본 서버](./console-guide/#origin-server-port)의 '[표 2] 사용 가능한 원본 서버 포트 번호' 참고 | 원본 서버 HTTP 프로토콜 포트(origins[0].httpPort와 origins[0].httpsPort 중 하나는 반드시 입력해야 합니다.)                                         |
| distributions[0].origins[0].httpsPort                                                 | Integer | 선택    |             | [콘솔 사용 가이드 > 원본 서버](./console-guide/#origin-server-port)의 '[표 2] 사용 가능한 원본 서버 포트 번호' 참고 | 원본 서버 HTTPS 프로토콜 포트(origins[0].httpPort와 origins[0].httpsPort 중 하나는 반드시 입력해야 합니다.)                                        |
| distributions[0].rootPathAccessControl                                                | Object  | 선택    |             |                                                                       | CDN 서비스의 루트 경로에 대한 접근 제어 설정                                                                                               | 
| distributions[0].rootPathAccessControl.enable                                         | Boolean | 필수    | true        | true/false                                                            | 루트 경로에 대한 접근 제어 사용(true)/미사용(false) 여부                                                                                    |
| distributions[0].rootPathAccessControl.controlType                                    | String  | 선택    |             | DENY, REDIRECT                                                        | enable이 true일 경우 필수 입력. 루트 경로에 대한 접근 제어 방식("DENY": 접근 거부, "REDIRECT": 지정한 경로로 리다이렉트)                                      | 
| distributions[0].rootPathAccessControl.redirectPath                                   | String  | 선택    |             |                                                                       | controlType이 "REDIRECT"일 경우 필수 입력. 루트 경로에 대한 요청을 리다이렉트할 경로(/를 포함한 경로로 입력합니다.)                                           |
| distributions[0].rootPathAccessControl.redirectStatusCode                             | Integer | 선택    |             | 301, 302, 303, 307                                                    | controlType이 "REDIRECT"일 경우 필수 입력. 리다이렉트 시 전달되는 HTTP 응답 코드                                                                 |
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

- `forwardHostHeader`의 기본값은 `domainAlias`를 설정한 경우 `REQUEST_HOST_HEADER`이고, 설정하지 않으면 `ORIGIN_HOSTNAME`입니다.



<a id="create-service-response"></a>
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
                "url": "http://test.callback.com/cdn?appKey={appKey}&status={status}&domain={domain}"
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
| distributions[0].domain                | String  | 생성된 도메인(서비스 이름)                                 |
| distributions[0].domainAlias           | List    | 도메인 별칭 목록(개인 또는 회사가 소유한 도메인 사용)            |
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
| distributions[0].useOriginHttpProtocolDowngrade | Boolean | 원본 서버가 HTTP 응답만 가능한 경우, CDN 서버에서 원본 서버로 요청 시 HTTPS 요청을 HTTP 요청으로 다운그레이드하기 위한 설정 사용 여부 |
| distributions[0].forwardHostHeader     | String  | CDN 서버가 원본 서버로 콘텐츠 요청 시 전달할 호스트 헤더 설정("ORIGIN_HOSTNAME": 원본 서버의 호스트 이름으로 설정, "REQUEST_HOST_HEADER": 클라이언트 요청의 호스트 헤더로 설정) |
| distributions[0].rootPathAccessControl  | Object  | CDN 서비스의 루트 경로에 대한 접근 제어 설정 | 
| distributions[0].rootPathAccessControl.enable | Boolean | 루트 경로에 대한 접근 제어 사용(true)/미사용(false) 여부        |
| distributions[0].rootPathAccessControl.controlType  | String  | enable이 true일 경우 필수 입력. 루트 경로에 대한 접근 제어 방식("DENY": 접근 거부, "REDIRECT": 지정한 경로로 리다이렉트) | 
| distributions[0].rootPathAccessControl.redirectPath | String | controlType이 "REDIRECT"일 경우 필수 입력. 루트 경로에 대한 요청을 리다이렉트할 경로(/를 포함한 경로로 입력합니다.)      |
| distributions[0].rootPathAccessControl.redirectStatusCode | Integer | controlType이 "REDIRECT"일 경우 필수 입력. 리다이렉트 시 전달되는 HTTP 응답 코드        |
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



<a id="retrieve-service"></a>
### 서비스 조회 { #retrieve-service }

<a id="retrieve-service-request"></a>
#### 요청


[URI]

| 메서드  | URI                                  |
| ---- | ------------------------------------ |
| GET  | /v3.0/appKeys/{appKey}/distributions |


[파라미터]

| 이름   | 타입   | 필수 여부 | 유효 범위     | 설명                         |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| domain | String | 선택      | 최대 255자    | 조회할 도메인(서비스 이름)   |
| status | String | 선택      | CDN 상태 코드 | CDN 상태 코드([표] CDN 상태 코드 참고) |

[예]
```
curl -X GET "https://cdn.api.nhncloudservice.com/v3.0/appKeys/{appKey}/distributions?domain={domain}" \
 -H "X-NHN-AUTHORIZATION: {secretKey}" \
 -H "Content-Type: application/json"
```

<a id="retrieve-service-response"></a>
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


[필드]

| 필드                                   | 타입    | 설명                                                         |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | 헤더 영역                                                    |
| header.isSuccessful                    | Boolean | 성공 여부                                                    |
| header.resultCode                      | Integer | 결과 코드                                                    |
| header.resultMessage                   | String  | 결과 메시지                                                  |
| distributions                          | List    | 생성된 CDN 오브젝트 목록                                     |
| distributions[0].domain                | String  | 도메인(서비스 이름)                                     |
| distributions[0].domainAlias           | List  | 도메인 별칭 목록(개인 또는 회사가 소유한 도메인 사용)                                                  |
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
| distributions[0].useOriginHttpProtocolDowngrade | Boolean | 원본 서버가 HTTP 응답만 가능한 경우, CDN 서버에서 원본 서버로 요청 시 HTTPS 요청을 HTTP 요청으로 다운그레이드하기 위한 설정 사용 여부 |
| distributions[0].forwardHostHeader     | String  | CDN 서버가 원본 서버로 콘텐츠 요청 시 전달할 호스트 헤더 설정("ORIGIN_HOSTNAME": 원본 서버의 호스트 이름으로 설정, "REQUEST_HOST_HEADER": 클라이언트 요청의 호스트 헤더로 설정) |
| distributions[0].rootPathAccessControl  | Object  | CDN 서비스의 루트 경로에 대한 접근 제어 설정 | 
| distributions[0].rootPathAccessControl.enable | Boolean | 루트 경로에 대한 접근 제어 사용(true)/미사용(false) 여부          |
| distributions[0].rootPathAccessControl.controlType  | String  | enable이 true일 경우 필수 입력. 루트 경로에 대한 접근 제어 방식("DENY": 접근 거부, "REDIRECT": 지정한 경로로 리다이렉트) | 
| distributions[0].rootPathAccessControl.redirectPath | String | controlType이 "REDIRECT"일 경우 필수 입력. 루트 경로에 대한 요청을 리다이렉트할 경로(/를 포함한 경로로 입력합니다.)        |
| distributions[0].rootPathAccessControl.redirectStatusCode | Integer | controlType이 "REDIRECT"일 경우 필수 입력. 리다이렉트 시 전달되는 HTTP 응답 코드          |
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


<a id="modify-service"></a>
### 서비스 수정 { #modify-service }

<a id="modify-service-request"></a>
#### 요청


[URI]

| 메서드  | URI                                  |
| ---- | ------------------------------------ |
| PUT  | /v3.0/appKeys/{appKey}/distributions |


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
          "url": "http://test.callback.com/cdn?appKey={appKey}&status={status}&domain={domain}"
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
| domainAlias           | List    | 선택      |        | 최대 255자                                                   | 도메인 별칭(개인 또는 회사가 소유한 도메인 사용) |
| defaultMaxAge         | Integer | 선택      | 0      | 0~2,147,483,647                                            | 캐시 만료 시간(초), 기본값 0은 604,800초입니다.              |
| cacheKeyQueryParam    | String  | 선택      | INCLUDE_ALL | INCLUDE_ALL/EXCLUDE_ALL                               | 캐시 키에 요청 쿼리 문자열 포함 여부 설정("INCLUDE_ALL": 전체 포함, "EXCLUDE_ALL": 전체 미포함) |
| origins               | List    | 필수      |        |                                                              | 원본 서버                                                    |
| origins[0].origin     | String  | 필수      |        | 최대 255자                                                   | 원본 서버(도메인 또는 IP)                                      |
| origins[0].originPath | String  | 선택      |        | 최대 8192자                                                  | 원본 서버 하위 경로                                          |
| origins[0].httpPort   | Integer  | 선택      |        |[콘솔 사용 가이드 > 원본 서버](./console-guide/#origin-server-port)의 '[표 2] 사용 가능한 원본 서버 포트 번호' 참고| 원본 서버 HTTP 프로토콜 포트(origins[0].httpPort와 origins[0].httpsPort 중 하나는 반드시 입력해야 합니다.)  |
| origins[0].httpsPort  | Integer  | 선택      |        |[콘솔 사용 가이드 > 원본 서버](./console-guide/#origin-server-port)의 '[표 2] 사용 가능한 원본 서버 포트 번호' 참고 | 원본 서버 HTTPS 프로토콜 포트(origins[0].httpPort와 origins[0].httpsPort 중 하나는 반드시 입력해야 합니다.) |
| useOriginHttpProtocolDowngrade | Boolean  | 필수     | false       | true/false         | 원본 서버가 HTTP 응답만 가능한 경우, CDN 서버에서 원본 서버로 요청 시 HTTPS 요청을 HTTP 요청으로 다운그레이드하기 위한 설정 사용 여부 |
| forwardHostHeader     | String  | 필수      |        | ORIGIN_HOSTNAME<br/>REQUEST_HOST_HEADER   | CDN 서버가 원본 서버로 콘텐츠 요청 시 전달할 호스트 헤더 설정("ORIGIN_HOSTNAME": 원본 서버의 호스트 이름으로 설정, "REQUEST_HOST_HEADER": 클라이언트 요청의 호스트 헤더로 설정)|
| rootPathAccessControl  | Object  | 선택 |  |  | CDN 서비스의 루트 경로에 대한 접근 제어 설정 | 
| rootPathAccessControl.enable | Boolean | 필수 | false | true/false | 루트 경로에 대한 접근 제어 사용(true)/미사용(false) 여부          |
| rootPathAccessControl.controlType  | String  | 선택 |  | DENY, REDIRECT | enable이 true일 경우 필수 입력. 루트 경로에 대한 접근 제어 방식("DENY": 접근 거부, "REDIRECT": 지정한 경로로 리다이렉트) | 
| rootPathAccessControl.redirectPath | String | 선택 |  | | controlType이 "REDIRECT"일 경우 필수 입력. 루트 경로에 대한 요청을 리다이렉트할 경로(/를 포함한 경로로 입력합니다.)        |
| rootPathAccessControl.redirectStatusCode | Integer | 선택 | | 301, 302, 303, 307 |controlType이 "REDIRECT"일 경우 필수 입력. 리다이렉트 시 전달되는 HTTP 응답 코드          |
| modifyOutgoingResponseHeaderControl                                  | Object  | 선택    |             |                                                                       | CDN에서 응답하는 HTTP 헤더를 추가/변경/삭제하는 설정                                                                                         |
| modifyOutgoingResponseHeaderControl.enable                           | Boolean | 필수    | true        | true/false                                                            | HTTP 응답 헤더를 추가/변경/삭제하는 설정 사용(true)/미사용(false) 여부                                                                          |
| modifyOutgoingResponseHeaderControl.headerList                       | List    | 선택    |         |                                                                       | HTTP 응답 헤더 목록                                                                                                             |
| modifyOutgoingResponseHeaderControl.headerList[0].action             | String  | 선택    |         | ADD, MODIFY, DELETE                                                   | HTTP 응답 헤더 변경 방식                                                                                                          |
| modifyOutgoingResponseHeaderControl.headerList[0].standardHeaderName | String  | 선택    |         | ACCESS_CONTROL_ALLOW_CREDENTIALS<br/>ACCESS_CONTROL_ALLOW_HEADERS<br/>ACCESS_CONTROL_ALLOW_METHODS<br/>ACCESS_CONTROL_ALLOW_ORIGIN<br/>ACCESS_CONTROL_EXPOSE_HEADERS<br/>ACCESS_CONTROL_MAX_AGE<br/>CACHE_CONTROL<br/>CONTENT_DISPOSITION<br/>CONTENT_TYPE<br/>P3P<br/>PRAGMA<br/>OTHER | 일반 HTTP 응답 헤더 이름                                                                                                          |
| modifyOutgoingResponseHeaderControl.headerList[0].customHeaderName   | String  | 선택    |         |                                                      | standardHeaderName이 "OTHER"일 경우 필수 입력. 사용자 정의 HTTP 응답 헤더 이름                                                               |
| modifyOutgoingResponseHeaderControl.headerList[0].headerValue        | String  | 필수    |         |                                                      | HTTP 응답 헤더 값                                                                                                              |
| callback              | Object  | 선택      |        |                                                              | CDN 서비스 배포 결과를 통보받을 콜백 URL(콜백 설정은 선택 입력입니다.) |
| callback.httpMethod   | String  | 필수      |        | GET/POST/PUT                                                 | 콜백의 HTTP 메서드                                           |
| callback.url          | String  | 필수      |        | 최대 1024자                                                  | 콜백 URL                                                     |

- `forwardHostHeader`의 기본값은 `domainAlias`를 설정한 경우 `REQUEST_HOST_HEADER`이고, 설정하지 않으면 `ORIGIN_HOSTNAME`입니다.

<a id="modify-service-response"></a>
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

<a id="delete-service"></a>
### 서비스 삭제 { #delete-service }

<a id="delete-service-request"></a>
#### 요청


[URI]

| 메서드    | URI                                  |
| ------ | ------------------------------------ |
| DELETE | /v3.0/appKeys/{appKey}/distributions |


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

> [주의] 여러 도메인을 입력하면 해당하는 서비스는 모두 종료됩니다.

<a id="delete-service-response"></a>
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


<a id="auth-token-api"></a>
## Auth Token API { #auth-token-api }

<a id="auth-token"></a>
### Auth Token 생성 { #auth-token }

<a id="create-auth-token-request"></a>
#### 요청

[URI]

| 메서드  | URI                           |
| ---- | ----------------------------- |
| POST | /v3.0/appKeys/{appKey}/auth-token |


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
| encryptKey    | String | 필수   |        |             | NHN Cloud CDN 콘솔에 표시된 Auth Token 인증 접근 관리 > 토큰 암호화 키 |
| durationSeconds | Integer | 필수 |        | 0~2,147,483,647 | 생성된 토큰이 유효한 시간(초) |
| singlePath      | String | 선택 |        |             | 생성된 토큰을 이용하여 접근할 단일 경로 |
| singleWildcardPath | String | 선택 |     |             | 생성된 토큰을 이용하여 접근할 단일 와일드카드 경로 |
| multipleWildcardPath | String | 선택 |   |             | 생성된 토큰을 이용하여 접근할 여러 개의 와일드카드 경로 |
| sessionId |           String | 선택 |    |  문자열 길이 최대 36바이트           | 단일 접근 요청에 대해 sessionId를 포함하여 토큰을 생성 |

* `singlePath`, `singleWildcardPath`, `multipleWildcardPath` 중 하나 이상의 값이 필수로 존재해야 합니다.
* 토큰 생성 및 사용에 대한 상세한 내용은 [콘솔 사용 가이드 > Auth Token 인증 접근 관리 > 2. 토큰 생성](./console-guide/#access-control-for-auth-token-authentication-create-a-token)을 참고하세요.


<a id="create-auth-token-response"></a>
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



<a id="purge-cache-api"></a>
## 캐시 재배포 API { #purge-cache-api }

<a id="purge-cache---item-particular-file-type"></a>
### 캐시 재배포(Purge) - ITEM(특정 파일 타입) { #purge-cache---item-particular-file-type }

<a id="purge-cache---item-particular-file-type-request"></a>
#### 요청

[URI]

| 메서드  | URI                           |
| ---- | ----------------------------- |
| POST | /v3.0/appKeys/{appKey}/purge/item |


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
| domain    | String | 필수      |        | 최대 255자            | 재배포할 도메인(서비스 이름)                                 |
| purgeList | List | 필수      |        |                       | 재배포 대상 URL 목록 |

<a id="purge-cache---item-particular-file-type-response"></a>
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

<a id="purge-cache---all-all-file-types"></a>
### 캐시 재배포(Purge) - ALL(전체 파일 타입) { #purge-cache---all-all-file-types }

<a id="purge-cache---all-all-file-types-request"></a>
#### 요청

[URI]

| 메서드  | URI                           |
| ---- | ----------------------------- |
| POST | /v3.0/appKeys/{appKey}/purge/all |


[요청 본문]

```json
{
	"domain": "sample.toastcdn.net"
}
```


[필드]

| 이름      | 타입   | 필수 여부 | 기본값 | 유효 범위             | 설명                                                         |
| --------- | ------ | --------- | ------ | --------------------- | ------------------------------------------------------------ |
| domain    | String | 필수      |        | 최대 255자            | 재배포할 도메인(서비스 이름)                                 |

<a id="purge-cache---all-all-file-types-response"></a>
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

- CDN 서비스를 신규로 생성한 후 약 1시간 이내에는 캐시 재배포 요청이 실패할 수 있습니다. 이후에도 실패가 계속되면 고객지원으로 문의하세요.
- Purge API 사용량 제한 정책이 있습니다. 자세한 내용은 [콘솔 사용 가이드 > CDN 캐시 재배포](./console-guide/#purge)의 '캐시 재배포 사용량 제한' 내용을 확인하세요.

<a id="purge"></a>
### 캐시 재배포(Purge) 조회 { #purge }
- API v3.0을 통한 캐시 재배포 시, 고속 캐시 재배포가 수행되어 요청 후 수 초 이내에 완료되므로 캐시 재배포 상태를 조회하는 API가 별도로 제공되지 않습니다.

<a id="domain-alias-api"></a>
## 도메인 별칭 API { #domain-alias-api }

<a id="register-domain-alias"></a>
### 도메인 별칭 등록 { #register-domain-alias }

<a id="register-domain-alias-request"></a>
#### 요청

[URI]

| 메서드  | URI                                          |
| ---- | -------------------------------------------- |
| POST | /v3.0/appKeys/{appKey}/alias-domains         |


[요청 본문]

```json
{
    "domain": "cdn.example.com"
}
```

[필드]

| 이름              | 타입     | 필수 여부 | 기본값  | 유효 범위                   | 설명                                                                                     |
| --------------- | ------ | ----- | ---- | ----------------------- | -------------------------------------------------------------------------------------- |
| domain          | String | 필수    |      | FQDN 형식, 최소 4자~최대 253자 | 등록할 도메인(전체 도메인 주소 형식으로 입력, toastcdn.net 도메인은 사용 불가)                                    |

<a id="register-domain-alias-response"></a>
#### 응답

[응답 본문]

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


[필드]

| 필드                                  | 타입      | 설명                                                                 |
| ----------------------------------- | ------- | ------------------------------------------------------------------ |
| header                              | Object  | 헤더 영역                                                              |
| header.isSuccessful                 | Boolean | 성공 여부                                                              |
| header.resultCode                   | Integer | 결과 코드                                                              |
| header.resultMessage                | String  | 결과 메시지                                                             |
| domain                              | Object  | 등록된 도메인 별칭 오브젝트                                                    |
| domain.aliasDomainDomSeq            | Integer | 도메인 별칭 ID                                                          |
| domain.domain                       | String  | 등록된 도메인                                                            |
| domain.validationStatus             | String  | 검증 상태 코드([표] 도메인 별칭 검증 상태 코드 참고)                                   |
| domain.validationScope              | String  | 검증 범위                                  |
| domain.validationTxtName            | String  | DNS TXT 레코드 추가 방식의 레코드 이름                                          |
| domain.validationTxtValue           | String  | DNS TXT 레코드 추가 방식의 레코드값                                            |
| domain.validationHttpPath           | String  | HTTP 파일 인증 방식의 HTTP 페이지 URL                                        |
| domain.validationHttpContent        | String  | HTTP 파일 인증 방식의 페이지 콘텐츠 값                                           |
| domain.validationHttpRedirectFrom   | String  | HTTP 리다이렉트 인증 방식의 리다이렉트 원본 URL                                     |
| domain.validationHttpRedirectTo     | String  | HTTP 리다이렉트 인증 방식의 리다이렉트 대상 URL                                     |
| domain.validationExpireDatetime     | DateTime | 검증 토큰 만료 일시                                                        |
| domain.validationCompleteDatetime   | DateTime | 검증 완료 일시                                                            |
| domain.distributionSeq              | Integer | 연동된 CDN 서비스 ID                                                      |
| domain.distribution                 | Object  | 연동된 CDN 서비스 정보                                                      |
| domain.distribution.domain          | String  | CDN 서비스 도메인                                                         |
| domain.distribution.status          | String  | CDN 서비스 상태 코드([표] CDN 상태 코드 참고)                                     |
| domain.createdAt                    | DateTime | 생성 일시                                                              |
| domain.updatedAt                    | DateTime | 변경 일시                                                              |


<a id="list-domain-aliases"></a>
### 도메인 별칭 목록 조회 { #list-domain-aliases }

<a id="list-domain-aliases-request"></a>
#### 요청

[URI]

| 메서드 | URI                                          |
| --- | -------------------------------------------- |
| GET | /v3.0/appKeys/{appKey}/alias-domains         |


[파라미터]

| 이름     | 타입      | 필수 여부 | 유효 범위                                                                       | 설명                                       |
| ------ | ------- | ----- | --------------------------------------------------------------------------- | ---------------------------------------- |
| domain | String  | 선택    | 최대 253자                                                                     | 조회할 도메인                                  |
| status | String  | 선택    | REQUEST_ACCEPTED, VALIDATION_IN_PROGRESS, VALIDATED, TOKEN_EXPIRED | 검증 상태 코드(,로 여러 상태 입력 가능)                |
| page   | Integer | 선택    | 기본값: 1                                                                      | 페이지 번호                                   |
| limit  | Integer | 선택    | 기본값: 10, 최대: 1000                                                           | 페이지당 조회 건수                               |

[예]
```
curl -X GET "https://cdn.api.nhncloudservice.com/v3.0/appKeys/{appKey}/alias-domains?status=VALIDATED&page=1&limit=10" \
 -H "X-NHN-AUTHORIZATION: {secretKey}" \
 -H "Content-Type: application/json"
```

<a id="list-domain-aliases-response"></a>
#### 응답

[응답 본문]

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


[필드]

| 필드                                    | 타입       | 설명                                                                 |
| ------------------------------------- | -------- | ------------------------------------------------------------------ |
| header                                | Object   | 헤더 영역                                                              |
| header.isSuccessful                   | Boolean  | 성공 여부                                                              |
| header.resultCode                     | Integer  | 결과 코드                                                              |
| header.resultMessage                  | String   | 결과 메시지                                                             |
| paging                                | Object   | 페이징 영역                                                             |
| paging.page                           | Integer  | 페이지 번호                                                             |
| paging.limit                          | Integer  | 페이지당 조회 건수                                                         |
| paging.totalCount                     | Integer  | 전체 건수                                                              |
| domains                               | List     | 도메인 별칭 오브젝트 목록                                                     |
| domains[0].aliasDomainDomSeq          | Integer  | 도메인 별칭 ID                                                          |
| domains[0].domain                     | String   | 등록된 도메인                                                            |
| domains[0].validationStatus           | String   | 검증 상태 코드([표] 도메인 별칭 검증 상태 코드 참고)                                   |
| domains[0].validationTxtName          | String   | DNS TXT 레코드 추가 방식의 레코드 이름                                          |
| domains[0].validationTxtValue         | String   | DNS TXT 레코드 추가 방식의 레코드값                                            |
| domains[0].validationHttpPath         | String   | HTTP 파일 인증 방식의 HTTP 페이지 URL                                        |
| domains[0].validationHttpContent      | String   | HTTP 파일 인증 방식의 페이지 콘텐츠 값                                           |
| domains[0].validationHttpRedirectFrom | String   | HTTP 리다이렉트 인증 방식의 리다이렉트 원본 URL                                     |
| domains[0].validationHttpRedirectTo   | String   | HTTP 리다이렉트 인증 방식의 리다이렉트 대상 URL                                     |
| domains[0].validationExpireDatetime   | DateTime | 검증 토큰 만료 일시                                                        |
| domains[0].validationCompleteDatetime | DateTime | 검증 완료 일시                                                            |
| domains[0].distributionSeq            | Integer  | 연동된 CDN 서비스 ID                                                      |
| domains[0].distribution               | Object   | 연동된 CDN 서비스 정보                                                      |
| domains[0].distribution.domain        | String   | CDN 서비스 도메인                                                         |
| domains[0].distribution.status        | String   | CDN 서비스 상태 코드([표] CDN 상태 코드 참고)                                     |
| domains[0].createdAt                  | DateTime | 생성 일시                                                              |
| domains[0].updatedAt                  | DateTime | 변경 일시                                                              |


<a id="delete-domain-alias"></a>
### 도메인 별칭 삭제 { #delete-domain-alias }

<a id="delete-domain-alias-request"></a>
#### 요청

[URI]

| 메서드    | URI                                                        |
| ------ | ---------------------------------------------------------- |
| DELETE | /v3.0/appKeys/{appKey}/alias-domains/{aliasDomainDomSeq}   |


[파라미터]

| 이름                | 타입      | 필수 여부 | 유효 범위 | 설명          |
| ----------------- | ------- | ----- | ----- | ----------- |
| aliasDomainDomSeq | Integer | 필수    |       | 도메인 별칭 ID  |


[예]
```
curl -X DELETE "https://cdn.api.nhncloudservice.com/v3.0/appKeys/{appKey}/alias-domains/{aliasDomainDomSeq}" \
 -H "X-NHN-AUTHORIZATION: {secretKey}" \
 -H "Content-Type: application/json"
```

<a id="delete-domain-alias-response"></a>
#### 응답

[응답 본문]

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
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

- CDN 서비스에 연동된 도메인은 삭제할 수 없습니다. CDN 서비스에서 도메인 별칭 연동을 해제한 후 삭제하세요.


<a id="run-domain-validation"></a>
### 도메인 검증 실행 { #run-domain-validation }

<a id="run-domain-validation-request"></a>
#### 요청

[URI]

| 메서드  | URI                                                                     |
| ---- |-------------------------------------------------------------------------|
| POST | /v3.0/appKeys/{appKey}/alias-domains/{aliasDomainDomSeq}/token/validate |


[요청 본문]

```json
{
    "validationMethod": "DNS_TXT"
}
```

[필드]

| 이름               | 타입     | 필수 여부 | 기본값 | 유효 범위          | 설명                                                            |
| ---------------- | ------ | ----- | --- | -------------- | ------------------------------------------------------------- |
| validationMethod | String | 필수    |     | DNS_TXT, HTTP  | 검증 방식("DNS_TXT": DNS TXT 레코드 추가 방식, "HTTP": HTTP 파일 또는 리다이렉트 인증 방식) |


<a id="run-domain-validation-response"></a>
#### 응답

[응답 본문]

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

[필드]

| 필드                                  | 타입      | 설명                                                                 |
| ----------------------------------- | ------- | ------------------------------------------------------------------ |
| header                              | Object  | 헤더 영역                                                              |
| header.isSuccessful                 | Boolean | 성공 여부                                                              |
| header.resultCode                   | Integer | 결과 코드                                                              |
| header.resultMessage                | String  | 결과 메시지                                                             |
| domain                              | Object  | 도메인 별칭 오브젝트                                                        |
| domain.aliasDomainDomSeq            | Integer | 도메인 별칭 ID                                                          |
| domain.domain                       | String  | 등록된 도메인                                                            |
| domain.validationStatus             | String  | 검증 상태 코드([표] 도메인 별칭 검증 상태 코드 참고)                                   |
| domain.validationScope              | String  | 검증 범위                                  |
| domain.validationTxtName            | String  | DNS TXT 레코드 추가 방식의 레코드 이름                                          |
| domain.validationTxtValue           | String  | DNS TXT 레코드 추가 방식의 레코드값                                            |
| domain.validationHttpPath           | String  | HTTP 파일 인증 방식의 HTTP 페이지 URL                                        |
| domain.validationHttpContent        | String  | HTTP 파일 인증 방식의 페이지 콘텐츠 값                                           |
| domain.validationHttpRedirectFrom   | String  | HTTP 리다이렉트 인증 방식의 리다이렉트 원본 URL                                     |
| domain.validationHttpRedirectTo     | String  | HTTP 리다이렉트 인증 방식의 리다이렉트 대상 URL                                     |
| domain.validationExpireDatetime     | DateTime | 검증 토큰 만료 일시                                                        |
| domain.validationCompleteDatetime   | DateTime | 검증 완료 일시                                                            |
| domain.distributionSeq              | Integer | 연동된 CDN 서비스 ID                                                      |
| domain.distribution                 | Object  | 연동된 CDN 서비스 정보                                                      |
| domain.distribution.domain          | String  | CDN 서비스 도메인                                                         |
| domain.distribution.status          | String  | CDN 서비스 상태 코드([표] CDN 상태 코드 참고)                                     |
| domain.createdAt                    | DateTime | 생성 일시                                                              |
| domain.updatedAt                    | DateTime | 변경 일시                                                              |

- 도메인 검증을 실행하기 전에 DNS TXT 레코드 추가 또는 HTTP 파일/리다이렉트 설정을 먼저 완료해야 합니다.
- 검증 토큰이 만료된 경우 검증 실행이 불가합니다. 토큰 재발급 API로 새 토큰을 발급받은 후 다시 검증을 진행하세요.


<a id="refresh-domain-validation-status"></a>
### 도메인 검증 상태 새로고침 { #refresh-domain-validation-status }

<a id="refresh-domain-validation-status-request"></a>
#### 요청

[URI]

| 메서드  | URI                                                                    |
| ---- |------------------------------------------------------------------------|
| POST | /v3.0/appKeys/{appKey}/alias-domains/{aliasDomainDomSeq}/token/refresh |


[예]
```
curl -X POST "https://cdn.api.nhncloudservice.com/v3.0/appKeys/{appKey}/alias-domains/{aliasDomainDomSeq}/token/refresh" \
 -H "X-NHN-AUTHORIZATION: {secretKey}" \
 -H "Content-Type: application/json"
```

<a id="refresh-domain-validation-status-response"></a>
#### 응답

[응답 본문]

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

[필드]

| 필드                                  | 타입      | 설명                                                                 |
| ----------------------------------- | ------- | ------------------------------------------------------------------ |
| header                              | Object  | 헤더 영역                                                              |
| header.isSuccessful                 | Boolean | 성공 여부                                                              |
| header.resultCode                   | Integer | 결과 코드                                                              |
| header.resultMessage                | String  | 결과 메시지                                                             |
| domain                              | Object  | 도메인 별칭 오브젝트                                                        |
| domain.aliasDomainDomSeq            | Integer | 도메인 별칭 ID                                                          |
| domain.domain                       | String  | 등록된 도메인                                                            |
| domain.validationStatus             | String  | 검증 상태 코드([표] 도메인 별칭 검증 상태 코드 참고)                                   |
| domain.validationScope              | String  | 검증 범위                                  |
| domain.validationTxtName            | String  | DNS TXT 레코드 추가 방식의 레코드 이름                                          |
| domain.validationTxtValue           | String  | DNS TXT 레코드 추가 방식의 레코드값                                            |
| domain.validationHttpPath           | String  | HTTP 파일 인증 방식의 HTTP 페이지 URL                                        |
| domain.validationHttpContent        | String  | HTTP 파일 인증 방식의 페이지 콘텐츠 값                                           |
| domain.validationHttpRedirectFrom   | String  | HTTP 리다이렉트 인증 방식의 리다이렉트 원본 URL                                     |
| domain.validationHttpRedirectTo     | String  | HTTP 리다이렉트 인증 방식의 리다이렉트 대상 URL                                     |
| domain.validationExpireDatetime     | DateTime | 검증 토큰 만료 일시                                                        |
| domain.validationCompleteDatetime   | DateTime | 검증 완료 일시                                                            |
| domain.distributionSeq              | Integer | 연동된 CDN 서비스 ID                                                      |
| domain.distribution                 | Object  | 연동된 CDN 서비스 정보                                                      |
| domain.distribution.domain          | String  | CDN 서비스 도메인                                                         |
| domain.distribution.status          | String  | CDN 서비스 상태 코드([표] CDN 상태 코드 참고)                                     |
| domain.createdAt                    | DateTime | 생성 일시                                                              |
| domain.updatedAt                    | DateTime | 변경 일시                                                              |


<a id="reissue-validation-token"></a>
### 검증 토큰 재발급 { #reissue-validation-token }

<a id="reissue-validation-token-request"></a>
#### 요청

[URI]

| 메서드  | URI                                                                    |
| ---- |------------------------------------------------------------------------|
| POST | /v3.0/appKeys/{appKey}/alias-domains/{aliasDomainDomSeq}/token/reissue |


[예]
```
curl -X POST "https://cdn.api.nhncloudservice.com/v3.0/appKeys/{appKey}/alias-domains/{aliasDomainDomSeq}/token/reissue" \
 -H "X-NHN-AUTHORIZATION: {secretKey}" \
 -H "Content-Type: application/json"
```

<a id="reissue-validation-token-response"></a>
#### 응답

[응답 본문]

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

[필드]

| 필드                                  | 타입      | 설명                                                                 |
| ----------------------------------- | ------- | ------------------------------------------------------------------ |
| header                              | Object  | 헤더 영역                                                              |
| header.isSuccessful                 | Boolean | 성공 여부                                                              |
| header.resultCode                   | Integer | 결과 코드                                                              |
| header.resultMessage                | String  | 결과 메시지                                                             |
| domain                              | Object  | 도메인 별칭 오브젝트                                                        |
| domain.aliasDomainDomSeq            | Integer | 도메인 별칭 ID                                                          |
| domain.domain                       | String  | 등록된 도메인                                                            |
| domain.validationStatus             | String  | 검증 상태 코드([표] 도메인 별칭 검증 상태 코드 참고)                                   |
| domain.validationScope              | String  | 검증 범위                                  |
| domain.validationTxtName            | String  | DNS TXT 레코드 추가 방식의 레코드 이름                                          |
| domain.validationTxtValue           | String  | DNS TXT 레코드 추가 방식의 레코드값                                            |
| domain.validationHttpPath           | String  | HTTP 파일 인증 방식의 HTTP 페이지 URL                                        |
| domain.validationHttpContent        | String  | HTTP 파일 인증 방식의 페이지 콘텐츠 값                                           |
| domain.validationHttpRedirectFrom   | String  | HTTP 리다이렉트 인증 방식의 리다이렉트 원본 URL                                     |
| domain.validationHttpRedirectTo     | String  | HTTP 리다이렉트 인증 방식의 리다이렉트 대상 URL                                     |
| domain.validationExpireDatetime     | DateTime | 검증 토큰 만료 일시                                                        |
| domain.validationCompleteDatetime   | DateTime | 검증 완료 일시                                                            |
| domain.distributionSeq              | Integer | 연동된 CDN 서비스 ID                                                      |
| domain.distribution                 | Object  | 연동된 CDN 서비스 정보                                                      |
| domain.distribution.domain          | String  | CDN 서비스 도메인                                                         |
| domain.distribution.status          | String  | CDN 서비스 상태 코드([표] CDN 상태 코드 참고)                                     |
| domain.createdAt                    | DateTime | 생성 일시                                                              |
| domain.updatedAt                    | DateTime | 변경 일시                                                              |

- 토큰이 재발급되면 이전 검증 정보는 초기화되며, 새 토큰 정보로 다시 검증을 진행해야 합니다.
- 검증 토큰이 만료(`TOKEN_EXPIRED`)된 경우 이 API를 호출하여 새 토큰을 발급받을 수 있습니다.

<a id="reissue-validation-token-domain-alias-validation-status-codes"></a>
#### 도메인 별칭 검증 상태 코드

다음은 도메인 별칭의 검증 상태를 나타내는 상태 코드로, 도메인 별칭 조회 시 검증 상태를 확인할 수 있습니다.

| 값                      | 설명                               |
| ---------------------- | -------------------------------- |
| REQUEST_ACCEPTED       | 도메인이 등록되어 검증 대기 중                |
| VALIDATION_IN_PROGRESS | 도메인 소유권 검증이 진행 중                 |
| VALIDATED              | 도메인 소유권 검증 완료, CDN 서비스 연동 가능     |
| TOKEN_EXPIRED          | 검증 토큰 만료, 토큰 재발급 후 다시 검증 필요     |


<a id="certificate-api"></a>
## 인증서 API { #certificate-api }
<a id="issue-new-certificate"></a>
### 신규 인증서 발급 { #issue-new-certificate }
<a id="issue-new-certificate-request"></a>
#### 요청

[URI]

| 메서드  | URI                           |
| ---- | ----------------------------- |
| POST | /v3.0/appKeys/{appKey}/certificates|


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
| certificateDomain    | String | 필수      |        | 최대 255자            | 신규 인증서를 발급할 도메인(전체 도메인 주소 형식으로 입력)|
| callbackHttpMethod  | String | 선택      |        | GET/POST/PUT        | 인증서 생성 처리 결과를 통보받을 콜백의 HTTP 메서드 |
| callbackUrl         | String | 선택      |        | 최대 1024자           | 인증서 생성 처리 결과를 통보받을 콜백 URL       |

* 인증서 발급에 대한 상세한 내용은 [콘솔 사용 가이드 > 인증서 관리 > 신규 인증서 발급](./console-guide/#issue-new-certificates)을 참고하세요.

<a id="issue-new-certificate-response"></a>
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

<a id="list-certificates"></a>
### 인증서 목록 조회 { #list-certificates }
<a id="list-certificates-request"></a>
#### 요청

[URI]

| 메서드  | URI                           |
| ---- | ----------------------------- |
| GET | /v3.0/appKeys/{appKey}/certificates|


<a id="list-certificates-response"></a>
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

<a id="delete-certificate"></a>
### 인증서 삭제 { #delete-certificate }
<a id="delete-certificate-request"></a>
#### 요청

[URI]

| 메서드  | URI                           |
| ---- | ----------------------------- |
| DELETE | /v3.0/appKeys/{appKey}/certificates|


[파라미터]

| 이름   | 타입   | 필수 여부 | 유효 범위     | 설명                         |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| dnsIdList | String | 필수      |     | 삭제할 인증서 ID(sanDnsId) 목록(,로 연결된 인증서 ID 목록)   |

[예]
```
curl -X DELETE "https://cdn.api.nhncloudservice.com/v3.0/appKeys/{appKey}/certificates?dnsIdList={dnsIdList}" \
 -H "X-NHN-AUTHORIZATION: {secretKey}" \
 -H "Content-Type: application/json"
```

<a id="delete-certificate-response"></a>
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


<a id="callback-response"></a>
## 콜백 응답 { #callback-response }
<a id="cdn-service"></a>
### CDN 서비스 { #cdn-service }
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
| distribution.domain                | String  | 도메인(서비스 이름)                                     |
| distribution.domainAlias           | List  | 도메인 별칭 목록(개인 또는 회사가 소유한 도메인 사용)                                 |
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
| distribution.useOriginHttpProtocolDowngrade | Boolean | 원본 서버가 HTTP 응답만 가능한 경우, CDN 서버에서 원본 서버로 요청 시 HTTPS 요청을 HTTP 요청으로 다운그레이드하기 위한 설정 사용 여부 |
| distribution.forwardHostHeader     | String  | CDN 서버가 원본 서버로 콘텐츠 요청 시 전달할 호스트 헤더 설정("ORIGIN_HOSTNAME": 원본 서버의 호스트 이름으로 설정, "REQUEST_HOST_HEADER": 클라이언트 요청의 호스트 헤더로 설정) |
| distribution.rootPathAccessControl  | Object  | CDN 서비스의 루트 경로에 대한 접근 제어 설정 | 
| distribution.rootPathAccessControl.enable | Boolean | 루트 경로에 대한 접근 제어 사용(true)/미사용(false) 여부          |
| distribution.rootPathAccessControl.controlType  | String  | enable이 true일 경우 필수 입력. 루트 경로에 대한 접근 제어 방식("DENY": 접근 거부, "REDIRECT": 지정한 경로로 리다이렉트) | 
| distribution.rootPathAccessControl.redirectPath | String | controlType이 "REDIRECT"일 경우 필수 입력. 루트 경로에 대한 요청을 리다이렉트할 경로(/를 포함한 경로로 입력합니다.)        |
| distribution.rootPathAccessControl.redirectStatusCode | Integer | controlType이 "REDIRECT"일 경우 필수 입력. 리다이렉트 시 전달되는 HTTP 응답 코드         |
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

<a id="certificate"></a>
### 인증서 { #certificate }
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
