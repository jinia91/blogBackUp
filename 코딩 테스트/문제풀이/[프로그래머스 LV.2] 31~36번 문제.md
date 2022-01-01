## 31.프렌즈 4블록

    class Solution {
        public int solution(int m, int n, String[] board) {
           	while(bomb(board)) while(move(board));
    		int cnt = 0;
    		for(int i =0; i<board.length; i++) {
    			for(int j =0; j<board[0].length(); j++) {
    				if(board[i].charAt(j) == '*')cnt++;
    			}
    		}
    	    return cnt;	
        }
    	
    	static boolean bomb(String[] board) {
    		
    		boolean[][] check = new boolean[board.length][board[0].length()];
    		boolean isBoomb = false;
    		
    		for(int i = 0; i<board.length; i++) {
    			for(int j = 0; j< board[0].length(); j++) {
    				
    				char target = board[i].charAt(j);
    				
    				if(target == '*')continue;
    				
    				if(j+1<board[0].length()&&
    					i+1<board.length&&
    					board[i].charAt(j+1) == target&&
    					board[i+1].charAt(j) == target &&
    					board[i+1].charAt(j+1) == target) {
    					isBoomb = true;
    					
    					check[i][j] = true;
    					check[i][j+1] = true;
    					check[i+1][j] = true;
    					check[i+1][j+1] = true;
    				}	
    			}			
    		}
    
    		if(!isBoomb) return false;
    		
    		for(int i = 0; i<board.length; i++) {
    			char[] cArr = board[i].toCharArray();
    			for(int j = 0; j< board[0].length(); j++) {
    				if(check[i][j]) {
    					cArr[j]  = '*';
    				}
    			}
    			board[i] = new String(cArr);
    		}
    		return true;
    	}
    	
    	static boolean move(String[] board) {
    
    		boolean isMoved =false;
    		
    		for(int i = 1; i<board.length; i++) {
    			char[] cArr = board[i].toCharArray();
    			for(int j = 0; j< board[0].length(); j++) {
    		
    				if(cArr[j] == '*'&&board[i-1].charAt(j)!='*') {
    					cArr[j]=board[i-1].charAt(j);
    					StringBuilder sb = new StringBuilder();
    					sb.append(board[i-1]);
    					sb.replace(j, j+1, "*");
    					board[i-1] = sb.toString();
    					isMoved = true;
    				}
    			}
    			board[i] = new String(cArr);
    		}	
    		return isMoved;
    	}
    }
    

소위 말하는 빡구현 문제

문제에서 요구하는 동작 자체는 간단해서

1. 문자가 같은 2x2 영역을 찾아 해당 문자들을 없애고
2. 없어진 문자들 윗영역에 문자가 있을경우 그 문자를 열을 고정시켜 아랫행으로 움직이기
3. 다 움직여진 board에서 1로 돌아가서 반복
4. 1이 동작 안할때까지

위의 요구사항들을 지켜 구현하면 된다.

이런 문제는 사실 디버깅이 훨씬 까다로워서 idle를 사용못하는 환경이라면 고통받았을거같다. 

해당 문제는 2018 카카오 블라인드 채용 1차이며 1차는 내가 알기론 idle 사용이 허용되는걸로 알고있어 나도 이클립스에서 디버깅을 통해 풀었다.


## 32. 피로도
    class Solution {
        
        static boolean[] checked;
    	static int max;
    
        public int solution(int k, int[][] dungeons) {
            checked = new boolean[dungeons.length];
    		dfs(dungeons, 0,k,0);
    		return max;
    	}
    	
    	static void dfs(int[][] dungeouns, int layer, int hp, int cnt) {
    		
    		max = Math.max(max, cnt);
    		if(layer == dungeouns.length) return;	
    		for(int i = 0; i< dungeouns.length; i++) {
    			if(checked[i]||
    				dungeouns[i][0]> hp) continue;
    			checked[i] = true;
    			dfs(dungeouns, layer+1, hp-dungeouns[i][1], cnt+1);
    			checked[i] = false;
    		}
    	}
    }


평범한 구현

문제의 피로도 개념이 직관적이지 않아서 hp로 바꿔서 풀었다

어째서 던전을 다녀왔는데 피로도가 감소하는거지?

## 33. 큰 수 만들기

    class Solution {
        public String solution(String number, int k) {
    		
            if(number.equals("0")) return "0";
            
            StringBuilder sb = new StringBuilder();
    		int index = 0;
    
    		for (int i = 0; i < number.length() - k; i++) {
    			int max = 0;
    			for (int j = index; j <= k + i; j++) {
    				if (number.charAt(j) > max + '0') {
    					max = number.charAt(j) - '0';
    					index = j + 1;
    				}
    			}
    			sb.append(max);
    		}
            return sb.toString();
        }
    }
    

문자열을 만들수 있는 탐색범위 안에서 최대 숫자를 찾아 높은 자릿수순으로 작성을 반복하는 그리디 유형

풀이법을 생각해내기까지 시간이 좀 걸렸는데 append 하려는 자릿수를 기준으로, 그 뒤의 남은 자릿수만큼은 탐색하지 않아야하는걸 생각해야한다.

`for (int j = index; j <= k + i; j++)`

3중 for문의 마지막부분 `j<=k+1`이 왜 성립하는지 고민해보자

## 34. 카펫
    class Solution {
        public int[] solution(int brown, int yellow) {
            int[] answer = new int[2];
    
    		for (int i = 1;i<=brown; i++) {
    			for (int j = i; j <= yellow + 2; j++) {
    				int tmp = j * 2 + (i - 2) * 2;
    				if (tmp == brown && i * j - tmp == yellow) {
    					answer[0] = j;
    					answer[1] = i;
    					break;
    				}
    			}
    		}
            return answer;
        }
    }
    


완전탐색을 사용하면 무난하게 풀 수 있다.

## 35. H-Index
    import java.util.*;
    import java.util.stream.*;
    class Solution {
        public int solution(int[] citations) {
           	List<Integer> list = Arrays.stream(citations).boxed().collect(Collectors.toList());
    		Collections.sort(list, Collections.reverseOrder());
    
    		int answer = 0;
    		for (int i = 0;i<list.size(); i++) {
    			if (list.get(i ) < i+1) break;
    			answer = i+1;
            }        
            return answer;
        }
    }


정렬 연습

역순 정렬을 쉽게하려고 스트림을 썼다.

## 36. 다리를 지나는 트럭

    import java.util.*;
    class Solution {
        public int solution(int bridge_length, int weight, int[] truck_weights) {
                LinkedList<Integer[]> pass = new LinkedList<>();
    		LinkedList<Integer[]> wait = new LinkedList<>();
    		
    		int passWeight = 0;
    		
    		for(Integer w : truck_weights) {
    			wait.add(new Integer[] {w,0});
    		}
    		
    		int answer = 0;
    		
    		for(int i = 1; ;i++) {
    			
    			for(Integer[] truck : pass) {
    				truck[1]++;
    			}
    			
    			if(!pass.isEmpty()&&pass.get(0)[1].equals(bridge_length)) {
    				
    				 Integer[] truck = pass.poll();
    				passWeight -= truck[0];				 
    			}
    			
    			if(passWeight<=weight&&!wait.isEmpty()&&wait.get(0)[0]+passWeight<=weight) {
    				Integer[] truck = wait.poll();
    				pass.offer(truck);
    				passWeight += truck[0];
    			}	
    			
    			if(pass.isEmpty()&&wait.isEmpty()) {
    				answer = i;
    				break;
    			}			
    		}
            return answer;
        }
    }
    

트럭으로 묘사했을뿐 사실 큐 두개를 사용해 간단한 스케쥴링알고리즘을 구현하는 문제였다.

대기열큐(wait)와 프로세스큐(pass) 두개를 이용해 시간이 지날때마다 처리하는 로직을 구현해주면 된다.

위의 풀이에서는 프로세싱중인 데이터의 처리시간과 현재 프로세스큐에 들어있는 데이터들의 양(weight 총량)을 구하기 위해 별도의 int값과 Integer배열을 사용했는데

이럴경우 프로세스큐 전체를 순환하는 탐색이 들어가기때문에 성능상 손실이 존재한다.

차라리 별도의 메타데이터를 저장시킬 int 배열을 만들어서 거기다가 기록하고 참조하는편이 성능상 더 나았을거같다.