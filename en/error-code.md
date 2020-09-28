## Content Delivery > CDN > Error Codes

| errorCode | errorMessage | Description |
| --- | --- | --- |
| 0 | SUCCESS | Successful |
| -1 | FAIL | Failed |
| -2 | ASSERT | Failed |
| 10000 | Domain does not exist. | Service does not exist. |
| 10001 | There is no path to purge. | Enter path to purge. |
| 10002 | The HTTP Method or url of Callback URL is not valid. | HTTP method or url of callback URL is not entered. |
| 10003 | Callback url format is not valid. | Callback url format is not valid. |
| 10004 | Exist a creating service. Please retry after it is finished. | Cannot execute requested task. Try again after previous request is completed. |
| 10006 | Not found resource | Resource is not found as requested, or the request is invalid. |
| 10007 | Alreay exists domain alias. | Domain Alias is already registered in another CDN service domain. |
| 10008 | Origin path must starts with a slash('/). | Start with '/' to enter origin path. |
| 10009 | Origin path must not end with a slash('/). | '/' is not allowed as the last character of the origin path. |
| 10010 | Origin domain is requried. | Origin server is required. |
| 10011 | Please enter origin domain without scheme. | Origin server cannot have scheme information, such as http://. |
| 10012 | Please enter the origin domain in public IP or domain format. | The origin server format is not valid: enter in domain or IP. |
| 10013 | Domain alias is invalid. | The Domain Alias format is not valid. |
| 10014 | Referrers is invalid. Enter referrers regular expression. | The referrer format is not valid: check if it is valid regular expression. |
| 10015 | Please enter use orgin 'Y' or 'N'. | Enter 'Y' or 'N'  regarding whether to use origins for cache expiration setting. |
| 10016 | Please enter origin. | Origin server setting is not available or invalid. |
| 10017 | Invalid Purge Type. Please enter purge type 'ITEM' or 'ALL' | The purge type is not valid: enter 'ITEM' or 'ALL'. |
| 10018 | Invalid Origin Port. Please enter a number between 0 and 65,536. | The origin server port is not valid: enter a number between 0 and 64, or 536. |
| 20001 | Failed to authenticate with AppKey. | Appkey is not valid. |
| 20002 | Failed to authenticate with the secret key. | SecretKey is not valid. |
| 30000 | Failed to process request. Please try again later. | Failed to process request. |
| 30001 | Purge usage has exceeded. Please try again later. | Purge volume has been exceeded. |
| 30002 | Data not created or does not exist. Please try again later. | Data has not been created or is not available. |
| 30003 | Request not processed in current service status. | Cannot process the request under the current service status. |
| 30004 | The status of the service is unknown. | The service status is unknown. |
| 30005 | Resource not found. | Resource cannot be found. |
| 80500 | An unknown error occurred during processing. Please contact the service provider. | Unknown error has occurred while processed. Contact service provider. |
