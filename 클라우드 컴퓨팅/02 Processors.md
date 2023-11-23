# 02. Processors

### ✨Process: 실행중인 프로그램. Disk에 있으면 program이라고 하고, RAM에 있는 경우에는 process라고 함. Process는 CPU, memory, 파일, I/O device 등 자원을 이용하여 실행된다

### User space: process는 각 process마다 공간을 가진다

![Untitled](02%20Processors/Untitled.png)

- stack: 동적으로 할당되는 영역. 지역변수, 매개변수, 함수가 쌓인다
- heap: 프로그래머가 할당하는 영역으로 메모리가 동적으로 할당되는 영역. Stack과 heap이 만나는 경우, 이를 stack overflow라고 부른다
- data: 전역변수가 들어있는 영역. 언어마다 다른데, 0으로 초기화 되어있다
- text: binary code가 저장된 영역. 즉, 코드가 들어있는 부분

<aside>
🏛️ Process는 강의실 하나 하나로 비유될 수 있다. Process는 OS로 부터 자원을 할당받아 실행된다. Kernel(OS)를 통해서 process끼리 통신할 수 있다

</aside>

<aside>
👷 Thread는 강의실 안에 있는 학생으로 비유될 수 있다. 전역 변수를 공유하며 각 thread는 지역변수를 가질 수 있다. 프로세스내에서 실행되는 흐름으로 프로세스 내에서 각각 독립적으로 실행될 수 있다. 하나의 thread오류가 다른 thread에 영향 미칠 수 있다

</aside>

- User space외에도 kernel이 사용해야할 영역도 필요하다. Kernel은 자료구조(메타데이터)들을 관리한다
- paging: kernel은 1개만 필요하고, user space는 process마다 필요한데, 그러면 자원이 너무 많이 필요하게 된다. 이런 경우, 논리 주소를 이용하여 같은 논리 주소라도 physical address를 다르게 mapping하여 관리하여 마치 user space를 여러개 사용하는 것처럼 인식시킨다

### Process의 state: running상태인 process는 하나만 존재할 수 있다

![Untitled](02%20Processors/Untitled%201.png)

- new: process가 만들어지고 있는 상태
- ready: process가 CPU로 할당되기 기다려지고 있는 상태
- running: process가 CPU에 의해서 실행되고 있는 상태
- waiting: process가 대기상황으로 넘어와 다시 호출될때까지 대기하는 상태
- terminated: process가 종료된 상태

### Process Control Block(PCB)

![Untitled](02%20Processors/Untitled%202.png)

- process state: 위에서 언급한 process의 state를 저장하고 있다
- process number(PID)
- context: program counter, register. Process의 상태가 바뀔때, CPU안의 값 바꾸게 되는 경우 register의 내용과 PC의 내용을 백업한다
- register: 하드웨어의 register값을 저장하고 있다
- Context switch: CPU가 다른 process로 switch될때, 이전 process의 저장된 내용을 저장해야 한다. 이후 이 process가 다시 실행될때, 이전 내용들을 다시 불러온다

### ✨Thread: 각 thread는 각자의 register, PC, stack을 가진다. 같은 process에 있는 thread들은 같은 address 공간을 공유한다

![Untitled](02%20Processors/Untitled%203.png)

- PCB와 TCB의 차이점은 stack과 PC값이 thread만큼 계속 생긴다
- TCB에서 heap과 data의 부분은 동기화 과정이 필요하다
- thread는 process하나를 만드는 것에 비해 자원이 훨씬 덜 사용된다. 단, thread는 오류가 하나만 나더라도 관련 thread 모두에 오류가 발생한다
- multi-thread를 이용하게 되면, 하나의 프로세스를 여러 core에서 처리하여 서로 동시에 동작하게 할 수 있다
- non-blocking system call: I/O때문에 시간이 걸릴때, single thread라면 작업이 끝날때까지 기다려야 하지만, multi-thread라면 I/O가 진행되고 있는 동안, 다른 thread가 계속 진행할 수 있다

### Kernel thread: kernel에 의해 동작하는 thread로, system call에 의해서 만들어짐. 각 thread는 TCB가 필요하다

<aside>
👉 장점: thread를 multi-core에 동작하게 할 수 있다. 다른 thread가 오래 걸리는 동작을 하고 있을때, 동시에 다른 작업을 수행할 수 있다

</aside>

<aside>
👉 단점: 모든 thread 동작은 kernel을 반드시 통과해야하기 때문에 kernel이 multi여야 효과적. Kernel만 multi로 하면 호환성 문제가 발생할 수도 있다

</aside>

### User thread: user library에서 구현, kernel support없이 만들어져 system call이 불필요. Library단에서 관리되어 먼저 끝난것들이 기다리다가 나중에 같이 관리됨. 기다리는 시간이 있기 때문에 단일 kernel로 mapping된다면 성능이 조금 낮아진다

<aside>
👉 장점: thread의 관리는 user space에서 진행된다. 빠르고, 효율적이다

</aside>

<aside>
👉 단점: thread하나가 system call을 하면, 나머지 thread도 모두 block된다. User multi-level은 multicore에서 동작하지 않을수도 있다

</aside>

### Multi-thread models: 어떻게 mapping될 수 있을까?

![Untitled](02%20Processors/Untitled%204.png)

- many-to-one: 여러 user thread가 하나의 kernel로 mapping되는 것. 이 경우 효과는 낮다
- one-to-one: 각 user thread가 각각 1:1로 kernel로 mapping되는 것. kernel thread가 계속 생기는 것은 불가능하다
- many-to-many: 여러 user thread가 여러개의 kernel로 mapping되는 것. Kernel을 한정된 범위에서 확보하는 것

Multi-threaded clients

- multi-threaded web client: 각 파일들은 개별의 thread에 의해 fetch되며, request들을 처리한다
- client는 call을 동시에 여러개 하고, 여러 thread가 각각 개별적으로 처리한다

Multi-threaded server 

- 성능향상: process를 하나 더 늘리는 것보다 더 싸다. Single thread server를 가진다면 multiprocessor system으로 확장되는 것을 저지하게 된다
- client와 마찬가지로 다른 작업이 진행되는 동안 지연되는 것을 숨길 수 있다
- file server: dispatcher thread가 작업이 오기 기다렸다가 작업이 오면 작업할 worker thread를 고른다. Worker thread는 작업을 진행하고, 해당 thread외에 다른 thread는 다른 작업을 진행할 수 있다

### OS가 하는 일

- process를 만들고, 실행하고, 종료 시킨다
- scheduling을 통해서 CPU의 자원을 할당하는 순서를 정한다
- resource sharing, synchronization을 관리한다

### ✨Virtualization: 한정된 자원을 여러사람이 개별적으로 쓸 수 있도록 하자

가상화 영역을 swap영역이라고 하는데, swap in은 메모리로 가져오는 것이고, swap out은 저장장치로 보내는 것이다

가상화의 예시

- CPU 가상화: 여러 process가 각각 컴퓨터를 동작시키는 효과를 준다
- memory 가상화: 가상 메모리
- network 가상화: 가상화 Wi-Fi, SDN
- 컴퓨터 시스템 가상화

### Computer system interfaces

![Untitled](02%20Processors/Untitled%205.png)

- general machine instruction: 하드웨어에 직접적으로 접근하는 application의 interface로, app에서 H/W로 직접적으로 접근하는 경우는 드물며,  add a1, a2, a3와 같은 경우와 같이 제한적으로 접근할 수 있다
- privileged machine instructions: read disk, 네트워크를 이용한 통신 같은 OS에서 hardware로  접근하는 경우에 이용되는 interface
- system call: library와 OS사이 interface. OS에 의해 제공된다
- Application Programming Interface: 많은 경우에 호출은 API에 의해 가려진다
- HAL: 하드웨어 추상화 레이어로, 프로그램이 하드웨어에 따라 별 차이 없이 다룰 수 있도록 OS에서 만들어주는 가교 역활로, API처럼 사용되어 프로그래밍시 특정 device에 종속되지 않게 할 수 있다

### ✨가상화의 3가지 방법

### Application virtualization: runtime을 가상화하는 것으로 library단에서 깔아줌. Platform에 독립적으로 동작하는 환경을 만드는 것이 목표로, 프로그램이 서로 다른 환경에서 동일하게 동작할 수 있도록 함

![Untitled](02%20Processors/Untitled%206.png)

예시> 자바의 경우, javac는 각기 다른 하드웨어에서 다른 버전을 설치해야 하지만, 다른 하드웨어에서 동일한 내용을 작성할 경우, javac를 거쳐서 나오는 .jar파일은 하드웨어에 상관 없이 동일하다. 이후 JVM을 거쳐서 나오는 기계어는 하드웨어에 따라 다르다

### Native Virtual Machine Monitor(VMM): native라는 것은 hardware바로 위에 구현된 것이다. 하드웨어 바로 위에서 firmware단으로 돌아단다. 제공되는 interface를 통해서 다른 OS, 다른 hardware platform에서 동작할 수 있다

![Untitled](02%20Processors/Untitled%207.png)

### Hosted Virtual Machine Monitor(VMM): host OS위에서 다른 OS가 동작할 수 있게 해주는 것. Virtual Machine이라고도 불린다. 3가지 방식중에서는 성능 면에서 가장 안좋다

![Untitled](02%20Processors/Untitled%208.png)

### ✨Virtual machine: physical computer의 자원, disk, CPU의 할당을 바꿀 수 있다

<aside>
👉 장점: 같은 machine상에서 여러 server를 돌릴 수 있다. 낮은 활용도를 가지는 높은 성능의 서버를 만들어낼 수 있다. 개발환경을 여러 OS 환경에서 test할 수 있고, 가상화 영역에서의 변화는 normal operation에 영향이 없다. Dynamic하게 복사할 수 있다.

</aside>

<aside>
👉 단점: 보안 문제가 발생할 수 있다. 가상화 영역에서 다른 영역으로 침투하지 못하도록 해야 한다. 자원을 공유하기 때문에, 하나에 비해서는 성능이 떨어진다. 구현이 어렵고, host에서 guest를 관리하는 것은 쉽지 않다 → CPU가 명령어를 직접 실행하자

</aside>

ex> VMware, Hypervisor(Xen) - interface를 하나 줄여서 성능을 향상, cloud(amzaon EC2, Google Compute Engine) - physical machine을 rent하는 것 대신에, 가상 머신을 rent하는 것

### Google data center

- data center technology: map reduce, google file system, big table
    - Jupiter: merchant silicon, centauri, middle block, spine block, aggregation block순으로 점점 모이면서 거대한 크기의 데이터센터를 만든다
- data center network: B4, Google Global Cache, Jupiter, Freedome, Andromeda
    - Freedom: 학교정도 범위에서 사용된다

### Google Jupiter design의 원칙

1. clos topology: 복합적인 계층 구조로 만든다. Mesh 형식으로 만들어 하나가 망가지더라도 잘 동작할 수 있도록 한다. Core근처로 가면 traffic이 몰리기 때문에 관리하는 것이 쉽지 않은다
    
    ![Untitled](02%20Processors/Untitled%209.png)
    
2. centralized control protocols: clos topology로 한다는 것은 기존 switch로 감당할 수 없다는 것을 의미한다. 이는 Software Defined Networking(SDN)을 통해서 SW적으로 handling한다. 이는 자체적으로 protocol을 만들어서 remodeling하는 것으로, master node가 link상태를 수집하고, 각 switch는 개별적으로 forwarding table을 계산한다

### Forwarding vs. routing

- forwarding: data plane영역에서 일어나며, 바로 앞으로 단순 전달만 한다
- routing: control plane영역에서 일어나며, 목적지까지 가는 end-to-end에 관여한다

→기존 방식과 차이점??

<aside>
🛣️ Per-router control plane: 기존의 방식으로, 각각의 개별적인 routing algorithm을 가지고 있는 router들이 forwarding table을 만든다

</aside>

<aside>
🔯 Logically centralized control plane: control plane영역에 있는 controller가 Control Agent(CA)와 상호작용하여 forwarding table을 만들어 준다

</aside>

### ✨Software Defined Networking(SDN): 중앙집중 방식으로 구성 오류 방지, 더 넓은 확장성, traffic 관리 같은 이점을 얻을 수 있다

- 개별 요소를 고려하지 않아도 되기 때문에 더 구현/설계가 편하다.
- main이 오류가 나면, 전체가 동작하지 못한다
- 이전 network는 vertically integrated라고 하여 폐쇄적이고 발전이 느리고 규모가 작았기 때문에 한가지 사항만 고려하면 되었다
- 최근의 경우에는 다양화 되었기 때문에 horizontal하다고 보이는데, 이는 open되고 빠르게 발전하며 규모가 훨씬 커져 고려해야할 요소가 많아졌다.

![Untitled](02%20Processors/Untitled%2010.png)

- data plane switches: 표준화된 forwarding을 제공. Controller에 의해 설치된 flow table을 사용. API형식으로 제공되어 OpenFlow같은 protocol을 이용하여 controller와 통신
- SDN controller: 네트워크 정보를 관리. South bound인 switch, north bound인 위 부분의 app과 통신한다. 성능, 확장성, 장애 방지, 견고성을 위해 분산 시스템으로 구현
- network-control apps: SDN에서 제공하는 API를 이용하여 control function을 구현한다. Routing, load balancing(기존에는 하지 못했던 것), access control(기존에는 하지 못했던, 특정 packet을 특정 구간만 이용하도록 하는 것)등의 기능을 수행한다
- OpenFlow control: controller와 switch사이에서 동작하여 message를 주고 받을때는 TCP를 이용한다
    - controller-to-switch: configuration에서 이용된다
    - asynchronous: switch에서 controller로 가는 메세지로, error report때 이용된다
    - symmetric: 서로 상호작용하는 메세지로, 시작하는 hello, 물어보면 대답하는 echo, 추가 확장을 위해 존재하는 vendor로 구성된다

SDN의 동작 예시

![Untitled](02%20Processors/Untitled%2011.png)

1. S1에 오류가 발생하여 openflow port를 통해서 control로 error message를 보낸다
2. SDN controller는 openflow message를 받고 상태를 업데이트 한다
3. 다익스트라 알고리즘은 link 상황이 바뀌면 call되는데, 현재 link상태가 바뀐 상태이므로 call된다
4. 다익스트라 알고리즘은 새롭게 업데이트 된 사항을 반영하여 새로운 route를 만들어 준다
5. link state routing app은 SDN에 flow table 계산하는 component와 통신한다
6. controller는 openflow를 이용하여 업데이트가 필요한 switch들에 update를 진행한다

### ✨Clients: 사용자가 원격 서버와 상호작용할 수 있도록 해준다

- Fat client: 서버쪽 일을 분담할 수 있는 client. 독립적인 app이 네트워크를 통해서 통신할 수 있도록 해준다
- Thin client: 모든 과정은 서버에서 처리하므로, client는 보여주는 역활만 하게 된다. 최근 trend는 thin client가 더 많다

### ✨Server: client에게 요청을 받은 여러가지 일을 한다. Port로 구분되어 요청을 처리하고, request가 잘 진행되는지 확인한다. 이때, protocol마다 port가 구분되기 때문에 다른 protocol로 동일한 port번호를 이용할 수 있다

- Iterative server: 서버 자체에서 요청을 처리하고, 필요하다면 response를 보내는 것
- Concurrent server: 요청을 직접 처리하지 않고, 개별적인 thread나 process로 작업을 넘긴다
- Server clusters: network로 연결된 machine들의 모임. 각 machine이 하나 이상의 server를 돌린다. 주로 LAN을 사용하며, 논리적으로 3가지 tier로 구분된다
    - first tier: packet을 적절한 server로 보낸다. HTTP request를 받아서 일부를 application server로보내서 추가적인 작업을 진행시킨다.
    - second tier: application server. 실제로 연산을 수행하는 부분
    - thired tier: data processing server. 데이터를 저장하는 부분
    - 모든 서버가 3계층중 하나에 있는 것은 아니다
    - 몇 machine은 한가하고 일부는 복잡할 수 있는데, 이는 virtualization을 통해 작업이 없는 machine으로 작업을 옮겨서 해결한다

### TCP handoff: 서버 load를 분산시키기 위해, 연결을 맺을때, server중 최적의 서버를 골라서 맺는 것

![Untitled](02%20Processors/Untitled%2012.png)

- transport-layer switch를 이용하여 타 server와 연동 가능
- 여러 server로 구성된 것을 사용자가 모르게 해야 한다. Entry point의 IP는 하나만 존재해야 한다
- TCP connection 요청을 확인하여 가장 잘 수행할 것 같은 하나의 서버를 골라서 요청을 처리하도록 함. 혹은 load balancing으로 처리
- 받은 요청에 대한 response를 보내는데, 이때, 송신자 IP는 switch로 해둔다. 이는 하나의 네트워크 포인트로 보이도록 하기 위함이다
- round-robin, informed decision으로 load balancing한다

### ✨Code migration: program load를 분산시키기 위해 데이터가 아닌, 프로그램을 건내는 것.

- load가 많은 machine에서 적은 machine으로 넘겨준다
- Code를 데이터로 넘겨서 client application이 database server로 옮겨지고, database server는 결과만 전달해줘서 통신을 최소화 한다

### Process의 세가지 구성 요소

- code segment: 실제 명령어가 있는 부분
- resource segment: 코드 실행을 위해 필요한 자원들
- execution segment: 수행중인 상태 같은 CPU의 contex를 저장한다

### Weak mobility: code segment만 보내거나 초기 데이터만 보내 전달되는 내용이 없기 때문에 다른 machine에서 처음부터 다시 수행해야 한다

### Strong mobility: code segment와 execution segment를 보낸다. 따라서 다른 machine에서도 데이터를 이어서 수행할 수 있다. 그러나 이 경우의 구현이 더 어렵다

### 서로 다른 system에서 migration에 발생할 수 있는 문제

- 다른 기종에서 실행이 어려운 경우일 수 있다
- local hardware, OS, runtime system중 하나라도 호환되지 않으면 실행이 힘들다 ⇒ 가상화를 사용하자!!!

### ✨MapReduce: 많은 양의 데이터를 한번에 처리할때, 데이터가 분산되어있지 않고, 덩어리로 되어 있다면 효과적으로 나누어 처리할 수 있다

### Client는 매우 큰 file에 대해서 어떻게 computation할까?

- 큰 파일을 읽는 것은 높은 대역폭, 네트워크를 사용한다
- data locality: map reduce를 통해서 datanode의 계산을 수행할 수 있다

### 구성 요소: map function과 reduce function을 명시시켜주어야 한다

- map function: key-value를 묶어주어 중간 단계의 key-value pair를 만들어 준다
- reduce function: 같은 중간 단계의 데이터 가지는 value들을  어떻게 merge시킬 수 있는지

### MapReduce 구조

- master server: task를 할당하여 input data를 잘라서 넘기고, task의 상황을 track한다
- map server(workers): map operation을 진행하고, 중간 결과를 저장한다
- reduce server: 중간 데이터를 처리하고 넘긴다

### MapReduce의 동작 과정

![Untitled](02%20Processors/Untitled%2013.png)

1. 데이터는 MapReduce library를 이용하여 input data를 block(16-64mb)으로 나눈다
    
    ![Untitled](02%20Processors/Untitled%2014.png)
    
2. user program은 program copy를 만들고, 그중 하나를 master thread로 설정하고 나머지는 worker thread로 설정한다
    
    ![Untitled](02%20Processors/Untitled%2015.png)
    
3. master는 map의 개수 M, key값의 개수 R을 유휴중인 worker에게 분배한다
    
    ![Untitled](02%20Processors/Untitled%2016.png)
    
4. 각 worker는 task를 처리하고 중간 key/value pair output을 만든다
    
    ![Untitled](02%20Processors/Untitled%2017.png)
    
5. 각 worker는 R개의 region으로 나누어진 intermediate value를 출력하고, master thread에게 알린다
    
    ![Untitled](02%20Processors/Untitled%2018.png)
    
6. master는 작업이 가능한 중간 데이터를 읽게되는 reduce-task worker에게 중간 데이터의 disk위치를 넘겨준다
    
    ![Untitled](02%20Processors/Untitled%2019.png)
    
7. 각 reduce-task worker는 중간 데이터를 sorting하고 reduce function을 호출한다. Reduce function의 output이 reduce-task의 partition output에 붙는다
    
    ![Untitled](02%20Processors/Untitled%2020.png)
    
8. master가 user process에게 완료되면 전달해준다
    
    ![Untitled](02%20Processors/Untitled%2021.png)
    
- 예시> URL 접속 횟수 count, target에 대한 source list
- MapReduce는 Google에서 search에 활용, Yahoo search에 활용, spam 탐지에 활용 되었고, facebook에서는 data mining, ad optimization, spam detection에 활용되었다
- 반복적 작업에 특화되어 있다
- framework가 이미 존재하는 노드에서 작업을 효과적으로 예약할 수 있도록 지원한다