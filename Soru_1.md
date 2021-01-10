# Soru 1) 1980’den itibaren spor grubu bazında en çok madalya alan 1. 3. 5. ülkeyi bulalım.
```SQL
select  Distinct Sport,
first_value(Country) over (partition by Sport order by  Sport, count(Medal) desc) as First_most_medals,
nth_value(Country, 3) over (partition by Sport order by  Sport, count(Medal) desc rows between unbounded preceding and 2 following) as Third_most_medals,
nth_value(Country, 5) over (partition by Sport order by  Sport, count(Medal) desc rows between unbounded preceding and 4 following) as Fifth_most_medals,
from dsmbootcamp.cem_celik.summer_medals
where Year >= 1980
group by  Country, Sport
order by Sport desc;
```
