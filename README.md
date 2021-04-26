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

    
     

   
