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
            CASE WHEN A.COC_DT > A.XRN_DT THEN ROUND(A.AMT * B.SQN * (A.ENGM_IRT+A.PTR_IRT)/100 12,0)
                 ELSE TRUNC(SUM(TRUNC(SUM(A.AMT
                                              * ROUND(CASE WHEN MONTHS_BETWEEN(A.COC_DT-1,B.INT_RCK_DT) >= A.ENGM_PD_MM_CNT*0.8 THEN A.ENGM_IRT*0.9
                                                           WHEN MONTHS_BETWEEN(A.COC_DT-1,B.INT_RCK_DT) >= A.ENGM_PD_MM_CNT*0.6 THEN A.ENGM_IRT*0.7
                                                           WHEN MONTHS_BETWEEN(A.COC_DT-1,B.INT_RCK_DT) >= A.ENGM_PD_MM_CNT*0.4 THEN A.ENGM_IRT*0.5
                                                           WHEN MONTHS_BETWEEN(A.COC_DT-1,B.INT_RCK_DT) >= A.ENGM_PD_MM_CNT*0.2 THEN A.ENGM_IRT*0.3
                                                           WHEN MONTHS_BETWEEN(A.COC_DT,B.INT_RCK_DT) < 1 THEN 0.1
                                                           ELSE A.ENGM_IRT*0.1
                                                      END,3)/100
                                              * 1/12))
                            OVER (ORDER BY B.INT_RCT_DT)
                                * (CASE WHEN MONTHS_BETWEEN(B.INT_DT,B.INT_RCK_DT) >= 1 THEN 1 ELSE (B.INT_DT-B.INT_RCK_DT)/(B.INT_DT1-B.INT_RCK_DT) END))
            END AS INT_AMT
     FROM BSE_TM A, INT_CAL_BSE_TM B)
select A.ACCT_NEW_DT AS "계좌신규일자",
       A.XRN_DT AS "만기일자",
       A.COC_DT AS "해지일자",
       A.ENGM_IRT AS "약정이율",
       A.PTR_IRT AS "만기우대이율",
       A.ENGM_PD_MM_CNT AS "약정개월수",
       A.AMT AS "약정금액",
       B.SQN AS "회차",
       TO_CHAT(B.INT_RCK_DT,'YYYYMMDD') AS "이자기산일",
       TO_CHAR(B.INT_DT,'YYYYMMDD') AS "이자일",
       TO_CHAR(B.INT_DT1,'YYYYMMDD') AS "원래이자일",
       C.ALY_INT_RT AS "적용이자율",
       C.INT_AMT AS "회차이자금액",
       SUM(C.INT_AMT) OVER (ORDER BY B.SQN) AS "누적이자금액",
       TRUNC(TRUNC(SUM(C.INT_AMT) OVER (ORDER BY B.SQN),-3) * 15.4/100) AS "세금",
       (SUM(C.INT_AMT) OVER (ORDER BY B.SQN)) - TRUNC(TRUNC(SUM(C.INT_AMT) OVER (ORDER BY B.SQN),-3) * 15.4/100) AS "세후이자"
  from BSE_TM A, INT_CAL_BSE_TM B, INT_CAL_TT C
 WHERE B.SQN = C.SQN
```

<!---
jy-clara/jy-clara is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
