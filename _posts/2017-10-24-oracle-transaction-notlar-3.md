---
layout: post
published: true
title: Oracle Transaction Notları 3
date: '2017-10-24'
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


```javascript
update parent set pk = 2;

```

`update parent set pk = 2`

`ERROR at line 1:`
`ORA-02292: integrity constraint (OPS$TKYTE.CHILD_FK_PARENT) violated - child`
`record found`


Şu şekilde değiştirirsek kontrol etmesini erteleyebiliriz.


```javascript
set constraint child_fk_parent deferred;
```

```javascript
	update parent set pk = 2;
1 row updated.
```


Bu aşamadan sonra tekrar kısıtlamanın ihlal edilip edilmediğini kontrol ettirmek için eski haline döndürmeliyiz.

```javascript
ops$tkyte%ORA11GR2> set constraint child_fk_parent immediate;
set constraint child_fk_parent immediate
*
ERROR at line 1:
ORA-02291: integrity constraint (OPS$TKYTE.CHILD_FK_PARENT) violated - parent
key not found
```


Kural ihlali hatası veriyor. Düzeltip tekrar denediğimizde commit edebileceğiz.

```javascript
ops$tkyte%ORA11GR2> update child set fk = 2;
1 row updated.

ops$tkyte%ORA11GR2> set constraint child_fk_parent immediate;
Constraint set.
ops$tkyte%ORA11GR2> commit;
Commit complete.
```

Bu özelliğin çalışabilmesi için ilgili kısıtlamanın en başta "**DEFERRABLE**" olarak belirtilmesi gerektiği unutulmamalı. Ancak ne olur ne olmaz diye de tüm kısıtlamalar "**DEFERRABLE INITIALLY IMMEDIATE**" olarak yazılmamalı. Aksi halde ilgili kolon için Oracle'ın indeksleme yöntemi değişeceğinden performans sorununa yol açacaktır.
