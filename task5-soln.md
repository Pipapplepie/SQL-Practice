5.1

![51](https://user-images.githubusercontent.com/107236740/192085692-54dff3a3-ea5a-4fb7-9de6-b99bf0c44da4.png)

返回到当前位置sale_price的最大值。

5.2

```sql
select  product_id
       ,product_name
       ,sale_price
       ,regist_date
       ,sum(sale_price) OVER (partition by regist_date
                                  order by regist_date) AS Current_sum_price
from product;
```

![52](https://user-images.githubusercontent.com/107236740/192085731-f15bada8-2e95-4010-83c4-0f154045f7da.png)

5.3

1）函数直接作用在整个表上，而非各个分区内；

2）
