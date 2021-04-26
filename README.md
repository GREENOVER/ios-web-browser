# iOS Web Browser Application
### 웹 브라우저를 앱으로 구현한 프로젝트
[Ground Rule](https://github.com/GREENOVER/ios-web-browser/blob/main/GroundRule.md)
***
#### What I learned✍️
- UIKit
- WebKit
- UIBarButtonItem
- UIActivityIndicator
- URL & String
- Animating
- WebView
- UserInteraction
- touch
- Alert

#### What have I done🧑🏻‍💻
- 로딩화면에서 인디케이터를 구현해보았다.
- String 입력을 통한 URL로의 convert를 구현해보았다.
- StoryBoard에서 적절한 UI 요소들을 배치하고 레이아웃 제약을 걸어보았다.
- UIButton의 활성/비활성을 조건에 따라 구현하였다.
- 웹뷰의 앞/뒤 이동 기능을 구현하였다.
- 유효한 URL 검증을 위해 URL을 lowercase 시킨 뒤 hasPrefix 메서드를 이용하여 해당 URL이 유효한지 구현하였다.
- URL 주소 앞 https:// 프로토콜 생략 시 자동으로 붙여줄 수 있도록 autoChangeUrl 함수를 구현하였다.
- 올바르지 않은 URL 접근 시 얼럿을 통해 알림 메시지를 나타낼 수 있도록 구현하였다.

#### Trouble Shooting👨‍🔧
 - 문제점 (1)
   - 원하는 해당 웹으로 이동하는 시점에 이동이 되는지 안되는지 로드가 될때까지 기다려야하는 문제가 있어 사용자 입장에서는 만약 오래 걸리는 페이지를 로드하면 정상적으로 사용자 동작이 인식되었는지 알 수 없다.
 - 원인 
   - 원하는 웹의 주소를 입력하고 이동 버튼을 클릭하고 해당 웹이 화면에 나오기까지 로드되는 시간 텀이 있는데 그 사이 어떠한 구현도 해주지 않았기에 해당 문제가 발생하였다.
 - 해결방안
   - UIActivityIndicatorView로 인디케이터를 하나 선언해주고 웹뷰가 웹 컨텐츠를 받기 시작하면 해당 인디케이터의 애니메이션이 동작하고 웹이 로드가 끝나면 인디케이터의 애니메이션이 종료되게하여 해결하였다.
     ```swift
     func webView(_ webView: WKWebView, didCommit navigation: WKNavigation!) {
          loadIndicator.startAnimating()
      }
     ```
     webView의 didCommit은 로드하고 싶은 웹뷰가 전달되어 커밋되었단 의미로 로드된게 아닌 요청을 받았을경우임으로 해당 메서드 안에 인디케이터 애니메이션이 시작될 수 있게 구현하였다.
     ```swift
     func webView(_ webView: WKWebView, didFinish navigation: WKNavigation!) {
          loadIndicator.stopAnimating()
      }
     ```
     WebView에 원하는 웹이 로드가 끝나면 인디케이터 애니메이션이 종료될 수 있게 구현하였다.   
     ![TS_1](https://user-images.githubusercontent.com/72292617/116015703-2c53b580-a675-11eb-995f-87e121389c4d.gif)
 - 문제점 (2)
   - URL 입력값이 http://나 https://가 붙지 않았을 경우나 올바르지 않은 URL을 입력했을때 검증할 수가 없어 사용자 입장에서 앱이 제대로 동작하지 않는것처럼 나타나게된다.
 - 원인
   - 유효한 URL에 대한 검증이 없고 http://나 https://가 붙지 않아도 페이지로 이동은 되지만 더 안전한 호출이 되지 않게되었다. 즉 입력받은 URL에 대한 검증이 필요하게 되었다.
 - 해결방안
   - 우선 입력받은 URL에 대해 URL 타입을 String 타입으로 변환해주고 문자열을 검사할때 쓰는 메서드인 hasPrefix 메서드를 구현하여 https://나 https://가 URL 값 앞에 있는지 검증하였다. 그전에 대소문자에 대한 케이스를 따로 다 나누기보다 lowercased() 메서드를 이용하여 전부 소문자로 통일해준 뒤 검증하였다.
     ```swift
     func verifyUrl(_ unidentifiedUrl: String) -> String {
        var identifyUrl = unidentifiedUrl.lowercased()
        if !(identifyUrl.hasPrefix("http://") || identifyUrl.hasPrefix("https://")) {
            showAlertMessage()
            identifyUrl = autoChangeUrl(identifyUrl)
        }
        return identifyUrl
      }
      ```
      위와 같이 검증 메서드안에서는 소문자로 변환해주고 http://나 https://가 접두어로 붙었는지 파악하고 없다면 올바르지 않은 형태라는 얼럿을 노출시키고 자동으로 변환될 수 있게 autoChangeUrl 메서드를 호출하게 된다.   
      ![TS_2](https://user-images.githubusercontent.com/72292617/116016410-7a69b880-a677-11eb-8b7b-dcf3a299b410.gif)   
  - 문제점 (3)
    - 아래와 같이 기기별 시뮬레이터로 앱을 구동 시 버튼들의 간격이 스토리보드와 다르게 어색하게 나타나는걸 볼 수 있다.
    <img width="300" alt="스크린샷 2021-04-26 오전 10 17 01" src="https://user-images.githubusercontent.com/72292617/116016733-8bff9000-a678-11eb-864f-d5212b7517b7.png">
  - 원인
    - 모호한 오토레이아웃이 적용되어 사이즈나 간격의 마진등이 누락된 부분이 있어 시뮬레이터를 각기 다른 기기로 구동 시킬때마다 각 시뮬레이터의 사이즈가 달라지게 됨으로 한쪽으로 치우치거나 스토리보드에서 배치한 UI로 보이지 않게 되는것이다. 즉 오토레이아웃을 적용하긴 했으나 어느 부분들이 아직 완벽하게 적용되지 않은 미비한 상태이다.
  - 해결방안
    - 우선 스토리보드에서 일일히 오토레이아웃을 확인하는것보다 LLDB 디버깅을 이용하여 모호한 오토레이아웃을 아래와 같이 탐색하였다.      
    <img width="300" alt="스크린샷 2021-04-26 오전 10 24 34" src="https://user-images.githubusercontent.com/72292617/116017091-9a01e080-a679-11eb-83bf-c32f61e2236f.png">   
   
    <img width="300" alt="스크린샷 2021-04-26 오전 10 25 17" src="https://user-images.githubusercontent.com/72292617/116017131-b30a9180-a679-11eb-9fb3-43395c22fa41.png">   
    Chisel LLDB 오픈소스 라이브러리의 명령어인 paltrace를 이용하였다. 해당 명령어는 서브뷰의 하이라키(계층구조)를 출력해준다. (오토레이아웃이 잘못되어 있으면 Ambiguous Layouts라고 뜨는걸 볼 수 있음) 위와 같이 참 많이도 AMBIGUOUS라고 모호하다는 표현이 참 많이 나온다... 그만큼 모호한 오토레이아웃이 많다는 의미이다. 해당 부분들에 대해 아래와 같이 쉽게 볼 수 있도록 보더색을 만들어주었다.   
   
    <img width="300" alt="스크린샷 2021-04-26 오전 10 29 58" src="https://user-images.githubusercontent.com/72292617/116017366-5b205a80-a67a-11eb-94e6-5bed4e4e72f0.png">      

    <img width="1270" alt="스크린샷 2021-04-26 오전 10 30 09" src="https://user-images.githubusercontent.com/72292617/116017381-64a9c280-a67a-11eb-914d-189e5818c603.png">   

    위와 같이 alamborder 역시 chisel 명령어로 객체에 보더를 생성해주는 명령어이다. 해당 명령어로 모호한 오토레이아웃이 적용된 객체의 주소값을 이용하여 해당 객체의 보더를 만들어주면 뷰 디버깅 창이나 시뮬레이터에 모호한 부분의 보더가 적용되어 쉽게 보고 수정할 수 있다.



     

   
