---
title: Azure Portal을 사용하여 Gen2 환경 설정 - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Portal을 사용하여 Azure Time Series Insights Gen2에서 환경을 설정하는 방법을 알아보세요.
author: riserrad
ms.author: riserrad
manager: edett
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: e10685b1b64e8e3cd636245625c13df12f177fbb
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107951837"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-portal"></a>Azure Portal를 사용하여 Azure Time Series Insights Gen2 환경 만들기

이 문서에서는 [Azure Portal](https://portal.azure.com/)을 사용하여 Azure Time Series Insights Gen2 환경을 만드는 방법을 설명합니다.

환경 프로비전 자습서에서는 이 프로세스를 안내합니다. 올바른 시계열 ID를 선택하는 방법을 알아보고 두 JSON 페이로드의 예제를 확인해 보겠습니다.</br>

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWzk3P]

## <a name="overview"></a>개요

Azure Time Series Insights Gen2 환경을 프로비전할 때는 다음의 Azure 리소스를 만듭니다.

* 종량제 가격 책정 모델을 따르는 Azure Time Series Insights Gen2 환경
* Azure Storage 계정
* 더 빠른 무제한 쿼리를 위한 선택적 웜 저장소

> [!TIP]
>
> * [환경을 계획하는 방법](./how-to-plan-your-environment.md)을 알아봅니다.
> * [이벤트 허브 원본을 추가](./how-to-ingest-data-event-hub.md)하는 방법 또는 [IoT Hub 원본을 추가](./how-to-ingest-data-iot-hub.md)하는 방법에 대해 읽어보세요.

다음 방법을 알게 됩니다.

1. 각 Azure Time Series Insights Gen 2 환경을 이벤트 원본과 연결합니다. 또한 타임스탬프 ID 속성과 고유한 소비자 그룹을 제공하여 환경에서 적절한 이벤트에 액세스할 수 있도록 합니다.

1. 프로비전이 완료되면 비즈니스 요구 사항에 맞게 액세스 정책 및 기타 환경 특성을 수정할 수 있습니다.

   > [!NOTE]
   > 환경을 프로비전할 때 첫 번째 단계는 선택 사항입니다. 이 단계를 건너뛰는 경우 나중에 데이터가 사용자 환경으로 흐르기 시작하고 쿼리를 통해 액세스할 수 있도록 이벤트 원본을 환경에 연결해야 합니다.

## <a name="create-the-environment"></a>환경 만들기

Azure Time Series Insights Gen 2 환경을 만들려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 *사물 인터넷* 에서 Azure Time Series Insights 리소스를 만듭니다.

1. **Gen2(L1)** 를 **계층** 으로 선택합니다. 환경 이름을 제공하고 사용할 구독 그룹 및 리소스 그룹을 선택합니다. 그런 다음 지원되는 위치를 선택하여 환경을 호스트합니다.

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration.png" alt-text="Azure Time Series Insights 인스턴스를 만듭니다." lightbox="media/how-to-create-environment-using-portal/environment-configuration.png":::

1. 시계열 ID를 입력합니다.

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration-2.png" alt-text="계속해서 Azure Time Series Insights 환경 구성을 만듭니다." lightbox="media/how-to-create-environment-using-portal/environment-configuration-2.png":::

   > [!NOTE]
   >
   > * 시계열 ID는 *대/소문자를 구분* 하며 *변경할 수 없습니다*. (설정한 후에는 변경할 수 없습니다.)
   > * 시계열 ID는 최대 *세 개* 의 키로 구성될 수 있습니다. 사용자 환경으로 데이터를 보내는 각 디바이스 센서를 고유하게 나타내는 데이터베이스의 기본 키로 간주합니다. 한 가지 속성을 사용하거나, 최대 세 가지 속성을 조합해 사용할 수 있습니다.
   > * [시계열 ID를 선택하는 방법](./how-to-select-tsid.md)에 대해 자세히 읽어보세요.

1. 스토리지 계정 이름, 계정 종류를 선택하고 [복제](../storage/common/redundancy-migration.md?tabs=portal) 선택 항목을 지정하여 Azure Storage 계정을 만듭니다. 이렇게 하면 Azure Storage 계정이 자동으로 만들어집니다. 기본적으로 [범용 v2](../storage/common/storage-account-overview.md) 계정이 만들어집니다. 계정은 이전에 선택한 Azure Time Series Insights Gen2 환경과 동일한 영역에 만들어집니다.
또는 새로운 Azure Time Series Gen2 환경을 만들 때 [Azure Resource Manager 템플릿](./time-series-insights-manage-resources-using-azure-resource-manager-template.md)을 통해 사용자 고유의 스토리지(byos)를 가져올 수도 있습니다.

1. **(선택 사항)** 사용자 환경에 있는 최신 데이터에 대해 더 빠른 무제한 쿼리를 원하는 경우 환경에서 웜 저장소를 사용하도록 설정합니다. Azure Time Series Insights Gen2 환경을 만든 후에는 왼쪽 탐색 창에서 **스토리지 구성** 옵션을 통해 웜 저장소를 만들거나 삭제할 수도 있습니다.

1. **(선택 사항)** 이제 이벤트 원본을 추가할 수 있습니다. 인스턴스가 프로비전될 때까지 기다릴 수도 있습니다.

   * Azure Time Series Insights는 [Azure IoT Hub](./how-to-ingest-data-iot-hub.md) 및 [Azure Event Hubs](./how-to-ingest-data-event-hub.md)를 이벤트 원본 옵션으로 지원합니다. 환경을 만들 때에는 단일 이벤트 원본만 추가할 수 있지만, 나중에 다른 이벤트 원본을 추가할 수 있습니다.

     이벤트 원본을 추가할 때 기존 소비자 그룹을 선택하거나 새 소비자 그룹을 만들 수 있습니다. 이벤트 원본에서 사용자 환경의 고유한 소비자 그룹을 사용하여 데이터를 읽는지 확인합니다.

   * 이벤트 원본에서 데이터 수집을 시작할 시기를 선택합니다. 기본값은 이벤트 원본을 만든 시점부터 수집을 시작하는 것입니다.

     > [!TIP]
     > **내 모든 데이터** 옵션을 선택하여 이벤트 원본에서 기존 데이터를 수집하는 경우 Azure Time Series Insights Gen2 환경에서 모든 데이터를 처리하므로 초기 대기 시간이 길어질 수 있습니다. 대기 시간은 궁극적으로 데이터가 인덱싱되면서 줄어들어야 합니다.

   * 적절한 타임스탬프 속성을 선택합니다. 기본적으로 Azure Time Series Insights는 각 이벤트 원본에 대한 메시지를 큐에 넣은 시간을 사용합니다.

     > [!TIP]
     > 메시지를 큐에 넣은 시간은 일괄 처리 이벤트 시나리오 또는 기록 데이터 업로드 시나리오에서 사용하기에 가장 적합한 구성 설정이 아닐 수 있습니다. 이러한 경우 타임스탬프 속성을 사용하거나 사용하지 않기로 결정했는지 확인합니다.

   :::image type="content" source="media/how-to-create-environment-using-portal/configure-event-source.png" alt-text="이벤트 원본 구성 탭" lightbox="media/how-to-create-environment-using-portal/configure-event-source.png":::

1. **검토 + 만들기** 를 선택하여 사용자 환경이 원하는 방식으로 프로비전 및 구성되었는지 확인합니다.

    :::image type="content" source="media/how-to-create-environment-using-portal/environment-confirmation.png" alt-text="검토 + 만들기 탭" lightbox="media/how-to-create-environment-using-portal/environment-confirmation.png":::

## <a name="next-steps"></a>다음 단계

* [환경 계획](./how-to-plan-your-environment.md)을 읽어 Azure Time Series Insights에서 일반적으로 사용할 수 있는 환경과 Gen2 환경에 대해 자세히 알아보세요.
* Azure Time Series Insights Gen2 환경의 [스트리밍 수집 이벤트 원본](./concepts-streaming-ingestion-event-sources.md)에 대해 알아보세요.
* [사용자 활동을 관리하는 방법](./how-to-provision-manage.md)에 대해 알아보세요.
