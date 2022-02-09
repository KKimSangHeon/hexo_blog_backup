---
title: django model사용시 queryset의 values 활용
date: 2021-02-21 22:08:56
categories:
- Web/App
- Django
tags:
- Django
---
model을 조회하고 이를 response로 리턴할 때 values를 활용하면 유용함.

### 쿼리셋 편하게 리턴하기 - values()

단순히 values() 를 활용할 경우 detail_order 내 외래키로 걸린 object들은 key만 리턴되는데..

아래의 경우 postnatal_care_man의 username 필드가 필요하여 아래와 같이 처리하였다.

values() 사용시 `__`을 잘 사용하면 좋을듯하다.
```
 detail_order_list = DetailOrder.objects.filter(order__customer=finder).values(
  'id',
  'road_address',
  'detail_address',
  'order_id',
  'start_time',
  'end_time',
  'detail',
  'postnatal_care_man__username'
  )


Response(detail_order_list.values(), status=status.HTTP_200_OK)
```
