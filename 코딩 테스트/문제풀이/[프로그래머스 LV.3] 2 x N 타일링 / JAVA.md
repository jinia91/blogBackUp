# 2 X N 타일링
    
        
    class Solution {
        public int solution(int n) {
            int[] dp = new int[n+1];
            
            dp[0] = 0;
            dp[1] = 1;
            dp[2] = 2;
            for(int i = 3; i<=n; i++){
                if(dp[i] == 0){
                    dp[i] = (dp[i-1] + dp[i-2])%1000000007;
                }
            }
            int answer = dp[n];
            return answer;
        }
    }
    

간단한 피보나치 문제

효율성 테스트가 있는걸 보면 재귀로는 아마 못풀것이다. 

[자세히는 이전 피보나치 풀이 확인](https://www.jiniaslog.co.kr/article/view?articleId=255)