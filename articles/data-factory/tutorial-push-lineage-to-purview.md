---
title: Data Factory 계보 데이터를 Azure Purview에 푸시
description: Data Factory 계보 데이터를 Azure Purview에 푸시하는 방법을 알아봅니다.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 08/10/2021
ms.openlocfilehash: ff6c53efea27bc2bb4e6273180aa64178d6a0295
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123424261"
---
# <a name="push-data-factory-lineage-data-to-azure-purview-preview"></a>Data Factory 계보 데이터를 Azure Purview에 푸시(미리 보기)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 자습서에서는 Data Factory UI(사용자 인터페이스)를 사용하여 작업을 실행하고 계보 데이터를 Azure Purview 계정에 보고하는 파이프라인을 만듭니다. 그러면, 모든 계보 정보를 Azure Purview 계정에서 볼 수 있습니다. 

현재 계보는 복사, Data Flow 및 SSIS 실행 작업에 대해 지원됩니다. [지원되는 Azure Data Factory 작업](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities)에서 지원되는 기능에 대한 자세한 내용을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

* **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure Data Factory**. Azure Data Factory가 없는 경우 [Azure Data Factory 만들기](./quickstart-create-data-factory-portal.md)를 참조하세요.
* **Azure  Purview 계정** Purview 계정은 Data Factory에서 생성된 모든 계보 데이터를 캡처합니다. Azure Purview 계정이 없는 경우 [Azure Purview 만들기](../purview/create-catalog-portal.md)를 참조하세요.

## <a name="run-pipeline-and-push-lineage-data-to-azure-purview"></a>파이프라인 실행 및 Azure Purview로 계보 데이터 푸시

### <a name="step-1-connect-data-factory-to-your-purview-account"></a>1단계: Purview 계정에 Data Factory 연결

[Azure Purview에 Data Factory 연결](connect-data-factory-to-azure-purview.md)의 단계에 따라 Data Factory와 Purview 계정 간의 연결을 설정할 수 있습니다.

### <a name="step-2-run-pipeline-in-data-factory"></a>2단계: Data Factory에서 파이프라인 실행

Data Factory에서 파이프라인, 복사 작업 및 데이터 흐름 작업을 만들 수 있습니다. 계보 데이터 캡처에 대한 추가 구성은 필요하지 않습니다. 계보 데이터는 작업 실행 중에 자동으로 캡처됩니다.

:::image type="content" source="./media/data-factory-purview/adf-activities-for-lineage.png" alt-text="복사 및 데이터 흐름 작업의 스크린샷":::

:::image type="content" source="./media/data-factory-purview/ssis-activities-for-lineage.png" alt-text="SSIS 패키지 실행 작업의 스크린샷":::

[Azure Data Factory를 사용하여 Azure Blob Storage에서 Azure SQL Database의 데이터베이스로 데이터 복사](./tutorial-copy-data-portal.md), [데이터 흐름 매핑을 사용하여 데이터 변환](./tutorial-data-flow.md) 및 [Azure에서 SSIS 패키지 실행](./tutorial-deploy-ssis-packages-azure.md)에서 복사, Data Flow 및 SSIS 실행 작업을 만드는 방법을 알아봅니다.

### <a name="step-3-monitor-lineage-reporting-status"></a>3단계: 계보 보고 상태 모니터링

파이프라인을 실행한 후 [파이프라인 모니터링 보기](monitor-visually.md#monitor-pipeline-runs)에서 다음 **계보 상태** 단추를 클릭하여 계보 보고 상태를 확인할 수 있습니다. 작업 출력 JSON -> `reportLineageToPurvew` 섹션에서도 동일한 정보를 사용할 수 있습니다.

:::image type="content" source="./media/data-factory-purview/monitor-lineage-reporting-status.png" alt-text="파이프라인 모니터링 보기에서 계보 보고 상태를 모니터링합니다.":::

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>4단계: Purview 계정에서 계보 정보 보기

Purview UI에서 자산을 찾아보고 "Azure Data Factory" 유형을 선택할 수 있습니다. 키워드를 사용하여 Data Catalog를 검색할 수도 있습니다.

:::image type="content" source="./media/data-factory-purview/view-dataset.png" alt-text="Purview에서 자산 검색 스크린샷" lightbox="./media/data-factory-purview/view-dataset.png":::

작업 자산에서 계보 탭을 클릭하면 모든 계보 정보를 볼 수 있습니다.

- 복사 작업:

    :::image type="content" source="./media/data-factory-purview/copy-lineage.png" alt-text="Purview의 복사 작업 계보 스크린샷" lightbox="./media/data-factory-purview/copy-lineage.png":::

- Data Flow 작업:

    :::image type="content" source="./media/data-factory-purview/dataflow-lineage.png" alt-text="Purview의 Data Flow 계보 스크린샷" lightbox="./media/data-factory-purview/dataflow-lineage.png":::

    > [!NOTE] 
    > 데이터 흐름 작업의 계보의 경우 원본 및 싱크만 지원됩니다. 데이터 흐름 변환의 계보는 아직 지원되지 않습니다.

- SSIS 패키지 실행 작업:

    :::image type="content" source="./media/data-factory-purview/ssis-lineage.png" alt-text="Purview에서 SSIS 계보 실행의 스크린샷" lightbox="./media/data-factory-purview/ssis-lineage.png":::

    > [!NOTE] 
    > SSIS 패키지 실행 작업의 계보의 경우 원본 및 대상만 지원됩니다. 변환의 계보는 아직 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

[카탈로그 계보 사용자 가이드](../purview/catalog-lineage-user-guide.md)

[Azure Purview에 Data Factory 연결](connect-data-factory-to-azure-purview.md)