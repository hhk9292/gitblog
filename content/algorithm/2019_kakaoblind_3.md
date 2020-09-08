---
title: "2019 KAKAO BLIND 3 후보키"
date: 2020-09-08T22:52:35+09:00
---

[문제 풀러 가기](https://programmers.co.kr/learn/courses/30/lessons/42890)



## 문제 풀이

### 제한사항

- relation은 2차원 문자열 배열이다.
- relation의 컬럼(column)의 길이는 `1` 이상 `8` 이하이며, 각각의 컬럼은 릴레이션의 속성을 나타낸다.
- relation의 로우(row)의 길이는 `1` 이상 `20` 이하이며, 각각의 로우는 릴레이션의 튜플을 나타낸다.
- relation의 모든 문자열의 길이는 `1` 이상 `8` 이하이며, 알파벳 소문자와 숫자로만 이루어져 있다.
- relation의 모든 튜플은 유일하게 식별 가능하다.(즉, 중복되는 튜플은 없다.)



### 풀이 방법

- 특정 키가 후보키인지 확인
  - 키 조합을 만들고(1개부터 column의 수 까지 나올 수 있음)
  - 검사하려는 키 조합이 후보키를 포함하고 있는지 확인
  - 후보키가 될 수 있는지 확인



### 코드

[전체 코드](https://github.com/hhk9292/algorithm/blob/master/2019KAKAOBLIND/3.py)

- 키 조합을 만들기 위해 itertools의 combinations 이용

  ```python
  # 후보키의 조합을 만들기 위해 combinations 사용
  from itertools import combinations
  
  n = len(relation[0])  # column 의 개수
  # 후보키는 1 개부터 n 개까지 나올 수 있음
  for l in range(1, n+1):
      keys = combinations(range(n), l)  # 0 ~ n-1 중에 l개 선택
  ```



- 후보키를 포함하는지 확인

  ```python
  # 후보키가 저장 되는 리스트
  candidate_keys = []
  
  for key in keys:
      set_keys = set(key)
      flag = True
      # 후보키 리스트에 저장되어 있는 후보키들과 비교
      for candidate_key in candidate_keys:
          # 현재 검사하려는 키가 이미 등록된 후보키를 포함하고 있으면 반복문 탈출
          if candidate_key & set_keys == candidate_key:
              flag = False
              break
              
      if not flag:
          continue
  ```

  

- 후보키가 될 수 있는지 확인

  ```python
  def check(relation, key):
      """후보키가 될 수 있는지 검사"""
      temp = set()
      for row in relation:
          candidate = tuple(row[i] for i in key)
          if candidate in temp:
              return False
          else:
              temp.add(candidate)
      return True
  ```

  ```python
  # solution 함수 내부
  if check(relation, key):
      candidate_keys.append(set_keys)
  ```

  

