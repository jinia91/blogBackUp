# 단속 카메라    
    import java.util.*;
    class Solution {
        public int solution(int[][] routes) {
            Arrays.sort(routes, ((o1, o2) -> o1[1] - o2[1]));
    
            int camera = Integer.MIN_VALUE;
            int cameraCnt = 0;
    
            for(var route : routes){
                if(route[0] > camera){
                    camera = route[1];
                    cameraCnt++;
                }
            }
            return cameraCnt;
        }
    }
    
그리디 문제

유사한 문제를 백준에서 푼 기억이 있어서 쉽게 풀었다.

풀이의 핵심은 루트가 겹치는 부분을 검토하여 교집합 영역에 카메라를 설치하는것인데, 만약 BF로 탐색을 하게되면 시간복잡도가 O(n^2)이고, 

N의 최대값이 10,000이므로 1억번 연산을 수행하므로 효율성 테스트를 통과하지 못할 것이다.

따라서 1회전 탐색으로 교집합 부분을 찾아야 하는데

각 루트의 끝부분(`route[1]`)으로 오름차순정렬하면 규칙을 찾기 쉬워진다.

![첨부 이미지](https://github.com/jinia91/blogBackUp/blob/main/img/9df1a62c-f379-41d6-bba1-61e125836650.png?raw=true)

[복잡하게 겹쳐있는 루트들을 bf로 다 탐색하는건 비효율적이므로]

![첨부 이미지](https://github.com/jinia91/blogBackUp/blob/main/img/be10bb44-56da-49d0-8b91-bb0e560e1421.png?raw=true)

[루트 끝을 기준으로 오름차순으로 정렬한뒤 규칙을 찾아보자!]


루트 R<sub>N</Sub>이 정렬되어 있을 때,

R<sub>2</Sub>의 end 부분이 R<sub>1</Sub> start보다 더 크다면 두 루트는 무조건 겹치게 되므로, 카메라를 R<sub>1</Sub> 의 end 부분에 설치한뒤 겹치는 루트를 검토하면 

카메라 한대로 감시 가능한 루트 최대치를 확인할 수 있다.

여기서 `if(route[0] > camera)` 다음 루트가 기존 카메라 위치보다 더 큰경우는 더이상 겹치지 않는 루트가 나오는 상황이므로

해당 루트에서 다시 카메라를 재설치한뒤 같은 검토를 반복하고, 이때 카메라 대수를 카운팅하면 우리가 원하는 최소 감시카메라 숫자를 구할 수 있다.

![첨부 이미지](https://github.com/jinia91/blogBackUp/blob/main/img/504f9bdf-414f-4a24-9579-4bf20de7ea76.png?raw=true)