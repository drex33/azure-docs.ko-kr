---
title: Azure Purview의 검색 기능 이해
description: 이 문서에서는 Azure Purview가 검색 기능을 통해 데이터 검색을 사용하도록 설정하는 방법을 설명합니다.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: 6bc9029a4b75206b5de99622729415b1aa36d4a2
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207529"
---
# <a name="understand-search-features-in-azure-purview"></a>Azure Purview의 검색 기능 이해

이 문서에서는 Azure Purview의 검색 환경에 대한 개요를 제공합니다. 검색은 Purview의 핵심 플랫폼 기능으로, 조직의 데이터 검색 및 데이터 사용 거버넌스 환경을 지원합니다.

## <a name="search"></a>검색

Purview 검색 환경은 관리형 검색 인덱스로 구동됩니다. Purview에 데이터 원본이 등록되면 검색 서비스에서 해당 메타데이터를 인덱싱하여 쉽게 검색할 수 있습니다. 인덱스는 검색 관련성 기능을 제공하고 수백만 개의 메타데이터 자산을 쿼리하여 검색 요청을 완료합니다. 검색을 통해 데이터를 검색, 이해, 사용하여 최대한의 가치를 얻을 수 있습니다.

Purview의 검색 환경은 3단계 프로세스입니다.

1. 검색 상자에는 최근에 사용한 키워드 및 자산이 포함된 기록이 표시됩니다.
1. 키 입력을 시작하면 검색에서 일치하는 키워드 및 자산을 제안합니다. 
1. 입력한 키워드와 일치하는 자산이 있는 검색 결과 페이지가 표시됩니다.

## <a name="reduce-the-time-to-discover-data"></a>데이터 검색 시간 단축

데이터 검색은 데이터 소비자를 위한 데이터 분석 또는 데이터 거버넌스 작업에 대한 첫 번째 단계입니다. 원하는 데이터의 위치를 알 수 없기 때문에 데이터 검색에는 시간이 많이 걸릴 수 있습니다. 데이터를 찾은 후에도 데이터를 신뢰하고 이에 대한 종속성을 적용해야 하는지 여부가 의심스러울 수 있습니다. 

Azure Purview에서 검색의 목표는 제스처 제공을 통해 데이터 검색 프로세스를 가속화하여 중요한 데이터를 신속하게 찾는 것입니다.

## <a name="recent-search-and-suggestions"></a>최근 검색 및 제안

여러 프로젝트에서 동시에 작업하는 경우가 많습니다. 이전 프로젝트를 더 쉽게 다시 시작할 수 있도록 Purview 검색은 최근 검색 키워드 및 제안을 볼 수 있는 기능을 제공합니다. 또한 검색 상자 드롭다운에서 **모두 보기** 를 선택하여 최근 검색 기록을 관리할 수 있습니다.

## <a name="filters"></a>필터

필터(*facets* 라고도 함)는 검색을 보완하도록 설계되었습니다. 필터는 검색 결과 페이지에 표시됩니다. 검색 결과 페이지의 필터에는 분류, 민감도 레이블, 데이터 원본, 소유자가 포함됩니다. 사용자는 필터에서 특정 값을 선택하여 일치하는 데이터 자산만 확인하고 일치하는 자산으로 검색 결과를 제한할 수 있습니다.

## <a name="hit-highlighting"></a>적중 항목 강조 표시

검색 결과 페이지에서 일치하는 키워드는 검색에서 데이터 자산이 반환된 이유를 쉽게 식별할 수 있도록 강조 표시됩니다. 키워드 일치는 데이터 자산 이름, 설명, 소유자 등의 여러 필드에서 발생할 수 있습니다.

적중 항목 강조 표시가 활성화되어 있더라도 데이터 자산이 검색에 포함된 이유가 항상 명확하지는 않을 수도 있습니다. 모든 속성은 기본값으로 검색됩니다. 따라서 열 수준 속성의 일치 때문에 데이터 자산이 반환될 수 있습니다. 또한 여러 사용자가 자신의 분류 및 설명을 사용하여 데이터 자산에 주석을 지정할 수 있으므로, 모든 메타데이터가 검색 결과 목록에 표시되지는 않습니다.

## <a name="sort"></a>정렬

사용자는 검색 결과를 정렬하는 두 가지 옵션을 사용할 수 있습니다. 자산 이름 또는 기본 검색 관련성을 기준으로 정렬할 수 있습니다.

## <a name="search-relevance"></a>검색 관련성

관련성은 검색 결과 페이지에서 기본 정렬 순서입니다. 검색 관련성은 검색 키워드(일부 또는 모두)를 포함하는 문서를 찾습니다. 검색 키워드의 많은 인스턴스를 포함하는 자산은 더 높은 순위를 갖습니다. 또한 사용자 지정 평가 메커니즘이 지속적으로 조정되어 검색 관련성이 향상됩니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure Portal에서 Azure Purview 계정 만들기](create-catalog-portal.md)
* [빠른 시작: Azure PowerShell/Azure CLI를 사용하여 Azure Purview 계정 만들기](create-catalog-powershell.md)
* [Purview Studio 사용](use-purview-studio.md)
