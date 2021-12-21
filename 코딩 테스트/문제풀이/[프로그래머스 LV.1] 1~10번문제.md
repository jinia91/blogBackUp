## 1. 직사각형 별찍기

    import java.util.Scanner;    
    class Solution {
        public static void main(String[] args) {
            Scanner sc = new Scanner(System.in);
            int n = sc.nextInt();
            int m = sc.nextInt();
            
        StringBuilder sb = new StringBuilder();
        
        for(int row = 0; row<m; row++){
            for(int  col= 0; col<n; col++){
                sb.append("*");
            }
            sb.append("\n");
        }
        System.out.println(sb);
      }
    }
    
이중 포문과 StringBuilder를 사용하면 간단히 풀 수 있었다.

## 2. x만큼 간격이 있는 n개의 숫자
    class Solution {
        public long[] solution(int x, int n) {
            
            long[] answer = new long[n];
            
            for(int i = 0; i< n; i++){
                
                answer[i] = x+x*(long)i;
                
            } 
            return answer;
        }
    }
    
단순 반복문과 형변환으로 풀이

## 3. 행렬의 덧셈
    class Solution {
        public int[][] solution(int[][] arr1, int[][] arr2) {
            int[][] answer = new int[arr1.length][arr1[0].length];
            
            for(int r =0; r<arr1.length; r++){
                for(int c = 0; c<arr1[0].length; c++){
                    answer[r][c] = arr1[r][c] + arr2[r][c];
                }
            }
            
            return answer;
        }
    }
    

다중배열에 .length  찍는것만 알면 간단함

## 4. 핸드폰 번호 가리기
    class Solution {
        public String solution(String phone_number) {
            
            StringBuilder sb = new StringBuilder();
            
            for(int i =0; i<phone_number.length()-4; i++){
                sb.append("*");
            }
            sb.append(phone_number.substring(phone_number.length()-4,phone_number.length()));
            
            String answer = sb.toString();
            return answer;
        }
    }
  
문자열 다루기 기본

## 5. 하샤드 수
    class Solution {
        public boolean solution(int x) {
            
            String str = String.valueOf(x);
            int sum =0;
            
            for(int i =0; i<str.length();i++){
                sum += str.charAt(i)-'0';
            }
            
            boolean answer = x%sum==0 ?
                true : false;
            
            return answer;
        }
    }
    
char -> int로 형변환할때 아스키코드 처리하는것 조심하기

## 6. 평균 구하기
    class Solution {
        public double solution(int[] arr) {
            
            int sum =0;
            
            for(int x : arr){
                sum += x;
            }
            
            double answer = sum/(double)arr.length;
            return answer;
        }
    }
    
산수

## 7. 콜라츠 추측
    class Solution {
        public int solution(int num) {
            if(numb == 1L) return 0;  

            int answer = 0;
            long numb = num;            
                        
            for(int i =1; i<=500; i++){
                         
              numb = numb%2==0 ?
                  numb/2 : numb*3+1;
                if(numb == 1) return i;
            }
            
            return -1;
        }
    }
    
코너 케이스 연습문제

## 8. 최대 공약수와 최소 공배수
    class Solution {
        
        public long[] solution(int n, int m) {
            
            int a = sol(Math.max(n,m),Math.min(n,m));
            long[] answer = {(long)a, (long)n/a * m/a *a};
            return answer;
        }
        
        static int sol(int bigNum,int mod){   
            if(bigNum%mod == 0) return mod;
            return sol(mod, bigNum%mod);
        }
    }
    

유클리드 호제법만 안다면 쉽게 풀수 있는 문제.
### 유클리드 호제법
#### 정의
>a, b 두 정수가 있을 때, r = a%b라면 a와 b의 최대 공약수는 b와 r의 최대 공약수와 같다.
> GCD(a,b) = GCD(b,r)

#### 증명
A = ad
B = bd
-> A와 B의 최소공약수는 d 이며, a,b는 서로소
A = Bq + r
위와 같은 식이 주어질때, r을 정리해보면
 r = A-Bq
r = ad - bdq = (a-bq)d
-> B와 r은 d의 공약수를 가짐을 확인할수 있다. 여기서 만약 (a-bq)와 b가 서로소라면 d는 최대공약수가 된다. 이를 증명하면
> **명제A** : (a-bq)와 b가 서로소가 아니다. 
> 라는 명제가 주어졌을때  두 수는 공약수를 가지고 있을 것이며
> a-bq = Xz, b=Yz 로 표현가능하다. 이때 두식을 대입하여 a로 정리해보면 다음과 같다.
> a-Yzq = Xz
> a= Xz+Yzq
> a=(X+Yq)z
> a가 다음과 같이 정의될 경우 a와 b는 z라는 공약수를 가짐을 확인할 수 있다. 그런데 a와 b가 서로소임을 증명 최상단에 전제로 두고 시작했으므로 모순이 생기므로 **명제 A**는 틀렸다는 사실을 확인할 수 있다.
> 따라서 귀류법으로 판단해보면 
> **(a-bq)와 b는 서로소 임을 확인할 수 있다.**

다시 원 증명으로 돌아와서 B와 r은 d 라는 최대공약수를 가지므로  GCD(a,b) = GCD(b,r) 임도 증명할 수 있다.

#### 코드
     static int sol(int bigNum,int mod){   
            if(bigNum%mod == 0) return mod;
            return sol(mod, bigNum%mod);
        }


유클리드 호제법에 의해 A와 B의 최대 공약수는 B와 A%B의 최대공약수와 같게된다.
여기서 반복하여 생각해보면, B와 A%B의 최대공약수는 A%B와 b%(A%B)의 최대공약수이며 이는 b%(A%B)와 (A%B)%(b%(A%B))의 최대공약수와 같을 것이다.

이처럼 재귀적으로 연결하면 언젠가 bigNum과 mod의 나눗셈이 나머지 없이 떨어지는 순간이 오게되고, GCD(a.b) = GCD(gcd,0) 가 나오게 된다.
약수의 정의에 의하면 0의 약수는 모든 정수이며(0 = 0*p), GCD(gcd,0) = gcd가 된다.

따라서 `if(bigNum%mod == 0)` 이 순간의 `mod`가 바로 최대 공약수이다.

최소공배수는 최대공약수만 알면 손쉽게 풀 수 있다.


## 9. 짝수와 홀수
    class Solution {
        public String solution(int num) {
            String answer = num%2 == 0?
                "Even" : "Odd";
            return answer;
        }
    }
    
홀짝 판별 기본

## 10. 제일 작은 수 제거하기
    class Solution {
        public int[] solution(int[] arr) {
            
            if(arr.length == 0) return new int[] {-1};
            
            int removeI = 0;
            
            for(int i = 0, min = Integer.MAX_VALUE; i< arr.length; i++){
                if(arr[i]<min){
                    min = arr[i];
                    removeI = i;
                } 
            }
            
            int[] answer = new int[arr.length-1];
            
            for(int i = 0, j=0; i<arr.length; i++){
                if(removeI == i) continue;
                answer[j++] = arr[i]; 
            }
            
            return answer;
        }
    }
    

-자바는 배열의 길이가 불변이므로 배열의 특정 요소를 제거하더라도 요구조건을 맞추기위해서는 배열을 새로 정의하는수밖에 없었다.
 배열을 리스트로 바꾸고 remove()함수를 호출한 뒤 다시 배열로 바꾸는방법도 생각해봤는데 리스트에는 원시타입을 집어넣지못하므로 Integer로 박싱하고 다시 배열로 만들때 int로 언박싱을 해야하니 더 번거로운거같다.
조금 더 좋은 로직이 없을까
