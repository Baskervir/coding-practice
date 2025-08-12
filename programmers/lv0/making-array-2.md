# 배열 만들기 2
https://school.programmers.co.kr/learn/courses/30/lessons/181921
---
## 문제 설명
정수 l과 r이 주어졌을 때, l 이상 r이하의 정수 중에서 숫자 "0"과 "5"로만 이루어진 모든 정수를 오름차순으로 저장한 배열을 return 하는 solution 함수를 완성해 주세요.

만약 그러한 정수가 없다면, -1이 담긴 배열을 return 합니다.

## 제한사항
+ 1 ≤ l ≤ r ≤ 1,000,000
```java
import java.util.*;

class Solution {
    public int[] solution(int l, int r) {
        List<Integer> regist = new ArrayList<>();
        Deque<Integer> q = new ArrayDeque<>();
        q.add(5);
        
        while (!q.isEmpty()) {
            int x = q.pollFirst();
            if (x > r) continue;
            if (x >= l) regist.add(x);
            
            q.addLast(x * 10);
            q.addLast(x * 10 + 5);
        }
        
        if (regist.isEmpty()) return new int[]{-1};
        int[] answer = new int[regist.size()];
        for (int i = 0; i < regist.size(); i++) {
            answer[i] = regist.get(i);
        }
            return answer;
    }
}
```