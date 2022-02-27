# 입국 심사

    import java.util.*;
 
     class Solution {
        public long solution(int n, int[] times) {
            Arrays.sort(times);
            long end = (long)times[times.length-1] * n;
            long answer = binarySearch(times, n, 0, end);
            return answer;
        }
        
        static long binarySearch(int[] times, int n, long start, long end){
            while(start <= end){
                long mid = (start + end) / 2;
                long passedPersonNum = Arrays.stream(times)
                        .mapToLong(time -> (mid / time)).sum();
                if(passedPersonNum >= n) end = mid-1;
                else start = mid +1;
            }
            return end+1;
        }
    }
    
이분 탐색 - 매개변수 탐색 - LowerBound에 해당 하는 문제였다.

이분탐색은 정렬되어있는 데이터 집합에서 데이터 범위를 이분할하여 좁혀가며 탐색하는 알고리즘으로 O(logN)의 시간복잡도를 갖는 탐색기법이다.


![image](https://blog.kakaocdn.net/dn/bCRI11/btqDEFKLWFh/Fh5mKeR8p6YxJ1lhKAiJYk/img.gif)


해당 문제는 이를 응용한 `매개변수 탐색` 유형으로 매개변수탐색(패러메트릭 서치)란 최적화 문제를 결정문제로 바꾸어 해결하는 기법을 말한다.

즉 `원하는 조건을 만족하는 가장 알맞은 값을 찾는 문제`를 의미한다.

## 문제 접근법

나는 이분탐색 - 매개변수탐색문제를 접근할때는 순차적인 템플릿을 따르는 편인데

>1. 이분탐색으로 물어볼것 정하기
> 2. 물어봤을때, 문제에서 원하는 최적해가 최소값인지(Lower Bound), 최대값인지(Upper Bound) 판단
> 3. 최적해 구하는 로직 구현
> 4. 디버깅

위의 순서를 거치는 편이다.

해당 문제 역시 같은 방식으로 접근했으며

>1. 이분탐색으로 총 걸리는 시간을 쿼리로 삼아 질의
> 2. 해당 질의에 `N += 총 걸리는 시간 / 개별 입국심사`가 주어진 n이면서, 최적해로 최소값(Lower Bound)이 나와야 됨을 확인
> 3. 매개변수탐색 - LowerBound 코드 작성
> 4. 테케를 통해 디버깅

으로 풀이했다.

이분탐색의 경우 왼쪽 오른쪽 커서지정에 따라 미묘하게 작동실패가 많이 일어나므로 여러 테케를 통해 디버깅을 할수밖에 없다. 

한방에 잘 구현하려면 많은 연습이 필요한듯...

