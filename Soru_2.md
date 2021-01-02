# Soru 2) 1980’den itibaren herhangi bir spor grubunda üst üste 3 veya daha fazla madalya almış atletleri bulalım.
```SQL
Select Distinct Athlete,Sport
from
(select Athlete,Sport, Year,
    CASE
        WHEN lead(Year,1) over(partition by Athlete order by Athlete, Year) - Year = 4 and  lead(Year,2) over(partition by Athlete order by Athlete, Year) - Year = 8 THEN 1
        ELSE 0
    END is_third_time
from dsmbootcamp.cem_celik.summer_medals
where Year > 1980 
group by Sport, Athlete, Year
order by Athlete, Year)
where is_third_time = 1;
```
