---
title: django model inner join - orm
date: 2021-02-21 22:01:41
categories:
- Web/App
- Django
tags:
- Django
---
django model의 inner 조인방법

order, detail_order 이라는 모델이 있을 때

#### detail order을 가져올 때 order내 customer이라는 컬럼을 통해 조인하는 방법
```
detail_order_list = DetailOrder.objects.filter(order__customer=finder)
둘 다 결과 동일
detail_order_list = DetailOrder.objects.select_related('order').filter(order__customer=finder)
```

즉 '__' 를 잘 활용하자...
