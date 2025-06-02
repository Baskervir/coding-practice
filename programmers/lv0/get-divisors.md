# 약수 구하기
https://school.programmers.co.kr/learn/courses/30/lessons/120897
---
## 문제 설명
정수 n이 매개변수로 주어질 때, n의 약수를 오름차순으로 담은 배열을 return하도록 solution 함수를 완성해주세요.

## 제한사항
+ 1 ≤ n ≤ 10,000
```java
class Solution {
    public int[] solution(int n) {
        int cnt = 0;
        
        for (int i = 1; i <= n; i++) {
            if (n % i == 0) {
                cnt++;
            }
        }
        
        int[] result = new int[cnt];
        int index = 0;
        
        for (int i = 1; i <= n; i++) {
            if (n % i == 0) {
                result[index++] = i;
            }
        }
        
        return result;
    }
}
```