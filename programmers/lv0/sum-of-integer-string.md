# 문자열 정수의 합
https://school.programmers.co.kr/learn/courses/30/lessons/181849
---
# 문제 설명
한 자리 정수로 이루어진 문자열 num_str이 주어질 때, 각 자리수의 합을 return하도록 solution 함수를 완성해주세요.

# 제한사항
+ 3 ≤ num_str ≤ 100
```java
class Solution {
    public int solution(String num_str) {
        int sum = 0;
        
        for (int i = 0; i < num_str.length(); i++) {
            sum += num_str.charAt(i) - '0';
        }
        
        return sum;
        
    }
}
```