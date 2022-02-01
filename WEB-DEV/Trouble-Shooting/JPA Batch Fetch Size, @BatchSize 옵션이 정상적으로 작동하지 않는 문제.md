# 요약
- 페치조인은 페이징이 안되는 단점이 존재
- x To N 연관관계의 엔티티를 성능 최적화 하여 페이징처리를 하기 위해서는 조회할 엔티티 그래프 계층(M개)만큼 쿼리를 날리는 Batch Fetch Size 옵션을 키면 됨
- **Batch Fetch Size을 켰으나 조회쿼리가 Batch되지 않고 N개 날라가는 문제 발생**

## 원하는 결과

> Order -(One To Many)-> OrderItemList -(Many To One)-> Item

1. order  조회 쿼리
2. order에 연관관계로 묶인 OrderItem을 `in()`으로 묶어서 쿼리
3. OrderItem에 연관관계로 묶인 Item을 `in()` 으로 묶어서 쿼리

총 3개의 쿼리가 날라가길 원함

## 현재 상황

컨트롤러단에서 서비스 레포지토리를 거쳐 orderList를 반환받은 뒤, dto로 변환하는과정에서 lazy loading을 통해 연관관계에 엮인 엔티티들을 배치로 끌어와야하는 로직

    @GetMapping("/api/v2/orders")
    public ResponseEntity<List<OrderDtoV2>> getOrders2(){
        List<Order> allByItemList = orderService.findAllByItemListNotFetch();
        List<OrderDtoV2> orderDtos = allByItemList.stream()
                .map(order -> OrderDtoV2.builder()
                        .id(order.getId())
                        .localDateTime(order.getOrderDate())
                        .status(order.getStatus())
                        .itemList(order.getOrderItemList().stream()
                                .map(orderItem -> orderItem.getItem().getName())
                                .collect(Collectors.toList()))
                        .build())
                .collect(Collectors.toList());

        return ResponseEntity.ok(orderDtos);
    }




최초 쿼리

    select
        order0_.order_id as order_id1_6_,
        order0_.delivery_id as delivery4_6_,
        order0_.member_id as member_i5_6_,
        order0_.order_date as order_da2_6_,
        order0_.status as status3_6_ 
    from
        orders order0_


OrderItem 쿼리가 N개 날라가는 현상

    select
        orderiteml0_.order_order_id as order_or5_5_1_,
        orderiteml0_.order_item_id as order_it1_5_1_,
        orderiteml0_.order_item_id as order_it1_5_0_,
        orderiteml0_.count as count2_5_0_,
        orderiteml0_.item_id as item_id4_5_0_,
        orderiteml0_.order_order_id as order_or5_5_0_,
        orderiteml0_.order_price as order_pr3_5_0_ 
    from
        order_item orderiteml0_ 
    where
        orderiteml0_.order_order_id=1
        
    select
        orderiteml0_.order_order_id as order_or5_5_1_,
        orderiteml0_.order_item_id as order_it1_5_1_,
        orderiteml0_.order_item_id as order_it1_5_0_,
        orderiteml0_.count as count2_5_0_,
        orderiteml0_.item_id as item_id4_5_0_,
        orderiteml0_.order_order_id as order_or5_5_0_,
        orderiteml0_.order_price as order_pr3_5_0_ 
    from
        order_item orderiteml0_ 
    where
        orderiteml0_.order_order_id=2
        


Item은 `in()`으로 묶여서 배치가 적용되었지만 앞의 계층에서 쿼리가 2번날라가고 이를 기준으로 묶여 결국 쿼리가 2번 날라감

    select
        item0_.item_id as item_id2_3_0_,
        item0_.name as name3_3_0_,
        item0_.price as price4_3_0_,
        item0_.stock_quantity as stock_qu5_3_0_,
        item0_.artist as artist6_3_0_,
        item0_.etc as etc7_3_0_,
        item0_.author as author8_3_0_,
        item0_.isbn as isbn9_3_0_,
        item0_.actor as actor10_3_0_,
        item0_.director as directo11_3_0_,
        item0_.dtype as dtype1_3_0_ 
    from
        item item0_ 
    where
        item0_.item_id in (
            ?, ?
        )
        
    select
        item0_.item_id as item_id2_3_0_,
        item0_.name as name3_3_0_,
        item0_.price as price4_3_0_,
        item0_.stock_quantity as stock_qu5_3_0_,
        item0_.artist as artist6_3_0_,
        item0_.etc as etc7_3_0_,
        item0_.author as author8_3_0_,
        item0_.isbn as isbn9_3_0_,
        item0_.actor as actor10_3_0_,
        item0_.director as directo11_3_0_,
        item0_.dtype as dtype1_3_0_ 
    from
        item item0_ 
    where
        item0_.item_id in (
            ?, ?
        )
        

옵션과 엔티티에 BatchSize 애너테이션을 부착했음에도 적용되고 있지 않는 현상

현재 총 쿼리 갯수 5개(1 + N(2) + 배치된 쿼리N(2))


## 원인 분석

### 구글링

구글링 도중 동일한 문제로 헤매는 블로그 포스팅을 발견

- [https://yjksw.github.io/jpa-default-batch-fetch-size-not-working/](https://yjksw.github.io/jpa-default-batch-fetch-size-not-working/)

하지만 해결법은 없음


### 유사 예제 시도

1 to N 연관관계로 묶인 다른 도메인에서 동작 조회

    @GetMapping("api/v2/members")
    public ResponseEntity<Object> getMembersWithOrders() {
        List<MemberDto> memberDtos =
                memberService.findAll()
                        .stream()
                        .map(member -> new MemberDto(member.getName(),member.getOderList().stream()
                                .map(order -> order.getId()+"")
                                .collect(Collectors.toList())))
                        .collect(Collectors.toList());

        Map<String,Object> result = new HashMap<>();
        result.put("members", memberDtos);
        result.put("Count", memberDtos.size());

        return ResponseEntity
                .ok(result);
    }
    
member -(One to Many) -> Order

조회에서 지연로딩을 배치로 묶어 조회시도해봄


    select
        member0_.member_id as member_i1_4_,
        member0_.created_date as created_2_4_,
        member0_.updated_date as updated_3_4_,
        member0_.city as city4_4_,
        member0_.street as street5_4_,
        member0_.zipcode as zipcode6_4_,
        member0_.name as name7_4_,
        member0_.team_id as team_id8_4_ 
    from
        member member0_


       select
            oderlist0_.member_id as member_i5_6_1_,
            oderlist0_.order_id as order_id1_6_1_,
            oderlist0_.order_id as order_id1_6_0_,
            oderlist0_.delivery_id as delivery4_6_0_,
            oderlist0_.member_id as member_i5_6_0_,
            oderlist0_.order_date as order_da2_6_0_,
            oderlist0_.status as status3_6_0_ 
        from
            orders oderlist0_ 
        where
            oderlist0_.member_id in (
                ?,?,?,?,?,?
            )
            

정상적으로 `batch fetch size` 옵션이 적용되어 쿼리가 2개 날라감!

### 성공 케이스와 실패 케이스 비교

- ~~엔티티 그래프가 3계층 vs 2계층~~ 
    추가 실험에서 3계층 그래프까지 조회해보니 정상적으로 쿼리가 3개 날라감
- 코드단위에서 분석중 서비스단에서 `@Transactional(readOnly = true)` 옵션의 `false` 차이 발견


## 트러블 슈팅

`@Transactional(readOnly = true)` 이거나 `@Transactional` 어노테이션이 없을경우 batch fetch size 옵션이 정상 작동되나

`@Transactional` 이거나 `@Transactional(readOnly = false)` 인 상황에서는 batch fetch size가 바로 적용안되고 한계층 건너서부터 적용되는 현상으로 판별

조회로직이므로 `@Transactional(readOnly = true)`를 서비스단 메서드에 부착하니 정상적으로 작동되었다.

## 원인?

`@Transactional`인 경우 해당 로직이 작동하고 결과값이 반환되면서 `Entity Manager`는 flush를 하게 되는데 만약 `readOnly = true` 옵션이 켜질 경우 flush를 하지 않는 것으로 알고 있다.

이후 엔티티가 컨트롤러단으로 넘어가면서 연관 엔티티를 조회할경우 `OSIV` 옵션에 의해 커넥션이 물린채 지연로딩이 일어나게 되는데

이 과정에서 flush 여부가 프록시 객체를 초기화하기 위한 배치 쿼리 작성에 지장을 주는게 아닐까라는 가설을 세웠다.

이를 증명하기 위해

**케이스 1**

    public List<Order> findAllWithMemberDelivery(int offset, int limit) {
        List<Order> resultList = em.createQuery(
                        "select o from Order o" +
                                " join fetch o.member m" +
                                " join fetch o.delivery d", Order.class)
                .setFirstResult(offset)
                .setMaxResults(limit)
                .getResultList();
        em.flush();
        return resultList;
    }

엔티티 매니저로 직접 엔티티를 호출후 **flush** 를 명시한뒤 반환

**케이스 1 결과**

- 쿼리 5개(N+1 문제 발생)


**케이스 2**

    public List<Order> findAllWithMemberDelivery(int offset, int limit) {
        return em.createQuery(
                        "select o from Order o" +
                                " join fetch o.member m" +
                                " join fetch o.delivery d", Order.class)
                .setFirstResult(offset)
                .setMaxResults(limit)
                .getResultList();
    }
    
flush를 하지 않고 그대로 반환

**케이스 2 결과**

- 쿼리 3개(정상 쿼리)


## 결론

위의 실험결과 flush 유무가 batch fetch size 동작에 영향을 준다는 것을 확인했다.

아마도 프록시 객체의 초기화와 flush 사이의 문제때문인거같은데 정확한 원인까지는 공부가 부족해서 파악하지 못했다.

jpa는 넘나 어려운것...