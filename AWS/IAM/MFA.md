## 2단계 인증
- 2FA라고도 부름
- 'IAM > 사용자 > 보안자격증명'에서 설정할 수 있다.
- 설정 후에는 로그인 후 2FA 인증 어플의 코드를 입력해야 로그인을 할 수 있다.

## MFA 인증
- 여러번의 인증 절차를 거쳐 인증을 하는 방법이다.
- 2단계 인증은 일반적으로 아이디/비밀번호 로그인에 MFA 애플리케이션이 제공하는 코드를 통한 인증을 해야 한다.

## 설정 방법
- 2FA 설정이 되어 있지 않다면 설정을 IAM 메인 화면에서 설정하라는 컨텐츠가 바로 나온다.
- '루트 사용자에 대해 MFA 추가'항목을 클릭한다. '내 보안 자격 증명'페이지에서 'MFA 할당'이라는 버튼이 나올 것.
- 디바이스 선택 화면에서 '인증관리자앱'을 선택한다. 다음 페이지에서 QR코드 인증이 아닌 '보안 키 보기'를 클릭해서 보안키를 `authy` 어플의 코드에 입력한다.
- `authy` 어플에서 인증 이름을 지어주고 `6digit`를 선택한다. 그러면 어플에서 6자리의 코드가 생성된다. 시간에 지남에 따라 코드가 바뀐다.
- 시간 간격에 따라서 달라진 서로 다른 6자리 코드를 AWS의 'MFA 코드 1'과 'MFA 코드 2'에 각각 넣어준다. 최대한 근접한 시간의 코드를 입력해 주어야 인증이 된다.

## 2단계 인증 툴
- [authy](https://authy.com/) : 애플리케이션을 다운로드하여 실행한다. 단, 전화번호 인증을 해야하기 때문에 번호가 바뀔 염려가 있다면 인증에 주의 해야 한다. 전화 및 문자로 `authy` 어플에 자신의 고유한 아이덴티티를 등록해야 한다.
- [authenticator](https://chrome.google.com/webstore/detail/authenticator/bhghoamapcdpbohphigoooaddinpkbai) : 크롬 익스텐션
- [google authenticator](https://apps.apple.com/us/app/google-authenticator/id388497605) : 모바일 어플리케이션

## Reference
- https://docs.aws.amazon.com/ko_kr/IAM/latest/UserGuide/id_credentials_mfa.html
