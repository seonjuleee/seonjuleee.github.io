---
layout: post
title: "[blog] jekyll theme 커스텀하기 - 글꼴 변경"
---

### 글꼴 변경
Google Fonts 사이트를 이용하여 쉽게 글꼴 변경이 가능하다.

![Google Fonts]({{"/assets/images/google_font.png"| relative_url}})

먼저 원하는 폰트를 선택 하면 Selected family 사이드바가 나타나게 되는데, 여기서 Embed를 눌러 @import와 CSS 코드를 복사해서 파일에 붙여넣으면 된다.

lanyon 테마의 경우, lanyon.css 파일에 코드를 붙여 넣으면 된다.

lanyon.css

```css
@import url('https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@100;300;400;500;700;900&display=swap');
```

```css
html {
  font-family: "Noto Sans KR", sans-serif, "PT Serif", Georgia, "Times New Roman", serif;
}
```
`font-family`에 `"Noto Sans KR", sans-serif`를 앞부분에 추가했다.