# new_line

**작은 데이터 프로젝트를 AI 코딩 에이전트와 함께, 기록이 남게 진행하는 작업 틀.**

CSV 몇 개를 받아 "무엇을 만들지 정하고 → 만들고 → 맞는지 확인하고 → 다른 사람에게 넘기는" 일을 짧은 시간 안에 해야 할 때 씁니다.
코드 생성은 AI가 빠르게 해 주지만, **왜 그렇게 정했는지·어디까지 확인했는지**는 쉽게 사라집니다. new_line는 그 흔적을 git과 몇 개의 짧은 문서에 남기도록 돕습니다.

Python 3.10 이상, 표준 라이브러리만 씁니다. 패키지 설치·외부 API가 필요 없습니다.

## 핵심 아이디어

| 원칙 | 뜻 |
|---|---|
| 프롬프트 하나 = 한 단계 | 큰 지시 한 번보다 "이것만 하고 멈춰"를 여러 번. 범위가 좁을수록 검증이 쉽다 |
| 먼저 예상, 그다음 실행 | 실행 전에 내 예상을 적고, 결과와 다르면 왜 다른지 묻는다 |
| 결정은 번호로 | 질문·답(Q) → 결정(D) → 요구사항(REQ) → commit을 한 줄로 잇는다 |
| 테스트 먼저 | 리스크와 결정을 테스트로 먼저 쓰고, 실패를 본 뒤 구현한다 |
| 사람이 읽는 문서 | 맨 위 3줄 요약, 표는 4칸 이하, 근거는 맨 아래 |
| 넘길 수 있게 | 새 세션이 handoff 요약만 읽고 이어갈 수 있는지 직접 확인한다 |

## 빠른 시작

도구는 프로젝트 폴더 **밖**에 받습니다.

```sh
git clone https://github.com/jhs-labs/new_line.git /tmp/new_line
cd my-project
python /tmp/new_line/install.py --target . --start
```

`--start`는 `.gitignore` 보강 → `git init`(없을 때만) → 원본 commit → 도구 설치 → 작업 양식 생성 → 설치 commit까지 한 번에 합니다.
단계별로 하려면 [docs/GIT.md](docs/GIT.md)를 보세요.

설치 후 AI 에이전트를 **프로젝트 폴더에서** 열고, "지금 적용 중인 AGENTS.md 규칙 3개만 말해 줘"로 규칙이 로드됐는지 확인합니다.

## 무엇을 어디서 보나

| 지금 필요한 것 | 파일 |
|---|---|
| 처음부터 끝까지 이어지는 13단계 프롬프트 | [docs/PROMPT_FLOW.md](docs/PROMPT_FLOW.md) |
| 전체 순서 한 장 | [docs/QUICKCARD.md](docs/QUICKCARD.md) |
| 데이터 지도 양식·예시 | [fde/templates/data_map.md](fde/templates/data_map.md), [예시](fde/examples/data_map_example.md) |
| 전달 직전 되돌아보기 양식 | [fde/templates/review.md](fde/templates/review.md) |
| 단계별 지시 모음 | [docs/STAGE_PROMPTS.md](docs/STAGE_PROMPTS.md) |
| 매 프롬프트·체크포인트 점검 | [docs/SELFCHECK.md](docs/SELFCHECK.md) |
| 데이터 처리 6단계 설계 | [docs/METHOD.md](docs/METHOD.md) |
| Git 사용법 | [docs/GIT.md](docs/GIT.md) |
| 검사·보관·묶기 명령 | [docs/RUNBOOK.md](docs/RUNBOOK.md) |
| 파일 목차 | [INDEX.md](INDEX.md) |

문서를 한꺼번에 AI에 읽히지 않습니다. 지금 단계에 필요한 한 곳만 엽니다.

## 두 가지 흐름

| 구분 | 단계 |
|---|---|
| 진행 상태 (7) | DISCOVERY → CONTRACT → SCAFFOLD → E2E_V0 → VERIFY → ACCEPT → SUBMIT |
| 데이터 처리 (6) | 읽기 → 정규화 → 정제 → 판정 → 순위/집계 → 표시 |

예: `STATE: VERIFY`, `METHOD: ④ 판정`.

## 작업 양식 (`fde/work/`)

| 파일 | 담는 것 |
|---|---|
| data_map | 파일·연결 키·리스크 후보·예상 vs 실제 |
| stakeholders | 최종 사용자·요청자·결정권자, 원하는 것과 충돌 |
| rules | 규칙 한 줄 → 출처(D) → 테스트 이름 (불변식 포함) |
| interview | 질문 카드(선택지 + 내 추천), 질문과 답 원문 (Q-ID) |
| decisions | 결정 한 줄(✅🟡❓), 이유, 예상 노트(예상 vs 실제) |
| problem | 성공 기준·만들 것·만들지 않을 것·제약·Unknown |
| requirements | REQ-ID·우선순위·합격 문장·검사 명령 |
| skeleton | 6단계별 입력·출력·리스크·결정 |
| state | 현재 단계·마지막 commit·체크포인트 |
| handoff | 이어받을 사람을 위한 20줄 요약 |

## 도구 (`fde/kit/`)

```sh
python fde/kit/preflight.py --port 3000
python fde/kit/init_work.py
python fde/kit/evaluate.py
python fde/kit/promote.py --report latest
python fde/kit/bundle.py
python fde/kit/status.py --intent   # Q→D→REQ→commit 빈칸
```

`fde/reusable/`에는 CSV/JSON 읽기·검증·집계·내보내기·작은 HTTP 화면 부품이 있습니다.
`fde/examples/live_params/`는 화면에서 기준값을 바꾸면 바로 다시 계산되는 예시입니다.

## 한계

- 예시와 양식은 질문 소재입니다. 실제 업무 정책이나 정답이 아닙니다.
- `status.py`의 시간표는 3시간 작업을 가정한 참고 타이머입니다. 실제 일정이 우선합니다.
- 웹 부품은 로컬 확인용입니다. 운영 서비스 수준의 인증·저장을 갖추지 않았습니다.

도구 자체 검사: `python -m unittest discover -s tests -v`
