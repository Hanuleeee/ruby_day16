# 20180703_day16



### 1. Watch(영화 정보 저장)

#### 프로젝트 생성하기

`$ gem install rails -v 5.0.7`

`$ rails _5.0.7_ new watcha`

`$ rails g scaffold movies` : movies controller와 movie model이 생성됨

'*routes*'  `root 'movies#index' `  추가



'*Gemfile*'     	

```ruby
gem 'turbolinks', '~> 5'  #주석처리
```

*app/assets/javascripts/application.js*

```ruby
#삭제
//= require turbolinks   
//= require_tree .

#추가
//= require popper   
//= require bootstrap
```

*application.html.erb*

```erb
<%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload'%>
↓↓  <!--수정 -->
<%= javascript_include_tag 'application'%>
```

: `turbolinks` 3군데서 제외시킴



*app/assets/stylesheets/application.scss*

```scss
//있는거 다지우고 추가
@import 'bootstrap';
```



#### 로그인 구현 (user authenticate(**devise**))

`gem 'devise'` : *Gemfile*에 추가하기 

`$ rails g devise:install` : 명령어 입력 후 수정 및 추가와 관련된 사항이 나온다.

'*config/environment/development.rb*'

```ruby
config.action_mailer.default_url_options = { host: 'localhost', port: 3000 } 
#추가
```

```bash
hanullllje:~/watcha_app $ rails g devise users
```



#### db 설정

'*db/migrate/create_movies*'   : 컬럼설정

```ruby
class CreateMovies < ActiveRecord::Migration[5.0]
  def change
    create_table :movies do |t|
      t.string    :title
      t.string    :genre
      t.string    :director
      t.string    :actor
      
      t.integer   :user_id  #외래키
      
      t.text      :description
      t.timestamps
    end
  end
end

```

'*app/models/movie.rb*'   :  관계설정 (1:m)

```ruby
belongs_to :user
```

'*app/models/user.rb*'  

```ruby
has_many :movies
```



```bash
hanullllje:~/watcha_app $ rake db:migrate  # db 구동
```



#### 이미지 업로드를 위한 설정 (image upload(local))

`$ rails g uploader image `

'*Gemfile*'

```ruby
# beautify
gem 'bootstrap', '~> 4.1.1'
# authentication
gem 'devise'
# file upload
gem 'carrierwave'      # 이미지 업로더

group :development do
    gem 'rails_db'     # 추가
end
```

```bash
hanullllje:~/watcha_app $ bundle install
```



```bash
hanullllje:~/watcha_app $ sudo apt-get update
hanullllje:~/watcha_app $ sudo apt-get install imagemagick
```

*app/models/movie.rb*

```
class Movie < ApplicationRecord
    # 해당 컬럼은 ImageUploader에서 관리할 것임
    mount_uploader :image_path, ImageUploader
    belongs_to :user
end
```

- `movie` db의 `image_path` 컬럼은 `ImageUploader`에서 관리할 것을 의미한다.

  

지난번에 한 **Image Uploader** 참고해보자



### 2. Javascript

- 기본적인 JS설명(front)

- HTML과 CSS는 우리가 만든 view를 조금 더 깔끔하게, 직관적으로 만들어주었지만 동적으로 HTML Dom을 추가하거나 수정, 삭제하는 등의 동적인 부분을 표현하기엔 부족했다. HTML과 CSS, 그리고 JS를 통해 완벽해진 view를 만들어보자.
- 자바스크립트는 HTML, CSS와 마찬가지로 무언가를 설치할 필요없이 브라우저에서 바로 동작한다. 브라우저의 콘솔에서 여러가지 자바스크립트 요소에 대해 알아보자.
- 서버 창 `F12 -> console`  에서 일회성으로 동작 가능하다.



#### 요소 선택자

- 프론트에서의 기본 자바스크립트는 3가지만 알고 있으면 된다. **요소, 이벤트, 함수**. 요소는 특정 HTML 요소를 찾아내는 선택자로 찾을 수 있고, **이벤트는 사용자가 사이트에서 하는 모든 행동**을 담을 수 있다. 함수는 개발자가 정의한 특정 로직을 의미한다.

```javascript
// document에는 사용자가 서버로부터 받아온 모든 HTML의 내용이 담겨있다.
document.getElementById('id') // id를 가진 하나의 요소를 찾는다. return 값은 요소 1개
document.getElementsByClassName('class') // class를 가진 요소를 찾는다. return 값은 배열형태
document.getElementsByTagName('tag') // HTML태그를 모두 찾는다. return 값은 배열형태
document.querySelector('css선택자') // css선택자로 요소를 찾는다.(먼저 발견된 하나의 요소만 리턴시켜줌) return 값은 요소 1개
document.querySelectorAll('css선택자') // css선택자로 요소를 찾는다. return 값은 배열형태
```



#### Console

- 크롬 브라우저의 개발자 도구에 보면 console 탭이 있다. 이 console 탭에서는 많은 메시지를 보고 자바스크립트를 동작시킬 수 있으며, 이 부분은 자바스크립트에서 console 객체로 조작할 수 있다.

* 입력하는 3가지 방법

  ```javascript
  console.log("hi")
  console.error("error")
  console.dir("btn")  // btn에 해당하는 모든 정보를 보여준다.
  ```



#### 이벤트

이벤트는 브라우저에서 일어나는 사건 혹은 행위를 의미한다. 사용자의 클릭, 스크롤, 드래그, 마우스 이동 등 다양한 이벤트가 일어난다. 자바스크립트에서는 해당 이벤트를 감지하고 이벤트를 감지했을 때 특정 결과를 만들어내게 끔 할 수 있다.

   Ex. '하이' 라고 쓰여있는 버튼을 눌렀더니 '바이'라고 바뀜

```javascript
btns[0].onmouseover = alert("하이!")
alert("hi");
confirm("삭제하시겠습니까?");
prompt("이름을 입력하세요");
"하이"
```



#### 이벤트 등록하는 방법 2가지 (이벤트 리스너 추가)

* 요소에 이벤트가 발생했을 때, 해당 이벤트가 발생했는지 감지하는 것을 이벤트 리스너라고 한다. 만약 `버튼을 클릭하다.` 라고 했을 때, `버튼`은 요소가 되고 `클릭하다`는 이벤트가 된다. 이벤트 리스너는 `버튼을 클릭했을 때`를 감지한다고 할 수 있겠다.

1. **요소.on[event]**

```javascript
1. 요소.on[이벤트이름] = function(매개변수){
	 //이벤트리스너	//이벤트핸들러(이벤트가 발생했을때 동작하는 행동)
  					 ...
  					 alert("건드리지마");
 					 }
```

```javascript
var btn = document.getElementsByClassName("btn");
btn[0].onmouseover = function(){ alert("나 건드리지마!!!");}
```



*응용하기*

1. 마우스를 오버하면 "건들지마요" alert()를 띄움
2. 마우스 오버를 3회 이상 하면 "그만! " 이라는 alert( )를 띄움

```javascript
<script>
var btn = document.getElementsByClassName("btn")[0];
var count = 0;
var msg = "나 건드리지마 ";
btn.onmouseover = function(){
	count++;
	if(count > 3 ){
		msg = "그만해라"
    }
	alert(msg);
}
</script>
```



2. **요소.addEventListener**

```javascript
var btn = document.getElementsByClassName("btn")[1];
btn.addEventListener("mouseover", function(){
	alert("하하하");
});
```





#### 함수 만들기 (3가지)

* 자바스크립트 함수는 기본적인 블록중에 하나이며, 작업을 수행하거나 값을 계산하는 등의 일종의 절차이다. 다시말해 레일즈에서 활용하던 액션에 해당한다고 보면 편할 것이다. 함수를 사용하려면  함수에서 동작하고자 하는 내용을 적어야 하는데 함수의 정의는 호출하고자 하는 범위 내에서 선언되어 있어야 한다.

1. 함수 표현식

   ```javascript
   func1  //불가
   var func1 = function(){
    alert("하위~1");
   }
   func1;
   // 선언되기 이전에 사용할 수 없다.
   ```

2.  함수 선언식

   ```javascript
   func2;  //가능
   function func2(){
   alert("하위~2");
   }
   //선언되기 이전에도 사용할 수 있다.
   ```

    

   *응용하기*

   ```javascript
   var btn = document.getElementsByClassName("btn")[0];
   btn.addEventListener("mouseover", func2); 
   // func2() : 이건 함수 실행, 함수명만 적어야 함수 등록
   
   var btn2 = document.getElementsByClassName("btn")[1];
   btn2.onmouseover =func1;  
   //함수 이름만 적어줘야 함수가 정상적으로 등록됨
   ```

   선언되어 있던 함수를 이벤트 핸들러로 사용할 경우, 함수 이름만 적어서 사용한다.

   함수이름() <- 이 형태는 함수의 실행을 의미한다.



3. 익명함수

   ```javascript
   // 버튼(요소)에 마우스를 오버(이벤트)했더니(이벤트 리스너)
   // 그 위에 있던 글자(요소)들이 갑자기 이상한 글자로 변해버린다(이벤트핸들러).
   
   var index =1;
   var text = document.getElementsByClassName("card-title")[index]
   var btn = document.getElementsByClassName("btn")[index];
   
   btn.addEventListener("mouseover", function(){
   var title = document.getElementsByClassName("card-title")[index];
   console.dir(title);
   title.innerText = "Don't touch me!!!!!!!"; //마우스를 올렸을때 title이 바뀜
   });
   
   btn.addEventListener("mouseout", function(){
     var title = document.getElementsByClassName("card-title")[index];
     title.innerText = text;
   });
   ```

    

   *응용하기*

   ```javascript
   //버튼(요소)에 마우스를 올리면(이벤트, 이벤트 리스너)
   //해당버튼(요소)의 class가 btn btn-danger로 변함(이벤트 핸들러)
   
   var btn = document.getElementsByClassName("btn")[0];
   btn.addEventListener("mouseover", function(){
     btn.setAttribute("class", "btn btn-danger ");
   })
   btn.addEventListener("mouseout", function() {
     btn.setAttribute("class", "btn btn-primary")
   })
   ```

   

#### 이벤트 핸들러

- 이벤트 리스너를 학습하면서 이벤트가 발생했을 경우 동작하는 함수를 지정했다. 이렇게 이벤트가 발생했을 때 해당 이벤트를 처리하는 함수를 이벤트 핸들러라고 한다. 최종적으로 우리가 만들 `버튼을 클릭했을 때 alert 창을 뜨게한다.` 와 같은 내용을 다시 정의해보면 `버튼(요소)을 클릭(이벤트)했을 때(이벤트 리스너), alert 창을 뜨게한다(이벤트 핸들러)` 의 형태로 보면 되겠다.





* 사용자 기능정의 (금욜)
  * page에 어떤게들어갈건지.. UI
  * 회원가입
  * 메인페이지
  * 게시판 목차
  * 게시판 입력창
  * 게시판 수정창(입력창)
  * 게시글 보기창
  * ...

```
p @instance.errors 
```

