## 7. 숫자의 표현

    class Solution {
        public int solution(int n) {  
           
            int sum = 0, answer = 0, e = 1;
    		
            for(int s = 1; s <= n; s++) {	
    			while(sum<n && e<=n) sum += e++;	
    			if(sum == n) answer++;	
    			sum -=s;
            }
            
            return answer;
        }
    }
    

투포인터 문제

만약 이중 for문으로 전체를 순회하려 했다면 시간복잡도 O(N<sup>2</sup>)이 나와 효율성 테스트에서 실패가 떳을 것이다.

![투포인터](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FsipoZ%2Fbtq3Iixh69G%2FxgEpPPEihbRv5rCTXjCck0%2Fimg.png)



투포인터는 for문안에 반복문이 하나 더 돈다는점은 이중 for문과 유사하지만, 

순회하는 배열을 가리킬 커서가 두개 존재하게되며(코드에서는 `s`, `e`, 그림에서는 L,R)

각 커서는 배열 첫부분에서 시작해, R커서가 배열끝까지 가고, L커서도 배열끝까지 가면 거기서 모든 순회가 종료되므로 ,각각 N +N의 탐색만 일어난다.

즉 시간복잡도 O(N)을 갖게 되어 훨씬 효율적인 탐색이 가능하다.


![image](https://imgs.developpaper.com/imgs/2019082811400913.gif)

[투포인터 탐색의 움직임 예시]

## 8. 땅따먹기

    class Solution {
        int solution(int[][] land) {
    
            for(int i =1; i<land.length; i++){            
                for(int j = 0; j< 4; j++){
                    
                    int max = 0;
                    
                    for(int l = 0; l<4; l++){
                        if(l == j) continue;
                        max = Math.max(max,land[i-1][l]);
                    }
                    
                    land[i][j] = max+land[i][j];
                }
            }
            
            int answer = 0;
            
            for(int x : land[land.length-1]){
                answer = Math.max(answer, x);
            }
    
            return answer;
        }
    }
    

유명한 DP 유형

문제에서 land 배열을 재활용할 일이 없기 때문에 메모이제이션 할 배열을 따로 선언하지 않고 그 위에 바로 값을 덮어 씌워 풀었다.

땅을 밟으며 한줄씩 내려온다고 생각할 때 맨 윗줄에서 출발하면 그 다음줄을 밟을 때 가장 큰값은

`DP[i][j] = land[i][j] + 이전 줄에서 j열을 제외한 가장 큰 값(max)`

위 식대로 구해진다.

이를 행의 길이만큼 반복하면 최종줄에 도착했을 때 최대값이 각각 기록되며 기록된값중 가장 큰 값을 구하면 된다.

## 9. 다음 큰 숫자

    class Solution {
        public int solution(int n) {
           
            String str = Integer.toBinaryString(n);
    		int oneCnt = 0;
    		for(int i =0; i<str.length(); i++) {
    			if(str.charAt(i) =='1') oneCnt++;
    		}
    
    		int answer = 0;
    		
    		for(int i = n+1; ;i++) {
    			
    			String next = Integer.toBinaryString(i);
    			
    			int cnt = 0;
    			for(int j =0; j<next.length(); j++) {
    				if(next.charAt(j) =='1') cnt++;
    			}
    			
    			if(oneCnt == cnt) {
    				answer = i;
    				break;
    			}
    		}
            
            return answer;
        }
    }
    

평범한 문자열 파싱

## 10. N진수 게임

    class Solution {
        public String solution(int n, int t, int m, int p) {
     		
    		StringBuilder sb = new StringBuilder();
    		StringBuilder tmpRs = new StringBuilder();
    		StringBuilder rs = new StringBuilder();
    		
    		for(int i = 0; ; i++) {
    			System.out.println(toNum(i,n,sb));
    			tmpRs.append(toNum(i,n,sb));
    			if(tmpRs.length()>=t*m+p) break;	
    		}
    		
    		for(int i = p-1; i<tmpRs.length();i=i+m) {	
    			rs.append(tmpRs.charAt(i));	
    		}
    		rs.setLength(t);
            return rs.toString();		
    	}
    	
    	static String toNum(int i, int n, StringBuilder sb) {
    		if(i == 0) return "0";
    		while(i!=0) {
    			int mod = i%n;
    			if(mod<10)
    			sb.append(mod);
    			else
    			sb.append((char)(65+mod-10));
    			i= i/n;
    		}
    		
    		String s = sb.reverse().toString();
    		sb.setLength(0);
    		return s;
    	}
    }
 


n진수로 변환하는걸 직접 구현했는데 n진수로 변환하는 메서드가 존재했다...

이참에 알아두자

- 10진수 -> N진수
`Integer.toString(int num, int N);`

- N진수 -> 10진수
`Integer.parseInt(String num, int N);`


## 11. 올바른 괄호

    import java.util.*;
    class Solution {
        boolean solution(String s) {
            Deque<Character> stack = new ArrayDeque<>();
            boolean answer = true;
    		
    		for(int i =0; i<s.length(); i++ ) {
    			
    			char c = s.charAt(i);
    			if(c =='(')	stack.push(c);
    			else if(c == ')') {
    				if(stack.isEmpty()) {
    					answer = false;
    					break;
    				}
    				if(stack.peek() == '(') stack.pop();
    				else {
    					answer = false;
    					break;
    				}
    			}	
    		}
            
            if(!stack.isEmpty()) return false;    
            return answer;
        }
    }
    
유명한 스택 유형

참고로 자바에서 Stack 클래스는 존재하지만 사용하지 않는걸 권장한다.

Stack은 Vector를 확장한 클래스인데 Vector는 Syncronized가 디폴트인 스레드 세이프한 클래스다. 문제는 동기화가 필요하지 않는 상황에서도 동기화가 켜져있으므로

모든 작업에 Lock 이 걸리며 단일 스레드 환경(특히 코테)에서 성능이 상대적으로 뒤떨어지게 된다.


![첨부 이미지](https://github.com/jinia91/blogBackUp/blob/main/img/19fb6651-87f8-4b45-8eba-65d6b255060e.png?raw=true)

[스택을 사용했을때 처리시간과 메모리]

![첨부 이미지](https://github.com/jinia91/blogBackUp/blob/main/img/5d74264b-a785-443c-a51a-3c28eaf136cf.png?raw=true)

[링크드 리스트를 사용했을때 처리시간과 메모리]

![첨부 이미지](https://github.com/jinia91/blogBackUp/blob/main/img/0cfd47b7-d87e-462e-bca8-9f1ffc575d27.png?raw=true)

[데큐 사용했을 때 처리시간과 메모리]

링크드 리스트는 스택처리도중 중간 요소를 끊거나 다른 동작을 할 때 보다 자유로울수 있고, 데큐는 해당 예시에서는 크게 차이나진 않았지만 메모리 효율성이 더 좋은걸로 알려져 있다.

스택을 처리할때는 데큐나 링크드 리스트를 사용하도록 하자.

## 12. 파일명 정렬

    import java.util.*;
   
    class Solution {
        public String[] solution(String[] files) {
           		Map<String, String[]> map = new HashMap<>();
    
    		for (String x : files) {
    
    			int i = 0;
    			String[] data = new String[3];
    
    			for (; i < x.length(); i++) {
    
    				if (x.charAt(i) >= '0' && x.charAt(i) <= '9') {
    					data[0] = x.substring(0, i).toLowerCase();
    					break;
    				}
    
    			}
    
    			for (int subI = i; i < x.length(); i++) {
    				if (x.charAt(i) < '0' || x.charAt(i) > '9') {
    					data[1] = Integer.parseInt(x.substring(subI, i)) + "";
    					break;
    				}
    				if (i == x.length() - 1)
    					data[1] = x.substring(subI);
    			}
    
    			if (i != x.length() - 1)
    				data[2] = x.substring(i);
    
    			map.put(x, data);
    		}
    
    		Arrays.sort(files, new Comparator<String>() {
    			
    			public int compare(String o1, String o2) {
    
    				if (map.get(o1)[0].equals(map.get(o2)[0])) {
    					if (Integer.parseInt(map.get(o1)[1]) == Integer.parseInt(map.get(o2)[1])) {
    						return 0;
    					}
    					return Integer.parseInt(map.get(o1)[1]) - Integer.parseInt(map.get(o2)[1]);
    				}
    
    				return map.get(o1)[0].compareTo(map.get(o2)[0]);
    			}
    		});
            return files;
        }
    }
    


객체지향적으로 Comparable을 구현한 클래스를 작성해 푸는것과 절차적으로 푸는것 둘 중 고민했는데, 코테에서 클래스를 정의해서 풀만큼 시간적 여유가 나지 않을거 같아서 그냥 절차적으로 풀었다.

주어진 파일을 HEAD, NUM, TAIL로 파싱하여 쪼갠뒤 해당 STRING들을 배열로 묶어 해시맵에 저장해 불필요한 객체 재생성을 막았다.

문제수준의 Comparator를 람다로 구현할 자신은 없어서 익명 클래스로 작성했다. 
