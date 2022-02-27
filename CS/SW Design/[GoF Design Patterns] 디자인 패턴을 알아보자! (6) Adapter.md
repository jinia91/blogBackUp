# Adapter 란?
## 어댑터 패턴 정의, Intend

![image](https://t1.daumcdn.net/cfile/tistory/226D46505402BF0A0D)

> 특정 코드가 클라이언트가 사용하려는 인터페이스와 호환되지 않을 때, 이를 호환되도록 해주는 패턴 

## 왜 필요한가? Pros

- 현재 앱의 서비스가 존재하고, 새로운 외부 라이브러리로 서비스를 구현해야하는 상황이 생겼다고 가정하자.
   이때 어댑터 패턴을 사용하면, 현재 앱의 서비스 인터페이스와 외부 라이브러리 코드를 전혀 수정하지 않고 어댑터만 구현해서 이용가능하므로 유연한 확장이 가능하다.
- 위의 사례처럼 기존 코드를 변경하지 않고 유연한 확장이 가능해지므로 `OCP`를 준수할 수 있다.
- 인터페이스와, 인터페이스를 사용하기 위한 변환 로직이 분리되므로 `SRP`를 준수할 수 있다. 

## 문제점은? Cons

- 인터페이스와 구현체의 분리, 새로운 어댑터 클래스 또는 인터페이스의 작성으로 코드가 복잡해진다.

# 구현방법

## 시나리오

> 기존 회원 서비스 로직이 존재하는 상태에서, 스프링 시큐리티 프레임워크를 추가하여 시큐리티로 계정 로그인을 구현하려는 상황


 ### 기존 계정 엔티티  
    

      public class Account {
    
        private String name;
    
        private String password;
    
        private String email;
    
        public String getName() {
            return name;
        }
    
        public void setName(String name) {
            this.name = name;
        }
    
        public String getPassword() {
            return password;
        }
    
        public void setPassword(String password) {
            this.password = password;
        }
    
        public String getEmail() {
            return email;
        }
    
        public void setEmail(String email) {
            this.email = email;
        }
    }


### 기존 계정 서비스 클래스

    public class AccountService {
    
        public Account findAccountByUsername(String username) {
            Account account = new Account();
            account.setName(username);
            account.setPassword(username);
            account.setEmail(username);
            return account;
        }
    
        public void createNewAccount(Account account) {
    
        }
    
        public void updateAccount(Account account) {
    
        }
    }

### 스프링 시큐리티 로그인 핸들러(Client)

    public class LoginHandler {
    
        UserDetailsService userDetailsService;
    
        public LoginHandler(UserDetailsService userDetailsService) {
            this.userDetailsService = userDetailsService;
        }
    
        public String login(String username, String password) {
            UserDetails userDetails = userDetailsService.loadUser(username);
            if (userDetails.getPassword().equals(password)) {
                return userDetails.getUsername();
            } else {
                throw new IllegalArgumentException();
            }
        }
    }

스프링 시큐리티 프레임워크는 계정 서비스로 `UserDetailsService` 라는 인터페이스와 구현체를 의존하고, 이때 엔티티로 `UserDetails` 인터페이스의 구현체를 사용한다.

따라서 기존 `Account`와 `AccountService` 를 위의 핸들러로 처리할수 있도록 구조를 수정해야하는 시나리오.

## Object adapter 패턴

![image](https://refactoring.guru/images/patterns/diagrams/adapter/structure-object-adapter.png?id=33dffbe3aece294162440c7ddd3d5d4f)

### UserDetails 인터페이스를 구현한 엔티티 어댑터 

    public class AccountUserDetails implements UserDetails {
    
        private final Account account;
    
        public AccountUserDetails(Account account) {
            this.account = account;
        }
    
        @Override
        public String getUsername() {
            return account.getName();
        }
    
        @Override
        public String getPassword() {
            return account.getPassword();
        }
    }

### UserDetailService를 구현한 서비스 클래스 어댑터 

    public class AccountUserDetailService implements UserDetailsService {
    
        private final AccountService accountService;
    
        public AccountUserDetailService(AccountService accountService) {
            this.accountService = accountService;
        }
    
        @Override
        public UserDetails loadUser(String username) {
            Account account = accountService.findAccountByUsername(username);
            return new AccountUserDetails(account);
        }
    }
    
### 설명

기존 코드들인 `Account` 와 `AccountService`는 `Adaptee`로서 스프링 시큐리티 프레임워크에 사용되어지기 위해 해당 인터페이스를 구현한 구현체에 래핑되는 방식으로 어댑터 클래스를 작성했다.

따라서 클라이언트인 `Handler`는 코드 변경없이 그대로 `UserDetails 와 UserDetailService`인터페이스만 의존하면서도 

기존에 존재하던 `Account` 와 `AccountService`를 어댑터안에서 사용하는 호환성을 보여준다.


## Class adapter 패턴

![image](https://refactoring.guru/images/patterns/diagrams/adapter/structure-class-adapter.png?id=e1c60240508146ed3b98ac562cc8e510)

### 다중상속으로 구현한 엔티티 어댑터 

    public class AccountUserDetails extends Account implements UserDetails {
        public AccountUserDetails(Account account) {
            this.setEmail(account.getEmail());
            this.setName(account.getName());
            this.setPassword(account.getPassword());
        }
    
        @Override
        public String getUsername() {
            return super.getName();
        }
    }
    

### 다중상속으로 구현한 서비스 클래스 어댑터 

    public class AccountUserDetailService extends AccountService implements  UserDetailsService{
        @Override
        public UserDetails loadUser(String username) {
            Account account = findAccountByUsername(username);
            return new AccountUserDetails(account);
        }
    }
    
### 설명

기존코드와 인터페이스 둘을 모두 상속(구현)하는 방식으로 어댑터 구현체를 작성하고 해당 구현체를 클라이언트의 인터페이스 구현체로 사용하는 방식

자바에서는 다중상속이 제한되어있으므로, 인터페이스로 분리가 되어있는 상황에서 사용가능하다.


# 다른 패턴과의 관계

- 브릿지패턴은 두 종류의 코드가 서로 독립적으로 개발될수 있게끔 사전에 설계하는 편이지만, 어댑터 패턴은 기존에 존재하던 코드에 추가로 다른 코드를 호환할때 사용되는 편이다.
- 어댑터 패턴을 `Object Adapter`로 사용할 경우 래핑하는 방식으로 구현이 되는데, 이때의 패턴 의도는(`Intend`) 다른 인터페이스로 변환하기 위함이다. 반면 프록시 패턴은 같은 인터페이스를 그대로 사용하며, 데코레이터 패턴은 인터페이스를 추가한다는 점에서 차이가 존재한다.
- 파사드 패턴(`Facade`)은 기존에 존재하는 객체에 인터페이스를 새로 정의한다는 점에서 어댑터 패턴과 차이가 있으며, 어댑터 패턴은 하나의 객체만 래핑하지만 파사드 패턴은 서브시스템의 전체 객체에 대해 작동한다는 점에서 차이가 있다.
- 브릿지, 스테이트, 전략, 어댑터 패턴은 서로 상당히 비슷한 구조를 띈다. 그러나 해결하려는 문제와 의도가 다르다는점을 명심하자. 디자인 패턴은 코드의 구조가 핵심이 아니라, 특정 문제와 그 문제 해결하려는 의도가 핵심이다!

# 실무에서 어떻게 사용되는지 사례

- java.util.Arrays#asList()
- java.util.Collections#list()
- java.util.Collections#enumeration()
- java.io.InputStreamReader(InputStream) 
- 스프링 `HandlerAdapter`

# [학습예제 코드는 깃허브에](https://github.com/jinia91/DesignPattern/tree/main/src/adapter)

# References

- [https://refactoring.guru/design-patterns/adapter](https://refactoring.guru/design-patterns/adapter)
- [백기선, 코드로 학습하는 GoF의 디자인 패턴](https://www.inflearn.com/course/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4/dashboard)
