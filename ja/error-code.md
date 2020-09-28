
## Content Delivery > CDN > エラーコード

| errorCode | errorMessage | 説明 |
| --- | --- | --- |
| 0 | SUCCESS | 処理成功 |
| -1 | FAIL | 処理失敗 |
| -2 | ASSERT | 処理失敗 |
| 10000 | Domain does not exist. | 存在しないサービスです。 |
| 10001 | There is no path to purge. | 再配布するパスを入力してください。 |
| 10002 | The HTTP Method or url of Callback URL is not valid. | Callback URLのHTTP MethodまたはURLが入力されていません。 |
| 10003 | Callback url format is not valid. | Callback URLのURL形式が正しくありません。 |
| 10004 | Exist a creating service. Please retry after it is finished. | 要求した作業を実行できない状態です。以前に要求した作業が完了した後に再度お試しください。
| 10006 | Not found resource | 要求したリソースが見つからないか、無効な要求情報です。 | 
| 10007 | Alreay exists domain alias. | すでに他のCDNサービスドメインに登録されているドメインエイリアス(Domain Alias)です。 | 
| 10008 | Origin path must starts with a slash('/). | ソースパスは'/'から入力してください。| 
| 10009 | Origin path must not end with a slash('/). | ソースパスの最後の文字に'/'を入力できません。 | 
| 10010 | Origin domain is requried. | 原本サーバーは必須入力事項です。 | 
| 10011 | Please enter origin domain without scheme. | 原本サーバーにはhttp://などのスキーマ(scheme)情報を入力できません。 | 
| 10012 | Please enter the origin domain in public IP or domain format. | 原本サーバー形式が正しくありません。ドメインまたはIP形式で入力してください。 | 
| 10013 | Domain alias is invalid. | Domain Alias形式が正しくありません。 | 
| 10014 | Referrers is invalid. Enter referrers regular expression. | リファラー(referrer)形式が正しくありません。有効な正規表現かを確認してください。 |
| 10015 | Please enter use orgin 'Y' or 'N'. | キャッシュ満了設定の原本設定を使用するかは、'Y'または'N'を入力してください。  |
| 10016 | Please enter origin. | 原本サーバー設定がないか、正しくありません。 |
| 10017 | Invalid Purge Type. Please enter purge type 'ITEM' or 'ALL' | 再配布(Purge)タイプが正しくありません。タイプは'ITEM'または'ALL'を入力してください。 |
| 10018 | Invalid Origin Port. Please enter a number between 0 and 65,536. | 原本サーバーのポートが正しくありません。0～65,536の数字を入力してください。 |
| 20001 | Failed to authenticate with AppKey. | AppKeyが有効ではありません。 |
| 20002 | Failed to authenticate with the secret key. | SecretKeyが有効ではありません。 |
| 30000 | Failed to process request. Please try again later. | 要求された処理に失敗しました。 |
| 30001 | Purge usage has exceeded. Please try again later. | 再配布使用量を越えました。 |
| 30002 | Data not created or does not exist. Please try again later. | データが作成されていないか、存在しません。 |
| 30003 | Request not processed in current service status. | 現在のサービス状態で処理できない要求です。 |
| 30004 | The status of the service is unknown. | 不明なサービス状態です。 |
| 30005 | Resource not found. | リソースが見つかりません。 |
| 80500 | An unknown error occurred during processing. Please contact the service provider. | 処理中に不明なエラーが発生しました。サービス提供者にお問い合わせください。
