## 31. 비밀지도
    class Solution {
        public String[] solution(int n, int[] arr1, int[] arr2) {
            
    		String[] answer= new String[n];
    		StringBuilder sb = new StringBuilder();
    		StringBuilder sb2 = new StringBuilder();
    		
    		for(int i = 0 ; i< n; i ++) {
    			
    			buildBinary(n, arr1, sb, i);
    			buildBinary(n, arr2, sb2, i);
    		
    			for(int j = 0 ; j< n; j++) {
    				
    				if(sb2.charAt(j) == '1') {
    					sb.replace(j, j+1, "1");
    				}
    			}
    			
    			answer[i] = sb.toString()
    					.replaceAll("1", "#")
    					.replaceAll("0", " ");
    			sb.setLength(0);
    			sb2.setLength(0);
    		}
            return answer;    
    	}
    
    	private static void buildBinary(int n, int[] arr1, StringBuilder sb, int i) {
    		String tmp = Integer.toBinaryString(arr1[i]);
    		int need0 = n-tmp.length();
    		
    		while(need0-->0) {
    			sb.append("0");
    		}
    		sb.append(tmp);
    	}
    }
    
2018 카카오 블라인드 채용 기출문제

기본적인 문자열 다루기 구현문제이므로 어려울것 없다. 10진수를 2진수로 바꾸는건 반복문을 써도 무방

## 32. 부족한 금액 계산하기
    class Solution {
        public long solution(int price, int money, int count) {
            long answer = 0L;
            long sum = 0L;   
            
            for(long i =1l; i<=count; i++){
                sum += price*i;
            }
    
            answer = sum - money;
            if(answer<0) answer = 0;
            return answer;
        }
    }
    
산수

## 33. 나머지가 1이 되는 수 찾기
    class Solution {
        public int solution(int n) {
            int answer = 0;
            
            for(int i = 1; i<n; i++){
                
                if(n%i==1){
                    answer = i; 
                    break;
                }     
            }
            
            return answer;
        }
    }
    
산수

## 34. 최소 직사각형
    class Solution {
        public int solution(int[][] sizes) {
            
            int maxl = 0;
    	    int maxr = 0;
            
    		for(int i = 0 ; i< sizes.length; i++) {
    			
    			maxl = Math.max(Math.max(sizes[i][0], sizes[i][1]), maxl);
    			maxr = Math.max(Math.min(sizes[i][0], sizes[i][1]), maxr);
    			
    		}
            return maxl*maxr;
        }
    }
    
Math.max(), Math.min() 활용

## 35. 2016년
    import java.time.LocalDate;
    
    class Solution {
        public String solution(int a, int b) {
            
            LocalDate date = LocalDate.of(2016, a, b);
            
            String answer = date.getDayOfWeek().toString().substring(0, 3);
            return answer;
        }
    }
    
요일을 String 배열로 만들고, 각 월별로 최종날짜(30, 31,윤년이므로 2월은 29)를 가진 배열을 만들고 인자로 받은 a,,b를 사용해 날짜 총합을 계산한 뒤 7로 나누어 요일을 구하는 것이 원래 문제 의도일 것이다.

귀찮아서 localDate로 풀었지만 만약 위의 로직을 떠올리지 못한다면 연습해보자

## 36. 두 개 뽑아서  더하기
    import java.util.*;
    class Solution {
        public int[] solution(int[] numbers) {
            
            Set<Integer> set = new HashSet<>();
            
            for(int i = 0 ; i<numbers.length; i++){
                for(int j =i+1; j< numbers.length; j++){
                    
                    set.add(numbers[i]+numbers[j]);
                }
            }
            
            int[] answer = new int[set.size()];
            
            int i = 0;
            
            for(Integer x : set){
                answer[i++] = x;
            }
            
            Arrays.sort(answer);
            
            return answer;
        }
    }
    
중복값 처리를 위해 해시셋 사용

## 37. 예산

    import java.util.*;
    
    class Solution {
        public int solution(int[] d, int budget) {
            
            Arrays.sort(d);
            int answer = Integer.MAX_VALUE;
            
            for(int i = 0; i< d.length; i++){
                
                if(budget<d[i]) {
                    answer = i; 
                    break;
                }
                
                budget -= d[i];
                
            }
          
            if(answer == Integer.MAX_VALUE) answer = d.length;
             
            return answer;
        }
    }
    
예산안들을 오름차순 정렬 후 예산이 부족한 시점까지 순차적으로 빼주면 된다.

예산안이 모두 통과되는 코너케이스를 고려해 answer의 최초값을 d의 제한사항 초과값을 넣어(MAX_VALUE) 처리해줬다.

## 38. 3진법 뒤집기
    class Solution {
        public int solution(int n) {
            
            StringBuilder sb = new StringBuilder();
    		
    		while(true) {					
    			sb.append(n%3);
    			n = n/3;
    			if(n==0) break;
    		}
    
    		int answer = 0;
            
    		for(int i =sb.length()-1, j=0; i>=0; i--) {		
    			answer += (sb.charAt(i)-'0')*Math.pow(3,j++);
    		}
            
            return answer;
        }
    }
    
진법 변환 연습문제
풀이에서는 3진법을 10진법으로 바꿀때 반복문으로 파싱하여 자릿수별로 일일이 계산했지만 `Integer.parseInt(sb,3);` 만 해도 변환이 가능하다. 

## 39. 약수의 개수와 덧셈
    class Solution {
        public int solution(int left, int right) {
            
            int answer = 0;
            for(int i = left; i<=right; i++){
                
                int cnt =0;
                
                for(int j = 1; j<=i; j++){
                    if(i%j == 0) cnt++;
                }
                
                if(cnt%2==0) answer +=i;
                else answer -=i;
            }
            
            return answer;
        }
    }
    
나는 약수의 개수를 모두 세기 위해 O(N<sup>2</sup>)의 복잡도로 풀었는데, 제곱수는 약수의 개수가 무조건 홀수라는 성질을 이용하면 O(N)으로도 풀 수 있다. 



    ## 40. 실패율
    import java.util.*;
       
    class Solution {
        public int[] solution(int N, int[] stages) {
    
            Double[][] failure = new Double[N][2];
    
    		for (int i = 0; i < failure.length; i++) {
    
    			int arrive = 0;
    			int pass = 0;
    
    			for (int j = 0; j < stages.length; j++) {
    				if (stages[j] >= i+1) {
    					pass++;
    				if(stages[j] == i+1) arrive++;
    				}
    			}
    
    			failure[i][0] = i+1d;
                	if(pass == 0) {
    				failure[i][1] = 0d;
    			}
    			else
    			failure[i][1] = (double)arrive / pass;
    		}
    		
    		Arrays.sort(failure, (a,b) ->
    		b[1].compareTo(a[1])==0?
    				a[0].compareTo(b[0])
    				:
    		b[1].compareTo(a[1]));
    
    		int[] answer = new int[N];
    		for(int i = 0; i< N; i++) {
    			answer[i] = (int)(double)failure[i][0];
    		}
            return answer;
        }
    }


2019 카카오 블라인드 채용 문제

카카오 기출답게 문제가 장황하게 긴데 크게 1) 실패율을 구하는 로직과 2) 정렬 을 나누어 생각하면 편하다.

각 스테이지별 실패율을 저장하기 위해 맵과 이중 배열, 배열 세개를 고민했는데 정렬처리하기 가장 편한 방법은 이중배열을 쓰는거라 생각해서 이중배열[N][2]로 데이터를 저장했다.

처음에 모든 참여자가 스테이지를 아예 도착하지 못하는 코너케이스를 생각 못해서 꽤 헤맸다. 

위의 경우 실패율이 0/0이므로 duoble 자료타입의 경우 ArithmeticException을 터뜨리지만, 나같은 경우 Double 박싱타입을 사용했기 때문에, NaN으로 반환되어 런타임 에러가 터지지 않았다. 에러가 터지질 않으니 디버깅이 안되서 더 헤맸던거 같다.

`pass == 0`일 때 실패율을 0으로 처리해 주면 해결


