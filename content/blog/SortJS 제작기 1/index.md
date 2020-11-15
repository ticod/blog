---
title: SortJS 제작기 1
date: "2020-10-29T20:48:00.000Z"
description: "JS로 정렬 시각화"
---

# SortJS 제작기 1

최근 스터디에서 정렬에 대해 정리하면서, 아래와 같이 시각화해보고 싶다는 생각이 들었다.



(출처: https://commons.wikimedia.org/wiki/File:Sorting_quicksort_anim.gif)

마침 최근에 노마드 코더님의 강의 중 JS로 그림판 만들기 ? 라는 강의를 보았고, 여기서 배운 것을 바탕으로 canvas 태그에 막대그래프를 그린 후, 정렬 진행 과정을 보여주면 되겠다 라는 생각이 들어서 만들게 되었다.



먼저, 기본 레이아웃과 기초 css를 만들고, (이 때 강의에서 봤던 reset.css를 가져왔고, 그 외 css도 필요한 부분을 가져와서 사용했다.) 자바스크립트 코딩에 착수하였다. 

(사실 자바스크립트 코딩을 진행하면서 어 딜레이 조절도 있으면 좋겠고, 초기화 기능은 필요하겠는데? 싶어서 나중에 추가했다.)



생각보다 어려웠던 부분이 있는데, 바로 딜레이다. 동기화를 시켜줘야 할 것 같은데, promise나 callback에 대한 개념이 없었기 때문에, 단순히 정렬을 시각화하자는 가벼운 마음에서 시작해서 js를 더 깊게 공부해야 겠다는 생각이 들었다. 배보다 배꼽이 더 큰 격이다.



아무튼, 딜레이를 위해 여러가지 코딩을 시도했고, 실패했다. 그래서 구글링을 통해 조사했고, 재밌는 소스를 발견했다.

```javascript
const timer = ms => new Promise(res => setTimeout(res, ms));
```

[(출처: https://stackoverflow.com/questions/3583724/how-do-i-add-a-delay-in-a-javascript-loop](https://stackoverflow.com/questions/3583724/how-do-i-add-a-delay-in-a-javascript-loop))

setTimeout에 무조건 함수를 넣어야 한다고 생각하고 있었는데, 그냥 이런식으로 딜레이 함수를 만들 수 있었다.



아 그리고 매개변수로 함수의 참조값을 전달해줘야 한다는 사실을 조금 뒤늦게 깨달았다.



현재 작업 중인 결과물을 깃허브 페이지로 만들어놨다.

[https://ticod.github.io/SortJS/](https://ticod.github.io/SortJS/)

일단 버블, 선택, 삽입 정렬을 구현해 놓은 상태이고, 그 외 정렬들도 js로 구현해서 시각화할 생각이다.