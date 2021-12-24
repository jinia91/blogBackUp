## 1. N개의 최소 공배수
    class Solution {
        public int solution(int[] arr) {
         	int gcd = arr[0];
    		int lcm = arr[0];
    		for(int i=1;i<arr.length; i++ ) {    			
    			gcd = gcd(lcm,arr[i]);
    			lcm = lcm*arr[i]/gcd;
    	}
    		return lcm;
    	}
    	
    	static int gcd(int a, int b) {
    		if(a%b == 0) return b;
    		return gcd(b,a%b);
    	}
    }
    

최소 공배수 문제는 최대 공약수문제이므로 유클리드 호제법만 알면 쉽게 풀 수 있는 문제였다.

유클리드 호제법을 모른다면 아래 링크를 참조하자

[8번 문제 참조](https://www.jiniaslog.co.kr/article/view?articleId=203)


## 2. JadenCase 문자열 만들기
    class Solution {
        public String solution(String s) {
            boolean isFirst = true;
            s=s.toLowerCase();
            StringBuilder sb = new StringBuilder();
            
            for(int i = 0; i<s.length(); i++){
            
                if(s.charAt(i) != ' '){
                    if(isFirst) {
                    	sb.append((s.charAt(i)+"").toUpperCase());
                    	isFirst = false;
                    }else sb.append(s.charAt(i));
                }else {
                	sb.append(s.charAt(i));
                	isFirst = true;
                }
            }
        return sb.toString();
        }
    }
    

문자열 파싱, 구현 문제

불린값하나로 분기만 잘 나누면 어렵지 않게 풀수 있다.

## 3. 행렬의 곱셈
    class Solution {
        public int[][] solution(int[][] arr1, int[][] arr2) {
            int[][] answer = new int[arr1.length][arr2[0].length];
    		
    		for(int i = 0 ; i< arr1.length; i++) {
    			for(int j =0; j< arr2[0].length; j++) {
    				for(int k = 0; k< arr2.length;k++) {
    					answer[i][j] += arr1[i][k]*arr2[k][j]; 
    				}
    			}
    		}
            return answer;
        }
    }

평범한 구현

## 4. 피보나치 수열
    class Solution {
        public int solution(int n) {
           
            int[] dp = new int[n+1];
            
            dp[0] = 0;
            dp[1] = 1;
            
            for(int i = 2; i<=n; i++){
                if(dp[i] == 0){
                    dp[i] = (dp[i-1] + dp[i-2])%1234567;
                }
            }
              
            int answer = dp[n];
            return answer;
        }
    }
    

내가 아는 피보나치 수열 구하는 방법은 크게 네가지가 존재한다.

### 1. 재귀로 풀이

재귀함수로 풀이할경우 피보나치를 거듭해 나갈때마다 연산이 중복되므로 매우 비효율적이다. 시간복잡도 O(N<SUP>2</SUP>)이고 더 적절한 알고리즘이 많으므로  피보나치 문제를 단순재귀로 풀생각은 하지 말자. 
 
### 2. 반복문사용

길이 3짜리 배열 `new int[] {0,1,1}` 을 선언하고 반복문을 돌면서 피보나치 수를 구해 배열에 덧씌우는 방식으로 시간복잡도 O(N)으로 풀 수 있다.

본 문제는 사실 최종 값인 N만 구하면 되므로 반복문을 사용하는게 가장 적절했을 것이다.


### 3. DP
내가 푼 방식이며 메모이제이션을 사용해 중복되는 연산값들을 dp[]배열에 저장하고 연산시 이를 사용하는 동적 계획법을 사용했다.

dp를 사용할경우 O(N) 의 시간복잡도를 갖지만 int[N] 배열을 초기화해야하므로 메모리를 많이 사용하게 되는 단점이 존재한다. 만약 문제에 공간복잡도의 제약이 있었다면 해당 풀이로 풀어선 안된다.

### 4. 행렬제곱을 사용한 분할 정복

![image](https://t1.daumcdn.net/cfile/tistory/991C634C5BFAB36335)

피보나치 수열을 점화식으로 나타내면 다음과 같은데 이 점화식을 행렬로도 표현해 볼 수 있다. 

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F9938434C5BFAB36307)
위 식을 전개해서 풀어보면 최종적으로
![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F99D2664E5BFAB4A708)

다음과같은 행렬의 제곱식으로 표현가능해진다.

자세한 증명은 아래 사이트에서 잘 설명되있으므로 궁금하면 참조해보도록 하자.

[https://st-lab.tistory.com/252](https://st-lab.tistory.com/252)

관련문제 : 
- [백준 2749번 문제 피보나치 수 3](https://www.acmicpc.net/problem/11444)
    - [해당문제 풀이](https://github.com/jinia91/CodingPractice/blob/main/JustPractice/no2749.java)
- [백준 11444번 문제 피보나치 수 6](https://www.acmicpc.net/problem/2749)
  - [해당문제 풀이](https://github.com/jinia91/CodingPractice/blob/main/JustPractice/no11444.java)

행렬 제곱을 사용해 분할정복으로 풀게되면 N개의 연산을 트리구조로 쌓아올려 O(logN)의 시간복잡도로 피보나치 수열을 풀 수 있게된다. 

내기억으론 조단위 N번째 피보나치 수열도 구할 수 있을정도였으니 알고리즘의 성능 차이를 체감할 수 있는 부분이다. 

### 5. 최솟값 만들기
    import java.util.*;
    
    class Solution{
        public int solution(int []A, int []B){
            
            Arrays.sort(A);
            Arrays.sort(B);
            
            int sum = 0;
            for(int i = A.length-1, j= 0; i>=0; i--){
            sum += A[i]*B[j++]; 
            }
            
            return sum;
        }
    }
  
작은수와 큰수끼리 짝짓는 아이디어를 떠올렸다면 쉽게 풀수 있는 문제

### 6. 최댓값 최솟값
    import java.util.*;
    class Solution {
        public String solution(String s) {
            
            StringTokenizer st = new StringTokenizer(s, " ");
            
            int max = -Integer.MAX_VALUE;
            
            int min = Integer.MAX_VALUE;
            
            while(st.hasMoreTokens()){
                
                int x = Integer.parseInt(st.nextToken());
                
                max=Math.max(x, max);
                min=Math.min(x, min);
                
            }
            
            return min+" "+max;
        }
    }
    

간단한 파싱과 Math.max() Math.min() 연습문제
