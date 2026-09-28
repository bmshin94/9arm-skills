# 9arm-skills 분석 & 활용 가이드 (대화 정리)

- 이 레포 (포크 버전): https://github.com/bmshin94/9arm-skills
- 원본 레포: https://github.com/thananon/9arm-skills
- 정리일: 2026-09-28

---

## 1. 전수조사 분석

### 한 줄 요약
Claude Code(AI 코딩 에이전트)에게 주는 **"업무 매뉴얼 모음집"**. 실행 코드가 아니라 AI가 읽고 따라 하는 마크다운 지시서(`SKILL.md`) 6개로 구성된다.

- 원작자: 태국 개발자·테크 유튜버 **9arm** (README 설치 경로 `thananon/9arm-skills`)
- 포크 버전(`bmshin94/9arm-skills`)은 qwen 스킬 2개와 `CLAUDE.md` 페르소나를 추가했다.

### 폴더 구조
```
9arm-skills/
├── .claude-plugin/plugin.json   # 스킬 6개를 묶은 플러그인 명세
├── CLAUDE.md                    # 레포 관리 규칙 + 페르소나
├── README.md                    # 설치법, 스킬 목록
├── docs/                        # (이 정리 문서)
├── scripts/
│   ├── link-skills.sh           # 스킬을 ~/.claude/skills/ 에 심볼릭 링크
│   └── list-skills.sh           # SKILL.md 목록 출력
└── skills/
    ├── engineering/   # debug-mantra, post-mortem, scrutinize, qwen-agent
    ├── productivity/  # management-talk, qwenchance
    └── misc/          # (비어 있음)
```
- `link-skills.sh`는 `personal/`, `in-progress/`, `deprecated/` 폴더의 스킬은 설치하지 않는다.

### 동작 원리
`SKILL.md` 상단 YAML의 `description`만 평소에 로드되어 있다가, 대화가 조건에 맞으면 본문 전체가 로드되어 그 규칙대로 동작한다. `/스킬이름`으로 직접 호출도 가능하다.

### 스킬 6개

| 스킬 | 하는 일 | 언제 | 도움 |
|---|---|---|---|
| debug-mantra | 디버깅 4계명: 재현 → 실패 경로 추적 → 가설 반증 → 모든 실험 기록 대조 | 버그·에러·스택트레이스 | 추측성 수정 방지, 재현 전 수정 제안 금지 |
| post-mortem | 수정된 버그의 공식 기술 기록(원인·메커니즘·수정·검증·누락 이유·후속조치) | 수정·검증 완료 후 | 재현/원인/수정/검증 중 하나라도 없으면 작성 거부 |
| scrutinize | 외부인 시선 리뷰: 더 간단한 방법 먼저 묻고 실제 코드 경로 추적 | PR·설계·계획 리뷰 | "LGTM" 금지, 근거 없는 지적 금지 |
| management-talk | 개발자 언어 → 리더십 언어, JIRA/슬랙/스탠드업/이메일/회의용 형식 | 상태 보고, 요약 | 코드 식별자 제거, JIRA 키·제품명 유지 |
| qwen-agent | 단순 작업을 `claude-9arm`(Qwen) 서브에이전트에 위임 | 대량 리네임, 포맷팅, 로그 요약 등 | 토큰 비용 절감 (설계·디버깅·보안은 위임 금지) |
| qwenchance | 루프·과도한 추론·컨텍스트 고갈 감지 및 인수인계 | 긴 작업, 반복 행동 | 같은 실패 명령 3회 금지, 추론 약 1000단어 제한 |

연계 흐름: `debug-mantra`(디버깅) → `post-mortem`(기술 기록) → `management-talk`(윗선 보고)

### 주의점
1. README 설치 명령이 원본(`thananon`)을 가리킨다. 포크 버전은 `bmshin94/9arm-skills` 사용.
2. qwen 스킬 2개는 `claude-9arm` alias와 9arm 게이트웨이가 필요한데 레포에 포함되어 있지 않다.
3. `qwenchance`가 호출하는 `handoff` 스킬이 레포에 없다.
4. **LICENSE 파일이 없다** → 원본 내용의 재배포·판매 권리가 불명확.
5. 예시의 `Tada`, `dumbModel`은 가상의 이름이다.

---

## 2. 쉬운 설명 (비유)

**Claude Code = 똑똑하지만 덤벙대는 신입사원, 이 레포 = 선배의 업무 수첩**

| 스킬 | 비유 |
|---|---|
| debug-mantra | 명탐정 수사 수칙 — 재현 전에는 체포(수정) 금지 |
| post-mortem | 사고 경위서 양식 |
| scrutinize | 까칠한 시니어 리뷰어 — "이거 꼭 필요해?" |
| management-talk | 개발자어 → 사장님어 통역사 |
| qwen-agent | 셰프(Claude)는 요리, 설거지는 알바(Qwen) |
| qwenchance | 마라톤 페이스메이커 — "같은 코스 3바퀴째야!" |

얻는 이점: ① AI의 대충 수정 감소 ② 문서·보고 시간 단축 ③ 비용 절약 + 긴 작업 안정성

---

## 3. Q&A

### 설치 및 사용법
사전 준비: Claude Code 설치(`npm install -g @anthropic-ai/claude-code`) 및 로그인.

```bash
# A. npx (권장)
npx skills add bmshin94/9arm-skills   # 포크 버전
npx skills add thananon/9arm-skills   # 원본

# B. 클론 + 스크립트 (Mac/Linux/WSL)
git clone https://github.com/bmshin94/9arm-skills.git
cd 9arm-skills
./scripts/link-skills.sh
./scripts/list-skills.sh

# C. 특정 프로젝트 전용: 스킬 폴더를 <프로젝트>/.claude/skills/ 에 복사
```
사용: `/debug-mantra`, `/scrutinize`, `/post-mortem` 직접 호출 또는 "이거 에러 나", "PR 리뷰해줘", "팀장님용 슬랙 버전" 같은 자연어로 자동 발동.

### 플러그인? 스킬? MCP?
- 본질은 **스킬(Agent Skills)**.
- `plugin.json`이 있어 플러그인 형태로도 포장되어 있으나 `marketplace.json`은 없어 `/plugin` 설치는 추가 설정이 필요할 수 있다.
- **MCP 아님** — 실행되는 서버 코드가 없다.

### API 토큰 필요?
- 스킬 자체: 불필요 (텍스트 파일)
- Claude Code 실행: Claude 구독 로그인 또는 Anthropic API 키
- qwen-agent: 9arm 게이트웨이 권한 필요 → 없으면 로컬 Qwen + Anthropic 호환 프록시로 alias 직접 구성
- JIRA 게시 기능: 선택, 사용 시 JIRA API 토큰 필요

### 왜 유명할까? (추정 — 스타 수는 직접 확인하지 못함)
1. 9arm 본인의 유튜버 인지도
2. 하드코어 시스템 개발 현업 노하우
3. 짧고 설치 한 줄, 코드 없이 바로 사용
4. 개발자 공통 고민(추측성 수정, 보고서, 토큰비)을 정확히 해결
5. Agent Skills 확산 시기의 좋은 예시

### 로컬 에이전트 구축에 도움?
큰 도움. `qwen-agent`는 "비싼 모델=두뇌, 싼 로컬 모델=손발" 분업 설계 교과서(작업 쪼개기, 자기완결형 지시, 결과 재검증), `qwenchance`는 작은 모델의 루프·컨텍스트 문제 가드레일.

```
Ollama/vLLM(Qwen) → LiteLLM 등 Anthropic 호환 프록시
→ alias claude-local='ANTHROPIC_BASE_URL=http://localhost:4000 claude --model <모델명>'
→ Claude Code가 qwen-agent 스킬로 잡일을 claude-local에 위임
```
스킬은 지시서일 뿐이므로 모델 서버·프록시는 별도 구축 필요.

### React / PHP로 만들 수 있나?
1. **스킬 자체**: 마크다운이라 언어 무관 — `react-component-review`, `php-security-check` 등 직접 제작 가능.
2. **스킬 활용 웹서비스**: React(프론트) + PHP(백엔드)에서 Claude API 호출 시 `SKILL.md`를 system 프롬프트로 주입.

```php
<?php // api/translate.php
$skill = file_get_contents(__DIR__ . '/skills/management-talk/SKILL.md');
$body = json_decode(file_get_contents('php://input'), true);
$ch = curl_init('https://api.anthropic.com/v1/messages');
curl_setopt_array($ch, [
  CURLOPT_RETURNTRANSFER => true,
  CURLOPT_POST => true,
  CURLOPT_HTTPHEADER => [
    'content-type: application/json',
    'x-api-key: ' . getenv('ANTHROPIC_API_KEY'), // 서버에만 보관
    'anthropic-version: 2023-06-01',
  ],
  CURLOPT_POSTFIELDS => json_encode([
    'model' => 'claude-sonnet-5',
    'max_tokens' => 2000,
    'system' => $skill,
    'messages' => [['role' => 'user', 'content' => "채널: {$body['channel']}\n\n{$body['text']}"]],
  ]),
]);
header('Content-Type: application/json');
echo curl_exec($ch);
```
```jsx
const res = await fetch('/api/translate.php', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ channel: 'slack', text: memo }),
});
const data = await res.json();
setResult(data.content[0].text);
```

---

## 4. 수익화 아이디어

> ⚠️ 원본에 LICENSE가 없으므로 원본 SKILL.md를 그대로 판매·유료 서비스에 사용하지 말 것. 구조와 아이디어만 참고하고 직접 새로 작성한다.

| # | 아이디어 | 난이도 | 내용 | 수익 모델 |
|---|---|---|---|---|
| 1 | 한국형 스킬팩 판매 | ⭐ | 주간보고, 품의서, 한국형 장애보고서, 한국어 코드리뷰, 온보딩 | 개인 1~3만원 / 팀 10~30만원 (크몽, Gumroad, 인프런) |
| 2 | 개발자어→보고서 번역 SaaS | ⭐⭐ | React+PHP. 개발 메모 → 슬랙/임원/고객공지/주간보고 | 무료 월 10회 → 개인 월 9,900원 → 팀 월 49,000원 |
| 3 | 포스트모템 자동화 | ⭐⭐⭐ | 슬랙 장애 대화+커밋+로그 → 장애보고서 초안 | 팀당 월 5~10만원 |
| 4 | 한국어·PHP 특화 코드리뷰 봇 | ⭐⭐⭐ | GitHub App, scrutinize 스타일, 그누보드/라라벨/CI 특화 | 레포당 월 1~3만원 |
| 5 | 강의·콘텐츠 | ⭐ | "Claude Code 스킬로 생산성 3배", "하이브리드 AI로 비용 절감" | 강의·전자책, 다른 사업의 마케팅 엔진 |
| 6 | 기업 맞춤 스킬 구축 외주 | ⭐⭐ | 사내 컨벤션·배포·리뷰 기준을 스킬화 | 건당 100~500만원 + 유지보수 |
| 7 | 로컬 LLM 셋업 대행 | ⭐⭐⭐ | 보안 민감 기업용 온프레미스 Qwen + 에이전트 | 구축비 + 월 운영비 |

### 추천 로드맵
| 기간 | 할 일 |
|---|---|
| 1~2주 | 한국형 스킬 3개 제작, 깃허브 공개 |
| 3~4주 | 블로그/유튜브 튜토리얼 발행 |
| 2개월 | 유료 스킬팩 + 랜딩페이지 |
| 3개월 | React+PHP 번역 SaaS MVP |
| 이후 | 반응 좋은 B2B 방향으로 확장 |

**1순위 추천: 아이디어 2** — React·PHP 스택과 일치하고 한 달 내 MVP 가능.
