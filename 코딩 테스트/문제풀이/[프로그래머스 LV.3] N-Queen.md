# N-Queen

    class Solution {
    
        static int[] visited;
    	static int cnt;
    	static int N;
        
        public int solution(int n) {
    	    visited = new int[n];
    		N = n;
    		dfs(0);
            return cnt;
        }
        
        static void dfs(int d) {
    		if (d == N) {
    			cnt++;
    		}
    
    		for (int x = 0; x < N; x++) {
    			if (isPutable(d, x)) {
    				visited[d] = x;
    				dfs(d+1);
    			}
    		}
    	}
    
    	static boolean isPutable(int d, int x) {
    		for (int i = 0; i < d; i++) {
    			if (visited[i] == x || visited[i] + (d - i) == x || visited[i] - (d - i) == x) {
    				return false;
    			}
    		}
    		return true;
    	}
    }
    



백트래킹 대표 유형으로 유명한 N-Queen 문제

백준에서 이미 동일한 문제를 풀어봐서 쉽게 해결했다.

**백트래킹이란 탐색중 유망성 검사를 통해 불필요한 탐색 가지를 줄이는 방식이다.**

위의 문제는 n열의 보드판 위에 n개의 퀸을 올리는 문제이므로 시간복잡도가 최대 O(N<SUP>N</SUP>)이 나오게 되는데

이를 

>(1) 모든 경우를 구하고<BR> 
(2) 요구사항이 맞는지 판단하는 로직이라면

매우 많은 시간이 걸리게된다.

여기서 

>(1) 탐색전 요구사항에 맞을지 유망성 검사를 하고<BR>
(2) 유망성검사에 탈락하면 다음탐색으로 넘어가면

탐색할 경우의 수를 훨씬 줄일수 있으므로 BF 탐색이더라도 보다 효율적인 동작을 하게 된다.


풀이에서는 `isPutable`을 통해 해당 DFS 의 Dept를 더 들어갈지 말지 사전에 판단하여 탐색할 경우의 수를 줄여나갔다.

문제를 풀이할때 접근한 사고방식은 

>1. 모든 경우의 수를 세어봐야겠네 - > BF
> 2. 경우의 수를 셀 때 한열씩 퀸을 두어야하니까 순차적으로 나아가면 DFS가 직관적이겠네 -> DFS
>3. 경우의수 탐색시 조건분기로 좀 더 성능 향상을 해야겠네 -> 백트래킹

순으로 접근했다. 

만약 위의 사고 과정에서 막히는 부분이 있다면 해당 개념을 좀 더 공부해보자
