# 12 factor?

클라우드 기업 heroku에서 클라우드 네이티브 앱(`SaaS`)을 만들기위한 12가지 조건, 방법론을 정리한 article

# 목표

- 시간이 지나면서 앱은 유기적으로 성장해야함
- 앱 코드베이스에서 작업하는 개발자들 간의 협업이 잘되어야함
- 시간이 지나면서 망가지는 소프트웨어 유지비용을 줄이는 법에 집중
- 이상적인 `SaaS` 앱 개발 방법 찾기

# Factors

## Factor 1. 코드 베이스
> VCS(`Version Control System`) 을 통해 하나의 코드로 통합하고 다양하게 배포하기

- VSC 툴(git, Subversion 등)을 통해 형상관리 하기
- 코드 베이스가 여러개있는 경우 앱이 아니라 분산시스템이며 이 분산시스템이 통합하여 하나의 앱이 되는 개념
  - 이때, 여러 앱은 동일한 코드를 공유하지 않을것, 공유하는 코드를 라이브러리화하고, 해당 라이브러리를 종속성 매니저로 관리하는식으로 구조 개선을 해라

## Factor 2. 종속성
> 명시적으로 선언되고 분리된 종속성
- app의 의존관계는 명시적으로 선언되야함을 의미 (maven인 경우 pom.xml, gradle인 경우 build.gradle, node인 경우 npm)
- 암시적, 묵시적 의존성은 절대 불가능!

## Factor 3. 설정
> 환경(`environment`)에 저장된 설정

- 데이터베이스 등 백엔드 서비스들의 리소스 핸들
- 외부서비스 인증정보
- 배포된 호스트의 정규화된 호스트이름처럼 각 배포마다 달라지는 값 등등...

배포마다 달라질수 있는 모든것들은 `Setting`

이를 하드코딩하지 않고 외부 설정파일로 둘것(스프링 yml, property 등 파일)

## Factor 4. 백엔드 `서비스`(외부 서비스)
> 메인 앱에 연결된 외부 서비스를 리소스로 취급하기

- 애플리케이션이 네트워크를 통해 이용하는 모든 서비스를 백엔드 서비스로 정의
  - DB, MsgQ, SMTP, Noti, Cache 등등

- Twelve-Factor App의 코드는 로컬 서비스와 서드파티 서비스를 구별하지 않음
- 폴리글랏을 통해 infra 는 추상화되어 느슨하게 연결되어야하고, 구현체는 언제든지 바뀔수 있어야함
- 위의 행위를 손쉽게 하고, 인지 부조화를 줄이기위해 앱의 아키텍쳐를 port - adapter 패턴으로 구현하는것이 적합
![image](https://user-images.githubusercontent.com/85499582/182835130-3cc29374-53f6-422a-b9c8-108a43975fb7.png)


## Factor 5. Build - Release - Run
> 철저히 분리된 빌드와 실행

- 코드 베이스는 일반적으로 3단계를 거쳐 배포로 변환

1. 빌드 : 코드 저장소를 빌드라는 실행 가능한 번들로 변환시키는 단계, 빌드 단계에서는 커밋된 코드중 배포 프로세스에서 지정된 버전을 사용하며, 종속성을 가져와 바이너리와 에셋 컴파일
2. 릴리즈 : 빌드와 배포의 현재 설정을 결합, 실행 환경에 바로 실핼될수있도록 준비
3. 실행(`Run Time`) : 애플리케이션 프로세스의 집합을 시작하며, 실행

![image](https://user-images.githubusercontent.com/85499582/182838380-c5e487c8-a5b3-4f16-88d7-ced000ca4566.png)

- CD 툴을 이용해 자동화


## Factor 6. 프로세스
> 앱을 하나 혹은 여러개의 stateless 로 실행

- 앱은 stateless 하며, 아무것도 [공유하지 않아야함](https://en.wikipedia.org/wiki/Shared-nothing_architecture)
- 유지될필요가 있는 모든 데이터는 안정된 infra에 저장되어야함(DB든 2차 캐시든)
- `Sticky Session` 금지! 세션데이터는 유효기간을 제공하는 데이터 저장소(`redis, memchaced`)등에 저장됨이 옳다


## Factor 7. 포트 바인딩
> 포트 바인딩을 사용해서 서비스를 공개함

- 앱을 타 앱이나 클라에서 접근할수 있도록 포트바인딩을 통해 서비스 open
- http 뿐 아니라 redis등 infra도 포트바인딩으로 open

reference
- [https://spring.io/blog/2015/01/20/microservice-registration-and-discovery-with-spring-cloud-and-netflix-s-eureka](https://spring.io/blog/2015/01/20/microservice-registration-and-discovery-with-spring-cloud-and-netflix-s-eureka)

## Factor 8. Concurrency
> factor 6의 프로세스 모델로 수평 확장할수 있어야함

- 앱은 여러개의 물리적인 머신에서 돌아가는 여러개의 프로세스로 넓게 확장가능해야함!
- stateless한 앱일수록 이런 수평확장이 용이

## Factor 9. Disposability
> 빠른 시작과 graceful shutdown!

- 프로세스를 시작하거나 종료할때 문제가 발생하지 않아야 코드나 설정의 변화를 적용해 빠르게 배포가 가능하다.
- 단순 무중단 배포를 넘어 graceful Shutdown이 되어야함
-
### Graceful Shutdown
- 프로세스 매니저로부터 SIGTERM 신호를 수신
- 서비스 포트의 수신을 중지
- 현재의 요청이 완료됨을 기다림
- 만약 worker 프로세스의 경우 현재 처리중인 작업을 작업큐로 되돌리는 방식

## Factor 10. dev/prod 일치
> dev, staging, production 환경 최대한 비슷하게 유지

`Continuous Deploy` 가 가능하도록 각 환경간

- 시간의 차이 최소화
- 담당자의 차이 최소화
- 툴의 차이 최소화

를 추구
 
## Factor 11. 로그
> 로그는 이벤트 스트림으로 취급할것

- 로그는 시간순서로 정렬된 스트림
- 로그는 고정된 시작과 끝이 있는게 아니라, app이 실행되는동안 계속되는 흐름
- app은 로그파일을 작성하거나 관리하려해서는 안됨
- 각 프로세스는 버퍼 없이 이벤트 스트림을 출력
- 여러 프로세스에서 전달된 로그 스트림을 다양한 툴과 방식으로 다루기

## Factor 12. admin process
> admin/maintenance 작업을 일회성 프로세스로 실행

앱개발시 필요한 일회성 관리나 유지보수 작업들이 존재

- 데이터베이스 마이그레이션 실행
- 임의의 코드를 실행하거나 라이브 DB에서 앱의 모델을 조사하기위해 콘솔을 실행
- 앱 저장소에 커밋된 일회성 스크립트 실행

해당 기능들을 일회성 api로 만들수도 있지만, 보통은 REPL shell을 권장, 배포단계에서 해당 스크립트를 실행토록하고 다음배포때는 제거하는 방식















