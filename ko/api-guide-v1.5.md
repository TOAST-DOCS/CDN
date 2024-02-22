## Content Delivery > CDN > API v1.5 가이드

NHN Cloud CDN에서 제공하는 Public API v1.5를 설명합니다.

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

모든 API는 appKey를 path 파라미터로 지정해야 합니다.
* 예) /v1.5/appKeys/**{appKey}**/distributions

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


## 서비스 API

### 서비스 생성

#### 요청


[URI]

| 메서드  | URI                                  |
| ---- | ------------------------------------ |
| POST | /v1.5/appKeys/{appKey}/distributions |


[요청 본문]

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

[필드]

| 이름                                   | 타입    | 필수 여부 | 기본값 | 유효 범위                   | 설명                                                         |
| -------------------------------------- | ------- | --------- | ------ | --------------------------- | ------------------------------------------------------------ |
| distributions                          | List    | 필수      |        |                             | 생성할 CDN의 오브젝트 목록                                   |
| distributions[0].region                | String  | 필수      |        | GLOBAL                | 서비스 지역("GLOBAL": 글로벌)           |
| distributions[0].useOriginHttpProtocolDowngrade | Boolean  | 필수     | false       | true/false         | 원본 서버가 HTTP 응답만 가능한 경우, CDN 서버에서 원본 서버로 요청 시 HTTPS 요청을 HTTP 요청으로 다운그레이드하기 위한 설정 사용 여부 |
| distributions[0].forwardHostHeader     | String  | 필수      |        | ORIGIN_HOSTNAME<br/>REQUEST_HOST_HEADER   | CDN 서버가 원본 서버로 콘텐츠 요청 시 전달할 호스트 헤더 설정("ORIGIN_HOSTNAME": 원본 서버의 호스트 이름으로 설정, "REQUEST_HOST_HEADER": 클라이언트 요청의 호스트 헤더로 설정)|
| distributions[0].useOrigin             | String  | 필수      |        | Y/N                         | 캐시 만료 설정("Y": 원본 설정 사용, "N":사용자 설정 사용)   |
| distributions[0].referrerType          | String  | 필수      |        | BLACKLIST/WHITELIST         | 리퍼러 접근 관리("BLACKLIST": 블랙리스트, "WHITELIST": 화이트리스트) |
| distributions[0].description           | String  | 선택      |        | 최대 255자                  | 설명                                                         |
| distributions[0].domainAlias           | String  | 선택      |        | 최대 255자                  | 도메인 별칭(개인 혹은 회사가 소유한 도메인 사용, 여러 개 입력 시 \n 토큰으로 구분해 입력해 주세요.) |
| distributions[0].maxAge                | Integer | 필수      | 0      | 0~2,147,483,647             | 캐시 만료 시간(초), 기본값 0은 604,800초입니다.             |
| distributions[0].referrers             | String  | 선택      |        | '\n' 토큰 포함, 최대 1024자 | 리퍼러(여러 개 입력 시 \n 토큰으로 구분해 입력해 주세요.)    |
| distributions[0].isAllowWhenEmptyReferrer | Boolean | 선택      | true      | true/false             | 리퍼러 헤더가 없는 경우 콘텐츠 접근 허용(true)/거부(false) 여부             |
| distributions[0].origins               | List    | 필수      |        |                             | 원본 서버 오브젝트 목록                                      |
| distributions[0].origins[0].origin     | String  | 필수      |        | 최대 255자                  | 원본 서버(도메인 또는 IP)                                     |
| distributions[0].origins[0].port       | Integer  | 선택      |        |[콘솔 사용 가이드 > 원본 서버](./console-guide/#_2)의 '[표 2] 사용 가능한 원본 서버 포트 번호' 참고| 원본 서버 HTTP 프로토콜 포트<br>(origins[0].port 설정 시 origins[0].httpPort와 origins[0].httpsPort는 입력하지 않습니다.)|
| distributions[0].origins[0].httpPort   | Integer  | 선택      |        |[콘솔 사용 가이드 > 원본 서버](./console-guide/#_2)의 '[표 2] 사용 가능한 원본 서버 포트 번호' 참고| 원본 서버 HTTP 프로토콜 포트<br>(origins[0].port 미설정 시 origins[0].httpPort와 origins[0].httpsPort 중 하나는 필수 입력해야 합니다.)  |
| distributions[0].origins[0].httpsPort  | Integer  | 선택      |        |[콘솔 사용 가이드 > 원본 서버](./console-guide/#_2)의 '[표 2] 사용 가능한 원본 서버 포트 번호' 참고 | 원본 서버 HTTPS 프로토콜 포트<br>(origins[0].port 미설정 시 origins[0].httpPort와 origins[0].httpsPort 중 하나는 필수 입력해야 합니다.) |
| distributions[0].origins[0].originPath | String  | 선택      |        | 최대 8192자                 | 원본 서버 하위 경로(/를 포함한 경로로 입력해 주세요.)        |
| distributions[0].callback              | Object  | 선택      |        |                             | CDN 생성 처리 결과를 통보받을 콜백 URL(콜백 설정은 선택 입력입니다.) |
| distributions[0].callback.httpMethod   | String  | 필수      |        | GET/POST/PUT                | 콜백의 HTTP 메서드                                           |
| distributions[0].callback.url          | String  | 필수      |        | 최대 1024자                 | 콜백 URL            |

- forwardHostHeader의 기본값은 domainAlias를 설정한 경우 REQUEST_HOST_HEADER이고, 미설정하면 ORIGIN_HOSTNAME입니다.


#### 응답


[응답 본문]

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
                    "origin" :  "static.resource.com",
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


[필드]

| 필드                                   | 타입    | 설명                                                         |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | 헤더 영역                                                    |
| header.isSuccessful                    | Boolean | 성공 여부                                                    |
| header.resultCode                      | Integer | 결과 코드                                                    |
| header.resultMessage                   | String  | 결과 메시지                                                  |
| distributions                          | List    | 생성된 CDN 오브젝트 목록                                   |
| distributions[0].domain                | String  | 생성된 도메인(서비스) 이름                                   |
| distributions[0].domainAlias           | String  | 도메인 별칭 목록(개인 혹은 회사가 소유한 도메인 사용)              |
| distributions[0].region                | String  | 서비스 지역("GLOBAL": 글로벌)            |
| distributions[0].description           | String  | 설명                                                         |
| distributions[0].status                | String  | CDN 상태 코드([표] CDN 상태 코드 참고)                                 |
| distributions[0].useOrigin             | String  | 원본 서버 설정 사용 여부("Y": 원본 서버 설정 사용, "N": 사용자 설정) |
| distributions[0].maxAge                | String  | 캐시 만료 시간(초)                                           |
| distributions[0].referrerType          | String  | 리퍼러 접근 관리("BLACKLIST": 블랙리스트, "WHITELIST": 화이트리스트) |
| distributions[0].referrers             | String  | 리퍼러 목록                                                  |
| distributions[0].origins               | List    | 원본 서버 오브젝트 목록                                      |
| distributions[0].origins[0].origin     | String  | 원본 서버(도메인 또는 IP)                                      |
| distributions[0].origins[0].originPath | String  | 원본 서버 하위 경로                                          |
| distributions[0].origins[0].port       | Integer | 원본 서버 포트                                               |
| distributions[0].callback              | Object  | 서비스 생성 처리 결과를 통보받을 콜백                        |
| distributions[0].callback.httpMethod   | String  | 콜백의 HTTP Method                                           |
| distributions[0].callback.url          | String  | 콜백 URL                                                     |


### 서비스 조회

#### 요청


[URI]

| 메서드  | URI                                  |
| ---- | ------------------------------------ |
| GET  | /v1.5/appKeys/{appKey}/distributions |


[파라미터]

| 이름   | 타입   | 필수 여부 | 유효 범위     | 설명                         |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| domain | String | 선택      | 최대 255자    | 조회할 도메인(서비스 이름)   |
| status | String | 선택      | CDN 상태 코드 | CDN 상태 코드([표] CDN 상태 코드 참고) |

[예]
```
curl -X GET "https://kr1-cdn.api.nhncloudservice.com/v1.5/appKeys/{appKey}/distributions?domain={domain}" \
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


[필드]

| 필드                                   | 타입    | 설명                                                         |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | 헤더 영역                                                    |
| header.isSuccessful                    | Boolean | 성공 여부                                                    |
| header.resultCode                      | Integer | 결과 코드                                                    |
| header.resultMessage                   | String  | 결과 메시지                                                  |
| distributions                          | List    | 생성된 CDN 오브젝트 목록                                     |
| distributions[0].domain                | String  | 도메인 이름(서비스 이름)                                     |
| distributions[0].domainAlias           | String  | 도메인 별칭 목록(개인 혹은 회사가 소유한 도메인 사용)              |
| distributions[0].region                | String  | 서비스 지역("GLOBAL": 글로벌)             |
| distributions[0].description           | String  | 설명                                                         |
| distributions[0].status                | String  | CDN 상태 코드([표] CDN 상태 코드 참고)                                 |
| distributions[0].useOrigin             | String  | 원본 서버 설정 사용 여부("Y": 원본 서버 설정 사용, "N": 사용자 설정) |
| distributions[0].maxAge                | String  | 캐시 만료 시간(초)                                           |
| distributions[0].referrerType          | String  | 리퍼러 접근 관리("BLACKLIST": 블랙리스트, "WHITELIST": 화이트리스트) |
| distributions[0].referrers             | String  | 리퍼러 목록                                                  |
| distributions[0].origins               | List    | 원본 서버 오브젝트 목록                                      |
| distributions[0].origins[0].origin     | String  | 원본 서버(도메인 또는 IP)                                      |
| distributions[0].origins[0].originPath | String  | 원본 서버 하위 경로                                          |
| distributions[0].origins[0].port       | Integer | 원본 서버 포트                                               |
| distributions[0].callback              | Object  | 서비스 배포 처리 결과를 통보받을 콜백                        |
| distributions[0].callback.httpMethod   | String  | 콜백의 HTTP Method                                           |
| distributions[0].callback.url          | String  | 콜백 URL                                                     |


### 서비스 수정

#### 요청


[URI]

| 메서드  | URI                                  |
| ---- | ------------------------------------ |
| PUT  | /v1.5/appKeys/{appKey}/distributions |


[요청 본문]

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


[필드]

| 이름                  | 타입    | 필수 여부 | 기본값 | 유효 범위                                                    | 설명                                                         |
| --------------------- | ------- | --------- | ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| domain                | String  | 필수      |        | 최대 255자                                                   | 수정할 도메인(서비스 이름)                                   |
| useOriginHttpProtocolDowngrade | Boolean  | 필수     | false       | true/false         | 원본 서버가 HTTP 응답만 가능한 경우, CDN 서버에서 원본 서버로 요청 시 HTTPS 요청을 HTTP 요청으로 다운그레이드하기 위한 설정 사용 여부 |
| forwardHostHeader     | String  | 필수      |        | ORIGIN_HOSTNAME<br/>REQUEST_HOST_HEADER   | CDN 서버가 원본 서버로 콘텐츠 요청 시 전달할 호스트 헤더 설정("ORIGIN_HOSTNAME": 원본 서버의 호스트 이름으로 설정, "REQUEST_HOST_HEADER": 클라이언트 요청의 호스트 헤더로 설정)|
| useOrigin             | String  | 필수      |        | Y/N                                                          | 캐시 만료 설정(Y: 원본 설정 사용, "N":사용자 설정 사용)      |
| referrerType          | String  | 필수      |        | BLACKLIST/WHITELIST                                          | 리퍼러 접근 관리("BLACKLIST": 블랙리스트, "WHITELIST": 화이트리스트) |
| description           | String  | 선택      |        | 최대 255자                                                   | 설명                                                         |
| domainAlias           | String  | 선택      |        | 최대 255자                                                   | 도메인 별칭(개인 혹은 회사가 소유한 도메인 사용, 여러 개 입력 시 \n 토큰으로 구분해 입력해 주세요.) |
| maxAge                | Integer | 선택      | 0      | 0~2,147,483,647                                            | 캐시 만료 시간(초), 기본값 0은 604,800초입니다.              |
| referrers             | String  | 선택      |        | '\n' 토큰 포함, 최대 1024자                                  | 리퍼러(여러 개 입력 시 \\n 토큰으로 구분해 입력해 주세요. )  |
| isAllowWhenEmptyReferrer | Boolean | 선택      | true      | true/false             | 리퍼러 헤더가 없는 경우 콘텐츠 접근 허용(true)/거부(false) 여부             |
| origins               | List    | 필수      |        |                                                              | 원본 서버                                                    |
| origins[0].origin     | String  | 필수      |        | 최대 255자                                                   | 원본 서버(도메인 또는 IP)                                      |
| origins[0].port       | Integer  | 선택      |        |[콘솔 사용 가이드 > 원본 서버](./console-guide/#_2)의 '[표 2] 사용 가능한 원본 서버 포트 번호' 참고| 원본 서버 HTTP 프로토콜 포트<br>(origins[0].port 설정 시 origins[0].httpPort와 origins[0].httpsPort는 입력하지 않습니다.)|
| origins[0].httpPort   | Integer  | 선택      |        |[콘솔 사용 가이드 > 원본 서버](./console-guide/#_2)의 '[표 2] 사용 가능한 원본 서버 포트 번호' 참고| 원본 서버 HTTP 프로토콜 포트<br>(origins[0].port 미설정 시 origins[0].httpPort와 origins[0].httpsPort 중 하나는 필수 입력해야 합니다.)  |
| origins[0].httpsPort  | Integer  | 선택      |        |[콘솔 사용 가이드 > 원본 서버](./console-guide/#_2)의 '[표 2] 사용 가능한 원본 서버 포트 번호' 참고 | 원본 서버 HTTPS 프로토콜 포트<br>(origins[0].port 미설정 시 origins[0].httpPort와 origins[0].httpsPort 중 하나는 필수 입력해야 합니다.) |
| origins[0].originPath | String  | 선택      |        | 최대 8192자                                                  | 원본 서버 하위 경로                                          |
| callback              | Object  | 선택      |        | CDN 서비스 배포 결과를 통보받을 콜백 URL(콜백 설정은 선택 입력입니다.) |                                                              |
| callback.httpMethod   | String  | 필수      |        | GET/POST/PUT                                                 | 콜백의 HTTP Method                                           |
| callback.url          | String  | 필수      |        | 최대 1024자                                                  | 콜백 URL                   |

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


### 서비스 부분 수정

서비스 일부 설정을 변경할 경우 부분 수정 API를 이용할 수 있습니다.

#### 요청


[URI]

| 메서드   | URI                                  |
| ----- | ------------------------------------ |
| PATCH | /v1.5/appKeys/{appKey}/distributions |


[요청 본문]

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


[필드]

| 이름                  | 타입    | 필수 여부 | 기본값 | 유효 범위                                                    | 설명                                                         |
| --------------------- | ------- | --------- | ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| domain                | String  | 필수      |        | 최대 255자                                                   | 수정할 도메인(서비스 이름)                                   |
| useOriginHttpProtocolDowngrade | Boolean  | 선택     |        | true/false         | 원본 서버가 HTTP 응답만 가능한 경우, CDN 서버에서 원본 서버로 요청 시 HTTPS 요청을 HTTP 요청으로 다운그레이드하기 위한 설정 사용 여부 |
| forwardHostHeader     | String  | 선택      |        | ORIGIN_HOSTNAME<br/>REQUEST_HOST_HEADER   | CDN 서버가 원본 서버로 콘텐츠 요청 시 전달 할 호스트 헤더 설정("ORIGIN_HOSTNAME": 원본 서버의 호스트 이름으로 설정, "REQUEST_HOST_HEADER": 클라이언트 요청의 호스트 헤더로 설정 |
| useOrigin             | String  | 선택      |        | Y/N                                                          | 캐시 만료 설정(Y: 원본 설정 사용, N:사용자 설정 사용)        |
| referrerType          | String  | 선택      |        | BLACKLIST / WHITELIST                                        | 리퍼러 접근 관리("BLACKLIST": 블랙리스트, "WHITELIST": 화이트리스트) |
| description           | String  | 선택      |        | 최대 255자                                                   | 설명                                                         |
| domainAlias           | String  | 선택      |        | 최대 255자                                                   | 도메인 별칭(개인 혹은 회사가 소유한 도메인 사용, 여러 개 입력 시 \n 토큰으로 분리하여 입력해 주세요.) |
| maxAge                | Integer | 선택      | 0      | 0~2,147,483,647                                              | 캐시 만료 시간(초), 기본값 0은 604,800초 입니다.           |
| referrers             | String  | 선택      |        | '\n' 토큰 포함, 최대 1024자                                  | 리퍼러(여러 개 입력 시 \\n 토큰으로 분리하여 입력해주세요. )  |
| isAllowWhenEmptyReferrer | Boolean | 선택      | true      | true/false             | 리퍼러 헤더가 없는 경우 콘텐츠 접근 허용(true)/거부(false) 여부             |
| origins               | List    | 선택      |        |                                                              | 원본 서버                                                    |
| origins[0].origin     | String  | 선택      |        | 최대 255자                                                   | 원본 서버(도메인 또는 IP)                                     |
| origins[0].port       | Integer | 선택      |        |[콘솔 사용 가이드 > 원본 서버](./console-guide/#_2)의 '[표 2] 사용 가능한 원본 서버 포트 번호' 참고| 원본 서버 HTTP 프로토콜 포트<br>(origins[0].port 설정 시 origins[0].httpPort와 origins[0].httpsPort는 입력하지 않습니다.)|
| origins[0].httpPort   | Integer  | 선택      |        |[콘솔 사용 가이드 > 원본 서버](./console-guide/#_2)의 '[표 2] 사용 가능한 원본 서버 포트 번호' 참고| 원본 서버 HTTP 프로토콜 포트<br>(origins[0].port 미설정 시 origins[0].httpPort와 origins[0].httpsPort 중 하나는 필수 입력해야 합니다.)  |
| origins[0].httpsPort  | Integer  | 선택      |        |[콘솔 사용 가이드 > 원본 서버](./console-guide/#_2)의 '[표 2] 사용 가능한 원본 서버 포트 번호' 참고 | 원본 서버 HTTPS 프로토콜 포트<br>(origins[0].port 미설정 시 origins[0].httpPort와 origins[0].httpsPort 중 하나는 필수 입력해야 합니다.) |
| origins[0].originPath | String  | 선택      |        | 최대 8192자                                                  | 원본 서버 하위 경로                                          |
| callback              | Object  | 선택      |        | CDN 서비스 배포 결과를 통보받을 콜백 URL(콜백 설정은 선택 입력입니다.) |                                                              |
| callback.httpMethod   | String  | 선택      |        | GET/POST/PUT                                                 | 콜백의 HTTP Method                                           |
| callback.url          | String  | 선택      |        | 최대 1024자                                                  | 콜백 URL                                                     |

- origins 필드를 설정할 때 origin, originPath 필드는 필수 입력값이며, port 필드나 httpPort, httpsPort 필드 중 하나는 필수로 입력해야 합니다.
- callback 필드를 설정할 때 httpMethod, url 필드는 필수 입력값입니다.

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
| DELETE | /v1.5/appKeys/{appKey}/distributions |


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

## 캐시 재배포 API

### 캐시 재배포(Purge)

#### 요청

[URI]

| 메서드  | URI                           |
| ---- | ----------------------------- |
| POST | /v1.5/appKeys/{appKey}/purges |


[요청 본문]

```json
{
	"domain": "sample.toastcdn.net",
	"purgeType": "ITEM",
	"purgeList":"/img_01.png"
}
```


[필드]

| 이름      | 타입   | 필수 여부 | 기본값 | 유효 범위             | 설명                                                         |
| --------- | ------ | --------- | ------ | --------------------- | ------------------------------------------------------------ |
| domain    | String | 필수      |        | 최대 255자            | 재배포할 도메인(서비스) 이름                                 |
| purgeType | List   | 필수      |        | ITEM / ALL | 재배포 타입("ITEM", "ALL")                       |
| purgeList | String | 선택      |        |                       | 재배포 대상 항목 목록(여러 개를 입력할 때는 \\n 토큰으로 구분해 입력해 주세요, purgeType이 ALL이면 입력하지 않아도 됩니다.) |


#### 응답

[응답 본문]

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


[필드]

| 필드                   | 타입      | 설명        |
| -------------------- | ------- | --------- |
| header               | Object  | 헤더 영역     |
| header.isSuccessful  | Boolean | 성공 여부     |
| header.resultCode    | Integer | 결과 코드     |
| header.resultMessage | String  | 결과 메시지    |
| purgeSeq             | Integer | 재배포 요청 번호 |

- CDN 서비스를 신규로 생성한 후 약 1시간 이내에는 캐시 재배포 요청이 실패할 수 있습니다. 이후에도 실패가 지속되는 경우 고객센터로 문의해주시기 바랍니다.
- 퍼지 API은 사용량 제한 정책이 있습니다. 자세한 내용은 [콘솔 사용 가이드 > CDN 캐시 재배포](./console-guide/#cdn-purge)의 '캐시 재배포 사용량 제한' 내용을 확인해주세요.
- ITEM, WILDCARD 타입은 요청 당 퍼지 경로 수가 제한 되어 있습니다. 초과하여 요청한 경우 요청 당 퍼지 경로 개수 만큼씩 나누어 퍼지 요청이 진행됩니다. 이 경우 처음 퍼지 요청의 재배포 요청 번호만 응답으로 전달됩니다.   

### 캐시 재배포(Purge) 조회

#### 요청


[URI]

| 메서드  | URI                           |
| ---- | ----------------------------- |
| GET  | /v1.5/appKeys/{appKey}/purges |


[파라미터]

| 이름           | 타입      | 필수 여부 | 기본값  | 유효 범위                                    | 설명              |
| ------------ | ------- | ----- | ---- | ---------------------------------------- | --------------- |
| domain       | String  | 필수    |      | 최대 255자                                  | 도메인(서비스) 이름     |
| page         | Integer | 선택    |      |                                          | 페이지 번호          |
| itemsPerPage | Integer | 선택    |      |                                          | 페이지당 재배포된 항목 개수 |
| startTime    | String  | 선택    |      | "yyyy-MM-ddTHH:mm:ss.SSSZ" 예)"2018-02-22T09:00:00.000Z"(UTC) | 기간 검색           |
| endTime      | String  | 선택    |      | "yyyy-MM-ddTHH:mm:ss.SSSZ" 예)"2018-02-22T09:00:00.000Z"(UTC) | 기간 검색           |


```
curl -X GET "https://kr1-cdn.api.nhncloudservice.com/v1.5/appKeys/{appKey}/purges?domain={domain}" \
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


[필드]

| 필드                      | 타입      | 설명                                |
| ----------------------- | ------- | --------------------------------- |
| header                  | Object  | 헤더 영역                             |
| header.isSuccessful     | Boolean | 성공 여부                             |
| header.resultCode       | Integer | 결과 코드                             |
| header.resultMessage    | String  | 결과 메시지                            |
| totalItems              | Integer | 재배포한 총 개수                         |
| purges                  | List    | 재배포 항목 목록                        |
| purges[0].seq           | Integer | 재배포 요청 번호                         |
| purges[0].progress      | Integer | 재배포 진행률                           |
| purges[0].purgeTime     | Long    | 재배포 요청 시간                         |
| purges[0].lastCheckTime | Long    | 재배포 수행 마지막 확인 시간                  |
| purges[0].type          | String  | 재배포 타입("ITEM", "WILDCARD", "ALL") |
| purges[0].path          | String  | 재배포 요청 항목                         |

## 콜백 응답
CDN 서비스에 콜백 기능이 설정되어있을 경우, 생성/수정/일시정지/재개/삭제 의 변경 작업 완료 시 콜백 URL에 아래와 같은 응답값을 전달합니다.

[응답 본문]
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

[필드]

| 필드                                   | 타입    | 설명                                                         |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | 헤더 영역                                                    |
| header.isSuccessful                    | Boolean | 성공 여부                                                    |
| header.resultCode                      | Integer | 결과 코드                                                    |
| header.resultMessage                   | String  | 결과 메시지                                                  |
| distribution                          | Object    | 변경 작업이 완료된 CDN 오브젝트                                     |
| distribution.appKey                   | String    | 앱 키                                  |
| distribution.domain                | String  | 도메인 이름(서비스 이름)                                     |
| distribution.domainAlias           | String  | 소유 도메인                                                  |
| distribution.region                | String  | 서비스 지역("GLOBAL": 글로벌)             |
| distribution.description           | String  | 설명                                                         |
| distribution.status                | String  | CDN 상태 코드([표] CDN 상태 코드 참고)                                 |
| distribution.createTime            | String  | 생성 일시                                                    |
| distribution.deleteTime            | String  | 삭제 일시                                                    |
| distribution.useOrigin             | String  | 원본 서버 설정 사용 여부("Y": 원본 서버 설정 사용, "N": 사용자 설정) |
| distribution.maxAge                | String  | 캐시 만료 시간(초)                                           |
| distribution.referrerType          | String  | 리퍼러 접근 관리("BLACKLIST": 블랙리스트, "WHITELIST": 화이트리스트) |
| distribution.referrers             | String  | 리퍼러 목록                                                  |
| distribution.origins               | List    | 원본 서버 오브젝트 목록                                      |
| distribution.origins[0].origin     | String  | 원본 서버(도메인 또는 IP)                                      |
| distribution.origins[0].originPath | String  | 원본 서버 하위 경로                                          |
| distribution.origins[0].port       | Integer | 원본 서버 포트                                               |
| distribution.callback              | Object  | 서비스 배포 처리 결과를 통보받을 콜백                        |
| distribution.callback.httpMethod   | String  | 콜백의 HTTP 메서드                                           |
| distribution.callback.url          | String  | 콜백 URL                                                     |
