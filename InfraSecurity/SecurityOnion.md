# Security Onion Directory

- [/etc/nsm/](#/etc/nsm/) : 보안양파 설정 파일 (튜닝 목적)
- [/nsm/](#/nsm/) : 보안양파가 탐지한 로그 파일이 보관되는 곳. (탐지 분석할 때 참고) 
- [/var/log/nsm/](#/var/log/nsm/) : 보안양파 오류 로그, 시스템 로그. (IDS 문제가 발생했을 때 이곳을 참고)



## /etc/nsm/

__/etc/nsm/rules/local.rules__ : 커스텀 룰 관리

룰의 형태는 "Header + Options" 이다.

Header : 

<br><br>

__/etc/nsm/name_of_sensor/snort.conf__ : 스노트 설정 파일

보통 기본값으로 유지, 네트워크 변수 설정으로 HOME 과 EXTERNAL 구분만 했음.

<br>

[설정 항목]

1. 네트워크 변수 설정 ( HOME/EXTERNAL/VLAN etc)
2. 디코더 설정
3. 기본 탐지 엔진 설정
4. 동적으로 로드되는 라이브러리 설정
5. 전처리 장치 설정
6. 출력 플러그인 설정
7. 룰셋 커스터마이징
8. 전처리 당계와 디코더 룰셋 커스터마이징
9. 공유된 객체 룰셋 커스터마이징

<br><br>

__/etc/nsm/securityonion.conf__ : 로그 보관 관련 설정

로그의 양을 산정하고 하드웨어 사양에 맞춰 다음 변수를 설정했음.

WARN 과 CRIT 설정, 경고와 삭제 행위가 시작될 때 약간의 갭이 있어 IDS 장애를 피하고 싶으면 60/70 정도로 낮추는게 좋음.

- DAYSTOKEEP=30 (로그 보관 주기)
- WARN_DISK_USAGE=80 (사용량 경고 %)
- CRIT_DISK_USAGE=90 (로그 삭제 %)

<br><br>

__/etc/nsm/rules/bpf.conf__ : BPF 중앙 관리 파일 (모든 센서, Snort/수리카타/Netsniff-ng/PRADS 에 반영된다.)

> BPF(Berkeley Packet Filter) : 패킷 필터를 위한 문법

<br>

__/etc/nsm/name_of_sensor/bpf *.conf__ : 위의 BPF 중앙 관리 파일의 개별 파일

불필요한 Traffic 을 거르는 용도. ex) SMB

개별 파일에 필터링을 적용하려면 해당 파일을 삭제 후 다시 생성한다.

<br><br>

__/etc/nsm/rules/__ : 룰 파일이 저장된 곳

"downloaded.rules" - 탐지 패턴에 대한 핵심 룰이 들어있다. 보통 업데이트만 하고 건드리지 않는다(불필요한 룰은 disablesid.conf 로 설정)

<br><br>

__/etc/nsm/name_of_sensor/classification.config__ : 커스텀 룰을 분류하는 목적. 위험도를 판단 할 수 있다.

config classficatoin : (분류이름), (설명), (우선순위)

<br><br>

__/etc/nsm/name_of_sensor/threshold.conf__ : 알럿의 임계치를 조절하는 용도

[Examples]

```bash
# 특정 ip 알럿 발생 금지, 네트워크 대역도 가능
suppress gen_id 1, sig_id 3000001, track by_dst, ip 216.58.221.14

# 임계치에 도달했을 때만 alert 발생(1분 동안 alert 1회)
event_filter gen_id 1, sig_id 3000001, type limit, track by_src, count 1, seconds 60
```

<br><br>

__/etc/nsm/pulledpork/disablesid.conf__ : 위에서 언급한 불필요한 룰 설정

"pcre:문자열" - 문자열 포함된 룰 제외

## /nsm/

__/nsm/sensor_data/name_of_sensor/dailylogs/날짜__ : 패킷 캡처 파일이 저장됨. 확장자를 ".pcap" 으로 변경하면 `WireShark` 에 띄울 수 있음.

<br><br>

## /var/log/nsm/



