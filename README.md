# 리눅스 명령어 top

## top에 대해서

### 시스템의 상태를 전반적으로 가장 빠르게 파악 가능(CPU, Memory, Process)

### 옵션 없이 입력하면 interval 간격(기본 3초)으로 화면을 갱신하며 정보를 보여줌

### top 실행 전 옵션

+ 순간의 정보를 확인하려면 `-b` 옵션 추가(batch 모드)
+ `-n` : top 실행 주기 설정(반복 횟수)


### top 실행 후 명령어

+ shift + p : CPU 사용률 내림차순
+ shit + m : 메모리 사용률 내림차순
+ shift + t : 프로세스가 돌아가고 있는 시간 순
+ k : kill. k 입력 후 PID 번호 작성. signal은 9
+ f : sort field 선택 화면 -> q 누르면 RES순으로 정렬
+ a : 메모리 사용량에 따라 정렬
+ b : Batch 모드로 작동
+ 1 : CPU Core별로 사용량 보여줌

### `top -b -n 1`

 <img width="670" alt="top 명령어 예시 사진" src="https://user-images.githubusercontent.com/106813806/171850590-c881c465-0d95-4777-b3a5-85a945faea0a.png">

+ 3:58 : 3시간 58분 전에 서버가 구동
+ load average : 현재 시스템이 얼마나 일을 하는지를 나타냄. 3개의 숫자는 1분, 5분, 15분 간의 평균 실행/대기 중인 프로세스의 수. CPU 코어수 보다 적으면 문제 없음
+ Tasks : 프로세스 개수
+ KiB Mem, Swap : 각 메모리의 사용량
+ PR : 실행 우선순위
+ VIRT, RES, SHR : 메모리 사용량 => 누수 check 가능
+ S : 프로세스 상태(작업중, I/O 대기, 유휴 상태 등)

## VIRT, RES, SHR

### 현재 프로세스가 사용하고 있는 메모리.

### VIRT

+ 프로세스가 사용하고 있는 virtual memory의 전체 용량
+ 프로세스에 할당된 가상 메모리 전체
+ SWAP + RES

### RES

+ 현재 프로세스가 사용하고 있는 물리 메모리의 양
+ 실제로 메모리에 올려서 사용하고 있는 물리 메모리
+ 실제로 메모리를 쓰고 있는 RES가 핵심

### SHR

+ 다른 프로세스와 공유하고 있는 shared memory의 양
+ 예시로 라이브러리를 들 수 있음. 대부분의 리눅스 프로세스는 glibc라는 라이브러리를 참고하기에 이런 라이브러리를 공유 메모리에 올려서 사용

## Memory Commit

### 프로세스가 커널에게 필요한 메모리를 요청하면 커널은 프로세스에 메모리 영역을 주고 실제로 할당은 하지 않지만 해당 영역을 프로세스에게 주었다는 것을 저장해둠

### 이런 과정을 Memory commit이라 부름

### 커널이 프로세스의 메모리 요청에 따라 즉시 할당하지 않고 Memory Commit과 같은 기술을 사용해 요청을 지연시키는 이유

+ fork()와 같은 새로운 프로세스를 만들기 위한 콜을 처리해야 하기 때문
+ fork() 시스템 콜을 사용하면 커널은 실행중인 프로세스와 똑같은 프로세스를 하나 더 만들고, exec() 시스템 콜을 통해 다른 프로세스로 변함. 이 때 확보한 메모리가 쓸모 없어질 수 있음
+ COW(Copy-On-Write) 기법을 통해 복사된 메모리 영역에 실제 쓰기 작업이 발생한 후 실질적인 메모리 할당을 진행

## 프로세스 상태

### SHR 옆에 있는 S 항목으로 볼 수 있음
+ D : Uninterruptiable sleep. 디스크 혹은 네트워크 I/O를 대기
+ R : 실행 중(CPU 자원을 소모)
+ S : Sleeping 상태, 요청한 리소스를 즉시 사용 가능
+ T : Traced or Stopped. 보통의 시스템에서 자주 볼 수 없는 상태
+ Z : zombie. 부모 프로세스가 죽은 자식 프로세스

---

# 리눅스 명령어 ps

## ps에 대해서

### ps(process status)의 줄임말로 ps 명령어는 현재 실행중인 프로세스 목록과 상태를 보여준다.

## ps 사용법

### 주요 옵션

|옵션|내용|
|:---:|:---:|
|-e|every(모든)프로세스|
|-f|full(완전한)포맷|
|-l|long(긴)포맷|

### 사용 방법 예시

`$ ps-ef` : 모든 프로세스를 풀 포맷으로 출력

![ps 명령어 예시](https://user-images.githubusercontent.com/106813806/171856801-6b1a1f47-f630-4ba7-8d43-5bad0336d43d.PNG)

### 출력 내용

|행 제목|내용|
|:---:|:---:|
|UID|실행 유저|
|PID|프로세스 ID|
|PPID|부모 프로세스 PID|
|C|CPU 사용량|
|STIME|Start Time|
|TTY|프로세스 제어 위치\+ 콘솔:tty1\+ 원격:pts/1|
|TIME|구동 시간|
|CMD|실행 명령어|

### BSD 계열 옵션

|옵션|내용|
|:---:|:---:|
|a|모든 사용자|
|u|프로세스의 사용자/소유자|
|x|데몬 프로세스(터미널 세션이 끊겨도 구동 되는 프로세스)|
|f|프로세스 상속관계 트리구조로 출력|
|ww|넓게(wide)|

### 실행 예시

`$ ps aux` : 실행중인 모든 프로세스 확인

![ps 명령어 예시 2](https://user-images.githubusercontent.com/106813806/171858015-61c96bcc-9866-4502-8d5e-c2ee78ecb3f6.PNG)

---

# ps와 top의 차이점

+ ps는 **ps한 시점에 proc에서 검색한 cpu 사용량**
+ top은 **proc에서 일정 주기로 합산해 cpu 사용율 출력**

---

# 리눅스 명령어 jobs

## jobs에 대하여

### **jobs 명령어는** 직업의 상태를 표시하는 명령이다. 현재 쉘 세션에서 실행시킨 백그라운드 작업의 목록이 출력되며, 각 작업에는 번호가 붙어 있어 kill 명령어 뒤에 '%번호'등으로 사용할 수 있다.

**`jobs[옵션][작업번호]`**

## jobs 사용법

### jobs로 출력되는 백그라운드 작업의 상태값

|상태|설명|
|:---:|:---:|
|Running|작업이 계속 진행중임|
|Done|작업이 완료되어 0을 반환|
|Done(code)|작업이 종료되었으며 0이 아닌 코드를 반환|
|Stopped|작업이 일시 중단|
|Stopped(SIGTSTP)|SIGTSTP 시그널이 작업을 일시 중단|
|Stopped(SIGSTOP)|SIGSTOP 시그널이 작업을 일시 중단|
|Stopped(SIGTTIN)|SIGTTIN 시그널이 작업을 일시 중단|
|Stopped(SIGTTOU)|SIGTTOU 시그널이 작업을 일시 중단|

### 주요 옵션

|옵션|설명|
|:---:|:---:|
|-l|프로세스 그룹 ID를 state필드 앞에 출력|
|-n|프로세스 그룹 중에 대표 프로세스 ID를 출력|
|-p|각 프로세스 ID에 대해 한 행씩 출력|
|command|지정한 명령어를 실행|

### 더 많은 옵션 알 수 있는 링크

<https://hbase.tistory.com/242>

---
# 리눅스 명령어 kill

## kill에 대해서

### kill 명령어는 프로세스에 시그널을 보내는 명령어이다

## kill 사용법

### 1) kill 시그널 리스트 확인

![klill 명령어 확인](https://user-images.githubusercontent.com/106813806/171863085-65e77ab3-2727-46b9-b134-b025bc709958.PNG)

### 2) 주요 시그널

|시그널|영어|설명|
|:---:|:---:|:---:|
|1) SIGHUP|Hang Up|세션이 종료될 때 시스템이 내리는 시그널|
|2) SIGINT|Interrupt|Ctrl + C, 종료 요청 시그널|
|9) SIGKILL|Kill|강제 종료 시그널|
|11) SIGSEGV|Segment Violation|메모리 침범이 일어날 때 시스템이 보내는 시그널|
|15) SIGTERM|Terminate|기본 값, 종료 요청 시그널|
|20) SIGTSTP|Temporary Stop|Ctrl + Z 일시 중지 요청 시그널|

### 3) 프로세스에 시그널 보내기

```
$ kill [option] PID

# 1234(PID) 프로세스 종료 
$ kill -9 1234
$ kill -SIGKILL 1234
```
