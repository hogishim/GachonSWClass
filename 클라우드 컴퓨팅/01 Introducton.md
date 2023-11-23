# 01. Introducton

### Federate learning: 데이터를 **쪼개서 여러개로 학습**한 후, 합친다. 끝나는 시간은 가장 오래걸린 task를 기준으로 끝난다.

### 파일: 내용 + 메타 데이터로 구성

- 내용: 파일의 내용으로, 잘 변하고, 많이 사용되지 않는다
- metadata: 자료구조로 표현하기 위한 내용으로, 소유주, 권한, 확장자, 저장 날짜, 용량, 위치 등 내용을 저장하고 있다. 잘 변하지 않으며, 자주 사용되고, 크기가 일정하다
- 포멧: 메타데이터의 내용을 업데이트하는 것

### Web search할때, 데이터가 출력되는 과정은?

1. Website주소를 입력하면 DNS에서 얻은 IP주소로 routing protocol을 이용하여 이동한다
2. Website에서 검색하고자 하는 내용을 검색한다
3. 사이트는 검색한 내용을 return시켜준다  

### ✨**Google File System(GFS)**: 내용이 분산되어 있어 네트워크를 통해 서로 접근 가능하다

![Untitled](01%20Introducton%20c20224911c844531a2f62bd98ca90117/Untitled.png)

- Chunk: chunk는 데이터를 쪼개놓은 것으로 page라고도 불리며, 구글의 경우 64mb로 잡고 있다. Chunk의 경우 적절한 크기를 잡는 것이 중요하다. 너무 크면 낭비가 많고, 너무 작으면 chunk가 너무 많아진다
- Chunk server: 각 파일에 대한 메타데이터를 저장하고 있다. Chunk server는 여러개 존재
- Master: chunk server에 대한 내용을 저장하고 있다
- Shadow master: master가 죽는 경우를 대비하고 있다
- 파일들은 중복 저장되어 서버 하나에 오류가 나더라도 이용할 수 있도록 한다. 단, 중복 저장 하면 관리 측면에서 난이도는 더 올라간다

### Computing power: 컴퓨터는 빠른 속도로 성능이 발전하고 있다

- 컴퓨터는 Moore’s law처럼 성능이 빨라지고 있고, 메모리의 용량 또한 커지고 가격이 내려가고 있다
- 네트워크 속도: 2G(text만 가능, 사진 제한적) → 3G(영상 가능) → 4G(HD급 영상 가능) → 5G(delay 거의 없다)
- IoT의 등장으로, 네트워크에 연결되는 host가 급격하게 늘어나고 있다

### ✨Distributed system: 하드웨어의 발전으로, 각각 독립적인 resource를 가지고 있는 computer를 네트워크를 이용하여 하나의 시스템처럼 이용하는 것. 가장 중요한 부분은 데이터 일관성(Coherency)이다

### Distributed system의 목표

- Resource sharing: 자원들은 서로 떨어져 있는데, 사용자들이 자원들을 접근하기 편하도록 한다
- Transparency(투명성):  하나의 시스템처럼 동작해야 하며, 돌아가는 back부분의 내용들은 사용자가 알 필요가 없다
    - access: resource들이 어떻게 접근되는지 알 필요가 없다
    - location: resource가 어디에 위치하는지 알 필요가 없다
    - migration: resource가 다른 곳으로 이동할지 모른다는 것을 알 필요가 없다
    - replication: resource가 복제되있다는 것을 고려하지 않아도 된다
    - concurrency: resource는 서로 다른 사용자에 의해 이용될 수 있다는 것을 알 필요가 없다
    - failure: 오류내용은 감추고, resource를 복구한다
    - delay, 오류를 완전히 가리는 것은 쉽지 않기 때문에 transparency를 완전히 유지하는 것은 쉽지 않다.
- Scalability(확장성): 관리적인 측면에서는 scalability가 클수록 관리가 힘들다
    - size scalability: 크기를 나타내며, 사용자수, 프로세스 수 등 숫자로 표현 가능한 것. 성능의 한계가 있을 수 있으나, 이는 여러 server를 둠으로써 해결할 수 있다
    - geographical scalability: 노드들 사이의 물리적인 거리. 대부분 server-client구조로 이루어 지는데, 거리가 멀다면 latency로 문제가 발생할 수 있다
    - administrative scalability: 관리 domain의 개수. 관리적인 측면에서는 scalability 낮은 것이 더 유리하다
    - Parellel computing: Scaling을 더 좋게 하는 방법
        - 데이터와 연산을 다양한 machine에서 수행할 수 있도록 하는 것. Client에게 연산을 넘기거나, 중앙으로 몰리지 않도록 한다
        - 다양한 copy들을 여러 machine에 나누어서 저장한다. 이 경우, read할 경우에는 문제가 발생하지 않지만, write를 하는 경우에는 문제가 발생할 수 있다
        - DNS: 계층화 구조로 이루어져, 분산되도록 한다
        - WWW: 서버는 한 곳이 처리하는 것이 아니라, 세계에 퍼진 여러 server가 처리한다
        
        ![Untitled](01%20Introducton%20c20224911c844531a2f62bd98ca90117/Untitled%201.png)
        
        - Symmetric Multiprocessor(SMP): RAM을 공유하는 shared memory
        - Distributed system(cloud): RAM을 공유하지 않고 network통한 교류하는 private memory. Delay가 발생할 수 있다.

### 컴퓨터의 구성요소: CPU(+Mainboard), RAM, SSD, network + 기타 I/O장치

- scheduler: 프로세스들을 관리
- file system: metadata, data들을 link
- device driver: OS와 상호작용
- memory 관리: 각 프로세스에 얼만큼 할당할 것인지
- network: 네트워크 연결과 관련하여 프로토콜을 저장한다
- coordinating: OS에서 process들이 잘 돌아갈 수 있도록 하는 것. Windows에서는 API, linux에서는 System call이 app과 h/w사이를 연결해준다

### ✨Middleware: app과 다양한 machine사이에 존재하여 각 app에 같은 interface를 제공하여 여러 시스템을 동시에 관리할 수 있도록 해준다. 밑단의 OS가 다르더라도, middleware 윗 단에서는 동일하게 사용할 수 있도록 한다.

![Untitled](01%20Introducton%20c20224911c844531a2f62bd98ca90117/Untitled%202.png)

### ✨Cloud computing: cloud는 parallel (모든 작업이 동시에 수행), distributed (모든 작업이 동시에 수행될 필요는 없는)작업의 일종으로, 서로 연결되거나 가상화된 컴퓨터들이 여분 자원을 준비했다가 자원을 유연하게 계약된 수준에 따라서 분배하여주는 서비스

- mainframe(1970~80), client-server(1990), web(2000)을 거쳐서 최근에는 cloud가 이용된다
- local에 resource가 있으면 distributed system, resource가 local에 없으면 cloud computing

### Cloud computing system의 분류

- infrastructure: networking + storage + server
- platform: SW단까지 packaging한 것
- framework: 정해진 틀에서 parameter들만 바꾸는 것

![Untitled](01%20Introducton%20c20224911c844531a2f62bd98ca90117/Untitled%203.png)

- Traditional IT: 표준화된 가상 머신
- IaaS
- PaaS
- SaaS

### ✨Symmetric Multiprocessor(SMP): 대칭형, 비슷하게 생긴 processor들을 이용

>>스마트폰은 asymmetric multiprocessor를 이용하는데, 이는 에너지의 한계 때문이다. 간단한 작업들을 성능이 덜 좋은 processor가 수행하고, 고성능 작업은 고성능 processor가 담당한다

![Untitled](01%20Introducton%20c20224911c844531a2f62bd98ca90117/Untitled%204.png)

- processor는 명령어를 해석하는 control unit, 연산을 수행하는 ALU로 구성된다
- cache는 메인 메모리까지 이동하는 시간을 단축시켜준다
- CPU는 메모리와만 대화한다
- System bus: 메모리에 접근하는 bus
- Local bus: I/O장치에 접근하는 bus. HW에서 메모리로 driver를 통해 올려진다
- multiprocessor는 processor자체를 크기를 더 작게 만들거나, processor가 담기는 공간을 크게 만듬으로써 구현 가능하다

### Process 수행 과정

1. 현재 위치에서 수행해야할 명령어를 fetch
2. OP code로 구성된 명령어를 decoding
3. 명령어 실행, 출력 저장

### Pipelining으로 수행할 시 발생할 수 있는 문제점

- data hazard: 이전 명령어의 결과를 이용하여 명령어 완료까지 기다려야함
- control hazard: 분기문을 수행할때 발생하는 문제점
- structure hazard: 써야하는 resourece
- 어떻게 해결할까??
    - write-through: 만약 변수가 write되는 경우, cache에 가지고 있지 않고 write이 되는 즉시 명령어를 수행한다
    - snoopy cache(bus sniffing): read를 한 후 같은 변수를 write하는 경우는 write through로 해결 불가능. 이 경우, bus를 계속 확인하고 있다가 write signal이 온다면 그 신호를 감지하고, 기존 내용을 삭제하고, 새로운 값을 요구한다

### Multi computers의 활용

- switched multicomputers on LAN: 컴퓨터를 여러대 두고, 네트워크 방식으로 message 주고 받으며 통신한다
- distributed systems: 네트워크로 여러대의 컴퓨터를 하나처럼 이용 → micro server: 라즈베리파이 같은 경량 하드웨어 여러개로 서버를 만들어 이용한다
- distributed computing: 높은 성능을 위해서, grid computing, cluster computing을 통해서 문제를 여러개로 쪼개서 해결한다
- distributed Home systems: 개인 device, TV, NAS(원격으로 파일을 접근할 수 있는 것)등이 규칙을 통해 연결된 것
- distributed Electronic Health Care Systems: PAN 범위의 네트워크로 sensor를 이용해 건강을 관리
- IoT: IoT는 분산 시스템으로, 인터넷에 접근하여 다양한 서비스를 이용한다