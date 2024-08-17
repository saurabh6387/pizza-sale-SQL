SELECT count(order_id) FROM orders;

SELECT 
    ROUND(SUM(pizzas.price * orders_details.quantity),
            2) AS total_sale
FROM
    orders_details
        JOIN
    pizzas ON pizzas.pizza_id = orders_details.pizza_id;
 
select pizza_types .name ,pizzas.price
from pizza_types join pizzas
on pizza_types.pizza_type_id=pizzas.pizza_type_id
order by price desc
limit 1;

select pizzas.size,count(orders_details.order_details_id) as order_count
from pizzas join orders_details
on pizzas.pizza_id=orders_details.pizza_id
group by size
order by order_count desc
limit 1;

select pizza_types.name,sum(orders_details.quantity) as total 
from pizza_types join pizzas
on pizza_types.pizza_type_id=pizzas.pizza_type_id
join orders_details
on orders_details.pizza_id=pizzas.pizza_id
group by pizza_types.name
order by total desc 
limit 5;

select pizza_types.category,sum(orders_details.quantity) as total_sale
from pizza_types join pizzas
on pizza_types.pizza_type_id=pizzas.pizza_type_id
join orders_details
on orders_details.pizza_id=pizzas.pizza_id
group by pizza_types.category
order by total_sale desc;

select hour(order_time), count(order_id) from orders
group by hour(order_time);

select category , count(name) as count_ from pizza_types
group by category;


select round(avg(all_orders)) from 
(select orders.order_date , sum(orders_details.quantity) as all_orders
from orders join orders_details
on orders.order_id=orders_details.order_id
group by orders.order_date)as order_all;

select pizza_types.name,
sum(orders_details.quantity*pizzas.price) as revenue 
from pizza_types join pizzas
on pizzas.pizza_type_id=pizza_types.pizza_type_id
join orders_details
on pizzas.pizza_id=orders_details.pizza_id
group by  pizza_types.name 
order by revenue  desc limit 3 ;

SELECT 
    pizza_types.category,
    ROUND(SUM(orders_details.quantity * pizzas.price) / (SELECT 
                    SUM(orders_details.quantity * pizzas.price)
                FROM
                    orders_details
                        JOIN
                    pizzas ON pizzas.pizza_id = orders_details.pizza_id) * 100,
            2) AS revenue_percentage
FROM
    pizza_types
        JOIN
    pizzas ON pizzas.pizza_type_id = pizza_types.pizza_type_id
        JOIN
    orders_details ON pizzas.pizza_id = orders_details.pizza_id
GROUP BY pizza_types.category
ORDER BY revenue_percentage DESC
LIMIT 3;


select name , revenue from 
(select category ,name,revenue,
rank() over(partition by category order by revenue desc) as rn
from
(select pizza_types.category,pizza_types.name,
sum((orders_details.quantity)*pizzas.price)as revenue 
from pizza_types join pizzas
on pizza_types.pizza_type_id=pizzas.pizza_type_id
join orders_details 
on orders_details.pizza_id=pizzas.pizza_id
group by pizza_types.category,pizza_types.name) as a) as b 
where rn <=3;
