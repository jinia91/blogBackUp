## 58. 타겟 넘버

    class Solution {
        public int solution(int[] numbers, int target) {
    		int answer = 0;
    		for(int i =0; i< 1<<numbers.length; i++) {
    			int sum = 0;
    			for(int j = 0;j<numbers.length; j++) {
    				if((1<<j & i) != 0) sum += numbers[j];
    				else sum -= numbers[j];
    			}
    			if(sum == target) answer++;
    		}
            return answer;
        }
    }
    
DFS/BFS 탐색유형이긴 한데 비트 연산으로 쉽게 풀수 있는 부분집합문제여서 비트연산으로 풀었다.

## 59. 더맵게
    import java.util.*;
    class Solution {
        public int solution(int[] scoville, int K) {
    		PriorityQueue<Long> pq = new PriorityQueue<>();
    		for(long x : scoville) 	pq.add(x);
    		int answer = 0;
    		long hot = pq.poll();
                    
    		while(hot < K) {			
    			long hot2 = pq.poll();
    			hot = hot + hot2*2;
    			pq.add(hot);
    			hot = pq.poll();
    			answer++;
                if(hot<K&&pq.isEmpty()) return -1;
    		}
            return answer;
        }
    }

우선순위 큐를 사용해서 풀었다.

힙 자료구조를 직접 구현해서 풀어도 되기는 하는데, 코테문제에서 힙을 구현하는 유형이 실제로 나올지 의문이다. 워낙 예전에 구현해봐서 다시 연습해야할거 같다.

[백준 no1927 힙 자료구조 구현 코드 예시](https://www.acmicpc.net/source/31512563)

## 기능 개발

    import java.util.*;
    class Solution {
        public List<Integer> solution(int[] progresses, int[] speeds) {
    		LinkedList<Integer> list = new LinkedList<>();
    
    		for (int x : progresses) list.add(x);
    
    		int cnt = 0;
    		int index = 0;
    		ArrayList<Integer> answer = new ArrayList<>();
    		
    		while (!list.isEmpty()) {
    			bulkAdd(list, speeds,index);
    			if (list.get(0) < 100) continue;
    			while (!list.isEmpty()&&list.get(0) >= 100) {
    				list.poll();
    				index++;
    				cnt++;
    			}
    			answer.add(cnt);
    			cnt = 0;
    		}
            return answer;		
    	}
    	static void bulkAdd(LinkedList<Integer> list, int[] speeds, int cnt) {
    
    		for (int i = 0; i < list.size(); i++) {
    			list.set(i, list.get(i) + speeds[i+cnt]);
    		}
    
    	}
    }
    

스택 큐 유형이긴 한데 그냥 List 컬렉션만으로 풀었다. 

문제 의도는 아마 두개다 큐로 처리해서 풀라는거 같은데 하나는 리스트로 , 다른 하나는 배열 그대로를 내부 커서를 사용해서 poll처리 시켰다.

효율적인 로직은 아닌거같아서 아쉬운 풀이 