## Item 69. 예외는 진짜 예외상황에서만

- 예외를 제어 흐름용으로 던지는 코드는 절대 쓰지말 것!

## Item 70. 복구가능한 상황에서는 checked를 프로그래밍 오류에서는 runtime Exception을 사용해라

||checked| unchecked  |
|--|--|--|
|처리여부| 명시적 예외처리 | 명시적 처리 강제 x |
|Transaction Rollback| Y | N |
|Ex| FileNotFoundException | NullPointerException |

- 코드상에서 해당 예외를 처리하는 상황이라면 checked 예외를 try-catch와 함께 사용

- 만약 api명세에 어긋나는(ex-유효성검사를 실시하지 않아 비정상적인 인자가 넘어오는) 경우, 전제조건을 지키지 않았으므로 프로그래밍 오류로 보아 런타임 예외를 던지는것이 바람직

## Item 71. 필요없는 checked Exception 사용은 피해라

- try - catch문은 코드의 가독성을 해치므로 메인 비지니스로직에 노출되지 않는 편이 더 좋음

- checked Exception 처리를 메인 로직상에서 피하는 방법
	1. 옵셔널 반환
	2. 상태검사 메서드로 쪼개어 boolean 값을 반환하는 unchecked Exception으로 변환


## Item 72. 표준 예외를 사용해라

|  |  |
|--|--|
|IllegalArgumentException  | 허용하지 않는 값이 인수로 건네진경우 |
| IllegalStatementException | 객체가 메서드를 수행하기 적절하지 못한 상태일 때 |
| NullPointException | null을 허용하지 않는 메서드에 null을 건냈을 때 |
| IndexOutBoundException | 인덱스 범위를 넘어섰을때 |
|ConcurrentModificationException|허용하지 않는 동시 수정이 발견되었을 때|
|UnsupportedOperationException|호출한 메서드를 지원하지 않을 때|



## Item 73. 추상화 수준에 맞는 예외를 던져라

- 적절한 예외를 던져 유실되는 부분이 없도록 하자

## Item 74. 메서드가 던지는 모든 예외는 문서화해라
## Item 75. 예외발생시 메세지 관리

- 예외 발생시의 사용 매개변수와 필드값 모두 기록하는 것이 유지보수에 있어서 수월하다

- 하지만 필요한 정보를 노출해서는 안되는경우도 존재하므로 노출해도 괜찮은 정보와 괜찮지 않은 정보를 구분하여 전자는 예외 메세지에, 후자는 로그로 분리하여 기록하도록 하자

## Item 76. 가능한 실패 원자적으로 만들어라

- 웹앱에서 실패 원자성이 깨지는 경우는 일반적으로 상태정보를 저장하는 DB 관련 로직에서 발생한다.

- 이를 방지하기위해서는 3가지 방법 존재

  1. 유효성 검사 코드를 코드블록 전면에 배치하여 비지니스 로직 실행 전에 처리

  2. 로직중 임시복사본을 만들어 수행함으로서 실패시 원자성 유지

  3. 실패시 롤백처리를 통해 원자성 유지
	  - 스프링에서는 @Transactional을 걸어 원자성을 유지할 수 있음
## Item 77. 예외를 무시하지 말라

- catch 블록은 에러를 처리하겠다는 명제적 표현이므로 블록안에 반드시 에러 처리 로직을 넣어줘야한다.

