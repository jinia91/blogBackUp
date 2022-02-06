# CORS란?

## 개요

채용과정에서 과제테스트중에 백엔드와 프론트 둘다 구현하는 요구사항이 있었다. 그동안 모놀리틱한 구조로만 만들어 봤기에 나로서는 첫 시도였는데 진행도중 Cors 이슈를 처음 접하게 되었다.

당시엔 시간에 쫒겨 급하게 해결하기만 했지만, 해당 내용이 궁금하여 이번에 정리해보려 한다.

**일단 CORS는 에러가 아니다.**

### CORS란?

> 교차 출처 리소스 공유(Cross-Origin Resource Sharing, CORS)는 추가 HTTP 헤더를 사용하여, 한 출처에서 실행 중인 웹 애플리케이션이 다른 출처의 선택한 자원에 접근할 수 있는 권한을 부여하도록 브라우저에 알려주는 체제를 의미한다. <br>
> \- MDN

즉 CORS는 하나의 기술일 뿐이며, 우리가 백엔드, 프론트엔드 구현에서 흔하게 접하는 이슈는 바로 **SOP 위반**이라고 말하는것이 더 정확하다.

## 브라우저와 SOP
우리가 사용하는 브라우저는 특정한 예외를 제외하고는 기본적으로 SOP을 따른다.

>동일 출처 정책(same-origin policy, SOP)은 어떤 출처에서 불러온 문서나 스크립트가 다른 출처에서 가져온 리소스와 상호작용하는 것을 제한하는 중요한 보안 방식.

즉 **SOP 위반이란** 브라우저가 특정 서버에서 응답을 받아 렌더링된 페이지에서 다른 출처의 리소스를 요청할때 발생하는 문제다.

여기서 이미지, 동영상등 다양한 출처의 리소스를 그동안 잘 사용해왔다는 의문점이 들 수 있는데

브라우저는 \<img>, \<video>, \<object> 등 우리가 일반적으로 쓰는 다양한 교차 출처에 대해선 예외로 허용하고 있으므로 모놀리틱한 구조에서는 sop위반을 만나기 어렵다.

하지만 백엔드서버와 프론트서버가 분리된 아키텍처에서는 최초에 프론트서버에서 클라이언트앱을 받아온 뒤 그 클라이언트앱에서 백엔드로 요청이 가기 때문에 **SOP 위반**이 발생한다.

또한 **SOP은 브라우저에만 적용**되기 때문에 postman이나 모바일 클라이언트에서는 SOP위반을 일반적으로 만나보기 어렵다.

## CORS 자세한 작동방식

![image](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS/preflight_correct.png)

1. 렌더링된 페이지에서 다른 출처로 요청이 있을경우 해당 출처로 `Options` 요청을 먼저보낸다.(프리 플라이트)

2. 이때 프리플라이트 요청 헤더에는 `Origin` 헤더가 존재하며 여기에는 현재 렌더링된 페이지의 출처가 기록되어있다.
3. 서버는 프리플라이트 요청을 받고 교차 출처가 허용되는지에 대해`Access-Control-Request-*` 헤더에 담아 응답을 보낸다.
4. 응답을 받은 브라우저는 헤더를 파싱해 교차 출처가 불가능하면 에러를, 가능하면 실제 요청을 다시 보낸다.


## 백엔드 서버에서의 대처법은?

아키텍처 레벨에서 프론트와 백엔드의 출처를 동일하게 만들도록 앞단에 프록시나 게이트를 만드는 방법도 존재하겠지만 

백엔드의 입장에서만 이야기하자면 결국 소통하고자하는 클라이언트 서버에 교차 출처를 허용해주면 된다.

그리고 그 방법은 `Access-Control-Request-*` 헤더를 조작하는 것이다.

# 스프링 서버에서 CORS 대처하기

## Sol.1 `Access-Control-Request-*` 부착 필터

Post맨을 통해 `Options`로 프리플라이트 요청을 해보면 해당 요청은 실제 컨트롤러까지 내려가지 않으며 `HandlerMapping`에서 처리되어 응답이 반환되는걸 확인할 수 있다.

![첨부 이미지](https://github.com/jinia91/blogBackUp/blob/main/img/ac7f0f4e-c4d8-4d56-95e8-0d1c74d400d8.png?raw=true)

따라서 CORS 인가를 위해서는 `HandlerMapping` 에 무언가 힌트를 주거나 디스패쳐 서블릿 앞단인 필터에서 로직을 수행하는것이 가장 바람직하다.

![image](https://supawer0728.github.io/images/spring-filter-interceptor/spring-request-lifecycle.jpg)

[스프링의 구조상 컨트롤러로 내려오지 않는 `Options` 요청은 특별한 전처리없이는 인터셉터에서 CORS처리가 불가능하다]

아래처럼 원하는 클라이언트 서버에 CORS 허가를 주는 `Access-Control-Request-*` 헤더를부착하도록 필터를 작성하면 해결!

    @Component
    @Order(Ordered.HIGHEST_PRECEDENCE)
    public class CorsFilter implements Filter {
    
        @Override
        public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain) throws IOException, ServletException {
            HttpServletRequest request = (HttpServletRequest) req;
            HttpServletResponse response = (HttpServletResponse) res;

            response.setHeader("Access-Control-Allow-Origin", "http://localhost:3000");
            response.setHeader("Access-Control-Allow-Credentials", "true");
            response.setHeader("Access-Control-Allow-Methods","*");
            response.setHeader("Access-Control-Max-Age", "3600");
            response.setHeader("Access-Control-Allow-Headers",
            Origin, X-Requested-With, Content-Type, Accept, Authorization");

            if("OPTIONS".equalsIgnoreCase(request.getMethod())) {
                response.setStatus(HttpServletResponse.SC_OK);
            }else {
                chain.doFilter(req, res);
            }
        }
    }



## Sol.2 @CrossOrigin 애너테이션

Sol.1은 원론적인 방법이며 당연하게도 스프링은 해당 기능을 추상화하여 훨씬 편리하게 제공하고 있다.

CORS를 허가해줄 컨트롤러 메서드나 컨트롤러에 `@CrossOrigin`을 부착해주기만 하면 설정 완료!

`@CrossOrigin` 은 기본적으로 모든 요청에 CORS를 허가해주기 때문에 만약 특정 출처에만 허가하고 싶다면 `@CrossOrigin(origins = "http://domain1.com, http://domain2.com")` 와 같이 작성해주면 된다.

## Sol.3 WebMvcConfigurer 에서 설정하기

어노테이션 방식 이외에도 `WebMvcConfigurer`를 상속받는 Config 구현체에 `addCorsMappings` 메서드를 오버라이딩하고 구현하는 방법도 존재한다.

    @Configuration
    public class WebConfig implements WebMvcConfigurer {
        @Override
        public void addCorsMappings(CorsRegistry registry) {
            registry
                    .addMapping("/**")
                    .allowedOrigins("http://domain1.com");
         }
     }
     


# References

- [MDN, 교차 출처 리소스 공유 (CORS)](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS)
- [https://wonit.tistory.com/572](https://wonit.tistory.com/572)
- [https://supawer0728.github.io/2018/04/04/spring-filter-interceptor/](https://supawer0728.github.io/2018/04/04/spring-filter-interceptor/)