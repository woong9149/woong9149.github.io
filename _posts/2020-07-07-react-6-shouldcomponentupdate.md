---
title: "[React]생활코딩 6️⃣ - shouldComponentUpdate"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
categories:
- React
- 생활코딩
- TIL
tags:
- React
- 생활코딩
- TIL
---

# shouldComponentUpdate
push( ) 와 concat( )중에서 state 값을 바꿀 때 concat( )을 사용해야 하는 이유는
**shouldComponentUpdate( )** 함수 때문이기도 하다.   

```python
   class TOC extends Component{
    shouldComponentUpdate(newProps, newState){
      console.log('===>TOC render shouldComponentUpdate',
      newProps.data, this.props.data);
      if(this.props.data === newProps.data){
        return false;
      }
      return true;
    }

    render(){
        var lists = [];
        var data = this.props.data; 
        var i = 0;
        while(i < data.length){
            lists.push(
            <li key={data[i].id}>
              <a 
              href={"/content/"+data[i].id}
              data-id={data[i].id}
              onClick={function(e){
                e.preventDefault();
                this.props.onChangePage(e.target.dataset.id);
              }.bind(this)}>
              {data[i].title}</a>
            </li>);
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
```

* App 컴포넌트가 랜더링 될 때, 그 속에 update가 없는 모든 컴포넌트가 랜더링 되는 것은 비효율적이다.   
* 해당 컴포넌트에 update된 내용이 있는지 없는지 체크해서 render( )의 실행 여부를 정하는  기능을 가진 것이  **shouldComponentUpdate( )** 함수다.   
* **push( )** 를 사용하면 원본 데이터가 변하기 때문에 shouldComponentUpdate( )에서**newProps.data와 this.props.data가 같고**,   
		**concat( )** 을 사용하면 원본 데이터가 그대로 유지되기때문에 **newProps.data와 this.props.data가 다르다**.
		
# immutable(불변)
concat( ) 대신 push( ) 를 쓰고싶을 때는, 배열이나 객체의 원본을 유지하면서 복제한 데이터를 생성할 수 있다.   
**배열**   
**Array.from( )**   

```python
	var a = [1,2];
	vat b = Array.from(a); // b = [1 , 2 ]
	console.log(a ===b); //false
```

<br/>
**객체**   
**Object.assign( )**   

	```python
var a = {name:'egoing'};
	var b =	Object.assign({ }, a ); //{ } 자리에는 빈 객체나 새로운 객체가 가능함.
	b.name = 'leezche'
	console.log(a ===b); // false
```

> **자바스크립트 배열의 단점**   
> push( ), concat( )등 명령어들의 대상에 대한 일관성이 떨어진다.