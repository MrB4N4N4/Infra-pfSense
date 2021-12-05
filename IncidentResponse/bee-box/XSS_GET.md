## XSS_GET

GET 요청으로 XSS 공격을 수행한 후 그것을 탐지하는 룰을 설정하는 연습이다.

<br>

_공격 수행_

![vmware_NoXyqxFj25](https://user-images.githubusercontent.com/79683414/144731162-9815c8ff-4d0b-42bc-9774-6cff4272a1e2.png)

<br>

_pfsense_snort(IPS)_

![vmrc_z0ohmeb5hf](https://user-images.githubusercontent.com/79683414/144731256-a615d199-44ce-4cb0-a92a-6062db5dfed7.png)

snort에 공격 시도가 alert 된다.



<br>

SecurityOnion(IDS) 에서는 해당 룰이 존재하지 않으므로 아무런 Alert 이 발생하지 않는다.

우선 공격 패킷을 분석하기 위해 아래와 같은 룰을 추가해주자.

<br>

_sudo nano /etc/nsm/rules/local.rules_

`alert tcp $EXTERNAL_NET any > $HTTP_SERVERS any (msg: "XSS in GET"; content:"script%3e"; nocase; classtype:web-application-attack; sid:21120501; rev:1;)

<br>

> nocase, classtype, sid, rev 는 대부분 공통적으로 들어가는 옵션이다.

<br>

룰 설정 후엔 아래의 명령어를 이용해 업데이트를 해준다.

```bash
sudo rule-update
sudo nsm --sensor --restart --only-snort-alert
```

<br>

룰 업데이트 후 다시 공격을 수행해봤다.

![vmrc_ZklDzBj6g1](https://user-images.githubusercontent.com/79683414/144734078-2bc2c7b4-b897-437c-bb1a-4c97ab847080.png)

<br>

3개의 패킷이 Alert 된다. 위에서 부터 순서대로

1. WAF 에서 Splunk 로 포워딩 해주는 패킷
2. WAF 에서 WEB 으로 보내는 패킷
3. 공격자의 패킷

구축한 인프라가 정상적으로 작동하고 있다. ~~뿌듯😭~~

<br>

룰이 제대로 적용되었다는 것을 확인하고,

공격 패킷을 자세히 살펴보자.

![vmrc_j9f0YDCNrA](https://user-images.githubusercontent.com/79683414/144734174-8afc05b3-4aef-4175-a4e5-d4a064a83ef7.png)

```http
GET /bWAPP/xss_get.php?firstname=%3Cscript%3Ealert%28%22Hacked%21%22%29%3C%2Fscript%3E&lastname=a&form=submit HTTP/1.1

Host: mylab.com

Upgrade-Insecure-Requests: 1

User-Agent: ...


```

<br>

GET 방식으로 Request를 보내기 때문에 URL의 `?variable=...` 을 통해 변수를 전달한다.

위의 `?firstname=%3Cscript~~~` 을 보면 확인할 수 있다.

탐색을 효율적으로 하기 위해서 HTTP의 Header 만 검사해주면 될 것 같다.

추가해줄 옵션은 다음과 같다.

1. 세션 활성화, 서버로 가는 패킷 - flow:to_server,established
2. GET 이므로, header만 검사 - http_header

<br>

![vmrc_4MNjDynCGt](https://user-images.githubusercontent.com/79683414/144734975-1e2f3ff8-96c3-4b09-ad6a-45d5ec1ba517.png)

<br>

!!

제대로 탐지가 되고 Splunk 로 포워딩 해주는 패킷은 Alert 하지 않게 되었다.

WEB-WAF 사이의 패킷은  공격자가 보낸 패킷과 동일하다. WAF가 프록시 역할을 하기 때문이다. 불필요하므로 Threshold 를 설정해주면 된다.

<br>

_sudo nano /etc/nsm/name_of_sensor/threshold.conf_

```bash
suppress gen_id 1, sig_id 21120501, track by_dst, ip 172.30.1.30
suppress gen_id 1, sig_id 21120501, track by_src, ip 172.30.1.30
```

![vmrc_q6u00aXUqD](https://user-images.githubusercontent.com/79683414/144736856-252ed472-2669-429b-9358-8d2e90fbe0f0.png)