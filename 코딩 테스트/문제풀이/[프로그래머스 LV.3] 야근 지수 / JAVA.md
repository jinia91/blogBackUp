# 야근 지수

    class Solution {
        public long solution(int n, int[] works) {
    
            long sumWorkTime = 0L;
            for(var w : works){
                sumWorkTime += w;
            }
            
            sumWorkTime = sumWorkTime - n;
            if(sumWorkTime <= 0) return 0;
    
            int[] dividedWorks = new int[works.length];
            int targetWorkTime = (int) (sumWorkTime / works.length);
    
            for(int i = 0; i< works.length; i++){
                if(works[i] < targetWorkTime) {
                    dividedWorks[i] = works[i];
                }
                else{
                    dividedWorks[i] = targetWorkTime;
                }
                sumWorkTime -= dividedWorks[i];
            }
            
            int index = 0;
            
            while(sumWorkTime>0){
                if(works[index] - dividedWorks[index] >0) {
                dividedWorks[index++]++;                
                sumWorkTime--;
                }
                else{
                    index++;
                }
                if(index == works.length) index = 0;
            }
    
            long answer = 0L;
    
            for(var x : dividedWorks){
                answer += (long) x * x;
            }
            return answer;
        }
    }
    
산수와 구현으로 풀었다.

풀면서도 너무 복잡하게 푸는거 같아서 효율성 테스트를 통과 못할 줄 알았는데, 생각보단 엄격하지 않았던거 같다.

좋은 풀이라고는 생각되지 않는데 다른 방법이 생각나지 않았다..


풀이 방법은 

1. `주어진 작업량의 총합 W` 에서 일하는 시간 `n`을 뺀 작업량 `W'`를 구하고
2. `W'` 를 남은 일 `N`으로 나누면 평균적인 남은 일 작업량 근사치 `targetWorkTime`을 구할 수 있다.
3. 이후 실제 작업량을 순회하면서 근사치 미달인경우는 그대로, 근사치 이상인경우는 근사치로 작업량을 할당하고
4. 할당하고 남은 작업량 `W'`를 마저 분할해주었다.

빅오 시간복잡도 자체는 O(N)인데 위의 풀이는 반복되는 순회를 너무 많이 해서 아마 더 효율적인 풀이가 있지 않을까 싶다.

