# 더 크게 합치기
https://school.programmers.co.kr/learn/courses/30/lessons/181939
---
# 문제 설명
연산 ⊕는 두 정수에 대한 연산으로 두 정수를 붙여서 쓴 값을 반환합니다. 예를 들면 다음과 같습니다.

+ 12 ⊕ 3 = 123
+ 3 ⊕ 12 = 312
양의 정수 a와 b가 주어졌을 때, a ⊕ b와 b ⊕ a 중 더 큰 값을 return 하는 solution 함수를 완성해 주세요.

단, a ⊕ b와 b ⊕ a가 같다면 a ⊕ b를 return 합니다.

## 제한사항
+ 1 ≤ a, b < 10,000
```java
class Solution {
    public int solution(int a, int b) {
        int ab = Integer.parseInt("" + a + b);
        int ba = Integer.parseInt("" + b + a);
        return Math.max(ab, ba);
    }
}
```