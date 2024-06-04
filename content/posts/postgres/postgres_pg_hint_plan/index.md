---
title: 'PostgreSQL pg_hint_plan'
date: 2024-06-04T13:58:34+09:00
draft: false
# summary:
# hideSummary: false
tags:
    - "postgres"
---

# pg_hint_plan
pg_hint_plan은 PostgreSQL에 쿼리를 전송할 때 쿼리와 함께 hint를 제공해 원하는 쿼리 플랜을 통해 쿼리가 실행되도록 만들 수 있다.

[pg_hint_plan_github](https://github.com/ossc-db/pg_hint_plan/tree/master)

github installation에 나와있는 대로 설치를 하면 된다.

## 주요 활용 방법
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

