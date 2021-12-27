## 19. n^2 배열 자르기

    class Solution {
        public int[] solution(int n, long left, long right) {
         
            int size = (int)(right - left) +1;
            
            int[] answer = new int[size];
            
            for(int i = 0; i<answer.length; i++){
                answer[i] = Math.max((int)Math.floor(left / n), (int)(left++ % n)) + 1;
            }
            
            return answer;
                    
        }
    }
    
n이 최대 10^7까지 주어지기 때문에 2차원 배열을 직접 만드려하면 시간초과나 메모리 초과가 떠버릴것이다.

문제 의도대로 행을 이어붙여서 규칙성을 찾아 left부터 right까지만 배열을 작성해야 하는 규칙찾기 유형



## 20.  점프와 순간이동

    import java.util.*;
    public class Solution {
        public int solution(int n) {
         
        int cnt = 0;
            
            while(n!=0){
                if(n%2 == 0) n = n/2; 
                else {
                    n= n/2;
                    cnt++;
                }
            }
        
            return cnt;
            
        }
    }
    
BFS 문제인줄 알았는데 그냥 수학문제였다. BFS로 접근시 시간 효율성 테스트를 통과하지 못한다.

2로 나눌때 나머지가 나오는 상황에서만 +1 이동을한다는점을 파악해 2로 계속 나누면서 나머지가 나오는 상황을 체크하는 규칙 찾기 유형



## 21. 이진변환 반복하기

    class Solution {
        public int[] solution(String s) {
            
       		int[] answer = new int[2];
    		
    		while (!s.equals("1")) {
    			String tmp = s.replaceAll("0", "");
    			
    			answer[0]++;
    			answer[1] += s.length()-tmp.length();
    
    			s = Integer.toBinaryString(tmp.length());
    			
    		}
            return answer;
        }
    }
    
간단한 문자열 다루기

## 22. 캐시
    import java.util.*;
    class Solution {
        public int solution(int cacheSize, String[] cities) {
         	LinkedList<String> cache = new LinkedList<>();
    		
    		int answer = 0;
    		
    		for(String city : cities) {
    			
                city = city.toLowerCase();  
                
    			if(cache.contains(city)) {
    				answer++;
    				cache.remove(city);
    				cache.add(city);
    				continue;
    			}		
    			if(cache.size()<cacheSize) {
    				cache.add(city);
    				answer += 5;
    				continue;
    			}		
                if(cache.size() != 0){
                cache.remove(0);
    			cache.add(city);
                }
    			answer += 5;
    		}
            
            return answer;
        }
    }
    
LRU 알고리즘의 캐시를 구현하는 문제로 2018 카카오 블라인드 채용 기출이다.

LRU는 가장 마지막에 사용한 데이터부터 삭제하는 알고리즘으로 운영체제 스케쥴링이나 캐시 등 다양한곳에서 사용된다.

해당 풀이에서는 LRU 구현을 위해 LinkedList를 사용해서 재갱신시 해당 데이터의 연결을 끊고 제일 뒤로 재연결하여 갱신하는 방식으로 구현했다.

같은 List 인터페이스 구현체인 ArrayList로도 구현은 가능하지만 잦은 추가 삭제에는 LinkedList가 더 효율적이라 판단했다.

## 23. 모음 사전

    import java.util.*;
    class Solution {
        
        static ArrayList<String> dic = new ArrayList<>();
    	static StringBuilder sb = new StringBuilder();
    	static String[] str = {"A","E","I","O","U"};
        
        public int solution(String word) {
    		buildDic(1);
    		Collections.sort(dic);
    		return dic.indexOf(word) + 1;
    	}
    
    	static void buildDic(int cur) {
    
    		for (String s : str) {
    			if(cur == 6) return;
    			
    			sb.append(s);
    			dic.add(sb.toString());
    			buildDic(cur + 1);
    			sb.setLength(cur - 1);
            }
    	}
    }
    

문자 `{"A","E","I","O","U"}`를 5개 층의 그래프로 삼아 DFS 탐색을 통해 모든 탐색에 문자열을 list에 담아서 풀었다.

## 24. 전력망 둘로 나누기

    import java.util.*;    
    class Solution {
        public int solution(int n, int[][] wires) {
            return bfs(wires);
        }
    
        static int bfs(int[][] wires) {
    		ArrayList<ArrayList<Integer>> graph = new ArrayList<>();		
    		buildGraph(wires, graph);
    
    		int mid = Integer.MAX_VALUE;
    		for (int i = 0; i < wires.length; i++) {
        			
    			boolean[] marked = new boolean[wires.length+2];
    			
    			int cnt = 1;
    
    			LinkedList<Integer> q = new LinkedList<>();
    			q.add(wires[i][0]);
    
    			while (!q.isEmpty()) {
    				int node = q.poll();
    				marked[node] = true;
    
    				for (int j = 0; j < graph.get(node).size(); j++) {
    					int next = graph.get(node).get(j);
                        
                        if((node == wires[i][0] && next == wires[i][1])||
                          (node == wires[i][1] && next == wires[i][0])) continue;
                        
    					if (!marked[next]) {
    						marked[next] = true;
    						cnt++;
    						q.add(next);
    					}
    				}
    			}
    			mid = Math.min(Math.abs(((wires.length + 1) - cnt)-cnt), mid);
    		}
    		return mid;
    	}
    
    	static void buildGraph(int[][] wires, ArrayList<ArrayList<Integer>> graph) {
    		for (int j = 0; j <= wires.length + 1; j++) {
    			graph.add(new ArrayList<Integer>());
    		}
    
    		for (int j = 0; j < wires.length; j++) {
    			graph.get(wires[j][0]).add(wires[j][1]);
    			graph.get(wires[j][1]).add(wires[j][0]);
    		}
    	}
    }





BFS, DFS로 그래프 탐색을 할 경우 인접행렬(`t[][]`) 로 구현하는 방법과 인접 리스트(`위의 풀이 예시 List<List<T>>`) 로 구현하는 두가지 방법이 존재한다.

### 인접 행렬
노드가 1,2,3,4 4개 있다고 가정할 때


|| 1 |2  |3  | 4 |
|---| --- | --- | --- | --- |
|1|- |  x| o | o |
|2| x | - | o |  o|
|3|  o| o | - | x |
|4|  o| o | x | - |

표처럼  연결된 좌표에 값을 넣어주어 그래프를 표현하는 방식이다. 보통 연결이 되지 않은 노드끼리는 Integer.MAX_VALUE를 주게 된다.

### 인접 리스트
위의 풀이 예시처럼 

먼저 List에 주어진 노드 갯수만큼 List를 추가하고 보통 index 0번째 리스트는 무시하고 1부터

    list.get(1).add(3);
    list.get(1).add(4);
    list.get(2).add(3);
    list.get(2).add(4);
    list.get(3).add(1);
    list.get(3).add(2);
    list.get(4).add(1);
    list.get(4).add(2);
    
노드에 연결된 정보를 저장하여 그래프를 표현한다.

### 인접 행렬과 인접 리스트 차이
- 인접 행렬은 모든 관계를 저장하므로 노드개수가 많을경우 **메모리 낭비가 심해진다.**
- 인접 리스트는 연결된 노드만 사용하므로 메모리가 효율적으로 사용된다.
- 연결되었는지 확인하기 위해 리스트를 하나하나 조회하므로 **속도가 느리다.**


### 풀이

BFS나 DFS를 사용해 그래프를 탐색하는 문제로 간선을 순회하며 끊는 로직이 들어가는 응용문제였다.

1. 주어진 간선으로 ArrayList<ArrayList\<Integer>> 이중구조로 그래프를 표현
2. 반복문으로 간선을 하나씩끊었다 가정하고 
3.  해당 그래프에서 **BFS탐색**으로 연결된 노드들 세기
4. 끊었던 간선은 다시 연결하고 다음 간선으로 가서 다시 2 반복

순으로 생각하여 문제를 풀이했는데 인접리스트를 이용해 문제를 풀었더니 코드가 많이 길어진다고 느껴졌다.

개인적으로 가독성이 더 좋게 느껴져서 그래프 탐색시 인접 리스트 방식으로 구현해왔는데, 코테 특성상 짧은 코드의 생산성도 무시할 수 없기 때문에 인접 행렬방식도 연습해야겠다고 느꼈다.

또한 인접 행렬로 풀이를 하면 속도가 10배이상 차이나는것도 충격이였다.

문제에서 노드 최대수가 100개밖에 안주어졌는데 이런경우라면 인접 행렬방식이 훨씬 좋은거 같다.