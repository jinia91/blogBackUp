# Facade 란?
## 퍼사드 패턴의 정의, Intend
> Facade는 프랑스어로 건물 정면을 의미하며, 다양한 서브 시스템들의 코드를 모은 간략한 인터페이스를 제공하는 패턴을 의미한다.
## 왜 필요한가? Pros
- 특정 목적을 위해 여러 서브시스템이 필요하고 서브시스템들을 오케스트레이션하여 하나의 큰 서비스를 제공한다고 가정해보자
- 문제는 이 커다란 서비스를 여러 클라이언트에서 사용하는 경우다.
- 여러 클라이언트마다 모든 서브시스템을 알고 그 서브시스템을 오케스트레이션하는 구현코드를 중복해서 기입하는것은 비효율적이며, DRY원칙에도 위배된다.
- DRY(dont repeat yourself) = 반복 금지의 원리, 복붙 금지 윈칙!
- 이때 퍼사드 패턴으로 아주 손쉽게 구현을 인터페이스 뒤로 숨길수 있다.
- 인터페이스를 사용하는 입장에서 이해가 쉬워지며, 개발의 유연성이 증가된다.
## 문제점은? Cons
- 객체지향적인 관점에서 보면 책임을 적절하게 할당하는것이 좋은 설계이며, 하나의 객체에 의존관계가 집중되는것을 지양한다.
- 하지만 퍼사드 패턴은 여러 의존관계를 하나의 인터페이스로 다시 모으기때문에, 관점에 따라서는 절차지향적인 코드 스타일로 볼수도 있으며, `God Object(너무 많은걸 알고있고, 너무 많은걸하는)`로 취급될수도 있다.
- 객체지향적 관점에서 퍼사드 패턴은 안티패턴으로 볼 여지도 충분함
- 실용적인 설계와 OOP간의 트레이드 오프로 생각하자
# 구현 코드
>퍼사드 패턴은 아주 쉬운 패턴이므로 간략한 학습예제로도 금방익힐수있다. 아래 예제는 비디오 파일이 String으로 주어졌을때(바이트코드로 생각하자) 코덱을 추출해 File로 convert시켜주는 간단한 예제다.
## before

### Client

    public class Client {
    
        public static void main(String[] args) {
            VideoFile newVideo = new VideoFile("new Video.ogg");
            Codec codec = CodecFactory.extract(newVideo);
            VideoFile read = BitrateReader.read(newVideo, codec);
            VideoFile convert = BitrateReader.convert(read, codec);
            new File(convert.getName() + convert.getCodecType());
            }
    }   
    
클라이언트는 `new Video.ogg` 라는 파일을 File로 바꾸기 위해 Codec, VideoFile, BitrateReader와 그 메서드들 등 너무많은 사항을 알아야하고

`convert`라는 하나의 유스케이스를 위한 구현내용을 너무 많이 알고있다.

위의 유스케이스를 사용하는 클라이언트가 10곳이고, 구현을 마친 뒤 갑자기 요구사항이 늘어 새로운 코드가 추가된다고 상상해보자.

중복된 코드들을 모두 찾아 수정하는일은 공수가 많이들것이다.

## after

    public class CodecFactory {
        public static Codec extract(VideoFile file){
            String codecType = file.getCodecType();
            if(codecType.equals("mpeg")){
                System.out.println("mpeg video");
                return new MpegCodec();
            }
            System.out.println("ogg video");
            return new OggCodec();
        }
    }
    

    public class Client {
    
        public static void main(String[] args) {
            ConverterFacade converterFacade = new ConverterFacade();
            converterFacade.convertVideo("New Video.ogg");
        }
    }
    
이제 모든 구현을 퍼사드 클래스 뒤로 감춤으로서 클라이언트는 퍼사드의 인터페이스만 알면 된다.

서브 시스템의 복잡도로부터 격리된 클라이언트는 느슨한 결합도를 통해 보다 유연한 변경과 확장이 가능해졌다.

# 다른 패턴과의 관계
- 퍼사드는 이미 존재하는 객체에 대한 새로운 인터페이스 제공이 가능한 반면, 어댑터 패턴은 이미 존재하는 인터페이스를 사용가능하게 해준다는 측면에서 차이가 있다.
- 퍼사드는 보통 하나로 충분하기때문에 싱글턴으로 만들기 적합하다.
- 퍼사드는 복잡한 엔티티를 완충하고 스스로 초기화한다는 점에서 프록시와 유사하다. 하지만 퍼사드와 달리, 프록시는 서비스 객체와 동일한 인터페이스를 가진다.
# [학습예제 코드는 깃헙에](https://github.com/jinia91/DesignPattern/tree/main/src/facade)
# References
- [https://en.wikipedia.org/wiki/God_object](https://en.wikipedia.org/wiki/God_object)
- [https://imasoftwareengineer.tistory.com/29](https://imasoftwareengineer.tistory.com/29)
- [https://ko.wikipedia.org/wiki/%ED%8D%BC%EC%82%AC%EB%93%9C_%ED%8C%A8%ED%84%B4](https://ko.wikipedia.org/wiki/%ED%8D%BC%EC%82%AC%EB%93%9C_%ED%8C%A8%ED%84%B4)
- [https://refactoring.guru/design-patterns/facade](https://refactoring.guru/design-patterns/facade)
- [백기선, 코드로 학습하는 GoF의 디자인 패턴](https://www.inflearn.com/course/%EB%94%94%EC%9E%90%EC%9D%B8-%ED%8C%A8%ED%84%B4/dashboard)
