<!-- pre-align:aligned sig=6a9497a9cc76 -->

<a id="content-delivery-cdn-api-v30-guide"></a>
## Content Delivery > CDN > API v3.0 ガイド { #content-delivery-cdn-api-v30-guide }

NHN Cloud CDNで提供するPublic API v3.0について説明します。

<a id="api"></a>
## API共通情報 { #api }

<a id="domain"></a>
### ドメイン { #domain }

| 名前             | ドメイン                                |
| --------------- | ----------------------------------- |
| CDN Public APIドメイン | https://cdn.api.nhncloudservice.com |

<a id="authentication-and-authorization"></a>
### 認証及び権限 { #authentication-and-authorization }
CDN API v3.0は、API認証の呼び出し及び認証のためにAppkeyとUser Access Keyトークンをサポートします。

Appkeyは、NHN Cloudの各サービスごとに発行される固有の認証キーであり、APIリクエスト時にサービスの識別と有効性の検証に使用されます。<br>User Access Keyトークンは、User Access Keyを基に発行されるBearerタイプの一時的なアクセストークンです。
各認証方法の確認及び使用に関する詳細は、それぞれ[Appkey](/nhncloud/ja/public-api/appkey/)と[User Access Keyトークン](/nhncloud/ja/public-api/user-access-key-token/)をご参照ください。

発行されたトークンはリクエストHeaderに含める必要があります。

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| X-NHN-AUTHORIZATION | Header | String | O | Public APIで発行されたBearerタイプのトークン |

<a id="response-common-information"></a>
### 共通レスポンス情報 { #response-common-information }

- すべてのAPIリクエストに対して'200 OK'のレスポンスを返します。詳細なレスポンス結果は、レスポンスボディのヘッダーをご参照ください。

[成功レスポンスボディ]

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
#### CDNステータスコード

以下はCDNサービスのステータスを示すステータスコードであり、サービスの照会時にサービスのステータスを確認できます。

| 値  | 説明              |
| ---------- | ------------------------ |
| OPENING    | サービス起動中    |
| OPEN       | サービス中         |
| MODIFYING  | 修正中           |
| RESUME     | 開始              |
| SUSPENDING | 停止進行中      |
| SUSPEND    | 停止              |
| CLOSING    | 使用終了中      |
| CLOSE      | 使用終了         |
| ERROR      | サービス作成中にエラーが発生 |

<a id="response-common-information-certificate-issuance-status-codes"></a>
#### 証明書発行ステータスコード

以下はドメインの証明書の発行ステータスを示すステータスコードであり、証明書の照会時に発行ステータスを確認できます。

| 値  | 説明              |
| ---------- | ------------------------ |
| PENDING_NEW        | 証明書の新規発行がリクエストされ、処理待機中   |
| PENDING_CANCEL     | 証明書発行のキャンセルがリクエストされ、ドメイン検証のキャンセル処理待機中   |
| PENDING_DELETE     | 発行された証明書の削除がリクエストされ、処理待機中  |
| PENDING_EXPIRE     | 発行された証明書の有効期限が切れ、有効期限切れ処理待機中  |
| VALIDATED          | ドメインの検証完了                     |
| DEPLOYED           | 証明書のデプロイ完了                     |
| WAITING_VALIDATION | ドメインの検証待機中                  |
| CANCELED           | ドメイン検証のキャンセル完了                 |
| DELETED            | ドメイン証明書の削除完了               |
| EXPIRED            | ドメイン証明書の有効期限切れ                   |


<a id="service-api"></a>
## サービスAPI { #service-api }

<a id="create-service"></a>
### サービスの作成 { #create-service }

<a id="create-service-request"></a>
#### リクエスト


[URI]

| メソッド | URI                                  |
| ---- |--------------------------------------|
| POST | /v3.0/appKeys/{appKey}/distributions |


[リクエスト本文]

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

[フィールド]

| 名前                                                                                    | タイプ      | 必須かどうか | デフォルト値         | 有効範囲                                                                 | 説明                                                                                                                        |
|---------------------------------------------------------------------------------------|---------|-------|-------------|-----------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| distributions                                                                         | List    | 必須    |             |                                                                       | 作成するCDNのオブジェクト一覧                                                                                                          |
| distributions[0].useOriginHttpProtocolDowngrade                                       | Boolean | 必須    | false       | true/false                                                                            | オリジンサーバーがHTTPレスポンスのみ可能な場合、CDNサーバーからオリジンサーバーへのリクエスト時にHTTPSリクエストをHTTPリクエストにダウングレードするための設定の使用有無                                     |
| distributions[0].forwardHostHeader                                                    | String  | 必須    |             | ORIGIN_HOSTNAME<br/>REQUEST_HOST_HEADER                               | CDNサーバーがオリジンサーバーにコンテンツをリクエストする際に送信するホストヘッダの設定("ORIGIN_HOSTNAME": オリジンサーバーのホスト名に設定、"REQUEST_HOST_HEADER": クライアントリクエストのホストヘッダに設定) |
| distributions[0].useOriginCacheControl                                                | Boolean | 任意    |             | true/false                                                            | キャッシュ有効期限の設定(true: オリジンサーバーの設定を使用、false: ユーザー設定を使用)。useOriginCacheControlまたはcacheTypeのいずれかを必ず入力する必要があります。                      |
| distributions[0].cacheType                                                            | String  | 任意    |             | BYPASS, NO_STORE                                                      | キャッシュタイプの設定。useOriginCacheControlまたはcacheTypeのいずれかを必ず入力する必要があります。                                                           |
| distributions[0].referrerType                                                         | String  | 必須    |             | BLACKLIST/WHITELIST                                                   | リファラーアクセスの管理("BLACKLIST": ブラックリスト、"WHITELIST": ホワイトリスト)                                                                        |
| distributions[0].referrers                                                            | List    | 任意    |             |                                                                       | 正規表現形式のリファラーヘッダ一覧                                                                                                      |
| distributions[0].isAllowWhenEmptyReferrer                                           | Boolean | 任意    | true        | true/false                                                                            | リファラーヘッダーがない場合のコンテンツアクセスを許可するかどうか (true: 許可、false: 拒否)                                                                                                              |
| distributions[0].isAllowPost                                                          | Boolean | 任意    | false       | true/false                                                                            | POSTメソッドを許可するかどうか (true: 許可、false: 拒否)                                                                                                                                              |
| distributions[0].isAllowPut                                                           | Boolean | 任意    | false       | true/false                                                                            | PUTメソッドを許可するかどうか (true: 許可、false: 拒否)                                                                                                                                               |
| distributions[0].isAllowPatch                                                         | Boolean | 任意    | false       | true/false                                                                            | PATCHメソッドを許可するかどうか (true: 許可、false: 拒否)                                                                                                                                             |
| distributions[0].isAllowDelete                                                        | Boolean | 任意    | false       | true/false                                                                            | DELETEメソッドを許可するかどうか (true: 許可、false: 拒否)                                                                                                                                            |
| distributions[0].useLargeFileOptimization                                             | Boolean | 任意    | false       | true/false                                                            | 大容量ファイル最適化設定の使用有無                                                                                                       |
| distributions[0].description                                                          | String  | 任意    |             | 最大255文字                                                               | 説明                                                                                                                        |
| distributions[0].domainAlias                                                          | List    | 任意    |             |                                                                       | ドメインエイリアス一覧(個人または会社が所有するドメインを使用)                                                                                         |
| distributions[0].defaultMaxAge                                                        | Integer | 任意    | 0           | 0～2,147,483,647                                                       | キャッシュの有効期限(秒)、デフォルト値0は604,800秒です。                                                                                          |
| distributions[0].cacheKeyQueryParam                                                   | String  | 任意    | INCLUDE_ALL | INCLUDE_ALL/EXCLUDE_ALL                                               | キャッシュキーにリクエストクエリ文字列を含めるかどうかの設定("INCLUDE_ALL": 全て含む、"EXCLUDE_ALL": 全て含まない)                                                     |
| distributions[0].origins                                                              | List    | 必須    |             |                                                                       | オリジンサーバーオブジェクト一覧                                                                                                             |
| distributions[0].origins[0].origin                                                    | String  | 必須    |             | 最大255文字                                                               | オリジンサーバー(ドメインまたはIP)                                                                                                          |
| distributions[0].origins[0].originPath                                                | String  | 任意    |             | 最大8192文字                                                              | オリジンサーバーのサブパス(/を含むパスで入力します。)                                                                                          |
| distributions[0].origins[0].httpPort                                                  | Integer | 任意    |             | [コンソール使用ガイド > オリジンサーバー](./console-guide/#origin-server-port)の'[表 2] 使用可能なオリジンサーバーのポート番号'を参照 | オリジンサーバーのHTTPプロトコルポート(origins[0].httpPortとorigins[0].httpsPortのいずれかを必ず入力する必要があります。)                                         |
| distributions[0].origins[0].httpsPort                                                 | Integer | 任意    |             | [コンソール使用ガイド > オリジンサーバー](./console-guide/#origin-server-port)の'[表 2] 使用可能なオリジンサーバーのポート番号'を参照 | オリジンサーバーのHTTPSプロトコルポート(origins[0].httpPortとorigins[0].httpsPortのいずれかを必ず入力する必要があります。)                                        |
| distributions[0].rootPathAccessControl                                                | Object  | 任意    |             |                                                                       | CDNサービスのルートパスに対するアクセス制御の設定                                                                                               |
| distributions[0].rootPathAccessControl.enable                                         | Boolean | 必須    | true        | true/false                                                                            | ルートパスに対するアクセス制御を使用するかどうか (true: 使用、false: 未使用)                                                                                                                            |
| distributions[0].rootPathAccessControl.controlType                                    | String  | 任意    |             | DENY, REDIRECT                                                        | enableがtrueの場合に必須入力。ルートパスに対するアクセス制御方式("DENY": アクセス拒否、"REDIRECT": 指定したパスへリダイレクト)                                      |
| distributions[0].rootPathAccessControl.redirectPath                                   | String  | 任意    |             |                                                                       | controlTypeが"REDIRECT"の場合に必須入力。ルートパスへのリクエストをリダイレクトするパス(/を含むパスで入力します。)                                           |
| distributions[0].rootPathAccessControl.redirectStatusCode                             | Integer | 任意    |             | 301, 302, 303, 307                                                    | controlTypeが"REDIRECT"の場合に必須入力。リダイレクト時に送信されるHTTPレスポンスコード                                                                 |
| distributions[0].modifyOutgoingResponseHeaderControl                                  | Object  | 任意    |             |                                                                                       | CDNから返されるHTTPレスポンスヘッダーを追加/変更/削除する設定                                                                                                                              |
| distributions[0].modifyOutgoingResponseHeaderControl.enable                           | Boolean | 必須    | true        | true/false                                                                            | HTTPレスポンスヘッダーを追加/変更/削除する設定を使用するかどうか (true: 使用、false: 未使用)                                                                                                          |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList                       | List    | 任意    |         |                                                                       | HTTPレスポンスヘッダ一覧                                                                                                             |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].action             | String  | 任意    |         | ADD, MODIFY, DELETE                                                   | HTTPレスポンスヘッダの変更方式                                                                                                          |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].standardHeaderName | String  | 任意    |         | ACCESS_CONTROL_ALLOW_CREDENTIALS<br/>ACCESS_CONTROL_ALLOW_HEADERS<br/>ACCESS_CONTROL_ALLOW_METHODS<br/>ACCESS_CONTROL_ALLOW_ORIGIN<br/>ACCESS_CONTROL_EXPOSE_HEADERS<br/>ACCESS_CONTROL_MAX_AGE<br/>CACHE_CONTROL<br/>CONTENT_DISPOSITION<br/>CONTENT_TYPE<br/>P3P<br/>PRAGMA<br/>OTHER | 一般的なHTTPレスポンスヘッダ名                                                                                                          |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].customHeaderName   | String  | 任意    |         |                                                      | standardHeaderNameが"OTHER"の場合に必須入力。ユーザー定義のHTTPレスポンスヘッダ名                                                               |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].headerValue        | String  | 必須    |         |                                                      | HTTPレスポンスヘッダ値                                                                                                              |
| distributions[0].callback                                                             | Object  | 任意    |             |                                                                       | CDN作成の処理結果の通知を受け取るコールバックURL(コールバックの設定は任意入力です。)                                                                               |
| distributions[0].callback.httpMethod                                                  | String  | 必須    |             | GET/POST/PUT                                                          | コールバックのHTTPメソッド                                                                                                              |
| distributions[0].callback.url                                                         | String  | 必須    |             | 最大1024文字                                                              | コールバックURL                                                                                                                    |

- `forwardHostHeader`のデフォルト値は、`domainAlias`を設定した場合は`REQUEST_HOST_HEADER`であり、設定していない場合は`ORIGIN_HOSTNAME`です。



<a id="create-service-response"></a>
#### レスポンス


[レスポンス本文]

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


[フィールド]

| フィールド                                  | タイプ   | 説明                                                      |
| -------------------------------------- | ------- | ---------------------------------------------------------- |
| header                                 | Object  | ヘッダ領域                                                 |
| header.isSuccessful                    | Boolean | 成否                                                 |
| header.resultCode                      | Integer | 結果コード                                                 |
| header.resultMessage                   | String  | 結果メッセージ                                               |
| distributions                          | List    | 作成されたCDNオブジェクト一覧                                |
| distributions[0].domain                | String  | 作成されたドメイン(サービス名)                                 |
| distributions[0].domainAlias           | List    | ドメインエイリアス一覧(個人または会社が所有するドメインを使用)            |
| distributions[0].region                | String  | サービス地域("GLOBAL":グローバル)          |
| distributions[0].description           | String  | 説明                                                      |
| distributions[0].status                | String  | CDNステータスコード([表] CDNステータスコードを参照)                               |
| distributions[0].defaultMaxAge         | Integer | キャッシュの有効期限(秒)                                         |
| distributions[0].cacheKeyQueryParam    | String  | キャッシュキーにリクエストクエリ文字列を含めるかどうかの設定("INCLUDE_ALL": 全て含む、"EXCLUDE_ALL": 全て含まない) |
| distributions[0].referrerType          | String  | リファラーアクセスの管理("BLACKLIST": ブラックリスト、"WHITELIST": ホワイトリスト) |
| distributions[0].referrers             | List    | 正規表現形式のリファラーヘッダ一覧                                |
| distributions[0].isAllowWhenEmptyReferrer | Boolean | リファラーヘッダがない場合のコンテンツアクセスを許可するかどうか (true: 許可、false: 拒否) |
| distributions[0].isAllowPost | Boolean | POSTメソッドを許可するかどうか (true: 許可、false: 拒否)           |
| distributions[0].isAllowPut | Boolean | PUTメソッドを許可するかどうか (true: 許可、false: 拒否)           |
| distributions[0].isAllowPatch | Boolean | PATCHメソッドを許可するかどうか (true: 許可、false: 拒否)           |
| distributions[0].isAllowDelete | Boolean | DELETEメソッドを許可するかどうか (true: 許可、false: 拒否)           |
| distributions[0].useLargeFileOptimization | Boolean | 大容量ファイル最適化設定を使用するかどうか |
| distributions[0].useOriginCacheControl | Boolean | オリジンサーバーの設定を使用するかどうか (true: オリジンサーバーの設定を使用、false: ユーザー設定を使用) |
| distributions[0].cacheType             | String  | キャッシュタイプの設定                                        |
| distributions[0].origins               | List    | オリジンサーバーオブジェクト一覧                                    |
| distributions[0].origins[0].origin     | String  | オリジンサーバー(ドメインまたはIP)                                    |
| distributions[0].origins[0].originPath | String  | オリジンサーバーのサブパス                                        |
| distributions[0].origins[0].httpPort   | Integer | オリジンサーバーのHTTPプロトコルポート                                             |
| distributions[0].origins[0].httpsPort  | Integer | オリジンサーバーのHTTPSプロトコルポート                                             |
| distributions[0].useOriginHttpProtocolDowngrade | Boolean | オリジンサーバーがHTTPレスポンスのみ可能な場合、CDNサーバーからオリジンサーバーへのリクエスト時にHTTPSリクエストをHTTPリクエストにダウングレードする設定を使用するかどうか |
| distributions[0].forwardHostHeader     | String  | CDNサーバーがオリジンサーバーにコンテンツをリクエストする際に送信するホストヘッダの設定("ORIGIN_HOSTNAME": オリジンサーバーのホスト名に設定、"REQUEST_HOST_HEADER": クライアントリクエストのホストヘッダに設定) |
| distributions[0].rootPathAccessControl  | Object  | CDNサービスのルートパスに対するアクセス制御の設定 |
| distributions[0].rootPathAccessControl.enable | Boolean | ルートパスに対するアクセス制御を使用するかどうか (true: 使用、false: 未使用)        |
| distributions[0].rootPathAccessControl.controlType  | String  | enableがtrueの場合に必須入力。ルートパスに対するアクセス制御方式("DENY": アクセス拒否、"REDIRECT": 指定したパスへリダイレクト) |
| distributions[0].rootPathAccessControl.redirectPath | String | controlTypeが"REDIRECT"の場合に必須入力。ルートパスへのリクエストをリダイレクトするパス(/を含むパスで入力します。)      |
| distributions[0].rootPathAccessControl.redirectStatusCode | Integer | controlTypeが"REDIRECT"の場合に必須入力。リダイレクト時に送信されるHTTPレスポンスコード        |
| distributions[0].modifyOutgoingResponseHeaderControl                                  | Object  | CDNから返されるHTTPレスポンスヘッダを追加/変更/削除する設定  |
| distributions[0].modifyOutgoingResponseHeaderControl.enable                           | Boolean | HTTPレスポンスヘッダを追加/変更/削除する設定を使用するかどうか (true: 使用、false: 未使用)  |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList                       | List    | HTTPレスポンスヘッダ一覧 |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].action             | String  | HTTPレスポンスヘッダの変更方式 |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].standardHeaderName | String  | 一般的なHTTPレスポンスヘッダ名 |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].customHeaderName   | String  | standardHeaderNameが"OTHER"の場合に必須入力。ユーザー定義のHTTPレスポンスヘッダ名 |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].headerValue        | String  | HTTPレスポンスヘッダ値 |
| distributions[0].callback              | Object  | サービスの作成処理結果の通知を受け取るコールバック                      |
| distributions[0].callback.httpMethod   | String  | コールバックのHTTPメソッド                                        |
| distributions[0].callback.url          | String  | コールバックURL                                                   |



<a id="retrieve-service"></a>
### サービスの照会 { #retrieve-service }

<a id="retrieve-service-request"></a>
#### リクエスト


[URI]

| メソッド | URI                                  |
| ---- | ------------------------------------ |
| GET  | /v3.0/appKeys/{appKey}/distributions |


[パラメータ]

| 名前   | タイプ   | 必須かどうか | 有効範囲      | 説明                         |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| domain | String | 任意  | 最大255文字 | 照会するドメイン(サービス名)   |
| status | String | 任意      | CDNステータスコード | CDNステータスコード([表] CDNステータスコードを参照) |

[例]
```
curl -X GET "https://cdn.api.nhncloudservice.com/v3.0/appKeys/{appKey}/distributions?domain={domain}" \
 -H "X-NHN-AUTHORIZATION: {secretKey}" \
 -H "Content-Type: application/json"
```

<a id="retrieve-service-response"></a>
#### レスポンス


[レスポンス本文]

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


[フィールド]

| フィールド                           | タイプ | 説明                                                 |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | ヘッダ領域                                            |
| header.isSuccessful                    | Boolean | 成否                                             |
| header.resultCode                      | Integer | 結果コード                                            |
| header.resultMessage                   | String  | 結果メッセージ                                          |
| distributions                          | List    | 作成されたCDNオブジェクト一覧                                     |
| distributions[0].domain                | String  | ドメイン(サービス名)                                     |
| distributions[0].domainAlias           | List  | ドメインエイリアス一覧(個人または会社が所有するドメインを使用)                                         |
| distributions[0].region                | String  | サービス地域("GLOBAL"：グローバル)             |
| distributions[0].status                | String  | CDNステータスコード([表] CDNステータスコードを参照)                                 |
| distributions[0].defaultMaxAge         | Integer  | キャッシュの有効期限(秒)                                          |
| distributions[0].cacheKeyQueryParam    | String  | キャッシュキーにリクエストクエリ文字列を含めるかどうかの設定("INCLUDE_ALL": 全て含む、"EXCLUDE_ALL": 全て含まない) |
| distributions[0].referrerType          | String  | リファラーアクセスの管理("BLACKLIST": ブラックリスト、"WHITELIST": ホワイトリスト) |
| distributions[0].referrers             | List    | 正規表現形式のリファラーヘッダ一覧                                 |
| distributions[0].isAllowWhenEmptyReferrer | Boolean | リファラーヘッダがない場合のコンテンツアクセスを許可するかどうか (true: 許可、false: 拒否) |
| distributions[0].isAllowPost          | Boolean | POSTメソッドを許可するかどうか (true: 許可、false: 拒否)             |
| distributions[0].isAllowPut           | Boolean | PUTメソッドを許可するかどうか (true: 許可、false: 拒否)             |
| distributions[0].isAllowPatch         | Boolean | PATCHメソッドを許可するかどうか (true: 許可、false: 拒否)             |
| distributions[0].isAllowDelete        | Boolean | DELETEメソッドを許可するかどうか (true: 許可、false: 拒否)             |
| distributions[0].useLargeFileOptimization | Boolean | 大容量ファイル最適化設定を使用するかどうか |
| distributions[0].useOriginCacheControl | Boolean | オリジンサーバーの設定を使用するかどうか (true: オリジンサーバーの設定を使用、false: ユーザー設定を使用) |
| distributions[0].cacheType             | String  | キャッシュタイプの設定                                        |
| distributions[0].origins               | List    | オリジンサーバーオブジェクト一覧                                      |
| distributions[0].origins[0].origin     | String  | オリジンサーバー(ドメインまたはIP)                                      |
| distributions[0].origins[0].originPath | String  | オリジンサーバーのサブパス                                         |
| distributions[0].origins[0].httpPort   | Integer | オリジンサーバーのHTTPプロトコルポート                                  |
| distributions[0].origins[0].httpsPort  | Integer | オリジンサーバーのHTTPSプロトコルポート                                 |
| distributions[0].useOriginHttpProtocolDowngrade | Boolean | オリジンサーバーがHTTPレスポンスのみ可能な場合、CDNサーバーからオリジンサーバーへのリクエスト時にHTTPSリクエストをHTTPリクエストにダウングレードする設定を使用するかどうか |
| distributions[0].forwardHostHeader     | String  | CDNサーバーがオリジンサーバーにコンテンツをリクエストする際に送信するホストヘッダの設定("ORIGIN_HOSTNAME": オリジンサーバーのホスト名に設定、"REQUEST_HOST_HEADER": クライアントリクエストのホストヘッダに設定) |
| distributions[0].rootPathAccessControl  | Object  | CDNサービスのルートパスに対するアクセス制御の設定 |
| distributions[0].rootPathAccessControl.enable | Boolean | ルートパスに対するアクセス制御を使用するかどうか (true: 使用、false: 未使用)          |
| distributions[0].rootPathAccessControl.controlType  | String  | enableがtrueの場合に必須入力。ルートパスに対するアクセス制御方式("DENY": アクセス拒否、"REDIRECT": 指定したパスへリダイレクト) |
| distributions[0].rootPathAccessControl.redirectPath | String | controlTypeが"REDIRECT"の場合に必須入力。ルートパスへのリクエストをリダイレクトするパス(/を含むパスで入力します。)        |
| distributions[0].rootPathAccessControl.redirectStatusCode | Integer | controlTypeが"REDIRECT"の場合に必須入力。リダイレクト時に送信されるHTTPレスポンスコード          |
| distributions[0].modifyOutgoingResponseHeaderControl                                  | Object  | CDNから返されるHTTPレスポンスヘッダを追加/変更/削除する設定  |
| distributions[0].modifyOutgoingResponseHeaderControl.enable                           | Boolean | HTTPレスポンスヘッダを追加/変更/削除する設定を使用するかどうか (true: 使用、false: 未使用)  |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList                       | List    | HTTPレスポンスヘッダ一覧 |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].action             | String  | HTTPレスポンスヘッダの変更方式 |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].standardHeaderName | String  | 一般的なHTTPレスポンスヘッダ名 |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].customHeaderName   | String  | standardHeaderNameが"OTHER"の場合に必須入力。ユーザー定義のHTTPレスポンスヘッダ名 |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].headerValue        | String  | HTTPレスポンスヘッダ値 |
| distributions[0].callback              | Object  | サービスのデプロイ処理結果の通知を受け取るコールバック                        |
| distributions[0].callback.httpMethod   | String  | コールバックのHTTPメソッド                                          |
| distributions[0].callback.url          | String  | コールバックURL                                                     |


<a id="modify-service"></a>
### サービスの修正 { #modify-service }

<a id="modify-service-request"></a>
#### リクエスト


[URI]

| メソッド | URI                                  |
| ---- | ------------------------------------ |
| PUT  | /v3.0/appKeys/{appKey}/distributions |


[リクエスト本文]

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


[フィールド]

| 名前                  | タイプ    | 必須かどうか | デフォルト値 | 有効範囲                                                     | 説明                                                         |
| --------------------- | ------- | --------- | ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| domain                | String  | 必須  |        | 最大255文字                                            | 修正するドメイン(サービス名)                                   |
| useOriginCacheControl | Boolean | 任意      |        | true/false                                                        | キャッシュ有効期限の設定(true: オリジンサーバーの設定を使用、false: ユーザー設定を使用)。useOriginCacheControlまたはcacheTypeのいずれかを必ず入力する必要があります。      |
| cacheType             | String  | 任意      |        | BYPASS, NO_STORE            | キャッシュタイプの設定。useOriginCacheControlまたはcacheTypeのいずれかを必ず入力する必要があります。                                         |
| referrerType          | String  | 必須      |        | BLACKLIST/WHITELIST                                          | リファラーアクセスの管理("BLACKLIST": ブラックリスト、"WHITELIST": ホワイトリスト) |
| referrers             | List    | 任意      |        |                                                              | 正規表現形式のリファラーヘッダ一覧 |
| isAllowWhenEmptyReferrer | Boolean | 任意      | true      | true/false             | リファラーヘッダがない場合のコンテンツアクセスを許可するかどうか (true: 許可、false: 拒否)             |
| isAllowPost           | Boolean | 任意      | false      | true/false             | POSTメソッドを許可するかどうか (true: 許可、false: 拒否)             |
| isAllowPut            | Boolean | 任意      | false      | true/false             | PUTメソッドを許可するかどうか (true: 許可、false: 拒否)             |
| isAllowPatch          | Boolean | 任意      | false      | true/false             | PATCHメソッドを許可するかどうか (true: 許可、false: 拒否)             |
| isAllowDelete         | Boolean | 任意      | false      | true/false             | DELETEメソッドを許可するかどうか (true: 許可、false: 拒否)             |
| useLargeFileOptimization | Boolean | 任意   | false      | true/false             | 大容量ファイル最適化設定を使用するかどうか |
| description           | String  | 任意  |        | 最大255文字                                            | 説明                                                 |
| domainAlias           | List    | 任意      |        | 最大255文字                                                  | ドメインエイリアス(個人または会社が所有するドメインを使用) |
| defaultMaxAge         | Integer | 任意      | 0      | 0～2,147,483,647                                            | キャッシュの有効期限(秒)、デフォルト値0は604,800秒です。              |
| cacheKeyQueryParam    | String  | 任意      | INCLUDE_ALL | INCLUDE_ALL/EXCLUDE_ALL                               | キャッシュキーにリクエストクエリ文字列を含めるかどうかの設定("INCLUDE_ALL": 全て含む、"EXCLUDE_ALL": 全て含まない) |
| origins               | List    | 必須  |        |                                                              | オリジンサーバー                                            |
| origins[0].origin     | String  | 必須  |        | 最大255文字                                            | オリジンサーバー(ドメインまたはIP)                                      |
| origins[0].originPath | String  | 任意      |        | 最大8192文字                                                 | オリジンサーバーのサブパス                                         |
| origins[0].httpPort   | Integer  | 任意      |        |[コンソール使用ガイド > オリジンサーバー](./console-guide/#origin-server-port)の「[表 2] 使用可能なオリジンサーバーのポート番号」を参照| オリジンサーバーのHTTPプロトコルポート(origins[0].httpPortとorigins[0].httpsPortのいずれかを必ず入力する必要があります。)  |
| origins[0].httpsPort  | Integer  | 任意      |        |[コンソール使用ガイド > オリジンサーバー](./console-guide/#origin-server-port)の「[表 2] 使用可能なオリジンサーバーのポート番号」を参照 | オリジンサーバーのHTTPSプロトコルポート(origins[0].httpPortとorigins[0].httpsPortのいずれかを必ず入力する必要があります。) |
| useOriginHttpProtocolDowngrade | Boolean  | 必須     | false       | true/false         | オリジンサーバーがHTTPレスポンスのみ可能な場合、CDNサーバーからオリジンサーバーへのリクエスト時にHTTPSリクエストをHTTPリクエストにダウングレードする設定を使用するかどうか |
| forwardHostHeader     | String  | 必須      |        | ORIGIN_HOSTNAME<br/>REQUEST_HOST_HEADER   | CDNサーバーがオリジンサーバーにコンテンツをリクエストする際に送信するホストヘッダの設定("ORIGIN_HOSTNAME": オリジンサーバーのホスト名に設定、"REQUEST_HOST_HEADER": クライアントリクエストのホストヘッダに設定)|
| rootPathAccessControl  | Object  | 任意 |  |  | CDNサービスのルートパスに対するアクセス制御の設定 |
| rootPathAccessControl.enable | Boolean | 必須 | false | true/false | ルートパスに対するアクセス制御を使用するかどうか (true: 使用、false: 未使用)          |
| rootPathAccessControl.controlType  | String  | 任意 |  | DENY, REDIRECT | enableがtrueの場合に必須入力。ルートパスに対するアクセス制御方式("DENY": アクセス拒否、"REDIRECT": 指定したパスへリダイレクト) |
| rootPathAccessControl.redirectPath | String | 任意 |  | | controlTypeが"REDIRECT"の場合に必須入力。ルートパスへのリクエストをリダイレクトするパス(/を含むパスで入力します。)        |
| rootPathAccessControl.redirectStatusCode | Integer | 任意 | | 301, 302, 303, 307 |controlTypeが"REDIRECT"の場合に必須入力。リダイレクト時に送信されるHTTPレスポンスコード          |
| modifyOutgoingResponseHeaderControl                                  | Object  | 任意    |             |                                                                                       | CDNから返されるHTTPレスポンスヘッダーを追加/変更/削除する設定                                                                                                                              |
| modifyOutgoingResponseHeaderControl.enable                           | Boolean | 必須    | true        | true/false                                                                            | HTTPレスポンスヘッダーを追加/変更/削除する設定を使用するかどうか (true: 使用、false: 未使用)                                                                                                          |
| modifyOutgoingResponseHeaderControl.headerList                       | List    | 任意    |         |                                                                       | HTTPレスポンスヘッダ一覧                                                                                                             |
| modifyOutgoingResponseHeaderControl.headerList[0].action             | String  | 任意    |         | ADD, MODIFY, DELETE                                                   | HTTPレスポンスヘッダの変更方式                                                                                                          |
| modifyOutgoingResponseHeaderControl.headerList[0].standardHeaderName | String  | 任意    |         | ACCESS_CONTROL_ALLOW_CREDENTIALS<br/>ACCESS_CONTROL_ALLOW_HEADERS<br/>ACCESS_CONTROL_ALLOW_METHODS<br/>ACCESS_CONTROL_ALLOW_ORIGIN<br/>ACCESS_CONTROL_EXPOSE_HEADERS<br/>ACCESS_CONTROL_MAX_AGE<br/>CACHE_CONTROL<br/>CONTENT_DISPOSITION<br/>CONTENT_TYPE<br/>P3P<br/>PRAGMA<br/>OTHER | 一般的なHTTPレスポンスヘッダ名                                                                                                          |
| modifyOutgoingResponseHeaderControl.headerList[0].customHeaderName   | String  | 任意    |         |                                                      | standardHeaderNameが"OTHER"の場合に必須入力。ユーザー定義のHTTPレスポンスヘッダ名                                                               |
| modifyOutgoingResponseHeaderControl.headerList[0].headerValue        | String  | 必須    |         |                                                      | HTTPレスポンスヘッダ値                                                                                                              |
| callback              | Object  | 任意      |        |                                                              | CDNサービスのデプロイ処理結果の通知を受け取るコールバックURL(コールバックの設定は任意入力です。) |
| callback.httpMethod   | String  | 必須  |        | GET/POST/PUT                                                 | コールバックのHTTPメソッド                                          |
| callback.url          | String  | 必須 |        | 最大1024文字                                             | コールバックURL                                                     |

- `forwardHostHeader`のデフォルト値は、`domainAlias`を設定した場合は`REQUEST_HOST_HEADER`であり、設定していない場合は`ORIGIN_HOSTNAME`です。

<a id="modify-service-response"></a>
#### レスポンス


[レスポンス本文]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    }
}
```


[フィールド]

| フィールド            | タイプ | 説明 |
| -------------------- | ------- | ------ |
| header               | Object  | ヘッダ領域 |
| header.isSuccessful  | Boolean | 成否 |
| header.resultCode | Integer | 結果コード |
| header.resultMessage | String | 結果メッセージ |

<a id="delete-service"></a>
### サービスの削除 { #delete-service }

<a id="delete-service-request"></a>
#### リクエスト


[URI]

| メソッド | URI                                  |
| ------ | ------------------------------------ |
| DELETE | /v3.0/appKeys/{appKey}/distributions |


[リクエスト本文]

```json
{
    "domains" : [
        "lhcsxuo0.toastcdn.net"
    ]
}
```


[フィールド]

| 名前      | タイプ     | 必須かどうか | デフォルト値  | 有効範囲 | 説明                    |
| ------- | ------ | ----- | ---- | ----- | --------------------- |
| domains | String | 必須    |      |       | 削除するドメイン、複数のドメインを入力可能 |

> [注意] 複数のドメインを入力すると、該当するサービスはすべて終了します。

<a id="delete-service-response"></a>
#### レスポンス


[レスポンス本文]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    }
}
```


[フィールド]

| フィールド            | タイプ | 説明 |
| -------------------- | ------- | ------ |
| header               | Object  | ヘッダ領域 |
| header.isSuccessful  | Boolean | 成否 |
| header.resultCode | Integer | 結果コード |
| header.resultMessage | String | 結果メッセージ |


<a id="auth-token-api"></a>
## Auth Token API { #auth-token-api }

<a id="auth-token"></a>
### Auth Token の作成 { #auth-token }

<a id="create-auth-token-request"></a>
#### リクエスト

[URI]

| メソッド | URI                           |
| ---- | ----------------------------- |
| POST | /v3.0/appKeys/{appKey}/auth-token |


[リクエスト本文]

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


[フィールド]

| 名前   | タイプ | 必須かどうか | デフォルト値 | 有効範囲          | 説明                                                      |
| --------- | ------ | --------- | ------ | --------------------- | ------------------------------------------------------------ |
| encryptKey | String | 必須 | | | NHN Cloud CDNコンソールに表示されたAuth Token認証アクセス管理 > トークン暗号化キー |
| durationSeconds | Integer | 必須 |        | 0～2,147,483,647 | 生成されたトークンが有効な時間(秒) |
| singlePath      | String | 任意 |        |             | 生成されたトークンを利用してアクセスする単一のパス |
| singleWildcardPath | String | 任意 |     |             | 生成されたトークンを利用してアクセスする単一のワイルドカードパス |
| multipleWildcardPath | String | 任意 |   |             | 生成されたトークンを利用してアクセスする複数のワイルドカードパス |
| sessionId |           String | 任意 |    |  文字列の長さは最大36バイト           | 単一のアクセスリクエストに対してsessionIdを含めてトークンを生成 |

* `singlePath`、`singleWildcardPath`、`multipleWildcardPath`のうち、1つ以上の値が必須で存在する必要があります。
* トークンの生成及び使用に関する詳細な内容は、[コンソール使用ガイド > Auth Token 認証アクセス管理 > 2. トークン生成](./console-guide/#access-control-for-auth-token-authentication-create-a-token)をご参照ください。


<a id="create-auth-token-response"></a>
#### レスポンス

[レスポンス本文]

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


[フィールド]

| フィールド                | タイプ   | 説明     |
| -------------------- | ------- | --------- |
| header               | Object  | ヘッダ領域  |
| header.isSuccessful  | Boolean | 成否  |
| header.resultCode    | Integer | 結果コード  |
| header.resultMessage | String  | 結果メッセージ |
| authToken             | Object    | 作成されたAuth Tokenオブジェクト |
| authToken.singlePathToken | String    | 単一のパスにアクセスできるように生成された認証トークン                                 |
| authToken.singleWildcardPathToken | String    | 単一のワイルドカードパスにアクセスできるように生成された認証トークン                 |
| authToken.multipleWildcardPathToken | String  | 複数のワイルドカードパスにアクセスできるように生成された認証トークン             |



<a id="purge-cache-api"></a>
## キャッシュ再配布API { #purge-cache-api }

<a id="purge-cache---item-particular-file-type"></a>
### キャッシュ再配布(Purge) - ITEM(特定のファイルタイプ) { #purge-cache---item-particular-file-type }

<a id="purge-cache---item-particular-file-type-request"></a>
#### リクエスト

[URI]

| メソッド | URI                           |
| ---- | ----------------------------- |
| POST | /v3.0/appKeys/{appKey}/purge/item |


[リクエスト本文]

```json
{
	"domain": "sample.toastcdn.net",
	"purgeList":["http://sample.toastcdn.net/img_01.png",
  "http://sample.toastcdn.net/img_02.png"]
}
```


[フィールド]

| 名前   | タイプ | 必須かどうか | デフォルト値 | 有効範囲          | 説明                                                      |
| --------- | ------ | --------- | ------ | --------------------- | ------------------------------------------------------------ |
| domain    | String | 必須      |        | 最大255文字            | 再配布するドメイン(サービス名)                                 |
| purgeList | List | 必須      |        |                       | 再配布対象のURL一覧 |

<a id="purge-cache---item-particular-file-type-response"></a>
#### レスポンス

[レスポンス本文]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    }
}
```


[フィールド]

| フィールド                | タイプ   | 説明     |
| -------------------- | ------- | --------- |
| header               | Object  | ヘッダ領域  |
| header.isSuccessful  | Boolean | 成否  |
| header.resultCode    | Integer | 結果コード  |
| header.resultMessage | String  | 結果メッセージ |

<a id="purge-cache---all-all-file-types"></a>
### キャッシュの再配布(Purge) - ALL(全てのファイルタイプ) { #purge-cache---all-all-file-types }

<a id="purge-cache---all-all-file-types-request"></a>
#### リクエスト

[URI]

| メソッド | URI                           |
| ---- | ----------------------------- |
| POST | /v3.0/appKeys/{appKey}/purge/all |


[リクエスト本文]

```json
{
	"domain": "sample.toastcdn.net"
}
```


[フィールド]

| 名前   | タイプ | 必須かどうか | デフォルト値 | 有効範囲          | 説明                                                      |
| --------- | ------ | --------- | ------ | --------------------- | ------------------------------------------------------------ |
| domain    | String | 必須      |        | 最大255文字            | 再配布するドメイン(サービス名)                                 |

<a id="purge-cache---all-all-file-types-response"></a>
#### レスポンス

[レスポンス本文]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    }
}
```


[フィールド]

| フィールド                | タイプ   | 説明     |
| -------------------- | ------- | --------- |
| header               | Object  | ヘッダ領域  |
| header.isSuccessful  | Boolean | 成否  |
| header.resultCode    | Integer | 結果コード  |
| header.resultMessage | String  | 結果メッセージ |

- CDNサービスを新規に構築した後、約1時間以内はキャッシュの再配布リクエストに失敗する場合があります。それ以降も失敗が続く場合は、カスタマーサポートへお問い合わせください。
- Purge APIの使用量制限ポリシーがあります。詳細は[コンソール使用ガイド > CDN キャッシュの再配布](./console-guide/#purge)の「キャッシュ再配布の使用量制限」の内容をご確認ください。

<a id="purge"></a>
### キャッシュの再配布(Purge)の照会 { #purge }
- API v3.0によるキャッシュの再配布時は、高速キャッシュ再配布が実行されリクエスト後数秒以内に完了するため、キャッシュ再配布の状態を照会するAPIは別途提供されません。

<a id="domain-alias-api"></a>
## ドメインエイリアスAPI { #domain-alias-api }

<a id="register-domain-alias"></a>
### ドメインエイリアスの登録 { #register-domain-alias }

<a id="register-domain-alias-request"></a>
#### リクエスト

[URI]

| メソッド | URI                                          |
| ---- | -------------------------------------------- |
| POST | /v3.0/appKeys/{appKey}/alias-domains         |


[リクエスト本文]

```json
{
    "domain": "cdn.example.com"
}
```

[フィールド]

| 名前              | タイプ     | 必須かどうか | デフォルト値  | 有効範囲                   | 説明                                                                               |
| --------------- | ------ | ----- | ---- | ----------------------- | -------------------------------------------------------------------------------------- |
| domain          | String | 必須    |      | FQDN形式、最小4文字～最大253文字 | 登録するドメイン(フルドメインアドレス形式で入力、toastcdn.netドメインは使用不可)                                    |

<a id="register-domain-alias-response"></a>
#### レスポンス

[レスポンス本文]

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


[フィールド]

| フィールド                                 | タイプ     | 説明                                                                |
| ----------------------------------- | ------- | ------------------------------------------------------------------ |
| header                              | Object  | ヘッダ領域                                                              |
| header.isSuccessful                 | Boolean | 成否                                                             |
| header.resultCode                   | Integer | 結果コード                                                             |
| header.resultMessage                | String  | 結果メッセージ                                                            |
| domain                                | Object  | 登録されたドメインエイリアスのオブジェクト                                                    |
| domain.aliasDomainDomSeq            | Integer | ドメインエイリアスID                                                          |
| domain.domain                       | String  | 登録されたドメイン                                                           |
| domain.validationStatus               | String  | 検証ステータスコード([表] ドメインエイリアスの検証ステータスコードを参照)                                   |
| domain.validationScope              | String  | 検証範囲                                 |
| domain.validationTxtName            | String  | DNS TXTレコード追加方式のレコード名                                         |
| domain.validationTxtValue           | String  | DNS TXTレコード追加方式のレコード値                                           |
| domain.validationHttpPath           | String  | HTTPファイル認証方式のHTTPページURL                                        |
| domain.validationHttpContent        | String  | HTTPファイル認証方式のページコンテンツ値                                          |
| domain.validationHttpRedirectFrom | String | HTTPリダイレクト認証方式のリダイレクト元URL |
| domain.validationHttpRedirectTo | String | HTTPリダイレクト認証方式のリダイレクト先URL |
| domain.validationExpireDatetime       | DateTime | 検証トークンの有効期限切れ日時                                                        |
| domain.validationCompleteDatetime   | DateTime | 検証完了日時                                                           |
| domain.distributionSeq                | Integer | 連携されたCDNサービスID                                                      |
| domain.distribution                   | Object  | 連携されたCDNサービス情報                                                      |
| domain.distribution.domain            | String  | CDNサービスドメイン                                                          |
| domain.distribution.status            | String  | CDNサービスステータスコード([表] CDNステータスコードを参照)                                     |
| domain.createdAt                    | DateTime | 作成日時                                                             |
| domain.updatedAt                    | DateTime | 変更日時                                                             |


<a id="list-domain-aliases"></a>
### ドメインエイリアス一覧の照会 { #list-domain-aliases }

<a id="list-domain-aliases-request"></a>
#### リクエスト

[URI]

| メソッド | URI                                          |
| --- | -------------------------------------------- |
| GET | /v3.0/appKeys/{appKey}/alias-domains         |


[パラメータ]

| 名前     | タイプ      | 必須かどうか | 有効範囲                                                              | 説明                                       |
| ------ | ------- | ----- | --------------------------------------------------------------------------- | ---------------------------------------- |
| domain | String | 任意 | 最大253文字 | 照会するドメイン |
| status | String  | 任意    | REQUEST_ACCEPTED, VALIDATION_IN_PROGRESS, VALIDATED, TOKEN_EXPIRED | 検証ステータスコード(カンマ(,)で複数のステータスを入力可能)                |
| page   | Integer | 任意    | デフォルト値: 1                                                              | ページ番号                                   |
| limit  | Integer | 任意    | デフォルト値: 10、最大: 1000                                                   | ページごとの照会件数                               |

[例]
```
curl -X GET "https://cdn.api.nhncloudservice.com/v3.0/appKeys/{appKey}/alias-domains?status=VALIDATED&page=1&limit=10" \
 -H "X-NHN-AUTHORIZATION: {secretKey}" \
 -H "Content-Type: application/json"
```

<a id="list-domain-aliases-response"></a>
#### レスポンス

[レスポンス本文]

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


[フィールド]

| フィールド                                   | タイプ      | 説明                                                                |
| ------------------------------------- | -------- | ------------------------------------------------------------------ |
| header                                | Object   | ヘッダ領域                                                              |
| header.isSuccessful                   | Boolean  | 成否                                                             |
| header.resultCode                     | Integer  | 結果コード                                                             |
| header.resultMessage                  | String   | 結果メッセージ                                                            |
| paging                                | Object   | ページング領域                                                            |
| paging.page                           | Integer  | ページ番号                                                            |
| paging.limit                          | Integer  | ページごとの照会件数                                                          |
| paging.totalCount | Integer | 総件数 |
| domains                               | List     | ドメインエイリアスのオブジェクト一覧                                                    |
| domains[0].aliasDomainDomSeq          | Integer  | ドメインエイリアスID                                                          |
| domains[0].domain | String | 登録されたドメイン |
| domains[0].validationStatus           | String   | 検証ステータスコード([表] ドメインエイリアスの検証ステータスコードを参照)                                   |
| domains[0].validationTxtName          | String   | DNS TXTレコード追加方式のレコード名                                         |
| domains[0].validationTxtValue         | String   | DNS TXTレコード追加方式のレコード値                                           |
| domains[0].validationHttpPath         | String   | HTTPファイル認証方式のHTTPページURL                                        |
| domains[0].validationHttpContent      | String   | HTTPファイル認証方式のページコンテンツ値                                          |
| domains[0].validationHttpRedirectFrom | String | HTTPリダイレクト認証方式のリダイレクト元URL |
| domains[0].validationHttpRedirectTo | String | HTTPリダイレクト認証方式のリダイレクト先URL |
| domains[0].validationExpireDatetime   | DateTime | 検証トークンの有効期限切れ日時                                                        |
| domains[0].validationCompleteDatetime | DateTime | 検証完了日時                                                           |
| domains[0].distributionSeq            | Integer  | 連携されたCDNサービスID                                                      |
| domains[0].distribution               | Object   | 連携されたCDNサービス情報                                                      |
| domains[0].distribution.domain          | String  | CDNサービスドメイン                                                        |
| domains[0].distribution.status        | String   | CDNサービスステータスコード([表] CDNステータスコードを参照)                                     |
| domains[0].createdAt                    | DateTime | 作成日時                                                             |
| domains[0].updatedAt                    | DateTime | 変更日時                                                             |


<a id="delete-domain-alias"></a>
### ドメインエイリアスの削除 { #delete-domain-alias }

<a id="delete-domain-alias-request"></a>
#### リクエスト

[URI]

| メソッド   | URI                                                        |
| ------ | ---------------------------------------------------------- |
| DELETE | /v3.0/appKeys/{appKey}/alias-domains/{aliasDomainDomSeq}   |


[パラメータ]

| 名前                | タイプ      | 必須かどうか | 有効範囲 | 説明          |
| ----------------- | ------- | ----- | ----- | ----------- |
| aliasDomainDomSeq | Integer | 必須   |       | ドメインエイリアスID  |


[例]
```
curl -X DELETE "https://cdn.api.nhncloudservice.com/v3.0/appKeys/{appKey}/alias-domains/{aliasDomainDomSeq}" \
 -H "X-NHN-AUTHORIZATION: {secretKey}" \
 -H "Content-Type: application/json"
```

<a id="delete-domain-alias-response"></a>
#### レスポンス

[レスポンス本文]

```json
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    }
}
```


[フィールド]

| フィールド            | タイプ | 説明 |
| -------------------- | ------- | ------ |
| header               | Object  | ヘッダ領域 |
| header.isSuccessful  | Boolean | 成否 |
| header.resultCode | Integer | 結果コード |
| header.resultMessage | String | 結果メッセージ |

- CDNサービスに連携されたドメインは削除できません。CDNサービスでドメインエイリアスの連携を解除してから削除してください。


<a id="run-domain-validation"></a>
### ドメイン検証の実行 { #run-domain-validation }

<a id="run-domain-validation-request"></a>
#### リクエスト

[URI]

| メソッド | URI |
| ---- |-------------------------------------------------------------------------|
| POST | /v3.0/appKeys/{appKey}/alias-domains/{aliasDomainDomSeq}/token/validate |


[リクエスト本文]

```json
{
    "validationMethod": "DNS_TXT"
}
```

[フィールド]

| 名前               | タイプ     | 必須かどうか | デフォルト値 | 有効範囲          | 説明                                                            |
| ---------------- | ------ | ----- | --- | -------------- | ------------------------------------------------------------- |
| validationMethod | String | 必須   |     | DNS_TXT, HTTP  | 検証方式("DNS_TXT": DNS TXTレコード追加方式、 "HTTP": HTTPファイルまたはリダイレクト認証方式) |


<a id="run-domain-validation-response"></a>
#### レスポンス

[レスポンス本文]

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

[フィールド]

| フィールド                                 | タイプ     | 説明                                                                |
| ----------------------------------- | ------- | ------------------------------------------------------------------ |
| header                              | Object  | ヘッダ領域                                                              |
| header.isSuccessful                 | Boolean | 成否                                                             |
| header.resultCode                   | Integer | 結果コード                                                             |
| header.resultMessage                | String  | 結果メッセージ                                                            |
| domain                              | Object  | ドメインエイリアスのオブジェクト                                                        |
| domain.aliasDomainDomSeq            | Integer | ドメインエイリアスID                                                          |
| domain.domain                       | String  | 登録されたドメイン                                                           |
| domain.validationStatus               | String  | 検証ステータスコード([表] ドメインエイリアスの検証ステータスコードを参照)                                   |
| domain.validationScope              | String  | 検証範囲                                 |
| domain.validationTxtName            | String  | DNS TXTレコード追加方式のレコード名                                         |
| domain.validationTxtValue           | String  | DNS TXTレコード追加方式のレコード値                                           |
| domain.validationHttpPath           | String  | HTTPファイル認証方式のHTTPページURL                                        |
| domain.validationHttpContent        | String  | HTTPファイル認証方式のページコンテンツ値                                          |
| domain.validationHttpRedirectFrom | String | HTTPリダイレクト認証方式のリダイレクト元URL |
| domain.validationHttpRedirectTo | String | HTTPリダイレクト認証方式のリダイレクト先URL |
| domain.validationExpireDatetime       | DateTime | 検証トークンの有効期限切れ日時                                                        |
| domain.validationCompleteDatetime   | DateTime | 検証完了日時                                                           |
| domain.distributionSeq                | Integer | 連携されたCDNサービスID                                                      |
| domain.distribution                   | Object  | 連携されたCDNサービス情報                                                      |
| domain.distribution.domain            | String  | CDNサービスドメイン                                                          |
| domain.distribution.status            | String  | CDNサービスステータスコード([表] CDNステータスコードを参照)                                     |
| domain.createdAt                    | DateTime | 作成日時                                                             |
| domain.updatedAt                    | DateTime | 変更日時                                                             |

- ドメインの検証を実行する前に、DNS TXTレコードの追加またはHTTPファイル/リダイレクトの設定を先に完了する必要があります。
- 検証トークンの有効期限が切れた場合、検証を実行できません。トークン再発行APIで新しいトークンを発行した後、再度検証を進めてください。


<a id="refresh-domain-validation-status"></a>
### ドメイン検証ステータスの更新 { #refresh-domain-validation-status }

<a id="refresh-domain-validation-status-request"></a>
#### リクエスト

[URI]

| メソッド | URI                                                                    |
| ---- |------------------------------------------------------------------------|
| POST | /v3.0/appKeys/{appKey}/alias-domains/{aliasDomainDomSeq}/token/refresh |


[例]
```
curl -X POST "https://cdn.api.nhncloudservice.com/v3.0/appKeys/{appKey}/alias-domains/{aliasDomainDomSeq}/token/refresh" \
 -H "X-NHN-AUTHORIZATION: {secretKey}" \
 -H "Content-Type: application/json"
```

<a id="refresh-domain-validation-status-response"></a>
#### レスポンス

[レスポンス本文]

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

[フィールド]

| フィールド                                 | タイプ     | 説明                                                                |
| ----------------------------------- | ------- | ------------------------------------------------------------------ |
| header                              | Object  | ヘッダ領域                                                              |
| header.isSuccessful                 | Boolean | 成否                                                             |
| header.resultCode                   | Integer | 結果コード                                                             |
| header.resultMessage                | String  | 結果メッセージ                                                            |
| domain                              | Object  | ドメインエイリアスのオブジェクト                                                        |
| domain.aliasDomainDomSeq            | Integer | ドメインエイリアスID                                                          |
| domain.domain                       | String  | 登録されたドメイン                                                           |
| domain.validationStatus               | String  | 検証ステータスコード([表] ドメインエイリアスの検証ステータスコードを参照)                                   |
| domain.validationScope              | String  | 検証範囲                                 |
| domain.validationTxtName            | String  | DNS TXTレコード追加方式のレコード名                                         |
| domain.validationTxtValue           | String  | DNS TXTレコード追加方式のレコード値                                           |
| domain.validationHttpPath           | String  | HTTPファイル認証方式のHTTPページURL                                        |
| domain.validationHttpContent        | String  | HTTPファイル認証方式のページコンテンツ値                                          |
| domain.validationHttpRedirectFrom | String | HTTPリダイレクト認証方式のリダイレクト元URL |
| domain.validationHttpRedirectTo | String | HTTPリダイレクト認証方式のリダイレクト先URL |
| domain.validationExpireDatetime       | DateTime | 検証トークンの有効期限切れ日時                                                        |
| domain.validationCompleteDatetime   | DateTime | 検証完了日時                                                           |
| domain.distributionSeq                | Integer | 連携されたCDNサービスID                                                      |
| domain.distribution                   | Object  | 連携されたCDNサービス情報                                                      |
| domain.distribution.domain            | String  | CDNサービスドメイン                                                          |
| domain.distribution.status            | String  | CDNサービスステータスコード([表] CDNステータスコードを参照)                                     |
| domain.createdAt                    | DateTime | 作成日時                                                             |
| domain.updatedAt                    | DateTime | 変更日時                                                             |


<a id="reissue-validation-token"></a>
### 検証トークンの再発行 { #reissue-validation-token }

<a id="reissue-validation-token-request"></a>
#### リクエスト

[URI]

| メソッド | URI                                                                    |
| ---- |------------------------------------------------------------------------|
| POST | /v3.0/appKeys/{appKey}/alias-domains/{aliasDomainDomSeq}/token/reissue |


[例]
```
curl -X POST "https://cdn.api.nhncloudservice.com/v3.0/appKeys/{appKey}/alias-domains/{aliasDomainDomSeq}/token/reissue" \
 -H "X-NHN-AUTHORIZATION: {secretKey}" \
 -H "Content-Type: application/json"
```

<a id="reissue-validation-token-response"></a>
#### レスポンス

[レスポンス本文]

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

[フィールド]

| フィールド                                 | タイプ     | 説明                                                                |
| ----------------------------------- | ------- | ------------------------------------------------------------------ |
| header                              | Object  | ヘッダ領域                                                              |
| header.isSuccessful                 | Boolean | 成否                                                             |
| header.resultCode                   | Integer | 結果コード                                                             |
| header.resultMessage                | String  | 結果メッセージ                                                            |
| domain                              | Object  | ドメインエイリアスのオブジェクト                                                        |
| domain.aliasDomainDomSeq            | Integer | ドメインエイリアスID                                                          |
| domain.domain                       | String  | 登録されたドメイン                                                           |
| domain.validationStatus               | String  | 検証ステータスコード([表] ドメインエイリアスの検証ステータスコードを参照)                                   |
| domain.validationScope              | String  | 検証範囲                                 |
| domain.validationTxtName            | String  | DNS TXTレコード追加方式のレコード名                                         |
| domain.validationTxtValue           | String  | DNS TXTレコード追加方式のレコード値                                           |
| domain.validationHttpPath           | String  | HTTPファイル認証方式のHTTPページURL                                        |
| domain.validationHttpContent        | String  | HTTPファイル認証方式のページコンテンツ値                                          |
| domain.validationHttpRedirectFrom | String | HTTPリダイレクト認証方式のリダイレクト元URL |
| domain.validationHttpRedirectTo | String | HTTPリダイレクト認証方式のリダイレクト先URL |
| domain.validationExpireDatetime       | DateTime | 検証トークンの有効期限切れ日時                                                        |
| domain.validationCompleteDatetime   | DateTime | 検証完了日時                                                           |
| domain.distributionSeq                | Integer | 連携されたCDNサービスID                                                      |
| domain.distribution                   | Object  | 連携されたCDNサービス情報                                                      |
| domain.distribution.domain            | String  | CDNサービスドメイン                                                          |
| domain.distribution.status            | String  | CDNサービスステータスコード([表] CDNステータスコードを参照)                                     |
| domain.createdAt                    | DateTime | 作成日時                                                             |
| domain.updatedAt                    | DateTime | 変更日時                                                             |

- トークンが再発行されると、以前の検証情報は初期化され、新しいトークン情報で再度検証を進める必要があります。
- 検証トークンの有効期限が切れた(`TOKEN_EXPIRED`)場合、このAPIを呼び出して新しいトークンを発行できます。

<a id="reissue-validation-token-domain-alias-validation-status-codes"></a>
#### ドメインエイリアスの検証ステータスコード

以下はドメインエイリアスの検証ステータスを示すステータスコードであり、ドメインエイリアスの照会時に検証ステータスを確認できます。

| 値 | 説明 |
| ---------------------- | -------------------------------- |
| REQUEST_ACCEPTED       | ドメインが登録され、検証待機中                |
| VALIDATION_IN_PROGRESS | ドメイン所有権の検証が進行中 |
| VALIDATED              | ドメイン所有権の検証完了、CDNサービスの連携可能     |
| TOKEN_EXPIRED          | 検証トークンの有効期限切れ、トークンの再発行後に再度検証が必要     |


<a id="certificate-api"></a>
## 証明書API { #certificate-api }
<a id="issue-new-certificate"></a>
### 新規証明書の発行 { #issue-new-certificate }
<a id="issue-new-certificate-request"></a>
#### リクエスト

[URI]

| メソッド | URI                           |
| ---- | ----------------------------- |
| POST | /v3.0/appKeys/{appKey}/certificates|


[リクエスト本文]

```json
{
    "certificateDomain": "example.domain.com",
    "callbackHttpMethod": "POST",
    "callbackUrl": "http://test.callback.com/cdn-certificate?appKey={appKey}&status={status}&domain={domain}"   
}
```


[フィールド]

| 名前   | タイプ | 必須かどうか | デフォルト値 | 有効範囲          | 説明                                                      |
| --------- | ------ | --------- | ------ | --------------------- | ------------------------------------------------------------ |
| certificateDomain    | String | 必須      |        | 最大255文字            | 新規証明書を発行するドメイン(フルドメインアドレス形式で入力)|
| callbackHttpMethod  | String | 任意      |        | GET/POST/PUT        | 証明書作成の処理結果の通知を受け取るコールバックのHTTPメソッド |
| callbackUrl         | String | 任意      |        | 最大1024文字           | 証明書作成の処理結果の通知を受け取るコールバックURL       |

* 証明書の発行に関する詳細な内容は、[コンソール使用ガイド > 証明書管理 > 新規証明書の発行](./console-guide/#issue-new-certificates)をご参照ください。

<a id="issue-new-certificate-response"></a>
#### レスポンス

[レスポンス本文]

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


[フィールド]

| フィールド                | タイプ   | 説明     |
| -------------------- | ------- | --------- |
| header               | Object  | ヘッダ領域  |
| header.isSuccessful  | Boolean | 成否  |
| header.resultCode    | Integer | 結果コード  |
| header.resultMessage | String  | 結果メッセージ |
| certificates         | List    | 発行された証明書一覧 |
| certificates[0].sanDnsId | String | 証明書ID    |
| certificates[0].dnsName  | String | 証明書ドメイン |
| certificates[0].dnsStatus | String | 証明書発行ステータスコード([表] 証明書発行ステータスコードを参照) |
| certificates[0].callbackHttpMethod | String | 証明書作成の処理結果の通知を受け取るコールバックのHTTPメソッド |
| certificates[0].callbackUrl | String | 証明書作成の処理結果の通知を受け取るコールバックURL |
| certificates[0].createDatetime | DateTime | 証明書作成日時 |
| certificates[0].updateDatetime | DateTime | 証明書変更日時 |
| certificates[0].hasCname | Boolean | CNAMEレコード設定の有無 |
| certificates[0].hasDistributionDomain | Boolean | CDNサービス連携の有無 |
| certificates[0].renewalStartDate | DateTime | 証明書の更新開始日時 |
| certificates[0].renewalEndDate | DateTime | 証明書の更新終了日時 |

<a id="list-certificates"></a>
### 証明書一覧の照会 { #list-certificates }
<a id="list-certificates-request"></a>
#### リクエスト

[URI]

| メソッド | URI                           |
| ---- | ----------------------------- |
| GET | /v3.0/appKeys/{appKey}/certificates|


<a id="list-certificates-response"></a>
#### レスポンス

[レスポンス本文]

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


[フィールド]

| フィールド                | タイプ   | 説明     |
| -------------------- | ------- | --------- |
| header               | Object  | ヘッダ領域  |
| header.isSuccessful  | Boolean | 成否  |
| header.resultCode    | Integer | 結果コード  |
| header.resultMessage | String  | 結果メッセージ |
| certificates         | List    | 発行された証明書一覧 |
| certificates[0].sanDnsId | String | 証明書ID    |
| certificates[0].dnsName  | String | 証明書ドメイン |
| certificates[0].dnsStatus | String | 証明書発行ステータスコード([表] 証明書発行ステータスコードを参照) |
| certificates[0].callbackHttpMethod | String | 証明書作成の処理結果の通知を受け取るコールバックのHTTPメソッド |
| certificates[0].callbackUrl | String | 証明書作成の処理結果の通知を受け取るコールバックURL |
| certificates[0].createDatetime | DateTime | 証明書作成日時 |
| certificates[0].updateDatetime | DateTime | 証明書変更日時 |
| certificates[0].hasCname | Boolean | CNAMEレコード設定の有無 |
| certificates[0].hasDistributionDomain | Boolean | CDNサービス連携の有無 |
| certificates[0].renewalStartDate | DateTime | 証明書の更新開始日時 |
| certificates[0].renewalEndDate | DateTime | 証明書の更新終了日時 |

<a id="delete-certificate"></a>
### 証明書の削除 { #delete-certificate }
<a id="delete-certificate-request"></a>
#### リクエスト

[URI]

| メソッド | URI                           |
| ---- | ----------------------------- |
| DELETE | /v3.0/appKeys/{appKey}/certificates|


[パラメータ]

| 名前   | タイプ   | 必須かどうか | 有効範囲      | 説明                         |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| dnsIdList | String | 必須      |     | 削除する証明書ID(sanDnsId)の一覧(カンマ(,)で区切られた証明書ID一覧)   |

[例]
```
curl -X DELETE "https://cdn.api.nhncloudservice.com/v3.0/appKeys/{appKey}/certificates?dnsIdList={dnsIdList}" \
 -H "X-NHN-AUTHORIZATION: {secretKey}" \
 -H "Content-Type: application/json"
```

<a id="delete-certificate-response"></a>
#### レスポンス

[レスポンス本文]

```json
{
    "header" : {
        "resultCode" :  0,
        "resultMessage" :  "SUCCESS",
        "isSuccessful" :  true
    }
}
```


[フィールド]

| フィールド                | タイプ   | 説明     |
| -------------------- | ------- | --------- |
| header               | Object  | ヘッダ領域  |
| header.isSuccessful  | Boolean | 成否  |
| header.resultCode    | Integer | 結果コード  |
| header.resultMessage | String  | 結果メッセージ |


<a id="callback-response"></a>
## コールバックレスポンス { #callback-response }
<a id="cdn-service"></a>
### CDNサービス { #cdn-service }
CDNサービスにコールバック機能が設定されている場合、作成、修正、一時停止、再開、削除の変更が完了すると、設定されたコールバックURLを呼び出します。
コールバック呼び出し時、リクエスト本文には次のようなCDNサービスの設定情報が含まれます。

[レスポンス本文]
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

[フィールド]

| フィールド                           | タイプ | 説明                                                 |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | ヘッダ領域                                            |
| header.isSuccessful                    | Boolean | 成否                                             |
| header.resultCode                      | Integer | 結果コード                                            |
| header.resultMessage                   | String  | 結果メッセージ                                          |
| distribution                          | Object    | 変更作業が完了したCDNオブジェクト                              |
| distribution.appKey | String | アプリキー |
| distribution.domain                | String  | ドメイン(サービス名)                                     |
| distribution.domainAlias | List | ドメインエイリアス一覧(個人または会社が所有するドメインを使用) |
| distribution.region | String | サービス地域("GLOBAL": グローバル) |
| distribution.status | String | CDNステータスコード([表] CDNステータスコードを参照) |
| distribution.defaultMaxAge | Integer | キャッシュの有効期限(秒) |
| distribution.cacheKeyQueryParam | String | キャッシュキーにリクエストクエリ文字列を含めるかどうかの設定("INCLUDE_ALL": 全て含む、"EXCLUDE_ALL": 全て含まない) |
| distribution.referrerType | String | リファラーアクセスの管理("BLACKLIST": ブラックリスト、"WHITELIST": ホワイトリスト) |
| distribution.referrers | List | 正規表現形式のリファラーヘッダ一覧 |
| distribution.useOriginCacheControl | Boolean | オリジンサーバーの設定を使用するかどうか (true: オリジンサーバーの設定を使用、false: ユーザー設定を使用) |
| distribution.createTime            | DateTime | 作成日時                                    |
| distribution.deleteTime            | DateTime | 削除日時                                    |
| distribution.origins | List | オリジンサーバーオブジェクト一覧 |
| distribution.origins[0].origin     | String  | オリジンサーバー(ドメインまたはIP)                                      |
| distribution.origins[0].originPath | String | オリジンサーバーのサブパス |
| distribution.origins[0].httpPort | Integer | オリジンサーバーのHTTPプロトコルポート |
| distribution.origins[0].httpsPort | Integer | オリジンサーバーのHTTPSプロトコルポート |
| distribution.useOriginHttpProtocolDowngrade | Boolean | オリジンサーバーがHTTPレスポンスのみ可能な場合、CDNサーバーからオリジンサーバーへのリクエスト時にHTTPSリクエストをHTTPリクエストにダウングレードする設定を使用するかどうか |
| distribution.forwardHostHeader | String | CDNサーバーがオリジンサーバーにコンテンツをリクエストする際に送信するホストヘッダの設定("ORIGIN_HOSTNAME": オリジンサーバーのホスト名に設定、"REQUEST_HOST_HEADER": クライアントリクエストのホストヘッダに設定) |
| distribution.rootPathAccessControl | Object | CDNサービスのルートパスに対するアクセス制御の設定 |
| distribution.rootPathAccessControl.enable | Boolean | ルートパスに対するアクセス制御を使用するかどうか (true: 使用、false: 未使用) |
| distribution.rootPathAccessControl.controlType | String | enableがtrueの場合に必須入力。ルートパスに対するアクセス制御方式("DENY": アクセス拒否、"REDIRECT": 指定したパスへリダイレクト) |
| distribution.rootPathAccessControl.redirectPath | String | controlTypeが"REDIRECT"の場合に必須入力。ルートパスへのリクエストをリダイレクトするパス(/を含むパスで入力します。) |
| distribution.rootPathAccessControl.redirectStatusCode | Integer | controlTypeが"REDIRECT"の場合に必須入力。リダイレクト時に送信されるHTTPレスポンスコード |
| distribution.modifyOutgoingResponseHeaderControl | Object | CDNから返されるHTTPレスポンスヘッダを追加/変更/削除する設定 |
| distribution.modifyOutgoingResponseHeaderControl.enable | Boolean | HTTPレスポンスヘッダを追加/変更/削除する設定を使用するかどうか (true: 使用、false: 未使用) |
| distribution.modifyOutgoingResponseHeaderControl.headerList | List | HTTPレスポンスヘッダ一覧 |
| distribution.modifyOutgoingResponseHeaderControl.headerList[0].action | String | HTTPレスポンスヘッダの変更方式 |
| distribution.modifyOutgoingResponseHeaderControl.headerList[0].standardHeaderName | String | 一般的なHTTPレスポンスヘッダ名 |
| distribution.modifyOutgoingResponseHeaderControl.headerList[0].customHeaderName | String | standardHeaderNameが"OTHER"の場合に必須入力。ユーザー定義のHTTPレスポンスヘッダ名 |
| distribution.modifyOutgoingResponseHeaderControl.headerList[0].headerValue | String  | HTTPレスポンスヘッダ値 |
| distribution.callback | Object | サービスのデプロイ処理結果の通知を受け取るコールバック |
| distribution.callback.httpMethod   | String  | コールバックのHTTPメソッド                                          |
| distribution.callback.url          | String  | コールバックURL                                                     |

<a id="certificate"></a>
### 証明書 { #certificate }
証明書の発行リクエスト時にコールバック情報が設定されている場合、ドメイン検証/ドメイン検証完了/証明書発行完了にステータス変更が完了すると、設定されたコールバックURLを呼び出します。
コールバック呼び出し時、リクエスト本文には次のような証明書の設定情報が含まれます。

[レスポンス本文]
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

[フィールド]

| フィールド                           | タイプ | 説明                                                 |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | ヘッダ領域                                            |
| header.isSuccessful                    | Boolean | 成否                                             |
| header.resultCode                      | Integer | 結果コード                                            |
| header.resultMessage                   | String  | 結果メッセージ                                          |
| certificate | Object | 変更作業が完了した証明書のオブジェクト |
| certificate.sanDnsId                   | String    | 証明書ID                                  |
| certificate.distributionSeq | String | 連携されたCDNサービスID |
| certificate.dnsName  | String | 証明書ドメイン |
| certificate.dnsStatus | String | 証明書発行ステータスコード([表] 証明書発行ステータスコードを参照) |
| certificate.validationDnsRecordName | String | ドメイン検証情報(DNS TXTレコード追加方式のレコード名)  |
| certificate.validationDnsToken | String | ドメイン検証情報(DNS TXTレコード追加方式のレコード値)  |
| certificate.validationHtmlUrl | String | ドメイン検証情報(HTTPページ追加方式のHTTPページURL)  |
| certificate.validationHtmlToken | String | ドメイン検証情報(HTTPページ追加方式のHTTPページ本文のコンテンツ値) |
| certificate.validationExpireDatetime | DateTime | ドメイン検証の有効期限切れ日時 |
| certificate.createDatetime | DateTime | 証明書の作成日時 |
| certificate.updateDatetime | DateTime | 証明書の変更日時 |
| certificate.deleteDatetime | DateTime | 証明書の削除日時 |
| certificate.callbackHttpMethod | String | 証明書作成の処理結果の通知を受け取るコールバックのHTTPメソッド |
| certificate.callbackUrl | String | 証明書作成の処理結果の通知を受け取るコールバックURL |
