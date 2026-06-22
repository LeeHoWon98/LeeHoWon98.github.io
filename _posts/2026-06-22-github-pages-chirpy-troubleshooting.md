---
title: "GitHub Pages와 Chirpy로 기술 블로그 만들기: 오류 해결 기록"
date: 2026-06-22 22:00:00 +0900
categories: [Blog, GitHub]
tags: [github-pages, jekyll, chirpy, ruby, git, troubleshooting]
---

## 시작하며

기술 블로그를 만들기 위해 GitHub Pages와 Chirpy 테마를 사용했다.

처음에는 테마 파일을 GitHub에 올리면 바로 블로그가 만들어질 것이라고 생각했다. 하지만 실제로는 Ruby 버전 문제, Bundler 설치 문제, GitHub 계정 인증 문제, 원격 저장소 충돌 등 여러 오류를 겪었다.

이번 글에서는 GitHub Pages 기반 기술 블로그를 만들면서 발생한 오류와 해결 과정을 정리한다.

---

## 1. `bundle` 명령어를 찾을 수 없는 문제

처음 `bundle install` 명령어를 입력했을 때 다음 오류가 발생했다.

```txt
'bundle'은(는) 내부 또는 외부 명령,
실행할 수 있는 프로그램 또는 배치 파일이 아닙니다.
```

이 오류는 Bundler가 설치되지 않았거나 Ruby가 설치되지 않았을 때 발생한다.

### 해결 방법

Windows 환경에서는 RubyInstaller를 이용해 Ruby를 설치했다.

설치 후에는 다음 명령어로 Bundler를 설치했다.

```bash
gem install bundler
```

설치가 정상적으로 되었는지 확인하기 위해 다음 명령어를 사용했다.

```bash
ruby -v
gem -v
bundle -v
```

각 명령어에서 버전 정보가 출력되면 설치가 완료된 것이다.

---

## 2. Ruby 4.0 버전 호환성 문제

Bundler 설치 후 `bundle install`을 실행했지만 다음 오류가 발생했다.

```txt
Because jekyll-theme-chirpy >= 7.1.0 depends on Ruby ~> 3.1
...
So, because current Ruby version is = 4.0.5,
version solving has failed.
```

현재 설치된 Ruby 버전은 4.0.5였지만, Chirpy 테마는 Ruby 3.x 버전을 요구했다.

### 해결 방법

Ruby 4.0을 제거한 뒤 RubyInstaller에서 Ruby 3.3.x 버전을 설치했다.

설치할 때 다음 항목을 체크해야 한다.

```txt
Add Ruby executables to your PATH
```

설치가 끝난 뒤 새 CMD 창을 열고 다음 명령어를 실행했다.

```bash
ruby -v
```

Ruby 3.3.x 버전이 출력되는 것을 확인한 뒤 다시 의존성 설치를 진행했다.

```bash
bundle install
```

그 후 로컬 서버를 실행했다.

```bash
bundle exec jekyll serve
```

브라우저에서 다음 주소로 접속하면 로컬 환경에서 블로그를 확인할 수 있다.

```txt
http://localhost:4000
```

---

## 3. GitHub Pages에서 기본 화면만 나오는 문제

로컬에서는 Chirpy 테마가 정상적으로 보였지만, GitHub Pages 주소에서는 제목만 있는 기본 화면이 나타났다.

원인은 Chirpy 테마 파일이 GitHub 저장소에 제대로 업로드되지 않았기 때문이었다.

### 확인한 파일

Chirpy 테마를 사용하려면 저장소에 다음과 같은 파일과 폴더가 있어야 한다.

```txt
_config.yml
Gemfile
_posts
assets
_tabs
.github
```

특히 Chirpy 테마의 배포 설정 파일이 포함되어 있는지 확인해야 한다.

```txt
.github/workflows/pages-deploy.yml
```

로컬 폴더에서 Chirpy 화면이 정상적으로 보인다면, 테마 자체의 문제는 아니다. 이 경우 GitHub 저장소에 로컬 파일을 push하면 된다.

---

## 4. `git push` 시 upstream branch 오류

GitHub에 파일을 올리기 위해 `git push`를 실행했을 때 다음 오류가 발생했다.

```txt
fatal: The current branch main has no upstream branch.
```

이 오류는 로컬의 `main` 브랜치가 GitHub 원격 저장소의 `main` 브랜치와 아직 연결되지 않았다는 뜻이다.

### 해결 방법

다음 명령어를 실행해 원격 저장소와 연결했다.

```bash
git push --set-upstream origin main
```

성공하면 다음과 같은 문구가 출력된다.

```txt
branch 'main' set up to track 'origin/main'
```

이후부터는 아래 명령어만으로 push할 수 있다.

```bash
git push
```

---

## 5. 다른 GitHub 계정으로 로그인되어 권한이 거부된 문제

원격 저장소에 push하려고 했을 때 다음 오류가 발생했다.

```txt
remote: Permission to LeeHoWon98/LeeHoWon98.github.io.git denied to gimo298.
fatal: unable to access ...
The requested URL returned error: 403
```

현재 Git이 `LeeHoWon98` 계정이 아닌 다른 GitHub 계정으로 인증되어 있었기 때문에 발생한 문제였다.

### 해결 방법

Windows 자격 증명 관리자에서 기존 GitHub 로그인 정보를 삭제했다.

```txt
제어판
→ 사용자 계정
→ 자격 증명 관리자
→ Windows 자격 증명
→ github.com 관련 항목 삭제
```

그 후 다시 push를 실행하고, 블로그 저장소의 소유자인 GitHub 계정으로 로그인했다.

```bash
git push --set-upstream origin main
```

---

## 6. 원격 저장소와 로컬 저장소의 충돌 문제

GitHub에서 저장소를 만들 때 README 파일이 이미 생성되어 있었다. 반면 로컬 Chirpy 폴더에도 README 파일이 존재했다.

이 상태에서 push를 실행하자 다음 오류가 발생했다.

```txt
rejected main -> main (fetch first)
error: failed to push some refs
```

원격 저장소에 로컬에 없는 파일이 존재하기 때문에 먼저 원격 변경 사항을 가져와야 한다는 의미다.

### 해결 방법

다음 명령어를 실행해 원격 저장소의 내용을 가져왔다.

```bash
git pull origin main --allow-unrelated-histories
```

하지만 README 파일 충돌이 발생했다.

```txt
CONFLICT (add/add): Merge conflict in README.md
```

README는 블로그 실행에 직접적인 영향을 주지 않기 때문에 로컬 Chirpy 테마의 README를 유지하기로 했다.

```bash
git checkout --ours README.md
git add README.md
git commit -m "Merge remote repository"
git push --set-upstream origin main
```

마지막으로 다음과 같은 메시지가 출력되면서 업로드가 완료되었다.

```txt
main -> main
branch 'main' set up to track 'origin/main'
```

---

## 7. 앞으로 글을 작성하고 배포하는 방법

GitHub Actions 배포 설정이 완료된 이후에는 글을 작성할 때마다 Pages 설정 화면에서 직접 배포할 필요가 없다.

`_posts` 폴더에 Markdown 파일을 작성한 뒤 GitHub에 push하면 자동으로 배포된다.

예를 들어 다음과 같이 글 파일을 만든다.

```txt
_posts/2026-06-22-c-pointer.md
```

그 후 아래 명령어를 실행한다.

```bash
git add .
git commit -m "Add C pointer post"
git push
```

그러면 GitHub Actions가 자동으로 실행되고, 잠시 후 GitHub Pages 사이트에 새 글이 반영된다.

배포 상태는 GitHub 저장소의 `Actions` 탭에서 확인할 수 있다.

- 노란색 표시: 배포 진행 중
- 초록색 체크: 배포 성공
- 빨간색 X: 배포 실패

---

## 마무리

이번 블로그 제작 과정에서 단순히 테마를 설치하는 것보다 개발 환경과 Git의 동작 방식을 이해하는 과정이 더 중요하다는 것을 느꼈다.

특히 다음 내용을 확인할 수 있었다.

1. Jekyll 테마는 Ruby 버전 호환성이 중요하다.
2. 로컬에서 정상적으로 보인다고 해서 GitHub Pages에도 자동으로 반영되는 것은 아니다.
3. GitHub 계정 인증 상태에 따라 push 권한 오류가 발생할 수 있다.
4. 로컬 저장소와 원격 저장소의 파일이 다르면 병합 과정이 필요할 수 있다.
5. GitHub Actions를 설정하면 이후에는 push만으로 자동 배포할 수 있다.

앞으로는 C언어, 파이썬, 자료구조, 알고리즘, 프로젝트 경험을 이 블로그에 꾸준히 기록할 예정이다.