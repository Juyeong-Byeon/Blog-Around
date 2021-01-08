---
title: Java - 자바의 조건문, 반복문, 배열 객체
date: "20200420"
description: "Java의 가장 기초적인 문법 part 2 조건문, 반복문, 배열 객체 "
---

# Java 기본문법: 조건문, 반복문, 배열객체

## 조건문

### if문 

```java
if(true){// 괄호 안이 참일시 실행 

}

if(false){

}else{// 거짓일시 실행

}

if(){

}else if(){

}else{

}
```
c와 동일

### switch case 문

``` java

switch (a){
    case 1:
        break;
    case 2:
        break;
    case 3:
        break;
    default:
        break;  
}

```

### for 문

```java
for(int i=0;i<10;i++){
    System.out.println(i);
}
```

### while 문

```java
while(){

}

do{

}while();
```

### break, continue
break: 하나의 loop을 빠져나감
continue: 다음 명령을 실행하지 않고 루프의 맨 앞으로 돌아감.

## 배열 객체

[] 를 이용해 하나의 변수로 여러 개의 변수를 표현 하는 것이다. 
자바에서는 객체이다. 따라서 row의 길이가 각각 다 다른,  다차원 배열을 만들 수도 있고,각각이 객체이기 때문에 배열에 타입이 다른 값을 저장 할 수 있다. 

```java
int arr1[];
int[] arr2;
//둘다 가능하다. 
```
[]내에서는 첨자 사용을 지정하지 않아도 되나, 배열 선언만으로는 배열이 메모리에 할당 되지 않는다. 따라서 직접 const한 값을 지정해주거나, new를 이용해 메모리 공간을 할당 해 주어야 한다. 

```java
int arr1[]={1,2,3,4};
int[] arr2={1,2,3,4};
//둘다 가능하다. 
```
new 를 이용할 경우 다음과 같이 해야한다. 

```java
int arr1[]=new int[10];
int []arr2=new int[10];
int arr3[];
arr3=new int[10];
```

