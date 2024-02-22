## Content Delivery > CDN > コンソール使用ガイド

この文書はNHN Cloud CDNコンソールでCDNサービスを構成して利用する方法を説明します。

## CDNサービス作成

**Contents Delivery > CDNのCDNサービス**タブで**作成**ボタンをクリックすると、**CDNサービス作成**ダイアログボックスが表示されます。
CDNサービスドメインは**[サービスID].toastcdn.net**の形式で自動作成されます。所有しているドメインをサービスドメインで利用するには、**ドメインエイリアス**(Domain Alias)機能を利用できます。
作成をリクエストした後、サービスの配布が完了するまで最大2時間かかります。配布が完了した後、サービスを利用できます。

> **[参考] CDN最初作成時、ダウンロード最適化所要期間**
> CDNを初めて作成した後、最大3日間はダウンロード速度が多少遅くなる場合があります。

### 基本情報
基本情報を設定します。
![CDNサービス作成-基本情報](https://static.toastoven.net/prod_cdn/v2/ja1/console-cdn-create-default_ja_202112.png)

- **サービス地域**
  GLOBALサービス地域は、全世界の拠点にあるCDNエッジサーバーを通してCDNサービスを提供します。
 ただし、**中国とロシア**はサービス地域から除外されます。

- **説明**
  CDNサービスの説明を追加します。

- **ドメインエイリアス**
  TOSAT CDNは基本的に**[サービスID].toastcdn.net**形式のサービスドメインアドレスを提供しています。
 基本サービスドメインアドレスではない所有ドメインでCDNサービスを利用するには**ドメインエイリアス**で設定してください。
 所有しているドメインでHTTPSプロトコルサービスを利用するには、まず**証明書管理**タブで証明書を発行した後、ドメインエイリアスを設定してください。
 ドメインエイリアスを設定した後は、ドメインのDNSサービス提供業者で、次のようにCNAMEレコードを登録する必要があります。DNS設定関連はDNSサービス提供業者にお問い合わせください。
    - レコードタイプ：**CNAME**
    - レコード名：**[ドメインエイリアスに登録したドメイン]**
    - レコード値(Rdata)：**[サービスID].toastcdn.net**
    - TTL：任意の値

- **コールバック**
  CDNサービス作成と変更作業(修正、一時停止/再開、削除)は数時間かかります。
 作業が完了した後、設定したコールバックURLに変更状態とCDN設定情報を受け取るにはコールバックを設定してください。コールバックで伝達される情報は[APIガイド文書](./api-guide-v2.0/#_23)を参照してください。
    1. **HTTP Method**と**コールバックURL**を入力します。
    2. Query ParameterにCDNサービス変更作業の結果を受け取るには、**コールバックURL**に次のパス(path)変数を含めて入力してください。 
       例：http://callback.url?appKey={appKey}&status={status}&isSuccessful={isSuccessful})

| パス(path)変数 | 説明 | サンプル伝達値 |
| ------------- | --- | ------- |
| {appKey} | CDNサービスのアプリキー | コンソールで発行したアプリキー |
| {domain} | CDNサービス名 | [サービスID].toastcdn.net |
| {status} | 現在のCDNサービスの状態 | OPEN、SUSPEND、CLOSE、ERROR |
| {isSuccessful} | サービス変更作業が成功したか(API v1.0はサポートしません) | "true"または"false" |

### オリジンサーバー
CDNサービスへ配布する原本ファイルを提供するサーバーを設定します。
![CDNサービス作成-基本情報](https://static.toastoven.net/prod_cdn/v2/ja1/console-cdn-create-origin_ja_202112.png)

- **原本タイプ**
    - オブジェクトストレージ：NHN Cloud Object Storageサービスで作成したコンテナをオリジンサーバーに設定します。
        - リージョン：オブジェクトストレージのコンテナ情報を照会するリージョンを選択します。
        - 名前：オリジンサーバーに設定するコンテナの名前を入力します。コンテナアクセスポリシーが**PUBLIC**のコンテナのみをオリジンサーバーとして使用できます。コンテナがオリジンサーバーとして使用可能な場合、オリジンサーバーとソースパスにコンテナ情報が自動的に入力されます。
    - インスタンス：NHN Cloud Instanceサービスで作成したインスタンスをオリジンサーバーに設定します。
        - リージョン：インスタンスリストを照会するリージョンを選択します。
        - インスタンス：リージョンを選択して、照会されたインスタンスリストからオリジンサーバーに設定するインスタンスを選択します。選択したインスタンスのIPがオリジンサーバーに自動的に入力され、使用するオリジンサーバーのポートは直接入力する必要があります。 Floating IPが接続されたインスタンスのみをオリジンサーバーとして使用できます。
    - 直接入力：別途運営中のオリジンサーバーを設定します。

- **オリジンサーバー**
オリジンサーバーはCDNサービスへ配布する原本ファイルを提供するサーバーです。オリジンサーバーはIPv4または完全修飾ドメイン名(FQDN、fully qualified domain name)形式で入力できます。IPアドレスは変更される可能性が高いため、ドメインで設定することを推奨します。 
 運用中のオリジンサーバーがない場合は、**オリジナルタイプ**の**インスタンス**オプションを選択してNHN Cloud Instanceサービスのインスタンスを使用するか、**オブジェクトストレージ**オプションを選択してNHN Cloud Object Storageサービスのコンテナを利用してください。
  CDNサービスドメインでHTTPS転送をサポートするには、オリジンサーバーがHTTPSレスポンスをサポートする必要があります。 
これはオリジンサーバーにNHN Cloud CDNが信頼する証明書のインストールが必要であることを意味します。 
  信頼する証明書は次の表を参照してください。  
 もしオリジンサーバーがHTTPSレスポンスをサポートできない場合、**原本リクエストHTTPプロトコルダウングレード**設定を利用してください。 
 ただし、**オリジンリクエストHTTPプロトコルのダウングレード**は制約があるため、オリジンサーバーがHTTPSプロトコルをサポートすることを推奨します。 

**[表1]信頼する証明書リスト**

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

- **オリジンサーバーポート**  
 オリジンサーバーはウェブプロトコルをサポートするサービスで運用する必要があります。運用中のHTTP/HTTPSプロトコルのサービスポート番号を設定できます。 
 オリジンサーバーのポートはHTTPまたはHTTPSポートのいずれか1つを入力する必要があり、設定していないポートはポートHTTP:80、HTTPS:443に設定されます。 
 オリジンサーバーのポートは制限されたポートのみ設定できます。設定できるポート番号は次の表を参照してください。  

**[表2]使用可能なオリジンサーバーポート番号**

|ポート番号|
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

- **ソースパス**  
 ソースパスは原本ファイルのパスのうち、下層パスを設定します。コンテンツをリクエストする時、ソースパスを省略してリクエストできます。

> **[例]ソースパスを /files/imagesに設定した場合** 
>
> - 原本ファイルURL：http://your.origin.com/**files/images**/logo.png 
> - CDNサービスURL：http://[サービスID].toastcdn.net/logo.png
> - CDNサービスURLでソースパス(/files/images)を省略してリクエストできます。 
- **オリジンリクエストHTTPプロトコルのダウングレード**  
  CDNエッジ(edge)サーバーは、オリジンサーバーに原本ファイルをリクエストする時、クライアントのオリジンリクエスト(request)のサービスプロトコル(HTTP/HTTPS)でリクエストします。  
 すなわち、クライアントからHTTPSでリクエストし、オリジンサーバーがHTTPSレスポンスをサポートしていない場合、 CDNエッジサーバーからオリジンサーバーにリクエストする時、HTTPSプロトコルでリクエストするため、原本ファイルを受け取れません。
オリジンサーバーがHTTPプロトコルのみサポートしている場合、**オリジンサーバーHTTPプロトコルダウングレード**設定を使用してCDNエッジサーバーからオリジンサーバーにリクエストする時、HTTPSプロトコルをHTTPプロトコルへダウングレードしてリクエストできます。
 つまり、クライアントからCDNエッジサーバー間は暗号化通信(HTTPS)で通信し、CDNエッジサーバーからオリジンサーバー間は非暗号化通信(HTTP)で通信します。
 原本リクエストHTTPプロトコルをダウングレードする時は、次のような制約があります。
> **[注意]オリジンリクエストHTTPプロトコルのダウングレードの制約事項**
> 1. サイトアドレス全体はプロトコルのダウングレードができません。例えばオリジンサーバーのサイトアドレス全体**www.nhn.com**はダウングレードできません。
> 2. GET、HEADおよびOPTIONSメソッド以外のメソッドはサポートされません。 
> 3. CDNサーバーからオリジンサーバーにダウングレードをリクエストする時、次のヘッダーは除外される場合があります。
>    Origin, Referer, Cookie, Cookie2, sec-\*, proxy-\*
- **Forward Host Header**
  CDNサーバーがオリジンサーバーへ原本ファイルをリクエストする時に送信する**Host**ヘッダー値を設定します。
 オリジンサーバーが名前ベースのバーチャルホストで運用中の場合、**リクエストホストヘッダー**設定が必要な場合もあります。オリジンサーバーの運用形態に従って適切な設定値を選択してください。
    - **オリジンサーバーホスト名**：オリジンサーバーのホスト名をHostヘッダーに設定します。 
    - **リクエストホストヘッダ**：クライアントリクエストのHostヘッダーに設定します。

### ルートパスアクセス管理
CDNサービスのルートパスに対するアクセス制御を設定できます。
![CDNサービス作成-ルートパス](https://static.toastoven.net/prod_cdn/v2/ja1/console-cdn-create-root-path_ja_202207.png)

- **ルートパスアクセス設定**
    - **使用**:ルートパスアクセス管理機能を有効にしてルートパスに対するリクエストを遮断するか、他のページにリダイレクトするように設定します。
    - **未使用**：ルートパスアクセス管理機能を無効にします。
- **アクセス制御方式**
    - **Deny**：ルートパスに対するリクエストにHTTP Response Code 403を返します。
    - **Redirect**：ルートパスに対するリクエストをユーザーが指定したパスにリダイレクトします。
- **リダイレクトパス**
 ルートパスへのリクエストをリダイレクトするパスを入力します。リダイレクトパスは'/'で始まり、CDNサービスの下位に存在するパスにする必要があります。
- **Redirect HTTP Response Code**
    - ルートパスへのリクエストをリダイレクトし、伝達するHTTP Response Codeを設定します。 
    - Redirect HTTP Response Codeは301、302、303、307の中から選択できます。

### メソッド
CDNで基本的に許可するメソッドはGET、HEAD、OPTIONSで、それ以外のメソッドをリクエストすると拒否されます。
該当メソッド以外のメソッドを許可するには、メソッドを選択して設定します。
![CDNサービス作成-ルートパス](https://static.toastoven.net/prod_cdn/v2/ja1/console-cdn-create-root-path_ja_202207.png)

- **メソッド許可設定**
 許可設定したメソッドリクエストはキャッシュされず、オリジンサーバーに伝達されます。


### キャッシュ
CDNキャッシュ動作設定とキャッシュ時間を設定できます。
![CDNサービス作成-キャッシュ](https://static.toastoven.net/prod_cdn/v2/ja1/console-cdn-create-cache_ja_202208.png)

- **キャッシュ設定**
  CDNサーバーが原本ファイルをキャッシュするときに使用するキャッシュ設定を選択できます。
    - **オリジン設定使用**：オリジンサーバーのレスポンスで提供したキャッシュ制御ヘッダー(Cache-Control、Expires)を優先して適用します。もしオリジンサーバーのレスポンスでキャッシュ制御ヘッダー(Cache-Control、Expires)が有効ではないか、存在しない場合、キャッシュ時間(秒)に指定した時間キャッシュされます。**オリジン設定を使用**オプションがデフォルト値です。
    - **ユーザー設定使用**：キャッシュ時間(秒)に指定した時間、キャッシュされます。
    - **Bypass Cache**：設定する前に作成されたキャッシュは維持したまま、以降のコンテンツリクエストをキャッシュしません。
    - **No Store**：既存のキャッシュをすべて削除し、CDNキャッシュ機能を無効にします。

- **キャッシュの有効期限(秒)**
 キャッシュの有効期限を指定するには**ユーザー設定使用**ボタンをクリックして**キャッシュの有効期限(秒)**でキャッシュの有効期限を変更します。

- **キャッシュキーにクエリ文字列を含めるかの設定**
  URLベースで作成されるキャッシュキーにリクエストクエリ文字列を含めるかどうかを設定できます。
    - 全て含める：リクエストに含まれた全てのクエリ文字列をキャッシュキーに含めます。キャッシュキーにリクエストクエリ文字列が含まれるため、同じコンテンツリクエストに対してクエリ文字列が変更される度に新しいキャッシュキーが作成されます。リクエストクエリ文字列を変更してコンテンツを新たにキャッシュしたい場合に選択します。 **全て含める**オプションがデフォルト値です。
    - 全て除外：リクエストに含まれたクエリ文字列を全て削除してURLだけを利用してキャッシュキーを作成します。リクエストクエリ文字列が継続的に変更される必要がある場合、このオプションを設定するとキャッシュが動作します。

> **[参考]キャッシュ時間のデフォルト値と有効範囲**
> キャッシュ時間のデフォルト値は0です。デフォルト値を0に設定すると、キャッシュ時間は604,800(単位/秒)=1週間です。
> キャッシュ時間は、デフォルト値の0から2,147,483,647(単位/秒)まで入力できます。

- **Large File Optimization**
  100MB以上の大容量ファイルをサービスするとき、性能と安定性を高めるための設定です。設定していない場合はCDNで許可するファイルの最大容量は1.8GB未満で、1.8GB以上の大容量ファイルをサービスするにはこの設定を必ず使用する必要があります。

> **[参考] NHN Cloud Object Storageサービスで作成したコンテナをオリジンサーバーとして使用する場合**
> Large File Optimization機能が正常に動作するにはオリジンサーバーから渡されるETagレスポンスヘッダが二重引用符で囲まれている必要があります。
> NHN Cloud Object StorageコンテナのETagレスポンスヘッダ形式設定の詳細についてはObject StorageサービスのAPIガイド内の[コンテナ設定変更 > RFCを遵守するETag形式使用設定](../../../../ja/Storage/Object%20Storage/ja/api-guide/#_24)を参照してください。


### リファラー(Referer)ヘッダアクセス管理
リファラーリクエストヘッダーにコンテンツのアクセス管理を設定します。
![CDNサービス作成-キャッシュ](https://static.toastoven.net/prod_cdn/v2/ja1/console-cdn-create-cache_ja_202208.png)

リファラーリクエストヘッダーは、現在リクエストされたページリンクより前のウェブページアドレスを含みます。リファラーリクエストヘッダーにより、どの経路でリクエストが流入したかを把握できます。リファラーヘッダアクセス管理では、特定のリファラーリクエストヘッダーのみユーザーコンテンツにアクセスできるよう設定できます。
正規表現形式で入力できます。複数入力する場合は改行して入力します。

- **ブラックリスト(blacklist)タイプ**：
    * 特定のリファラーリクエストヘッダーのみアクセスを制限する時に適しています。
* リファラーリクエストヘッダーの値が設定した正規表現式にマッチする文字列の場合、コンテンツへのアクセスが制限されます。マッチしない文字列の場合、コンテンツへのアクセスが許可されます。
- **ホワイトリスト(whitelist)タイプ**：
    * 特定のリファラーリクエストヘッダーのみアクセスを許可する時に適しています。
    * リファラーリクエストヘッダーの値が正規表現式にマッチする文字列の場合、コンテンツへのアクセスが許可されます。マッチしない文字列の場合、コンテンツへのアクセスが制限されます。

- **リファラーヘッダがない場合、アクセス許可**
 リファラー(referer)リクエストヘッダがない場合に、コンテンツアクセスを許可するかどうかを選択します。
    - **許可**：リファラーリクエストヘッダがない場合、コンテンツアクセスを許可し、リファラーアクセス制御が動作しません。
    - **拒否**：リファラーリクエストヘッダがない場合、コンテンツアクセスが拒否され、登録されたリファラーについてのみアクセスを許可します。

> [例]
>
> * タイプ：ホワイトリスト(Whitelist)
> * 正規表現：`^https://[a-zA-Z0-9._-]*\.nhn\.com/.*`
> 任意のnhn.comサブドメインの下層パスからリソースをリクエストした場合にのみ、コンテンツアクセスを許可します。
>
> **[参考]正規表現式のエスケープ文字**
> 一部の文字は、正規表現で特殊文字として扱われます。
> 例えば、正規表現でドット(`.`)は、すべての文字にマッチする特殊文字です。
> 特殊文字としての意味ではなく、通常の文字として解釈する必要がある場合は、エスケープ文字バックスラッシュ(`\`)を特殊文字の前に追加してください。(例： `\.`)
> 正規表現の特殊文字には`^ . [ ] $ ( ) | * + ? { } \`などがあります。
> 複数のリファラーを制御する時は、次の行に連続して入力します。
> APIで複数のリファラーを設定する時は\nトークンで区切って入力します。
### Auth Token認証アクセス管理
Auth Token認証アクセス管理は、コンテンツの要求に認証トークンを追加してCDNエッジサーバーで検証されたトークンのみコンテンツアクセスを許可するセキュリティ機能です。
1度だけコンテンツアクセスを許可したり、制限されたユーザーのみコンテンツにアクセスできるように制御できます。
トークンなかったり、無効なトークンでコンテンツを要求した場合、403 Forbiddenレスポンスが返り、コンテンツにアクセスできません。

Auth Token認証アクセスをCDNサービスに適用するには、次の段階に従って作業する必要があります。

> **[注意]** 
>
> Auth Token認証アクセス管理はNHN Cloud CDNを利用してサービス中のアプリケーションでも次の実装を行う必要があります。
> 1. コンテンツアクセスに必要なトークンを作成する必要があります。
> 2. クライアント(最終コンテンツ消費者)が、作成されたトークンを含めてコンテンツを要求できるようにする必要があります。
> この作業を行わずにAuth Token認証アクセス管理を設定した場合、トークン検証失敗によりコンテンツの要求が失敗する場合があるため注意してください。
#### 1. NHN Cloud CDNコンソール > Auth Token認証アクセス管理設定
CDNコンソールで、次の内容を参考にしてAuth Token認証アクセス管理を設定します。

![CDNサービス作成-Auth Token認証アクセス管理](https://static.toastoven.net/prod_cdn/v2/ja1/console-cdn-create-auth-token_ja_202105.png)

- **トークン認証を使用するかどうか**
    - **使用**：Auth Token認証アクセス管理機能を有効化してトークン検証した後、コンテンツにアクセスできるようにします。
    - **未使用**：Auth Token認証アクセス管理機能を無効化します。
- **トークン位置**
 コンテンツ要求時、トークンを伝達する位置を選択します。
    - **Cookie(Cookie)**：標準Cookieでトークンを伝達します。Cookieの使用をサポートしないデバイスおよびブラウザはトークン認証が正常に動作しない場合があるため注意してください。
    - **リクエストヘッダ(Request header)**：リクエストヘッダにトークンを伝達します。  
    - **クエリー文字列(Query string)**：クエリー文字列にトークンを伝達します。  
- **トークン名**
    - トークン値を伝達するトークンの名前です。**token**で固定された値で、コンソール設定から変更できません。
- **トークン暗号化キー**
    - トークン作成に必要な暗号化キーです。CDNサービスを作成または修正すると、暗号化キーは自動的に作成されます。
    - 暗号化キーは外部に表示されないように注意してください。 
- **トークン認証対象設定**  
   コンテンツアクセス時、トークンを認証するファイル対象を設定します。
   トークン認証対象ファイルの場合にのみトークンを検証し、認証対象ファイルではない場合はトークン検証を行わないため、トークンなしでコンテンツアクセスが可能です。
   指定されたリクエストURLパスまたはファイル拡張子のみトークン検証を行うには、リクエストURLパスと拡張子を入力してください。未入力の場合、すべてのファイルに対してトークンを検証します。
    - **認証対象設定**：設定されたリクエストURLパスとファイル拡張子のファイルのみ、トークンを検証します。
    - **認証例外対象設定**：設定されたリクエストURLパスとファイル拡張子以外のファイルのトークンを検証します。
    - **リクエストURLパス**：コンテンツURLがリクエストURLパスと一致しない場合、トークン認証対象または例外対象に設定します。
        - リクエストURLパスは'/'で始まる必要があり、ワイルドカード文字(複数の文字列：*、単一の文字：?)を使用できます(例： /nhn/\*)。
        - リクエストURLパスはクエリー文字列は含みません。
        - リクエストURLパスはアスキー(ascii)コード文字のみ入力可能です。
        - 複数入力するには次の行に入力してください。複数入力した場合、1つだけ一致してもトークンアクセス制御が動作します。 
        - ファイル拡張子も入力した場合は、ファイル拡張子条件が一致してもトークンアクセス制御が動作します。
    - **ファイル拡張子**：コンテンツURLがファイル拡張子と一致する場合、トークン認証対象または例外対象に設定します。
        - '.'を含まないファイル拡張子を入力します(例：pdf、png)。
        - 複数入力するには、次の行に入力してください。複数入力した場合、1つだけ一致してもトークンアクセス制御が動作します。 
        - リクエストパスURLも入力した場合は、リクエストパスURL条件が一致してもトークンアクセス制御が動作します。

> **[注意]リクエストURLパスとファイル拡張子**
> リクエストURLパスとファイル拡張子の両方を設定した場合、2つの条件のうち1つだけ一致してもトークンアクセス制御が動作します。
> [例]リクエストURLパス **/nhn/\***,、ファイル拡張子**png****が設定された場合： /nhn下位のすべてのファイルまたはファイル拡張子がpngのコンテンツに対してトークンを検証します。

#### 2. トークン作成
最終コンテンツユーザーがコンテンツにアクセスするには、トークンと一緒にコンテンツを要求する必要があります。したがって、トークンを作成して最終コンテンツユーザーに発行する必要があります。
トークン作成はNHN Cloud CDNを利用してサービス中のアプリケーションで実装する必要があります。
トークンの作成方法は、次のサンプルコードを参考にしてください。

##### Javaサンプルコード
- このサンプルコードは、下記のような制約事項があります。
- JDK 7以上、org.projectlombok:lombok、org.apache.commons:commons-lang3ライブラリと依存性があります。

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
    // NHN Cloudコンソールで確認した認証トークン暗号化キー
    private static final String AUTH_TOKEN_ENCRYPT_KEY = "{NHN Cloud CDNサービスのトークン暗号化キー}";
    // トークン有効時間(seconds)
    private static final Long TOKEN_DURATION_SECONDS = 3600L;
    public static void main(String[] args) throws AuthTokenException {
        String path = "/nhn/%EC%9D%B8%EC%A6%9D/%E1%84%91%E1%85%A1%E1%84%8B%E1%85%B5%E1%86%AF.png";
        String singleWildcardPath = "/nhn/%EC%9D%B8%EC%A6%9D/*";
        String[] multipleWildcardPath = {"/nhn/%EC%9D%B8%EC%A6%9D*", "/nhn/auth/*"};
        System.out.println(" ----------------- ");
        System.out.println("基本トークン発行");
        System.out.println(" ----------------- ");
        AuthToken authToken = new AuthToken(AUTH_TOKEN_ENCRYPT_KEY, TOKEN_DURATION_SECONDS);
        System.out.println("単一URLトークン：token=" + authToken.generateURLToken(path));
        System.out.println("ワイルドカードトークン：token=" + authToken.generateWildcardPathToken(singleWildcardPath));
        System.out.println("マルチワイルドカードトークン：token=" + authToken.generateWildcardPathToken(multipleWildcardPath));
        System.out.println(" ----------------- ");
        System.out.println("セッション識別子を含むトークン発行");
        System.out.println(" ----------------- ");
        AuthToken authTokenWithSession = new AuthToken(AUTH_TOKEN_ENCRYPT_KEY, TOKEN_DURATION_SECONDS, "example-sessionId");
        System.out.println("単一URLトークン： token=" + authTokenWithSession.generateURLToken(path));
        System.out.println("ワイルドカードトークン：token=" + authTokenWithSession.generateWildcardPathToken(singleWildcardPath));
        System.out.println("複数ワイルドカードトークン：token=" + authTokenWithSession.generateWildcardPathToken(multipleWildcardPath));
    }
    public static class AuthToken {
        /** トークン暗号化アルゴリズム(SHA256固定) **/
        private static final String HMAC_SHA_256 = "HmacSHA256";
        /**トークン暗号化キー(NHN Cloud CDNコンソール > Auth Token認証アクセス管理 > 暗号化キー) **/
        private String key;
        /** セッション識別子 */
        private String sessionId;
        /**トークンの有効時間(単位：秒) */
        private Long durationSeconds;
        /**トークン作成前、url encodeを適用するかどうか */
        private Boolean escapeEarly;
        /** トークンBodyフィールドのセパレータ */
        private final String fieldDelimiter = "~";
        /** wildcardPathセパレータ */
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
        * 単一URLに対するトークンを作成します。
        * @param path : contents url (example: /auth/contents/example.png)
        * @return created token
        * @throws AuthTokenException
        */
        public String generateURLToken(String path) throws AuthTokenException {
            return generateToken(createExpireTime(), this.sessionId, path, null);
        }
        /**
        * ワイルドカードパスに対するトークンを作成します。
        * @param wildcardPath : "/auth/contents/*"
        * @return作成されたトークン値
        * @throws AuthTokenException
        */
        public String generateWildcardPathToken(String wildcardPath) throws AuthTokenException {
            return generateWildcardPathToken(new String[] {wildcardPath});
        }
        /**
        * 複数のワイルドカードパスに対するトークンを作成します。
        * @param wildcardPaths (example: ["/auth/contents/*", "/auth/*/images/*"])
        * @return作成されたトークン値
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
- **AuthTokenクラスのメンバー変数説明**
    - **key**：NHN Cloud CDNコンソールに表示されたAuth Token認証制御管理 > トークン暗号化キーを入力します。
    - **sessionId**：単一アクセスリクエストに対する固有識別子を含めてトークンを作成するにはsessionIdを入力します。
        - セッションIDごとに有効なトークンを作成して一回性トークンを作成したり、さまざまな事例に活用できます。
        - セッションIDは[出力可能アスキー文字表](https://ko.wikipedia.org/wiki/ASCII#%EC%B6%9C%EB%A0%A5_%EA%B0%80%EB%8A%A5_%EC%95%84%EC%8A%A4%ED%82%A4_%EB%AC%B8%EC%9E%90%ED%91%9C.)で構成する必要があります。
        - セッションIDの長さは36バイトを超えてはいけません。
    - **durationSeconds**：作成されたトークンが有効な時間(秒)、有効時間が過ぎたトークンはトークン認証に失敗します。
        - トークン有効時間をあまりにも短く設定すると、CDNエッジサーバーでトークンを検証する前にトークンが有効期限切れになる場合があるため、注意してください。期待するトークン有効時間より10秒以上長く設定することを推奨します。
        - トークン作成サーバーの時間同期化設定NTP (Network Time Protocol、NTP)が有効かを必ず検証してください。同期化されていない時間情報によりトークン有効時間検証が失敗する場合があります。

- **AuthTokenクラスの公開メソッド(Public Method)**
    - **public String generateURLToken(String path)**
        - 単一パスに対するトークンを作成します。
        - [例] path: authToken.generateURLToken("/auth/contents/example.png")
        - [注意]パスまたはセッションIDはURLエンコード文字列に変更した後、トークンを作成してください(例：**/nhn/認証/ファイル.png** → **/nhn/%E8%AA%8D%E8%A8%BC/%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB.png**)。
        - [注意] **!**, **~**文字は予約された文字のため、パスまたはセッションIDに含めないでください。

    - **public String generateWildcardPathToken(String wildcardPath), public String generateWildcardPathToken(String... wildcardPaths)**
        - ワイルドカードパスとマッピングされるパスのトークンを作成します。パスのパターンが一致する場合、ワイルドカードトークン1つで複数のコンテンツURLのトークンを認証できます。
            - [例1] wildcardPath: authToken.generateWildcardPathToken("/auth/contents/*") : /auth/contents下位のすべてのファイルに対してトークンを発行します。
            - [例2] wildcardPath: authToken.generateWildcardPathToken("/auth/contents/*.png") : /auth/contentsパスのpngファイルに対するトークンを発行します。
            - [例3] wildcardPath: authToken.generateWildcardPathToken("/auth/contents/exmaple?.png") : /auth/contentsパスのexampleと単一文字が結合したpngファイルに対するトークンを発行します。
            - [注意]パスまたはセッションIDはURLエンコード文字列に変更した後にトークンを作成してください(例：**/nhn/認証/ファイル.png** → **/nhn/%E8%AA%8D%E8%A8%BC/%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB.png**)。
            - [注意] **!**, **~**文字は予約された文字のため、パスまたはセッションIDに含めないでください。
        - 作成されたトークンは**exp={expirationTime}~acl={path!path!path}~id={sessionId}~hmac={HMAC}**形式で作成されます。
            - [例]作成されたトークン: **exp=1600331503~acl=%2fnhn%2f*.png~id=session-id1~hmac=2509123dcabe2fc199e3ac44793e4e135a09590ff4ebf6a902ea26469ead7f91**

#### 3. 作成されたトークンをコンテンツリクエストに含める
クライアント(最終コンテンツ消費者)がコンテンツリクエスト時、コンソールで設定したトークン位置に作成されたトークン値を含めてリクエストするようにします。

  - **トークン位置：Cookie**
    ```
    curl --cookie "token={作成されたトークン値}" \
    -X GET http://xxx.toastcdn.net/auth/contents/example.png
    ```
  - **トークン位置：リクエストヘッダ**
    ```
    curl -H "token: {作成されたトークン値}" \
    -X GET http://xxx.toastcdn.net/auth/contents/example.png
    ```
  - **トークン位置：クエリー文字列**
    ```
    curl -d "token={作成されたトークン値}" \
    -X GET http://xxx.toastcdn.net/auth/contents/example.png
    ```


### HTTPレスポンスヘッダ
CDNからユーザーにレスポンスする際に伝達されるヘッダを追加/変更/削除する機能です。
ヘッダは重複していないヘッダー名で最大10個まで設定できます。
![CDNサービス作成-レスポンスヘッダ(イメージ修正必要)](https://static.toastoven.net/prod_cdn/v2/ja1/console-cdn-create-auth-token_ja_202105.png)

- **HTTPレスポンスヘッダ設定**
    - **使用**: HTTPレスポンスヘッダを追加/変更/削除する設定を使用します。
    - **未使用**: HTTPレスポンスヘッダを設定しません。
- **Action**: HTTPレスポンスヘッダを変更する方式を選択します。
    - **Add**:設定されたヘッダ名を追加してユーザーにレスポンスします。
    - **Modify**:設定されたヘッダ名がコンテンツに存在する場合、ヘッダ値を変更し、存在しない場合は追加してユーザーにレスポンスします。
    - **Delete**:設定されたヘッダ名を削除してユーザーにレスポンスします。
- **ヘッダ名**: Access-Control-Allow-Origin, Cache-Control, Content-Typeなどのヘッダ名をリストから選択するか、「直接入力」を選択してユーザーが定義したカスタムヘッダー名を設定できます。
- **カスタムヘッダ名**:ユーザーが定義したヘッダ名を入力します。アルファベットと数字、'-'、'_'のみ入力可能です。
- **ヘッダ値**:ヘッダ名の値を設定することができ、必須入力値です。

> **[参考] CORS(オリジン間リソース共有)設定**
> 下記のようにHTTPレスポンスヘッダを設定してCORSを許可できます。
> - **Action**: Modify
> - **ヘッダ名**: Access-Control-Allow-Origin
> - **ヘッダ値**: *(ワイルドカード)または許可する元のURI



## 設定

### CDNサービスの設定変更
サービスドメイン名と地域以外のCDNサービス設定を変更できます。
![CDNサービスの修正を有効化](https://static.toastoven.net/prod_cdn/v2/ja1/console-cdn-modify1_ja_202105.png)

1. 変更するCDNサービスをCDNサービスリストから選択します。
2. 画面下、**設定**タブの**修正**ボタンをクリックします。

次のように変更可能な項目が有効になります。
![CDNサービスの修正を確認-イメージ修正必要](https://static.toastoven.net/prod_cdn/v2/ja1/console-cdn-modify2_ja_202105.png)

* 変更する設定内容を修正します。 
* **確認**ボタンをクリックして、変更を完了します。
* 説明とコールバック以外の設定を変更するには、全てのCDNサーバーに反映される必要があるため、修正作業に時間がかかる場合があります。

**修正作業は数十分以内に完了しますが、ドメインエイリアス設定の変更時は数時間かかる場合があります。**

> **[参考] CDNサービス修正中配布状態とサービス状態** 
> サービス修正作業が進行中の場合は、既存のCDNサービス設定で運用されます。
> 修正作業が失敗した場合、既存設定情報にロールバックされ、CDNサービスリストの配布状態が赤色で表示されます。設定情報にエラーがあったり、内部的なエラーが発生した場合は修正作業が失敗します。 
### CDNサービスの一時停止と再開
CDNサービスを一時的に中断または、再開することができます。


1. 一時停止するCDNサービスを選択します。
2. **一時停止**ボタンをクリックします。
![CDNサービス-一時停止](https://static.toastoven.net/prod_cdn/v2/ja1/console-cdn-pause_ja_202105.png)
3. 証明書の連携をしているCDNサービスには、証明書の無効警告案内が表示されます。証明書の無効を回避するには、証明書更新開始日の前にCDNサービスを再開する必要があります。
![CDNサービス-一時停止](https://static.toastoven.net/prod_cdn/v2/ja1/console-cdn-restart_ja_202105.png)
4. 一時停止状態のCDNサービスを再開するには再開するCDNサービスを選択します。
5. **再開**ボタンをクリックします。




> **[参考]一時停止と再開動作の遅延**
> CDNサービスの一時停止と再開は、CDNサービスドメインのDNSレコードを変更して動作します。 
> したがってキャッシュDNSサーバーでTTLの間キャッシュされているか、DNSの配信に応じて、一時停止/再開が完了しても、即時に一時停止/再開が動作しない場合もあります。
> **[注意]発行された証明書が連携されているCDNサービスの一時停止**
> 証明書を連携しているCDNサービスを一時停止する場合、証明書の更新ができません。
> **証明書管理** > 証明書リストの**証明書更新開始日**の前にCDNサービスを再開してください。
> 証明書更新開始日から5日間は、証明書更新期間のため、この期間に一時停止を行うと証明書が無効になる場合もあるため、注意してください。

### CDNサービスの削除
CDNサービスを削除します。削除すると復旧できませんので注意してください。 

1. 削除するCDNサービスを選択します。
2. **削除**ボタンをクリックします。
![CDNサービス-削除](https://static.toastoven.net/prod_cdn/v2/ja1/console-cdn-delete_ja_202105.png)
3. 証明書が連携されているCDNサービスは、証明書無効警告案内が表示されます。証明書が無効になるのを回避するには、サービス中の他のCDNサービスに証明書を連携してください。


> **[参考] CDNサービス削除の所要時間**
> CDNサービス削除作業は数時間(最大2～3時間)かかる場合があります。
> **[注意]発行された証明書が連携されているCDNサービスの削除**
> 証明書が連携されているCDNサービスを削除すると、証明書を更新できません。 
> **証明書管理**の証明書リストから、**証明書更新開始日**より前にサービス中の他CDNサービスへ連携してください。
> 証明書更新開始日から5日間は、証明書更新期間です。この期間に削除を行うと証明書が無効になる場合があるため、注意してください。

## CDNキャッシュの再配布(purge)
CDNキャッシュサーバーは、キャッシュ設定に従って、指定された時間オリジンサーバーのファイルをキャッシュします。ファイルをキャッシュすると、原本ファイルが変更されてもキャッシュが満了するまでは変更前の原本ファイルを維持します。 
変更された原本ファイルへ即時にアップデートするには、**キャッシュ再配布**をリクエストする必要があります。
キャッシュ再配布を行うと、リクエストしたコンテンツの古いキャッシュデータを削除し、オリジンサーバーから新しい原本ファイルを再度キャッシュします。 

1. 変更したいサービスをCDNサービスリストから選択します。
2. **キャッシュ再配布**タブをクリックします。
![CDNキャッシュ再配布](https://static.toastoven.net/prod_cdn/v2/ja1/console-cdn-purge_ja_202105.png)

3. キャッシュ再配布タイプを選択します。
  - CDNサービスドメインに応じてサポートされるキャッシュ再配布タイプとリクエスト様式が異なるため注意してください。
  - ドメインの再配布タイプとリクエスト様式
    * 特定ファイル：再配布するコンテンツのURLを入力します。リクエストしたURLのみキャッシュが再配布されるため、ドメインエイリアスに複数のサービスドメインアドレスがある場合、各URLにリクエストする必要があります。
      * 例)基本サービスドメインアドレス： http://[サービスID].toastcdn.net/path/to/file1.jpg
      * 例)ドメインエイリアスドメインアドレス： http://customer.domain.com/path/to/file1.jpg
    * 全体ファイル：キャッシュファイルをすべて削除します。オリジンサーバーに膨大なトラフィックが流入する場合もあるため注意してください。
4. 選択したキャッシュ再配布タイプに合わせて、再配布するファイルを指定します。
5. **キャッシュ再配布**ボタンをクリックして、再配布をリクエストします。

キャッシュ再配布は使用量制限があるため、下記の表を参照して使用量が超過しないように注意してください。

|分類 |[サービスID].toastcdn.net |
|---|---|
| 制限単位 | プロジェクト別(AppKey) |
| 特定ファイル | 1秒間に1回リクエスト可能、リクエスト毎のURL数制限：200 URL |
| ワイルドカード | 未サポート |
| 全体ファイルタイプ | 5分毎のリクエスト可能数：1回 |

> **[注意] [サービスID].toastcdn.comサービスを作成した後、キャッシュ再配布失敗エラー**
> CDNサービスを作成した後、約1時間はキャッシュ再配布リクエストが失敗する場合があります。その後も継続して失敗する場合はサポートへお問い合わせください。
## 証明書の管理
所有しているドメインでコンテンツをHTTPSで転送するには、所有しているドメインの証明書をCDNサーバーに配布する必要があります。証明書がない場合、クライアント(ブラウザ)とCDNエッジサーバー間の暗号化通信(HTTPS)を行うことができず、証明書エラーが発生します。
NHN Cloud CDNの証明書管理は次のような機能を提供します。

- 単一ドメインタイプの証明書発行
- 全世界の拠点のCDNサーバーに証明書を配布(中国とロシアは除外)
- 証明書満了前の自動更新

### 新規証明書の発行
**証明書管理**タブで証明書を発行できます。
![CDN新規証明書の発行](https://static.toastoven.net/prod_cdn/v2/ja1/console-certificate-create_ja_202105.png)

1. **証明書管理**タブの**新規証明書発行**ボタンをクリックします。
2. 発行する証明書のドメインを全体ドメインアドレス(FQDN、fully qualified domain name)形式で入力します。
3. 証明書発行案内の内容を確認し、**確認**ボタンをクリックします。
4. 新規証明書の発行をリクエストすると、**証明書管理**タブの証明書ドメインが表示されます。証明書の状態が**ドメイン検証**状態に変更された後にドメイン検証作業を進行してください。 

> **[注意]証明書発行前の確認事項**
> 1. 所有しているドメインのみ証明書の発行が可能なため、先にドメインを購入してから進行してください。 
> 2. 他の認証機関(CA、certificate authority)で発行した証明書は利用できません。 
> 3. 単一ドメインの証明書発行のみ行えます。ワイルドカード、マルチドメインなどの証明書はサポートしません。
> 4. 証明書の発行は1つのプロジェクトにつき5個に制限されます。限度の調整が必要な場合はNHN Cloudサポートへお問い合わせください。
> 5. 新規証明書の発行をリクエストした後、ドメイン検証段階は数十分(最大1～2時間)後に変更される場合があります。証明書の状態がドメイン検証状態に変更されたらNHN Cloudプロジェクトメンバーを対象にメールが送信されます。もしシステムエラーによりメールが送信されない場合、コンソールで状態を確認してください。 
### ドメイン検証
新規証明書の発行をリクエストした後、証明書の状態が｢ドメイン検証｣になったら、ドメインの検証を行ってください。
ドメインの検証方法は、コンソールでドメインを選択して確認するか、プロジェクトメンバーに転送されたドメイン検証ガイドメールの内容を参照してください。

![CDNドメイン検証](https://static.toastoven.net/prod_cdn/v2/ja1/console-certificate-domain-validation_ja_202105.png)

ドメイン検証は、発行をリクエストした証明書ドメインの実際の所有者であることを確認する段階です。ドメイン検証を行っていない場合、証明書の発行ができません。
ドメインの所収者であることを確認するために、ドメイン検証方式を通してドメインの制御権限を確認します。 
ドメイン検証方式には、**DNS TXTレコード追加**または**HTTPページ追加**方式があります。**2つの方式のうち1つを実施**してください。

![CDNドメイン検証](https://static.toastoven.net/prod_cdn/v2/ja1/console-certificate-domain-validation2_ja_202105.png)

#### DNS TXTレコード追加方式
ドメインのDNS制御権限を確認してドメインを検証します。

1. ドメインのDNSサービス提供業者のDNS管理ページでTXTレコードを追加します。
   DNSの設定方法はDNSサービス提供業者によって異なる場合があります。設定方法は該当サービス業者へお問い合わせください。
  - レコードタイプ：**TXT**
  - TTL：**60**. 60に設定できない場合は、できるだけ小さい値を設定してください。
  - レコード名：**_acme-challenge.[発行をリクエストした証明書ドメイン].**コンソールまたは送信されたメールガイドの**レコード名**を作成します。
  - レコード値：**任意の文字列**(コンソールまたは送信されたメールガイドの**レコード値**を作成します。)

2. nslookupコマンドを使って、追加したTXTレコードを確認します。DNS配信時間に応じて確認できるまで時間がかかる場合があります。
    `nslookup -type=TXT _acme-challenge.[発行リクエストした証明書ドメイン].`

次の画面はNHN Cloud DNS+ サービスで設定した例です。DNSサービス提供業者によって設定方法は異なる場合があります。
![CDNドメイン検証](https://static.toastoven.net/prod_cdn/v2/ja1/console-certificate-domain-validation-dns_ja_202105.png)


#### HTTPページ追加方式
ドメインが接続されたWebサーバーにHTTPページを追加してドメインを検証します。

1. Webサーバーの**http://[発行リクエストした証明書ドメイン]/.well-known/acme-challenge/[任意の文字列]**パスにHTTPページを追加します。 
2. HTTPページの本文にコンソールまたは送信されたメールガイドの**ページコンテンツ(トークン)**の値を設定します。 
3. Webブラウザで**http://[発行リクエストした証明書ドメイン]/.well-known/acme-challenge/[任意の文字列]**URLで接続したら**ページコンテンツ(トークン)**の値が画面に表示されるかを確認します。 

> **[注意]ドメイン検証の注意事項**
> 1. ドメイン検証は、証明書発行リクエストを行った日から**5日以内**に実施する必要があります。**期間内に実施しなかった場合、証明書の発行は自動でキャンセル処理**が行われます。
> 2. ドメイン検証作業完了後、検証が成功した際は、数時間で証明書の発行および配布作業が行われます。1日以上実施されない場合、ドメイン検証作業の内容が正しいかを確認します。問題がないにもかかわらず実施されない場合はNHN Cloudサポートへお問い合わせください。
> 3. ドメイン検証方式のHTTPページ追加方式は、HTTPサーバーが基本ポート80で運用中の場合にのみ使用できます。ポートの変更ができない場合は、DNS TXTレコード追加方式を利用してください。
### 証明書の発行および配布
ドメイン検証に成功したら、数時間以内に証明書の発行および配布作業が実施されます。 
コンソールで証明書の状態が**証明書発行および配布**段階と表示され、NHN Cloudプロジェクトメンバーを対象に通知メールが送信されます。 
この段階では別途作業する内容はありません。

>  **[参考]証明書の発行と配布段階の作業時間**
> 証明書の発行および配布作業は最大9時間以上かかる場合があります。 
### CDNサービス連携
発行された証明書を利用するには、CDNサービスと連携する必要があります。 
この作業を行わなかったり、作業内容を維持しない場合、発行された証明書が無効になる場合があるため、注意してください。 

1. **CNAMEレコード設定**：証明書ドメインのDNSサービス提供業者のDNS管理で、次のCNAMEレコードを追加します。 
    - レコードタイプ：**CNAME**
    - TTL：任意の値。頻繁に変更する必要がある場合は小さい値に設定することを推奨します。レコード変更時、キャッシュDNSサーバーにTTL時間中はキャッシュされる場合があります。
    - レコード名：**[証明書ドメイン].**(例：test.alias.com.com.)
    - レコード値：**[連携するCDNサービスドメイン]**(例：xxxxxxxx.toastcdn.net)
次の画面はNHN Cloud DNS+ サービスで設定した例です。DNSサービス提供業者によって設定方法は異なる場合があります。
![CDNサービス連携-CNAME委任](https://static.toastoven.net/prod_cdn/v2/ja1/console-certificate-service-cname_ja_202105.png)

2. **ドメインエイリアス設定**：証明書を利用するCDNサービスにドメインエイリアス設定を追加します。
    -  **CDNサービス**タブで連携するCDNサービスを選択して**修正**ボタンをクリックします。ドメインエイリアスに証明書ドメインを追加して**確認** ボタンをクリックします。
![CDNサービス連携-ドメインエイリアス](https://static.toastoven.net/prod_cdn/v2/ja1/console-certificate-service-alias_ja_202105.png)

>  **[参考] CNAMEレコード配信時間**
> CNAMEレコードの設定時、さまざまな要因でDNSの配信に時間がかかることがあります。したがってサービス連動プロセスを正しく行った後も一定時間、証明書の発行状態が[CDNサービス連動待機]と表示されることがあります。
> 設定を正しく行っても24時間以上[CDNサービス連動待機]状態が続く場合はNHN Cloudサポートへお問い合わせください。
>  **[注意]証明書満了注意事項**
> NHN Cloud CDNで提供する証明書は、証明書が無効になる前に自動で証明書の更新を実施します。
> 自動的に証明書の更新を行うには、使用中の証明書がCDNサービスと連携している必要があります。
> CDNサービスと連携していない場合、証明書更新期間に更新することができず、証明書が無効になる場合があります。
> 証明書の更新は**証明書管理** > リストに表示された証明書更新開始日から**5日以内**に行われます。
> 証明書が無効にならないように、次の設定事項を常に維持してください。
> 
> 1. 証明書のドメインは、CNAMEレコードに連携するCDNサービスドメインアドレスに委任する必要があります。
> 2. 連携するCDNサービスのドメインエイリアスに証明書ドメインが設定されている必要があります。
> 3. 証明書が連携されたCDNサービスを一時停止すると、証明書を更新できません。証明書の更新開始日前に再開するか、運用中の他のCDNサービスに証明書を連携してください。
> 4. 証明書が連携されたCDNサービスを削除すると、証明書を更新できません。削除する前に、運用中の他のCDNサービスに証明書を連携してください。 
CDNサービス連携作業が完了すると、証明書の状態が｢正常｣と表示されます。
![CDN証明書正常状態](https://static.toastoven.net/prod_cdn/v2/ja1/console-certificate-active_ja_202105.png)

>  **[参考]発行された証明書エラー発生時の措置事項**
> NHN Cloud CDNで提供する証明書のRoot証明書の中の1つであるIdenTrust DST Root CA x3が2021年9月30日に有効期限切れになり、一部の古い端末や旧型ブラウザで問題が発生することがあります。
> クライアントでERR_CERT_DATE_INVALIDエラーにより問題が発生する場合は、以下の内容を参考にしてOS設定変更後にアップデートまたはRoot証明書の手動インストールなどの措置が必要です。
> 1. ISRG x1証明書ダウンロードリンク： [ダウンロードリンク](https://letsencrypt.org/certs/isrgrootx1.pem)
> 2. Windows OS設定変更参考ガイド：[リンク](https://docs.microsoft.com/en-us/skype-sdk/sdn/articles/installing-the-trusted-root-certificate)
> 3. Chromeブラウザ参考ガイド：[リンク](https://docs.vmware.com/en/VMware-Adapter-for-SAP-Landscape-Management/2.0.1/Installation-and-Administration-Guide-for-VLA-Administrators/GUID-D60F08AD-6E54-4959-A272-458D08B8B038.html)
## 統計

ネットワーク転送量、HTTP状態コード別の統計および、ダウンロードが最も多いコンテンツの順位統計を確認できます。
7日以内の統計データは正確ではないため、参考程度に利用してください。正確な統計データは7日後に確認してください。

1. **Contents Delivery > CDN**の**統計**タブをクリックします。
![cdn_08_201812](https://static.toastoven.net/prod_cdn/cdn_08_ja1_202105.png)
2. 統計を確認するには、CDNサービスを選択します。
3. 検索期間を入力します。
4. 検索期間内のデータ周期は、選択した期間に応じて自動的に選択されます。
5. **検索**ボタンをクリックします。

>  **[Note] 最大検索期間**
> 過去90日間のデータのみ照会できます。

>  **[参考] Top Contents By Hits統計制約事項 **
> 1日前まで、1日以上の範囲で照会が可能です。
> 100KB以下のコンテンツまたはリクエスト回数が1日50回未満のコンテンツは統計から除外されます。
