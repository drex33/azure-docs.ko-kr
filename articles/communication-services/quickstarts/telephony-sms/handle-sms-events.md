---
title: 빠른 시작 - 배달 보고서 및 인바운드 메시지에 대한 SMS 이벤트 처리
titleSuffix: An Azure Communication Services quickstart
description: Azure Communication Services를 사용하여 SMS 이벤트를 처리하는 방법을 알아봅니다.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: ea86ec191cd56f123a0caf7af4e7822a91101a23
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123251157"
---
# <a name="quickstart-handle-sms-events-for-delivery-reports-and-inbound-messages"></a>빠른 시작: 배달 보고서 및 인바운드 메시지에 대한 SMS 이벤트 처리

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Communication Services SMS 이벤트를 처리하기 위해 Azure Event Grid를 사용하여 Azure Communication Services를 시작합니다.

## <a name="about-azure-event-grid"></a>Azure Event Grid 정보

[Azure Event Grid](../../../event-grid/overview.md)는 클라우드 기반 이벤트 서비스입니다. 이 문서에서는 [Communication Services 이벤트](../../../event-grid/event-schema-communication-services.md)에 대한 이벤트를 구독하고, 이벤트를 트리거하여 결과를 확인하는 방법에 대해 알아봅니다. 일반적으로 이벤트 데이터를 처리하고 작업을 수행하는 엔드포인트에 이벤트를 보냅니다. 이 문서에서는 메시지를 수집하고 표시하는 웹앱에 이벤트를 보냅니다.

## <a name="prerequisites"></a>사전 요구 사항
- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure Communication Services 리소스. 자세한 내용은 [Azure Communication Services 리소스 만들기](../create-communication-resource.md) 빠른 시작에서 확인할 수 있습니다.
- SMS 지원 전화 번호입니다. [전화 번호를 가져옵니다](./get-phone-number.md).

## <a name="setting-up"></a>설치

### <a name="enable-event-grid-resource-provider"></a>Event Grid 리소스 공급자를 사용하도록 설정

이전에 Azure 구독에서 Event Grid를 사용하지 않은 경우 다음 단계에 따라 Event Grid 리소스 공급자를 등록해야 할 수 있습니다.

Azure Portal에서 다음을 수행합니다.

1. 왼쪽 메뉴에서 **구독** 을 선택합니다.
2. Event Grid에 대해 사용 중인 구독을 선택합니다.
3. 왼쪽 메뉴의 **설정** 에서 **리소스 공급자** 를 선택합니다.
4. **Microsoft.EventGrid** 를 찾습니다.
5. 등록되지 않은 경우 **등록** 을 선택합니다.

등록을 완료하는 데 잠시 시간이 걸릴 수 있습니다. **새로 고침** 을 선택하여 상태를 업데이트합니다. **상태** 가 **등록됨** 이면 진행할 준비가 된 것입니다.

### <a name="event-grid-viewer-deployment"></a>Event Grid Viewer 배포

이 빠른 시작에서는 [Azure Event Grid Viewer 샘플](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/)을 사용하여 이벤트를 거의 실시간으로 볼 수 있습니다. 이렇게 하면 사용자에게 실시간 피드 환경이 제공됩니다. 또한 각 이벤트의 페이로드를 검사할 수도 있습니다.

## <a name="subscribe-to-the-sms-events-using-web-hooks"></a>웹후크를 사용하여 SMS 이벤트 구독

포털에서 만든 Azure Communication Services 리소스로 이동합니다. Communication Services 리소스 내에서 **Communication Services** 페이지의 왼쪽 메뉴에서 **이벤트** 를 선택합니다.

:::image type="content" source="./media/handle-sms-events/select-events.png" alt-text="리소스의 이벤트 페이지 내에서 이벤트 구독 단추 선택을 보여 주는 스크린샷":::

**이벤트 구독 추가** 를 눌러 만들기 마법사를 시작합니다.

**이벤트 구독 만들기** 페이지에서 이벤트 구독에 대한 **이름** 을 입력합니다.

특정 이벤트를 구독하여 추적하려는 SMS 이벤트와 이벤트를 보내는 위치를 Event Grid에 알릴 수 있습니다. 드롭다운 메뉴에서 구독하려는 이벤트를 선택합니다. SMS에 대해 `SMS Received` 및 `SMS Delivery Report Received`를 선택할 수 있습니다.

**시스템 항목 이름** 을 제공하라는 메시지가 표시되면 고유한 문자열을 자유롭게 제공합니다. 이 필드는 사용자 환경에 영향을 주지 않으며 내부 원격 분석 용도로 사용됩니다.

[Azure Communication Services에서 지원하는 이벤트](../../../event-grid/event-schema-communication-services.md)의 전체 목록을 확인하세요.

:::image type="content" source="./media/handle-sms-events/select-events-create-eventsub.png" alt-text="선택한 SMS 받음 및 SMS 전송 보고서 받음 이벤트 유형을 보여 주는 스크린샷":::

**엔드포인트 유형** 으로 **웹후크** 를 선택합니다.

:::image type="content" source="./media/handle-sms-events/select-events-create-linkwebhook.png" alt-text="웹후크로 설정되는 엔드포인트 유형 필드를 보여 주는 스크린샷":::

**엔드포인트** 에 대해 **엔드포인트 선택** 을 클릭하고 웹앱의 URL을 입력합니다.

이 경우 빠른 시작의 앞부분에서 설정한 [Azure Event Grid Viewer 샘플](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/)의 URL을 사용합니다. 샘플의 URL 형식은 `https://{{site-name}}.azurewebsites.net/api/updates`입니다.

그런 다음, **선택 확인** 을 선택합니다.

:::image type="content" source="./media/handle-sms-events/select-events-create-selectwebhook-epadd.png" alt-text="웹후크 엔드포인트 확인을 보여 주는 스크린샷":::

## <a name="viewing-sms-events"></a>SMS 이벤트 보기

### <a name="triggering-sms-events"></a>SMS 이벤트 트리거

이벤트 트리거를 보려면 먼저 이벤트를 생성해야 합니다.

- Communication Services 전화 번호에서 문자 메시지를 받으면 `SMS Received` 이벤트가 생성됩니다. 이벤트를 트리거하려면 메시지를 휴대폰에서 Communication Services 리소스에 연결된 전화 번호로 보내기만 하면 됩니다.
- Communication Services 전화 번호를 사용하여 사용자에게 SMS를 보내면 `SMS Delivery Report Received` 이벤트가 생성됩니다. 이벤트를 트리거하려면 [SMS 보냄](../telephony-sms/send.md) 옵션에서 `Delivery Report`를 사용하도록 설정해야 합니다. `Delivery Report`를 사용하여 메시지를 휴대폰에 보내보세요. 이 작업이 완료되면 Azure 계정에서 몇 USD 센트 이하의 작은 비용이 발생합니다.

[Azure Communication Services에서 지원하는 이벤트](../../../event-grid/event-schema-communication-services.md)의 전체 목록을 확인하세요.

### <a name="receiving-sms-events"></a>SMS 이벤트 받기

위의 작업 중 하나가 완료되면 `SMS Received` 및 `SMS Delivery Report Received` 이벤트를 엔드포인트로 보낸다는 것을 알 수 있습니다. 이러한 이벤트는 처음에 설정한 [Azure Event Grid Viewer 샘플](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/)에 표시됩니다. 이벤트 옆에 있는 눈 모양 아이콘을 눌러 전체 페이로드를 볼 수 있습니다. 이벤트는 다음과 같이 표시됩니다.

:::image type="content" source="./media/handle-sms-events/sms-received.png" alt-text="SMS 받음 이벤트에 대한 Event Grid 스키마를 보여 주는 스크린샷":::

:::image type="content" source="./media/handle-sms-events/sms-delivery-report-received.png" alt-text="SMS 전송 보고서 이벤트에 대한 Event Grid 스키마가 표시된 스크린샷":::

[이벤트 스키마 및 기타 이벤트 개념](../../../event-grid/event-schema-communication-services.md)에 대해 자세히 알아보세요.

## <a name="clean-up-resources"></a>리소스 정리

Communication Services 구독을 정리하고 제거하려면 리소스 또는 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다. [리소스 정리](../create-communication-resource.md#clean-up-resources)에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 SMS 이벤트를 사용하는 방법을 알아보았습니다. Event Grid 구독을 만들어 SMS 메시지를 받을 수 있습니다.

> [!div class="nextstepaction"]
> [SMS 보내기](../telephony-sms/send.md)

다음을 수행할 수도 있습니다.

 - [이벤트 처리 개념에 대해 알아보기](../../../event-grid/event-schema-communication-services.md)
 - [Event Grid에 대해 알아보기](../../../event-grid/overview.md)