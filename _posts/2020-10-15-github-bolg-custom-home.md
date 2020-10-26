---
layout: post
title: "jekyll theme 커스텀하기 - lanyon 테마 메인 화면 변경"
categories: [Blog]
---

### Home 화면에서 글 제목만 보이게 설정

다른 테마는 보통 홈 화면에서 글 목록을 보여주곤 하는데, lanyon 테마는 홈 화면에서 글 전체를 전부 목록에 담아 보여줘서 다른 글을 찾기 위해서는 스크롤을 길게 내려야만 했다.

![변경 전 홈 화면 상태]({{"/assets/images/before.png"| relative_url}})
>변경 전 홈 화면 상태

그래서 커스터마이징을 통해 홈 화면에서 글 제목만 보이도록 설정하고자 한다.

index.html
```html
{% raw %}
---
layout: default
title: Home
---

<div class="posts">
  {% for post in paginator.posts %}
  <div class="post">
    <h1 class="post-title">
      <a href="{{ post.url | absolute_url }}">
        {{ post.title }}
      </a>
    </h1>

    <span class="post-date">{{ post.date | date_to_string }}</span>

    {{ post.content }} <!-- 이 부분을 삭제-->
  </div>
  {% endfor %}
</div>
{% endraw %}
```

여기서 `{% raw %}{{ post.content }}{% endraw %}`
이부분을 삭제하면 글의 제목만 보여지게 된다.

![변경 후 홈 화면 상태]({{"/assets/images/after.png"| relative_url}})
>변경 후 홈 화면 상태
