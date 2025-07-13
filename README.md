# 작품 아카이브 시스템

작품을 체계적으로 관리하고 웹 페이지로 자동 생성하는 시스템입니다.

## 📁 프로젝트 구조

```
templateForLs/
├── index.html              # 메인 갤러리 페이지
├── generateContent.js      # 콘텐츠 생성 스크립트
├── content.json           # 생성되는 작품 데이터 (자동생성)
├── README.md              # 이 파일
└── artworks/              # 작품 폴더
    └── 1_작품명/           # 개별 작품 폴더
        ├── 작품명.html     # 작품 상세 페이지 (자동생성)
        ├── thumnail.png    # 썸네일 이미지
        ├── description.txt # 작품 설명
        ├── 1_이미지.jpg    # 순번_이름 형식의 미디어
        ├── 2_비디오.mp4    # 순번_이름 형식의 미디어
        └── link_1_출처.txt # 링크 파일
```

## 🚀 시작하기

0.  
액션에 넣어야합니당앙 
노드 js로 하시면 됩니다! 

name: Generate Content

on:
  push:
    branches: [ main ]
  workflow_dispatch:

permissions:
  contents: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '20'
          
      - name: Install dependencies
        run: npm ci || npm install
      
      - name: Generate content
        run: node generateContent.js
      
      - name: Commit and push changes
        run: |
          git config —global user.name 'GitHub Actions'
          git config —global user.email 'actions@github.com'
          git add .
          git commit -m "자동 콘텐츠 생성" || echo "No changes to commit"
          git push
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }} 


### 1. 새 작품 추가하기

1. `artworks/` 폴더에 새 폴더 생성
   - 폴더명 형식: `순번_작품명` (예: `2_새로운그림`)

2. 작품 폴더에 필요한 파일들 추가:

#### 필수 파일
- **썸네일**: `thumbnail.png` 또는 `thumnail.png`
- **설명**: `description.txt`

#### 선택 파일
- **미디어 파일**: `순번_이름.확장자` 형식
  - 예: `1_메인이미지.jpg`, `2_과정영상.mp4`
  - 지원 확장자: `.jpg`, `.jpeg`, `.png`, `.gif`, `.webp`, `.mp4`, `.mov`, `.webm`, `.avi`, `.mkv`, `.bmp`, `.tiff`, `.svg`

- **링크 파일**: `link_순번_이름.txt` 형식
  - 예: `link_1_참고자료.txt`
  - 파일 내용에는 URL만 입력

### 2. 콘텐츠 생성

터미널에서 다음 명령어 실행:

```bash
node generateContent.js
```

### 3. 결과 확인

- `index.html`: 메인 갤러리 페이지
- `artworks/작품폴더/작품명.html`: 각 작품의 상세 페이지

## 📋 파일 명명 규칙

### 작품 폴더
```
순번_작품명
```
- 예: `1_오늘그림`, `2_추상작업`, `3_디지털아트`

### 미디어 파일
```
순번_파일명.확장자
```
- 예: `1_메인작품.jpg`, `2_상세컷.png`, `3_제작과정.mp4`

### 링크 파일
```
link_순번_링크제목.txt
```
- 예: `link_1_인스타그램.txt`, `link_2_참고자료.txt`
- 파일 내용: URL 주소만 입력

### 썸네일 파일
```
thumbnail.png 또는 thumnail.png
```

### 설명 파일
```
description.txt
```

## 🎨 작품 폴더 예시

```
artworks/1_오늘그림/
├── 오늘그림.html          # 자동 생성됨
├── thumnail.png           # 썸네일
├── description.txt        # 작품 설명
├── 1_sampleMovie.mov      # 첫 번째 미디어
├── 2_sampleImg.png        # 두 번째 미디어
└── link_1_출처자료.txt    # 관련 링크
```

**description.txt 내용 예시:**
```
이것은 첫 번째 작품 설명입니다.
여러 줄로 작성할 수 있으며, 
작가는 자유롭게 작품에 대한 설명을 추가할 수 있습니다.

작품 제작일: 2023년 5월 15일
재료: 아크릴, 캔버스
```

**link_1_출처자료.txt 내용 예시:**
```
https://www.instagram.com/movingweb/
```

## 🔧 생성되는 파일들

### content.json
모든 작품 정보가 JSON 형태로 저장됩니다:
```json
[
  {
    "title": "오늘그림",
    "folder": "1_오늘그림",
    "thumbnail": "thumnail.png",
    "media": ["1_sampleMovie.mov", "2_sampleImg.png"],
    "text": "작품 설명 내용...",
    "links": [
      {
        "title": "출처자료",
        "url": "https://www.instagram.com/movingweb/"
      }
    ]
  }
]
```

### 개별 작품 HTML 페이지
각 작품 폴더에 `작품명.html` 파일이 생성되어 다음을 포함합니다:
- 썸네일 이미지
- 작품 설명
- 모든 미디어 파일 (이미지/비디오)
- 관련 링크들

## 🌐 웹페이지 실행

### 로컬에서 보기
1. `index.html` 파일을 브라우저로 열기
2. 또는 로컬 서버 실행:
   ```bash
   python3 -m http.server 8000
   ```
   그 후 브라우저에서 `http://localhost:8000` 접속

### 기능
- **메인 페이지**: 모든 작품이 카드 형태로 표시
- **작품 상세**: 썸네일 클릭 시 해당 작품의 상세 페이지로 이동
- **반응형 디자인**: 다양한 화면 크기에 대응

## 🎯 주의사항

1. **파일명**: 특수문자 사용 시 웹에서 제대로 표시되지 않을 수 있음
2. **이미지 크기**: 썸네일은 적절한 크기로 최적화 권장
3. **비디오 포맷**: `.mp4` 형식이 가장 호환성이 좋음 (`.mov`는 일부 브라우저에서 지원 안됨)
4. **순번**: 미디어 파일의 순번은 표시 순서를 결정함

## 📝 업데이트 방법

1. 새 작품 추가 후
2. `node generateContent.js` 실행
3. 자동으로 `content.json`과 개별 HTML 파일들이 업데이트됨

---

**개발자**: [작가명]  
**업데이트**: 2024년
