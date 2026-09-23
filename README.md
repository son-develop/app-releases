# app-releases

맥 앱 자동 업데이트(Tauri) **배포 저장소**입니다. 여러 앱이 함께 씁니다.

> ⚠️ **직접 커밋하지 마세요.** Release 는 각 앱 소스 저장소의 CI 가 자동으로 만듭니다.
> 이 저장소에는 배포 산출물(`latest.json`, `*.app.tar.gz`, `*.sig`)과
> 통합 재사용 워크플로우만 있습니다.

## 구조

- 앱마다 **고정 태그** Release 를 하나씩 가집니다 (예: `githand`).
- 업데이터 엔드포인트(앱별 고정):

  ```
  https://github.com/son-develop/app-releases/releases/download/<app-slug>/latest.json
  ```

  GitHub 의 `/releases/latest/` 별칭을 쓰지 않으므로 앱끼리 섞이지 않습니다.

## 통합 워크플로우

`.github/workflows/release-macos-tauri.yml` — private 소스에서 빌드·서명하고
이 저장소의 앱별 고정 태그 Release 에 게시하는 재사용 워크플로우입니다.

각 앱 소스 저장소에서 이렇게 호출합니다:

```yaml
jobs:
  release:
    uses: son-develop/app-releases/.github/workflows/release-macos-tauri.yml@main
    with:
      release-repo: son-develop/app-releases
      app-slug: githand
      product-name: GitHand
      # Cargo 워크스페이스에 src-tauri 가 들어 있으면 산출물이 뿌리에 쌓인다
      bundle-dir: target
      frontend-build: pnpm install --frozen-lockfile && pnpm build
    secrets: inherit
```

## 새 앱 추가

1. 그 앱 소스에 `release.yml`(caller) 추가 후 `app-slug` 지정
2. 앱 `tauri.conf.json` 의 `endpoints` 를 위 스킴으로
3. 시크릿(`TAURI_SIGNING_PRIVATE_KEY`, `TAURI_SIGNING_PRIVATE_KEY_PASSWORD`, `RELEASE_TOKEN`) 공유
