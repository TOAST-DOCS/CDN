
## マイグレーションガイド

### 概要 
2020年3月24日TOAST定期メンテナンスの前に作成した**[サービスID].cdn.toastcloud.com**ドメインのCDNサービスは、**2020年5月26日10:00:00 KSTまで**サービスが提供されます。
サービス提供期間後は、**[サービスID].cdn.toastcloud.com**ドメインのCDNサービスは終了します。

**[サービスID].cdn.toastcloud.com**ドメインのCDNサービス終了後は、下記の作業が行えません。
  - CDNサービスドメインでコンテンツのダウンロード
  - コンソールとAPIからサービスの操作

CDNサービスを継続して利用するには、下記のガイドを参照してマイグレーション作業を進行してください。 

## マイグレーション進行順序

### 1. CDNサービスマイグレーション対象確認
1. TOAST CDNコンソールページの**CDNサービス**タブに移動します。 
2. サービスドメインが**[サービスID].cdn.toastcloud.com**の場合、マイグレーションが必要なCDNサービスです。
    ![マイグレーション-対象リスト](https://static.toastoven.net/prod_cdn/v2/migration-target-list.png)
3. サービス名の横にある**マイグレーション**ボタンをクリックします。 
4. **マイグレーション**ボタンをクリックすると、CDNサービス作成画面が表示されます。CDNサービス作成画面は既存CDNサービス設定情報がデフォルトで設定されています。 

### 2. 新規CDNサービス設定を確認してサービス作成

1. CDNサービス作成画面でCDNサービス設定内容を確認します。
    CDNサービス設定の詳細な情報は[ユーザーコンソールガイド](./console-guide/#cdn)を参照します。

    ![マイグレーション-サービス作成1](https://static.toastoven.net/prod_cdn/v2/migration-create-modal.png)
    ![マイグレーション-サービス作成2](https://static.toastoven.net/prod_cdn/v2/migration-create-modal-options.png)    
    
    1. **サービス地域** 
        - KOREA(韓国)サービス地域をサポートしません。 
        - 韓国を含んでいるGLOBALサービス地域のみ提供されます。 
        - **中国とロシア**はGLOBALサービス地域に含まれていません。

    2. **ドメインエイリアス** 
        - ドメインエイリアスに設定されたドメインで暗号化通信(HTTPS)を利用するには、先に証明書を発行してください。
            - CDNサービスを作成する前に**証明書管理**タブで**新規証明書発行**ボタンをクリックして先に証明書を発行してください。証明書の発行を行わなかった場合、HTTPSプロトコルサービスを利用する時、証明書エラーが発生します。
            - 証明書の詳細は、[ユーザーコンソールガイド > 証明書管理](./console-guide/#_5)を参照してください。
        
    3. **オリジンサーバー**
        - 既存CDNサービスはHTTPプロトコル転送のみサポートしていましたが、新規CDNサービスは**[サービスID].toastcdn.net**サービスドメインに対してHTTP/HTTPSプロトコルの転送をサポートします。
        - **オリジンサーバーポート**はオリジンサーバーで運用中のHTTP/HTTPSポート番号を設定します。設定していない場合、未設定と表示され、基本ポート(HTTP:80、HTTPS:443)に設定されます。
            - オリジンサーバーは、許可されたポートのみ使用できます。許可されたポートは[ユーザーコンソールガイド > オリジンサーバー](./console-guide/#_2)の**[表2]使用可能なオリジンサーバーポート番号**を参照してください。
        - CDNサービスドメイン(またはドメインエイリアス)で暗号化通信(HTTPS)をサポートするには、オリジンサーバーでHTTPSレスポンスをサポートする必要があります。
            - CDNエッジ(edge)サーバーは、｢man-in-the-middle(MITM)｣攻撃を防ぐためにオリジンサーバーの証明書の有効性を確認します。 
            - オリジンサーバーにはTOAST CDNが信頼する証明書または認証機関(CA、certificate authority)の証明書がインストールされている必要があります。
            - TOAST CDNが信頼する証明書または認証機関は[ユーザーコンソールガイド > オリジンサーバー](./console-guide/#_2)の**[表1]信頼する証明書リスト**を参照してください。
        - もしオリジンサーバーがHTTPSレスポンスをサポートするのが難しい場合、**原本リクエストHTTPプロトコルダウングレード**設定を利用してください。 
            - ただし、**原本リクエストHTTPプロトコルダウングレード**設定は、制約があるため、内容を必ず確認してください。 

    4. **原本リクエストHTTPプロトコルダウングレード**
        - オリジンサーバーがHTTPレスポンスのみサポート可能な場合、原本リクエストHTTPプロトコルダウングレード設定を**使用**に設定します。 
        - 原本リクエストHTTPプロトコルダウングレードを**使用**に設定した場合、CDNエッジ(edge)サーバーから原本をリクエストした時、HTTPSプロトコルリクエストはHTTPプロトコルにダウングレードしてリクエストします。 
        - ただし、この設定は次のような制約があるため、注意して使用してください。 
            1. サイトアドレス全体はプロトコルのダウングレードができません。(例：オリジンサーバーのサイトアドレス全体｢www.toast.com｣はダウングレードできません。)
            2. GET、HEADおよびOPTIONSメソッド以外のメソッドはサポートされません。
            3. CDNサーバーからオリジンサーバーにダウングレードをリクエストする時、次のヘッダは除外される場合があります。
                - Origin, Referer, Cookie, Cookie2, sec-\*, proxy-\*

    5. **Forward Host Header** 
    CDNサーバーがオリジンサーバーに原本ファイルをリクエストする時に伝達する**Host**ヘッダ値を設定します。
 オリジンサーバーがName-based virtual hostで運用中の場合、**リクエストホストヘッダ**設定が必要な場合もあります。オリジンサーバーの運用形態に従って適切な設定値を選択してください。
        - 原本ホスト名：オリジンサーバーのホスト名をHostヘッダに設定します。 
        - リクエストホストヘッダ：クライアントリクエストのHostヘッダに設定します。

    6. **キャッシュ** 
    CDNキャッシュサーバーのキャッシュ運用方法とキャッシュ時間を設定します。 
        - **原本設定を使用**：オリジンサーバーのレスポンスで提供したキャッシュ制御ヘッダ(Cache-Control、Expires)を優先して適用します。もしオリジンサーバーのレスポンスにキャッシュ制御ヘッダ(Cache-Control、Expires)が有効ではないか、存在しない場合、キャッシュ時間(秒)に指定した時間キャッシュします。**原本設定を使用**オプションがデフォルト値です。
        - **ユーザー設定を使用**：キャッシュ時間(秒)に指定した時間、キャッシュします。
        - **キャッシュ時間(秒)**：CDNキャッシュサーバーに原本ファイルをどれだけの時間キャッシュするか、TTLを設定します。

2. **確認**ボタンをクリックしてCDNサービスを作成します。 
3. 新たに作成されたCDNサービスは、**[サービスID].toastcdn.net**ドメインで作成され、青色の感嘆符アイコンが表示されます。
    ![マイグレーションサービス作成後リスト](https://static.toastoven.net/prod_cdn/v2/migration-new-create.png)

    
### 3. 新規作成したCDNサービスのテストと運用サービスの適用

新規作成した**[サービスID].toastcdn.net**サービスを、運用中のサービスへ適用する前にテストを行ってサービスへと配布します。


#### 3.1基本サービスドメイン([サービスID].cdn.toastcloud.com)でサービス中の場合 
1. 新規作成したCDNサービスをテストするためのテスト用ビルドを作成します。 
    - テスト用ビルドは、既存**[サービスID].cdn.toastcloud.com**のサービスドメインの代わりに、新規作成したCDNサービスの **[サービスID].toastcdn.net**サービスドメインアドレスでビルドします。 
3. テスト用ビルドをローカル環境または開発環境サーバーで実行し、動作に問題がないかテストします。 
    ![基本サービスドメイン-テスト環境](https://static.toastoven.net/prod_cdn/v2/ja/migration-test-test-build-before.png)
4. テストが完了したら、運用中のサービスビルドに新規作成したCDNサービスドメイン**[サービスID].toastcdn.net**を適用して配布します。
    ![基本サービスドメイン-テスト環境-適用](https://static.toastoven.net/prod_cdn/v2/ja/migration-test-test-build-after.png)

#### 3.2ドメインエイリアス(domain alias)でサービス中の場合 
ドメインエイリアスは基本で提供するサービスドメインアドレス**[サービスID].cdn.toastcloud.com**ではなく、所有ドメインでCDNサービスを利用する場合です。

##### [事前作業]クライアントとCDNエッジ(edge)間で暗号化通信(HTTPS)をサポートするには、先に[証明書発行]作業を進行します。
1. クライアントとCDNエッジサーバー間で暗号化通信(HTTPS)をサポートするには、CDNエッジサーバーへ所有ドメインの証明書配布が必要になります。証明書の発行と配布作業は**証明書管理**タブで行えます。[ユーザーコンソールガイド > 証明書管理](./console-guide/#_5)を参照して **証明書の発行および配布**段階まで進行します。 
2. 証明書の発行および配布が完了したら、CDNサービス連携段階で**CNAMEレコード設定を除き、**ドメインエイリアス設定のみ新規作成されたCDNサービスに設定します。 
    - **CNAMEレコード設定は運用中のサービスに影響を与えるため、テストが完了した後に行う必要があります。**

##### テスト方法1：ローカルPC環境のhostsファイルを編集して確認する方法
1. nslookupコマンドを使って**[サービスID].toastcdn.net**のAレコードに設定されたCDNエッジ(edge)サーバーのIPアドレスを確認します。
    - nslookupコマンドは、OSによってコマンドや結果の形式が異なる場合があります。
    AレコードのIPアドレスを確認します。下記の例のように複数登録されている場合があります。複数照会される場合は任意の1つのIPのみ使用してください。

    ![nslookup-IP確認](https://static.toastoven.net/prod_cdn/v2/migration-nslookup.png)

2. テストを行うローカルまたは開発環境のhostsファイルに確認したエッジIPアドレスとドメインを入力します。
    ```
    xxx.xxx.xxx.xxx your-alias.domain.com
    ```
3. ローカルまたは開発環境でHostsファイルを編集し、動作に問題がないかテストします。
   ![ドメインエイリアス-hostsファイル編集](https://static.toastoven.net/prod_cdn/v2/ja/migration-test-alias-hosts-before.png)
4. テストが完了したら、運用中のドメインエイリアスのCNAMEレコードを**[サービスID].toastcdn.net**に委任します。 
   ![ドメインエイリアス-hostsファイル編集-適用](https://static.toastoven.net/prod_cdn/v2/ja/migration-test-alias-hosts-after.png)


##### テスト方法2：新しいDomain Aliasを作成してテストする方法 
1. hostsファイルの変更ができない場合(モバイルアプリケーションなど)は、新しいドメインエイリアスをテスト用に作成して確認します。(例：alias-for-test.doamin.com)
2. 任意の新しいドメインエイリアスも証明書の発行が必要なため、証明書管理で先に証明書の発行と配布を行ってください。(文書上部の**[事前作業]クライアントとCDNエッジ(edge)間で暗号化通信(HTTPS)をサポートするには、先に[証明書発行]作業を進行します。**内容を参照します。)
3. テスト用ビルドを作り、任意の新しいドメインエイリアスで設定してテスト用ビルドを作成します。 
4. ローカルまたは開発環境でサービスを起動し、サービスの動作に問題がないかテストします。
   ![ドメインエイリアス-テストビルド](https://static.toastoven.net/prod_cdn/v2/ja/migration-test-alias-build-before.png)
5. テストが完了したら、ドメインエイリアスのCNAMEレコードを**[サービスID].toastcdn.net**に委任します。 
   ![ドメインエイリアス-テストビルド-適用](https://static.toastoven.net/prod_cdn/v2/ja/migration-test-alias-build-after.png)

### 4. 既存CDNサービスの削除 
1. すべてのマイグレーション作業が完了したら、**統計**タブで既存CDNサービスの統計を照会します。 
2. 既存CDNサービスの統計を確認して、トラフィック流入がなければ、既存**[サービスID].cdn.toastcloud.com**サービスを削除します。統計データは約30分遅延して作成されるため、十分に時間を置いてトラフィックの流入を確認します。
  ![マイグレーション完了後、削除](https://static.toastoven.net/prod_cdn/v2/migration-old-delete.png)
