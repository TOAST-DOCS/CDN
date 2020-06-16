## Content Delivery > CDN > 콘솔 사용 가이드

이 문서는 TOAST CDN 콘솔에서 CDN 서비스를 구성하고 이용하는 방법을 설명합니다.

## CDN 서비스 생성

**Contents Delivery > CDN**의 **CDN 서비스** 탭에서 **생성** 버튼을 클릭하면 **CDN 서비스 생성** 대화 상자가 나타납니다.
CDN 서비스 도메인은 **[서비스ID].toastcdn.net** 형식으로 자동 생성됩니다. 만일 소유하고 있는 도메인을 서비스 도메인으로 이용하려면 **도메인 별칭**(domain alias) 기능을 이용할 수 있습니다.
생성을 요청한 후 서비스 배포가 완료될 때까지 최대 2시간이 걸립니다. 배포가 완료된 후 서비스를 이용할 수 있습니다.

### 기본 정보
기본 정보를 설정합니다.
![CDN서비스생성-기본정보](https://static.toastoven.net/prod_cdn/v2/console-cdn-create-default2.png)

- **서비스 지역**
  GLOBAL 서비스 지역은 전 세계 거점에 위치한 CDN 에지 서버를 통해 CDN 서비스를 제공합니다.
  단, **중국과 러시아**는 서비스 지역에서 제외됩니다.

- **설명**
  CDN 서비스의 설명을 추가합니다.

- **도메인 별칭**
  TOSAT CDN은 기본으로 **[서비스ID].toastcdn.net** 형식의 서비스 도메인 주소를 제공하고 있습니다.
  기본 서비스 도메인 주소가 아닌 소유한 도메인으로 CDN 서비스를 이용하려면 **도메인 별칭**에서 설정하면 됩니다.
  소유한 도메인으로 HTTPS 프로토콜 서비스를 이용하려면 먼저 **인증서 관리** 탭에서 인증서를 발급한 후 도메인 별칭을 설정하시기 바랍니다.
  도메인 별칭 설정 후에는 도메인의 DNS 서비스 제공 업체에서 CNAME 레코드를 다음과 같이 등록해야 합니다. DNS 설정 관련 문의는 DNS 서비스 제공 업체에 하시기 바랍니다.
    - 레코드 타입: **CNAME**
    - 레코드 이름: **[도메인 별칭에 등록한 도메인]**
    - 레코드값(Rdata): **[서비스ID].toastcdn.net**
    - TTL: 임의의 값

### 원본 서버
CDN 서비스로 배포할 원본 파일을 제공하는 서버를 설정합니다.
![CDN서비스생성-기본정보](https://static.toastoven.net/prod_cdn/v2/console-cdn-create-origin2.png)

- **원본 서버**
  원본 서버는 CDN 서비스로 배포할 원본 파일을 제공하는 서버입니다. 원본 서버는 IPv4 또는 전체 도메인 주소(FQDN, fully qualified domain name) 형식으로 입력할 수 있습니다. IP 주소는 변경될 가능성이 높기 때문에 도메인으로 설정하는 것을 권장합니다.  
  운영 중인 원본 서버가 없다면, TOAST Compute 서비스의 인스턴스를 사용하거나 TOAST Storage 서비스의 Object Storage를 이용할 수 있습니다.  
  CDN 서비스 도메인으로 보안 전송(HTTPS)를 지원하려면 원본 서버는 HTTPS 응답을 지원해야 합니다.  
  이는 원본 서버에 TOAST CDN이 신뢰하는 인증서가 설치돼 있어야한다는 뜻입니다.
  신뢰하는 인증서는 다음 표를 참고하시기 바랍니다.  
  만일, 원본 서버가 HTTPS 응답을 지원할 수 없다면 **원본 요청 HTTP 프로토콜 다운그레이드** 설정을 이용하시기 바랍니다.  
  단, **원본 요청 HTTP 프로토콜 다운그레이드**는 제약 사항이 있으므로 원본 서버가 HTTPS 프로토콜을 지원하는 것을 권장합니다.  

**[표 1] 신뢰하는 인증서 목록**

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

- **원본 서버 포트**  
  원본 서버는 웹 프로토콜을 지원하는 서비스로 운영해야 합니다. 운영 중인 HTTP/HTTPS 프로토콜의 서비스 포트 번호를 설정할 수 있습니다.  
  원본 서버 포트는 HTTP 또는 HTTPS 포트 중 하나를 반드시 입력해야 하며, 설정하지 않은 포트는 기본 포트 HTTP:80, HTTPS:443으로 설정됩니다.  
  원본 서버 포트는 제한된 포트만 설정할 수 있습니다. 설정 가능한 포트 번호는 다음 표를 참고하시기 바랍니다.  

**[표 2] 사용 가능한 원본 서버 포트 번호**

|포트 번호|
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

- **원본 경로**  
  원본 파일의 경로 중 하위 경로를 설정합니다. 콘텐츠를 요청할 때 원본 경로를 생략하고 요청할 수 있습니다.

> **[예시] 원본 경로를 /files/images로 설정한 경우**
>
> - 원본 파일 URL: http://your.origin.com/**files/images**/logo.png
> - CDN 서비스 URL: http://[서비스ID].toastcdn.net/logo.png
> - CDN 서비스 URL에서 원본 경로(/files/images)를 생략하여 요청할 수 있습니다.

- **원본 요청 HTTP 프로토콜 다운그레이드**  
  CDN 에지(edge) 서버는 원본 서버에 원본 파일을 요청할 때 클라이언트의 원본 요청(request)의 서비스 프로토콜(HTTP/HTTPS)로 요청합니다.  
  즉, 클라이언트가 HTTPS로 요청하고 원본 서버가 HTTPS 응답을 지원하지 않으면, CDN 에지 서버에서 원본 서버로 요청할 때 HTTPS 프로토콜로 요청하기 때문에 원본 파일을 응답받을 수 없습니다.  
  원본 서버에서 HTTP 프로토콜만 운영한다면, **원본 서버 HTTP 프로토콜 다운그레이드** 설정을 사용해 CDN 에지 서버에서 원본 서버로 요청할 때 HTTPS 프로토콜을 HTTP 프로토콜로 다운그레이드해서 요청할 수 있습니다.  
  즉, 클라이언트에서 CDN 에지 서버 구간은 보안 통신(HTTPS)으로 통신하고, CDN 에지 서버에서 원본 서버 구간은 비보안 통신(HTTP)으로 통신하게 됩니다.  
  원본 요청 HTTP 프로토콜을 다운그레이드할 때는 다음과 같은 제약 사항이 있습니다.  
> **[주의] 원본 요청 HTTP 프로토콜 다운그레이드 제약 사항**
> 1. 전체 사이트 주소는 프로토콜 다운그레이드를 할 수 없습니다. 예를 들어 원본 서버의 전체 사이트 주소인 **www.toast.com**는 다운그레이드할 수 없습니다.
> 2. GET, HEAD 및 OPTIONS 메서드 외 메서드는 지원되지 않습니다.
> 3. CDN 서버에서 원본 서버로 다운그레이드를 요청할 때 다음의 헤더는 제외될 수 있습니다.
>    Origin, Referer, Cookie, Cookie2, sec-\*, proxy-\*

- **Forward Host Header**
  CDN 서버가 원본 서버에 원본 파일을 요청할 때 전달할 **Host** 헤더값을 설정합니다.  
  원본 서버가 Name-based virtual host로 운영 중이라면 **요청 호스트 헤더** 설정이 필요할 수 있습니다. 원본 서버의 운영 형태에 따라 적합한 설정 값을 선택하시기 바랍니다.
  - **원본 호스트 이름**: 원본 서버의 호스트 이름을 Host 헤더로 설정합니다.
  - **요청 호스트 헤더**: 클라이언트 요청의 Host 헤더로 설정합니다.

> **[주의] 보안 전송(HTTPS) 사용 시 Host 헤더와 원본 서버 인증서의 유효성 검사**
> 클라이언트가 보안 전송(HTTPS)으로 콘텐츠를 요청하면 CDN 서버는 원본 서버의 인증서 유효 여부를 확인합니다.
> 원본 서버에는 Host 요청 헤더와 일치하는 CN(Common Name) 또는 SAN (Subject Alternate Name)의 인증서가 설치되어 있어야 합니다.
> Host 요청 헤더와 일치하는 인증서가 원본 서버에 설치되어 있지 않은 경우 보안 전송 오류가 발생합니다.
> Host 요청 헤더는 Forward Host Header 설정에 따라 요청 호스트 헤더 또는 원본 호스트 이름으로 설정되므로 유의하시기 바랍니다.


### 캐시

CDN 캐시 동작 설정과 만료 시간을 설정할 수 있습니다.
![CDN서비스생성-캐시](https://static.toastoven.net/prod_cdn/v2/console-cdn-create-cache2.png)

- **캐시 만료 설정**
  원본 서버의 Cache Control 응답 헤더를 통해 캐시를 설정할 수 있습니다.
    - **원본 설정 사용**: 원본 서버의 응답에서 제공한 캐시 제어 헤더(Cache-Control, Expires)를 우선 적용합니다. 만일 원본 서버의 응답에 캐시 제어 헤더(Cache-Control, Expires)가 유효하지 않거나 없는 경우, 캐시 만료 시간(초)에 지정한 시간 동안 캐시됩니다.  **원본 설정 사용** 옵션이 기본값입니다.
    - **사용자 설정 사용**: 캐시 만료 시간(초)에 지정한 시간 동안 캐시됩니다.

> **[참고] 캐시 만료 시간 기본값과 유효 범위**
> 캐시 만료 시간 기본값은 0입니다. 기본값을 0으로 설정하면 캐시 만료 시간은 604,800(단위/초)=1주일입니다.
> 캐시 만료 시간은 기본값인 0부터 2,147,483,647(단위/초)까지 입력할 수 있습니다.

### 리퍼러(referer) 헤더 접근 관리
리퍼러 요청 헤더로 콘텐츠의 접근 관리를 설정합니다.
![CDN서비스생성-캐시](https://static.toastoven.net/prod_cdn/v2/console-cdn-create-cache2.png)

리퍼러 요청 헤더는 현재 요청된 페이지의 링크 이전의 웹 페이지 주소를 포함합니다. 리퍼러 요청 헤더로 어떤 경로에서 요청이 유입되었는지 알 수 있습니다. 리퍼러 헤더 접근 관리는 특정 리퍼러 요청 헤더만 사용자 콘텐츠에 접근할 수 있도록 설정할 수 있습니다.
정규 표현식 형태로 입력할 수 있으며, 여러 개를 입력할 때는 줄바꿈을 한 뒤 입력합니다.

- **블랙리스트(blacklist) 타입**:
    * 특정 리퍼러 요청 헤더만 접근을 제한할 때 적합합니다.
    * 리퍼러 요청 헤더값이 설정한 정규 표현식에 매칭되는 문자열이면 콘텐츠 접근이 제한됩니다. 매칭되지 않는 문자열이면 콘텐츠 접근이 허용됩니다.
- **화이트리스트(whitelist) 타입**:
    * 특정 리퍼러 요청 헤더만 접근을 허용할 때 적합합니다.
    * 리퍼러 요청 헤더값이 정규 표현식에 매칭되는 문자열이면 콘텐츠 접근이 허용됩니다. 매칭되지 않는 문자열이면 콘텐츠 접근이 제한됩니다.

> **[주의]**
> 리퍼러 요청 헤더가 없는 경우 접근 제어는 동작하지 않습니다.
>
> **[예시]**
>
> * 타입: 화이트리스트(whitelist)
> * 정규 표현식: `^https://[a-zA-Z0-9._-]*\.toast\.com/.*`
> 임의의 toast.com 서브 도메인의 하위 경로에서 리소스를 요청한 경우에만 콘텐츠 접근을 허용합니다.
>
> **[참고] 정규 표현식의 이스케이프 문자**
> 일부 문자는 정규 표현식에서 특수 문자로 사용됩니다.
> 점(`.`)을 예로 들자면, 정규 표현식에서 점(`.`)은 모든 문자와 일치함을 나타내는 특수 문자입니다.
> 특수 문자로의 의미가 아닌 일반 문자 그대로 해석해야 한다면 이스케이프 문자 백슬래시(`\`)를 특수 문자 앞에 추가하면 됩니다(예: `\.`).
> 정규 표현식의 특수 문자에는 `^ . [ ] $ ( ) | * + ? { } \` 등이 있습니다.
> 여러 개의 리퍼러를 제어할 때는 다음 줄에 연속해 입력합니다.
> API를 이용해 여러 개의 리퍼러를 설정할 때는 \n 토큰으로 구분해 입력합니다.


## 설정

### CDN 서비스 설정 변경
서비스 도메인 이름과 지역을 제외한 CDN 서비스 설정을 변경할 수 있습니다.
![CDN서비스수정활성화](https://static.toastoven.net/prod_cdn/v2/console-cdn-modify3.png)

1. 변경할 CDN 서비스를 CDN 서비스 목록에서 선택합니다.
2. 화면 아래 **설정** 탭의 **수정** 버튼을 클릭합니다.

다음과 같이 변경할 수 있는 항목이 활성화됩니다.
![CDN서비스수정확인](https://static.toastoven.net/prod_cdn/v2/console-cdn-modify2.png)

* 변경할 설정 내용을 수정합니다.
* **확인** 버튼을 클릭해 변경을 완료합니다.
* 설명을 제외한 다른 설정을 변경하려면 전체 CDN 서버에 반영돼야 해서 시간이 오래 걸릴 수 있습니다.

**수정 작업은 몇 십분 내 완료되며, 도메인 별칭 설정이 변경된 경우에는 몇 시간이 걸릴 수 있습니다.**

> **[참고] CDN 서비스 수정 중 배포 상태와 서비스 상태**
> 서비스 수정 작업이 진행 중이면 기존 CDN 서비스 설정으로 운영됩니다.
> 만약 수정 작업에 실패하면 기존 설정 정보로 롤백되며, CDN 서비스 목록의 배포 상태가 빨간색 원으로 표시됩니다. 설정 정보에 오류가 있거나 내부적으로 오류가 발생했을 때 수정 작업에 실패합니다.

### CDN 서비스 일시 정지와 재시작
CDN 서비스를 일시적으로 중단하거나 재시작할 수 있습니다.


1. 일시 정지할 CDN 서비스의 선택합니다.
2. **일시 정지** 버튼을 클릭합니다.
![CDN서비스-일시정지](https://static.toastoven.net/prod_cdn/v2/console-cdn-pause2.png)
3. 인증서가 연동된 CDN 서비스에는 인증서 만료 경고 안내가 표시됩니다. 인증서가 만료되지 않게 하려면 인증서 갱신 시작일 이전에 CDN 서비스를 재시작해야 합니다.
![CDN서비스-일시정지](https://static.toastoven.net/prod_cdn/v2/console-cdn-restart2.png)
4. 일시 정지 상태의 CDN 서비스를 재시작하려면 재시작할 CDN 서비스를 선택합니다.
5. **재시작** 버튼을 클릭합니다.




> **[참고] 일시 정지와 재시작 동작의 지연**
> CDN 서비스 일시정지와 재시작은 CDN 서비스 도메인의 DNS 레코드를 변경하여 동작됩니다.
> 따라서 캐시 DNS 서버에서 TTL 동안 캐시되어 있거나 DNS 전파에 따라 일시정지/재시작이 완료 되어도 즉시 일시정지/재시작이 동작되지 않을 수 있습니다.

> **[주의] 발급된 인증서가 연동된 CDN서비스의 일시정지**
> 인증서가 연동된 CDN 서비스를 일시정지 하는 경우, 인증서 갱신이 불가합니다.
> **인증서 관리** > 인증서 목록의 **인증서 갱신 시작일** 이전에 CDN 서비스를 재시작하시기 바랍니다.
> 인증서 갱신 시작일로 부터 5일 동안은 인증서 갱신 기간이므로 해당 기간에 일시정지를 하면 인증서가 만료될 수 있으므로 유의하시기 바랍니다.


### CDN 서비스 삭제
CDN 서비스를 삭제합니다. 삭제 작업은 복구할 수 없으므로 유의하시기 바랍니다.

1. 삭제할 CDN 서비스를 선택합니다.
2. **삭제** 버튼을 클릭합니다.
![CDN서비스-삭제](https://static.toastoven.net/prod_cdn/v2/console-cdn-delete2.png)
3. 인증서가 연동된 CDN 서비스에는 인증서 만료 경고 안내가 표시됩니다. 인증서가 만료되지 않게 하려면 서비스 중인 다른 CDN 서비스에 인증서를 연동하시기 바랍니다.


> **[참고] CDN 서비스 삭제 소요 시간**
> CDN 서비스 삭제 작업은 몇 시간(최대 2~3시간)이 걸릴 수 있습니다.

> **[주의] 발급된 인증서가 연동된 CDN 서비스의 삭제**
> 인증서가 연동된 CDN 서비스를 삭제하면, 인증서를 갱신할 수 없습니다.
> **인증서 관리**의 인증서 목록에서 **인증서 갱신 시작일** 이전에 서비스 중인 다른 CDN 서비스로 연동하시기 바랍니다.
> 인증서 갱신 시작일로부터 5일 동안은 인증서 갱신 기간이므로 해당 기간에 삭제하면 인증서가 만료될 수 있으므로 유의하시기 바랍니다.


## CDN 캐시 재배포(purge)
CDN 캐시 서버는 캐시 설정에 따라 지정된 만료 시간 동안 원본 서버의 파일을 캐시합니다. 파일을 캐시하면 원본 파일이 변경되어도 캐시가 만료되기 전까지는 변경전 원본 파일을 유지합니다.
변경된 원본 파일로 콘텐츠가 즉시 업데이트되려면 **캐시 재배포**를 요청해야 합니다.
캐시 재배포를 하면 요청한 콘텐츠의 오래된 캐시 데이터를 삭제하고 원본 서버에서 새 원본 파일을 다시 캐시합니다.

1. 변경하려는 서비스를 CDN 서비스 목록에서 선택합니다.
2. **캐시 재배포** 탭을 클릭합니다.
![CDN캐시재배포](https://static.toastoven.net/prod_cdn/v2/console-cdn-purge2.png)

3. 캐시 재배포 타입을 선택합니다.
  - CDN 서비스 도메인에 따라 지원되는 캐시 재배포 타입과 요청 양식이 다르므로 유의하시기 바랍니다.
  - 재배포 타입과 요청 양식
    * 특정 파일: 재배포할 콘텐츠의 URL을 입력합니다. 요청한 URL만 캐시가 재배포되므로 도메인 별칭으로 여러 서비스 도메인 주소가 있다면 각 URL 주소로 요청해야 합니다.
      * 예) 기본 서비스 도메인 주소: http://[서비스ID].toastcdn.net/path/to/file1.jpg
      * 예) 도메인 별칭 도메인 주소: http://customer.domain.com/path/to/file1.jpg
    * 전체 파일: 캐시 파일을 모두 삭제합니다. 원본 서버에 과도한 트래픽이 유입될 수 있으므로 주의하시기 바랍니다.
4. 선택한 캐시 재배포 타입에 맞게 재배포할 파일을 지정합니다.
5. **캐시 재배포** 버튼을 클릭해 재배포를 요청합니다.

캐시 재배포는 사용량 제한이 있으므로 아래의 표를 참고하시고 사용량이 초과되지 않도록 유의하시기 바랍니다.

|분류|[서비스ID].toastcdn.net |
|---|---|
| 제한 단위 | 프로젝트별(Appkey) |
| 특정 파일 | 1초당 요청 가능: 1회, 요청당 URL 수 제한: 200 URL |
| 전체 파일 타입 | 5분당 요청 가능: 1회 |

> **[주의] [서비스ID].toastcdn.net 서비스를 생성한 후 캐시 재배포 실패 오류**
> CDN 서비스를 생성한 후 약 1시간 이내에는 캐시 재배포 요청에 실패할 수 있습니다. 이후에도 계속 실패하면  고객 센터로 문의해주시기 바랍니다.

## 인증서 관리
소유한 도메인으로 콘텐츠를 보안 전송(HTTPS)하려면 CDN 서버에 소유한 도메인의 인증서를 배포해야 합니다. 인증서가 없으면 클라이언트(브라우저)와 CDN 에지 서버 간 보안 통신(HTTPS)을 할 수 없어 인증서 오류가 발생합니다.
TOAST CDN의 인증서 관리는 다음과 같은 기능을 제공합니다.

- 단일 도메인 타입의 인증서 발급
- 전 세계 거점의 CDN 서버에 인증서 배포(중국과 러시아 지역은 제외)
- 인증서 만료 전 자동 갱신

### 신규 인증서 발급
**인증서 관리** 탭에서 인증서를 발급할 수 있습니다.
![CDN신규인증서발급](https://static.toastoven.net/prod_cdn/v2/console-certificate-create.png)

1. **인증서 관리** 탭의 **신규 인증서 발급** 버튼을 클릭합니다.
2. 발급할 인증서의 도메인을 전체 도메인 주소(FQDN, fully qualified domain name)형식으로 입력합니다.
3. 인증서 발급 안내 내용을 확인하고 **확인** 버튼을 클릭합니다.
4. 신규 발급 인증서를 요청하면 **인증서 관리** 탭의 인증서 도메인이 표시됩니다. 인증서 상태가 **도메인 검증** 상태로 변경되면 이후 도메인 검증 작업을 진행하시기 바랍니다.

> **[주의] 인증서 발급 전 확인 사항**
> 1. 소유한 도메인만 인증서를 발급할 수 있으므로 먼저 도메인을 구매하신 후 진행하시기 바랍니다.
> 2. 다른 인증 기관(CA, certificate authority)에서 발급한 인증서는 이용할 수 없습니다.
> 3. 단일 도메인의 인증서 발급만 가능합니다. 와일드 카드, 멀티 도메인 등의 인증서는 지원하지 않습니다.
> 4. 인증서 발급은 프로젝트당 5개로 제한됩니다. 한도 조정이 필요한 경우 TOAST 고객 센터로 문의하시기 바랍니다.
> 5. 신규 인증서 발급 요청 후 도메인 검증 단계는 몇 십분(최대 1~2시간) 후 변경될 수 있습니다. 인증서 상태가 도메인 검증 상태로 변경되면 TOAST 프로젝트 멤버를 대상으로 이메일 발송됩니다. 만일 시스템 오류로 이메일이 발송되지 않는다면 콘솔에서 상태를 확인하시기 바랍니다.

### 도메인 검증
신규 인증서 발급을 요청한 후 인증서 상태가 '도메인 검증'이 되면 도메인을 검증하시기 바랍니다.
도메인 검증 방법은 콘솔에서 도메인을 선택하여 확인하거나, 프로젝트 멤버에게 전송된 도메인 검증 가이드 메일의 내용을 참고하시기 바랍니다.

![CDN도메인검증](https://static.toastoven.net/prod_cdn/v2/console-certificate-domain-validation.png)

도메인 검증은 발급 요청한 인증서 도메인의 실제 소유자인지 확인하는 단계 입니다. 도메인 검증을 진행하지 않으면 인증서를 발급할 수 없습니다.
도메인 소유자인지 확인하기 위해 도메인 검증 방식으로 도메인의 제어 권한을 확인합니다.
도메인 검증 방식에는 **DNS TXT 레코드 추가** 또는 **HTTP 페이지 추가** 방식이 있으며 **두 가지 방식 중 하나만 진행**하면 됩니다.

![CDN도메인검증](https://static.toastoven.net/prod_cdn/v2/console-certificate-domain-validation2.png)

#### DNS TXT 레코드 추가 방식
도메인의 DNS 제어 권한을 확인해 도메인을 검증합니다.

1. 도메인의 DNS 서비스 제공 업체의 DNS 관리 페이지에서 TXT 레코드를 추가합니다.
   DNS 설정 방법은 DNS 서비스 제공 업체에 따라 다를 수 있습니다. 관련 설정은 해당 서비스 업체로 문의하시기 바랍니다.
  - 레코드 타입: **TXT**
  - TTL: **60**. 60으로 설정할 수 없다면 되도록 작게 설정하시기 바랍니다.
  - 레코드 이름: **_acme-challenge.[발급 요청한 인증서 도메인].**  콘솔 또는 발송된 이메일 가이드의 **레코드 이름**을 작성합니다.
  - 레코드값: **임의의 문자열** (콘솔 또는 발송된 이메일 가이드의 **레코드값**을 작성합니다.)

2. nslookup 명령어로 추가한 TXT 레코드가 질의되는지 확인합니다. DNS 전파 시간에 따라 질의되기까지 시간이 소요될 수 있습니다.
    `nslookup -type=TXT _acme-challenge.[발급 요청한 인증서 도메인].`

다음 화면은 TOAST DNS+ 서비스에서 설정한 예시입니다. DNS 서비스 제공 업체에 따라 설정 방법은 다를 수 있습니다.
![CDN도메인검증](https://static.toastoven.net/prod_cdn/v2/console-certificate-domain-validation-dns.png)


#### HTTP 페이지 추가 방식
도메인이 연결된 웹 서버에 HTTP 페이지를 추가해 도메인을 검증합니다.

1. 웹 서버의 **http://[발급 요청한 인증서 도메인]/.well-known/acme-challenge/[임의의 문자열]** 경로에 HTTP 페이지를 추가합니다.
2. HTTP 페이지의 본문에 콘솔 또는 발송된 이메일 가이드의 **페이지 콘텐츠(토큰) **값으로 설정합니다.
3. 웹 브라우저에서  **http://[발급 요청한 인증서 도메인]/.well-known/acme-challenge/[임의의 문자열]** URL로 접속하면  **페이지 콘텐츠(토큰)** 값이 화면에 표시되는지 확인합니다.

> **[주의] 도메인 검증 주의 사항**
> 1. 도메인 검증은 인증서 발급 요청일로부터 **5일 이내**에 진행해야 합니다. **기간 내 진행하지 않으면 인증서 발급은 자동으로 취소**됩니다.
> 2. 도메인 검증 작업 완료 후 검증에 성공하면 몇 시간 내 인증서 발급 및 배포 작업이 진행됩니다. 하루 이상 진행되지 않으면 도메인 검증 작업 내용이 올바른지 확인합니다. 문제가 없는데도 진행되지 않으면 TOAST 고객 센터로 문의해 주시기 바랍니다.
> 3. 도메인 검증 방식 중 HTTP 페이지 추가 방식은 HTTP 서버가 기본 포트 80 포트로 운영 중일 때만 가능합니다. 포트를 변경할 수 없다면 DNS TXT 레코드 추가 방식을 이용하시기 바랍니다.

### 인증서 발급 및 배포
도메인 검증을 통과하면 몇 시간 내 인증서 발급 및 배포 작업이 진행됩니다.
콘솔의 인증서 상태가  **인증서 발급 및 배포** 단계로 표시되며, TOAST 프로젝트 멤버 대상으로 알림 메일이 발송됩니다.
이 단계에서는 별도로 작업할 내용은 없습니다.

>  **[참고] 인증서 발급과 배포 단계의 작업 시간**
> 인증서 발급 및 배포 작업은 최대 9시간 이상 걸릴 수 있습니다.

### CDN 서비스 연동
발급된 인증서를 이용하려면 CDN 서비스와 연동해야 합니다.
이 작업을 진행하지 않거나 작업 내용을 유지하지 않으면 발급된 인증서가 만료될 수 있으므로 주의하시기 바랍니다.

1. **CNAME 레코드 설정**: 인증서 도메인의 DNS 서비스 제공 업체의 DNS 관리에서 다음의 CNAME 레코드를 추가합니다.
    - 레코드 타입:  **CNAME**
    - TTL: 임의의 값. 자주 변경해야 한다면 작게 설정하시기를 권장합니다. 레코드 변경 시 캐시 DNS 서버에 TTL 시간 동안 캐시될 수 있습니다.
    - 레코드 이름:  **[인증서 도메인].** (예시: test.alias.com.com.)
    - 레코드값:  **[연동할 CDN 서비스 도메인]** (예시: xxxxxxxx.toastcdn.net)
다음 화면은 TOAST DNS+ 서비스에서 설정한 예시입니다. DNS 서비스 제공 업체에 따라 설정 방법은 다를 수 있습니다.
![CDN서비스연동-CNAME위임](https://static.toastoven.net/prod_cdn/v2/console-certificate-service-cname.png)

2. **도메인 별칭 설정**: 인증서를 이용할 CDN 서비스에 도메인 별칭 설정을 추가합니다.
    -  **CDN 서비스** 탭에서 연동할 CDN 서비스를 선택하고  **수정** 버튼을 클릭합니다. 도메인 별칭에 인증서 도메인을 추가한 후  **확인** 버튼을 클릭합니다.
![CDN서비스연동-도메인별칭](https://static.toastoven.net/prod_cdn/v2/console-certificate-service-alias2.png)


>  **[주의] 인증서 만료 주의 사항**
> TOAST CDN에서 제공하는 인증서는 인증서 만료 전 자동으로 인증서를 갱신합니다.
> 자동으로 인증서를 갱신하려면 반드시 사용 중인 인증서가 CDN 서비스와 연동돼 있어야 합니다.
> CDN 서비스와 연동돼 있지 않으면 인증서 갱신 기간에 갱신되지 않아 인증서가 만료될 수 있습니다.
> 인증서 갱신은 **인증서 관리**의 목록에 표시된 인증서 갱신 시작일로부터 **5일 이내** 진행됩니다.
> 인증서가 만료되지 않도록 다음의 설정 사항을 항상 유지하시기 바랍니다.
>
> 1. 인증서의 도메인은 CNAME 레코드로 연동할 CDN 서비스 도메인 주소로 위임해야 합니다.
> 2. 연동할 CDN 서비스의 도메인 별칭에 인증서 도메인이 설정되어 있어야 합니다.
> 3. 인증서가 연동된 CDN 서비스를 일시 정지하면 인증서를 갱신할 수 없습니다. 인증서 갱신 시작일 이전에 재시작하거나 다른 운영 중인 CDN 서비스에 인증서를 연동하시기 바랍니다.
> 4. 인증서가 연동된 CDN 서비스를 삭제하면 인증서를 갱신할 수 없습니다. 삭제하기 전에 운영 중인 다른 CDN 서비스에 인증서를 연동하시기 바랍니다.

CDN 서비스 연동 작업이 완료되면 인증서 상태가 '정상'으로 표시됩니다.
![CDN인증서정상상태](https://static.toastoven.net/prod_cdn/v2/console-certificate-active.png)

## 통계

네트워크 전송량, HTTP 상태 코드별 통계 및 다운로드가 가장 많은 콘텐츠의 순위 통계를 확인할 수 있습니다.
7일 이내 통계 데이터는 정확하지 않으므로 참고용으로만 이용하시기 바랍니다. 정확한 통계 데이터는 7일 이후에 확인하시기 바랍니다.

1. **Contents Delivery > CDN**의 **통계** 탭을 클릭합니다.
![cdn_08_201812](https://static.toastoven.net/prod_cdn/cdn_08_201812.png)
2. 통계를 확인하려면 CDN 서비스를 선택합니다.
3. 검색 기간을 입력합니다.
4. 검색 기간 내 데이터 주기는 선택한 기간에 따라 자동으로 선택됩니다.
5. **검색** 버튼을 클릭합니다.
