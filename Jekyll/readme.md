# Jekyll

Jekyll : Static site generator

* HTML / css 를 잘 몰라도 정적 사이트를 만들기 편함
* 자신만의 theme / layout 을 만들어서 커스텀 가능
* GitHub pages 과 호환이 가능하다는 장점

## Mac 에 Jekyll 설치

* Ruby : ruby로 Jekyll 이 만들어짐
* RubyGems : ruby 를 위한 package manager. (package manager 컴퓨터에 다른 프로그램을 다운/업데이트 등 관리해주는 프로그램)

1. ruby version 확인

Jekyll 은 현재 (2023/05/28) 기준으로 2.5.0 이상의 버전에서 사용 가능

```
ruby -v
```

2. gem version 확인

```
gem -v
```

<img width="654" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/3c5c0e7d-1f4a-43bb-b5bc-d51817229e80">

3. Jekyll 설치

```
gem install jekyll bundler
```

위 커멘드를 실행하면 아래와 같은 에러가 뜸

```
ERROR:  While executing gem ... (Gem::FilePermissionError)
You don't have write permissions for the /Library/Ruby/Gems/2.6.0 directory.
```

이런 에러가 뜨는 이유는 Apple 이 Mac 에 미리 설치되어 있는 Ruby 버전에 직접 gem 을 설치할 수 없게 하기 때문임. gem 을 Mac 에 설치하기 위한 올바른 방법은 Ruby 의 별도 버전을 설치하는 것이라 함. (https://developer.apple.com/forums/thread/697249)

* `chruby` 를 사용해서 해결할 수 있는 방법
	1. chruby 를 다운받기 위한 Homebrew 설치 : `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`
	2. chruby 설치 : `brew install chruby`
	3. chruby configuration
		1. .bash_profile 파일 열기 : `open -e ~/.bash_profile`
		2. .bash_profile 에 다음 줄 추가
			`source /usr/local/opt/chruby/share/chruby/chruby.sh`
			`source /usr/local/share/chruby/auto.sh`
		4. .bash_profile 실행 : `source ~/.bash_profile`
	4. `ruby-install` 설치 : `brew install chruby ruby-install`
	5. `ruby-install` 로 현재의 stable 한 버전의 ruby 설치 : `ruby-install ruby`
	6. 현재 설치된 ruby version 들 확인 : `chruby`
		<img width="196" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/80b35dcd-b6d9-473a-a953-b2d33a91b693">
	7. 루비 버전 선택 후 switch : `chruby (버전)`. 버전이 선택되면 `chruby` 를 했을 때 해당 버전 앞에 별표가 붙음
		<img width="240" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/2c555478-81bd-4c99-8657-6f6404f65244">
	9. `gem install jekyll bundler` 다시 실행
	
4. Jekyll version 확인

```
jekyll -v
```

<img width="223" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/90904d9d-f4b9-4363-9936-193d9c259ccc">

## Jekyll 사이트 생성

1. 사이트 생성하고 싶은 디렉토리에서 아래 커맨드 실행. (이름은 자유)

```
jekyll new YJBlog
```

이러면 설정한 이름으로 디렉토리 생성됨

<img width="87" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/221d6a48-5491-4a64-ac11-e84463a5954f">

Jekyll 은 아래와 같이 기본 파일들을 구성한 채로 디렉토리를 생성해줌. Jekyll 이 이런 파일들을 모두 함께 컴파일해서 웹을 'serve' 함

<img width="232" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/8e5e4086-84ae-464b-9ef0-d0bd036ed70c">

<img width="689" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/92824a1f-7f5f-4698-8d56-a37d25290016">

2. 해당 디렉토리로 이동 후 로컬 서버에서 웹 페이지 띄우기

```
cd 디렉토리
bundle exec jekyll serve #실행하는 최초 시점에만 bundle exec 꼭 붙여야함
```

<img width="689" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/a89a2376-643e-4493-9e36-ef3a63e7174f">

실행하면 위와 같이 로컬 호스트 port 4000 에서 웹사이트를 돌림.

3. `http://127.0.0.1:4000/` 에서 웹페이지 확인

<img width="1072" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/75279060-17af-4f3c-8774-2b2f18da997d">

### Directory 구조

1. `_posts` : 블로그 포스트 폴더
2. `_site` : 웹사이트의 최종 버전을 담고 있음. 웹사이트를 보여주기 위한 이미지/정적 파일등 최종 웹사이트를 보여주기 위한 파일들이 있음. 여기에 있는 파일들을 웹서버에 올리면 사이트가 보여질 것임. 직접 수정하는 게 아니라 우리가 변경하는 거에 따라서 수정됨
3. `_config.yml` : YAML(키-값 을 저장하기 위한 언어) 를 사용해서 우리의 사이트의 attribute / setting 등을 저장하기 위한 파일
4. `404.html`
5. `about.markdown`
6. `Gemfile` : ruby 가 사용함. 우리의 웹사이트의 dependency 를 관리하는 파일. 플러그인 설정 가능
7. `Gemfile.lock`
8. `index.markdown`

## Liquid

Jekyll 이 사용하는 templating 언어.

세 가지 메인 component

1. objects
2. tags
3. filters

### Objects

페이지의 컨텐츠로 보여질 미리 정해진 [variable](https://jekyllrb.com/docs/variables/)들이 사이트에서 보여질 수 있게 함. `{{ (variable) }}` 과 같이 사용.

e.g. `{{ page.title }}` : `page.title` variable 을 출력함

### Tags

https://jekyllrb.com/docs/liquid/tags/

템플릿에 사용할 로직 / control flow 를 정의할 수 있음. `{%`, `%}` 사이에 입력.

e.g.

```
{% if page.show_sidebar %}
	<div class="sidebar">
		sidebar content
	</div>
{% endif %}
```

만약 `show_sidebar` page variable 이 true 면 사이드 바를 보여줌.

### Filters

https://jekyllrb.com/docs/liquid/filters/

Object 의 output 을 바꿔줌. `|` 를 구분자로 사용.

e.g. `{{ "hi" | capitalize }}` : `hi` 가 아닌 `Hi` 를 출력.

## Front Matter

페이지에 대한 정보. (타이틀/이미지/저자 등)

파일 맨 윗 부분에 `---` 로 시작함. Liquid 에서 `page.~~` 로 front matter 변수들을 호출할 수 있음. e.g. `{{ page.my_number }}`

1. jekyll 에서 이미 만들어준 `_posts` 폴더의 예제 post 열기

포스트는 markdown 형식.

<img width="264" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/4f3eede1-c9e4-4cd3-a3de-3ba84a2fae0b">

가장 상단에 보이는 위의 사진과 같은 부분이 front matter. 포스트에 대한 정보를 담고 있음. YAML, JSON 언어 사용 가능.(둘다 키-값 정보를 저장할 수 있음)

2. 타이틀 수정해보기

Front matter 에 있는 타이틀을 수정하고 사이트를 다시 로드할 경우 수정한 타이틀이 반영된 것을 확인 가능.

<img width="259" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/02a09378-7abb-4aea-84c8-341b8ce3a0c3">

<img width="164" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/6123cc3c-b7bb-441e-81e8-f7f16c53a5a6">

=> 즉 우리가 사용하는 theme 이 front matter 에 있는 정보를 가져와서 블로그 포스트를 보여주는데 사용하고 있는 것임.

Front matter 는 실제 url 의 일부로도 사용됨.

<img width="412" alt="image" src="https://github.com/Yoojin99/100-Days-of-SwiftUI-Practice/assets/41438361/c92e2a2a-c05d-4f9a-9e72-d7dcd1a8d480">

## Post 생성

