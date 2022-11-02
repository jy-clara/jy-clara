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
       FROM DUAL),
     INT_CAL_BSE_TM AS
    (SELECT LEVEL AS SQN,
            ADD_MONTHS(A.ACCT_NEW_DT,LEVEL-1) AS INT_RCT_DT,
            LEAST(ADD_MONTHS(A.ACCT_NEW_DT,LEVEL),A.COC_DT) AS INT_DT,
            ADD_MONTHS(A.ACCT_NEW_DT) AS INT_DT1
       FROM BSE_TM A
     CONNECT BY LEVEL <= CEIL(MONTHS_BETWEEN(A.COC_DT, A.ACCT_NEW_DT))),
     INT_CAL_TT AS
    (SELECT B.SQN,
            ROUND(CASE WHEN C.COC_DT > A.XRN_DT THEN A.ENGM_IRT+A.PTR_IRT
                       WHEN MONTHS_BETWEEN(A.COC_DT-1,B.INT_RCK_DT) >= A.ENGM_PD_MM_CNT×0.8 THEN A.ENGM_IRT×0.9
                       WHEN MONTHS_BETWEEN(A.COC_DT-1,B.INT_RCK_DT) >= A.ENGM_PD_MM_CNT×0.6 THEN A.ENGM_IRT×0.7
                       WHEN MONTHS_BETWEEN(A.COC_DT-1,B.INT_RCK_DT) >= A.ENGM_PD_MM_CNT×0.4 THEN A.ENGM_IRT×0.5
                       WHEN MONTHS_BETWEEN(A.COC_DT-1,B.INT_RCK_DT) >= A.ENGM_PD_MM_CNT×0.2 THEN A.ENGM_IRT×0.3
                       WHEN MONTHS_BETWEEN(A.COC_DT-1,B.INT_RCK_DT) < 1 THEN 0.1
                       ELSE A.ENGM_IRT*0.1
                  END,2) AS ALY_INT_RT,
select qq
  from dual
```

<!---
jy-clara/jy-clara is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
