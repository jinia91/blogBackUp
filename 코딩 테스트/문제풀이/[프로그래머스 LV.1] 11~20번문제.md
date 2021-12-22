
## 11. 정수 제곱근 판별
    class Solution {
        public long solution(long n) {
            
            double x = Math.sqrt(n);
            int y = (int) x;
            
            if(x != y) return -1;
            
            long answer = (long)((x+1)*(x+1));
            return answer;
        }
    }
    

형변환으로 제곱근 정수 판별 아이디어

## 12. 정수 내림차순으로 배치하기

    import java.util.Arrays;
    class Solution {
        public long solution(long n) {
            
            String str = String.valueOf(n);
            char[] cArr = str.toCharArray();
            
            Arrays.sort(cArr);
            
            char[] cArrR = new char[cArr.length];
            for(int i = cArr.length-1, j=0; i>=0; i--) {
            	cArrR[j++] = cArr[i];
            }
            
            long answer = Long.valueOf(new String(cArrR));
            return answer;
        }
    }
    
처음에는 Comparator로 역순 sort 처리하려했는데 원시타입 배열은 Comparator가 적용되지 않았다.  

박싱 배열을 만들어서 스트림으로 매핑시키고 역순처리후 다시 원시타입으로 바꾸는것도 생각해봤는데 너무 번거롭고 박싱/언박싱 비용도 많이 드는거 같아서

>long -> String -> char[] ->정렬 -> 역순으로 new char[] -> String - > long

로 처리했다. 더 간단한 방법이 있을까?

## 13. 자연수 뒤집어 배열로 만들기
    class Solution {
        public int[] solution(long n) {
            
            String str = String.valueOf(n);
    
            int[] answer = new int[str.length()];
    
            for(int i = str.length()-1, j=0; i>=0; i--){
                answer[j++] = str.charAt(i)-'0';
            }
            
            return answer;
        }
    }

문자열<->숫자 처리 연습

## 14. 자릿수 더하기
    import java.util.*;  
    public class Solution {
        public int solution(int n) {
            int answer = 0;
    
            String str = String.valueOf(n);
            
            for(int i =0; i<str.length(); i++){
                answer += str.charAt(i)-'0';
            }
            return answer;
        }
    }
    
문자열<->숫자 처리 연습

## 15. 이상한 문자 만들기
    class Solution {
        public String solution(String s) {
        
            StringBuilder sb = new StringBuilder();
    		
            for(int i =0, j=0; i<s.length(); i++){    
                if(s.charAt(i) == ' ') {
                    sb.append(" ");
                    j = 0;
                    continue;
                } 
                
            	sb = j++%2 ==0 ?
            	  sb.append((s.charAt(i)+"").toUpperCase())
            	  :
            	  sb.append((s.charAt(i)+"").toLowerCase());	
            }
            
            String answer = sb.toString();
            return answer;
        }    
    }
    
구현문제

띄어쓰기를 기준으로 각 단어마다 짝홀을 다시 판단하는 로직이 핵심이다.

## 16. 약수의 합
    class Solution {
        public int solution(int n) {
         
            int answer = 0;
         
            for(int i =1; i<=n; i++){
                if(n%i == 0) answer += i;    
            }
            return answer;
        }
    }
    
산수

## 17. 시저 암호
    class Solution {
        public String solution(String s, int n) {
            
            StringBuilder sb = new StringBuilder();
            
            for(int i =0; i<s.length(); i++){                
                char x = s.charAt(i);                
                if(x == ' '){
                    sb.append(" ");
                }               
                if(65<=x&&x<=90){
                    if(x+n >90){
                        sb.append((char)(64 + (x+n-90)));
                    }
                    else
                    sb.append((char)(x+n));
                }                
                else if(97<=x&&x<=122){
                    if(x+n >122){
                        sb.append((char)(96 + (x+n-122)));
                    }
                    else
                    sb.append((char)(x+n));
                }               
            }         
            String answer = sb.toString();
            return answer;
        }
    }
    
구현 문제

아스키 코드를 외우지 않았다면 (int) 'A', (int)'Z' 로 표현해도 상관없다.

최초에 대문자 소문자 조건절을 사용하지 않으면 대문자 +7이상 수가 밀릴경우 소문자와 겹쳐서 에러가 날수 있음을 주의

## 18. 문자열 정수로 바꾸기

    class Solution {
        public int solution(String s) {
            
            int answer = Integer.parseInt(s);
            return answer;
        }
    }
    
charAt으로 가져와 `-'0'`으로 모든 자릿수를 파싱 -> int로 변환하고 자릿수별로 *10<sup>n</sup>을 하여 구현해볼수도 있다.

## 19. 수박수박수박수박수?
    class Solution {
        public String solution(int n) {
           
            StringBuilder sb = new StringBuilder();
    
            for(int i =1; i<=n; i++){
                sb = i%2 != 0 ?
                    sb.append("수")
                    :
                    sb.append("박");
            }
            
            String answer = sb.toString();
            return answer;
        }
    }
    
구현 기본

## 20. 소수 찾기
    class Solution {
        
        public int solution(int n) {
            
            boolean[] prime = new boolean[n+1];
            makePrime(prime);
            
            int answer = 0;
            
            for(boolean x : prime){
                if(!x) answer++;
            }
            
            return answer;
        }
        
        static void makePrime(boolean[] prime){
            
            prime[0] = true;
            prime[1] = true;
            
            for(int i = 2; i<=Math.sqrt(prime.length); i++){
                if(prime[i] == true) continue;
                
                for(int j = 2; i*j<prime.length;j++){
                    prime[i*j] =true;
                }
            }
        }
    }
    

소수는 1과 자기 자신 이외에는 더 이상 나눠지지 않는 수 이므로 수가 입력될 때마다 2부터 해당 수 n까지 나누어 떨어지는 수가 있는지를 세는 방법도 가능할 것이다.
    
         // 모든 수 탐색
	static boolean isSosu(int number) {
		for(int i = 1; i <= number ; i++) {
			if(number == 1) return false;
			if(number%i == 0 && i != number && i != 1) {
				return false; 
			}
		}
	return true;
	}

하지만 위와같은 로직은 시간복잡도 O(N<sup>2</sup>)을 갖기 때문에 효율적이지 못하다.

### 에라토테네스의 체
에라토테네스의 체는 **1과 자신 이외에 소수는 나눠지지 않는다**는 명제의 역을 생각하여 **나눠지면 소수가 아니다**를 **배수는 소수가 아니다**로 바꿔 생각한 방법이다.

1부터 100까지의 숫자중 소수 세기를 간단히 생각해보자

우선 1부터 100까지 보드가 있고 거기서 소수가 아닌 숫자만 색칠을 할 것이다.

1. 1은 소수가 아니다. (색칠)
2. 2는 소수다.
3. 2를 제외한 2의 배수는 소수가 아니다. (배수들 전부 색칠)
4. 3은 소수다.
5. 3을 제외한 3의 배수는 소수가 아니다. (배수들 전부 색칠)
6. **4는 2의 배수이므로 소수가 아니다.**(이미 색칠되어있음)
7. ....

x. **10은 2와 5의 배수이므로 소수가 아니다.** (이미 색칠되어있음)
x. 10의 배수는 소수가 아니다. (이미 색칠되어있음)

여기까지 배수들을 전부 색칠했을 경우 우리는 10 이하 수들의 곱으로 된 모든 배수를 전부 색칠한 셈이다.

> 2의 배수들, 3의 배수들, 4의 배수들... 10의 배수들을 모두 체크했다. <br>
> 파악하지 않은 영역은 11*11 부터인데 우리는 100까지의 숫자만 체크하면 되므로 무시해도 된다.

![에라토테네스의 체](https://upload.wikimedia.org/wikipedia/commons/b/b9/Sieve_of_Eratosthenes_animation.gif)

이제 보드판에서 체크하지 않은 영역이 바로 소수가 된다.

100까지의 숫자들 중 그 제곱근인 10까지만 해당수(a)가 배수(b)를 했을때(a*b) 100이하만 탐색(a*b<100)하면 되므로

시간 복잡도는 O(n loglogn)의 시간복잡도를 갖게 되며 O(N<sup>2</sup>)보다 훨씬 빠르게 수행된다.

### 코드

    static void makePrime(boolean[] prime){
                
                prime[0] = true;
                prime[1] = true;
                
                for(int i = 2; i<=Math.sqrt(prime.length); i++){
                    if(prime[i] == true) continue;
                    
                    for(int j = 2; i*j<prime.length;j++){
                        prime[i*j] =true;
                    }
                }
            }
            
배열 특성상 편의를 위해 index 0값을 포함한 N+1 배열길이로 초기화하고 소수가 아닌 숫자를 true로 초기화시켜주면 된다.

2부터 N의 제곱근까지 소수가 아니면 continue, 소수면 그 배수들을 전부 체크해주기만 하면 끝.

이후 체를 활용하여 소수문제를 풀면 된다. 이번 문제에서는 소수의 갯수를 구하는 문제이므로 prime이 false인 숫자를 셌다.