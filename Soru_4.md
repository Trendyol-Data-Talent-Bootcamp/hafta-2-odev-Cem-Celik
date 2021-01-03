* **Bizim isteğimiz sample.content_category ve sample.content_category_20201222_00_59 tablolarını karşılaştırarak
insert edilen yeni kayıtları sample.content_category tablosunada eklemek** *
* **Update edilen kayıtlar var ise sample.content_category tablosunda da update etmek silinmiş olan kayıtların sample.content_category tablosundaki karşılıklarının is_deleted alanını true olarak güncellemek ve silmeden saklamaktır.** *
* **Ve bunu yaparken tek bir create or replace table ya da merge statementı kullanarak yapmak istiyoruz.** *
* **Gereken sorguyu çalıştırdıktan sonra sample.content_category ve sample.content_category_target tablolarının içerikleri birebir aynı olmalıdır!** *

```SQL
------- to see content_category_20201222_00_59 ------------
select * from `dsmbootcamp.cem_celik.content_category_20201222_00_59`;
------- to see content_category_target ------------------
select * from `dsmbootcamp.cem_celik.content_category_target`;
------- to see content_category --------------------
select * from `dsmbootcamp.cem_celik.content_category`;



---------- Solution -----------
-------------------------------

--- Looking for changes into two table ----- 

Merge `dsmbootcamp.cem_celik.content_category` T
Using `dsmbootcamp.cem_celik.content_category_20201222_00_59` S 
on T.id = S.id
WHEN MATCHED AND T.cdc_date != S.cdc_date then
    Update set T.cdc_date = S.cdc_date, T.category = S.category
WHEN MATCHED AND T.category != S.category then
    Update set T.category = S.category
WHEN NOT MATCHED BY TARGET then
    insert (cdc_date, is_deleted, id, category) VALUES(S.cdc_date, S.is_deleted, S.id, S.category)
WHEN NOT MATCHED BY SOURCE then
    Update set T.is_deleted = true;



---- Proof of Solution ----
WITH table2 as (
    Select *,farm_fingerprint(to_json_string(t2)) as _hash2
    from `dsmbootcamp.cem_celik.content_category_target` t2
)

Select *
from 
(select *,farm_fingerprint(to_json_string(t1)) as _hash1
from `dsmbootcamp.cem_celik.content_category t1) table1
right outer join table2
on table1.id = table2.id
where table1._hash1 <> table2._hash2;
```
