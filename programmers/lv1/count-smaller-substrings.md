# 크기가 작은 부분 문자열
https://school.programmers.co.kr/learn/courses/30/lessons/147355
---
## 문제 설명
숫자로 이루어진 문자열 t와 p가 주어질 때, t에서 p와 길이가 같은 부분문자열 중에서, 이 부분문자열이 나타내는 수가 p가 나타내는 수보다 작거나 같은 것이 나오는 횟수를 return하는 함수 solution을 완성하세요.

예를 들어, t="3141592"이고 p="271" 인 경우, t의 길이가 3인 부분 문자열은 314, 141, 415, 159, 592입니다. 이 문자열이 나타내는 수 중 271보다 작거나 같은 수는 141, 159 2개 입니다.

## 제한사항
+ 1 ≤ p의 길이 ≤ 18
+ p의 길이 ≤ t의 길이 ≤ 10,000
+ t와 p는 숫자로만 이루어진 문자열이며, 0으로 시작하지 않습니다.
```java
class Solution {
    public int solution(String t, String p) {
        int result = 0;
        long diff = Long.parseLong(p);
        
        int length = t.length() - p.length() + 1;
        
        long[] nums = new long[length];
        
        for (int i = 0; i < length; i++) {
            String sub = t.substring(i, i + p.length());
            nums[i] = Long.parseLong(sub);
        }
        
        for (int i = 0; i < length; i++) {
            if (nums[i] <= diff) {
                result++;
            }
        }
        
        return result;
    }
}
```