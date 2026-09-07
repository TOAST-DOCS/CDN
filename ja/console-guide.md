<!-- pre-align:aligned sig=9deb1697d05f -->

<a id="content-delivery-cdn-console-user-guide"></a>
## Content Delivery > CDN > コンソール使用ガイド { #content-delivery-cdn-console-user-guide }

このドキュメントでは、NHN Cloud CDNコンソールでCDNサービスを構成し、利用する方法を説明します。

<a id="creating-cdn-service"></a>
## CDNサービスの作成 { #creating-cdn-service }

**Contents Delivery > CDN**の**CDNサービス**タブで**作成**ボタンをクリックすると、**CDNサービスの作成**ダイアログボックスが表示されます。
CDNサービスドメインは[サービスID].toastcdn.net形式で自動生成されます。所有しているドメインをサービスドメインとして利用する場合は、**ドメインエイリアス**(domain alias)機能を利用できます。
作成をリクエストしてからサービスのデプロイが完了するまで、最大2時間かかります。デプロイが完了した後にサービスを利用できます。

> [参考] CDN初回作成時のダウンロード最適化にかかる期間
> CDNの初回作成後、最大3日間はダウンロード速度が多少遅くなる場合があります。

<a id="basic-information"></a>
### 基本情報 { #basic-information }
基本情報を設定します。
![CDNサービス作成-基本情報](https://static.toastoven.net/prod_cdn/v2/console-cdn-create-default2_202403.png)

<a id="basic-information-description"></a>
#### 説明
CDNサービスの説明を追加します。

<a id="basic-information-domain-alias"></a>
#### ドメインエイリアス
TOAST CDNは、既定で[サービスID].toastcdn.net形式のサービスドメインアドレスを提供しています。
既定のサービスドメインアドレスではなく、所有するドメインでCDNサービスを利用するには、**ドメインエイリアス**で設定します。
所有するドメインでHTTPSプロトコルサービスを利用するには、まず**証明書管理**タブで証明書を発行してからドメインエイリアスを設定する必要があります。
ドメインエイリアスを設定した後は、ドメインのDNSサービスプロバイダーでCNAMEレコードを次のように登録する必要があります。DNSの設定については、DNSサービスプロバイダーにお問い合わせください。
- レコードタイプ: `CNAME`
- レコード名: `[ドメインエイリアスに登録したドメイン]`
- レコード値(Rdata): `[サービスID].toastcdn.net`
- TTL: 任意の値

<a id="basic-information-callback"></a>
#### コールバック
CDNサービスの作成と変更操作(変更、一時停止/再開、削除)には数時間かかります。
操作が完了した後、設定したコールバックURLで変更状態とCDN設定情報を受信するには、コールバックを設定してください。コールバックで配信される情報については、[APIガイド](./api-guide-v2.0/#cdn-service)をご参照ください。
- **HTTP Method**と**コールバックURL**を入力します。
- Query ParameterでCDNサービスの変更操作結果を受信するには、**コールバックURL**に次のパス(path)変数を含めて入力してください。
  例: `http://callback.url?appKey={appKey}&status={status}&isSuccessful={isSuccessful})`

| パス(path)変数 | 説明 | 値の例 |
| ------------- | --- | ------- |
| {appKey} | CDNサービスのアプリキー | コンソールで発行したアプリキー |
| {domain} | CDNサービス名 | [サービスID].toastcdn.net |
| {status} | 現在のCDNサービス状態 | OPEN, SUSPEND, CLOSE, ERROR |
| {isSuccessful} | サービス変更操作の成否(API v1.0はサポートしていません) | "true" または "false" |

<a id="origin"></a>
### オリジンサーバー { #origin }
CDNサービスで配布するオリジナルファイルを提供するサーバーを設定します。
![CDNサービス作成-基本情報](https://static.toastoven.net/prod_cdn/v2/console-cdn-create-origin2_202403.png)

<a id="origin-type"></a>
#### オリジンタイプ
- Object Storage: NHN Cloud Object Storageサービスで作成したコンテナをオリジンサーバーとして設定します。
    - リージョン: Object Storageのコンテナ情報を照会するリージョンを選択します。
    - 名前: オリジンサーバーとして設定するコンテナの名前を入力します。コンテナのアクセス制御が`PUBLIC`であるコンテナのみをオリジンサーバーとして使用できます。コンテナがオリジンサーバーとして使用可能な場合、オリジンサーバーとオリジンパスにコンテナ情報が自動的に入力されます。
- Instance: NHN Cloud Instanceサービスで作成したインスタンスをオリジンサーバーとして設定します。
    - リージョン: インスタンス一覧を照会するリージョンを選択します。
    - インスタンス: リージョンを選択し、照会されたインスタンス一覧からオリジンサーバーとして設定するインスタンスを選択します。選択したインスタンスのIPがオリジンサーバーに自動的に入力され、使用するオリジンサーバーのポートは手動で入力する必要があります。フローティングIPが紐付けられたインスタンスのみをオリジンサーバーとして使用できます。
- 直接入力: 別途稼働中のオリジンサーバーを設定します。

<a id="origin-server"></a>
#### オリジンサーバー
オリジンサーバーは、CDNサービスで配布するオリジナルファイルを提供するサーバーです。オリジンサーバーはIPv4または全体ドメインアドレス(FQDN, fully qualified domain name)形式で入力できます。IPアドレスは変更される可能性が高いため、ドメインで設定することを推奨します。
稼働中のオリジンサーバーがない場合は、**オリジンタイプ**の**Instance**を選択してNHN Cloud Instanceサービスのインスタンスを使用するか、**Object Storage**を選択してNHN Cloud Object Storageサービスのコンテナを利用できます。
CDNサービスドメインでセキュア通信(HTTPS)をサポートするには、オリジンサーバーがHTTPS応答をサポートしている必要があります。
これは、オリジンサーバーにNHN Cloud CDNが信頼する証明書がインストールされている必要があるという意味です。
信頼する証明書については、次の表をご参照ください。
もし、オリジンサーバーがHTTPS応答をサポートできない場合は、**オリジンリクエストのHTTPプロトコルダウングレード**設定を利用してください。
ただし、**オリジンリクエストのHTTPプロトコルダウングレード**には制約事項があるため、オリジンサーバーがHTTPSプロトコルをサポートすることをおすすめします。

[表 1] 信頼する証明書一覧

| Common name| Expire Date |SHA-1 Fingerprint |
|---|---|---|
|SecureTrust CA|1.Jan.30|8782c6c304353bcfd29692d2593e7d44d934ff11|
|Entrust.net Certification Authority (2048)|24.Jul.29|503006091d97d4f5ae39f7cbe7927d7d652d3431|
|DigiCert Global Root CA|10.Nov.31|a8985d3a65e5e5c4b2d7d66d40c6dd2fb19c5436|
||30.Sep.23|36b12b49f9819ed74c9ebc380fc6568f5dacb2f7|
|QuoVadis Root CA 2 G3|13.Jan.42|093c61f38b8bdc7d55df7538020500e125f5c836|
|thawte Primary Root CA|17.Jul.36|91c6d6ee3e8ac86384e548c299295c756c817b81|
|Go Daddy Root Certificate Authority - G2|1.Jan.38|47beabc922eae80e78783462a79f45c254fde68b|
|GeoTrust Primary Certification Authority|17.Jul.36|323c118e1bf7b8b65254e2e2100dd6029037f096|
|VeriSign Class 3 Public Primary Certification Authority - G4|19.Jan.38|22d5d8df8f0231d18df79db7cf8a2d64c93f6c3a|
|Entrust Root Certification Authority|28.Nov.26|b31eb1b740e36c8402dadc37d44df5d4674952f9|
||29.May.29|5f3b8cf2f810b37d78b4ceec1919c37334b9c774|
|AffirmTrust Commercial|31.Dec.30|f9b5b632455f9cbeec575f80dce96e2cc7b278b7|
|Amazon Root CA 4|26.May.40|f6108407d6f8bb67980cc2e244c2ebae1cef63be|
|Certum CA|11.Jun.27|6252dc40f71143a22fde9ef7348e064251b18118|
|DST Root CA X3|30.Sep.21|dac9024f54d8f6df94935fb1732638ca6ad77c13|
|TC TrustCenter Class 2 CA II|1.Jan.26|ae5083ed7cf45cbc8f61c621fe685d794221156e|
|SwissSign Gold CA - G2|25.Oct.36|d8c5388ab7301b1b6ed47ae645253a6f9f1a2761|
|USERTrust ECC Certification Authority|19.Jan.38|d1cbca5db2d52a7f693b674de5f05a1d0c957df0|
|QuoVadis Root CA 2|25.Nov.31|ca3afbcf1240364b44b216208880483919937cf7|
|COMODO ECC Certification Authority|19.Jan.38|9f744e9f2b4dbaec0f312c50b6563b8e2d93c311|
|USERTrust RSA Certification Authority|19.Jan.38|2b8f1b57330dbba2d07a6c51f70ee90ddab9ad8e|
|ISRG Root X1|4.Jun.35|cabd2a79a1076a31f21d253635cb039d4329a5e8|
|DigiCert High Assurance EV Root CA|10.Nov.31|5fb7ee0633e259dbad0c4c9ae6d38f1a61c7dc25|
|VeriSign Class 3 Public Primary Certification Authority - G5|17.Jul.36|4eb6d578499b1ccf5f581ead56be3d9b6744a5e5|
|GlobalSign|15.Dec.21|75e0abb6138512271c04f85fddde38e4b7242efe|
|QuoVadis Root CA 3|25.Nov.31|1f4914f7d874951dddae02c0befd3a2d82755185|
|GlobalSign|18.Mar.29|d69b561148f01c77c54578c10926df5b856976ad|
|Starfield Services Root Certificate Authority - G2|1.Jan.38|925a8f8d2c6d04e0665f596aff22d863e8256f3f|
|Baltimore CyberTrust Root|13.May.25|d4de20d05e66fc53fe1a50882c78db2852cae474|
|AAA Certificate Services|1.Jan.29|d1eb23a46d17d68fd92564c2f1f1601764d8e349|
|Amazon Root CA 3|26.May.40|0d44dd8c3c8c1a1a58756481e90f2e2affb3d26e|
|VeriSign Class 3 Public Primary Certification Authority - G3|17.Jul.36|132d0d45534b6997cdb2d5c339e25576609b5cc6|
|GlobalSign Root CA|28.Jan.28|b1bc968bd4f49d622aa89a81f2150152a41d829c|
|Actalis Authentication Root CA|22.Sep.30|f373b387065a28848af2f34ace192bddc78e9cac|
|AffirmTrust Networking|31.Dec.30|293621028b20ed02f566c532d1d6ed909f45002f|
|AffirmTrust Premium|31.Dec.40|d8a6332ce0036fb185f6634f7d6a066526322827|
|QuoVadis Root Certification Authority|18.Mar.21|de3f40bd5093d39b6c60f6dabc076201008976c9|
||6.Jun.37|feb8c432dcf9769aceae3dd8908ffd288665647d|
|GeoTrust Primary Certification Authority - G3|2.Dec.37|039eedb80be7a03c6953893b20d2d9323a4c2afd|
|thawte Primary Root CA - G2|19.Jan.38|aadbbc22238fc401a127bb38ddf41ddb089ef012|
|VeriSign Universal Root Certification Authority|2.Dec.37|3679ca35668772304d30a5fb873b0fa77bb70d54|
|Cybertrust Global Root|15.Dec.21|5f43e5b1bff8788cac1cc7ca4a9ac6222bcc34c6|
|Global Chambersign Root|1.Oct.37|339b6b1450249b557a01877284d9e02fc3d2d8e9|
|SwissSign Silver CA - G2|25.Oct.36|9baae59f56ee21cb435abe2593dfa7f040d11dcb|
|Amazon Root CA 1|17.Jan.38|8da7f965ec5efc37910f1c6e59fdc1cc6a6ede16|
|Entrust Root Certification Authority - G2|8.Dec.30|8cf427fd790c3ad166068de81e57efbb932272d4|
|Amazon Root CA 2|26.May.40|5a8cef45d7a69859767a8c8b4496b578cf474b1a|
|DigiCert Assured ID Root CA|10.Nov.31|0563b8630d62d75abbc8ab1e4bdfb5a899b24d43|
||30.Jun.34|2796bae63f1801e277261ba0d77770028f20eee4|
|COMODO Certification Authority|1.Jan.30|6631bf9ef74f9eb6c9d5a60cba6abed1f7bdef7b|
|AddTrust External CA Root|30.May.20|02faf3e291435468607857694df5e45b68851868|
|COMODO RSA Certification Authority|19.Jan.38|afe5d244a8d1194230ff479fe2f897bbcd7a8cb4|
|thawte Primary Root CA - G3|2.Dec.37|f18b538d1be903b6a6f056435b171589caf36bf2|
|DigiCert Global Root G3|15.Jan.38|7e04de896a3e666d00e687d33ffad93be83d349e|
|GeoTrust Global CA|21.May.22|de28f4a4ffe5b92fa3c503d1a349a7f9962a8212|
|DigiCert Global Root G2|15.Jan.38|df3c24f9bfd666761b268073fe06d1cc8d4f82a4|

<a id="origin-server-port"></a>
#### オリジンサーバーのポート
オリジンサーバーはWebプロトコルをサポートするサービスとして稼働させる必要があります。稼働中のHTTP/HTTPSプロトコルのサービスポート番号を設定できます。  
オリジンサーバーのポートはHTTPまたはHTTPSポートのいずれかを必ず入力する必要があり、設定していないポートは既定のポートである`HTTP:80`、`HTTPS:443`として設定されます。  
オリジンサーバーのポートは、制限されたポートのみ設定できます。設定可能なポート番号については、次の表をご参照ください。

[表 2] 使用可能なオリジンサーバーのポート番号

| ポート番号 |
|---|
|72, 488, 1080, 1443, 7070|
|8000-9001|
|11080-11110|
|80-89|
|591, 1088, 2080, 7612|
|12900-12949|
|443, 777, 1111, 7001, 7777|
|9901-9908|
|45002|

<a id="origin-server-original-path"></a>
#### オリジンパス
オリジナルファイルのパスのうち、下位パスを設定します。コンテンツをリクエストする際、オリジンパスを省略してリクエストできます。

> [例] オリジンパスを /files/images に設定した場合
>
> - オリジナルファイルのURL: http://your.origin.com/files/images/logo.png
> - CDNサービスのURL: http://[サービスID].toastcdn.net/logo.png
> - CDNサービスのURLからオリジンパス(/files/images)を省略してリクエストできます。

<a id="origin-server-downgrading-http-protocols-requesting-originals"></a>
#### オリジンリクエストのHTTPプロトコルダウングレード
CDNエッジ(edge)サーバーは、オリジンサーバーにオリジナルファイルをリクエストする際、クライアントのオリジンへのリクエスト(request)のサービスプロトコル(HTTP/HTTPS)でリクエストします。
つまり、クライアントがHTTPSでリクエストし、オリジンサーバーがHTTPS応答をサポートしていない場合、CDNエッジサーバーからオリジンサーバーへリクエストする際にHTTPSプロトコルでリクエストするため、オリジナルファイルに対する応答を受信できません。
オリジンサーバーでHTTPプロトコルのみを稼働させている場合は、**オリジンサーバーHTTPプロトコルダウングレード**設定を使用し、CDNエッジサーバーからオリジンサーバーへリクエストする際にHTTPSプロトコルをHTTPプロトコルにダウングレードしてリクエストできます。
つまり、クライアントからCDNエッジサーバー間はセキュア通信(HTTPS)で通信し、CDNエッジサーバーからオリジンサーバー間は非セキュア通信(HTTP)で通信することになります。
オリジンリクエストのHTTPプロトコルをダウングレードする際には、次のような制約事項があります。
> [注意] オリジンリクエストのHTTPプロトコルダウングレードに関する制約事項
> - 全体サイトアドレスはプロトコルのダウングレードができません。例えば、オリジンサーバーの全体サイトアドレスである www.nhn.com はダウングレードできません。
> - `GET`、`HEAD`及び`OPTIONS`メソッド以外のメソッドはサポートされていません。
> - CDNサーバーからオリジンサーバーへダウングレードをリクエストする際、次のヘッダは除外される場合があります。
    >    `Origin`, `Referer`, `Cookie`, `Cookie2`, `sec-\*`, `proxy-\*`

<a id="origin-server-forward-host-header"></a>
#### Forward Host Header
CDNサーバーがオリジンサーバーにオリジナルファイルをリクエストする際に渡す`Host`ヘッダ値を設定します。
オリジンサーバーがName-based virtual hostとして稼働している場合、**リクエストホストヘッダ**の設定が必要になる場合があります。オリジンサーバーの運用形態に応じて、適切な設定値を選択してください。
- オリジンホスト名: オリジンサーバーのホスト名を`Host`ヘッダとして設定します。
- リクエストホストヘッダ: クライアントリクエストの`Host`ヘッダとして設定します。

> [注意] セキュア通信(HTTPS)使用時のHostヘッダとオリジンサーバー証明書の有効性検証
> クライアントがセキュア通信(HTTPS)でコンテンツをリクエストすると、CDNサーバーはオリジンサーバー証明書の有効性を確認します。
> オリジンサーバーには、`Host`リクエストヘッダと一致するCN(Common Name)またはSAN(Subject Alternate Name)の証明書がインストールされている必要があります。
> `Host`リクエストヘッダと一致する証明書がオリジンサーバーにインストールされていない場合、セキュア通信エラーが発生します。
> `Host`リクエストヘッダはForward Host Headerの設定に応じてリクエストホストヘッダまたはオリジンホスト名として設定されるため、ご注意ください。

<a id="controlling-the-access-of-root-path"></a>
### ルートパスのアクセス管理 { #controlling-the-access-of-root-path }
CDNサービスのルートパスに対するアクセス制御を設定できます。
![CDNサービス作成-ルートパス](https://static.toastoven.net/prod_cdn/v2/console-cdn-create-origin2_202403.png)

<a id="controlling-the-access-of-root-path-set-root-path-access"></a>
#### ルートパスのアクセス設定
- 使用: ルートパスアクセス管理機能を有効にして、ルートパスへのリクエストをブロックするか、他のページにリダイレクトするように設定します。
- 使用しない: ルートパスアクセス管理機能を無効にします。
<a id="controlling-the-access-of-root-path-access-control-method"></a>
#### アクセス制御方式
- Deny: ルートパスへのリクエストに対してHTTP Response Code 403を応答します。
- Redirect: ルートパスへのリクエストをユーザーが指定したパスにリダイレクトします。
<a id="controlling-the-access-of-root-path-redirect-path"></a>
#### Redirectパス
ルートパスへのリクエストをリダイレクトするパスを入力します。Redirectパスは'/'で始まる必要があり、CDNサービスの下位に存在するパスである必要があります。
<a id="controlling-the-access-of-root-path-redirect-http-response-code"></a>
#### Redirect HTTP Response Code
- ルートパスへのリクエストをリダイレクトして渡すHTTP Response Codeを設定します。
- Redirect HTTP Response Codeは、301、302、303、307の中から選択できます。

<a id="method"></a>
### メソッド { #method }
CDNで既定で許可するメソッドは`GET`、`HEAD`、`OPTIONS`であり、これ以外のメソッドをリクエストすると拒否されます。
該当メソッド以外のメソッドを許可するには、任意のメソッドを選択して設定します。
![CDNサービス作成-ルートパス](https://static.toastoven.net/prod_cdn/v2/console-cdn-create-root-path_202403.png)

<a id="method-allow-method-settings"></a>
#### メソッド許可設定
許可設定したメソッドのリクエストはキャッシュされず、オリジンサーバーに転送されます。

<a id="cache"></a>
### キャッシュ { #cache }

CDNキャッシュの動作設定と有効期限を設定できます。
![CDNサービス作成-キャッシュ](https://static.toastoven.net/prod_cdn/v2/console-cdn-create-root-path_202403.png)

<a id="cache-configuration-of-cache"></a>
#### キャッシュ設定
CDNサーバーがオリジナルファイルをキャッシュする際に使用するキャッシュ設定を選択できます。
- オリジン設定の使用: オリジンサーバーの応答で提供されたキャッシュ制御ヘッダ(`Cache-Control`、`Expires`)を優先的に適用します。もしオリジンサーバーの応答にキャッシュ制御ヘッダ(`Cache-Control`、`Expires`)が有効でないか存在しない場合、キャッシュ有効期限(秒)で指定した期間キャッシュされます。**オリジン設定の使用**オプションが既定値です。
- ユーザー設定の使用: キャッシュ有効期限(秒)で指定した期間キャッシュされます。
- Bypass Cache: 該当設定以前に作成されたキャッシュは維持したまま、それ以降のコンテンツリクエストをキャッシュしません。
- No Store: 既存のキャッシュをすべて削除し、CDNキャッシュ機能を無効にします。

<a id="cache-expiration-time-seconds"></a>
#### キャッシュ有効期限(秒)
キャッシュ有効期限を指定するには、**ユーザー設定の使用**ボタンをクリックし、**キャッシュ有効期限(秒)**でキャッシュ有効期限を変更します。

<a id="cache-set-inclusion-of-query-string-in-cache-key"></a>
#### キャッシュキーのクエリ文字列包含設定
URLベースで生成されるキャッシュキーにリクエストのクエリ文字列を含めるかどうかを設定できます。
- すべて含める: リクエストに含まれるすべてのクエリ文字列をキャッシュキーに含めます。キャッシュキーにリクエストのクエリ文字列が含まれるため、同じコンテンツリクエストに対してクエリ文字列が変更されるたびに新しいキャッシュキーが生成されます。リクエストのクエリ文字列を変更して新しくコンテンツをキャッシュする場合に選択します。**すべて含める**オプションが既定値です。
- すべて除外: リクエストに含まれるクエリ文字列をすべて除外し、URLのみを使用してキャッシュキーを生成します。リクエストのクエリ文字列が継続的に変更される場合、該当オプションを設定することでキャッシュが動作します。

> [参考] キャッシュ有効期限の既定値と有効範囲
> キャッシュ有効期限の既定値は0です。既定値を0に設定すると、キャッシュ有効期限は604,800(単位/秒)=1週間になります。
> キャッシュ有効期限は、既定値の0から2,147,483,647(単位/秒)まで入力できます。


<a id="cache-large-file-optimization"></a>
#### Large File Optimization
100MB以上の大容量ファイルをサービスする際、パフォーマンスと安定性を高めるための設定です。設定しない場合、CDNで許可されるファイルの最大容量は1.8GB未満であり、1.8GB以上の大容量ファイルをサービスするには、本設定を必ず使用する必要があります。

> [参考] NHN Cloud Object Storageサービスで作成したコンテナをオリジンサーバーとして使用する場合
> Large File Optimization機能が正常に動作するには、オリジンサーバーから渡される`ETag`レスポンスヘッダが二重引用符で囲まれた形式である必要があります。
> NHN Cloud Object Storageコンテナでの`ETag`レスポンスヘッダ形式の設定に関する詳細は、Object StorageサービスのAPIガイド内の[コンテナ設定の変更 > RFCに準拠したETag形式の使用設定](/nhncloud/ja/Storage/Object%20Storage/ja/api-guide/#change-container-settings/)をご参照ください。


<a id="access-management-for-referer-header"></a>
### リファラー(referer)ヘッダのアクセス管理 { #access-management-for-referer-header }
リファラーリクエストヘッダでコンテンツのアクセス管理を設定します。
![CDNサービス作成-キャッシュ](https://static.toastoven.net/prod_cdn/v2/console-cdn-create-root-path_202403.png)

リファラーリクエストヘッダには、現在リクエストされているページのリンク元Webページアドレスが含まれます。リファラーリクエストヘッダにより、どの経路からリクエストが流入したかを把握できます。リファラーヘッダのアクセス管理では、特定のリファラーリクエストヘッダのみがユーザーコンテンツにアクセスできるよう設定できます。
正規表現の形式で入力でき、複数入力する場合は改行して入力します。

<a id="access-management-for-referer-header-access-control-types"></a>
#### アクセス制御方式
- ブラックリスト(blacklist)タイプ
    * 特定のリファラーリクエストヘッダのみアクセスを制限する場合に適しています。
    * リファラーリクエストヘッダ値が設定した正規表現にマッチする文字列であれば、コンテンツへのアクセスが制限されます。マッチしない文字列であれば、コンテンツへのアクセスが許可されます。
- ホワイトリスト(whitelist)タイプ
    * 特定のリファラーリクエストヘッダのみアクセスを許可する場合に適しています。
    * リファラーリクエストヘッダ値が正規表現にマッチする文字列であれば、コンテンツへのアクセスが許可されます。マッチしない文字列であれば、コンテンツへのアクセスが制限されます。

<a id="access-management-for-referer-header-content-access-if-referer-header-is-unavailable"></a>
#### リファラーヘッダがない場合のアクセス許可
リファラー(referer)リクエストヘッダがない場合のコンテンツアクセス許可の有無を選択します。
- 許可: リファラーリクエストヘッダがない場合はコンテンツへのアクセスを許可し、リファラーのアクセス制御は動作しません。
- 拒否: リファラーリクエストヘッダがない場合はコンテンツへのアクセスを拒否し、登録されたリファラーに対してのみアクセスを許可します。

> [例]
> - タイプ: ホワイトリスト(whitelist)
> - 正規表現: `^https://[a-zA-Z0-9._-]*\.nhn\.com/.*`
    > 任意のnhn.comサブドメインの下位パスからリソースをリクエストした場合にのみ、コンテンツへのアクセスを許可します。
>
> [参考] 正規表現のエスケープ文字
> - 一部の文字は、正規表現において特殊文字として使用されます。
> - ドット(`.`)を例に挙げると、正規表現においてドット(`.`)はすべての文字と一致することを示す特殊文字です。
> - 特殊文字としての意味ではなく、一般文字そのままとして解釈する必要がある場合は、エスケープ文字のバックスラッシュ(`\`)を特殊文字の前に追加します(例: `\.` )。
> - 正規表現の特殊文字には、`^ . [ ] $ ( ) | * + ? { } \`などがあります。
> - 複数のリファラーを制御する場合は、次の行に続けて入力します。
> - APIを使用して複数のリファラーを設定する場合は、`\n`トークンで区切って入力します。


<a id="access-control-for-auth-token-authentication"></a>
### Auth Token認証のアクセス管理 { #access-control-for-auth-token-authentication }
Auth Token認証のアクセス管理は、コンテンツのリクエストに認証トークンを追加し、CDNエッジサーバーで検証されたトークンのみコンテンツへのアクセスを許可するセキュリティ機能です。
一回限りでコンテンツへのアクセスを許可したり、制限されたユーザーのみがコンテンツにアクセスできるように制御できます。
トークンがない、または無効なトークンでコンテンツをリクエストした場合、`403 Forbidden`応答が送信され、コンテンツにアクセスできません。

Auth Token認証アクセスをCDNサービスに適用するには、次のステップに従って作業を行う必要があります。

> [注意]
>
> Auth Token認証のアクセス管理は、NHN Cloud CDNを利用してサービス中のアプリケーションでも以下の実装が必要です。
> - コンテンツへのアクセスに必要なトークンを作成する必要があります。
> - クライアント(最終コンテンツの消費者)が、作成されたトークンを含めてコンテンツをリクエストできるようにする必要があります。
     > この作業を行わずにAuth Token認証のアクセス管理を設定した場合、トークンの検証失敗によってコンテンツのリクエストが失敗する可能性があるため、ご注意ください。


<a id="access-control-for-auth-token-authentication-nhn-cloud-cdn-console-access-control-settings-for-auth-token-authentication"></a>
#### 1. NHN Cloud CDNコンソール > Auth Token認証のアクセス管理設定

CDNコンソールで以下の内容を参考にして、Auth Token認証のアクセス管理を設定します。

![CDNサービス作成-Auth Token認証のアクセス管理](https://static.toastoven.net/prod_cdn/v2/console-cdn-create-auth-token_202403.png)

##### トークン認証の使用有無
- 使用: Auth Token認証のアクセス管理機能を有効にし、トークンを検証した後にコンテンツにアクセスできるようにします。
- 使用しない: Auth Token認証のアクセス管理機能を無効にします。
- トークンの位置: コンテンツのリクエスト時にトークンを渡す位置を選択します。
    - クッキー(Cookie): 標準クッキーでトークンを渡します。クッキーの使用をサポートしていないデバイスやブラウザでは、トークン認証が正常に動作しない場合があるため注意してください。
    - リクエストヘッダ(Request header): リクエストヘッダにトークンを渡します。
    - クエリ文字列(Query string): クエリ文字列にトークンを渡します。
##### トークン名
- トークン値を渡すトークンの名前です。`token`で固定された値であり、コンソール設定で変更できません。
##### トークンの暗号化キー
- トークンの生成に必要な暗号化キーです。CDNサービスを作成または修正すると、暗号化キーは自動的に生成されます。
- 暗号化キーが外部に公開されないように注意してください。
##### トークン認証対象の設定
コンテンツへのアクセス時にトークンを認証するファイルの対象を設定します。  
トークン認証対象のファイルである場合にのみトークンを検証し、認証対象のファイルでない場合はトークンの検証を行わないため、トークンなしでコンテンツにアクセスできます。  
指定されたリクエストURLパスまたはファイル拡張子のみトークンの検証を行うには、リクエストURLパスと拡張子を入力してください。入力しない場合、すべてのファイルに対してトークンを検証します。
- 認証対象の設定: 設定されたリクエストURLパスとファイル拡張子のファイルのみトークンを検証します。
- 認証例外対象の設定: 設定されたリクエストURLパスとファイル拡張子を除外したファイルのトークンを検証します。
- リクエストURLパス: コンテンツのURLがリクエストURLパスと一致する場合、トークンの認証対象または例外対象として設定します。
    - リクエストURLパスは'/'で始まる必要があり、ワイルドカード文字(複数文字列: \*、単一文字: ?)を使用できます(例: /nhn/\*)。
    - リクエストURLパスにはクエリ文字列を含めません。
    - リクエストURLパスはASCII(アスキー)コード文字のみ入力可能です。
    - 複数を入力するには、次の行に入力してください。複数を入力した場合、1つでも一致すればトークンのアクセス制御が動作します。
    - ファイル拡張子とともに入力した場合には、ファイル拡張子の条件が一致してもトークンのアクセス制御が動作します。
- ファイル拡張子: コンテンツのURLがファイル拡張子と一致する場合、トークンの認証対象または例外対象として設定します。
    - '.'を含まないファイル拡張子を入力します(例: pdf, png)。
    - 複数を入力するには、次の行に入力してください。複数を入力した場合、1つでも一致すればトークンのアクセス制御が動作します。
    - リクエストパスURLとともに入力した場合には、リクエストパスURLの条件が一致してもトークンのアクセス制御が動作します。

> [注意] リクエストURLパスとファイル拡張子
> リクエストURLパスとファイル拡張子の両方を設定した場合、2つの条件のうち1つでも一致すればトークンのアクセス制御が動作します。
> [例] リクエストURLパス `/nhn/\*`、ファイル拡張子 `png`が設定された場合: /nhn配下のすべてのファイル、またはファイル拡張子がpngであるコンテンツに対してトークンを検証します。

<a id="access-control-for-auth-token-authentication-create-a-token"></a>
#### 2. トークンの生成
最終コンテンツユーザーがコンテンツにアクセスするには、トークンとともにコンテンツをリクエストする必要があります。したがって、トークンを生成して最終コンテンツユーザーに発行する必要があります。
トークンの生成は、NHN Cloud CDNを利用してサービス中のアプリケーションで実装される必要があります。
トークンの生成方法は、次のサンプルコードを参考にしてトークンを生成します。

##### Javaサンプルコード
- このサンプルコードには、以下のような制約事項があります。
- JDK 7以上、org.projectlombok:lombok、org.apache.commons:commons-lang3ライブラリとの依存性があります。

```java
import org.apache.commons.lang3.StringUtils;
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import javax.xml.bind.DatatypeConverter;
import java.io.UnsupportedEncodingException;
import java.math.BigInteger;
import java.net.URLEncoder;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.util.Calendar;
import java.util.TimeZone;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class NhnCloudAuthTokenAccessControlExample {

    // NHN Cloudコンソールで確認した認証トークンの暗号化キー
    private static final String AUTH_TOKEN_ENCRYPT_KEY = "{NHN Cloud CDNサービスのトークン暗号化キー}";
    // トークン有効時間(seconds)
    private static final Long TOKEN_DURATION_SECONDS = 3600L;


    public static void main(String[] args) throws AuthTokenException {

        String path = "/nhn/%EC%9D%B8%EC%A6%9D/%E1%84%91%E1%85%A1%E1%84%8B%E1%85%B5%E1%86%AF.png";
        String singleWildcardPath = "/nhn/%EC%9D%B8%EC%A6%9D/*";
        String[] multipleWildcardPath = {"/nhn/%EC%9D%B8%EC%A6%9D*", "/nhn/auth/*"};

        System.out.println(" ----------------- ");
        System.out.println(" 基本トークン発行 ");
        System.out.println(" ----------------- ");

        AuthToken authToken = new AuthToken(AUTH_TOKEN_ENCRYPT_KEY, TOKEN_DURATION_SECONDS);

        System.out.println("単一URLトークン: token=" + authToken.generateURLToken(path));
        System.out.println("ワイルドカードトークン: token=" + authToken.generateWildcardPathToken(singleWildcardPath));
        System.out.println("マルチワイルドカードトークン: token=" + authToken.generateWildcardPathToken(multipleWildcardPath));

        System.out.println(" ----------------- ");
        System.out.println(" セッション識別子を含めたトークン発行 ");
        System.out.println(" ----------------- ");

        AuthToken authTokenWithSession = new AuthToken(AUTH_TOKEN_ENCRYPT_KEY, TOKEN_DURATION_SECONDS, "example-sessionId");
        System.out.println("単一URLトークン: token=" + authTokenWithSession.generateURLToken(path));
        System.out.println("ワイルドカードトークン: token=" + authTokenWithSession.generateWildcardPathToken(singleWildcardPath));
        System.out.println("複数ワイルドカードトークン: token=" + authTokenWithSession.generateWildcardPathToken(multipleWildcardPath));

    }


    public static class AuthToken {

        /** トークン暗号化アルゴリズム(SHA256固定) **/
        private static final String HMAC_SHA_256 = "HmacSHA256";

        /** トークン暗号化キー (NHN Cloud CDNコンソール > Auth Token認証アクセス管理 > 暗号化キー) **/
        private String key;

        /** セッション識別子 */
        private String sessionId;

        /** トークンの有効時間(単位: 秒) */
        private Long durationSeconds;

        /** トークン生成前のurl encode適用有無 */
        private Boolean escapeEarly;

        /** トークンBodyフィールドの区切り文字 */
        private final String fieldDelimiter = "~";

        /** wildcardPath区切り文字 */
        private final String aclDelimiter = "!";


        public AuthToken(String key, Long durationSeconds) {
            this.key = key;
            this.sessionId = null;
            this.durationSeconds = durationSeconds;
            this.escapeEarly = true;

        }


        public AuthToken(String key, Long durationSeconds, String sessionId) {
            this.key = key;
            this.sessionId = sessionId;
            this.durationSeconds = durationSeconds;
            this.escapeEarly = true;
        }


        /**
        * 単一URLに対するトークンを生成します。
        * @param path : contents url (example: /auth/contents/example.png)
        * @return created token
        * @throws AuthTokenException
        */
        public String generateURLToken(String path) throws AuthTokenException {
            return generateToken(createExpireTime(), this.sessionId, path, null);

        }


        /**
        * ワイルドカードパスに対するトークンを生成します。
        * @param wildcardPath : "/auth/contents/*"
        * @return 生成されたトークン値
        * @throws AuthTokenException
        */
        public String generateWildcardPathToken(String wildcardPath) throws AuthTokenException {
            return generateWildcardPathToken(new String[] {wildcardPath});
        }

        /**
        * 複数のワイルドカードパスに対するトークンを生成します。
        * @param wildcardPaths (example: ["/auth/contents/*", "/auth/*/images/*"])
        * @return 生成されたトークン値
        * @throws AuthTokenException
        */
        public String generateWildcardPathToken(String... wildcardPaths) throws AuthTokenException {
            return generateToken(createExpireTime(), this.sessionId, null, wildcardPaths);

        }


        private String createExpireTime() {
            Long nowSeconds = Calendar.getInstance(TimeZone.getTimeZone("UTC")).getTimeInMillis() / 1000L;
            Long exp = nowSeconds + this.durationSeconds;
            return exp.toString();
        }


        private String generateToken(String exp, String sessionId, String path, String[] wildcardPaths) throws AuthTokenException {

            try {

                StringBuilder token = new StringBuilder();
                token.append("exp=")
                    .append(exp)
                    .append(this.fieldDelimiter);

                if (wildcardPaths != null && wildcardPaths.length > 0) {
                    token.append("acl=")
                        .append(escapeEarly(StringUtils.join(wildcardPaths, this.aclDelimiter)))
                        .append(this.fieldDelimiter);
                }

                if (sessionId != null && sessionId.length() > 0) {
                    token.append("id=")
                        .append(escapeEarly(sessionId))
                        .append(this.fieldDelimiter);
                }

                StringBuilder hashSource = new StringBuilder(token);
                if (path != null && path.length() > 0) {
                    hashSource.append("url=")
                              .append(escapeEarly(path))
                              .append(this.fieldDelimiter);

                }

                // remove last fieldDelimiter char
                hashSource.deleteCharAt(hashSource.length() - 1);

                Mac hmac = Mac.getInstance(HMAC_SHA_256);
                byte[] keyBytes = DatatypeConverter.parseHexBinary(this.key);
                SecretKeySpec secretKey = new SecretKeySpec(keyBytes, HMAC_SHA_256);
                hmac.init(secretKey);

                byte[] hmacBytes = hmac.doFinal(hashSource.toString().getBytes());
                return token.toString() + "hmac=" + String.format("%0" + (2 * hmac.getMacLength()) + "x", new BigInteger(1, hmacBytes));

            } catch (NoSuchAlgorithmException e) {
                throw new AuthTokenException(e.getMessage());
            } catch (InvalidKeyException e) {
                throw new AuthTokenException(e.getMessage());
            }

        }


        private String escapeEarly(final String text) throws AuthTokenException {
            if (this.escapeEarly == true) {
                try {
                    StringBuilder newText = new StringBuilder(URLEncoder.encode(text, "UTF-8"));
                    Pattern pattern = Pattern.compile("%..");
                    Matcher matcher = pattern.matcher(newText);
                    String tmpText;
                    while (matcher.find()) {
                        tmpText = newText.substring(matcher.start(), matcher.end()).toLowerCase();
                        newText.replace(matcher.start(), matcher.end(), tmpText);
                    }
                    return newText.toString();
                } catch (UnsupportedEncodingException e) {
                    return text;
                } catch (Exception e) {
                    throw new AuthTokenException(e.getMessage());
                }
            } else {
                return text;
            }
        }

    }

    public static class AuthTokenException extends Exception {
        private static final long serialVersionUID = 1L;


        public AuthTokenException(String msg) {
            super(msg);
        }
    }

}
```
##### AuthTokenクラスのメンバー変数の説明
- `key`: NHN Cloud CDNコンソールに表示されたAuth Token認証アクセス管理 > トークン暗号化キーを入力します。
- `sessionId`: 単一アクセスリクエストに対する固有識別子を含めてトークンを生成するには、sessionIdを入力します。
    - セッションIDごとに有効なトークンを生成し、一回限りのトークンを生成したり様々なケースで活用できます。
    - セッションIDは[出力可能なASCII文字表](https://ko.wikipedia.org/wiki/ASCII#%EC%B6%9C%EB%A0%A5_%EA%B0%80%EB%8A%A5_%EC%95%84%EC%8A%A4%ED%82%A4_%EB%AC%B8%EC%9E%90%ED%91%9C.)で構成する必要があります。
    - セッションIDの長さは最大36バイトです。
- `durationSeconds`: 生成されたトークンが有効な時間(秒)。有効時間が過ぎたトークンはトークン認証に失敗します。
    - トークンの有効時間を短く設定しすぎると、CDNエッジサーバーでトークンを検証する前にトークンが期限切れになる可能性があるためご注意ください。期待するトークン有効時間より10秒以上大きく設定することをおすすめします。
    - トークン生成サーバーの時刻同期設定NTP(Network Time Protocol)が有効であるか必ず検証してください。同期されていない時刻情報によって、トークン有効時間の検証が失敗する可能性があります。
##### AuthTokenクラスの公開メソッド(Public Method)
- `public String generateURLToken(String path)`
    - 単一パスに対するトークンを生成します。
        - [例] path: authToken.generateURLToken("/auth/contents/example.png")
    - [注意] パスまたはセッションIDはURLエンコード文字列に変更した後にトークンを生成してください(例: `/nhn/認証/ファイル.png` => `/nhn/%EC%9D%B8%EC%A6%9D/%E1%84%91%E1%85%A1%E1%84%8B%E1%85%B5%E1%86%AF.png`)。
    - [注意] `!`、`~`の文字は予約された文字として使用されるため、パスまたはセッションIDに含めないようにします。
- `public String generateWildcardPathToken(String wildcardPath)`, `public String generateWildcardPathToken(String... wildcardPaths)`
    - ワイルドカードパスとマッピングされるパスのトークンを生成します。パスのパターンが一致する場合、1つのワイルドカードトークンで複数のコンテンツURLのトークンを認証できます。
        - [例1] wildcardPath: authToken.generateWildcardPathToken("/auth/contents/*") : /auth/contents配下のすべてのファイルに対してトークンを発行します。
        - [例2] wildcardPath: authToken.generateWildcardPathToken("/auth/contents/*.png") : /auth/contentsパスのpngファイルに対するトークンを発行します。
        - [例3] wildcardPath: authToken.generateWildcardPathToken("/auth/contents/example?.png") : /auth/contentsパスのexampleと単一文字が結合されたpngファイルに対するトークンを発行します。
    - [注意] パスまたはセッションIDはURLエンコード文字列に変更した後にトークンを生成してください(例: `/nhn/認証/ファイル.png` => `/nhn/%EC%9D%B8%EC%A6%9D/%E1%84%91%E1%85%A1%E1%84%8B%E1%85%B5%E1%86%AF.png`)。
    - [注意] `!`、`~`の文字は予約された文字として使用されるため、パスまたはセッションIDに含めないようにします。
    - 生成されたトークンは`exp={expirationTime}~acl={path!path!path}~id={sessionId}~hmac={HMAC}`形式で生成されます。
        - [例] 生成されたトークン: `exp=1600331503~acl=%2fnhn%2f*.png~id=session-id1~hmac=2509123dcabe2fc199e3ac44793e4e135a09590ff4ebf6a902ea26469ead7f91`

<a id="access-control-for-auth-token-authentication-include-created-token-to-the-request-of-content"></a>
#### 3. 生成されたトークンをコンテンツリクエストに含める
クライアント(最終コンテンツユーザー)がコンテンツリクエスト時、コンソールで設定したトークンの位置に、生成されたトークン値を含めてリクエストするようにします。

- トークンの位置: クッキー
  ```
  curl --cookie "token={生成されたトークン値}" \
  -X GET http://xxx.toastcdn.net/auth/contents/example.png
  ```
- トークンの位置: リクエストヘッダ
  ```
  curl -H "token: {生成されたトークン値}" \
  -X GET http://xxx.toastcdn.net/auth/contents/example.png
  ```
- トークンの位置: クエリ文字列
  ```
  curl -d "token={生成されたトークン値}" \
  -X GET http://xxx.toastcdn.net/auth/contents/example.png
  ```


<a id="http-response-header"></a>
### HTTPレスポンスヘッダ { #http-response-header }
CDNからユーザーに応答する際に渡されるヘッダを追加/変更/削除する機能です。
ヘッダは重複しないヘッダ名で最大10個まで設定できます。
![CDNサービス作成-レスポンスヘッダ(画像修正必要)](https://static.toastoven.net/prod_cdn/v2/console-cdn-create-http-response-header_202403.png)

<a id="http-response-header-settings"></a>
#### HTTPレスポンスヘッダの設定
- 使用: HTTPレスポンスヘッダを追加/変更/削除する設定を使用します。
- 使用しない: HTTPレスポンスヘッダを設定しません。
- Action: HTTPレスポンスヘッダを変更する方式を選択します。
    - Add: 設定されたヘッダ名を追加してユーザーに応答します。
    - Modify: 設定されたヘッダ名がコンテンツに存在する場合はヘッダ値を変更し、存在しない場合は追加してユーザーに応答します。
    - Delete: 設定されたヘッダ名を削除してユーザーに応答します。
- ヘッダ名: `Access-Control-Allow-Origin`、`Cache-Control`、`Content-Type`などのヘッダ名を一覧から選択するか、「直接入力」を選択してユーザーが定義したカスタムヘッダ名を設定できます。
- カスタムヘッダ名: ユーザーが定義したヘッダ名を入力します。英数字と'-'、'_'のみ入力可能です。
- ヘッダ値: ヘッダ名の値を設定でき、必須の入力値です。

> [参考] CORS(Cross-Origin Resource Sharing)の設定
> 以下のようにHTTPレスポンスヘッダを設定してCORSを許可できます。
> - Action: Modify
> - ヘッダ名: `Access-Control-Allow-Origin`
> - ヘッダ値: `*`(ワイルドカード)または許可するオリジンURI



<a id="settings"></a>
## 設定 { #settings }

<a id="modify-cdn-service-setting"></a>
### CDNサービスの設定変更 { #modify-cdn-service-setting }
サービスドメイン名を除くCDNサービスの設定を変更できます。
![CDNサービス変更の有効化](https://static.toastoven.net/prod_cdn/v2/console-cdn-modify3_202403.png)

1. 変更するCDNサービスをCDNサービス一覧から選択します。
2. **修正**ボタンをクリックします。

次のようにCDNサービス修正ページに移動します。
![CDNサービス修正の確認-画像修正必要](https://static.toastoven.net/prod_cdn/v2/console-cdn-modify2_202403.png)

1. 変更する設定内容を修正します。
2. **確認**ボタンをクリックして変更を完了します。

説明とコールバック設定を除く他の設定を変更するには、全体のCDNサーバーに反映する必要があるため、時間がかかる場合があります。修正作業は数十分以内に完了しますが、ドメインエイリアスの設定が変更された場合は数時間かかることがあります。

> [参考] CDNサービス修正中のデプロイ状態とサービス状態
> サービスの修正作業が進行中の場合、既存のCDNサービス設定で運用されます。
> もし修正作業に失敗すると既存の設定情報にロールバックされ、CDNサービス一覧のデプロイ状態が赤い円で表示されます。設定情報にエラーがあるか、または内部的にエラーが発生した際に修正作業に失敗します。

<a id="suspend-and-resume-cdn"></a>
### CDNサービスの一時停止と再開 { #suspend-and-resume-cdn }
CDNサービスを一時的に停止したり、再開したりできます。


1. 一時停止するCDNサービスを選択します。
2. **一時停止**ボタンをクリックします。
   ![CDNサービス-一時停止](https://static.toastoven.net/prod_cdn/v2/console-cdn-pause2_202403.png)
3. 証明書が連携されたCDNサービスには、期限切れの警告メッセージが表示されます。証明書が期限切れにならないようにするには、証明書の更新開始日より前にCDNサービスを再開する必要があります。
   ![CDNサービス-再開](https://static.toastoven.net/prod_cdn/v2/console-cdn-restart2_202403.png)
4. 一時停止状態のCDNサービスを再開するには、再開するCDNサービスを選択します。
5. **再開**ボタンをクリックします。




> [参考] 一時停止と再開の動作遅延
> CDNサービスの一時停止と再開は、CDNサービスドメインのDNSレコードを変更して動作します。
> したがって、キャッシュDNSサーバーでTTLの期間キャッシュされていたり、DNSの伝播状況によっては、一時停止/再開が完了しても即座に一時停止/再開が動作しない場合があります。

> [注意] 発行された証明書が連携されたCDNサービスの一時停止
> 証明書が連携されたCDNサービスを一時停止する場合、更新ができません。
> **証明書管理** > 証明書一覧の**証明書の更新開始日**より前にCDNサービスを再開してください。
> 証明書の更新開始日から5日間は証明書の更新期間であるため、該当期間に一時停止すると期限切れになる可能性があるためご注意ください。


<a id="delete-cdn"></a>
### CDNサービスの削除 { #delete-cdn }
CDNサービスを削除します。削除作業は復元できないためご注意ください。

1. 削除するCDNサービスを選択します。
2. **削除**ボタンをクリックします。
   ![CDNサービス-削除](https://static.toastoven.net/prod_cdn/v2/console-cdn-delete2_202403.png)
3. 証明書が連携されたCDNサービスには、期限切れの警告メッセージが表示されます。証明書が期限切れにならないようにするには、サービス中の他のCDNサービスに証明書を連携してください。


> [参考] CDNサービスの削除にかかる時間
> CDNサービスの削除作業は数時間(最大2~3時間)かかる場合があります。

> [注意] 発行された証明書が連携されたCDNサービスの削除
> 証明書が連携されたCDNサービスを削除すると、証明書を更新できません。
> **証明書管理**の証明書一覧から、**証明書の更新開始日**より前にサービス中の他のCDNサービスへ連携してください。
> 証明書の更新開始日から5日間は証明書の更新期間であるため、該当期間に削除すると期限切れになる可能性があるためご注意ください。


<a id="purge"></a>
## Purge { #purge }
CDNキャッシュサーバーは、キャッシュ設定に従って指定された有効期限の間、オリジンサーバーのファイルをキャッシュします。ファイルをキャッシュすると、オリジナルファイルが変更されてもキャッシュの有効期限が切れるまでは変更前のオリジナルファイルを維持します。
変更されたオリジナルファイルでコンテンツを即時にアップデートするには、**パージ**をリクエストする必要があります。
パージを実行すると、リクエストしたコンテンツの古いキャッシュデータを削除し、オリジンサーバーから新しいオリジナルファイルを再度キャッシュします。

1. 変更するサービスをCDNサービス一覧から選択します。
2. **パージ**タブを選択し、**パージ**ボタンをクリックします。
   ![CDNキャッシュ再配布](https://static.toastoven.net/prod_cdn/v2/console-cdn-purge2_202403.png)

3. パージタイプを選択します。
    - CDNサービスドメインによって、サポートされるキャッシュ再配布のタイプとリクエスト形式が異なるためご注意ください。
    - パージタイプとリクエスト形式
        * 特定のファイル: 再配布するコンテンツのURLを入力します。リクエストしたURLのキャッシュのみがパージされるため、ドメインエイリアスで複数のサービスドメインアドレスを使用している場合は、各URLアドレスでリクエストする必要があります。
            * 例) 既定のサービスドメインアドレス: http://[サービスID].toastcdn.net/path/to/file1.jpg
            * 例) ドメインエイリアスのアドレス: http://customer.domain.com/path/to/file1.jpg
        * すべてのファイル: キャッシュファイルをすべて削除します。オリジンサーバーに過度なトラフィックが流入する可能性があるため、ご注意ください。
4. 選択したパージタイプに合わせて、パージするファイルを指定します。
5. **確認**ボタンをクリックしてパージをリクエストします。

パージには使用量制限があるため、以下の表を参考にして使用量を超過しないようご注意ください。

|分類 |[サービスID].toastcdn.net |
|---|---|
| 制限単位 | プロジェクト別(Appkey) |
| 特定のファイル | 1秒あたりのリクエスト可能回数: 1回、リクエストごとのURL数の制限: 200 URL |
| すべてのファイルタイプ | 5分あたりのリクエスト可能回数: 1回 |

> [注意] [サービスID].toastcdn.netサービスの作成後にパージ失敗エラーが発生する場合
> CDNサービスを作成してから約1時間以内は、パージリクエストが失敗する場合があります。それ以降も継続して失敗する場合は、[NHN Cloudカスタマーサポート](https://www.nhncloud.com/jp/support/inquiry)にお問い合わせください。

<a id="statistics"></a>
## 統計 { #statistics }

ネットワーク転送量、HTTPステータスコード別の統計、およびダウンロード数が最も多いコンテンツのランキング統計を確認できます。
7日以内の統計データは正確ではないため、参考用としてのみご利用ください。正確な統計データは7日以降にご確認ください。

1. 統計を確認するサービスをCDNサービス一覧から選択します。
2. **統計**タブをクリックします。
   ![cdn_08_201812](https://static.toastoven.net/prod_cdn/v2/console-statistics_202403.png)
3. 検索期間または日付を選択します。
4. 検索期間内のデータ周期は、選択した期間に応じて自動的に選択されます。

>  [参考] 最大検索期間
> 直近90日間の統計データのみ照会可能です。

<a id="alias-domain"></a>
## ドメインエイリアス { #alias-domain }
ドメインエイリアス(Domain Alias)は、NHN Cloud CDNで既定で提供される[サービスID].toastcdn.netドメインの代わりに、お客様が所有するドメインでCDNサービスを利用できる機能です。
ドメインエイリアスを使用するには、**ドメインエイリアス**タブでドメインを登録した後、所有権の検証を完了し、CDNサービスのドメインエイリアス項目に連携させる必要があります。所有するドメインでHTTPSプロトコルサービスを利用するには、**証明書管理**タブで証明書を発行してください。

> [参考] ドメインエイリアスを利用したHTTPSサービスの設定フロー  
> 所有するドメインでHTTPSプロトコルサービスを利用するには、以下の手順を順番に進める必要があります。  
> HTTPのみを使用する場合は、② 証明書発行のステップを省略できます。
> ```
> ① ドメインの登録および所有権の検証(ドメインエイリアスタブ)
>         ↓
> ② 証明書の発行およびドメイン検証(証明書管理タブ、HTTPS利用時は必須)
>         ↓
> ③ CDNサービスにドメインエイリアスを連携(CDNサービスタブ)
>         ↓
> ④ CDNサービスドメインのDNS CNAMEレコード設定(DNSサービスプロバイダー)
> ```

<a id="register-a-domain"></a>
### ドメイン登録 { #register-a-domain }
**ドメインエイリアス**タブでドメインを登録できます。

1. **ドメインエイリアス**タブの**ドメイン追加**ボタンをクリックします。
2. 登録するドメインを全体ドメインアドレス(FQDN, fully qualified domain name)形式で入力します(例: cdn.example.com)。
3. **確認**ボタンをクリックするとドメインが登録され、検証待機状態になります。

> [注意] ドメイン登録時の確認事項
> -  全体ドメインアドレス(FQDN)形式のみ入力できます(例: cdn.example.com)。
> -  すでに登録されているドメインは重複して登録できません。

<a id="verify-a-domain"></a>
### ドメイン検証 { #verify-a-domain }
登録したドメインの所有権を確認するため、ドメイン検証を進める必要があります。  
ドメインの検証方式には、DNS TXTレコードの追加、HTTPファイル認証、HTTPリダイレクト認証の3つの方式があり、いずれか1つの方式のみ進めれば問題ありません。  
**ドメインエイリアス**タブで登録したドメインを選択すると、検証に必要な情報と検証方法を確認できます。

<a id="verify-a-domain-dns-txt-record-addition-method"></a>
#### DNS TXTレコード追加方式
ドメインのDNS制御権限を確認し、ドメインを検証します。

1. ドメインのDNSサービスプロバイダーのDNS管理ページでTXTレコードを追加します。
   DNSの設定方法はDNSサービスプロバイダーによって異なる場合があります。関連する設定については、該当サービスプロバイダーにお問い合わせください。
    - レコードタイプ: `TXT`
    - TTL: `60`。60に設定できない場合は、可能な限り小さく設定してください。
    - レコード名: コンソールに表示された`レコード名`の値を入力します。
    - レコード値: コンソールに表示された`レコード値`を入力します。
2. TXTレコードの設定後、コンソールで**検証実行**ボタンをクリックします。

<a id="verify-a-domain-http-file-authentication-method"></a>
#### HTTPファイル認証方式
ドメインが紐付けられたWebサーバーにHTTPページを追加し、ドメインを検証します。

1. コンソールに表示された`HTTPページURL`パスに、Webサーバーからアクセス可能なページを作成します。
2. 作成したページの本文に、コンソールに表示された`ページコンテンツ(トークン)`の値を設定します。
3. Webブラウザで該当URLにアクセスし、ページコンテンツの値が表示されることを確認します。
4. 確認後、コンソールで**検証実行**ボタンをクリックします。

> [注意] HTTPファイル認証方式は、HTTPの既定ポート(80)で運用中のWebサーバーでのみ使用できます。

<a id="verify-a-domain-http-redirect-authentication-method"></a>
#### HTTPリダイレクト認証方式
ドメインが紐付けられたWebサーバーにリダイレクトを設定し、ドメインを検証します。

1. コンソールに表示された`リダイレクト元URL`に対して、`リダイレクト先URL`へHTTP 301または302リダイレクトを設定します。
2. リダイレクト設定後、コンソールで**検証実行**ボタンをクリックします。

> [注意] ドメイン検証に関する注意事項
> 1. ドメイン検証トークンは、登録日から14日間有効です。期間内に検証が完了しない場合、トークンは期限切れとなり、トークンの再発行後に再度検証する必要があります。
> 2. トークンが期限切れになった場合、コンソールで**トークン再発行**ボタンをクリックして新しい検証トークンを発行できます。トークンが再発行されると以前の検証情報は初期化されるため、新しいトークン情報で再度検証を進める必要があります。
> 3. ドメイン検証が完了すると、状態が**検証完了**に変更され、CDNサービスに連携できるようになります。

[表] ドメインエイリアスの検証状態

| 状態 | 説明 |
|----------| ----------------------------------------------------- |
| 検証待機 | ドメインが登録され、検証を待機している状態 |
| 検証進行中 | ドメイン所有権の検証が進行中の状態 |
| 検証完了 | ドメイン所有権の検証が完了し、CDNサービスに連携可能な状態 |
| 検証トークン期限切れ | 検証トークンが期限切れになった状態(トークン再発行後に再検証が必要) |

<a id="integrate-an-alias-domain-with-the-cdn-service"></a>
### CDNサービスにドメインエイリアスを連携 { #integrate-an-alias-domain-with-the-cdn-service }
ドメインの検証が完了すると、CDNサービスにドメインエイリアスを連携できます。

1. **CDNサービス**タブで連携させるCDNサービスを選択し、**修正**ボタンをクリックします。
2. **基本情報**の**ドメインエイリアス**項目で、検証が完了したドメインを選択します。
    - 検証が完了したドメインのみ選択できます。設定したいドメインが一覧に表示されない場合、**ドメインエイリアス**タブでドメインを先に登録し、検証を完了させてください。
3. **確認**ボタンをクリックして変更を完了します。
4. ドメインエイリアスを設定した後は、ドメインのDNSサービスプロバイダーでCNAMEレコードを次のように設定する必要があります。DNS設定に関するお問い合わせは、DNSサービスプロバイダーにご連絡ください。
    - レコードタイプ: `CNAME`
    - レコード名: `[ドメインエイリアスに登録したドメイン]`
    - レコード値(Rdata): `[サービスID].toastcdn.net`
    - TTL: 任意の値

> [参考] ドメインエイリアス変更時の所要時間
> ドメインエイリアスが変更された場合、サービスの修正作業に1時間以上かかることがあります。

> [注意] ドメインエイリアス変更時の証明書に関する注意事項  
> ドメインエイリアスからCDN証明書のドメインを削除した場合、証明書が期限切れになる可能性があります。  
> 証明書を継続して維持するには、ドメインエイリアスを再度追加して認証した後、CDNサービスに再びドメインエイリアスとして連携させてください。

<a id="delete-a-domain"></a>
### ドメイン削除 { #delete-a-domain }
登録したドメインエイリアスを削除できます。

1. **ドメインエイリアス**タブで、削除するドメインを選択します。
2. **削除**ボタンをクリックします。

> [注意] ドメイン削除時の確認事項
> CDNサービスに連携されたドメインは削除できません。ドメインを削除するには、まずCDNサービスからドメインエイリアスの連携を解除してから削除してください。


<a id="certificate"></a>
## 証明書管理 { #certificate }
所有するドメインでコンテンツをセキュア通信(HTTPS)で送信するには、CDNサーバーに所有するドメインの証明書をデプロイする必要があります。証明書がない場合、クライアント(ブラウザ)とCDNエッジサーバー間でセキュア通信(HTTPS)を行うことができず、証明書エラーが発生します。
NHN Cloud CDNの証明書管理では、以下の機能を提供しています。

- 単一ドメインタイプの証明書発行
- 全世界拠点のCDNサーバーへ証明書をデプロイ(中国およびロシア地域を除く)
- 期限切れになる前に自動更新

<a id="issue-new-certificates"></a>
### 新規証明書の発行 { #issue-new-certificates }
**証明書管理**タブで証明書を発行できます。
![CDN新規証明書の発行](https://static.toastoven.net/prod_cdn/v2/console-certificate-create_202403.png)

1. **証明書管理**タブの**新規証明書の発行**ボタンをクリックします。
2. 発行対象のドメインを全体ドメインアドレス(FQDN, fully qualified domain name)形式で入力します。
3. 証明書発行の案内内容を確認し、**確認**ボタンをクリックします。
4. 新規証明書の発行をリクエストすると、**証明書管理**タブに該当のドメインが表示されます。証明書のステータスが**ドメイン検証**に変更されたら、ドメイン検証作業を進めてください。

> [注意] 証明書発行前の確認事項
> 1. ご自身で所有しているドメインに対してのみ証明書を発行できるため、あらかじめドメインを購入した上で作業を進めてください。
> 2. 外部の認証局(CA, certificate authority)で発行された証明書は利用できません。
> 3. 単一ドメインの証明書のみ発行可能です。ワイルドカード証明書やマルチドメイン証明書には対応していません。
> 4. 証明書の発行上限は、1プロジェクトあたり5個までとなります。上限の引き上げをご希望の場合は、[NHN Cloudカスタマーサポート](https://www.nhncloud.com/jp/support/inquiry)にお問い合わせください。
> 5. 新規証明書の発行リクエスト後、ステータスがドメイン検証待ちに変わるまで数十分(最大1〜2時間)かかる場合があります。ステータスが変更されると、NHN Cloudプロジェクトのメンバー宛てに通知メールが送信されます。システムエラー等でメールが届かない場合は、コンソール画面から状態をご確認ください。

<a id="validate-domain"></a>
### ドメイン検証 { #validate-domain }
新規証明書の発行リクエスト後、ステータスがドメイン検証状態に変わったら、以下の手順でドメイン検証を行ってください。
詳細なドメイン検証手順については、コンソール上で対象ドメインを選択して確認するか、プロジェクトメンバー宛てに送信されるガイドメールの内容をご参照ください。

![CDNドメイン検証](https://static.toastoven.net/prod_cdn/v2/console-certificate-domain-validation_202403.png)

ドメイン検証とは、発行リクエストしたドメインの実際の所有者であることを証明するための手続きです。この検証を完了しない限り、証明書は発行されません。
所有権を証明するため、指定された方式を用いてドメインの制御権限があることを確認します。
検証方式には「DNS TXTレコードの追加」と「HTTPページの追加」の2種類があり、いずれか一方の方式で検証を完了させてください。

![CDNドメイン検証](https://static.toastoven.net/prod_cdn/v2/console-certificate-domain-validation2_202403.png)

<a id="validate-domain-adding-dns-txt-records"></a>
#### DNS TXTレコード追加方式
ドメインのDNS制御権限を利用して検証を行います。

1. ドメインのDNSサービスプロバイダーのDNS管理ページでTXTレコードを追加します。
   DNSの設定方法はDNSサービスプロバイダーによって異なる場合があります。関連する設定については、該当サービスプロバイダーにお問い合わせください。
    - レコードタイプ: `TXT`
    - TTL: `60`。60に設定できない場合は、可能な限り小さく設定してください。
    - レコード名: `_acme-challenge.[対象ドメイン].` (コンソール画面、またはガイドメールに記載されているレコード名を入力します)
    - レコード値: 任意の文字列 (コンソール画面、またはガイドメールに記載されているレコード値を入力します)

2. `nslookup` コマンドを使用して、追加したTXTレコードが正しく引けるか(名前解決できるか)を確認します。DNSの伝播状況によっては、反映されるまでに時間がかかる場合があります。
   `nslookup -type=TXT _acme-challenge.[対象ドメイン].`

以下の画面は、NHN Cloud DNS+サービスでの設定例です。具体的な設定手順は、ご利用のDNSサービスプロバイダーによって異なります。
![CDNドメイン検証](https://static.toastoven.net/prod_cdn/v2/console-certificate-domain-validation-dns_202105.png)


<a id="validate-domain-adding-http-pages"></a>
#### HTTPページ追加方式
ドメインが紐付けられているWebサーバーに指定のHTTPページを配置して検証を行います。

1. Webサーバー上の `http://[対象ドメイン]/.well-known/acme-challenge/[指定された文字列]` のパスにHTTPページを作成します。
2. 作成したページの本文(コンテンツ)に、コンソールまたはガイドメールに記載されているトークン値を設定します。
3. Webブラウザで `http://[発行リクエストした証明書のドメイン]/.well-known/acme-challenge/[任意の文字列]` URLにアクセスし、`ページコンテンツ(トークン)`の値が画面に表示されるか確認します。

> [注意] ドメイン検証に関する注意事項
> - ドメイン検証は、証明書の発行リクエスト日から5日以内に行う必要があります。期間内に行われない場合、証明書の発行は自動的にキャンセルされます。
> - ドメイン検証作業の完了後、検証に成功すると通常数時間以内に証明書の発行およびデプロイ作業が開始されます。1日以上進まない場合は、検証用の設定(DNSレコードやHTTPページ)が正しいか再確認してください。設定に問題がないにもかかわらず進展しない場合は、[NHN Cloudカスタマーサポート](https://www.nhncloud.com/jp/support/inquiry)にお問い合わせください。
> - 「HTTPページ追加方式」は、WebサーバーがHTTPの既定ポート(80番)で稼働している場合のみ利用可能です。80番ポートを使用できない環境の場合は、「DNS TXTレコード追加方式」をご利用ください。

<a id="issue-and-deploy-certificates"></a>
### 証明書の発行およびデプロイ { #issue-and-deploy-certificates }
ドメイン検証を通過すると、数時間以内に証明書の発行およびCDNサーバーへのデプロイ作業が開始されます。
コンソール上のステータスが**証明書発行およびデプロイ**に変わり、NHN Cloudプロジェクトのメンバー宛てに通知メールが送信されます。
お客様側で対応が必要な作業はありません。デプロイの完了をお待ちください。

> [参考] 証明書の発行およびデプロイにかかる時間
> 証明書の発行およびデプロイ処理が完了するまでに、最大で9時間以上かかる場合があります。

<a id="integrate-with-cdn-service"></a>
### CDNサービスへの連携 { #integrate-with-cdn-service }
発行された証明書を利用するには、対象のCDNサービスに連携させる必要があります。
連携作業を行わない場合や、連携設定を解除してしまった場合、証明書の自動更新が行われず期限切れとなる恐れがあるためご注意ください。

1. CNAMEレコードの設定: 対象ドメインのDNS管理画面(ご利用のDNSプロバイダー)にて、以下のCNAMEレコードを追加します。
    - レコードタイプ: `CNAME`
    - TTL: 任意の値。頻繁な変更が想定される場合は、短い時間を設定することを推奨します。レコード変更時にキャッシュDNSサーバーでTTLの期間キャッシュされる場合があります。
    - レコード名: `[証明書のドメイン].` (例: `test.alias.com.`)
    - レコード値: `[連携先のCDNサービスドメイン]` (例: `xxxxxxxx.toastcdn.net`)
      以下の画面は、NHN Cloud DNS+サービスでの設定例です。具体的な設定手順は、ご利用のDNSサービスプロバイダーによって異なります。
      ![CDNサービス連携-CNAME委任](https://static.toastoven.net/prod_cdn/v2/console-certificate-service-cname_202105.png)

2. ドメインエイリアスの設定: 証明書を適用するCDNサービスに、ドメインエイリアスを設定します。
    - **CDNサービス**タブから連携先のCDNサービスを選択し、**修正**ボタンをクリックします。「ドメインエイリアス」の項目に証明書のドメインを追加し、**確認**ボタンをクリックして保存します。
       ![CDNサービス連携-ドメインエイリアス](https://static.toastoven.net/prod_cdn/v2/console-certificate-service-alias2_202403.png)

> [参考] CNAMEレコードの伝播時間
> CNAMEレコードを設定した際、DNSの伝播(浸透)には時間がかかる場合があります。そのため、連携設定を正しく完了していても、しばらくの間は証明書のステータスが「CDNサービス連携待機」のままとなることがあります。
> 設定が正しいにもかかわらず、24時間以上「CDNサービス連携待機」からステータスが変わらない場合は、設定内容をご確認の上、[NHN Cloudカスタマーサポート](https://www.nhncloud.com/jp/support/inquiry)にお問い合わせください。

> [注意] 証明書の期限切れに関する注意事項
> NHN Cloud CDNで発行された証明書は、有効期限を迎える前に自動的に更新処理が行われます。
> 自動更新は、証明書一覧に表示されている「更新開始日」から5日以内に実行されます。
> 自動更新を正常に完了させるには、以下の条件をすべて満たしている必要があります。
> 条件を満たしていない場合、更新処理が失敗し、証明書が期限切れとなる恐れがありますのでご注意ください。
>
> - 証明書のドメインから、連携先のCDNサービスドメイン(xxxxx.toastcdn.net)へCNAMEレコードで正しく委任されていること。
> - 連携先のCDNサービス側で、ドメインエイリアスとして該当のドメインが設定されていること。
> - 証明書が連携されているCDNサービスを「一時停止」にすると、自動更新は行われません。
     >    - 更新開始日を迎える前にサービスを「再開」するか、稼働中の別のCDNサービスへ証明書を付け替えてください。
> - 証明書が連携されているCDNサービスを「削除」すると、自動更新は行われません。
     >    - サービスを削除する前に、稼働中の別のCDNサービスへ証明書を付け替えてください。
> - 対象ドメインのネームサーバー(NS)やDNS設定が、正常な状態で維持されていること。
     >    - DNSエラー(無効なネームサーバー、CAAレコードの応答不可など)が発生していると、証明書の発行および更新に失敗します。エラーが放置されると、最終的に証明書が期限切れになる場合があります。

CDNサービスへの連携が完了すると、証明書のステータスが「正常」と表示されます。
![CDN証明書の正常状態](https://static.toastoven.net/prod_cdn/v2/console-certificate-active_202403.png)

> [参考] クライアント側で証明書エラーが発生する場合の対処方法
> NHN Cloud CDNで発行される証明書のルート証明書の1つ(IdenTrust DST Root CA X3)は、2021年9月30日に期限切れになり、一部の古い端末や旧型ブラウザで問題が発生する場合があります。
> エンドユーザーの環境で `ERR_CERT_DATE_INVALID` 等のエラーが発生する場合は、OSのアップデートを実施するか、新しいルート証明書(ISRG Root X1)を手動でインストールするようご案内ください。
> - ISRG Root X1 証明書のダウンロード: [ダウンロードリンク](https://letsencrypt.org/certs/isrgrootx1.pem)
> - Windows OSでのルート証明書インストール手順(参考): [リンク](https://cert.crosscert.com/윈도우windows-운영체제-pc에서-루트인증서-설치방법/)
> - Chromeブラウザでの対応手順(参考): [リンク](https://docs.vmware.com/en/VMware-Adapter-for-SAP-Landscape-Management/2.0.1/Installation-and-Administration-Guide-for-VLA-Administrators/GUID-D60F08AD-6E54-4959-A272-458D08B8B038.html)
