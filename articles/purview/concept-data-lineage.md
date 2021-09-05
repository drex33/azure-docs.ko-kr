---
title: Azure Purview의 데이터 계보(미리 보기)
description: 데이터 계보의 개념을 설명합니다.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: da2f34014f3c789762cc2743b971aa76ad1049a9
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112574437"
---
# <a name="data-lineage-in-azure-purview-data-catalog-client"></a>Azure Purview Data Catalog 클라이언트의 데이터 계보

이 문서에서는 Azure Purview Data Catalog의 데이터 계보 개요를 제공합니다. 또한 데이터 시스템을 카탈로그와 통합하여 데이터 계보를 캡처하는 방법을 자세히 설명합니다. Purview는 조직 데이터 자산의 여러 부분에 있는 데이터에 대한 계보를 다음을 포함한 여러 준비 수준에서 캡처할 수 있습니다.

- 다양한 플랫폼에서 스테이징된 완전한 원시 데이터
- 변환 및 준비된 데이터
- 시각화 플랫폼에서 사용하는 데이터

## <a name="use-cases"></a>사용 사례

데이터 계보는 데이터의 원본에 걸친 수명 주기, 그리고 시간이 지남에 따라 데이터 자산에서 이동하는 것으로 이해됩니다. 문제 해결, 데이터 파이프라인의 근본 원인 추적, 디버깅 등의 여러 가지 이전 버전 확인이 필요한 시나리오에 사용됩니다. 계보는 데이터 품질 분석, 규정 준수 및 영향 분석이라고도 하는 "가상" 시나리오에도 사용됩니다. 계보는 데이터가 변환된 방법을 포함하여 원본에서 대상으로 이동하는 데이터를 표시하기 위해 시각적으로 표현됩니다. 대부분 엔터프라이즈 데이터 환경의 복잡성을 고려할 때 주변 데이터 요소를 통합하거나 마스킹하지 않고는 이해하기 어려울 수 있습니다.

## <a name="lineage-experience-in-azure-purview-data-catalog"></a>Azure Purview Data Catalog의 계보 환경

Purview Data Catalog는 다른 데이터 처리, 스토리지 및 분석 시스템과 연결하여 계보 정보를 추출합니다. 이 정보는 Catalog에서 일반적인 시나리오 관련 계보 환경을 나타내도록 결합됩니다.

:::image type="content" source="media/concept-lineage/lineage-end-end.png" alt-text="BLOB 스토어에서 Power BI 대시보드로 복사되는 데이터를 보여주는 엔드투엔드 계보":::

데이터 자산에 데이터 추출, 변환(ETL/ELT 시스템), 분석 및 시각화 시스템을 수행하는 시스템이 포함될 수 있습니다. 각 시스템은 시스템 경계 내에 있는 데이터의 상태와 품질을 설명하는 다양한 정적 및 작업 메타데이터를 캡처합니다. 데이터 카탈로그의 계보 목표는 가능한 가장 세부적인 수준에서 각 데이터 시스템의 이동, 변환 및 작업 메타데이터를 추출하는 것입니다.

다음 예제는 여러 시스템에서 데이터를 이동하는 일반적인 사용 사례로, Data Catalog는 계보를 위해 각 시스템을 연결합니다.

- Data Factory는 온-프레미스/원시 영역에서 클라우드의 랜딩 존으로 데이터를 복사합니다. 
- Synapse, Databricks 등의 데이터 처리 시스템은 Notebook을 사용하여 랜딩 존에서 큐레이팅된 영역으로 데이터를 처리 및 변환합니다.
- 최적의 쿼리 성능 및 집계를 위해 분석 모델로 데이터를 추가로 처리합니다. 
- 데이터 시각화 시스템은 메타 모델을 통해 데이터 세트와 프로세스를 사용하여 BI 대시보드, ML 실험 등을 만듭니다.

## <a name="lineage-granularity"></a>계보 세분성

다음 섹션에서는 Purview로 수집되는 계보 정보의 세분성에 대한 세부 정보를 다룹니다. 이 세분성은 Purview에서 지원되는 데이터 시스템에 따라 다를 수 있습니다.

### <a name="entity-level-lineage-sources--process--targets"></a>엔터티 수준 계보: 원본 > 프로세스> 대상 

- 계보는 그래프로 표현되고, 일반적으로 컴퓨팅 시스템에서 호출하는 프로세스에 의해 연결되는 데이터 스토리지 시스템의 원본 및 대상 엔터티를 포함합니다. 
- 데이터 시스템은 데이터 카탈로그에 연결하여 기본 데이터 시스템(예: SQL 저장 프로시저, Notebook 등)의 물리적 개체를 참조하는 고유한 개체를 생성 및 보고합니다.
- 소유권 같은 추가 메타데이터가 포함된 충실도가 높은 계보는 원본 및 대상 엔터티에 대해 인간이 읽을 수 있는 형식으로 계보를 표시하도록 캡처됩니다. 예: 파티션 또는 파일 수준이 아닌 하이브 테이블 수준의 계보.

### <a name="column-or-attribute-level-lineage"></a>열 또는 특성 수준 계보

대상 엔터티에서 특성을 만들거나 파생하는 데 사용되는 소스 엔터티의 특성을 식별합니다. 원본 특성의 이름을 대상에서 유지하거나 이름을 바꿀 수 있습니다. ADF와 같은 시스템은 온-프레미스 환경에서 클라우드로 일 대 일 복사를 수행할 수 있습니다. 예: `Table1/ColumnA -> Table2/ColumnA`.

### <a name="process-execution-status"></a>프로세스 실행 상태

근본 원인 분석 및 데이터 품질 시나리오를 지원하기 위해 데이터 처리 시스템의 작업 실행 상태를 캡처합니다. 이 요구 사항은 다른 데이터 처리 시스템의 모니터링 기능 대체와 관련이 없으며, 그 목표 또한 이를 대체하는 것이 아닙니다. 

## <a name="summary"></a>요약

계보는 품질, 신뢰 및 감사 시나리오를 지원하기 위한 Purview Data Catalog의 중요한 기능입니다. 데이터 카탈로그의 목표는 환경 내의 모든 데이터 시스템이 자연스럽게 연결하고 계보를 보고할 수 있는 강력한 프레임워크를 빌드하는 것입니다. 메타데이터가 제공되면 데이터 카탈로그는 데이터 시스템에서 제공하는 메타데이터를 함께 사용하여 데이터 거버넌스 사용 사례를 지원할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure Portal에서 Azure Purview 계정 만들기](create-catalog-portal.md)
* [빠른 시작: Azure PowerShell/Azure CLI를 사용하여 Azure Purview 계정 만들기](create-catalog-powershell.md)
* [빠른 시작: Purview Studio 사용](use-purview-studio.md)
