# 거스름돈
    class Solution {
    
    static int[] dp;
    
    public int solution(int n, int[] money) {
        
        dp = new int[n + 1];
        dp[0] = 1;

        for (int i = 0; i< money.length; i++) {
            for (int j = money[i]; j < dp.length; j++) {
                dp[j] += (dp[j - money[i]])%1000000007;
            }
        }        
        
        int answer = dp[n];        
        return answer;
      }
    }
    
주어진 돈의 가짓수`m`이 100종류이므로, 만약 모든 가짓수를 dfs로 구하려면 스택이 100개 쌓여 오버플로우가 날 것이다. 

보다 빠르게 풀기위해서는 [피보나치문제처럼](https://www.jiniaslog.co.kr/article/view?articleId=561) 메모이제이션을 사용하여 반복연산을 줄이는 DP를 사용해야 했다.


`money = {1,2,5}`, `n =5`로 주어졌을때 나오는 모든 가짓수 표를 통해 간략히 풀이를 살펴보면,


|  | 0 |1  | 2 |3  |4  | 5 |
| --- | --- | --- | --- | --- | --- | --- |
| 1 |  1| 1 |1  | 1 |  1|  1|
| 1,2 | 1 | 1 | 2 | 2 | 3 | 3 |
|  1,2,5|1  | 1 | 2 |2  |3  |4  |

위는 1만 사용했을경우, 1과 2를 사용했을 경우, 1과 2와 5를 모두 사용했을 경우 숫자를 표현할 수 있는 가짓수를 구한 테이블이다.

테이블의 값을 자세히 보면 점화식을 도출할 규칙성을 찾아볼 수 있는데

![첨부 이미지](https://github.com/jinia91/blogBackUp/blob/main/img/058264c0-0414-4662-983b-f5cd475c63b2.png?raw=true)

테이블의 특정 위치 값은(합이 N, M개의 화폐사용) M-1개의 화폐를 사용한 합의 N인 값 + M개의 화폐를 사용한 합이 N-M<SUB>K</SUB>의 값 이 더해져서 이루어짐을 확인할 수 있다.

이를 점화식으로 표현하면

A<SUB>N,M</SUB> = A<SUB>N,M-1</SUB> + A<SUB>N-K,M</SUB> 이며 해당 점화식을 화폐 K부터 순차적으로 작성해나가면 문제에서 요구한 테이블의 마지막 값에 도달할 수 있다.


여기서 DP테이블을 위의 표처럼 2차원 배열로 작성해도 되지만, 

![첨부 이미지](https://github.com/jinia91/blogBackUp/blob/main/img/1db73488-9a21-41ce-a46a-1a41a00a1da7.png?raw=true)

네모친 부분을 구한 순간 위에 빗금친 테이블은 앞으로 사용할일이 없는 쓰레기값이 되기때문에 1차원 메모이제이션 배열에 덧씌우는 구현을 하면 공간복잡도를 훨씬 절약할 수 있다.


        // 1차원 배열에 덧씌우기로 메모리 절약
          for (int i = 0; i< money.length; i++) {
                for (int j = money[i]; j < dp.length; j++) {
                    dp[j] += (dp[j - money[i]])%1000000007;
                }
            } 
            
