## 21. 서울에서 김서방 찾기
    import java.util.Arrays;
    class Solution {
        public String solution(String[] seoul) {
            
            int index = 0;
            
            for(int i =0; i<seoul.length; i++){
                if(seoul[i].equals("Kim")) {
                    index = i;
                    break;
                    }
            }
            
            StringBuilder sb = new StringBuilder();
            
            sb.append("김서방은 ")
              .append(index)
              .append("에 있다");
            
            
            String answer = sb.toString();
            return answer;
        }
    }
   
탐색 기본 연습

## 22. 문자열 다루기 기본
    class Solution {
        public boolean solution(String s) {
            
            if(s.length() != 4 && s.length() != 6) return false;
            
            for(int i = 0; i<s.length(); i++){
                if(s.charAt(i)<'0'||s.charAt(i)>'9')
                    return false;
            }
            
            return true;
        }
    }

기본문제

## 23. 문자열 내림차순으로 배치하기
    import java.util.Arrays;   
    class Solution {
        public String solution(String s) {
           
            char[] cArr = s.toCharArray();
            Arrays.sort(cArr);
    		
            StringBuilder sb = new StringBuilder();
            for(int i = s.length()-1; i>=0; i--){
                sb.append(cArr[i]);
            }
            
            String answer = sb.toString();
            return answer;
        }
    } 
    

자료타입 배열 정렬후 역순으로 배열 초기화

## 24. 문자열 내 p와 y의 개수
    class Solution {
        boolean solution(String s) {
        
            int p = 0;
            int y = 0;
            
            for(int i =0; i<s.length(); i++){                
                String x = s.charAt(i)+"";                
                if(x.equalsIgnoreCase("p")) p++;
                if(x.equalsIgnoreCase("y")) y++;
            }
    
            return p == y;
        }
    }

대소문자 무시 equalsIgnoreCase() 

## 25. 문자열 내마음대로 정렬하기
    import java.util.*;    
    class Solution {
        public String[] solution(String[] strings, int n) {
            Arrays.sort(strings, (s1,s2) -> 
    		    s1.charAt(n)==s2.charAt(n) ?
    			s1.compareTo(s2)
    			:
    		        s1.charAt(n)-s2.charAt(n) );
            return strings;
        }
    }
    
Comparator 구현 문제

여러가지 정렬방식이 있겠지만 자료형 정렬이 아니므로 Comparator를 사용한 sort가 제일 무난하다.

람다에서 조건절이 필요하다면 삼항연산자로 쓰면된다.

## 26. 두 정수 사이의 합
    class Solution {
        public long solution(int a, int b) {
    
            long answer = 0;
            
            for(int i = Math.min(a,b); i<=Math.max(a,b); i++){            
                answer += i;            
            }
            return answer;
        }
    }
    
기본 문제

## 27. 나누어 떨어지는 숫자 배열
    import java.util.*;    
    class Solution {
        public int[] solution(int[] arr, int divisor) {
            
            ArrayList<Integer> list = new ArrayList<>();
            
            for(int x : arr){
                if(x%divisor == 0){
                    list.add(x);
                }
            }
            
            if(list.size() == 0) return new int[] {-1};
            
            Collections.sort(list);
            
            int[] answer = new int[list.size()];
            for(int i =0; i<list.size(); i++){
                answer[i] = list.get(i);
            }
            
            return answer;
        }
    }
    
숫자 배열 정렬 구현


## 28. 같은 숫자는 싫어

    import java.util.*;
    public class Solution {
        public int[] solution(int []arr) {
            
            int[] tmp = new int[arr.length];
            
            tmp[0] = arr[0];
            int index =1;
            
            for(int i=1; i<arr.length; i++){
                if(tmp[index-1] != arr[i]){
                    tmp[index++] = arr[i];
                }    
            }        
            
            int[] answer = Arrays.copyOfRange(tmp,0,index); 
            
            return answer;
        }
    }
    
반복문 내에서 인덱스 두개 다루기 연습문제이고 copyOfRange같은 메서드를 모를경우 for문을 돌아 직접 작성해도 무방

## 29. 다트게임

    import java.util.*;
    class Solution {
    public int solution(String dartResult) {

       	int[] scores = new int[3];

	int subStringIndex = 0;

	for (int i = 0; i < 3; i++) {

	    for (int j = subStringIndex; j < dartResult.length(); j++) {

		if (dartResult.charAt(j) < '0' || dartResult.charAt(j) > '9') {

			scores[i] = Integer.parseInt(dartResult.substring(subStringIndex, j));

			char area = dartResult.charAt(j);
			if (area == 'D')
				scores[i] = scores[i] * scores[i];
			if (area == 'T')
				scores[i] = scores[i] * scores[i] * scores[i];

			subStringIndex = j+1;

			if (dartResult.length() - 1 > j 
                        	&& dartResult.charAt(j + 1) == '*') {
                        
				scores[i] = scores[i] * 2;
				if (i != 0) scores[i - 1] = scores[i - 1] * 2;
				subStringIndex++;
                        
			} else if (dartResult.length() - 1 > j 
                              		&& dartResult.charAt(j + 1) == '#') {
                        
				scores[i] = -scores[i];
				subStringIndex++;
                        
			}
			break;
		}
	    }
	}

	int answer = 0;
	for (int x : scores) {
		answer += x;
	}
	return answer;
       }
    }
    
2018년도 카카오 블라인드 채용 기출문제 

여러 코너케이스를 포함하는 구현문제로 문제를 읽고 차근차근 구현해나가면 되므로 크게 어렵지는 않았다. 

다만 이런 구현문제 특성상 시간이 많이 잡아먹히는건 어쩔수가 없는듯.. 좀더 짧고 빠르게 코딩할수 있도록 연습할 필요를 느꼈다.


## 30. 가운데 글자 가져오기
    class Solution {
        public String solution(String s) {
            String answer = "";
            
            if(s.length()%2==0){
                answer = s.substring(s.length()/2-1,s.length()/2+1);
            }
            else{
                answer = s.charAt(s.length()/2)+"";
            }
            
            return answer;
        }
    }
    

문자열 다루기 기본연습