## react 렌더링 방식 : CSR ( Client Side Rendering )
 1. 접속요청
 2. index.html 빈껍데기를 브라우저에 전달한다.
 3. 빈화면을 유저에게 먼저 렌더링
 4. 후속으로 서버에서 JavaScript 번들을 전달한다.
 5. 브라우저에서 JavaScript 를 실행 한 후
 6. 컨텐츠를 렌더링 한다 
 ---------------------------------- 여기까지 걸리는 시간을 FCP : First Content for Paint 라고 한다. 
 7. 초기접속 이후 페이지 이동 시
 8. 브라우저에서 JavaScript 를 실행 해서  ( 4번에서 서버에서 JS 번들을 모두 전달 받았다 )
 9. 컨텐츠를 렌더링 한다.

 <img width="1202" height="883" alt="image" src="https://github.com/user-attachments/assets/e3edd6c4-8b92-4d35-a652-6a3d76a278c4" />

**FCP :**
first Contentful Paint
- 3sec 이상일 경우 : 이탈률 32% 증가
- 5sec 이상일 경우 : 이탈률 90% 증가
- 6sec 이상일 경우 : 이달률 106% 증가
- 10sec 이상일 경우 : 이탈률 123% 증가 
 
### 장점

- 페이지 이동이 매우 빠르고 쾌적하다
  
### 단점

- 초기 접속 속도(FCP)가 느리다.
  - FCP 의 속도가 느려질 수록 급속도로 이탈률이 늘어난다.
 

## Next.js 렌더링 방식 : 사전 렌더링
**React 의 렌더링 방식인 CSR 의 단점을 해결**
 1. 접속요청
 2. 서버에서 자바스크립트 코드를 실행 하여 HTML 생성
 3. 생성된 HTML 을 브라우저에 전달
 4. 브라우저에서는 HTML 파일로 화면 렌더링   
------------------------------------- FCP ------------------------------   
     ( 웹과 상호작용은 불가능 : 버튼 클릭이나 페이지 이동은 불가능 )
 5. 이후 서버로부터 접속한 페이지의 JS 를 전달받는다.
 6. 브라우저에서는 JS 를 실행 하여 
 7. 렌더링되어 있는 HTML 요소들과 연결  
 ---------------------------------- 여기까지 걸리는 시간을 TTI : Time To Interative 라고 한다. 
 8. 초기 접속된 페이지의 Link 태그로 연결된 컴포넌트를 Pre-fetching 하여 미리 받아온다.
 9. 이후 페이지 이동 시 ( 아래는  React 와 동일하다. )
 10. 브라우저에서 JavaScript 를 실행 해서  
 11. 컨텐츠를 렌더링 한다.


<img width="1012" height="738" alt="image" src="https://github.com/user-attachments/assets/21da9ea6-962a-4236-9e91-166710022c64" />

### Pre-fetching 이란
- 사전에 페이지를 미리 불러오는 것으로 현재 페이지에 존재하는 **Link 태그**에 연결된 페이지를 **미리 다운로드** 해서 클릭 시 바로 렌더링 되게 하는 방식이다. Link 컴포넌트에 연결된 페이지는 기본적으로 prefetch 가 되어 있으나, 버튼으로 페이지를 불러오는 방식은 prefetch가 안되다.

**수동으로 Pre-fetching**   
- 컴포넌트가 렌더링 될때 1번만 불러와지게 설정하면 prefetch 가 된다.
 ```
  useEffect(() => {
    router.prefetch("/test");
  });
  ```
**Link 태그에서 Prefetch 하지 않으려면 Prefetch 속성을 false로 한다.**
```
<Link href='/test' prefetch={false}>테스트</Link>
```
