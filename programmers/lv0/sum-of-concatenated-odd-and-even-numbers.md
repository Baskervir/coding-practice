# 이어 붙인 수
https://school.programmers.co.kr/learn/courses/30/lessons/181928
---
## 문제 설명
정수가 담긴 리스트 num_list가 주어집니다. num_list의 홀수만 순서대로 이어 붙인 수와 짝수만 순서대로 이어 붙인 수의 합을 return하도록 solution 함수를 완성해주세요.

## 제한사항
+ 2 ≤ num_list의 길이 ≤ 10
+ 1 ≤ num_list의 원소 ≤ 9
+ num_list에는 적어도 한 개씩의 짝수와 홀수가 있습니다.
```java
class Solution {
    public int solution(int[] num_list) {
        StringBuilder odd = new StringBuilder();
        StringBuilder even = new StringBuilder();
        
        for (int i = 0; i < num_list.length; i++) {
            if (num_list[i] % 2 != 0) {
                odd.append(num_list[i]);
            } else {
                even.append(num_list[i]);
            }
        }
        
        return Integer.parseInt(odd.toString()) + Integer.parseInt(even.toString());
    }
}
```
