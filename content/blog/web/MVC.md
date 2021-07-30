---
title: "MVC"
date: "20210729"
description: "MVC 아키텍쳐 정리 해보자."
---

<h5 style='color:red;'> 20210729 작성중<h5>

# MVC
MVC(Model-View-Controller) 모델은 아키텍쳐 패턴으로써 소프트웨어 서비스에서 관심사를 분리해 코드의 재사용성을 높히고 유지보수를 쉽게 하기위해 사용한다. 사실 MVC는 정말 널리알려지고, 아키텍쳐 패턴을 처음 배울때 한번쯤은 꼭 공부해보는 아키텍쳐 패턴이다. 본인도 현재 다니고 있는 회사에서 MVC패턴을 지향해 프론트엔드코드를 짜고 있기 때문에 처음 회사에 들어와서 아키텍쳐 패턴을 공부했던것이 바로 MVC이다. 이번 글에서는 이 MVC 패턴을 사용하는 이유와 M,V,C 각각의 역할을 알아보고 간단한 MVC 구조 코드를 작성해볼 것이다. 

## MVC의 지향점
먼저 MVC의 간단한 정의와 적용하는 이유를 알아보자. MVC는 관심사를 Model-View-Controller 분리하는 아키텍쳐 패턴으로써 이를 통해서 코드의 재사용성을 높히고 유지보수를 쉽게 하기위해 사용한다. 관심사 분리는 Model,View,Controller단위로 나누어지게 된다. 구현체마다 약간씩 역할이 다르지만 주된 역할은 다음과 같다. 


![MVC-출처 MDN](./assets/model-view-controller-light-blue.png)

Model은 데이터와 해당 데이터에 대한 비즈니스 로직을 가지고 있고, View는 이 데이터를 보여주는 역할을 한다. 그리고 Controller는 이 둘 사이에서 View를 통해 입력이 들어오면 모델에게 전달해 Model을 업데이트 해주고 이를 View에 반영해주는 역할을 한다.


 왜 이렇게 관심사를 분리했는데 유지보수성과 재사용성이 좋아질까? 상황에 따라 다르겠지만. 코드에 구조가 전혀 없다고 가정해보자. 코드의 구조가 전혀 없는 todolist app을 한번 보자.

 ```html

 <!DOCTYPE html>
<html lang="en">
<head>
   
    <title>Document</title>
</head>
<body>
    <input id=todoInput type='text'>
    <button id='addButton' onClick="onClickAddTodo">
    추가
    </button>

    <button id='deleteButton' onClick="onClickAddTodo">
    삭제
    </button>

    <button id='updateButton' onClick="onClickAddTodo">
    수정
    </button>

    <div id='todoList'>

    </div>
     <script type='text/javascript'>

        const todoApp={
            user:undefined
            todoList:undefined
        }
        function onClickAddTodo(){
            //...some code
            //view 처리
            const todoListContainer=document.getElementById('todoList');
            //view 처리
            const todoInput=document.getElementById('todoInput');
            //데이터 처리
            todoApp.todoList.append(new Todo(todoInput.value));
            //view 처리
            todoApp.todoList.forEach((todo)=>{
                const newTodo=document.createElement('div');
                newTodo.innerText=todo.value

                const wrapper=document.createElement('div');
                wrapper.appendChild(newTodo);
                
                todoList.appendChild(newTodo);
                todoListContainer.appendChild();
            })   
        }
        function onClickDelete(){
            //...some code
        }
        function onClickupdateButton(){
            //...some code
        }
    </script>
</body>
</html>
 ```
코드를 보면 사용자에게 보여지는 view를 그리는 로직, 데이터를 처리하는 로직, 둘을 업데이트하고 제어하는 로직이 모두 물리적으로 한 파일에 있을뿐더러 관련된 로직들이 섞여있다.때문에 위 코드를 볼때 각각의 코드가 무엇을 하는지 파악이 어렵다. 여를 들어 todolist의 정보를 저장하는 todoApp object와 각각의 함수들이 어떤 관계를 가지는지 알기도 어렵고 각각의 함수도 view로직이 섞여있어 todoApp object와만 관계가 있다고 하기도 어렵다. 이런 경우 코드간의 영향이 커지고 이에따라 유지보수성과 재사용성이 안좋아진다. 예를 들어 todo를 추가할 때 추가되는 view 모습을 바꾸고 싶다면 onClickAddTodo 함수를 수정해야하는데 내부에 데이터의 처리와 view 모습에 대한 정의가 함께 있기 때문에 변경에 따라 영향을 받을 확률이 커질 것이다. 

이런 문제를 해결하기 위해서 MVC에서는 데이터 그리고 이를 처리하는 로직의 단위인 Model, 화면을 구성하는 단위인 View, 둘을 제어하는 로직인 Controller로 관심사를 분리해 관리한다. 


## 예시와 적용
앞선 부분에서 구조가 없는 todolist를 보면서 구조화의 필요성과 MVC 적용이유를 설명했다. 이번엔 list app에 MVC를 적용해서 각각의 Model,View,Controller를 설명하고 코드의 재사용성과 유지보수성을 어떻게 개선시키는지 확인해보자.



### Model
 보통 Model은 이렇게 설명한다. "소프트웨어가 알아야할 데이터에 대한 정의와 비즈니스 로직을 담당한다." 하지만 이런 설명은 View나 Controller에 대한 설명보다 직관적이지 못해서 일반적으로 이해하기 힘든 부분인 것 같다. 하지만 이해하고 보면 별게 아니다. 아래 코드를 보자.

 ```TS

class TodoApp{
    constructor(
        //data에 대한 정의
        private user:User,
        private todoList:Todo[]
    ){
        //
    }
    //하위는 비즈니스 로직들.
    public getTodoList():Todo[]{
        return this.todoList;
    }

    public append(todo:Todo):void{
        this.todoList.append(todo);
    }

    public update(changedTodo:Todo):void{
        this.todoList=this.todoList.map((todo)=>{
            if(todo.id===changedTodo.id)return changedTodo
            else return todo;
        })
    }

    public delete(id:number):void{
        this.todoList=this.todoList.filter((todo)=>todo.id!==id);
    }
}
 ```

 이 코드는 todolist를 저장하고, 특정 로직을 통해서 저장된 데이터에 대해 처리를 해주는 앱의 모델이다. 여기서 다시한번 "소프트웨어가 알아야할 데이터에 대한 정의와 비즈니스 로직을 담당한다." 이 문장을 보자. 여기서 "데이터에 대한 정의는" Todo List app 이 가져야할 데이터인 유저 정보와 (todolist)할일에 대한 정보이다. 
 ```TS
 ... some code
    constructor(
        private user:User,
        private todoList:Todo[]
    ){

    }
  ...
 ```

 그리고 아래처럼 앞에서 정의한 데이터에대한 처리를 하는 로직을 모델에 함께 정의하는데, 이게 바로 비즈니스 로직이다. 즉 해당 데이터를 정책에 맞게 처리하는 로직이 비즈니스 로직이다. 
```TS
 public getTodoList():Todo[]{
        return this.todoList;
    }

    public append(todo:Todo):void{
        this.todoList.append(todo);
    }

    public update(changedTodo:Todo):void{
        this.todoList=this.todoList.map((todo)=>{
            if(todo.id===changedTodo.id)return changedTodo
            else return todo;
        })
    }

    public delete(id:number):void{
        this.todoList.filter((todo)=>todo.id!==id);
    }
```
위 코드에서 알 수 있듯이 모델은 view나 controller등에 대한 내용은 전혀 알지 못한다. 때문에 모델에은 View나 controller의 변화와 상관없이 독립성을 유지할수 있다. 또한 View와 Controller또한 모델의 비즈니스 로직이 변화하더라도 인터페이스만 유지되면 영향을 받지 않기때문에 코드의 유지보수가 좋아진다고 볼수 있다. 

### View

```TS


```


### Controller



## 한계 


## 결론
