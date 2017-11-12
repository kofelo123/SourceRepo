### Test  

- Test   

<pre><code>
create or replace view order_view
as
select d.odseq, o.oseq, o.id, o.indate, d.pseq,d.quantity, m.name mname,
m.zip_num, m.address, m.phone, p.name pname, p.price2, d.result   
from orders o, order_detail d, member m, product p 
where o.oseq=d.oseq and o.id = m.id and d.pseq = p.pseq;
           
</code></pre>
