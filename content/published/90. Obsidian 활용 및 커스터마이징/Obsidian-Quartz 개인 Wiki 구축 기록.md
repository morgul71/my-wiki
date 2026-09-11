---
title: Obsidian-Quartz 개인 Wiki 구축 기록
created: 2026-09-10
status: 진행중
tags:
  - obsidian
  - quartz
  - github
  - wiki
  - pkm
feature: 99. Attachment/d50902e7225e8c83d1b61de36cd93c9c_MD5.jpg
thumbnail: thumbnails/resized/96e8492a4c6cc615d4263ee6f13b2398_86cf658e.webp
---

# Obsidian-Quartz 개인 Wiki 구축 기록

## 1. 프로젝트 목적

Obsidian을 버리고 다른 Wiki로 옮기는 것이 목적이 아니다.

기본 원칙은 다음과 같다.

> **Obsidian은 나의 지식 원본이자 작업실이고,  
> Quartz는 정리된 지식을 웹에서 접근하기 위한 개인 Wiki이다.**

따라서 역할을 다음과 같이 분리한다.

- **Obsidian** → 지식의 원본 / 작성 / 연결 / 수정
- **Obsidian Git** → 기존 Vault 전체 백업 및 버전 관리
- **Quartz** → Obsidian Markdown을 웹 Wiki로 변환
- **GitHub `my-wiki`** → 웹 공개용 별도 저장소
- **GitHub Pages** → 실제 인터넷 Wiki 호스팅

최종 목표:

Obsidian에서 평소처럼 공부하고 메모하다가  
웹 Wiki에 공개하고 싶은 노트만 선택해서 발행한다.

---

# 2. 전체 시스템 구조

현재 구축한 구조:

Obsidian Vault
↓
`publish: true` 노트 선별
↓
PowerShell 동기화 스크립트
↓
Quartz `content/published`
↓
Quartz Build
↓
Git Commit
↓
GitHub Push
↓
GitHub Actions
↓
GitHub Pages
↓
개인 Web Wiki

개인 Wiki 주소:

`https://morgul71.github.io/my-wiki/`

---
# 3. 중요한 설계 원칙

## 기본값은 비공개

Vault 전체를 Quartz에 연결하지 않는다.

공개하고 싶은 노트에만 다음 Property를 넣는다.

```yaml
---
publish: true
---





Obsidian에서는 실제 파일에 다음처럼 저장될 수도 있다.

```
publish: "true"
```

현재 스크립트는 두 형식 모두 인식하도록 설정했다.

### 공개

```
publish: true
```

또는

```
publish: "true"
```

### 비공개

```
publish: false
```

또는 `publish` Property 자체가 없는 노트.

즉,

> **명시적으로 publish를 true로 지정한 노트만 Wiki로 보낸다.**


# 4. 현재 주요 경로

## Obsidian Vault

```
C:\skydancer\skydancer-jaey
```

테스트 노트:

```
C:\skydancer\skydancer-jaey\00. Inbox\Wiki 공개 테스트.md
```

## Quartz

```
C:\Quartz\quartz
```

Quartz 공개 콘텐츠:

```
C:\Quartz\quartz\content
```

자동 공개 노트 영역:

```
C:\Quartz\quartz\content\published
```


# 5. GitHub 구조

GitHub 사용자:

```
morgul71
```

웹 Wiki 전용 Repository:

```
my-wiki
```

Git remote 구조:

```
origin
→ https://github.com/morgul71/my-wiki.git

upstream
→ https://github.com/jackyzha0/quartz.git
```

역할:

- `origin` → 나의 Wiki
- `upstream` → Quartz 공식 프로젝트

현재 사용하는 Branch:

```
v5
```


# 6. Quartz 설치

현재 Quartz:

```
Quartz v5.0.0
```

Node:

```
v24.20.0
```

npm:

```
11.19.0
```

Quartz 설치 후 설정:

```
npm i
npx quartz create
```

설정 시 선택:

- Template → Obsidian
- Content → Empty Quartz
- Base URL → `morgul71.github.io/my-wiki`

설정 파일:

```
C:\Quartz\quartz\quartz.config.yaml
```


# 7. Quartz 로컬 테스트

NAS가 8080 포트를 사용하고 있어서 Quartz 테스트 포트를 8081로 변경했다.

실행:

```
npx quartz build --serve --port 8081
```

접속:

```
http://localhost:8081
```

정상적으로 Quartz 5 화면이 표시됨.


# 8. Quartz Theme 문제

초기 Build 과정에서 다음 오류 발생:

```
Theme "default" was installed but could not be loaded
```

해결:

```
npm install @quartz-themes/core @quartz-themes/default
```

확인:

```
npm ls @quartz-themes/default
```

설치 확인 후 Quartz Build 성공.


# 9. Excalidraw 경고

현재 Build 시 다음 경고가 발생한다.

```
Could not load plugin
"@quartz-community/obsidian-plugin-excalidraw"
to detect category. Skipping.
```

현재 Quartz Build와 Wiki 생성에는 문제가 없으므로 일단 보류.

나중에 필요하면:

- Excalidraw plugin 정상 설치  
    또는
- 사용하지 않으면 plugin 비활성화

검토 예정.


# 10. GitHub Pages 구축

GitHub Repository:

```
morgul71/my-wiki
```

GitHub Pages 설정:

```
Settings
→ Pages
→ Source
→ GitHub Actions
```

Quartz 전용 GitHub Actions 파일 생성:

```
.github/workflows/deploy.yml
```

처음에는 Pages 활성화 전에 Action이 실행되어 Deploy가 실패했다.

오류:

```
Failed to create deployment (status: 404)
Ensure GitHub Pages has been enabled
```

Pages에서 GitHub Actions를 활성화한 후:

```
Re-run all jobs
```

실행.

결과:

```
build  → Success
deploy → Success
```

최종 Wiki:

```
https://morgul71.github.io/my-wiki/
```

정상 접속 확인 완료.


# 11. publish 테스트

Obsidian에서 테스트 노트 생성:

```
Wiki 공개 테스트.md
```

Property:

```
publish: true
```

테스트 내용:

```
# Wiki 공개 테스트

이 노트는 Obsidian에서 작성하고 Quartz Wiki로 공개하는 테스트 노트입니다.

## 목표

Obsidian → Quartz → GitHub → GitHub Pages

이 과정이 정상적으로 작동하는지 확인한다.
```

처음에는 수동으로 Quartz에 복사해서 정상 표시되는지 확인했다.

이후 자동화 스크립트로 전환.


# 12. 자동 선별 동기화

스크립트:

```
C:\Quartz\quartz\sync-published.ps1
```

역할:

1. Obsidian Vault 전체에서 Markdown 검색
2. `.obsidian`, `.git`, `.trash` 제외
3. `publish: true`인 노트만 선택
4. Obsidian 폴더 구조 유지
5. Quartz의 `content/published`로 복사

예:

```
Obsidian

00. Inbox
└─ Wiki 공개 테스트.md
```

↓

```
Quartz

content
└─ published
   └─ 00. Inbox
      └─ Wiki 공개 테스트.md
```


# 13. 공개 해제 기능

현재 `sync-published.ps1`은 동기화할 때:

```
C:\Quartz\quartz\content\published
```

영역만 새로 만든다.

중요:

> **Obsidian Vault의 원본 파일은 삭제하거나 수정하지 않는다.**

테스트:

```
publish: true
```

→ Wiki 복사본 생성

이후:

```
publish: false
```

로 변경.

스크립트 실행:

```
& "C:\Quartz\quartz\sync-published.ps1"
```

결과:

```
Finished. Published notes: 0
```

파일 존재 여부 확인:

```
Test-Path "C:\Quartz\quartz\content\published\00. Inbox\Wiki 공개 테스트.md"
```

결과:

```
False
```

즉,

```
publish: true
→ Wiki 포함

publish: false
→ Wiki 제외
```

정상 작동 확인 완료.


# 14. Explorer 문제와 해결

Quartz가 노트를 정상적으로 Build했지만 Explorer에 새 노트가 처음에는 보이지 않았다.

Build 결과:

```
Found 2 input files from `content`
Parsed 2 Markdown files
Filtered out 0 files
```

실제 HTML도 정상 생성됨:

```
public
└─ published
   └─ 00.-inbox
      └─ wiki-공개-테스트.html
```

직접 URL 접근 시 정상 표시됨.

`contentIndex.json`에도 테스트 노트가 정상 등록된 것을 확인.

원인:

> 브라우저에 저장된 기존 Quartz Explorer 상태.

Chrome 시크릿 창에서 접속하자:

```
published
```

폴더가 정상적으로 나타남.

기존 Chrome의 localhost 사이트 데이터를 초기화한 후 정상 표시됨.

따라서 Quartz Build나 Explorer 설정 문제는 아니었음.


# 15. 원클릭 Wiki 배포

스크립트 생성:

```
C:\Quartz\quartz\publish-wiki.ps1
```

목적:

한 번 실행하면 다음 작업을 자동 수행.

```
1. sync-published.ps1
       ↓
2. Quartz Build
       ↓
3. git add content
       ↓
4. Git commit
       ↓
5. Git push origin v5
       ↓
6. GitHub Actions
       ↓
7. GitHub Pages 자동 배포
```

실행 명령:

```
& "C:\Quartz\quartz\publish-wiki.ps1"
```


# 16. 원클릭 배포 첫 테스트

처음에는 $LASTEXITCODE 검사 때문에 Sync가 성공했는데도 실패로 판단하는 문제가 발생했다.

기존:

```
if ($LASTEXITCODE -ne 0)
```

수정:

```
& $SyncScript
$SyncSucceeded = $?

if (-not $SyncSucceeded) {
    Write-Host "Sync failed."
    exit 1
}
```

수정 후 정상 작동.


# 17. 첫 실제 자동 배포 성공

Obsidian:

```
publish: true
```

설정 후:

```
& "C:\Quartz\quartz\publish-wiki.ps1"
```

실행.

성공 과정:

```
Step 1: Sync published notes
Published notes: 1

Step 2: Build Quartz
Found 2 input files
Build 성공

Step 3: Check Git changes
테스트 노트 발견

Step 4: Commit
Update published wiki 2026-09-10 18:04

Step 5: Push
v5 -> v5
```

최종:

```
Wiki publish completed successfully.
```

Git commit:

```
2fd1b60
```

GitHub Pages에서도 실제 노트가 정상적으로 열리는 것을 확인했다.


# 18. 현재 완성된 Workflow

앞으로 기본 사용 방식:

## ① Obsidian에서 평소처럼 작성

Definitions, BOOK, 사진관, 공부 노트 등을 기존 방식 그대로 작성.

## ② 공개할 가치가 있는 노트 선택

Properties:

```
publish: true
```

## ③ Wiki 배포

```
& "C:\Quartz\quartz\publish-wiki.ps1"
```

## ④ 자동 처리

```
Obsidian
↓
공개 노트 선별
↓
Quartz
↓
Build
↓
Git
↓
GitHub
↓
GitHub Actions
↓
GitHub Pages
```

## ⑤ 웹에서 접근

```
https://morgul71.github.io/my-wiki/
```


# 19. 현재 시스템에서 중요한 안전 원칙

### Obsidian Vault 전체를 Quartz에 직접 연결하지 않는다.

이유:

- Journal
- 개인 메모
- Readwise 자료
- 첨부파일
- Obsidian 설정
- 내부 작업 노트

등이 의도하지 않게 공개될 위험이 있기 때문이다.

따라서:

> **Whitelist 방식**

을 사용한다.

즉,

```
publish: true
```

인 노트만 공개한다.


# 20. 기존 Obsidian Git과의 관계

기존 Obsidian Git은 그대로 유지한다.

역할이 다르다.

### Obsidian Git

```
전체 Vault
→ 백업
→ 버전 관리
```

### Quartz GitHub

```
공개하기로 선택한 노트
→ 웹 Wiki
→ GitHub Pages
```

두 시스템을 섞지 않는다.


# 21. 현재 남아 있는 개선사항

다음 작업에서 검토할 것.

## 1. 배포 실행을 더 쉽게 만들기

현재:

```
& "C:\Quartz\quartz\publish-wiki.ps1"
```

향후:

- Windows 바로가기
- 배치 파일
- Obsidian 명령
- QuickAdd
- 버튼

등으로 한 번에 실행할 수 있게 만들기.

최종 목표:

> Obsidian에서 Publish → 클릭 한 번 → Wiki 갱신


## 2. Wiki 정보 구조 개선

현재:

```
published
└─ 00. Inbox
   └─ Wiki 공개 테스트
```

이 구조는 내부 관리에는 좋지만 Wiki 방문자에게는 좋지 않다.

향후 예:

```
Definitions
Books
Photography
Essays
English
AI
```

등 웹용 구조를 별도로 설계할 필요가 있다.

중요한 아이디어:

> **Obsidian의 내부 폴더 구조와 Wiki의 외부 정보 구조를 반드시 같게 만들 필요는 없다.**


## 3. 실제 노트 테스트

테스트 노트가 아닌 실제 노트 몇 개로 확인할 것.

우선 후보:

- `70. Definitions`
- `20. BOOK`
- 영어 공부 노트
- AI / Obsidian 관련 노트
- 꽃봄사진관 관련 정리 노트

확인할 것:

- Wikilink
- Backlink
- Graph View
- 한글 검색
- 이미지
- 첨부파일
- 내부 링크
- 태그


## 4. 링크된 노트 처리

중요한 향후 문제.

A 노트를 공개했는데 A가 B 노트를 링크하고 있고  
B는 `publish: false`라면 어떻게 할 것인가?

정책 결정 필요.

가능한 방법:

1. 링크만 끊기
2. 비공개 노트 링크 제거
3. 관련 노트도 함께 공개하도록 경고
4. 공개 전에 broken link 검사

향후 설계 필요.


## 5. 이미지/첨부파일 처리

현재는 Markdown만 선별한다.

실제 Wiki 운영을 위해서는 공개 노트가 사용하는:

- 사진
- 이미지
- PDF
- Excalidraw

등을 어떻게 안전하게 선별해서 같이 보낼지 결정해야 한다.

Vault의 `99. Attachment` 전체를 공개하면 안 된다.

따라서:

> **공개 노트에서 실제 참조하는 첨부파일만 선별 복사**

하는 방향이 가장 안전하다.


## 6. Excalidraw 경고 정리

현재 Build마다:

```
Could not load plugin
@quartz-community/obsidian-plugin-excalidraw
```

경고가 발생.

실제 Excalidraw 공개가 필요한지 판단 후:

- 정상 설치  
    또는
- plugin 비활성화

예정.


## 7. publish-wiki.ps1 정리

현재 첫 성공 테스트에서 Sync 결과가 두 번 출력되는 현상이 있었다.

실제 배포에는 문제 없지만 다음 작업 때 스크립트를 점검하여 출력 중복을 제거한다.


# 22. 다음 작업 시작점

다음 작업은 여기서 시작한다.

### 우선순위 1

`publish-wiki.ps1`을 더블클릭 또는 Obsidian 내부에서 실행할 수 있도록 만들기.

### 우선순위 2

웹 Wiki에서:

```
published
00. Inbox
```

같은 내부 폴더 이름을 그대로 보여주지 않고,

```
Definitions
Books
Photography


Essays

등 읽기 좋은 Wiki 구조 설계.

### 우선순위 3

실제 `70. Definitions` 노트 2~3개를 `publish: true`로 지정하여:

- 링크
- Backlink
- Graph
- 한글 검색

테스트.

### 우선순위 4

이미지 및 첨부파일의 안전한 자동 선별 복사 설계.


# 23. 현재 상태 요약

2026-09-10 현재:

- Quartz 5 설치
- Node/npm 설치 확인
- Quartz 로컬 실행
- GitHub `my-wiki` Repository 생성
- Quartz origin/upstream 분리
- GitHub Pages 설정
- GitHub Actions 자동 배포
- 실제 Web Wiki 생성
- `publish: true` 노트 선별
- Obsidian 폴더 구조 유지 복사
- `publish: false` 공개 해제
- Explorer 문제 해결
- Quartz 자동 Build
- Git 자동 Commit
- GitHub 자동 Push
- GitHub Pages 실제 배포 확인
- Obsidian 내부 원클릭 Publish
- Wiki용 정보 구조 설계
- 실제 Definitions 노트 테스트
- Wikilink/Backlink 검증
- 이미지/첨부파일 자동 처리
- Excalidraw 경고 정리


# 24. 한 문장으로 정리

> **Obsidian을 개인 지식의 원본으로 유지하면서 `publish: true`로 선택한 노트만 Quartz를 통해 GitHub Pages 개인 Wiki에 안전하게 자동 출판하는 시스템의 기본 파이프라인을 완성했다.**



![[99. Attachment/d50902e7225e8c83d1b61de36cd93c9c_MD5.jpg]]

