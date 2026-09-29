# StudyMate
## AI Coding Agent 비교 프로젝트 요구사항 명세서

Version: 1.0

---

# 1. 프로젝트 개요

## 1.1 프로젝트명

StudyMate

## 1.2 프로젝트 목적

StudyMate는 자격증 시험을 준비하는 사용자가 다음 정보를 관리하고
자신의 학습 현황을 확인할 수 있는 웹 애플리케이션이다.

- 준비 중인 자격증
- 자격증별 과목
- 학습 시간
- 문제 풀이 기록
- 과목별 학습 통계
- 문제 정답률

본 프로젝트는 동일한 요구사항을 서로 다른 AI Coding Agent에게 제공하여
소프트웨어 개발 과정 전체를 비교하는 것을 목적으로 한다.

비교 대상:

- Cursor
- Claude Code
- Codex

각 AI Coding Agent는 자신의 프로젝트를 독립적으로 구현하며
다른 Agent가 생성한 코드, Repository, 설계 또는 구현 결과를
참고해서는 안 된다.

---

# 2. 핵심 개발 원칙

다음 원칙을 반드시 준수한다.

1. PROJECT_SPEC.md를 프로젝트의 최우선 요구사항으로 사용한다.

2. 요구사항에 없는 대규모 기능을 임의로 추가하지 않는다.

3. 유지보수가 가능한 구조로 작성한다.

4. 모든 코드를 하나의 파일에 작성하지 않는다.

5. 기능별 책임을 적절하게 분리한다.

6. 코드 중복을 최소화한다.

7. 중요한 비즈니스 로직에는 테스트를 작성한다.

8. 사용자 입력값을 검증한다.

9. 비밀번호를 평문으로 저장하지 않는다.

10. 민감한 정보를 코드에 직접 작성하지 않는다.

11. 기존 기능을 깨뜨리지 않도록 변경 후 테스트한다.

12. 필요 이상의 라이브러리를 사용하지 않는다.

13. 새로운 라이브러리를 추가할 경우 이유를 설명한다.

14. 사용자가 이해할 수 있도록 작업 결과를 한국어로 설명한다.

15. 변수명, 함수명, 클래스명 등 소스코드의 식별자는
    일반적인 영어 명명 규칙을 사용한다.

---

# 3. 기술 스택

다음 기술 스택을 사용한다.

## Backend

- Python
- FastAPI
- SQLAlchemy

## Database

- SQLite

## Frontend

- HTML
- CSS
- Vanilla JavaScript

다음 프론트엔드 프레임워크는 사용하지 않는다.

- React
- Vue
- Angular
- 기타 SPA Framework

## Testing

- pytest

## Version Control

- Git
- GitHub

기술 스택을 임의로 다른 프레임워크로 변경하지 않는다.

단, 명시된 기술을 사용하기 위해 필요한 보조 라이브러리는
합리적인 범위에서 사용할 수 있다.

추가 라이브러리를 사용할 경우 선택 이유를 설명한다.

---

# 4. 사용자 관리

## 4.1 회원가입

사용자는 계정을 생성할 수 있다.

필수 입력값:

- username
- password
- password_confirmation

조건:

- username은 중복될 수 없다.
- 비밀번호와 비밀번호 확인 값이 일치해야 한다.
- 비밀번호는 평문으로 데이터베이스에 저장하지 않는다.
- 잘못된 입력에 대해 적절한 오류 메시지를 제공한다.

---

## 4.2 로그인

등록된 사용자는 로그인할 수 있다.

입력값:

- username
- password

로그인 성공 시 Dashboard로 이동한다.

로그인 실패 시 적절한 오류 메시지를 표시한다.

---

## 4.3 로그아웃

로그인한 사용자는 로그아웃할 수 있다.

로그아웃 후 로그인 화면으로 이동한다.

---

# 5. 자격증 관리

로그인한 사용자는 자신이 준비 중인 자격증을 관리할 수 있다.

예:

- 정보처리기사
- 리눅스마스터 2급
- 네트워크관리사 2급
- AWS Solutions Architect Associate

## 기능

- 자격증 등록
- 자격증 목록 조회
- 자격증 상세 조회
- 자격증 수정
- 자격증 삭제

## Certificate 데이터

최소한 다음 정보를 가진다.

- id
- user_id
- name
- description
- created_at

각 Certificate는 생성한 User에게 속한다.

사용자는 다른 사용자의 Certificate에 접근할 수 없다.

---

# 6. 과목 관리

각 자격증에는 여러 개의 과목을 등록할 수 있다.

예:

리눅스마스터 2급

- 리눅스 일반
- 리눅스 운영 및 관리
- 리눅스 활용

## 기능

- 과목 등록
- 과목 목록 조회
- 과목 상세 조회
- 과목 수정
- 과목 삭제

## Subject 데이터

최소한 다음 정보를 가진다.

- id
- certificate_id
- name
- description

Subject는 하나의 Certificate에 속한다.

---

# 7. 학습 기록

사용자는 과목별 학습 기록을 등록할 수 있다.

## StudyRecord 데이터

최소한 다음 정보를 가진다.

- id
- subject_id
- study_date
- study_minutes
- memo
- created_at

예:

과목:
리눅스 운영 및 관리

학습 날짜:
2026-09-29

학습 시간:
90분

메모:
파일 권한과 chmod 복습

## 기능

- 학습 기록 등록
- 학습 기록 목록 조회
- 학습 기록 수정
- 학습 기록 삭제

## Validation

study_minutes는 0보다 커야 한다.

잘못된 값이 입력되면 저장하지 않고
적절한 오류 메시지를 제공한다.

---

# 8. 문제 풀이 기록

사용자는 과목별 문제 풀이 결과를 기록할 수 있다.

## QuestionRecord 데이터

최소한 다음 정보를 가진다.

- id
- subject_id
- solved_date
- total_questions
- correct_answers
- memo

예:

과목:
리눅스 일반

총 문제:
20

정답:
16

## Validation

total_questions는 1 이상이어야 한다.

correct_answers는 다음 조건을 만족해야 한다.

- 0 이상
- total_questions 이하

잘못된 값은 저장하지 않는다.

---

# 9. Dashboard

로그인 후 기본 화면은 Dashboard이다.

Dashboard에서는 최소한 다음 정보를 확인할 수 있어야 한다.

## 9.1 전체 공부 시간

현재 로그인한 사용자의 모든 StudyRecord의
study_minutes 합계를 계산한다.

예:

총 공부시간

18시간 30분

---

## 9.2 오늘 공부 시간

오늘 날짜에 해당하는 StudyRecord의
study_minutes를 합산한다.

---

## 9.3 자격증별 공부 시간

각 Certificate에 속한 StudyRecord의 시간을 합산한다.

예:

정보처리기사
15시간

리눅스마스터 2급
8시간

네트워크관리사 2급
5시간

---

## 9.4 과목별 공부 시간

각 Subject별 StudyRecord의 시간을 합산한다.

---

## 9.5 문제 정답률

다음 방식으로 계산한다.

정답률 =

correct_answers 총합
/
total_questions 총합
× 100

예:

리눅스 일반

80%

문제 풀이 기록이 존재하지 않는 경우
0으로 나누는 오류가 발생해서는 안 된다.

---

# 10. 데이터 관계

최소한 다음 관계를 만족해야 한다.

User

1 : N

Certificate


Certificate

1 : N

Subject


Subject

1 : N

StudyRecord


Subject

1 : N

QuestionRecord

전체 구조:

User
 └── Certificate
      └── Subject
           ├── StudyRecord
           └── QuestionRecord

Foreign Key 및 관계 설정은 SQLAlchemy를 사용한다.

관련 데이터 삭제 시 데이터 무결성이 깨지지 않도록
적절한 삭제 정책을 설계한다.

삭제 정책을 결정한 이유를 설명한다.

---

# 11. 접근 제어

사용자는 자신의 데이터에만 접근할 수 있어야 한다.

예:

User A가 생성한 Certificate를
User B가 URL을 직접 입력하여 조회할 수 없어야 한다.

다음 작업 모두에서 접근 제어를 적용한다.

- 조회
- 생성
- 수정
- 삭제

다음 데이터에 적용한다.

- Certificate
- Subject
- StudyRecord
- QuestionRecord

단순히 UI에서 버튼을 숨기는 방식으로 구현해서는 안 된다.

서버 측에서 실제 접근 권한을 검증해야 한다.

---

# 12. UI 요구사항

화려한 디자인은 요구하지 않는다.

다음 원칙을 따른다.

- 깔끔하고 단순한 UI
- 사용하기 쉬운 화면
- 메뉴 이동이 명확할 것
- 입력 폼의 label이 명확할 것
- Validation 오류를 사용자가 확인할 수 있을 것
- 모바일에서도 기본적인 사용이 가능할 것

기본 Navigation에는 다음 메뉴를 포함한다.

- Dashboard
- Certificates
- Study Records
- Question Records
- Logout

로그인하지 않은 사용자는 로그인 및 회원가입 화면에
접근할 수 있어야 한다.

---

# 13. 테스트 요구사항

pytest를 사용하여 자동화 테스트를 작성한다.

최소한 다음 항목을 테스트한다.

## Authentication

- 회원가입 성공
- username 중복 가입 거부
- 로그인 성공
- 잘못된 비밀번호 로그인 실패
- 로그아웃

## Certificate

- 생성
- 조회
- 수정
- 삭제

## Subject

- 생성
- 조회
- 수정
- 삭제

## StudyRecord

- 정상적인 공부시간 저장
- 0 이하 공부시간 거부
- 수정
- 삭제

## QuestionRecord

- 정상적인 기록 저장
- total_questions가 1 미만인 경우 거부
- correct_answers가 total_questions보다 큰 경우 거부
- 수정
- 삭제

## Access Control

User A의 데이터를 User B가

- 조회
- 수정
- 삭제

할 수 없는지 테스트한다.

## Dashboard

- 전체 공부시간 계산
- 오늘 공부시간 계산
- 자격증별 공부시간 계산
- 과목별 공부시간 계산
- 정답률 계산
- 문제 기록이 없는 경우 정상 처리

테스트를 통과시키기 위해
테스트 자체를 임의로 삭제하거나 약화해서는 안 된다.

---

# 14. README 요구사항

README.md에는 최소한 다음 내용을 포함한다.

- 프로젝트 소개
- 주요 기능
- 사용 기술
- 프로젝트 구조
- 설치 방법
- 실행 방법
- 테스트 실행 방법
- 환경변수 설정 방법
- 데이터베이스 구조
- 주요 설계 결정

처음 프로젝트를 접하는 개발자가 README만 보고
로컬 환경에서 실행할 수 있도록 작성한다.

---

# 15. Git / GitHub Repository 관리

이 프로젝트의 Git 및 GitHub 관리는
AI Coding Agent의 개발 작업에 포함된다.

---

## 15.1 독립 Repository

각 Agent는 독립적인 GitHub Repository를 사용한다.

Repository 이름:

Cursor:

studymate-cursor

Claude Code:

studymate-claude

Codex:

studymate-codex

현재 자신이 어떤 AI Coding Agent인지 판단하여
해당 Repository 이름을 사용한다.

판단할 수 없다면 사용자에게 질문한다.

다른 Agent의 Repository 또는 코드를 참고해서는 안 된다.

---

## 15.2 Repository Visibility

GitHub Repository는 Public으로 생성한다.

Repository 생성이 가능한 환경에서는
Agent가 직접 생성한다.

GitHub 인증 또는 권한 문제로 생성할 수 없다면
임의로 우회하지 말고 사용자에게 필요한 조치를 요청한다.

---

## 15.3 Git 초기 설정

프로젝트 시작 시 다음을 확인한다.

- Git 설치 여부
- 현재 디렉터리가 Git Repository인지
- Git 사용자 설정 상태
- GitHub 인증 상태
- GitHub Remote 연결 상태

필요한 경우 Git Repository를 초기화한다.

기본 branch 이름은 다음과 같다.

main

---

## 15.4 Branch 전략

main branch에서 직접 기능을 개발하지 않는다.

기능 개발 시 feature branch를 생성한다.

예:

feature/auth

feature/certificates

feature/subjects

feature/study-records

feature/question-records

feature/dashboard

필요한 경우 적절한 이름의 다른 branch를 사용할 수 있다.

main은 가능한 한 실행 가능한 안정적인 상태를 유지한다.

---

## 15.5 Commit

작업을 의미 있는 단위로 나누어 commit한다.

전체 프로젝트를 하나의 거대한 commit으로 저장하지 않는다.

Conventional Commits 형식을 사용한다.

예:

chore: initialize project

feat: add user registration

feat: add certificate management

fix: validate study minutes

test: add authentication tests

refactor: simplify dashboard service

docs: update README

Commit 전에 다음을 확인한다.

1. git status
2. 변경된 파일
3. git diff
4. 관련 테스트 결과

테스트가 실패한 상태에서는 원칙적으로 commit하지 않는다.

---

## 15.6 Push

정상적으로 완료된 commit은
자신의 Remote Repository에 push할 수 있다.

일반적인 feature branch push는
별도의 사용자 승인 없이 수행할 수 있다.

다른 Agent의 Repository에는 push하지 않는다.

---

## 15.7 Pull Request

각 주요 Phase의 기능 개발이 완료되면
가능한 환경에서는 Pull Request를 생성한다.

Pull Request에는 최소한 다음 내용을 작성한다.

- 구현한 기능
- 주요 변경 사항
- 주요 변경 파일
- 테스트 결과
- 중요한 설계 결정

Pull Request를 생성하는 것은 허용한다.

main branch로 Merge하기 전에는
사용자에게 승인을 요청한다.

---

## 15.8 Git 안전 규칙

다음과 같은 destructive operation은
사용자 승인 없이 실행하지 않는다.

- git reset --hard
- git push --force
- git push --force-with-lease
- git clean -fd
- 기존 branch 강제 삭제
- commit history 재작성
- Remote Repository 삭제

사용자가 작성한 기존 변경사항을 임의로 삭제하지 않는다.

---

# 16. 보안 요구사항

최소한 다음 사항을 고려한다.

- 비밀번호 해싱
- 인증되지 않은 사용자 접근 제한
- 사용자별 데이터 접근 제어
- 입력값 Validation
- Session 또는 인증 정보의 안전한 관리

다음과 같은 민감정보를 코드에 직접 작성하지 않는다.

- Password
- API Key
- Secret Key
- Access Token

필요한 값은 환경변수로 관리한다.

.env 파일을 사용하는 경우 반드시 .gitignore에 포함한다.

실제 Secret 값을 Repository에 commit하지 않는다.

필요한 경우 .env.example을 제공한다.

---

# 17. 프로젝트 구조

구체적인 디렉터리 구조는
AI Coding Agent가 직접 설계한다.

단 다음 조건을 만족해야 한다.

- 기능별 책임이 적절하게 분리되어야 한다.
- 모든 코드를 main.py 하나에 작성하면 안 된다.
- 테스트 코드는 별도의 tests 디렉터리에서 관리한다.
- HTML template 파일을 적절하게 분리한다.
- CSS 및 JavaScript 파일을 적절하게 관리한다.
- 설정 관련 코드를 적절하게 분리한다.

Phase 1에서 제안한 프로젝트 구조와
그 구조를 선택한 이유를 설명한다.

---

# 18. 개발 Phase

프로젝트는 다음 순서로 진행한다.

각 Phase를 독립적인 작업 단위로 취급한다.

---

## Phase 0

Repository 및 개발 환경 초기화

---

## Phase 1

요구사항 분석 및 설계

---

## Phase 2

프로젝트 기본 구조 구축

---

## Phase 3

회원가입 / 로그인 / 로그아웃

---

## Phase 4

자격증 관리

---

## Phase 5

과목 관리

---

## Phase 6

학습 기록 관리

---

## Phase 7

문제 풀이 기록 관리

---

## Phase 8

Dashboard 구현

---

## Phase 9

접근 제어 및 보안 점검

---

## Phase 10

전체 테스트 및 버그 수정

---

## Phase 11

README 및 프로젝트 최종 정리

---

# 19. Phase 진행 규칙

Phase 0을 제외한 기능 개발 Phase에서는
가능하면 다음 과정을 따른다.

1. 현재 Repository 상태 확인

2. 해당 Phase 요구사항 분석

3. 필요한 feature branch 생성

4. 구현 계획 작성

5. 기능 구현

6. 테스트 작성 또는 기존 테스트 보완

7. 테스트 실행

8. 오류 발생 시 원인 분석 및 수정

9. git diff 검토

10. 의미 있는 단위로 commit

11. Remote Repository에 push

12. 가능한 경우 Pull Request 생성

13. 사용자에게 작업 결과 보고

main으로 Merge하기 전에는 사용자 승인을 요청한다.

---

# 20. Phase 완료 보고 형식

각 Phase가 완료되면 최소한 다음 내용을 사용자에게 보고한다.

## 완료한 작업

이번 Phase에서 구현하거나 수행한 내용

## 주요 변경 파일

생성 또는 수정된 주요 파일

## 테스트 결과

실행한 테스트와 결과

예:

23 passed

## Git

- 현재 branch
- 생성한 commit
- Remote push 여부
- Pull Request 여부

## 주요 설계 결정

이번 Phase에서 중요한 기술적 결정이 있었다면 설명

## 문제 또는 주의사항

현재 알려진 문제나 다음 Phase에서 고려해야 할 사항

---

# 21. Phase 0 상세 요구사항

프로젝트 개발을 시작하기 전에
Repository 환경을 구성한다.

다음 순서로 작업한다.

1. 현재 개발 환경 확인

2. Git 설치 여부 확인

3. GitHub 인증 상태 확인

4. 자신에게 지정된 Repository 이름 확인

5. 프로젝트 작업 디렉터리 확인 또는 생성

6. Git Repository 초기화

7. 기본 branch를 main으로 설정

8. 적절한 .gitignore 생성

9. PROJECT_SPEC.md를 Repository에 포함

10. 초기 README.md 생성

11. GitHub Repository 생성

12. Remote origin 연결

13. 최초 commit 생성

Commit message:

chore: initialize StudyMate project

14. 최초 commit을 GitHub에 push

완료 후 다음 내용을 보고한다.

- AI Coding Agent
- Repository 이름
- Repository URL
- Repository visibility
- 현재 branch
- 최초 commit
- Remote 연결 상태
- GitHub 인증 상태

GitHub Repository를 직접 생성할 수 없는 환경이라면
그 사실을 사용자에게 설명하고 필요한 최소 작업만 요청한다.

임의로 다른 Repository를 사용하지 않는다.

---

# 22. Phase 1 상세 요구사항

Phase 1에서는 아직 애플리케이션 기능을 구현하지 않는다.

PROJECT_SPEC.md 전체를 분석하고
개발 설계를 수행한다.

다음을 사용자에게 제안한다.

1. 요구사항 요약

2. 예상 프로젝트 디렉터리 구조

3. 각 디렉터리 및 주요 파일의 역할

4. 데이터베이스 테이블 설계

5. SQLAlchemy 관계 설계

6. 데이터 삭제 정책

7. 인증 방식

8. Password Hashing 방식

9. Session 관리 방식

10. 주요 URL 또는 Route 구조

11. 접근 제어 구현 전략

12. Validation 전략

13. Dashboard 통계 계산 전략

14. 테스트 구조

15. 사용할 추가 Python Package와 선택 이유

16. Phase 2~11 구현 계획

17. 예상되는 기술적 문제

중요한 설계 선택지가 여러 개 존재한다면
각 선택지의 장단점을 설명한다.

PROJECT_SPEC.md에서 명확하게 결정되지 않은 중요한 사항은
임의로 확정하지 말고 사용자에게 질문한다.

Phase 1에서는 애플리케이션 기능 구현을 시작하지 않는다.

설계 결과를 사용자에게 보고한 후
Phase 2 진행 승인을 기다린다.

---

# 23. AI Coding Agent 작업 규칙

당신은 이 프로젝트의 개발을 담당하는 AI Coding Agent이다.

다음 규칙을 반드시 따른다.

1. 작업 시작 전에 PROJECT_SPEC.md를 읽는다.

2. 현재 Repository와 Git 상태를 확인한다.

3. 기존 코드가 있다면 수정하기 전에 구조를 이해한다.

4. 요구사항과 기존 구현을 비교한 뒤 작업한다.

5. 필요한 파일만 수정한다.

6. 사용자 요청과 관련 없는 코드를 임의로 대규모 수정하지 않는다.

7. 기존 기능을 삭제하지 않는다.

8. 구현 후 관련 테스트를 실행한다.

9. 테스트 실패 원인을 숨기지 않는다.

10. 테스트를 통과시키기 위해 테스트를 임의로 삭제하거나
    assertion을 약화하지 않는다.

11. 오류가 발생하면 원인을 분석한 뒤 수정한다.

12. 라이브러리를 추가할 경우 이유를 설명한다.

13. 작업 결과를 한국어로 설명한다.

14. 소스코드는 일반적인 영어 명명 규칙을 따른다.

15. Git commit은 의미 있는 단위로 나눈다.

16. Git history를 임의로 파괴하지 않는다.

17. Secret 정보를 Repository에 commit하지 않는다.

18. 다른 AI Coding Agent의 Repository를 참고하지 않는다.

19. 사용자의 기존 변경사항을 임의로 되돌리지 않는다.

20. 작업 중 중요한 의사결정이 필요한 경우 사용자에게 질문한다.

---

# 24. 금지 사항

다음 행동을 하지 않는다.

- 다른 Agent의 StudyMate Repository 열람
- 다른 Agent가 작성한 코드 복사
- 요구사항을 임의로 축소
- 테스트 삭제를 통한 테스트 통과
- 비밀번호 평문 저장
- Secret Key 하드코딩
- .env commit
- 사용자 승인 없는 force push
- 사용자 승인 없는 destructive Git operation
- 사용자 승인 없는 main merge
- 프로젝트 전체를 하나의 파일에 구현
- 요구되지 않은 대규모 기능 추가

---

# 25. 비교 실험 공정성

본 프로젝트는 AI Coding Agent 비교 실험이므로
다음 조건을 유지한다.

각 Agent는 동일한 PROJECT_SPEC.md를 사용한다.

각 Agent는 독립적으로 설계한다.

각 Agent는 독립적으로 구현한다.

각 Agent는 자신의 Repository만 관리한다.

다른 Agent의 결과물을 참고하지 않는다.

사용자가 명시적으로 요구하지 않는 한
다른 Agent의 구현 방식을 추측하거나 모방하지 않는다.

---

# 26. 현재 수행할 작업

PROJECT_SPEC.md를 처음 전달받았다면
Phase 0부터 시작한다.

현재는 전체 애플리케이션을 한 번에 구현하지 않는다.

먼저 Phase 0을 수행한다.

Phase 0 완료 후 결과를 사용자에게 보고한다.

이후 Phase 1을 수행한다.

Phase 1에서는 설계만 수행하며
애플리케이션 기능을 구현하지 않는다.

Phase 1 결과를 사용자에게 보고한 뒤
작업을 중단한다.

Phase 2를 시작하기 전에 반드시
사용자의 승인을 기다린다.