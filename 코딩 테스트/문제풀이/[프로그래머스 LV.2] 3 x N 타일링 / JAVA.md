# 3 X N 타일링

    class Solution {  
        static long[] dpTable;
        
        public int solution(int n) {
            dpTable = new long[n+1];
            dpTable[0] = 1; 
            dpTable[2] = 3;
            dpTable[4] = 11;
            
            for(int i = 6;i <= n;i+=2){
                long temp = dpTable[i-2]*3;
    
                
                for(int j = 2; j*2 <= i; j++){
                    temp += dpTable[i - j*2]*2;
                }
                
            dpTable[i] = temp % 1000000007;            
            }
        
            return (int) dpTable[n];
        }
    }
    


규칙을 찾고 그 규칙에 맞게 메모이제이션을 하여 시간복잡도를 줄이는 문제였다.

규칙찾기가 굉장히 까다로웠고 처음에는 스택으로 풀었는데 효율성체크가 일부 터져서 반복문으로 변경했더니 통과하였다.

개인적으로 재귀표현이 간단하고 가독성이 좋다보니 dp문제들을 탑다운으로 접근하여 재귀로 풀어내는경우가 많은데, 이런식으로 효율성체크에서 터지고 나니 바텀업으로 반복문으로 작성하는데 좀더 친숙해져야겠다고 느꼈다.

정말 오랜만에 알고리즘 문제를 풀었는데 뇌가 많이 굳은게 느껴진다.. 오늘부터 다시 1일 1PS 재활해보자!





