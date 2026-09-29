# StudyMate

자격증 시험 준비를 위한 개인 학습 관리 웹 애플리케이션입니다.
Codex가 독립적으로 개발하며 `PROJECT_SPEC.md`를 요구사항 기준으로 사용합니다.

## 현재 상태

Phase 0: 저장소 초기화 완료. Phase 1: 설계 제안 작성 완료, 정책 및 Phase 2 진행 승인 대기.
아직 애플리케이션 코드는 없으며 실행 가능한 서비스가 아닙니다.

## 구현 예정 기능

- 회원가입, 로그인, 로그아웃
- 사용자별 자격증과 과목 관리
- 학습 시간 및 문제 풀이 기록 관리
- 전체·오늘·자격증별·과목별 학습 시간과 정답률 Dashboard
- 서버 측 접근 제어 및 입력값 검증

## 기술

Python, FastAPI, SQLAlchemy, SQLite, HTML, CSS, Vanilla JavaScript, pytest.
프론트엔드 SPA 프레임워크는 사용하지 않습니다.

## 현재 구조

- `PROJECT_SPEC.md`: 원본 요구사항
- `.gitignore`: 비밀정보, 로컬 DB, Python 생성 파일 제외
- `README.md`: 프로젝트 소개와 진행 상태
- `docs/PHASE_1_DESIGN.md`: 요구사항 분석, 구조·DB·인증·검증·테스트 설계와 후속 계획
- `docs/PHASE_REPORTS.md`: Phase 0/1 결과와 검증 기록

## 개발 환경 및 실행

Python 3.12 이상과 Git을 사용할 예정입니다.
현재 작업 환경에는 Python이 PATH에 없으므로 앱 제공 Python 3.12.14를 사용해 `.venv`를 생성했습니다.
일반 개발 환경에서는 Python 설치 후 `python -m venv .venv`로 준비할 수 있습니다.
Phase 2에서 의존성 파일, 환경변수 예시, 애플리케이션 진입점과 테스트 기반을 추가합니다.
설치·실행·테스트 명령은 실제 검증 후 이 문서에 기록합니다.
현재 실행하거나 테스트할 애플리케이션은 없습니다.

## 환경변수

실제 `.env`, 비밀번호, 세션 토큰, API 키는 커밋하지 않습니다.
설정 항목과 `.env.example`은 Phase 2에서 추가할 예정입니다.

## 데이터베이스 및 설계

기본 관계는 User → Certificate → Subject → StudyRecord / QuestionRecord입니다.
[Phase 1 설계](docs/PHASE_1_DESIGN.md)에 테이블, 인증, 삭제 정책, 접근 제어, 테스트 전략을 제안했습니다.
main 병합과 Phase 2 진행은 사용자 승인 후 수행합니다.
