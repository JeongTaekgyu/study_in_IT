# DNS (Domain Nam System)

DNS 서버는 이름에 대한 요청을 IP 주소로 변환하여 최종 사용자가 도메인 네임을 웹 브라우저에 입력할 때 해당 사용자를 어떤 서버에 연결할 것인지를 제어합니다. 이 요청을 **쿼리**라고 부른다.

<br>

## 1. DNS 동작 원리

먼저 모든 단말(PC)은 DNS 서버의 IP 주소가 설정되어 있어야 한다. 보통 PC는 DHCP 프로토콜로 IP 주소를 할당 받으면서 DNS 서버 IP 주소를 DHCP Option 6을 통해 함께 받는다. (보통 2개의 DNS IP 주소를 받는다.
Primary DNS 서버가 죽었을때 Secondary DNS 서버에 물어 보기 위해서...) 

어느 PC의 예시를 보자.
DNS 서버 주소가 203.248.252.2와 164.124.101.2로 설정 되어 있다. 이  2개의 주소는 LG U+ DNS 서버 주소이다. ([www.whois.co.kr](http://www.whois.co.kr/)에서 IP 주소 관련 정보 확인이 가능함)

 

![img](https://www.netmanias.com/ko/?m=attach&no=1996)

 

1. 이제 아래 그림과 같이 PC 브라우저에서 www.naver.com을 입력한다. 그러면 PC는 미리 설정되어 있는 DNS (단말에 설정되어 있는 이 DNS를 Local DNS라 부름, 해당 PC의 경우는 203.248.252.2)에게 "www.naver.com이라는 hostname"에 대한 IP 주소를 물어본다.
2. Local DNS에는 "www.naver.com에 대한 IP 주소"가 있을 수도 없을 수도 있다. 만약 있다면 Local DNS가 바로 PC에 IP 주소를 주고 끝난다. 본 설명에서는 Local DNS에 "www.naver.com에 대한 IP 주소"가 없다고 가정하자
3. Local DNS는 이제 "www.naver.com에 대한 IP 주소"를 찾아내기 위해 다른 DNS 서버들과 통신(DNS 메시지)을 시작한다. 먼저 Root DNS 서버에게 "너 혹시 www.naver.com에 대한 IP 주소 아니?"라고 물어본다. 이를 위해 각 Local DNS 서버에는 Root DNS 서버의 정보 (IP 주소)가 미리 설정되어 있어야 한다.
4. 여기서 "Root DNS"라 함은 좀 특별한 녀석이다(기능의 특별함이 아니고 그 존재감이...) 이 Root DNS 서버는 전세계에 13대가 구축되어 있다. 미국에 10대, 일본/네덜란드/노르웨이에 각 1대씩... 그리고 우리나라의 경우 Root DNS 서버가 존재하지는 않지만 Root DNS 서버에 대한 미러 서버를 3대 운용하고 있다고 한다.
5. Root DNS 서버는 "www.naver.com의 IP 주소"를 모른다. 그래서 Local DNS 서버에게 "난 www.naver.com에 대한 IP 주소 몰라. 나 말고 내가 알려주는 다른 DNS 서버에게 물어봐~"라고 응답을 한다.
6. 이 다른 DNS 서버는 "com 도메인"을 관리하는 DNS 서버이다.
7. 이제 Local DNS 서버는 "com 도메인을 관리하는 DNS 서버"에게 다시 "너 혹시 www.naver.com에 대한 IP 주소 아니?"라고 물어본다.
8. 역시 "com 도메인을 관리하는 DNS 서버"에도 해당 정보가 없다. 그래서 이 DNS 서버는 Local DNS 서버에게 "난 www.naver.com에 대한 IP 주소 몰라. 나 말고 내가 알려주는 다른 DNS 서버에게 물어봐~"라고 응답을 한다. 이 다른 DNS 서버는 "naver.com 도메인"을 관리하는 DNS 서버이다.
9. 이제 Local DNS 서버는 "naver.com 도메인을 관리하는 DNS 서버"에게 다시 "너 혹시 www.naver.com에 대한 IP 주소 있니?"라고 물어본다.
10. "naver.com 도메인을 관리하는 DNS 서버"에는 "www.naver.com 호스트네임에 대한 IP 주소"가 있다. 그래서 Local DNS 서버에게 "응! www.naver.com에 대한 IP 주소는 222.122.195.6이야~"라고 응답을 해준다.
11. 이를 수신한 Local DNS는 www.naver.com에 대한 IP 주소를 캐싱을 하고(이후 다른 넘이 물어보면 바로 응답을 줄 수 있도록) 그 IP 주소 정보를 단말(PC)에 전달해 준다.

 <br>

이와 같이 Local DNS 서버가 여러 DNS 서버를 차례대로 (Root DNS 서버 -> com DNS 서버 -> naver.com DNS 서버) 물어봐서 그 답을 찾는 과정을 **Recursive Query**라고 부른다.

 

![img](https://www.netmanias.com/ko/?m=attach&no=1997)

 <br>

그리고 한가지만 더!

- http://www.naver.com/index.html   -> 이거는 URL이라 부른다.
- www.naver.com               -> 이거는 Host Name이라 부른다.
- .com                     -> 이거는 Top-level Domain Name이라 부른다.
- .naver.com                 -> 이거는 Second-level Domain Name이라 부른다.

<br>

## 2. DNS의 구성 요소

- **도메인 네임 스페이스 (Domain Name Space)**
  - DNS는 거대한 분산 네이밍 시스템이며, 도메인 네임 스페이스는 이러한 DNS가 저장/관리하는 계층적 구조를 의미한다.
  - 최상위 Root DNS 서버가 존재하고, 그 하위로 인터넷에 연결된 모든 노드가 연속해서 이어진 계층 구조로 구성되어 있다. 각 레벨(Top level, Second level등)의 도메인은 그 하위 도메인에 관한 정보를 관리하는 구조이다.

- **네임 서버 (Name Server)**
  - 문자열로 표현된 도메인 이름을 실제 컴퓨터가 통신할 때 사용하는 숫자로 표현된 IP 주소로 변환하는 시켜주어야한다. 이러한 동작을 위해서는 도메인 네임 스페이스의 트리 구조에 대한 정보가 필요하며, 이러한 정보를 가지고 있는 서버를 네임 서버라고 한다. 즉, 도메인 이름을 IP 주소로 변환하는 것을 네임 서비스라고 하며 리졸버(Resolver)로 부터 요청 받은 도메인 이름에 대한 IP정보를 다시 리졸버로 전달해주는 역할을 수행하는 것이 네임 서버이다.
- **리졸버 (Resolver)**
  - 리졸버는 웹 브라우저와 같은 DNS 클라이언트의 요청을 네임 서버로 전달하고 네임 서버로부터 정보(도메인 이름과 IP 주소)를 받아 클라이언트에게 제공하는 기능을 수행한다. 이 과정에서 리졸버는 하나의 네임 서버에게 DNS 요청을 전달하고 해당 서버에 정보가 없으면 다른 네임 서버에게 요청을 보내 정보를 받아 온다
- **DNS 기본 동작 과정**
  - 사용자가 요청한 도메인의 IP 주소는 Local DNS 서버가 다른 DNS 서버와 통신하여 사용자에게 제공한다. 즉, Local DNS 서버는 사용자의 요청을 대신하여 해당 도메인의 IP 주소 정보를 획득할 때까지 필요에 따라 여러 DNS 서버와의 통신을 수행하고(Recursion, 재귀적 통신), 해당 정보를 획득한 후 사용자에게 그 결과 값을 전달한다. (Local DNS 서버에 캐쉬되어 있는 경우에는 다른 DNS 서버와의 연동 없이 바로 사용자에게 결과 값을 전달한다.) 다음은 실제 웹 브라우저에서 URL을 입력하여 요청 했을 경우 동작되는 Flow다.
