## 61. 124 나라
    class Solution {
        public String solution(int n) {
            int[] nums = {1, 2,4 };
    		StringBuilder sb = new StringBuilder();
    		while (n > 0) {
    			n--;
    			sb.append(nums[(n % 3)]);
    			n = n/3;
    		}
    
            return sb.reverse().toString();
        }
    }
    

주어진 수`n`을 임의의 진법으로 변환하는 문제로 진법 변환 로직을 몇번 짜봤다면 쉽게 풀이가능하다.

3진법을 1,2,4 숫자체계로 표현하여 풀이


## 62. 멀쩡한 사각형

    class Solution {
        public long solution(int w, int h) {
            int gcd =  gcd(Math.max(w,h), Math.min(w,h));
            return w * (long) h -(w+h)+gcd;
        }   
    	static int gcd(int a, int b) {
    		if(a%b == 0) return b;
    		return gcd(b, a%b);
    	}    
    }
    


프로그래머스 lv1, lv2 풀면서 처음으로 자력으로 못풀었다. 

규칙성 찾기를 최대 공약수를 활용해 푸는 유형으로 자세한 풀이는 아래 링크 참조

[https://taesan94.tistory.com/55](https://taesan94.tistory.com/55)


## 63. 단체사진 찍기

    class Solution {
        
        static StringBuilder sb = new StringBuilder();
    	static int cnt;
    	static String[] dic = { "A", "C", "F", "J", "M", "N", "R", "T" };
    	static boolean[] visited;
    
        
        public int solution(int n, String[] data) {
            
            cnt = 0;
            sb.setLength(0);
            visited = new boolean[8];
            
        	dfs(0,data);
            return cnt;
        }
        
        	static void dfs(int dept, String[] data) {
    		if (dept == 8) {
    			if (check(data)) cnt++;
    			return;
    		}
    
    		for (int i = 0; i < 8; i++) {
    			if (visited[i]) continue;
    			sb.append(dic[i]);
    			visited[i] = true;
    			
    			dfs(dept+1, data);
    			
    			sb.deleteCharAt(dept);
    			visited[i] = false;
    		}
    	}
    
    	static boolean check(String[] data) {
    		
    		for(String d : data) {
    			
    			String a = d.charAt(0) +"";
    			String b = d.charAt(2) +"";
    			String op = d.charAt(3) +"";
    			
    			int cri = Integer.parseInt(d.charAt(4)+""); 
    			int dis = Math.abs((sb.indexOf(a) - sb.indexOf(b)))-1;  
    			
    			switch (op) {
    			case "=" : if(dis != cri) return false;
    			break;
    			case ">" : if(dis <= cri) return false;
    			break;
    			case "<" : if(dis >= cri) return false;
    			break;
    			}
    		}
    		return true;
    	}
    }
    

주어진 조건에 해당하는 순열을 찾는 유형으로 2017 카카오코드 본선 기출

카카오 기출에 순열 조합 유형은 무조건 하나 들어가는듯하다. DFS로 모든 순열을 찾고 찾은 배열이 조건에 맞는지 체크해서 `cnt++` 처리를 해줬다.