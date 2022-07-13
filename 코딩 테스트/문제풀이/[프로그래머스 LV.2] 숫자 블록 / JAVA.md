# 숫자 블록

    class Solution {
        public int[] solution(long begin, long end) {
            int beginI = (int) begin;
            int endI = (int) end;
            int[] answer = new int[endI - beginI +1];
            int index = 0;
            
            for (int i = beginI; i<=endI; i++){
                boolean flag = false;
                if(i == 1) {
                    answer[index++] = 0;
                    continue;
                }
            
                for (int j = 2; j*j <= i; j++) {
                    if (i % j == 0 && i / j <= 10000000) {
                        answer[index++] = i/j;
                        flag = true;
                         break;
                    }
                }
                if(!flag) answer[index++] = 1;
            }
            return answer;
        }
    }
    

소수 찾기 문제의 응용으로, 요구사항이 
- 소수인 블록은 1
- 소수가 아닌 블록은 가장 큰 약수

를 범위로 구하는 문제였다.

따라서 에라토테네스의 체를 구할때와 유사한 방법으로 구해야하는 블록마다 `2<= x <= 제곱근` 만큼 나누어 떨어지는지를 체크하여 
- 나누어 떨어질경우 가장 작은수로 나누어 떨어지는 값이 가장 큰약수
- 나누어 떨어지지 않을경우 flag를 통해 소수임을 확인하여 블록에 1을 기입

하는식으로 문제를 풀었다.

또한 조건에서 블록은 천만번까지 존재하기 때문에, 약수가 천만 이하인 조건식으로 필터링하는 조건을 추가했다.

조건 자체는 파악하기 쉬웠는데 생각한거처럼 동작을 잘 안해서 디버깅하는데 좀 애먹었다.