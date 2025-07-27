---
title: Jekyll Chirpy 테마 빌드 오류 해결 - Can't find stylesheet to import 완벽 가이드
date: 2025-01-27 23:30:00 +0900
categories: [Development, Jekyll]
tags: [Jekyll, Chirpy, GitHub Pages, SCSS, Bootstrap, 오류해결]
author: <author_id>
---

Jekyll Chirpy 테마를 사용하여 GitHub Pages 블로그를 만들다가 다음과 같은 오류를 만난 적이 있나요?

```bash
Error: Can't find stylesheet to import.
  ╷
1 │ @use 'vendors/bootstrap';
  │ ^^^^^^^^^^^^^^^^^^^^^^^^
  ╵
  main.bundle.scss 1:1
```

이 글에서는 이 오류의 원인과 해결 방법을 단계별로 설명하겠습니다.

## 문제 상황

GitHub Actions에서 Jekyll 사이트를 빌드할 때 다음과 같은 오류가 발생합니다:

```bash
Conversion error: Jekyll::Converters::Scss encountered an error while converting 'assets/css/jekyll-theme-chirpy.scss':
Can't find stylesheet to import.
```

특히 로컬에서는 정상 작동하지만 CI 환경에서만 실패하는 경우가 많습니다.

## 원인 분석

### 근본 원인
이 오류는 **Chirpy 테마의 초기화 과정이 완료되지 않았기 때문**에 발생합니다. Chirpy 테마는 단순히 Git clone으로는 완전히 설치되지 않으며, 추가적인 빌드 과정이 필요합니다.

### 구체적인 문제점
1. `_sass/vendors/_bootstrap.scss` 파일이 비어있거나 존재하지 않음
2. `assets/js/dist/` 폴더의 JavaScript 파일들이 생성되지 않음
3. 필요한 정적 에셋들이 초기화되지 않음

## 해결 방법

### 1단계: Chirpy 초기화 명령 실행

가장 중요한 해결책은 Chirpy 테마의 공식 초기화 명령을 실행하는 것입니다:

```bash
bash tools/init.sh
```

이 명령어는 다음 작업들을 수행합니다:

#### CSS 파일 빌드
```bash
> jekyll-theme-chirpy@7.3.1 build:css
> node purgecss.js
```
- Bootstrap CSS를 PurgeCSS로 최적화하여 `_sass/vendors/_bootstrap.scss`에 생성
- 불필요한 CSS 제거로 파일 크기 최적화

#### JavaScript 파일 빌드
```bash
> jekyll-theme-chirpy@7.3.1 build:js
> rollup -c --bundleConfigAsCjs --environment BUILD:production
```
- `assets/js/dist/` 폴더에 필요한 모든 JavaScript 파일 생성
- `app.min.js`, `commons.min.js`, `theme.min.js` 등

#### 서브모듈 초기화
```bash
Submodule 'assets/lib' (https://github.com/cotes2020/chirpy-static-assets.git) registered
```

### 2단계: 필수 환경 확인

#### Node.js 버전
최신 버전의 Node.js가 필요합니다. 구버전 사용 시 빌드가 실패할 수 있습니다:

```bash
# Ubuntu/Debian에서 최신 Node.js 설치
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt-get install -y nodejs
```

#### Ruby 버전
Ruby 3.0 이상 권장:

```bash
ruby --version
# ruby 3.0.0 이상이어야 함
```

### 3단계: Windows 환경 대응

Windows 환경에서는 추가 조치가 필요할 수 있습니다:

#### WSL2 사용 방법
```bash
# 개행 문자 변환 후 실행
sed -i 's/\r//' tools/init
bash tools/init
```

#### 수동 초기화 (WSL2를 사용할 수 없는 경우)
```bash
# 불필요한 파일들 삭제
rm -f Gemfile.lock
rm -f .travis.yml
rm -rf _posts
rm -rf docs

# 워크플로우 파일 이름 변경
mv .github/workflows/pages-deploy.yml.hook .github/workflows/pages-deploy.yml

# 플랫폼 추가
bundle lock --add-platform x86_64-linux
```

### 4단계: GitHub Actions 설정

Repository Settings에서 GitHub Pages 설정:

1. **Settings** → **Pages** 이동
2. **Build and deployment** 섹션에서 **"GitHub Actions"** 선택
3. Jekyll Configure 버튼 클릭하여 워크플로우 생성

## 검증 방법

### 로컬 테스트
```bash
# 의존성 설치
bundle install

# Jekyll 빌드 테스트
bundle exec jekyll build

# 로컬 서버 실행
bundle exec jekyll serve
```

성공 시 `http://127.0.0.1:4000`에서 사이트 확인 가능합니다.

### 파일 구조 확인
초기화 후 다음 파일들이 생성되었는지 확인:

```
_sass/vendors/_bootstrap.scss  # Bootstrap CSS 파일
assets/js/dist/               # JavaScript 빌드 파일들
├── app.min.js
├── commons.min.js
├── theme.min.js
└── ...
assets/lib/                   # 정적 에셋 서브모듈
```

## 추가 문제 해결

### JavaScript 파일이 생성되지 않는 경우
```bash
npm install
npm run build
```

### Git 충돌 발생 시
```bash
# 원격 변경사항 가져오기
git pull --rebase

# 충돌 해결 후
git add .
git rebase --continue
```

### 권한 문제 (Linux/macOS)
```bash
chmod +x tools/init.sh
```

## 핵심 포인트

### 왜 로컬에서는 작동했을까?
- 로컬에서는 이전에 어떤 과정에서 일부 파일들이 생성되어 있었을 가능성
- CI 환경은 항상 깨끗한 상태에서 시작하므로 초기화되지 않은 파일로 인해 빌드 실패

### Chirpy 테마의 특징
Chirpy는 **두 단계 설치 과정**을 가집니다:
1. **Git clone**: 소스 코드 다운로드
2. **초기화**: 빌드 및 최적화 (`bash tools/init.sh`)

이 두 번째 단계를 건너뛰면 빌드 오류가 발생합니다.

## 실제 해결 사례

최근 이 문제를 직접 경험했는데, 다음과 같은 과정으로 해결했습니다:

1. **문제 발견**: GitHub Actions에서 계속 빌드 실패
2. **원인 파악**: `_sass/vendors/_bootstrap.scss` 파일이 비어있음을 확인
3. **해결**: `bash tools/init.sh` 실행으로 모든 파일 정상 생성
4. **검증**: 로컬 빌드 성공 후 GitHub에 push하여 CI 빌드도 성공

특히 Git rebase 과정에서 설정 파일 충돌이 발생했지만, 기존 사용자 설정을 유지하면서 해결할 수 있었습니다.

## 결론

Jekyll Chirpy 테마의 "Can't find stylesheet to import" 오류는 대부분 **초기화 과정 누락**으로 인해 발생합니다. `bash tools/init.sh` 명령 한 번으로 대부분의 문제가 해결되므로, Chirpy 테마 사용 시 이 단계를 잊지 마세요.

이 방법으로 해결되지 않는다면 Node.js/Ruby 버전 호환성이나 환경별 설정을 다시 확인해보시기 바랍니다.

## 참고 자료

- [Chirpy 공식 문서](https://chirpy.cotes.page/)
- [Jekyll 공식 문서](https://jekyllrb.com/docs/)
- [GitHub Pages 설정 가이드](https://docs.github.com/en/pages)

---

*이 글이 도움이 되셨나요? 댓글로 알려주세요! 추가 질문이나 다른 오류 사례도 언제든 환영합니다.* 🚀
