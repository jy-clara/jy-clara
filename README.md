## 나는...
- 👋 Hi, I’m Clara
- 🌱 I’m currently learning Git, VSCode, Node.js.

## Code
```sql
WITH BSE_TM AS
    (SELECT TO_DATE('20200221','YYYYMMDD') AS ACCT_NEW_DT,
            TO_DATE('20230221','YYYYMMDD') AS XRN_DT,
            TO_DATE(TO_CHAR(SYSDATE,'YYYYMMDD'),'YYYYMMDD') AS COC_DT,
            3 AS ENGM_IRT,
            1 AS PTR_IRT,
            36 AS ENGM_PD_MM_CNT,
            100000 AS AMT
       FROM DUAL)
select qq
  from dual
```

<!---
jy-clara/jy-clara is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
