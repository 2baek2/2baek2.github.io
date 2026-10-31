---
title: "Postgres 명령어 모음"
date: 2024-06-04 10:20:21+0900
categories: [기술, DB]
tags: [postgres]
---

# postgres 명령어

## postgres 재시작

```bash
sudo sudo systemctl restart postgresql
```

## 테이블 이름 바꾸기

```sql title:"change table name"
ALTER TABLE table_name
RENAME TO new_table_name;
```

## 인덱스 조회

```sql title:"select index"
SELECT * FROM pg_indexes WHERE tablename = 'table_name';
```
