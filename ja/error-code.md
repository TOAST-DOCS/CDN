
## Content Delivery > CDN > エラーコード

| errorCode | errorMessage | 説明                                                                                                      |
|-----------| --- |----------------------------------------------------------------------------------------------------------|
| 0         | SUCCESS | 処理成功                                                                                                   |
| -1        | FAIL | 処理失敗                                                                                                   |
| -2        | ASSERT | 処理失敗                                                                                                   |
| 10000     | Domain does not exist. | 存在しないサービスです。                                                                                          |
| 10001     | There is no path to purge. | 再配布するパスを入力してください。                                                                                        |
| 10002     | The HTTP Method or url of Callback URL is not valid. | コールバックURLのHTTPメソッドまたはURLが入力されていません。                                                                     |
| 10003     | Callback url format is not valid. | コールバックURLのURL形式が正しくありません。                                                                               |
| 10004     | Exist a creating service. Please retry after it is finished. | 要求した作業を実行できない状態です。以前に要求した作業が完了した後に再度お試しください。                                                    |
| 10006     | Not found resource | 要求したリソースが見つからないか、無効な要求情報です。                                                                          |
| 10007     | Alreay exists domain alias. | すでに他のCDNサービスドメインに登録されているドメインエイリアス(domain alias)です。                                                          |
| 10008     | Origin path must starts with a slash('/). | ソースパスは'/'から入力してください。                                                                                    |
| 10009     | Origin path must not end with a slash('/). | ソースパスの最後の文字に'/'を入力できません。                                                                          |
| 10010     | Origin domain is required. | 原本サーバーは必須入力事項です。                                                                                      |
| 10011     | Please enter origin domain without scheme. | 原本サーバーにはhttp:// などのスキーマ(scheme)情報を入力できません。                                                         |
| 10012     | Please enter the origin domain in public IP or domain format. | 原本サーバー形式が正しくありません。ドメインまたはIP形式で入力してください。                                                             |
| 10013     | Domain alias is invalid. | ドメインエイリアス形式が正しくありません。                                                                                    |
| 10014     | Referrers is invalid. Enter referrers regular expression. | リファラー(referrer)形式が正しくありません。有効な正規表現かを確認してください。                                                       |
| 10015     | Please enter useOriginCacheControl or cacheType. | キャッシュ設定を行うにはuseOriginCacheControlまたはcacheTypeのいずれかを必ず入力する必要があります。                                          |
| 10016     | Please enter origin. | 原本サーバー設定がないか、正しくありません。                                                                                 |
| 10017     | Invalid Purge Type. Please enter purge type 'ITEM' or 'ALL' | 再配布(purge) タイプが正しくありません。サーバー設定がないか、正しくありません。                                                  |
| 10018     | Invalid Origin Port. Please enter a number between 0 and 65,536. | 原本サーバーのポートが正しくありません。0～65,536の数字を入力してください。                                                          |
| 10019     | Up to three domain aliases can be registered. | 最大3個のドメインエイリアスまで登録できます。                                                                               |
| 10020     | The requested domain alias has duplicated values. | すでに登録されているドメインエイリアスのため登録できません。                                                                          |
| 10021     | Already deleted distribution. | すでに削除されているCDNサービスです。                                                                                       |
| 10023     | The port field cannot be set with either httpPort or httpsPort. | portとhttpPort、httpsPortは同時に入力できません。portを入力するか、httpPortとhttpsPortを入力してください。                  |
| 10024     | The origin server port is not set. | origin server portは必須入力値です。PortまたはhttpPort、httpsPort項目にオリジンサーバーポートを入力してください。                      |
| 10025     | Invalid origin port. The httpPort equals httpsPort. | httpPortとhttpsPortは別の値を入力してください。                                                                      |
| 10027     | The description's maximum length is limited to 255 characters. | CDNサービスの説明は最大255文字まで入力できます。                                                                            |
| 10028     | The Root Path Control 'controlType' is not set. Please enter type 'DENY' or 'REDIRECT' | controlTypeは必須入力値です。 'DENY'または'REDIRECT'を入力してください。                                                   |
| 10029     | The Root Path Control 'redirectPath' is not set. | controlTypeが'REDIRECT'の場合、redirectPathは必須入力値です。                                                    |
| 10030     | 'redirectPath' must starts with a slash('/). | redirectPathは'/'から入力してください。                                                                             |
| 10031     | The Root Path Control 'redirectStatusCode' is not set. Please enter Code 301 or 302 or 303 or 307 | controlTypeが'REDIRECT'の場合、redirectStatusCodeは必須入力値です。301、302、303、307のいずれかを入力してください。               |
| 10032     | Invalid 'redirectStatusCode'. Please enter Code 301 or 302 or 303 or 307 | redirectStatusCodeの入力値が正しくありません。301、302、303、307のいずれかを入力してください。                                        |
| 10033     | Auth Token 'encryptKey' is not set. | encryptKeyは必須入力値です。 NHN Cloud CDNコンソールに表示される[Auth Token認証制御管理 > トークン暗号化キー]値を入力してください。               |
| 10034     | Auth Token 'durationSeconds' is not set. | durationSecondsは必須入力値です。トークン有効時間として設定する値を秒単位で入力してください。                                             |
| 10035     | Invalid 'sessionId'. Please enter smaller than 36 bytes for the length of character string. | sessionId文字列の長さは36バイトを超えられません。                                                                    |
| 10036     | There is no path to generate token. Please enter 'singlePath' or 'singleWildcardPath' or 'multipleWildcardPath'. | singlePath、singleWildcardPath、multipleWildcardPathのうち1つ以上の値が必ず存在する必要があります。                           |
| 10037     | Auth token paths ('singlePath', 'singleWildcardPath', 'multipleWildcardPath') must start with a slash(/). | singlePath、singleWildcardPath、multipleWildcardPathは(/)で始まる必要があります。                                     |
| 10038     | Invalid character exists. Since ! and ~ are used as reserved characters, do not include them in path or session ID. | !、~文字は予約文字として使用されるため、パスまたはセッションIDに含められません。                                                          |
| 10039     | HTTP Response Header 'headerList' is not set. | headerListは必須入力値です。                                                                                   |
| 10040     | HTTP Response Header 'action' is not set. | actionは必須入力値です。 'ADD', 'MODIFY', 'DELETE'のいずれかの値を入力してください。                                           |
| 10041     | HTTP Response Header 'headerValue' is not set. | headerValueは必須入力値です。                                                                                  |
| 10042     | There is no headerName to modify. Please enter 'standardHeaderName' or 'customHeaderName'.    | standardHeaderNameに事前定義された値を入力する必要があります。 standardHeaderNameが'OTHER'の場合にはcustomHeaderNameを必ず入力する必要があります。 |
| 10043     | Exceeded the maximum HTTP Response Header 'headerList' count. Up to 10 header can be registered.  | headerListには最大10個までヘッダリストを設定できます。                                                                 |
| 10044     | Invalid 'customHeaderName'. Only alphabet, numbers, and '-', '_' are available.   | 無効なcustomHeaderNameです。アルファベットと数字、特殊文字のうち-, _のみ設定できます。                                               |
| 10045     | 'customHeaderName' has duplicated values.  | 重複した customHeaderName ヘッダー名が存在します。同じヘッダー名を複数設定することはできません。                                           |
| 10046     | 'standardHeaderName' has duplicated values.  | 重複した standardHeaderName ヘッダ名が存在します。同じヘッダ名を複数設定することはできません。                                         |
| 10101     | This dnsName is already registered. If you are the dnsName owner, please contact us.  | すでに登録されている証明書ドメインです。当該ドメインの実所有者であれば、サービスプロバイダーにお問い合わせください。                                                 |
| 10102     | Temporarily, the dnsName cannot be registered.  | 証明書ドメインの登録に失敗しました。しばらくしてから再試行してください。                                                                       |
| 10103     | Invalid dnsName. Enter FQDN(Fully Qualified Domain Name).  | 証明書ドメインの入力が無効です。完全なドメインアドレス(FQDN, fully qualified domain name)形式で入力してください。                            |
| 10105     | Invalid domain or not in a deletable state.  | 証明書を削除できません。 間違ったドメインを入力したか、削除できる状態ではありません。                                                        |
| 10110     | You have exceeded the number of certificates you can issue. Please contact customer service to issue more certificates.  | 発行できる最大証明書数を超過しました。 追加の証明書発行が必要な場合は、サービスプロバイダーにお問い合わせください。                                       |
| 10111     | No certificate available Please try again in a few minutes.  | 証明書を発行することができません。 もう一度お試しください。                                                                           |
| 10114     | Invalid statistics search period. fromDate or toDate can be set within 90 days.  | 誤った統計照会期間です。fromDateとtoDateは90日以内の日付にのみ設定できます。      |
| 10115     | Invalid Top Contents By Hits statistics search period. fromDate or toDate can be set daily basis up to the previous day.  | Ton Contents By Hits統計の無効な照会期間です。fromDateとtoDateは前日までの1日以上の日付範囲のみ設定できます。 fromDateとtoDateは1日前までの日付範囲のみ設定できます。
| 20001     | Failed to authenticate with AppKey. | アプリケーションキー(appkey)が有効ではありません。                                                                                   |
| 20002     | Failed to authenticate with the secret key. | 秘密鍵(secret key)が有効ではありません。                                                                                  |
| 21000     | OpenStack Api fail.| 基本インフラサービスの情報照会に失敗しました。しばらくしてからもう一度お試しください。 |
| 21002     | Get iaas token fail.| 基本インフラサービストークンの発行に失敗しました。しばらくしてからもう一度お試しください。 |
| 21003     | IaaS not enabled.| 選択したリージョンにオリジンサーバーとして設定可能なインスタンスが存在しません。 |
| 21004     | Object storage not enabled.| Object Storageサービスが有効化されていません。 |
| 30000     | Failed to process request. Please try again later. | 要求された処理に失敗しました。                                                                                           |
| 30001     | Purge usage has exceeded. Please try again later. | 再配布使用量を越えました。                                                                                         |
| 30002     | Data not created or does not exist. Please try again later. | データが作成されていないか、存在しません。                                                                                |
| 30003     | Request not processed in current service status. | 現在のサービス状態で処理できない要求です。                                                                              |
| 30004     | The status of the service is unknown. | 不明なサービス状態です。                                                                                        |
| 30005     | Resource not found. | リソースが見つかりません。                                                                                          |
| 80500     | An unknown error occurred during processing. Please contact the service provider. | 処理中に不明なエラーが発生しました。サービス提供者にお問い合わせください。                                                               |
