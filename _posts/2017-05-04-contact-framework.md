---
layout: post
title: "Contacts Framework"
description: "Contacts Framework 사용법"
date: 2017-05-05
tags: [iOS, Contacts]
comments: true
---

[가나다라](https://itunes.apple.com/app/id509879186) 앱 개발을 하면서 한번씩 연락처 연동에 대한 이슈가 생겼다.  
다행히 Fabric 을 통해 본 결과 iOS8 사용자는 없었기에  
쿨하게 iOS9 로 올리면서 AddressBook.framework -> Contacts.framework 로 변경을 시도한다.  

# Contacts Framework

전화번호부 책 개념의 [CNContactStore](https://developer.apple.com/reference/contacts/cncontactstore) 안에 연락처 클래스인 [CNContact](https://developer.apple.com/reference/contacts/cncontact)가 있다.  

연락처 클래스인 CNContact는 아래와 같은 기능을 가지고 있다.  
* thread-safe
* immutable value object of contact properties

![CNContact](https://docs-assets.developer.apple.com/published/b8e77913b1/14ac15c8-1221-44a6-aaa3-061a4fc54d80.png)

## 연락처 사용권한 획득

```swift
let store = CNContactStore()

func requestAccess() {
  store.requestAccess(for: .contacts) { (granted, error) in
    if granted {
      // 연락처 사용권한 획득 성공
    } else {
      // 연락처 사용권한 획득 실패
    }
  }
}
```

## 연락처(CNContact) 받아오기

Contacts.framework 에서 연락처 정보를 받아오는 경우에는 **CNKeyDescriptor** 정보가 필요하다  
예를 들어 성, 이름 정보가 필요한경우  

```swift

let containerId = store.defaultContainerIdentifier()
let predicate = CNContact.predicateForContactsInContainer(withIdentifier: containerId)
let contacts = try? store.unifiedContacts(matching: predicate,
  keysToFetch: [CNContactFamilyNameKey, CNContactGivenNameKey])

```

위 코드처럼 keysToFetch 값에 원하는 정보의 키값을 배열로 넘겨주면 된다.
여기서 처음 보이는 **store.defaultContainerIdentifier()** 은 여러 전화번호부 책 중 기본이 되는 책의 고유 id 값이라고 보면 된다.  

즉, 다른 서비스 및 앱(Google, Facebook 등등)에 연결되어 있는 연락처에 대해서도 받아오려면

```swift

var contacts = [CNContact]()

try? store.containers(matching: nil).forEach({ (container) in
    let predicate = CNContact.predicateForContactsInContainer(withIdentifier: container.identifier)

    if let people = try? store.unifiedContacts(matching: predicate,
      keysToFetch: [CNContactFamilyNameKey, CNContactGivenNameKey]) {
        contacts.append(contentsOf: people)
    }
})

```

위와 같이 처리를 하면 모든 전화번호부 책에 있는 연락처를 받아온다고 생각하면 된다.  


## 연락처 추가/수정

[CNSaveRequest](https://developer.apple.com/reference/contacts/cnsaverequest) 라는 녀석이 있다  
연락처의 변경사항에 대해 저장을 해주는 클래스이고 사용법은 간단하다.

```swift

// Add contact
let newContact = CNMutableContact()
newContact.givenName = "John"
newContact.familyName = "Appleseed"

let saveRequest = CNSaveRequest()
saveRequest.add(newContact, toContainerWithIdentifier: nil)
try! store.execute(saveRequest)

// Modify Contact
let mutableContact = newContact.mutableCopy() as? CNMutableContact
mutableContact.givenName = "Bill"

let modifyRequest = CNSaveRequest()
modifyRequest.update(mutableContact)
try! store.execute(saveRequest)

```

처음에도 언급했듯이 CNContact 클래스는 **immutable value object** 이므로  
CNMutableContact 로 **mutableCopy** 를 한뒤에 원하는 값을 넣어주고  
CNSaveRequest를 실행시키는 방식으로 처리가 된다.

## vCard로 내보내기

플랫폼을 이동을 하기위해 사용하거나, 연락처 백업을 위해 vCard 내보내기 기능을 제공한다.  
사용법은 거의 동일하지만 연락처를 받아오는 방식이 조금 다르다.  

```swift

let request = CNContactFetchRequest(keysToFetch: [CNContactVCardSerialization.descriptorForRequiredKeys()])
var contacts = [CNContact]()
try self.store.enumerateContacts(with: request) { (contact, stop) in
    contacts.append(contact)
}

let vCard = try CNContactVCardSerialization.data(with: contacts)

```

vCard 데이터를 <mark>UIActivityViewController</mark>를 통하거나 파일로 저장시켜서 원하는 처리를 하면된다.

<hr/>
위 코드는 XCode 8.3.2, Swift 3.1 버전을 기준으로 작성되었습니다.
