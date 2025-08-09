# 문자열을 정수로 변환하기
https://school.programmers.co.kr/learn/courses/30/lessons/181848
---
## 문제 설명
숫자로만 이루어진 문자열 n_str이 주어질 때, n_str을 정수로 변환하여 return하도록 solution 함수를 완성해주세요.

## 제한사항
+ 1 ≤ n_str ≤ 5
+ n_str은 0부터 9까지의 정수 문자로만 이루어져 있습니다.
```java
class Solution {
    public int solution(String n_str) {
        return Integer.parseInt(n_str);
    }
}
```