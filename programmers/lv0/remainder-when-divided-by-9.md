# 9로 나눈 나머지
https://school.programmers.co.kr/learn/courses/30/lessons/181914
---
# 문제 설명
음이 아닌 정수를 9로 나눈 나머지는 그 정수의 각 자리 숫자의 합을 9로 나눈 나머지와 같은 것이 알려져 있습니다.
이 사실을 이용하여 음이 아닌 정수가 문자열 number로 주어질 때, 이 정수를 9로 나눈 나머지를 return 하는 solution 함수를 작성해주세요.

# 제한사항
+ 1 ≤ number의 길이 ≤ 100,000
+ number의 원소는 숫자로만 이루어져 있습니다.
+ number는 정수 0이 아니라면 숫자 '0'으로 시작하지 않습니다.
```java
class Solution {
    public int solution(String number) {
        int sum = 0;
        for (int i = 0; i < number.length(); i++) {
            sum += number.charAt(i) - '0';
        }
        
        return sum % 9;
    }
}
```