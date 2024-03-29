---
title: "multipart/form-data"
date: "20211222"
description: "multipart/form-data가 무엇일까?"
---

# 들어가며

이번에 미리캔버스 서비스에 폰트, 로고 업로드 기능이 생기며 사진과 폰트의 업로드를 구현해야 할 일 들이 생겼다.
이 작업을 하면서 form tag에서 선택된 폰트파일과 사진을 FormData의 필드로 실어 직접 https request의 body에 포함시켜 서버요청을 하게 되었다.
form 태그를 이용한 방식과 어떤 차이가 있는지 MDN을 확인해보게 되었다.
확인 한 결과 MDN에 따르면 FormData 인터페이스는 form tag에서 multipart/form-data를 enctype으로 사용한 것과 같은 포멧의 데이터를 생성해준다고 한다.
이번 글에서는 multipart/form-data가 무엇인지 알아보려고 한다.

# <form/> enctype 속성

multipart/form-data는 일단 form tag의 enctype중 하나이다. 일반적으로 파일을 업로드 할 때는
아래와 같은 방식으로 enctype를 설정해주게 된다.

```html
<form method="post" enctype="multipart/form-data">
  <input type="file" />
</form>
```

하지만 나는 이번 피쳐에서 폼을 화면에 드러내고 있지 않기 때문에
내부적으로만 input 태그를 생성해준 후 click이벤트를 fire시켜서 파일 선택과정을 진행하고 있어 아래와 같은 방법을 사용하지는 않았다.
나는 위 과정에서 받아온 FileList 객체를 FormData를 생성한 후 내부에 key, value 방식으로 set해준 후 post의 body에 실어 전송을 해주었다.

이 과정에서 enctype 가 무엇인지에 대해 궁금증이 생겼고, FormData에는 왜 enctype을 지정해주지 않는지 궁금해져서 알아보게 되었다.

enctype은 서버에 제출할 때 사용되는 MIME (Multipurpose Internet Mail Extensions) type 이라고 한다.
가능한 타입 들은 아래와 같이 3가지가 있다.

application/x-www-form-urlencoded: 디폴트 값으로서, 폼데이터를 URL-Encode 즉url safe 하게 변환 시키겠다는 뜻이다.

multipart/form-data: 여러 형태의 데이터를 제출할 것임을 명시

text/plain: 공백문자를 +로 인코딩 하는 것을 의미.

그리고 FormData에서 제공하는 인터페이스를 사용하게 되면 multipart/form-data를 사용하는 것과 같은 형태의 데이터를 만들어 준다고 한다.
때문에 이미 enctype이 multipart/form-data로 되어있기 때문에 추가적인 설정이 필요 없는 것이다.

# ok 근데 인코딩 방식이 서버에 전달하는거랑 뭔상관?

일단 HTTP req의 형태를 한번 보자
[http](./assets/http.png)

http프로토콜의 POST 요청은 4개의 영역으로 이루어져있다. 이때 서버가 해당 요청을 받은 뒤 파일을 디코드 하기 위해서 Header의 Content-type 필드에 Message Body에 들어가는 payload가 어떤 MIME 타입인지 명시해야한다.
이때 위에서 말한 3개의 MIME타입을 넣어주게 되는데, 위 정보에 따라서 디코딩을 하기 때문에 정상적으로 디코딩을 위해서는 이를 지정해줄 필요가 있는 것이다.
그중에 특히 파일 업로드에 multipart/form-data를 사용하는 이유는 여러 파일을 업로드 할 때 파일의 파트별로 바이너리를 읽어들이게 되는데,여러 파일의영역과 파일의 형식을 알맞게 읽기 위해서 boundary 별로 파일을 나눠줘야 함을 명시하는 것이다.

```http
POST /test.html HTTP/1.1
Host: example.org
Content-Type: multipart/form-data;boundary="boundary"

--boundary
Content-Disposition: form-data; name="field1"

value1
--boundary
Content-Disposition: form-data; name="field2"; filename="example.txt"

value2
--boundary--
```
