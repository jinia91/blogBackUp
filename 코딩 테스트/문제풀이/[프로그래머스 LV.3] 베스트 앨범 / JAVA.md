# 베스트 앨범
    import java.util.*;
    class Solution {
        public List<Integer> solution(String[] genres, int[] plays) {
            Map<String, Integer> totalCounter = new HashMap<>();
            Map<String, Map<Integer, Integer>> genresMap = new HashMap<>();
    
            for(int i = 0; i< genres.length; i++){
    
                if(!totalCounter.containsKey(genres[i])){
                    totalCounter.put(genres[i], plays[i]);
                    HashMap<Integer, Integer> dataMap = new HashMap<>();
                    dataMap.put(i, plays[i]);
                    genresMap.put(genres[i], dataMap);
                }
                else {
                    totalCounter.put(genres[i], totalCounter.get(genres[i]) + plays[i]);
                    Map<Integer, Integer> dataMap = genresMap.get(genres[i]);
                    dataMap.put(i, plays[i]);
                }
            }
    
            List<Integer> answer = new ArrayList<>();
    
            List<Map.Entry<String, Integer>> counterEntry = new LinkedList<>(totalCounter.entrySet());
            counterEntry.sort((o1, o2) -> o2.getValue() - o1.getValue());
            for(var en : counterEntry){
                String genre = en.getKey();
                Map<Integer, Integer> elementData = genresMap.get(genre);
                List<Map.Entry<Integer, Integer>> entries = new LinkedList<>(elementData.entrySet());
                if(entries.size() == 0) continue;
                entries.sort((o1, o2) -> o2.getValue() - o1.getValue() == 0 ?
                        o1.getKey() - o2.getKey() : o2.getValue() - o1.getValue());
    
                answer.add(entries.get(0).getKey());
                if(entries.size() == 1) continue;
                answer.add(entries.get(1).getKey());
            }
            return answer;
        }
    }
    

평범한 정렬, 구현문제

내가 자주 사용하는 방식인 해시맵을 사용하는 카운터로 중복값처리를하면서 총량 비교를 했고,

이후엔 해시맵의 밸류에 맵을 자료구조로 사용하여 데이터를 넣고 정렬한 뒤 각 장르별 1순위 2순위 곡을 선별했다.

좀 더 객체지향적으로 풀려면 곡을 맵으로 다루는게 아니라 `Comparable`을 구현한 `Song` 클래스로 정의하고 함수들을 분리하면 나은 코드가 될 것 같다.

정렬과 해시에 대해서만 이해하면 크게 어렵지 않은 문제