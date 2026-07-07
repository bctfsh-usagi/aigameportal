# 배포 가이드 (Claude Code에게 이 파일을 보여주고 진행을 맡기면 됩니다)

## 프로젝트 구조
```
portal/
├── index.html              # 게임 포털 메인 (게임 목록 허브)
├── DEPLOY.md               # 이 문서
└── games/
    └── geuljogak/          # 글조각 (한글 자모 데일리 퍼즐, PWA)
        ├── index.html
        ├── manifest.webmanifest
        ├── sw.js
        ├── icon-192.png
        └── icon-512.png
```

## Claude Code에게 요청할 작업 순서

### 1. GitHub 저장소 생성 및 푸시
```
이 폴더를 git 저장소로 초기화하고, GitHub에 새 공개 저장소를 만들어 푸시해줘.
저장소 이름은 [원하는 이름]으로 해줘.
```
- `gh` CLI가 설치·로그인되어 있으면 Claude Code가 저장소 생성까지 처리 가능
- 없으면 `gh auth login` 부터 안내받기

### 2. GitHub Pages 활성화
```
이 저장소에 GitHub Pages를 설정해줘. main 브랜치의 루트에서 배포하도록.
```
- `gh api` 명령으로 Pages 설정 가능

### 3. 커스텀 도메인 연결
```
내 도메인 [example.com]을 이 GitHub Pages에 연결해줘.
CNAME 파일을 만들고, 내가 도메인 등록업체에서 설정해야 할 DNS 레코드를 알려줘.
```
- Claude Code가 하는 일: 저장소 루트에 `CNAME` 파일 생성 (내용: 도메인 한 줄)
- **사용자가 직접 해야 하는 일**: 도메인 등록업체(가비아, Cloudflare 등) 관리 페이지에서 DNS 레코드 추가
  - 루트 도메인(example.com): A 레코드 4개
    - 185.199.108.153
    - 185.199.109.153
    - 185.199.110.153
    - 185.199.111.153
  - www 서브도메인: CNAME 레코드 → `[깃허브아이디].github.io`
  - ※ Cloudflare를 쓰고 API 토큰을 주면 Claude Code가 DNS까지 자동화 가능
- DNS 반영 후 저장소 Settings → Pages 에서 "Enforce HTTPS" 켜기 (반영까지 최대 24시간)

### 4. 배포 확인 체크리스트
- [ ] https://[도메인]/ 에서 포털이 열리는가
- [ ] https://[도메인]/games/geuljogak/ 에서 게임이 열리는가
- [ ] 모바일에서 홈 화면 추가 → 전체화면 앱으로 실행되는가
- [ ] HTTPS 자물쇠가 뜨는가 (PWA·광고 모두 HTTPS 필수)

## 새 게임 추가 방법
1. `games/새게임이름/` 폴더에 게임 파일 추가 (index.html 필수, thumb.png 권장 — 640×400 대표 이미지)
2. `games.json`의 `games` 배열에 항목 하나 추가:
   ```json
   {
     "id": "새게임이름",
     "title": "게임 제목",
     "desc": "카드에 보일 한두 문장 설명",
     "tags": ["태그1", "태그2"],
     "path": "games/새게임이름/",
     "thumb": "games/새게임이름/thumb.png",
     "type": "arcade",
     "added": "2026-07-07",
     "time": "평균 3분"
   }
   ```
   - `type`: `"daily"`면 DAILY 배지, 그 외에는 `added` 기준 14일간 NEW 배지 자동
   - 카드·게임 개수·히어로의 추천 게임(가장 최근 `added`)이 전부 자동 갱신됨
3. 커밋 & 푸시하면 자동 배포

## 광고 연동 시 참고
- 글조각의 `index.html` 상단 주석에 AdSense H5 게임 광고 연동 지점이 표시되어 있음
- 하단 배너(.banner)는 애드핏/AdSense 배너 코드로 교체
- AdSense 심사를 위해 개인정보처리방침 페이지(privacy.html) 추가 권장
