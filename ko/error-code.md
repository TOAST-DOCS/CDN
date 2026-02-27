## Content Delivery > CDN > 오류 코드

| errorCode | errorMessage | 설명                                                                                                       |
|-----------| --- |----------------------------------------------------------------------------------------------------------|
| 0         | SUCCESS | 처리 성공                                                                                                    |
| -1        | FAIL | 처리 실패                                                                                                    |
| -2        | ASSERT | 처리 실패                                                                                                    |
| 10000     | Domain does not exist. | 존재하지 않는 서비스입니다.                                                                                          |
| 10001     | There is no path to purge. | 재배포할 경로를 입력해 주세요.                                                                                        |
| 10002     | The HTTP Method or url of Callback URL is not valid. | 콜백 URL의 HTTP 메소드 또는 URL이 입력되지 않았습니다.                                                                     |
| 10003     | Callback url format is not valid. | 콜백 URL의 URL 형식이 올바르지 않습니다.                                                                               |
| 10004     | Exist a creating service. Please retry after it is finished. | 요청한 작업을 수행할 수 없는 상태입니다. 이전에 요청한 작업이 완료된 후 다시 시도해 주세요.                                                    |
| 10006     | Not found resource | 요청한 리소스를 찾을 수 없거나 잘못된 요청 정보입니다.                                                                          |
| 10007     | Alreay exists domain alias. | 이미 다른 CDN 서비스 도메인에 등록된 도메인 별칭(domain alias)입니다.                                                          |
| 10008     | Origin path must starts with a slash('/). | 원본 경로는 '/'부터 입력해 주세요.                                                                                    |
| 10009     | Origin path must not end with a slash('/). | 원본 경로의 마지막 문자로 '/'를 입력할 수 없습니다.                                                                          |
| 10010     | Origin domain is required. | 원본 서버는 필수 입력 사항입니다.                                                                                      |
| 10011     | Please enter origin domain without scheme. | 원본 서버에는 http:// 와 같은 스키마(scheme) 정보를 입력할 수 없습니다.                                                         |
| 10012     | Please enter the origin domain in public IP or domain format. | 원본 서버 형식이 올바르지 않습니다. 도메인 또는 IP 형식으로 입력해 주세요.                                                             |
| 10013     | Domain alias is invalid. | 도메인 별칭 형식이 올바르지 않습니다.                                                                                    |
| 10014     | Referrers is invalid. Enter referrers regular expression. | 리퍼러(referrer) 형식이 올바르지 않습니다. 유효한 정규 표현식인지 확인해 주세요.                                                       |
| 10015     | Please enter useOriginCacheControl or cacheType. | 캐시 설정을 위해 useOriginCacheControl이나 cacheType 중 하나는 반드시 입력해야 합니다.                                          |
| 10016     | Please enter origin. | 원본 서버 설정이 없거나 올바르지 않습니다.                                                                                 |
| 10017     | Invalid Purge Type. Please enter purge type 'ITEM' or 'ALL' | 재배포(purge) 타입이 올바르지 않습니다. 타입은 'ITEM' 또는 'ALL'로 입력해 주세요.                                                  |
| 10018     | Invalid Origin Port. Please enter a number between 0 and 65,536. | 원본 서버의 포트가 올바르지 않습니다. 0~65,536 사이의 숫자로 입력해 주세요.                                                          |
| 10019     | Up to three domain aliases can be registered. | 최대 3개의 도메인 별칭까지 등록이 가능합니다.                                                                               |
| 10020     | The requested domain alias has duplicated values. | 이미 등록된 도메인 별칭으로, 신규로 등록이 불가합니다.                                                                          |
| 10021     | Already deleted distribution. | 이미 삭제된 CDN 서비스입니다.                                                                                       |
| 10023     | The port field cannot be set with either httpPort or httpsPort. | port와 httpPort, httpsPort는 동시에 입력할 수 없습니다. port를 입력하거나 혹은 httpPort와 httpsPort를 입력해 주세요.                  |
| 10024     | The origin server port is not set. | origin server port는 필수 입력값입니다. port 혹은 httpPort 혹은 httpsPort 항목에 원본 서버 포트를 입력해 주세요.                      |
| 10025     | Invalid origin port. The httpPort equals httpsPort. | httpPort와 httpsPort 값을 다르게 입력해 주세요.                                                                      |
| 10027     | The description's maximum length is limited to 255 characters. | CDN 서비스의 설명은 최대 255자까지 가능합니다.                                                                            |
| 10028     | The Root Path Control 'controlType' is not set. Please enter type 'DENY' or 'REDIRECT' | controlType은 필수 입력값입니다. 'DENY' 또는 'REDIRECT'를 입력해 주세요.                                                   |
| 10029     | The Root Path Control 'redirectPath' is not set. | controlType이 'REDIRECT'일 경우, redirectPath는 필수 입력값입니다.                                                    |
| 10030     | 'redirectPath' must starts with a slash('/). | redirectPath는 '/'부터 입력해 주세요.                                                                             |
| 10031     | The Root Path Control 'redirectStatusCode' is not set. Please enter Code 301 or 302 or 303 or 307 | controlType이 'REDIRECT'일 경우, redirectStatusCode는 필수 입력값입니다. 301, 302, 303, 307 중에 입력해 주세요.               |
| 10032     | Invalid 'redirectStatusCode'. Please enter Code 301 or 302 or 303 or 307 | redirectStatusCode 입력값이 올바르지 않습니다. 301, 302, 303, 307 중에 입력해 주세요.                                        |
| 10033     | Auth Token 'encryptKey' is not set. | encryptKey는 필수 입력값입니다. NHN Cloud CDN 콘솔에 표시되는 [Auth Token 인증 제어 관리 > 토큰 암호화 키] 값을 입력해 주세요.               |
| 10034     | Auth Token 'durationSeconds' is not set. | durationSeconds는 필수 입력값입니다. 토큰 유효 시간으로 설정할 값을 초 단위로 입력해 주세요.                                             |
| 10035     | Invalid 'sessionId'. Please enter smaller than 36 bytes for the length of character string. | sessionId 문자열의 길이는 36바이트를 초과할 수 없습니다.                                                                    |
| 10036     | There is no path to generate token. Please enter 'singlePath' or 'singleWildcardPath' or 'multipleWildcardPath'. | singlePath, singleWildcardPath, multipleWildcardPath 중 하나 이상의 값이 필수로 존재해야 합니다.                           |
| 10037     | Auth token paths ('singlePath', 'singleWildcardPath', 'multipleWildcardPath') must start with a slash(/). | singlePath, singleWildcardPath, multipleWildcardPath는 '/'로 시작해야 합니다.                                     |
| 10038     | Invalid character exists. Since ! and ~ are used as reserved characters, do not include them in path or session ID. | !, ~ 문자는 예약된 문자로 사용되므로 경로 또는 세션 ID에 포함할 수 없습니다.                                                          |
| 10039     | HTTP Response Header 'headerList' is not set. | headerList는 필수 입력값입니다.                                                                                   |
| 10040     | HTTP Response Header 'action' is not set. | action은 필수 입력값입니다. 'ADD', 'MODIFY', 'DELETE' 중 하나의 값을 입력해 주세요.                                           |
| 10041     | HTTP Response Header 'headerValue' is not set. | headerValue는 필수 입력값입니다.                                                                                  |
| 10042     | There is no headerName to modify. Please enter 'standardHeaderName' or 'customHeaderName'.    | standardHeaderName에 사전 정의된 값을 입력해야 합니다. standardHeaderName이 'OTHER'일 경우에는 customHeaderName을 필수로 입력해야 합니다. |
| 10043     | Exceeded the maximum HTTP Response Header 'headerList' count. Up to 10 header can be registered.  | headerList에는 최대 10개까지 헤더 목록을 설정할 수 있습니다.                                                                 |
| 10044     | Invalid 'customHeaderName'. Only alphabet, numbers, and '-', '_' are available.   | 잘못된 customHeaderName입니다. 알파벳과 숫자, 특수문자 중 -, _만 설정할 수 있습니다.                                               |
| 10045     | 'customHeaderName' has duplicated values.  | 중복된 customHeaderName 헤더 이름이 존재합니다. 동일한 헤더 이름은 여러 개 설정할 수 없습니다.                                           |
| 10046     | 'standardHeaderName' has duplicated values.  | 중복된 standardHeaderName 헤더 이름이 존재합니다. 동일한 헤더 이름은 여러 개 설정할 수 없습니다.                                         |
| 10101     | This dnsName is already registered. If you are the dnsName owner, please contact us.  | 이미 등록되어 있는 인증서 도메인입니다. 해당 도메인의 실소유주라면 서비스 제공자에게 문의해 주세요.                                                 |
| 10102     | Temporarily, the dnsName cannot be registered.  | 인증서 도메인 등록에 실패하였습니다. 잠시 후 시도해 주세요.                                                                       |
| 10103     | Invalid dnsName. Enter FQDN(Fully Qualified Domain Name).  | 잘못된 인증서 도메인 입력입니다. 전체 도메인 주소(FQDN, fully qualified domain name) 형식으로 입력해 주세요.                            |
| 10105     | Invalid domain or not in a deletable state.  | 인증서를 삭제할 수 없습니다. 잘못된 도메인 입력이거나 삭제할 수 있는 상태가 아닙니다.                                                        |
| 10110     | You have exceeded the number of certificates you can issue. Please contact customer service to issue more certificates.  | 발급할 수 있는 최대 인증서 개수를 초과하였습니다. 추가로 인증서 발급이 필요할 경우 서비스 제공자에게 문의해 주세요.                                       |
| 10111     | No certificate available Please try again in a few minutes.  | 인증서를 발급할 수 없습니다. 잠시 후 시도해 주세요.                                                                           |
| 10114     | Invalid statistics search period. fromDate or toDate can be set within 90 days.  | 잘못된 통계 조회 기간입니다. fromDate와 toDate는 90일 이내의 날짜로만 설정할 수 있습니다.      |
| 10115     | Invalid Top Contents By Hits statistics search period. fromDate or toDate can be set daily basis up to the previous day.  | Ton Contents By Hits 통계의 잘못된 조회 기간입니다. fromDate와 toDate는 하루 전까지 하루 이상의 날짜 범위로만 설정할 수 있습니다.      |
| 20001     | Failed to authenticate with AppKey. | 앱키(appkey)가 유효하지 않습니다.                                                                                   |
| 20002     | Failed to authenticate with the secret key. | 비밀 키(secret key)가 유효하지 않습니다.                                                                                  |
| 21000     | OpenStack Api fail.| 기본 인프라 서비스 정보 조회에 실패했습니다. 잠시 후 다시 시도해 주세요. |
| 21002     | Get iaas token fail.| 기본 인프라 서비스 토큰 발급에 실패했습니다. 잠시 후 다시 시도해 주세요. |
| 21003     | IaaS not enabled.| 선택한 리전에 원본 서버로 설정 가능한 인스턴스가 존재하지 않습니다. |
| 21004     | Object storage not enabled.| Object Storage 서비스가 활성화되어 있지 않습니다. |
| 30000     | Failed to process request. Please try again later. | 요청 처리에 실패했습니다.                                                                                           |
| 30001     | Purge usage has exceeded. Please try again later. | 재배포 사용량을 초과했습니다.                                                                                         |
| 30002     | Data not created or does not exist. Please try again later. | 데이터가 생성되지 않았거나 존재하지 않습니다.                                                                                |
| 30003     | Request not processed in current service status. | 현재 서비스 상태에서 처리할 수 없는 요청입니다.                                                                              |
| 30004     | The status of the service is unknown. | 알 수 없는 서비스 상태입니다.                                                                                        |
| 30005     | Resource not found. | 리소스를 찾을 수 없습니다.                                                                                          |
| 80500     | An unknown error occurred during processing. Please contact the service provider. | 처리 중 알 수 없는 오류가 발생했습니다. 서비스 제공자에게 문의해 주세요.                                                               |
