+++
title = "How to Query"
date = 2019-09-09T11:41:04+07:00
draft = false
image = "https://purepng.com/public/uploads/large/purepng.com-oracle-logologobrand-logoiconslogos-251519939816xngul.png"
+++

Here's a brief summary, what i always do when asssigned to write time-consuming process in oracle SQL.
<!--more-->

### 1. Write working query

Write working query like below.

{{< highlight sql >}}
SELECT
  TLF.FILE_NAME,
  TLF.AUTH_TYP AS MTI, 
  SUBSTR(TLF.AUTH_TRAN_CDE_R, 1, 2) AS TRX_CDE, 
  TLF.AUTH_RESPONDER AS RESPONDER, 
  TLF.AUTH_ORIGINATOR AS ORIGINATOR,
  COUNT(*) AS TOTAL_TRX
FROM GGSDEV.TLF TLF
WHERE 1=1
  AND SUBSTR(TLF.FILE_NAME, -6) = '160907'
  AND TLF.HEAD_REC_TYP = '01' -- 01 = CODE FOR CUSTOMER TRANSACTION, SEE DDL TLF/PTLF IN B24
  AND TLF.AUTH_TYP IN (SELECT MSG_TYP FROM TBP_ATM_MSG_TYP WHERE FLAG = 'Y')
  AND TLF.AUTH_TRAN_CDE_R = '500000'
  AND TLF.Q1_UTIL_CDE IN (SELECT UTIL_CDE FROM TBP_ATM_UTIL_CDE WHERE FLAG = 'Y')
GROUP BY 
  TLF.FILE_NAME,
  TLF.AUTH_TYP,
  SUBSTR(TLF.AUTH_TRAN_CDE_R, 1, 2),
  TLF.AUTH_RESPONDER,
  TLF.AUTH_ORIGINATOR, 
  TLF.Q1_UTIL_CDE
{{< /highlight >}}

not so complicated but **it might** consume *resource* so much. TLF is a table that stores retail transactions
from a channel and contains millions row with 5 days retention.

> developers must have that feeling for heavy task process

skip this part *or just copy the most complicated-long-heavy-tasks query from your plsql*
  
### 2. Check oracle's scenario
Using CBO (Cost Based Optimizer) method, and check what steps oracle will do to your query. Add 
<code>explain plan for</code> before query:
{{< highlight sql >}}
EXPLAIN PLAN FOR
SELECT
  TLF.FILE_NAME,
  TLF.AUTH_TYP AS MTI, 
  SUBSTR(TLF.AUTH_TRAN_CDE_R, 1, 2) AS TRX_CDE, 
  TLF.AUTH_RESPONDER AS RESPONDER, 
  TLF.AUTH_ORIGINATOR AS ORIGINATOR,
  COUNT(*) AS TOTAL_TRX
FROM GGSDEV.TLF TLF
WHERE 1=1
  AND SUBSTR(TLF.FILE_NAME, -6) = '160907'
  AND TLF.HEAD_REC_TYP = '01' -- 01 = CODE FOR CUSTOMER TRANSACTION, SEE DDL TLF/PTLF IN B24
  AND TLF.AUTH_TYP IN (SELECT MSG_TYP FROM TBP_ATM_MSG_TYP WHERE FLAG = 'Y')
  AND TLF.AUTH_TRAN_CDE_R = '500000'
  AND TLF.Q1_UTIL_CDE IN (SELECT UTIL_CDE FROM TBP_ATM_UTIL_CDE WHERE FLAG = 'Y')
GROUP BY 
  TLF.FILE_NAME,
  TLF.AUTH_TYP,
  SUBSTR(TLF.AUTH_TRAN_CDE_R, 1, 2),
  TLF.AUTH_RESPONDER,
  TLF.AUTH_ORIGINATOR, 
  TLF.Q1_UTIL_CDE
{{< /highlight >}}

Execute the query, oracle working explain plan will return <code>Explained.</code>

To check the result, execute below query:

{{< highlight sql >}}
SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY);
{{< /highlight >}}

The result will look like this:

``` sql
Plan hash value: 947008351
 
-----------------------------------------------------------------------------------------------------
| Id  | Operation                      | Name               | Rows  | Bytes | Cost (%CPU)| Time     |
-----------------------------------------------------------------------------------------------------
|   0 | SELECT STATEMENT               |                    |     1 |    68 | 16605   (2)| 00:03:20 |
|   1 |  HASH GROUP BY                 |                    |     1 |    68 | 16605   (2)| 00:03:20 |
|   2 |   NESTED LOOPS                 |                    |     1 |    68 | 16604   (2)| 00:03:20 |
|   3 |    NESTED LOOPS                |                    |     1 |    61 | 16603   (2)| 00:03:20 |
|*  4 |     TABLE ACCESS FULL          | TLF                |    35 |  1890 | 16601   (2)| 00:03:20 |
|*  5 |     TABLE ACCESS BY INDEX ROWID| TBP_ATM_UTIL_CDE   |     1 |     7 |     1   (0)| 00:00:01 |
|*  6 |      INDEX UNIQUE SCAN         | TBP_UTIL_CDE_PK    |     1 |       |     0   (0)| 00:00:01 |
|*  7 |    TABLE ACCESS BY INDEX ROWID | TBP_ATM_MSG_TYP    |     1 |     7 |     1   (0)| 00:00:01 |
|*  8 |     INDEX UNIQUE SCAN          | TBP_ATM_MSG_TYP_PK |     1 |       |     0   (0)| 00:00:01 |
-----------------------------------------------------------------------------------------------------
 
Predicate Information (identified by operation id):
---------------------------------------------------
 
   4 - filter("TLF"."AUTH_TRAN_CDE_R"='500000' AND "TLF"."Q1_UTIL_CDE" IS NOT NULL AND 
              "TLF"."AUTH_TYP" IS NOT NULL AND SUBSTR("TLF"."FILE_NAME",-6)='160907' AND 
              "TLF"."HEAD_REC_TYP"='01' AND SUBSTR("TLF"."AUTH_TRAN_CDE_R",1,2)='50')
   5 - filter("FLAG"='Y')
   6 - access("TLF"."Q1_UTIL_CDE"="UTIL_CDE")
   7 - filter("FLAG"='Y')
   8 - access("TLF"."AUTH_TYP"="MSG_TYP")
```

you can <code>explain plain</code> using oracle sql developer. select your query then press <kbd>F10</kbd>

if you use oracle sql developer, the result preview will be like this
![Explain plan plain][1]

As seen in blocquotes and image. Oracle will do full scanning to TLF table, imagine oracle will read all that million 
data everytime the procedure executed.
One of things, that easiest to do to tweak that query, is **indexing**. 

Indexing is like creating a 'table of contents'
in document. So, if you want to find the chapter you want to read, you dont have to read all the contents first. That 
'table of contents' you made will guide you to the right chapter you want to read directly.

> Tips: I will avoid to write 'LIKE' condition with '%' as prefix, specifically like '%blabla', your indexing will useless. Ask why?

### 3. Indexing
wip...

### 4. find tuning advisor

wip...

[1]: /img/posts/explain-plan-result-sqldeveloper.png