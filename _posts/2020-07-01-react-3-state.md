---
title: "[React]생활코딩 3️⃣ - State"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
categories:
- React
- 생활코딩
- State
- Key
tags:
- React
- 생활코딩
- State
- Key
---

#  State 소개
*  컴포넌트의 동작을 다양하게 바꾸고 싶을때 사용자에게 제공하는 것으로,
	 React는 컴포넌트를 **외부에서 조작**할 때는 **props**를 사용하고,   
	 **내부적으로 상태를 관리**할 때는 **state**를 사용한다.   
 
*  state는 **사용자**에게 중요하고, **props**는 구현자에게 중요하다.   
		state와 props 는 철저하게 분리되어 있어야 한다.   
		사용하는 쪽과 구현하는 쪽을 철저하게 격리시켜서 양쪽의 편의성을 각자 도모하는 것이 좋은 부품을 만드는 것의 핵심이고,    
		리액트도 마찬가지이다.   
	
# 	State 사용
컴포넌트에 하드코딩으로 넣어줬던 props의 값을 state로 만들고 그 state의 값을 porps로 전달한다.   
컴포넌트가 실행될때 render() 함수보다 먼저 실행되어 **props를 초기화**시키는  **constructor( )** 함수를 사용한다.   

```python
class App extends Component{

  constructor(props){
    super(props);
    this.state = {
      subject:{title:'WEB', sub: 'World Wide Web'}
    }
  }

  render(){
    return (
      <div className="App">
        <Subject title = {this.state.subject.title} sub = {this.state.subject.sub}></Subject>    
        <TOC></TOC>
        <Content title="HTML" desc="HTML is HyperText Markup Language."></Content>
      </div>
    );
	}
}
```
상위 컴포넌트의 상태를 하위컴포넌트의 props로  전달하는 것은 얼마든지 가능하다 .


# key
복수의 elements를 생성할 때는 **key** 라는 특수한 props를 사용해야한다.

```python
import React,{Component} from 'react';
// import logo from './logo.svg';
import './App.css';
import TOC from './components/TOC';
import Content from './components/Content';
import Subject from './components/Subject';


class App extends Component{

  constructor(props){
    super(props);
    this.state = {
      subject:{title:'WEB', sub: 'World Wide Web'},
      contents:[
        {id:1, title:'HTML', desc:'HTML is for information...'},
        {id:2, title:'CSS', desc:'CSS is for design'},
        {id:3, title:'JavaScript', desc:'JavaScript is for interactive...'}
      ]
    }
  }

  render(){
    return (
      <div className="App">
        <Subject title = {this.state.subject.title} sub = {this.state.subject.sub}></Subject>
        <TOC data = {this.state.contents} ></TOC>
        <Content title="HTML" desc="HTML is HyperText Markup Language."></Content>
      </div>
    );
}
}

export default App;#TOC.js를 갖다쓰는 쪽에서 TOC라는 class를 갖다쓸 수 있게 해주는 코드;

```

state에 property를 배열로 선언하고 그 안에 객체의 형태로 값을 만든다.

<br/>

```python
import React,{Component} from 'react';



class TOC extends Component{
    render(){
        var lists = [];
        var data = this.props.data;  // TOC는 내부적으로 this.props.data를 갖고있음
        var i = 0;
        while(i < data.length){
            lists.push(<li key={data[i].id}><a href={"/content/"+data[i].id}>{data[i].title}</a></li>);
			#여러개 목록을 자동으로 생성할 때는 식별자(key={data[i].id})가 있어야함
            i = i + 1;
        }
      return (
        <nav>
              <ul>
                  {lists}
              </ul>
        </nav>
      )
    }
  }

  export default TOC;
```

<br/>
**식별자(key={data[i].id})를 주지않았을때 발생하는 error**

![스크린샷 2020-07-01 오후 11 45 42](https://user-images.githubusercontent.com/42554237/86263943-d224c800-bbfc-11ea-8482-3537b41a48f7.png)