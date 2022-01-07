## 55. 메뉴 리뉴얼

    import java.util.*;
    class Solution {
        static boolean[] visited;
        public List<String> solution(String[] orders, int[] course) {
    		ArrayList<String> answer = new ArrayList<>();
    		
    		for(int corse : course) {
    			
    			HashMap<String, Integer> map = new HashMap<>();
    			
    			for(String o : orders) {
    				
    				char[] cArr = o.toCharArray();
    				Arrays.sort(cArr);
    				o = new String(cArr);
    				
    				visited = new boolean[o.length()];
    				dfs(map, o, corse,0,0);	
    			}
    			
    			LinkedList<Map.Entry<String, Integer>> list = new LinkedList<>(map.entrySet());
    			list.sort((o1, o2) -> o2.getValue()- o1.getValue());
    			
                if(list.isEmpty()) continue;
    			int max = list.get(0).getValue();
    			
    			for(Map.Entry<String, Integer> entry : list){
    				if(entry.getValue() < max || entry.getValue() ==1) break;
    				answer.add(entry.getKey());
    			}
    		}
    		
    		Collections.sort(answer);
            return answer;		
    	}
    
    	static void dfs(HashMap<String, Integer> map, String o, int corse, int d, int index) {
    		if(d == corse) {
    			StringBuilder sb = new StringBuilder();
    			for(int i = 0; i<o.length();i++) {
    				if(visited[i]) sb.append(o.charAt(i));
    			}
    		
    			if(map.containsKey(sb.toString())) {
    				map.put(sb.toString(), map.get(sb.toString())+1);
    			}
    			else {
    				map.put(sb.toString(), 1);
    			}
    			return;
    		}
    		
    		for(int i = index; i < o.length(); i++) {
    			
    			if(visited[i]) continue;
    			
    			visited[i] = true;
    			dfs(map,o,corse,d+1,i+1);
    			visited[i] = false;
    		}
    	}
    }
    

21년 카카오 블라인드 채용 기출

주문의 문자열 길이 n에서 요구하는 course의 값 r만큼 조합을 뽑아내 해당 조합들을 해시맵으로 저장하여 카운팅하고 그 카운팅값이 제일 높은 문자열을 뽑아내었다.

조합구하기는 dfs로 무난하게 구현했는데 이걸 저장하고 정렬할때 어떤 자료구조를 쓸지 고민되었다.

 맵을 entrySet으로 변환해 List로 만들고, 밸류값을 기준으로 내림차순 정렬해서 해결

## 56. 행렬 테두리 회전하기

    import java.util.*;
    class Solution {
        public List<Integer> solution(int rows, int columns, int[][] queries) {
    		
            int[][] board = new int[rows][columns];
    		
    		for(int i = 0; i< rows; i++) {
    			for(int j = 0; j<columns; j++) {
    				board[i][j] = j+1+i*columns;
    			}
    		}
    		
    		ArrayList<Integer> answer = new ArrayList<>();
    		
    		for(int[] q : queries) {
    			
    			int[][] tmpB = new int[rows][columns];
    			for(int i =0; i<tmpB.length;i++) {
    				tmpB[i] = board[i].clone();
    			}
    			
    			int min = Integer.MAX_VALUE;
    			
    			for(int i = q[1]; i<q[3]; i++) {
    				tmpB[q[0]-1][i] = board[q[0]-1][i-1];
    				min = Math.min(min, board[q[0]-1][i-1]);
    			}
    			
    			for(int i = q[0]; i<q[2]; i++) {
    				tmpB[i][q[3]-1] = board[i-1][q[3]-1];
    				min = Math.min(min, board[i-1][q[3]-1]);
    			}
    			
    			for(int i = q[1]-1; i<q[3]-1; i++) {
    				tmpB[q[2]-1][i] = board[q[2]-1][i+1];
    				min = Math.min(min, board[q[2]-1][i+1]);
    			}
    			
    			for(int i = q[0]-1; i<q[2]-1; i++) {
    				tmpB[i][q[1]-1] = board[i+1][q[1]-1];
    				min = Math.min(min, board[i+1][q[1]-1]);			
    			}
    			
    			board = tmpB;
    						
    			answer.add(min);
    		}
            return answer;
        }
    }
    

2021 데브매칭 기출

소위 말하는 빡구현

별다른 편법이 없고 문제에서 주어진대로 4 방향 배열을 돌려야한다.

백준에 배열돌리기 유형을 풀어보면 도움이 많이 된다.

[백준 배열돌리기 1](https://www.acmicpc.net/problem/16926)

[백준 배열돌리기 2](https://www.acmicpc.net/problem/16927)


## 57. 짝지어 제거하기
      class Solution{
        public int solution(String s){
    		char[] zip = new char[s.length()];
    		int index = 0;    
    		for(int i =0; i<s.length();i++) {
    			zip[index++] = s.charAt(i);
    			while(index >1&& zip[index-1] == zip[index-2]) index -= 2;
    		}
            if(new String(zip).substring(0, index).length() >0) return 0;
            return 1;
        }
    }
    
중복 문자열 제거 유형으로 문자 배열에 문자를 하나씩 집어넣으며 중복될시 배열을 가르키는 커서를 움직이는 방식으로 구현했다.

단순히 덧씌우기이므로 문자를 중복제거하거나 전체 문자열을 재탐색하지 않으므로 효율성 테스도 쉽게 통과한다.

현 블로그 댓글 기능을 만들 때 개행 중복 제거에서 썼던 알고리즘과 동일한 알고리즘을 써서 쉽게 풀었다.