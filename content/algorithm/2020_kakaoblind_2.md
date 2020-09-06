---
title: "2020 KAKAO BLIND 2 문자열 압축 괄호 변환"
date: 2020-09-06T20:03:52+09:00
---

[문제 풀러 가기](https://programmers.co.kr/learn/courses/30/lessons/60058)



## 문제 풀이

### 변환 과정

```
1. 입력이 빈 문자열인 경우, 빈 문자열을 반환합니다. 
2. 문자열 w를 두 "균형잡힌 괄호 문자열" u, v로 분리합니다. 단, u는 "균형잡힌 괄호 문자열"로 더 이상 분리할 수 없어야 하며, v는 빈 문자열이 될 수 있습니다. 
3. 문자열 u가 "올바른 괄호 문자열" 이라면 문자열 v에 대해 1단계부터 다시 수행합니다. 
  3-1. 수행한 결과 문자열을 u에 이어 붙인 후 반환합니다. 
4. 문자열 u가 "올바른 괄호 문자열"이 아니라면 아래 과정을 수행합니다. 
  4-1. 빈 문자열에 첫 번째 문자로 '('를 붙입니다. 
  4-2. 문자열 v에 대해 1단계부터 재귀적으로 수행한 결과 문자열을 이어 붙입니다. 
  4-3. ')'를 다시 붙입니다. 
  4-4. u의 첫 번째와 마지막 문자를 제거하고, 나머지 문자열의 괄호 방향을 뒤집어서 뒤에 붙입니다. 
  4-5. 생성된 문자열을 반환합니다.
```



### 매개변수 설명

- p는 '(' 와 ')' 로만 이루어진 문자열이며 길이는 2 이상 1,000 이하인 짝수입니다.
- 문자열 p를 이루는 '(' 와 ')' 의 개수는 항상 같습니다.
- 만약 p가 이미 올바른 괄호 문자열이라면 그대로 return 하면 됩니다.



### 풀이 방법

- 변환 과정에 맞게 재귀함수를 만들면 된다.



### 코드

[전체 코드](https://github.com/hhk9292/algorithm/blob/master/2020KAKAOBLIND/2.py)

- 올바른 괄호 문자열인지 확인하는 함수

  ```python
  def is_right(p):
      result = False
      opens = []  # 열린 괄호가 들어가는 리스트
      for bracket in p:
          if bracket == "(":
              opens.append("(")
          else:  #  닫힌 괄호면 opens 리스트를 검사
              # 닫힌 괄호가 나왔을 때 opens가 비어있으면 종료 후 False 반환
              if len(opens) == 0:
                  break
              else:
                  opens.pop()
      # 모든 검사를 마쳤을 때 opens가 비어있으면 True 반환
      else:
          if len(opens) == 0:
              result = True
  
      # 그렇지 않으면 False 반환
      return result
  ```

  

- 괄호를 뒤집는 함수

  ```python
  def reverse_brackets(p):
      result = ''
      for bracket in p:
          if bracket == "(":
              result += ")"
          else:
              result += "("
      return result
  ```

  

- 변환 과정에 따라 올바른 문자열로 변환하는 함수

  ```python
  def solution(p):
      # 입력이 빈 문자열이거나 올바른 문자열일 경우 그대로 반환
      if (p == '') or (is_right(p)):
          return p
      else:
          open_cnt = 0   # "("의 개수
          close_cnt = 0  # "("의 개수
          for i in range(len(p)):
              if p[i] == "(":
                  open_cnt += 1
              else:
                  close_cnt += 1
              # 최초의 균형잡힌 괄호 문자열을 찾아서 u에 저장하고 뒷부분 v에 저장
              if open_cnt == close_cnt:
                  u = p[: i+1]
                  v = p[i+1:]
                  break
          
          # u가 올바른 문자열일 경우
          if is_right(u):
              return u + solution(v)
          # 그렇지 않은 경우
          else:
              return "(" + solution(v) + ")" + reverse_brackets(u[1:-1])
  ```

  