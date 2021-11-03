---
title: Azure Synapse 데이터베이스 템플릿 개념
description: Azure Synapse 내의 데이터베이스 템플릿에 대해 자세히 알아보기
author: gsaurer
ms.author: gesaur
ms.service: synapse-analytics
ms.subservice: database-editor
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 628c815905f5c8afb29df028d7ba74020b941043
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441308"
---
# <a name="lake-database-templates"></a>Lake 데이터베이스 템플릿

Azure Synapse Analytics 다양한 산업에서 조직 요구 사항에 따라 데이터베이스 모델을 쉽게 사용하고 만들 수 있는 표준화된 데이터베이스 템플릿을 제공합니다. 이러한 템플릿에는 데이터 모델에 대한 이해를 구축하기 위한 풍부한 메타데이터가 포함되어 있습니다. 이러한 템플릿을 사용하여 Lake 데이터베이스를 만들고 Azure Synapse 분석 런타임을 사용하여 비즈니스 사용자에게 인사이트를 제공합니다.

Azure Synapse Lake 데이터베이스 템플릿과 관련된 개념을 알아봅니다. 이러한 템플릿을 사용하여 더 나은 이해와 생산성을 위해 풍부한 메타데이터가 있는 데이터베이스를 만듭니다.  

## <a name="business-area-templates"></a>비즈니스 영역 템플릿  

비즈니스 영역 템플릿은 비즈니스 또는 주제 영역에 대해 가장 포괄적이고 세부적인 데이터 보기를 제공합니다. 비즈니스 영역 모델을 주제 영역 또는 도메인 템플릿이라고도 합니다. 비즈니스 영역 템플릿에는 업계 내의 특정 비즈니스와 관련된 테이블 및 열이 포함되어 있습니다. 데이터 관리자, 데이터 거버넌스 팀 및 조직 내의 비즈니스 팀은 비즈니스 영역 템플릿을 사용하여 비즈니스 요구 사항 및 범위에 대한 자세한 통신을 용이하게 하는 비즈니스 중심 데이터 스키마를 빌드할 수 있습니다. 각 비즈니스 영역 템플릿은 해당 업계 엔터프라이즈 데이터베이스 템플릿의 공통 엔터티 집합에서 생성되어 비즈니스 영역 템플릿에 다른 산업 모델과 일치하는 공통 키, 특성 및 정의를 갖도록 합니다. 예를 들어 회계 & 재무 보고, 마케팅, 예산 & 예측은 소매 또는 금융과 같은 많은 산업의 비즈니스 영역 템플릿입니다. 

![비즈니스 영역 템플릿 예제](./media/concepts-database-templates/business-area-template-example.png)

## <a name="enterprise-templates"></a>Enterprise 템플릿 

Enterprise 데이터베이스 템플릿에는 특정 산업 내의 조직에서 관심을 가질 가능성이 가장 높은 테이블의 하위 집합이 포함되어 있습니다. 대략적인 개요를 제공하고 관련 비즈니스 영역 템플릿 간의 연결을 설명합니다. 이러한 템플릿은 다양한 유형의 대규모 프로젝트에 대한 액셀러레이터로 사용할 수 있습니다. 예를 들어 은행 템플릿에는 "Banking"이라는 하나의 엔터프라이즈 템플릿이 있습니다. 

![Enterprise 템플릿 예제](./media/concepts-database-templates/enterprise-template-example.png)

## <a name="table"></a>테이블

테이블은 다른 개체와 구별할 수 있는 독립적인 개체입니다. 예를 들어 Customer, Store, Channel 등이 있습니다.

## <a name="column"></a>열

각 테이블은 열 집합으로 설명됩니다. 각 열에는 이름, 설명, 데이터 형식이 있으며 테이블과 연결됩니다. 데이터베이스 템플릿에는 약 30,000개의 열이 있습니다. 예를 들어 CustomerId는 Customer 테이블의 열입니다.

## <a name="primary-key"></a>기본 키

기본 키는 전체 테이블을 고유하게 식별하는 데 도움이 됩니다. 테이블에서 튜플을 고유하게 식별해야 합니다. 예를 들어 CustomerId 열의 키는 Customer 테이블의 각 고객을 식별하기 위해 고유성을 적용합니다.

## <a name="foreign-key"></a>외래 키

외식 키는 값이 다른 테이블의 기본 키와 일치하는 열 또는 열의 조합입니다. 두 테이블 간의 연결을 설정하는 데 도움이 됩니다. 예를 들어 트랜잭션 테이블의 CustomerId는 트랜잭션을 완료한 고객을 나타냅니다. 외신 키는 항상 기본 키(예: Customer 테이블의 CustomerId 기본 키)에 대한 관계를 맺습니다.

## <a name="composite-key"></a>복합 키

복합 키는 테이블을 고유하게 식별하는 데 필요한 둘 이상의 열로 구성된 키입니다. 예를 들어 Order 테이블에서는 레코드를 고유하게 식별하기 위해 OrderNumber와 ProductId가 모두 필요할 수 있습니다.

## <a name="relationships"></a>관계

관계는 두 테이블 간의 연결 또는 상호 작용입니다. 예를 들어 Customer 및 CustomerEmail 테이블은 서로 관련되어 있습니다. 관계에는 두 개의 테이블이 있습니다. 부모 테이블과 자식 테이블이 있으며, 종종 외식 키로 연결됩니다. 테이블에서 테이블로의 관계라고 할 수 있습니다.

## <a name="table-partitions"></a>테이블 파티션

Lake 데이터베이스를 사용하면 성능 향상을 위해 테이블을 위해 기본 데이터를 분할할 수 있습니다. 데이터베이스 편집기에서 테이블의 스토리지 설정에서 파티션 구성을 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

아래 링크를 사용하여 데이터베이스 디자이너 기능을 계속 탐색합니다.
- [빠른 시작](quick-start-create-lake-database.md)
- [Lake 데이터베이스 개념](concepts-lake-database.md)
