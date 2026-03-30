<div id="top"></div>

<h2>📌 Spring Boot: ReadSync 유저 및 보안 모듈 (User & Security)</h2>

<ul>
<li><b>프로젝트명</b>: ReadSync (AI 기반 소셜 독서 플랫폼 백엔드)</li>
<li><b>담당 역할</b>: 유저 도메인, 소셜 로그인(OAuth 2.0), JWT 인증/인가, 중복 로그인 제어 로직 전담</li>
<li><b>개발 기간</b>: (202X.XX.XX ~ 202X.XX.XX)</li>
<li>
🔗 <b>포트폴리오 GitHub</b>:
<a href="https://github.com/jaeseung9/ReadSync-backend" target="_blank" rel="noreferrer">
https://github.com/jaeseung9/ReadSync-backend
</a>
</li>
<li>
🔗 <b>API 명세서 / 노션 등 (선택)</b>:
<a href="#" target="_blank" rel="noreferrer">
(링크가 있다면 입력해주세요)
</a>
</li>
</ul>

<h3>✅ 주요 개발 내용</h3>
<ul>
<li>Spring Security와 JWT를 활용한 <b>Stateless 기반 인증/인가 아키텍처</b> 설계 및 구현</li>
<li>OAuth 2.0 프로토콜을 이용한 <b>소셜 로그인 연동</b> (카카오, 구글 등) 및 자동 회원가입 로직 구축</li>
<li>Refresh Token 관리를 통한 <b>중복 로그인 방지</b> 및 다중 접속 제어 (Redis 또는 DB 활용)</li>
<li>Spring Data JPA를 활용한 <code>User</code> 엔티티 설계 및 데이터 영속성, 객체 지향적 쿼리 최적화</li>
</ul>

<h3>📚 학습 및 성과</h3>
<ul>
<li><b>Spring Security Filter Chain</b>의 동작 원리를 깊이 있게 이해하고 커스텀 필터 적용</li>
<li>JWT의 생명주기(Access/Refresh Token)를 분리하여 <b>보안성과 사용자 편의성</b>을 동시에 확보하는 방법 학습</li>
<li>다중 클라이언트 환경에서 발생할 수 있는 <b>동시 접속 이슈 및 세션 탈취 방지</b>를 위한 방어 로직 설계</li>
<li>JPA의 영속성 컨텍스트를 이해하고, 트랜잭션 범위 내에서 안전하게 유저 상태를 변경하는 방법 숙달</li>
</ul>

<hr/>

<h1 align="center">🔐 ReadSync Backend — User & Security Module</h1>
<p align="center">
<b>Spring Security + JWT + OAuth2.0</b>으로 구현한 안전하고 확장성 높은 인증 시스템


소셜 간편 로그인 · 상태 비저장 인증 · 중복 로그인 방지 · JPA 유저 관리
</p>
<p align="center">
<a href="#demo" target="_blank" rel="noreferrer">▶ (Swagger/Postman 등 테스트 데모 영상이 있다면 링크 삽입)</a>
</p>

<hr/>

<h2 id="tldr">🧭 TL;DR (빠른 소개)</h2>
<ul>
<li><b>핵심 스택</b>: Java, Spring Boot, Spring Security, Spring Data JPA, JWT, OAuth2 Client, (Redis/MySQL)</li>
<li><b>핵심 기능</b>: 소셜 인증, Access/Refresh 토큰 발급 및 검증, 중복 로그인 강제 로그아웃, 권한(Role)별 API 접근 제어</li>
<li><b>설계</b>: Stateless 아키텍처, <code>SecurityFilterChain</code> 커스터마이징, 관심사 분리(인증 레이어와 비즈니스 레이어)</li>
<li>자세한 기술/설계는 아래 <a href="#full">📚 상세 내용</a>에 포함 (접기/펼치기)</li>
</ul>
<p><a href="#top">⬆ Back to top</a></p>

<hr/>

<h2 id="toc">📚 Table of Contents</h2>
<ol>
<li><a href="#about">프로젝트 소개</a></li>
<li><a href="#demo">데모 & 시스템 아키텍처</a></li>
<li><a href="#features">주요 특징</a></li>
<li><a href="#stack">개발 환경 & 기술 스택 (요약)</a></li>
<li><a href="#structure">프로젝트 구조</a></li>
<li><a href="#controls">API & 인증 플로우 (요약)</a></li>
<li><a href="#run">실행 방법 (요약)</a></li>
<li><a href="#design">보안 설계 개요 (요약)</a></li>
<li><a href="#collision">중복 로그인 & 토큰 관리 (요약)</a></li>
<li><a href="#learned">학습 내용 (요약)</a></li>
<li><a href="#full">📚 상세 내용 (전체 본문, 접기/펼치기)</a></li>
<li><a href="#insights">🌱 느낀점 및 트러블 슈팅 (전체 본문, 접기/펼치기)</a></li>
<li><a href="#credits">👏 크레딧 (전체 본문, 접기/펼치기)</a></li>
</ol>
<p><a href="#top">⬆ Back to top</a></p>

<hr/>

<h2 id="about">1) 프로젝트 소개</h2>
<p>
독서를 장려하는 AI 기반 소셜 플랫폼 <b>ReadSync</b>의 백엔드 시스템입니다.
저는 이 중 전체 시스템의 안전한 문지기 역할을 하는 <b>유저 도메인과 보안(Security) 인프라</b>를 전담하여,
확장성 높은 Stateless 인증 환경과 소셜 로그인 연동을 성공적으로 구축했습니다.
</p>
<p><a href="#top">⬆ Back to top</a></p>

<hr/>

<h2 id="demo">2) 데모 & 시스템 아키텍처</h2>
<p><i>(이곳에 Swagger API 명세 스크린샷, ERD 유저 테이블 스크린샷, 또는 인증 흐름도 아키텍처 이미지를 삽입하세요.)</i></p>

<div>
</div>
<div>
</div>
<p><a href="#top">⬆ Back to top</a></p>

<hr/>

<h2 id="features">3) 🚀 주요 특징</h2>
<ul>
<li>🛡 <b>안전한 API 보호</b>: Custom JwtFilter를 통한 매 요청 토큰 검증 및 권한 인가</li>
<li>🔑 <b>소셜 간편 로그인</b>: OAuth2 Client를 활용한 플랫폼(구글, 카카오 등) 연동 및 자동 회원가입</li>
<li>⛔ <b>중복 로그인 제어</b>: 이전 접속자의 Refresh Token을 만료/무효화하여 다중 기기 비정상 접근 차단</li>
<li>💾 <b>JPA 최적화</b>: Spring Data JPA를 이용한 안전한 유저 정보 영속성 관리 및 수정</li>
<li>🔄 <b>토큰 갱신(Reissue)</b>: Access Token 만료 시, 유효한 Refresh Token을 검증해 중단 없는 서비스 제공</li>
</ul>
<p><a href="#top">⬆ Back to top</a></p>

<hr/>

<h2 id="stack">4) 🧱 개발 환경 & 기술 스택 (요약)</h2>
<ul>
<li><b>Language</b>: Java (JDK 17)</li>
<li><b>Framework</b>: Spring Boot (3.x)</li>
<li><b>Security</b>: Spring Security, OAuth2 Client, JJWT</li>
<li><b>Database & ORM</b>: MySQL (또는 PostgreSQL), Spring Data JPA</li>
<li><b>Cache / Session</b>: (Redis - 사용하셨다면 기재, 아니면 삭제)</li>
</ul>
<p><a href="#full">➡ 상세 스택으로 이동</a></p>
<p><a href="#top">⬆ Back to top</a></p>

<hr/>

<h2 id="structure">5) 📁 프로젝트 구조 (Security & User 중심)</h2>
<pre><code>plaintext
ReadSync-backend/
├── src/main/java/com/readsync/
│   ├── global/
│   │   ├── security/            # Security 설정 및 Custom Filter
│   │   │   ├── SecurityConfig.java
│   │   │   ├── JwtAuthenticationFilter.java
│   │   │   └── CustomAuthenticationEntryPoint.java
│   │   ├── jwt/                 # JWT 발급 및 검증 유틸
│   │   │   └── JwtProvider.java
│   │   └── oauth2/              # 소셜 로그인 핸들러 및 서비스
│   │       ├── CustomOAuth2UserService.java
│   │       └── OAuth2SuccessHandler.java
│   ├── domain/
│   │   ├── user/                # 유저 도메인
│   │   │   ├── entity/User.java
│   │   │   ├── repository/UserRepository.java
│   │   │   ├── service/UserService.java
│   │   │   └── controller/UserController.java
│   │   └── auth/                # 로그인, 토큰 재발급 컨트롤러
└── README.md
</code></pre>
<p><a href="#top">⬆ Back to top</a></p>

<hr/>

<h2 id="controls">6) 🔄 API & 인증 플로우 (요약)</h2>
<ul>
<li><b>소셜 로그인 시도</b>: <code>/oauth2/authorization/{provider}</code> → 인증 성공 시 JWT 발급 반환</li>
<li><b>API 접근</b>: HTTP Header <code>Authorization: Bearer {AccessToken}</code> 포함하여 요청</li>
<li><b>토큰 재발급</b>: <code>/api/v1/auth/reissue</code> (Refresh Token 검증 후 새 토큰 발급)</li>
<li><b>로그아웃</b>: <code>/api/v1/auth/logout</code> (저장소의 Refresh Token 삭제 처리)</li>
</ul>
<p><a href="#top">⬆ Back to top</a></p>

<hr/>

<h2 id="run">7) 💻 실행 방법 (요약)</h2>
<p><b>요구사항</b></p>
<ul>
<li>Java 17 이상</li>
<li>MySQL (또는 사용하신 DB) 실행</li>
</ul>

<p><b>빌드 & 실행</b></p>
<pre><code>bash
git clone https://github.com/jaeseung9/ReadSync-backend.git
</code></pre>
<ol>
<li><code>src/main/resources/application-oauth.yml</code> (또는 로컬 환경변수)에 OAuth2 Client ID/Secret 설정</li>
<li>DB 연결 정보(URL, Username, Password) 설정</li>
<li><code>./gradlew build</code> 후 <code>java -jar build/libs/ReadSync-0.0.1-SNAPSHOT.jar</code> 실행</li>
</ol>
<p><a href="#full">➡ 실행 상세로 이동</a></p>
<p><a href="#top">⬆ Back to top</a></p>

<hr/>

<h2 id="design">8) 🧩 보안 설계 개요 (요약)</h2>
<ul>
<li><b>인증 파이프라인</b>: <code>UsernamePasswordAuthenticationFilter</code> 이전에 커스텀 <code>JwtFilter</code>를 배치하여 토큰을 먼저 가로채고 검증.</li>
<li><b>OAuth2 통합</b>: <code>OAuth2UserService</code>를 상속받아 소셜 플랫폼(카카오, 구글 등)에서 가져온 속성(Attributes)을 내부 <code>User</code> 엔티티로 자동 매핑.</li>
<li><b>예외 처리</b>: 토큰 만료, 변조, 권한 부족 등의 상황을 <code>AuthenticationEntryPoint</code>와 <code>AccessDeniedHandler</code>로 캐치하여 클라이언트에게 명확한 규격의 에러 JSON 반환.</li>
</ul>
<p><a href="#top">⬆ Back to top</a></p>

<hr/>

<h2 id="collision">9) ⚙️ 중복 로그인 & 토큰 관리 (요약)</h2>
<ul>
<li><b>중복 제어</b>: 새로운 기기에서 로그인 시 발급되는 새로운 Refresh Token으로 기존 저장소(DB/Redis)의 토큰을 덮어씌움. 이전 기기에서 Access Token 만료 후 재발급(Reissue) 요청 시, 토큰 불일치로 탈취/중복 판단하여 강제 로그아웃 처리.</li>
<li><b>보안성 강화</b>: Access Token은 짧은 수명(예: 30분)으로, Refresh Token은 긴 수명(예: 2주)으로 설정하여 탈취 피해 최소화.</li>
</ul>
<p><a href="#top">⬆ Back to top</a></p>

<hr/>

<h2 id="learned">10) 📖 학습 내용 (요약)</h2>
<ul>
<li>Spring Security의 <b>FilterChain Proxy</b> 구조 및 인증/인가(Authentication/Authorization) 아키텍처</li>
<li>HTTP의 무상태성(Stateless)과 <b>JWT의 구조(Header, Payload, Signature)</b> 완벽 이해</li>
<li>JPA 영속성 컨텍스트(Persistence Context)의 지연 쓰기(Dirty Checking)를 활용한 유저 정보 업데이트 로직</li>
<li>OAuth2.0 인가 코드(Authorization Code) 기반의 서버 대 서버 통신 흐름</li>
</ul>
<p><a href="#top">⬆ Back to top</a></p>

<hr/>

<h2 id="full">11) 📚 상세 내용 (전체 본문, 접기/펼치기)</h2>
<details>
<summary><b>클릭하여 펼치기</b> — 보안 아키텍처 / 토큰 관리 로직 / JPA 매핑 상세</summary>


<h3 id="full-stack">A. 사용 기술 스택 상세</h3>
<ul>
<li><b>Spring Security</b>: 엔드포인트 보호 및 전역적인 보안 설정. CSRF 비활성화(REST API 특성), SessionManagement를 Stateless로 설정.</li>
<li><b>JJWT (Java JWT)</b>: 안전한 서명 알고리즘(HS512 등)을 사용하여 토큰의 생성, 파싱, Claims 추출 로직 구현.</li>
<li><b>Spring Data JPA</b>: 유저 정보 조회 시 발생할 수 있는 N+1 문제를 고려한 Fetch Join 전략(필요시) 및 Repository 계층 구현.</li>
</ul>

<h3 id="full-oop">B. 보안 아키텍처 구조</h3>
<ul>
<li><b>JwtProvider</b>: 토큰의 생성 및 유효성 검증 책임을 갖는 유틸리티 클래스 (SRP 준수).</li>
<li><b>JwtAuthenticationFilter</b>: HTTP Request Header에서 토큰을 추출하고, <code>JwtProvider</code>를 통해 검증한 뒤 <code>SecurityContextHolder</code>에 <code>Authentication</code> 객체를 저장하는 핵심 필터.</li>
<li><b>OAuth2SuccessHandler</b>: 소셜 로그인 성공 직후 호출되며, 회원가입 처리 후 최종적으로 클라이언트(프론트엔드)로 JWT를 전달(리다이렉트 또는 JSON 응답)하는 역할.</li>
</ul>

<h3 id="full-collision">C. 중복 로그인 & 세션 제어 상세 로직</h3>
<ol>
<li>사용자 A가 '기기 1'에서 로그인 → RefreshToken_1 생성 및 서버 저장소(DB/Redis) 기록.</li>
<li>사용자 A가 '기기 2'에서 로그인 → RefreshToken_2 생성 및 서버 저장소 갱신 (기존 Token_1은 무효화).</li>
<li>'기기 1'의 AccessToken 만료 후 Reissue 요청 → 서버가 받은 Token_1과 저장소의 Token_2 불일치 확인.</li>
<li>서버는 재발급을 거부하고 401 Unauthorized 반환 → 프론트엔드에서 '다른 기기에서 로그인되어 로그아웃됩니다' 알림 처리.</li>
</ol>

<p><a href="#top">⬆ Back to top</a></p>
</details>

<hr/>

<h2 id="insights">12) 🌱 느낀점 및 트러블 슈팅 (Trouble Shooting)</h2>
<ul>
<li><b>OAuth2와 JWT 통합의 딜레마</b>: Spring Security가 제공하는 기본 OAuth2는 세션 기반(JSESSIONID)으로 동작하려 하여, API 서버가 지향하는 Stateless(JWT) 구조와 충돌하는 문제를 겪었습니다. 이를 해결하기 위해 <code>OAuth2AuthenticationSuccessHandler</code>를 직접 구현하여, 소셜 인증이 끝난 직후 세션을 파기하고 커스텀 JWT를 생성하여 프론트엔드로 전달하도록 인증 흐름을 성공적으로 재설계했습니다.</li>
<li><b>중복 로그인 제어 로직 최적화</b>: 단순히 JWT만 사용하면 토큰을 서버에서 제어할 수 없다는 한계(Stateless의 단점)를 깨달았습니다. 이를 보완하기 위해 Refresh Token을 데이터베이스(또는 Redis)에 저장하여 상태를 일부 관리(Stateful)하는 하이브리드 방식을 적용, 비정상적인 다중 접속 기기를 효과적으로 차단할 수 있었습니다.</li>
<li><b>JPA 엔티티 설계의 중요성</b>: 소셜 로그인 특성상 비밀번호가 없는 유저, 플랫폼별 다른 식별자(Provider ID) 등을 어떻게 하나의 <code>User</code> 엔티티로 통합할지 고민했습니다. 확장성을 고려해 Provider 필드와 Role(권한) 필드를 Enum으로 관리하여, 향후 다양한 소셜 플랫폼이 추가되더라도 DB 스키마 변경 없이 유연하게 대처할 수 있도록 설계했습니다.</li>
</ul>
<p><a href="#top">⬆ Back to top</a></p>

<hr/>

<h2 id="credits">13) 👏 크레딧</h2>
<p>
Author: 서재승 (Seo Jae Seung)


Email: <a href="mailto:seojaeseung9@gmail.com">seojaeseung9@gmail.com</a>


Blog: <a href="https://seungcoding.tistory.com/" target="_blank" rel="noreferrer">https://seungcoding.tistory.com/</a>


GitHub: <a href="https://github.com/jaeseung9" target="_blank" rel="noreferrer">https://github.com/jaeseung9</a>
</p>
<p><a href="#top">⬆ Back to top</a></p>
