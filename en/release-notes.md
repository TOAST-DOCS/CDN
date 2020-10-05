## Content Delivery > CDN > Release Notes

### September 3, 2020

#### Feature Updates 
* Referer Header Access Management: Added setting for enabling content access when there's no referer request header.  

### June 23, 2020

#### Feature Updates
* Support for the following service domain has been closed:[ServiceID].cdn.toastcloud.com


### March 24, 2020

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
	* For more details, see [Console User Guide > Certificate Management](./console-guide/#_5).
* API Support for (Old) Service Domain (*.cdn.toastcloud.com) and (New) Service Domain (*.toastcdn.net)
	* (Old) [ServiceID].cdn.toastcloud.com is available without changing the old API (lower than v1.5). However, newly added features are not available. 
	* (New) [ServiceID].toastcdn.net is available even without changing previous API (lower than v1.5). New features are added to API specifications that are higher than v1.5. 
* Cache Purging 
	* High-speed Cache Purging: Cache is completely purged within seconds after it is requested. With high-speed cache purging, changed content can be applied to raise its credibility.  
	* Changed Request Method for Cache Purging of Specific File Type: It has been updated to enter the entire URL address of a file to purge a cache. 
		* e.g.) Previously: /images/toast.png -> Now: http://[ServiceID].toastcdn.net/images/toast.png
	* Closed the wildcard-type cache purging service 
	* Changed usage restriction policy 
		* For more details, see [Console User Guide > Purging CDN Cache](./console-guide/#cdn-purge).
* urveillance setting is to be closed.

### February 26, 2019 

#### Feature Updates
* Fixed Purging Error at Particular CDN Service 
	* With the origin path set at the origin server, it was not properly purged if the path does not include the origin path: the error has been fixed.    
		* The purging path must exclude origin path of the origin server. 
	* Fixed an error in which it was not properly purged when a multiple number of domain aliases were registered. 

* Domain Alias Restriction 
	* No more than 3 domain alises are allowed. 


### January 15, 2019 

#### Feature Updates 
* APIs for Partial CDN Modification 
	* Added APIs to modify only partial service settings.

### August 28, 2018 

#### Feature Updates
* Validity Checks for CDN Service Setting  
	* Added validity checks for setting information to check invalid CDN setting information.

### May 29, 2018 

#### Feature Updates
* Updates for CDN API 1.5v
	* Upgraded API stability to provide better quality service. 
	* With the completion of service deployment (change), successful task and service status is sent via callback. 
* Deployment status shows on dashboard to find processing status of service deployment (change). 
	* When service deployment (change) is made via API, service status can be found on console, via v1.5 or higher APIs only.   


### January 25, 2018 

#### Feature Updates
* Added Delete CDN API 
* Added callback service to Create and Modify CDN 
	* Calls to create or modify CDN service can be registered via console or API. 
		* After service is completely created or modified, the newly created or modified CDN information is delivered via registered callback. 

### July 20, 2017 

#### Feature Updates
* Depooyed CDN APIs. For more details, see API Guide.   
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
	* Three types of statistics are provided, and delays may occur between statistica data and actual data. 
		* Traffic Usage Volume: Network bandwidth and transfer volume are available. 
		* Statistics of Each HTTP Response: CDN cache hit ratio is available by HTTP status code. 
		* Top contents: The most-searched content can be found.  

#### Bug Fixes
* Fixed bugs in Statistics > Service Name Selection UI  
	* Fixed an error in which the service name selection UI is only partially exposed when service description is long. 

### December 22, 2016 

#### Feature Updates
* Updated to change status to 'OPEN' at an available access time when creating a service
* Supports CORS (Cross-Origin Resource Sharing) 

#### Bug Fixes
* Fixed inoperability of Global Purge 
