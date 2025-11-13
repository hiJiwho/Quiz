# JSON SVG 퀴즈 생성기 - 프로젝트 가이드

## 1\. 프로젝트 개요

이 프로젝트는 JSON(제이슨) 형식의 데이터를 기반으로 인터랙티브 퀴즈를 생성하고, 풀고, 공유하며, 인쇄할 수 있는 웹 애플리케이션입니다.

사용자가 직접 퀴즈 데이터를 JSON으로 작성하여 앱에 붙여넣으면, 앱이 이 데이터를 읽어 동적인 퀴즈 환경을 제공합니다. 특히 문제와 보기(선지)에 SVG(벡터 그래픽) 이미지를 포함시킬 수 있어, 텍스트뿐만 아니라 수학 도형이나 다이어그램 문제도 출제할 수 있습니다.

**주요 기능:**

  * **JSON 기반 퀴즈 생성:** 텍스트 편집기에서 퀴즈 데이터를 유연하게 관리합니다.
  * **인터랙티브 퀴즈 풀이:** 2분할 화면(문제/보기)으로 퀴즈를 풀고 즉시 피드백을 받습니다.
  * **팝업 퀴즈 모드:** 퀴즈를 새 팝업창에서 풀고, 결과는 메인창에서 확인할 수 있습니다.
  * **SVG 지원:** 문제와 5지선다 보기에 SVG 그래픽을 포함할 수 있습니다.
  * **타이머 기능:** 퀴즈 전체에 대한 **총 시간 제한**을 설정할 수 있습니다.
  * **A4 인쇄 최적화:** '문제지'와 '정답/해설지'를 2열 10pt 폰트로 깔끔하게 인쇄할 수 있습니다.
  * **공유 링크 생성 (데이터 숨김):** `IndexedDB`를 이용해 원본 JSON을 URL에서 숨긴 채 퀴즈를 공유할 수 있습니다.
  * **(신규) Iframe 모드:** `?iframe=` 쿼리 스트링으로 특정 UI만 표시할 수 있습니다. (예: 퀴즈 풀기 전용)

-----

## 2\. 연동 가이드: JSON 데이터 구조

퀴즈를 생성하려면 정해진 JSON 형식을 따라야 합니다. JSON 데이터는 1개의 **객체(`{}`)**입니다.

### 최상위 객체 (`{}`)의 속성

| 속성 | 타입 | 필수 여부 | 설명 |
| :--- | :--- | :--- | :--- |
| `title` | String | 선택 | 퀴즈의 제목입니다. (예: "기말고사 대비 퀴즈") |
| `totalTimeLimitSeconds` | Number | 선택 | 퀴즈 **전체**의 제한 시간(초)입니다. (예: 600 = 10분) |
| `questions` | Array | **필수** | 실제 문제들을 담는 배열입니다. (아래 '문제 객체' 참고) |

### `questions` 배열의 문제 객체 (`{}`) 속성

| 속성 | 타입 | 필수 여부 | 설명 |
| :--- | :--- | :--- | :--- |
| `question` | String | **필수** | 문제의 질문 텍스트입니다. |
| `options` | Array | **필수** | 5지선다 보기를 담는 배열입니다. (아래 '보기 객체' 참고) |
| `answerIndex` | Number | **필수** | 정답의 인덱스(순서)입니다. (0부터 시작: 1번 답은 0, 5번 답은 4) |
| `svgImage` | String | 선택 | 문제 `question` 텍스트 아래에 표시될 SVG 이미지 코드입니다. (SVG 태그 전체를 문자열로) |
| `explanation` | String | 선택 | 답 선택 시와 답안지에 표시될 해설 텍스트입니다. |

### 보기 객체 (`options` 배열의 요소)

`options` 배열은 1개 이상의 '보기 객체'를 포함해야 합니다.

| 속성 | 타입 | 필수 여부 | 설명 |
| :--- | :--- | :--- | :--- |
| `text` | String | **필수** | 보기에 표시될 텍스트입니다. (자동 줄바꿈 지원) |
| `svgImage` | String | 선택 | 보기 `text` 옆에 표시될 SVG 이미지 코드입니다. |



### JSON 데이터 예제 (3문제, 총 시간 60초)

```json
{
    "title": "기본 상식 퀴즈 (60초)",
    "totalTimeLimitSeconds": 60,
    "questions": [
        {
            "question": "다음 중 HTML(HyperText Markup Language)의 약자가 올바른 것은?",
            "options": [
                { "text": "Hyperlinks and Text Markup Language" },
                { "text": "Home Tool Markup Language" },
                { "text": "HyperText Markup Language" },
                { "text": "Hyper Tool Multi Language" },
                { "text": "HighText Machine Language" }
            ],
            "answerIndex": 2,
            "explanation": "HTML은 'HyperText Markup Language'의 약자입니다. 웹 페이지의 구조를 정의하는 마크업 언어입니다."
        },
        {
            "question": "다음은 텍스트가 매우 길어서 두 줄 이상으로 넘어가는 선지에 대한 테스트입니다. 이 텍스트는 자동으로 줄바꿈되어야 하며, 버튼의 높이도 그에 맞게 자동으로 늘어나야 합니다.",
            "options": [
                { "text": "짧은 텍스트" },
                { "text": "JQuery" },
                { "text": "이것은 매우 긴 텍스트입니다. 이 텍스트는 버튼 밖으로 넘치지 않고 자동으로 줄바꿈(wrapping)되어 여러 줄에 걸쳐 표시되어야 합니다. HTML 요소는 이것이 가능합니다." },
                { "text": "JavaScript" },
                { "text": "CSS (정답)" }
            ],
            "answerIndex": 4
        },
        {
            "question": "다음 보기 중 '둥근 사각형(Rounded Rectangle)'을 나타내는 SVG는 무엇인가요?",
            "svgImage": "<svg viewBox='0 0 100 60' xmlns='[http://www.w3.org/2000/svg](http://www.w3.org/2000/svg)'><rect x='10' y='10' width='80' height='40' rx='10' stroke='blue' stroke-width='3' fill='lightblue'/></svg>",
            "options": [
                { "text": "원", "svgImage": "<svg viewBox='0 0 40 40' xmlns='[http://www.w3.org/2000/svg](http://www.w3.org/2000/svg)'><circle cx='20' cy='20' r='18' stroke='black' stroke-width='2' fill='none'/></svg>" },
                { "text": "일반 사각형", "svgImage": "<svg viewBox='0 0 40 40' xmlns='[http://www.w3.org/2000/svg](http://www.w3.org/2000/svg)'><rect x='5' y='5' width='30' height='30' stroke='black' stroke-width='2' fill='none'/></svg>" },
                { "text": "삼각형", "svgImage": "<svg viewBox='0 0 40 40' xmlns='[http://www.w3.org/2000/svg](http://www.w3.org/2000/svg)'><polygon points='20,5 35,35 5,35' stroke='black' stroke-width='2' fill='none'/></svg>" },
                { "text": "둥근 사각형", "svgImage": "<svg viewBox='0 0 40 40' xmlns='[http://www.w3.org/2000/svg](http://www.w3.org/2000/svg)'><rect x='5' y='5' width='30' height='30' rx='8' stroke='black' stroke-width='2' fill='none'/></svg>" },
                { "text": "타원", "svgImage": "<svg viewBox='0 0 40 40' xmlns='[http://www.w3.org/2000/svg](http://www.w3.org/2000/svg)'><ellipse cx='20' cy='20' rx='18' ry='12' stroke='black' stroke-width='2' fill='none'/></svg>" }
            ],
            "answerIndex": 3,
            "explanation": "정답은 4번째 보기입니다. <rect> 태그의 'rx' 속성은 모서리의 둥근 정도를 지정합니다."
        }
    ]
}

-----

## 3\. 연동 가이드: 공유 링크 및 Iframe 모드

퀴즈 데이터(JSON)가 담긴 링크를 생성하여 다른 사용자에게 공유할 수 있습니다.

### 3.1 공유 링크 생성 및 작동 방식

1.  **공유 링크 생성 (방법 1: 앱 사용 - 권장)**
    1.  `https://hijiwho.github.io/Quiz/` 페이지에 접속합니다.
    2.  JSON 입력창에 위에서 만든 JSON 데이터를 붙여넣습니다.
    3.  `[문제 불러오기]` 버튼을 클릭합니다.
    4.  `[공유 링크 생성]` 버튼을 클릭하면 클립보드에 `https://hijiwho.github.io/Quiz/#q=[아주 긴 문자열]` 형태의 URL이 복사됩니다.

2.  **공유 링크 작동 (데이터 숨김 처리)**
    1.  다른 사용자가 위에서 생성된 `...#q=[...]` 링크로 접속합니다.
    2.  앱은 URL의 `#q=` 데이터를 브라우저의 `IndexedDB`에 **임시 저장**합니다.
    3.  즉시 URL에서 `#q=` 부분을 제거한 깔끔한 주소(예: `.../Quiz/`)로 **자동 리다이렉트**합니다.
    4.  리다이렉트된 페이지는 `IndexedDB`에 임시 저장된 퀴즈 데이터를 읽어와 '퀴즈 풀기 / 인쇄' 화면을 띄웁니다.
    5.  퀴즈 데이터를 읽어온 **즉시 `IndexedDB`에서 해당 데이터를 삭제**합니다. (새로고침 시 퀴즈 데이터가 사라지고 첫 화면이 뜸)

### 3.2 (고급) 프로그래밍으로 공유 링크 생성하기

다른 시스템에서 이 퀴즈 앱으로 링크를 동적으로 생성해야 한다면, 앱 내부의 `generateShareLink` 함수와 동일한 인코딩 과정을 거쳐야 합니다. (한글 등 유니코드 문자를 Base64로 안전하게 인코딩하기 위함)

**JavaScript (Node.js/브라우저) 예제:**

```javascript
// 1. 위에서 만든 퀴즈 JSON 데이터를 문자열(String)로 준비합니다.
const jsonData = `{"title":"테스트","questions":[{"question": "문제1", "options": [{"text":"보기1"}], "answerIndex": 0}]}`;

// 2. JSON 문자열을 UTF-8 바이트 배열로 인코딩합니다.
const utf8Bytes = new TextEncoder().encode(jsonData);

// 3. 바이트 배열을 바이너리 문자열로 변환합니다.
const binaryString = String.fromCodePoint(...utf8Bytes);

// 4. 바이너리 문자열을 Base64로 인코딩합니다.
// 5. Base64 문자열을 URL-Safe하게 변환합니다. (+ -> -, / -> _, = 제거)
const base64Data = btoa(binaryString)
    .replace(/\+/g, '-')
    .replace(/\//g, '_')
    .replace(/=+$/, '');

// 6. 최종 URL을 조립합니다.
const baseUrl = "[https://hijiwho.github.io/Quiz/](https://hijiwho.github.io/Quiz/)";
const shareUrl = `${baseUrl}#q=${base64Data}`;

// console.log(shareUrl);
// 출력: [https://hijiwho.github.io/Quiz/#q=](https://hijiwho.github.io/Quiz/#q=)[아주 긴 문자열]

### 3.3 (고급) Iframe 모드 (`?iframe=` 쿼리)

URL 쿼리 스트링 `?iframe=` 값을 조절하여 앱의 UI를 제어할 수 있습니다. 이 기능은 퀴즈 앱을 다른 웹사이트에 `<iframe>`으로 삽입할 때 유용합니다.

* `?iframe=1`: '퀴즈 풀기 시작' 버튼만 표시됩니다. (퀴즈 풀기 전용 모드)
    * (퀴즈가 시작되면 '처음으로' 버튼이 숨겨집니다.)
* `?iframe=2`: '문제지 인쇄'와 '정답/해설지 인쇄' 버튼만 표시됩니다. (인쇄 전용 모드)
* `?iframe=0` (또는 파라미터 없음): 모든 기능(퀴즈 풀기, 인쇄, JSON 편집) 버튼이 표시됩니다.

**링크 생성 예시 (퀴즈 풀기 전용 Iframe):**

`https://hijiwho.github.io/Quiz/?iframe=1#q=[아주 긴 문자열]`

이 링크를 `<iframe>`의 `src`로 사용하면, 해당 프레임에는 '퀴즈 풀기' 버튼만 보이게 됩니다.
