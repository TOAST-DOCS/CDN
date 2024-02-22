## Content Delivery > CDN > 콘솔 사용 가이드

이 문서는 NHN Cloud CDN 콘솔에서 CDN 서비스를 구성하고 이용하는 방법을 설명합니다.

## CDN 서비스 생성

**Contents Delivery > CDN**의 **CDN 서비스** 탭에서 **생성** 버튼을 클릭하면 **CDN 서비스 생성** 대화 상자가 나타납니다.
CDN 서비스 도메인은 **[서비스ID].toastcdn.net** 형식으로 자동 생성됩니다. 만일 소유하고 있는 도메인을 서비스 도메인으로 이용하려면 **도메인 별칭**(domain alias) 기능을 이용할 수 있습니다.
생성을 요청한 후 서비스 배포가 완료될 때까지 최대 2시간이 걸립니다. 배포가 완료된 후 서비스를 이용할 수 있습니다.

> **[참고] CDN 최초 생성 시 다운로드 최적화 소요 기간**
> CDN 최초 생성 후 최대 3일까지 다운로드 속도가 다소 느려질 수 있습니다.

### 기본 정보 
기본 정보를 설정합니다.
![CDN서비스생성-기본정보](https://static.toastoven.net/prod_cdn/v2/console-cdn-create-default2_202112.png)

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

- **콜백**
  CDN 서비스 생성과 변경 작업(수정, 일시정지/재시작, 삭제)은 몇 시간이 걸립니다. 
  작업이 완료된 후 설정한 콜백 URL로 변경 상태와 CDN 설정 정보를 전달받으려면 콜백을 설정하시기 바랍니다. 콜백으로 전달되는 정보는 [API 가이드 문서](./api-guide-v2.0/#_23)를 참고하시기 바랍니다.
    1. **HTTP Method**와 **콜백 URL**을 입력합니다.
    2. Query Parameter로 CDN 서비스 변경 작업에 대한 결과를 전달받으려면 **콜백 URL**에 다음의 경로(path) 변수를 포함해 입력해 주세요. 
         예: http://callback.url?appKey={appKey}&status={status}&isSuccessful={isSuccessful})

| 경로(path) 변수 | 설명 | 예시 전달 값 |
| ------------- | --- | ------- |
| {appKey} | CDN 서비스 앱 키 | 콘솔에서 발급한 앱 키 |
| {domain} | CDN 서비스 이름 | [서비스ID].toastcdn.net |
| {status} | 현재 CDN 서비스 상태 | OPEN, SUSPEND, CLOSE, ERROR |
| {isSuccessful} | 서비스 변경 작업 성공 여부(API v1.0은 지원하지 않습니다.) | "true" 또는 "false" |


### 원본 서버
CDN 서비스로 배포할 원본 파일을 제공하는 서버를 설정합니다.
![CDN서비스생성-기본정보](https://static.toastoven.net/prod_cdn/v2/console-cdn-create-origin2_202112.png)

- **원본 타입**
    - 오브젝트 스토리지: NHN Cloud Object Storage 서비스에서 생성한 컨테이너를 원본 서버로 설정합니다.
        - 리전: 오브젝트 스토리지의 컨테이너 정보를 조회할 리전을 선택합니다.
        - 이름: 원본 서버로 설정하고자 하는 컨테이너의 이름을 입력합니다. 컨테이너 접근 정책이 **PUBLIC**인 컨테이너만을 원본 서버로 사용할 수 있습니다. 컨테이너가 원본 서버로 사용 가능할 경우, 원본 서버와 원본 경로에 컨테이너 정보가 자동으로 입력됩니다.
    - 인스턴스: NHN Cloud Instance 서비스에서 생성한 인스턴스를 원본 서버로 설정합니다.
        - 리전: 인스턴스 목록을 조회할 리전을 선택합니다.
        - 인스턴스: 리전을 선택하여 조회된 인스턴스 목록에서 원본 서버로 설정할 인스턴스를 선택합니다. 선택한 인스턴스의 IP가 원본 서버에 자동으로 입력되며, 사용할 원본 서버의 포트는 직접 입력해야 합니다. 플로팅 IP가 연결된 인스턴스만을 원본 서버로 사용할 수 있습니다.
    - 직접 입력: 별도로 운영 중인 원본 서버를 설정합니다.

- **원본 서버**
  원본 서버는 CDN 서비스로 배포할 원본 파일을 제공하는 서버입니다. 원본 서버는 IPv4 또는 전체 도메인 주소(FQDN, fully qualified domain name) 형식으로 입력할 수 있습니다. IP 주소는 변경될 가능성이 높기 때문에 도메인으로 설정하는 것을 권장합니다.
  운영 중인 원본 서버가 없다면, **원본 타입**의 **인스턴스** 옵션을 선택하여 NHN Cloud Instance 서비스의 인스턴스를 사용하거나 **오브젝트 스토리지** 옵션을 선택하여 NHN Cloud Object Storage 서비스의 컨테이너를 이용할 수 있습니다.
  CDN 서비스 도메인으로 보안 전송(HTTPS)을 지원하려면 원본 서버는 HTTPS 응답을 지원해야 합니다.
  이는 원본 서버에 NHN Cloud CDN이 신뢰하는 인증서가 설치돼 있어야 한다는 뜻입니다.
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
> 1. 전체 사이트 주소는 프로토콜 다운그레이드를 할 수 없습니다. 예를 들어 원본 서버의 전체 사이트 주소인 **www.nhn.com**는 다운그레이드할 수 없습니다.
> 2. GET, HEAD 및 OPTIONS 메서드 외 메서드는 지원되지 않습니다. 
> 3. CDN 서버에서 원본 서버로 다운그레이드를 요청할 때 다음의 헤더는 제외될 수 있습니다.
>    Origin, Referer, Cookie, Cookie2, sec-\*, proxy-\*

- **Forward Host Header**
  CDN 서버가 원본 서버에 원본 파일을 요청할 때 전달할 **Host** 헤더 값을 설정합니다.
  원본 서버가 Name-based virtual host로 운영 중이라면 **요청 호스트 헤더** 설정이 필요할 수 있습니다. 원본 서버의 운영 형태에 따라 적합한 설정 값을 선택하시기 바랍니다.
    - **원본 호스트 이름**: 원본 서버의 호스트 이름을 Host 헤더로 설정합니다. 
    - **요청 호스트 헤더**: 클라이언트 요청의 Host 헤더로 설정합니다.

> **[주의] 보안 전송(HTTPS) 사용 시 Host 헤더와 원본 서버 인증서의 유효성 검사**
> 클라이언트가 보안 전송(HTTPS)으로 콘텐츠를 요청하면 CDN 서버는 원본 서버의 인증서 유효 여부를 확인합니다.
> 원본 서버에는 Host 요청 헤더와 일치하는 CN(Common Name) 또는 SAN (Subject Alternate Name)의 인증서가 설치되어 있어야 합니다. 
> Host 요청 헤더와 일치하는 인증서가 원본 서버에 설치되어 있지 않은 경우 보안 전송 오류가 발생합니다.
> Host 요청 헤더는 Forward Host Header 설정에 따라 요청 호스트 헤더 또는 원본 호스트 이름으로 설정되므로 유의하시기 바랍니다.

### 루트 경로 접근 관리
CDN 서비스의 루트 경로에 대한 접근 제어를 설정할 수 있습니다.
![CDN서비스생성-루트경로](https://static.toastoven.net/prod_cdn/v2/console-cdn-create-root-path_1.png)

- **루트 경로 접근 설정**
    - **사용**: 루트 경로 접근 관리 기능을 활성화하여 루트 경로에 대한 요청을 차단하거나, 다른 페이지로 리다이렉트하도록 설정합니다.
    - **미사용**: 루트 경로 접근 관리 기능을 비활성화 합니다.
- **접근 제어 방식**
    - **Deny**: 루트 경로에 대한 요청에 HTTP Response Code 403을 응답합니다.
    - **Redirect**: 루트 경로에 대한 요청을 사용자가 지정한 경로로 리다이렉트 합니다.
- **Redirect 경로**
  루트 경로에 대한 요청을 리다이렉트할 경로를 입력합니다. Redirect 경로는 '/'로 시작해야 하고, CDN 서비스의 하위에 존재하는 경로여야 합니다.
- **Redirect HTTP Response Code**
    - 루트 경로에 대한 요청을 리다이렉트하고 전달할 HTTP Response Code를 설정합니다. 
    - Redirect HTTP Response Code는 301, 302, 303, 307 중에서 선택할 수 있습니다.

### 메서드
CDN에서 기본적으로 허용하는 메서드는 GET, HEAD, OPTIONS로, 이외의 메서드를 요청하면 거절 처리됩니다.
해당 메서드 이외의 메서드를 허용하려면 원하는 메서드를 선택해 설정합니다.
![CDN서비스생성-루트경로](https://static.toastoven.net/prod_cdn/v2/console-cdn-create-root-path_1.png)

- **메서드 허용 설정**
  허용 설정한 메서드 요청은 캐싱되지 않고 원본 서버로 전달됩니다.
  

### 캐시

CDN 캐시 동작 설정과 만료 시간을 설정할 수 있습니다.
![CDN서비스생성-캐시](https://static.toastoven.net/prod_cdn/v2/console-cdn-create-cache2_202208.png)

- **캐시 설정**
  CDN 서버가 원본 파일을 캐싱할 때 사용할 캐시 설정을 선택할 수 있습니다.
    - **원본 설정 사용**: 원본 서버의 응답에서 제공한 캐시 제어 헤더(Cache-Control, Expires)를 우선 적용합니다. 만일 원본 서버의 응답에 캐시 제어 헤더(Cache-Control, Expires)가 유효하지 않거나 없는 경우, 캐시 만료 시간(초)에 지정한 시간 동안 캐시됩니다.  **원본 설정 사용** 옵션이 기본값입니다.
    - **사용자 설정 사용**: 캐시 만료 시간(초)에 지정한 시간 동안 캐시됩니다. 
    - **Bypass Cache**: 해당 설정 이전에 생성된 캐시는 유지한 채 이후 콘텐츠 요청을 캐싱하지 않습니다.
    - **No Store**: 기존의 캐시를 모두 제거하고 CDN 캐시 기능을 비활성화합니다.
    
- **캐시 만료 시간(초)**
  캐시 만료 시간을 지정하려면 **사용자 설정 사용** 버튼을 클릭하고 **캐시 만료 시간(초)**에서 캐시 만료 시간을 변경합니다.

- **캐시 키 쿼리 문자열 포함 설정**
  URL 기반으로 생성되는 캐시 키에 요청 쿼리 문자열을 포함할지 설정할 수 있습니다.
    - 전체 포함: 요청에 포함된 전체 쿼리 문자열을 캐시 키에 포함합니다. 캐시 키에 요청 쿼리 문자열이 포함되므로, 동일한 컨텐츠 요청에 대해 쿼리 문자열이 변경될 때마다 새로운 캐시 키가 생성됩니다. 요청 쿼리 문자열을 변경하여 컨텐츠를 새로 캐싱하고자 할 경우 선택합니다. **전체 포함** 옵션이 기본값입니다.
    - 전체 제외: 요청에 포함된 쿼리 문자열을 모두 제외하고 URL만을 이용하여 캐시 키를 생성합니다. 요청 쿼리 문자열이 지속적으로 변경되어야 하는 경우, 해당 옵션을 설정해야 캐싱이 동작합니다.

> **[참고] 캐시 만료 시간 기본값과 유효 범위**
> 캐시 만료 시간 기본값은 0입니다. 기본값을 0으로 설정하면 캐시 만료 시간은 604,800(단위/초)=1주일입니다.
> 캐시 만료 시간은 기본값인 0부터 2,147,483,647(단위/초)까지 입력할 수 있습니다.


- **Large File Optimization**
  100MB 이상의 대용량 파일을 서비스할 때 성능과 안정성을 높이기 위한 설정입니다. 설정하지 않을 경우 CDN에서 허용하는 파일의 최대 용량은 1.8GB 미만이며, 1.8GB 이상의 대용량 파일을 서비스하려면 본 설정을 반드시 사용해야 합니다.

> **[참고] NHN Cloud Object Storage 서비스에서 생성한 컨테이너를 원본 서버로 사용하는 경우**
> Large File Optimization 기능이 정상적으로 동작하기 위해서는 원본 서버에서 전달되는 ETag 응답 헤더가 큰따옴표로 묶인 형태여야 합니다.
> NHN Cloud Object Storage 컨테이너에 ETag 응답 헤더 형식 설정에 대한 자세한 내용은 Object Storage 서비스의 API 가이드 내 [컨테이너 설정 변경 > RFC를 준수하는 ETag 형식 사용 설정](../../../../ko/Storage/Object%20Storage/ko/api-guide/#_24)을 참고하시기 바랍니다.


### 리퍼러(referer) 헤더 접근 관리
리퍼러 요청 헤더로 콘텐츠의 접근 관리를 설정합니다.
![CDN서비스생성-캐시](https://static.toastoven.net/prod_cdn/v2/console-cdn-create-cache2_202208.png)

리퍼러 요청 헤더는 현재 요청된 페이지의 링크 이전의 웹 페이지 주소를 포함합니다. 리퍼러 요청 헤더로 어떤 경로에서 요청이 유입되었는지 알 수 있습니다. 리퍼러 헤더 접근 관리는 특정 리퍼러 요청 헤더만 사용자 콘텐츠에 접근할 수 있도록 설정할 수 있습니다.
정규 표현식 형태로 입력할 수 있으며, 여러 개를 입력할 때는 줄바꿈을 한 뒤 입력합니다.

- **접근 제어 방식**
    - **블랙리스트(blacklist) 타입**:
        * 특정 리퍼러 요청 헤더만 접근을 제한할 때 적합합니다.
        * 리퍼러 요청 헤더값이 설정한 정규 표현식에 매칭되는 문자열이면 콘텐츠 접근이 제한됩니다. 매칭되지 않는 문자열이면 콘텐츠 접근이 허용됩니다.
    - **화이트리스트(whitelist) 타입**:
        * 특정 리퍼러 요청 헤더만 접근을 허용할 때 적합합니다.
        * 리퍼러 요청 헤더값이 정규 표현식에 매칭되는 문자열이면 콘텐츠 접근이 허용됩니다. 매칭되지 않는 문자열이면 콘텐츠 접근이 제한됩니다.

- **리퍼러 헤더가 없는 경우 접근 허용**
  리퍼러(referer) 요청 헤더가 없는 경우 콘텐츠 접근 허용 여부를 선택합니다.
    - **허용**: 리퍼러 요청 헤더가 없는 경우 콘텐츠 접근을 허용하며 리퍼러 접근 제어가 동작하지 않습니다.
    - **거부**: 리퍼러 요청 헤더가 없는 경우 콘텐츠 접근을 거부하고 등록된 리퍼러에 대해서만 접근을 허용합니다.
  
> **[예시]**
>
> * 타입: 화이트리스트(whitelist)
> * 정규 표현식: `^https://[a-zA-Z0-9._-]*\.nhn\.com/.*`
> 임의의 nhn.com 서브 도메인의 하위 경로에서 리소스를 요청한 경우에만 콘텐츠 접근을 허용합니다.
>
> **[참고] 정규 표현식의 이스케이프 문자**
> 일부 문자는 정규 표현식에서 특수 문자로 사용됩니다. 
> 점(`.`)을 예로 들자면, 정규 표현식에서 점(`.`)은 모든 문자와 일치함을 나타내는 특수 문자입니다.
> 특수 문자로의 의미가 아닌 일반 문자 그대로 해석해야 한다면 이스케이프 문자 백슬래시(`\`)를 특수 문자 앞에 추가하면 됩니다(예: `\.`).
> 정규 표현식의 특수 문자에는 `^ . [ ] $ ( ) | * + ? { } \` 등이 있습니다.
> 여러 개의 리퍼러를 제어할 때는 다음 줄에 연속해 입력합니다.
> API를 이용해 여러 개의 리퍼러를 설정할 때는 \n 토큰으로 구분해 입력합니다.


### Auth Token 인증 접근 관리
Auth Token 인증 접근 관리는 콘텐츠 요청에 인증 토큰을 추가하여 CDN 에지 서버에서 검증된 토큰만 콘텐츠 접근을 허용하는 보안 기능입니다.
일회성으로 콘텐츠 접근 허용하거나 제한된 사용자만 콘텐츠를 접근 할 수 있도록 제어할 수 있습니다.
토큰이 없거나 유효하지 않은 토큰으로 콘텐츠를 요청한 경우, 403 Forbidden 응답이 전송되며 콘텐츠에 접근할 수 없습니다.

Auth Token 인증 접근을 CDN 서비스에 적용하려면 다음의 단계에 따라 작업이 필요합니다.

> **[주의]** 
>
> Auth Token 인증 접근 관리는 NHN Cloud CDN을 이용해 서비스 중인 애플리케이션에서도 다음의 구현이 필요합니다.
> 1. 콘텐츠 접근에 필요한 토큰을 생성해야 합니다.
> 2. 클라이언트(최종 콘텐츠 소비자)가 생성된 토큰을 포함하여 콘텐츠를 요청할 수 있도록 해야합니다.
> 이 작업을 하지 않고 Auth Token 인증 접근 관리를 설정할 경우, 토큰 검증 실패로 인해 콘텐츠 요청이 실패될 수 있으므로 주의하시기 바랍니다.


#### 1. NHN Cloud CDN 콘솔 > Auth Token 인증 접근 관리 설정

CDN 콘솔에서 다음의 내용을 참고하여 Auth Token 인증 접근 관리를 설정합니다.

![CDN서비스생성-Auth Token 인증 접근 관리](https://static.toastoven.net/prod_cdn/v2/console-cdn-create-auth-token_202105.png)

- **토큰 인증 사용 여부**
    - **사용**: Auth Token 인증 접근 관리 기능을 활성화하여 토큰 검증한 후 콘텐츠에 접근할 수 있도록 합니다.
    - **미사용**: Auth Token 인증 접근 관리 기능을 비활성화 합니다.
- **토큰 위치**: 콘텐츠 요청 시 토큰을 전달할 위치를 선택합니다.
    - **쿠키(Cookie)**: 표준 쿠키로 토큰을 전달합니다. 쿠키 사용을 지원하지 않는 장치 및 브라우저는 토큰 인증이 정상적으로 동작하지 않을 수 있으므로 주의하시기 바랍니다.
    - **요청 헤더(Request header)**: 요청 헤더에 토큰을 전달합니다.
    - **쿼리 문자열(Query string)**: 쿼리 문자열에 토큰을 전달합니다.
- **토큰 이름**
    - 토큰값을 전달할 토큰의 이름입니다. **token** 으로 고정된 값이며 콘솔 설정에서 변경이 불가합니다.
- **토큰 암호화 키**
    - 토큰 생성에 필요한 암호화키 입니다. CDN 서비스를 생성 또는 수정하면 암호화 키는 자동으로 생성됩니다.
    - 암호화키는 외부로 노출되지 않도록 주의하시기 바랍니다.
- **토큰 인증 대상 설정**  
  콘텐츠 접근 시 토큰을 인증할 파일 대상을 설정합니다.  
  토큰 인증 대상 파일인 경우에만 토큰을 검증하며, 인증 대상 파일이 아닌 경우에는 토큰 검증을 수행하지 않으므로 토큰 없이 콘텐츠 접근이 가능합니다.  
  지정된 요청 URL 경로 또는 파일 확장자만 토큰 검증을 하려면 요청 URL 경로와 확장자를 입력해주세요. 입력하지 않은 경우 모든 파일에 대해 토큰을 검증합니다.  
    - **인증 대상 설정**: 설정된 요청 URL 경로와 파일 확장자의 파일만 토큰을 검증합니다.
    - **인증 예외 대상 설정**: 설정된 요청 URL 경로와 파일 확장자를 제외한 파일의 토큰을 검증합니다.
    - **요청 URL 경로**: 콘텐츠 URL이 요청 URL 경로와 일치되는 경우 토큰 인증 대상 또는 예외 대상으로 설정합니다.
        - 요청 URL 경로는 '/'로 시작해야 하며 와일드카드 문자(여러 문자열: \*, 단일 문자: ?)를 사용할 수 있습니다(예: /nhn/\*).
        - 요청 URL 경로는 쿼리 문자열은 포함하지 않습니다.
        - 요청 URL 경로는 아스키(ascii) 코드 문자만 입력 가능합니다.
        - 여러 개를 입력하려면 다음 줄에 입력하세요. 여러 개를 입력한 경우 하나만 일치해도 토큰 접근 제어가 동작합니다.
        - 파일 확장자와 함께 입력한 경우에는 파일 확장자 조건이 일치해도 토큰 접근 제어가 동작합니다.
    - **파일 확장자**: 콘텐츠 URL이 파일 확장자와 일치되는 경우 토큰 인증 대상 또는 예외 대상으로 설정합니다.
        - '.'을 포함하지 않은 파일 확장자를 입력합니다(예: pdf, png).
        - 여러 개를 입력하려면 다음 줄에 입력하세요. 여러 개를 입력한 경우 하나만 일치해도 토큰 접근 제어가 동작합니다.
        - 요청 경로 URL과 함께 입력한 경우에는 요청 경로 URL 조건이 일치해도 토큰 접근 제어가 동작합니다.

> **[주의] 요청 URL 경로와 파일 확장자**
> 요청 URL 경로와 파일 확장자 모두 설정한 경우, 두 조건 중 하나만 일치해도 토큰 접근 제어가 동작합니다.
> [예시] 요청 URL 경로 **/nhn/\***, 파일 확장자 **png** 가 설정된 경우: /nhn 하위의 모든 파일 또는 파일 확장자가 png인 콘텐츠에 대해 토큰을 검증합니다.

#### 2. 토큰 생성 
최종 콘텐츠 사용자가 콘텐츠에 접근하려면 토큰과 함께 콘텐츠를 요청해야 합니다. 따라서, 토큰을 생성해 최종 콘텐츠 사용자에게 발급해야 합니다.
토큰 생성은 NHN Cloud CDN을 이용해 서비스 중인 애플리케이션에서 구현되어야 합니다.
토큰 생성 방법은 다음의 샘플 코드를 참고하여 토큰을 생성합니다.

##### Java 샘플 코드
- 이 샘플 코드는 아래와 같은 제약 사항이 있습니다.
- JDK 7 이상, org.projectlombok:lombok, org.apache.commons:commons-lang3 라이브러리와 의존성이 있습니다.

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

    // NHN Cloud 콘솔에서 확인한 인증 토큰 암호화 키
    private static final String AUTH_TOKEN_ENCRYPT_KEY = "{NHN Cloud CDN 서비스의 토큰 암호화 키}";
    // 토큰 유효 시간(seconds)
    private static final Long TOKEN_DURATION_SECONDS = 3600L;


    public static void main(String[] args) throws AuthTokenException {

        String path = "/nhn/%EC%9D%B8%EC%A6%9D/%E1%84%91%E1%85%A1%E1%84%8B%E1%85%B5%E1%86%AF.png";
        String singleWildcardPath = "/nhn/%EC%9D%B8%EC%A6%9D/*";
        String[] multipleWildcardPath = {"/nhn/%EC%9D%B8%EC%A6%9D*", "/nhn/auth/*"};

        System.out.println(" ----------------- ");
        System.out.println(" 기본 토큰 발급 ");
        System.out.println(" ----------------- ");

        AuthToken authToken = new AuthToken(AUTH_TOKEN_ENCRYPT_KEY, TOKEN_DURATION_SECONDS);

        System.out.println("단일 URL 토큰: token=" + authToken.generateURLToken(path));
        System.out.println("와일드카드 토큰: token=" + authToken.generateWildcardPathToken(singleWildcardPath));
        System.out.println("멀티 와일드카드 토큰: token=" + authToken.generateWildcardPathToken(multipleWildcardPath));

        System.out.println(" ----------------- ");
        System.out.println(" 세션 식별자를 포함한 토큰 발급 ");
        System.out.println(" ----------------- ");

        AuthToken authTokenWithSession = new AuthToken(AUTH_TOKEN_ENCRYPT_KEY, TOKEN_DURATION_SECONDS, "example-sessionId");
        System.out.println("단일 URL 토큰: token=" + authTokenWithSession.generateURLToken(path));
        System.out.println("와일드카드 토큰: token=" + authTokenWithSession.generateWildcardPathToken(singleWildcardPath));
        System.out.println("복수 와일드카드 토큰: token=" + authTokenWithSession.generateWildcardPathToken(multipleWildcardPath));

    }


    public static class AuthToken {

        /** 토큰 암호화 알고리즘(SHA256 고정) **/
        private static final String HMAC_SHA_256 = "HmacSHA256";

        /** 토큰 암호화 키 (NHN Cloud CDN 콘솔 > Auth Token 인증 접근 관리 > 암호화 키) **/
        private String key;

        /**  세션 식별자 */
        private String sessionId;

        /** 토큰의 유효 시간(단위: 초) */
        private Long durationSeconds;

        /** 토큰 생성 전 url encode 적용 여부 */
        private Boolean escapeEarly;

        /** 토큰 Body 필드의 구분자 */
        private final String fieldDelimiter = "~";

        /** wildcardPath 구분자 */
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
        * 단일 URL에 대한 토큰을 생성합니다.
        * @param path : contents url (example: /auth/contents/example.png)
        * @return created token
        * @throws AuthTokenException
        */
        public String generateURLToken(String path) throws AuthTokenException {
            return generateToken(createExpireTime(), this.sessionId, path, null);

        }


        /**
        * 와일드카드 경로에 대한 토큰을 생성합니다.
        * @param wildcardPath : "/auth/contents/*"
        * @return 생성된 토큰값
        * @throws AuthTokenException
        */
        public String generateWildcardPathToken(String wildcardPath) throws AuthTokenException {
            return generateWildcardPathToken(new String[] {wildcardPath});
        }

        /**
        * 복수 개의 와일드카드 경로에 대한 토큰을 생성합니다.
        * @param wildcardPaths (example: ["/auth/contents/*", "/auth/*/images/*"])
        * @return 생성된 토큰값
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
- **AuthToken 클래스의 멤버 변수 설명**
    - **key**: NHN Cloud CDN 콘솔에 표시된 Auth Token 인증 제어 관리 > 토큰 암호화 키를 입력합니다.
    - **sessionId**: 단일 접근 요청에 대한 고유 식별자를 포함하여 토큰을 생성하려면 sessionId를 입력합니다.
        - 세션 ID 별로 유효한 토큰을 생성하여 일회성 토큰을 생성하거나 다양한 사례에 활용할 수 있습니다.
        - 세션 ID는 [출력 가능 아스키 문자표](https://ko.wikipedia.org/wiki/ASCII#%EC%B6%9C%EB%A0%A5_%EA%B0%80%EB%8A%A5_%EC%95%84%EC%8A%A4%ED%82%A4_%EB%AC%B8%EC%9E%90%ED%91%9C.)로 구성해야 합니다.
        - 세션 ID는 문자열의 길이는 최대 36바이트를 초과할 수 없습니다.
    - **durationSeconds**: 생성된 토큰이 유효한 시간(초), 유효 시간이 지난 토큰은 토큰 인증에 실패합니다.
        - 토큰 유효 시간을 너무 작게 설정하면 CDN 에지 서버에서 토큰 검증하기 전에 토큰이 만료될 수 있으니 유의하시기 바랍니다. 기대하는 토큰 유효 시간보다 10초이상 크게 설정하기를 권장합니다.
        - 토큰 생성 서버의 시간 동기화 설정 NTP (Network Time Protocol, NTP)이 유효한지 반드시 검증하시기 바랍니다. 동기화 되지 않은 시간 정보로 인해 토큰 유효 시간 검증이 실패할 수 있습니다.
- **AuthToken 클래스의 공개 메서드(Public Method)**
    - **public String generateURLToken(String path)**
        - 단일 경로에 대한 토큰을 생성합니다.
        - [예시] path: authToken.generateURLToken("/auth/contents/example.png")
        - [주의] 경로 또는 세션 ID는 URL 인코딩 문자열로 변경한 후에 토큰을 생성하시기 바랍니다(예: **/nhn/인증/파일.png** => **/nhn/%EC%9D%B8%EC%A6%9D/%E1%84%91%E1%85%A1%E1%84%8B%E1%85%B5%E1%86%AF.png**).
        - [주의] **!**, **~** 문자는 예약된 문자로 사용되므로 경로 또는 세션 ID에 포함하지 않도록 합니다.
    - **public String generateWildcardPathToken(String wildcardPath), public String generateWildcardPathToken(String... wildcardPaths)**
        - 와일드카드 경로와 매핑되는 경로의 토큰을 생성합니다. 경로의 패턴이 일치하는 경우, 와일드카드 토큰 하나로 여러 콘텐츠 URL의 토큰을 인증할 수 있습니다.
            - [예시1] wildcardPath: authToken.generateWildcardPathToken("/auth/contents/*") : /auth/contents 하위의 모든 파일에 대해 토큰을 발급합니다.
            - [예시2] wildcardPath: authToken.generateWildcardPathToken("/auth/contents/*.png") : /auth/contents 경로의 png 파일에 대한 토큰을 발급합니다.
            - [예시3] wildcardPath: authToken.generateWildcardPathToken("/auth/contents/exmaple?.png") : /auth/contents 경로의 example 와 단일 문자가 결합된 png 파일에 대한 토큰을 발급합니다.
            - [주의] 경로 또는 세션 ID는 URL 인코딩 문자열로 변경한 후에 토큰을 생성하시기 바랍니다(예: **/nhn/인증/파일.png** => **/nhn/%EC%9D%B8%EC%A6%9D/%E1%84%91%E1%85%A1%E1%84%8B%E1%85%B5%E1%86%AF.png**).
            - [주의] **!**, **~** 문자는 예약된 문자로 사용되므로 경로 또는 세션 ID에 포함하지 않도록 합니다.
        - 생성된 토큰은 **exp={expirationTime}~acl={path!path!path}~id={sessionId}~hmac={HMAC}** 형식으로 생성됩니다.
            - [예시] 생성된 토큰: **exp=1600331503~acl=%2fnhn%2f*.png~id=session-id1~hmac=2509123dcabe2fc199e3ac44793e4e135a09590ff4ebf6a902ea26469ead7f91**

#### 3. 생성된 토큰을 콘텐츠 요청에 포함
클라이언트(최종 콘텐츠 소비자)가 콘텐츠 요청시 콘솔에서 설정한 토큰 위치에 생성된 토큰값을 포함하여 요청하도록 합니다.

  - **토큰 위치: 쿠키**
    ```
    curl --cookie "token={생성된 토큰값}" \
    -X GET http://xxx.toastcdn.net/auth/contents/example.png
    ```
  - **토큰 위치: 요청 헤더**
    ```
    curl -H "token: {생성된 토큰값}" \
    -X GET http://xxx.toastcdn.net/auth/contents/example.png
    ```
  - **토큰 위치: 쿼리 문자열**
    ```
    curl -d "token={생성된 토큰값}" \
    -X GET http://xxx.toastcdn.net/auth/contents/example.png
    ```


### HTTP 응답 헤더
CDN에서 사용자에게 응답 시 전달되는 헤더를 추가/변경/삭제하는 기능입니다.
헤더는 중복되지 않은 헤더 이름으로 최대 10개까지 설정할 수 있습니다.
![CDN서비스생성-응답헤더(이미지수정필요)](https://static.toastoven.net/prod_cdn/v2/console-cdn-create-http-response-header.png)

- **HTTP 응답 헤더 설정**
    - **사용**: HTTP 응답 헤더를 추가/변경/삭제하는 설정을 사용합니다.
    - **미사용**: HTTP 응답 헤더를 설정하지 않습니다.
- **Action**: HTTP 응답 헤더를 변경하는 방식을 선택합니다.
    - **Add**: 설정된 헤더 이름을 추가하여 사용자에게 응답합니다.
    - **Modify**: 설정된 헤더 이름이 콘텐츠에 존재할 경우 헤더 값을 변경하고, 존재하지 않을 경우 추가하여 사용자에게 응답합니다.
    - **Delete**: 설정된 헤더 이름을 삭제하여 사용자에게 응답합니다.
- **헤더 이름**: Access-Control-Allow-Origin, Cache-Control, Content-Type 등의 헤더 이름을 목록에서 선택하거나 "직접 입력"을 선택하여 사용자가 정의한 커스텀 헤더 이름을 설정할 수 있습니다.
- **커스텀 헤더 이름**: 사용자가 정의한 헤더 이름을 입력합니다. 영문자와 숫자, '-', '_'만 입력 가능합니다.
- **헤더 값**: 헤더 이름의 값을 설정할 수 있으며 필수 입력값입니다.

> **[참고] CORS(교차 출처 리소스 공유) 설정**
> 아래와 같이 HTTP 응답 헤더를 설정하여 CORS를 허용할 수 있습니다.
> - **Action**: Modify
> - **헤더 이름**: Access-Control-Allow-Origin
> - **헤더 값**: *(와일드카드) 혹은 허용할 원본 URI



## 설정

### CDN 서비스 설정 변경
서비스 도메인 이름과 지역을 제외한 CDN 서비스 설정을 변경할 수 있습니다.
![CDN서비스수정활성화](https://static.toastoven.net/prod_cdn/v2/console-cdn-modify3_202105.png)

1. 변경할 CDN 서비스를 CDN 서비스 목록에서 선택합니다.
2. 화면 아래 **설정** 탭의 **수정** 버튼을 클릭합니다.

다음과 같이 변경할 수 있는 항목이 활성화됩니다.
![CDN서비스수정확인-이미지수정필요](https://static.toastoven.net/prod_cdn/v2/console-cdn-modify2_202105.png)

* 변경할 설정 내용을 수정합니다. 
* **확인** 버튼을 클릭해 변경을 완료합니다.
* 설명과 콜백 설정을 제외한 다른 설정을 변경하려면 전체 CDN 서버에 반영돼야 해서 시간이 오래 걸릴 수 있습니다. 

**수정 작업은 몇 십분 내 완료되며, 도메인 별칭 설정이 변경된 경우에는 몇 시간이 걸릴 수 있습니다.**

> **[참고] CDN 서비스 수정 중 배포 상태와 서비스 상태** 
> 서비스 수정 작업이 진행 중이면 기존 CDN 서비스 설정으로 운영됩니다.
> 만약 수정 작업에 실패하면 기존 설정 정보로 롤백되며, CDN 서비스 목록의 배포 상태가 빨간색 원으로 표시됩니다. 설정 정보에 오류가 있거나 내부적으로 오류가 발생했을 때 수정 작업에 실패합니다. 

### CDN 서비스 일시 정지와 재시작
CDN 서비스를 일시적으로 중단하거나 재시작할 수 있습니다.


1. 일시 정지할 CDN 서비스의 선택합니다.
2. **일시 정지** 버튼을 클릭합니다.
![CDN서비스-일시정지](https://static.toastoven.net/prod_cdn/v2/console-cdn-pause2_202105.png)
3. 인증서가 연동된 CDN 서비스에는 인증서 만료 경고 안내가 표시됩니다. 인증서가 만료되지 않게 하려면 인증서 갱신 시작일 이전에 CDN 서비스를 재시작해야 합니다.
![CDN서비스-일시정지](https://static.toastoven.net/prod_cdn/v2/console-cdn-restart2_202105.png)
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
![CDN서비스-삭제](https://static.toastoven.net/prod_cdn/v2/console-cdn-delete2_202105.png)
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
![CDN캐시재배포](https://static.toastoven.net/prod_cdn/v2/console-cdn-purge2_202105.png)

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

|분류 |[서비스ID].toastcdn.net |
|---|---|
| 제한 단위 | 프로젝트별(Appkey) |
| 특정 파일 | 1초당 요청 가능: 1회, 요청당 URL 수 제한: 200 URL |
| 전체 파일 타입 | 5분당 요청 가능: 1회 |

> **[주의] [서비스ID].toastcdn.net 서비스를 생성한 후 캐시 재배포 실패 오류**
> CDN 서비스를 생성한 후 약 1시간 이내에는 캐시 재배포 요청에 실패할 수 있습니다. 이후에도 계속 실패하면 고객 센터로 문의해주시기 바랍니다.

## 인증서 관리 
소유한 도메인으로 콘텐츠를 보안 전송(HTTPS)하려면 CDN 서버에 소유한 도메인의 인증서를 배포해야 합니다. 인증서가 없으면 클라이언트(브라우저)와 CDN 에지 서버 간 보안 통신(HTTPS)을 할 수 없어 인증서 오류가 발생합니다.
NHN Cloud CDN의 인증서 관리는 다음과 같은 기능을 제공합니다.

- 단일 도메인 타입의 인증서 발급
- 전 세계 거점의 CDN 서버에 인증서 배포(중국과 러시아 지역은 제외)
- 인증서 만료 전 자동 갱신

### 신규 인증서 발급 
**인증서 관리** 탭에서 인증서를 발급할 수 있습니다.
![CDN신규인증서발급](https://static.toastoven.net/prod_cdn/v2/console-certificate-create_202105.png)

1. **인증서 관리** 탭의 **신규 인증서 발급** 버튼을 클릭합니다.
2. 발급할 인증서의 도메인을 전체 도메인 주소(FQDN, fully qualified domain name)형식으로 입력합니다.
3. 인증서 발급 안내 내용을 확인하고 **확인** 버튼을 클릭합니다.
4. 신규 발급 인증서를 요청하면 **인증서 관리** 탭의 인증서 도메인이 표시됩니다. 인증서 상태가 **도메인 검증** 상태로 변경되면 이후 도메인 검증 작업을 진행하시기 바랍니다. 

> **[주의] 인증서 발급 전 확인 사항**
> 1. 소유한 도메인만 인증서를 발급할 수 있으므로 먼저 도메인을 구매하신 후 진행하시기 바랍니다. 
> 2. 다른 인증 기관(CA, certificate authority)에서 발급한 인증서는 이용할 수 없습니다. 
> 3. 단일 도메인의 인증서 발급만 가능합니다. 와일드카드, 멀티 도메인 등의 인증서는 지원하지 않습니다.
> 4. 인증서 발급은 프로젝트당 5개로 제한됩니다. 한도 조정이 필요한 경우 NHN Cloud 고객 센터로 문의하시기 바랍니다.
> 5. 신규 인증서 발급 요청 후 도메인 검증 단계는 몇 십분(최대 1~2시간) 후 변경될 수 있습니다. 인증서 상태가 도메인 검증 상태로 변경되면 NHN Cloud 프로젝트 멤버를 대상으로 이메일 발송됩니다. 만일 시스템 오류로 이메일이 발송되지 않는다면 콘솔에서 상태를 확인하시기 바랍니다. 

### 도메인 검증 
신규 인증서 발급을 요청한 후 인증서 상태가 '도메인 검증'이 되면 도메인을 검증하시기 바랍니다.
도메인 검증 방법은 콘솔에서 도메인을 선택하여 확인하거나, 프로젝트 멤버에게 전송된 도메인 검증 가이드 메일의 내용을 참고하시기 바랍니다.

![CDN도메인검증](https://static.toastoven.net/prod_cdn/v2/console-certificate-domain-validation_202105.png)

도메인 검증은 발급 요청한 인증서 도메인의 실제 소유자인지 확인하는 단계 입니다. 도메인 검증을 진행하지 않으면 인증서를 발급할 수 없습니다.
도메인 소유자인지 확인하기 위해 도메인 검증 방식으로 도메인의 제어 권한을 확인합니다. 
도메인 검증 방식에는 **DNS TXT 레코드 추가** 또는 **HTTP 페이지 추가** 방식이 있으며 **두 가지 방식 중 하나만 진행**하면 됩니다.

![CDN도메인검증](https://static.toastoven.net/prod_cdn/v2/console-certificate-domain-validation2_202105.png)

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

다음 화면은 NHN Cloud DNS+ 서비스에서 설정한 예시입니다. DNS 서비스 제공 업체에 따라 설정 방법은 다를 수 있습니다.
![CDN도메인검증](https://static.toastoven.net/prod_cdn/v2/console-certificate-domain-validation-dns_202105.png)


#### HTTP 페이지 추가 방식 
도메인이 연결된 웹 서버에 HTTP 페이지를 추가해 도메인을 검증합니다. 

1. 웹 서버의 **http://[발급 요청한 인증서 도메인]/.well-known/acme-challenge/[임의의 문자열]** 경로에 HTTP 페이지를 추가합니다. 
2. HTTP 페이지의 본문에 콘솔 또는 발송된 이메일 가이드의 **페이지 콘텐츠(토큰) **값으로 설정합니다. 
3. 웹 브라우저에서  **http://[발급 요청한 인증서 도메인]/.well-known/acme-challenge/[임의의 문자열]** URL로 접속하면  **페이지 콘텐츠(토큰)** 값이 화면에 표시되는지 확인합니다. 

> **[주의] 도메인 검증 주의 사항**
> 1. 도메인 검증은 인증서 발급 요청일로부터 **5일 이내**에 진행해야 합니다. **기간 내 진행하지 않으면 인증서 발급은 자동으로 취소**됩니다.
> 2. 도메인 검증 작업 완료 후 검증에 성공하면 몇 시간 내 인증서 발급 및 배포 작업이 진행됩니다. 하루 이상 진행되지 않으면 도메인 검증 작업 내용이 올바른지 확인합니다. 문제가 없는데도 진행되지 않으면 NHN Cloud 고객 센터로 문의해 주시기 바랍니다.
> 3. 도메인 검증 방식 중 HTTP 페이지 추가 방식은 HTTP 서버가 기본 포트 80 포트로 운영 중일 때만 가능합니다. 포트를 변경할 수 없다면 DNS TXT 레코드 추가 방식을 이용하시기 바랍니다.

### 인증서 발급 및 배포
도메인 검증을 통과하면 몇 시간 내 인증서 발급 및 배포 작업이 진행됩니다. 
콘솔의 인증서 상태가  **인증서 발급 및 배포** 단계로 표시되며, NHN Cloud 프로젝트 멤버 대상으로 알림 메일이 발송됩니다. 
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
    - 레코드값:  **[연동할 CDN 서비스 도메인]** (예: xxxxxxxx.toastcdn.net)
다음 화면은 NHN Cloud DNS+ 서비스에서 설정한 예시입니다. DNS 서비스 제공 업체에 따라 설정 방법은 다를 수 있습니다.
![CDN서비스연동-CNAME위임](https://static.toastoven.net/prod_cdn/v2/console-certificate-service-cname_202105.png)

2. **도메인 별칭 설정**: 인증서를 이용할 CDN 서비스에 도메인 별칭 설정을 추가합니다. 
    -  **CDN 서비스** 탭에서 연동할 CDN 서비스를 선택하고  **수정** 버튼을 클릭합니다. 도메인 별칭에 인증서 도메인을 추가한 후  **확인** 버튼을 클릭합니다.
![CDN서비스연동-도메인별칭](https://static.toastoven.net/prod_cdn/v2/console-certificate-service-alias2_202105.png)

>  **[참고] CNAME 레코드 전파 시간**
> CNAME 레코드 설정 시 다양한 요인에 따라 DNS 전파에 시간이 소요될 수 있습니다. 따라서 서비스 연동 과정을 올바르게 수행한 뒤에도 일정 시간 동안 인증서 발급 상태가 [CDN 서비스 연동 대기]로 표시될 수 있습니다.
> 설정을 올바르게 하였는데도 24시간 이상 [CDN 서비스 연동 대기] 상태가 지속될 경우 NHN Cloud 고객 센터로 문의해 주시기 바랍니다.

>  **[주의] 인증서 만료 주의 사항**
> NHN Cloud CDN에서 제공하는 인증서는 인증서 만료 전 자동으로 인증서를 갱신합니다. 
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
![CDN인증서정상상태](https://static.toastoven.net/prod_cdn/v2/console-certificate-active_202105.png)

>  **[참고] 발급된 인증서 오류 발생 시 조치 사항**
> NHN Cloud CDN에서 제공하는 인증서의 Root 인증서 중 하나인 IdenTrust DST Root CA x3가 2021년 9월 30일에 만료되어, 일부 오래된 단말 또는 구형 브라우저에서 문제가 발생할 수 있습니다.
> 클라이언트에서 ERR_CERT_DATE_INVALID 오류로 문제가 발생하는 경우, 아래의 내용을 참고하여 OS 설정 변경 후 업데이트 또는 Root 인증서 수동 설치 등의 조치가 필요합니다.
> 1. ISRG x1 인증서 다운로드 링크: [다운로드 링크](https://letsencrypt.org/certs/isrgrootx1.pem)
> 2. Windows OS 설정 변경 참고 가이드: [링크](https://cert.crosscert.com/윈도우windows-운영체제-pc에서-루트인증서-설치방법/)
> 3. 크롬 브라우저 참고 가이드: [링크](https://docs.vmware.com/en/VMware-Adapter-for-SAP-Landscape-Management/2.0.1/Installation-and-Administration-Guide-for-VLA-Administrators/GUID-D60F08AD-6E54-4959-A272-458D08B8B038.html)

## 통계

네트워크 전송량, HTTP 상태 코드별 통계 및 다운로드가 가장 많은 콘텐츠의 순위 통계를 확인할 수 있습니다.
7일 이내 통계 데이터는 정확하지 않으므로 참고용으로만 이용하시기 바랍니다. 정확한 통계 데이터는 7일 이후에 확인하시기 바랍니다. 

1. **Contents Delivery > CDN**의 **통계** 탭을 클릭합니다.
![cdn_08_201812](https://static.toastoven.net/prod_cdn/cdn_08_202105.png)
2. 통계를 확인하려면 CDN 서비스를 선택합니다.
3. 검색 기간을 입력합니다.
4. 검색 기간 내 데이터 주기는 선택한 기간에 따라 자동으로 선택됩니다.
5. **검색** 버튼을 클릭합니다.

>  **[참고] 최대 검색 기간**
> 최근 90일간의 통계 데이터만 조회가 가능합니다.

>  **[참고] Top Contents By Hits 통계 제약사항 **
> 하루 이전까지, 하루 이상의 범위로 조회가 가능합니다.
> 100KB 이하의 콘텐츠 혹은 요청 횟수가 하루 50번 미만인 콘텐츠는 통계에서 제외됩니다.
