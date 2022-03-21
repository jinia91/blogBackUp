# 섬 연결하기

    import java.util.*;
    
    class Solution {
        static int[] parent;
        static int answer;
        public int solution(int n, int[][] costs) {
            parent = new int[n];
            for(int i =0; i<n; i++){
                parent[i] = i;
            }
    
            List<Edge> list = new ArrayList<>();
            for (int[] edgeArr : costs) {
                list.add(new Edge(edgeArr));
            }
            Collections.sort(list);
    
            for (Edge edge : list) {
               answer += union(edge);
            }
    
            return answer;
            
        }
        
        static int union(Edge edge){
            int x = find(edge.nodeA);
            int y = find(edge.nodeB);
    
            if(x==y) return 0;
            if(x>y) parent[x] = y;
            else parent[y] = x;
            return edge.cost;
        }
    
        static int find(int x){
            if(x == parent[x]) return x;
            else return parent[x] = find(parent[x]);
        }
    
        static class Edge implements Comparable<Edge>{
            int nodeA;
            int nodeB;
            int cost;
    
            public Edge(int[] edgeArr) {
                this.nodeA = edgeArr[0];
                this.nodeB = edgeArr[1];
                this.cost = edgeArr[2];
            }
    
            public int compareTo(Edge o) {
                return this.cost - o.cost;
            }
        }
    }
    

최소 신장 트리 (MST, Minimum Spanning Tree) 만들기 문제였다.

유형자체는 유명해서 금방 알아챘는데 정작 `union-find` 알고리즘 템플릿을 까먹어서 결국 인터넷을 참조했다. 복습차원에서 좋았던 문제

# 주요개념

## MST

신장트리(Spanning Tree)란 최소한의 간선갯수로 연결된 트리를 의미하며, 최소 신장트리(MST)는 사용된 간선들의 가중치 합이 가장 작은 트리를 의미한다.

위의 문제는 가중치(`cost`)가 가장 낮은 연결트리를 구하라고 했기때문에 MST 구하기 문제에 해당했다.

## Union-Find

최소한으로 연결된 트리 구하기 관련 문제에서 자주 나오는 알고리즘.

유니온 파인드란 그래프에서 여러 노드들이 같은 집합에 속하는지 확인하고

같은 집합에 속하도록 연결해주는 동작을 정의한 알고리즘으로 노드의 부모를 가리키는 배열 하나와 `union()`,`find()` 두 메서드로 구성되있다.

`union-find` 유형은 소위 말하는 알면 쉽게 맞추고 모르면 한참을 헤메는 유형이기 때문에, 왠만하면 템프릿을 외워두도록 하자.

## 크루스칼 알고리즘

음수 가중치가 없는 무방향 그래프에서 MST를 찾는 알고리즘으로 `Union Find`의 기본 템플릿에, 가중치 정보를 담은 간선을 정의하는 방식으로(`데이터구조체를 정의하든, class로 정의하든`) 구성된다.

쉽게 생각해서 `가중치`가 존재하는 `MST`만들기를 하려면, 
= `Unoin-Find`알고리즘 + `가중치 저장, 비교가능한 간선 정의` 로 암기하고 있으면 된다.

# 풀이

위에서 간략하게 알고리즘을 설명하면서 대부분 이야기했다.

 
      List<Edge> list = new ArrayList<>();
                for (int[] edgeArr : costs) {
                    list.add(new Edge(edgeArr));
                }
                Collections.sort(list);
                
가중치를 담은 간선들을 저장후, 가중치 오름차순으로 정렬을 한 뒤,

    for (Edge edge : list) {
                   answer += union(edge);
           }

간선을 순차적으로 꺼내면서 노드들을 연결해주면 된다.           

이때 

            if(x==y) return 0;
            if(x>y) parent[x] = y;
            else parent[y] = x;
            return edge.cost;
            
`x==y` 는 두 노드가 이미 연결되어있음을 뜻하는것이므로, cost 0을 반환하고, 그렇지 않을경우는 연결을 해주며 해당 cost를 반환해 합해주면 우리가 구하고자하는 MST의 cost가 나오게 된다.
