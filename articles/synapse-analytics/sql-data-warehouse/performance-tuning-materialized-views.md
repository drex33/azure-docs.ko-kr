---
title: 구체화된 뷰를 사용한 성능 조정
description: 쿼리 성능 개선을 위해 구체화된 뷰를 사용할 때 알아야 할 권장 사항 및 고려 사항에 대해 자세히 알아봅니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/17/2021
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick; azure-synapse
ms.openlocfilehash: 2b3e2af66334ebb82c8113caac2f8b78d5c8bf20
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128674493"
---
# <a name="performance-tune-with-materialized-views"></a>구체화된 뷰를 사용한 성능 조정

Azure Synapse의 전용 SQL 풀의 구체화된 뷰는 복잡한 분석 쿼리에 대해 유지 관리 부담이 적은 방법을 제공하여 쿼리를 변경하지 않고 빠른 성능을 얻을 수 있도록 합니다. 이 문서에서는 구체화된 뷰 사용에 대한 일반적인 지침을 설명합니다.

## <a name="materialized-views-vs-standard-views"></a>구체화된 뷰 및 표준 뷰 비교

Azure Synapse의 전용 SQL 풀은 표준 및 구체화된 뷰를 지원합니다.  둘 다 SELECT 식을 사용하여 생성되고 쿼리에 논리 테이블로 표시되는 가상 테이블입니다.  뷰는 일반적인 데이터 계산의 복잡성을 캡슐화하고 계산 변경에 추상화 계층을 추가하므로 쿼리를 다시 작성할 필요가 없습니다.  

표준 뷰는 뷰가 사용될 때마다 데이터를 계산합니다.  디스크에 저장되는 데이터는 없습니다. 일반적으로 사용자는 표준 뷰를 전용 SQL 풀에서 논리 개체와 쿼리를 구성하는 데 도움이 되는 도구로 사용합니다.  표준 뷰를 사용하려면 쿼리에서 해당 뷰를 직접 참조해야 합니다.

구체화된 뷰는 테이블과 마찬가지로 전용 SQL 풀에서 데이터를 미리 컴퓨팅하고 저장하고 유지 관리합니다.  구체화된 뷰가 사용될 때마다 다시 컴퓨팅할 필요가 없습니다.  따라서 구체화된 뷰의 데이터 전체 또는 일부를 사용하는 쿼리에서 더 빠른 성능을 얻을 수 있습니다.  그뿐 아니라 쿼리에서는 구체화된 뷰를 직접 참조하지 않고 사용할 수 있으므로 애플리케이션 코드를 변경할 필요가 없습니다.  

표준 뷰의 대부분의 요구 사항은 구체화된 뷰에 적용됩니다. 구체화된 뷰 구문 및 기타 요구 사항에 대한 자세한 내용은 [CREATE MATERIALIZED VIEW AS SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true)를 참조하세요.

| 비교                     | 보기                                         | 구체화된 뷰
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------|
|정의 보기                 | 전용 SQL 풀에 저장됩니다.              | 전용 SQL 풀에 저장됩니다.
|뷰 콘텐츠                    | 뷰가 사용될 때마다 생성됩니다.   | 뷰 생성 동안 전처리된 후 전용 SQL 풀에 저장됩니다. 데이터가 기본 테이블에 추가되면 업데이트됩니다.
|데이터 새로 고침                    | 항상 업데이트됩니다.                               | 항상 업데이트됩니다.
|복합 쿼리에서 뷰 데이터를 검색하는 속도     | 느림                                         | 빠름  
|추가 스토리지                   | 예                                           | 예
|구문                          | CREATE VIEW                                  | CREATE MATERIALIZED VIEW AS SELECT

## <a name="benefits-of-using-materialized-views"></a>구체화된 뷰 사용의 이점

잘 디자인된 구체화된 뷰는 다음과 같은 이점을 제공합니다.

- JOIN 및 집계 함수를 사용하여 복잡한 쿼리의 실행 시간을 줄입니다. 쿼리가 복잡할수록 실행 시간 절감 가능성이 높아집니다. 쿼리의 컴퓨팅 비용이 높고 결과 데이터 세트가 작은 경우 가장 많은 이점을 얻을 수 있습니다.  
- 전용 SQL 풀의 최적화 프로그램은 배포된 구체화된 뷰를 자동으로 사용하여 쿼리 실행 플랜을 향상시킬 수 있습니다.  이 프로세스는 사용자에게 투명하게 진행되어 더 빠른 쿼리 성능을 제공하며 쿼리에 구체화된 뷰에 대한 직접 참조를 수행하도록 요구하지 않습니다.
- 뷰에 대한 유지 관리 부담이 낮아집니다.  기본 테이블의 모든 증분 데이터 변경 사항은 동기 방식으로 구체화된 뷰에 자동으로 추가됩니다. 즉, 기본 테이블과 구체화된 뷰가 동일한 트랜잭션에서 업데이트됩니다.  이 디자인에서는 구체화된 뷰를 쿼리하여 기본 테이블을 직접 쿼리하는 것과 동일한 데이터를 반환할 수 있습니다.  
- 구체화된 뷰의 데이터를 기본 테이블과 다르게 배포할 수 있습니다.  
- 구체화된 뷰의 데이터는 일반 테이블의 데이터와 동일한 고가용성 및 복원력 혜택을 얻습니다.  

전용 SQL 풀에서 구현된 구체화된 뷰는 다음과 같은 이점도 제공합니다.

다른 데이터 웨어하우스 공급자와 비교할 때 전용 SQL 풀에서 구현된 구체화된 뷰는 다음과 같은 이점도 제공합니다.

- 집계 함수를 광범위하게 지원합니다. [CREATE MATERIALIZED VIEW AS SELECT(Transact-SQL)](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql)를 참조하세요.
- 쿼리 관련 구체화된 뷰 권장 사항을 지원합니다.  [EXPLAIN(Transact-SQL)](/sql/t-sql/queries/explain-transact-sql)을 참조하세요.
- 기본 테이블의 데이터 변경 내용으로 자동 및 동기식 데이터 새로 고침 추가적인 조치가 필요하지 않습니다.
 
## <a name="common-scenarios"></a>일반적인 시나리오  

구체화된 뷰는 일반적으로 다음과 같은 시나리오에서 사용됩니다.

**대규모 데이터에 대해 복합 분석 쿼리의 성능을 향상시켜야 합니다.**

복합 분석 쿼리는 일반적으로 더 많은 집계 함수 및 테이블 조인을 사용하므로 쿼리 실행에서 순서 섞기 및 조인과 같이 과도한 컴퓨팅이 필요한 작업이 발생합니다.  그렇기 때문에 특히 큰 테이블에서 복잡한 분석 쿼리를 완료하는 데 시간이 더 오래 걸립니다.  

사용자는 일반적인 쿼리 계산에서 반환된 데이터에 대해 구체화된 뷰를 만들 수 있으므로 쿼리에 이 데이터가 필요할 때 재계산이 필요하지 않습니다. 따라서 컴퓨팅 비용이 줄어들고 쿼리 응답이 빨라집니다.

**쿼리를 전혀 변경하지 않거나 최소로만 변경하면서 더 빠른 성능 필요**

전용 SQL 풀의 스키마 및 쿼리 변경은 일반적으로 정기적인 ETL 작업 및 보고를 지원하기 위해 최소로만 유지됩니다.  뷰로 인해 발생하는 비용을 쿼리 성능 향상으로 상쇄할 수 있는 경우 쿼리 성능 조정을 위해 구체화된 뷰를 사용할 수 있습니다.

크기 조정 및 통계 관리 등의 다른 조정 옵션과 비교할 때, 구체화된 뷰를 생성 및 유지 관리하면 프로덕션 변경에 따른 영향을 줄이고 잠재적인 성능 향상도 높일 수 있습니다.

- 구체화된 뷰를 만들거나 유지 관리해도 기본 테이블에 대해 실행되는 쿼리에는 영향을 주지 않습니다.
- 쿼리 최적화 프로그램은 쿼리에서 직접 뷰 참조 없이 배포된 구체화된 뷰를 자동으로 사용할 수 있습니다. 이 기능을 통해 성능 조정 시 쿼리를 변경할 필요가 줄어듭니다.

**더 빠른 쿼리 성능을 위해 다른 데이터 배포 전략 필요**

전용 SQL 풀은 분산 쿼리 처리 시스템입니다.  SQL 테이블의 데이터는 세 가지 [배포 전략](sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)(해시, 라운드 로빈 또는 복제) 중 하나를 사용하여 60개 노드에 배포됩니다.   

데이터 배포는 테이블을 만들 때 지정되며 테이블을 삭제할 때까지 변경되지 않고 유지됩니다. 구체화된 뷰를 디스크의 가상 테이블로 유지하면 해시 및 라운드 로빈 데이터 배포가 지원됩니다.  사용자는 기본 테이블과는 다르지만 뷰를 사용하는 쿼리 성능에 가장 적합한 데이터 배포를 선택할 수 있습니다.  

## <a name="design-guidance"></a>디자인 지침

구체화된 뷰를 사용하여 쿼리 성능을 개선하는 일반적인 지침은 다음과 같습니다.

**워크로드를 고려한 디자인**

구체화된 뷰를 만들기 전에 쿼리 패턴, 중요도, 빈도 및 결과 데이터 크기를 기준으로 워크로드를 깊이 있게 이해하는 것이 중요합니다.  

사용자는 쿼리 최적화 프로그램에서 권장하는 구체화된 뷰에 대해 `EXPLAIN WITH_RECOMMENDATIONS <SQL_statement>`를 실행할 수 있습니다.  이러한 권장 사항은 쿼리별로 다르므로 단일 쿼리를 활용하는 구체화된 뷰가 같은 워크로드의 다른 쿼리에는 최적이 아닐 수 있습니다.  

워크로드 요구 사항을 고려해서 이러한 권장 사항을 평가합니다.  이상적인 구체화된 뷰는 워크로드의 성능에 도움이 되는 뷰입니다.  

**더 빠른 쿼리 및 비용 간 상쇄 관계에 유의해야 합니다.**

구체화된 각 뷰에 대해 데이터 스토리지 비용 및 뷰 유지 관리 비용이 발생합니다.  기본 테이블의 데이터가 변경되면 구체화된 뷰의 크기가 늘어나고 물리적 구조도 변경됩니다.  쿼리 성능 저하를 방지하기 위해 구체화된 각 뷰는 SQL 엔진에서 별도로 유지 관리됩니다.  

구체화된 뷰의 수 및 기본 테이블의 변경이 늘어나면 유지 관리 워크로드도 높아집니다.   사용자는 모든 구체화된 뷰에서 발생하는 비용을 쿼리 성능 향상으로 상쇄시킬 수 있는지 확인해야 합니다.  

이 쿼리를 실행하여 전용 SQL 풀에서 구체화된 뷰 목록을 생성할 수 있습니다.

```sql
SELECT V.name as materialized_view, V.object_id
FROM sys.views V
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
```

구체화된 뷰의 수를 줄이는 옵션:

- 워크로드에서 복합 쿼리에 자주 사용되는 공통 데이터 세트를 식별합니다.  최적화 프로그램에서 실행 플랜을 만들 때 구성 요소로 사용할 수 있게 이러한 데이터 세트를 저장하는 구체화된 뷰를 만듭니다.  

- 사용량이 낮거나 더 이상 필요하지 않은 구체화된 뷰를 삭제합니다.  사용하지 않도록 설정된 구체화된 뷰는 유지 관리되지 않지만 스토리지 비용은 계속 발생합니다.  

- 데이터가 겹치지 않는 경우에도 생성된 구체화된 뷰를 동일하거나 유사한 기본 테이블에 결합합니다.  구체화된 뷰를 결합하면 개별 뷰를 합한 것보다 크기가 더 커지지만 뷰 유지 관리 비용은 줄어듭니다.  다음은 그 예입니다.

```sql

-- Query 1 would benefit from having a materialized view created with this SELECT statement

SELECT A, SUM(B)
FROM T
GROUP BY A

-- Query 2 would benefit from having a materialized view created with this SELECT statement

SELECT C, SUM(D)
FROM T
GROUP BY C

-- You could create a single mateiralized view of this form

SELECT A, C, SUM(B), SUM(D)
FROM T
GROUP BY A, C

```

**모든 성능 조정에서 쿼리를 변경해야 하는 것은 아님**

SQL 쿼리 최적화 프로그램은 배포된 구체화된 뷰를 자동으로 사용하여 쿼리 성능을 향상시킬 수 있습니다.  이 지원은 구체화된 뷰를 만들 때 지원되지 않는 집계를 사용하는 뷰 및 쿼리를 참조하지 않는 쿼리에 투명하게 적용됩니다.  쿼리를 변경할 필요가 없습니다. 쿼리의 예상 실행 플랜을 확인하여 구체화된 뷰가 사용되는지 알아볼 수 있습니다.  

**구체화된 보기 모니터링**

구체화된 뷰는 CCI(클러스터형 columnstore 인덱스)가 있는 테이블과 마찬가지로 전용 SQL 풀에 저장됩니다.  구체화된 뷰에서 데이터를 읽으면 CCI 인덱스 세그먼트가 검색되고 기본 테이블의 모든 증분 변경 내용이 적용됩니다. 증분 변경 수가 너무 많으면 구체화된 뷰에서 쿼리를 확인하는 것이 기본 테이블을 직접 쿼리하는 것보다 더 오래 걸릴 수 있습니다.  

쿼리 성능 저하를 방지하려면 [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)를 실행하여 뷰의 overhead_ratio(total_rows / max(1, base_view_row))를 모니터링하는 것이 좋습니다.  사용자는 overhead_ratio가 너무 높은 경우 구체화된 뷰를 다시 빌드해야 합니다.

**구체화된 뷰 및 결과 세트 캐싱**

전용 SQL 풀의 이 두 가지 기능은 쿼리 성능 튜닝에 사용됩니다. 결과 세트 캐싱은 정적 데이터에 대한 반복 쿼리에서 높은 동시성 및 빠른 응답을 얻는 데 사용됩니다.  

캐시된 결과를 사용하려면 쿼리를 요청하는 캐시의 형식이 캐시를 생성한 쿼리와 일치해야 합니다.  또한 캐시된 결과가 전체 쿼리에 적용되어야 합니다.  

구체화된 뷰를 사용하면 기본 테이블의 데이터를 변경할 수 있습니다.  구체화된 뷰의 데이터는 쿼리 부분에 적용될 수 있습니다.  이 지원을 통해 더 빠른 성능을 위해 일부 계산을 공유하는 여러 쿼리에서 동일한 구체화된 뷰를 사용할 수 있습니다.

## <a name="example"></a>예제

이 예에서는 매장보다 카탈로그를 통해 더 많은 비용을 소비하는 고객을 찾는 TPCDS와 유사한 쿼리를 사용하여 선호 고객과 해당 고객의 원래 국가/지역을 식별합니다.   이 쿼리에는 SUM()과 GROUP BY를 포함하는 세 개의 하위 SELECT 문 UNION에서 상위 100개 레코드를 선택하는 작업이 포함됩니다.

```sql
WITH year_total AS (
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
       ,'s' sale_type
FROM customer
     ,store_sales
     ,date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
       ,'c' sale_type
FROM customer
     ,catalog_sales
     ,date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
       ,'w' sale_type
FROM customer
     ,web_sales
     ,date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
         )
  SELECT TOP 100
                  t_s_secyear.customer_id
                 ,t_s_secyear.customer_first_name
                 ,t_s_secyear.customer_last_name
                 ,t_s_secyear.customer_birth_country
FROM year_total t_s_firstyear
     ,year_total t_s_secyear
     ,year_total t_c_firstyear
     ,year_total t_c_secyear
     ,year_total t_w_firstyear
     ,year_total t_w_secyear
WHERE t_s_secyear.customer_id = t_s_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_c_secyear.customer_id
   AND t_s_firstyear.customer_id = t_c_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_secyear.customer_id
   AND t_s_firstyear.sale_type = 's'
   AND t_c_firstyear.sale_type = 'c'
   AND t_w_firstyear.sale_type = 'w'
   AND t_s_secyear.sale_type = 's'
   AND t_c_secyear.sale_type = 'c'
   AND t_w_secyear.sale_type = 'w'
   AND t_s_firstyear.dyear+0 =  1999
   AND t_s_secyear.dyear+0 = 1999+1
   AND t_c_firstyear.dyear+0 =  1999
   AND t_c_secyear.dyear+0 =  1999+1
   AND t_w_firstyear.dyear+0 = 1999
   AND t_w_secyear.dyear+0 = 1999+1
   AND t_s_firstyear.year_total > 0
   AND t_c_firstyear.year_total > 0
   AND t_w_firstyear.year_total > 0
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_s_firstyear.year_total > 0 THEN t_s_secyear.year_total / t_s_firstyear.year_total ELSE NULL END
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_w_firstyear.year_total > 0 THEN t_w_secyear.year_total / t_w_firstyear.year_total ELSE NULL END
ORDER BY t_s_secyear.customer_id
         ,t_s_secyear.customer_first_name
         ,t_s_secyear.customer_last_name
         ,t_s_secyear.customer_birth_country
OPTION ( LABEL = 'Query04-af359846-253-3');
```

쿼리의 예상 실행 플랜을 확인합니다.  18개의 순서 섞기 작업과 17개의 조인 작업이 있으며 실행하는 데 더 많은 시간이 걸립니다. 이제 3개의 하위 SELECT 문 각각에 대해 구체화된 뷰 하나를 만듭니다.

```sql
CREATE materialized view nbViewSS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.store_sales
     ,dbo.date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
GO
CREATE materialized view nbViewCS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
          , count_big(*) as cb
FROM dbo.customer
     ,dbo.catalog_sales
     ,dbo.date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

GO
CREATE materialized view nbViewWS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.web_sales
     ,dbo.date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

```

원래 쿼리의 실행 플랜을 다시 확인합니다.  이제 조인 수가 17에서 5로 변경되고 순서 섞기가 발생하지 않습니다.  플랜에서 필터 작업 아이콘을 선택하면 해당 출력 목록에 기본 테이블 대신 구체화된 뷰에서 데이터를 읽어온 것으로 표시됩니다.  

 ![Plan_Output_List_with_Materialized_Views](./media/performance-tuning-materialized-views/output-list.png)

구체화된 뷰를 사용하면 코드를 변경하지 않고도 동일한 쿼리가 빠르게 실행됩니다.  

## <a name="next-steps"></a>다음 단계

더 많은 개발 팁은 [전용 SQL 풀 개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요.
