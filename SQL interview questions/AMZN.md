### Q1
>Transaction Table: transaction_id, customer_id, product_id, payment_date, amount
>Prime Table: customer_id, prime_status (prime-free, prime-paid, prime-student) , prime_start_date, prime_end_date

Note: if a customer is not in the Prime table, then non-prime.
Q: What is the total spend of each Prime type (prime_status) in 2018?
Follow-up: What if some people might start prime several times

1. Select all the customers whose start date and end date of prime are in 2018 (including customers who become prime member several times in 2018)
2. Join transaction table on customer id and filter the transactions for each customer that payment_date is between the prime date
3. Group the transaction amount and aggregate by prime_status
```
with prime_2018 as (select customer_id, prime_status,
       case when prime_start_date <= '2018-01-01' then '2018-01-01' else prime_start_date end as prime_start_date,
       case when Prime_end_date >='2018-12-31' then '2018-12-31' else prime_end_date end as prime_end_date
from prime p
join transaction t on p.customer_id = t.customer_id)

select prime_status, sum(amount) as total_amount
from prime_2018
where payment_date >= prime_start_date and payment_date <= prime_end_date
group by prime_status
```
```
SELECT p.prime_status, SUM(IFNULL(t.amount, 0)) AS total_spend
FROM Transaction t
LEFT JOIN Prime p
ON t.customer_id = p.customer_id
WHERE p.prime_status IS NOT NULL AND year(t.payment_date) = 2018 AND t.payment_date BETWEEN p.prime_start_date AND p.prime_end_date
GROUP BY p.prime_status
```
