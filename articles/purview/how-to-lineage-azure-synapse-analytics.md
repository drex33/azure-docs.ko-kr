---
title: Azure Synapse Analytics의 메타데이터 및 계보
description: 이 문서에서는 Azure Synapse Analytics 및 Azure Purview를 연결하여 데이터 계보를 추적하는 방법을 설명합니다.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 02ea61356157c7e2a421c736a1af5bd87a1bcb5c
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212364"
---
# <a name="how-to-get-lineage-from-azure-synapse-analytics-into-azure-purview"></a>Azure Synapse Analytics에서 Azure Purview로 계보를 가져오는 방법

이 문서에서는 데이터 계보를 추적하기 위해 Azure Purview 계정에 Azure Synapse 작업 영역 계정을 연결하는 데 필요한 단계를 설명합니다. 또한 이 문서에서는 검사 범위 및 지원되는 계보 기능에 대한 세부 정보를 제공합니다.

## <a name="supported-azure-synapse-capabilities"></a>지원되는 Azure Synapse 기능

현재 Azure Purview는 다음과 같은 Azure Synapse 파이프라인 작업에서 런타임 계보를 캡처합니다.

- [데이터 복사](../data-factory/copy-activity-overview.md?context=/azure/synapse-analytics/context/context)
- [데이터 흐름](../data-factory/concepts-data-flow-overview.md?context=/azure/synapse-analytics/context/context)

> [!IMPORTANT]
> 원본 또는 대상이 지원되지 않는 데이터 스토리지 시스템을 사용하는 경우 Azure Purview가 계보를 삭제합니다.

[!INCLUDE[azure-synapse-supported-activity-lineage-capabilities](includes/data-factory-common-supported-capabilities.md)]

## <a name="access-secured-azure-purview-account"></a>액세스 보안 Azure 부서의 범위 계정
      
부서의 범위 계정이 방화벽으로 보호 되는 경우 Azure Synapse에서 부서의 범위 개인 끝점을 통해 [보안 부서의 범위 계정에 액세스](../synapse-analytics/catalog-and-governance/how-to-access-secured-purview-account.md) 하도록 허용 하는 방법을 알아봅니다.

## <a name="bring-azure-synapse-lineage-into-purview"></a>Azure Synapse 계보를 Purview로 가져오기

### <a name="step-1-connect-azure-synapse-workspace-to-your-purview-account"></a>1단계: Azure Synapse 작업 영역을 Purview 계정에 연결

Azure Synapse 작업 영역을 Purview에 연결할 수 있으며 연결을 통해 Azure Synapse가 계보 정보를 Purview에 푸시할 수 있습니다. [Azure Purview에 Synapse 작업 영역 연결](../synapse-analytics/catalog-and-governance/quickstart-connect-azure-purview.md)의 단계를 수행합니다. 여러 Azure Synapse 작업 영역은 전체적인 계보 추적을 위해 단일 Azure Purview 계정에 연결할 수 있습니다.

### <a name="step-2-run-pipeline-in-azure-synapse-workspace"></a>2단계: Azure Synapse 작업 영역에서 파이프라인 실행

Azure Synapse 작업 영역에서 복사 작업을 사용하여 파이프라인을 만들 수 있습니다. 계보 데이터 캡처에 대한 추가 구성은 필요하지 않습니다. 계보 데이터는 작업 실행 중에 자동으로 캡처됩니다.

### <a name="step-3-monitor-lineage-reporting-status"></a>3단계: 계보 보고 상태 모니터링

Azure Synapse 파이프라인을 실행 한 후에는 Synapse 파이프라인 모니터링 보기에서 다음 **계보 상태** 단추를 선택 하 여 계보 보고 상태를 확인할 수 있습니다. 작업 출력 JSON -> `reportLineageToPurvew` 섹션에서도 동일한 정보를 사용할 수 있습니다.

:::image type="content" source="../data-factory/media/data-factory-purview/monitor-lineage-reporting-status.png" alt-text="파이프라인 모니터링 보기에서 계보 보고 상태를 모니터링합니다.":::

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>4단계: Purview 계정에서 계보 정보 보기

Purview 계정에서 자산을 찾아보고 “Azure Synapse Analytics” 유형을 선택할 수 있습니다. 키워드를 사용하여 Data Catalog를 검색할 수도 있습니다.

:::image type="content" source="./media/how-to-lineage-azure-synapse-analytics/browse-azure-synapse-assets.png" alt-text="Purview에서 Azure Synapse 자산을 찾아봅니다."

Synapse 계정 -> 파이프라인 -> 작업을 선택하면 계보 정보를 볼 수 있습니다.

:::image type="content" source="./media/how-to-lineage-azure-synapse-analytics/browse-azure-synapse-pipeline-lineage.png" alt-text="Purview에서 Azure Synapse 파이프라인 계보를 찾아봅니다." lightbox="./media/how-to-lineage-azure-synapse-analytics/browse-azure-synapse-pipeline-lineage.png":::

## <a name="next-steps"></a>다음 단계

[카탈로그 계보 사용자 가이드](catalog-lineage-user-guide.md)

[계보에 대한 Azure Data Share 링크](how-to-link-azure-data-share.md)
