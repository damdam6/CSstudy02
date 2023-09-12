## 3.4 CPU 스케줄링 알고리즘

- 왜 CPU 스케줄링이 필요할까?
  - **CPU Burst가 짧은 I/O bound job이 섞여있기 때문에 CPU 스케줄링 필요**
  - CPU Burst : CPU 작업을 연속적으로 사용하는 시간
  - I/O Burst : I/O 작업을 연속적으로 사용하는 시간
  - I/O bound job - 사용자와 interactive한 작업 (I/O Burst 많음)
  - CPU bound job - 계산 위주의 job (CPU Burst 위주)

##### Scheduling Criteria (성능척도)

- **System 입장에서의 성능척도**
  - **CPU utilization (이용률)** : CPU가 얼마나 쉬지 않는지
  - **Throughput (처리량)** : 같은 시간에 얼마나 많은 업무를 처리했는지
- **프로세스 입장에서의 성능척도**
  - **Turnaround Time (소요 시간)** : 프로세스가 CPU를 쓰기위해 들어와서 나갈때까지의 총 시간
    Waiting Time + CPU 사용한 Time
  - **Waiting Time (대기 시간)** : CPU가 ready queue에서 대기한 총 시간
  - **Response Time (응답 시간)** : 최초로 CPU를 얻기까지 기다린 시간
    프로세스 완료기준이 아닌 CPU Burst 기준으로 시간/업무를 측정

### 3.4.1 비선점형 방식 (non-Preemptive)

- 프로세스가 스스로 CPUㅇ를 포기하는 방식으로 강제로 프로세스를 중지하지 않음
- 문맥교환에 의한 부하가 적음

##### FCFS(First Come First Served)

- Convoy Effect : 우연히 짧은 순서대로 일을 처리하게 되었다면 수행능력이 매우 차이가 나게 된다. 하지만 그 반대는 매우 안좋음

##### SJF(Shortest Job First)

- 실행시간이 가장 짧은 (CPU Burst가 가장 짧은) 프로세스를 가장 먼저 실행하는 알고리즘
- Average Waiting Time을 최소화 할 수 있다.
- Preemptive(SRF) / Nonpreemptive(SJF)로 나눌 수 있음
- 단점 :
  1. [Starvation] CPU Burst가 가장 긴 프로세스는 계속 진행되지 않음
  2. 정확한 CPU Burst를 알 수 없음 (과거 CPU 사용 정도를 통해 예측은 할 수 있음 [Exponential averaging])

##### 우선순위

- 우선순위에 따라 스케줄링 (일반적으로 작을수록 higher priority)
- Preemptive / Nonpreemptive로 나눌 수 있음
- 단점 : 역시 기아 현상 (starvation) 발생
- 해결법 (Aging) : 오래 기다리게 되면 우선순위를 조금씩 높혀줌

### 3.4.2 선점형 방식 (Preemptive)

- 현대 운영체제가 쓰는 방식
- 지금 사용하고 있는 프로세스를 알고리즘에 의해 중단시켜버리고 강제로 다른 프로세스에 CPU 소유권을 할당하는 방식

##### RR (RoundRobin)

- 일정한 크기의 할당 시간(time quantum, q)마다 CPU를 사용하고 다시 ready Queue에서 대기 (우선순위 큐)
- 특징
  - CPU Burst 길이에 비례하여 대기 시간이 길어진다
  - 어떠한 Process도 (n-1)q time unit 이상 기다리지 않는다
- 장점 : **응답시간이 매우 빨라짐**, 예측 알고리즘 필요 X
- 단점 : q가 너무 크면 FCFS 와 같아지고 q가 너무 작아지면 **context switching overhead**가 커짐 + **Turnaround time이 길어짐**
- 로드밸런서의 트래픽 분산 알고리즘으로 사용되기도

##### SRF

- SJF의 선점형 방식

##### 다단계 큐 (Multilevel Queue)

Ready Queue를 여러 개로 계층분할, 각 queue는 독립적인 스케줄링 알고리즘 사용

- 장점 : 큐간 프로세스 이동이 불가하여 스케줄링 부담이 적음
- 단점 : 유연성이 떨어짐

* **Foreground (interactive)** : **RR**
* **Background (batch - no human interaction, long CPU Burst)** : **FCFS**

- Fixed priority scheduling : 우선순위대로 부여 > **Starvation** 문제
- Time slice : 각 큐에 CPU time을 적절한 비율로 할당
  ex) 80% Foreground RR, 20% Background FCFS

##### Multilevel Feedback Queue

- 프로세스가 다른 큐로 이동 가능 (**Aging**)
- priority가 높을수록 RR time q 를 짧게 줌, priority가 낮아질수록 q가 증가 > FCFS
- CPU 시간이 짧은 process는 빨리 빠져나가지만 아닌 프로세스는 아래 queue로 내려감
- CPU 사용 시간 예측 필요 X
