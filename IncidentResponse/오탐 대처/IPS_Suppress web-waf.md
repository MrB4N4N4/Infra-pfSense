## IPS_Suppress web-waf

아래와 같이, WEB 과 WAF 사이의 패킷을 Alert 하는 상황이 발생하였다.

외부-WAF-WEB 방식으로 통신하기 때문에, 외부-WAF 와 WAF-WEB 의 패킷이 중복적으로 발생한다. 아래의 Alert 은 불필요하다고 판단되어 Snort 룰을 추가해 주었다.

![vmrc_9k7nElCigI](https://user-images.githubusercontent.com/79683414/144730487-31102981-e3bb-46e1-b653-b59b8991a6ac.png)

<br><br>

DMZ 영역의 룰만 추가해 주면 된다.

IP를 기준으로 Suppress 하지 않고 Snort 룰을 따로 추가해주었다.

WEB 또는 WAF와 통신하는 패킷을 Pass 하는 것이 아니라 WEB-WAF 통신 패킷 만 Pass 하기 위함이다.

<br>

추가해준 룰은 아래와 같다.

`pass tcp 172.30.1.2 any <> 172.30.1.30 any`

![vmrc_jG643Jwr6o](https://user-images.githubusercontent.com/79683414/144730948-77d0a841-dc54-4f9b-893b-722d514f783e.png)

