---
title: "2020 KAKAO BLIND 6 외벽 점검"
date: 2020-09-06T20:31:54+09:00
---

[문제 풀러 가기](https://programmers.co.kr/learn/courses/30/lessons/60062)



## 문제 풀이

### 제한사항

- n은 1 이상 200 이하인 자연수입니다.
- weak의 길이는 1 이상 15 이하입니다.
  - 서로 다른 두 취약점의 위치가 같은 경우는 주어지지 않습니다.
  - 취약 지점의 위치는 오름차순으로 정렬되어 주어집니다.
  - weak의 원소는 0 이상 n - 1 이하인 정수입니다.
- dist의 길이는 1 이상 8 이하입니다.
  - dist의 원소는 1 이상 100 이하인 자연수입니다.
- 친구들을 모두 투입해도 취약 지점을 전부 점검할 수 없는 경우에는 -1을 return 해주세요.



### 풀이 방법

- weak, dist 배열의 크기가 크지 않으므로 완전 탐색을 이용
  - 점검하러 가는 친구들의 수를 정한다.
  - 친구들의 순서를 정한다.
  - 원형 외벽을 돌려가며 수리할 수 있는지 확인
    - 예를들어 n=12일 때 [1, 5, 6, 10]을 검사하고 나서 [5, 6, 10, 13] ... 이런식으로 검사



### 코드

[전체 코드](https://github.com/hhk9292/algorithm/blob/master/2020KAKAOBLIND/6.py)

- 외벽을 점검하는 함수

  ```python
  def check(friends, wall):
      
      i = 0  # 점검 하려는 외벽의 인덱스
  
      for friend in friends:
          f = wall[i] + friend  # 점검할 수 있는 최장 거리 외벽
          if f >= wall[-1]:  # 모든 외벽을 점검하면
              return True
  
          else:
              # 점검할 외벽을 찾아서 이동
              for j in range(i, len(wall)):
                  if wall[j] > f:
                      i = j
                      break
      return False
  ```



- 외벽을 점검하는 데 필요한 최소 인원을 반환하는 함수

  ```python
  def solution(n, weak, dist):
      # 친구들의 순서를 정하기 위해 permutations 이용
      from itertools import permutations
      flag = True
      # 점검하러 가는 친구들의 수
      for friends_number in range(1, len(dist)+1):
          # 그 수를 가지고 순서 정하기
          friends_permu = permutations(dist, friends_number)
          for friends in friends_permu:
              # 원형 외벽이기 때문에 배열을 돌려가며 해야함
              for i in range(len(weak)):
                  left = weak[:i]
                  right = weak[i:]
                  wall = right + [elem + n for elem in left]
                  if check(friends, wall):
                      answer = friends_number
                      flag = False
                      break
  
              if not flag:
                  break
  
          if not flag:
              break
  
      else:
          answer = -1
  
      return answer
  ```

  

