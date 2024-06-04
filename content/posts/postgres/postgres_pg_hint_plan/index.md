---
title: 'PostgreSQL pg_hint_plan'
date: 2024-06-04T13:58:34+09:00
draft: false
# summary:
# hideSummary: false
tags:
    - "postgres"
font: arial
---

# pg_hint_plan
pg_hint_plan은 PostgreSQL에 쿼리를 전송할 때 쿼리와 함께 hint를 제공해 원하는 쿼리 플랜을 통해 쿼리가 실행되도록 만들 수 있다.

[pg_hint_plan_github](https://github.com/ossc-db/pg_hint_plan/tree/master)

github installation에 나와있는 대로 설치를 하면 된다.

## 쿼리 전
나는 pg_hint_plan을 사용하기 전에 아래 sql을 입력하고 활용한다.

```sql
load 'pg_hint_plan';
set pg_hint_plan.message_level to notice;
SET pg_hint_plan.debug_print TO on;
```
그러면 아래처럼 쿼리 힌트를 제공했을 때 어떤 힌트가 사용되고 있는지 알 수 있다.
```sql
NOTICE: available indexes for IndexScan
NOTICE: pg_hint_plan:
used hint:
not used hint:
duplication hint:
error hint:
```

## Hint list

아래 표를 [github hint_list](https://github.com/ossc-db/pg_hint_plan/blob/master/docs/hint_list.md) 여기서 확인할 수 있다.

The available hints are listed below.

| Group | Format | Description |
|:------|:-------|:------------|
| Scan method | `SeqScan(table)`| Forces sequential scan on the table. |
| | `TidScan(table)` | Forces TID scan on the table. |
| | `IndexScan(table[ index...])` | Forces index scan on the table.  Restricts to specified indexes if any. |
| | `IndexOnlyScan(table[ index...])` | Forces index-only scan on the table.  Restricts to specified indexes if any.  Index scan may be used if index-only scan is not available. |
| | `BitmapScan(table[ index...])`| Forces bitmap scan on the table.  Restricts to specified indexes if any. |
| | `IndexScanRegexp(table[ POSIX Regexp...])`<br>`IndexOnlyScanRegexp(table[ POSIX Regexp...])`<br>`BitmapScanRegexp(table[ POSIX Regexp...])` | Forces index scan, index-only scan (For PostgreSQL 9.2 and later) or bitmap scan on the table.  Restricts to indexes that matches the specified POSIX regular expression pattern. |
| | `NoSeqScan(table)`| Forces to *not* do sequential scan on the table. |
| | `NoTidScan(table)`| Forces to *not* do TID scan on the table.|
| | `NoIndexScan(table)`| Forces to *not* do index scan and index-only scan on the table. |
| | `NoIndexOnlyScan(table)`| Forces to *not* do index only scan on the table. |
| | `NoBitmapScan(table)` | Forces to *not* do bitmap scan on the table. |
| Join method| `NestLoop(table table[ table...])` | Forces nested loop for the joins on the tables specified. |
| | `HashJoin(table table[ table...])`| Forces hash join for the joins on the tables specified. |
| | `MergeJoin(table table[ table...])` | Forces merge join for the joins on the tables specified. |
| | `NoNestLoop(table table[ table...])`| Forces to *not* do nested loop for the joins on the tables specified. |
| | `NoHashJoin(table table[ table...])`| Forces to *not* do hash join for the joins on the tables specified. |
| | `NoMergeJoin(table table[ table...])` | Forces to *not* do merge join for the joins on the tables specified. |
| Join order | `Leading(table table[ table...])` | Forces join order as specified. |
| | `Leading(<join pair>)`| Forces join order and directions as specified.  A join pair is a pair of tables and/or other join pairs enclosed by parentheses, which can make a nested structure. |
| Behavior control on Join | `Memoize(table table[ table...])` | Allows the topmost join of a join among the specified tables to Memoize the inner result. Not enforced. |
| | `NoMemoize(table table[ table...])` | Inhibits the topmost join of a join among the specified tables from Memoizing the inner result. |
| Row number correction | `Rows(table table[ table...] correction)` | Corrects row number of a result of the joins on the tables specified.  The available correction methods are absolute (#<n>), addition (+<n>), subtract (-<n>) and multiplication (*<n>).  <n> should be a string that strtod() can understand. |
| Parallel query configuration | `Parallel(table <# of workers> [soft\|hard])` | Enforces or inhibits parallel execution of the specified table.  <# of workers> is the desired number of parallel workers, where zero means inhibiting parallel execution.  If the third parameter is soft (default), it just changes max\_parallel\_workers\_per\_gather and leaves everything else to the planner.  Hard enforces the specified number of workers. |
| GUC | `Set(GUC-param value)` | Sets GUC parameter to the value defined while planner is running. |

### 활용 방법
#### IndexScan
Scan method에서 IndexScan은 `IndexScan(table_name index_name)`와 같이 입력하면 된다.

#### Join method
테이블 여러개를 HashJoin을 활용해서 join하고 싶다면 `HashJoin(table_name1 table_name2 table_name3)`와 같이 입력하면 된다.

#### Join order
테이블이 여러개 있는 경우 특정 테이블을 먼저 join하고자 한다면 `Leading(table_name1 table_name2)`와 같이 입력하면 된다. 

join 순서까지 정해주고 싶다면 `Leading((table_name1 table_name2))`와 같이 활용하면 된다.

#### Join method + Join order
table 1, 2, 3을 hash join하고 그 중 table 1과 2를 먼저 join 하도록 원한다면 아래와 같이 활용하면 된다.
```sql
/*+
HashJoin(table_name1 table_name2 table_name3)
Leading(table_name1 table_name2)
*/
select ...
```

