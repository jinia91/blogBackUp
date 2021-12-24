## 51. 키패드 누르기

    class Solution {
        public String solution(int[] numbers, String hand) {
        	
    		int[] l = new int[] {3,0};
    		int[] r = new int[] {3,2};
    
    		StringBuilder sb = new StringBuilder();
    		
    		for(int n : numbers) {
    			
    			switch(n) {
    			case 1: 
    			case 4: 
    			case 7: 
    				l = mapNum(n);
    				sb.append("L");
    				break;
    			case 3 :
    			case 6 :
    			case 9 :
    				r = mapNum(n);
    				sb.append("R");
    				break;
    			case 2 :
    			case 5 :
    			case 8 :
                case 0 :        
    				if(dis(l,mapNum(n))>dis(r,mapNum(n))) {
    					r= mapNum(n);
    					sb.append("R");
    				}
    				else if (dis(l,mapNum(n))==dis(r,mapNum(n))) {
    					if(hand.equals("right")) {
    						r= mapNum(n);
    						sb.append("R");	
    					}
    					else {
    						l = mapNum(n);
    						sb.append("L");	
    					}
    				}
    				else {
    					l = mapNum(n);
    					sb.append("L");
    				}
    			}
    		}
            
            return sb.toString();
    		
    	}
    	
    	static int[] mapNum(int n) {
    		switch (n) {
    		case 0 : return new int[] {3,1};
    		case 1 : return new int[] {0,0};
    		case 2 : return new int[] {0,1};
    		case 3 : return new int[] {0,2};
    		case 4 : return new int[] {1,0};
    		case 5 : return new int[] {1,1};
    		case 6 : return new int[] {1,2};
    		case 7 : return new int[] {2,0};
    		case 8 : return new int[] {2,1};
    		case 9 : return new int[] {2,2};
    		}
    		return new int[] {0,0};
    	}
    	
    	static int dis(int[] a, int[] b) {
    		return Math.abs(a[0]-b[0])+Math.abs(a[1]-b[1]);
    	}
    }
    

2020 카카오 인턴쉽 문제

입력받은 번호에 맞게끔 왼손 오른손의 좌표값이 변하도록 구현하는게 핵심이며

왼손과 오른손 좌표를 배열형태로 저장하고 입력받은 번호를 순차적으로 읽어서 분기에 맞게 왼손 오른손 좌표가 바뀌도록 했다.

해당 풀이는 모든 분기마다 새로운 배열을 생성하여 반환하게되서 GC가 비효율적으로 많이 돌아간다. 

메서드에 손 좌표 배열도 파라미터로 받아 배열값을 바꾸는 방식으로 코드를 짜는게 훨씬 나을거 같다.


## 52. 숫자 문자열과 영단어
    import java.util.*;
    class Solution {
        public int solution(String s) {
    
            Map<String, String> map = new HashMap<>();
    		map.put("zero", "0");
    		map.put("one", "1");
    		map.put("two", "2");
    		map.put("three", "3");
    		map.put("four", "4");
    		map.put("five", "5");
    		map.put("six", "6");
    		map.put("seven", "7");
    		map.put("eight", "8");
    		map.put("nine", "9");
    
    		for(String x : map.keySet()) {
    			s = s.replaceAll(x, map.get(x));
    		}
            
            return Integer.parseInt(s);
            
        }
    }
    
51번과 비슷하게 데이터를 매핑시켜 활용하는 구현 문제

이번에는 불필요한 객체생성을 하지 않기 위해 맵을 사용하고 해시로 조회하여 시간복잡도를 낮췄다.


## 53. 신규 아이디 추천

    class Solution {
        public String solution(String new_id) {
            
            String s = new_id;
            
            StringBuilder sb = new StringBuilder();
    		
    		for(int i =0; i< s.length(); i++) {
    			
    			char c = s.charAt(i);
    			
    			//1
    			c= Character.toLowerCase(c);
    			
    			//2
    			if(!('a'<=c&&c<='z')&&
    			   !('0'<=c&&c<='9')&&
    			   c != '-'&&
    			   c != '_'&&
    			   c != '.') {
    				continue;
    			}
    			//3
    			if(i>0&&sb.length()>0&&
    					sb.charAt(sb.length()-1) == '.'&&c == '.') {
    				continue;
    			}
    			//4
    			if(c=='.'&&
    					sb.length() == 0) continue;
    			
    			sb.append(c+"");
    			
    		}
    		
    		//4
    		if(sb.length() != 0 &&sb.charAt(sb.length()-1) == '.') sb.setLength(sb.length()-1);
    		
    		//5
    		if(sb.length() == 0) sb.append("a");
    		
    		// 6
    		if(sb.length() >= 16) {
    			sb.setLength(15);
    			if(sb.charAt(14) == '.') sb.setLength(14);
    		}
    		
    		//7
    		if(sb.length()<=2) {
    			for(int i = 3-sb.length(); i>0; i--)
    			sb.append(sb.charAt(sb.length()-1));
    		}
    
            return sb.toString();
        }
    }
    

입력값을 조건에 맞게 검증하고 고치는 문제로 요구사항을 차근차근 읽어가며 그대로 구현하기만 하면 된다. 

특별히 어려운 코너케이스도 없었다.

## 54. 로또의 최고순위와 최저순위
    class Solution {
        public int[] solution(int[] lottos, int[] win_nums) {
            
            int matches = 0;
            int unknown = 0;
            for(int lotto : lottos){
                if(lotto == 0){
                    unknown++;
                    continue;
                } 
                
                for(int win_num : win_nums){
                    if(win_num == lotto){
                        matches++;
                    }
                }
            }
            
            int a = 7-matches;
            if(a >= 7) a = 6;
            int b = 7-(unknown+matches);
            if(b >= 7) b = 6;
            
            int[] answer = {b, a};
            return answer;
        }
    }
    
평범한 구현문제였음

