# 08. Cloud computing

### Cloud의 정의

- NIST: 네트워크를 통해 온디맨드로 접근할 수 있는 공유된 컴퓨팅 자원(네트워크, 서버, 스토리지, 애플리케이션 및 서비스)의 집합을 참조한다. 이러한 자원은 빠르게 프로비저닝 및 릴리스되며, 사용자는 최소한의 관리 노력으로 자원에 대한 서비스를 제공받을 수 있다
    - provision: 미리 자원등을 준비해놓고, 필요할 때마다 전달하는 것
- 위키피디아: 인터넷을 통해 컴퓨팅 리소스를 제공하는 기술. 이는 서버, 스토리지, 데이터베이스, 네트워킹, 소프트웨어, 분석 등과 같은 컴퓨팅 서비스를 필요한 만큼 사용자에게 제공한다. 클라우드 컴퓨팅은 온디맨드로 자원을 프로비저닝하고 릴리스하며, 자원의 공유와 확장을 통해 효율적으로 작동한다
- Buyya: 인터넷을 통해 액세스 가능한 가상화된 컴퓨팅 리소스의 집합으로, 이 리소스는 필요에 따라 신속하게 프로비저닝되고 해제될 수 있으며, 서비스 제공자는 사용에 따라 유연하게 비용을 책정할 수 있습니다. 서비스 level에 대해서 사전에 계약하고, 계약한대로 제공한다

### 🧙‍♂️속성 & 특징

![Untitled](08%20Cloud%20computing%/Untitled.png)

- scalability elasticity: 확장성은 시스템이 서비스의 성능과 가용성 유지하면서 작업 부하를 처리하는데 역활을 하고, 탄력성은 작업 부하 변동에 신속하게 대응하여 리소스를 효율적으로 사용하도록 한다
- availability reliability: 가용성은 사용자가 서비스를 항상 이용할 수 있는 것, 신뢰성은 시스템이 예상 가능한 방식으로 작동하고, 데이터의 손실이나 오류 없이 정확한 결과를 제공하는 것
- performance optimization: 자원을 효율적으로 사용하고 관리하여 비용 절감에 도움을 줌. 대규모 시스템, 고부하 환경에서 안정적으로 동작하도록 하여 신뢰성 향상
- accessibility portability: 접근성은 소프트웨어를 더 많은 사람들이 액세스하게 만들고, 이식성은 소프트웨어를 다양한 환경에서 유연하게 사용할 수 있도록 해줌
- manageability interoperability: 관리 가능성은 소프트웨어나 시스템을 효율적으로 관리하고 운영하기 위한 기능을 갖추는 것을 목표로, 상호운용성은 다른 시스템과 통합과 상호작용을 용이하게 만들어 다른 시스템과의 호환성을 높여주는 것

### Utility computing: 컴퓨팅 리소스의 사용을 수도, 전기와 같이 공공으로 제공하는 것

- 사용자가 필요에 따라 컴퓨팅 리소스를 사용하고, 사용한 만큼 지불
- resource를 최대한 많이 사용하면서 최소한의 비용을 들이는 것을 목표로 한다

### Service oriented architecture(SOA): 서로 통신하는 서비스의 모임

- 시스템 개발 및 통합 단계에서 사용되는 유연한 설계 원칙 포함
- 여러 도메인에서 사용할 수 있는 느슨하게 통합된 서비스 제공
- 주소 web service model로 구현. Process단위가 아닌, 서비스 단위

### Quality of Service: 돈 더 많이 내는 사람이 더 잘 쓸 수 있도록, 돈 덜 내는 사람이 더 불편을 감안하도록. 법적인 효력은 없다

- network분야 외에도 customer care evaluation, technological evaluation부분이 추가 되었다

### Service level agreement: network서비스 제공자와 사용자 사이의 관계. QoS와 같은 측정 가능한 내용을 기준으로 계약한다. 법적 효력 존재한다

- 성능, 문제 관리 내역, 오류에 대한 penalty, 문서화된 보안 성능같은 것들

### Dynamic provisioning: 이용률은 일정하게 유지되는 것이 아니라, 계속 변동된다. 따라서 이에 맞춰서 제공하는 것은 어렵다

![Untitled](08%20Cloud%20computing%/Untitled%201.png)

- 자원을 너무 많이 잡으면 사용자가 많이 없는 경우에 자원이 낭비되고, 자원이 너무 적으면 사용자가 많은 경우에 자원을 사용하지 못하는 사용자가 너무 많다
- 유동적으로 사용자 수에 따라서 여유분 줄 수 있게, dynamic하게 자원을 잡아준다

### Multi-tenant design: 하나의 인스턴스를 여러개 client가 이용

- 데이터 구성을 가상으로 분할하도록 설계하였기 때문에 각 client는 맞춤형 가상 어플리케이션 인스턴스와 함께 작동한다
- client로 부터의 요청사항
    - customization: 각 환경에 맞춰서 동일하게
    - quality of service: 적절한 수준의 보안 및 견고성 제공

### Performance guarantees: cloud공급자는 강력한 인프라 또는 기타 리소스를 사용하여 고도로 최적화된 성능의 환경을 구축한 이후, 사용자에게 서비스 제공

- parallel computing: 동시에 수행. 큰 작업이 나누어져 수행된다
- load balancing: workload를 분산시켜주는 것. Resource 활용, 성능 개선, 에너지 효율성 개선
- job scheduling: task를 쪼갠 것이 job. 이 job이 서로 비슷하게 끝나야 merge하는 것이 편하다

### Uniform access: OS, browser에 상관 없이, 동일하게 접근할 수 있도록 해야 함

### Thin client: 브라우저를 단순하게

- cheap client HW: cloud제공자가 여러 client session을 동시에 관리할때, client는 더 값싼 HW로 만들어질 수 있다
- diversity of end devices: end user는 TV, 스마트폰 처럼 다양한 device를 이용한다
- client simplicity: local system은 완전한 작동이 필요하지 않다

### 🧙‍♂️Cloud를 사용하면서 얻을 수 있는 이점

- reduce initial investment: 기업이 아닌, cloud제공자가 투자 risk와 기반 시설을 가지고 있다. 기업이 제공하면 긴 배포시간이 소요되지만, cloud가 제공하면 빠르게 제공된다
- reduce capital expenditure: 기업이 IT부서를 운영하고, 인적 자원과 시설에 대한 투자, 긴 배포시간이 소요되는 것과 달리, cloud는 cloud기어이 관리하고, 기업은 cloud이용 비용에 대한 비용만 내면 되고, 더 빠르게 배포될 수 있다
- improve industrail specialization: 기업이 모든 것을 관리해야하고, 관리 효율성 저하, 독립 기업인 기존 방식과 달리, cloud는 기업이 자신의 사업에 대해서만 관리하면 되고, cloud기업이 전문적으로 효율적인 관리를 해주며, win-win 파트너십을 만든다
- improve resource utilization:  기업이 운영하면 resource가 낭비되고, power낭비, cooling system낭비 되는 것과 달리, cloud를 이용하면 자원을 공유하고, 효율적으로 power와 기타 자원을 공유할 수 있다
- reduce local computing power: 기업이 고성능 HW 필요, 필요 어플리케이션 설치, 휴대성 제한이 되지만, cloud를 이용하면 인터넷과 연결하기위한 HW만 필요, 어플리케이션 설치 불필요, 기본적인 휴대성 제공과 같은 효과를 얻을 수 있다
- reduce local storage power: 기업이 운영하면 제한적인 local disk, 활용도 낮음, 데이터 일관성 유지 힘듦, 주기적 backup이 필요하지만, cloud로 운영하면 필요에 따라 dynamic하게 할당, 일관성 cloud기업에서 유지, cloud가 일관성 보장과 같은 이점 있다
- variety of end devices: 기업이 관리하면 데스크톱만 이용 가능, power 소비에 따른 제한된 기능의 문제점 있지만, cloud를 이용하면 작은 device에서도 사용 가능하고, computing이 많이 필요한 작업에 대해 cloud에서 연산하도록 넘겨줄 수 있다

### 🧙‍♂️System models

![Untitled](08%20Cloud%20computing%/Untitled%202.png)

### Infrastructure as a Service(IaaS): 소비자는 OS 및 어플리케이션을 포함할 수 잇는 SW를 배포하고, processing, storage, network 및 기타 컴퓨팅 리소스를 provision하는 것이다

![Untitled](08%20Cloud%20computing%/Untitled%203.png)

- 소비자는 직접 infrastructure를 제어하지 않지만, OS, 스토리지, 배포된 app을 제어할 수 있으며, 일부 네트워크 구성 요소를 제한적으로 제어할 수 있다
- Amazon EC2, Eucalyputs, OpenNebula 등
- virtualization: 물리적인 자원이 멀리 떨어져 있을때, 추상화 하는 기술
    
    ![Untitled](08%20Cloud%20computing%/Untitled%204.png)
    
    - OS를 hypervisor위로 올려놓는다
    - 여러 OS가 물리적인 HW를 공유하고, 다른 서비스들을 제공한다
    - utilization 개선, availability, security, convenience를 개선시켜 준다
- resource management interface 제공
    - virtual machine: 생성, 일시중단, 재개, 종료와 같은 기본적인 기능을 제공해야 한다
    - virtual storage: 공간 할당, 공간 release, 데이터 쓰기, 읽기와 같은 기능 제공
    - virtual network: IP 할당, 도메인 이름 등록, 연결 설립, bandwidth제공 같은 기능 제공
- system monitoring interface 제공: 쓰는 사람, 제공하는 사람 모두 모니터링 필요
    - virtual machine: CPU loading, memory utilization, IO loading, 내부 네트워크 loading같은 기능을 제공
    - virtual storage: 가상 공간 활용, 데이터 복제, storage device대역폭 등을 제공
    - virtual network: virtual network bandwidth, 네트워크연결, load balacing등 기능을 제공

### Platform as a Service: 공급자가 지원하는 도구를 사용하여 소비자가 생성하거나 획득한 어플리케이션을 클라우드 인프라에 배포하는 것

![Untitled](08%20Cloud%20computing%/Untitled%205.png)

- 소비자는 네트워크, 서버, OS, 스토리지를 포함한 인프라를 관리하거나 제어하지는 않지만, 구축된 어플리케이션은 제어할 수 있다
- Microsoft windows Azure, Google app engine, Hadoop등
- programming IDE제공
    - 사용자는 programming IDE를 이용하여 서비스를 개발
        - IDE는 기본 런타임에서 지원되는 전체 기능을 통합해야 함
        - IDE는 profiler, debugger, 테스트 환경 등을 제공하야 함
    - 런다임 환경에서 제공되는 API는 다양하게 제공될 수 있지만, 계산, 스토리지 및 통신 자원 운영과 같은 공통 운영 기능이 있다

### Software as a Service: 클라우드에서 제공하는 공급자의 어플리케이션을 사용. 다양한 client에서 어플리케이션에 access가능

![Untitled](08%20Cloud%20computing%/Untitled%206.png)

- 사용자는 제한된 어플리케이션 구성 설정 제외하고, 다른 내용을 제어하지 않는다
- Google app, SalesForce.com, EyeOS등

### 🧙‍♂️Deployment model

### Public cloud: cloud서비스를 제공하는 기업이 소유하여, 일반 대중들이 사용하거나, 대형 기업에서 사용하도록 되어있다

- external cloud 혹은 multi-tenant cloud로 알려져있으며, 이 모델은 open되어 접근할 수 있는 cloud환경을 제공

### Private cloud: 조직을 위해서만 동작. 직접 관리하거나 제3자가 관리한다

- internal cloud, 혹은 on-premise cloud로 불린다. Cloud를 소유하고 있는 대상에게만 제한적으로 권한이 주어진다
- public cloud와의 비교

|  | public cloud | private cloud |
| --- | --- | --- |
| infrastructure | homogeneous | heterogeneous |
| policy model | common defined | customized |
| resource model | shared & multi-tenant | dedicated |
| cost model | operational expenditure | capital expenditure |
| economy model | large economy of scale | end-to-end control |

### Community cloud: 조직 여러개가 share할 수 있는 cloud. 공유하게 되면, 여러가지 공유 규정에 대해 설정해야 한다

### Hybrid cloud: 두개 이상의 cloud를 합친 것. 각기 다른 cloud를 기술적으로 결합하여 사용한다