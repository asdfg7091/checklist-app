# 업무 체크리스트 — 실시간 동기화 버전

스텝이 폰에서 체크리스트를 제출하면, 매니저 폰/PC 화면에 **몇 초 안에 자동으로** 뜨는 버전입니다.
Firebase(Firestore)라는 무료 실시간 데이터베이스를 씁니다. 신용카드 등록 없이 무료 한도로 충분합니다.

**배포된 링크**: https://asdfg7091.github.io/checklist-app/ (GitHub Pages, 이미 배포·설정 완료됨)

**Firebase 프로젝트**: `cafe-checklist-app` (이 앱 전용 프로젝트입니다)

> ⚠️ **다른 프로젝트와 절대 공유하지 마세요.** 이전에 같은 Firebase 프로젝트(`checklist-app-b6e3f`)를
> `schedule-app`(근무표) 프로젝트와 같이 쓰다가, 그쪽에서 보안 규칙을 자기 앱에 맞게 새로 배포하면서
> 이 앱 규칙까지 덮어써서 "Missing or insufficient permissions" 오류가 났던 적이 있어요.
> 그래서 이 앱만을 위한 별도 프로젝트(`cafe-checklist-app`)로 분리했습니다 — 앞으로 다른 Firebase
> 앱을 만들 때는 반드시 새 프로젝트를 만들어서 쓰세요.

아래 내용은 이미 완료되어 있어서 참고용입니다. 프로젝트를 새로 옮기거나 처음부터 만들 때만 따라 하면 돼요.

---

## 1단계. Firebase 프로젝트 만들기 (5분)

1. https://console.firebase.google.com 접속 → 구글 계정으로 로그인
2. **"프로젝트 추가"** 클릭 → 프로젝트 이름 입력 (예: `내카페체크리스트`) → 계속
3. Google Analytics는 "사용 안 함"으로 두고 **프로젝트 만들기**

## 2단계. Firestore Database 만들기

1. 왼쪽 메뉴에서 **Firestore Database** 클릭 → **데이터베이스 만들기**
2. 위치는 `asia-northeast3 (서울)` 선택
3. 보안 규칙은 **테스트 모드로 시작** 선택 (일단 열어두고, 3단계에서 규칙을 바꿔줄게요)

## 3단계. Firestore 보안 규칙 붙여넣기

1. Firestore Database 화면에서 **규칙(Rules)** 탭 클릭
2. 아래 내용을 전부 붙여넣고 **게시(Publish)**:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /targets/{doc} { allow read, write: if true; }
    match /items/{doc} { allow read, write: if true; }
    match /submissions/{doc} { allow read, write: if true; }
    match /reviews/{doc} { allow read, write: if true; }
    match /calendars/{doc} { allow read, write: if true; }
  }
}
```

> ⚠️ 이 규칙은 "이 앱이 쓰는 5개 문서함만" 링크를 아는 사람이면 누구나 읽고 쓸 수 있게 열어둔 것입니다.
> 로그인 시스템이 없는 대신 단순함을 택한 구조라, **링크를 아무 데도 공개로 올리지 마세요** (내부 스텝/매니저에게만 전달).
> 더 강한 보안이 필요하면 Firebase Authentication(이메일 로그인)을 추가하는 걸 추천드려요 — 필요하면 말씀해주세요.

## 4단계. 웹 앱 등록하고 설정 값 복사

1. Firebase 콘솔 좌측 상단 톱니바퀴 → **프로젝트 설정**
2. 아래로 스크롤 → **내 앱** → `</>` (웹) 아이콘 클릭
3. 앱 닉네임 입력 (예: `checklist-web`) → **앱 등록**
4. 화면에 나오는 `firebaseConfig` 객체를 복사 (아래 예시 형태)

```js
const firebaseConfig = {
  apiKey: "AIza...",
  authDomain: "내카페체크리스트.firebaseapp.com",
  projectId: "내카페체크리스트",
  storageBucket: "내카페체크리스트.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abcd1234"
};
```

## 5단계. `index.html`에 설정 값 입력

이 폴더의 `index.html` 파일을 열어서 상단 부분을 찾으세요:

```js
var FIREBASE_CONFIG = {
  apiKey: "",
  authDomain: "",
  projectId: "",
  storageBucket: "",
  messagingSenderId: "",
  appId: ""
};
```

4단계에서 복사한 값을 각 항목에 붙여넣고 저장하세요.

## 6단계. 다시 배포하기 (수정한 내용 반영하기)

이미 GitHub Pages로 배포되어 있어요: **https://asdfg7091.github.io/checklist-app/**
`index.html`을 수정한 뒤 아래 명령어로 올리면 1분 안에 그 링크에 반영돼요.

```bash
git add index.html
git commit -m "설정값 입력"
git push
```

깃허브 계정 로그인 없이 파일만 바꾸고 싶다면, github.com/asdfg7091/checklist-app 저장소 페이지에서
`index.html`을 열어 연필(수정) 아이콘으로 직접 편집 후 커밋해도 돼요.

### 대안: Netlify Drop / Firebase Hosting

GitHub 대신 다른 곳에 올리고 싶다면:
- **Netlify Drop**: https://app.netlify.com/drop 에 이 폴더를 드래그하면 계정 없이 즉시 배포돼요
- **Firebase Hosting** (선택, Node.js 필요):

Node.js가 설치되어 있다면:

```bash
npm install -g firebase-tools
firebase login
firebase init hosting
firebase deploy
```

`firebase init hosting` 진행 중 "public directory"를 이 폴더로 지정하면 됩니다.

---

## 완성 후 확인할 것

- 매니저 비밀번호는 코드 안에 `MANAGER_PW = "0001"`로 하드코딩되어 있어요. 바꾸고 싶으면 `index.html`에서 `0001`을 검색해 원하는 값으로 바꾸세요.
- 스텝이 제출 → 매니저 화면에 몇 초 안에 자동으로 뜨는지 확인하세요 (같은 링크를 다른 기기 2개에서 열어보면 됩니다).
- 사진은 캡처 시 자동으로 압축(최대 900px, JPEG 60%)되어 저장되므로 Firestore 무료 한도 안에서 넉넉하게 쓸 수 있어요.
- 매니저 화면 "보관소" 탭의 **전체 데이터 백업 다운로드**로 언제든 JSON 백업을 받을 수 있어요.

## 무료 한도로 충분한가요?

Firestore 무료 한도(Spark 플랜)는 하루 read 5만 건, write 2만 건, 저장용량 1GB예요.
카페 직원 몇 명이 하루 2번(오픈/마감) 체크리스트를 쓰는 규모라면 넉넉하게 무료로 운영됩니다.
