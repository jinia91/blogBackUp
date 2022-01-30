# 64. 카카오 프렌즈 컬러링북
    import java.util.*;
    class Solution {
        
        static boolean[][] visited;
    	static int[] dY = { -1, 1, 0, 0 };
    	static int[] dX = { 0, 0, -1, 1 };
    	static int cnt;
    	static int numberOfArea;
    	
        public int[] solution(int m, int n, int[][] picture) {
    		visited = new boolean[m][n];
            cnt = 0;
            numberOfArea = 0;
            
    		for(int i =0; i<m; i++) {
    			for(int j=0; j<n; j++) {
    				bfs(new int[] {i, j}, picture);
    			}
    		}
    		
            int[] answer = new int[2];
    		answer[0] = numberOfArea;
    		answer[1] = cnt;
    		
            return answer;
        }
        
        static void bfs(int[] cur, int[][] picture) {
    		if (visited[cur[0]][cur[1]])
    			return;
    		
    		LinkedList<int[]> q = new LinkedList<>();
    
    		q.add(cur);
    		visited[cur[0]][cur[1]] = true;
    		int color = picture[cur[0]][cur[1]];
    		if(color == 0) return;
    		int tmpCnt = 1;
    		
    		while (!q.isEmpty()) {
    
    			int[] index = q.poll();
    			
    			for (int i = 0; i < 4; i++) {
    				int[] nextIndex = new int[2];
    				nextIndex[0] = index[0] + dY[i];
    				nextIndex[1] = index[1] + dX[i];
    
    				if (nextIndex[0] < 0 || nextIndex[0] >= visited.length || nextIndex[1] < 0
    						|| nextIndex[1] >= visited[0].length) continue;
    				if(visited[nextIndex[0]][nextIndex[1]]) continue;
    				if(color != picture[nextIndex[0]][nextIndex[1]]) continue;
    				
    				visited[nextIndex[0]][nextIndex[1]] = true;
    				tmpCnt++;
    				q.add(nextIndex);
    			}
    		}
    
    		numberOfArea++;
    		cnt = Math.max(cnt, tmpCnt);
    	}
    }
    

2017 카카오코드 예선 기출

평범한 bfs문제였고 bfs유형을 충분히 연습했다면 쉽게 풀 수 있다.


# 65. 오픈 채팅방

    import java.util.*;
    class Solution {
        public List<String> solution(String[] record) {
            Map<String, String> userList = new HashMap();
    		for (String r : record) {
    
    			String[] split = r.split(" ");
                
                if (split[0].equals("Enter")) {
    				userList.put(split[1], split[2]);
    			}
    
    
    			if (split[0].equals("Change")) {
    				userList.put(split[1], split[2]);
    			}
    		}
    
    		List<String> answer = new ArrayList<>();
    		
    		for(String r : record) {
    			
    			String[] split = r.split(" ");
    			
    			if(split[0].equals("Enter")) {
    				answer.add(userList.get(split[1])+"님이 들어왔습니다.");
    			}
    			
    			if(split[0].equals("Leave")) {
    				answer.add(userList.get(split[1])+"님이 나갔습니다.");
    			}
    			
    		}
            return answer;
        }
    }
    

2019 카카오 블라인드 공채 문제

문자열 다루기유형이고 요구사항 자체는 어렵지 않았는데 빨리풀려다 보니 굉장히 비효율적으로 풀었다.

일단 2N번 순회를 하는것도 아쉽고 split으로 쪼개는것도 상단에 한번만 해서 그 배열로 조회를하면 되는데 불필요하게 2번이나 한다.

코드 리뷰가 들어갔다면 분명 욕먹었을 풀이


# 66. 문자열 압축

    class Solution {
        public int solution(String s) {
            if(s.length() == 1) return 1;
    	    int answer = Integer.MAX_VALUE;
            
    		for(int i =1; i<=s.length()/2; i++) {
    			
    			StringBuilder sb = new StringBuilder();
    			String post = "";
    			int zipCnt = 1;
    			
    			for(int j = 0; j<s.length()/i; j++) {
    				String cri = s.substring(i*j, (i*j)+i);
    				if(cri.equals(post)){
    					zipCnt++;
    					continue;
    				}
    				if(zipCnt > 1) {
    					sb.append(cnt).append(post);
    					zipCnt = 1;
    				}
    				else {
    					sb.append(post);
    				}
    				
    				post = cri;	
    			}
    			
    			if(zipCnt > 1) {
    				sb.append(zipCnt).append(post);
    				zipCnt = 1;
    			}
    			else {
    				sb.append(post);
    			}
    
    			if(s.length()%i != 0) {
    				sb.append(s.substring(s.length()-s.length()%i,s.length()));
    			}
    			
    			answer = Math.min(answer, sb.length());
    						
    		}
            return answer;
        }
    }
    
2020 카카오 블라인드 공채 문제

구현 유형이고 구현 방법은 알겠는데 정작 로직을 완벽하게 떠올리기가 힘들어서 결국 구글링의 도움을 받았다.

압축하는 문자열의 길이를 동적으로 바꾸면서 그 다음 문자열로 넘어가는`s.substring(i*j, (i*j)+i)`를 떠올리기가 까다로웠던 문제

구현 유형을 좀 더 연습해봐야겠다.
