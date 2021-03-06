# spring_study_week5

1. 통합 테스트
2. Validation
3. 데이터 로깅(다음시간에~~)

## Quiz

1. 의존성 주입을 위한 어노테이션은?

    - @Autowired


2. Field, 생성자에서도

    - 순환참조 이슈 해결 위함
        - `순환참조`: A와 B 두 객체가 있을 때, 서로가 서로를 참조하고 있는 상황


3. 빌더 패턴이 가지는 이점?

    - 명시적으로 필드에 값 주입 가능
    - setter 주입보다 Immutable 하게 코드 작성 가능 (가독성 향상)
    - 필드가 많을 경우 생성자 오버로딩을 많이 하지 않고도 주입 가능


4. Restful API가 제공하는 기본적인 HTTP Method 4가지

    - GET: 조회
    - POST: 생성
    - PUT: 수정
        - 필드가 가지고 있는 데이터가 3개라면 그 데이터 3개를 모두 바꾸는 경우에 사용 (Fetch의 경우 하나만 바꿀 때)
        - 실무에서 그걸 다 지키지는 않음,,
    - DELETE: 제거


5. DTO 객체를 생성하는 이유는?

    - Domain Layer의 객체와 Pressentation Layer(화면에 보이는 영역)의 객체를 구분하기 위함
    - 두 계층 간 객체들의 역할은 분명히 다르다


6. Transaction이란?

    - 특정 비지니스에서 하나의 업무 단위라고 정의하는 것
        - ex) ATM - 은행에서 있는 하나하나의 업무 단위
    - ACID
        - Atomic(원자성): 업무가 하나의 일련 과정이어야 함
            - 더이상 쪼개질 수 없는 하나의 업무 단위
        - Consistency(일관성): 업무 결과가 작업 끝난 후에도 유지되어야 함
        - Isolation(고립성): 업무 처리 시 다른 업무가 끼어들 수 없다
            - 트랜잭션이 발행하고 있을 때, 다른 트랜잭션들이 끼어들 수 없음
            - ex) ATM에서 돈을 인출하는 중에 다른 곳에서도 동시에 인출을 한다?
        - Durability(영구성): 작업 내용이 영구히 저장되어야 함 (DB기준이기 때문에 어플리케이션 레벨에서는 달라질 수 있음)
    - NonBolcking
        - ATM에서 돈을 인출하는데 다른 곳에서 인출하지 못하게 막음으로써, 속도가 저하된다.
        - ACID는 Blocking 방식으로 비효율적이라는 의견이 있기 때문에, 이를 해결하기 위한 방안


7. 정적 팩토리 메서드 (of Pattern)

    - 객체 생성하는 코드를 팩토리 메서드 형태로 제공
    - 코드 중복 최소화
    - 비즈니스와 객체 생성에 대한 영역을 구분할 수 있음


8. 컨트롤러 앞단에서 예외처리 해주도록 스프링에서 제공해주는 기능은?

    - ControllerAdvice
        - 예외처리라는 업무에 종속되어있지는 않음
        - Controller 이전에 뭔가를 해줄 수 있다.
        - Exceptionhandller가 있기 때문에 동작이 가능한 것
    - @RestControllerAdvice


9. 자바 중 이슈 null 처리를 강제하기 위해서 추가된 기능?

    - Optional
        - 자바 언어는 기본적으로 null이 존재. 그러나, 실무에서 null point Exception이 많은 장애를 일으키기 시작함.
        - 최근 언어들은 null이 허용되지 않는 것이 default값이 되게 됨.
        - Kotlin, Swift 등은 기본적으로 null을 처리하지 않음


10. 테스트코드를 작성하는 이유

    - 쓰는 것 자체로 시스템의 안정성을 향상시킬 수 있음

    - 테스트코드는 다른 개발자들에게 하나의 문서로 사용 가능함

        - 내가 만든 함수에 대한 테스트를 명싲거으로 작성함으로써 다른 개발자들은 이 테스트 코드를 보고 내가 만든 함수가 어떻게 동작하는지를 보다 정확히 알 수 있음

        - 작성된 함수가 동작할 때 흔하지 않은 예외 케이스를 접할 수 있는데 그럴 때마다 테스트 케이스를 추가하면 이런 부분에 대해서 주위 다른 개발자에게 인계가 정확히 될 수 있음



## Controller 유닛 테스트

- @WebMvcTest 어노테이션은 Controller 외 관련된 객체만 스프링 컨테이너 빈으로 등록

```java
@WebMvcTest(ArticleController.class)
class ArticleControllerTest {
    
}
```

- Mock 객제를 스프링 컨테이너에 등록하고 싶다면?
    - @MockBean을 활용

```java
@WebMvcTest(ArticleController.class)
class ArticleControllerTest {
    @MockBean
    ArticleService articleService;
}
```

## 통합 테스트



- @SpringBootTest
    - 모든 빈들을 테스트에 올리게 됨


- MockMvc
    - API를 테스트할 떄, 요청할 수 있는 클라이언트가 필요함
    - API 콜을 하는 것이 MockMvc


- @Beforeeach
    - 초기 설정 함수
    - 테스트 함수 이전에 실행됨


- 테스트 코드를 작성할 때는 given, when, then의 세 가지 절차를 거쳐서 작성!!
    - given - post 호출하기 위한 dto 생성
    - when - mockMvc(mvc.perform)를 활용해서 호출하려고 하는 api의 url을 입력해서 가져옴
        - accept: 나 이거 받을 수 있어. 이거 줘야돼라고 서버에 요청(말만 하는거 받는건 받아봐야 알 수 있음)
        - contentType: 진짜 실제로 그 타입을 받아야됨
        - content: 데이터를 주고받을 때 get에서는 쿼리로 url에 다 하는데 post는 body에 내용을 넣어서 통신. 거기서 그 body 통신이 content라고 생각하면 편함 (기본적으로 모든 통신은 String 타입이기 때문에 ObjectMapper를 통해 Json 스타일로 변환해주기)

- 테스트 코드 구현

```java
@SpringBootTest
public class ArticleIntegrationTest {
    @Autowired
    private WebApplicationContext context;

    @Autowired
    private ArticleRepository articleRepository;

    // API 테스트를 할 때, 요청하는 클라이언트를 대신해주는 역할
    // API call을 해서 잘 동작하는지 확인
    private MockMvc mvc;

    // 각 테스트 실행 이전에 설정을 하기 위한 함수(초기 설정 함수)
    @BeforeEach
    public void setup(){
        mvc = MockMvcBuilders
                .webAppContextSetup(context)
                .build();
    }

    @Test
    @DisplayName("post() 실행되면 article 객체가 새로 생성되어야 한다.")
    public void post_whenItIsOccured_thenArticleShouldbeStored() throws Exception {
        //given
        ArticleDto.ReqPost requestBodyStub = ArticleDto.ReqPost.builder()
                .title("title")
                .content("content")
                .build();

        //when
        mvc.perform(post("/api/v1/artile")
                .characterEncoding("utf-8")
                // client가 서버에 미리 얘기해주는 것. 어떤 타입을 받을 수 있는지
                .accept(MediaType.APPLICATION_JSON) 
                // 실제로 그 타입을 받아와야만 함. 하나의 값만 넣을 수 있음
                .contentType(MediaType.APPLICATION_JSON) 
                .content(new ObjectMapper().writeValueAsString(requestBodyStub))
        )
                .andDo(print())
                .andExpect(status().isOk()); // 하나의 Test의 역할을 함

        //then
        // 실제 db의 객체 갯수 받아오기
        long size = articleRepository.count();
        assertThat(size).isEqualTo(1);

    }
}
```



### 번외

- accept: 클라이언트가 서버에 미리 말해주는거. html, xml 타입 등 특정 값만 받을 수 있다고 하면 accept를 통해서 서버에 전달함


- JUnit5 사용중
    - 실제로 실무에 들어가면 실무는 최신기술보다 4를 사용해서 문법이 조금 다를 수 있음...!
        - 4에 대해서도 조금 보고 가라,,,
    - given, when, then으로 코드를 짜고있지만, 코드 상에서 제약 조건이 있지는 않음
    - 또한, Mocking이 쉽지 않다. 지금 하는 방법이 가장 대중적인 방법
    - 쉽게 할 수 있지 않을까? junit을 사용하지 않을 수 있지 않을까?
        - spock를 사용하면 조금 더 쉽게 사용할 수 있음
        - 하지만, 스프링에서 지원하고있지 않기 때문에 대부분의 회사에서 쓰지는 않는다,,,
        - 기회가 되거나 하면 라이브러리 찾아보거라~ Groovy를 사용한다,,,



# Validation(유효성 검증)

- 클라이언트가 서버에 요청을 보냈을 때, 그 요청값이 정상적인지 선행적으로 체크를 하는 로직
- API 앞단에서 미리 요청 데이터를 검증하고 후에 시스템으로 이로 인한 문제가 없도록 함

### 직접 처리

- Article에서 Post를 하려고 한다
    - title, content가 필요.
    - 정상적으로 값이 들어오지 않았다면? Post 실패
    - 이 값이 잘 들어오는지 체크를 해주는 것이 validation


- 어떻게 구현할까?
    - 값을 확인하는 코드를 controller 에서 확인하고, 예외값 던져주면 되지 않을까?


- Assert 유틸 클래스 활용해서 Controller에서 validation 처리

```java
// vo/ApiCode.java
public enum ApiCode {
    // ...
    BAD_REQUEST("CM0002", "요청 정보가 올바르지 않습니다.")
    ;
    // ...
}
```

- Assert에서 Blank인 경우 예외처리를 위한 코드 작성

```java
// exception/Asserts.java
public class Asserts {
    // ...
    
    public static void isBlank(@Nullable String str, ApiCode code, String msg) {
        if(Strings.isBlank(str)) {
            throw new ApiException(code,msg);
        }
    }
}
```

- Blank Vs. Null Vs. Empty
    - 받았는데 스트링 값이 null이면 오류 던져라~
    - Blank: 문자열은 들어왔는데 띄어쓰기까지 포함된 경우 `" "`
    - Null: 말 그대로 값이 아예 없는 것
    - Empty: 문자열의 길이는 0이고 `""`인 것. (조건이 empty면 whitespace는 pass됨)


- @PostMapping에 validation할 수 있는 코드 추가

```java
// controller/ArticleController.java
public class ArticleController{
    // ...
    @PostMapping
    public Response<Long> post(@RequestBody ArticleDto.ReqPost request) {
        //validation할 수 있는 코드
        Asserts.isBlank(request.getTitle(),ApiCode.BAD_REQUEST, "title은 필수입니다.");
        Asserts.isNull(request.getContent(),ApiCode.BAD_REQUEST,"content는 필수입니다.");
        
        return Response.ok(articleService.save(Article.of(request)));
    }
    // ...
}
```



### Spring Validation으로 Validation 처리

- 데이터를 검증하는 케이스는 일반적으로 Null, 빈값, 날짜의 포맷, 금액의 포맷, 요구되지 않은 값 등으로 어느정도 정형화 되어있음
- 스프링에서는 spring-validation이라는 의존성으로 보다 simple하게 validation처리할 수 있는 기능을 제공함

```
// build.gradle

dependencies {
  // ...
  
  implementation 'org.springframework.boot:spring-boot-starter-validation'
  
  // ...
}
```

- Spring Validation 적용 위한 @Valid 추가

```java
public class ArticleController {
    
    // ...
  
    @PostMapping
    public Response<Long> post(@RequestBody @Valid ArticleDto.ReqPost request) {
        return Response.ok(articleService.save(Article.of(request)));
    }
    
    // ...
}
```

```java
public class ArticleDto {
    @Getter
    @Builder
    public static class ReqPost {
        @NotBlank
        String title;
        @NotBlank
        String content;
    }
    
    // ...
}
```

- title, content에 공백 넣고 테스트 하면 예외 발생 확인 가능
- Validation 처리가 가지는 중요 요소 중 하나는 API를 사용하는 client에게 어떤 값이 잘못되어 있는지 정보 제공 가능
- 작성한 Validation 코드는 API로 문제점 전달. Spring Validation은 서버 로그에만 오류가 기록. Client에는 400에러만 출력
- Spring Validation의 오류 내용이 API로 넘어가게 작성

```java
@RestControllerAdvice
public class ControllerExceptionHandler {
    
    // ...
  
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public Response<String> methodArgumentNotValidException(MethodArgumentNotValidException e) {
        return Response.<String>builder().code(ApiCode.BAD_REQUEST).data(e.getMessage()).build();
    }
}
```

- 확인해보면 API 응답 바뀐 것 확인 가능
- Client는 메시지 보고 확인할 수 있어야 함

### 번외

- @RequestBody
    - http 기반 통신에서 post 기반 통신할 때, body를 받아오는 어노테이션
    - 메서드 argument Exception이 뜨게 된다.
    - 바디로 데이터 가져왔을 때, 검증 실패하면 저렇게 뜬다


- @RequestParam
    - get 통신을 하게 된다면, 쿼리 스트링 값을 받아오는 어노테이션
    - 얘를 통해 값을 받아오면 method argument exception이 아닌 다른 오류가 뜨게 됨
        - ConstraintViolationException
    - => 해결하기 위해서는 다른걸 새로 만들어줘야 함

```java
@RestControllerAdvice
public class ControllerExceptionHandler {
    // ...
  
    /* @RequestBody 데이터 검증 실패시 발생한다. */
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public Response<String> methodArgumentNotValidException(MethodArgumentNotValidException e) {
        return Response.<String>builder().code(ApiCode.BAD_REQUEST).data(e.getMessage()).build();
    }

    /* @RequestParam 데이터 검증 실패시 발생한다. */
    @ExceptionHandler(ConstraintViolationException.class)
    public Response<String> constraintViolationException(ConstraintViolationException e) {
        return Response.<String>builder().code(ApiCode.BAD_REQUEST).data(e.getMessage()).build();
    }
}
```
