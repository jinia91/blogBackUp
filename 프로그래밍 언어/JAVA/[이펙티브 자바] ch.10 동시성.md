## Itme 78. 공유중인 가변데이터는 동기화해 사용하라

- 여러 쓰레드에서 공유되어지는 데이터가 있다면 동기화는 필수.

- 공유되어지는 데이터가 불변이 아니라면 읽기에도 동기화처리를 해주는것이 옳다.

## Item 79. 과도한 동기화는 피해라

### 동기화의 위험성 Dead Lock(교착상태)
- 무한 대기 상태 : 여러 쓰레드가 서로 상대방의 작업이 끝나기만 기다리고 있어 다음단계로 진행되지 못하는 상태 
- 실무에서는 사실상 코드단위로 상황에 따라 대처법이 다 다름

- 교착상태 발생조건 : 4개조건이 모두 부합될때 발생
	1. 상호배제 : 프로세스들이 필요로하는 자원에 대해 배타적 통제권요구
	2. 점유대기 : 자원을 점유한상태에서 다른 자원 대기
	3. 비선점 : 선점기법x
	4. 순환대기 : 각 프로세스(쓰레드)는 순환적으로 다음 프로세스 요구자원 보유
	
- 교착상태 해결
	- 위의 조건중 하나를 제거

### 필연적인 성능 저하

- 멀티코어가 일반화된 오늘날 과도한 동기화는 락을 얻는데 드는 비용 + 다른 코더들이 동기화된 자원을 얻기위해 낭비되는 지연시간만큼 비용이 매우 커짐

- JVM은 컴파일러단계에서 코드 최적화를 진행하는데 이때 변수 호이스팅등이 일어난다. 그러나 동기화처리(syncronized)를 하면 이러한 최적화가 일어나지 않으므로 숨겨진 비용이 발생

### 동기화중 외부 메서드 호출 금지

## Itme 80. 스레드보다는 Excutor Framework

- 실행자 (excutor) : 쓰레드 풀을 생성하고 관리하여 병렬 프로그래밍을 가능하게 해주는 프레임워크

		import java.util.concurrent.ExecutorService;
		import java.util.concurrent.Executors;
		import java.util.concurrent.TimeUnit;

		public class ExecutorServiceTest {

		    public static void main(String args[]) throws InterruptedException {
		        ExecutorService executor = Executors.newFixedThreadPool(4);

		        executor.submit(() -> {
		            String threadName = Thread.currentThread().getName();
		            System.out.println("Job1 " + threadName);
		        });
		        executor.submit(() -> {
		            String threadName = Thread.currentThread().getName();
		            System.out.println("Job2 " + threadName);
		        });
		        executor.submit(() -> {
		            String threadName = Thread.currentThread().getName();
		            System.out.println("Job3 " + threadName);
		        });
		        executor.submit(() -> {
		            String threadName = Thread.currentThread().getName();
		            System.out.println("Job4 " + threadName);
		        });

		        // 더이상 ExecutorService에 Task를 추가할 수 없습니다.
		        // 작업이 모두 완료되면 쓰레드풀을 종료시킵니다.
		        executor.shutdown();

		        // shutdown() 호출 전에 등록된 Task 중에 아직 완료되지 않은 Task가 있을 수 있습니다.
		        // Timeout을 20초 설정하고 완료되기를 기다립니다.
		        // 20초 전에 완료되면 true를 리턴하며, 20초가 지나도 완료되지 않으면 false를 리턴합니다.
		        if (executor.awaitTermination(20, TimeUnit.SECONDS)) {
		            System.out.println(LocalTime.now() + " All jobs are terminated");
		        } else {
		            System.out.println(LocalTime.now() + " some jobs are not terminated");

		            // 모든 Task를 강제 종료합니다.
		            executor.shutdownNow();
		        }

		        System.out.println("end");
		    }
		}

- 주요 기능
	1. 특정 task가 완료되길 기다리기
	2. 태스크 모음 중 아무것 하나 혹은 몯느 태스크가 완료되기를 기다리기
	3. 실행자 서비스가 종료하기를 기다리기
	4. 완료된 태스크들의 결과를 차례로 받기
	5. 태스크를 특정 시간에 혹은 주기적으로 실행하게 하기
	
- 병렬 프로그래밍에 필요한 기본적인 기능들이 이미 구현되어있으므로 직접 쓰레드를 만들어 실행하기보다는 실행자 프레임워크를 사용하도록 하자


## Item 81. wait, notify 보다는 동시성 유틸리티를 사용하자

- Item 80과 마찬가지로 병렬 프로그래밍을 지향한다면 쓰레드를 직접조작하는 wait notify보다는 이미 만들어져있는 고수준 동시성 유틸리티를 사용하는것이 더 바람직

- 대표적인 동시성 유틸리티로는 Item 80에서 소개한바 있는 실행자 프레임워크, concurrent colleciton, synchronizer 가 있다.

### synchronizedMap vs ConcurrentHashMap

- 과거에는 멀티 스레드 환경에서 synchronizedMap을 사용

- ConcurrentHashMap은 이진트리구조로 된 해시맵의 필요한 일부분만을 lock을 걸어 전체에 락을 거는 synchronizedMap보다 더 성능이 우수

## Item 82. 스레드 안전성 수준을 문서화하라

### 스레드 안전성 기준

1. 불변
2. 무조건 스레드 안전
3. 조건부 스레드 안전
4. 스레드 안전하지 않음
5. 스래드 적대적

## Item 83. 지연초기화는 신중히 사용할것

- 지연초기화 : 필드의 초기화 시점을 그값이 처음 필요할때까지 늦추는 기법

- 스프링에서 일반적으로 생성자 주입을 통해 참조하는 객체를 초기화하지만, 때때로 순환참조 문제를 해결하거나 다른 이유로 지연 초기화를 통해 주입을 하는 경우가 있다.

- 멀티 스레드 환경에서는 지연초기화가 까다러운데 지연 초기화하는 필드를 둘 이상의 스레드가 공유한다면 어떤 형태로든 반드시 동기화를 하는것이 바람직하다.

### 정적 필드용 지연초기화 홀더 클래스 관용구

- 클래스는 클래스가 처음 쓰일때 비로소 초기화된다.

- 지연 초기화를 사용하는 이유중 만약 성능 문제때문에 초기화를 미루고 클래스의 다른 메서드를 사용하다가 해당 필드를 사용하는 시점에서 지연초기화를 하는 케이스라면 해당 필드를 별개의 홀더 클래스(내부 스태틱 클래스)로 분리하는 디자인 패턴을 생각해 볼 수 있다.

	   // 특정 멤버변수를 들고있는 내부 스태틱 클래스로 분리
	   private static class FieldHolder{
			static final FieldType field = computeFieldValue();
		}

		// 외부 클래스에서 내부 멤버 변수를 호출하는 메서드
		private static FieldType getField(){
			return FieldHolder.field;
		}

### 인스턴스 필드 지연 초기화용 이중검사 관용구

	private volatile FieldType field;
	
	private FieldType getField(){
		FieldType result = field;
		if (result != null){	// 1차검사
			return result;
		}	
		synchronized(this){
			if(field == null){		// 2차 검사로 null시 지연 초기화
				field = computeFieldValue();
			}
			return field;	// 2차 검사로 반환 이후론 1차검사에서 반환되므로 synchronized로 인한 비용, 성능저하 문제 x
		}
	}


## Item 84. 프로그램의 동작을 스레드 스케줄러에 기대지 말라

- 운영체제의 스케쥴러는 기본적으로 예측 불가능하다.
-  우선순위 부여는 프로그래밍에서 단지 hint이므로 기대하는 동작을 보장하지 않는다.

- 따라서 스케줄러와 우선순위에 의존하는 프로그래밍은 지양하도록 하자