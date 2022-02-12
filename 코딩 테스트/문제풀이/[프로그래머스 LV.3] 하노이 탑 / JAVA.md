# 하노이의 탑

    import java.util.*;
    class Solution {
        
        static List<List<Integer>> answer = new ArrayList<>();
    
        public List<List<Integer>> solution(int n) {
    		hanoiAction(n, new Tower(1), new Tower(2), new Tower(3));
            return answer;
        }
        
        static void hanoiAction(int n, Tower origin, Tower other, Tower goal) {
    		
    		if(n ==1) {
    			movePlateAtoB(origin, goal);
    			return;
    		}
    		
    		hanoiAction(n-1, origin, goal, other);	
    		movePlateAtoB(origin, goal);	
    		hanoiAction(n-1, other, origin, goal);				
    	}
    
    	static void movePlateAtoB(Tower A, Tower B) {
    		ArrayList<Integer> list = new ArrayList<Integer>();
    		list.add(A.index);
    		list.add(B.index);
    		answer.add(list);
    	}
    	
    	static class Tower{
    
            Integer index;
    
    		public Tower(Integer index) {
    			super();
    			this.index = index;
    		}	
    	}
    }
    

재귀 유형으로 유명한 하노이 탑 문제.

원판을 이동하는 메커니즘을 점화식으로 표현할 수 있으면 재귀로 코드를 작성하는데 큰 어려움은 없다.

## 하노이의 탑 점화식 구하기

점화식은 다음 과정을 이해하면 작성하기 쉬운데

![첨부 이미지](https://github.com/jinia91/blogBackUp/blob/main/img/3b00ea04-f7ce-45e2-ac4f-e5e91a578eda.png?raw=true)


1. 타워는 3개 존재하며 N개의 원판이 A타워에 전부 있는 상황
2. 여기서 우리는 모든 A타워의 원판을 C타워로 이동시켜야한다.
3. 타워A의 원판들을 크게 N번째 원판 하나와 N-1개의 원판으로 구분해서 인식해보자

![첨부 이미지](https://github.com/jinia91/blogBackUp/blob/main/img/b25eb041-f0f8-4364-9c68-6bc396926934.png?raw=true)

4\. N-1개의 원판을 B타워로 이동시키기 위해서는 x<sub>n-1</sub> 만큼의 이동횟수 필요할 것이고 그것을 행해 이동시켰다 가정해보자

![첨부 이미지](https://github.com/jinia91/blogBackUp/blob/main/img/0122c533-7884-448a-973b-103984c1bbe2.png?raw=true) 

5\. 이제 마지막 남은 n번 원판을 목적지인 C타워로 이동시킨다. 이때 드는 경우의수는 1.

![첨부 이미지](https://github.com/jinia91/blogBackUp/blob/main/img/d76889ed-f3c7-44a2-bc13-4ab85d1ee03a.png?raw=true)

6\. 그리고 아까 B타워로 이동시켰던 N-1개의 원판을 같은 방식으로 C타워로 이동시키면 총 x<sub>n-1</sub> 만큼의 이동횟수 필요할 것이다.


위의 과정들을 종합해보면 N개의 원판들을 A타워에서 C타워로 이동시키기 위해 드는 총 이동횟수 X<sub>n</sub>은

x<sub>n</sub> = x<sub>n-1</sub> + 1 + x<sub>n-1</sub>
= 2x<sub>n-1</sub> +1

이며 이를 정리해보면

x<sub>n</sub> +1 = X<sub>n</sub> 이라 할때

X<sub>n</sub> = 2X<sub>n-1</sub> 이며


x<sub>1</sub>은 원판 하나를 A타워에서 C타워로 이동시키는 이동횟수를 의미하고, 1이므로

X<sub>1</sub> = x<sub>1</sub>+1 = 2가 된다.

따라서 
X<sub>n</sub> = 2X<sub>n-1</sub> = 2*2<sup>n-1</sup> = 2<sup>n</sup> 이고

x<sub>n</sub> +1 = X<sub>n</sub> 이므로

x<sub>n</sub> = 2<sup>n</sup> -1이 된다.

따라서 하노이 탑을 이동시키는 이동횟수는 위의 점화식으로 표현할 수 있으며, 점화식은 재귀 코드로 구현가능하기때문에

재귀코드로 점화식을 표현하면서 실제 이동 행동을 추적하면 문제에서 요구하는 답을 구할 수 있다.




## 실제 코드로 구현해보기

위의 코드를 보면 

     static void hanoiAction(int n, Tower origin, Tower other, Tower goal) {
        		
        		if(n ==1) {
        			movePlateAtoB(origin, goal);
        			return;
        		}
        		
        		hanoiAction(n-1, origin, goal, other);	// 점화식에서 Xn-1에 해당
        		movePlateAtoB(origin, goal);	//점화식에서 1에 해당
        		hanoiAction(n-1, other, origin, goal);	// 점화식에서 Xn-1에 해당 2번째			
        }
        
하노이 액션이 재귀를 하는 메인 로직으로 

1. 그림에서 그렸던 n-1개의 원판을 other 목적지로 이동시키는 재귀호출 x<sub>n-1</sub>
2. 이후 하나 남은 원판을 목적지 goal 타워에 이동시키는 메서드 호출
3. 그림에서처럼 다시 가운데 있던(other) 원판을 최종 목적이진 goal 타워로 이동시키는 재귀호출 x<sub>n-1</sub>


3개의 메서드를 호출하며, `n ==1` 일때, 위의 점화식에서 설명한 x<sub>1</sub> = 1  탈출구문을 통해 마무리 지으면된다.

타워의 경우 그냥 `Integer`로 받아서 처리를 해도 되지만, 이해도를 돕기 위해 별도의 `Tower` 클래스로 간단히 정의하여 좀더 객체지향적으로 접근했다.
 
만약 코딩테스트였다면 그냥 Integer로 받아 코드량을 줄였을 것이다.