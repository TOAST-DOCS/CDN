## Content Delivery > CDN > API v2.0ガイド

NHN Cloud CDNで提供するPublic API v2.0について説明します。

## API共通情報

### ドメイン

| 名前       | ドメイン                            |
| --------------- | ------------------------------------- |
| CDN Public APIドメイン | https://kr1-cdn.api.nhncloudservice.com |

### 事前準備

APIを使用するにはアプリキー(Appkey)とセキュリティキー(SecretKey)が必要です。
アプリキーとセキュリティキーは、コンソール右上の**URL & Appkey**メニューで確認できます。

### リクエスト共通情報

#### リクエストヘッダ

| 名前     | 説明                 |
| ------------- | ------------------------- |
| Authorization | コンソールで発行されたセキュリティキー(SecretKey) |

#### Pathパラメータ

すべてのAPIは、pathパラメータにアプリキーを指定する必要があります。
* 例) /v2.0/appKeys/**{appKey}**/distributions

| 名前 | 説明             |
| ------ | --------------------- |
| appKey | コンソールで発行されたアプリキー(Appkey) | 

### レスポンス共通情報

#### ヘッダ

すべてのAPIリクエストに対して、**200 OK**でレスポンスします。詳細なレスポンス結果は、次の例のようなレスポンス本文のヘッダを参照してください。

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Success"
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

#### CDN状態コード

次はCDNサービスの状態を表す状態コードです。サービスの照会時にサービスの状態を確認できます。

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

#### 証明書発行ステータスコード

以下はドメインの証明書発行ステータスを表すステータスコードで、証明書の照会時に発行ステータスを確認できます。

| 値   | 説明               |
| ---------- | ------------------------ |
| PENDING_NEW        | 証明書新規発行が要求され処理待機中 |
| PENDING_CANCEL     | 証明書の発行がキャンセル要求され、ドメイン検証キャンセル処理待機中 |
| PENDING_DELETE     | 発行された証明書が削除要求され、処理待機中 |
| PENDING_EXPIRE     | 発行された証明書が期限切れになり、期限切れ処理待機中 |
| VALIDATED          | ドメイン検証完了               |
| DEPLOYED           | 証明書の配布完了               |
| WAITING_VALIDATION | ドメイン検証待機中            |
| CANCELED           | ドメイン検証キャンセル完了           |
| DELETED            | ドメイン証明書の削除完了         |
| EXPIRED            | ドメイン証明書の期限切れ              |

## サービスAPI

### サービス作成

#### リクエスト


[URI]

| メソッド | URI                                  |
| ---- | ------------------------------------ |
| POST | /v2.0/appKeys/{appKey}/distributions |


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
          "url": "http://test.callback.com/cdn?=appKey={appKey}&status={status}&domain={domain}"
      }
    }
  ]
}
```

[フィールド]

| 名前                                                                                   | タイプ     | 必須か | デフォルト値        | 有効範囲                                                                | 説明                                                                                                                       |
|---------------------------------------------------------------------------------------|---------|-------|-------------|-----------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| distributions                                                                         | List    | 必須   |             |                                                                       | 作成するCDNのオブジェクトリスト                                                                                                         |
| distributions[0].useOriginHttpProtocolDowngrade                                       | Boolean | 必須   | Boolean       | true/false                                                            | オリジンサーバーがHTTPレスポンスのみ可能な場合、CDNサーバーからオリジンサーバーにリクエストする時、HTTPSリクエストからHTTPリクエストにダウングレードするための設定を使用するかどうか                                     |
| distributions[0].forwardHostHeader                                                    | String  | 必須   |             | ORIGIN_HOSTNAME<br/>REQUEST_HOST_HEADER                               | CDNサーバーがオリジンサーバーにコンテンツをリクエストする時、伝達するホストヘッダ設定("ORIGIN_HOSTNAME"：オリジンサーバーのホスト名で設定、"REQUEST_HOST_HEADER"：クライアントリクエストのホストヘッダで設定) |
| distributions[0].useOriginCacheControl                                                | Boolean | 任意   |             | true/false                                                            | キャッシュ期限設定(true：オリジンサーバー設定を使用、false：ユーザー設定を使用)。 useOriginCacheControlまたはcacheTypeのいずれかを必ず入力する必要があります。                      |
| distributions[0].cacheType                                                            | String  | 任意   |             | BYPASS, NO_STORE                                                      | キャッシュタイプ設定。 useOriginCacheControlまたはcacheTypeのいずれかを必ず入力する必要があります。                                                           |
| distributions[0].referrerType                                                         | String  | 必須   |             | BLACKLIST/WHITELIST                                                   | リファラーアクセス管理("BLACKLIST":ブラックリスト、 "WHITELIST":ホワイトリスト)                                                                        |
| distributions[0].referrers                                                            | List    | 任意   |             |                                                                       | 正規表現形式のリファラーヘッダリスト                                                                                                     |
| distributions[0].isAllowWhenEmptyReferrer                                             | Boolean | 任意   | true        | true/false                                                            | リファラーヘッダがない場合、コンテンツアクセス許可(true)/拒否(false)                                                                                |
| distributions[0].isAllowPost                                                          | Boolean | 任意   | false       | true/false                                                            | POSTメソッド許可(true)/拒否(false)                                                                                            |
| distributions[0].isAllowPut                                                           | Boolean | 任意   | false       | true/false                                                            | PUTメソッド許可(true)/拒否(false)                                                                                             |
| distributions[0].isAllowPatch                                                         | Boolean | 任意   | false       | true/false                                                            | PATCHメソッド許可(true)/拒否(false)                                                                                           |
| distributions[0].isAllowDelete                                                        | Boolean | 任意   | false       | true/false                                                            | DELETEメソッド許可(true)/拒否(false)                                                                                          |
| distributions[0].useLargeFileOptimization                                             | Boolean | 任意   | false       | true/false                                                            | 大容量ファイル最適化設定の使用有無                                                                                                       |
| distributions[0].description                                                          | String  | 任意   |             | 最大255文字                                                              | 説明                                                                                                                       |
| distributions[0].domainAlias                                                          | List    | 任意   |             |                                                                       | ドメインエイリアスリスト(個人または会社が所有しているドメインを使用)                                                                                           |
| distributions[0].defaultMaxAge                                                        | Integer | 任意   | 0           | 0~2,147,483,647                                                       | キャッシュ満了時間(秒)、デフォルト値0は604,800秒です。                                                                                          |
| distributions[0].cacheKeyQueryParam                                                   | String  | 任意   | INCLUDE_ALL | INCLUDE_ALL/EXCLUDE_ALL                                               | キャッシュキーにリクエストクエリ文字列を含めるかの設定("INCLUDE_ALL"：全て含める、"EXCLUDE_ALL"：全て含めない)                                                     |
| distributions[0].origins                                                              | List    | 必須   |             |                                                                       | オリジンサーバーオブジェクトリスト                                                                                                            |
| distributions[0].origins[0].origin                                                    | String  | 必須   |             | 最大255文字                                                              | オリジンサーバー(ドメインまたはIP)                                                                                                          |
| distributions[0].origins[0].originPath                                                | String  | 任意   |             | 最大8192文字                                                             | オリジンサーバーの下層パス(/を含むパスで入力してください。)                                                                                         |
| distributions[0].origins[0].httpPort                                                  | Integer | 任意   |             | [コンソール使用ガイド > オリジンサーバー](./console-guide/#_2)の「[表2]使用可能なオリジンサーバーポート番号」参照 | オリジンサーバーHTTPプロトコルポート(origins[0].httpPortとorigins[0].httpsPortのいずれか1つは必ず入力する必要があります。)                                         |
| distributions[0].origins[0].httpsPort                                                 | Integer | 任意   |             | [コンソール使用ガイド > オリジンサーバー](./console-guide/#_2)の「[表2]使用可能なオリジンサーバーポート番号」参照 | オリジンサーバーHTTPSプロトコルポート(origins[0].httpPortとorigins[0].httpsPortのいずれか1つは必ず入力する必要があります。)                                        |
| distributions[0].rootPathAccessControl                                                | Object  | 任意   |             |                                                                       | CDNサービスのルートパスに対するアクセス制御設定                                                                                              | 
| distributions[0].rootPathAccessControl.enable                                         | Boolean | 必須   | true        | true/false                                                            | ルートパスのアクセス制御使用(true)/未使用(false)                                                                                    |
| distributions[0].rootPathAccessControl.controlType                                    | String  | 任意   |             | DENY, REDIRECT                                                        | enableがtrueの場合は必須入力。ルートパスへのアクセス制御方式("DENY"：アクセス拒否、"REDIRECT"：指定したパスへリダイレクトリダイレクト)                                      | 
| distributions[0].rootPathAccessControl.redirectPath                                   | String  | 任意   |             |                                                                       | controlTypeが"REDIRECT"の場合は必須入力。ルートパスへのリクエストをリダイレクトするパス(/を含めたパスで入力してください。)                                           |
| distributions[0].rootPathAccessControl.redirectStatusCode                             | Integer | 任意   |             | 301, 302, 303, 307                                                    | controlTypeが"REDIRECT"の場合は必須入力。リダイレクトする時に伝達されるHTTPレスポンスコード                                                                |
| distributions[0].modifyOutgoingResponseHeaderControl                                  | Object  | 任意   |             |                                                                       | CDNからレスポンスするHTTPヘッダを追加/変更/削除する設定                                                                                        |
| distributions[0].modifyOutgoingResponseHeaderControl.enable                           | Boolean | 必須   | true        | true/false                                                            | HTTPレスポンスヘッダを追加/変更/削除する設定を使用するか。使用(true)/未使用(false)                                                                          |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList                       | List    | 任意   |         |                                                                       | HTTPレスポンスヘッダリスト                                                                                                            |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].action             | String  | 任意   |         | ADD, MODIFY, DELETE                                                   | HTTPレスポンスヘッダ変更方式                                                                                                         |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].standardHeaderName | String  | 任意   |         | ACCESS_CONTROL_ALLOW_CREDENTIALS<br/>ACCESS_CONTROL_ALLOW_HEADERS<br/>ACCESS_CONTROL_ALLOW_METHODS<br/>ACCESS_CONTROL_ALLOW_ORIGIN<br/>ACCESS_CONTROL_EXPOSE_HEADERS<br/>ACCESS_CONTROL_MAX_AGE<br/>CACHE_CONTROL<br/>CONTENT_DISPOSITION<br/>CONTENT_TYPE<br/>P3P<br/>PRAGMA<br/>OTHER | 一般HTTPレスポンスヘッダ名                                                                                                         |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].customHeaderName   | String  | 任意   |         |                                                      | standardHeaderNameが"OTHER"の場合は必須入力。ユーザー定義HTTPレスポンスヘッダ名                                                              |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].headerValue        | String  | 必須   |         |                                                      | HTTPレスポンスヘッダ値                                                                                                             |
| distributions[0].callback                                                             | Object  | 任意   |             |                                                                       | CDN作成処理結果の通知を受けるコールバックURL(コールバック設定は任意入力です。)                                                                               |
| distributions[0].callback.httpMethod                                                  | String  | 必須   |             | GET/POST/PUT                                                          | コールバックのHTTPメソッド                                                                                                             |
| distributions[0].callback.url                                                         | String  | 必須   |             | 最大1024文字                                                             | コールバックURL                                                                                                                    |

- forwardHostHeaderのデフォルト値は、domainAliasを設定した場合はREQUEST_HOST_HEADERで、未設定の場合はORIGIN_HOSTNAMEです。


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
                "url": "http://test.callback.com/cdn?=appKey={appKey}&status={status}&domain={domain}"
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
| distributions                          | List    | 作成されたCDNオブジェクトリスト                                |
| distributions[0].domain                | String  | 作成されたドメイン(サービス)名                                |
| distributions[0].domainAlias           | List    | ドメインエイリアスリスト(個人または会社が所有しているドメインを使用)            |
| distributions[0].region                | String  | サービス地域("GLOBAL":グローバル)          |
| distributions[0].description           | String  | 説明                                                      |
| distributions[0].status                | String  | CDNステータスコード([表] CDNステータスコード参考)                               |
| distributions[0].defaultMaxAge         | Integer | キャッシュ満了時間(秒)                                         |
| distributions[0].cacheKeyQueryParam    | String  | キャッシュキーにリクエストクエリ文字列を含めるかの設定("INCLUDE_ALL"：全て含める、"EXCLUDE_ALL"：全て含めない) |
| distributions[0].referrerType          | String  | リファラーアクセス管理("BLACKLIST"：ブラックリスト、"WHITELIST"：ホワイトリスト) |
| distributions[0].referrers             | List    | 正規表現形式のリファラーヘッダリスト                             |
| distributions[0].isAllowWhenEmptyReferrer | Boolean | リファラーヘッダがない場合のコンテンツアクセス許可(true)/拒否(false) |
| distributions[0].isAllowPost | Boolean | POSTメソッド許可(true)/拒否(false)           |
| distributions[0].isAllowPut | Boolean | PUTメソッド許可(true)/拒否(false)           |
| distributions[0].isAllowPatch | Boolean | PATCHメソッド許可(true)/拒否(false)           |
| distributions[0].isAllowDelete | Boolean | DELETEメソッド許可(true)/拒否(false)           |
| distributions[0].useLargeFileOptimization | Boolean | 大容量ファイル最適化設定の使用有無   |
| distributions[0].useOriginCacheControl | Boolean | オリジンサーバー設定を使用するか(true：オリジンサーバー設定を使用、 false：ユーザー設定を使用) |
| distributions[0].cacheType             | String  | キャッシュタイプ設定                                       |
| distributions[0].origins               | List    | オリジンサーバーオブジェクトリスト                                   |
| distributions[0].origins[0].origin     | String  | オリジンサーバー(ドメインまたはIP)                                    |
| distributions[0].origins[0].originPath | String  | オリジンサーバーの下層パス                                       |
| distributions[0].origins[0].httpPort   | Integer | オリジンサーバーHTTPプロトコルポート                                            |
| distributions[0].origins[0].httpsPort  | Integer | オリジンサーバーHTTPSプロトコルポート                                            |
| distributions[0].useOriginHttpProtocolDowngrade | Boolean | オリジンサーバーがHTTPレスポンスのみ可能な場合、CDNサーバーからオリジンサーバーにリクエストする時、HTTPSリクエストからHTTPリクエストにダウングレードするための設定を使用するか |
| distributions[0].forwardHostHeader     | String  | CDNサーバーがオリジンサーバーにコンテンツをリクエストする時、伝達するホストヘッダ設定("ORIGIN_HOSTNAME"：オリジンサーバーのホスト名で設定、"REQUEST_HOST_HEADER"：クライアントリクエストのホストヘッダで設定 |
| distributions[0].rootPathAccessControl  | Object  | CDNサービスのルートパスに対するアクセス制御設定 | 
| distributions[0].rootPathAccessControl.enable | Boolean | ルートパスに対するアクセス制御使用(true)/未使用(false)          |
| distributions[0].rootPathAccessControl.controlType  | String  | enableがtrueの場合は必須入力。ルートパスに対するアクセス制御方式("DENY"：アクセス拒否、"REDIRECT"：指定したパスにリダイレクト) | 
| distributions[0].rootPathAccessControl.redirectPath | String | controlTypeが"REDIRECT"の場合は必須入力。ルートパスに対するリクエストをリダイレクトするパス(/を含めたパスで入力してください。)      |
| distributions[0].rootPathAccessControl.redirectStatusCode | Integer | controlTypeが"REDIRECT"の場合は必須入力。リダイレクトする時に伝達されるHTTPレスポンスコード       |
| distributions[0].modifyOutgoingResponseHeaderControl                                  | Object  | CDNからレスポンスするHTTPヘッダを追加/変更/削除する設定 |
| distributions[0].modifyOutgoingResponseHeaderControl.enable                           | Boolean | HTTPレスポンスヘッダを追加/変更/削除する設定を使用するか。使用(true)/未使用(false)  |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList                       | List    | HTTPレスポンスヘッダリスト |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].action             | String  | HTTPレスポンスヘッダ変更方式 |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].standardHeaderName | String  | 一般HTTPレスポンスヘッダ名 |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].customHeaderName   | String  | standardHeaderNameが"OTHER"の場合は必須入力。ユーザー定義HTTPレスポンスヘッダ名 |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].headerValue        | String  | HTTPレスポンスヘッダ値 |
| distributions[0].callback              | Object  | サービス作成処理結果の通知を受けるコールバック                     |
| distributions[0].callback.httpMethod   | String  | コールバックのHTTPメソッド                                        |
| distributions[0].callback.url          | String  | コールバックURL                                                   |



### サービス照会

#### リクエスト


[URI]

| メソッド | URI                                  |
| ---- | ------------------------------------ |
| GET  | /v2.0/appKeys/{appKey}/distributions |


[パラメータ]

| 名前 | タイプ | 必須か | 有効範囲 | 説明                 |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| domain | String | 任意  | 最大255文字 | 照会するドメイン(サービス名)   |
| status | String | 任意  | CDN状態コード | CDN状態コード([表] CDN状態コード参照) |

[例]
```
curl -X GET "https://kr1-cdn.api.nhncloudservice.com/v2.0/appKeys/{appKey}/distributions?domain={domain}" \
 -H "Authorization: {secretKey}" \
 -H "Content-Type: application/json"
```

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


[フィールド]

| フィールド                           | タイプ | 説明                                                 |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | ヘッダ領域                                            |
| header.isSuccessful                    | Boolean | 成否                                             |
| header.resultCode                      | Integer | 結果コード                                            |
| header.resultMessage                   | String  | 結果メッセージ                                          |
| distributions                          | List    | 作成されたCDNオブジェクトリスト                             |
| distributions[0].domain                | String  | ドメイン名(サービス名)                                     |
| distributions[0].domainAlias           | List  | ドメインエイリアスリスト(個人または会社が所有しているドメインを使用)                                                  |
| distributions[0].region                | String  | サービス地域("GLOBAL"：グローバル)             |
| distributions[0].status                | String  | CDN状態コード([表] CDN状態コード参照)                                 |
| distributions[0].defaultMaxAge         | Integer  | キャッシュ満了時間(秒)                                           |
| distributions[0].cacheKeyQueryParam    | String  | キャッシュキーにリクエストクエリ文字列を含めるかの設定("INCLUDE_ALL"：全て含める、"EXCLUDE_ALL"：全て含めない) |
| distributions[0].referrerType          | String  | リファラーアクセス管理("BLACKLIST"：ブラックリスト、"WHITELIST"：ホワイトリスト) |
| distributions[0].referrers             | List    | 正規表現形式のリファラーヘッダリスト                            |
| distributions[0].isAllowWhenEmptyReferrer | Boolean | リファラーヘッダがない場合のコンテンツアクセス許可(true)/拒否(false) |
| distributions[0].isAllowPost          | Boolean | POSTメソッド許可(true)/拒否(false)            |
| distributions[0].isAllowPut           | Boolean | PUTメソッド許可(true)/拒否(false)            |
| distributions[0].isAllowPatch         | Boolean | PATCHメソッド許可(true)/拒否(false)            |
| distributions[0].isAllowDelete        | Boolean | DELETEメソッド許可(true)/拒否(false)            |
| distributions[0].useLargeFileOptimization | Boolean | 大容量ファイル最適化設定使用有無   |
| distributions[0].useOriginCacheControl | Boolean | オリジンサーバー設定を使用するか(true：オリジンサーバー設定を使用、 false：ユーザー設定を使用) |
| distributions[0].cacheType             | String  | キャッシュタイプ設定                                       |
| distributions[0].origins               | List    | オリジンサーバーオブジェクトリスト                              |
| distributions[0].origins[0].origin     | String  | オリジンサーバー(ドメインまたはIP)                                      |
| distributions[0].origins[0].originPath | String  | オリジンサーバーの下層パス                                  |
| distributions[0].origins[0].httpPort   | Integer | オリジンサーバーHTTPプロトコルポート                             |
| distributions[0].origins[0].httpsPort  | Integer | オリジンサーバーHTTPSプロトコルポート                            |
| distributions[0].forwardHostHeader     | String  | サービス配布処理結果を受け取るコールバック                   |
| distributions[0].useOriginHttpProtocolDowngrade | Boolean | オリジンサーバーがHTTPレスポンスのみ可能な場合、CDNサーバーからオリジンサーバーにリクエストする時、HTTPSリクエストからHTTPリクエストにダウングレードするための設定を使用するか |
| distributions[0].forwardHostHeader     | String  | CDNサーバーがオリジンサーバーにコンテンツをリクエストする時、伝達するホストヘッダ設定("ORIGIN_HOSTNAME"：オリジンサーバーのホスト名で設定、"REQUEST_HOST_HEADER"：クライアントリクエストのホストヘッダで設定 |
| distributions[0].rootPathAccessControl  | Object  | CDNサービスのルートパスに対するアクセス制御設定 | 
| distributions[0].rootPathAccessControl.enable | Boolean | ルートパスに対するアクセス制御使用(true)/未使用(false)          |
| distributions[0].rootPathAccessControl.controlType  | String  | enableがtrueの場合は必須入力。ルートパスに対するアクセス制御方式("DENY"：アクセス拒否、"REDIRECT"：指定したパスにリダイレクト) | 
| distributions[0].rootPathAccessControl.redirectPath | String | controlTypeが"REDIRECT"の場合は必須入力。ルートパスに対するリクエストをリダイレクトするパス(/を含めたパスで入力してください。)        |
| distributions[0].rootPathAccessControl.redirectStatusCode | Integer | controlTypeが"REDIRECT"の場合は必須入力。リダイレクトする時に伝達されるHTTPレスポンスコード        |
| distributions[0].modifyOutgoingResponseHeaderControl                                  | Object  | CDNからレスポンスするHTTPヘッダを追加/変更/削除する設定 |
| distributions[0].modifyOutgoingResponseHeaderControl.enable                           | Boolean | HTTPレスポンスヘッダを追加/変更/削除する設定を使用するか。使用(true)/未使用(false)  |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList                       | List    | HTTPレスポンスヘッダリスト |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].action             | String  | HTTPレスポンスヘッダ変更方式 |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].standardHeaderName | String  | 一般HTTPレスポンスヘッダ名 |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].customHeaderName   | String  | standardHeaderNameが"OTHER"の場合は必須入力。ユーザー定義HTTPレスポンスヘッダ名 |
| distributions[0].modifyOutgoingResponseHeaderControl.headerList[0].headerValue        | String  | HTTPレスポンスヘッダ値 |
| distributions[0].callback              | Object  | サービス配布処理結果の通知を受けるコールバック                |
| distributions[0].callback.httpMethod   | String  | コールバックのHTTPメソッド                                          |
| distributions[0].callback.url          | String  | コールバックURL                                                     |


### サービス修正

#### リクエスト


[URI]

| メソッド | URI                                  |
| ---- | ------------------------------------ |
| PUT  | /v2.0/appKeys/{appKey}/distributions |


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
                  "headerValue": "custom-header-value"
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


[フィールド]

| 名前          | タイプ | 必須か | デフォルト値 | 有効範囲                                            | 説明                                                 |
| --------------------- | ------- | --------- | ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| domain                | String  | 必須  |        | 最大255文字                                            | 修正するドメイン(サービス名)                                   |
| useOriginCacheControl | Boolean | 選択   |        | true/false                                                        | キャッシュ期限設定(true：オリジンサーバー設定を使用、 false：ユーザー設定を使用). useOriginCacheControlまたはcacheTypeのいずれかを必ず入力する必要があります。      |
| cacheType             | String  | 選択   |        | BYPASS, NO_STORE            | キャッシュタイプ設定。 useOriginCacheControlまたはcacheTypeのいずれかを必ず入力する必要があります。                                          |
| referrerType          | String  | 必須  |        | BLACKLIST/WHITELIST                                          | リファラーアクセス管理("BLACKLIST"：ブラックリスト、"WHITELIST"：ホワイトリスト) |
| referrers             | List    | 任意 |        |                                                              | 正規表現形式のリファラーヘッダリスト |
| isAllowWhenEmptyReferrer | Boolean | 任意     | true      | true/false             | リファラーヘッダがない場合、コンテンツアクセス許可(true)/拒否(false)             |
| isAllowPost           | Boolean | 選択   | false      | true/false             | POSTメソッド許可(true)/拒否(false)            |
| isAllowPut            | Boolean | 選択   | false      | true/false             | PUTメソッド許可(true)/拒否(false)            |
| isAllowPatch          | Boolean | 選択   | false      | true/false             | PATCHメソッド許可(true)/拒否(false)            |
| isAllowDelete         | Boolean | 選択   | false      | true/false             | DELETEメソッド許可(true)/拒否(false)            |
| useLargeFileOptimization | Boolean | 選択 | false      | true/false             | 大容量ファイル最適化設定使用有無   |
| description           | String  | 任意  |        | 最大255文字                                            | 説明                                                 |
| domainAlias           | List    | 任意 |        | 最大255文字                                              | ドメインエイリアス(個人または会社が所有しているドメインを使用) |
| defaultMaxAge         | Integer | 任意 | 0      | 0～2,147,483,647                                            | キャッシュ満了時間(秒)、デフォルト値0は604,800秒です。              |
| cacheKeyQueryParam    | String  | 選択   | INCLUDE_ALL | INCLUDE_ALL/EXCLUDE_ALL                               | キャッシュキーにリクエストクエリ文字列を含めるかの設定("INCLUDE_ALL"：全て含める、"EXCLUDE_ALL"：全て含めない) |
| origins               | List    | 必須  |        |                                                              | オリジンサーバー                                            |
| origins[0].origin     | String  | 必須  |        | 最大255文字                                            | オリジンサーバー(ドメインまたはIP)                                      |
| origins[0].originPath | String  | 任意  |        | 最大8192文字                                           | オリジンサーバーの下層パス                                  |
| origins[0].httpPort   | Integer  | 任意    |        |[コンソール使用ガイド > オリジンサーバー](./console-guide/#_2)の「[表2]使用可能なオリジンサーバーポート番号」参照 | オリジンサーバーHTTPプロトコルポート(origins[0].httpPortとorigins[0].httpsPortのいずれか1つは必ず入力する必要があります。)  |
| origins[0].httpsPort  | Integer  | 任意    |        |[コンソール使用ガイド > オリジンサーバー](./console-guide/#_2)の「[表2]使用可能なオリジンサーバーポート番号」参照 | オリジンサーバーHTTPSプロトコルポート(origins[0].httpPortとorigins[0].httpsPortのいずれか1つは必ず入力する必要があります。) |
| useOriginHttpProtocolDowngrade | Boolean  | 必須 | true/false       |          | オリジンサーバーがHTTPレスポンスのみ可能な場合、CDNサーバーからオリジンサーバーにリクエストする時、HTTPSリクエストからHTTPリクエストにダウングレードするための設定を使用するか |
| forwardHostHeader     | String  | 必須 |        | ORIGIN_HOSTNAME<br/>REQUEST_HOST_HEADER   | CDNサーバーがオリジンサーバーにコンテンツをリクエストする時、伝達するホストヘッダ設定("ORIGIN_HOSTNAME"：オリジンサーバーのホスト名で設定、"REQUEST_HOST_HEADER"：クライアントリクエストのホストヘッダで設定 |
| rootPathAccessControl  | Object  | 任意 |  |  | CDNサービスのルートパスに対するアクセス制御設定 | 
| rootPathAccessControl.enable | Boolean | 必須 | false | true/false | ルートパスに対するアクセス制御使用(true)/未使用(false)          |
| rootPathAccessControl.controlType  | String  | 任意 |  | DENY, REDIRECT | enableがtrueの場合は必須入力。ルートパスに対するアクセス制御方式("DENY"：アクセス拒否、"REDIRECT"：指定したパスにリダイレクト) | 
| rootPathAccessControl.redirectPath | String | 任意 |  | | controlTypeが"REDIRECT"の場合は必須入力。ルートパスに対するリクエストをリダイレクトするパス(/を含めたパスで入力してください。)        |
| rootPathAccessControl.redirectStatusCode | Integer | 任意 | | 301, 302, 303, 307 |controlTypeが"REDIRECT"の場合は必須入力。リダイレクトする時に伝達されるHTTPレスポンスコード        |
| modifyOutgoingResponseHeaderControl                                  | Object  | 任意   |             |                                                                       | CDNからレスポンスするHTTPヘッダを追加/変更/削除する設定                                                                                        |
| modifyOutgoingResponseHeaderControl.enable                           | Boolean | 必須   | true        | true/false                                                            | HTTPレスポンスヘッダを追加/変更/削除する設定を使用するか。使用(true)/未使用(false)                                                                          |
| modifyOutgoingResponseHeaderControl.headerList                       | List    | 任意   |         |                                                                       | HTTPレスポンスヘッダリスト                                                                                                            |
| modifyOutgoingResponseHeaderControl.headerList[0].action             | String  | 任意   |         | ADD, MODIFY, DELETE                                                   | HTTPレスポンスヘッダ変更方式                                                                                                         |
| modifyOutgoingResponseHeaderControl.headerList[0].standardHeaderName | String  | 任意   |         | ACCESS_CONTROL_ALLOW_CREDENTIALS<br/>ACCESS_CONTROL_ALLOW_HEADERS<br/>ACCESS_CONTROL_ALLOW_METHODS<br/>ACCESS_CONTROL_ALLOW_ORIGIN<br/>ACCESS_CONTROL_EXPOSE_HEADERS<br/>ACCESS_CONTROL_MAX_AGE<br/>CACHE_CONTROL<br/>CONTENT_DISPOSITION<br/>CONTENT_TYPE<br/>P3P<br/>PRAGMA<br/>OTHER | 一般HTTPレスポンスヘッダ名                                                                                                         |
| modifyOutgoingResponseHeaderControl.headerList[0].customHeaderName   | String  | 任意   |         |                                                      | standardHeaderNameが"OTHER"の場合は必須入力。ユーザー定義HTTPレスポンスヘッダ名                                                              |
| modifyOutgoingResponseHeaderControl.headerList[0].headerValue        | String  | 必須   |         |                                                      | HTTPレスポンスヘッダ値                                                                                                             |
| callback              | Object  | 任意  |        | CDNサービス配布結果の通知を受けるコールバックURL(コールバック設定は任意入力です。) |                                                              |
| callback.httpMethod   | String  | 必須  |        | GET/POST/PUT                                                 | コールバックのHTTPメソッド                                          |
| callback.url          | String  | 必須 |        | 最大1024文字                                             | コールバックURL                                                     |

- forwardHostHeaderのデフォルト値は、domainAliasを設定した場合はREQUEST_HOST_HEADERで、未設定の場合はORIGIN_HOSTNAMEです。

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

### サービス削除

#### リクエスト


[URI]

| メソッド | URI                                  |
| ------ | ------------------------------------ |
| DELETE | /v2.0/appKeys/{appKey}/distributions |


[リクエスト本文]

```json
{
    "domains" : [
        "lhcsxuo0.toastcdn.net"
    ]
}
```


[フィールド]

| 名前 | タイプ | 必須か | デフォルト値 | 有効範囲 | 説明            |
| ------- | ------ | ----- | ---- | ----- | --------------------- |
| domains | String | 必須 |      |       | 削除するドメイン、複数ドメイン入力可 |

**\* 複数のドメイン入力時、該当サービスはすべて終了します。**

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


## Auth Token API

### Auth Token作成

#### リクエスト

[URI]

| メソッド | URI                           |
| ---- | ----------------------------- |
| POST | /v2.0/appKeys/{appKey}/auth-token |


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
| encryptKey    | String | 必須 |        |             | NHN Cloud CDNコンソールに表示されたAuth Token認証制御管理 > トークン暗号化キー |
| durationSeconds | Integer | 必須 |        | 0～2,147,483,647 | 作成されたトークンが有効な時間(秒) |
| singlePath      | String | 任意 |        |             | 作成されたトークンを利用してアクセスする単一パス |
| singleWildcardPath | String | 任意 |     |             | 作成されたトークンを利用してアクセスする単一ワイルドカードパス |
| multipleWildcardPath | String | 任意 |   |             | 作成されたトークンを利用してアクセスする複数のワイルドカードパス |
| sessionId |           String | 任意 |    | 文字列の長さ最大36バイト        | 単一アクセスリクエストに対してsessionIdを含めてトークンを作成 |

* singlePath、singleWildcardPath、multipleWildcardPathのうち1つ以上の値が必ず存在する必要があります。
* トークン作成および使用についての詳細は[コンソール使用ガイド > Auth Token認証アクセス管理 > 2. トークン作成](./console-guide/#auth-token)を参照してください。


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
| authToken.singlePathToken | String    | 単一パスにアクセスできるように作成された認証トークン                              |
| authToken.singleWildcardPathToken | String    | 単一ワイルドカードパスにアクセスできるように作成された認証トークン              |
| authToken.multipleWildcardPathToken | String  | 複数のワイルドカードパスにアクセスできるように作成された認証トークン          |

## キャッシュ再配布API

### キャッシュ再配布(Purge) -  ITEM(特定ファイルタイプ)

#### リクエスト

[URI]

| メソッド | URI                           |
| ---- | ----------------------------- |
| POST | /v2.0/appKeys/{appKey}/purge/item |


[リクエスト本文]

```json
{
	"domain": "sample.toastcdn.net",
	"purgeList":["http://sample.toastcdn.net/img_01.png",
  "http://sample.toastcdn.net/img_02.png"]
}
```


[フィールド]

| 名前 | タイプ | 必須か | デフォルト値 | 有効範囲     | 説明                                                 |
| --------- | ------ | --------- | ------ | --------------------- | ------------------------------------------------------------ |
| domain    | String | 必須  |        | 最大255文字     | 再配布するドメイン(サービス)名                         |
| purgeList | List | 必須 |        |                       | 再配布対象URLリスト |

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

| フィールド           | タイプ | 説明 |
| -------------------- | ------- | --------- |
| header               | Object  | ヘッダ領域 |
| header.isSuccessful  | Boolean | 成否 |
| header.resultCode    | Integer | 結果コード |
| header.resultMessage | String  | 結果メッセージ |

### キャッシュ再配布(Purge) -  ALL(全体ファイルタイプ)

#### リクエスト

[URI]

| メソッド | URI                           |
| ---- | ----------------------------- |
| POST | /v2.0/appKeys/{appKey}/purge/all |


[リクエスト本文]

```json
{
	"domain": "sample.toastcdn.net"
}
```


[フィールド]

| 名前 | タイプ | 必須か | デフォルト値 | 有効範囲     | 説明                                                 |
| --------- | ------ | --------- | ------ | --------------------- | ------------------------------------------------------------ |
| domain    | String | 必須  |        | 最大255文字     | 再配布するドメイン(サービス)名                         |

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

| フィールド           | タイプ | 説明 |
| -------------------- | ------- | --------- |
| header               | Object  | ヘッダ領域 |
| header.isSuccessful  | Boolean | 成否 |
| header.resultCode    | Integer | 結果コード |
| header.resultMessage | String  | 結果メッセージ |

- CDNサービスを新規で作成した後、約1時間はキャッシュ再配布リクエストが失敗する場合もあります。その後も失敗が続く場合はサポートへお問い合わせください。
- パージAPI使用量制限ポリシーがあります。詳細な内容は[コンソール使用ガイド > CDNキャッシュ再配布](./console-guide/#cdnpurge)のキャッシュ再配布使用量制限]を確認してください。

### キャッシュ再配布(Purge)照会
- API v2.0を通してキャッシュを再配布する時、高速キャッシュ再配布が実行され、リクエスト後、数秒以内に完了するため、キャッシュ再配布状態を照会するAPIが別途提供されません。

## 証明書API
### 新規証明書の発行
#### リクエスト

[URI]

| メソッド | URI                           |
| ---- | ----------------------------- |
| POST | /v2.0/appKeys/{appKey}/certificates|


[リクエスト本文]

```json
{
    "certificateDomain": "example.domain.com",
    "callbackHttpMethod": "POST",
    "callbackUrl": "http://test.callback.com/cdn-certificate?appKey={appKey}&status={status}&domain={domain}"   
}
```


[フィールド]

| 名前 | タイプ | 必須かどうか | デフォルト | 有効範囲       | 説明                                                   |
| --------- | ------ | --------- | ------ | --------------------- | ------------------------------------------------------------ |
| certificateDomain    | String | 必須 |        | 最大255文字       | 新規証明書を発行したいドメイン(フルドメインアドレス形式で入力)|
| callbackHttpMethod  | String | 任意 |        | GET/POST/PUT        | 証明書作成処理結果の通知を受けるコールバックのHTTPメソッド |
| callbackUrl         | String | 任意 |        | 最大1024文字      | 証明書作成処理結果の通知を受けるコールバックURL       |

* 証明書発行の詳細については、[コンソール使用ガイド > 証明書管理 > 新規証明書の発行](./console-guide/#_7)を参照してください。

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

| フィールド             | タイプ | 説明  |
| -------------------- | ------- | --------- |
| header               | Object  | ヘッダ領域  |
| header.isSuccessful  | Boolean | 成否  |
| header.resultCode    | Integer | 結果コード  |
| header.resultMessage | String  | 結果メッセージ |
| certificates         | List    | 発行された証明書リスト |
| certificates[0].sanDnsId | String | 証明書ID    |
| certificates[0].dnsName  | String | 証明書ドメイン |
| certificates[0].dnsStatus | String | 証明書発行ステータスコード([表]証明書発行ステータスコード参考) |
| certificates[0].callbackHttpMethod | String | 証明書作成処理結果の通知を受けるコールバックのHTTPメソッド |
| certificates[0].callbackUrl | String | 証明書作成処理結果の通知を受けるコールバックURL |
| certificates[0].createDatetime | DateTime | 証明書作成日時 |
| certificates[0].updateDatetime | DateTime | 証明書変更日時 |
| certificates[0].hasCname | Boolean | CNAMEレコードを設定有無 |
| certificates[0].hasDistributionDomain | Boolean | CDNサービス連動を行うかどうか |
| certificates[0].renewalStartDate | DateTime | 証明書更新開始日時 |
| certificates[0].renewalEndDate | DateTime | 証明書更新終了日時 |

### 証明書リスト照会
#### リクエスト

[URI]

| メソッド | URI                           |
| ---- | ----------------------------- |
| GET | /v2.0/appKeys/{appKey}/certificates|


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

| フィールド             | タイプ | 説明  |
| -------------------- | ------- | --------- |
| header               | Object  | ヘッダ領域 |
| header.isSuccessful  | Boolean | 成否   |
| header.resultCode    | Integer | 結果コード |
| header.resultMessage | String  | 結果メッセージ |
| certificates         | List    | 発行された証明書リスト |
| certificates[0].sanDnsId | String | 証明書ID    |
| certificates[0].dnsName  | String | 証明書ドメイン |
| certificates[0].dnsStatus | String | 証明書発行ステータスコード([表]証明書発行ステータスコード参考) |
| certificates[0].callbackHttpMethod | String | 証明書作成処理結果の通知を受けるコールバックのHTTPメソッド |
| certificates[0].callbackUrl | String | 証明書作成処理結果の通知を受けるコールバックURL |
| certificates[0].createDatetime | DateTime | 証明書作成日時 |
| certificates[0].updateDatetime | DateTime | 証明書変更日時 |
| certificates[0].hasCname | Boolean | CNAMEレコード設定を行うかどうか |
| certificates[0].hasDistributionDomain | Boolean | CDNサービス連動を行うかどうか |
| certificates[0].renewalStartDate | DateTime | 証明書更新開始日時 |
| certificates[0].renewalEndDate | DateTime | 証明書更新終了日時 |

### 証明書の削除
#### 要請

[URI]

| メソッド | URI                           |
| ---- | ----------------------------- |
| DELETE | /v2.0/appKeys/{appKey}/certificates|


[パラメータ]

| 名前 | タイプ | 必須かどうか | 有効範囲 | 説明                   |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| dnsIdList | String | 必須 |     | 削除する証明書ID(sanDnsId)リスト(、で区切られた証明書IDリスト)   |

[例]
```
curl -X GET "https://kr1-cdn.api.nhncloudservice.com/v2.0/appKeys/{appKey}/certificates?dnsIdList={dnsIdList}" \
 -H "Authorization: {secretKey}" \
 -H "Content-Type: application/json"
```

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

| フィールド             | タイプ | 説明  |
| -------------------- | ------- | --------- |
| header               | Object  | ヘッダ領域 |
| header.isSuccessful  | Boolean | 成否   |
| header.resultCode    | Integer | 結果コード |
| header.resultMessage | String  | 結果メッセージ |

## 統計API
### トラフィック統計照会
#### リクエスト

[URI]

| メソッド | URI                           |
| ---- | ----------------------------- |
| GET | /v2.0/appKeys/{appKey}/statistics/traffic|


[パラメータ]

| 名前 | タイプ | 必須かどうか | 有効範囲 | 説明                   |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| domain | String | 必須 | 最大255文字 | 照会するドメイン(サービス名)   |
| fromDate | DateTime | 必須 |  | 統計照会開始日時 |
| toDate | DateTime | 必須 |  | 統計照会終了日時 |

- fromDate, toDateフィールドは過去90日まで照会できます。
- fromDate, toDateフィールドはISO 8601形式の日付文字列形式で入力します。
  - UTC表記：yyyy-MM-dd'T'HH:mm:ssZ
  - UTC基準タイムオフセット表記：yyyy-MM-dd'T'HH:mm:ss±hh:mm

[例]
```
curl -X GET "https://kr1-cdn.api.nhncloudservice.com/v2.0/appKeys/{appKey}/statistics/traffic?domain={domain}&fromDate={fromDate}&toDate={toDate}" \
 -H "Authorization: {secretKey}" \
 -H "Content-Type: application/json"
```

#### レスポンス

[レスポンス本文]

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


[フィールド]

| フィールド             | タイプ | 説明  |
| -------------------- | ------- | --------- |
| header               | Object  | ヘッダ領域 |
| header.isSuccessful  | Boolean | 成否   |
| header.resultCode    | Integer | 結果コード |
| header.resultMessage | String  | 結果メッセージ |
| statistics         | List    | トラフィック統計データリスト |
| statistics[0].dateTime | DateTime | 統計時間 |
| statistics[0].bandwidth  | String | 統計時間の帯域幅(Mbps)  |
| statistics[0].transferred | String | 統計時間の転送量(bytes) |

### HTTPステータスコード別の統計照会
#### リクエスト

[URI]

| メソッド | URI                           |
| ---- | ----------------------------- |
| GET | /v2.0/appKeys/{appKey}/statistics/http|


[パラメータ]

| 名前 | タイプ | 必須かどうか | 有効範囲 | 説明                   |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| domain | String | 必須 | 最大255文字 | 照会するドメイン(サービス名)   |
| fromDate | DateTime | 必須 |  | 統計照会開始日時 |
| toDate | DateTime | 必須 |  | 統計照会終了日時 |

- fromDate, toDateフィールドは過去90日まで照会できます。
- fromDate, toDateフィールドはISO 8601形式の日付文字列形式で入力します。
  - UTC表記：yyyy-MM-dd'T'HH:mm:ssZ
  - UTC基準タイムオフセット表記：yyyy-MM-dd'T'HH:mm:ss±hh:mm

[例]
```
curl -X GET "https://kr1-cdn.api.nhncloudservice.com/v2.0/appKeys/{appKey}/statistics/http?domain={domain}&fromDate={fromDate}&toDate={toDate}" \
 -H "Authorization: {secretKey}" \
 -H "Content-Type: application/json"
```

#### レスポンス

[レスポンス本文]

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


[フィールド]

| フィールド             | タイプ | 説明  |
| -------------------- | ------- | --------- |
| header               | Object  | ヘッダ領域 |
| header.isSuccessful  | Boolean | 成否   |
| header.resultCode    | Integer | 結果コード |
| header.resultMessage | String  | 結果メッセージ |
| statistics         | List    | トラフィック統計データリスト |
| statistics[0].dateTime | DateTime | 統計時間 |
| statistics[0].successHits  | Long | レスポンスHTTPステータスコードが2xxの呼び出し数 |
| statistics[0].notModifiedHits | Long | レスポンスHTTPステータスコードが304の呼び出し数 |
| statistics[0].redirectsHits | Long | レスポンスHTTPステータスコードが301、302の呼び出し数 |
| statistics[0].notFoundHits | Long | レスポンスHTTPステータスコードが404の呼び出し数 |
| statistics[0].permissionHits | Long | レスポンスHTTPステータスコードが401、403、415の呼び出し数 |
| statistics[0].serverErrorHits | Long | レスポンスHTTPステータスコードが5xxの呼び出し数 |
| statistics[0].etcHits | Long | 2xx、3xx、4xx、5xx以外のレスポンスHTTPステータスコードAPI呼び出し数 |

### ダウンロードが最も多いコンテンツの順位統計
#### リクエスト

[URI]

| メソッド | URI                           |
| ---- | ----------------------------- |
| GET | /v2.0/appKeys/{appKey}/statistics/topcontent|


[パラメータ]

| 名前 | タイプ | 必須かどうか | 有効範囲 | 説明                   |
| ------ | ------ | --------- | ------------- | ---------------------------- |
| domain | String | 必須 | 最大255文字 | 照会するドメイン(サービス名)   |
| fromDate | DateTime | 必須 |  | 統計照会開始日時 |
| toDate | DateTime | 必須 |  | 統計照会終了日時 |

- fromDate, toDateフィールドは過去90日まで照会できます。
- fromDate, toDateフィールドはISO 8601形式の日付文字列形式で入力します。
  - UTC表記：yyyy-MM-dd'T'HH:mm:ssZ
  - UTC基準タイムオフセット表記：yyyy-MM-dd'T'HH:mm:ss±hh:mm

[例]
```
curl -X GET "https://kr1-cdn.api.nhncloudservice.com/v2.0/appKeys/{appKey}/statistics/topcontent?domain={domain}&fromDate={fromDate}&toDate={toDate}" \
 -H "Authorization: {secretKey}" \
 -H "Content-Type: application/json"
```

#### レスポンス

[レスポンス本文]

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


[フィールド]

| フィールド             | タイプ | 説明  |
| -------------------- | ------- | --------- |
| header               | Object  | ヘッダ領域 |
| header.isSuccessful  | Boolean | 成否   |
| header.resultCode    | Integer | 結果コード |
| header.resultMessage | String  | 結果メッセージ |
| statistics         | List    | トラフィック統計データリスト |
| statistics[0].rank | Integer | 統計時間 |
| statistics[0].successHits  | Long | レスポンスHTTPステータスコードが2xxの呼び出し数 |
| statistics[0].succDataTransferred  | Long | レスポンスHTTPステータスコードが2xxの呼び出しトラフィック転送量(MBytes)  |

## コールバックレスポンス
### CDNサービス
CDNサービスにコールバック機能が設定されている場合、作成、修正、一時停止、再開、削除変更の完了時に設定されたコールバックURLを呼び出します。
コールバック呼び出し時、リクエスト本文には次のようなCDNサービス設定情報が含まれます。

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
                  "headerValue": "custom-header-value"
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
| distribution.appKey                   | String    | アプリケーションキー                             |
| distribution.domain                | String  | ドメイン名(サービス名)                                     |
| distribution.domainAlias           | List  | ドメインエイリアスリスト(個人または会社が所有しているドメインを使用)                                 |
| distribution.region                | String  | サービス地域("GLOBAL"：グローバル)             |
| distribution.status                | String  | CDNステータスコード([表] CDNステータスコード参照)                                 |
| distribution.defaultMaxAge         | Integer  | キャッシュ満了時間(秒)                                           |
| distribution.cacheKeyQueryParam    | String  | キャッシュキーにリクエストクエリ文字列を含めるかの設定("INCLUDE_ALL"：全て含める、"EXCLUDE_ALL"：全て含めない) |
| distribution.referrerType          | String  | リファラーアクセス管理("BLACKLIST"：ブラックリスト、"WHITELIST"：ホワイトリスト) |
| distribution.referrers             | List    | 正規表現形式のリファラーヘッダリスト                            |
| distribution.useOriginCacheControl | Boolean | オリジンサーバー設定を使用するか(true：オリジンサーバー設定を使用、 false：ユーザー設定を使用) |
| distribution.createTime            | DateTime | 作成日時                                    |
| distribution.deleteTime            | DateTime | 削除日時                                    |
| distribution.origins               | List    | オリジンサーバーオブジェクトリスト                                 |
| distribution.origins[0].origin     | String  | オリジンサーバー(ドメインまたはIP)                                      |
| distribution.origins[0].originPath | String  | オリジンサーバー下層パス                                     |
| distribution.origins[0].httpPort   | Integer | オリジンサーバーHTTPプロトコルポート                                          |
| distribution.origins[0].httpsPort  | Integer | オリジンサーバーHTTPSプロトコルポート                                          |
| distribution.forwardHostHeader     | String  | サービス配布処理結果を受け取るコールバック                   |
| distribution.useOriginHttpProtocolDowngrade | Boolean | オリジンサーバーがHTTPレスポンスのみ可能な場合、CDNサーバーからオリジンサーバーにリクエストする時、HTTPSリクエストからHTTPリクエストにダウングレードするための設定を使用するか |
| distribution.forwardHostHeader     | String  | CDNサーバーがオリジンサーバーにコンテンツをリクエストする時に伝達するホストヘッダ設定("ORIGIN_HOSTNAME"：オリジンサーバーのホスト名で設定、"REQUEST_HOST_HEADER"：クライアントリクエストのホストヘッダで設定 |
| distribution.rootPathAccessControl  | Object  | CDNサービスのルートパスに対するアクセス制御設定 | 
| distribution.rootPathAccessControl.enable | Boolean | ルートパスに対するアクセス制御使用(true)/未使用(false)          |
| distribution.rootPathAccessControl.controlType  | String  | enableがtrueの場合は必須入力。ルートパスに対するアクセス制御方式("DENY"：アクセス拒否、"REDIRECT"：指定したパスにリダイレクト) | 
| distribution.rootPathAccessControl.redirectPath | String | controlTypeが"REDIRECT"の場合は必須入力。ルートパスに対するリクエストをリダイレクトするパス(/を含めたパスで入力してください。)        |
| distribution.rootPathAccessControl.redirectStatusCode | Integer | controlTypeが"REDIRECT"の場合は必須入力。リダイレクトする時に伝達されるHTTPレスポンスコード       |
| distribution.modifyOutgoingResponseHeaderControl                      | Object  | CDNからレスポンスするHTTPヘッダを追加/変更/削除する設定 |
| distribution.modifyOutgoingResponseHeaderControl.enable               | Boolean | HTTPレスポンスヘッダを追加/変更/削除する設定を使用するか。使用(true)/未使用(false)  |
| distribution.modifyOutgoingResponseHeaderControl.headerList           | List    | HTTPレスポンスヘッダリスト |
| distribution.modifyOutgoingResponseHeaderControl.headerList[0].action | String  | HTTPレスポンスヘッダ変更方式 |
| distribution.modifyOutgoingResponseHeaderControl.headerList[0].standardHeaderName | String  | 一般HTTPレスポンスヘッダ名 |
| distribution.modifyOutgoingResponseHeaderControl.headerList[0].customHeaderName | String  | standardHeaderNameが"OTHER"の場合は必須入力。ユーザー定義HTTPレスポンスヘッダ名 |
| distribution.modifyOutgoingResponseHeaderControl.headerList[0].headerValue | String  | HTTPレスポンスヘッダ値 |
| distribution.callback              | Object  | サービス配布処理結果を受け取るコールバック                   |
| distribution.callback.httpMethod   | String  | コールバックのHTTPメソッド                                          |
| distribution.callback.url          | String  | コールバックURL                                                     |

### 証明書
証明書発行リクエスト時、コールバック情報が設定されている場合、ドメイン検証/ドメイン検証完了/証明書発行完了にステータス変更が完了すると、設定されたコールバックURLを呼び出します。
コールバック呼び出し時、リクエスト本文には次のような証明書設定情報が含まれます。

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

| フィールド                             | タイプ | 説明                                                   |
| -------------------------------------- | ------- | ------------------------------------------------------------ |
| header                                 | Object  | ヘッダ領域                                                |
| header.isSuccessful                    | Boolean | 成否                                                  |
| header.resultCode                      | Integer | 結果コード                                              |
| header.resultMessage                   | String  | 結果メッセージ                                            |
| certificate                          | Object    | 変更作業が完了した証明書オブジェクト                                |
| certificate.sanDnsId                   | String    | 証明書ID                                  |
| certificate.distributionSeq                   | String    | 連動したCDNサービスID                                  |
| certificate.dnsName  | String | 証明書ドメイン |
| certificate.dnsStatus | String | 証明書発行ステータスコード([表]証明書発行ステータスコード参考) |
| certificate.validationDnsRecordName | String | ドメイン検証情報(DNS TXTレコード追加方式のレコード名)  |
| certificate.validationDnsToken | String | ドメイン検証情報(DNS TXTレコード追加方式のレコード値)  |
| certificate.validationHtmlUrl | String | ドメイン検証情報(HTTPページ追加方式のHTTPページURL)  |
| certificate.validationHtmlToken | String | ドメイン検証情報(HTTPページ追加方式のHTTPページ本文コンテンツ値)  |
| certificate.validationExpireDatetime | DateTime | ドメイン検証の有効期限 |
| certificate.createDatetime | DateTime | 証明書の作成日時 |
| certificate.updateDatetime | DateTime | 証明書の変更日時 |
| certificate.deleteDatetime | DateTime | 証明書の削除日時 |
| certificate.callbackHttpMethod | String | 証明書作成処理結果の通知を受けるコールバックのHTTPメソッド |
| certificate.callbackUrl | String | 証明書作成処理結果の通知を受けるコールバックURL |
