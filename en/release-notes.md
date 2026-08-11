<!-- pre-align:aligned sig=dead32bfc6a5 -->

<a id="content-delivery-cdn-release-notes"></a>
## Content Delivery > CDN > Release Notes { #content-delivery-cdn-release-notes }

<a id="june-23-2026"></a>
### June 23, 2026 { #june-23-2026 }

<a id="june-23-2026-feature-updates"></a>
#### Feature Updates

* Added CDN Public API v3.0
  * For more information, see [API v3.0 Guide](./api-guide-v3.0/).

<a id="april-28-2026"></a>
### April 28, 2026 { #april-28-2026 }

<a id="april-28-2026-added-features"></a>
#### Added Features
* Added alias domain feature
	* Added an alias domain feature that allows you to register a domain you own as an alias for a CDN service domain.
	* Supports DNS TXT record addition, HTTP file authentication, and HTTP redirect authentication as domain ownership validation methods.
	* For more information, see [Console Guide > Alias Domain](./console-guide/#alias-domain).
* Added alias domain API
	* Added APIs for alias domain registration, retrieval, and deletion, as well as domain validation, validation status refresh, and validation token reissue.
	* For more information, see [API v2.0 Guide > Alias Domain API](./api-guide-v2.0/#alias-domain-api).

<a id="april-28-2026-feature-updates"></a>
#### Feature Updates
* Discontinued support for Top Contents by Hits statistics in the console
	* Support for the Top Contents by Hits statistics feature (ranking of most downloaded content) provided in the Statistics tab of the console has been discontinued.


<a id="april-29-2025"></a>
### April 29, 2025 { #april-29-2025 }

<a id="april-29-2025-feature-updates"></a>
#### Feature Updates
* Ended support for the Statistics Public API 
	* Support Ended APIs
		* Query Traffic Statistics
		* Query Statistics by HTTP Status Code
		* Query Ranking Statistics for Content with the Most Downloads
		
<a id="june-25-2024"></a>
### June 25, 2024 { #june-25-2024 }

<a id="june-25-2024-feature-updates"></a>
#### Feature Updates
* Changed the CDN Public API domain
	* Previous: https://kr1-cdn.api.nhncloudservice.com
	* Current: https://cdn.api.nhncloudservice.com

<a id="march-12-2024"></a>
### March 12, 2024 { #march-12-2024 }

<a id="march-12-2024-feature-updates"></a>
#### Feature Updates
* Changed UI design of the console page

<a id="november-28-2023"></a>
### November 28, 2023 { #november-28-2023 }

<a id="november-28-2023-feature-updates"></a>
#### Feature Updates
* Improved Top Content By Hits graph
  * Fixed an issue where, when the number of content rank data is more than 10, the circular graph is broken. The graph only shows up to 10 content rankings.

<a id="september-26-2023"></a>
### September 26, 2023 { #september-26-2023 }

<a id="september-26-2023-feature-updates"></a>
#### Feature Updates
* Restriction on Statistics Search Period
  * Restricted the date range so that statistics can only be viewed within 90 days.

<a id="june-27-2023"></a>
### June 27, 2023 { #june-27-2023 }

<a id="june-27-2023-feature-updates"></a>
#### Feature Updates
* Added a featrue to set HTTP response headers
  * You can add, change, and delete headers that are passed when the CDN responds to the user. For more information, see [Console User Guide > HTTP Response Header](./console-guide/#http-response-header).

<a id="august-23-2022"></a>
### August 23, 2022 { #august-23-2022 }

<a id="august-23-2022-feature-updates"></a>
#### Feature Updates
* Added Large File Optimization
	* You can set a feature to handle large files. For more information, refer to [Console Guide > Cache](./console-guide/#cache).

<a id="july-26-2022"></a>
### July 26, 2022 { #july-26-2022 }

<a id="july-26-2022-feature-updates"></a>
#### Feature Updates
* Added Allow Method Settings
	* You can set whether to allow the POST, DELETE, PUT, PATCH requests. For more information, refer to [Console Guide > Allow Method Settings](./console-guide/#method).
* Added Configuration of Cache
	* Added Bypass Cache, No Store in Configuration of Cache. For more information, refer to [Console Guide > Cache](./console-guide/#cache).

<a id="june-30-2022"></a>
### June 30, 2022 { #june-30-2022 }

<a id="june-30-2022-feature-updates"></a>
#### Feature Updates
* Added Certificate API
	* Added certificate issue/query/delete API. For more information, refer to [API v2.0 Guide > Certificate API](./api-guide-v2.0/#certificate-api).
* Added Statistics API
	* Added API to query network traffic volume, statistics by HTTP status code, and ranking statistics for content with the most downloads. For more information, refer to API v2.0 Guide > Statistics API.


<a id="may-24-2022"></a>
### May 24, 2022 { #may-24-2022 }

<a id="may-24-2022-feature-updates"></a>
#### Feature Updates
* Added an API to create an Auth Token
	* Added an API to create an authentication token that is required to access content for which access control for Auth Token authentication is enabled. For more information, refer to [API v2.0 Guide > Create an Auth Token](./api-guide-v2.0/#create-an-auth-token).

<a id="december-28-2021"></a>
### December 28, 2021 { #december-28-2021 }

<a id="december-28-2021-feature-updates"></a>
#### Feature Updates
* Enabled HTTP/2 protocol support for the CDN service. HTTP/2 is supported by default.
* Added the origin type setting feature
	* You can set as the origin server by retrieving object storage and instance information from NHN Cloud. For more information, refer to [Console Guide > Origin Server](./console-guide/#origin).

<a id="november-23-2021"></a>
### November 23, 2021 { #november-23-2021 }

<a id="november-23-2021-feature-updates"></a>
#### Feature Updates
* Added a feature to set whether to include a query string in cache key. For more information, refer to [Console Guide > Cache](./console-guide/#cache).

<a id="july-27-2021"></a>
### July 27, 2021 { #july-27-2021 }

<a id="july-27-2021-feature-updates"></a>
#### Feature Updates
* The CDN Public API domain has been changed.
    * AS-IS: https://api-gw.cloud.toast.com/tc-cdn
    * TO-BE: https://kr1-cdn.api.nhncloudservice.com

<a id="may-25-2021"></a>
### May 25, 2021 { #may-25-2021 }

<a id="may-25-2021-feature-updates"></a>
#### Feature Updates
* The root path accessibility function has been added. For more details, please refer to [Console Guide > Root Path Access Control ](./console-guide/#controlling-the-access-of-root-path).

<a id="october-6-2020"></a>
### October 6, 2020 { #october-6-2020 }

<a id="october-6-2020-feature-updates"></a>
#### Feature Updates
* Access management by token authentication has been added. For more details, visit [Console Guide > Auth Token Access Management](./console-guide/#access-control-for-auth-token-authentication).
* Access Management for Referrer Header: Added the setting to enable or disable content access, when there is no referrer request header.

<a id="june-23-2020"></a>
### June 23, 2020 { #june-23-2020 }

<a id="june-23-2020-feature-updates"></a>
#### Feature Updates
* Support for the following service domain has been closed:[ServiceID].cdn.toastcloud.com


<a id="march-24-2020"></a>
### March 24, 2020 { #march-24-2020 }

<a id="march-24-2020-feature-updates"></a>
#### Feature Updates
* CDN Service Regions: Provided for the GLOBAL region only.
	* Korea-only CDN service is to be closed.
	* Please use the Global Service region that includes the Korea region.
* Change of New CDN Service Domain
	* When a new CDN service is created, [ServiceID].toastcdn.net is provided as service domain address
	* Previous [ServiceID].cdn.toastcloud.com service domain cannot be issued anew; since [ServiceID].cdn.toastcloud.com is valid until 10:00:00 KST of May 26, 2020, it must be migrated to a new CDN service.
	* Regarding migration method, see [Migration Guide](./migration/).
* Support of HTTP/HTTPS Service Protocols
	* [Service ID].toastcdn.net, which is issued for a new service, supports HTTP/HTTPS by default.
* Added CDN Service Setup Option
	* Setting HTTP/HTTPS Port at Origin Server: Service port can be set for each HTTP/HTTPS protocol at the origin server.
	* Downgrading HTTP Protocols: HTTPS request from CDN Server to the Origin Server can be downgraded to HTTP protocol.
	* Forward Host Header: When a content is requested from CDN server to origin server, either host name or requested host header can be selected as host header.
	* For more details, see [Console User Guide](./console-guide/).
* Added Certificate Management Features
	* To use CDN service with your own domain, HTTPS protocol service is provided as part of certificate management features. With certificate management, certificates can be easily issued and automatically renewed before expired.
	* For more details, see [Console User Guide > Certificate Management](./console-guide/#certificate).
* API Support for (Old) Service Domain (*.cdn.toastcloud.com) and (New) Service Domain (*.toastcdn.net)
	* (Old) [ServiceID].cdn.toastcloud.com is available without changing the old API (lower than v1.5). However, newly added features are not available.
	* (New) [ServiceID].toastcdn.net is available even without changing previous API (lower than v1.5). New features are added to API specifications that are higher than v1.5.
* Cache Purging
	* High-speed Cache Purging: Cache is completely purged within seconds after it is requested. With high-speed cache purging, changed content can be applied to raise its credibility.
	* Changed Request Method for Cache Purging of Specific File Type: It has been updated to enter the entire URL address of a file to purge a cache.
		* e.g., Previously: /images/img.png → Now: http://[ServiceID].toastcdn.net/images/img.png
	* Closed the wildcard-type cache purging service
	* Changed usage restriction policy
		* For more details, see [Console User Guide > Purging CDN Cache](./console-guide/#purge).
* Surveillance setting is to be closed.


<a id="february-26-2019"></a>
### February 26, 2019 { #february-26-2019 }

<a id="february-26-2019-feature-updates"></a>
#### Feature Updates
* Fixed Purging Error at Particular CDN Service 
	* With the origin path set at the origin server, it was not properly purged if the path does not include the origin path: the error has been fixed.
		* The purging path must exclude origin path of the origin server.
	* Fixed an error in which it was not properly purged when a multiple number of domain aliases were registered. 
	
* Domain Alias Restriction
	* No more than 3 domain aliases are allowed.


<a id="january-15-2019"></a>
### January 15, 2019 { #january-15-2019 }

<a id="january-15-2019-feature-updates"></a>
#### Feature Updates
* APIs for Partial CDN Modification 
	* Added APIs to modify only partial service settings.

<a id="august-28-2018"></a>
### August 28, 2018 { #august-28-2018 }

<a id="august-28-2018-feature-updates"></a>
#### Feature Updates
* Validity Checks for CDN Service Setting 
	* Added validity checks for setting information to check invalid CDN setting information. 

<a id="may-29-2018"></a>
### May 29, 2018 { #may-29-2018 }

<a id="may-29-2018-feature-updates"></a>
#### Feature Updates
* Updates for CDN API v1.5
	* Upgraded API stability to provide better quality service.
	* With the completion of service deployment (change), successful task and service status is sent via callback.
* Deployment status shows on dashboard to find processing status of service deployment (change).
	* When service deployment (change) is made via API, service status can be found on console, via v1.5 or higher APIs only. 


<a id="january-25-2018"></a>
### January 25, 2018 { #january-25-2018 }

<a id="january-25-2018-feature-updates"></a>
#### Feature Updates
* Added Delete CDN API 
* Added callback service to Create and Modify CDN 
	* Calls to create or modify CDN service can be registered via console or API.
		* After service is completely created or modified, the newly created or modified CDN information is delivered via registered callback. 

<a id="july-20-2017"></a>
### July 20, 2017 { #july-20-2017 }

<a id="july-20-2017-feature-updates"></a>
#### Feature Updates
* Deployed CDN APIs. For more details, see API Guide.  
	* Added Create, Modify, and Query CDN APIs.
	* Added Purge, and Query Purge APIs.

* Supports Lower Paths of Origin Server
	* Only domain- or IP-based origin servers were available for setting, but lower paths of origin server can also be set now.

* Upgraded Features of Statistics
	* Upgraded UIs to easily find statistics by each time unit (hourly, daily, weekly, or monthly).
	* Adjusted statistical unit by search period.
		* Every 5 minutes when the search period is below 6 hours
		* Every hour when the search period is below 1 day
		* Every day when the search period is over 1 day 
	* Three types of statistics are provided, and delays may occur between statistical data and actual data. 
		* Traffic Usage Volume: Network bandwidth and transfer volume are available. 
		* Statistics of Each HTTP Response: CDN cache hit ratio is available by HTTP status code.
		* Top contents: The most-searched content can be found. 

<a id="july-20-2017-bug-fixes"></a>
#### Bug Fixes
* Fixed bugs in Statistics > Service Name Selection UI
	* Fixed an error in which the service name selection UI is only partially exposed when service description is long.

<a id="december-22-2016"></a>
### December 22, 2016 { #december-22-2016 }

<a id="december-22-2016-feature-updates"></a>
#### Feature Updates
* Updated to change status to 'OPEN' at an available access time when creating a service 
* Supports CORS (Cross-Origin Resource Sharing)

<a id="december-22-2016-bug-fixes"></a>
#### Bug Fixes
* Fixed inoperability of Global Purge
