---
title: "[React]생활코딩 7️⃣- update & delete"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
categories:
- react
- 생활코딩
- TIL
tags:
- react
- 생활코딩
- TIL
---

# App.js 리팩토링
```python
class App extends Component {
  constructor(props){
    super(props);
    this.max_content_id = 3;
    this.state = {
      mode:'read',
      selected_content_id:1,
      Subject: {title:'Web', sub:'World Wide Web!'},
      welcome: {title:'welcome', desc:'hello, React!!'},
      contents: [
        {id:1, title:'HTML', desc:'HTML is for information'},
        {id:2, title:'CSS', desc:'CSS is for design'},
        {id:3, title:'JavaScript', desc:'JavaScript is for interactive'}
      ]
    }
  }
  getReadContent(){
    var i = 0;
      while(i < this.state.contents.length){
        var data = this.state.contents[i];
        if(data.id === this.state.selected_content_id){
          return data;
        }
        i = i + 1 ;
      }
  }
  getContent(){
    var _title, _desc, _article = null;
    if(this.state.mode === 'welcome'){
      _title = this.state.welcome.title;
      _desc = this.state.welcome.desc;

      _article = <ReadContent title={_title} desc={_desc}></ReadContent>

    } else if(this.state.mode === 'read'){

      var _content = this.getReadContent();

      _article = <ReadContent title={_content.title} desc={_content.desc}></ReadContent>

    } else if(this.state.mode === 'create'){

      _article = <CreateContent onSubmit={function(_title, _desc) {
        
        this.max_content_id = this.max_content_id +1;
        var _contents = this.state.contents.concat(
          {id:this.max_content_id, title:_title, desc:_desc}
        );
        this.setState({
          contents : _contents
        });
        console.log(_title, _desc);
      }.bind(this)}></CreateContent>

    } else if(this.state.mode === 'update'){
      
      _content = this.getReadContent();
      _article = <UpdateContent data={_content} onSubmit={function(_title, _desc) {
        
        this.max_content_id = this.max_content_id +1;

        var _contents = this.state.contents.concat(
          {id:this.max_content_id, title:_title, desc:_desc}
        );
        this.setState({
          contents : _contents
        });
        console.log(_title, _desc);
      }.bind(this)}></UpdateContent>

    } else if(this.state.mode === 'delete'){

      _article = <DeleteContent></DeleteContent>

    }
    return _article;
  }
  render(){
    console.log('App render');
    
    return (
      <div className="App">
        <Subject 
        title={this.state.Subject.title} 
        sub={this.state.Subject.sub}
        onChangePage={function(){
          this.setState({mode : 'welcome'});
        }.bind(this)}>
        </Subject>
        <TOC 
        onChangePage={function(id){
          this.setState({
            mode:'read',
            selected_content_id:Number(id)
          });
        }.bind(this)}
        data={this.state.contents}>
        </TOC>
        <Control onChangeMode={function(_mode) {
          this.setState({
            mode : _mode
          });
        }.bind(this)}></Control>
        {this.getContent()}
      </div>
    );
  }
}
```
* render() 함수내에서 mode에 따라 컴포넌트를 변경시켜주는 부분을 getContent( ) 함수로 분리시켜준다.
* 선택된 id값과 일치하는 data를 가져오는 부분을 getReadContent( ) 함수로 분리시켜준다.

# update 구현: form 작업
UpdateContent.js
```python
import React, { Component } from 'react';

class UpdateContent extends Component{
    constructor(props){
      super(props);
      this.state = {
        title:this.props.data.title,
        desc:this.props.data.desc,
        id:this.props.data.id
      }
      this.inputFormHandler = this.inputFormHandler.bind(this);
    }
    inputFormHandler(e){
      this.setState({[e.target.name]:e.target.value});
    }
    render(){
      console.log('UpdateContent render');
      return (
        <article>
            <h2>Update</h2>
            <form action="/create_process" method="post"
              onSubmit={function(e){
                e.preventDefault();
                this.props.onSubmit(
                 this.state.id,
                 this.state.title,
                 this.state.desc
                );
              }.bind(this)}
            >
              <input type="hidden" name="id" value={this.state.id}></input>
              <p>
                <input 
                  type="text" 
                  name="title" 
                  placeholder="title"
                  value={this.state.title}
                  onChange={this.inputFormHandler}
                ></input>
                </p>
              <p>
                <textarea 
                  onChange={this.inputFormHandler} 
                  name="desc" 
                  placeholder="description" 
                  value={this.state.desc}></textarea>
              </p>
              <p>
                <input type="submit"></input>
              </p>
            </form>
        </article>
      );
    }
  }

export default UpdateContent;

```

* input태그와 textarea태그에서 onChange 이벤트가 발생했을 때, 실행 할 inputFormHandler( )메소드를 constructor안에 선언한다

```python
constructor(props){
      super(props);
      this.state = {
        title:this.props.data.title,
        desc:this.props.data.desc,
        id:this.props.data.id
      }
      this.inputFormHandler = this.inputFormHandler.bind(this); // UpdateContent가 로드될때 this를 binding
    }
    inputFormHandler(e){//constructor 외부에 함수 선언
      this.setState({[e.target.name]:e.target.value});
    }

```

APP.js
```python
else if(this.state.mode === 'update'){
      _content = this.getReadContent();
      _article = <UpdateContent data={_content} onSubmit={
        function(_id, _title, _desc) {
        var _contents = Array.from(this.state.contents); //원본 배열을 유지하고, 원본을 복제한 배열 생성

        var i = 0;
        while(i < _contents.length){
          if(_contents[i].id === _id){
            _contents[i] = {id:_id, title:_title, desc:_desc};
            break;
          }
          i = i+1;
        }
        this.setState({
          contents : _contents,
          mode:'read'
        });
        console.log(_title, _desc);
      }.bind(this)}></UpdateContent>

}

```

* UpdateContent 컴포넌트에서 넘어온 _id,_title,_desc를 받아 반복문을 이용해  contents 중 id 값이 _id와 일치하는 요소의 값에 각각 _title, _desc 를 대입해준다.

# Delete 구현
*  버튼을 눌렀을 때는, confirm창을 띄워서 사용자의 실수를 방지해준다.   
*  **splice( )** 메소드를 이용해 배열의 삭제할 요소를 삭제하고, 나머지 요소들로 setState 해준다.

```python
<Control onChangeMode={function(_mode) {
          if(_mode === 'delete'){
            if(window.confirm("realy?")){
              var _contents = Array.from(this.state.contents);
              var i = 0;
              while(i < this.state.contents.length){
                if(_contents[i].id === this.state.selected_content_id){
                  _contents.splice(i,1);
                  break;
                }
                i = i + 1;
              }
              this.setState({
                mode:'welcome',
                contents:_contents
              });
              alert('deleted!');
            }
          } else {
            this.setState({
              mode : _mode
            });
          }
          
}.bind(this)}></Control>
```