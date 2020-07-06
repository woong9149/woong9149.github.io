---
title: "[React]생활코딩 5️⃣ -create"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
categories:
- React
- 생활코딩
- create
tags:
- React
- 생활코딩
- create
---

# 베이스캠프
**props**는 **read-only**, **state**는 **수정이 가능**하다.   
상위 컴포넌트가 하위컴포넌트에게 데이터를 전달할때는 **props**,     
하위 컴포넌트가  상위 컴포넌트에게 데이터를 전달할 때는 **event를** 쓴다.   
 
####  Redux 
각각의 컴포넌트의 데이터를 하나의 저장공간에 저장해서,    Redux안의 데이터가 바뀌면 연결퇸 컴포넌트의 데이터가 모두 바뀐다.
# Create 구현 :  mode변경
<img width="435" alt="스크린샷 2020-07-06 오후 10 38 42" src="https://user-images.githubusercontent.com/42554237/86599843-299aad80-bfda-11ea-9667-71e00f88012f.png">

Create, Update, Delete 버튼을 추가하는 Control 컴포넌트를 만들어 App.js에 연결하고,   
이벤트를 부여해 this.setState( ) 함수로 mode 값을 변경하도록 한다.

Control.js
```python
import React,{Component} from 'react';

class Control extends Component{
    render(){//class안의 함수는 function을 생략하고 사용 가능
      //컴포넌트는 최상위태그 하나로 감싸져야한다.
      return (
        <ul>
          <li><a href="/create" onClick={function(e){
            e.preventDefault();
            this.props.onChangeMode('create');
          }.bind(this)}>create</a></li>
          <li><a href="/update" onClick={function(e){
            e.preventDefault();
            this.props.onChangeMode('update');
          }.bind(this)}>update</a></li>
          <li><input onClick={function(e){
            e.preventDefault();
            this.props.onChangeMode('delete');
          }.bind(this)} type="button" value="delete" ></input></li>
        </ul>
  
      )
    }
  }

  export default Control;
```

<br/>
App.js
```python
import React,{Component} from 'react';
// import logo from './logo.svg';
import './App.css';
import TOC from './components/TOC';
import Content from './components/Content';
import Subject from './components/Subject';
import Control from './components/Control';


class App extends Component{

  constructor(props){
    super(props);
    this.state = {
      mode:'Welcome',
      selected_content_id:2,
      subject:{title:'WEB', sub: 'World Wide Web'},
      welcome:{title:'Welcome', desc:'Hello, React!!'},
      contents:[
        {id:1, title:'HTML', desc:'HTML is for information...'},
        {id:2, title:'CSS', desc:'CSS is for design'},
        {id:3, title:'JavaScript', desc:'JavaScript is for interactive...'}
      ]
    }
  }

  render(){
    console.log('App render');
    var _title, _desc = null;
    if(this.state.mode === 'Welcome'){
      _title = this.state.welcome.title;
      _desc  = this.state.welcome.desc;
    }else if(this.state.mode === 'read'){
      var i = 0;
      while(i < this.state.contents.length){
        var data = this.state.contents[i];
        if(data.id === this.state.selected_content_id){
          _title = data.title;
          _desc = data.desc;
          break;
        }
        i = i + 1;
      }
      // _title = this.state.contents[0].title;
      // _desc  = this.state.contents[0].desc;
    }
    return (
      <div className="App">
        <Subject title = {this.state.subject.title} 
        sub = {this.state.subject.sub} 
          onChangePage = {function(){
            this.setState({mode:'Welcome'})
          }.bind(this)}
        >
        </Subject>
        <TOC onChangePage = {function(id){
          this.setState({
            mode:'read',
            selected_content_id: Number(id)
          })
        }.bind(this)} data = {this.state.contents} ></TOC>
        <Control onChangeMode={function(_mode){
          this.setState({
            mode:_mode
          });
        }.bind(this)}></Control>
        <Content title={_title} desc={_desc}></Content>
      </div>
    );
}
}

export default App;//TOC.js를 갖다쓰는 쪽에서 TOC라는 class를 갖다쓸 수 있게 해주는 코드;

```

# Create 구현 :  mode 전환기능
mode가 read,create로 변할 때 Content 컴포넌트를 각각 ReadContent, CreateContent로 전환되게 한다.   
**App.js**
```python
import React,{Component} from 'react';
// import logo from './logo.svg';
import './App.css';
import TOC from './components/TOC';
import ReadContent from './components/ReadContent';
import CreateContent from './components/CreateContent';
import Subject from './components/Subject';
import Control from './components/Control';


class App extends Component{

  constructor(props){
    super(props);
    this.state = {
      mode:'Welcome',
      selected_content_id:2,
      subject:{title:'WEB', sub: 'World Wide Web'},
      welcome:{title:'Welcome', desc:'Hello, React!!'},
      contents:[
        {id:1, title:'HTML', desc:'HTML is for information...'},
        {id:2, title:'CSS', desc:'CSS is for design'},
        {id:3, title:'JavaScript', desc:'JavaScript is for interactive...'}
      ]
    }
  }

  render(){
    console.log('App render');
    var _title, _desc,_article = null;
    if(this.state.mode === 'Welcome'){
      _title = this.state.welcome.title;
      _desc  = this.state.welcome.desc;
      _article = <ReadContent title={_title} desc={_desc}></ReadContent>
    }else if(this.state.mode === 'read'){
      var i = 0;
      while(i < this.state.contents.length){
        var data = this.state.contents[i];
        if(data.id === this.state.selected_content_id){
          _title = data.title;
          _desc = data.desc;
          break;
        }
        i = i + 1;
      }
      _article = <ReadContent title={_title} desc={_desc}></ReadContent>
    }else if(this.state.mode === 'create'){
      _article = <CreateContent title={_title} desc={_desc}></CreateContent>
    }
    return (
      <div className="App">
        <Subject title = {this.state.subject.title} 
        sub = {this.state.subject.sub} 
          onChangePage = {function(){
            this.setState({mode:'Welcome'})
          }.bind(this)}
        >
        </Subject>
        <TOC onChangePage = {function(id){
          this.setState({
            mode:'read',
            selected_content_id: Number(id)
          })
        }.bind(this)} data = {this.state.contents} ></TOC>
        <Control onChangeMode={function(_mode){
          this.setState({
            mode:_mode
          });
        }.bind(this)}></Control>
        {_article}
      </div>
    );
}
}

export default App;//TOC.js를 갖다쓰는 쪽에서 TOC라는 class를 갖다쓸 수 있게 해주는 코드;

```

* ReadContent, CreateContent를 각각 생성하고,
* 컴포넌트가 들어갈 위치에  {_article}를 준다.
* mode값이 read면  ReadContent, create이면  CreateContent를 로 전환되게 한다.

# Create  구현 : form
![스크린샷 2020-07-07 오전 12 14 31](https://user-images.githubusercontent.com/42554237/86609410-de3acc00-bfe6-11ea-9503-0e9042a2e4c6.png)

submit을 했을 때, Create 기능으로 JavaScript아래에 리스트가 추가되게 한다.   

**onSubmit** : form이 submit 될 때 실행되어야 하는 이벤트를 부여한다.   HTML form의 고유 기능이다.

CreateContent.js
```python
class CreateContent extends Component {
    render(){
      return (
        <article>
              <h2>Create</h2>
              <form action="/create_process" method="post" onSubmit={function(e){
                e.preventDefault();
								this.props.onSubmit( 
              e.target.title.value,
              e.target.desc.value);
              }.bind(this)}>
                <p>
                  <input type="text" name="title" placeholder="title"></input>
                </p>
                <p>
                  <textarea name="desc" placeholder="description"></textarea>
                </p>
                <p>
                  <input type="submit"></input>
                </p>
              </form>
          </article>
      )
    }
  }
```
* form이 submit 되면 action, method의 값에 따라 전송이 되어야하지만, 
submit 될 때 onSubmit의 이벤트가 실행되어  **e.preventDefault();** 로 본래의 동작을 막는다.
* **e.target**은 form을 가리킨다.

# Create  구현 : Content 변경

push( ) 를 이용한 state 

} else if (this.state.mode === 'create') {
_article = <CreateContent onSubmit={function (_title, _desc) {
  this.max_content_id =  this.max_content_id + 1;
   this.state.contents.push(
     { id: this.max_content_id, title: _title, desc: _desc }
   );
  this.setState({
     contents: this.state.contents
    });
  }.bind(this)}></CreateContent>;
}


### push( ) vs concat( )

**push( )** 는 원본 데이터를 바꾼다. 성능을 개선할 때 굉장히 까다롭다.
**concat( )** 은 원본 데이터를 바꾸지 않는다. 성능을 개선할 때 편하다.

ex)
```python
var arr = [1,2];

arr.push(3);
console.log(arr) // [1,2,3]

var arr2 = [1,2];

var result = arr2.concat(3);
console.log(result); // [1,2,3]
console.log(arr2);//[1,2]
```