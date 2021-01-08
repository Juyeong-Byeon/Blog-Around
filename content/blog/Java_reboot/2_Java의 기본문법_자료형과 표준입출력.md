---
title: Java - 자바의 자료형과 표준 입출력
date: "20200420"
description: "Java의 가장 기초적인 문법 "
---

# Java의 기본문법1: 자료형과 표준 입출력

자바 프로그램의 가장 기본적인 구조는 다음과 같다. 

```java
public class Java_EX_01{
    public static void main(String[] args){
        System.out.println("Hello World!");
    }
}
```
## import 문장
java.lang을 재외한 모든 패키지를 가져올때는 import 문장을 이용해 가져와야 한다. 
```java
import java.util.Scanner;//입력시 사용하는 유틸리티 패키지를 포함
import java.util.*// util 패키지 안의 모든 class를 본 패키지에 포함
```

## 주석 
다른 언어들과 다르지 않다. 블록주석은 "/**/"이고, 줄 주석은 "//"으로 처리 할 수 있다. 

## 기본 입출력 스트림

데이터를 외부로 출력하거나 외부로부터 입력받는 일련의 과정이다. 

표준 입출력 스트림은 다음과 같이 사용할 수 있다. 

출력
```java
System.out.print("내용");//내용
System.out.println("내용");//내용\n
System.out.printf("%d",10);//10 c의 printf()와 같이 출력지정자 사용가능 
System.err.println("에러는 없습니다.");//에러문구 출력
```
출력필드에는 out, in, err 등이 있는데, System. 안에 static으로 정의 되어있다. 



입력
```java
char c=system.in.read();//단 한 문자만을 char형태로 읽어온다.

import java.util.Scanner;

java.util.Scanner input=new java.util.Scanner(System.in);
// Scanner 객체를 사용할 변수에 초기화를 해주어야 함.

String a=input.next();//다음 공백문자 전까지 string형태로 읽어온다.
int num=input.nextInt();// int가 끝날때 까지 읽어온다. 
float num2=input.nextFloat();
String line=input.nextLine();//줄을 모두 읽어들여 String으로 반환.
```

## 변수
프로그램이 수행되는 과정에서 변할 수 있는 값이며 데이터를 기억시킬 수 있는 장소이다. 

여느 c 계열 언어들과 다르지 않다. 단 String 이 원시 타입이 아닌, String 객체라는 점을 기억해야 겠다. 

## casting (형 변환)

다음과 같은 꼴로 변환한다. : 
(데이터형) 변수명
```java
float a=1.233;
int b=(int)a;//1 명시적 변환 cast연산자 이용
```
묵시적 변환은 따로 explicit 한 문법이 필요 없다. 변환될 자료형의 표현 범위가 변환할 자료형의 표현 범위보다 작을 때, 묵시적 변환이 허용된다. 

    다음과 같은 순서로 가능하다. 
         
    byte->[short,char]->int->long->float->double

## 연산자 

연산자란 변수와 상수등 데이터의 개상에 대해 연산동작을 행하는 기호를 뜻한다. 이따 연산의 대상을 피연산자(operand)라고 한다. 

1. 단항연산자
```java
int a=0;
a++//1
a--//0
++a//1
--a//0
//전위 후위의 시점 차이가 있음.
```
2. 이항 연산자
```
+, -, /, %
```
3. 삼항 연산자.

여느 c계열 언어들과 다르지 않다. 




