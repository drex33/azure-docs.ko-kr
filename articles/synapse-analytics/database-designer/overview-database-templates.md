---
title: Azure Synapse 데이터베이스 템플릿 개요
description: 데이터베이스 템플릿에 대한 자세한 정보
author: gsaurer
ms.author: gesaur
ms.service: synapse-analytics
ms.topic: overview
ms.date: 11/02/2021
ms.custom: template-overview, ignite-fall-2021
ms.openlocfilehash: 121e1a105bc2d2d01d4069b8a791443de25a8dc3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029470"
---
# <a name="what-is-azure-synapse-database-templates"></a>Azure Synapse 데이터베이스 템플릿의 개념

비즈니스 문제를 해결하기 위한 목적으로 데이터가 원본 시스템에서 데이터 웨어하우스 및 데이터 마트로 이동될 때에는 많은 양식을 사용합니다. 데이터베이스 템플릿은 데이터를 인사이트로 변환하는 데 도움이 될 수 있습니다. 데이터베이스 템플릿은 특정 업계의 요구 사항을 충족 하도록 미리 디자인된 비즈니스 및 기술 데이터 정의 집합입니다. 이는 모범 사례, 정부 규정, 복잡한 데이터 및 업계별 조직의 분석 요구 사항에서 파생된 공통 요소를 제공하는 청사진 역할을 합니다. 

이러한 정보 청사진은 조직에서 데이터 거버넌스, 보고, 비즈니스 인텔리전스 및 고급 분석을 위한 데이터 솔루션을 계획, 설계 및 디자인하는 데 사용할 수 있습니다. 데이터 모델은 입증된 업계 데이터 아키텍처를 적시에 예측 가능한 방식으로 구현할 수 있는 통합된 비즈니스 차원의 정보 아키텍처를 제공합니다. 

예를 들어 소매 고객에 대한 제품 권장 사항 솔루션을 빌드하는 경우 고객이 구매한 대상 및 구매로 이어진 트랜잭션을 이해하기 위해서는 기본적인 청사진이 필요합니다. 구매한 스토어에 대한 정보도 필요할 수 있습니다. 고객이 전용 프로그램의 일부인지도 파악해야 합니다. 이 사용 사례를 달성하기 위해서는 다음의 스키마가 필요합니다. 

 - 제품 
 - 트랜잭션 
 - TransactionLineItem 
 - Customer 
 - CustomerLoyalty 
 - 스토어 

이 사용 사례는 소매 데이터베이스 템플릿에서 6개의 테이블을 선택하여 설정할 수 있습니다. 

[[retail-database-template-example.png|alt=소매 데이터베이스 템플릿 예제]]

일반적인 데이터베이스 템플릿은 특정 업계의 핵심 요구 사항을 해결하며 다음과 같이 구성됩니다. 

 - 지원되는 [비즈니스 영역 템플릿](concepts-database-templates.md#business-area-templates) 집합
 - 하나 이상의 [엔터프라이즈 템플릿](concepts-database-templates.md#enterprise-templates)  

## <a name="available-database-templates"></a>사용 가능한 데이터베이스 템플릿 

현재 Azure Synapse Studio 내에서 사용자가 lake 데이터베이스 만들기를 시작하는 데 사용할 수 있는 6개의 데이터베이스 템플릿이 있습니다. 

 - **뱅킹** - 뱅킹 데이터를 분석하는 회사용
 - **소비자 상품** - 소비자가 구매하여 사용하는 상품의 제조업체 또는 생산자
 - **펀드 관리** - 투자자의 투자 자금을 관리하는 회사용
 - **생명 보험** - 생명 보험을 제공하거나 연금을 판매하거나 또는 둘 다 수행하는 회사용
 - **상해 보험** - 재산에 대한 위험 및 다양한 형태의 책임 보장에 대한 보험을 제공하는 회사용
 - **소매** - 여러 채널을 통해 고객에게 소비자 상품 또는 서비스를 제공하는 판매자용

배출 및 탄소 관리는 모든 업계에서 중요한 논의 사항이므로 사용 가능한 모든 데이터베이스 템플릿에 이러한 구성 요소를 포함시켰습니다. 이러한 구성 요소를 통해 직접 및 간접 greenhouse 가스 방출을 추적하고 보고해야 하는 회사의 작업이 쉬워집니다.

## <a name="next-steps"></a>다음 단계
아래 링크를 사용하여 데이터베이스 디자이너 기능을 계속 탐색합니다.
- [데이터베이스 템플릿 개념](concepts-database-templates.md)
- [빠른 시작](quick-start-create-lake-database.md)
