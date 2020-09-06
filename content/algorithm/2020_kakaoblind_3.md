---
title: "2020 KAKAO BLIND 3 자물쇠와 열쇠"
date: 2020-09-06T20:15:27+09:00
---

[문제 풀러 가기](https://programmers.co.kr/learn/courses/30/lessons/60059)



## 문제 풀이

### 제한사항

- key는 M x M(3 ≤ M ≤ 20, M은 자연수)크기 2차원 배열입니다.
- lock은 N x N(3 ≤ N ≤ 20, N은 자연수)크기 2차원 배열입니다.
- M은 항상 N 이하입니다.
- key와 lock의 원소는 0 또는 1로 이루어져 있습니다.
  - 0은 홈 부분, 1은 돌기 부분을 나타냅니다.



### 풀이 방법

- 열쇠가 자물쇠 외부에서도 이동할 수 있기 때문에 더 큰 2차원 배열을 생성한다.
- 열쇠를 이동 및 회전시키면서 자물쇠를 열 수 있는지 확인
- key: 열쇠 배열, n: 열쇠의 크기, lock: 자물쇠 배열, m: 자물쇠 크기, board: 열쇠가 이동하는 전체 배열



### 코드

[전체 코드](https://github.com/hhk9292/algorithm/blob/master/2020KAKAOBLIND/3.py)

- 열쇠를 시계방향으로 90도 돌리는 함수

  ```python
  def rotate_key(key, n):
      temp = [[0] * n for _ in range(n)]
      for i in range(n):
          for j in range(n):
              temp[j][n-i-1] = key[i][j]
      return temp
  ```

  

- 열쇠가 자물쇠를 열 수 있는지 확인하는 함수

  ```python
  def check(i, j, key, n, board, m):
      # i, j는 열쇠의 좌측 상단 지점
      for k in range(n):
          for l in range(n):
              # key 와 board 를 맞춰봄
              board[i+k][j+l] += key[k][l]
  
      # lock 을 탐색 하다가 1이 아닌 곳이 있으면 False 리턴
      for k in range(m):
          for l in range(m):
              if board[n+k-1][n+l-1] != 1:
                  return False
  
      return True
  ```

  

- 열쇠가 자물쇠를 열 수 있는지 확인하는 함수

  ```python
  def solution(key, lock):
      n = len(key)  # key 의 크기
      m = len(lock)  # lock 의 크기
  
      # key 가 lock 외부로 나갈 수 있기 때문에 최대 크기의 board 를 만든다.
      board = [[0] * (m + 2*n - 2) for _ in range(m + 2*n - 2)]
      for i in range(m):
          for j in range(m):
              board[n+i-1][n+j-1] = lock[i][j]
  
      # key 를 움직여가며 체크
      for i in range(n+m-1):
          for j in range(n+m-1):
              # 회전하면서 체크하므로 4번 해야한다.
              for _ in range(4):
                  # 성공하면
                  if check(i, j, key, n, board, m):
                      return True
                  # 실패 했을 때
                  else:
                      # lock 부분을 초기화 시켜준다.
                      for k in range(m):
                          for l in range(m):
                              board[n+k-1][n+l-1] = lock[k][l]
                      key = rotate_key(key, n)
  
      # 성공하지 못하고 여기로 나왔을 때
      return False
  ```

  

  