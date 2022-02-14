# 최고의 집합

    import java.util.*;
    
    class Solution {
        
        public int[] solution(int n, int s) {
            int x = s/n;
            int y = s%n;
    
            if(x<1) return new int[] {-1};
    
            int[] answer = new int[n];
            
            for(int i =0; i<n-y; i++){
                answer[i] = x;    
            }
            for(int i =n-y; i<n; i++){
                answer[i] = x+1;    
            }
            return answer;
        }
    }
    
단순 산수문제였다.

처음에는 bf로 n으로 쪼개는 모든 경우를 다 구현해 곱하고 비교 하려했는데, n이 너무 커서 터무니 없는 짓인걸 깨달았다.

그렇다면 규칙성이 있을거로 생각해 잘 살펴보니 단순히 N차 방정식의 해를 구하는 문제였다.

즉 N개의 수의 합이 S일때 그 곱 P가 최대가 되는 값을 찾는 문제이므로

예를 들어 `n=2`, `s=36` 일경우를 가정해보면

    x + y = 36
    y = 36 - x
    
    p = x * y
    p = x * (36 - x)
    p = 36x -x^2
    
위와같은 이차 방정식이 나오며 해당 방정식 그래프를 그리면 

    dp/dx = 36 - 2x = 0
    x = 18, y = 36- 18 = 18
    
두 수가 각각 18일때 최대 p를 가짐을 증명할 수 있다.

여기서 직관을 더해 조금 더 생각해보면 결국 n개의 수가 편차가 가장 적게 분포되어있을 때, 곱이 가장 최대가 됨을 생각해볼 수 있으며

이를 활용하면 위와 같은 코드로 풀어낼 수 있다.

`s/n`이 1 미만인 코너케이스 처리만 잊지 않으면 해결!