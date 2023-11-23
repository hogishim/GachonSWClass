# 05. Synchronization

### Clock synchronization: 여러 시스템이나 장치들이 동일한 시간을 가지도록 하는 것이 목표

- 중앙 집중식 방식: 시간의 개념이 명확하다. Process가 시간을 요청한다면, system call을 하고, kernel이 시간을 알려주게 된다
- 분산 시스템: 분산 시스템에서 시간 동기화 하는 것 중요하지만, 쉽지 않다

### 🧙‍♂️Physical clocks: 석영의 초당 발생하는 진동수를 파악하여 1초를 정의

- clock stew: 네트워크에서 서로 다른 기기에서의 시간은 약간씩 오차가 생길 수 있다
- 어떻게 다른 기기들과 synchronize할 수 있을까, 어떻게 실제 clock과 synchronize할 수 있을까

### Universal Coordinated Time(UTC): 세슘 주기수 이용하여 지구상에서 모두 동일한 시간을 가지도록 함. 표준화된 시간으로 지구를 24개의 시간대로 나눔

- 인공위성은 정확한 시간을 가지도록 하는 것이 중요하다. 따라서 인공위성은 원자시계를 사용한다 - 원자시계는 delay가 없다
- UTC는 인공위성을 통해서 전파된다

### Global Positioning System(GPS): 위성 기반 시스템. 각 위성은 3개의 원자시계를 가지고 있다

- Time-of-Arrival(ToA): signal 속도 c(빛의 시간), 위성에서 신호를 보낸 송신 시간 Ti, GPS 수신기에서 수신 받은 시간 Tnow를 이용하여 인공위성까지의 거리 di를 계산할 수 있다
    
    ![Untitled](05%20Synchronization/Untitled.png)
    
- trilateration(삼각 측량): 수신자에게 signal이 오기까지 약간의 delay가 생기기 때문에 송신자와 수신자간의 sync가 안 맞을 수 있기 때문에 오차를 조정하는 과정이 필요하다
    
    ![Untitled](05%20Synchronization/Untitled%201.png)
    
    - ti: 위성 i로부터 메세지를 받은 시간
    - Δr: 수신자의 clock에서 발생하는 오차
    
    ![Untitled](05%20Synchronization/Untitled%202.png)
    
    - 실제 거리는 오차를 뺴주는 과정이 필요하다
    - xr, yr은 수신자의 x, y좌표 값이다
    - 위성을 1개 더 이용하게 되는 경우. 1개의 추가적인 방정식이 필요하다 - 그러나 clock stew극복

### Network Time Protocol(NTP): 서버는 정확한 시간을 가지고 있지만, 각노드는, server로 부터 요청을 받아도 propagation delay때문에 sync가 안 맞을 수 있다

![Untitled](05%20Synchronization/Untitled%203.png)

- client는 전송한 시간 T1, server가 전달받은 시간 T2, 서버가 다시 보낸 T3, 서버로 부터 받은 T4를 알고 있다
- A가 B에 비해 θ만큼 더 느린 경우. B의 시간 = A의 시간 + θ 이다
- Propagation delay가 server → client, client → server가 같다고 가정하였을때, 다음과 같이 계산될 수 있다
    
    ![Untitled](05%20Synchronization/Untitled%204.png)
    
- 따라서, θ는 다음과 같이 계산할 수 있다
    
    ![Untitled](05%20Synchronization/Untitled%205.png)
    

### 🧙‍♂️Logical clocks: 정확한 시간 파악할 필요 없고, 선후 관계만 파악하자

- linux에서는 소스파일을 수정하는 경우, object파일을 만들게 되는데, 여러개의 object파일중 만든 시간을 보고 compile이 필요한지 아닌지 파악한다. 그러나 분산 시스템에서 각 시스템에서 만들어진 파일의 선후관계 파악은 쉽지 않다
- physical clock의 경우에는 실제 시간과 동일해야 하지만, event의 선후 관계를 파악하는 것이 더 중요할 수 있다

### Happen-Before(HB) relation: 선후관계만 파악하자

- e가 e’보다 더 먼저 일어났다면 e → e’로 표시한다
- 메세지 m의 경우 반드시 send(m) → recieve(m)으로 표시한다
- e → e’, e’ → e’’라면, e → e’’이 성립된다
- 선후관계를 명확하게 파악할 수 없는 경우에는 concurrent하다고 부른다
- 발생할 수 있는 문제점
    - happened before관계에서 어떻게 global view를 유지할 수 있을까?
        - c(a)는 a의 time value값이라고 가정
        - a, b가 같은 프로세스 내에서의 event라면, a → b인 경우 C(a) < C(b)
        - a가 b에게 메세지를 보내게 되는 경우, C(a) < C(b)가 성립
    - global한 시계가 없는 경우에는 어떻게 일관성을 유지할까? - 각기 다른 logical clock을 유지한다
- Lamport’s logical clocks: 각기 다른 logical clock을 유지하고 있는 경우, m1, m2에서는 문제가 발생하지 않지만 m3, m4의 경우는 이전에 온 내용의 clock이 더 느리기 때문에 문제가 발생한다
    
    ![Untitled](05%20Synchronization/Untitled%206.png)
    
    - 다음과 같이 clock의 시간 값을 바꾸고, 순서에 맞게 시간을 조정해준다
        
        ![Untitled](05%20Synchronization/Untitled%207.png)
        
        - 이전 time값 보다는 적어도 1 이상 차이가 있어야 한다
        - pi가 메세지 m을 보내는 경우, 메세지의 timestamp ts(m) = ci
        - 메세지 수신받는 경우, 받은 timestamp와 비교해서 time을 둘중 더 큰 수로 설정한다

### 🧙‍♂️Mutual Exclusion algorithm: 알고리즘 상으로 한 시점에 두 자원이 동시에 이용하지 못하도록 한다

- critical section(CS): 여러 프로세스가 공유할 수 있지만, 한 시점에서는 반드시 하나의 프로세스만 사용중이여야 한다

### Centralized system: 중앙 OS에서 알아서 자원을 관리 해준다

### Distributed system: 중앙 통제 어렵기 때문에, process들은 공유 자원들에 대한 access 조정이 필요하다

- Centralized algorithm: 중앙집중방식. Coordinator가 관리한다. Queue를 이용한다
    - single point failure: coordiantor에서 발생하는 오류는 치명적이다
    - 자원을 쓰고 있는 사람이 없다면 바로 할당하여준다
        
        ![Untitled](05%20Synchronization/Untitled%208.png)
        
    - 이미 쓰고 있다면, permission이 주어질때까지 queue에 들어가서 대기하고 있게 된다
        
        ![Untitled](05%20Synchronization/Untitled%209.png)
        
    - 이전에 사용중이던 process가 사용 완료한다면, queue에 있는 순서대로 자원을 받는다
        
        ![Untitled](05%20Synchronization/Untitled%2010.png)
        
    
    <aside>
    👍 장점: 대기하다 보면 언젠가는 자원을 할당 받게 된다, starvation이 발생하지 않는다, 구현하기 간편하다
    
    </aside>
    
    <aside>
    👎 단점: 오류에 취약하다, 성능에 따라 병목현상이 발생할 수 있다
    
    </aside>
    
    - entry-exit 사이에 message: request / ok / release
    - entry 이전의 delay: request / ok
- Token ring algorithm: 단방향 원형으로 구현하여 관리한다
    
    ![Untitled](05%20Synchronization/Untitled%2011.png)
    
    - token은 프로세스 간에 넘겨지는 메세지이다. Token을 가지고 있는 사람이 자원을 할당받을 수 있다
    - 초기 상태에서는 process 0에게 token(자원)을 준다
    - token은 ring을 돌아가고, process가 자원을 할당받고 싶은 경우, token이 넘어올때까지 대기하게 되고, token을 받으면 사용한 이후에 다음 process로 넘겨주게 된다
    
    <aside>
    👍 장점: starvation과 deadlock을 방지할 수 있다
    
    </aside>
    
    <aside>
    👎 단점: token이 loss되는 경우가 발생하면, 새로운 token을 만들어야 한다
    
    </aside>
    
    - entry-exit 사이에 message: 1(바로 사용하는 경우), ∞(다른 process가 언제 끝날지 모른다)
    - entry 이전의 delay: 0 ~ n-1(n개의 process가 존재)
- distributed algorithm: 순서대로 우선순위를 가지고 자원을 할당받는다
    - 프로세스가 자원을 원하는 경우, resource의 이름, 프로세스 번호, 현재 logical time을 가지고 있는 message를 생성한다
    - 메세지를 다른 프로세스들에게 보내고 다른 프로세스로 부터 OK를 기다린다
        - 프로세스가 메세지를 받을 떄 만약 자원을 사용중이지 않고, 사용할 예정이 아니라면, okay 메세지를 return
        - 메세지를 받을 때 만약 자원을 사용중이면, reply를 보내지 않는다
        - 메세지를 받았을때, 자신도 자원을 사용하고 싶어하는 경우, timestamp를 비교하여 timestamp가 더 낮은 프로세스, 즉, 먼저 request를 보낸애가 이용되도록 한다
            - 받은 메세지가 더 낮은 timestamp를 가지고 있는 경우, ok를 보낸다
            - 자신이 더 낮은 timestamp를 가지고 있는 경우, queue에 넣는다
    - process는 모두에게 ok가 올 때까지 기다리고, 모두 ok가 오는 경우, 사용을 하고, 사용이 끝나는 경우, 모두에게 ok를 보낸다
    - 여러군데 failure가 날 수 있기 때문에 오류가 날 수 있는 확률이 centrailized에 비해 높고, 한군데라도 fail이 일어나면, ok를 보낼 수 없기 때문에 무한히 기다리게 된다
    - entry-exit 사이의 message: 2(n-1)
    - entry 이전의 delay: 2(n-1)
- decentrailized(voting) algorithm: 각 자원은 n개의 coordinator들을 가진다
    - client는 n개의 coordinator에게 permission을 요청하고, 과반수 이상의 coordinator가 허락하면 자원을 사용하고, 과반수 이상을 못 받으면 이후에 다시 시도하게 된다
    - client는 n개의 요청을 보내고 n개의 요청을 받게 된다
    - client는 사용한 이후에 n개의 message를 보내게 된다
    
    <aside>
    👍 장점: 하나의 coordinator가 fail되는 경우라도, 다른 coordinator가 작동중이기 때문에 과반수만 확보한다면 오류 없이 자원을 사용할 수 있다
    
    </aside>
    
    <aside>
    👎 단점: 동시에 자원에 대한 요청이 많이 오는 경우, 과반수가 넘지 못하는 경우가 생길 수 있고, 계속해서 major가 되지 못하는 경우, starvation현상이 발생할 수 있다
    
    </aside>
    
    - entry-exit 사이의 message: 2mk + m
    - entry 이전의 delay: 2mk
        - m은 coordinator의 개수, k는 시도 횟수 이다

|  | centrailized | token ring | distributed | decentrailized |
| --- | --- | --- | --- | --- |
| entry-exit사이 message | request/ok/release | 1, ∞ | 2(n-1) | 2mk + m |
| entry 이전의 delay | request/ok | 0 ~ n-1 | 2(n-1) | 2mk |

### 🧙‍♂️Election algorithms: 초기 상태, 혹은 오류가 난 경우에 coordinator역활 process를 뽑아야 한다. 많은 경우에, coordinator는 직접 뽑히는데, 이는 centralized solution으로 귀결된다

### Election by bullying: 각 프로세스들은 weight을 가지고 있다. 매번 가장 높은 weight을 가지고 있는 process를 coordinator로 뽑게 된다

- coordinator 역활을 하고 있는 process가 오류가 나서 새로 coordinator를 뽑아야 하는 경우, 다른 process에게 election message를 주게 된다.
- Pk process는 자신보다 높은 identifier를 가지는 process에게 election message를 보낸다
- 만약 아무도 응답을 보내지 않는다면 Pk가 새로운 coordinator가 된다
- 더 높은 process에서 응답이 온다면 Pk의 역활은 종료된다

![Untitled](05%20Synchronization/Untitled%2012.png)

- 진행 과정
    1. 더 높은 weight가지는 process에게 election message를 보내게 되는데, 이미 fail된 process는 메세지를 보내지 않고, 5기준으로는 자신보다 높은 6에게 메세지가 돌아오게 된다. 
    2. 6입장에서는 자신보다 상위 weight가지는 7에 보냈는데, 오류가 났기 때문에 응답이 없게 되고, 자신에게 응답이 없는 것을 확인하면 자신이 새로운 coordinator가 된다
    3. , 다른 process들에게 자신이 새로운 coordinator가 되었다고 알려주게 된다