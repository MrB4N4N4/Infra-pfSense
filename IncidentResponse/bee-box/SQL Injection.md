## SQL Injection

![vmware_A8gxU1A0SL](https://user-images.githubusercontent.com/79683414/144789460-a8c6f7f6-d29a-417e-8612-e99111e465eb.png)

<br>

Bee-box 의 'SQL Injection(GET/Search)-low'이다.

SQL Injection 이 가능한지 ` ' ` 를 입력해본 상태이다. 

보안적인 요소가 잘 구현이 되었다면 필터링 함수로 걸러지지만 low 단계에서는 그렇지 않다.

일단, Sguil 의 상태를 확인해보자.

<br>

![vmrc_mu1ad8crA8](https://user-images.githubusercontent.com/79683414/144789686-a3fff431-8a80-4c3d-84d6-3ff01d8ead42.png)

<br>

룰의 내용을 확인해보니 핵심은,

1. 세션 연결 상태, 서버에서 나가는 패킷
2.  200 state 코드 탐지
3.  "error in you SQL syntax" 탐지
4.  etc...

서버에서 SQL 오류가 발생하였을 때 발생하는 Alert 이다.

<br>

Union Select 를 이용한 인젝션을 수행해봤더니 아래와 같은 Alert 이 발생했다.

![vmrc_UHmIfPhl5d](https://user-images.githubusercontent.com/79683414/144796284-bb6b8de7-e1a2-43fb-a1da-0492e3bf028b.png)

<br>

SQL 인젝션에 사용되는 키워드들을 필터링 해주면 될것 같다. 

- 