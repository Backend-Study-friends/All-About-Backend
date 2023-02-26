# Containerless한 스프링 부트

![ldld](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FEnLog%2FbtrZNLbJJEo%2Fo6EWLLu7ZsuvpKFwbWZXyK%2Fimg.png "이미지 설명(title)")


```java
@SpringBootApplication
public class AppApplication {

	public static void main(String[] args) {
		SpringApplication.run(AppApplication.class, args);
	}

}
```

기본적으로 스프링 부트로 프로젝트를 생성하면 위와 같은 클래스를 자동으로 생성해줍니다. 그 안에는 메인 함수가 있고, 그 메인 함수를 실행하면 자동으로 서버가 뜨게 됩니다. 그러나

```java
SpringApplication.run(HellobootApplication.class, args);
```

이 줄이 어떤 작용을 하는지 자세히는 모르는 사람이 많습니다. 

 _**저 줄이 어떤 작용을 하는지 알게 된다면 스프링 부트가 어떻게 Containerless를 제공하는지 알 수 있게 됩니다.**_ 차근차근 어떻게 Containerless를 제공하는지 알아봅시다.

### 학습목표

---

-    스프링 부트가 왜 나왔는지와 철학을 통해 containerless한 스프링 부트를 이해할 수 있다.
-   스프링부트가 어떻게 containerless를 제공하는지 코드를 통해 알 수 있다.

### 스프링 부트가 나오게 된 배경과 철학(핵심 키워드)

---

#### 스프링 부트가 나오게 된 배경

먼저 스프링 부트가 왜, 어떻게 세상에 나오게 됐는지 알아보겠습니다.

![ldld](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbox79A%2FbtrZLS92y93%2FfBz3ar7VhslDIeDadxYGVk%2Fimg.png)

놀랍게도 스프링 부트의 시작은 **한 깃허브 이슈**였습니다. 2012년 10월에 한 개발자는 스프링이 가지고 있는 현 문제들을 들이밀면서 스프링에 개혁이 필요하다고 주장하고 있습니다. 개발자는 서블릿 컨테이너와 같은 옛날 프레임워크 개념을 알고있는 사람들이 드물어지고 있다. 그렇기에 _**서블릿 컨테이너 없는 웹 어플리케이션**_ 아키텍쳐를 원한다고 글을 작성하였죠.

![ldld](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FzzwP7%2FbtrZJVGeRkj%2Flb4bJGfZPoLdsvycxP0G20%2Fimg.png)

그러자 스프링 부트 프로젝트 리더인 필 웹이라는 사람이 스프링 프레임워크를 뜯어 고치는 것보다 새로인 프로젝트인 스프링 부트를 시작하는게 낫다고 판단했다는 댓글을 남깁니다.

#### 스프링 부트의 가장 큰 철학 중 하나인 Containerless

1\. Containerless

![ldld](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fb49QMU%2FbtrZJCG4U1S%2F9lLrfeGOYkv29sgK955xu0%2Fimg.png)


톰캣과 같은 서블릿 컨테이너가 서블릿에 연결시켜주고, 서블릿이 응답한 것을 클라이언트가 받아갑니다. 그러나 [서블릿이 가지고 있는 여러 한계점](https://yiyj1030.tistory.com/443)을 극복하기 위해 사람들이 스프링을 만들어서 아래와 같은 구조로 서버를 구성하게 됩니다.

![ldld](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FxpEOA%2FbtrZ0JqdShy%2FYcofJjpMFVEydSINcAylp0%2Fimg.png)

위와 같이 스프링 컨테이너를 뒤에 두고 서블릿 컨테이너와 상호작용하게 하게 하였습니다. 그러나 개발자인 우리는 스프링 컨테이너 구성하는 것에 힘을 쏟고, 고민만 해도 벅찬데 서블릿 컨테이너를 구성하는데에도 많은 시간을 들여야 했죠. 그래서 **서블릿 컨테이너 설정 시간과 고민을 없애주자.**라는게 containerless 개념이라고 생각하면 됩니다.

### Containerless를 어떻게 도와주는지 알아보자

---

스프링 부트가 서블릿 어떻게 containerless를 제공하는지 알기 위해서 스프링이 작동하는 방법을 코드로 작성해보겠습니다.

#### **빈 서블릿 컨테이너를 띄우기**

```java
TomcatServletWebServerFactory serverFactory = new TomcatServletWebServerFactory();
WebServer webServer = serverFactory.getWebServer();
webServer.start();
```

![ldld](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbEEZod%2FbtrZQcz39ob%2FKfc9LtuX3tpkLXiiYaXo80%2Fimg.png)

위와 같은 빈 서블릿 컨테이너(톰캣)를 띄웠습니다.

#### **간단한 서블릿 띄우기**

```java
TomcatServletWebServerFactory serverFactory = new TomcatServletWebServerFactory();
WebServer webServer = serverFactory.getWebServer(servletContext -> {
  servletContext.addServlet("hello", new HttpServlet() {
     @Override
     protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String name = req.getParameter("name");
        resp.setStatus(HttpStatus.OK.value());
        resp.setHeader(HttpHeaders.CONTENT_TYPE, MediaType.TEXT_PLAIN_VALUE);
        resp.getWriter().println("Hello " + name);
     }
  }).addMapping("/hello");
});
webServer.start();
```

[##_Image|kage@y9dOw/btrZSTGZuny/NXoNvlbMzwQmNq7WSdXCW0/img.png|CDM|1.3|{"originWidth":972,"originHeight":772,"style":"alignCenter","width":412,"height":327,"filename":"스크린샷 2023-02-19 오후 9.43.11.png"}_##]

그리고 위와 같은 코드를 작성하면 httpServlet을 띄울 수 있게 됩니다. 간단하게 /hello라는 경로로 오는 요청에 name 파라미터를 가지고 들어오면 hello + "name"을 띄워주는 간단한 예제입니다. 

#### **DispatcherServlet을 이용하여 Containerless화**

```java
@RequestMapping("/hello")
public class HelloController {

   @GetMapping
   @ResponseBody
   public String hello(String name) {
     return "Hello " + name;
   }
}
```

```java
public static void main(String[] args) {
   GenericWebApplicationContext applicationContext = new GenericWebApplicationContext();
   applicationContext.registerBean(HelloController.class);
   applicationContext.refresh();

   TomcatServletWebServerFactory serverFactory = new TomcatServletWebServerFactory();
   WebServer webServer = serverFactory.getWebServer(servletContext -> {
      servletContext.addServlet("dispatcherServlet",
            new DispatcherServlet(applicationContext)
      ).addMapping("/*");
   });
   webServer.start();
}
```

위의 코드를 보시면 HelloController를 빈으로 등록하고 그냥 DispatcherServlet이라는 서블릿을 만들고, 그 안에 스프링 컨테이너를 넣는 것을 볼 수 있습니다.

![ldld](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Flsft6%2FbtrZSSux29r%2Fw6opsTHTblfFyRA0Qk4Uj1%2Fimg.png)

위와 같은 상황이죠. **디스패처 서블릿은 빈을 다 뒤져서 웹 요청을 처리할 수 있는 빈을 찾습니다**. 그래서 위와 같이 DispatcherServlet이 HelloController라는 빈을 찾아서 매핑 처리를 할 수 있는 것입니다.

#### **스프링에서 제공하는 메서드와 같이 리팩토링**

```java
public static void run(Class<?> applicationClass, String... args) {
   AnnotationConfigWebApplicationContext applicationContext = new AnnotationConfigWebApplicationContext() {
      @Override
      protected void onRefresh() {
         super.onRefresh();
         ServletWebServerFactory serverFactory = this.getBean(ServletWebServerFactory.class);
         DispatcherServlet dispatcherServlet = this.getBean(DispatcherServlet.class);

         //ApplicationContextAware에서 setApplicationContext란 메서드가 알아서 넣어줌
         //dispatcherServlet.setApplicationContext(this);

         WebServer webServer = serverFactory.getWebServer(servletContext -> {
            servletContext.addServlet("dispatcherServlet", dispatcherServlet)
                  .addMapping("/*");
         });
         webServer.start();
      }
   };
   applicationContext.register(applicationClass);
   applicationContext.refresh();
}
```

위와 같이 메서드의 형태로 코드를 리팩토링 하게 된다면

```java
public static void main(String[] args) {
   run(HellobootApplication.class, args);
}
```

우리가 메인 메서드에서 많이 봤던 모양으로 리팩토링이 됩니다.

### 마무리

---

간단하게 스프링 부트가 우리가 Servlet을 신경쓰지않는 개발, 즉 Containerless한 개발을 할 수 있게 돕는지 알아봤습니다. 옛날에 공부한 개념을 다시 상기하여 정리하려고 하니 뒤죽박죽 섞여버렸네요... 다시 깔끔하게 정리하겠습니다!