# 배열의 원소 삭제하기
https://school.programmers.co.kr/learn/courses/30/lessons/181844
---
## 문제 설명
정수 배열 arr과 delete_list가 있습니다. arr의 원소 중 delete_list의 원소를 모두 삭제하고 남은 원소들은 기존의 arr에 있던 순서를 유지한 배열을 return 하는 solution 함수를 작성해 주세요.

## 제한사항
+ 1 ≤ arr의 길이 ≤ 100
+ 1 ≤ arr의 원소 ≤ 1,000
+ arr의 원소는 모두 서로 다릅니다.
+ 1 ≤ delete_list의 길이 ≤ 100
+ 1 ≤ delete_list의 원소 ≤ 1,000 
+ delete_list의 원소는 모두 서로 다릅니다.
```java
import java.util.*;

class Solution {
    public int[] solution(int[] arr, int[] delete_list) {
        Set<Integer> del = new HashSet<>();
        for (int x : delete_list) del.add(x);
        
        List<Integer> target = new ArrayList<>();
        for (int x : arr) {
            if (!del.contains(x)) target.add(x);
        }
        
        int[] answer = new int[target.size()];
        for (int i = 0; i < target.size(); i++) {
            answer[i] = target.get(i);
        }
        
        return answer;
    }
}
```