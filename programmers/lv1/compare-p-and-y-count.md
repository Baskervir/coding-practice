# 문자열 내 p와 y의 개수
https://school.programmers.co.kr/learn/courses/30/lessons/12916
---
## 문제 설명
대문자와 소문자가 섞여있는 문자열 s가 주어집니다.<br>
s에 'p'의 개수와 'y'의 개수를 비교해 같으면 True,<br>
다르면 False를 return 하는 solution를 완성하세요.<br>
'p', 'y' 모두 하나도 없는 경우는 항상 True를 리턴합니다.<br>
단, 개수를 비교할 때 대문자와 소문자는 구별하지 않습니다.
<br><br>
예를 들어 s가 "pPoooyY"면 true를 return하고 "Pyy"라면 false를 return합니다.

## 제한사항
+ 문자열 s의 길이 : 50 이하의 자연수
+ 문자열 s는 알파벳으로만 이루어져 있습니다.
---
## 입출력 예
| s	| answer |
| --- | --- |
| "pPoooyY"	| true |
| "Pyy"	| false |
```declarative
class Solution {
    boolean solution(String s) {
        int countP = 0;
        int countY = 0;
        
        String str = s.toLowerCase();
        
        for (int i = 0; i < str.length(); i++) {
            char ch = str.charAt(i);
            if (ch == 'p') {
                countP++;
            } else if (ch == 'y') {
                countY++;
            }
        }
        
        return countP == countY;
    }
}
```