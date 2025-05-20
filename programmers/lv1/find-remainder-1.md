# 나머지가 1이 되는 수 찾기
https://school.programmers.co.kr/learn/courses/30/lessons/87389
---
## 문제 설명
자연수 n이 매개변수로 주어집니다.<br>
n을 x로 나눈 나머지가 1이 되도록 하는 가장 작은 자연수<br>
x를 return 하도록 solution 함수를 완성해주세요.<br>
답이 항상 존재함은 증명될 수 있습니다.

## 제한사항
+ 3 ≤ n ≤ 1,000,000
---
## 입출력 예
| n	| result |
| --- | --- |
| 10	| 3 |
| 12	| 11 |
```declarative
class Solution {
    public int solution(int n) {
        int result = 0;
        
        for (int i = 1; i <= n; i++) {
            int value = n % i;
            
            if (value == 1) {
                result = i;
                break;
            }
        }
        
        return result;
    }
}
```