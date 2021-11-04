---
title: 업계 AI 솔루션
description: Azure Synapse Analytics의 업계 AI 솔루션
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: garye
ms.date: 11/02/2021
author: nelgson
ms.author: negust
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0a4476b1324938acd2c753368f26bc7aafbed62b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053261"
---
# <a name="industry-ai-solutions"></a>업계 AI 솔루션

모든 업계에는 해결해야 하는 일반적인 업계별 문제가 있습니다. 이 문서에서는 일반적인 업계 문제를 신속하게 해결하기 위해 Azure Synapse 작업 영역에서 사용할 수 있는 업계 솔루션을 다룹니다. 현재 제공되는 AI 솔루션은 제품 추천을 위한 소매 솔루션입니다. 이 솔루션을 소매점에서 사용하는 방법에 대한 자세한 내용은 아래에서 확인할 수 있습니다.

## <a name="target-user"></a>대상 사용자

권장 사항 솔루션은 코드 및 기계 학습 개념에 익숙한 기존 데이터 과학자 및 새로운 데이터 과학자를 대상으로 합니다. 이 솔루션은 소매 도메인의 특정 문제를 해결하기 위해 이러한 사용자의 생산성을 가속화하기 위한 것입니다.

## <a name="retail-product-recommendation-solution"></a>소매 제품 권장 사항 솔루션

**소매 - 제품 권장 사항** 솔루션은 Synapse에서 바로 개발하기 위한 강력하고 확장 가능한 추천 엔진을 제공합니다. 이 솔루션을 사용하는 경우 제품 권장 사항에 대한 기계 학습 모델을 학습할 수 있는 Notebook을 받게 됩니다.

고유한 비즈니스 요구 사항을 충족하도록 미리 구성된 Notebook을 사용자 지정해야 할 수 있습니다.

이 소매 권장 사항 솔루션은 두 가지 모드로 배포할 수 있습니다. 샘플 데이터로 사용해 보거나 Synapse의 소매용 데이터베이스 템플릿을 사용하여 모델링된 데이터베이스를 사용할 수 있습니다.

**소매 - 제품 권장 사항** 솔루션은 콘텐츠 기반 필터링 권장 사항에 대한 권장 사항 파이프라인을 제공합니다. 콘텐츠 기반 필터링 파이프라인은 LightGBM 알고리즘을 사용하여 사용자 및 항목 기능을 기반으로 사용자 기본 설정을 예측하는 모델을 학습합니다. 이러한 기능은 사용자 프로필 및 항목 프로필과 같은 정적 기능뿐만 아니라 집계된 사용자 동작 패턴과 같은 동적 기능이 될 수 있습니다. 콘텐츠 기반 필터링 형식 권장 사항 시스템은 종종 "개인 맞춤형 권장 사항" 또는 "원하는 새 제품"과 같은 권장 사항에 사용됩니다.

## <a name="get-started"></a>시작

1. Synapse 작업 영역을 엽니다.
1. 홈 화면의 **자세히 검색** 섹션에서 **지식 센터** 를 선택합니다.
1. 지식 센터에서 **갤러리 찾아보기** 를 선택합니다.
1. 갤러리에서 **데이터베이스 템플릿** 탭을 선택하고, **AI 솔루션** 섹션까지 아래로 스크롤한 후, "소매 - 제품 권장 사항" 솔루션을 선택합니다. **계속** 을 클릭합니다.
1. 다음 두 옵션 중 하나를 선택합니다.
   * “샘플 데이터 사용” 
   * “작업 영역 데이터베이스에서 내 데이터 사용” 예를 들어 이 옵션은 소매 데이터베이스 템플릿으로 모델링된 데이터베이스일 수 있습니다.
   
    **배포** 를 클릭하면 Synapse 작업 영역에서 Notebook이 열립니다.

1. 이제 작업 영역에서 Notebook이 열립니다. 이 Notebook을 Spark 풀에 연결하고 탐색을 시작할 수 있습니다. 이 Notebook은 특정 요구 사항에 맞게 사용자 지정됩니다.

> [!NOTE]
> 사용자 고유의 데이터베이스를 선택하는 경우 사용자 고유의 테이블 및 열 이름을 사용하도록 Notebook을 사용자 지정해야 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Synapse Analytics 시작](../get-started.md)
* [작업 영역 만들기](../get-started-create-workspace.md)
