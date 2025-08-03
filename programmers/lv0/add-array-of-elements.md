# 배열의 원소만큼 추가하기
https://school.programmers.co.kr/learn/courses/30/lessons/181861
---
## 문제 설명
아무 원소도 들어있지 않은 빈 배열 X가 있습니다. 양의 정수 배열 arr가 매개변수로 주어질 때, arr의 앞에서부터 차례대로 원소를 보면서 원소가 a라면 X의 맨 뒤에 a를 a번 추가하는 일을 반복한 뒤의 배열 X를 return 하는 solution 함수를 작성해 주세요.

## 제한사항
+ 1 ≤ arr의 길이 ≤ 100
+ 1 ≤ arr의 원소 ≤ 100
```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public int[] solution(int[] arr) {
        List<Integer> list = new ArrayList<>();
        
        for (int a : arr) {
            for (int i = 0; i < a; i++) {
                list.add(a);
            }
        }
        
        int[] answer = new int[list.size()];
        for (int i = 0; i < list.size(); i++) {
            answer[i] = list.get(i);
        }
        
        return answer;
    }
}
```