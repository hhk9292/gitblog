---
title: "2020 KAKAO BLIND 7 블록 이동하기"
date: 2020-09-06T20:31:58+09:00
typora-copy-images-to: ./
typora-root-url: ..
---

[문제 풀러 가기](https://programmers.co.kr/learn/courses/30/lessons/60063)



## 문제 풀이

### 제한사항

- board의 한 변의 길이는 5 이상 100 이하입니다.
- board의 원소는 0 또는 1입니다.
- 로봇이 처음에 놓여 있는 칸 (1, 1), (1, 2)는 항상 0으로 주어집니다.
- 로봇이 항상 목적지에 도착할 수 있는 경우만 입력으로 주어집니다.



### 풀이 방법

- 모든 격자무늬에 보조선을 그어 로봇의 방향과 위치를 나타낼 수 있게 만들었다.

  ![image_7_1](/algorithm/images/image_7_1.png)

  - 위 그림처럼 나누면 로봇의 위치와 방향을 각 격자점에 1:1로 매칭시킬 수 있다. 

- BFS 탐색으로 목적지까지 가는 데 소요되는 최단시간을 찾는다.

  

### 코드

[전체 코드](https://github.com/hhk9292/algorithm/blob/master/2020KAKAOBLIND/7.py)

- 움직일 수 있는 방향이 담킨 배열

  ```python
  # 예를 들어 move[0]은 오른쪽(+y) 방향으로 움직인다
  move = [
          (0, 2),
          (0, -2),
          (2, 0),
          (-2, 0),
          (-1, 1),
          (1, -1),
          (-1, -1),
          (1, 1)
      ]
  ```

  

- 직진 가능성을 검사하는 코드

  ```python
  def straight_check(loc, land):
      x, y = loc
      length = len(land)
      result = []
      if (loc[0] % 2) and (loc[1] % 2 == 0):  # 가로
          if y <= length - 4 and land[x][y + 3] == 0:
              # + 방향 직진 가능
              result.append(0)
  
          if y >= 3 and land[x][y - 3] == 0:
              # - 방향 직진 가능
              result.append(1)
  
      if (loc[0] % 2 == 0) and (loc[1] % 2):  # 세로
          if x <= length - 4 and land[x + 3][y] == 0:
              # + 방향 직진 가능
              result.append(2)
  
          if x >= 3 and land[x - 3][y] == 0:
              # - 방향 직진 가능
              result.append(3)
  
      return result
  ```

  

- 회전과 평행이동 가능성을 검사하는 코드

  ```python
  def rotate_check(loc, land):
      # 회전 가능성을 보는 검사지만 평행 이동도 검사함
      x, y = loc
      length = len(land)
      result = []
      if (loc[0] % 2) and (loc[1] % 2 == 0):  # 가로
          # 아래쪽 검사
          if x <= length - 3:
              if (land[x+2][y-1] == 0) and (land[x+2][y+1] == 0):
                  result.append(2)
                  result.append(5)
                  result.append(7)
  
          # 위쪽 검사
          if x >= 2:
              if (land[x-2][y-1] == 0) and (land[x-2][y+1] == 0):
                  result.append(3)
                  result.append(4)
                  result.append(6)
  
      if (loc[0] % 2 == 0) and (loc[1] % 2):  # 세로
          # 오른쪽 검사
          if y <= length - 3:
              if (land[x-1][y+2] == 0) and (land[x+1][y+2] == 0):
                  result.append(0)
                  result.append(4)
                  result.append(7)
  
          # 왼쪽 검사
          if y >= 2:
              if (land[x-1][y-2] == 0) and (land[x+1][y-2] == 0):
                  result.append(1)
                  result.append(5)
                  result.append(6)
      return result
  ```



- 최단시간을 반환하는 함수

  ```python
  def solution(board):
      move = [
          (0, 2),
          (0, -2),
          (2, 0),
          (-2, 0),
          (-1, 1),
          (1, -1),
          (-1, -1),
          (1, 1)
      ]
      n = len(board)
  
      """
      가로 세로 모든 칸을 2개로 나누어서 진행
      로봇의 몸통의 위치에 따라 가로 세로가 결정되고 앞 뒤 방향성도 사라지므로
      위 사항을 고려하지 않아도 됨
      """
  
      land = [[0] * (2 * n + 1) for _ in range(2 * n + 1)]
      for i in range(n):
          for j in range(n):
              x, y = 2 * i + 1, 2 * j + 1
              if board[i][j] == 1:
                  for k in range(-1, 2):
                      for l in range(-1, 2):
                          land[x + k][y + l] = 1
  
  
      length = len(land)
      # 도착점 두 군데: 가로 방향, 세로 방향
      final = {(length - 2, length - 3), (length - 3, length - 2)}
  
      visit = set()  # 방문 여부를 검사하는 집합
      visit.add((1, 2))  # 출발점 방문 체크
  
      # BFS 탐색
      queue = []
      queue.append((1, 2, 0))  # 위치와 현재까지 걸린 시간 저장
      while queue:
          cx, cy, cnt = queue.pop(0)
          directs = []  # 움직일 수 있는 방향을 담는 배열
          directs.extend(straight_check((cx, cy), land))
          directs.extend(rotate_check((cx, cy), land))
          for direct in directs:
            nx, ny = cx + move[direct][0], cy + move[direct][1]
              # 목적지에 도착할 수 있으면 while 문을 빠져나감
              if (nx, ny) in final:
                  queue = []
                  break
              
              if (nx, ny) not in visit:
                  visit.add((nx, ny))
                  queue.append((nx, ny, cnt + 1))
      answer = cnt + 1
      return answer
  ```
  
  