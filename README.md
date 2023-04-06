use mavenmovies;

-- List all actors whose first name start with A
select * from actor 
where first_name like '%a';

select * from actor 
where first_name regexp '^a';

select * from actor 
where first_name like 'a%';

select * from actor 
where first_name regexp 'a$';

select * from actor 
where first_name regexp '[xyz]';

select * from actor 
where first_name regexp '^[xyz]';

select * from actor 
where first_name regexp '[^ABC]';

select * from actor 
where first_name regexp '[ABC]$';

select * from actor 
where first_name regexp '^[A-C]';
select * from actor 
where first_name regexp '[0-9]';

select * from actor 
where first_name LIKE '_A%';

select * from actor 
where first_name regexp '^[A-Z][A]';


select * from actor 
where first_name regexp '^.[A]';

-- Find all email addresses which contain a '.' and an '@' symbol and ends with '.org'
select * from customer where email regexp '[.][@].org$';

select actor_id, count(*) as film_count from film_actor
group by actor_id
having film_count > 25;

select email from customer 
where email regexp '*[.]*[@]*.org$';
-- Find the number of films in Action, Animation and Children categories
select name, count(*) from category
group by name

-- Which customer paid the maximum amount
select customer_id,sum(amount) as total_cost from payment
group by customer_id
order by total_cost desc
limit 1;
with customer_cte
as
(select customer_id,sum(amount) as total_cost from payment
group by customer_id
order by total_cost 
limit 5)
select * from customer_cte

-- case expression

-- clasify the category of film across the ages




-- What are the average number of films in which an actor as acted in
select avg(film_count) from 
(
select  actor_id,count(*) as film_count from film_actor
group by actor_id) temp;
-- 
select avg(film_count) from 
(select  actor_id,count(*) as film_count from film_actor
group by actor_id) as T;
-- 
create temporary table T 
(select  actor_id,count(*) as film_count from film_actor
group by actor_id)
select avg(film_count) from T


-- CTE : Common Table Expression
/* WITH CTE_NAME
AS (QUERY)
MY_QUERY
*/
with actor_cte
as
(select actor_id ,first_name from actor)
select * from actor_cte

select name from category;
select name,
case name 
when 'children' then 'all ages'
when 'family' then 'all ages'
when 'sport' then 'all ages'
when 'horror' then 'adult'
when 'animation' then 'all ages'
when 'music' then 'teens'
when 'games' then 'teens'
else 'other'
end as type
from category;

/*Label the movies as short , medium, long based on the
length of the movie
length < 50 : short
50 < length < 100 : medium
length > 100 : long

title, label*/

select title,length,
case length
when length < 25 then 'very short'
when length < 50 then 'short'
when length < 100 then 'medium'
else 'long'
end as label
from film;



-- write a SQL query to find the actors who played a role in the movie 'Annie Identity'. Return all the fields of the actor Table

select * from film;
select * from actor;
select * from film_actor;
select a.actor_id,a.first_name,a.last_name, f.title,f.film_id from actor a
inner join film_actor fa 
on a.actor_id = fa.actor_id
inner join film f
on f.film_id = fa.film_id
where f.title = 'Annie Identity'


-- Which customer has the highest customer ID number, whose first name starts with an 'E' and has an address ID lower than 500?
select * from customer;

select max(customer_id),first_name,address_id from customer
where first_name regexp '^e' and address_id < 500;

-- Find the films which are rented by both Indian and Pakistani customers. (Hint: You can use CTE’s)
select ind_films as common_films from

(with cust_india as (select c.customer_id, c.first_name, c.last_name, ct.city, cn.country from customer c 
inner join address a on c.address_id = a.address_id 
inner join city ct on ct.city_id = a.city_id
inner  join country cn on cn.country_id = ct.country_id
inner join film f on c.customer_id
group by c.customer_id
having cn.country = 'india'), # Pulls the indian customers

cust_india_films as (select f.title ind_films, ci.first_name, ci.last_name, ci.country from 
cust_india ci inner join rental r on ci.customer_id = r.customer_id
inner join inventory i on r.inventory_id = i.inventory_id
inner join film f on i.film_id = f.film_id), # Pull the films rented by Indian customers

cust_pakistan as (select c.customer_id, c.first_name, c.last_name, ct.city, cn.country from customer c 
inner join address a on c.address_id = a.address_id 
inner join city ct on ct.city_id = a.city_id
inner  join country cn on cn.country_id = ct.country_id
inner join film f on c.customer_id
group by c.customer_id
having cn.country = 'pakistan'), # Pull the pakistan customers

cust_pakistan_films as (select f.title pak_films from 
cust_pakistan cp inner join rental r on cp.customer_id = r.customer_id
inner join inventory i on r.inventory_id = i.inventory_id
inner join film f on i.film_id = f.film_id) # Pull the films rented by pakistan customers

select * from cust_pakistan_films cpf 
inner join cust_india_films cif on cpf.pak_films = cif.ind_films
group by cif.ind_films) dummy; # Get the common films.












-- Find the films (if any) which are rented by Indian customers and not rented by Pakistani customers.

select ind_films from
(with cust_india as (select c.customer_id, c.first_name, c.last_name, ct.city, cn.country from customer c 
inner join address a on c.address_id = a.address_id 
inner join city ct on ct.city_id = a.city_id
inner  join country cn on cn.country_id = ct.country_id
inner join film f on c.customer_id
group by c.customer_id
having cn.country = 'india'), # Pulls the indian customers

cust_india_films as (select f.title ind_films, ci.first_name, ci.last_name, ci.country from 
cust_india ci inner join rental r on ci.customer_id = r.customer_id
inner join inventory i on r.inventory_id = i.inventory_id
inner join film f on i.film_id = f.film_id), # Pull the films rented by Indian customers

cust_pakistan as (select c.customer_id, c.first_name, c.last_name, ct.city, cn.country from customer c 
inner join address a on c.address_id = a.address_id 
inner join city ct on ct.city_id = a.city_id
inner  join country cn on cn.country_id = ct.country_id
inner join film f on c.customer_id
group by c.customer_id
having cn.country = 'pakistan'), # Pull the pakistan customers

cust_pakistan_films as (select f.title pak_films from 
cust_pakistan cp inner join rental r on cp.customer_id = r.customer_id
inner join inventory i on r.inventory_id = i.inventory_id
inner join film f on i.film_id = f.film_id) # Pull the films rented by pakistan customers

select * from cust_pakistan_films cpf 
right join cust_india_films cif on cpf.pak_films = cif.ind_films
group by cif.ind_films
having cpf.pak_films is null) dummy; 


-- Find the customers who paid a sum of 100 dollars or more, for all the rentals taken by them.
SELECT c.first_name,c.last_name, sum(amount) as total FROM payment p
inner join customer c on c.customer_id = p.customer_id
group by c.customer_id
having total > 100
order by total desc;








select * from customers
where country in ('usa','norway')

-- name contains a-c anywhere
select * from customers
where contactfirstname regexp '.[a-c]'

-- name not starting with letter a
select * from customers
where contactfirstname regexp '^an?'

select * from customers
where contactfirstname regexp '[a-e][a-f][^a]'

select * from customers
where contactfirstname regexp 'an|pm'

select * from orders;
select * from orderdetails;

select status,
sum(quantityOrdered*priceeach)
from orders
inner join orderdetails
using(ordernumber)
group by status;



use mavenmovies;
select * from actor
where first_name like '^m'

-- count the number of cities in a country

delimiter //
 
 create procedure cities_count(IN country_name CHAR(10), OUT city_count INT)
 BEGIN
 SELECT COUNT(*) into city_count from city join country on city.country_id = country.country_id
 where country = country_name;
end //


delimiter //
create procedure my_loops_even(IN user_input int)
begin
 declare num int default 0;
 declare sum int default 0;
 while num < user_input do
 set sum = sum + num;
 set num = num + 2;
 end while;
 select sum;
 end //
 
drop procedure my_loops_fac;

DELIMITER //
CREATE PROCEDURE LoopDemo()
BEGIN
	DECLARE x  INT;
	DECLARE str  VARCHAR(255);
	SET x = 1;
	SET str =  '';
	loop_label:  LOOP
		IF  x > 10 THEN 
			LEAVE  loop_label;
		END  IF;
            
		SET  x = x + 1;
		IF  (x mod 2) THEN
			ITERATE  loop_label;
		ELSE
			SET  str = CONCAT(str,x,',');
		END  IF;
	END LOOP;
	SELECT str;
END //

drop procedure whileeloop;
DELIMITER //
create procedure whileeloop(IN User_input int)
begin
declare num int default 1;
declare res varchar(50);
set res = cast(num as char);
while num < User_input do
set num = num+2;
set res = concat(res,',',num);
end while;
select res;
end //


delimiter//
create procedure product_even(in user_input int)
begin
declare num int default 1;
declare res varchar(50);
set res = s
while 

end //

