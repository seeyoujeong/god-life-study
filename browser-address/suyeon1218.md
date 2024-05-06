# IP 주소

1. **IPv4**

   32 비트를 8비트 단위로 나눠 나타낸다. `123.45.23.212`

2. **IPv6**

   `2001:db8:ff00:53:2342` 와 같은 방식으로 64비트를 16비트로 나눠 나타낸다.

# IP 주소와 MAC 주소

1. 도메인 이름은 DNS 서버를 통해 도메인의 IP 주소로 바뀐다.
2. 컴퓨터는 IP 주소를 통해 데이터를 전송하려는 과정에서 ARP 프로토콜을 사용한다.
   - ARP(Address Resolution Protocol): IP주소를 물리 주소인 MAC주소로 변환한다.
   - RAPR: MAC주소를 IP주소로 변환하기 위해 사용한다.
3. MAC 주소로 변환된 물리 주소를 사용하여 컴퓨터는 네트워크 통신을 한다.

# **주소창에 google.com을 입력하면 일어나는 일**

### **브라우저의 DNS 조회**

사용자가 주소창에 [google.com](http://google.com) 을 입력하면 브라우저는 DNS 서버를 조회한다.

### **로컬 DNS 캐시 조회**

로컬 DNS 서버가 이전에 조회한 도메인에 대한 정보를 저장한 로컬 DNS 캐시를 확인한다. 만약 캐시에 해당 정보가 있으면 바로 반환한다.

> 해당 캐시는 파일 시스템에 저장되어 있다. 호스트 파일이라고 불리며 호스트 파일엔 도메인 네임과 IP 주소가 매핑되어 저장되어 있다.

### **로컬 DNS 서버 조회**

캐시에 해당 도메인이 존재하지 않는다면 PC 에 등록된 로컬 DNS 서버에 도메인 IP 를 조회한다.

> 로컬 DNS 서버는 인터넷 등록 당시 가입한 기지국 (SKT, KT) 이 등록해준 DNS 서버를 의미한다.
>
> [참조블로그](https://inpa.tistory.com/entry/WEB-🌐-DNS-개념-동작-완벽-이해-★-알기-쉽게-정리)

### **로컬 DNS 서버 → 루트 DNS 조회**

- **로컬 → 루트**
  캐시에 해당 정보가 없는 경우 루트 DNS 서버에 해당 도메인에 대한 IP 주소를 요청한다.
- **루트 → 최상위 → 루트 → 로컬**
  루트 DNS 서버에서 해당 도메인을 찾을 수 없다면 로컬 DNS 에게 최상위 DNS 서버의 IP 주소를 알려준다.

### **로컬 DNS 서버 → 최상위(Top Level Domain) DNS 조회**

- **로컬 → 최상위**
  로컬 DNS 서버는 최상위 DNS 서버에 해당 도메인에 대한 IP 주소를 요청한다.
- **최상위 → 도메인 → 최상위 → 로컬**
  DNS 서버에서 도메인을 찾을 수 없다면 로컬 DNS 서버에 도메인 DNS 서버의 IP 주소를 알려준다.

### **로컬 DNS 서버 → 도메인 DNS 조회**

- **로컬 → 최상위**
  로컬 DNS 서버는 반환받은 IP 로 다시 해당 도메인의 DNS 서버에 요청을 보낸다.

### **도메인의 DNS 서버 → 브라우저**

“google.com” 도메인의 DNS 서버는 해당 도메인의 IP 주소를 반환한다.

도메인 DNS 서버에서 반환받은 IP 주소는 브라우저에 전달되고, 브라우저는 IP 주소를 이용해 사이트를 띄운다.

# DNS 서버란?

DNS (Domain Name System) 서버는 도메인을 IP로 바꾸거나, IP를 도메인으로 바꾸는 서비를 제공하는 시스템이다.