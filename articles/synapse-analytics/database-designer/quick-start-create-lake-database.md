---
title: Azure Synapse lake 데이터베이스 및 데이터베이스 템플릿에 대 한 빠른 시작
description: 빠른 시작 synapse lake 데이터베이스 및 데이터베이스 템플릿 사용 방법
author: gsaurer
ms.author: gesaur
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 615d31355c56d3a6616aac18d92cba27e4648057
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103092"
---
# <a name="quickstart-create-a-new-lake-database-leveraging-database-templates"></a>빠른 시작: 데이터베이스 템플릿을 활용 하 여 새 Lake 데이터베이스 만들기

이 빠른 시작에서는 데이터베이스 템플릿을 적용 하 여 lake 데이터베이스를 만들고, 데이터를 새 모델에 맞추고, 통합 된 환경을 사용 하 여 데이터를 분석 하는 방법에 대 한 종단 간 시나리오를 단계별로 실행할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항
- 갤러리에서 lake 데이터베이스 템플릿을 탐색 하려면 최소 Synapse 사용자 역할 권한이 필요 합니다.
- Synapse 관리자 또는 Synapse 참여자 권한은 Synapse 작업 영역에서 lake 데이터베이스를 만드는 데 필요 합니다.
- Storage Data lake에는 Blob 데이터 참가자 권한이 필요 합니다.

## <a name="create-a-lake-database-from-database-templates"></a>데이터베이스 템플릿에서 lake 데이터베이스 만들기

새 데이터베이스 템플릿 (미리 보기) 기능을 사용 하 여 데이터베이스에 대 한 데이터 모델을 구성 하는 데 사용할 수 있는 lake 데이터베이스를 만듭니다. 

이 시나리오에서는 소매점 데이터베이스 템플릿을 사용 하 고 다음 엔터티를 선택 합니다. 
 - **RetailProduct** -제품은 잠재적 고객의 요구를 충족할 수 있는 시장에 제공할 수 있는 모든 제품입니다. 이 제품은 연관 된 모든 물리적, 심리학, 기호화 된 및 서비스 특성의 합계입니다.
 - **Transaction** -실행 가능 작업 또는 고객 활동의 가장 낮은 수준입니다.
트랜잭션은 하나 이상의 불연속 이벤트로 구성 됩니다.
 - **TransactionLineItem** -제품 및 수량을 기준으로 한 줄 항목 마다 하나씩 분할 된 트랜잭션의 구성 요소입니다.
 - **파티** -파티는 개인, 조직, 법률 엔터티, 소셜 조직 또는 비즈니스에 관심이 있는 비즈니스 단위입니다.
 - **고객** -고객은 제품 또는 서비스를 보유 하거나 구매한 개인 또는 법률 엔터티입니다.
 - **채널** -채널은 제품 또는 서비스를 판매 및/또는 배포 하는 방법입니다.
 - 
이를 찾는 가장 쉬운 방법은 테이블을 포함 하는 다양 한 비즈니스 영역 위의 검색 상자를 사용 하는 것입니다. 
 
![데이터베이스 템플릿 예제](./media/quick-start-create-lake-database/model-example.png)

 
## <a name="configure-lake-database"></a>Lake 데이터베이스 구성
 
데이터베이스를 만든 후 저장소 계정 & filepath가 데이터를 저장 하려는 위치로 설정 되었는지 확인 합니다. 이 경로는 기본적으로 Synapse analytics 내에서 기본 저장소 계정으로 변경 되지만 필요에 따라 변경할 수 있습니다. 
  
 ![Lake 데이터베이스 예제](./media/quick-start-create-lake-database/lake-database-example.png)
 
레이아웃을 저장 하 고 Synapse 내에서 사용할 수 있게 하려면 모든 변경 내용을 게시 합니다. 이 단계에서는 lake 데이터베이스의 설치를 완료 하 고 Synapse Analytics 및 외부에서 모든 구성 요소에 사용할 수 있도록 합니다. 

## <a name="ingest-data-to-lake-database"></a>Lake 데이터베이스에 데이터 수집

데이터를 lake 데이터베이스로 수집 하기 위해 데이터베이스 테이블에 직접 데이터를 로드 하는 **작업 영역 DB** 커넥터를 포함 하는 코드 무료 데이터 흐름 매핑으로 [파이프라인](../data-integration/data-integration-data-lake.md) 을 실행할 수 있습니다. 대화형 Spark 노트북을 사용 하 여 lake 데이터베이스 테이블에 데이터를 수집할 수도 있습니다.

```Spark
%%sql
INSERT INTO `retail_mil`.`customer` VALUES (1,'2021-02-18',1022,557,101,'Tailspin Toys (Head Office)','Waldemar Fisar',90410,466);
```

## <a name="query-the-data"></a>데이터 쿼리

Lake 데이터베이스를 만든 후에는 다양 한 방법으로 데이터를 쿼리할 수 있습니다. 현재 Synapse 내에서 새로 만든 lake 데이터베이스 형식을 자동으로 이해 하 고이를 통해 데이터를 노출 하는 SQL-Ondemand 지원 합니다. 

```sql
SELECT TOP (100) [ProductId]
,[ProductName]
,[ProductDescription]
,[ProductInternalName]
,[ItemSku]
,[PrimaryBrandId]
FROM [Retail_mil].[dbo].[RetailProduct]
```

Synapse 내에서 데이터에 액세스 하는 다른 방법은 새 Spark 노트북을 열고 여기에 통합 된 환경을 사용 하는 것입니다.

```spark
df = spark.sql("SELECT * FROM `Retail_mil`.`RetailProduct`")
df.show(10)
```

## <a name="train-machine-learning-models"></a>기계 학습 모델 학습

Lake 데이터베이스를 사용 하 여 machine learning 모델을 학습 하 고 데이터의 점수를 매길 수 있습니다. 자세한 내용은 [기계 학습 모델 학습](../machine-learning/tutorial-automl.md) 

## <a name="next-steps"></a>다음 단계

아래 링크를 사용하여 데이터베이스 디자이너 기능을 계속 탐색합니다.
 - [데이터베이스 템플릿 개념](concepts-database-templates.md)
 - [Lake 데이터베이스 개념](concepts-lake-database.md)
 - [Lake 데이터베이스를 만드는 방법](create-empty-lake-database.md)
