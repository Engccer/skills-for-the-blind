# skills-for-the-blind

시각장애인 스크린 리더 사용자가 코딩 에이전트 CLI(Claude Code, Codex, Antigravity 등)로 일하는 전체 루프를 한 번에 갖추기 위한 스킬 번들이다. 문서를 읽고(docparse, hwpx-automation), 줄이고(abridge), 듣고(agent-cli-tts-summary), 말로 입력한다(speech-toolkit). 다섯 스킬 모두 시각장애인 저자가 실무에서 검증했다.

이 저장소 자체에는 기능 코드가 없다. 실제 동작은 다섯 개의 독립 공개 저장소가 각각 담당하며, 여기서는 그것들을 한 번에 설치·점검하는 얇은 오케스트레이터 스킬(`SKILL.md`)만 제공한다. 각 멤버 스킬은 이 번들 없이도 독립적으로 설치해 쓸 수 있고, 반대로 이 번들을 설치하면 다섯 개를 한 번에 갖출 수 있다.

## 멤버 스킬

| 스킬 | 담당 영역 | 개별 설치 |
|---|---|---|
| **[docparse](https://github.com/Engccer/docparse)** | PDF·이미지·스캔·HWPX 등 다양한 문서를 고품질 마크다운으로 파싱(읽기) | `npx skills add Engccer/docparse -g` |
| **[hwpx-automation](https://github.com/Engccer/hwpx-automation)** | HWP/HWPX 문서 변환·편집·서명 이미지 삽입(쓰기·구조 조작) | `npx skills add Engccer/hwpx-automation -g` |
| **[abridge](https://github.com/Engccer/abridge)** | 문서를 원문 톤을 살려 발췌형으로 압축(줄이기) | `npx skills add Engccer/abridge -g` |
| **[agent-cli-tts-summary](https://github.com/Engccer/agent-cli-tts-summary)** | 에이전트 턴 요약을 음성으로 듣기(TTS 훅 설치, 요약 언어 선택 가능·기본 한국어) | `npx skills add Engccer/agent-cli-tts-summary -g` |
| **[speech-toolkit](https://github.com/Engccer/speech-toolkit)** | 텍스트↔음성 변환(TTS/STT), 오디오북·전사(말로 입력·듣기) | `npx skills add Engccer/speech-toolkit -g` |

## 설치

### 필요한 것

- **코딩 에이전트 CLI**: Claude Code, Codex, Antigravity 등 스킬(SKILL.md)을 인식하는 에이전트.
- **Node.js**: 설치 명령이 Node.js에 포함된 `npx`로 실행된다. `node --version`으로 확인한다.
- **Python 3.10 이상**: docparse·hwpx-automation·speech-toolkit의 스크립트와 agent-cli-tts-summary의 설치 보조 스크립트에 필요하다. 설치 자체는 Python 없이 끝나며, abridge와 agent-cli-tts-summary의 음성 재생 루프(OS 내장 음성)는 Python 없이 동작한다.

가장 빠른 방법은 이 메타 저장소 하나만 설치하는 것이다:

```bash
npx skills add Engccer/skills-for-the-blind -g
```

설치 후 코딩 에이전트에게 "skills-for-the-blind 설치해줘"라고 요청하면, 이 스킬이 오케스트레이터 역할을 맡아 위 다섯 멤버 스킬을 순서대로 설치하고 결과를 검증해 보고한다.

멤버 스킬만 개별로 설치하고 싶다면 표의 명령을 하나씩 실행하거나, 아래 스크립트로 한 번에 실행한다.

```bash
for repo in docparse hwpx-automation abridge agent-cli-tts-summary speech-toolkit; do
  npx skills add "Engccer/$repo" -g
done
```

## 비용 안내

번들과 다섯 스킬의 코드 자체는 모두 무료·오픈소스(MIT)다. 실제 비용은 사용자가 선택해서 호출하는 클라우드 API 사용량에서만 발생한다.

- **완전 무료로도 핵심 루프가 동작한다**: docparse의 로컬 파서(hwpx_local, opendataloader), hwpx-automation 전체(HWP/HWPX 변환·편집), agent-cli-tts-summary의 기본 OS 내장 음성(Windows SAPI, macOS `say`)만으로 "문서 읽기 → 편집 → 요약 → 음성으로 듣기"까지 API 키 없이 가능하다.
- **선택적으로 클라우드 API를 쓰면 그만큼만 과금된다**: docparse의 고급 파서(LlamaParse, Upstage, Gemini, Mistral 등), abridge의 오디오북 후처리(ElevenLabs), speech-toolkit의 TTS/STT 스크립트(Gemini, OpenAI, ElevenLabs, Speechify, Deepgram, Daglo, Mistral)는 각 제공자의 API 키가 있어야 동작하며, 대부분 무료 티어를 함께 제공한다. 상세 환경 변수 목록은 각 멤버 저장소의 README와 이 저장소 `SKILL.md`의 표를 참고한다.

## 호스팅 프록시를 제공하지 않는 이유

이 번들은 API 키를 대신 처리해 주는 호스팅 프록시 서버를 두지 않는다. 각 스크립트가 사용자 자신의 API 키로 제공자에 직접 호출하도록 만들어, 비용 통제와 데이터 흐름을 사용자 손에 그대로 남기기 위함이다.

## License

MIT (c) 2026 Engccer

각 멤버 저장소도 모두 MIT이지만, 일부는 서드파티 의존성에 별도 라이선스가 붙는다(예: hwpx-automation의 `python-hwpx`는 비상업 라이선스). 상세는 해당 멤버 저장소의 README·LICENSE·NOTICE를 참고한다.

---

## English

skills-for-the-blind is a skill bundle that gives a blind screen-reader user working with coding agent CLIs (Claude Code, Codex, Antigravity, and similar) the full workflow in one install: read documents (docparse, hwpx-automation), condense them (abridge), listen to agent replies (agent-cli-tts-summary), and speak instead of type (speech-toolkit). All five skills have been field-tested by a blind author in real work.

This repository holds no functional code of its own. The actual work is done by five independent public repositories; this repo is only a thin orchestrator skill (`SKILL.md`) that installs and verifies all five in one pass. Each member skill also works standalone without this bundle, and installing this bundle pulls in all five at once.

**Member skills:**

| Skill | Scope | Individual install |
|---|---|---|
| [docparse](https://github.com/Engccer/docparse) | Parses PDFs, images, scans, and HWPX files into high-quality Markdown (reading) | `npx skills add Engccer/docparse -g` |
| [hwpx-automation](https://github.com/Engccer/hwpx-automation) | Converts, edits, and inserts signature images into HWP/HWPX documents (writing/structural edits) | `npx skills add Engccer/hwpx-automation -g` |
| [abridge](https://github.com/Engccer/abridge) | Condenses documents into tone-preserving, extractive summaries (shortening) | `npx skills add Engccer/abridge -g` |
| [agent-cli-tts-summary](https://github.com/Engccer/agent-cli-tts-summary) | Installs a hook that reads agent turn summaries aloud, in a selectable language (Korean by default) | `npx skills add Engccer/agent-cli-tts-summary -g` |
| [speech-toolkit](https://github.com/Engccer/speech-toolkit) | Text-to-speech and speech-to-text scripts for narration, audiobooks, and transcription (speaking/listening) | `npx skills add Engccer/speech-toolkit -g` |

**Prerequisites:** a coding agent CLI that recognizes skills (Claude Code, Codex, Antigravity, or similar), Node.js (the installer runs through `npx`), and Python 3.10+ for the scripts used by docparse, hwpx-automation, speech-toolkit, and agent-cli-tts-summary's setup helpers. The install itself completes without Python, and abridge plus agent-cli-tts-summary's playback loop (OS-native voices) run without it.

**Install:**

```bash
npx skills add Engccer/skills-for-the-blind -g
```

After installing, ask your coding agent to "install skills-for-the-blind" and this skill will install and verify all five members in turn. To install members individually, run the commands in the table above, or loop over all five:

```bash
for repo in docparse hwpx-automation abridge agent-cli-tts-summary speech-toolkit; do
  npx skills add "Engccer/$repo" -g
done
```

**Cost:** the bundle and all five skills are free and open source (MIT). Costs only come from optional cloud API calls you choose to make. The core loop (read, edit, summarize, listen) works entirely for free using docparse's local parsers, all of hwpx-automation, and agent-cli-tts-summary's default OS-native voices. Advanced parsers, audiobook narration, and the speech-toolkit TTS/STT scripts require your own API key for the relevant provider (most offer a free tier).

**Why no hosted proxy:** this bundle does not run a hosted proxy that handles API keys on your behalf. Every script calls the provider directly with your own key, keeping cost control and data flow entirely in your hands.

**License:** MIT (c) 2026 Engccer. Member repositories are also MIT, though some carry additional third-party licenses (for example, hwpx-automation's `python-hwpx` dependency is non-commercial). See each member repository's README/LICENSE/NOTICE for details.
