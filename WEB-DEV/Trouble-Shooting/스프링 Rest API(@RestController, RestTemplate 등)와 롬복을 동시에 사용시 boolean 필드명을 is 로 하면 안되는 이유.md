# 개요
- Redis에 넘긴 캐시값이 제대로 반환되지 않는 상황

# 현재 상황(AS-IS)
- 회사 작업중 Redis로 넘긴 밸류값이 제대로 반환받지 못하고 있는 문제 발견
- 분명 boolean 으로 true 를 넘겼음에도 캐시를 찾을땐 false 로 반환

# 원하는 결과(TO-BE)
- 제대로된 밸류값을 받아와야함

# 문제 해결을 위한 시도
## 원인 트래킹
### 객체생성시점
 정확히 어느시점에서 문제가 발생하는지 fail point 를 찾기위해 객체 생성시점부터 디버깅을 하였다.
    - 객체는 정상적으로 boolean을 true로 생성 확인
    - 캐싱하기 직전까지 true 값임을 확인

### 반환시점

- 반환되는 값의 boolean 이 false임을 확인
- 다른 필드의 값들은 정확히 반환됨을 확인

e.g.

    // 객체 생성, 객체 캐싱시점
    class EG {
    String field = "e.g.";
    booelan isWaiting = true;
    }
    
    // 반환값
    class EG {
    String field = "e.g.";
    booelan isWaiting = false;
    }
    


# 원인 분석

필드 하나만 값이 다르게 나온다는점에서 스프링이 `rest` 통신을할때, 해당 객체를 Json으로 변환하고, 받아오는 과정에서 무언가 문제가 생긴것은 아닌가 하는 의문이 들었다.

따라서 Redis 콘솔에 직접 접근하여, 저장되는 값을 확인하였다.

    e.g.
         // 레디스에 저장된 값 예시
        class EG {
        String field = "e.g.";
        booelan waiting = true;
        }
        

회사 코드라 수도코드로 표기하면 위와같이 저장됨을 확인하였다.

즉 외부로 나가는 객체의 boolean 타입의 필드명이 바뀐상황!

위의 객체를 받아 초기화하면서 이름이 일치하지 않는 `isWaiting` 필드는 기본값인 false로 기입된것이였다.

## 이유

스프링부트, 스프링은 Rest 통신시 내부적으로 `Jackson` 라이브러리를 사용해 객체를 Json으로 매핑하고 있다.

여기서 Jackson 라이브러리의 동작중 핵심은 자바빈 규약의 `getter` 를 사용해 json의 키값을 설정한다는 점인데, 

만약 `Lombok`을 사용하고 있다면 문제가 발생할 수 있다!

롬복의 경우 boolean 필드의 `getter`를 접두사 is를 붙여서 작성하며, 만약 필드명 자체에 is가 존재한다면

그대로 `getter`를 생성한다.

즉

    @Getter
    class EG {
    String field = "e.g.";
    booelan isWaiting = true;
    
       boolean iswaiting(){
        return this.isWaiting;
       }
    }

위와같이 `getter`를 만들게된다.

여기서 만약 `jackson`라이브러리를 사용해  Json으로 변환시 해당 키밸류값은

    {field : "e.g", 
    waiting : "true"}
    
이처럼 매핑되어 rest 통신을 하게되고, 해당 값을 다시 받게되면 

    class EG {
        String field = "e.g.";
        booelan waiting = true;
    }
    
위와같이 스프링은 객체를 인식하게된다.

이를 기존

    class EG {
        String field = "e.g.";
        booelan isWaiting = true;
    }
    
객체에 매핑하려하니, waiting 필드명이 달라서 그냥 기본값인 false로 초기화된채 값이 날라가버린것이다.

# 트러블 슈팅

외부로 나가는 객체의 필드명에 is를 제거함으로서 정상작동됨을 확인하였다.

# 느낀점
처음엔 논리적으로 아무리 생각해도 정상작동이 안되는 이유를 몰라서 2시간넘게 헤맸는데, 조금더 시야를 확장하여 프레임워크차원에서 절차를 의심하였더니 생각보다 문제가 쉽게 해결되었다.

당연하게 사용하는 라이브러리, 프레임워크에대해 100% 신뢰와 확신을 갖고 사용하다보니 트러블슈팅을 하는데 오래걸렸는데, 이런부분을 왜라고 생각하고 의심하는 자세도 필요하다는걸 깨달았다.

