---
layout: post
title: "Localized Kit"
description: "Localized Kit Mac Application"
date: 2017-05-17
tags: [Localized, macOS]
comments: true
---

## 개발동기
Localized Kit은 iOS Developers KR 카카오톡 오픈채팅방에서 질문중에 탄생을 하게 되었다.
![개발동기]({{ site.url }}/assets/images/localized_kit/motivation.png){: .center-image}  

필요성은 예전부터 나도 느끼고 있었기에, 응(?) 그러면 만들어보자~! 라는 생각으로 만들기 시작!

## 요구사항 및 필요기능
- 로컬라이즈 파일을 쉽게 불러 올 수 있어야 된다.
- Key 값이 변경되면 모든 언어의 Key값이 같이 변경이 되어야 된다.
- 추가/삭제/수정이 가능해야 된다.

#### 파일 불러오기
우선 파일 위치를 어떻게 받아오는게 편할까? 라고 생각했을때
그냥 프로젝트 폴더를 Drag & Drop으로 넣으면 알아서 똭(!) 나오자 라는 생각이 들었다.

![메인화면]({{ site.url }}/assets/images/localized_kit/empty_main.png)

여기에서 프로젝트 폴더를 넣으면

![로컬라이즈 리스트]({{ site.url }}/assets/images/localized_kit/import_main.png)

위 이미지와 같이 해당 폴더 내에 있는 <mark>strings</mark> 파일 리스트를 노출이 된다.
실제로 Framework 나 Bundle 내에 있는 strings 파일도 리스트업 되도록 작성되어 있다.
저기서 원하는 파일을 선택하게 되면 해당 strings 파일에 있는 Key, Value 값 리스트가 뜨게 된다.

![상세화면]({{ site.url }}/assets/images/localized_kit/detail_localized.png)

#### Key값 변경하기
Key 또는 Value의 변경을 원하는 해당 라인을 선택(Double Click)하면
입력창이 뜨게 되고 선택했던 값들이 먼저 들어가 있게 된다.
여기서 Key 값을 변경시에 다른 언어의 파일들도 동일하게 변경이 된다. (이 기능이 정말 꿀!!!)
Value는 각 언어별로 변경이 되는게 맞기 때문에 Value 변경시에는 해당 언어만 변경이 되도록 처리가 되어있다.

![수정입력폼]({{ site.url }}/assets/images/localized_kit/modify_form.png)

이렇게 수정을 하면!???

![수정완료화면]({{ site.url }}/assets/images/localized_kit/modify_localized.png)

이렇게 값이 변경 되는걸 확인할 수 있다.

#### 추가/삭제하기
수정은 그냥 선택을 해서 하면 되는것이고,
추가, 삭제 기능은 있어야 그나마 편하게 Localized Kit 앱만으로 관리가 된다고 생각을 했다.
둘중에 우선 구현이 편한(?) 삭제 기능부터 만들어 보았다.

##### 삭제하기  
제일 쉽게 생각할 수 있는 선택하고 <mark>delete</mark> 키를 누르면  
삭제할래? 말래? Alert떠서 선택 그리고 업데이트 된 파일을 저장한다 라고 생각을 했다.  

![삭제화면]({{ site.url }}/assets/images/localized_kit/delete_action.png)

##### 추가하기  
여기서 엄청 고민이 많이 되었다...  
1. 추가버튼을 어딘가 둘것인가??
2. 제일 밑에 그냥 입력폼이 생겨서 계속 쓸수 있게 할것인가?
3. 단축키로 입력하게 할것인가?

위 3가지 방법 중에.. 결국 3번 단축키를 선택하였다.  
<mark>'+'</mark> 단축키를 누르면 입력폼을 띄우자!!!! 라는 생각으로 작업을 했고

우선 여기서 고민해야 되는 부분은 기존에 등록되어 있던 Key는 등록이 되면 안된다! 라고 생각을 했고  
기존에 등록된 Key를 입력시에 아래와 같은 경고창이 뜨도록 처리하였다.

![추가실패화면]({{ site.url }}/assets/images/localized_kit/new_alert.png)

Key 값의 중복만 없으면 하단에 추가가 되게 되고  
여기서 하나의 기능을 더 추가해야 편하게 쓸수 있다고 생각을 했다.. 그 기능은..!?  

##### Drag 으로 순서변경하기  
분명 나열된 순서대로 strings 파일이 생성이 될텐데 순서 변경을 해야된다면?  
근데 그걸 파일을 열어서 직접 해야된다면.. 겁나 피곤할꺼 같다는 생각이 들었다..  
그래서 원하는 Row를 선택하고 Drag를 하면 순서변경이 가능하다  

## 마무리
우선 지난주 금요일(2017/5/12) 정도에 얘기가 나왔고, 짧은 시간을 내어서 한번 만들게 되었다.  
macOS 앱 개발에 능숙하진 않아서 미려한 디자인, UI는 아니지만 우선 기능적으로는 편리하다고 생각이 되고  

추후 몇가지 기능 추가 또는 소스 정리 후에 <mark>오픈소스</mark>로 공개를 생각중에 있다.  
소스가 이쁘지 않고.. macOS 코드라 더욱.. 이쁘지 않아서 계속 마음엔 걸리지만  
누군가 나에게 PR로 더 이쁜 코드를 보내줄거라 믿기 때문에!!ㅎㅎㅎ
마지막으로 파일을 다운로드 할수 있는 링크를 남기고 이만 사라지도록 하겠다...  

## Download
[Localized Kit]({{ site.url }}/assets/binary/LocalizeKit.zip)  
sign 하지 않고 export한 앱입니다.  
혹시나 외부 공유시 출처만 남겨주시길..바랍니다.  
