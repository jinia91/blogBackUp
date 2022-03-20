# Intro
블로그의 이미지 업로드 로직은 서버에서 깃헙api를 사용해 깃헙 레포로 이미지를 올리고 url을 받아오는 방식이였다.

이러다보니 이미지를 올릴때마다 불필요한 커밋도 찍히게 되서 언젠가 다른 이미지 서버로 스택 마이그레이션을 진행하려고 마음먹었다가 이번에 AWS S3로 변경하게 되었다.

## Goal

1.  추후에 또다른 이미지 서버 변경을 생각해, 기존 로직을 전략패턴으로 리팩토링해보기
2. 동적으로 전략이 바뀌는 방식도 테스트해보기


# 기존 코드 전략패턴으로 코드 개선


## 기존 ImgUploadService

    @Service
    @Transactional
    @RequiredArgsConstructor
    public class UploadImgService {
    
        @Value("${git.gitToken}")
        private String gitToken;
    
        @Value("${git.imgRepo}")
        private String gitRepo;
    
        @Value("${git.imgUrl}")
        private String imgUrl;
    
        /*
            - 이미지 저장 로직
                1. 깃허브 Repo에 이미지 업로드
                2. 업로드된 Url 반환
        */
        public String storeImg(MultipartFile multipartFile) throws IOException {
            if (multipartFile.isEmpty()) {
                throw new IllegalArgumentException("이미지가 존재하지 않습니다.");
            }
    
            String storeFileName = createStoreFileName(multipartFile.getOriginalFilename());
    
            GitHub gitHub = new GitHubBuilder().withOAuthToken(gitToken).build();
            GHRepository repository = gitHub.getRepository(gitRepo);
            repository.createContent().path("img/"+storeFileName)
                    .content(multipartFile.getBytes()).message("thumbnail").branch("main").commit();
    
            return imgUrl + storeFileName + "?raw=true";
        }
    
        /*
            - 이미지 중복 방지용 무작위 파일 이름 생성
        */
        private String createStoreFileName(String originalFilename) {
            String ext = extractExt(originalFilename);
            String uuid = UUID.randomUUID().toString();
            return uuid + "." + ext;
        }
    
        /*
            - 파일 이름 추출
        */
        private String extractExt(String originalFilename) {
            int pos = originalFilename.lastIndexOf(".");
            return originalFilename.substring(pos + 1);
        }
    }
    

기존 `ImgUploadService`는 그 이름과는 달리 사실상 `GithubImgUploadService` 였으며,  깃헙api로 이미지를 업로드하는 책임과 파일이름을 만들어서 반환하는 책임이 섞여있어 좋은 객체지향 코드로 볼 수 없었다.

이를 전략패턴을 사용해 메인 알고리즘을 분리하여 compositon 구조로 변경했다.

## after - ImgUploadService
    @Service
    @Transactional
    @RequiredArgsConstructor
    public class ImgUploadServiceImpl {
    
        private final ImgUploadStrategy imgUploadStrategy;
    
        public String storeImg(MultipartFile multipartFile) throws IOException {
            validateFile(multipartFile);
            String storeFileName = createStoreFileName(multipartFile.getOriginalFilename());
            return imgUploadStrategy.uploadFile(multipartFile, storeFileName);
        }
        
        /**
        * 이미지 파일 검증
        */
        private void validateFile(MultipartFile multipartFile) {
            if (multipartFile.isEmpty()) {
                throw new IllegalArgumentException("이미지가 존재하지 않습니다.");
            }
        }
    
        /*
            - 이미지 중복 방지용 무작위 파일 이름 생성
        */
        private String createStoreFileName(String originalFilename) {
            String ext = extractExt(originalFilename);
            String uuid = UUID.randomUUID().toString();
            return uuid + "." + ext;
        }
    

        /*
            - 파일 이름 추출
        */
        private String extractExt(String originalFilename) {
            int pos = originalFilename.lastIndexOf(".");
            return originalFilename.substring(pos + 1);
        }
    }
    
## after-ImgUploadStrategy

     /**
      * 파일 업로드 전략패턴 추상화 인터페이스
      */
    public interface ImgUploadStrategy {
        String uploadFile(MultipartFile file, String storeFileName) throws IOException;
     }
     
## after-GithubImgUploadStrategy

     @RequiredArgsConstructor
     @Service
     public class GithubRepoImgUploadStrategy implements ImgUploadStrategy {
    
        /*
        - 설정 파일로 잡아놓은 깃헙 이미지 레포지토리와 토큰
    */
        @Value("${git.gitToken}")
        private String gitToken;
    
        @Value("${git.imgRepo}")
        private String gitRepo;
    
        @Value("${git.imgUrl}")
        private String rootUrl;
    
        /*
            - 이미지 업로드 로직
                1. 깃허브 Repo에 이미지 업로드
                2. 업로드된 Url 반환
        */
        @Override
        public String uploadFile(MultipartFile multipartFile, String storeFileName) throws IOException {
            GitHub gitHub = new GitHubBuilder().withOAuthToken(gitToken).build();
            GHRepository repository = gitHub.getRepository(gitRepo);
            repository.createContent().path("img/"+storeFileName)
                    .content(multipartFile.getBytes()).message("thumbnail").branch("main").commit();
            return rootUrl + storeFileName + "?raw=true";
        }
    }
    


전략패턴을 사용해 기존`ImgUploadService`에서 `ImgUpload`알고리즘을 분리하여 추상화하고, `Github api`를 사용하는 구현체로 계층화한 모습이다.

이제 여기서 `AwsS3ImgUploadStrategy` 구현체만 따로 개발하고 `@bean`을 갈아끼우기만하면 끝!

기존의 구조(`전략패턴변경후`)에서 아무런 코드변화없이 새로운 전략(`AWS S3`)으로 변경되기때문에 `OCP`를 준수한 객체지향적 코드가 된다!

## AWS S3 ImgUploadService 개발

     /**
     * S3 이미지 업로드 전략 구현체
     */
    @RequiredArgsConstructor
    @Service
    public class AwsS3ImgUploadStrategy implements ImgUploadStrategy {
    
        private final AmazonS3 amazonS3;
        @Value("${cloud.aws.s3.bucket}")
        private String bucket;
    
        @Override
        public String uploadFile(MultipartFile file, String storeFileName) {
            ObjectMetadata metadata = createObjectMetadata(file);
            try(InputStream inputStream = file.getInputStream()) {
                amazonS3.putObject(new PutObjectRequest(bucket, storeFileName, inputStream, metadata)
                        .withCannedAcl(CannedAccessControlList.PublicRead));
            } catch (IOException e) {
                throw new IllegalArgumentException("파일 업로드에 실패했습니다.");
            }
            return amazonS3.getUrl(bucket, storeFileName).toString();
        }
    
        /**
         * s3 api를 위한 dto 생성 메서드
         * @param file 업로드 요청된 이미지 파일
         * @return s3 api 요구 스펙 dto
         */
        private ObjectMetadata createObjectMetadata(MultipartFile file) {
            ObjectMetadata metadata = new ObjectMetadata();
            metadata.setContentLength(file.getSize());
            metadata.setContentType(file.getContentType());
            return metadata;
            }
        }
        

# 전략패턴을 응용해 동적으로 전략이 바뀌도록 코드짜보기

전략패턴으로 개발도중 궁금한 점이 하나 생겼다.

위의 방식은 @Service를 뗏다 붙여서 정적으로 전략을 바꾸는 방식이지만, 전략들을 모두 빈등록시킨후 동적으로 런타임시점에서 전략을 선택하도록 할수는 없을까?

당연한 이야기지만 두 구현체 모두에 @Service를 붙이게 되면 의존관계 주입시 타입이 `ImgUploadStrategy`이므로 스프링은 어떤 구현체를 집어넣을지 판단하지 못해 에러가 터지게 된다.

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/30ebe153-5f08-444a-a17e-89ae9a17f4b6.png)  

## Map을 통해 같은 타입 Bean 여러개 주입하기

![첨부 이미지](https://jinia-img-bucket.s3.ap-northeast-2.amazonaws.com/ca32732d-77b1-406d-b882-0e8e87e043e5.png)

위와같이 `Map<String, BeanType>` 을 통해 DI를 받으면 해결된다.

이때 key값은 구현체의 클래스명을 컨벤션에 맞게 첫글자 소문자로 바꾼것이며 이를 통해 동적으로 여러 전략들을 사용할 수 있게된다.

키값이 String이라 애매하다면 해당 String을 Enum으로 관리하는 방법도 괜찮을것같다.

그 외에도 `@Qualifier(name="")`어노테이션을 통해 빈의 이름을 짓고 주입할 곳에 그 이름을 넣어 만드는 방법도 존재한다.

