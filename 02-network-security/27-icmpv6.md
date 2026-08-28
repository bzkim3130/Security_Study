# ICMPv6 프로토콜

## 한줄 요약
> ICMPv6는 IPv4의 ICMP+ARP+IGMP 기능을 하나로 통합했으며, NDP(이웃탐색프로토콜)의 NS/NA가 ARP를 대체한다.

## 1. 개요

| 항목 | 내용 |
|---|---|
| Next Header 값 | 58 |
| 통합된 기능 | IPv4의 ICMP(오류/제어) + ARP(주소해석) + IGMP(멀티캐스트그룹관리) |
| 메시지 분류 | Error Message(1~127), Informational Message(128~255) |

## 2. 메시지 유형 계층

```
ICMPv6
├── Error Messages (Type 1~127)
│     Destination Unreachable, Packet Too Big, Time Exceeded 등
│
└── Informational Messages (Type 128~255)
      ├── Echo Request(128) / Echo Reply(129)
      ├── MLD (Multicast Listener Discovery) — IGMP 대체
      └── NDP (Neighbor Discovery Protocol) — ARP 대체
            ├── RS (Router Solicitation, 133)
            ├── RA (Router Advertisement, 134)
            ├── NS (Neighbor Solicitation, 135)
            ├── NA (Neighbor Advertisement, 136)
            └── Redirect (137)
```

## 3. NDP 동작 순서 (호스트가 라우터/이웃을 찾는 과정)

```
[라우터 탐색]
호스트 → RS(라우터야 있니?) → 네트워크
라우터 → RA(나 여기있어+설정정보) → 호스트

[이웃(MAC주소) 탐색, ARP 역할 대체]
호스트A → NS(이 IP의 MAC 주소 뭐야?) → 호스트B
호스트B → NA(내 MAC은 이거야) → 호스트A
```

## 4. IPv4 vs IPv6 기능 대응표

| IPv4 기능 | IPv6에서의 대응 |
|---|---|
| ARP(주소해석) | NS/NA (NDP) |
| ICMP Redirect | ICMPv6 Redirect(Type 137) |
| IGMP(멀티캐스트) | MLD |
| ICMP(오류/제어) | ICMPv6 Error/Informational Message |

## 암기 팁
- **"알사네나"**: RS(알려줘 라우터야)-RA(응 나여기)-NS(네주소알려줘)-NA(응 이거야) — RS→RA→NS→NA 순서
- **"ICMPv6 = ICMP+ARP+IGMP 3-in-1"**
- **Next Header 58 = ICMPv6 전용번호**

## 헷갈리는 포인트
- IPv6에는 **ARP가 존재하지 않음** — NS/NA가 그 역할을 대체 (ARP 프로토콜 자체가 IPv6엔 없다는 점이 함정)
- MLD(멀티캐스트)와 NDP(이웃탐색)는 둘 다 ICMPv6의 Informational Message에 속하지만 **역할이 완전히 다름** — MLD는 IGMP 대체, NDP는 ARP 대체
- Next Header 값(58)을 IPv4의 Protocol 필드(ICMP=1)와 헷갈리기 쉬움

## 관련 기출/문제
- ICMPv6가 통합한 IPv4 프로토콜 3가지(ICMP/ARP/IGMP)
- NDP 메시지 순서(RS→RA→NS→NA)와 Type 번호
- Next Header 값(58) 암기
- MLD vs NDP 구분

## 💬 내 코멘트
- 
