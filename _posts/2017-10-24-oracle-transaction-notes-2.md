---
layout: post
published: false
title: Oracle Transaction Notes 2
---

## DDL and Atomicity

In Oracle, there is a certain class of statements that are atomic only at the statement level. DDL, Data Definition Language,  is one instance to that class
    Here how it basically works:
    
   - At the beginning commits any outstanding work
   - Perform DDL operation
   - Commit or rollback in the end
    
 !Commits Automatically!
 
 DDL Statements:
    
   - Create, alter, drop schema objects
   - Grand\revoke privileges and roles
   - Analyze, Audit, Comment 
   - Rename, Truncate
  
  
! Oracle database implicitly commits the current transaction before and after every DDL statement

## Durability

Normally when a transaction is committed, its changes are permanent. There is 2 exception to this rule.

### 1- Write extension to commit

In default mode, (commit write wait) commit works synchronously. If you write "COMMIT WRITE NOWAIT" it becomes an async process and application does not wait for the redo log files to be writtten to disc. While this method provide performance increase, it breaks durability.
This feature must be used in applications which are automatically restarable. 
Better don't use at all
