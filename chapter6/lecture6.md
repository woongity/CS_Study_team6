### 다중 프로그래밍 시스템

~~~
  병행 수행중인 비동기적 프로세스들이 공유 자원에 동시에 접근하게 되면 문제가 발생할수 있다!
~~~
![image1](스크린샷%202021-09-09%20오후%205.33.42.png)
시나리오 1) 명령 수행 과정 1,2,3,A,B,C result = 2
시나리오 2) 명령 수행 과정 1,2,A,B,C,3 result = 1

- 동기화 
  -  프로세스들이 서로 동작을 맞추는 것.
  -  프로세스들이 서로 정보를 공유하는 것.

- 용어 정리
  - 비동기적 : 프로그램의 진행이 순서대로 일어나지 않음. 프로세스들이 서로에 대해서 모름
  - 병행적 : 여러개의 프로세스들이 동시에 시스템에 존재.
  - 공유 데이터(shared data) : 여러 프로세스들이 공유하는 데이터
  - 임계 영역(critical section) : 공유 데이터를 접근하는 코드 영역
  - 상호 배제(mutual exclusion) : 둘 이상의 프로세스가 동시에 critical section에 진입하는 것을 막는것.

- SW 해결 방법
  - 단점 : 느리고, 구현이 복잡, ME primitive 실행중 preemption 발생 가능 -> overhead 발생, busy waiting.
  - 기본 연산(mutual exclusion primitive)
    - enter cs() : critical section 진입 전 검사(다른 프로세스가 critical section에 있는지 검사)
    - exit cs() : critical section을 벗어낼때의 후처리과정

  - Dekker's Algorithm
  ![image2](스크린샷%202021-09-09%20오후%207.04.25.png)
  1. 상대방 깃발이 내려가 있다면 바로 CS로 들어감
  2. 둘다 깃발을 들고있다면, 내 턴인지 확인 내턴이 아니라면 깃발을 내림. 그리고 내 턴을 기다림.

  - Peterson's Algorithm
  - ![image3](스크린샷%202021-09-09%20오후%205.33.42.png)
  - 상대편 턴이고, 깃발을 들고있으면 양보. 내 턴이라면 그냥 들어감.
  
  - n-process mutual exclusion
    - dijkstra's Algorithm
      - flag
        - idle : 프로세스가 임계 지역 진입을 시도하고 있지 않을때
        - want-in : 프로세스의 임계지역 진입 시도 1단계일때
        - in-cs : 프로세스의 임계 지역 진입 시도 2단계 또는 임계 지역 내에 있을때
        ![image4](스크린샷%202021-09-09%20오후%207.22.25.png)
        - 임계 1 ,2 로 나누어서 구성.
  -HW 해결 방법
    - SW 해결 방법을 위해서 등장. 하지만 busy waiting(while 돌면서 기다리는 것은 해결하지 못함.)
    - HW적으로는 이전값 기록, tru로 설정, 값 변환을 preemption 없이 해결가능.
    - 방법 
    - ![image](스크린샷%202021-09-09%20오후%207.53.52.png)
      - 3개 이상의 프로세스의 경우 한명이 계속 들어가지 못하는 경우가 발생하기도 함. -> 한명이 나가고 다른 애가 들어갈때 한명이 계속 밀리는 경우가 발생 가능.
      - 솔루션 
        - 기다릴지 말지를 결정하는 waiting 배열을 통해서, 기다리는 애중 나와 가장 가까운 애를 임계 영역에 진입할수 있도록 해줌.

  - OS Supported SW solution
    - 방법 
      - spinlock
        - busy waiting 문제를 여전히 해결하지 못함. 
        - 문제점 : 멀티 프로세싱에서만 사용가능.
        - 정수 변수 사용, 하지만 atomic 연산인 초기화, p(),v() 연산으로만 접근이 가능
      ~~~
        p(s){
          while(S<=0) do
          endwhile
          s = s-1;
        }
        // 물건을 꺼내는 연산
      ~~~
      ~~~~
      V(S) {
        S = S+1
      }
      // 물건을 집어 반납하는 연산.
      ~~~~
      ![image](스크린샷%202021-09-09%20오후%209.26.57.png)
      
      - Semaphore
        - busy waiting 문제를 해결
        - 음이 아닌 정수형 변수 사용. 하지만 atomic 연산인 초기화, p(),v() 연산으로만 접근이 가능
        - 임의의 s변수 하나에 ready queue 하나가 할당이 된다.
        - 기다리는 게 아니라 큐에 담아서 계속해서 기다림.
        - 운이 나쁜경우 , 계속해서 할당 받지 못하는 starvation 문제가 발생할수 있다.
          ~~~
          p(s){
            // 물건이 있다면  
            if(s>0)
              then s = s-1;
            // 물건이 없다면 while문 돌지말고, queue에서 기다림.
            else wait on the queue Q;
          }
          ~~~

          ~~~
            v(S){
              // 기다리는 프로세스가 존재한다면
              if(waiting processes on Q){
                // 일시킴.
                then wakeup one of them
              }
              // 기다리는 프로세스가 없다면 물건을 하나 돌려놓고 감.
              else S = S+1;
            }
          ~~~
        - 종류
          - binary semaphore : s가 0과 1두종류의 값만을 가짐. 상호배제나 프로세스 동기화의 목적으로 사용

          - counting semaphore : s가 0이상의 정수값을 가짐. producer-consumer 문제를 해결하기 위해서 사용.

        - semaphore로 해결 가능한 동기화 문제들
          - 상호배제 문제
            ![image](스크린샷%202021-09-10%20오전%2012.21.36.png)
          - 프로세스 동기화 문제
            - 프로세스들의 실행 순서 맞추기
            ![image](스크린샷%202021-09-10%20오전%2012.23.19.png)
             
          - 생산자 소비자 문제
          ![image](스크린샷%202021-09-10%20오전%2012.35.51.png) 
            - 생산자 : 메세지를 생성하는 프로세스 그룹.
            - 소비자 : 메세지를 전달받는 프로세스 그룹.
            - 생성자가 버퍼로 생산을 하는 동안에 소비자가 버퍼에서 읽어 갈수 없음.
          ![image](스크린샷%202021-09-10%20오전%2012.43.23.png)

          - reader/writer problem
           ![image](스크린샷%202021-09-10%20오전%2012.56.26.png) 
            - reader : 데이터에 대해서 읽기 연산만 수행, reader들은 동시 데이터 접근이 가능.
            - writer : 데이터에 대해 갱신 연산을 수행, writer들 또는 reader와 writer이 동시에 데이터 접근시 상호배제가 필요
           - reader / writer에 대한 우선권 부여가 필요. -> reader가 읽고 있다면 writer는 작업하지 않도록 구성.
  - EventCount/ Sequencer
    - no busy waiting, no starvation, low-level control 가능
    - semaphore의 starvation을 막기위해 도입. 번호표와 비슷한 개념
    - sequencer
      - 정수형 변수
      - 생성시 0으로 초기화, 감소하지 않음.
      - 발생 사건들의 순서 유지, ticket() 연산으로만 접근 가능.
        - ticket 연산 : 지금까지 ticket()연산이 호출된 횟수를 반환.
    - event count
      - 정수형 변수, 생성시 0으로 초기화, 감소하지않음
      - 특정 사건의 발생 횟수 기록. 
      - read, advance, await 연산만으로 접근 가능.
        - read(E) 연산 : 현재 event count값 반환 , 번호표를 뽑는 행위와 유사
        - advance(E) 연산 : e = e+1 ,e를 기다리는 프로세스를 깨움, 은행원이 번호표 벨을 누르는 행위와 유사
        - await(E,v) 연산 : v는 정수형 변수, e< v라면 가다림. 번호표를 기다리는 행위와 유사.


  
  - monitor(language-level construct)
    - 장점 : 사용이 쉽다. dealock 발생 가능성이 낮음
    - 단점 : 지원하는 언어에서만 사용가능, 컴파일러가 os를 이해하고 잇어야 함.
    - ![image](스크린샷%202021-09-10%20오전%207.36.24.png)
    - monitor : CS와 critical data를 모아놓은 하나의 방
    - 구조 
      - 진입 큐 : 모니터 내의 function 수만큼 존재
      - mutual exclusion : 모니터 내에는 항상 하나의 프로세스만 진입 가능
        - 정보 은폐 : 공유 데이터는 모니터 내의 프로세스만 접근 가능
        - 조건 큐 : 모니터 내의 특정 이벤트를 기다리는 프로세스가 대기
        - 신호제공자 큐 : 모니터에 항상 하나의 신호제공자 큐가 존재. 모니터에서 빠지면서 다음 프로세스로 넘겨줄때, 모니터안에는 1개의 프로세스밖에 접근이 안되므로, 신호 제공자 큐로 이동후, 깨워준다.
      - 문제 해결
        - 자원 할당 문제
        ![image](스크린샷%202021-09-10%20오전%207.49.41.png) 
        ~~~
          // 대출
          procedure requestR(){
            if(R_available) then
              R_free.wait();
            R_Available = false;
          }
          // 반납
          procedure releaseR(){
            R_Available = true
            R_free.signal();
          }
        ~~~

        - producer - consumer problem
          ![image](스크린샷%202021-09-10%20오전%207.56.39.png) 
          ~~~
              //producer
              procedure fillBuf(){
                  // 큐가 꽉참
                if(물건수 == N) 기다림
                else{
                  물건 넣기
                  물건 갯수 +1
                  in = (in+1)mod N //다음 물건 놓을 위치 설정
                  vufHasData.signal() // 물건을 기다리는 애를 깨워줌
                }
              }
              // consumer
              procedure emptyBuf(){
                if(물건수 == 0) 기다림
                else{
                  물건 꺼냄.
                  물건 갯수 -1
                  물건 위치 설정
                  다음 물건 놓을 위치 설정
                  공간을 기다리는 애를 깨워줌
              }
          ~~~
        - Dining philosopher problem
          ![image](스크린샷%202021-09-10%20오전%208.16.47.png) 
          - 조건 
            - 공유 자원 : 스파게티(shared data), 포크(process)
            - 철학자들은 생각, 스파게티 먹는 일만 반복.
            - 스파게티를 먹기 위해서는 좌우 포크를 두개 들어야함
            ~~~
              //포크를 주움
              function pickup(){
                if (!내 포크가 2개) 기다림
                else {
                  포크를 집음. 
                  옆 사람이 집을수 있는 포크 갯수를 줄임.
                }
              }
              // 포크를 내려놓음
              function putdown(){
                옆 사람 포크 +1
                if(옆 사람 포크가 2개) 깨워줌
              }
            ~~~