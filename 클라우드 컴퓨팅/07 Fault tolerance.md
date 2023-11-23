# 07. Fault tolerance

### 🧙‍♂️Failure

- non-distributed system: 오류가 모든 component들에게 영향을 미칠 수 있다. 그렇게 되면 시스템 전체가 안될 수 있다
- distributed system: 오류가 나더라도, 전반적인 성능에 심각한 오류가 안나도록 할 수 있어야 하고, 오류가 복귀되는 동안 계속 동작해야 한다 → fault tolerant: 고장나도 okay!

### Dependability: fault tolerant하다는 것은 dependable system과 연관이 있다

- availability: 가용성. 사용자가 서비스를 이용할 수 있는 시간의 비율. 보통 %로 표시되며, 전체 시간에서 얼만큼 이용할 수 있을까
- reliability: 안정성. 얼마마다 한번씩 오류가 날지
    - Mean Time To Failure(MMTF): component가 오류가 날 때까지의 평균적인 term, 얼만큼의 term 마다 오류가 나는지
- safety: 재앙적인 오류가 나지 않도록 하는 것. Catastrophic한 경우가 적어야 안정성이 높아진다
- maintainability: 오류가 나더라도 잘 복구할 수 있어야 한다. 유지 보수가 쉬워야 한다는 것

### 오류가 발생하는 순서: fault → error → failure

1. fault: error가 발생하는 원인. Packet error, noisy한 channel, wireless interference와 같은 것
2. error: failure를 유발할 수 있는 상태. 잎선 fault에 의해 잘못된 packet을 받는 것과 같은 경우
3. failure: 시스템이 더이상  서비스를 제공할 수 없는 상태
    - crash failure: 일시정지하는 것. 일시정지되기 전까지는 정상적으로 동작한다
    - omission failure: 새로 들어오는 요청을 응답할 수 없는 것
        - receive omission: 수신 오류. Server가 client의 요청에 응답하지 못하는 것
        - send omission: 송신 오류. Server가 요청을 받았지만, respond에 실패하는 것
    - timing failure: output은 정상적으로 나오지만, deadline안에 맞추지 못하는 것
    - response failure: server의 응답이 부정확하다
        - value failure: 값이 잘못 나온 것. 원하는 내용을 보내지 못한 것
        - state transmission failure: server는 예상하지 못한 요청을 받고, 실행 결과 부정확한 state가 된다
    - arbitrary failure: 임의의 순간에 임의의 failure가 발생하는 것. 매번 똑같은 오류가 나는 것이 아니기 때문에 오류가 나는 부분을 찾기 힘들다

### Redundancy: redundancy를 통해서 failure를 숨긴다

- information redundancy: Forward Error Correction과 같이, parity를 추가하여 해당 코드를 이용하여 packet error를 복구할 수 있다. 네트워크에서 많이 사용한다
- time redundancy: 재송신을 진행한다. 일시적이거나 간헐적인 오류에 효과적이다
- physical redundancy: 물리적으로 복제하는 것. 비용이 필요하다

### 🧙‍♂️Process resilience(회복력): process를 group으로 복제하여 process 장애로부터 보호한다

- process group: 여러가지 동일한 process를 분할
    - group에서의 process가 fail된다면, 다른 애들이 대신 해줄 수 있다
    - 그룹의 process는 동적이기 때문에 시스템 동작중에 참여하거나, 이탈할 수 있다
    - group내로 오는 모든 message를 모든 멤버들이 받을 수 있다
    - group은 외부에서 보면 하나의 process처럼 동작한다
    - flat group: fault tolerance에 좋다
        
        ![Untitled](07%20Fault%20tolerance/Untitled.png)
        
        - 관리하는 것이 복잡하기 때문에 overhead가 많이 발생할 수 있다
        - 분산 알고리즘
    - hierachical group: 하나의 coordinator가 관리
        
        ![Untitled](07%20Fault%20tolerance/Untitled%201.png)
        
        - coordinator가 죽으면 fault tolerant가 불가능
        - 중앙 집중형 알고리즘
- Process replication
    - primary-based protocol: 계층구조 형식으로 group이 조직되어 있다. Primary에 오류가 발생하면 새로운 primary를 정해진 방식에 의해 결정한다
    - quorum-based protocol: group은 동일한 process집합에 의해 그룹으로 구성된다. Single point failure가 아니다

### K-fault tolerant group: K개의 member가 동시에 fail되더라도 감출 수 있는 것

- crash failure semantics: k+1, 즉 한개만 돌아가면 괜찮다
- arbitrary failure semantics, group output defined by voting: fault가 난 애 k개, 그리고 failure를 가리기 위한 2k+1개, 총 3k+1개가 필요하다 → 왜? - Byzantine General’s problem

### Byzantine General’s problem: 일부에 배신자가 있더라도 어떻게 정보를 통일할까?? - 과반수 이하로 배신자가 있다면 okay

![Untitled](07%20Fault%20tolerance/Untitled.jpeg)

- general과 lieutenant가 각각 정상이거나 배신자가 될 수 있다
- general이 leiutenant에게 명령을 내리면, leiutenant들이 서로 명령을 확인하고, 과반이 넘는 명령으로 명령을 수행
- IC1: 모든 정상 general은 같은 결과를 출력한다
- IC2: commander가 정상이라면 나머지 general모두 명령어를 따라야 한다
- 짬찌중에서 배신자가 있다면: 배신자 짬찌가 명령어를 잘못 전달해서, 배신자가 아닌 짬찌에게 알려주고, 그렇게 되면 attack retreat이 과반수를 둘다 못넘기게 된다

![Untitled](07%20Fault%20tolerance/Untitled%202.png)

- commander가 배신자라면?: 명령을 두 짬찌에게 각각 반대로 전달하기 때문에 과반수 확보가 불가능하다

![Untitled](07%20Fault%20tolerance/Untitled%203.png)

- 한명의 배신자가 있을때(k = 1일), 위와 같이 3(3k)라면 제대로 수행이 불가능하다. 만약 여기서 한명의 배신가자 있을때, 3k+1, 즉 4명이 있다면 배신자가 있더라도 과반 이상의 정상 명령어가 있기 때문에, 문제 없이 수행할 수 있고, 배신자가 있다는 것도 한명이 다르게 말했기 때문에 파악할 수 있다

![Untitled](07%20Fault%20tolerance/Untitled%204.png)

- commander가 배신자라면?: 나머지 짬찌들이 모두 배신자가 아니더라도, 명령어를 다르게 받게 된다. 즉, commander가 잘못되면 해결 불가능 하다

![Untitled](07%20Fault%20tolerance/Untitled%205.png)

### Consensus under arbitrary failure semantics: 1개의 primary, n-1개의 backup process가 존재한다

- Byzantine agreeement(BA)
    - BA1: 오류가 없는 모든 백업 process는 동일한 값을 가지고 있어야 한다
    - BA2: primary에 결함이 없다면 모든 오류가 없는 backup process는 primary가 보낸 내용을 정확하게 저장한다
- Byzantine의 경우처럼, k개의 오류가 있다면, 오류가 없는 2k+1개가 존재해야 하기 때문에, k개의 오류에 대해서 총 3k+1개가 필요하다

### Failure detection

- failure를 detect하는 방법: 주기적으로 살아있는지 확인하거나(Polling), 수동적으로 message가 올때까지 기다린다
- timeout: 특정 시간까지 기다리고, timeout 넘었는데 안오면 fault로 인식하면 된다. 그러나 이 경우, 정상적인데 network문제때문에 늦게 도착할 경우, 정상인데 fault로 인식해버릴수도 있다
    - gossiping or active probing: 정기적으로 이웃과 정보를 주고 받아서 살아있는지 확인한다

### 🧙‍♂️Communication problem: process자체가 아니라, communication에 오류 발생할 수 있다

- error detection: checksum으로 일부 bit를 이용하여 정보가 맞는지 틀린지 확인한다. Packet에 순서를 부여하여 순서대로 제대로 오는지 확인한다
- error correction: redundancy를 이용하여 packet을 자동으로 복구시키거나, 재전송을 요청한다

### RPC: RPC에서 발생할 수 있는 문제

- client가 server의 위치를 찾을 수 없다: 서버가 다운되거나, 서버가 업그레이드 된 경우. Server가 client에게 다시 report하면 해결
- client request를 못받음: 재송신을 요청한다. 계속 오류 나는 경우, 그냥 포기한다
- server crashes: 정상적으로 메세지를 받았더라도 서버가 보내기 전에 내부에서 오류가 난다면 relpy를 보낼 수 없다
    
    ![Untitled](07%20Fault%20tolerance/Untitled%206.png)
    
    - 서버는 완료 ACK을 보내지만, client는 ACK만 받고 결과를 받지 못한다
    - 서버가 오류가 난 이후 복구 되었다면, server는 client에게 crash가 일어나서 복구 후 다시 running되고 있다고 알림 → client는 서버가 실제로 보냈는지 파악할 수 없다
- server의 response가 사라지는 경우: timeout을 통해 detect할 수 있는데, server에서 오류가 났는지, timeout이 되어서 못받은 것인지 파악할 수 없기 때문에 server가 실제로 작업을 수행했는지 파악할 수 없다
    - idempotent: 같은 연산을 여러번 하더라도 바뀌지 않도록 해야한다. 각 request를 server가 구분할 수 있도록 해줘야 한다
- client에서 crash가 나는 경우: server는 일을 하고 있는데, client는 crash되었기 때문에 자원을 낭비하고 있다. 이를 orphan computation이라고 한다
    - client가 오류 해결 이후 message를 보내고, server는 orphan들을 제거한다
    - 특정 기간 동안만 연산을 수행하도록 하고, 이후에는 버려버린다
    - oprphan을 제거할때 발생할 수 있는 문제점: 쓰던 도중에 file을 lock해놨는데 갑자기 제거되면 lock이 평생 유지될 수 있다

---

### 🧙‍♂️Reliable group communication

### Transmission의 종류

![Untitled](07%20Fault%20tolerance/Untitled%207.png)

- unicast transmission: point-to-point, 즉 1대 1연결
- broadcast transmission: 1대 다 연결
- multicast transmission: 1대 그룹 연결

### Reliable multicast: message가 전달되는 과정에서 failure만 고려한다

- reliable unicast: transport layer는 안정적인 point-to-point연결이 되도록 보장해준다
- reliable multicast: 메세지가 group에 있는 모든 멤버에게 전달될 수 있도록 해준다
- receiver의 feedback
    - ACK: 잘 받았다고 알리는 것
    - NACK: 못받아서 재송신을 요구하는 것

![Untitled](07%20Fault%20tolerance/Untitled%208.png)

- sender는 그동안 보냈던 메시지를 저장하는 history buffer를 가지고, sequence number를 messsage에 줘서 수신자가 혹시 받지 못한 message가 있는지 확인할 수 있도록 해준다
- 만약 3번째 receiver처럼 메세지를 받지 못했다면, NACK을 보내게 된다
- Reliable multicasting scalability
    
    ![Untitled](07%20Fault%20tolerance/Untitled%209.png)
    
    - ACK을 사용하는 경우: sender가 N개에게 보내는 경우, ACK이 N개 오기 때문에 너무 많아져서 복잡해진다
    - NACK을 사용하는 경우: sender가 보냈을때, 정상적으로 받은 경우 말고, 잘 못받은 경우에만 NACK을 보낸다. ACK보다는 scalability측면에서 더 유리하다
        - NACK이 언제올지 모르기 때문에, history buffer에 언제까지 저장하고 있을지 애매하다
        - 결국 혼잡도등 문제로 대규모 재전송이 필요한 경우, NACK을 많이 보내야하기 때문에 같은 문제가 발생한다

### Scalable Reliable Multicasting(SRM)

![Untitled](07%20Fault%20tolerance/Untitled%2010.png)

- NACK을 사용하는데, 수신자는 random하게 기다렸다가 multicast한다
- 이 경우, 여러개의 failure가 나더라도 NACK은 하나만 보내면 된
- 성공적으로 받은 수신자도 방해받을 수 있다

### Hierarchical Feedback Control

![Untitled](07%20Fault%20tolerance/Untitled%2011.png)

- group은 여러개의 subgroup으로 나누어지고, tree구조로 조직된다
- 메세지를 보낼때, local에 있는 local coordinator를 통해 보내게 된다
    - G에 있는 S가 메세지를 보내고 싶을때, S는 G에 도달하기 위해서 coordinator C를 포함한 그룹 맴버 모두에게 reliable multicast를 활용하여 전파한다
    - C는 tree형태로 되어있는 모든 neighbor에게 메세지를 forward한다
    - 다른 coordinator들은 계속 자신이 메세지를 받은 coordinator를 제외한 이웃하고 있는 coordinator모두에게 메세지를 보낸다
    - coordinator는 자신의 group에 있는 모든 member들 에게도 multicast한다
- ACK기반: coordinator C가 coordinator C’에게 메세지를 보내는 경우, C’가 ACK을 보낼때까지 buffer에 가지고 대기하고 있는다
- NACK기반: C가 C’에게 메세지를 보내는 경우, C’가 메세지를 받지 못한 경우에만 NACK을 보낸다
- coordinator의 ACK, NACK 메세지는 다른 process부터의 많은 control message들을 병합하게 된다 - 더 나은 scalability를 제공한다

### 메시지 송신과 수신의 차이점: 메세지를 수신할때는 reception과 delivery에 level의 차이가 존재한다

![Untitled](07%20Fault%20tolerance/Untitled%2012.png)

### 🧙‍♂️Process failure: process가 fail되었을때, 어떻게 대처해야 할까?

### Virtually synchronous: 메세지가 오류가 없는 모든 프로세스에 전달 되거나, 아니면 아예 다 못받거나 두가지 경우만 되야 한다

- 메세지는 오류가 나지 않은 모든 프로세스에 전달 되거나, 나머지 모두가 무시하거나
- 그룹 관점: 메세지가 보내졌을때, 각 프로세스들은 multicast가 끝날때까지 하나처럼 동작하고 있어야 한다
    - view change: process가 새로 참여하거나 이탈하는 경우와 같은 view change는 multicast동안 발생하면 안된다. View change는 다른 multicast가 통과할 수 없는 장벽으로 작용한다

### Message ordering: virtually synchronous와는 독립적으로, 메세지는 group끼리 계약된 내용에 따라 순서를 지켜야 한다

- FIFO-ordered multicasting: 같은 프로세스 메세지는 순서대로, 다른 프로세스 메세지는 상관없음
    
    ![Untitled](07%20Fault%20tolerance/Untitled%2013.png)
    
    - 같은 색이면 같은 프로세스라고 가정할때, 파란 프로세스가 P1에서는 m3, m4, m5의 순서를 가지는데, P2에서는 m4, m3, m5의 순서를 가지기 때문에 ordering이 맞지 않다
- casually-ordered multicasts: m1→m2가 casually related라면, 반드시 그 순서를 지켜야 한다
    
    ![Untitled](07%20Fault%20tolerance/Untitled%2014.png)
    
    - m1 → m4, m5 → m8이 casually related된 경우라면, 반드시 모든 프로세스에서 두 message의 선후관계가 지켜져야 한다
    - 선후관계를 서로 다른 프로세스에서 확인하는 것은 어렵다
- totally-ordered multicasts: 실제 보낸 순서와는 상관 없이, 모두 동일한 순서로 왔다면 okay
    
    ![Untitled](07%20Fault%20tolerance/Untitled%2015.png)
    
    - 다른 메세지는 모두 순서가 동일하기 때문에 괜찮지만, p1과 p2의 m1, m2순서, p2와 p3의 m0, m8의 순서가 맞지 않기 때문에 ordering이 맞지 않다
    

| multicast | basic message ordering |
| --- | --- |
| reliable multicast | none |
| FIFO multicast | FIFO-ordered delivery |
| cascaul multicast | casual-ordered delivery |
| atomic multicast | TO-ordered |

### 🧙‍♂️Distributed commit: 각 local transaction들이 모두 수행되거나, 아니면 아무도 안하거나 둘 중 하나여야 한다

### Two-phase commit(2PC): node가 모두 준비된 경우에만 COMMIT, 그 외의 경우에는 ABORT

![Untitled](07%20Fault%20tolerance/Untitled%2016.png)

- 2PC 알고리즘: coordiantor는 worker에게 commit할 수 있는지 물어보고, 모든 worker가 VOTE-COMMIT이라고 하면, coordinator는 GLOBAL-COMMIT 메세지를 보내고, 그 외의 경우에는 coordinator는 GLOBAL-ABORT라고 보낸다. Worker는 GLOBAL 메세지에 복종한다

![Untitled](07%20Fault%20tolerance/Untitled%2017.png)

- 모든 참여자는 실제 commit하기 전에, coordinator에게 feedback을 보내야 한다
- worker에 failure가 난 경우: GLOBAL-ABORT
    - failure는 node가 message를 기다리고 있는 상태일때 오류가 난다
    - coordinator가 wait인 상태일때 failure가 나는 경우, timeout이 날때까지 COMMIT을 받지 못하는 경우, GLOBAL-ABORT 해준다
        
        ![Untitled](07%20Fault%20tolerance/Untitled%2018.png)
        
- coordinator가 오류난 경우
    - INIT상태: coordinator가 요청을 보낼때, 보내지 못하고 오류가 난 경우, timeout까지 기다린 이후에 ABORT 메세지를 coordinator에게 보낸다
        
        ![Untitled](07%20Fault%20tolerance/Untitled%2019.png)
        
    - READY상태: VOTE-COMMIT을 보낸 이후에 오류가 난 경우, worker들은 이미 commit준비를 한 상태이기 때문에 block해놓고 기다리다가 coordinator가 restart되면, 명령어를 받고 종료시킨다
        
        ![Untitled](07%20Fault%20tolerance/Untitled%2020.png)
        
        - worker가 READY한 상태라면 다른 프로세스에게 물어봐야 한다
            - 이 상황은 worker들은 VOTE_REQUEST를 coordinator로 부터 받고 응답을 보냈지만 coordinator가 fail된 것이다
            - worker는  coordinator의 마지막 명령어를 받지 못한 것이다
            - coordinator가 오류를 수정하고 정상으로 돌아올때까지 block하고 기다려야 한다
    - coordinator오류에 대한 대처
        
        
        | worker의 상태 | 진행 방향 |
        | --- | --- |
        | COMMIT | COMMIT한다 |
        | ABORT | ABORT한다 |
        | INIT | ABORT한다 |
        | READY | 다른 worker에 확인한다 |

### Three-Phase Commit(3PC): COMMIT 들어가기 전에 상태 하나를 더 추가한다

![Untitled](07%20Fault%20tolerance/Untitled%2021.png)

![Untitled](07%20Fault%20tolerance/Untitled%2022.png)

- worker가 오류나는 경우
    - WAIT상태: GLOBAL-ABORT
        - coordinator는 WAIT상태로 worker로부터 응답 오기를 기다린다
        - WAIT에서, timeout으로 VOTE를 충분히 받지 못하는 경우에는, GLOBAL-ABORT시킨다
            
            ![Untitled](07%20Fault%20tolerance/Untitled%2023.png)
            
    - PRECOMMIT상태: GLOBAL-COMMIT
        - worker 모두 VOTE-COMMIT을 보내거나, READY-COMMIT을 보낸 상태이다
        - 하나의 worker가 crash난 경우일수 있지만, 다른 worker들은 COMMIT을 보낸 상황이다. 따라서 일단 GLOBAL-COMMIT을 보내고, crash난 worker는 나중에 복구된 이후에 실행하게 된다
            
            ![Untitled](07%20Fault%20tolerance/Untitled%2024.png)
            
- coordinator가 오류나는 경우
    - INIT상태: timeout까지 기다렸다가 ABORT한다
    - READY상태: timeout까지 기다리고, 이후에 Q라는 다른 participant에게 상태를 물어본다. Q가 precommit인 경우에만 COMMIT
        - Q가 INIT인 경우: 다른 worker모두 PRECOMMIT이 아니라는 것이기 때문에 ABORT
        - Q가 ABORT인 경우: ABORT
        - Q가 READY인 경우: 다른 모든 worker가 READY이고, COMMIT상태라는 것이 아니기 때문에 ABORT
        - Q가 PRECOMMIT인 경우: 다른 worker들이 모두 INIT이나 ABORT가 아니기 때문에, 최소한 READY상태이기 때문에 COMMIT
    - PRECOMMIT상태: worker들은 timeout이후에, coordinator가 오류가 났다고 판단하고, 다른 worker들이 모두 최소 READY상태라는 것이기 때문에 COMMIT한다
- coordinator가 오류난 이후라면 새로운 coordinator를 뽑는다. 이때 새로운 coordinator는 process의 상태를 파악한다
    - 하나라도 ABORT된 경우에는 ABORT해준다
    - 하나라도 COMMIT된 경우에는 COMMIT해준다
    - 모두 READY인 경우에는 ABORT해준다
    - 하나라도 PRE-COMMIT인 경우, PRE-COMMIT을 보내고, PRE-COMMIT상태로 바꾼다
- block은 잘 일어나지 않지만, 일어난다면 무겁다