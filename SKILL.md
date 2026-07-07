---
name: skills-for-the-blind
description: 시각장애인 코딩 에이전트 사용자를 위한 스킬 번들 설치·설정·검증 오케스트레이터. 문서 읽기(docparse, hwpx-automation), 요약(abridge), 에이전트 응답 음성 듣기(agent-cli-tts-summary), 텍스트↔음성 변환(speech-toolkit) 5종을 한 번에 설치한다. 다음 요청에 사용: skills-for-the-blind 설치, 시각장애인용 스킬 셋업, 스크린 리더 사용자 에이전트 환경 구성, blind user agent setup. Install and configure the skills-for-the-blind bundle: five field-tested skills covering the full workflow of a blind screen-reader user working with coding agent CLIs.
license: MIT
---

# skills-for-the-blind

시각장애인 스크린 리더 사용자가 코딩 에이전트 CLI(Claude Code, Codex, Antigravity 등)로 일하는 전체 루프를 덮는 스킬 번들이다: 문서를 읽고, 줄이고, 듣고, 말로 입력한다. 다섯 스킬 모두 시각장애인 저자가 실무에서 검증했다.

이 저장소 자체는 코드를 담지 않는 얇은 오케스트레이터다. 실제 기능은 아래 다섯 개의 독립 공개 저장소가 각각 담당하며, 이 스킬은 그것들을 한 번에 설치·점검·보고하는 역할만 한다.

## 멤버 스킬

| 스킬 | 담당 영역 | 개별 설치 |
|---|---|---|
| **docparse** | PDF·이미지·스캔·HWPX 등 다양한 문서를 고품질 마크다운으로 파싱(읽기) | `npx skills add Engccer/docparse -g` |
| **hwpx-automation** | HWP/HWPX 문서 변환·편집·서명 이미지 삽입(쓰기·구조 조작) | `npx skills add Engccer/hwpx-automation -g` |
| **abridge** | 문서를 원문 톤을 살려 발췌형으로 압축(줄이기) | `npx skills add Engccer/abridge -g` |
| **agent-cli-tts-summary** | 에이전트 턴 요약을 한국어 음성으로 듣기(TTS 훅 설치) | `npx skills add Engccer/agent-cli-tts-summary -g` |
| **speech-toolkit** | 텍스트↔음성 변환(TTS/STT), 오디오북·전사(말로 입력·듣기) | `npx skills add Engccer/speech-toolkit -g` |

## 전체 설치

이 스킬이 이미 설치되어 실행 중이라면(즉 이 SKILL.md가 트리거되었다면), 아래 다섯 명령을 순서대로 실행해 멤버 스킬을 모두 설치한다.

```bash
# bash / zsh / Git Bash
for repo in docparse hwpx-automation abridge agent-cli-tts-summary speech-toolkit; do
  npx skills add "Engccer/$repo" -g
done
```

```powershell
# Windows PowerShell
foreach ($repo in "docparse","hwpx-automation","abridge","agent-cli-tts-summary","speech-toolkit") {
  npx skills add "Engccer/$repo" -g
}
```

사용자가 특정 스킬만 원한다고 명시하면(예: "요약이랑 TTS만") 해당 항목만 설치하고 나머지는 건너뛴다. 별다른 지정이 없으면 다섯 개 전부를 설치한다.

## 설치 확인

각 `npx skills add` 실행 후 다음을 확인해 보고한다:

1. 명령이 오류 없이 종료되었는가(설치 실패 시 저장소 이름 오타나 네트워크 문제를 먼저 의심).
2. 스킬 디렉터리(예: `~/.claude/skills/docparse`)가 실제로 생성되었는가.
3. 코딩 에이전트를 재시작하거나 새 세션을 시작하면 다섯 스킬의 트리거 문구(예: "PDF 파싱해줘", "원문 톤 살려서 요약해줘")에 반응하는가.

설치 자체는 API 키가 없어도 끝난다. 다만 클라우드 파서·TTS/STT 제공자를 실제로 호출하려면 아래 환경 변수가 필요하므로, 설치 직후 사용자가 어떤 기능을 쓸 계획인지 확인하고 필요한 키 발급을 안내한다.

## 환경 변수와 비용

| 스킬 | 비용 구조 | 관련 환경 변수 |
|---|---|---|
| docparse | 로컬 파서(hwpx_local, opendataloader)는 무료·오프라인. 클라우드 파서는 실제로 호출한 것만 과금 | `LLAMAPARSE_API_KEY`, `UPSTAGE_API_KEY`, `GEMINI_API_KEY`, `MISTRAL_API_KEY`, `COREPIN_API_KEY`, `GOOGLE_VISION_API_KEY`(택1 이상, 무료 티어 존재) |
| hwpx-automation | 전부 무료·로컬 실행. HWP→HWPX 변환은 Java, PDF 변환·서명 삽입은 Windows+한컴오피스 필요(둘 다 API 비용 없음) | 없음 |
| abridge | 요약 자체는 에이전트가 직접 수행하므로 무료. 선택적 오디오북 생성 단계에서만 유료 API 호출 | `ELEVENLABS_API_KEY`(오디오북 선택 시에만) |
| agent-cli-tts-summary | 기본값인 OS 내장 음성(Windows SAPI, macOS `say`)은 무료. 선택적 클라우드 음성 provider만 과금 | `GEMINI_API_KEY`(선택적 provider) |
| speech-toolkit | 스크립트별 클라우드 API 종량 과금(제공자마다 무료 티어 존재) | `GEMINI_API_KEY`, `OPENAI_API_KEY`, `ELEVENLABS_API_KEY`, `SPEECHIFY_API_KEY`, `DEEPGRAM_API_KEY`, `DAGLO_API_KEY`, `MISTRAL_API_KEY`(사용할 스크립트에 필요한 것만) |

각 환경 변수는 사용자 셸이나 시스템 환경 변수로만 설정한다. 어떤 스크립트에도 키를 하드코딩하지 않는다. 무료로만 쓰고 싶다면 docparse의 로컬 파서(hwpx_local, opendataloader)와 hwpx-automation, agent-cli-tts-summary의 OS 내장 음성만으로도 "문서 읽기 → 편집 → 요약 → 음성으로 듣기"의 핵심 루프가 동작한다.

## 참고

이 저장소는 다섯 멤버 스킬의 코드를 복제하지 않는다. 각 스킬의 상세 사용법·함정·레퍼런스는 설치 후 해당 스킬 폴더의 `SKILL.md`와 `references/`를 따른다.
