## Content Delivery > CDN > 错误代码

| errorCode | errorMessage | 说明 |
| --- | --- | --- |
| 0 | SUCCESS | 处理成功 |
| -1 | FAIL | 处理失败 |
| -2 | ASSERT | 处理失败 |
| 10000 | Domain does not exist.| 不存在的服务。|
| 10001 | There is no path to purge.| 请输入要重新分配的路径。|
| 10002 | The HTTP Method or url of Callback URL is not valid.| 未输入Callback URL的HTTP Method或URL。|
| 10003 | Callback url format is not valid.| Callback URL的URL格式错误。|
| 10004 | Exist a creating service.Please retry after it is finished.| 当前为无法执行请求操作的状态。请在完成之前的请求操作后重试。|
| 10006 | Not found resource | 无法找到请求的资源或请求信息错误。|
| 10007 | Alreay exists domain alias.| 已在其他CDN服务域注册过的Domain Alias（域别名）。|
| 10008 | Origin path must starts with a slash('/).| 原始路径请以‘/’为开头输入。|
| 10009 | Origin path must not end with a slash('/).| 原始路径的最后一个字符不可输入‘/’。|
| 10010 | Origin domain is requried.| 原始服务器为必须输入事项。|
| 10011 | Please enter origin domain without scheme.| 原始服务器中不可输入http://等模式(scheme)信息。|
| 10012 | Please enter the origin domain in public IP or domain format.| 原始服务器格式错误。请按照域或IP格式输入。|
| 10013 | Domain alias is invalid.| Domain Alias格式错误。|
| 10014 | Referrers is invalid.Enter referrers regular expression.| 引用(referrer)格式错误。请确认是否为有效的正规表达式。|
| 10015 | Please enter use orgin 'Y' or 'N'.| 是否使用缓存到期设置的原始设置请输入’Y’或 ‘N’。|
| 10016 | Please enter origin.| 无原始服务器设置或设置错误。|
| 10017 | Invalid Purge Type.Please enter purge type ‘ITEM’ or ‘ALL’ | 重新分配（purge）类型错误。类型请输入‘ITEM’或’ALL’。|
| 10018 | Invalid Origin Port.Please enter a number between 0 and 65,536.| 原始服务器端口错误。请输入0~65,536之间的数字。|
| 20001 | Failed to authenticate with AppKey.| AppKey无效。|
| 20002 | Failed to authenticate with the secret key.| SecretKey无效。|
| 30000 | Failed to process request.Please try again later.| 请求处理失败。|
| 30001 | Purge usage has exceeded.Please try again later.| 超过重新分配使用量。|
| 30002 | Data not created or does not exist.Please try again later.| 未创建数据或数据不存在。|
| 30003 | Request not processed in current service status.| 当前服务状态下无法处理的请求。|
| 30004 | The status of the service is unknown.| 未知的服务状态。|
| 30005 | Resource not found.| 无法找到资源。|
| 80500 | An unknown error occurred during processing.Please contact the service provider.| 处理时发生未知错误。请咨询服务提供商。|