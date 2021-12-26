## 13. 압축
    
    import java.util.*;
    class Solution {
        public List<Integer> solution(String msg) {
           	Map<String, Integer> dic = new HashMap<>();
    
    		for (int i = 'A', j = 1; i <= 'Z'; i++) {
    			dic.put((char) i + "", j++);
    		}
    
    		List<Integer> answer = new ArrayList<>();
    
    		for (int i = 0; i < msg.length();) {
    
    			char c = msg.charAt(i);
    			StringBuilder sb = new StringBuilder();
    
    			sb.append(c);
    			int tmp = 0;
    			while (dic.containsKey(sb.toString())) {
    
    				tmp = dic.get(sb.toString());
    				if (i == msg.length()-1) {
    					i++;
    					break;
    				}
    				sb.append(msg.charAt(i+1));
    				i++;
    			}
    	
    			answer.add(tmp);
    			dic.put(sb.toString(), dic.size() + 1); 
    			
    		}
    
            return answer;
        }
    }
    
2018 카카오 블라인트 채용 기출

압축을 위한 사전을 해시맵으로 처리해주는 아이디어만 떠올리면 문자열 파싱자체는 크게 어렵지 않다.


##    14. 가장 큰 정사각형 찾기
    class Solution{
        public int solution(int [][]board){
    		int max = 0;	
                    
    		for(int i = 0; i< board.length; i++) {		
    			for(int j = 0; j< board[0].length; j++) {			        
                    
                    if(board[i][j] == 0) continue;
                    
                    if(i>0&&j>0){
                        board[i][j] = Math.min(board[i-1][j], 
                                      Math.min(board[i][j-1], 
                                               board[i-1][j-1]))
                            +board[i][j];
    			    } 
                    max = Math.max(board[i][j], max);
    		    }
            }
            return max*max;
        }
    }
    
대표적인 2차원 DP 유형

이전에 풀어봤던 땅따먹기 문제랑 비슷하며 코너케이스만 잘 생각하면 어렵지 않게 풀 수 있다.


## 15. 방금 그곡
    import java.util.*; 
    class Solution {
        public String solution(String m, String[] musicinfos) {
        	
            String[] tmpAnswer = new String[2];
    
    		for (String str : musicinfos) {
    			String[] tmp = str.split(",");
    
    			int playTime = getPlayTime(tmp[0], tmp[1]);
    
    			List<String> pList = new ArrayList<>();
    			parsing(tmp[3], pList);
    
    			StringBuilder sb = new StringBuilder();
    
    			for (int i = 0; i < playTime; i++) {
    				sb.append(pList.get(i % pList.size()));
    			}
    
    			if (sb.toString().contains(m)) {                    
            		if(!sb.toString().replaceAll(m+"#", " ").contains(m)) continue;
    				if (tmpAnswer[1]==null||
    					Integer.parseInt(tmpAnswer[0]) < playTime) {
    					
                        tmpAnswer[0] = playTime + "";
    					tmpAnswer[1] = tmp[2];
    				
                    }
    			}
    		}
            
            if(tmpAnswer[1] == null) return "(None)";
            return tmpAnswer[1];
    		
    	}
    
    	static void parsing(String tmp, List<String> parse) {
    		for (int i = 0; i < tmp.length(); i++) {
    			char c = tmp.charAt(i);
    			if ((c == 'A' || c == 'C' || c == 'D' || c == 'E' || c == 'F' || c == 'G') && i + 1 < tmp.length()
    					&& tmp.charAt(i + 1) == '#') {
    
    				parse.add(c + "" + tmp.charAt(++i));
    			}else {
    				parse.add(c + "");
    			}
            }
    	}
    
    	static int getPlayTime(String a, String b) {
            int s = (60 * Integer.parseInt(a.substring(0, 2)) + Integer.parseInt(a.substring(3)));
            int e = (60 * Integer.parseInt(b.substring(0, 2)) + Integer.parseInt(b.substring(3)));
            return e - s;
        }
    }
    

2018 카카오 블라인드 채용 기출

푸는데 1시간은 걸린거 같다. 많이 헤멨던 문제.

1. 음악 재생 시간 구하기
2. 음을 토큰화해서 저장
3. 토큰화된 음을 재생시간만큼 반복
4. 반복된 음에 찾는 멜로디가 존재하는지 판별
5. 순차적으로 판별하면서 조건이 맞으면서 재생시간이 더 긴 곡이 나타나면 갱신

위의 순서로 생각하고 작성했는데 2의 음을 토큰화하는 로직과 4의 멜로디 판별에서 계속 오류가 터져서 많이 헤맸다.

위의 풀이는 주어진 음 12개를 #이 존재하는지 판별하며 토큰화시키는데 이보다는 #이 붙은 음을 다른 한글자`H,I,J,K 등`으로 `replaceAll` 시켜서 작업하는게 훨씬 나았을거 같다.

## 16. 방문 길이

    import java.util.*;
    class Solution {
        public int solution(String dirs) {
            
         		int x = 0, y = 0;
    
    		Set<String> set = new HashSet<>();
    		StringBuilder sb = new StringBuilder();
    
    		for (int i = 0; i < dirs.length(); i++) {
    			
    
    			if (dirs.charAt(i) == 'U') {
    
    				if (y == 5)	continue;
    
    				sb.append(x).append(",").append(y).append("-");
    				sb.append(x).append(",").append(y+1);
    				set.add(sb.toString());
    				sb.setLength(0);
    
    				sb.append(x).append(",").append(y+1).append("-");
    				sb.append(x).append(",").append(y);
    				set.add(sb.toString());
    				sb.setLength(0);
    				
    				y++;
    			}
    			if (dirs.charAt(i) == 'D') {
    
    				if (y == -5) continue;
    			
    				sb.append(x).append(",").append(y).append("-");
    				sb.append(x).append(",").append(y-1);
    				set.add(sb.toString());
    				sb.setLength(0);
    
    				sb.append(x).append(",").append(y-1).append("-");
    				sb.append(x).append(",").append(y);
    				set.add(sb.toString());
    				sb.setLength(0);
    				
    				y--;
    			}
    			if (dirs.charAt(i) == 'R') {
    
    				if (x == 5) continue;
    				
    				sb.append(x).append(",").append(y).append("-");
    				sb.append(x+1).append(",").append(y);
    				set.add(sb.toString());
    				sb.setLength(0);
    
    				sb.append(x+1).append(",").append(y).append("-");
    				sb.append(x).append(",").append(y);
    				set.add(sb.toString());
    				sb.setLength(0);
    				
    				x++;
    			}
    			if (dirs.charAt(i) == 'L') {
    
    				if (x == -5) continue;
    				
    				sb.append(x).append(",").append(y).append("-");
    				sb.append(x-1).append(",").append(y);
    				set.add(sb.toString());
    				sb.setLength(0);
    
    				sb.append(x-1).append(",").append(y).append("-");
    				sb.append(x).append(",").append(y);
    				set.add(sb.toString());
    				sb.setLength(0);
    				
    				x--;
    			}
    		}
            
            return set.size()/2;
        }
    }
    
방문 좌표 A -> B를 String으로 만들어 해시셋에 넣어 중복처리를 해주었다.

방향이 아닌 단순히 길에 대한 중복을 묻는 문제이므로 B->A 루트도 같이 저장하고 해시셋 사이즈/2 처리를 해서 답을 구했다.

스트링빌더 때문에 로직이 많이 장황해졌는데 효율성 테스트가 없기 때문에 단순히 문자열 연산`+`으로 처리하거나 중복코드를 최대한 메서드로 빼서 코드를 더 간결하게 만들어야 했다. 

개인적으로 아쉬운 풀이

## 17. 스킬트리

    class Solution {
        public int solution(String skill, String[] skill_trees) {
           	int cnt = 0;
    
    		for (String check : skill_trees) {
    
    			int index = 0;
    			boolean isPossibleSkill = true;
    
    			for (int i = 0; i < check.length(); i++) {
    
    				if (index < skill.length() &&
    						skill.charAt(index) == check.charAt(i))
    					index++;
    
    				else if (skill.substring(index).contains(check.charAt(i) + "")) {
    					isPossibleSkill = false;
    					break;
    
    				}
    			}
    
    			if (isPossibleSkill)
    				cnt++;
    		}
    
            return cnt;
        }
    }
    

문자열 다루기 구현


## 18. 쿼드압축 후 개수 세기

    class Solution {
        public int[] solution(int[][] arr) {
            int[] answer = new int[2];
    		divide(arr, 0, 0, 0, answer);
    		return answer;
    	}
    
    	static void divide(int[][] arr, int r, int c, int d, int[] ans) {
    
    		if (isSame(arr,r,c,d)) {
    			ans[arr[r][c]] += 1;
    			return;
    		}
            
            int dis = arr.length / (int)Math.pow(2, d + 1);
            
    		divide(arr, r, c, d + 1, ans);
    		divide(arr, r + dis, c, d + 1, ans);
    		divide(arr, r, c + dis, d + 1, ans);
    		divide(arr, r + dis, c + dis, d + 1, ans);
    
    		return;
    	}
    
    	static boolean isSame(int[][] arr, int r, int c, int d) {
    
    		int first = arr[r][c];
            int dis = arr.length / (int)Math.pow(2, d);
    		
            for (int i = r; i < r + dis; i++) {
    			for (int j = c; j < c + dis; j++) {
    				if (first != arr[i][j])
    					return false;
    			}
    		}
    		return true;
    	}
    }

오랜만에  풀어본 분할정복 문제

분할정복은 문제를 재귀적으로 쪼개어 작은 부분부터 답을 구해나가는 알고리즘으로 병합정렬이나 세그먼트 트리 문제에서 메인으로 쓰이므로 꼭 알아두자

개인적으로 분할정복문제를 풀 때는 위의 코드처럼 분할하는 메서드와 분할된 부분을 계산하는(정복) 로직을 분리하여 푸는걸 선호한다.

