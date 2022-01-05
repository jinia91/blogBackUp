## 49. 빛의 경로 사이클

    import java.util.*;
    class Solution {
    	static int[][] mapping = {
    			{0,1,2,3},	
    			{2,3,1,0},
    			{3,2,0,1}
    	};
    	static int[] dY = {-1,1,0,0};
    	static int[] dX = {0,0,-1,+1};	
    	static int[][][] visit;   
        static List<Integer> answer = new ArrayList<>();
        public List<Integer> solution(String[] grid) {
    		
    		visit = new int[4][grid.length][grid[0].length()];
    			
    		for(int i = 0; i<grid.length;i++) {
    			for(int j =0; j<grid[0].length();j++) {
    				for(int k = 0; k < 4; k++) {
    					bfs(new int[] {i,j,k}, grid,answer);
    				}
    			}
    		}    
    
            Collections.sort(answer);
            return answer;
        }
        
        static void bfs(int[] start, String[] grid, List<Integer> answer) {
    		if(visit[(start[2])][start[0]][start[1]] == 1) return;
    		
    		int cnt = 0;
    	
    		LinkedList<int[]> q = new LinkedList<>();
    		q.add(start);
    		visit[(start[2])][start[0]][start[1]] = 1;
    		
    		while(!q.isEmpty()) {
    		    int[] cur = q.poll();	
    			cnt++;
    			int[] nextIndex = new int[3];
    			
                int nextD = resolveD(grid[cur[0]].charAt(cur[1]), cur[2]);
    			
    			nextIndex[0] = cur[0]+dY[nextD];
    			if(nextIndex[0]>=grid.length) nextIndex[0] = 0;
    			if(nextIndex[0]<0) nextIndex[0] = grid.length-1;
    			
    			nextIndex[1] = cur[1]+dX[nextD];
    			if(nextIndex[1]>=grid[0].length()) nextIndex[1] = 0;
    			if(nextIndex[1]<0) nextIndex[1] = grid[0].length()-1;
    			
    			nextIndex[2] = nextD;
    			
    			if(visit[nextIndex[2]][nextIndex[0]][nextIndex[1]] == 1) break;
    			
    			visit[nextIndex[2]][nextIndex[0]][nextIndex[1]] = 1;
    			q.add(nextIndex);
    		}
    		answer.add(cnt);
    	}
    	
    	static int resolveD(char c, int d) {
    		if(c == 'S') return mapping[0][d];
    		if(c == 'L') return mapping[1][d];
    		else return mapping[2][d];
    	}
    }
    
3차원 경로탐색으로 사이클의 갯수를 세는 문제

문제가 꽤 난해한데 노드안에 들어있는 `S,L,R`에 따라 방향이 상하좌우 4갈래로 나뉘기때문에 2차원 보드가 방향성에 따른 4계층으로 나뉘는 그림을 추상화하면 이해하기 편하다.

예전에 백준에서 3차원 토마토 문제를 플었던 경험이 많이 도움됬다.

[백준 3차원 토마토 문제 참조](https://www.acmicpc.net/problem/7569)

3차원의 경로탐색이란걸 이해하고 나면 노드간 간선은 단 하나뿐이므로 BFS나 DFS로 탐색을 수행하는데는 큰 문제 없다.

방향에 따라 어떤 계층의 노드로 연결되는지는 `int[][] mapping`에 하드코딩하여 위치를 찾게했고 보드의 경계를 벗어날때 반대편 노드로 간선이 이어지는 예외처리를 해주었다.

근데 레벨2 치고는 많이 어렵지않았나 싶다. 백준 골드 3~4정도 느낌


## 50. 튜플
    import java.util.*;
    class Solution {
        public List<Integer> solution(String s) {
    		s = s.substring(2, s.length()-2).replace("},{" ," ");
    		String[] split = s.split(" ");
    		
    		ArrayList<ArrayList<Integer>> arrList = new ArrayList<>();
    		
    		for(String str : split) {
    			ArrayList<Integer> tmp = new ArrayList<>();
    			String[] numS = str.split(",");
    			
    			for(String num : numS) {
    				tmp.add(Integer.parseInt(num));
    			}
    			arrList.add(tmp);
    		}
    		
    		Collections.sort(arrList, (o1, o2) -> o1.size() - o2.size());
    		
    		ArrayList<Integer> answer = new ArrayList<>();
    		answer.add(arrList.get(0).get(0));		
    		
    		for(int i =1; i< arrList.size(); i++) {
    			
    			ArrayList<Integer> before = arrList.get(i-1);
    			ArrayList<Integer> now = (ArrayList<Integer>)arrList.get(i).clone();
    			
    			now.removeAll(before);
    			
    			answer.add(now.get(0));
    		}
            return answer;
        }
    }
    

19 카카오 인턴쉽 기출

주어진 문자열을 2중 배열로 파싱하고 배열의 길이순으로 정렬하면 된다.

인접 배열간 차집합 하면 요소가 반드시 하나만 남게되므로 코너케이스를 다룰게 없어 편했다.


## 51. 수식 최대화
    import java.util.*;
    class Solution {
        static String op = "*+-";
    	static int[] priority;
    
        public long solution(String expression) {
    		long max = 0;	
    		for (int i = 0; i < 3; i++) {
    			for (int j = 0; j < 3; j++) {
    				for (int k = 0; k < 3; k++) {
    					if (i == j || j == k || i == k)
    						continue;
    					priority = new int[] { i, j, k };
    					max = Math.max(max, cal(postExpFrom(expression)));
    				}
    			}
    		}
            return max;
    	}
    
    	static long cal(String postExp) {
    		String[] exp = postExp.split(" ");
    
    		LinkedList<String> calSt = new LinkedList<>();
    
    		long sum = 0;
    
    		for (String n : exp) {
    			if (n.equals("*") || n.equals("-") || n.equals("+")) {
    				long b = Long.parseLong(calSt.pop());
    				long a = Long.parseLong(calSt.pop());
    				switch (n) {
    				case "*":
    					calSt.push(a * b + ""); break;
    				case "-":
    					calSt.push(a - b + ""); break;
    				case "+":
    					calSt.push(a + b + ""); break;
    				}
    				continue;
    			}
    			calSt.push(n);
    		}
    		return Math.abs(Long.parseLong(calSt.pop()));
    	}
    
    	static String postExpFrom(String expression) {
    
    		LinkedList<Character> opSt = new LinkedList<>();
    		StringBuilder post = new StringBuilder();
    
    		for (int i = 0; i < expression.length(); i++) {
    			char c = expression.charAt(i);
    
    			if (c >= '0' && c <= '9') {
    				post.append(c);
    				continue;
    			}
    
    			post.append(" ");
    
    			for (int j = 0; j < 3; j++) {
    				if (c == op.charAt(j)) {
    					if (opSt.isEmpty())
    						opSt.push(c);
    					else {
    						while(!opSt.isEmpty()&&priority[op.indexOf(c)] >= priority[op.indexOf(opSt.peek())]) {
    							post.append(opSt.pop()).append(" ");
    						}
    						opSt.push(c);
    					}
    					break;
    				}
    			}
    		}
    
    		post.append(" ");
    
    		while (!opSt.isEmpty())
    			post.append(opSt.pop()).append(" ");
    
    		return post.toString();
    	}
    }
    

2020 카카오 인턴십 기출

1. 중위표기법으로 주어진 계산식을 후위표기법으로 바꾸고 
2. 이를 계산할 계산기를 구현해서, 
3. 총 6가지의 연산 우선순위별 계산값을 비교하면 된다.

풀이법 자체는 후위표기식이 워낙 유명하므로 금방 떠올렸는데 구현하는데 시간이 꽤 걸렸다. 괄호가 없었던게 그나마 다행

실전에서 이정도 풀이를 제시간안에 풀수있을지 걱정된다.


## 52. 거리두기 확인하기

    import java.util.*;
    class Solution {    
        static int[] dY = {-1,1,0,0};
    	static int[] dX = {0,0,-1,1};
    	static boolean[][] visit;
        
        public List<Integer> solution(String[][] places) {
    		ArrayList<Integer> answer = new ArrayList<>();
    		
    		for(String[] place : places) {	
    			ArrayList<int[]> pList = findP(place);
    			boolean isSafe = true;
    			for(int[] person : pList) {
    				if(!isSafe) break;
    				isSafe = bfs(place,person);
    			}	
    			if(isSafe) answer.add(1);
    			else answer.add(0);
    		}
    		return answer;
    	}
    
    	static ArrayList<int[]> findP(String[] places){
    		
            ArrayList<int[]> list = new ArrayList<>();
    		
    		for(int i = 0; i< places.length; i++) {
    			for(int j = 0; j< places[0].length(); j++) {
    				if(places[i].charAt(j) == 'P') list.add(new int[] {i,j});
    			}
    		}
    		return list;
    	}
    	
    	static boolean bfs(String[] places, int[] pIndex) {
    		LinkedList<int[]> q = new LinkedList<>();
    		visit = new boolean[places.length][places[0].length()];
    		q.add(new int[] {pIndex[0],pIndex[1], 0});
    		visit[pIndex[0]][pIndex[1]] = true;
    		
    		while(!q.isEmpty()) {
    			int[] cur = q.poll();
    			
    			for(int i =0; i< 4 ; i++) {	
    				int[] next = new int[3];
    				
    				next[0] = cur[0] + dY[i];
    				if(places.length<= next[0] ||next[0] <0) continue;
    				
    				next[1] = cur[1] + dX[i];
    				if(places[0].length()<= next[1] ||next[1] <0) continue;
    				
    				next[2] = cur[2] +1;
    				
    				if(places[next[0]].charAt(next[1]) == 'X' ||
    						visit[next[0]][next[1]]) continue;
    				if(places[next[0]].charAt(next[1]) == 'P' && next[2] <=2) 
    					return false;
    				
    				q.add(next);
    				visit[next[0]][next[1]] = true;
    			}
    		}
    		return true;
    	}
    }

2021 카카오 인턴십 기출이며 bfs 응용

대기실별 모든 사람들간 거리를 탐색하며 2이하인 경우가 있다면 안전하지 않고, 없다면 안전하다고 반환해주면 된다.

## 53. 뉴스 클러스터링
    import java.util.*;
    class Solution {
        public int solution(String str1, String str2) {
    		ArrayList<String> set1 = makeSet(str1);
    		ArrayList<String> set2 = makeSet(str2);
    		
    		ArrayList<String> inter = new ArrayList<>();
    		ArrayList<String> uni = new ArrayList<>();
    		
    		for(String s : set1) {
    			if(set2.remove(s)) inter.add(s);
    			uni.add(s);
    		}
    		uni.addAll(set2);
            if(uni.size() == 0) return 65536;
            return ((65536*inter.size())/uni.size());		
    	}
    
    	static ArrayList<String> makeSet(String str1) {
    		ArrayList<String> set1 = new ArrayList<>();
    		str1 = str1.toLowerCase();
    		
    		for(int i = 0; i< str1.length()-1; i++) {
    			String tmp = str1.substring(i,i+2);
    			
    			if(tmp.charAt(0)<'a'||tmp.charAt(0)>'z'||
    					tmp.charAt(1)<'a'||tmp.charAt(1)>'z') continue;			
    			set1.add(tmp);
    		}
    		return set1;
    	}
    }
    
2018 카카오 블라인드 채용 기출로 합집합 차집합 문제

중복을 허용하는 다중집합이므로 removeAll 같은 메서드를 사용해서는 안되며 하나하나 조회를 해서 제거해야한다.

## 54. 괄호 변환
    import java.util.*;
    class Solution {
        public String solution(String p) {
            String answer = divide(p);
            return answer;
        }
        static String divide(String p) {
    		if(p.length() == 0) return "";
    
    		LinkedList<Character> st = new LinkedList<>();
    		boolean right = false;
    		int subIndex = 0;
    
    		for (int i = 0; i < p.length(); i++) {
    			char c = p.charAt(i);
    			if (i == 0 && c == '(') right = true;
    
    			if (right) {
    				if (c == ')') st.pop();
    				else st.push(c);
    
    				if (st.isEmpty()) {
    					subIndex = i;
    					break;
    				}
    			}
    			
    			else {
    				if (c == '(') st.pop();
    				else st.push(c);
    
    				if (st.isEmpty()) {
    					subIndex = i;
    					break;
    				}
    			}
    		}	
    		String u = p.substring(0,subIndex+1);
    		String v = p.substring(subIndex+1);
    
    		v = divide(v);
    		
    		if(right) return u + v;
    
    		StringBuilder sb = new StringBuilder();
    		sb.append("(").append(v).append(")");
    		
    		for(int i = 1; i< u.length()-1; i++) {
    			if(u.charAt(i) =='(') sb.append(")");
    			if(u.charAt(i) ==')') sb.append("(");
    		}
    		return sb.toString();
    	}
    }

2020 카카오 블라인드 채용 기출

문제 요구사항과 구현 설명이 잘 설명되어있기때문에 지문대로 잘 따라 구현하면된다. 
괄호문제 답게 스택 응용