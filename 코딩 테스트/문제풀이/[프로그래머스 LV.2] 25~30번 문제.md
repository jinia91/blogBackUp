## 25.  교점에 별 만들기
    import java.util.*;
    import java.awt.*;
    
    class Solution {
        public String[] solution(int[][] line) {
    		Set<Point> set = new HashSet<>();
    		
    		int maxX = -Integer.MAX_VALUE, minX = Integer.MAX_VALUE, 
    			maxY = -Integer.MAX_VALUE, minY = Integer.MAX_VALUE;
    		
    		for(int i = 0; i< line.length; i++) {
    			
    			for(int j = i+1; j<line.length; j++) {
    				
    				long AD_BC = (long)line[i][0]*line[j][1] - (long)line[i][1]*line[j][0];
    				long BF_ED = (long)line[i][1]*line[j][2] - (long)line[i][2]*line[j][1];
    				long EC_AF = (long)line[i][2]*line[j][0] - (long)line[i][0]*line[j][2];
    				
    				if(AD_BC == 0 ||
    				   BF_ED%AD_BC !=0 ||
    				   EC_AF%AD_BC !=0 ) continue;
    			
    				int x = (int)(BF_ED/AD_BC);
    				int y = (int)(EC_AF/AD_BC);
    				 set.add(new Point(x,y));
    				 
    				 maxX = Math.max(maxX, x);
    				 maxY = Math.max(maxY, y);
    				 minX = Math.min(minX, x);
    				 minY = Math.min(minY, y);
    				
    			}
    			
    		}
    		
    		int h= maxY-minY +1, w= maxX-minX +1;
   		
    		String[] answer = new String[h];
    		
    		char[] dummy = new char[w];
    		Arrays.fill(dummy, '.');
    		Arrays.fill(answer, new String(dummy));
    		
    		for(Point p : set) {
    			char[] cArr = answer[maxY-p.y].toCharArray() ;
    			cArr[p.x-minX] = '*';
    			answer[maxY-p.y] = new String(cArr);
    		}
    
            return answer;
        }
    }
    
기하 구현 유형

수학 공식을 문제에서 주어지긴 하는데 구현 자체가 매우 귀찮았다. 패러미터로 넘어오는값이 최대 100만이 넘기때문에 곱셈을 할경우를 생각해 long으로 처리해주었고

`.` 으로 채운 `String` 배열에 y(행) 좌표를 찾아 `char`배열로 바꾼뒤 x(열) 좌표값을 수정하여 구현하였다.  

좌표를 다루는 클래스를 만들어도 되지만 개인적으로 시간절약을 위해 좌표를 다루는 문제의 경우 awt 패키지의 Point 좌표를 자주쓰는편

거리구하기나 좌표 이동등 간단한 메서드가 구현되있어서 편하다.


## 26. 구명보트
    class Solution {
        public int solution(int[] people, int limit) {
            int answer = 1;
            Arrays.sort(people);   
            for(int s = 0, e = people.length-1; s< people.length; s++){   
                int tmp = limit;
                tmp = tmp - people[s];
                while(s<e){
                    answer++;
                    e--;                
                    if(tmp<people[e]) continue;
                    break;
                }
            }
            return answer;
        }
    }
    

분류는 탐욕법이긴 한데, 풀이자체는 투포인터에 해당한다.

투포인터를 구현할 수 있다면 쉽게 풀수 있음

## 27. 주식 가격
    import java.util.*;
    class Solution {
        public int[] solution(int[] prices) {
    		int[] answer = new int[prices.length];		
    		for(int i = 0; i<prices.length; i++) {
    			for(int j = i+1; j<prices.length; j++) {
    				if(prices[i]<=prices[j]) answer[i]++;
                    else {
                        answer[i]++;
                        break;
                    }
    			}	
    		}        
            return answer;
        }
    }
    
문제가 이해가 안가서 한참 헤멨다.

문제를 이해하고나서도 왜 스택 / 큐로 분류되어있는지 이해가 안가는 문제

그냥 완탐으로 풀었다.
 
## 28. 영어 끝말잇기
    import java.util.*;
    class Solution {
        public int[] solution(int n, String[] words) {
    		int[] answer = new int[2];
    		
    		Set<String> set = new HashSet<>();
    	
    		set.add(words[0]);
    		
    		for(int i = 1; i< words.length; i++) {
    			set.add(words[i]);
    			if(set.size()!=i+1||
    			   words[i].length()<=1||
    			   words[i].charAt(0) != words[i-1].charAt(words[i-1].length()-1)) {
    			answer[0] = i%n+1;
    			answer[1] = i/n+1;
    			break;
    			}
            } 
            return answer;
        }
    }
    
해시를 사용한 평범한 문자열 다루기

## 29. 삼각 달팽이
    import java.util.*;
    class Solution {
        public List<Integer> solution(int n) {
            int[][] arr = new int[n][n];
    		
    		for(int i = 0, r=-1,c=0, num=1; i<(n+2)/3; i++) {
    			
    			for(int j = 0; j<n-3*i; j++) {
    				arr[++r][c] = num++;
    			}		
    			for(int j =0; j<n-3*i-1; j++) {
    				arr[r][++c] = num++;
    			}			
    			for(int j = 0; j<n-3*i-2; j++) {
    				arr[--r][--c] = num++;
    			}	
    		}
    		
    		List<Integer> answer = new ArrayList<>();
    		
    		for(int[] i : arr) {
    			for(int j : i) {
    				if(j == 0) continue;
    				answer.add(j);
    			}
    		}
            return answer;       
        }
    }
    
구현

별찍기 응용이라 보면된다. 구현유형을 연습하려면 삼성 sw 역량 기출을 풀어보는걸 추천한다.

## 30. 2개 이하로 다른 비트

    class Solution {
        public long[] solution(long[] numbers) {
    		long[] answer = new long[numbers.length];
    
    		for (int i = 0; i < numbers.length; i++) {
    
    			long num = numbers[i];
    
    			String n = toBinary(num);
    
    			if (n.charAt(n.length() - 1) == '0') {
    				answer[i] = num+1;
    				continue;
    			}
    
    			int cnt = -1;
    			for (int j = n.length() - 1; j >= 0; j--) {
    				if (n.charAt(j) == '0')break;
    				cnt++;
    			}
    			
    			answer[i] = num+(long)Math.pow(2, cnt);
    		}
    		return answer;
    	}
    
    	static String toBinary(long n) {
    
    		StringBuilder sb = new StringBuilder();
    
    		while (n > 0) {
    			if (n % 2 == 1)
    				sb.append(1);
    			else
    				sb.append(0);
    			n = n / 2;
    		}
    
    		sb.append(0);
    
    		return sb.reverse().toString();
    
    	}
    }
    

처음에는 구현문제로 생각하여 비교하려는 수를 2진법으로 바꾸어 모든 자릿수를 탐색하고 거기서 틀린 갯수를 세는식으로 구현했는데

시간 효율성 테스트에서 통과하지 못했다.

그렇다면 규칙성 문제라고 판단하여 다시 살펴보니 

맨 뒷자리부터 
...0 -> +1
...01 -> + 1 (2^0)
...011 -> +2(2^1)
...0111 -> +4(2^2)
...
의 규칙성을 찾아 해당 방법으로 풀이하였다.

