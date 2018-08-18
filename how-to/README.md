## Step 1. 적절한 의미 제공

### 대화 상자 호출 버튼(링크)에 연관 의미 제공

`aria-haspopup` 사용 (see. [WAI-ARIA 1.1](https://www.w3.org/TR/wai-aria-1.1/#aria-haspopup) )

요소(element)에 의해 호출 될 수 있는 메뉴나 대화상자 같은 팝업 요소의 가용성과 형태를 나타내는데 사용되는 aria
속성(property).

WAI-ARIA 1.0에서는 `aria-haspopup`은 [true|false] 값만으로 정의 되어있었으나, WAI-ARIA 1.1에서
[false|true|menu|listbox|tree|grid|dialog]로 변경 되었습니다.

현재 시점에서 대다수의 보조기술들이 WAI-ARIA 1.1 명세의 각 값들이 구현되지 않은 것으로 보고되어 있기 때문에 (대부분의
보조기술들이 1.0 명세에서 의도되었던 팝업 컨텍스트 메뉴 혹은 하위 메뉴로 사용), `aria-haspopup="dialog"`를
단독으로 사용하는 것은 인식의 혼동을 가져올 수 있기 때문에 `title` 속성과 병행하는 것을 추천 합니다.

As-Is

```html
<a href="#voc-dialog" class="util-menu-item btn-voc">
  <span class="far fa-envelope" aria-hidden="true"></span> 고객문의
</a>
```

To-Be

```html
<a href="#voc-dialog" class="util-menu-item btn-voc" aria-haspopup="dialog" title="대화상자">
  <span class="far fa-envelope" aria-hidden="true"></span> 고객문의
</a>
```

### 대화 상자에 "대화 상자"라는 의미 부여

`role`을 이용하여 "dialog" 의미 부여.

> 대화 상자는 사용자가 정보를 입력하거나 응답하도록하는 데 가장 자주 사용됩니다. 워크 플로를 방해하도록 설계된 대화
> 상자는 대개 모달입니다.

> 작성자는 `aria-label` 또는 `aria-labelledby` 속성을 사용하여 대화 상자 레이블을 제공해야(should)합니다 .

> **[- Accessible Rich Internet Applications (WAI-ARIA) 1.1](https://www.w3.org/TR/wai-aria-1.1/#dialog)**

위 요구 사항에 따라, 대화 상자에 대한 제목에 해당하는 요소가 없다면 `aria-label`을 이용하여, 그렇지 않으면
`aria-labelledby`를 이용하여 대화 상자 레이블을 제공합니다.

본 실습 파일에는 제목에 해당하는 요소가 없기 때문에 `aria-label`을 이용합니다.

As-Is

```html
<div id="voc-dialog" class="layer-popup">
  <div class="dialog">
    <div class="dialog-header"></div>
    <div class="dialog-body">
      ...
```

To-Be

```html
<div id="voc-dialog" class="layer-popup">
  <div class="dialog" role="dialog" aria-label="문의 메일 작성">
    <div class="dialog-header"></div>
    <div class="dialog-body">
      ...
```

### 대화 상자에 "modal" 속성 부여

`aria-modal`이라는 속성 이용.

> 요소가 "모달"인지 여부를 나타냄

> aria-modal 속성은 "모달" 요소의 존재가 페이지에서 다른 콘텐츠의 사용을 배제하는데 사용됩니다. 예를 들어, 모달
> 대화 상자가 표시되는 경우, 모달 대화 상자가 초점을 잃거나 더 이상 표기되지 않을 때까지 대화상자의 콘텐츠로
> 사용자 인터랙션을 제한될 것으로 기대됩니다.

> **[- Accessible Rich Internet Applications (WAI-ARIA) 1.1](https://www.w3.org/TR/wai-aria-1.1/#aria-haspopup)**

But... 아직 aria-modal을 지원하는 AT가 많지 않기 때문에 사용자 인터랙션의 제한 처리가 병행 되어야...

As-Is

```html
<div id="voc-dialog" class="layer-popup">
  <div class="dialog" role="dialog" aria-label="문의 메일 작성">
    <div class="dialog-header"></div>
    <div class="dialog-body">
      ...
```

To-Be

```html
<div id="voc-dialog" class="layer-popup">
  <div class="dialog" role="dialog" aria-label="문의 메일 작성" aria-modal="true">
    <div class="dialog-header"></div>
    <div class="dialog-body">
      ...
```


## Step 2. 대화 상자가 열리면, 초점을 대화 상자에 포함 된 요소로 이동

앞서 보았던 키보드 접근성 요구사항을 다시 보면,

> - 초점을 대화 상자에 포함 된 요소로 이동
> - 다른 방법을 사용하는 것이 바람직한 조건이 아니라면 초점은 처음에 포커스가 있는 첫 번째 요소에 설정
> - 첫 번째 상호 작용 요소를 중점으로하는 콘텐츠의 크기가 커서 시작 부분을 스크롤 할 수 없게 만들 수있는 경우
>   대화 상자 제목이나 첫 번째 단락과 같은 대화 상자의 맨 위에있는 정적 요소에 `tabindex=-1` 을 추가하여
>   해당 요소로 초점 이동
> - 대화 상자가 추가 정보를 제공하거나 처리를 계속하는 상호 작용으로 제한되는 경우 가장 자주 사용되는 요소 (예 :
>   확인 또는 계속 버튼)에 초점을 설정하는 것이 좋음

일반적인 경우에는 첫 번째 항목의 것과 같이 첫 번째 입력 상자인 "제목"에 해당하는 `input` 요소로 초점을 주면 되지만,
본 실습에서는 입력 양식 이전에 존재하는 안내 문구를 반드시 인지하도록 하는 요구사항이 있다는 가정으로 진행해 보도록
하겠습니다.

초점이 첫 번째 입력 상자로 이동될 경우, 보다 앞서 위치되어 있는 안내 문구를 낭독하지 않고 건너 뛰기 때문에 안내문구
보다 앞선 위치에 placeholder 역할을 해 줄 빈 a 요소를 추가하여 해당 요소로 초점을 이동시키도록 합니다.

최초 대화 상자가 열릴 때에만 필요한 요소이기 때문에, `tabindex=-1`을 추가하여 JavaScript에 의해서만 초점을 얻을
수 있도록 하고, 이후 `tab`키에 의한 초점 이동에는 대응되지 않도록 합니다.

As-Is

```html
<div id="voc-dialog" class="layer-popup">
    <div class="dialog" role="dialog" aria-label="문의 메일 작성">
      <div class="dialog-header"></div>
```

```javascript
function openModal (event) {
  ...
  modal.classList.add('open');
}
```

to-Be

```html
<div id="voc-dialog" class="layer-popup">
    <div class="dialog" role="dialog" aria-label="문의 메일 작성">
      <a id="dialog-placeholder" tabindex="-1"></a>
      <div class="dialog-header"></div>
```

```javascript
function openModal (event) {
  ...
  modal.classList.add('open');

  // placeholder 역할을 해 주는 요소를 찾아서
  var placeholder = modal.querySelector('#dialog-placeholder');

  // 요소로 초점 이동
  placeholder.focus();
}
```

## Step 3. 대화 상자 내부로 키보드 trapping

> - `tab` :
>   + 초점을 대화 상자 안의 다음 tabbable 요소로 이동
>   + 초점이 대화 상자의 마지막 tabbable 요소에 있는 경우 초점을 대화 상자의 첫 번째 tabbable 요소로 이동

> - `shift` + `tab` :
>   + 대화 상자 내의 이전 tabbable 요소로 이동
>   + 초점이 대화 상자의 첫 번째 tabbable 요소에 있는 경우 초점을 대화 상자의 마지막 tabbable 요소로 이동

As-Is

```javascript
function bindeKeyEvt(event) {
  event = event || window.event;
  var keycode = event.keycode || event.which;

  switch(keycode) {
    case 27:  // esc key
      closeModal(event);
      break;
    default:
      break;
  }
}
```

To-Be

```javascript
function bindeKeyEvt(event) {
  event = event || window.event;
  var keycode = event.keycode || event.which;

  // event가 발생한 요소
  var currEl = event.target || event.srcElement;

  // 첫 번째 탭 가능한 요소
  var firstTabbableEl = document.querySelector('#subject');

  // 마지막 탭 가능한 요소
  var lastTabbableEl = document.querySelector('#voc-dialog .btn-close');

  switch(keycode) {
    case 27:  // esc key
      closeModal(event);
      break;
    case 9:   // tab key
      // 탭 키가 눌린 요소가 마지막 요소이고, Shift 키가 눌리지 않았다면
      if (currEl === lastTabbableEl && !event.shiftKey) {
        // 기본 동작을 막고 첫 번째 탭 가능한 요소로 초점 이동
        event.preventDefault();
        firstTabbableEl.focus();
      }
      // 탭 키가 눌린 요소가 마지막 요소이고, Shift 키가 눌렸다면
      if (currEl === firstTabbableEl && event.shiftKey ) {
        // 기본 동작을 막고 마지막 탭 가능한 요소로 초점 이동
        event.preventDefault();
        lastTabbableEl.focus();
      }
      break;
    default:
      break;
  }
}
```

## Step 4. 대화 상자 외부로 탐색 제한

`role="dialog"`와 `aria-modal="true"`가 이 역할을 해주도록 제안되어 있지만 아직 미구현된 부분이나 오류가 발생되는
부분이 있고, HTML에서의 inert 속성 역시 아직 초안 단계에 머물러 있기 때문에 대화 상자 외부로의 탐색을 막기 위해서는
다른 방법이 필요합니다.

> 노드가 비활성(inert)인 경우, 유저 에이전트는 유저 인터랙션 이벤트들을 타겟팅 할 목적에 대해 노드가 없는 것 처럼
> 수행해야(must) 하고, 텍스트 검색 유저 인터페이스에 대해 무시할 수 있으며, 사용자가 그 노드 내의 텍스트를 선택하는
> 것을 못하게 할 수 있습니다.
>
> **[- HTML 5.2 - Section 5.2 Inert Subtrees](https://www.w3.org/TR/html/editing.html#inert-subtrees)**

> cf.) [Google inert](https://github.com/GoogleChrome/inert-polyfill),
> [WICG inert](https://github.com/WICG/inert)

보통 모달 대화 상자를 구현할 경우 diemed 레이어로 바탕 페이지에 대한 접근을 막아두고 대화 상자를 만드는 방법을 통해
일부 차단하고 있으나, 스크린리더와 같은 키보드 접근에 대해서는 이 구현 방법은 적용되지 않기 때문에 inert와 상응하는
방법으로 `aria-hidden`을 사용합니다. <br>

단, 모달 대화 상자는 `aria-hidden`에 의해 제한되지 않아야 하므로 `aria-hidden`이 설정된 요소의 하위 요소가
아니어야 합니다.

따라서, 개발 편의 상 가급적 dialog 요소를 level 1 수준으로 위치시키는 것이 정신건강에 좋습니다 :)

우선, 대화 상자가 열릴 때 대화 상자 외 타 요소로의 탐색을 제한하기 위해 타 요소를 비활성화 합니다.

As-Is

```javascript
function openModal (event) {
  ...
  if(!modal) return;
  if(btnClose)
    btnClose.addEventListener('click', closeModal, false);
  document.addEventListener("keydown", bindeKeyEvt, false);
  modal.classList.add('open');
  placeholder.focus();
}
```

To-Be
```javascript
function openModal (event) {
  ...
  if(!modal) return;
  if(btnClose)
    btnClose.addEventListener('click', closeModal, false);
  document.addEventListener("keydown", bindeKeyEvt, false);
  // 비활성 처리 함수 호출
  setInertness(modal);
  modal.classList.add('open');
  placeholder.focus();
}

// 비활성 처리 함수 선언
function setInertness (dialog) {
  // non-presentaion elements 제외
  var ommits = ["script", "meta", "link", "style", "base"];
  for(var i = -1, node; node = dialog.parentNode.children[++i];){
    if(node == dialog || ommits.indexOf(node.tagName.toLowerCase()) > -1)
      continue;
    //  대화상자 외 요소들에 `aria-hidden=true`, `inert` 속성 설정
    node.setAttribute("aria-hidden", "true");
    node.setAttribute("inert", "");
  }
}
```

대화 상자를 닫을 때에 다시 원래 상태로 돌려놓기 위해 비활성 처리 한 것을 다시 돌려놓도록 해제 처리합니다.

As-Is

```javascript
function closeModal (event) {
  ...
  modal.classList.remove('open');
}
```

To-Be

```javascript
function closeModal (event) {
  ...
  modal.classList.remove('open');
  // 비활성화 처리 해제 함수 호출
  unsetInertness();
}

// 비활성화 처리 해제 함수 선언
function unsetInertness () {
  // 비활성 처리시 설정했던 inert 속성으로 요소 가져오기
  var nodes = document.querySelectorAll('[inert]');
  for(var i = -1, node = null; node = nodes[++i];){
    // `aria-hidden="true"`, `inert` 속성 제거
    node.removeAttribute('aria-hidden');
    node.removeAttribute('inert');
  }
}
```

## Step 5. 대화 상자가 닫히면, 대화 상자가 열릴 때 마지막 위치했던 초점으로 이동

대화 상자가 닫히면 이전에 초점을 얻은 요소가 모달에 있을 경우 초점 손실이 발생할 가능성이 큽니다.

대화 상자의 구현 방식에 따라 초점 손실 시 문서의 처음부터 다시 탐색을 시작해야 하거나, 대화 상자에서의 마지막 초점
위치를 기준으로 초점 탐색을 다시 시작해야 하기 때문에 프로그램적으로 초점을 관리해야 합니다.

따라서, 대화 상자를 열 때 대화 상자를 호출한 컨트롤에 대한 참조를 저장하고, 대화 상자가 닫히면 초점을 저장해둔
컨트롤로 돌려주도록 합니다.

실습 파일에는, 아래 코드와 같이 대화 상자를 호출하는 요소가 이미 참조되어 있기 때문에, 대화 상자가 닫힐 때 초점을
돌려주는 부분에 대해서만 추가로 스크립트를 작성하면 됩니다.

```javascript
var btnVoc = document.querySelector('.btn-voc');
btnVoc.addEventListener('click', openModal, false);
```

As-Is

```javascript
function closeModal (event) {
  ...
  modal.classList.remove('open');
  unsetInertness();
}
```

To-Be
```javascript
function closeModal (event) {
  ...
  modal.classList.remove('open');
  unsetInertness();
  btnVoc.focus();
}
```

## Step 6. 추가적인 이슈 사항 정리

### iOS + VoiceOver issue

일단 위 단계까지 진행이 되면 어느 정도의 접근성 향상이 이루어진 상태로 마무리 될 수 있으나, 현재 알려진 몇 가지 
이슈들이 존재하기 때문에 이를 피해야 할 필요가 있습니다.

그 중 대표적인 것이 iOS safari + VoiceOver의 조합에서 대화 상자가 초기에 `display: none`으로 설정 된 경우, 
대화 상자를 노출 시 `display: block`으로 갱신하고 프로그램적으로 초점을 대화 상자 내부로 이동시켜도 VoiceOver가 
초점을 이동시키지 않는 버그가 존재합니다.

이를 해결하기 위한 방법으로 `display: none` - `display: block`을 토글하는 대신, `visibility: hidden` - 
`visibility: visible`로 토글하는 방식을 사용할 수 있습니다.

대부분 모달 대화 상자는 `position: fixed` 혹은 `position: absolute`를 사용하기 때문에 `visibility: hidden`의  DOM tree 순서에 따른 사이드 이펙트는 피해질 수 있습니다. 그럼에도 불구하고 이 부분에 대한 사이드 이펙트가 걱정 된다면 
`hidden` 속성(attribute)를 추가적으로 사용할 수 있습니다. 

실습에서는 `hidden` 속성과 `visibility` 속성(property)을 사용하도록 한 번 더 수정을 가해보겠습니다.

As-Is

```css
.layer-popup {
  display: none;
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  background-color: rgba(0,0,0, 0.75);
  z-index: 900
}

.layer-popup:target,
.layer-popup.open {
  display: block
}
```

```html
<div id="voc-dialog" class="layer-popup">
  <div class="dialog" role="dialog" aria-label="문의 메일 작성" aria-modal="true">
    ...
  </div>
</div>
```

To-Be
```css
.layer-popup {
  visibility: hidden;
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  background-color: rgba(0,0,0, 0.75);
  z-index: 900
}

.layer-popup:target,
.layer-popup.open {
  visibility: visible
}
```

```html
<div id="voc-dialog" class="layer-popup" hidden>
  <div class="dialog" role="dialog" aria-label="문의 메일 작성" aria-modal="true">
    ...
  </div>
</div>
```

`hidden` 속성이 사용되었을 때에는, JavaScript에 의해 hidden 속성을 변경해주어야 하므로 JS 파일에도 수정이 필요합니다.

As-Is

```javascript
function openModal (event) {
  ...
  setInertness(modal);
  modal.classList.add('open');
  placeholder.focus();
}

function closeModal (event) {
  ...
  modal.classList.remove('open');
  unsetInertness();
  btnVoc.focus();
}
```

To-Be

```javascript
function openModal (event) {
  ...
  setInertness(modal);
  modal.hidden = false;
  modal.classList.add('open');
  placeholder.focus();
}

function closeModal (event) {
  ...
  modal.classList.remove('open');
  modal.hidden = true;
  unsetInertness();
  btnVoc.focus();
}
```

### 화면 확대 시 대화 상자 내용 탐색 불가

대화 상자를 만들 때 접근성 이슈에 관하여 종종 놓치는 부분 중 하나가 화면 확대에 대한 부분입니다. 물론 이 부분은 
꼭 화면 확대가 아니더라도 작은 스크린 해상도에서도 발생될 수 있는 이슈에 속하기도 합니다.

<video src="/video/zoom-test.webm" width="100%" controls="controls"></video>

실습 예제에는 이미 해당 이슈가 어느 정도 해결이 되어 있고 해결 방법이 상황에 따라 달라지기 때문에 해결을 위한 별도의
작업이 소개되지는 않습니다.

다만, 대화 상자의 접근성 이슈를 해결 할 때 대부분이 스크린리더 사용자에 대한 대응에 국한 되는 경우가 많은데 이 외에도 
여러 관점에서 접근성 이슈를 살펴보고 해결할 수 있도록 관심을 기울이는 것이 필요합니다.