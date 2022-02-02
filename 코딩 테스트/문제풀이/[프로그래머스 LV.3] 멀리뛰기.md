# 멀리 뛰기


    class Solution {
        
        static long[] dp;
        
        public long solution(int n) {        
            if(n == 1) return 1;
            
            dp = new long[n+1];
            dp[0] = 0;
            dp[1] = 1;
            dp[2] = 2;
            
            for(int i = 2; i<=n; i++){
                if(dp[i] == 0){
                    dp[i] = (dp[i-1] + dp[i-2])%1234567;
                }
            }
            
            long answer = dp[n];
            
            return answer;
        }  
    }
    

피보나치 수열 문제

[레벨2 4. 피보나치 수열](https://www.jiniaslog.co.kr/article/view?articleId=255) 보다도 n 요구사항 최대치가 훨씬 작기 때문에 오히려 레벨2 보다 쉬운 문제라고 볼 수 있다.

왜 레벨3인지 의문

피보나치를 단순 재귀로 풀 경우 시간복잡도가 O(2<SUP>N</SUP>) 이므로 N이 50만 넘어가도 대부분의 컴퓨터가 죽어버린다.

위처럼 DP를 사용해 풀거나 분할정복을 통해 접근하도록하자

자세한 풀이는 [레벨2 4. 피보나치 수열](https://www.jiniaslog.co.kr/article/view?articleId=255) 에서 썼으므로 패스