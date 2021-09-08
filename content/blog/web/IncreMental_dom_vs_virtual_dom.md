---
title: "Incremental DOM vs virtual DOM"
date: "20210908"
description: "Incremental DOM vs virtual DOM에 대한 글을 읽으며 생각한 점을 정리해보자."
---

# Incremental DOM vs Virtual DOM

한줄 요약:
Virtual DOM은 js object 기반으로 모델링된 DOM 전체를 상태변화가 있을때 마다 생성해서 비교하기 때문에 빠르지만 메모리를 많이 사용하고 Incremental DOM은 실제 DOM에 접근해 virtual dom 보다는 느리지만 메모리를 효율 적으로 사용한다.