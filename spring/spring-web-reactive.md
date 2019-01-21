# Web On Ractive Stack
이번 파트는 `Netty`, `Udertow`, `Servlet 3.1+ Container`와 같은 논-블록킹 서버에서 실행 하기 위해 만들어진 `Reactive Streams API`를 기반으로 만들어진 `reactive-stack` 웹 애플리케이션에 대한 지원을 다룹니다.

## 1. Spring WebFlux
`Spring Web MVC`에 포함된 원래의 웹 프레임워크는 `Servlet API`와 `Servlet Container`를 위해 만들어졌습니다. `reactive-stack` 웹 프레임워크인 `Spring WebFlux`는 5.0 버전에 추가되었습니다. `Spring WebFlux`는 완전히 논-블록킹하고 Reactive Stream `back pressure`를 지원하고 `Netty`, `Undertow`, `Servlet3.1+ Container`와 같은 서버에서 동작합니다. 두 웹 프레임워크 모두 소스 모듈(`spring-webmvc`,`spring-weflux`)의 이름을 반영하였고, 스프링 프레임워크에서 서로 나란히 공존할 수 있습니다. 각 모듈은 선택사항입니다. 애플리케이션은 하나 또는 다른 모듈을 사용하거나 경우에 따라 두 가지 모두를 사용할 수 있습니다.(예를 들면, `Spring MVC` 컨트롤러와 reactive `WebClient`를 함께 사용할 수 있습니다.)

### 1.1 Overview
왜 Spring WebFlux가 만들어졌을까요?

해답의 일부는 논-블록킹 웹 스택은 적은 수의 스레드로 동시성을 처리하고 보다 적은 하드웨어 자원으로 확장하기 위해 필요하기 때문입니다. `Servlet 3.1`은 논-블록킹 I/O 를 위한 API를 제공하고있습니다. ~~그러나 이 기능을 사용하면 계약이 동기식(`Filter`, `Servlet`) 또는 블로킹(`getParamter`, `getPart`)인 `Servlet API`의 나머지 부분에서 벗어날 수 있습니다. 이것은 새로운 공통 API가 모든 논-블록킹 런타임에 대한 기초 역할을 제공하기 위한 동기부여가 됐다. 그것은 비동기, 논-블록킹에 대해서 처리를 하는 잘하는 서버(`Netty`) 때문에 중요하다.(이부분 다시)~~s