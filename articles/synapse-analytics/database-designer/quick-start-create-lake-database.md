---
title: Azure Synapse Lake 데이터베이스 및 데이터베이스 템플릿에 대한 빠른 시작
description: synapse lake 데이터베이스 및 데이터베이스 템플릿을 사용하는 방법 빠른 시작
author: gsaurer
ms.author: gesaur
ms.service: synapse-analytics
ms.subservice: database-editor
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 4db8799afdaf4a69278ce40c76baf05ab0eb1eee
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467191"
---
# <a name="quickstart-create-a-new-lake-database-leveraging-database-templates"></a>빠른 시작: 데이터베이스 템플릿을 활용하여 새 Lake 데이터베이스 만들기

이 빠른 시작을 통해 데이터베이스 템플릿을 적용하여 Lake 데이터베이스를 만들고, 새 모델에 데이터를 정렬하고, 통합 환경을 사용하여 데이터를 분석하는 방법에 대한 엔드 2 엔드 시나리오를 실행할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항
- 갤러리에서 레이크 데이터베이스 템플릿을 탐색하려면 최소한 Synapse 사용자 역할 권한이 필요합니다.
- Synapse 관리자 또는 Synapse 기여자 권한은 레이크 데이터베이스를 만들기 위한 Synapse 작업 영역에 필요합니다.
- Storage 데이터 레이크에 대한 Blob 데이터 기여자 권한이 필요합니다.

## <a name="create-a-lake-database-from-database-templates"></a>데이터베이스 템플릿에서 레이크 데이터베이스 만들기

새 데이터베이스 템플릿(미리 보기) 기능을 사용하여 데이터베이스에 대한 데이터 모델을 구성하는 데 사용할 수 있는 Lake 데이터베이스를 만듭니다. 

이 시나리오에서는 Retail 데이터베이스 템플릿을 사용하고 다음 엔터티를 선택합니다. 
 - **RetailProduct** - 제품은 잠재 고객의 요구를 충족할 수 있는 시장에 제공할 수 있는 모든 제품입니다. 해당 제품은 연결된 모든 물리적, 실체, 기호화 및 서비스 특성의 합계입니다.
 - **트랜잭션** - 실행 가능한 작업 또는 고객 활동의 가장 낮은 수준입니다.
트랜잭션은 하나 이상의 불연속 이벤트로 구성됩니다.
 - **TransactionLineItem** - 제품 및 수량별로 세분화되는 트랜잭션의 구성 요소로, 품목당 하나씩.
 - **파티** - 당사자는 비즈니스에 관심 있는 개인, 조직, 법인, 소셜 조직 또는 사업부입니다.
 - **고객** - 고객은 제품 또는 서비스를 구입했거나 구매한 개인 또는 법인입니다.
 - **채널** - 채널은 제품 또는 서비스가 판매 및/또는 배포되는 수단입니다.
 - 
테이블을 찾는 가장 쉬운 방법은 테이블이 포함된 여러 비즈니스 영역 위의 검색 상자를 사용하는 것입니다. 
 
![데이터베이스 템플릿 예제](./media/quick-start-create-lake-database/model-example.png)

 
## <a name="configure-lake-database"></a>Lake 데이터베이스 구성
 
데이터베이스를 만든 후 파일 경로를 & 스토리지 계정이 데이터를 저장하려는 위치로 설정되어 있는지 확인합니다. 경로는 기본적으로 Synapse 분석 내의 기본 스토리지 계정으로 설정되지만 필요에 따라 변경할 수 있습니다. 
  
 ![Lake 데이터베이스 예제](./media/quick-start-create-lake-database/lake-database-example.png)
 
레이아웃을 저장하고 Synapse Publish all changes(모든 변경 내용 게시) 내에서 레이아웃을 사용할 수 있도록 합니다. 이 단계에서는 lake 데이터베이스의 설정을 완료하고 Synapse Analytics 및 외부의 모든 구성 요소에서 사용할 수 있도록 합니다. 

## <a name="ingest-data-to-lake-database"></a>레이크 데이터베이스에 데이터 수집

Lake 데이터베이스에 데이터를 수집하려면 **작업 영역 DB** 커넥터가 있는 코드 없는 데이터 흐름 매핑을 사용하여 [파이프라인을](../data-integration/data-integration-data-lake.md) 실행하여 데이터를 데이터베이스 테이블에 직접 로드할 수 있습니다. 대화형 Spark Notebook을 사용하여 Lake 데이터베이스 테이블에 데이터를 수집할 수도 있습니다.

```Spark
%%sql
INSERT INTO `retail_mil`.`customer` VALUES (1,'2021-02-18',1022,557,101,'Tailspin Toys (Head Office)','Waldemar Fisar',90410,466);
```

## <a name="query-the-data"></a>데이터 쿼리

Lake 데이터베이스를 만든 후에는 데이터를 쿼리하는 다양한 방법이 있습니다. 현재 Synapse 내에서 새로 만든 lake 데이터베이스 형식을 자동으로 이해하고 이를 통해 데이터를 노출하는 SQL-Ondemand 지원합니다. 

```sql
SELECT TOP (100) [ProductId]
,[ProductName]
,[ProductDescription]
,[ProductInternalName]
,[ItemSku]
,[PrimaryBrandId]
FROM [Retail_mil].[dbo].[RetailProduct]
```

Synapse 내에서 데이터에 액세스하는 다른 방법은 새 Spark Notebook을 열고 통합 환경을 사용하는 것입니다.

```spark
df = spark.sql("SELECT * FROM `Retail_mil`.`RetailProduct`")
df.show(10)
```

## <a name="train-machine-learning-models"></a>기계 학습 모델 학습

Lake 데이터베이스를 사용하여 기계 학습 모델을 학습시키고 데이터를 점수 매기는 데 사용할 수 있습니다. 자세한 내용은 [기계 학습 모델 학습](../machine-learning/tutorial-automl.md) 

## <a name="next-steps"></a>다음 단계

아래 링크를 사용하여 데이터베이스 디자이너 기능을 계속 탐색합니다.
 - [데이터베이스 템플릿 개념](concepts-database-templates.md)
 - [Lake 데이터베이스 개념](concepts-lake-database.md)
 - [Lake 데이터베이스를 만드는 방법](create-empty-lake-database.md)
