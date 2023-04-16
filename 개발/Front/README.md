<details>
<summary>SPA</summary>
<div markdown="1">

* Single Page Application
  * 기존에는 서버로 GET 요청을 보내 원하는 html 파일을 받아 브라우저에서 렌더링하는 구조
  * SPA는 하나의 html 파일에서 component를 교체하는 방식으로 여러 page를 보여주는 방식
  * Ajax 기반으로 구현 가능
    * Ajax는 Asynchronous Javascript and XML의 약자
    * Javascript 기반으로 비동기로 서버에 요청을 보내 원하는 데이터를 가져오는 통신 방법
    * 전 페이지를 다시 요청해 정보를 갱신하지 않고 원하는 정보만 업데이트할 수 있음
  * 장점
    * 필요한 component만 업데이트하면 되기 때문에 성능적인 부분에서 유리함
    * 서버가 템플릿 연산을 수행하지 않아도 되기 때문에 서버의 부하를 덜 수 있음
    * 모바일 서비스 제공 시 같은 API를 공유하는데 용이함
  * 단점
    * 빌드한 Javascript 파일을 한 번에 받기 때문에 초기 구동 느림
    * 검색엔진 최적화(SEO)에 불리
      * Javascript를 읽지 못하는 검색엔진의 경우 인식하지 못함, 읽을 수 있더라도 오버헤드가 큼
      * 단일 템플릿을 사용하기 때문에 다른 페이지에 동일한 메타 데이터를 삽입하게 됨

</div>
</details>