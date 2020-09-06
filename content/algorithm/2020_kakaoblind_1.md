---
title: "2020 KAKAO BLIND 1 문자열 압축 Python"
date: 2020-09-06T18:55:07+09:00
---

[문제 풀러 가기](https://programmers.co.kr/learn/courses/30/lessons/60057)



## 문제 풀이

### 제한사항

- s의 길이는 1 이상 1,000 이하입니다.
- s는 알파벳 소문자로만 이루어져 있습니다.



### 풀이 방법

- 문자열의 최대 길이가 1000밖에 되지 않으므로 완전탐색을 이용하면 된다. 

- 문자열을 앞에서부터 끊어서 압축하는 방식이기 때문에 전체 길이의 1/2 까지만 탐색하면 된다. 



### 코드

[전체 코드](https://github.com/hhk9292/algorithm/blob/master/2020KAKAOBLIND/1.py)

```python
def solution(s):
    n = len(s)  # 문자열의 길이
    answer = n
    # 문자열 체크는 1 ~ n//2 까지만 하면 된다.
    # 문자열의 길이가 8일 때 5글자 이상의 문자로는 압축을 할 수 없기 때문
    for l in range(1, n//2 + 1):
        check_list = []  # 문자열이 저장 될 곳
        check_list.append(s[0: l])  # 최초 문자열 저장
        cnt = 1  # 개수 체크
        for k in range(l, n, l):
            substr = s[k: k+l]
            # 가장 마지막에 추가된 문자열과 같다면 개수 1 추가
            if substr == check_list[-1]:
                cnt += 1
            # 다르다면 마지막에 저장되어있는 문자에 숫자를 추가
            else:
                # cnt가 1이면 무시
                if cnt == 1:
                    pass
                else:
                    # 개수를 추가해서 다시 저장
                    check_list.append(str(cnt) + check_list.pop())
                    # 개수 초기화
                    cnt = 1
                # 새로운 문자열 저장
                check_list.append(substr)
        # 마지막 문자열과 그 앞 문자열이 같을 때 한 번 더 검사
        if cnt == 1:
            pass
        else:
            check_list.append(str(cnt) + check_list.pop())
        
        # 길이가 가장 짧은 문자열의 길이를 저장
        length = len(''.join(check_list))
        if length < answer:
            answer = length

    return answer
```



