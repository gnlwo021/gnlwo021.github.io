---
title: GitHub Blog - 댓글 기능 추가하기
date: 2024-11-07
tags: [Blogging, Git, Jekyll]
categories: [GitHub Blog]
comments: true
---

> 해당 블로그는 [jekyll-theme-chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) 테마를 기준으로 작성하였습니다.

## 서론
---
블로그에는 댓글이 사람들과 소통할 수 있는 중요한 창구입니다. GitHub블로그를 사용하시는 분들은 기본적으로 댓글 기능을 지원하지 않기때문에 추가적인 설정이 필요합니다.

이번 글에서는 GitHub 블로그에 댓글 기능을 추가할 수 있는 방법을 알아보겠습니다.

## 댓글 기능 추가
---
GitHub 블로그에서 사용할 수 있는 댓글 종류는 **`disqus`**, **`utterances`**, **`giscus`** 총 3가지가 있습니다. github블로그를 이용하는 대부분의 사용자들은 `Disqus`를 사용한다고 합니다.

하지만 `Disqus`는 무료 버전을 사용하면 광고가 붙어 사용하기 불편하고 로딩도 꽤나 느리다고 합니다.

그래서 저는 GitHub 이슈를 기반으로 쉽게 댓글을 관리할 수 있는 **`utterances`**를 사용했습니다. 
<blockquote class="prompt-tip"><code class="highlighter-rouge">utterances</code>는 Issues에서 댓글 알람 설정도 가능해서 편리합니다.</blockquote>

### Utterances 설치
GitHub App에서 [Utterances](https://github.com/apps/utterances)를 설치해야합니다. [Utterances](https://github.com/apps/utterances)로 접속해서 install 버튼 클릭을 누릅니다.
![Utterances 사진2](/assets/img/Utterances설치2.png)
<blockquote class="prompt-tip">이미 설치가 된 경우 Configure으로 표시되지만 설치가 되지 않은 경우는 install이 표시됩니다.</blockquote>

이후 install 버튼을 클릭하면 Repository를 선택하는 부분이 나옵니다. `Only select repositories`를 체크하고 `Select repositories`에서 github 블로그를 선택한 후 Install을 클릭합니다.
<img src="/assets/img/Utterances설치3.png" alt="Utterances 사진3" width="400">

다음단계로 넘어오면 `utterances`를 설정하는 화면이 보이실텐데 이 부분은 `jekyll-theme-chirpy`를 적용하면 댓글을 설정하는 파일이 이미 있기때문에 생략합니다.
![Utterances](/assets/img/Utterances.png)

### Issues 활성화
`utterances`를 사용하기 위해서는 Issues를 활성화 해야합니다.
본인의 github.io repository로 이동 후 `Settings` → `General`탭에서 스크롤하여 밑으로 내리면 Features가 보이실텐데 Issues를 체크하여 활성화합니다.
![Issues](/assets/img/Issues.png)

### _config.yml 설정
`_config.yml`파일로 이동 후 `ctrl+f`로 comments를 입력하여 comments로 이동합니다. 이후 아래 코드처럼 입력합니다.
```yaml
...
comments:
  # Global switch for the post-comment system. Keeping it empty means disabled.
  provider: utterances # [disqus | utterances | giscus]
  # The provider options are as follows:
  disqus:
    shortname: # fill with the Disqus shortname. › https://help.disqus.com/en/articles/1717111-what-s-a-shortname
  # utterances settings › https://utteranc.es/
  utterances:
    repo: gnlwo021/gnlwo021.github.io # <gh-username>/<repo> #계정명/저장소이름
    issue_term: pathname # < url | pathname | title | ...>
    label: comments
...
```
- `disqus`나 `giscus`는 무시합니다.
- `utterances`부분에서 provider는 `utterances`를 사용하기 때문에 utterances를 입력합니다.
- `repo`는 `계정명/저장소이름`을 적어줍니다.

이런식으로 설정을 해주면 나중에 페이지마다 `<script>`를 사용할 필요 없이 코드 한줄만 추가해서 사용할 수 있습니다.

### 댓글 사용방법
github 블로그를 사용하는 사용자라면 모두 알다시피 포스트를 생성할때는 `/_posts/`경로에 생성합니다.
`/_posts/`에 페이지를 생성하면 맨 위쪽에 아래 코드를 입력합니다.
```yaml
---
comments: true
---
```
<blockquote class="prompt-tip">댓글을 비활성화 하고싶다면 false로 설정해주면 됩니다.</blockquote>

로컬서버를 켜서 댓글 활성화가 되어있는지 확인합니다.
![comment](/assets/img/comment.png)

댓글을 추가하면 github repository의 `Issues`탭에 보면 댓글이 추가된 것을 확인할 수 있습니다.
![댓글지우기3](/assets/img/댓글지우기3.png)

## 댓글을 지워보자
---
댓글을 지우는 방법은 매우 간단합니다. github repository의 `Issues`탭으로 이동하여 댓글이 달린 포스팅으로 이동합니다.
![댓글지우기1](/assets/img/댓글지우기1.png)

지우고 싶은 댓글에 Delete를 클릭하여 삭제합니다.
![댓글지우기2](/assets/img/댓글지우기2.png)
댓글 삭제에 성공하면 댓글 기능은 성공적으로 추가되었습니다!

## 마무리
---
GitHub 블로그에 댓글 기능 하나 추가하는 데도 꽤 오래 걸렸습니다.. 이번 포스팅에서는 댓글 기능을 추가했지만, 앞으로 다른 기능들도 추가해서 블로그의 완성도를 더 높여보고자 합니다.