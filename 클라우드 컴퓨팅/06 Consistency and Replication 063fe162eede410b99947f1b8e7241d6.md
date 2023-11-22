# 06. Consistency and Replication

### 🧙‍♂️Replication: 데이터 혹은 서비스를 여러개의 copy를 만들어주는 것. 같은 데이터가 여러개의 device에 나누어 저장된다

- 장점
    - 신뢰성 향상: 문제가 생기더라도 데이터 복구가 가능하다
    - 성능 향상(scalability): 사용자가 많아짐에 따라 server를 여러개 두어 work를 분산시킬 수 있다. 지리적 영역이 증가되어 접근 시간이 단축되어 proximity 향상
- 문제점 - consistency problem: copy를 계속 늘리는 것은 불가능 하다. Replica가 늘어나면 write하는 만큼 업데이트해야 하기 때문에 cost가 올라가 performance가 내려가게 된다
- 웹페이지: 한번 load한 이후에는 local에 저장해놓아 다시 접속하는 경우 loading시간이 이전보다 짧다. 사이트에 업데이트가 생기더라도 내용이 바뀌지 않는 inconsistency문제가 발생할 수 있다
    - conditional get: cache는 새롭게 내용이 업데이트 된 경우에는 이전 내용을 건내주지 않는다
    - forward web proxy: HTTP request를 보내는 경우, proxy에게 요청을 보낸다. Proxy는 caching을 하고 있기 때문에 cache가 있다면 바로 return, 없는 경우에는 직접 요청을 처리한다. Proxy는 근처에 있기 때문에 반응 시간을 더 빠르게 해주며, traffic을 분산시켜준다
    - reverse web proxy: client의 요청을 받아 서버로 전달하는 역활로, reverse proxy server가 요청을 받아 web server로 전달하게 된다
        - 성능 최적화: 검색 결과를 caching. 자주 사용되는 command caching
        - load balancing: web server를 load balancing해줌
        - 추가적인 보안
- caching의 효율성: cache가 있어서 바로 return해줄 수 있는 경우(cache hit rate)보다 update해야 하는 경우(miss rate)가 많은 경우 비효율적이다. Network전송 속도를 높일 필요가 있다
- tight consistency: 모든 사용자는 반드시 같은 내용을 읽을 수 있어야 한다. Update가 생기는 경우, 바로 반영한다. Update는 모든 copy에 대해 single atomic operation으로 진행된다
- consistency vs. overhead: replication이 너무 많아지면 consistency문제가 발생하고, consistency 유지시키는 것은 cost가 들기 때문에 performance를 저하 시킨다. Performance를 너무 높이다 보면 global synchromization이 낮아지게 되는 dilema

![Untitled](06%20Consistency%20and%20Replication%20063fe162eede410b99947f1b8e7241d6/Untitled.png)

### Data store: 공유되는 저장소인 distributed shared memory(💯DSM), database, file system등에서 read, write 작업을 수행한다

- 물리적으로 여러가지 기계들로 분산되어 저장되어 있다
- 원본 파일 제외하고, 복사본이 존재하기 때문에 다른 복사본들로 write를 하도록 전파되야 한다

### 🧙‍♂️Consistency models: data store과 client들 사이의 계약

- 규칙에 따라서 얼만큼의 consistency룰 유지시킬 것인지 결정한다
- 그림에서 시간은 수평축이고, 왼쪽일수록 이전 시간, 오른쪽 일수록 최근 시간이다
- W(x)a: item x에 값 a를 써준다
- R(x)b: item x에 값을 읽어주는데, return되는 값은 b이다
- tight → sequential → casual → FIFO순으로 점점 loose해진다

### Tight consistency: 값이 업데이트 된 이후에는 무조건 최신 데이터의 내용을 업데이트 해야 한다. 구현은 쉽지만 performance가 낮아진다

![Untitled](06%20Consistency%20and%20Replication%20063fe162eede410b99947f1b8e7241d6/Untitled%201.png)

- 실제로 write를 하자 마자 반영되는 것이 아니라, 약간의 delay가 발생할 수 있기 때문에 약간의 inconsistency가 발생할 수 있다

### Sequential consistency: 실제 순서가 중요한 것이 아니라, 모든 경우에 순서가 똑같기만 하면 된다

![Untitled](06%20Consistency%20and%20Replication%20063fe162eede410b99947f1b8e7241d6/Untitled%202.png)

- 이 경우, p1과 p2의 순서에 상관 없이 p3, p4의 순서가 b→a로 동일하게 나오기 때문에 sequentially consistent

![Untitled](06%20Consistency%20and%20Replication%20063fe162eede410b99947f1b8e7241d6/Untitled%203.png)

- 이 경우 p1과 p2의 순서에 상관 없이, p3와 p4의 순서가 서로 다르기 때문에 sequentially inconsistent

### Casual consistency: sequential보다 loose. Casually related된, 순서를 파악할 수 있는 write는 read의 순서가 맞아야 하고, 순서를 파악할 수 있는 write인 concurrent write의 경우에는 순서가 달라도 상관 없다

![Untitled](06%20Consistency%20and%20Replication%20063fe162eede410b99947f1b8e7241d6/Untitled%204.png)

- p1과 p2에서 write가 이루어 졌는데, a와 b의 경우 p1에서 write를 한 이후에 p2에서 a를 read했고 이후에 b에 write를 했기 때문에 casually related write다. 또한 같은 p1에서 a를 쓴 이후에 c를 썼기 때문에 이 경우 또한 casually related write이다. 그러나 p2에서 쓴 b와 p1에서 쓴 c의 경우에는 순서를 파악할 수 없기 때문에 concurrent write로 볼 수 있다
    - 이 경우에서 a → c, a → b의 경우를 만족해야 한다. P3의 경우는 만족하고, p4의 경우 역시 두 경우 만족하기 때문에 casually consistent하다고 볼 수 있다
    - p1과 p2의 경우에는 a를 쓴 이후에 p2에서 read를 했고, 이후에 b를 썼기 때문에 casually related여서 a → b순서를 만족해야 한다. P3의 경우에 이를 만족하지 못하기 때문에 casually inconsistent하고, p3와 p4의 경우 모두 순서가 동일하지 않기 때문에 sequentially inconsistent하다
        
        ![Untitled](06%20Consistency%20and%20Replication%20063fe162eede410b99947f1b8e7241d6/Untitled%205.png)
        
    - p1과 p2는 a와 b의 순서를 파악할 수 없기 때문에 concurrent write이다. 따라서 이 경우 p3와 p4모두 a b의 순서가 상관 없다. 따라서 casually consistent하다. 그러나 p3, p4에서 순서가 모두 다르기 때문에 sequentially inconsistent하다
        
        ![Untitled](06%20Consistency%20and%20Replication%20063fe162eede410b99947f1b8e7241d6/Untitled%206.png)
        

### FIFO consistency: 가장 loose한 경우로, 같은 process에서 쓴 경우만 순서가 맞으면 되고, 다른 process에서 쓴 경우의 순서는 상관 없다

![Untitled](06%20Consistency%20and%20Replication%20063fe162eede410b99947f1b8e7241d6/Untitled%207.png)

- 같은 process에서 쓴  b → c의 순서만 맞으면 되고, a의 순서는 상관 없다. P3와 p4의 경우 모두 b → c를 만족하기 때문에 FIFO consistency를 만족한다. 순서가 다르기 때문에 sequential을 만족하지 않고, casual의 경우 a→b→c를 만족해야 하기 때문에 p3때문에 casual inconsistent하다

### 🧙‍♂️Client centric consistency models: client의 관점에서 client에 따라 다르게 . Single client에 대한 내용을 보장하며, 서로 다른 client가 동시에 접근하는 것은 보장하지 못한다.

- xi: copy Li에서 x의 item 버전
- W(xi ; xj): write에 대해서 순서를 파악할 수 있고, w(xi) → w(xj)순서
- W(xi | xj): write에 대해 누가 먼저인지 모르게 concurrent하게 발생한 write

### Eventual consistency: update가 장기간동안 일어나지 않는다면, 결국 모든 replica들은 알아서 consistent하게 될 것이다

- write가 거의 없이 read가 많은 경우거나, concurrent한 update가 많이 없는 경우에 유리하다
- DB system은 update가 거의 없고 대부분 read하는 경우가 많기 때문에 이러한 방식이 유리하다
- web cache의 경우, 웹페이지의 update는 webmaster에 의해 이루어지는데, 실시간으로 update안되더라도 상관 없기 때문에 이 방식이 유리하다
- client에서 항상 같은 replica를 접근하는 경우는 상관 없지만, 만약 다른 replica를 접근하게 되는 경우 문제가 발생할 수 있다
    
    ![Untitled](06%20Consistency%20and%20Replication%20063fe162eede410b99947f1b8e7241d6/Untitled%208.png)
    
    - 특정 지점에서 업데이트 한 이후, A에 반영이 되었을때, 이후 update내용이 전달되지 않은 상태에서 다른 지점에서 업데이트가 되지 않은 B에 접속하였을 경우, 문제가 발생할 수 있다
    - B에서 읽더라도 A와 동일한 상태여야 한다

### Monotonic-read consistency: client가 data를 읽는 경우, 항상 같은 value, 혹은 더 최신 내용을 return해준다. 즉, client가 value를 확인한 이후에는, 이전 version을 확인하지 않는다

- 분산 이메일 DB의 경우, 접속해서 이메일을 확인한 이후, 다른 곳에서 다시 접속해도 동일한 내용을 보여주어야 하며, 새로운 메일이 온 경우라면 새로운 메일을 추가하여 보여주어야 한다
- 이 경우는, write의 순서가 확실하기 때문에, x1이후에 x2오는 것이 적절하다
    
    ![Untitled](06%20Consistency%20and%20Replication%20063fe162eede410b99947f1b8e7241d6/Untitled%209.png)
    
- write의 순서가 확실하지 않기 때문에 어느 것이 newer인지 확인할 수 없다. 따라서 monotonic-read consistenct를 보장하지 못한다
    
    ![Untitled](06%20Consistency%20and%20Replication%20063fe162eede410b99947f1b8e7241d6/Untitled%2010.png)
    

### Monotonic-write consistency: write를 기준으로 순서를 파악하는 것으로, write operation은 같은 client의 다른 write작업이 끝난 이후에 진행된다

- FIFO와 유사하게, write operation은 모든 replica에서 동일해야 한다
- write operation은 client사이에서 모두 동일해야 하는데, 각 x1, x2, x3의 순서가 명확하기 떄문에 monotonic-write consistency가 성립한다
    
    ![Untitled](06%20Consistency%20and%20Replication%20063fe162eede410b99947f1b8e7241d6/Untitled%2011.png)
    
- write의 순서 모르면 monotonic이 유지되지 않는다.예시는 monotonic write이 보장되지 않는다
    
    ![Untitled](06%20Consistency%20and%20Replication%20063fe162eede410b99947f1b8e7241d6/Untitled%2012.png)
    

### Read-your-writes consistency: 같은 client에서 write된 내용은 이어지는 read에서 내용에 update되어야 한다

- 모든 write는 이어지는 read가 이루어지기 전에 완료되어야 한다
- cache는 항상 update된 내용을 반영하여 가장 최신 버전임을 보장해야 한다
- 최신 write를 기준으로 x1 → x2이기 때문에 read를 하면 x2가 나와야 한다.
    
    ![Untitled](06%20Consistency%20and%20Replication%20063fe162eede410b99947f1b8e7241d6/Untitled%2013.png)
    
- x1과 x2는 concurrent하게 쓰였기 때문에 write가 끝난 이후 write가 이루어진 것이 아니라 x1 업데이트 내용이 없기 때문에 read-your-write이 보장되지 않는다
    
    ![Untitled](06%20Consistency%20and%20Replication%20063fe162eede410b99947f1b8e7241d6/Untitled%2014.png)
    
- read-your-writes를 보장하려면 두 read모두 x2여야 한다. 따라서 read-your-writes consistency를 만족하지 못한다. Monotonic read는 만족한다
    
    ![Untitled](06%20Consistency%20and%20Replication%20063fe162eede410b99947f1b8e7241d6/Untitled%2015.png)
    

### Write-follow-reads consistency: item x에 대한 write는, 가장 최근에 read되었던 내용과 동일하거나, 그 내용에서 추가된 내용에 write를 하게 된다

- 읽어온 가장 최신 값에서 추가된 내용인 x3이후에 x3를 쓰게 되었기 때문에 write-follow-reads consistency를 만족한다
    
    ![Untitled](06%20Consistency%20and%20Replication%20063fe162eede410b99947f1b8e7241d6/Untitled%2016.png)
    
- 읽어온 최신 값에서 x1과 x2의 선후 관계를 모르기 때문에 x3를 추가로 쓰는 것은 write-follow-reads consistency를 보장하지 못한다
    
    ![Untitled](06%20Consistency%20and%20Replication%20063fe162eede410b99947f1b8e7241d6/Untitled%2017.png)
    

### Client centric은 client가 원하는것에 집중하여 시스템 전체의 일관성을 방지할 수 있다. Consistency요청을 relax하고, single client-view로 보자

### 🧙‍♂️Replica management

### Content replication: 세개의 동심원 링으로 논리적으로 구성된다

![Untitled](06%20Consistency%20and%20Replication%20063fe162eede410b99947f1b8e7241d6/Untitled%2018.png)

- static replicas
    - permanent replicas: 초기 replica의 set으로 분산 데이터의 저장소를 구성한다. 원본과 똑같이 복사하며, 위치는 바뀌지 않는다. Web site mirroring, distributed DB와 같은 경우
- dynamic replicas
    - sever-initated replica: 동적으로 서버의 요청에 따라 움직여지는 process. Content Distribution Network(CDN), reverse web proxy등이 있다
        - 외국에서 요청이 너무 많다면 해당 국가에 서버를 구축하고 사본을 옮겨서 제공
        - 서버에 대한 load를 줄이고, 파일이 client근처로 이동하여 더 빠르게 이용할 수 있다
        - 통으로 복사하지 않고, 알고리즘을 이용하여 특정 파일을 골라서 복사하게 된다
            - cnt(S, F)를 이용하여 F에 대해 server S에서 접근하는 횟수를 파악한다
            - threshold값을 설정해두고, del(S, F)이하로 값이 떨어지게 되는 경우, 복제본을 삭제하고, rep(S, F)값보다 더 개수가 많아지는 경우, 복제본을 새로 만들게 된다
            - 이 모든 과정은 서버에 의해 관리된다
                
                ![Untitled](06%20Consistency%20and%20Replication%20063fe162eede410b99947f1b8e7241d6/Untitled%2019.png)
                
            - A의 count를 파악하고, 이외에도 각기 다른 지역에서 각각의 파일의 요청 횟수를 파악하고, threshold값을 설정해둔다. 요청수, 거리 등을 종합적으로 판단한다
    - client-initaed replica: 동적으로 사용자의 요청에 따라 움직여지는 process. Web cache, web browser cache같은 것들이 있다
        - client cache로 알려져있고, client의 machine이나 혹은 개별적인 machine에서 client에 의해 관리 받는다

### Content distribution: 업데이트된 컨텐츠를 replica server로 propagate

- model: client-server combination에 대해서만 고려한다
    - 업데이트가 무효화가 된 경우에만 전파: 수정된 데이터의 규모가 크고, read 빈도수 적다
    - 수정된 데이터를 하나의 copy부터 다른 부분으로 전송: read가 write보다는 더 많이 발생한다
    - update operation을 다른 copy로 복사: bandwidth와 processing power를 고려해야 한다
- observation: 하나의 솔루션이 베스트로 볼 수 없다. Bandwidth와 read-to-write ration에 의해서 결정된다고 볼 수 있다
- pushing update: 서버에서 업데이트가 진행되는 것으로 target의 요청과 상관 없이 update된다. Consistency가 높으며, 많은 client가 이용하고, read-to-update ration가 많은 경우에 유리
- pulling update: client에서 업데이트를 진행하는 것으로 client가 요청했을때 update가 일어난다. Client cache에서 주로 일어나며, read-to-update ration가 낮을때 유리

### 🧙‍♂️Consistency protocols: 복사본들끼리 consistency를 어떻게 맞출지

### Primary-backup protocol: 하나의 서버가 책임지는 것

- 모든 write operation은 하나의 서버, 즉 primary server로 forwarding된다
- 전통적으로 distributed DB나 file system에서 이용된다
    
    ![Untitled](06%20Consistency%20and%20Replication%20063fe162eede410b99947f1b8e7241d6/Untitled%2020.png)
    
    - write request가 오면 primary가 모두 중지시킨 이후, update가 완료된다면, ACK을 받고 이후에 자원들을 다시 풀어주게 된다
- 단점: client는 write이 모두 끝날 때가지 계속 기다려야 한다 → 대안: nonblocking approach
- blocking vs. non-blocking
    - write speed는 non-blocking이 더 좋다
    - 업데이트중 오류가 나거나, sequential consistency, tight consistency의 경우에도 blocking approach는 데이터 일관성을 보장할 수 있다

### Quorum-based protocols: 분산 형식의 protocol. 쓰거나 읽어도 되는지 permission을 받아야 하는 시스템

- 파일은 N개의 서버에 분산되어 있다
- read를 하는 서버 Nr, write를 하는 Nw가 존재한다
- write(update): client는 N개의 서버 중에서 과반수에서 okay를 받아야 update를 진행할 수 있다
    - 만약 5개중 3개에서 okay를 받아서 version2로 업데이트 된 경우, 나머지 2개의 서버는 아직 version1으로 인식하고 있다
- read: Nr + Nw > N을 만족하는 Nr개에게 물어본다. 이때 서버들이 return으로 version1, version2 이렇게 return하는 경우 version2를 최신으로 인식하면 된다
    
    ![Untitled](06%20Consistency%20and%20Replication%20063fe162eede410b99947f1b8e7241d6/Untitled%2021.png)
    
- 다음과 같이 Nr + Nw > N이면 반드시 최신 버전이 한개 이상은 겹치게 된다
- Nw > N/2: 동시에 여러명이 write할 수 없다
- ROWA(Read-one, Write-all): 다 끝나야만 read가능