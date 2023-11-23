# 03. Storage architecture

### Architectural style

- components: 교체 가능한 인터페이스를 가진 단위 unit. 분산시스템에서는 어떻게 조직하고 서로 소통할지를 결정한다
- connector: component들이 어떻게 동작할지를 결정하는 것.
- component간 통신: remote procedure call, message, data stream등
- 주요 style: layerd architecuture, object-based architectures

### Layered architectures: layer 기반으로 배치됨. 네트워크에서 protocol이 쌓여있는 구조를 생각하면 된다

![Untitled](03%20Storage%20architecture/Untitled.png)

- pure layered architecture: 아래 방향으로만 통신하는 down call 방식
- mixed layered organization: 일부를 jump할 수 있는 방식
- layered organization with upcalss: downcall, upcall 모두 가능한 방식

### Object-based architectures: component들은 object이며, procedure call을 통해서 서로 연결되어, 하나하나가 연동되어 돌아간다

![Untitled](03%20Storage%20architecture/Untitled%201.png)

### 🌟Storage architecture: 다양한 방식의 저장장치 구조가 존재한다

- basic architecture: client-server, three-tier, peer-to-peer
- distributed storage: RAID systems, storage network
- data centers: google, facebook
- Handoop File System(HDFS)

### 🌟Two tier architecture(client-server)

- client: network에 묶여서 서버의 자원을 활용하기 위해서 요청을 보낸다
- server: client가 보낸 요청을 처리하고, 자원에 대한 관리를 한다
- centralized architectures: 요청을 보내면, 답이 올때까지 block되어 있음

![Untitled](03%20Storage%20architecture/Untitled%202.png)

- server-client communication
    - connectionless: 이전 내용 상관 없이, 연결 맺을때마다 초기화 된다. Idempotent한 경우(같은 연산을 무한으로 반복해도 똑같은 결과가 나오는 ex - read)에서 사용된다
    - connection-oriented: 이전내용이 저장되어, 새로 연결을 맺더라도 이전 결과가 나오도록 한다
    - TCP vs. UDP: 용도마다 비교하여 적절한 상황에 사용한다. TCP는 빠른 전송보다는 안정적으로 손실 없이 데이터를 보내야 하는 경우 사용하고, UDP는 손실이 있더라도 빠르게 보내야하는 경우 사용한다

![Untitled](03%20Storage%20architecture/Untitled%203.png)

### 🌟3-tier view: server, client에 데이터를 저장하는 DB를 따로 분리

![Untitled](03%20Storage%20architecture/Untitled%204.png)

- client가 요청을 보냈을때, DB에 접근이 필요한 경우라면, 서버에서 DB server까지 요청이 갔다가 돌아올 때까지 기다리는 시간이 필요하다
- user-interface level: user와 상호작용 하는 부분
- processing level: application의 핵심적인 기능
- data level: database나 file system과의 통신

### web search가 일어나는 과정

![Untitled](03%20Storage%20architecture/Untitled%205.png)

1. 사용자가 입력한 내용이 app server로 전단된다
2. 효과적인 query를 위해서 내용을 전처리하는 과정을 거친 후, query진행 위해 DB로 보낸다
3. DB에서 전달되는 내용이 ranking algorithm을 거쳐서 후처리된 후, HTML페이지 형태로 user에게 다시 전달된다

### Client의 cover범위

![Untitled](03%20Storage%20architecture/Untitled%206.png)

1. client는 display역활만 수행하는, terminal기반 시스템
2. UI가 독립되어 운용되는, web기반 시스템
3. 입력 form을 전달하는 형식으로 운영되는, 중간에 오류를 확인하는 form 기반 시스템
4. server는 단순하게 DB만 접속하고 나머지는 client로. 금융앱 등
5. DB의 cache가 존재하는 시스템

<aside>
😀 thin client의 장점: client입장에서 보안 관리가 더 편하고, 유지보수가 쉬우며, error의 개수가 더 적게 나타난다

</aside>

<aside>
🤨 thin client의 단점: server의 error개수가 많아지고, 멀티미디어 내용이 많다면 서버에 과부화가 걸릴 수 있다

</aside>

### Peer-to-peer architecture: 각 node들이 server의 역활과 client의 역활을 동시에 수행하는 mesh network구조

![Untitled](03%20Storage%20architecture/Untitled%207.png)

### 🌟Distributed storage systems

- RAID
- network storage: DAS/SAN/NAS

### RAID level의 결정: 데이터 오류나는 것은 중요하지 않고, 높은 성능이 요구되는 경우 RAID 0, 높은 안정성과 빠른 복구가 필요한 경우 RAID1, 데이터의 규모가 클때, RAID4, RAID5를 이용

### RAID 0: parity와 mirroring이 존재하지 않는다

![Untitled](03%20Storage%20architecture/Untitled%208.png)

- parity: 오류가 있는지 체크에 이용
- mirroring: 데이터 중복 저장
- 단순하게 read와 write만 하는 구조로, 동시에 여러 disk를 read/write할 수 있다
- disk에 접속하는 시간이 bottle neck을 유발한다
- 공간 효율: 1
- 중복이 없기 때문에 오류가 생긴 경우 복구가 불가능 하다

### RAID 1 : striping없이 mirroring만 가능

![Untitled](03%20Storage%20architecture/Untitled%209.png)

- striping: 여러 disk를 하나의 disk처럼 사용하는 것
- 같은 내용을 멀리 떨어져 있는 disk에도 써준다
- 내용이 여러개 있기 때문에 read의 속도는 향상시켜 주지만, 중복하여 write해줘야 하기 때문에, write의 속도는 저하된다
- 중복되어 저장되어 복구가 가능하다
- 공간 효율: 1/n → redundancy는 공간을 낭비시킨다

### RAID 4: block level striping + parity disk

![Untitled](03%20Storage%20architecture/Untitled%2010.png)

- line을 맞춰서 mirroring block을 정하고, parity block을 꺼내서 오류가 난다면 복구 시킴
- parity disk의 Ap에 A1, A2, A3의 내용을 저장한다
- read는 똑같지만, write의 경우 추가적으로 disk를 더 쓰는 시간이 소요된다
- parity block에 쓰이는 공간이 낭비되지만, 데이터가 안정적으로 저장될 수 있다
- 공간 효율: (n-1)/n

### RAID 5: 가장 많이 사용되는 경우. Parity부하를 분산시키기 위해 parity block을 분산시킴

![Untitled](03%20Storage%20architecture/Untitled%2011.png)

- parity정보가 disk마다 분산 되어 있다
- read는 동일하고, write의 경우는 RAID4와 동일하다
- 모든 disk가 중복하여 저장하기 때문에 write에 stress가 늘어난다
- RAID4, RAID5는 rebuild에 시간이 많이 소요된다

### Network storage

![Untitled](03%20Storage%20architecture/Untitled%2012.png)

- Direct Attached Storage(DAS): host장치에 붙어서 저장은 I/O port를 통해서 일어난다
- Network-Attached Storage(NAS): IP기반 원격 접속으로,  Remote Procedure Call을 이용하여, 네트워크를 통해서 쉽게 접근할 수 있다. 그러나 여러대가 동시에 온다면 성능이 저하됨
- Storage Area Network(SAN): 광통신, 광스위치등 private network connection을 이용하여, server가 storage들이 직접 연결된 것처럼 보이게 함. 성능은 좋지만, NAS에 비해서 더 비용이 많이 든다

### 🌟Web search application structure

![Untitled](03%20Storage%20architecture/Untitled%2013.png)

- Top-level Aggregator(TLA): 처음 보여지는 서버. Top에서 middle로 넘겨준다
- Middle-level Aggregator(MLA): MLA를 거친 이후, worker node에서 결과를 return시켜준다. Return받은 결과는 MLA에서 sorting과정을 거쳐서 위로 보내준다
- strict deadlines(SLA): 기간안에 맞춰서 결과를 주는 것
- missed deadline: deadline이후에 결과를 받는 것
- 사용료에 따라서 달라지게 할 수 있다. QOS(Quality Of Service)와 비슷하다

### 기존의 파일 시스템

- permission이 부여되지 않는다
- 파일의 메타데이터를 읽어와야하기 때문에, 하나의 사진을 접근하는데 여러번의 operation이 필요할 수 있다
    - 파일의 메타 데이터가 파일의 이름을 inode number로 바꿔줄 때, inode를 disk로 부터 읽어올 때, 파일 자체를 읽을 때 disk에서부터 메모리로 이동하여 읽힌다
- inode: 파일의 metadata의 구조체. Metadata는 형식이나 크기가 잘 바뀌지 않기 때문에 배열의 형식으로 나타낸다
- inode number: 파일의 메타데이터가 저장되어 있는 index
- hard link:  동일한 파일에 두개 이상의 이름을 가지는 것. 다른 파일명을 가지더라도 inode가 일치한다. 하드 링크는 같은 시스템 내에서만 생성할 수 있으며, 하드 링크 파일이 삭제될 경우 연결된 모든 파일이 삭제된다.
- soft link: 바로가기의 생성이 예시. 파일 디렉토리 알지 못해도 다른 경로에서 동일한 파일, 디렉토리를 참조할 수 있다. 원본을 변경하면 링크된 모든 파일에 반영된다. 바로가기의 파일 크기는 0. 소프트링크의 경우 원본을 삭제하면 링크된 파일은 삭제되지는 않지만 사용할 수 없게 된다.

### 🌟페이스북 사진 저장소의 저장 방식

![Untitled](03%20Storage%20architecture/Untitled%2014.png)

- hot zone: access가 많은
- cold zone: access가 적은
- CDN(Content Delivery Network):load balancing을 해줄 수 있다
- CDN은 방문이 많은 사진을 효과적으로 handling한다. 그러나 SNS 사이트에서는 종종 인기가 낮은 콘텐츠에 대해서 많은 request가 발생할 수 있다 - long tail
1. 페이지를 방문할 때, 사용자의 브라우저는 웹 서버로 HTTP요청을 보낸다
2. 각 이미지에 대해서 서버는 데이터를 다운로드할 위치로 브라우저를 안내하는 URL을 보낸다
3. 인기가 있는 사이트라면 URL은 CDN을 가리키는데, 캐시되어있는 경우 즉시 응답하고, 없다면 CDN은 URL을 확인하고 데이터를 받아온다
4. 이후 CDN은 캐시된 데이터를 업데이트 하고 사용자에게 이미지를 보낸다

### File system implementation

- Metadata
- 
    - directory structure: 파일을 관리하고, 파일 이름 목록(파일 ID)를 관리
    - File Control Block(FCB): 각 파일의 세부 사항, 파일 ID(리눅스에서 inode number), 파일에서 디스크 block으로 가는 mapping정보 등 파일에 대한 정보를 가지고 있다
- Data blocks: 디스크에 실제로 데이터가 저장되어 있는 block
- Directory: 파일 이름들의 list
    - 트리 형태로 나타나 상하위 directory끼리 연결시켜준다 - 가장 상위 directory는 2
    - file operation들을 지원

### 🌟Haystack photo storage architecture

![Untitled](03%20Storage%20architecture/Untitled%2015.png)

서버는 NAS로 HTTP요청을 보내고, NAS에서 반환하는 cache파일을 custom system call을 이용하여 직접 open하여 handling한다. NAS는 이미지를 분산하여 저장하는데, 이러한 cache는 잘 접속되지 않는 이미지에 대해서는 cache될 가능성이 낮기 때문에 약간의 성능 개선만 이루어진다

![Untitled](03%20Storage%20architecture/Untitled%2016.png)

페이스북은 인기있는 이미지를 제공하기 위해 CDN을 사용하고, long tail에 대해서 효율적으로 응답하기 위해서 haystack을 사용한다

- haystack directory: 요청받은 사진 & URL에 대한 논리적인 volume ID
- haystack cache: 새롭게 user에게 요청받은 사진이 cache되어 있다
- haystack store: memcached기반의 파일 인덱스
    - 논리적 볼륨 ID와 물리적 볼륨 ID를 매핑
    - key와 대체 key가 있는 물리적인 볼륨
    - 바늘을 신속하게 검색하기 위해서 디스크 동작 없이 물리적 볼륨 ID로 매핑되는 키/대체키를 사용하는 메모리 내 데이터 구조

### Memcached: 분산 메모리 캐싱 시스템

- 디스크 대신 RAM에 캐싱하여 동적 DB기반 웹사이트의 속도를 높이는데 사용됨
- 외부 데이터의 원본을 읽어야 하는 횟수 감소
- facebook memcached: photo저장 서버는 haystack store file에 있는 in-memory index를 모두 저장하고 있다. 노드당 수억의 사진이 있기 때문에 index는 메모리에 적합해야 한다

### 🌟Facebook cold storage system

- 8%의 최근에 업로드된 사진이 82%의 traffic을 사용한다 → 모두 같은 data center에 저장되어 있다면 비효율적!
- disk: rack마다 2PB의 용량을 가지며, 많은 전력을 사용하기 때문에 주기적으로 duty cycle을 이용하여 쉬는시간을 줄 수 있도록 한다
- 가장 중요한 것은 file이 날라가면 안되는 것이다. 파일을 백업하여 보존하는 것이 중요하다
- 단순하게 모든 데이터를 그대로 복제하게 된다면 복제한 개수만큼 disk가 필요하기 때문에 자원이 많이 필요하다 → erasure coding을 사용한다!!
- disk대신 blue ray를 사용하여, 로봇 팔로 데이터를 가져올때 사용
- 100GB blue-ray disk에 read-only data를 저장한다

### Erasure coding: 데이터를 여러개로 나누어 쪼개서 그 중 몇개의 조각만 이용하더라도 복원할 수 있도록 하는 것

- reed-solomon coding: 완전한 복제품이 없더라도 parity, checksum같은 것들을 이용하여 일부 쪼개진 데이터를 이용하여 복구할 수 있다
- 쪼개진 여러개의 데이터를 여러 disk로 나누어 저장하면 에너지, 공간을 절약하고, 더 많이 망가지더라도 잘 복구할 수 있다

### 🌟Hadoop File System(HDFS): 아주 큰 data set을 저장하기 위해 만든 분산 저장 시스템. 안정성과 높은 속도를 제공한다.

- 여러 machine에 파일을 중복시켜 저장한다 → 모두 망가지는 경우가 아니라면 데이터 손실 x
- 구글의 파일 시스템과 유사한 오픈소스 SW로, 구글의 file system, map reduce, big table의 논문들로 부터 시작되었다
- 구글의 file system역활은 Hadoop Distributed File System에서 수행한다
- 구글의 map reduce역활은 JobTracker와 TaskTracker가 진행한다
- BigTable DB는 HBase가 수행한다

### Commodity hardware: 2-level구조로 구성된다

![Untitled](03%20Storage%20architecture/Untitled%2017.png)

- uplink rack은 더 빠르게, 중간 rack은 조금 더 드리게 만들어준다
- 노드는 주로 일반 PC로 사용한다
- 20~40개의 노드와 rack으로 구성된다

### HDFS 구조

![Untitled](03%20Storage%20architecture/Untitled%2018.png)

- PC는 block의 크기를 page의 size와 일치시켜 만들어 주지만, HDFS는 공간의 낭비가 있더라도 block의 크기를 크게, 64mb로 만든다
- 중복 파일을 여러곳으로 흩어지게 저장한다

![Untitled](03%20Storage%20architecture/Untitled%2019.png)

1. client는 이름만 알고 요청을 보낸다
2. NameNode는 metadata의 정보를 가지고 있다. File을 file ID와 연결하여 mapping해준다
3. secondary namenode는 namenode의 log를 동시에 저장하면서 shadowing을 하고 있다. NameNode가 문제가 발생하는 경우, secondary namenode가 대체를 하는 것은 불가능하다. 
4. datanode는 block-id를 디스크의 물리적 주소에 저장하고 있다

### HDFS read / write / error recovery

- read: 파일의 위치, 혹은 파일의 정보를 NameNode가 알려준다
    
    ![Untitled](03%20Storage%20architecture/Untitled%2020.png)
    
- write: 중복된 파일에서 consistency문제가 발생할 수 있기 때문에 client는 first datanode로 write하고, datanode는 데이터를 다음 datanode로, 또 다음 노드로 보낸다. Replica에 모두 쓰여지면, client는 써야하는 다음 block으로 넘어간다. Latency가 중요한 app에서는 사용할 수 없다
    
    ![Untitled](03%20Storage%20architecture/Untitled%2021.png)
    
- error recovery: error가 발생했다고 report하고, backup 데이터에 새롭게 linking해주고, 새로운 백업 파일을 만들어준다
    
    ![Untitled](03%20Storage%20architecture/Untitled%2022.png)
    

### NameNode metadata

- meta-data in memory: 메타데이터 전체는 메인메모리에 있다. Paging에 대한 요청이 없다(disk로 swap되지 않음)
- 메타데이터의 종류: file의 리스트. File, 파일 구성 요소의 block들의 리스트
- transaction log: 파일 생성, 파일 삭제 등의 기록

### DataNode

- block server: local file system에 데이터 저장, block에 대한 meta-data저장, client에게 메타데이터와 데이터를 제공, checksum의 정기적인 유효성 검사
- block report: 현재 존재하는 block에 대한 report를 주기적으로 NameNode에 제공
- data pipelining제공: 특정된 데이터노드에 데이터를 제공
- Heart beats: data node는 heart beat를 보내는데, 매 10번째 heart beat는 block report이다. 이 block report를 이용하여 NameNode가 metadata를 만든다

### Rack awareness: rack에 대한 정보를 가지고 있으면, 전송속도를 향상시키고, 데이터 안정성을 보장한다

- 하나의 block의 복제본을 둘 이상 포함하는 datanode는 존재하지 않는다
- 충분한 rack이 존재하는 경우, 동일한 block의 복제본을 두 개 이상 포함하는 rack은 없음

### Block placement: 복제품을 어디에 배치하게 되는지. Client는 가장 가까운 복사본을 이용한다

- 첫번째 복제본은 local node에
- remote rack에 두번째 복제
- 같은 remote rack에 세번째 복제
- 이후 추가적인 복제본은 random하게 배치됨

### AvatarNode: 페이스북에서 개발한 기술로, Hadoop cluster에서 데이터처리 향상 위해 사용

- NameNode에서 오류가 발생한다면 local file system, 혹은 remote file system에서 저장된 transaction log를 이용한다
- 안정성 위해서 namenode를 여러개 운영한다. active - standby가 pair로 묶여서 share하고 있다가 오류나면 바로 standby가 실행된다
- active한 node가 transaction log를 NFS에 써주고, standby는 그것을 읽어서 memory에 최신 metadata를 반영한다
- distributed lock: 데이터 처리 병렬화를 위해 사용. 다수의 client에 대해 데이터 일관성 유지 위해사용

### Hadoop balancing: cluster balancing(load balancing)을 해준다

- client가 HDFS로부터 reading하는 경우, 각 block에 대해서 datanode를 받아오고, 각 block에서 첫 data node를 골라준다. 이후, block을 순차적으로 읽는다
- rebalancing: namenode에 의해서 수행된다. 먼저 불균형한 데이터의 위치를 파악하고, 분산시켜주기 위해서 새로운 노드로 데이터를 이동시키거나, 다른 노드로 이동시킨다
- rebalancing의 단점: access pattern이나 load기반으로 수정이 이루어지는 것이 아니고, hot spot 데이터 자동처리를 지원하지 않는다

### HDFS RAID: 데이터의 안정성과 신뢰성을 보호하기 위한 기술

- 데이터 블록을 여러 개의 디스크에 복제하여 줌
- 기본적으로 3개의 복제본을 저장
- 각 파일의 3번째 복제본을 parity disk로 옮겨오고, 세번째 복제본은 삭제한다
- RaidNode: replica가 오류가 난다면 자동으로 고쳐준다
- Reed Solomon encoding: 오래된 파일에 대해서 오류가 있는지 확인하게 된다