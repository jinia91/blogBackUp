# 줄서는 법

    import java.util.*;
    
    class Solution {
        static long cnt;
    	static List<Integer> answer;
    	static boolean[] visited;
    	static long[] dp;  
        
        public List<Integer> solution(int n, long k) {
    		visited = new boolean[n+1];
            dp = new long[n+1];
    		dfs(0, new ArrayList<>(), n, k);
            return answer;
        }
        
    	static List<Integer> dfs(int d, List<Integer> list, int N, long K) {     
            if(d == N) {
    			answer = new ArrayList<>(list);
    			return answer;
    		}
            
    		for(int i = 1;i<=N;i++) {
    			if(visited[i]) continue;	
    
                long tmpCnt = factorial(N-d-1); 
    			if(d+1 != N&&K > tmpCnt + cnt) {
    				cnt += tmpCnt;
    				continue;
    			}
    			
    			list.add(i);
    			visited[i] = true;
        		return	dfs(d+1, list,N,K);
    	    }
            return answer;
        }
    	
    	static long factorial(int num) {
    		if(dp[num] != 0) return dp[num];
    		if(num == 0) {
    			return 0;
    		}
    		if(num == 1) {
    			return 1;
    		}
    		return dp[num] = num * factorial(num -1);
    	}
    }
    


시간 효율성 잡기 상당히 까다로웠던 문제

맨 처음에는 단순히 DFS로 순열을 만들어보았는데 최대 20! 경우의수가 나오기 때문에 시간복잡도에서 걸려버렸다.

따라서 시간복잡도를 잡기 위해  팩토리얼로 해당 자리에 사람이 줄을 섰을때 나올 경우의수를 구해서 

해당 경우의 수가 `구하고자하는 K` 번째에 못 미치는 경우 더 이상 dept를 들어가지 않는 `유망성 판단` 백트래킹 알고리즘을 추가했다.


             long tmpCnt = factorial(N-d-1); 
			if(d+1 != N&&K > tmpCnt + cnt) {
				cnt += tmpCnt;
				continue;
			}

문제는 해당 로직이 추가되어도 반복적인 팩토리얼 계산때문인지 시간 효율성 테스트를 절반밖에 통과하지 못했다.

결국 팩토리얼 계산을 반복하지 않기 위해서는 크게 2가지 방법을 생각했다.

1. 수학으로 해당 자리에 들어갈 숫자를 바로 찾기
2. 팩토리얼 값 자체를 기록해서 한번 계산하면 더이상 계산하지 않게 하기(memoization)

1번 방법은 로직이 복잡하지 않다면 반복계산을 한번에 점프할수 있어 매우 효율적이라 판단했지만 기존에 만들어둔 dfs 알고리즘을 많이 건드려야할거같아서 

쉽게 알고리즘을 추가할 수 있는 2번 DP(Dynamic Programing)를 선택했다.

   	static long factorial(int num) {
    		if(dp[num] != 0) return dp[num]; // 메모배열에서 값이 존재하면 계산하지 않고 바로 반환하는 DP
    		
![첨부 이미지](https://github.com/jinia91/blogBackUp/blob/main/img/ba9c6367-c094-43f4-8b00-620c2df9133e.png?raw=true)

[시간 복잡도 통과!]

