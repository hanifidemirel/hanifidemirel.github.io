---
layout: post
published: true
title: Oracle Transactions Notları 3
---
## Deferrable Constraint ve Cascading Update

Oracle 8.0'dan itibaren kısıtlamalar(constraint) ertelenebilme(deferrable) özelliğine sahip. 
"CASCADE UPDATE" e göre çok daha performanslı olan bu özelliği anlatacağım bu yazıda.

```javascript
 create table parent( pk int primary key )
 ```
 
_Table created._


```javascript
1 create table child
2 ( fk constraint child_fk_parent
3 references parent(pk)
4 deferrable
5 initially immediate )

  ```
_Table created._

```javascript
insert into parent values ( 1 );

insert into child values ( 1 );
```


Tabloyu oluştururken "initially immediate" diye belirttiğimiz için kısıtlama kontrolü hemen yapılacak ve aşağıdaki statement hata verecektir.


```update parent set pk = 2;```

`update parent set pk = 2`

`ERROR at line 1:`
`ORA-02292: integrity constraint (OPS$TKYTE.CHILD_FK_PARENT) violated - child`
`record found`