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


 왜 이렇게 관심사를 분리했는데 유지보수성이 좋아질까? 상황에 따라 다르겠지만. 코드에 구조가 전혀 없다고 가정해보자. 


## 예시와 적용
앞선 부분에서 MVC 적용이유와 각각의 역할을 설명 했다. 이를 정말 기초적인 앱인 TODO list app에 적용해서 각각의 Model,View,Controller를 설명해보려고 한다.  

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
        this.todoList.filter((todo)=>todo.id!==id);
    }
}
 ```

 이 코드는 todolist를 저장하고, 특정 로직을 통해서 저장된 데이터에 대해 처리를 해주는 코드이다. 여기서 다시한번 "소프트웨어가 알아야할 데이터에 대한 정의와 비즈니스 로직을 담당한다." 이 문장을 보자.

### View


### Controller