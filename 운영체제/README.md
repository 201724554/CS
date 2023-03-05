* **Process**
  * 실행 중인 프로그램으로, 디스크로부터 메모리에 적재되어 CPU를 할당받음 
  * 각 프로세스는 메모리 공간을 할당 받음 - code, heap, stack
  * 생성될 때 PCB를 생성함
    * PID, 프로세스 상태, Process counter, 레지스터 상태 등 정보 포함
    * 리눅스의 경우, task_struct라는 자료구조에 트리 형식으로 커널 메모리에 저장됨
* **Thread**
  * 프로세스의 실행 단위
  * 같은 프로세스 안의 스레드는 heap 영역의 자원을 공유하지만 각자만의 스택과 pc를 가짐
* **System Call**
  * 운영체제의 커널이 제공하는 기능을 사용하기 위한 인터페이스
  * User 모드에서 Kernel 모드의 기능을 사용할 때 system call을 호출
  * trap 중 하나
    * aka. Software interrupt
    * Exception, System call, Interrupt 등
    * System call은 트랩 번호 0x80을 가짐
    * trap 발생 시 trap table에 저장된 trap handler를 실행
    * system call의 경우 trap table에서 system call handler를 실행하고, handler 안에서 system call 번호를 통해 특정 system call 수행
  * 호출 시, 현재 레지스터 정보를 kernel stack에 저장
  * system call을 수행한 후, kernel stack에 저장한 레지스터 정보를 이용해 복구
* **Context switching**
  * metrics 
    * turnaround time - 프로세스 종료 시간 - 프로세스 arrival time
    * response time
    * fairness
  * Non preemptive
    * 프로세스가 스스로 CPU의 점유를 포기하고 다른 프로세스로 넘김
    * FIFO, SJF 등
    * 거의 사용되지 않음 - 프로세스가 스스로 CPU를 넘겨주기 때문에 중간에 죽어버리면 교착상태에 빠질 수 있음
  * Preemptive
    * timer interrupt를 통해 운영체제가 강제로 CPU의 점유를 변경함
    * STCF(shortest to completed first), Round Robin 
    * MLFQ
      * 우선순위 별로 여러개 queue가 있고, 각 queue 안에서는 RR 방식으로 스케줄링
      * 일단 프로세스가 처음 생성되면 우선순위가 제일 높은 queue에 할당됨
      * queue 마다 할당되는 time slice가 있는데, 주어진 time slice을 얼마나 쓰느냐에 따라 우선순위를 조정함 - 많이 쓰면 우선순위가 낮은 queue로 이동
      * 우선순위가 높은 queue에 프로세스가 계속 할당돼 우선순위가 낮은 queue의 프로세스의 starvation을 방지하기 위해 주기적으로 priority boost 실행
* **Virtual memory**
  * Segmentation
    * **todo**
  * Paging
    * **todo**
* **Linux**
  * **todo** OOM killer