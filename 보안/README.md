* **Encryption algorithm**
  * Symmetric
    * 대표적으로 DES
    * **todo:** details..
    * 3-DES, AES...
  * Asymmetric
    * 대표적으로 RSA
    * **todo:** details...
* **HTTPS**
  * 클라이언트(주로 브라우저)가 서버로 특정 자원을 요청함
  * 서버는 클라이언트가 secure connection을 만들 수 있는 공개 키가 포함된 인증서를 보냄
  * 클라이언트는 인증서가 CA가 발급한 유효한 인증서인지 확인
  * 유효성이 확인되면 클라이언트와 서버는 공개 키를 이용해 SSL/TLS handshake를 통해 공유 키를 교환
  * 이 단계에서 공유된 키를 이용해 데이터를 암호화 함
* **SSL/TLS handshake**
  * 클라이언트가 시작 - 서버에게 사용할 암호화 기법, 프로토콜 등 옵션을 보냄 - client hello
  * 서버는 받은 옵션 중 하나를 선택하고 CA에서 발급받은 인증서를 전송함
  * 클라이언트는 인증서의 유효성을 확인하고, 세션 키를 인증서에 포함된 서버의 공개 키로 암호화해 전송
  * 서버는 자신의 비밀 키로 세션 키를 복호화해 획득함
  * cipher suite를 변경하고 handshake 종료