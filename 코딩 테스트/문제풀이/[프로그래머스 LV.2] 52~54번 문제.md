
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

파티션을 막힌 벽이라 생각하고 `cnt++` 하는 길찾기 유형처럼 풀이했다.

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

문제 요구사항과 구현 설명이 잘 설명되어있기때문에 지문대로 잘 따라 구현하면 된다. 

괄호문제 답게 스택을 응용해서 풀었다.