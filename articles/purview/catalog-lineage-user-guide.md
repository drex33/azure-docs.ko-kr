---
title: Data Catalog 계보 사용자 가이드
description: 이 문서에서는 Azure Purview의 카탈로그 계보 기능에 대한 개요를 제공합니다.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: e2923e29652a92de188366168140bf9def43c082
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209471"
---
# <a name="azure-purview-data-catalog-lineage-user-guide"></a>Azure Purview Data Catalog 계보 사용자 가이드

이 문서에서는 Azure Purview Data Catalog의 데이터 계보 기능에 대한 개요를 제공합니다.

## <a name="background"></a>배경

Azure Purview의 플랫폼 기능 중 하나는 데이터 프로세스에서 만든 데이터 세트 간의 계보를 표시하는 기능입니다. Data Factory, Data Share, Power BI와 같은 시스템은 이동하면서 계보를 캡처합니다. 사용자 지정 계보 보고는 Atlas 후크 및 REST API를 통해서도 지원됩니다.

## <a name="lineage-collection"></a>계보 컬렉션 
 엔터프라이즈 데이터 시스템에서 Azure Purview에 수집된 메타데이터는 엔드투엔드 데이터 계보를 표시하도록 전체적으로 붙여집니다. Purview로 계보를 수집하는 데이터 시스템은 다음과 같은 세 가지 유형으로 광범위하게 분류됩니다.

### <a name="data-processing-system"></a>데이터 처리 시스템
데이터 통합 및 ETL 도구는 실행 시 Azure Purview에 계보를 푸시할 수 있습니다. Data Factory, Data Share, Synapse, Azure Databricks 등의 도구는 이 데이터 시스템 범주에 속합니다. 데이터 처리 시스템은 다른 데이터베이스 및 스토리지 솔루션의 원본으로 데이터 세트를 참조하여 대상 데이터 세트를 만듭니다. 현재 계보에 대해 Purview와 통합된 데이터 처리 시스템의 목록은 아래 표에 나와 있습니다.

| 데이터 처리 시스템 | 지원되는 범위 |
| ---------------------- | ------------|
| Azure Data Factory | [복사 작업](how-to-link-azure-data-factory.md#copy-activity-support) <br> [데이터 흐름 작업](how-to-link-azure-data-factory.md#data-flow-support) <br> [SSIS 패키지 작업 실행](how-to-link-azure-data-factory.md#execute-ssis-package-support) |
| Azure Synapse Analytics | [복사 작업](how-to-lineage-azure-synapse-analytics.md#copy-activity-support) <br> [데이터 흐름 작업](how-to-lineage-azure-synapse-analytics.md#data-flow-support) |
| Azure Data Share | [스냅샷 공유](how-to-link-azure-data-share.md) |
 
### <a name="data-storage-systems"></a>데이터 스토리지 시스템
SQL Server, Teradata, SAP 등의 데이터베이스 및 스토리지 솔루션에는 스크립팅 언어를 사용하여 데이터를 변환하는 쿼리 엔진이 있습니다. 저장 프로시저의 데이터 계보는 Purview로 수집되고 다른 시스템의 계보와 붙여집니다.

| 데이터 스토리지 시스템 | 지원되는 범위 |
| ---------------------- | ------------|
| Teradata | 저장 프로시저

### <a name="data-analytics--reporting-systems"></a>데이터 분석 및 보고 시스템
Azure ML 및 Power BI와 같은 데이터 시스템은 Azure Purview로 계보를 보고합니다. 이러한 시스템은 스토리지 시스템의 데이터 세트를 사용하고 메타 모델을 통해 처리하여 BI 대시보드, ML 실험 등을 만듭니다.

| 데이터 분석 및 보고 시스템 | 지원되는 범위 |
| ---------------------- | ------------|
| Power BI | [데이터 세트, 데이터 흐름, 보고서 및 대시보드](register-scan-power-bi-tenant.md)

## <a name="get-started-with-lineage"></a>계보 시작하기

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWxTAK]

Purview의 계보에는 데이터 세트 및 프로세스가 포함됩니다. 데이터 세트는 노드라고도 하며, 프로세스를 에지라고도 합니다.

* **데이터 세트(노드)** : 프로세스에 대한 입력으로 제공되는 데이터 세트(구조적 또는 비구조적)입니다. 예를 들어 SQL Table, Azure blob, 파일(예: .csv 및 .xml)은 모두 데이터 세트로 간주됩니다. Purview의 계보 섹션에서 데이터 세트는 사각형 상자로 표시됩니다.

* **프로세스(에지)** : 데이터 세트에 대해 수행되는 작업 또는 변환을 프로세스라고 합니다. 예를 들어 ADF 복사 작업, Data Share 스냅샷 등이 있습니다. Purview의 계보 섹션에서 프로세스는 모서리가 둥근 상자로 표시됩니다.

Purview의 자산에 대한 계보 정보에 액세스하려면 다음 단계를 수행합니다.

1. Azure Portal에서 [Azure Purview 계정 페이지로](https://aka.ms/purviewportal)이동합니다.

1. 목록에서 Azure Purview 계정을 선택하고 **개요** 페이지에서 **Purview 계정 시작** 을 선택합니다.

1. Azure Purview의 **홈** 페이지에서 데이터 세트 이름 또는 프로세스 이름 (예: ADF 복사 또는 데이터 흐름 작업)을 검색합니다. 그 다음 Enter 키를 누릅니다.

1. 검색 결과에서 자산을 선택하고 해당 **계보** 탭을 선택합니다.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-lineage-from-asset.png" alt-text="계보 탭을 선택하는 방법을 보여주는 스크린샷" border="true":::

## <a name="asset-level-lineage"></a>자산 수준 계보

Azure Purview는 데이터 세트 및 프로세스에 대한 자산 수준 계보를 지원합니다. 자산 수준 계보를 보려면 카탈로그에서 현재 자산의 **계보** 탭으로 이동합니다. 현재 데이터 세트 자산 노드를 선택합니다. 기본적으로 데이터에 속하는 열 목록은 왼쪽 창에 표시됩니다.

   :::image type="content" source="./media/catalog-lineage-user-guide/view-columns-from-lineage.png" alt-text="계보 페이지에서 열 보기를 선택하는 방법을 보여주는 스크린샷" border="true":::

## <a name="dataset-column-lineage"></a>데이터 세트 열 계보

데이터 세트의 열 수준 계보를 보려면 카탈로그에서 현재 자산의 **계보** 탭으로 이동하고 아래 단계를 수행합니다.

1. 계보 탭의 왼쪽 창에서 데이터 계보에 표시하려는 각 열 옆의 확인란을 선택합니다.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png" alt-text="계보 페이지에서 표시할 열을 선택하는 방법을 보여주는 스크린샷" lightbox="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png":::

2. 왼쪽 창이나 계보 캔버스의 데이터 세트에서 선택한 열을 마우스로 가리키면 열 매핑이 표시됩니다. 모든 열 인스턴스가 강조 표시됩니다.

   :::image type="content" source="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png" alt-text="열 이름을 마우스로 가리켜서 데이터 계보 경로에서 열 흐름을 강조 표시하는 방법을 보여주는 스크린샷" lightbox="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png":::

3. 열 수가 왼쪽 창에 표시할 수 있는 크기 보다 큰 경우, 필터 옵션을 사용하여 이름별로 특정 열을 선택합니다. 또는 마우스를 사용하여 목록을 스크롤할 수 있습니다.

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-columns-by-name.png" alt-text="계보 페이지에서 열 이름별로 열을 필터링하는 방법을 보여주는 스크린샷" lightbox="./media/catalog-lineage-user-guide/filter-columns-by-name.png":::

4. 계보 캔버스에 더 많은 노드와 에지가 포함된 경우, 필터를 사용하여 데이터 자산을 선택하거나 이름별로 노드를 처리합니다. 또는 마우스를 사용하여 계보 창 주위로 이동할 수 있습니다.

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-assets-by-name.png" alt-text="계보 페이지에서 이름별로 데이터 자산 노드를 보여주는 스크린샷" lightbox="./media/catalog-lineage-user-guide/filter-assets-by-name.png":::

5. 왼쪽 창의 토글을 사용하여 계보 캔버스의 데이터 세트 목록을 강조 표시합니다. 토글을 해제하면 선택한 열 중 하나 이상이 포함된 자산이 모두 표시됩니다. 토글을 설정하면 모든 열을 포함하는 데이터 세트만 표시됩니다.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png" alt-text="토글을 사용하여 계보 페이지의 노드 목록을 필터링하는 방법을 보여주는 스크린샷" lightbox="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png":::

## <a name="process-column-lineage"></a>열 계보 처리
데이터 프로세스는 하나 이상의 입력 데이터 세트를 사용하여 하나 이상의 출력을 생성할 수 있습니다. Purview에서는 프로세스 노드에 대해 열 수준 계보를 사용할 수 있습니다. 
1. 입력 및 출력 데이터 세트를 열 패널의 드롭다운에서 전환합니다.
2. 하나 이상의 표에서 열을 선택하여 입력 데이터 세트에서 해당 출력 데이터 세트로 흐르는 계보를 확인합니다.

   :::image type="content" source="./media/catalog-lineage-user-guide/process-column-lineage.png" alt-text="프로세스 노드의 열 계보를 보여주는 스크린샷" lightbox="./media/catalog-lineage-user-guide/process-column-lineage.png":::

## <a name="browse-assets-in-lineage"></a>계보에서 자산 찾아보기
1. 모든 자산에 대해 **자산으로 전환** 을 선택하여 계보 보기에서 해당하는 메타데이터를 봅니다. 이는 계보 보기에서 카탈로그의 다른 자산을 검색하는 효과적인 방법입니다.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-switch-to-asset.png" alt-text="계보 데이터 자산에서 자산으로 전환을 선택하는 방법의 스크린샷" lightbox="./media/catalog-lineage-user-guide/select-switch-to-asset.png":::

2. 계보 캔버스는 인기 있는 데이터 세트에 대해 복잡해질 수 있습니다. 혼란을 피하기 위해 기본 보기에는 자산에 대한 5가지 수준의 계보만 포커스가 표시됩니다. 계보 캔버스에서 거품을 선택 하 여 나머지 계보를 확장할 수 있습니다. 데이터 소비자는 캔버스에서 관심이 없는 자산을 숨길 수도 있습니다. 더 간단하게 줄이려면 계보 캔버스의 위쪽에서 **추가 계보** 토글을 해제합니다. 이 작업을 수행하면 계보 캔버스의 모든 메시지 풍선이 숨겨집니다.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png" alt-text="더 많은 계보를 토글로 설정/해제하는 방법을 보여주는 스크린샷" lightbox="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png":::

3. 계보 캔버스의 스마트 단추를 사용하여 계보의 최적 보기를 가져옵니다. 카탈로그의 몰입형 계보 환경에서 자동 레이아웃, 크기에 맞게, 확대/축소, 전체 화면, 탐색 맵을 사용할 수 있습니다.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png" alt-text="계보 스마트 단추 선택 방법을 보여주는 스크린샷" lightbox="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png":::

## <a name="next-steps"></a>다음 단계

* [계보에 대한 Azure Data Factory 링크](how-to-link-azure-data-factory.md)
* [계보에 대한 Azure Data Share 링크](how-to-link-azure-data-share.md)