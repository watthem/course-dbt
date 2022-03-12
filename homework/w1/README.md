## Objectives

- Answer these questions using the data available using SQL queries. You should query the dbt generated tables you have created.

## How many users do we have?

- Answer: 130

### Work

 ```sql

SELECT

COUNT(DISTINCT user_id) AS user_count

FROM dbt_matthew_h.stg_users

```

## On average, how many orders do we receive per hour?

- Answer: 7.5~

### Work

```sql

with hours as (
  select 
    date_trunc('hour', created_at) as hour_created, 
    count(distinct order_id) as order_count 
  from 
    dbt_matthew_h.stg_orders 
  group by 
    1
) 
select 
  avg(order_count) 
from 
  hours

```

## On average, how long does an order take from being placed to being delivered?

- Answer: 96 hours

### Work

```sql

with order_delivery_time as (

select

extract(

EPOCH

from

"delivered_at" :: timestamp - "created_at" :: timestamp

)/ 3600 as delivery_span,

count(distinct order_id) as order_count

from

dbt_matthew_h.stg_orders

group by

1

)

select

avg(delivery_span)

from

order_delivery_time

```

- StackOverflow Help: <https://stackoverflow.com/questions/1964544/timestamp-difference-in-hours-for-postgresql>

## How many users have only made one purchase? Two purchases? Three+ purchases?

> Note: you should consider a purchase to be a single order. In other words, if a user places one order for 3 products, they are considered to have made 1 purchase.

- Answers:
  - 1 = 25
  - 2 = 28
  - 3+ 71

### Work

```sql

with user_orders as (
  select 
    user_id, 
    count(order_id) as order_frequency 
  from 
    dbt_matthew_h.stg_orders 
  group by 
    1
) 
select 
  case when order_frequency = 1 then '1' when order_frequency = 2 then '2' else '3+' end as order_frequency, 
  count(user_id) 
from 
  user_orders 
group by 
  1 
order by 
  1


```

### On average, how many unique sessions do we have per hour?

- Answer: 16.32~

### Work

```sql

with user_sessions as (
  select 
    date_trunc('hour', created_at) as hour_ordered, 
    count(distinct session_id) as session_count 
  from 
    dbt_matthew_h.stg_events 
  group by 
    1
) 
select 
  avg(session_count) 
from 
  user_sessions


```
