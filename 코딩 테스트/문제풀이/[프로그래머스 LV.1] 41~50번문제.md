## 41. 폰켓몬
    import java.util.*;
    class Solution {
        public int solution(int[] nums) {
            
            Set<Integer> set = new HashSet<>();
            
            for(int x : nums){
                set.add(x);
            }
            
            int n = nums.length / 2;
            
            if(n>set.size()) return set.size();
            return n;
        }
    }
    
문제가 길어서 헤깔릴 수 있는데 결국 구하고자 하는 건 한번에 가져갈 수 있는 폰켓몬의 가짓수이다.

따라서 폰켓몬의 가짓수가 총 몇개인지 해시셋으로 중복제거하고 그걸 한번에 가져갈 수있는 수(nums/2)와 비교하여 분기처리해주면 된다.

## 42. 체육복
    import java.util.*;
    import java.util.stream.*;
    class Solution {
        public int solution(int n, int[] lost, int[] reserve) {
           
    		Arrays.sort(lost);
    		Arrays.sort(reserve);
    
    		List<Integer> lostList = Arrays.stream(lost).boxed().collect(Collectors.toList());
    		List<Integer> reserveList = Arrays.stream(reserve).boxed().collect(Collectors.toList());
    		
    		for (int i = 0; i < lostList.size(); i++) {
    			for (int j = 0; j < reserveList.size(); j++) {
    				if (lostList.get(i).equals(reserveList.get(j))) {
    					lostList.remove(i--);
    					reserveList.remove(j);
    					break;
    				}
    			}
    		}
    
    		for (int i = 0; i < lostList.size(); i++) {
    			for (int j = 0; j < reserveList.size(); j++) {
    				if (lostList.get(i) - 1 == reserveList.get(j) ||
                       lostList.get(i) + 1 == reserveList.get(j)) {
    					lostList.remove(i--);
    					reserveList.remove(j);
    					break;
    				}
    			}
    		}
            
            return n-lostList.size();
        }
    }
    

그리디 문제.

그리디 문제는 매 선택에서 가장 최적의 답을 택하길 반복하는 알고리즘으로 원칙적으로는 그리디접근이 최적해임을 보장하는 수학적 증명을 해야하지만, 증명자체가 워낙 복잡해서 보통 테스트케이스만 통과하는식으로 문제를 푸는 편이다.

해당 문제는 
1) 체육복을 도둑맞은 학생리스트와 여벌체육복이 있는 학생리스트를 각각 정렬시킨후

2) 순차적으로 각 리스트에 중복되는 요소들은 제거하고 이후 도둑맞은 학생기준 +-1을 통해 여벌 체육복이 있는 학생을 찾아나가는식으로 풀었다.

해당 풀이에서는 탐색을 위해 두개의 리스트를 O(N<sup>2</sup>) 시간 복잡도로 완전탐색했는데, 사실 해쉬셋을 사용하여 해시 기반으로 찾으면 O(N) 의 시간복잡도로 풀 수 있다. 해당 풀이로는 시간효율성 테케가 있었다면 틀렸을 것이다.


## 43. 모의 고사

    import java.util.*;
    class Solution {
        public List<Integer> solution(int[] answers) {
        	
    		int[] a = {1,2,3,4,5};
    		int[] b = {2,1,2,3,2,4,2,5};
    		int[] c = { 3, 3, 1, 1, 2, 2, 4, 4, 5, 5};
    		
    		List<Integer> answer = new ArrayList<>();
    		
    		int aAnswer = checkAnswer(answers, a);
    		int bAnswer = checkAnswer(answers, b);
    		int cAnswer = checkAnswer(answers, c);
    
            int max = Math.max(aAnswer, Math.max(cAnswer, bAnswer));
            
    		if(aAnswer == max) answer.add(1);
    		if(bAnswer == max) answer.add(2);
    		if(cAnswer == max) answer.add(3);
    		
            return answer;
            
    	}
    
    	private static int checkAnswer(int[] answers, int[] a) {
    		int cnt = 0;
    		for(int i =0; i<answers.length; i++) {		
    			if(a[i%a.length] == answers[i]) cnt++;		
    		}
    		return cnt;
    	}
    }


 완전탐색 기본 문제
주어진 학생들의 패턴을 적용해서 모든 답을 체크하고 비교대조해주면 된다.

출력에서 조금 고민했는데 프로그래머스는 정해진 리턴값을 변경해도 된다고 하길래 조작이 쉬운 List로 변경했다.

최대값이 동일한 학생들을 add()처리만 해주면 되므로 배열 길이를 신경쓰지 않아도 되서 훨씬 편했다.


## 44. K번째 수
    import java.util.*;
    class Solution {
        public int[] solution(int[] array, int[][] commands) {
            int[] answer = new int[commands.length];
    		
    		for(int i = 0; i< commands.length; i++) {
    			
    			int a = commands[i][0];
    			int b = commands[i][1];
    			int c = commands[i][2];
    			
    			List<Integer> tmp = new ArrayList<>();
    
    			for(int j = a-1; j<b; j++) {
    				tmp.add(array[j]);	
    			}
    			
    			Collections.sort(tmp);
    			
    			answer[i] = tmp.get(c-1);
    		}
            return answer;
        }
    }
    
배열 잘라내기를 할 때 copyOfRange()를 생각 못해서 그냥 반복문으로 잘라냈다. 

난이도 자체는 평범한 lv1 정렬 문제

## 45. 완주하지 못한 선수
    import java.util.*;
    class Solution {
        public String solution(String[] participant, String[] completion) {
            
    		Map<String, Integer> map = new HashMap<>();
    
    		for (String str : participant) {
    
    			if (map.containsKey(str)) {
    				map.put(str, map.get(str) + 1);
    			} else {
    				map.put(str, 1);
    			}
    		}
    
    		for (String str : completion) {
    
    			if (map.get(str)>1) {
    				map.put(str, map.get(str) - 1);
    			} else {
    				map.remove(str);
    			}
    		}
    		
    		String answer = "";
    		Set<String> keySet = map.keySet();
    		for(String str : keySet) {
    			answer = str;
    		}
            
            return answer;
        }
    }
    

처음에 문제 유형을 안보고 이중 for문으로 풀었다가 시간초과가 나왔다.

문제를 다시 확인후 해시맵을 사용한 카운터를 만들어서 풀이

해당 문제의 카운터 구현방식은 다른 알고리즘 문제를 풀때도 꽤 자주 쓰이므로 알아두자

## 46. 소수 만들기
    import java.util.*;
    class Solution {
        public int solution(int[] nums) {
          	
            boolean[] prime = new boolean[3001];
    		prime[0] = true;
    		prime[1] = true;
    		for(int i = 2; i<=Math.sqrt(prime.length);i++) {
    			if(prime[i]) continue;
    			for(int j = 2; j*i<=prime.length; j++) {
    				prime[i*j] = true;
    			}
    		}
    
            int cnt = 0;
    		
    		for(int i = 0; i< nums.length-2; i++) {
    			for(int j = i+1; j<nums.length-1; j++) {	
    				for(int k = j+1; k<nums.length; k++) {		
    					int num = nums[i]+nums[j]+nums[k];		
    					if(!prime[num]) cnt++;		
    				}
    			}
    		}
    		
            return cnt;
        }
    }
    
 에라토테네스의 체를 만들고 완전탐색으로 3개값들을 모두 구해 처리하면 된다.

[20번 문제 에라토테네스의 체 풀이 참조](https://www.jiniaslog.co.kr/article/view?articleId=205)

## 47. 내적
    class Solution {
        public int solution(int[] a, int[] b) {
           	int sum = 0;
    		
    		for(int i = 0; i<a.length; i++) {
    			sum += a[i]*b[i];
    		}
    	
            return sum;
        }
    }
    

산수

## 48. 음양 더하기
    class Solution {
        public int solution(int[] absolutes, boolean[] signs) {
            
            int sum = 0;
            
            for(int i =0; i< absolutes.length; i++){
            
                if(signs[i])
                    sum += absolutes[i];
                else
                    sum -= absolutes[i];
            }
            return sum;
            
        }
    }
    
기본 구현

## 49. 없는숫자 더하기
        class Solution {
            public int solution(int[] numbers) {
                int answer = 45;
                
                for(int x : numbers){
                    answer -= x;
                }
                
                return answer;
            }
        }
        
산수

## 50. 크레인 인형뽑기
    import java.util.*;
    class Solution {
        public int solution(int[][] board, int[] moves) {
        	Stack<Integer> bucket = new Stack<>();
    		
    		int cnt = 0;
    		
    		for(int command : moves) {
    			int doll = pickUp(board, command-1);
    		
                if(doll == 0) continue;
                
    			if(bucket.size() == 0) bucket.add(doll);
    			else {
    				if(bucket.peek().equals(doll)) {
    					bucket.pop();
    					cnt = cnt+2;
    				}
    				else bucket.add(doll);
    			}
    		}
    		return cnt;
    	}
    	
    	static int pickUp(int[][] board, int command) {
    		
    		int doll = 0;
    		
    		for(int i =0; i<board.length; i++) {
    			if(board[i][command] !=0) {
    				doll = board[i][command];
    				board[i][command] = 0;
    				return doll;
    			}
    		}
    		return doll;
    	}
    }
    

2019 카카오 인턴쉽 문제

1. 인형을 뽑는 동작
2. 바구니에서 중복인형 제거 동작

두가지를 구현하는게 핵심이며 바구니는 스택을 사용했다.

평범한 구현 문제였음

