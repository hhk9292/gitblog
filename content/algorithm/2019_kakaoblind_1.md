---
title: "2019 KAKAO BLIND 1 오픈채팅방"
date: 2020-09-08T22:52:26+09:00
---

[문제 풀러 가기](https://programmers.co.kr/learn/courses/30/lessons/42888)



## 문제 풀이

### 제한사항

- record는 다음과 같은 문자열이 담긴 배열이며, 길이는 `1` 이상 `100,000` 이하이다.
- 다음은 record에 담긴 문자열에 대한 설명이다.
  - 모든 유저는 [유저 아이디]로 구분한다.
  - [유저 아이디] 사용자가 [닉네임]으로 채팅방에 입장 - Enter [유저 아이디] [닉네임] (ex. Enter uid1234 Muzi)
  - [유저 아이디] 사용자가 채팅방에서 퇴장 - Leave [유저 아이디] (ex. Leave uid1234)
  - [유저 아이디] 사용자가 닉네임을 [닉네임]으로 변경 - Change [유저 아이디] [닉네임] (ex. Change uid1234 Muzi)
  - 첫 단어는 Enter, Leave, Change 중 하나이다.
  - 각 단어는 공백으로 구분되어 있으며, 알파벳 대문자, 소문자, 숫자로만 이루어져있다.
  - 유저 아이디와 닉네임은 알파벳 대문자, 소문자를 구별한다.
  - 유저 아이디와 닉네임의 길이는 `1` 이상 `10` 이하이다.
  - 채팅방에서 나간 유저가 닉네임을 변경하는 등 잘못 된 입력은 주어지지 않는다.



### 풀이 방법

- 각 이벤트 별로 문구를 저장한다.
- uid를 키, 닉네임을 value로 하는 딕셔너리를 만들어 닉네임이 변경될때마다 업데이트 한다.
- 이벤트가 저장된 리스트를 순회하면서 uid를 닉네임으로 변경해준다.

### 코드

[전체코드](https://github.com/hhk9292/algorithm/blob/master/2019KAKAOBLIND/1.py)

- 딕셔너리

  ```python
  # uid 를 key 로 하고 name 을 value 로 하는 딕셔너리
  names = {}
  ```

  

- 이벤트 저장

  ```python
  temp = []  # 이벤트가 저장될 리스트
  for event in record:
      # 각 event 를 공백을 기준으로 나눈다.
      event = event.split(" ")  # ex) ['Enter', 'uid1234', 'Muzi']
      if event[0] == 'Enter':
          temp.append([event[1], "님이 들어왔습니다."])
          names[event[1]] = event[2]
      elif event[0] == 'Leave':
          temp.append([event[1], "님이 나갔습니다."])
      else:
          names[event[1]] = event[2]
  ```

  

- uid를 닉네임으로 변경

  ```python
  answer = []
  for ans in temp:
      ans[0] = names[ans[0]]
      ans = ''.join(ans)
      answer.append(ans)
  ```

  

