---
title: Power BI를 사용하여 로그 및 메트릭 시각화
description: Power BI를 사용하여 Azure Cognitive Search 로그 및 메트릭을 시각화합니다.
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 57dd31643560b54f6f54b8352966a4c05cb6a35b
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892896"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>Power BI를 사용하여 Azure Cognitive Search 로그 및 메트릭 시각화

[Azure Cognitive Search](./search-what-is-azure-search.md)는 작업 로그 및 서비스 메트릭을 Azure Storage 계정으로 보낼 수 있습니다. 그러면 Power BI에서 시각화할 수 있습니다. 이 문서에서는 Power BI 템플릿 앱을 사용하여 데이터를 시각화하는 단계와 방법을 설명합니다. 템플릿을 사용하면 쿼리, 인덱싱, 작업 및 서비스 메트릭에 대한 정보를 포함하여 검색 서비스에 대한 자세한 인사이트를 얻을 수 있습니다.

Power BI Template App **Azure Cognitive Search에서 다음을 찾을 수 있습니다. 이를 위해 [Power BI Apps 마켓플레이스](https://appsource.microsoft.com/marketplace/apps)에서 로그 및 메트릭** 을 분석합니다.

## <a name="set-up-the-app"></a>앱 설정

1. 검색 서비스에 대한 메트릭 및 리소스 로깅을 사용하도록 설정합니다.

    1. 로그를 보관할 수 있는 기존 [Azure Storage 계정을](../storage/common/storage-account-create.md) 만들거나 확인합니다
    1. Azure Portal에서 Azure Cognitive Search Service로 이동합니다
    1. 왼쪽 열의 모니터링 섹션에서 **진단 설정** 을 선택합니다

        :::image type="content" source="media/search-monitor-logs-powerbi/diagnostic-settings.png" alt-text="Azure Cognitive Search 서비스의 모니터링 섹션에서 진단 설정을 선택하는 방법을 보여 주는 스크린샷." border="false":::

    1. **+ 진단 설정 추가** 를 선택합니다
    1. **스토리지 계정에 보관** 을 선택하고, 스토리지 계정 정보를 제공하고, **Operationlogs** 및 **AllMetrics** 를 확인합니다.

        :::image type="content" source="media/search-monitor-logs-powerbi/add-diagnostic-setting.png" alt-text="진단 설정 페이지에서 메트릭 및 리소스 로깅을 선택하는 방법을 보여 주는 스크린샷":::
    1. **저장** 을 선택합니다.

1. 로깅을 사용하도록 설정한 후에는 검색 서비스를 사용하여 로그 및 메트릭 생성을 시작합니다. 컨테이너가 이러한 로그를 사용하여 Blob 스토리지에 표시되기까지 최대한 시간이 소요됩니다. 검색 트래픽 로그에 대한 **insights-logs-operationlogs** 컨테이너와 메트릭에 대한 **insights-metrics-pt1m** 컨테이너가 표시됩니다.

1. [Power BI Apps 마켓플레이스](https://appsource.microsoft.com/marketplace/apps)에서 Azure Cognitive Search Power BI 앱을 찾아 새 작업 영역 또는 기존 작업 영역에 설치합니다. 이 앱을 **Azure Cognitive Search: 로그 및 메트릭 분석** 이라고 합니다.

1. 앱을 설치한 후 Power BI의 앱 목록에서 앱을 선택합니다.

    :::image type="content" source="media/search-monitor-logs-powerbi/azure-search-app-tile.png" alt-text="앱 목록에서 선택할 Azure Cognitive Search 앱을 보여 주는 스크린샷":::

1. **연결** 을 선택하여 데이터에 연결합니다.

    :::image type="content" source="media/search-monitor-logs-powerbi/get-started-with-your-new-app.png" alt-text="Azure Cognitive Search 앱에서 데이터에 연결하는 방법을 보여 주는 스크린샷":::

1. 로그 및 메트릭이 포함된 스토리지 계정의 이름을 입력합니다. 기본적으로 앱은 지난 10 일간의 데이터를 확인하지만 이 값은 **days** 매개 변수를 사용하여 변경할 수 있습니다.

    :::image type="content" source="media/search-monitor-logs-powerbi/connect-to-storage-account.png" alt-text="Azure Cognitive Search에 연결 페이지에서 스토리지 계정 이름 및 쿼리할 일 수를 입력하는 방법을 보여주는 스크린샷":::

1. 인증 방법으로 **키** 를 선택하고 스토리지 계정 키를 제공합니다. 개인 정보 수준으로 **프라이빗** 을 선택합니다. 로그인 을 클릭하여 로드 프로세스를 시작합니다.

    :::image type="content" source="media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png" alt-text="인증 방법, 계정 키 및 Azure Cognitive Search 연결에서의 개인 정보 수준을 입력하는 방법을 보여 주는 스크린샷":::

1. 데이터를 새로 고칠 때까지 기다립니다. 보유한 데이터 양에 따라 다소 시간이 걸릴 수 있습니다. 아래 표시기를 기준으로 데이터를 아직 새로 고치는 중인지 확인할 수 있습니다.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-refreshing.png" alt-text="데이터 새로 고침 페이지에서 정보를 읽는 방법을 보여 주는 스크린샷":::

1. 데이터 새로 고침이 완료되면 **Azure Cognitive Search 보고서** 를 선택하여 보고서를 봅니다.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-report.png" alt-text="데이터 새로 고침 페이지에서 Azure Cognitive Search 보고서를 선택하는 방법을 보여 주는 스크린샷":::

1. 보고서를 연 후 데이터와 함께 열리도록 페이지를 새로 고쳐야 합니다.

    :::image type="content" source="media/search-monitor-logs-powerbi/powerbi-search.png" alt-text="Azure Cognitive Search Power BI 보고서의 스크린샷":::

## <a name="modify-app-parameters"></a>앱 매개 변수 수정

다른 스토리지 계정에서 데이터를 시각화하거나 쿼리할 데이터의 일 수를 변경하려는 경우 아래 단계에 따라 **Days** 및 **StorageAccount** 매개 변수를 변경합니다.

1. Power BI apps로 이동하여 Azure Cognitive Search 앱을 찾고 **앱 편집** 단추를 선택하여 작업 영역을 확인합니다.

    :::image type="content" source="media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png" alt-text="Azure Cognitive Search 앱에 대한 앱 편집 단추를 선택하는 방법을 보여 주는 스크린샷":::

1. 데이터 세트 옵션에서 **설정** 을 선택합니다.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-settings.png" alt-text="Azure Cognitive Search 데이터 세트 옵션에서 설정을 선택하는 방법을 보여 주는 스크린샷":::

1. 데이터 집합 탭에서 매개 변수 값을 변경하고 **적용** 을 선택합니다. 연결에 문제가 있는 경우 동일한 페이지에서 데이터 원본 자격 증명을 업데이트 합니다.

1. 작업 영역으로 다시 이동하고 데이터 세트 옵션에서 **지금 새로 고침** 을 선택합니다.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png" alt-text="Azure Cognitive Search 데이터 세트 옵션에서 새로 고침을 선택하는 방법을 보여 주는 스크린샷":::

1. 업데이트된 데이터를 보려면 보고서를 엽니다. 최신 데이터를 보려면 보고서를 새로 고쳐야 할 수도 있습니다.

## <a name="troubleshooting-report-issues"></a>보고서 문제 해결

데이터를 볼 수 없는 경우 다음 문제 해결 단계를 수행합니다.

1. 보고서를 열고 페이지를 새로 고쳐 최신 데이터를 볼 수 있는지 확인합니다. 보고서에는 데이터를 새로 고치는 옵션이 있습니다. 최신 데이터를 가져오려면 이 옵션을 선택합니다.

1. 제공한 스토리지 계정 이름 및 액세스 키가 올바른지 확인합니다. 스토리지 계정 이름은 검색 서비스 로그로 구성된 계정과 일치해야 합니다.

1. 스토리지 계정에 컨테이너 **insights-logs-operationlogs** 및 **insights-metrics-pt1m** 과 각 컨테이너의 데이터가 포함되어 있는지 확인합니다. 로그 및 메트릭은 폴더의 여러 레이어에 포함됩니다.

1. 데이터 세트를 계속 새로 고치는 중인지 보려면 확인합니다. 새로 고침 상태 표시기는 위의 8단계에서 표시됩니다. 계속 새로 고치는 중인 경우 새로 고침이 완료될 때까지 기다렸다가 보고서를 열고 새로 고칩니다.

## <a name="next-steps"></a>다음 단계

+ [검색 작업 및 작업 모니터링](search-monitor-usage.md)
+ [Power BI란?](/power-bi/fundamentals/power-bi-overview)
+ [Power BI 서비스의 디자이너를 위한 기본 개념](/power-bi/service-basic-concepts)