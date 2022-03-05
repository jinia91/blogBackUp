# Goal
- 브랜치 관리 전략 숙지

## 앞으로 더 알아볼 것
- 깃에 대한 이해도 더 자세히!
- 깃 액션

# 브랜치 관리 전략

## 브랜치 관리 전략이란?

## Git - flow

![image](https://camo.githubusercontent.com/4aa46830c32e5ef7655be7b10ab77bd6a9939e31284a8ecca710cef12fd564ec/687474703a2f2f6e7669652e636f6d2f696d672f6769742d6d6f64656c4032782e706e67)

가장 최초로 제안된 workflow 방식

### 브랜치 종류가 5개나?

깃 플로우 관리 전략에서 사용되는 브랜치 종류는 다음과같다.


|  |  | 
| --- | --- | 
| main / master | - 현재 운영계에서 작동하고 있는 최신 버전을 가리키고 있는 브랜치<br> - 경우에 따라선 main 브랜치에서 배포가 일어나기도 하지만 원칙적으로는release, 또는 hotfix 브랜치에서 배포후 main으로 병합된다. | 
| develop | - 미래에 출시될 다음 버전을 가리키고 있는 브랜치<br>  |  
| feature | - 특정 기능 개발을 위해 develope에서 분리된 브랜치<br> - 원칙적으론 feature 브랜치가 가리키는 작업물은 develop 브랜치로 다시 통합된다. | 
| hotfix | - 현재 운영 서버에서 장애가 발생할경우 1순위로 해당 장애를 fix하기 위해 main 브랜치에서 따오는 브랜치 <br> - 원칙적으론 트러블슈팅 후 hotfix 브랜치에서 배포하고 실 서버가 정상적으로 작동됨을 확인하면 해당 브랜치의 커밋을 main과 develop 으로 각각 머지하여 싱크를 맞춘다  |
|release | - develop에서 최종적으로 완성된 다음버전을 QA하고 배포하기위해 분리한 브랜치 <BR> - 해당 브랜치에서 QA완료 후 최종본을 배포하고, 배포가 정상적으로 완료되면 main과 develop으로 각각 머지하여 싱크를 맞춘다. | 

### 그래서 이렇게 하면 장점이 뭔데?

브랜치 타입이 하는 일을 최대한 이해하기 쉽게 써보려했지만, 그럼에도 깃 플로우 브랜치 관리 전략은 꽤 복잡하게 느껴진다.

어째서 이렇게 복잡한 방식으로 개발을 할까?

개발 시나리오 하나를 떠올려보겠다.

    - 인기있는 모바일 어플리케이션을 출시한 개발팀이 있다.
    - 이 팀은 현재 안드로이드 개발자가 5명, ios개발자는 2명인 상황
    - 만약 이 팀의 개발 프로세스가 2주동안 '기획 - 디자인 - 개발 - QA - 릴리즈' 라고 생각해보자
    - 기획안이 나오고 디자인을 거친후, 각 클라이언트 개발자들은 개발에 착수한다. 하지만 인력차이로 안드로이드쪽이 1주일만에 모든 개발, QA를 마친상황!
    - IOS개발은 인력부족으로 2주를 꽉꽉채우게 된다.
    - 이때, 안드로이드 개발자들은 새로운 기획이 나오지 않기 때문에 할게 없는.....
    
개발 팀이 커지고, 직무가 분업화될수록 하나의 기획에서 발생하는 업무양이 각각 다르고, 업무 완료 속도도 다를수밖에없다.

따라서 보다 효율적인 업무 프로세스 방식이 필요한데, 위의 시나리오를 다음과같이 바꿔보자


    - 개발 프로세스를 바꿔 여러 기획안을 두고 기획안에 필요한 기능개발을 작게 분할해서 우선순위를 매겨보자
    - 이제 잘게 쪼개진 수많은 기획-업무들이 우선순위순으로 나열되어있다.
    - 팀원들은 회의를 거쳐 우선순위순으로 각각 업무를 할당받고 개별적으로 작업에 들어간다.
    - 종료된 작업순으로 develop 브랜치로 차근차근 병합되고 팀원들은 다음 우선순위 작업에 착수한다.
    - 다음 버전 릴리즈가 되었다고 판단된 develop 브랜치의 커밋내역을 release 브랜치로 분리하고 qa후 배포!
    - 이 와중에도 각팀원들은 다음 우선순위 기획안의 업무내역을 수행하고있기때문에 빡빡한 일정으로 효율적이고 기민한 개발문화가 형성가능하다!(Agile)

위와같은 시나리오를 수행하기 위해서는 많은수로 분화되는 feature 브랜치와 이를 통합하는 develop 그리고 긴급 장애 fix를 위한 hotfix 브랜치와 배포를 위한 relase 마지막으로 버전관리를 위한 main 브랜치 5 타입이 모두 필요하게 된다.


## Github - flow
giit - flow 에서 언급했던 시나리오를 잘 생각해보면, 전제조건으로 큰 프로젝트와, 큰 규모의 팀이 붙는다.

만약 qa팀도 없고, 개발팀 규모도 작고, 프로젝트도 작은 규모라면 git - flow 방식은 너무 번거로울것이다.

이럴때 사용되는 간소한 브랜치 관리전략이 바로 github - flow 다.

![image](https://camo.githubusercontent.com/1206cff5e1da4b7ed742937a9234c6736c47db8866c3005ce0ae0266ad65c50f/68747470733a2f2f6d69726f2e6d656469756d2e636f6d2f6d61782f313136362f302a367054354834766e756a564c637930532e706e67) 

그림으로 보기에도 단순하지 않은가?

github-flow 전략은 버전관리와 실서버 배포가 항상 메인브랜치에서 일어나며, 버그 수정과 기능 변경은 main 브랜치에서 따서 작업후 pull request를 한다.

여기서 중요한점은 pull request에서 머지되면 바로 배포가 일어나는만큼 머지전에 충분한 테스트와 코드리뷰가 동반되야 한다.

만약 팀원이 많다면 수많은 feature가 분리되고 해당 feature들이 병합될때마다 배포가 일어나기때문에, 큰 규모의 팀에서 행하기엔 적합하지 않을것이다.

하지만 규모가 작고 서비스가 빠른 주기로 개발되어야한다면 github - flow는 적절한 브랜치 관리 전략이 될 수 있다!


## Gitlab - flow

github -flow는 너무 간소하고, git - flow는 너무 복잡하다고 느낀다면 gitlab-low 전략은 어떠한가?

![image](https://camo.githubusercontent.com/805cc97fd57cca10339b278f4db275f9a675dcb45cb76752d7bac5fc87b352fa/68747470733a2f2f61626f75742e6769746c61622e636f6d2f696d616765732f6769745f666c6f772f656e7669726f6e6d656e745f6272616e636865732e706e67)

기본적으로는 github -flow처럼 기능 개발후 main 브랜치로 병합하는 방식이지만,

해당 커밋내역을 바로 배포하는것이 아니라 preproduction 브랜치와 production 브랜치를 두고 배포의 시기를 인위적으로 조절하는 방식이다.

이렇게 되면 수많은 feature 내역이 동시다발적으로 개발되어 main 브랜치로 병합되더라도 잦은 배포가 일어나지않고 적절한 시기에 다음버전으로 릴리즈를 진행할 수 있게된다.



# 그래서 어떤 방식이 제일 좋을까?

세가지 방식중 최고의 방식은 존재하지 않는다. 시나리오를 들어 설명했던것처럼 팀의 상황과 프로젝트의 규모마다 상황은 제각각이며

팀이 가장 효율적이고 생산적이라고 판단되는 전략을 선택하는것이 베스트다.

또한 실무에선 해당 방식을 교과서처럼 전적으로 따르기보단, 팀의 상황을 고려해 변형을 가하는 방식도 얼마든지 가능할 것이다.


# References

- [https://velog.io/@jinuku/Git-%EB%B8%8C%EB%9E%9C%EC%B9%98-%EC%A0%84%EB%9E%B5](https://velog.io/@jinuku/Git-%EB%B8%8C%EB%9E%9C%EC%B9%98-%EC%A0%84%EB%9E%B5)
- [https://techblog.woowahan.com/2553/](https://techblog.woowahan.com/2553/)
- [https://blog.lulab.net/programming-tools/the-need-for-a-git-branch-strategy/](https://blog.lulab.net/programming-tools/the-need-for-a-git-branch-strategy/)
- [https://hyeon9mak.github.io/git-branch-strategy/](https://hyeon9mak.github.io/git-branch-strategy/)
- [https://tecoble.techcourse.co.kr/post/2021-07-15-git-branch/](https://tecoble.techcourse.co.kr/post/2021-07-15-git-branch/)
- [https://github.com/gyoogle/tech-interview-for-developer/blob/master/ETC/Git%20vs%20GitHub%20vs%20GitLab%20Flow.md](https://github.com/gyoogle/tech-interview-for-developer/blob/master/ETC/Git%20vs%20GitHub%20vs%20GitLab%20Flow.md)