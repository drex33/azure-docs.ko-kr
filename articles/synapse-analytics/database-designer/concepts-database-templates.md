---
title: Azure Synapse 데이터베이스 템플릿 개념
description: Azure Synapse 내의 데이터베이스 템플릿에 대 한 자세한 정보
author: gsaurer
ms.author: gesaur
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 3ae4d5efc5c80a6a4ff5fdd4c1c5e77ac0d7cb30
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102904"
---
# <a name="lake-database-templates"></a>Lake 데이터베이스 템플릿

Azure Synapse Analytics는 조직의 요구에 따라 손쉽게 사용 하 고 데이터베이스 모델을 만들 수 있는 다양 한 산업에 대해 표준화 된 데이터베이스 템플릿을 제공 합니다. 이러한 템플릿에는 데이터 모델에 대 한 이해를 위한 다양 한 메타 데이터가 포함 됩니다. 이러한 템플릿을 사용 하 여 lake 데이터베이스를 만들고 Azure Synapse 분석 런타임을 사용 하 여 비즈니스 사용자에 게 통찰력을 제공 합니다.

Azure Synapse의 lake 데이터베이스 템플릿과 관련 된 개념을 알아봅니다. 이러한 템플릿을 사용 하 여 더 나은 이해 및 생산성을 위해 다양 한 메타 데이터를 포함 하는 데이터베이스를 만듭니다.  

## <a name="business-area-templates"></a>비즈니스 영역 템플릿  

비즈니스 영역 템플릿은 비즈니스 또는 주제 영역에 대 한 데이터를 가장 포괄적이 고 세부적으로 보여 주는 뷰를 제공 합니다. 비즈니스 영역 모델을 주제 영역 또는 도메인 템플릿이 라고도 합니다. 비즈니스 영역 템플릿에는 업계 내 특정 비즈니스와 관련 된 테이블 및 열이 포함 되어 있습니다. 조직 내의 데이터 관리자, 데이터 거 버 넌 스 팀 및 비즈니스 팀은 비즈니스 요구 사항 및 범위에 대 한 세부적인 통신을 용이 하 게 하는 비즈니스 중심 데이터 스키마를 작성 하는 데 비즈니스 영역 템플릿을 사용할 수 있습니다. 각 비즈니스 영역 템플릿은 해당 업계 enterprise 데이터베이스 템플릿에서 공통 된 엔터티 집합 으로부터 생성 되어 비즈니스 영역 템플릿에 다른 산업 모델과 일치 하는 공통 키, 특성 및 정의를 포함 합니다. 예를 들어 회계 & 재무 보고, 마케팅, 예산 & 예측은 소매, 은행 등 많은 산업에 대 한 비즈니스 영역 템플릿입니다. 

![비즈니스 영역 템플릿 예제](./media/concepts-database-templates/business-area-template-example.png)

## <a name="enterprise-templates"></a>Enterprise 템플릿 

Enterprise 데이터베이스 템플릿에는 특정 업계 내의 조직에 가장 관심을 가질 수 있는 테이블의 하위 집합이 포함 되어 있습니다. 개략적인 개요를 제공 하 고 관련 된 비즈니스 영역 템플릿 간의 연결을 설명 합니다. 이러한 템플릿은 많은 형식의 많은 프로젝트에 대 한 액셀러레이터 역할을 합니다. 예를 들어 뱅킹 템플릿에는 "은행" 이라는 엔터프라이즈 템플릿이 하나 있습니다. 

![Enterprise 템플릿 예제](./media/concepts-database-templates/enterprise-template-example.png)

## <a name="table"></a>테이블

테이블은 다른 개체와 구분할 수 있는 독립적인 존재를 가진 개체입니다. 예를 들어 Customer, Store, Channel 등이 있습니다.

## <a name="column"></a>열

각 테이블에는 일련의 열이 설명 되어 있습니다. 각 열에는 이름, 설명, 데이터 형식이 있으며 테이블과 연결 됩니다. 데이터베이스 템플릿에는 3만 개의 열이 있습니다. 예를 들어 CustomerId는 Customer 테이블의 열입니다.

## <a name="primary-key"></a>기본 키

기본 키는 전체 테이블을 고유 하 게 식별 하는 데 도움이 됩니다. 테이블에서 튜플을 고유 하 게 식별 해야 합니다. 예를 들어 CustomerId 열의 키는 테이블 고객의 각 고객을 식별 하기 위해 고유성이 적용 됩니다.

## <a name="foreign-key"></a>외래 키

외래 키는 열 또는 값이 다른 테이블의 기본 키와 일치 하는 열의 조합입니다. 두 테이블 간의 링크를 설정 하는 데 도움이 됩니다. 예를 들어 트랜잭션 테이블의 CustomerId는 트랜잭션을 완료 한 고객을 나타냅니다. 외래 키에는 항상 기본 키 (예: Customer 테이블의 CustomerId 기본 키)에 대 한 관계가 있습니다.

## <a name="composite-key"></a>복합 키

복합 키는 테이블을 고유 하 게 식별 하는 데 필요한 두 개 이상의 열로 구성 된 키입니다. 예를 들어 Order 테이블에서 OrderNumber와 ProductId는 모두 레코드를 고유 하 게 식별 하는 데 필요할 수 있습니다.

## <a name="relationships"></a>관계

관계는 두 테이블 간의 연결 또는 상호 작용입니다. 예를 들어 Customer 및 CustomerEmail 테이블은 서로 관련 되어 있습니다. 관계에는 두 개의 테이블이 포함 되어 있습니다. 부모 테이블과 자식 테이블은 일반적으로 외래 키로 연결 됩니다. 테이블에서 테이블로의 관계를 나타낼 수 있습니다.

## <a name="table-partitions"></a>테이블 파티션

Lake 데이터베이스를 사용 하면 테이블에 대 한 기본 데이터를 분할 하 여 성능을 향상 시킬 수 있습니다. 데이터베이스 편집기의 테이블 저장소 설정에서 파티션 구성을 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

아래 링크를 사용하여 데이터베이스 디자이너 기능을 계속 탐색합니다.
- [빠른 시작](quick-start-create-lake-database.md)
- [Lake 데이터베이스 개념](concepts-lake-database.md)
