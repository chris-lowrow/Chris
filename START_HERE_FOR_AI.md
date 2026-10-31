# START HERE FOR AI · 릴스 자동 편집 키트 설치

## 사람이 읽는 부분 (1분)

이 폴더는 AI(Claude Code)가 읽고 스스로 설치하는 키트입니다. 사람은 아래 세 가지만 하면 됩니다.

1. GitHub에서 새 저장소를 만듭니다. **반드시 비공개(Private)** 로 만드세요.
2. 압축을 푼 폴더 **안의 파일과 폴더를 전부** 저장소에 올립니다.
   (저장소 화면 → Add file → Upload files → 끌어다 놓기 → Commit changes)
3. Claude 앱이나 claude.ai/code에서 그 저장소로 Claude Code 세션을 열고 이렇게 말합니다.

   > START_HERE_FOR_AI.md 읽고 순서대로 진행해줘

그 다음은 AI가 순서대로 안내합니다. 중간에 설정 화면에서 몇 가지를 켜 달라고 하는데,
한 번에 모아서 알려줍니다. API 키는 채팅에 붙여 넣지 말고, AI가 알려주는 설정 칸에 넣으세요.

---

## AI가 읽는 부분

너는 이 저장소에서 인스타그램 릴스를 자동으로 편집해 줄 Claude Code다. 이 파일은 설치 순서다.
처음부터 끝까지 순서대로 진행하고, 사람에게는 한국어로 짧고 쉽게 말한다.

### 원칙

- 이 파일을 열 때마다 1단계와 2단계를 다시 실행한다. 둘 다 몇 번을 돌려도 같은 결과가 나오고,
  2단계 진단 결과를 보면 어디까지 끝났는지 알 수 있다. 진단에서 처음 실패한 곳부터 이어간다.
- 사람이 해야 하는 설정(커넥터, 네트워크, 키, 설치 스크립트)은 **한 번에 모아서** 하나의 목록으로
  알려준다. 클라우드 환경 설정과 커넥터는 새 세션에서 적용되므로, 여러 번 나눠 부탁하면 사람이
  세션을 여러 번 새로 열어야 한다.
- API 키, 토큰, 비밀번호는 절대 채팅으로 받지 않는다. 사람이 붙여 넣으면 쓰지 말고, 그 키를
  해당 서비스에서 폐기하고 새로 만들어 설정 칸에 넣으라고 말한다.
- 크레딧이 드는 작업(ElevenLabs, Higgsfield, Firecrawl)은 예상 비용을 먼저 말하고 허락받는다.
- 막힌 네트워크를 우회하지 않는다. 막히면 어떤 주소를 허용해야 하는지 사람에게 말한다.
- 할 수 없는 일을 했다고 하지 않는다. 실패하면 실패한 명령과 메시지를 그대로 보여준다.

### 0단계 · 실행 환경 확인

- 셸 명령을 실행할 수 없는 일반 채팅이라면 여기서 멈추고 사람에게 말한다:
  "이 키트는 Claude Code에서만 동작해요. claude.ai/code에서 이 파일이 들어 있는 GitHub 저장소로
  세션을 열어 주세요."
- `echo "$CLAUDE_CODE_REMOTE"`가 `true`면 클라우드 세션이다. 이 파일은 클라우드 기준으로 쓰여 있다.
  사람 컴퓨터에서 도는 Claude Code라면 맨 아래 "내 컴퓨터에서 쓸 때"를 함께 따른다.

### 1단계 · 파일 배치 (AI 혼자)

업로드할 때 점(.)으로 시작하는 파일이 빠지지 않도록 이름을 바꿔 두었다. 저장소 루트에서 원래
이름으로 돌린다. 이미 있는 파일은 덮어쓰지 않고 합친다.

```bash
set -e
if [ -d claude ]; then python3 - <<'PY'
import os, shutil
for d, _, fs in os.walk("claude"):              # copy only files that are not there yet
    t = os.path.join(".claude", os.path.relpath(d, "claude")); os.makedirs(t, exist_ok=True)
    for f in fs:
        if not os.path.exists(os.path.join(t, f)): shutil.copy2(os.path.join(d, f), os.path.join(t, f))
shutil.rmtree("claude")
PY
fi
if [ -f mcp.json ]; then
  if [ -f .mcp.json ]; then
    python3 -c "import json;a=json.load(open('.mcp.json'));b=json.load(open('mcp.json'));a.setdefault('mcpServers',{});[a['mcpServers'].setdefault(k,v) for k,v in b['mcpServers'].items()];json.dump(a,open('.mcp.json','w'),indent=2)"
    rm mcp.json
  else mv mcp.json .mcp.json; fi
fi
if [ -f gitignore.txt ]; then touch .gitignore; while read -r l; do grep -qxF "$l" .gitignore || echo "$l" >> .gitignore; done < gitignore.txt; rm gitignore.txt; fi
chmod +x .claude/skills/reels-make/scripts/*.sh
ls .claude/skills .claude/instagram
```

- 스킬 폴더 19개(`reels-make`, `reels-style`, `watch`, `ig-*` 등)와 `.claude/instagram/`이 보이면 된다.
- 사람에게 저장소가 비공개인지 확인한다. 공개면 GitHub 저장소 → Settings → General → 맨 아래
  Danger Zone → Change visibility → Private로 바꾸라고 안내한다. 운영 전략, 드라이브 폴더 ID,
  회원 이야기가 이 저장소에 쌓이기 때문이다.
- 바뀐 파일은 커밋한다 (8단계의 "기본 브랜치에 반영"도 읽어 둘 것).

### 2단계 · 진단 (AI 혼자)

아래를 전부 실행하고, 결과를 표 하나로 사람에게 보여준다 (항목 / 상태 / 필요한 조치).

```bash
# 도구
ffmpeg -hide_banner -filters 2>/dev/null | grep -q zscale && echo "OK ffmpeg(zscale)" || echo "NO ffmpeg 또는 zscale 없음"
python3 -c "import yaml, PIL" 2>/dev/null && echo "OK python yaml/PIL" || echo "NO python yaml/PIL"
command -v npx >/dev/null && echo "OK node $(node -v)" || echo "NO node/npx"
# 키 (값은 출력하지 않는다)
[ -n "${ELEVENLABS_API_KEY:-}" ] && echo "OK ELEVENLABS_API_KEY" || echo "NO ELEVENLABS_API_KEY"
# 네트워크: 숫자가 나오면 연결됨(401·404도 연결된 것). 000이나 프록시 403이면 막힘
for u in https://drive.google.com https://drive.usercontent.google.com https://api.elevenlabs.io/v1/models \
         https://cdn.jsdelivr.net/npm/gsap@3.14.2/dist/gsap.min.js https://registry.npmjs.org/hyperframes; do
  printf "%s " "$u"; curl -s -o /dev/null -m 15 -w "%{http_code}\n" "$u"; done
```

- 프록시가 막았는지 헷갈리면 응답 본문을 본다 (`curl -s -m 15 URL | head -c 300`). 정책이나 차단을
  말하는 내용이면 막힌 것이다. 클라우드 세션에 `/root/.ccr/README.md`가 있으면 거기 적힌 진단 방법을 쓴다.
- 커넥터: 너의 도구 목록에 Google Drive 도구(예: `search_files`, `download_file_content`)가 있는지 본다.
  없으면 연결 필요. Higgsfield 도구는 선택이다.
- 도구가 없고 패키지 설치가 허용돼 있으면 이번 세션에서 바로 설치해 본다:
  `apt-get update && apt-get install -y ffmpeg`, `pip install pyyaml pillow`
  (pip가 거부하면 `--break-system-packages`를 붙인다). 그래도 3단계의 설치 스크립트는 넣게 한다.
  클라우드 컨테이너는 세션마다 새로 만들어져서, 설치 스크립트가 없으면 다음 세션에 다시 없어진다.

진단이 전부 OK면 4단계로 간다.

### 3단계 · 사람이 할 설정 (한 번에 모아서)

2단계에서 빠진 것만 골라 아래 형식으로 하나의 목록을 만들어 보여준다. 메뉴 이름은 앱 버전에 따라
조금 다를 수 있다고 덧붙인다.

**A. 클라우드 환경 설정** (세션 화면 위쪽 제목 줄의 클라우드 환경 메뉴 → Edit)

1. **Network access**: Limited를 고르고 Allowed domains에 아래를 넣는다. "Allow package managers"
   체크는 켜 둔다. (앱이 예전 버전이면 Custom 아래에 있고, 기본 패키지 목록 포함 체크가 있다.)
   - 필수: `drive.google.com`, `drive.usercontent.google.com`, `api.elevenlabs.io`, `cdn.jsdelivr.net`
   - 선택: `upload.higgsfield.ai`, `d8j0ntlcm91z4.cloudfront.net` (Higgsfield로 AI 영상을 만들 때),
     `api.firecrawl.dev` (경쟁 계정·릴스 조사), `api.perplexity.ai` (논문·수치 확인)
2. **Environment variables**: `ELEVENLABS_API_KEY=키값` 한 줄. 선택: `FIRECRAWL_API_KEY`,
   `PERPLEXITY_API_KEY`. (Network secrets 칸만 보이면 거기에 같은 이름으로 넣고, 4단계에서 AI가 확인한다.)
3. **Setup script**: 아래 두 줄. (설치 스크립트는 환경 변수를 읽지 못하니 키를 여기에 쓰지 않는다.)
   ```bash
   command -v ffmpeg >/dev/null || (apt-get update && apt-get install -y ffmpeg)
   pip install -q pyyaml pillow || pip install -q --break-system-packages pyyaml pillow
   ```

**B. ElevenLabs 키 만들기** (음성 인식으로 자막 타이밍을 뽑는다)

- elevenlabs.io 가입 → 프로필 → API Keys → Create. 권한에서 **Speech to Text**를 켠다.
  AI 목소리(얼굴 없는 영상)를 쓸 거면 **Text to Speech**와 **Voices** 읽기도 켠다.
- 무료 요금제는 한 달 10,000크레딧이다. 음성 인식은 1분에 약 70크레딧이라 한 달 2시간 남짓 쓸 수 있다.
  AI 목소리는 1분에 약 1,000크레딧이라 무료로는 한 달 10분이 안 된다.

**C. 커넥터** (https://claude.ai/customize/connectors)

- 필수: **Google Drive** 연결. 영상은 드라이브로 주고받는다 (채팅 첨부는 영상에 너무 작다).
- 선택: **Higgsfield** (AI 영상·이미지. 유료 요금제와 크레딧 필요. 자세 방향 설명에는 쓰지 않는다).

**D. 구글 드라이브 폴더**

- 드라이브에 "릴스 원본" 폴더를 만들고 공유 → 일반 액세스 → "링크가 있는 모든 사용자"(뷰어).
  폴더 링크는 다음 세션(4단계)에서 채팅으로 보내 달라고 한다. 폴더 링크는 키가 아니라서 채팅으로 받아도 된다.

**E. 다 했으면**

- 환경 설정과 커넥터는 새 세션에서 적용된다. 같은 저장소로 **새 세션**을 열고
  "설정 했어. START_HERE_FOR_AI.md 이어서 진행해줘"라고 말하라고 안내한다.

### 4단계 · 동작 확인 (AI 혼자, 크레딧 거의 없음)

1. 2단계 진단을 다시 돌려 전부 OK인지 본다.
2. 렌더 테스트 (크레딧 없음, 1~3분. 첫 실행은 렌더러를 내려받아 더 걸린다):
   ```bash
   bash .claude/skills/reels-make/scripts/smoke.sh
   ```
   마지막 줄이 `SMOKE OK .../work/smoke/reel.mp4`면 성공. 결과 영상을 사람에게 보내도 된다(3초짜리 테스트 화면).
3. ElevenLabs 키 테스트 (약 1~2크레딧. 사람에게 먼저 말한다):
   ```bash
   mkdir -p work/stt-test && ffmpeg -v error -y -f lavfi -i "sine=frequency=300:duration=2" work/stt-test/t.mp3
   python3 .claude/skills/reels-make/scripts/stt.py work/stt-test/t.mp3 --out work/stt-test
   ```
   `words.json: 0 words`가 나오면 성공 (사인파라 단어는 없다). `HTTP 401`에 `missing_permissions`면
   키에 Speech to Text 권한이 없는 것이다.
4. 드라이브 테스트: 사람에게 "릴스 원본" 폴더 링크를 받아(`/folders/FOLDER_ID`) Google Drive 커넥터로 나열한다 (`search_files`, `parentId = 'FOLDER_ID'`).
   사람에게 휴대폰으로 아무 영상이나 10초 찍어 그 폴더에 올리게 하고, 내려받아 본다:
   ```bash
   curl -sSL -o work/test.mov "https://drive.usercontent.google.com/download?id=FILE_ID&export=download&confirm=t"
   ffprobe -v error -show_entries format=duration work/test.mov
   ```
   길이가 나오면 성공. HTML이 받아지면 파일이나 폴더가 "링크가 있는 모든 사용자"로 공유되지 않은 것이다.

### 5단계 · 촬영 설정 안내 (사람)

사람에게 그대로 전한다.

- 아이폰: 설정 > 카메라 > 포맷 "고효율", 비디오 녹화 "1080p 30fps", **HDR 비디오 끔**.
  (HDR로 찍으면 색이 뜨고 렌더가 아주 느려진다. 자동 변환은 되지만 처음부터 끄는 게 낫다.)
- 세로로 찍고, 위쪽에 그래픽이 올라가니 얼굴과 동작은 화면 가운데에 둔다.
- 대본 하나는 한 번에 쭉 찍는다. 틀리면 멈추지 말고 2초 쉰 뒤 그 문장 처음부터 다시 말한다.
  AI가 마지막으로 끝까지 말한 테이크를 고르고, 말 없는 구간은 자동으로 자른다.
- 다 찍으면 드라이브 폴더에 올리고 채팅에 "올렸어"라고만 말한다.

### 6단계 · 내 계정에 맞추기 (AI + 사람 인터뷰)

1. `.claude/instagram/strategy.template.md`를 열고, `[ ]` 칸을 사람에게 한두 개씩 물어서 채운다
   (계정 핸들, 분야와 대상, 소속, 톤, 메인 포맷, 업로드 주기, 드라이브 폴더 ID).
   "(기본값)" 줄은 그대로 두되, 사람이 다르게 원하면 바꾼다. 다 채우면 `strategy.md`로 저장하고
   템플릿은 지운다. 소속·회원 정보처럼 개인적인 내용은 비공개 저장소라서 적어도 된다.
2. 편집 스타일:
   - 기본으로 시작: `cp .claude/skills/reels-make/style.default.yaml .claude/instagram/style.yaml`
     (어두운 톤 정보형. 장면마다 얼굴 전체 / 위 그래픽 아래 얼굴 / 그래픽 전체가 2~5초마다 바뀌고,
     자막은 가운데 이음선에 흰 글씨 + 검은 테두리, 강조는 노랑.)
   - 따라 하고 싶은 릴스가 있으면: 사람이 그 릴스를 화면 녹화해 드라이브에 올리고 `/reels-style`을
     실행해 `style.yaml`을 만든다.
3. 말투 (선택): 사람이 쓴 글이나 캡션 3개를 받아 `.claude/instagram-agent/voice.template.md` 형식으로
   `.claude/instagram/voice.md`를 쓴다. 대본·캡션 스킬이 이 파일을 읽는다.
4. 끝나면 커밋하고 기본 브랜치에 반영한다 (8단계).

### 7단계 · 첫 영상

사람이 "올렸어"라고 하면 `.claude/skills/reels-make/SKILL.md`를 처음부터 읽고 그대로 따른다
(얼굴 영상은 `face` 모드). 큰 흐름:

1. 드라이브 폴더에서 새 파일을 찾아 curl로 내려받는다.
2. `stt.py`로 음성 인식 (1분에 약 70크레딧) → 같은 문장을 여러 번 말했으면 마지막 완성 테이크를 고른다.
3. `cut.py`로 공백과 실수를 자른다 (HDR은 자동으로 SDR 변환).
4. 인식 오류(이름, 숫자)를 고친 뒤 `plan.json`을 쓴다. 문장마다 그 말을 연기하는 그래픽(소품, 비교,
   숫자, 해부학 화면)을 고른다.
5. `build.py` → `render.sh` → 프레임을 뽑아 직접 확인 → 고칠 게 없으면 `output/날짜-주제.mp4`로 보내고,
   `/ig-caption`으로 캡션을 쓴다.
6. 사람에게 피드백을 받고, 사람이 정한 것은 `strategy.md`의 "사용자가 바꾼 결정"에 날짜와 함께 적는다.

### 8단계 · 설치 마무리

- 루트 `CLAUDE.md` 첫 줄("처음 설치할 때…")을 지운다.
- 클라우드 세션은 보통 세션 전용 브랜치(`claude/...`)에 커밋한다. 다음 세션은 저장소의
  **기본 브랜치**에서 시작하므로, 거기에 반영되지 않은 `strategy.md`, `style.yaml`, `log.md`는 다음
  세션에서 보이지 않는다. 그래서 작업이 끝날 때마다 기본 브랜치에 반영한다:
  기본 브랜치로 바로 push가 되면 그렇게 하고, 막히면 사람에게 물어본 뒤 PR을 만들어 "Merge"
  버튼을 눌러 달라고 한다. 또는 사람이 GitHub 저장소 → Settings → Default branch에서 작업 브랜치를
  기본 브랜치로 바꿔도 된다.
- 사람에게 할 수 있는 일을 짧게 알려준다:
  - "올렸어": 드라이브의 새 영상을 편집해서 MP4로 보내 줌
  - `/watch`: 영상이나 레퍼런스를 초 단위로 보고 설명
  - `/reels-style`: 레퍼런스 릴스의 편집 스타일을 규칙으로 저장
  - `/ig-reel`: 대본과 후킹, `/ig-caption`: 캡션과 해시태그, `/ig-plan`: 한 주 계획
  - `/adscore`: 올린 릴스의 인사이트 숫자로 점수와 약점
  - `/ig-viral`, `/spy`: 잘 되는 릴스와 경쟁 계정 조사 (Firecrawl 크레딧)

## 늘 지키는 편집 규칙

- 그래픽은 자막을 다시 쓰지 않는다. 말에 나오는 대상을 보여준다 (앱을 말하면 앱 화면, 시간을 말하면
  시계, 논문을 말하면 논문 카드, 근육을 말하면 해부학 앱 녹화).
- 화면 속 숫자, 시각, 논문 정보는 실제 값만 쓴다. 논문은 초록에서 저자·연도·숫자를 확인하고,
  확인하지 못한 것은 사람에게 확인 못 했다고 말한다.
- 자세 방향이 중요한 설명(골반 기울기, 근육이 당기는 방향)은 AI 영상을 쓰지 않는다. 해부학 앱 녹화를
  멈춘 장면 위에 선·화살표·원·이름표를 그린다.
- 건강 주제: 진단이나 "치료된다", "낫는다" 같은 표현을 쓰지 않는다. 일반 정보로 말하고 숫자에는 근거를 단다.
- 회원이 나오는 영상은 본인 동의를 받았는지 사람에게 확인한다.
- 효과음은 담백하게. 배경음악은 사람이 인스타그램에서 직접 고른다.
- 보내기 전에 프레임을 뽑아 직접 본다: 자막이 얼굴이나 그래픽을 가리는지, 그래픽이 말보다 먼저
  나오는지, 글이 이상하게 줄바꿈되는지.
- 레퍼런스에서는 후킹 구조와 주제만 가져오고, 남의 대본·영상·편집을 그대로 쓰지 않는다.
- 크레딧이 드는 작업은 먼저 비용을 말한다. 영상 공유가 끝나면 드라이브 공유를 다시 제한으로 돌려도
  된다고 알려준다.
- 작업이 끝나면 커밋하고 기본 브랜치에 반영한다. `work/`와 `output/`은 커밋하지 않는다.

## 문제가 생기면

| 증상 | 원인 | 조치 |
| --- | --- | --- |
| 내려받은 영상이 몇 KB짜리 HTML | 드라이브 파일이 공개 공유가 아님 | 폴더나 파일을 "링크가 있는 모든 사용자"로 공유 |
| curl이 403, 또는 000 | 네트워크 허용 목록에 주소가 없음 | 3단계 A-1에 그 주소 추가 후 새 세션 |
| `ELEVENLABS_API_KEY is not set` | 환경 변수 없음 | 3단계 A-2에 추가 후 새 세션 |
| `HTTP 401 ... missing_permissions` | 키에 Speech to Text 권한 없음 | ElevenLabs에서 키 권한 켜기 |
| 렌더가 1초 영상에 수십 초 | HDR 영상 | cut.py가 변환한 `work/cut.mp4`를 쓰는지 확인, 다음부터 HDR 끄고 촬영 |
| 렌더에서 글꼴이 기본 글꼴 | `cdn.jsdelivr.net` 막힘 | 허용 목록에 추가 |
| Drive 도구가 안 보임 | 커넥터 미연결 | claude.ai/customize/connectors에서 연결 후 새 세션 |
| `ffmpeg: No such filter: zscale` | ffmpeg가 zimg 없이 빌드됨 | Ubuntu의 `apt-get install ffmpeg`로 설치한 것을 쓴다 |
| 다음 세션에 전략·스타일 파일이 없음 | 기본 브랜치에 반영 안 됨 | 8단계대로 기본 브랜치에 반영 |

## 내 컴퓨터에서 쓸 때 (클라우드가 아닌 경우)

- 저장소를 내려받거나 이 폴더를 그대로 열고 Claude Code를 실행한 뒤 같은 말을 하면 된다.
- 네트워크 허용 목록과 설치 스크립트는 필요 없다. 대신 직접 설치한다:
  macOS `brew install ffmpeg node`, `pip3 install pyyaml pillow`. (Windows는 WSL의 Ubuntu에서 쓴다.)
- API 키는 셸 설정 파일에 `export ELEVENLABS_API_KEY=...`로 넣는다. 저장소 안 파일에는 넣지 않는다.
- 렌더가 브라우저를 못 찾으면 Chrome 실행 파일 경로를 `HYPERFRAMES_BROWSER_PATH`로 지정한다
  (macOS: `/Applications/Google Chrome.app/Contents/MacOS/Google Chrome`).
- `.mcp.json`의 playwright 항목은 클라우드 경로(`/opt/pw-browsers`)를 쓰니 내 컴퓨터에서는 지우거나
  `--executable-path` 두 줄을 뺀다.
