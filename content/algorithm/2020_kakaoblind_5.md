---
title: "2020 KAKAO BLIND 5 기둥과 보 설치"
date: 2020-09-06T20:31:51+09:00
---

[문제 풀러 가기](https://programmers.co.kr/learn/courses/30/lessons/60061)



## 문제 풀이

### 제한사항

- n은 5 이상 100 이하인 자연수입니다.
- build_frame의 세로(행) 길이는 1 이상 1,000 이하입니다.
- build_frame의 가로(열) 길이는 4입니다.
- build_frame의 원소는 [x, y, a, b]형태입니다.
  - x, y는 기둥, 보를 설치 또는 삭제할 교차점의 좌표이며, [가로 좌표, 세로 좌표] 형태입니다.
  - a는 설치 또는 삭제할 구조물의 종류를 나타내며, 0은 기둥, 1은 보를 나타냅니다.
  - b는 구조물을 설치할 지, 혹은 삭제할 지를 나타내며 0은 삭제, 1은 설치를 나타냅니다.
  - 벽면을 벗어나게 기둥, 보를 설치하는 경우는 없습니다.
  - 바닥에 보를 설치 하는 경우는 없습니다.
- 구조물은 교차점 좌표를 기준으로 보는 오른쪽, 기둥은 위쪽 방향으로 설치 또는 삭제합니다.
- 구조물이 겹치도록 설치하는 경우와, 없는 구조물을 삭제하는 경우는 입력으로 주어지지 않습니다.
- 최종 구조물의 상태는 아래 규칙에 맞춰 return 해주세요.
  - return 하는 배열은 가로(열) 길이가 3인 2차원 배열로, 각 구조물의 좌표를 담고있어야 합니다.
  - return 하는 배열의 원소는 [x, y, a] 형식입니다.
  - x, y는 기둥, 보의 교차점 좌표이며, [가로 좌표, 세로 좌표] 형태입니다.
  - 기둥, 보는 교차점 좌표를 기준으로 오른쪽, 또는 위쪽 방향으로 설치되어 있음을 나타냅니다.
  - a는 구조물의 종류를 나타내며, 0은 기둥, 1은 보를 나타냅니다.
  - return 하는 배열은 x좌표 기준으로 오름차순 정렬하며, x좌표가 같을 경우 y좌표 기준으로 오름차순 정렬해주세요.
  - x, y좌표가 모두 같은 경우 기둥이 보보다 앞에 오면 됩니다.



### 풀이 방법

- 설치하는 경우는 제한사항에 맞게 검사하고 설치
- 삭제하는 경우는 대상 건축물을 먼저 삭제 한 다음에 다른 건축물에 대해 설치 검사를 해서 설치할 수 없는 건축물이 하나라도 있을 경우 대상 건축물을 다시 설치하는 방식으로 구현
  - 대상 건축물이 영향을 주는 건축물들만 따로 해도 되지만 전체 건축물이 최대 1000개 밖에 되지 않기 때문에 모든 건축물에 대해 검사해도 상관 없음



### 코드

[전체 코드](https://github.com/hhk9292/algorithm/blob/master/2020KAKAOBLIND/5.py)

- 건축물을 설치할 수 있는지 확인하는 함수

  ```python
  def check(elem, wall):
      x, y, kind = elem
      if kind == 0:  # 기둥
          if y == 0:  # 바닥에 설치
              return True
          if (x, y-1, 0) in wall:  # 한 칸 아래에 기둥이 있는 경우
              return True
          if (x, y, 1) in wall or (x-1, y, 1) in wall:  # 현 위치나 왼쪽에 보가 있는 경우
              return True
          return False
  
      else:  # 보
          if (x, y-1, 0) in wall or (x+1, y-1, 0) in wall:  # 한 칸 아래나 아래 오른쪽에 기둥이 있는 경우
              return True
          if (x-1, y, 1) in wall and (x+1, y, 1) in wall:  # 양 옆에 보가 있는 경우
              return True
          return False
  ```

  

- 구조물을 반환하는 함수

  ```python
  def solution(n, build_frame):
      wall = set()  # 건축물이 들어갈 set
      for build in build_frame:  # build = [x좌표, y좌표, 기둥/보, 설치/삭제]
          elem = (build[0], build[1], build[2])
          if build[3] == 1:  # 설치
              # 여기에 체크 함수
              if check(elem, wall):
                  wall.add(elem)
              pass
          else:
              # 여기에 체크 함수
              # 먼저 삭제 후 다른 건축물들에 대해 check 함수를 돌린다.
              wall.remove(elem)
              for other_elem in wall:
                  # 다른 건축물 중 하나라도 건축이 불가능 할 경우
                  # 건축물을 다시 추가한다.
                  if not check(other_elem, wall):
                      wall.add(elem)
                      break
  
      answer = [list(elem) for elem in wall]
      answer.sort(key=lambda elem: (elem[0], elem[1], elem[2]))
      return answer
  ```

  