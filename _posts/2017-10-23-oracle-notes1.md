---
layout: post
published: true
title: Oracle Transactions Notes I
date: '2017-10-20'
---


Transactions take database from one consistent state to another. When committed, the database will be in either of these states for sure. Transactions in Oracle provide these ACID properties.

- Atomicity: Either all of a transaction happens or nothing happens.
- Consistency: Transactions take database from one consistent state to another.
- Isolotion: Effects of a transaction may not be visible to others till committed.
- Durability: Once committed, it is permanent.



!! Always explicitly "commit" or "rollback" trannsaction while terminating.

"rollback to <savepoint>" : Creates a marked point within a transaction.
  
  **1. Atomicity**
 
   a- Statement Level Atomicity: Oracle silently wraps a <savepoint> around each call to database.
  
  ```javascript
Savepoint statement1;
Insert into t values ( 1 );
If error then rollback to statement1;
Savepoint statement2;
Insert into t values ( -1 );
If error then rollback to statement2;
```

The trigger that fired by second insert will also be rolled back.

   b- Procedure Level Atomicity: 
   Oracle considers PL/SQL anonymous blocks to be statement as well. Oracle treats stored procedure call as an atomic statement! But there should not be:
     
`when others...." (exception handler)`
    
   c- Transaction Level Atomicity:
