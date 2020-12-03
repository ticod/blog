---
title: TCP와 UDP
date: "2020-12-03T09:46:00.000Z"
description: "TCP, UDP 정리"
---

## TCP

---

Transmission Control Protocol, 전송 제어 프로토콜
OSI 4계층(transport)에 해당된다. 안정성, 신뢰성을 보장한다.

### 특징

1. **연결 지향 프로토콜**

    데이터 전송 전, 3-way Handshake를 통해 연결을 설정한다. 연결 종료시에는 4-way Handshake를 통해 종료한다.
    Loosly Connected를 가진다고 하고, 강한 연결을 의미하는 '가상회선'이라는 표현보다는 '연결지향적'이라는 표현 사용한다.

    소켓을 통해 연결에서 회선을 식별한다.

2. **신뢰성 보장**

    패킷 손실, 중복, 순서 변경 등이 일어나지 않도록 보장한다.
    
3. **안정적인 데이터 전송**

    네트워크 상에서 한 번에 보낼 수 있는 데이터의 양은 제한되어있다. 따라서 분할해서 보내야 하는데, 분할한 데이터에는 고유 번호(sequence numbers)를 부여하여, 데이터의 순서를 식별할 수 있다.
    또한, 데이터가 제대로 전송되지 않았다면 다시 전송하는 것이 가능하다. 데이터 수신시 수신측에서 ACK를 보냄으로, 수신이 됐는지 확인할 수 있다. 일정 시간이 지나도 ACK가 오지 않으면(timeout) 데이터를 재전송한다. 이를 위해서 송신측에서는 수신측으로부터 ACK를 받지 않은 데이터를 보관한다. (buffer unacknowledged data)

4. **Flow Control(흐름제어)**

    송신측과 수신측의 데이터 처리 속도 차이를 해결하기 위한 기법으로, 수신자는 받을 수 있는 데이터 크기(바이트 수)를 송신자에게 전달하고, 송신자는 수신자가 허용하는 바이트 수 만큼 데이터를 전송한다.

    

    ### 기법

    1. Stop-and-wait

        데이터를 1개 보내고, ACK가 오면 그 다음 데이터를 보낸다. => 속도가 느리고, 비효율적이다.

        데이터를 수신 했지만 ACK가 유실된다면, 설정된 Time out 이후 데이터를 재전송하게 된다.  이러면 데이터의 중복이 발생하고, 중복된 데이터를 삭제 후 수신측에서 ACK를 재전송하게된다.

    2. Sliding Window

        수신측에서 window 크기를 설정한다.

        - window는 송수신간의 버퍼를 이야기한다.

        송신측에서 window에 데이터를 담고, 수신측에 전송한다. 수신측에서 ACK가 오면 ACK가 온 수 만큼 window의 경계를 아직 전송하지 않은 데이터로 넓힌다.

        ![Sliding Window](.\SlidingWindow.png)

5. **Congestion Control(혼잡 제어)**

    초기 TCP에서는 없던 요소로, 흐름제어와 마찬가지로 송신측과 수신측의 데이터 처리 속도 차이를 해결하기 위한 기법. 흐름제어와 다른 점은 한정된 네트워크 대역폭에 사용자가 많아진다면 네트워크 회선이 부하를 감당하지 못하고, 이 때 송신 속도를 감속한다. 예시로, 중간 라우터에 데이터가 몰리게 되고, 송신하는 속도가 수신하는 속도보다 느리면 계속 데이터가 쌓이게 된다. 이는 오버플로우, 데이터 손실을 발생시킨다. 이를 해결하기 위해 송신측에서 전송속도를 강제로 줄여야 하는데, 이를 혼잡 제어라고 한다.

    (깨진 독에 물을 붓는데, 차오른다. 이를 방지하기 위해, 물을 붓는 속도를 줄인다.)

    

    ### 기법

    1. AIME (합 증가 / 곱 감소)

        데이터를 전송하면서 문제없이 도착하면 점차 속도를 높인다. (윈도우의 크기를 1씩 증가시킨다) 만약 전송하다가 패킷 전송에 실패하거나, timeout이 발생하면 속도를 절반으로 줄인다.

        장점으로는 네트워크 대역폭을 공평하게 사용한다는 점이 있고, 단점으로는 전송 속도가 올라가는데 오래걸리고, 사후에 속도가 감소되기 때문에 미리 감지할 수 없다는 단점이 있다.

    2. Slow start (느린 시작)

        AIME의 단점을 보완하는 방법이다. 데이터를 전송 후 도착시 ACK 개수만큼 window의 크기를 늘린다. 즉, 한 번의 주기가 끝나면 window의 크기가 두 배가 된다. 전송 중 문제가 생긴다면 window의 크기를 1로 줄여버린다. 이후, 문제가 발생한 window 크기의 절반까지는 이전과 동일하게 두 배씩 window의 크기가 커지게 되고, 그 이후로는 완만하게 증가시킨다. (1씩 증가시킨다) => 지수적으로 증가시킨다.

    3. Congestion Avoidance (혼잡 회피)

        문제 감지시 지수 방식이 아닌 가산 증가 방식을 채택

    4. Fast Retransmit (빠른 재전송)

        재전송 큐 과정 중 중간 누락된 세그먼트는 빠르게 재전송하는 방식

    5. Fast Recovery (빠른 회복)

        문제 발생시 window의 크기를 1이 아닌 절반으로 줄인 이후, AIME 방식으로 1씩 증가시킨다.

6. **Full-Duplex(전이중 방식)**

    전송이 양방향으로 동시에 일어날 수 있다.

7. **Point to Point 방식**

    유니캐스트만 가능하다. (멀티캐스트, 브로드캐스트를 지원하지 않는다)

8. **real time system에 적합하지 않다.**

    TCP는 전달에 따른 지연에 취약하다. 실시간 통신에는 주로 UDP를 사용한다. 즉, 게임같은 부분에서는 UDP를 많이 사용한다.

### 데이터 전송 단위: Segment

- TCP에서 사용되는 데이터 단위 (TCP 패킷이라는 단어를 사용하기도 하는데, 패킷은 Network 계층(3계층)에서 사용되는 데이터 단위)
- TCP 헤더를 포함해서 말하는 경우도, 포함하지 않고 말하는 경우도 있음.
- TCP 헤더는 20 ~ 60byte로 구성됨
- TCP 헤더 구조

![TCP Header](.\TCPHeader.png)

- 구조 설명
  - Source Port : 송신 측 Port
  - Destination Port : 수신 측 Port
  - Sequence Number : 데이터의 순서 식별 (최대값 이후에는 0으로 다시 시작)
  - Header Length : 헤더의 길이 표시
  - URG, ACK, ... (Flag Bits) : 
  - Window Size : 윈도우 크기 표시, 수신 측에서 송신 측으로 버퍼 여유
  - Checksum : 체크섬, 데이터의 오류 검사
  - Urgent Pointer : 긴급 데이터일 수 있는 현재 데이터의  마지막 위치, 현재 데이터와의 offset
  - Option : MSS (Maximum Segment Size), Window Scaling (윈도우 크기 조정) 등 다양한 TCP 옵션



## UDP

-----

User Datagram Protocol, 사용자 데이터그램 프로토콜
OSI 4계층(transport)에 해당된다.

### 특징

1. 비연결형 프로토콜 (no handshaking)
2. 신뢰성이 낮다. 이유는 아래의 특징들에서 나타난다.
3. 데이터의 순서를 신경쓰지 않는다.
4. ACK가 없고, 메세지가 도착했는지 확인하지 않는다.
5. 흐름제어가 없다. 즉, 일정한 속도로 그냥 계속 보내게 된다.
6. 헤더에 있는 checksum 이외에 특별한 오류 검출, 제어가 없다.
7. TCP에 존재하는 많은 기능들이 빠지기 때문에, 훨씬 적은 네트워크 리소스를 사용한다. 이는, 빠른 요청과 응답이 필요한 realtime에 적합하다.
8. 멀티 캐스팅이 가능하다.
9. 전송 속도에 제한이 없다.

### 데이터 전송 단위: Datagram(Message)

- 8바이트만 사용

![UDP Header](.\udp-1606970296668.png)

- Source/Destination Port : 송신/수신 측 Port
- Length : 패킷의 길이
- Checksum : 체크섬, 데이터의 오류 검사

### 왜 사용되는가?

언뜻 보면 단점밖에 없지만 사용되는 이유는 간단하다. 데이터의 크기가 작고, 데이터가 확실하게 도착하지 않았다고 다시 데이터를 보낼 필요가 없는 상황에 쓰는 것이다. 예시로, 동영상 스트리밍이 있다. TCP라면 픽셀 하나가 잘못 보내졌다고, 그 큰 데이터를 다시보내야 하는 상황이 생긴다. 또한 안정적으로 데이터를 보내기 위해 속도 제어까지 하게 된다. UDP라면? 픽셀 하나가 잘못보내져도 신경쓰지않고, 다음 데이터를 전송하면 되는 것이다. 오히려 이런 상황에서는 UDP가 더 안정적일 수도 있겠다고 생각했다.

- 데이터가 손실되도 괜찮은 상황 (ex. VoIP)
- 데이터가 손실됐을 때 복구를 따로 구현해놓은 상황 (ex. NFS)



## 차이점 정리

-----

![TCP UDP DIFF](.\tcpudp diff.png)



### 정리 후 느낀점

---

정리하다보니 TCP 위주로 정리하는 느낌이 되었다. UDP는 TCP에 있는 다양한 기능들이 빠져있는 느낌이 들었다. OSI 7계층에 대해서도 정리해 보면 흐름이 더 눈에 보일 것 같다. 막연하게 TCP를 구현해보고 싶다는 생각이 있었는데, 검색해보면서 굉장히 복잡하게 기능들이 얽혀있구나 하는 것을 느꼈다. 그리고 프로토콜에 대해 조사하는게 생각보다 재밌었고, 다른 관련된 프로토콜에 대한 정보도 알게 되어서 자바로 한번 구현해 보고 싶다는 생각이 들었다.



### 출처

---

[http://www.ktword.co.kr/abbr_view.php?m_temp1=347&id=428](http://www.ktword.co.kr/abbr_view.php?m_temp1=347&id=428)

http://www.ktword.co.kr/abbr_view.php?nav=&m_temp1=1889&id=1103

http://www.ktword.co.kr/abbr_view.php?m_temp1=323

[https://geek-university.com/ccna/transmission-control-protocol-tcp-explained/](https://geek-university.com/ccna/transmission-control-protocol-tcp-explained/)

https://d2.naver.com/helloworld/47667

https://ooeunz.tistory.com/91

https://geek-university.com/ccna/transmission-control-protocol-tcp-explained/

https://geek-university.com/ccna/user-datagram-protocol-udp-explained/

TCP와 UDP 관련 흥미로운 토론? : https://qastack.kr/gamedev/431/is-the-tcp-protocol-good-enough-for-real-time-multiplayer-games