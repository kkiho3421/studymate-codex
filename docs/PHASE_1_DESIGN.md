# Phase 1 — 요구사항 분석 및 설계 제안

기준: PROJECT_SPEC.md v1.0 전체. 작성일: 2026-09-29. 담당 Agent: Codex.
이 문서는 설계 제안이며 애플리케이션 구현은 포함하지 않는다.
중요 정책은 아래 승인 대기 목록으로 관리한다. Phase 2 시작과 main 병합은 별도 승인 대상이다.

## 1. 요구사항과 범위

- User: username/password/password_confirmation으로 가입, 로그인, 로그아웃.
- Certificate: 본인 자격증 등록·목록·상세·수정·삭제.
- Subject: 본인 자격증에 속한 과목 등록·목록·상세·수정·삭제.
- StudyRecord: 과목별 날짜·양의 정수 공부 시간(분)·메모 등록·목록·수정·삭제.
- QuestionRecord: 과목별 날짜·총 문제·정답·메모 등록·목록·수정·삭제.
- Dashboard: 본인 전체/오늘/자격증별/과목별 공부 시간과 합계 기반 정답률.
- 모든 데이터 작업에 서버 측 소유권 검사. 입력 검증, 한국어 오류, 모바일 기본 사용 지원.
- 지정 스택: Python/FastAPI/SQLAlchemy/SQLite, HTML/CSS/Vanilla JS, pytest.
- 이메일 인증, 소셜 로그인, 비밀번호 재설정, 결제, 공유, 관리자 기능은 범위에 추가하지 않는다.
- 다른 Agent의 코드·저장소·결과는 참고하지 않는다.

## 2. 승인 대기 정책

명세서 §22에 따라 다음 사항은 확정하지 않고 사용자에게 질문했다.

1. 오늘의 기준: Asia/Seoul 제안. 서버 로컬 시간 사용은 단순하지만 배포 위치에 따라 통계가 달라진다. 사용자별 시간대는 유연하지만 설정 기능이 추가된다.
2. 삭제: 상위 항목을 삭제하면 하위 과목/기록도 삭제하고 UI에서 영향과 되돌릴 수 없음을 확인받는 방식 제안. RESTRICT는 기록을 보호하지만 개별 삭제가 번거롭다. soft delete는 복구가 가능하지만 모든 조회/통계의 필터와 유지 비용이 늘어난다.
3. 인증: DB 저장 opaque session, 24시간 고정 만료 제안. 브라우저를 닫아도 만료 전까지 쿠키 유지. 매 요청 연장하지 않으며 로그인 시 새 토큰을 발급한다.
4. username: 영문·숫자·밑줄 3~50자, 앞뒤 공백 제거 후 소문자로 정규화해 대소문자 구분 없이 중복 검사 제안. 한글 허용 여부와 표시 이름 보존은 미확정.
5. password: 8~128자, 확인값 일치, 공백을 포함해 입력값 그대로 해싱 제안. 임의의 문자 조합 강제는 하지 않는다.

추가 입력 경계 제안: 이름 1~100자, 설명 2,000자 이하, 메모 5,000자 이하. 날짜는 유효한 ISO 날짜를 받되 명세에 없는 미래 날짜 금지는 추가하지 않는다. 정수 필드는 SQLite 정수 범위를 벗어나면 저장 전에 오류로 처리한다. 이 경계도 구현 전 정책 검토 대상이다.

응답이 없으면 위 정책은 승인 대기로 남겨 두며, 설계 문서 작성만 완료한다.

## 3. 예상 프로젝트 구조와 역할

아래는 Phase 2 이후 만들 구조로, 현재 존재하는 구현 파일을 의미하지 않는다.

```text
StudyMate-Codex/
  PROJECT_SPEC.md
  README.md
  requirements.txt
  requirements-dev.txt
  .env.example
  .gitignore
  app/
    __init__.py
    main.py
    config.py
    database.py
    dependencies.py
    models/
      __init__.py
      user.py
      session.py
      certificate.py
      subject.py
      study_record.py
      question_record.py
    schemas/
      auth.py
      certificate.py
      subject.py
      study_record.py
      question_record.py
    routes/
      auth.py
      certificates.py
      subjects.py
      study_records.py
      question_records.py
      dashboard.py
    services/
      auth.py
      ownership.py
      certificates.py
      subjects.py
      study_records.py
      question_records.py
      dashboard.py
    security/
      passwords.py
      sessions.py
      csrf.py
    templates/
      base.html
      auth/
      certificates/
      subjects/
      study_records/
      question_records/
      dashboard.html
      errors/
    static/
      css/main.css
      js/main.js
  tests/
    conftest.py
    test_auth.py
    test_certificates.py
    test_subjects.py
    test_study_records.py
    test_question_records.py
    test_access_control.py
    test_dashboard.py
    test_integrity.py
    test_security.py
  docs/
    PHASE_1_DESIGN.md
    PHASE_REPORTS.md
```

- main.py는 앱 생성, 라우터 등록, 정적 파일, 예외 처리와 생명주기 연결만 담당한다.
- config.py는 환경 설정, database.py는 연결·FK 활성화·요청 단위 SQLAlchemy Session을 담당한다.
- dependencies.py는 DB와 로그인 사용자 의존성을 제공한다. SQLAlchemy Session과 인증 세션은 별개 개념이므로 이름을 구분한다.
- models는 저장 구조, schemas는 입력 규칙, routes는 HTTP와 form 재표시, services는 소유권·CRUD·집계를 담당한다.
- security는 비밀번호·인증 토큰·CSRF 처리를 분리한다. templates/static은 화면과 표시 보조를 담당한다.
- tests는 독립 DB에서 동작하는 검증, docs는 설계와 Phase 보고를 저장한다.

책임 분리로 테스트와 변경 범위를 좁히되, 단순 CRUD를 위해 범용 repository 계층이나 불필요한 추상 클래스는 만들지 않는다.
기능 구현 시 위 디렉터리를 필요한 순서로 생성하며 빈 파일을 미리 대량 생성하지 않는다.

## 4. 데이터베이스 테이블

공통: id는 INTEGER primary key, 모든 FK는 NOT NULL 및 인덱스 설정. created_at과 세션 시각은 UTC로 생성하고 SQLite 저장/복원 규약을 한 곳에서 통일한다. 학습 날짜는 시각 없는 DATE이다. 선택 설명/메모는 NOT NULL 빈 문자열 기본값으로 단순화한다.

### users

- id, username VARCHAR(50) NOT NULL UNIQUE, password_hash TEXT NOT NULL, created_at DATETIME NOT NULL.
- username 정규화 후 UNIQUE 적용. 사전 조회 외에도 동시 가입 시 IntegrityError를 처리하고 rollback한다.
- password_confirmation은 검증만 하고 저장하지 않는다.

### certificates

- id, user_id FK users.id, name VARCHAR(100) NOT NULL, description TEXT NOT NULL DEFAULT '', created_at DATETIME NOT NULL.
- 사용자별 동일 이름은 명세에 금지 규칙이 없어 허용한다.

### subjects

- id, certificate_id FK certificates.id, name VARCHAR(100) NOT NULL, description TEXT NOT NULL DEFAULT ''.
- 동일 자격증 내 같은 과목 이름도 별도 중복 금지 규칙을 추가하지 않는다.

### study_records

- id, subject_id FK subjects.id, study_date DATE NOT NULL, study_minutes INTEGER NOT NULL, memo TEXT NOT NULL DEFAULT '', created_at DATETIME NOT NULL.
- CHECK(study_minutes > 0). (subject_id, study_date) 복합 인덱스.
- 같은 날 여러 학습 기록을 허용한다.

### question_records

- id, subject_id FK subjects.id, solved_date DATE NOT NULL, total_questions INTEGER NOT NULL, correct_answers INTEGER NOT NULL, memo TEXT NOT NULL DEFAULT ''.
- CHECK(total_questions >= 1), CHECK(correct_answers >= 0 AND correct_answers <= total_questions).
- (subject_id, solved_date) 복합 인덱스. 같은 날 여러 기록 허용.

### auth_sessions (인증 지원 테이블 제안)

- id, user_id FK users.id, token_hash CHAR(64) NOT NULL UNIQUE, csrf_token TEXT NOT NULL, created_at DATETIME NOT NULL, expires_at DATETIME NOT NULL.
- secrets로 생성한 충분한 길이의 무작위 세션 토큰은 쿠키에만 전달하고 DB에는 SHA-256 digest만 저장한다. 비밀번호 해싱과는 다르다.
- expires_at 인덱스. 로그인/로그아웃 시 만료 세션을 정리하며, 정리 여부와 관계없이 만료 검증으로 접근을 거부한다.
- 사용자 삭제 UI/API는 범위에 없지만 무결성을 위해 FK 삭제 정책은 정의한다.

SQLite는 VARCHAR 길이를 강제하지 않으므로 길이 제한을 입력 검증에 맡기고 핵심 수치 조건은 DB CHECK로도 보호한다.

## 5. SQLAlchemy 관계와 삭제 정책

- User.certificates ↔ Certificate.user: 1:N.
- Certificate.subjects ↔ Subject.certificate: 1:N.
- Subject.study_records ↔ StudyRecord.subject: 1:N.
- Subject.question_records ↔ QuestionRecord.subject: 1:N.
- User.auth_sessions ↔ AuthSession.user: 1:N.

SQLAlchemy 2.x typed Declarative 모델과 back_populates를 사용한다. 승인 시 각 FK에 ondelete='CASCADE', 부모 collection에 cascade='all, delete-orphan', passive_deletes=True를 적용한다. 자식에서 부모로 delete cascade는 설정하지 않는다.
모든 연결에서 PRAGMA foreign_keys=ON을 적용하고 실제 적용 여부와 직접 DB 삭제/ORM 삭제 모두 테스트한다.
삭제는 소유권과 CSRF 검사 후 하나의 트랜잭션에서 처리하며, 실패 시 rollback한다. 삭제 확인 화면에는 하위 과목/학습/문제 기록 수를 표시한다.

이 제안은 부모 없는 기록을 남기지 않고 구현 복잡도를 낮춘다. 다만 영구 삭제이므로 사용자의 정책 승인이 필요하다.

## 6. 화면·인증·Password Hashing·Session

화면은 Jinja2 서버 렌더링 HTML과 일반 form 제출, POST 성공 후 303 redirect를 제안한다. Vanilla JS는 삭제 확인과 표시 보조에 사용한다. JSON API와 화면 로직을 중복 구현하지 않는다. 대안인 HTML+fetch API는 외부 클라이언트 확장에 유리하지만 현 범위에는 로딩/오류/UI 상태 처리 비용이 늘어난다.

인증 선택지:

- DB 세션(제안): 즉시 로그아웃/폐기가 가능하고 브라우저 앱에 적합하다. 요청별 DB 조회와 만료 정리가 필요하다.
- 서명 쿠키: DB 조회는 줄지만 서버에서 개별 세션을 즉시 폐기하기 어렵고 쿠키 내용은 암호화되지 않는다.
- JWT: 별도 API 클라이언트 확장에는 유리하지만 현재 서비스에는 갱신/폐기 관리가 추가된다.

비밀번호는 pwdlib[argon2]의 Argon2id로 해싱/검증하며 라이브러리의 salt 생성과 검증 API를 사용한다. bcrypt는 널리 사용되지만 입력 길이 제약을 관리해야 한다. PBKDF2는 표준 라이브러리만으로 가능하지만 파라미터·포맷·업그레이드 관리가 추가되어 Argon2id를 제안한다.
사용자가 없는 로그인도 dummy hash 검증 경로를 사용하고 실패 메시지는 '아이디 또는 비밀번호가 올바르지 않습니다'로 통일한다. 해시·토큰·비밀번호·확인값은 응답/로그에 출력하지 않는다.

세션 쿠키는 HttpOnly, SameSite=Lax, Path=/, Domain 미지정. 운영 HTTPS에서는 Secure=true를 강제하고 localhost HTTP 개발에서만 false를 허용한다. 세션 토큰은 localStorage에 저장하지 않는다. 로그인 성공 시 새 토큰으로 교체하고 기존 요청의 세션은 폐기한다. 로그아웃은 POST로 DB 세션을 삭제하고 같은 쿠키 경로로 삭제한다. 다른 브라우저 세션은 유지한다.

인증된 상태의 변경 form에는 세션에 결합된 CSRF 토큰을 hidden input으로 넣고 상수 시간 비교한다. 가입/로그인도 CSRF 검증을 수행한다. 이를 위해 짧은 수명의 사전 인증 토큰을 쿠키와 form에 제공하고 서버 HMAC 서명으로 검증하는 방식 제안(APP_SECRET 환경변수 필요). 허용 Origin 검사도 함께 하며 SameSite만으로 CSRF 방어를 대체하지 않는다. 관련 세부 흐름은 Phase 3에서 실패 경로까지 테스트한다.

## 7. URL / Route 구조

JSON 전용 API가 아닌 서버 렌더링 화면 경로이다. 모든 GET은 데이터를 변경하지 않는다.

- GET /: 로그인 여부에 따라 /dashboard 또는 /login으로 이동.
- GET, POST /register: 가입 화면/가입 처리. 성공 후 로그인 화면으로 이동.
- GET, POST /login: 로그인 화면/인증. 성공 후 /dashboard.
- POST /logout: 로그아웃 후 /login.
- GET /dashboard: 본인 통계.
- GET /certificates, GET /certificates/new, POST /certificates.
- GET /certificates/{certificate_id}, GET /certificates/{certificate_id}/edit, POST /certificates/{certificate_id}/edit, POST /certificates/{certificate_id}/delete.
- GET /certificates/{certificate_id}/subjects, GET /certificates/{certificate_id}/subjects/new, POST /certificates/{certificate_id}/subjects.
- GET /subjects/{subject_id}, GET /subjects/{subject_id}/edit, POST /subjects/{subject_id}/edit, POST /subjects/{subject_id}/delete.
- GET /study-records, GET /study-records/new, POST /study-records.
- GET /study-records/{record_id}/edit, POST /study-records/{record_id}/edit, POST /study-records/{record_id}/delete.
- GET /question-records, GET /question-records/new, POST /question-records.
- GET /question-records/{record_id}/edit, POST /question-records/{record_id}/edit, POST /question-records/{record_id}/delete.

기록 목록은 certificate_id/subject_id 선택 필터를 지원하는 설계이며 전달된 ID도 소유권을 검사한다. 등록/수정 form의 과목 목록은 본인 데이터만 제공한다. 기록의 과목 변경을 허용하되 이전 기록과 새 과목 모두 소유권을 확인한다. Subject의 소속 자격증 이동은 별도 요구가 없어 제공하지 않는다.
Navigation은 Dashboard, Certificates, Study Records, Question Records, Logout을 포함한다. 모든 입력 label, 한국어 필드 오류, 키보드 사용, 좁은 화면의 세로 배치를 고려한다.

## 8. 접근 제어와 오류

- require_current_user 의존성이 쿠키 digest/만료/사용자 존재를 검증한다.
- 보호 화면/처리는 미인증이면 로그인 화면으로 redirect하고 어떠한 변경도 수행하지 않는다.
- Certificate는 id AND user_id 조건, Subject와 기록은 부모 Certificate까지 join해 user_id 조건으로 조회한다.
- services/ownership.py에 소유권 조회를 모아 모든 GET/POST/생성 부모 검증에 재사용한다.
- 클라이언트의 user_id를 신뢰하지 않는다. 숨김 필드나 URL을 조작해도 서버 조회 조건으로 차단한다.
- 없는 객체와 타인 객체는 동일한 404. CSRF 오류는 403. 입력 오류는 422와 한국어 field message. username 중복은 409와 form 오류.
- 소유권을 검사한 객체만 변경하며 commit까지 같은 요청의 DB Session을 사용한다.
- Jinja2 자동 escaping 유지, 메모에 safe 처리를 하지 않는다. SQLAlchemy 파라미터 바인딩으로 SQL을 생성한다.

## 9. Validation

Pydantic schema에서 필수값·정수·날짜·길이·교차 필드를 검증하고 routes에서 오류를 원래 form에 연결한다. 비밀번호를 form에 다시 채우지 않는다.
브라우저 required/min/max는 편의 기능이며 우회 요청도 서버에서 거부한다.
소수/비숫자 공부 시간이나 문제 개수는 거부하고 무단 반올림하지 않는다. 음수 정답도 거부한다. 비어 있는 숫자/잘못된 날짜/공백뿐인 이름을 테스트한다.
실패한 입력은 DB에 저장하지 않는다. 핵심 정수 조건은 DB CHECK에서도 재검증한다.

## 10. Dashboard 집계

- 공통 조건: Certificate.user_id == 현재 사용자.
- 전체 공부 시간: 공부 기록 SUM(study_minutes), 빈 결과는 COALESCE(..., 0).
- 오늘 공부 시간: 승인된 시간대에서 계산한 date를 study_date와 비교한다. created_at 날짜로 계산하지 않는다.
- 자격증별/과목별 시간: 각 키로 GROUP BY. 기록이 없는 자격증/과목도 0분으로 표시하도록 집계 subquery를 LEFT JOIN한다.
- 정답률: 전체 및 과목별 SUM(correct_answers) / SUM(total_questions) * 100. 자격증별 표시가 필요하면 같은 계산을 재사용한다. 기록별 정답률 평균은 사용하지 않는다.
- 분모가 0이면 계산값 0으로 처리하고 화면에는 '문제 풀이 기록 없음'을 함께 표시한다. 표시는 소수점 한 자리, 계산 중간 반올림은 하지 않는다.
- 공부 기록과 문제 기록은 각각 집계한 뒤 결합한다. 두 N 관계를 한 번에 join하면 행이 증폭되어 합계가 잘못되므로 금지한다.
- 분 합계를 divmod(total_minutes, 60)으로 시간/분 표시한다. 작은 SQLite 앱이므로 캐시와 별도 통계 테이블은 추가하지 않는다.

## 11. 테스트 구조 및 요구사항 대응

tests/conftest.py: 요청 간 공유 가능한 임시 SQLite 파일, foreign_keys=ON, DB dependency override, 두 사용자와 독립 TestClient, 날짜 제어 fixture. 개발 DB를 절대 사용하지 않는다. 정상 form 제출 fixture는 실제 CSRF 흐름을 따른다.

- test_auth: 가입 성공/중복/확인 불일치, 해시 평문 아님과 verify, 로그인 성공/실패, 로그아웃 후 토큰 재사용 거부.
- test_certificates 및 test_subjects: 생성·목록·상세·수정·삭제, 필수값/부모 존재/오류 메시지.
- test_study_records: 정상 저장, 0/음수/소수 거부, 목록/수정/삭제, 날짜 오류.
- test_question_records: 정상 저장, total<1, correct<0, correct>total, 목록/수정/삭제, 수정에도 동일 검증.
- test_access_control: 두 사용자로 모든 엔티티 조회/수정/삭제 차단을 parameterize. 타인 부모를 사용한 생성·과목 변경·필터 조작도 차단. 404 뒤 DB가 변하지 않았는지 확인.
- test_dashboard: 전체/오늘/자격증별/과목별 시간, 가중 정답률(1/1 + 0/9 = 10%), 무기록, 0정답, 타인 제외, 여러 학습·문제 기록 조합에서 join 증폭 방지, 자정 경계.
- test_integrity: cascade 정책 승인 시 모든 하위 데이터 제거/타인 데이터 유지, 직접 FK 위반과 CHECK 거부, rollback.
- test_security: 미인증 전체 보호, CSRF 누락/오류, 만료 세션, 세션 교체, 쿠키 속성, escaping, 비밀정보 응답 노출 방지.
- 수동 UI 검증: 가입→로그인→자격증→과목→기록→통계→로그아웃, 모바일 폭, label과 오류 표시, 삭제 확인.

각 Phase의 관련 테스트 후 전체 회귀 테스트를 실행한다. 기존 테스트를 삭제/약화해 통과시키지 않는다. Phase 1에는 구현이 없어 pytest 실행 결과를 생성하지 않는다.

## 12. 추가 Python package와 설정

이 단계에서는 패키지를 설치하지 않는다. Phase 2에서 Python 3.12에서 호환성을 확인하고 실제 설치 버전을 requirements에 고정한다.

- fastapi, sqlalchemy: 지정 기술. Pydantic/Starlette는 FastAPI 기반 의존성으로 활용.
- uvicorn: ASGI 개발/실행 서버.
- jinja2: 분리된 HTML template의 서버 렌더링.
- python-multipart: HTML form 본문 파싱.
- pwdlib[argon2]: 안전한 비밀번호 해싱/검증과 향후 재해싱 지원.
- python-dotenv: README에 안내할 로컬 .env 로딩. 운영 환경변수가 우선하도록 구성.
- tzdata: Windows에서도 ZoneInfo의 IANA 시간대 데이터를 제공.
- pytest: 지정 테스트 도구. httpx: FastAPI TestClient의 요청 전송 의존성.

SQLite 드라이버, secrets, hashlib, hmac, datetime, zoneinfo는 Python 표준 라이브러리를 사용한다. JWT/Redis/프론트엔드 프레임워크/별도 CSS framework는 추가하지 않는다. 초기 스키마 생성은 명시적 초기화 명령을 제공하고, 데이터 보존이 필요한 스키마 변경이 생기면 migration을 설계한다. create_all이 기존 스키마를 갱신한다고 가정하지 않는다.

환경변수 제안: DATABASE_URL, APP_SECRET(사전 인증 CSRF 서명), APP_TIMEZONE, SESSION_TTL_SECONDS, COOKIE_SECURE, APP_ENV, ALLOWED_ORIGIN. 테스트는 독립 설정을 주입한다. 운영에서 누락된 secret이나 안전하지 않은 설정이면 시작을 거부한다. 실제 값은 commit하지 않고 .env.example에는 설명용 값만 둔다.

## 13. Phase 2~11 구현 계획

각 Phase는 상태 확인→별도 브랜치/계획→구현→관련·회귀 테스트→diff 검토→Conventional Commit→push→PR→한국어 보고 순서로 진행한다. main 병합은 항상 사용자 승인 후 한다.

- Phase 2: 승인 정책 반영, 의존성 고정, app/config/database/template/static/test 기반, 환경변수 예시와 실행 명령 검증. 인증/CRUD 기능은 다음 Phase.
- Phase 3: User/AuthSession, 회원가입·로그인·로그아웃, 해싱/세션/CSRF와 인증 테스트.
- Phase 4: Certificate 모델·입력·화면·CRUD·소유권 검증과 테스트.
- Phase 5: Subject 관계·CRUD·부모 소유권 및 삭제 정책 검증.
- Phase 6: StudyRecord 등록·조회·수정·삭제와 시간·날짜 validation 및 테스트.
- Phase 7: QuestionRecord CRUD와 정답/총 문제 교차 검증 및 테스트.
- Phase 8: Dashboard 집계·표시, 날짜 경계/0분모/가중 정답률/중복 join 회귀 테스트.
- Phase 9: 전 경로 접근 제어·CSRF·쿠키·XSS·secret·삭제 무결성 점검. 보안 구현을 이 단계까지 미루지 않고 누락을 감사한다.
- Phase 10: 전체 pytest와 수동 UI 시나리오, 오류 재현/수정, 모바일 사용과 회귀 검증.
- Phase 11: README에 설치/실행/테스트/환경변수/구조/DB/설계 결정 확정, 깨끗한 환경에서 안내 검증, 최종 PR/보고.

## 14. 예상 기술 문제

- Windows PATH에 python/py/uv가 없음: 앱 제공 Python 3.12.14로 프로젝트 .venv를 만들고 절대 경로 실행을 검증한다. 다른 개발자는 Python 3.12 이상을 설치해야 한다.
- OneDrive 디렉터리의 SQLite 동기화/파일 잠금: 개발 DB는 ignore하며 로컬 전용 경로로 DATABASE_URL을 바꿀 수 있게 한다. 서비스 운영은 동기화 폴더 밖을 권장한다.
- SQLite 동시 쓰기 잠금: 요청별 Session, 짧은 트랜잭션, 오류 rollback. 비동기 DB 라이브러리를 불필요하게 추가하지 않는다.
- timezone-aware datetime의 SQLite 복원: UTC 규약과 경계 테스트로 일관성을 보장한다.
- UNIQUE 사전 검사만으로는 동시 가입을 막지 못함: DB UNIQUE와 IntegrityError 처리 병행.
- form 오류가 기본 JSON 오류로 노출될 수 있음: route/form 오류 매핑을 테스트한다.
- cascade/CSRF/세션은 실제 요청 및 DB 수준 테스트가 필요하다.
- 의존성 설치 네트워크/인증/권한 문제가 발생하면 우회하지 않고 필요한 최소 조치를 요청한다.

## 15. 기술 참고 자료

프로젝트 간 비교 대상 저장소는 열람하지 않았다. 아래는 라이브러리의 공식 문서만 참고했다.

- [FastAPI password hashing](https://fastapi.tiangolo.com/tutorial/security/oauth2-jwt/): pwdlib 및 Argon2 지원 확인. 예제의 JWT 아키텍처 자체를 채택하는 것은 아니다.
- [SQLAlchemy cascades](https://docs.sqlalchemy.org/en/20/orm/cascades.html): ORM cascade와 DB ON DELETE, passive_deletes 연계.
- [SQLite foreign keys](https://sqlite.org/foreignkeys.html): FK 활성화와 삭제 동작.
- [Starlette responses](https://starlette.dev/responses/): 쿠키 속성 설정 인터페이스.

## 16. 종료 조건

Phase 1 설계 산출물과 PR을 제공한 뒤 중단한다. 승인되지 않은 정책을 구현하지 않는다. 사용자 승인 전 Phase 2 시작 및 main 병합을 하지 않는다.
