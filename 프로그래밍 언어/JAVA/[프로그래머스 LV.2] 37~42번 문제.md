## 37. 위장

    import java.util.*;
    class Solution {
        public int solution(String[][] clothes) {
    
            Map<String, Integer> map = new HashMap<>();
    		buildMap(map,clothes);
    	
    		int cnt = 1;
    		for(String s:map.keySet()) {			
    			cnt *= map.get(s)+1;			
    		}
    		return cnt-1;
    	}	
        
    	static void buildMap(Map<String, Integer> map, String[][] clothes) {
    		for(String[] c : clothes) {		
    			if(map.containsKey(c[1])) {
    				map.put(c[1], map.get(c[1])+1);
    			}
    			else {
    				map.put(c[1], 1);
    			}
    		}
    	}
    }
    

옷입는 경우의 수를 구하는 수학문제

해시를 사용해 옷을 종류별로 구분하고 경우의 수만 구하면된다.

dfs로 직접 모든 가짓수를 탐색할 수 있기는 한데 해당 문제에서는 시간초과가 뜬다. 어차피 백트래킹할 이유가 없으므로 수학으로 풀자

## 38. 배달
    import java.util.*;
    class Solution {
        static int[] d;
        public int solution(int N, int[][] road, int K) {
    		d = new int[N+1];
    		Arrays.fill(d, Integer.MAX_VALUE);
    
    		di(1,buildGraph(N, road));
    		
    		int cnt = 0;
    		for(int dis : d) {
    			if(dis<=K) cnt++;
    		}
    	    return cnt;
        }
    	
    	static class Edge implements Comparable<Edge>{
    		int node;
    		int dis;
    		
    		public Edge(int b, int c) {
    			node = b;
    			dis = c;
    		}
    		
    		@Override
    		public int compareTo(Solution.Edge o) {
    			return this.dis - o.dis;
    		}
    	}
    
    	static ArrayList<ArrayList<Edge>> buildGraph(int N, int[][] road) {
    		
    		ArrayList<ArrayList<Edge>> graph = new ArrayList<>();
    		
    		for(int i = 0; i<= N; i++) {
    			graph.add(new ArrayList<Edge>());
    		}
    		for(int[] r : road) {
    			graph.get(r[0]).add(new Edge(r[1],r[2]));
    			graph.get(r[1]).add(new Edge(r[0],r[2]));
    		}
    		return graph;
    	}
    	
    	static void di(int startNode, ArrayList<ArrayList<Edge>> graph) {
    		
    		PriorityQueue<Edge> pq = new PriorityQueue<>();
    
    		pq.add(new Edge(startNode,0));
    		d[startNode] = 0;
    		
    		while(!pq.isEmpty()) {
    			
    			Edge e = pq.poll();	
                
    			if(d[e.node] < e.dis) continue;
                
    			for(int i =0; i<graph.get(e.node).size();i++) {			
    				Edge tmpEdge = graph.get(e.node).get(i);
    				if(d[tmpEdge.node] < d[e.node] + tmpEdge.dis) continue;
    				d[tmpEdge.node] = d[e.node] + tmpEdge.dis;
    				pq.add(new Edge(tmpEdge.node,d[tmpEdge.node]));
    			}	
    		}
    	}
    } 
    

  
X 노드에서 출발하여 모든 노드의 최단거리 탐색하는 다익스트라 알고리즘을 사용한 길찾기 기본유형

위의 풀이는 우선순위 큐를 사용해 시간복잡도를 개선한 다익스트라로 O((N+E)logN) (N는 정점의 개수, E는 한 정점의 주변 노드) 시간복잡도를 갖는다.

양방향 간선인것만 고려하면 크게 헤깔릴일은 없다.


## 39. 괄호 회전하기

    import java.util.*;
    class Solution {
        public int solution(String s) {
    		StringBuilder sb = new StringBuilder();
    		sb.append(s);
            
    		int cnt = 0;
    		
    		for(int i =0; i<s.length();i++) {			
    			if(check(sb)) cnt++;
    			rotate(sb);
    		}
    		return cnt;
    	}
    	
    	static void rotate(StringBuilder sb) {
    		sb.append(sb.charAt(0));
    		sb.deleteCharAt(0);
    	}
    	
    	static boolean check(StringBuilder sb) {
    		LinkedList<Character> st = new LinkedList<>();
    		
    		for(int i =0; i<sb.length(); i++) {
    		
    			char c = sb.charAt(i);
    				if(c == '}'){
    				if(!st.isEmpty()&&st.peek() == '{') st.pop();
    				else return false;
    			}else if(c == ']'){
    				if(!st.isEmpty()&&st.peek() == '[') st.pop();
    				else return false;
    			}else if(c == ')'){
    				if(!st.isEmpty()&&st.peek() == '(') st.pop();
    				else return false;
    			}
    			else st.push(c);
    		}
    		if(st.isEmpty()) return true;
    		else return false;
    	}	
    }
    

회전은 큐 동작을 응용하고 괄호 검토는 스택을 사용하여 구현하면된다. 



## 40. 후보키

    import java.util.*;
    class Solution {
    
        static ArrayList<ArrayList<Integer>> checkList = new ArrayList<>();
       
        public int solution(String[][] relation) {
            for(int i =1; i<=relation[0].length;i++) {
                String[] sArr = new String[relation.length];
                Arrays.fill(sArr, "");
                dfs(relation, i, 0, new ArrayList<>(),0,sArr);
            }
            return checkList.size();
        }
    
        static void dfs(String[][] relation, int size, int d, ArrayList<Integer> check, int index, String[] sArr) {
    
            if(size == d) return;
    
            loop:for(int i =index; i<relation[0].length; i++) {
    
                check.add(i);
                for(ArrayList<Integer> arr : checkList) {
    
                    if(check.containsAll(arr)) {
                        check.remove(check.size()-1);
                        continue loop;
                    }
                }
    
                Set<String> set = new HashSet<>();
                boolean isCandi = true;
    
                String[] tmp = new String[relation.length];
    
                for(int j=0; j<relation.length; j++ ) {
    
                    tmp[j] = sArr[j]+" "+relation[j][i];
                    if(set.contains(tmp[j])) isCandi = false;
                    set.add(tmp[j]);
                }
    
                if(isCandi) {
                    checkList.add((ArrayList<Integer>)check.clone());
                    check.remove(check.size()-1);
                    continue;
                }
                else dfs(relation,size,d+1,check, i+1,tmp);
                check.remove(check.size()-1);
            }       
    
        }
    }
    

카카오 19년 블라인드 채용 기출 빡구현 유형

풀면서 엄청 고생한 문제로 풀이 자체도 만족스럽지 못하다.

일단 아이디어는

1. 속성을 합치는 복합키도 후보키가 될 수 있으므로 후보키는 최대 주어진 속성(N)만큼 속성이 합쳐진다. 따라서 속성이 1인 후보키부터 N인 후보키까지 반복문으로 탐색

2. 탐색은 dfs로 수행하며 각 속성 최대 사이즈별로 dfs 스택을 쌓고 해시 set을 사용하여 유일성 검증

3. 이미 탐색한 합성 후보키때문에 현재 탐색 후보키의 최소성이 깨지는지 검증로직을 추가

순으로 구현을 했는데 이렇게하면 size가 1인 후보키를 탐색완료한후 반복문으로 다시 size가 1인 후보키부터 2인 후보키를 만들어나가고 이걸 계속반복하므로 시간복잡도가 매우 올라가게된다.

dfs로 조합풀이를 하다보니 오작동도 많이 나오고 효율적인 풀이가 안되는 것 같은데 이참에 비트연산으로 조합푸는걸 연습해봐야겠다.

## 41. 순위 검색

    import java.util.*;
    class Solution {
        public int[] solution(String[] info, String[] query) {
    		Map<String, List<Integer>> map = new HashMap<>();
    
    		for (String i : info) {
    			String[] split = i.split(" ");
    
    			for (int j = 0; j <= 4; j++) {
    				dfs(split, 0, j, "", map,0);
    			}
    		}
    
    		for (List<Integer> list : map.values()) {
    			Collections.sort(list);
    		}
    
    		int[] answer = new int[query.length];
    
    		for (int i = 0; i < query.length; i++) {
    
    			String[] split = query[i].split(" and ");
    			String[] tmp3N4 = split[3].split(" ");
    			split[3] = tmp3N4[0];
    
    			StringBuilder sb = new StringBuilder();
    
    			for (int j = 0; j < 4; j++) {
    				if (split[j].equals("-"))
    					continue;
    				sb.append(split[j]);
    			}
    
    			List<Integer> list = map.getOrDefault(sb.toString(), new ArrayList<>());
    
    			answer[i] = binarySearch(list, Integer.parseInt(tmp3N4[1]));
    
    		}
            return answer;
    	}
    
    	private static int binarySearch(List<Integer> list, int target) {
    		
    		if (list.size() == 0)
    			return 0;
    
    		int s = 0;
    		int e = list.size();
    
		while (s < e) {
			int mid = (s + e) / 2;
			if (list.get(mid) >= target) {
				e = mid;
			} else {
				s = mid+1;
			}
		}

		return list.size()-e;
    	}
    
    	static void dfs(String[] split, int d, int size, String str, Map<String, List<Integer>> map, int index) {
    
    		if (d == size) {
    			if (map.containsKey(str)) {
    				map.get(str).add(Integer.parseInt(split[4]));
    			} else {
    				List<Integer> l = new ArrayList<>();
    				l.add(Integer.parseInt(split[4]));
    				map.put(str, l);
    			}
    			return;
    		}
    
    		for (int i = index; i < split.length-1; i++) {
    			String tmp = str;
    			tmp += split[i];
    			dfs(split, d + 1, size, tmp, map, i+1);
    		}
    	}
    }
    

올해 카카오 블라인드 채용 기출

문제자체는 40번보다는 훨씬 쉬웠다. 지문에서 시간복잡도를 고려하라고 명시되있으므로 

1. info로 제공된 문자열을 쪼개고 40번문제처럼 nC1~nCn 조합을 모두 구해 해시맵의 key로 저장
2. 이때 value는 list로 만들어 점수를 저장후 내림차순 정렬
3. 이후 LowerBound 이분탐색으로 조건에 맞는 지원자수를 구하면 된다.

이 문제 역시 조합을 사용하는 경우의수 구하기를 dfs로 풀었는데 여기서 오류도 많이나고 디버깅하느라 시간이 꽤 소비됬다.

비트연산으로 순열 조합푸는 연습을 하도록하자

## 42. 예상 대진표

    class Solution
    {
        public int solution(int n, int a, int b){
            
            int answer = 0;
            for(int i =1; ; i++){
                
                a = (a+1)/2;
                b = (b+1)/2;
                
                if(a == b) {
                    answer = i;
                    break;
                }
            }
            
            return answer;
        }
    }
    

단순 산수 