---
title: "2019 KAKAO BLIND 2 실패율"
date: 2020-09-08T22:52:30+09:00
---

[문제 풀러 가기](https://programmers.co.kr/learn/courses/30/lessons/42889)



## 문제 풀이

### 제한사항

- 스테이지의 개수 N은 `1` 이상 `500` 이하의 자연수이다.
- stages의 길이는 `1` 이상 `200,000` 이하이다.
- stages에는 1 이상 N + 1이하의 자연수가 담겨있다.
  - 각 자연수는 사용자가 현재 도전 중인 스테이지의 번호를 나타낸다.
  - 단, `N + 1` 은 마지막 스테이지(N 번째 스테이지) 까지 클리어 한 사용자를 나타낸다.
- 만약 실패율이 같은 스테이지가 있다면 작은 번호의 스테이지가 먼저 오도록 하면 된다.
- 스테이지에 도달한 유저가 없는 경우 해당 스테이지의 실패율은 `0` 으로 정의한다.



### 풀이 방법

- 각 스테이지 별 누적 인원을 만든다.
- (현재 스테이지 인원 - 다음 스테이지 인원) / 현재 스테이지 인원 로 실패율을 구한다.



### 코드

[전체 코드](https://github.com/hhk9292/algorithm/blob/master/2019KAKAOBLIND/2.py)

- 스테이지 별 누적 인원을 만드는 함수

  ```python
  def accum_list(N, stages):
      """인덱스가 낮아지는 쪽으로 값이 합쳐지는 누적 배열 리턴"""
      temp = [0] * (N + 2)  # 인덱스가 N + 1 까지 있어야 하기 때문
      for stage in stages:
          temp[stage] += 1
  
      for i in range(N, 0, -1):  # N 부터 1 까지 이동하면서 누적
          temp[i] += temp[i+1]
  
      return temp
  ```



- 실패율을 구하는 함수

  ```python
  def solution(N, stages):
      # 각 인덱스에 해당하는 스테이지에 도착한 인원이 저장된다.
      numbers = accum_list(N, stages)
      answer = [0] * (N)
      for i in range(1, N + 1):
          if numbers[i] == 0:  # 도달한 인원이 0 이면
              answer[i - 1] = 0  # 실패율 0
          else:
              # 현재 스테이지에 있는 인원 = 현재 스테이지를 도달한 인원 - 다음 스테이지를 도달한 인원
              answer[i - 1] = (numbers[i] - numbers[i+1]) / numbers[i]
  
      answer = list(enumerate(answer))
      answer.sort(key=lambda x: (-x[1], x[0]))
      answer = [i + 1 for (i, rate) in answer]
      return answer
  ```

  