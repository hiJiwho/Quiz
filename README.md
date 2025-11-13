# JSON SVG 퀴즈 생성기 - 프로젝트 가이드

## 1\. 프로젝트 개요

이 프로젝트는 JSON(제이슨) 형식의 데이터를 기반으로 인터랙티브 퀴즈를 생성하고, 풀고, 공유하며, 인쇄할 수 있는 웹 애플리케이션입니다.

사용자가 직접 퀴즈 데이터를 JSON으로 작성하여 앱에 붙여넣으면, 앱이 이 데이터를 읽어 동적인 퀴즈 환경을 제공합니다. 특히 문제와 보기(선지)에 SVG(벡터 그래픽) 이미지를 포함시킬 수 있어, 텍스트뿐만 아니라 수학 도형이나 다이어그램 문제도 출제할 수 있습니다.

**주요 기능:**

* **JSON 기반 퀴즈 생성:** 텍스트 편집기에서 퀴즈 데이터를 유연하게 관리합니다.
* **인터랙티브 퀴즈 풀이:** 2분할 화면(문제/보기)으로 퀴즈를 풀고 즉시 피드백을 받습니다. (팝업 없음)
* **SVG 지원:** 문제와 5지선다 보기에 SVG 그래픽을 포함할 수 있습니다.
* **타이머 기능:** 퀴즈 전체에 대한 **총 시간 제한**을 설정할 수 있습니다.
* **A4 인쇄 최적화:** '문제지'와 '정답/해설지'를 2열 10pt 폰트로 깔끔하게 인쇄할 수 있습니다.
* **공유 링크 생성 (URL 숨김):** `IndexedDB`를 이용해 원본 JSON을 URL에서 숨긴 채 퀴즈를 공유할 수 있습니다.
* **?mode= 쿼리:** `?mode=` 쿼리 스트링으로 특정 UI만 표시하거나 공유 흐름을 제어할 수 있습니다.

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

<br>

### JSON 데이터 예제 (PDF 기반 5문제)

```json
{
    "title": "중학교 과학 심화 평가 (5, 6, 7단원 연계)",
    "totalTimeLimitSeconds": 300,
    "questions": [
        {
            "question": "볼록 렌즈를 이용하여 물체 A의 실상을 스크린에 맺었다. 물체 A를 렌즈의 초점거리(f)의 2배 지점(2f)에서 렌즈로부터 멀리 이동시켰을 때(여전히 실상이 맺히는 범위 내), 상의 변화로 옳은 것은?",
            "svgImage": "<svg viewBox='0 0 200 60' xmlns='[http://www.w3.org/2000/svg](http://www.w3.org/2000/svg)' class='w-full max-w-sm border rounded-md'><path d='M 10 30 H 190' stroke='gray' stroke-dasharray='4 2' /><path d='M 100 10 V 50' stroke='black' stroke-width='2' /><path d='M 100 10 Q 110 30 100 50' stroke='black' stroke-width='2' fill='none' /><path d='M 100 10 Q 90 30 100 50' stroke='black' stroke-width='2' fill='none' /><path d='M 50 25 V 30 H 48 L 50 25 L 52 30 H 50' stroke='black' stroke-width='1.5' fill='none' /><text x='50' y='22' text-anchor='middle' font-size='10'>A</text><text x='50' y='38' text-anchor='middle' font-size='10'>2f</text><text x='150' y='38' text-anchor='middle' font-size='10'>2f</text><text x='100' y='8' text-anchor='middle' font-size='10'>렌즈</text></svg>",
            "options": [
                { "text": "실상의 크기는 커지고, 렌즈로부터 멀어진다." },
                { "text": "실상의 크기는 작아지고, 렌즈에 가까워진다." },
                { "text": "실상의 크기는 변하지 않고, 렌즈에 가까워진다." },
                { "text": "실상의 크기는 작아지고, 렌즈로부터 멀어진다." },
                { "text": "실상의 크기는 커지고, 렌즈에 가까워진다." }
            ],
            "answerIndex": 1,
            "explanation": "볼록 렌즈에서 물체가 2f(초점거리의 2배)보다 멀어지면, 실상은 렌즈 반대편의 f와 2f 사이(렌즈에 가까워짐)에 맺히며 그 크기는 물체보다 작아집니다."
        },
        {
            "question": "질량 2kg인 물체가 직선 운동을 한다. 0초부터 4초까지의 속도-시간 그래프가 오른쪽과 같을 때, 0초부터 4초까지 물체에 작용한 알짜힘의 평균 크기(N)는?",
            "svgImage": "<svg viewBox='0 0 100 70' xmlns='[http://www.w3.org/2000/svg](http://www.w3.org/2000/svg)' class='w-full max-w-xs border rounded-md'><path d='M 10 60 H 90 M 10 60 V 10' stroke='black' stroke-width='1' /><text x='5' y='15' font-size='8'>속도(m/s)</text><text x='85' y='65' font-size='8'>시간(s)</text><path d='M 10 58 L 10 60' stroke='black' stroke-width='1' /><text x='10' y='68' text-anchor='middle' font-size='8'>0</text><path d='M 50 58 L 50 60' stroke='black' stroke-width='1' /><text x='50' y='68' text-anchor='middle' font-size='8'>2</text><path d='M 80 58 L 80 60' stroke='black' stroke-width='1' /><text x='80' y='68' text-anchor='middle' font-size='8'>4</text><path d='M 10 20 L 12 20' stroke='black' stroke-width='1' /><text x='7' y='23' text-anchor='end' font-size='8'>4</text><path d='M 10 20 H 50' stroke='gray' stroke-dasharray='2 2' /><path d='M 50 20 V 60' stroke='gray' stroke-dasharray='2 2' /><path d='M 80 20 V 60' stroke='gray' stroke-dasharray='2 2' /><path d='M 10 60 L 50 20 L 80 20' stroke='red' stroke-width='2' fill='none'/></svg>",
            "options": [
                { "text": "0 N" },
                { "text": "1 N" },
                { "text": "2 N" },
                { "text": "3 N" },
                { "text": "4 N" }
            ],
            "answerIndex": 2,
            "explanation": "평균 알짜힘 F = m * a (질량 * 평균 가속도). 평균 가속도 a = (나중 속도 - 처음 속도) / (총 시간) = (4 m/s - 0 m/s) / 4s = 1 m/s². 따라서 F = 2kg * 1 m/s² = 2 N 입니다."
        },
        {
            "question": "원소 A는 3주기 1족, 원소 B는 2주기 17족이다. 이 두 원소가 결합하여 생성된 화합물 AB에 대한 설명으로 옳지 않은 것은?",
            "options": [
                { "text": "화합물 AB는 이온 결합 물질이다." },
                { "text": "A는 양이온이 되기 쉽고, B는 음이온이 되기 쉽다." },
                { "text": "화합물 AB는 상온에서 단단한 고체 상태로 존재한다." },
                { "text": "화합물 AB는 수용액 상태에서 전기 전도성이 없다." },
                { "text": "A 원소는 전자를 1개 잃고, B 원소는 전자를 1개 얻어 안정한 전자 배치를 이룬다." }
            ],
            "answerIndex": 3,
            "explanation": "A는 3주기 1족(예: 나트륨, Na)이고 B는 2주기 17족(예: 플루오린, F)이므로, 금속과 비금속의 결합인 이온 결합 물질(예: NaF)을 형성합니다. 이온 결합 물질은 고체에서 전기가 통하지 않지만, 물에 녹아 이온화되면(수용액 상태) 전기 전도성이 있습니다."
        },
        {
            "question": "다음 두 화학 반응과 관련된 에너지 변화에 대한 심화 분석으로 옳은 것을 모두 고른 것은? (단, 모든 반응은 표준 상태에서 진행된다.) [반응 1] 2H₂(g) + O₂(g) → 2H₂O(l) + Q₁ (Q₁>0) [반응 2] CaCO₃(s) + Q₂ → CaO(s) + CO₂(g) (Q₂>0)",
            "options": [
                { "text": "ㄱ" },
                { "text": "ㄴ" },
                { "text": "ㄱ, ㄴ" },
                { "text": "ㄴ, ㄷ" },
                { "text": "ㄱ, ㄴ, ㄷ" }
            ],
            "answerIndex": 2,
            "explanation": "ㄱ: Q₁>0은 반응 시 열(Q₁)이 방출된다는 뜻이므로 발열 반응입니다. 발열 반응에서는 생성물의 에너지 총합이 반응물보다 낮아 더 안정합니다. (옳음) ㄴ: Q₂>0은 반응 시 열(Q₂)이 흡수된다는 뜻이므로 흡열 반응입니다. 흡열 반응은 외부에서 에너지를 공급받아야 일어납니다. (옳음) ㄷ: 반응 1의 역반응(물의 전기분해)은 에너지를 흡수하는 흡열 반응입니다. 반응 2의 역반응은 에너지를 방출하는 발열 반응입니다. (틀림)"
        },
        {
            "question": "해양판이 대륙판 아래로 섭입하는 수렴형 경계(섭입대) 근처에서, 일부 지역에서 지진파의 속도가 주변 맨틀보다 비정상적으로 빠르게 측정되는 현상이 관찰되었다. 이 현상으로부터 추론할 수 있는 지하 구조에 대한 설명으로 가장 적절한 것은?",
            "svgImage": "<svg viewBox='0 0 150 70' xmlns='[http://www.w3.org/2000/svg](http://www.w3.org/2000/svg)' class='w-full max-w-sm border rounded-md'><rect x='0' y='20' width='100' height='50' fill='#aaccff' /><text x='50' y='45' text-anchor='middle' font-size='10' fill='black'>해양판</text><path d='M 80 20 L 120 60 L 150 60 L 150 20 Z' fill='#d2b48c' /><text x='125' y='40' text-anchor='middle' font-size='10' fill='black'>대륙판</text><path d='M 100 20 L 115 35' stroke='black' stroke-width='2' /><text x='107' y='18' font-size='8'>섭입대</text><rect x='80' y='50' width='50' height='20' fill='#ffcc88' opacity='0.7' /><text x='105' y='62' font-size='8' fill='red'>고속도대(빠름)</text><text x='30' y='62' font-size='8'>주변 맨틀(느림)</text></svg>",
            "options": [
                { "text": "섭입된 판 주변은 주변 맨틀보다 밀도가 높고 온도가 낮아 지진파의 속도가 증가하는 고속도대가 형성된다." },
                { "text": "섭입된 판 주변은 마그마 활동으로 인해 온도가 높아져 지진파의 속도가 감소하는 저속도대가 형성된다." },
                { "text": "판이 침강하면서 상부 맨틀과의 마찰이 심해져 P파와 S파의 속도 차이가 급격히 줄어든다." },
                { "text": "섭입대 하부에서는 핵과의 경계가 가까워져 지진파가 액체 상태의 외핵을 통과하게 된다." },
                { "text": "지진파의 속도 증가는 판의 경계가 확장하는 발산형 경계에서 주로 나타나는 현상이다." }
            ],
            "answerIndex": 0,
            "explanation": "섭입하는 해양판은 차갑고 밀도가 높습니다. 반면 주변의 맨틀은 뜨겁고 상대적으로 밀도가 낮습니다. 지진파는 차갑고 밀도가 높은 물질을 더 빠르게 통과하므로, 섭입하는 판은 '고속도대(High-velocity zone)'를 형성합니다. (저속도대는 주로 맨틀이 부분적으로 녹아있는 연약권 등에서 나타납니다.)"
        }
    ]
}
````

-----

## 3\. 연동 가이드: URL 공유 및 모드 (`?mode=`)

이 앱은 `IndexedDB`와 URL `?mode=` 파라미터를 사용하여 URL에 데이터를 노출하지 않고 퀴즈를 공유하고 실행 모드를 제어합니다.

### 3.1 URL 공유 흐름 (데이터 숨김)

1.  **퀴즈 생성자가 `Qexport` (퀴즈 공유) 버튼 클릭**

    1.  현재 퀴즈 데이터(JSON)와 `mode=Qex` 상태가 `IndexedDB`에 저장됩니다.
    2.  페이지가 즉시 깔끔한 기본 URL(예: `/`)로 리다이렉트됩니다.
    3.  리다이렉트된 페이지는 `IndexedDB`에서 `Qex` 상태를 읽어와 **'퀴즈 공유 링크 생성' 화면**을 띄웁니다.
    4.  데이터를 읽은 후, `IndexedDB`의 상태는 **삭제**됩니다.
    5.  생성자가 이름과 메시지를 입력하고 '공유 링크 생성하기' 버튼을 누릅니다.
    6.  `/?mode=Import&name=...&msg=...#q=[데이터]` 형태의 **최종 공유 링크**가 생성됩니다.

2.  **퀴즈 수신자가 `Import` 링크 클릭**

    1.  수신자가 `/?mode=Import...#q=...` 링크로 접속합니다.
    2.  앱이 `mode` 파라미터와 `#q=` 데이터를 감지하고, 이 모든 상태를 `IndexedDB`에 저장합니다.
    3.  페이지가 즉시 깔끔한 기본 URL(예: `/`)로 **자동 리다이렉트**됩니다.
    4.  리다이렉트된 페이지는 `IndexedDB`에서 `Import` 상태를 읽어와, **'퀴즈 받기' 화면**(보낸 사람 이름, 메시지, [퀴즈 풀기], [인쇄하기] 버튼)을 띄웁니다.
    5.  데이터를 읽은 후, `IndexedDB`의 상태는 **삭제**됩니다.

3.  **수신자가 [퀴즈 풀기] 또는 [인쇄하기] 버튼 클릭**

    1.  [퀴즈 풀기]를 누르면, `mode=Quiz`와 퀴즈 데이터가 `IndexedDB`에 다시 저장됩니다.
    2.  [인쇄하기]를 누르면, `mode=Print`와 퀴즈 데이터가 `IndexedDB`에 다시 저장됩니다.
    3.  페이지가 다시 `/`로 리다이렉트됩니다.
    4.  리다이렉트된 페이지는 `IndexedDB`에서 `Quiz` 또는 `Print` 상태를 읽어와, 해당 기능(퀴즈 풀기 전용 또는 인쇄 전용) 화면을 띄웁니다.
    5.  데이터를 읽은 후, `IndexedDB`의 상태는 **삭제**됩니다.

### 3.2 URL `?mode=` 파라미터 설명

이 앱의 모든 공유 및 실행 흐름은 `IndexedDB`와 리다이렉션을 통해 제어되지만, `?mode=` 파라미터는 그 흐름을 시작하는 **진입점** 역할을 합니다.

  * `?mode=Import`: (공유 링크) 퀴즈 받기 화면으로 진입합니다. (반드시 `#q=` 데이터가 필요합니다)
  * `?mode=Qexport`: (내부용) 퀴즈 공유 링크 생성 화면으로 진입합니다. (DB에 데이터 필요)
  * `?mode=Aexport`: (내부용) 답지 열람/인쇄 화면으로 진입합니다. (DB에 데이터 필요)
  * `?mode=Quiz`: (내부용/Iframe용) 퀴즈 풀기 전용 모드로 진입합니다. (DB에 데이터 필요, '처음으로' 버튼 숨김)
  * `?mode=Print`: (내부용/Iframe용) 인쇄 전용 모드로 진입합니다. (DB에 데이터 필요, '퀴즈 풀기' 버튼 숨김)

### 3.3 (고급) 프로그래밍으로 공유 링크 생성하기

다른 시스템에서 이 퀴즈 앱으로 링크를 동적으로 생성해야 한다면, 다음 형식을 따르세요.

**링크 형식:**
`[기본 URL]/?mode=Import&name=[이름(Base64)]&msg=[메시지(Base64)]#q=[퀴즈JSON(Base64)]`

**JavaScript (Node.js/브라우저) 예제:**

```javascript
// 1. 퀴즈 JSON 데이터
const jsonData = `{"title":"테스트","questions":[{"question": "문제1", "options": [{"text":"보기1"}], "answerIndex": 0}]}`;
// 2. 보낼 사람 이름, 메시지
const senderName = "홍길동";
const senderMsg = "이거 풀어봐!";

// 3. 퀴즈 데이터 인코딩 (URL-Safe Base64)
const utf8Bytes = new TextEncoder().encode(jsonData);
const binaryString = String.fromCodePoint(...utf8Bytes);
const base64Data = btoa(binaryString)
    .replace(/\+/g, '-')
    .replace(/\//g, '_')
    .replace(/=+$/, '');

// 4. 이름, 메시지 인코딩 (Standard Base64)
const encodedName = btoa(encodeURIComponent(senderName));
const encodedMsg = btoa(encodeURIComponent(senderMsg));

// 5. 최종 URL 조립
const baseUrl = "[https://hijiwho.github.io/Quiz/](https://hijiwho.github.io/Quiz/)";
const shareUrl = `${baseUrl}?mode=Import&name=${encodedName}&msg=${encodedMsg}#q=${base64Data}`;

// console.log(shareUrl);
```
