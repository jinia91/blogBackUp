## 43. 게임 맵 최단거리

    import java.util.*;
    class Solution {
        public int solution(int[][] maps) {
            return bfs(maps);
        }
        static int bfs(int[][] map) {
            LinkedList<int[]> q = new LinkedList<>();
    
            if(map[0][0] == 0) return -1; 
    
            q.add(new int[] {0,0,1});
            map[0][0] = 0;
    
            int[] dy = {-1,1,0,0};
            int[] dx = {0,0,-1,+1};
    
            while(!q.isEmpty()) {
    
                int[] cur = q.poll();
    
                for(int i =0; i<4; i++) {
    
                    int nextY = cur[0]+dy[i];
                    int nextX = cur[1]+dx[i];
    
                    if(nextY<0||nextY>=map.length||
                            nextX<0||nextX>=map[0].length) continue;
    
                    if(map[nextY][nextX] == 0) continue;
    
                    if(nextY == map.length-1 && nextX == map[0].length-1) {
                        return cur[2]+1;
                    }
    
                    int[] next = new int[] {nextY, nextX,cur[2]+1};
                    q.add(next);
                    map[nextY][nextX] = 0;
                }
            }
            return -1;
        }
    }
    

가중치가 없는 최단거리 길찾기 유형의 기본으로 DFS나 BFS를 사용하여 탐색을 하면된다.

개인적으로 DFS는 스택오버플로우를 일으킬 위험이 있기 때문에 둘다 가능한 상황에서는 BFS쪽을 더 선호한다. 해당문제 역시 BFS로 풀었다.

맵의 방문 여부를 길을 지우는 방식으로 구현하여 메모리를 아끼고 위치정보와 이동거리는 int[3] 배열을 사용했다.

## 44. 조이스틱

    import java.util.*;
    class Solution {
        static int cnt;
        public int solution(String name) {
    		boolean[] changed = new boolean[name.length()];
    		int i = 0;
    
    		while (true) {
    			change(name, i, changed);
    			int tmpL = findLeft(i, name, changed);
    			int tmpR = findRight(i, name, changed);
    
    			if(tmpL == 0) break;
    			
    			int calL = i-tmpL>0 ? i-tmpL : name.length()-tmpL +i;
    			int calR = tmpR - i>0? tmpR - i: name.length()-i +tmpR;
    			if(calL >= calR) {
    				i = tmpR;
    				cnt += calR;
    			}
    			else {
    				i = tmpL;
    				cnt += calL;
    			}
    		}
            return cnt;
    	}
    
    	static int findRight(int index, String name, boolean[] changed) {
    		for(int i =index; i<name.length(); i++) {
    			if(name.charAt(i)!= 'A'&& !changed[i]) return i;
    		}
    		for(int i=0; i<index;i++) {
    			if(name.charAt(i)!= 'A'&& !changed[i]) return i;			
    		}
    		
    		return 0;
    	}
    
    	static int findLeft(int index, String name, boolean[] changed) {
    		for (int i = index; i >= 0; i--) {
    			if (name.charAt(i) != 'A' && !changed[i]) return i;
    		}
    		
    		for (int i = name.length()-1; i > index; i--) {
    			if (name.charAt(i) != 'A' && !changed[i]) return i;
    		}
    		return 0;
    	}
    
    	static void change(String name, int i, boolean[] changed) {
    		char targetC = name.charAt(i);
    		if (targetC <= 'M') {
    			cnt += targetC - 'A';
    		} else {
    			cnt += 'Z' - targetC + 1;
    		}
    		changed[i] = true;
    	}
    }
    

그리디 유형으로 분류되어있긴 한데 감을 못 잡아서 그냥 구현으로 풀었다.



## 45. 소수찾기

    class Solution {
        static boolean[] board = new boolean[10000000];
        static boolean[] visited = new boolean[10000000];
        static boolean[] checked;
        static int cnt;
        public int solution(String numbers) {
            prime();
            checked = new boolean[numbers.length()];
            dfs(numbers,0,"");
            return cnt;
        }
        
        static void dfs(String numbers,int d, String str){
            
            if(d == numbers.length()) return;
            
            for(int i =0; i<numbers.length();i++){
                if(checked[i]) continue;
                checked[i] = true;
                String tmp = str + (numbers.charAt(i)-'0');
                if(!board[Integer.parseInt(tmp)]&&
                    !visited[Integer.parseInt(tmp)]) {
                    visited[Integer.parseInt(tmp)] = true;
                    cnt++;}
                dfs(numbers,d+1,tmp);
                checked[i] = false;
            }      
        }    
        
        
        static void prime(){
           
            board[0] = true;
            board[1] = true;
            
            for(int i=2; i<=Math.sqrt(board.length); i++){
                    if(board[i]) continue;
                for(int j = 2; j*i<board.length;j++){
                    board[i*j] = true;
                }
            } 
        }   
    }
    
숫자들을 조합하여 소수를 만드는 유형으로 에라토테네스의 체와 DFS를 사용한  순열로 구했다.

## 46. 가장 큰 수

    import java.math.*;
    import java.util.*;
    import java.util.stream.*;
    class Solution {
        public String solution(int[] numbers) {
    
    		List<Integer> ns = Arrays.stream(numbers).boxed().collect(Collectors.toList());
    
    		Collections.sort(ns, new Comparator<Integer>() {
    			@Override
    			public int compare(Integer o1, Integer o2) {
    				 String a = o1+ "" +o2;
    				 String b = o2+ "" +o1;
            		return -(new BigInteger(a)).compareTo(new BigInteger(b));
    			}
    		});
    		
    		StringBuilder sb = new StringBuilder();
    		for(Integer x : ns) {
    			sb.append(x);
    		}
    	    return new BigInteger(sb.toString()).toString();	    
        }
    }
    
숫자를 이어 붙일때 더 큰수가 되도록 정렬하는 문제로 요구사항대로 정렬을 구현하기만 하면 된다.

## 47. 프린터

    import java.util.*;
    class Solution {
        public int solution(int[] priorities, int location) {
            
            PriorityQueue<Docu> pq = new PriorityQueue<>();
            LinkedList<Docu> q = new LinkedList<>();
            for(int i = 0; i< priorities.length; i++){
                pq.add(new Docu(priorities[i],i));
                q.add(new Docu(priorities[i],i));
            }
            return print(location,q, pq);   
        }
        
        static int print(int l, LinkedList<Docu> q,PriorityQueue<Docu> pq){
            
            int cnt = 0;
            
            while(!q.isEmpty()){
                Docu target = pq.poll();
                Docu tmp = null;
                while(true){  
                    tmp = q.poll();
                    if(target.priority == tmp.priority) {
                        cnt++;
                        break;
                    }
                    q.add(tmp);
                }
                    if(tmp.index == l) break;
            }
            return cnt;
        }
           
        static class Docu implements Comparable<Docu>{ 
            int priority;
            int index;
                
            public Docu(int p, int i){
                this.priority = p;
                this.index = i;
            }
            
            @Override
            public int compareTo(Docu d){
                return d.priority-this.priority;
            }      
        }   
     }
     
예전에 백준에서 똑같은 문제를 푼적 있어서 쉽게 풀었다.

큐를 사용한 스케쥴링 구현 유형이며 큐 두개를 사용하는게 핵심이다. 

내림차순으로 뽑은 문서의 우선순위와 실제 대기열 큐에서 뽑을 문서의 우선순위가 같아질때까지 문제가 요구하는 대기열 조작을 해주면된다.



## 48. 전화번호 목록
    import java.util.*;
    class Solution {
        public boolean solution(String[] phone_book) {
            
    		Arrays.sort(phone_book, (o1, o2) -> o1.length() - o2.length());
            
    		Set<String> set = new HashSet<>();
    		int l = phone_book[0].length();
    		boolean flag = false;
    		boolean answer = true;
            
    		loop:for (String s : phone_book) {
    			if (s.length() != l) flag = true;
    			if (!flag) {
    
    				if(set.contains(s)) {
    					answer = false;
    					break;
    				}
    				
    				set.add(s);
    				continue;
    			}
    			
    			for(int i = l; i<=s.length(); i++) {
    				
    				String sub= s.substring(0,i);
    				
    				if(set.contains(sub)) {
    					answer = false;
    					break loop;
    				}
    			}
    				set.add(s);
    		}
            return answer;
        }
    }
    

어제 풀었던 41번 카카오 기출 순위검색 문제와 유사한 문제였다.

전화번호의 접두 여부를 판단하기 위해 가능한 길이수를 모두 subString 처리하며 contains로 확인하는것이 핵심

