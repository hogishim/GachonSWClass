# 04. Network architecture

### IP주소 체계

- IPv4: 32bit의 unique한 address. 다른 장치에서 같은 IP주소를 가질 수 없다 - NAT을 사용하는 경우, local IP주소가 할당되기 때문에, 다른 NAT에서 제공하는 IP와 겹칠 수 있다
- IPv6: 아직 많이 사용되지는 않지만, 128bit 주소체계. 2 byte씩 끊어서 2 * 8 = 16 byte

### 🌟Network congestion: network가 너무 busy하다면, router queue가 overflow되어 packet drop이 발생할 수 있다

- 공유하는 자원: link bandwidth(대역폭), switch와 router에 있는 buffer
- router: 서로 다른 네트워크로 보내는 경우에 이용되며 header의 내용을 확인하여 routing통해서 목적지를 보고 내보낸다
- switch: local network에서 사용하는 것으로 MAC주소를 확인하여 해당 MAC주소를 가지는 곳으로 보내준다
- hub: 들어오는것은 하나지만, 내보내는 것을 구분하지 않고, 전체로 broadcasting한다
- capacity보다 계속해서 더 많이 보내는 경우, router에 queueing하는 packet이 많아지고, queuing delay가 늘어나면서 buffer overflow가 발생하여 packet loss가 발생한다
- congestion control: 네트워크 내에서 데이터 전송량이 많아 loss가 발생하는 경우, sender의 속도를 조절하여 loss가 발생하지 않도록 하는 것
- ACK을 확인하여, 왕복시간을 보고 혼잡도를 파악할 수 있다
- flow control: 데이터의 송, 수신자 간에 차이가 발생하는 경우, 이 속도차이를 조절하는 과정이다

### TCP window: window 크기를 조절하여 데이터 전송 속도를 조절

![Untitled](04%20Network%20architecture/Untitled.png)

- packet loss는 congestion 발생의 척도로, congestion window(cwnd)를 이용하여 congestion control을 진행한다
- reciever는 advertised window(awnd)를 이용하여 flow control에 이용한다. ACK을 보내는 경우, awnd의 값을 같이 넣어주어 데이터를 전송할떄, receiver의 window 상황을 고려하여 보내게 된다
- sender는 cwnd, awnd의 최소값을 고려해가며 전송속도를 조절한다

### TCP congestion control: 처음에는 느리게 보내고, 시간이 지날수록 보내는 속도를 급격하게 늘린다

![Untitled](04%20Network%20architecture/Untitled%201.png)

- 처음 시작은 낮은 속도로 보내고, exponential하게 속도를 증가 시킨다
- ssthresh값에 도달하는 경우, 이후로는 linear하게 증가시킨다
- ACK이 3번 drop되는 경우가 생기면, drop 시키고 처음부터 다시 시작하게 된다
- TCP Reno는 TCP fast recovery를 이용하여 ssthresh값까지 조절할 수 있다. ACK이 3번 연속으로 loss가 발생하면, ssthresh값을 줄인 후, ssthresh값부터 다시 linear하게 증가시키면서 보낸다
- AIMD: additive increase + multiplicative decrease. 오류가 없다면 1씩 증가시키고, 오류가 생기면 반으로 줄이는 것
    
    ![Untitled](04%20Network%20architecture/Untitled%202.png)
    

### 🌟Multipath TCP(MPTCP): 하나의 TCP 연결에서 여러 경로를 사용하여 데이터를 전송하게 해주는 protocol

- throughput을 향상시켜줄 수 있고, error에 대한 회복이 더 향상된다
- apple siri: multipath TCP 기반으로, 여러 경로를 사용하여 효율적으로 데이터를 전송하고, 음성 명령을 인식하는 데 필요한 데이터를 전송한다. MPTCP를 통해서 빠른 데이터 전송과 대역폭 활용을 가능하게 하고, 이동중에 Wi-Fi와 celluar 네트워크의 전환이 자연스럽고 끊김 없이 진행될 수 있게 해준다
- KT 기가 LTE 서비스: interface가 여러개. 여러 네트워크를 동시에 사용할 수 있게 한다
- multihoming: 각기 다른 IP를 가지고, 다른 네트워크를 이용할 수 있다

<aside>
☝ 장점: 기존 app에서 조금만 수정하면 사용할 수 있으며, 기존에 존재하는 network system을 그대로 이용할 수 있다

</aside>

### MPTCP operation

- 여러개의 네트워크를 대상으로 subflow를 만들어 여러 path로 이용할 수 있다
- application과 TCP 사이에 존재하여, HTTP에게는 정상적인 TCP로 보일 수 있지만, 여러개의 TCP subflow를 관리하고 있다
    
    ![Untitled](04%20Network%20architecture/Untitled%203.png)
    
- MPTCP handshake: first subflow는 TCP의 3-way handshake와 유사하게 진행되고, second subflow는 first의 연결이 맺어진 이후, 3-way handshake를 통해서 연결된다
    
    ![Untitled](04%20Network%20architecture/Untitled%204.png)
    
- Multipath TCP backup mode: 안정성을 위해 주로 사용한다. 휴대폰의 경우, WI-Fi를 main으로 사용하고 있더라도, backup connection이 LTE를 통해 진행되고 있다가, Wi-Fi가 사용 불가능 상황이라면 LTE를 이용하게 된다. 단, backup이 되어있더라도 실제 이용되고 있는 subflow는 한가지 뿐이다. 안정성을 유지하면서도 더 적은 에너지를 사용한다
- Multipath TCP full mode: 가능한 모든 multipath자원을 전송에 이용한다. 전송 속도가 향상된다

### 🌟Type of communication

- persistent communication: TCP와 비슷하게, 계속 저장되어 있는 상태
    
    ![Untitled](04%20Network%20architecture/Untitled%205.png)
    
    - 전송되는 메세지는 수신자에게 전달 완료까지 communication middleware에 저장된다
    - 수신받는 app은 메세지가 제출되었을때, 실행중일 필요는 없다
    - 메세지를 전달 받지 못하는 경우는 없다
- transient communication: 일시적인 communication. UDP와 비슷하다
    
    ![Untitled](04%20Network%20architecture/Untitled%206.png)
    
    - 메세지가 보내지는 것은 반드시 송, 수신자의 app이 모두 실행중인 경우에만 가능하다
    - 수신자의 app이 실행중이지 않은 경우, message는 drop된다. 따라서 메세지를 받지 못하는 경우가 생길 수 있다
    - 거의 모든 transport-layer단의 communication이 해당한다
- asynchronous communication: sender는 메세지를 보낸 직후, waiting과정 없이 다른 작업을 이어서 한다
- synchronous communication: sender는 reply를 받을때까지 block되어 waiting하고 있다

<aside>
👎 synchronous communication의 단점: block된 동안 다른 일을 수행할 수 없기 때문에 performance가 하락한다, 오류가 발생하면 아무것도 할 수 없기 때문에 빠르게 복구해야 한다, synchronous가 필요한 경우가 많지 않다

</aside>

- 대부분의 경우, client-server구조에서는 transient synchronous communication을 사용한다
    - client-server는 통신할때 모두 동작중인 상태여야 한다
    - client는 server로 부터 모든 응답을 받을때까지 block하고 있다
    - server는 들어오는 모든 요청에 대해서 기다리고 있고, 이후에 작업을 진행한다

### Distributed system에서의 통신 방법

- 서로 다른 기계들 사이에서 명시적인 message교환: procedure를 주고 받는데, programmer에게 communication을 숨기지 않기 때문에 transparency를 달성하지 못할 수 있다
- 프로그램이 procedure를 call할 수 있게 하자 → remote procedure call

### 🌟Remote Procedure Call(RPC): 분산시스템에서 procedure호출을 위해 사용되는 call로, 원격 서버에 있는 procedure를 call하는 것이다

![Untitled](04%20Network%20architecture/Untitled%207.png)

- 다른 방식은 복잡하고 에러 복구, 데이터 보호가 필요하다
- 어플리케이션 특정된 더 높은 수준의 추상화를 시켜서 문제를 해결한다
- procedure가 다른 machine에서 호출 되었다고 인식되면 안된다
- marshalling: message형식으로 함수이름 + parameter를 packing하는 것
- unmarhsalling: 전달받은 message를 unpacking하는 것
- 여러 시스템을 이어주기 때문에 서로 parameter type같은 내용들이 호환이 안될 수 있다
- 참조 변수가 parameter로 들어가는 경우, 서버는 client가 보낸 identifier를 이용하여 주소가 client와 다르더라도 찾을 수 있다
- RPC를 양 side는 같은 protocol을 사용해야 하고, 자료 구조 및 통신 유형을 통일시켜야 한다

### Endian: 데이터가 어떻게 memory에서 share될지 결정해준다

- big-endian: 값을 끊어서 저장할때, 앞부분부터 저장하는 것으로, 앞의 내용을 더 낮은 주소에 저장하게 된다. Header의 경우 앞에 저장이 되어있기 때문에 header정보가 중요한 네트워크 같은 경우에 이용한다
- little-endian: 값을 끊어서 저장할때, 뒷 부분부터 저장하는 것으로, 뒤의 내용을 더 낮은 주소에 저장하게 된다. 연산의 경우 뒤에서부터 진행하기 때문에 연산이 많은 경우 little-endian을 사용한다
- intel은 little endian을 사용하고, motorola, network에서는 big endian을 사용한다

<aside>
☝ 장점: 개발자들은 procedure model에 익숙하다. RPC를 local에서 호출하는 것처럼 보이게 할 수 있다. Transparency를 지킬 수 있다

</aside>

### RPC의 동작 과정

![Untitled](04%20Network%20architecture/Untitled%208.png)

1. client stub의 동작
    - client stub: local procedure call을 네트워크를 건너서 수행되도록 하는 request로 바꾸어 주는 코드. 원격 호출을 local 호출로 proxying해준다
    - 이전과 동일하게 local OS를 호출하지만, client stub는 이전과 다르게 parameter를 packing한다
    - send를 위한 call을 따르면서, client stub는 recieve를 call하고, reply가 올때까지 block하고 있다
2. server stub의 동작
    - server stub: server stub은 네트워크로 부터 온 request를 local procedure call로 바꾸어 주는 코드로, 실제 server를 호출하게 된다
    - receive: block을 시키고 들어오는 message를 기다릴때 이용된다
    - parameter를 unpack하고, 정상적인 server procedure처럼 진행하기 때문에 server는 remote에서 온 요청인지 모른다
    - 서버는 작업을 수행한 이후 결과를 return해준다
    - server stub은 result를 message에 넣어주고, client에게 send해준다
3. client server로 comeback
    - client OS는 message가 client process로 온 것을 확인한다
    - client process는 receive를 위한 block을 unblock한다
    - client stub은 message를 unpack하고 result를 unpack하고 return을 다른 procedure과 마찬가지로 caller에게 전달한다