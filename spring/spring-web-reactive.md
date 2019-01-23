# Web On Ractive Stack
이번 파트는 `Netty`, `Udertow`, `Servlet 3.1+ Container`와 같은 논-블록킹 서버에서 실행 하기 위해 만들어진 `Reactive Streams API` 기반인 `reactive-stack` 웹 애플리케이션에 대한 지원을 다룹니다.

## 1. Spring WebFlux
`Spring Web MVC`에 포함된 원래의 웹 프레임워크는 `Servlet API`와 `Servlet Container`를 위해 만들어졌습니다. `reactive-stack` 웹 프레임워크인 `Spring WebFlux`는 5.0 버전에 추가되었습니다. `Spring WebFlux`는 완전히 논-블록킹하고 Reactive Stream의 `back pressure`를 지원하고 `Netty`, `Undertow`, `Servlet3.1+ Container`와 같은 서버에서 동작합니다. 두 웹 프레임워크 모두 소스 모듈(`spring-webmvc`,`spring-weflux`)의 이름을 반영하였고, 스프링 프레임워크에서 서로 공존할 수 있습니다. 각 모듈은 선택사항입니다. 애플리케이션은 하나 또는 다른 모듈을 사용하거나 경우에 따라 두 가지 모두를 사용할 수 있습니다.(예를 들면, `Spring MVC` 컨트롤러와 reactive `WebClient`를 함께 사용할 수 있습니다.)

### 1.1 Overview
왜 Spring WebFlux가 만들어졌을까요?

해답의 일부는 논-블록킹 웹 스택은 적은 수의 스레드로 동시성을 처리하고 보다 적은 하드웨어 자원으로 확장하기 위해 필요하기 때문입니다. `Servlet 3.1`은 논-블록킹 I/O 를 위한 API를 제공하고있습니다. ~~그러나 이 기능을 사용하면 계약이 동기식(`Filter`, `Servlet`) 또는 블로킹(`getParamter`, `getPart`)인 `Servlet API`의 나머지 부분에서 벗어날 수 있습니다. 이것은 새로운 공통 API가 모든 논-블록킹 런타임에 대한 기초 역할을 제공하기 위한 동기부여가 됐다. 그것은 비동기, 논-블록킹에 대해서 처리를 하는 잘하는 서버(`Netty`) 때문에 중요하다.(이부분 다시)~~

해답의 다른 부분은 함수형 프로그래밍입니다. 자바 5 에서는 `Annotation` 추가로 개발자들이 개발하기 더 쉽게 만들었고(`@RestController`, `@Test`와 같이 사용하는), 자바 8 에서는 람다식을 추가하여 함수형 API들을 제공하고 있습니다. 람다식 추가는 논-블록킹 애플리케이션과 continuation-style API(CompletableFuture 또는 ReactiveX와 같은)들에 좋은 효과를 가져왔고, 비동기 로직의 선언적 구성을 허용하게 만들었습니다. 프로그래밍 모델 레벨에서 자바 8은 Spring WebFlux의 어노테이션이 달린 컨트롤러와 함께 함수형 엔드포인트를 제공 할 수 있게 만들어줍니다.

### 1.1.1 Define "Reactive"
우리는 간단하게 논-블록킹과 함수형에 대해서 언급했지만, reactive의 의미는 무엇입니까?

reactive 라는 용어는 변화에 반응하는 것을 중심으로 만들어진 프로그래밍 모델들을 의미합니다 - I/O 이벤트에 반응하는 네트워크 구성 요소, 마우스 이벤트에 반응하는 UI 컨트롤러 등이 있습니다. 그러한 점에서 논-블록킹 은 반응적이다, 왜냐하면 블록되는 대신에 작업이 완료되거나 데이터를 사용할 수 있게될 때 알림에 반응하는 모드이기 때문이다.

또한 Spring 팀이 "reactive" 라고 부르는 또 다른 이유는 논-블록킹 back pressure라는 중요한 메커니즘 때문입니다. 동기적으로 반드시 수행되어야 하는 코드는 블로킹 호출은 호출자를 기다려야 하기 때문에 자연스럽게 배압을 제공하고 있습니다. 논-블록킹 코드에서 이벤트 발생율을 제어하여 publisher의 속도보다 높지 않도록 하는 배압의 관리가 중요합니다.

리액티브 스트림은 자바9 에서 채택한 작은 스펙이고 상호작용을 비동기 구성요소와 배압사이의 상호 작용을 정의한 것입니다. 예를들어 Publisher와 같은 역할을 하는 데이터 레파지토리는 Subscriber 역할을 하는 HTTP 서버가 응답에 쓸 수 있는데이터를 생성할 수 있습니다. 리액티브 스트림의 주요 목적은 Publisher가 생성하는 데이터를 속도를 Susbcriber가 제어할 수 있게하는 것입니다.

일반적인 질문: Publisher 가 데이터 제공 속도를 늦출 수 없는 경우에 어떻게 해야할까요?
리액티브 스트림의 목적은 오직 메커니즘과 경계를 설정하는 것입니다. Publisher가 속도를 늦출 수 없다면, 버퍼에 담아두거나, 삭제하거나, 실패 여부를 결정해야합니다.