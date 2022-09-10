## Headless browser?

- 브라우저 창을 실제로 운영체제 창으로 띄우지 않고 대신 화면을 그려주는 작업을 가상으로 진행해주는 방법으로 실제 브라우저와 동일하게 동작하지만 창은 뜨지 않는 방식으로 동작

## Headless Chrome?

- Headless 환경에서 Chrome을 사용할 수 있도록 하는 것

## Headless Chrome 사용 시 필요 사항

- JDK 8 이상
- 최신 버전 Chrome
    - Headless mode는 Mac이나 Linux에서는 Chrome 59부터, Windows는 Chrome 60부터 지원
    - Chrome 버전 확인 → chrome://version 주소창에 복붙
- Chrome 버전에 맞는 ChromeDriver
    - [https://chromedriver.chromium.org/downloads](https://chromedriver.chromium.org/downloads)
    - Mac일 경우 위 방법 혹은

```
brew install chromedriver
```

- Selenium

```
implementation ("org.seleniumhq.selenium:selenium-java:4.4.0")
```

- Mac에서 Selenium 실행 시, 'chromedriver'은(는) Apple에서 악성 소프트웨어가 있는지 확인할 수 없기 때문에 열 수 없습니다. 에러 발생 해결방안
    - 시스템 환경설정 > 보안 및 개인 정보 보호 > 일반 탭 > 하단 다음에서 다운로드한 앱 허용 > 확인없이 허용

### Reference

[https://developer.chrome.com/blog/headless-chrome/#using-chromedriver](https://developer.chrome.com/blog/headless-chrome/#using-chromedriver)

[https://www.scrapingbee.com/blog/introduction-to-chrome-headless/](https://www.scrapingbee.com/blog/introduction-to-chrome-headless/)

[https://www.selenium.dev/documentation/](https://www.selenium.dev/documentation/)