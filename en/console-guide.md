## Content Delivery > CDN > Console User Guide

This document describes how CDN service is configured and applied on TOAST CDN console. 

## Creating CDN 

Go to **Content Delivery > CDN** and to **CDN Service** and click **Create**, and the **Creating CDN** window pops up.  
CDN service domain is automatically created in the **[ServiceID].toastcdn.net** format. To use your own domain, enable **Domain Alias**. 
It takes up to 2 hours to complete deployment after service is requested for creation. Service becomes available after it is completely deployed. 

### Basic Information
Set basic information.
![Creating CDN- Basic Information](https://static.toastoven.net/prod_cdn/v2/console-cdn-create-default2.png)

- **Service Region**
  GLOBAL service is provided via CDN edge servers located around the globe. 
  Note, however, **China and Russia** are excluded from service regions. 

- **Description**
  More description is added on CDN service.  

- **Domain Alias**
  The default service domain address of TOAST CDN is provided in the **[ServiceID].toastcdn.net** format. 
  To use CDN service with your own domain, enable **Domain Alias**. 
  To use HTTPS protocol with your own domain, get a certificate issued from **Certificate Management** and set domain alias.
  After domain alias is set, register CNAME record at DNS provider of domain, like follows. Please consult your DNS provider regarding DNS settings. 
    - Record Type: **CNAME**
    - Record Name: **[Registered domain for domain alias]**
    - Record Value (Rdata): **[ServiceID].toastcdn.net**
    - TTL: Randomly selected 

- **Callback**
 It takes hours to create and change CDN service (e.g. Modify, Suspend/Resume, and Delete)
 After a task is completed, enable the callback setting to receive change status via callback URL and CDN setting information. See [API Guide](./api-guide-v2.0/#_23) to find information sent to callback. 
    1. Enter **HTTP Method** and **Callback URL**.
    2. To receive results on the change of CDN via query parameter, include the following path variable to **Callback URL**.  
         e.g.: http://callback.url?appKey={appKey}&status={status}&isSuccessful={isSuccessful})

| Path Variables | Description | Delivered Value|
| ------------- | --- | ------- |
| {appKey} | Appkey of CDN Service | Appkey issued on console |
| {domain} | Name of CDN Service | [ServiceID].toastcdn.net |
| {status} | Current status of CDN | OPEN, SUSPEND, CLOSE, ERROR |
| {isSuccessful} | Whether service change is successful (API v1.0 is not supported.) | "true" or "false" |

### Origin Server
Set server providing original files to be deployd to CDN.
![Creating CDN- Basic Information](https://static.toastoven.net/prod_cdn/v2/console-cdn-create-origin2.png)

- **Origin Server**
  The origin server provides original files to be deployd for CDN service. You may use IPv4 or the entire domain address (FQDN: Fully Qualified Domain Name) type for the origin server. Since IP address is highly likely to be changed, it is recommended to set with domain.  
  If there is no operating origin server, use an instance of TOAST Compute or object storage of TOAST Storage. To support secured transfer (HTTPS) via CDN domain, the origin server must support HTTPS response. This means a certificate credited by TOAST CDN must be installed at the origin server. Refer to the following table for credible certificates. 
  If the origin server cannot support HTTPS response, use the **Downgrading HTTP Protocols Requesting Originals** setting. 
  Nevertheless, since **Downgrading HTTP Protocols Requesting Originals** has constraints, it is recommended that the origin server support HTTPS protocol.   

**[Table 1] List of Credible Certificates**

| Common Name| Expiry Date |SHA-1 Fingerprint |
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

- **Origin Server Port**  
  An origin server must be operated by a web-protocol support service. Service port numbers can be set for HTTP/HTTPS protocols under operations.  
  Either HTTP or HTTPS must be entered for the origin server port, and if not set, a port is set by default with HTTP:80 or HTTPS:443.
  Only limited number of ports are available as the original port. Refer to the following table for available port numbers. 

**[Table 2] Available Origin Server Port Numbers**

|Port Number|
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

- **Original Path**  
  Set the lower paths of an original file. Content may be requested without the original path. 
  
> **[Example] When the original path is set with /files/images** 
>
> - URL of Original File: http://your.origin.com/**files/images**/logo.png 
> - URL of CDN Service: http://[ServiceID].toastcdn.net/logo.png
> - Origin paths may be missing from URL of CDN service when it is requested. 

- **Downgrading HTTP Protocols Requesting Originals**  
  The CDN edge server requests origin server of the original files via service protocol (HTTP/HTTPS) of client's original request.    
  That is, when a client requests via HTTPS but if the origin server does not support HTTPS response, original files do not come as response.   
  If the origin server operates HTTPS protocols only, enable the **Downgrading HTTP Protocols Requesting Originals** setting and make a request from CDN edge server to origin server by downgrading HTTPS to HTTP protocol.    
  In short, the CDN edge server section of client is communicated via HTTPS, while the origin server section of CDN edge server is communicated via HTTP.   
  Note the following constraints when downgrading HTTP protocols requesting originals:  
  
> **[Caution] Constraints for Downgrading HTTP Protocols Requesting Originals**
> 1. Protocol downgrade is not applied to the entire website address. For instance, **www.toast.com**, which is the entire site address of the origin server, cannot be downgraded.   
> 2. No other methods than GET, HEAD, or OPTIONS, are supported. 
> 3. When a downgrade is requested from CDN to an origin server, following headers may be excluded:
>    Origin, Referer, Cookie, Cookie2, sec-\*, proxy-\*

- **Forward Host Header**
  Set **Host** header value to be sent along with a request of CDN server for original files to origin server. 
  If the origin server is run as name-based virtual host, **Request Host Header** setting may be required. Select an appropriate value depending on the opearting type of an origin server.  
  - **Original Host Name**: Set the host name of origin server as the host header.
    - **Request Host Header**: Set as the host header of client request. 

### Cache

CDN cache operations and expiration time can be set.  
![Creating CDN-Cache](https://static.toastoven.net/prod_cdn/v2/console-cdn-create-cache2.png)

- **Setting Cache Expiration**
  Cache can be configured from the response header of cache control at the origin server. 
    - **Enable Original Setting**: Apply the cache control header first, as provided by the origin server's response. If cache control header is not valid or unavailable, it is cached during specified cache expiration time (seconds). **Enable Original Setting** is default. 
    - **Enable User-Configuration**: Cached during specified cache expiration time (seconds). 

> **[Note] Default and Validity of Cache Expiration Time**
> Default cache expiration time is 0. With 0 as default, the cache expiration time is 604,800 (seconds)=1 week.
> Cache expiration time is available from 0 as default to 2,147,483,647(seconds).

### Manage Referrer Header Access  
Content access management is set with the referrer request header.
![Creating CDN Service - Cache](https://static.toastoven.net/prod_cdn/v2/console-cdn-create-cache2.png)

The referrer request header includes the webpage address of previous links of the currently requested page. It helps to find the paths a request comes from. With referrer header access management, only particular request headers can be configured to access user content. Enter in regex, and break the lines to enter many.

- **Blacklist Type**:
    * Appropriate to restrict the access of particular referrer request headers only. 
    * Content access is restricted if a referrer request header matches regex setup. If not, content access is allowed. 
- **Whitelist Type**:
    * Appropriate to allow the access of particular referrer request headers only. 
    * Content access is allowed if a referrer request header matches regex setup. If not, content access is not allowed. 

- **리퍼러 헤더가 없는 경우 접근 허용**
  리퍼러(referer) 요청 헤더가 없는 경우 콘텐츠 접근 허용 여부를 선택합니다.
    - **허용**: 리퍼러 요청 헤더가 없는 경우 콘텐츠 접근을 허용하며 리퍼러 접근 제어가 동작하지 않습니다.
    - **거부**: 리퍼러 요청 헤더가 없는 경우 콘텐츠 접근을 거부하고 등록된 리퍼러에 대해서만 접근을 허용합니다.

> **[Example]**
>
> * Typ: Whitelist
> * Regex:`^https://[a-zA-Z0-9._-]*\.toast\.com/.*`
> Content access is allowed only when resources are requested from lower paths of a toast.com sub-domain. 
>
> **[Note] Regex Escape Characters**
> Some characters are used as special characters for regex. 
> For instance, a period (`.`) indicates agreement with all characters for regex. 
> To understand a special character as a general one, add backlash before it.(e.g.: `\.`).
> Regex special characters include `^, ., [, ], $, (, ), |, *, +, ?, {, }, and \`.
> To control many referrers, enter in consecutive lines. 
> To set many referrers with APIs, delimit with \n tokens. 


## Settings

### Modify CDN Service Setting
CDN service setting can be modified, except the name and region of service domain.
![Enabling CDN Service Modification](https://static.toastoven.net/prod_cdn/v2/console-cdn-modify3.png)

1. Select a CDN service to modify from the list. 
2. Click **Modify** from the **Setting** at the bottom of the page. 

Then, items that are modifiable are activated like below.  
![Checking CDN Service Modification](https://static.toastoven.net/prod_cdn/v2/console-cdn-modify2.png)

* Modify the setting.  
* Click **OK** to complete with changes. 
* To edit other settings, except description and callack setting, it may take more time than usual since changes must be applied throughout the whole CDN server.  

**Modification takes dozens of minutes, but change of domain alias may take a few hours.**

> **[Note] Deployment Status while Modifying CDN and Service Status** 
> If service is under modification, CDN runs in the existing service setting.
> If it fails to modify, it is rolled back to the existing setting information and the deployment status shows red circle on CDN list.  Modification fails if there is error in setting information, or internal error occurs.  

### Suspend and Resume CDN
CDN service can be suspended or resumed. 


1. Select a CDN service to suspend.
2. Click **Suspend**.
![CDN Service- Suspend](https://static.toastoven.net/prod_cdn/v2/console-cdn-pause2.png)
3. A warning guide will show for CDN services that are integrated with certificate. To prevent certificate expiration, CDN service must be resumed before a start day of certificate renewal.
![CDN Service-Suspend](https://static.toastoven.net/prod_cdn/v2/console-cdn-restart2.png)
4. To resume suspended CDN service, select a CDN service to resume.
5. Click **Resume**.




> **[Note] Delays in Suspension and Resumption**
> Suspension and resumption of CDN service operates by changing DNS records of CDN domain.  
> Accordingly, even if it is cached during TTL at cache DNS server or suspension/resumption is completed, immediate suspension/resumption may not work depending on DNS transfer. 

> **[Caution] Suspending CDN Service Integrated with Issued Certificate**
> When a CDN service integrated with certificate is suspended, the certificate cannot be renewed. 
> Please resume CDN before **Start Day of Certificate Renewal** from **Certificate Management** > Certificate List. 
> A certificate is allowed to be renewed for 5 days after start day of renewal, and a suspension during the period may cause the certificate to get expired. 


### Delete CDN
CDN service can be deleted. Once deleted, however, a service cannot be recovered. 

1. Select a CDN service to delete.
2. Click **Delete**.
![CDN Service-Delete](https://static.toastoven.net/prod_cdn/v2/console-cdn-delete2.png)
3. A warning guide will show for CDN services that are integrated with certificate. To prevent certificate expiration, please integrate certificate with another running CDN service.

> **[Note] Required Time to Delete CDN**
> It may take a few hours (up to 3) to delete CDN service. 

> **[Caution] Deleting CDN Service Integrated with Issued Certificate**
> When a CDN service integrated with certificate is deleted, the certificate cannot be renewed. 
> Please integrate the certificate with another running CDN before **Start Day of Certificate Renewal** from **Certificate Management** > Certificate List.
> A certificate is allowed to be renewed for 5 days after start day of renewal, and deletion during the period may cause the certificate to get expired.


## Purging CDN Cache  
CDN cache server cahces origin server files during specified expiration time depending on the cache setting. When a file is cached, the original file before change shall be maintained until cache is expired, even if there is a change in the original file. 
To immediately update content to changed original file, **Purge Cache** must be requested. 
By purging cache, outdated cache data are deleted from requested content while a new original file is cached again at the origin server. 

1.  Select a service to change from the list of CDN services.
2. Click **Purge Cache**.
![Purging CDN Cache](https://static.toastoven.net/prod_cdn/v2/console-cdn-purge2.png)

3. Select a purge type. 
  - Note that each CDN service domain may support different purge type and request format of cache.  
  - Purge type and request format
    * Particular Files: Enter URL of content to purge. Since cache purge is applied for a requested URL only, purge must be requested to each URL address if there are many service domain addresses.   
      * e.g.) Domain address for default service: http://[ServiceID].toastcdn.net/path/to/file1.jpg
      * e.g.) Domain address for domain alias: http://customer.domain.com/path/to/file1.jpg
    * All Files: Delete all cache files. Note that excessive traffic inflow may be incurred to the origin server.
4. Specify a file to purge depending on the selected cache purge type.
5. Click **Purge Cache** to request for a purge.  

Take caution that cache purge does not exceed the capacity limit, in reference of the table as below:

|Category |[ServiceID].toastcdn.net |
|---|---|
| Unit of Restrictions | Per project (Appkey) |
| Particular Files | Requests per second: 1 time, URLs per request: 200 URLs |
| All File Types | Requests per 5 minutes: 1 time |

> **[Caution] Failed Cache Purge after [ServiceID].toastcdn.net is created**
> Cache purge request may fail within about an hour after CDN service is created. If failure continues afterwards, contact Customer Center.  

## Managing Certificates
To use secure tranfer (HTTPS) via your own domain, certificate of your own domain must be deployed to CDN server. Without a certificate, secured communication (HTTPS) is unavailable between client (browser) and CDN edge server, causing error of certificate. 
Certificate management of TOAST CDN provides the following features: 

- Issue single domain-type certificates 
- Deploy certificates to CDN servers at global points (except China and Russia)
- Auto-renew certificates before expired 

### Issue New Certificates
Certificates can be issued from the **Certificate Management** tab. 
![Getting New CDN Certificates](https://static.toastoven.net/prod_cdn/v2/console-certificate-create.png)

1. Go to **Certificate Management** and click **Issue New Certificates**.
2. Enter domain of certificate to get, in the format of full domain address(FQDN, fully qualified domain name).
3. Check the guide to issue certificates and click **OK**.
4. When a new certificate is requested, certificate domain shows on the **Certificate Management** tab. When the certificate status is changed to **Validate Domain**, domain can be validated.   

> **[Caution] Checkpoints before Getting a Certificate**
> 1. Purchase a domain first, if not owned, becuase certificates can be issued to owned domains only. 
> 2. Certificates issued from other certificate authorities are not allowed. 
> 3. Only single-domain certificates can be issued. Wildcard or multi-domain certificates are not supported. 
> 4. Each project allows no more than 5 certificates. If you need more than that, contact TOAST Customer Center.  
> 5. After certificate issuance is requested, the Validate Domain phase may be activated in a dozon of minutes (up to 2 hours). If your certificate changes status to Validate Domain, email shall be sent to TOAST project members. If email is not sent due to system error, check status on console.    

### Validate Domain
You're ready to validate domain, after a new certificate is requeted, when certificate status is changed to 'Validate Domain'. 
You may select a domain on console or refer to domain validation guide via email sent to project members.

![Validating CDN Domain](https://static.toastoven.net/prod_cdn/v2/console-certificate-domain-validation.png)

Domain validation is required to see if the requester for certificate is the actual owner of its domain. Without this process, certificate cannot be issued.  
As part of a domain validation method to check domain owner, domain control role must be validated.   
Domain validation can be carried out by **Adding DNS TXT Recors** or **Adding HTTP Pages**, and you can **Choose Either of the Two Methods**.

![Validating CDN Domain](https://static.toastoven.net/prod_cdn/v2/console-certificate-domain-validation2.png)

#### Adding DNS TXT Records 
Check DNS control role of domain to validate domain.  

1. Add TXT record on the DNS management page of domain's DNS service provider. 
   Each DNS service provider may provide different configuration method. Consult your service provider regarding DNS setting. 
  - Record Type: **TXT**
  - TTL: **60**. If 60 is unavailable, set the smallest possible number. 
  - Record Name: **_acme-challenge.[Certificate Domain Requested of Issuance].** Fill in the **Record Name** of console or email guide as sent.   
  - Record Value: **Random Character String** (fill in the **Record Value** of console or email guide as sent.)

2. See if TXT record, added for nslookup command, is well queried. It may take some time to query depending on the DNS transfer time. 
    `nslookup -type=TXT _acme-challenge.[Certificate Domain Requested of Issuance].`


Following page shows a setting example for TOAST DNS+. Each DNS provider may provide different configuration method. 
![Validating CDN Domain](https://static.toastoven.net/prod_cdn/v2/console-certificate-domain-validation-dns.png)


#### Adding HTTP Pages
Add an HTTP page to a web server connected with domain to validate the domain. 

1. Add an HTTP page to the path of **http://[Certificate Domain Requested of Issuance]/.well-known/acme-challenge/[random character string]** on the web server. 
2. Set **Page Content (Token)** of console or email guide as sent for the main body of the HTTP page.  
3. On a web browser, access the URL of **http://[Certificate Domain Requested of Issuance]/.well-known/acme-challenge/[random character string]**, and see if **Page Content (Token)** shows on the page.  

> **[Caution] Cautions for Domain Validation**
> 1. Domain must be validated **within 5 days** since when a certificate is requested of issuance. **Otherwise, getting a certificate shall be automatically revoked**.   
> 2. When domain is successfully validated, certificate is to be issued and deployed within hours. Unless it proceeds more than a day, check if domain has been properly validated. If it still does not proceed, contact TOAST Customer Center.
> 3. Adding HTTP Pages is available only when the HTTP server runs on 80 ports. If port change is unavailable, please take another option of Adding DNS TXT Records. 

### Issue and Deploy Certificates
Once domain is validated, a certificate shall be issued and deployed within hours.  
Certificate status on console shows the phase of **Issue and Deploy Certificates**, and notification mail is sent to project members. 
This phase requires no specific tasks. 

>  **[Note] Time Required to Issue and Deploy Certificates**
> It may take up to 9 hours to issue and deploy a certificate. 

### Integrate with CDN Service
A certificate must be integrated with CDN service to be enabled. 
If this task is undone or not maintained, issued certificate may expire. 

1. **CNAME Record Setting**: Add the following CNAME record to DNS management of DNS service provider of certificate domain. 
    - Record Type:  **CNAME**
    - TTL: Random. Small count is recommended if frequent changes are expected. When there is change in the record, it may be cached during TTL at the cache DNS server. 
    - Record Name:  **[Certificate Domain].** (Example: test.alias.com.com.)
    - Record Value:  **[CDN Service Domain to be Integrated]** (Example: xxxxxxxx.toastcdn.net)
Following page shows a setting example for TOAST DNS+. Each DNS provider may provide different configuration method.
![Integrating CDN Services-Assign CNAME](https://static.toastoven.net/prod_cdn/v2/console-certificate-service-cname.png)

2. **Domain Alias Setting**: Add domain alias setting for the CDN to use certificate. 
    -  Select CDN to be integrated from **CDN Service** and click **Modify**. Add certificate domain to domain alias and click **OK**.  
![Integrating CDN-Domain Alias](https://static.toastoven.net/prod_cdn/v2/console-certificate-service-alias2.png)


>  **[Caution] Caution for Certificate Expiration**
> Certificates provided by TOAST CDN are automatically renewed before expired. 
> For auto-renewal, user's certificate must be integrated with CDN service. 
> Otherwise, certificates may not be renewed during specific period and get expired. 
> A certificate shall be renewed within **5 days** after a renewal start day as indicated on the list of **Certificate Management**.   
> To prevent certificates from expired, maintain the following settings at all times: 
> 
> 1. Assign domain of a certificate to the domain address of CDN service which is to be integrated with CNAME record. 
> 2. Set certificate domain for domain alias of CDN service to be integrated with. 
> 3. When a CDN service integrated with certificate is suspended, the certificate cannot be renewed. Resume before a renewal start day or integrate it to another running CDN service.  
> 4. When a CDN service integrated with certificate is deleted, the certificate cannot be renewed: integrate it to another running CDN service before deleting. 

When certificate is fully integration with CDN, the certificate status shows 'Activated'. 
![Activated Status of Certificate](https://static.toastoven.net/prod_cdn/v2/console-certificate-active.png)

## Statistics

Check statistics on the network transfer volume, HTTP status code, most downloaded content, and more.  
Statistical data within 7 days are recommended only as reference, since they may not be precise: precise data are available after 7 days.   

1. Click **Statistics** from **Content Delivery > CDN**.
![cdn_08_201812](https://static.toastoven.net/prod_cdn/cdn_08_201812.png)
2. Select a CDN service to check statistics. 
3. Enter search period. 
4. Data cycle within a search period is automatically selected depending on the period. 
5. Click **Search**. 
