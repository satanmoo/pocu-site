# POCU 노트 배포 가이드

## 라이브 주소

**https://pocu-site.pages.dev**

## 구조

```
note-pocu (노트 원본, Obsidian 볼트)
  └─ pocu-site/content  ← git submodule로 연결
       └─ npx quartz build → public/ (정적 사이트)
            └─ GitHub Actions → Cloudflare Pages 업로드
```

- 이 레포(pocu-site)는 Quartz v4 클론. `upstream` = jackyzha0/quartz, 기본 브랜치 `v4`
- Cloudflare에는 빌드 산출물 `public/`만 올라감 (레포에는 커밋 안 됨)
- 배포 트리거는 **이 레포의 v4 push** (note-pocu push만으로는 배포 안 됨)

## 노트 반영 루틴

```sh
# 1. note-pocu에서 노트 작업 후 커밋·푸시 (평소대로)

# 2. pocu-site에서 submodule 포인터 갱신 + 푸시
cd ~/personal/pocu-site
git -C content pull origin main
git add content
git commit -m "update notes"
git push        # → GitHub Actions가 자동 빌드·배포
```

배포 상태 확인: https://github.com/satanmoo/pocu-site/actions

## 로컬 미리보기

```sh
cd ~/personal/pocu-site
npx quartz build --serve    # → http://localhost:8080
```

## Quartz 업데이트

```sh
cd ~/personal/pocu-site
npx quartz update           # upstream(jackyzha0/quartz) 변경분 머지
```

커스텀은 `quartz.config.ts` 한 파일뿐이라 충돌 나면 여기만 보면 됨.
주요 설정: `markdownLinkResolution: "shortest"` (전체 경로 링크 + 파일명만 쓴
임베드 둘 다 동작), `parseTags: false` (어셈블리 `#MAX` 표기 태그 오인 방지),
`ignorePatterns`에 `old_note`·`.obsidian`·`.claude`.

## 자격 증명

- GitHub secrets (pocu-site → Settings → Secrets → Actions):
  `CLOUDFLARE_API_TOKEN` (Pages Edit 권한), `CLOUDFLARE_ACCOUNT_ID`
- 토큰 재발급 시: Cloudflare 대시보드 → API Tokens → Roll → secret 값만 교체
