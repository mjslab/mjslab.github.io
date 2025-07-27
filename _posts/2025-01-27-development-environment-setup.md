---
title: "개발 환경 구축하기"
date: 2025-01-27 14:30:00 +0900
categories: [Development, Setup]
tags: [개발환경, VSCode, Git, 생산성]
author: mjs
description: "효율적인 개발을 위한 환경 구축 가이드입니다."
---

## 개발 환경의 중요성

좋은 개발 환경은 생산성과 코드 품질에 직접적인 영향을 미칩니다. 오늘은 제가 사용하는 개발 환경 구축 방법을 공유하겠습니다.

## 필수 도구들

### 1. 코드 에디터 - VS Code

**주요 확장 프로그램:**
- GitLens
- Prettier
- ESLint
- Python Extension Pack
- Live Server

```json
{
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "git.autofetch": true
}
```

### 2. 버전 관리 - Git

**기본 설정:**
```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
git config --global init.defaultBranch main
```

**유용한 alias:**
```bash
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.cm commit
```

### 3. 터미널 환경

**Oh My Zsh 설치:**
```bash
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

**추천 플러그인:**
- zsh-autosuggestions
- zsh-syntax-highlighting
- fzf

## 프로젝트별 환경 설정

### Python 프로젝트
```bash
# 가상환경 생성
python -m venv venv
source venv/bin/activate  # Linux/Mac
# venv\Scripts\activate   # Windows

# 의존성 관리
pip install -r requirements.txt
```

### Node.js 프로젝트
```bash
# 노드 버전 관리
nvm install node
nvm use node

# 패키지 설치
npm install
# 또는
yarn install
```

## 생산성 팁

### 1. 코드 스니펫 활용
자주 사용하는 코드 패턴을 스니펫으로 저장하여 시간을 절약하세요.

### 2. 단축키 마스터
- `Ctrl+Shift+P`: 명령 팔레트
- `Ctrl+P`: 파일 빠른 열기
- `Ctrl+Shift+F`: 전체 검색

### 3. 워크스페이스 설정
프로젝트별로 워크스페이스를 구성하여 설정을 분리하세요.

## 마무리

개발 환경은 한 번 설정해두면 장기간 사용하게 됩니다. 시간을 투자해서 자신에게 맞는 환경을 구축하는 것이 중요합니다.

다음 포스트에서는 더 구체적인 프로젝트 설정 방법을 다루겠습니다.


test2
---

*개발 환경에 대한 질문이나 추천 도구가 있으시면 댓글로 공유해주세요!*