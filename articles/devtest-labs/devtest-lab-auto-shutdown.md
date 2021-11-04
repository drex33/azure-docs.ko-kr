---
title: Azure 가상 컴퓨터에 대 한 자동 종료 정책 관리
description: 설정 된 시간에 가상 컴퓨터를 자동으로 종료 하는 랩 자동 종료 정책을 설정 하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 10/29/2021
ms.openlocfilehash: 9842ed461cd8860a006de130402cd586ed135873
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476078"
---
# <a name="configure-auto-shutdown-schedule-and-policy-for-azure-virtual-machines-in-devtest-labs"></a>DevTest Labs에서 Azure virtual machines에 대 한 자동 종료 일정 및 정책 구성

Azure DevTest Labs를 사용 하면 각 랩에 대 한 정책을 관리 하 여 랩에서 비용을 제어할 수 있습니다. 이 문서에서는 랩 수준에서 Azure Vm (가상 머신)에 대 한 자동 종료 정책을 구성 하는 방법을 보여 줍니다. 또한 정의 된 정책 내의 개별 수준에서 Vm에 대 한 자동 종료 설정을 구성 하는 방법을 보여 줍니다. 모든 랩 정책을 설정하는 방법을 보려면 [Azure DevTest Labs에서 랩 정책 정의](devtest-lab-set-lab-policy.md)를 참조하세요.

## <a name="configure-auto-shutdown-schedule"></a>자동 종료 일정 구성

랩 소유자는 랩의 Vm에 대 한 종료 일정을 구성할 수 있습니다. 자동 종료 일정은 랩의 Vm이 종료 되는 시간을 지정할 수 있도록 하 여 랩 낭비를 최소화 합니다. 현재 일정의 30 분 이내에 자동 종료 일정 업데이트는 다음 날의 일정에 적용 됩니다.

랩 일정을 보거나 변경 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **DevTest Labs** 에서 랩으로 이동 합니다.

1. **설정** 아래에서 **구성 및 정책** 을 선택 합니다.

   :::image type="content" source="./media/devtest-lab-auto-shutdown/portal-lab-configuration-policies.png" alt-text="DevTest Labs 홈 페이지의 스크린샷":::

1. **구성 및 정책** 페이지의 **일정** 에서 **자동 종료** 를 선택 합니다.

1. 다음 속성을 구성합니다.

    |속성 | Description |
    |---|---|
    |사용| 이 정책을 사용 하도록 설정 하려면 **켜기** 를 선택 하 고, 사용 하지 않도록 설정 하려면 **Off** 를 선택 합니다.|
    |예약 된 종료| 현재 랩에서 모든 Vm을 종료 하는 시간을 입력 합니다.|
    |표준 시간대| 드롭다운 목록에서 표준 시간대를 선택 합니다.|
    |자동 종료 전에 알림을 보내시겠습니까? | 지정 된 자동 종료 시간 전에 30 분 전에 알림을 보내려면 **예** 또는 **아니요** 를 선택 합니다. **예** 를 선택할 경우 알림을 게시하거나 보낼 위치를 지정하는 이메일 주소 또는 웹후크 URL 엔드포인트를 입력합니다. 사용자에게 알림이 전송되고 종료를 지연할 수 있는 옵션이 제공됩니다. 자세한 내용은 아래의 [알림](#notifications)을 참조 하세요.|
    |Webhook URL| 자동 종료가 발생 하려고 하면 지정 된 webhook 끝점에 알림이 게시 됩니다.|
    |메일 주소| 경고 알림 전자 메일을 받을 세미콜론으로 구분 된 전자 메일 주소 집합을 입력 합니다.|

   :::image type="content" source="./media/devtest-lab-auto-shutdown/auto-shutdown.png" alt-text="자동 종료 일정 정보의 스크린샷":::
 
1. **저장** 을 선택합니다.  기본적으로 사용 정책은 현재 랩의 모든 Vm에 적용 됩니다. 개별 VM의 일정을 수정 하려면 아래에서 [개별 vm에 대 한 자동 종료 구성](#configure-auto-shutdown-for-individual-vms)을 참조 하세요.

## <a name="configure-auto-shutdown-policy"></a>자동 종료 정책 구성

중앙에서 모든 랩 VM에 종료 정책을 적용하는 것은 물론, 랩 사용자가 개별 머신의 일정을 설정하는 수고를 덜어줄 수도 있습니다. 다음 단계를 수행 하 여이 정책을 구성 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **DevTest Labs** 에서 랩으로 이동 합니다.

1. **설정** 아래에서 **구성 및 정책** 을 선택 합니다.

1. **구성 및 정책** 페이지의 **일정** 에서 **자동 종료 정책** 을 선택 합니다.

1. 사용자가 개별 VM의 종료 일정에 대해 보유 하 게 될 제어 수준을 선택 합니다. 

    | 제어 수준 | Description |
    |----|----|
    |사용자가 일정을 설정하고 옵트아웃할 수 있음| 랩 사용자는 랩 일정을 재정의 하거나 옵트아웃 (opt out) 할 수 있습니다. 이 옵션은 랩 사용자에 게 Vm의 자동 종료 일정에 대 한 모든 권한을 부여 합니다.|
    |사용자가 일정을 설정하지만 옵트아웃할 수 없음| 랩 사용자는 랩 일정을 재정의할 수 있습니다. 그러나 자동 종료 정책은 옵트아웃 (opt out) 할 수 없습니다. 이 옵션은 랩의 모든 컴퓨터가 자동 종료 일정에 있는지 확인 합니다. 랩 사용자는 종료 알림을 수정할 수도 있습니다.|
    |사용자가 랩 관리자가 설정한 일정을 제어 하지 않습니다.| 랩 사용자는 랩 일정을 재정의 하거나 옵트아웃 (opt out) 할 수 없습니다. 이 옵션은 랩 관리자에 게 랩의 모든 컴퓨터 일정에 대 한 완전 한 제어를 제공 합니다.|

   :::image type="content" source="./media/devtest-lab-auto-shutdown/auto-shutdown-policy-options.png" alt-text="자동 종료 정책 옵션의 스크린샷":::

1. **저장** 을 선택합니다. 종료 정책에 대 한 변경 내용은 기존 Vm이 아닌 랩에서 만든 새 Vm에만 적용 됩니다.

## <a name="configure-auto-shutdown-for-individual-vms"></a>개별 Vm에 대 한 자동 종료 구성

1. **DevTest Labs** 에서 랩으로 이동 합니다.

1. **내 랩에서** **내 가상 컴퓨터** 를 선택 합니다. 그런 다음 가상 컴퓨터를 선택 합니다.

   :::image type="content" source="./media/devtest-lab-auto-shutdown/portal-lab-virtual-machines.png" alt-text="가상 컴퓨터 목록의 스크린샷":::

1. **가상 컴퓨터** 페이지의 **작업** 아래에서 **자동 종료** 를 선택 합니다.

   :::image type="content" source="./media/devtest-lab-auto-shutdown/virtual-machines-autho-shutdown.png" alt-text="가상 컴퓨터 홈 페이지의 스크린샷":::

1. 구성은 위의 [자동 종료 일정 구성](#configure-auto-shutdown-schedule)에 설명 된 것과 동일 합니다. 자동 종료 정책은 위의 [자동 종료 정책 구성](#configure-auto-shutdown-policy)에 설명 된 대로 구성을 수정 하는 랩 사용자의 기능을 결정 합니다. 

## <a name="view-activity-logs-for-auto-shutdown-updates"></a>자동 종료 업데이트에 대한 활동 로그 보기

자동 종료 일정 또는 정책을 업데이트 하면 VM의 [활동 로그](../azure-monitor/essentials/activity-log.md)에 기록이 표시 됩니다. 검색할 때 랩의 리소스 그룹을 제거 하 고 및에 대 한 [필터](../azure-monitor/essentials/activity-log.md#view-the-activity-log) **작업** 을 추가 `Add or modify policies` `Add or modify schedules` 합니다.

## <a name="notifications"></a>알림

자동 종료가 구성 되 면 Vm이 영향을 받기 전에 30 분 후에 알림이 랩 사용자에 게 전송 됩니다. 이 옵션을 사용하면 랩 사용자가 종료되기 전에 작업을 저장할 수 있습니다. 또한 알림은 VM에서 계속 작업 해야 하는 경우 각 VM에 대 한 링크도 제공 합니다. 사용자는 다음과 같은 옵션을 선택할 수 있습니다.

- 이 시간 동안 자동 종료 건너뛰기
- 1 시간 동안 자동 종료를 다시 알림
- 2 시간 동안 자동 종료를 다시 알림

웹후크를 지정한 경우 웹후크 URL로 알림이 전송됩니다.  전자 메일 주소를 지정 하면 해당 전자 메일 주소로 전자 메일이 전송 됩니다. 웹후크를 사용하면 특정 이벤트를 구독하는 통합을 빌드하거나 설정할 수 있습니다. 해당 이벤트 중 하나가 트리거되면 DevTest Labs는 웹후크에 구성된 URL로 HTTP POST 페이로드를 보냅니다. 웹후크에 응답하는 방법에 대한 자세한 내용은 [Azure Functions HTTP 트리거 및 바인딩 개요](../azure-functions/functions-bindings-http-webhook.md) 또는 [Azure Logic Apps에 대한 HTTP 트리거 추가](../connectors/connectors-native-http.md#add-an-http-trigger)를 참조하세요.

Azure Logic Apps 및 여유 시간과 같은 다양 한 앱에서 광범위 하 게 지원 되므로 웹 후크를 사용 하는 것이 좋습니다.  웹후크를 사용하면 알림을 보내는 고유한 방법을 구현할 수 있습니다. 예를 들어이 문서에서는 Azure Logic Apps를 사용 하 여 VM 소유자에 게 자동 종료 전자 메일 알림을 구성 하는 방법을 안내 합니다. 먼저 실습에서 자동 종료 알림을 사용 하도록 설정 하는 기본 단계를 빠르게 수행해 보겠습니다.

## <a name="create-a-logic-app-that-sends-email-notifications"></a>메일 알림을 보내는 논리 앱 생성하기

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 은 서비스를 Office 365 및 Twitter와 같은 다른 클라이언트와 통합할 수 있는 커넥터를 제공 합니다. 개략적인 수준에서 전자 메일 알림에 대 한 논리 앱을 설정 하는 작업은 4 단계로 나눌 수 있습니다.

- 논리 앱을 만듭니다.
- 기본 제공 템플릿을 구성합니다.
- 메일 클라이언트와 통합합니다.
- 웹후크 URL을 가져옵니다.

### <a name="create-a-logic-app"></a>논리 앱 만들기

시작하려면 다음 단계를 수행하여 Azure 구독에서 논리 앱을 만듭니다.

1. 왼쪽 메뉴에서 **+ 리소스 만들기** 를 선택하고 **통합**, **논리 앱** 을 차례로 선택합니다.

    ![새 논리 앱 메뉴](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. **논리 앱 - 만들기** 페이지에서 다음 단계를 수행합니다.
    1. 논리 앱의 **이름** 을 입력합니다.
    2. Azure **구독** 을 선택합니다.
    3. 새 **리소스 그룹** 을 만들거나 기존 리소스 그룹을 선택합니다.
    4. 논리 앱의 **위치** 를 선택합니다.

        ![새 논리 앱 - 설정](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)

3. **알림** 에서 알림의 **리소스로 이동** 을 선택합니다.

    ![리소스로 이동](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. **배포 도구** 범주에서 **논리 앱 디자이너** 를 선택합니다.

    ![HTTP 요청/응답 선택](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. **HTTP 요청-응답** 페이지에서 **이 템플릿 사용** 을 선택합니다.

    ![이 템플릿 사용 옵션 선택](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. **요청 본문 JSON 스키마** 섹션에 다음 JSON을 복사합니다.

    ```json
    {
        "$schema": "http://json-schema.org/draft-04/schema#",
        "properties": {
            "delayUrl120": {
                "type": "string"
            },
            "delayUrl60": {
                "type": "string"
            },
            "eventType": {
                "type": "string"
            },
            "guid": {
                "type": "string"
            },
            "labName": {
                "type": "string"
            },
            "owner": {
                "type": "string"
            },
            "resourceGroupName": {
                "type": "string"
            },
            "skipUrl": {
                "type": "string"
            },
            "subscriptionId": {
                "type": "string"
            },
            "text": {
                "type": "string"
            },
            "vmName": {
                "type": "string"
            },
            "vmUrl": {
                "type": "string"
            },
            "minutesUntilShutdown": {
                "type": "string"
            }
        },
        "required": [
            "skipUrl",
            "delayUrl60",
            "delayUrl120",
            "vmName",
            "guid",
            "owner",
            "eventType",
            "text",
            "subscriptionId",
            "resourceGroupName",
            "labName",
            "vmUrl",
            "minutesUntilShutdown"
        ],
        "type": "object"
    }
    ```

    ![“요청 본문 JSON 스키마” 스크린샷](./media/devtest-lab-auto-shutdown/request-json.png)

7. 디자이너에서 **+ 새 단계** 를 선택하고 다음 단계를 수행합니다.
    1. **Office 365 Outlook - 메일 보내기** 를 검색합니다.
    2. **작업** 에서 **메일 보내기** 를 선택합니다.

        ![메일 보내기 옵션](./media/devtest-lab-auto-shutdown/select-send-email.png)

    3. **로그인** 을 선택하여 메일 계정에 로그인합니다.
    4. **받는 사람** 필드를 선택한 다음, 소유자를 선택합니다.
    5. **제목** 을 선택하고 메일 알림의 제목을 입력합니다. 예: “labName 랩의 vmName 머신 종료”
    6. **본문** 을 선택하고 메일 알림의 본문 내용을 정의합니다. 예: “vmName은 15분 후에 종료되도록 예약되었습니다. 이번에는 종료 건너뛰기: URL 1시간 종료 연기: delayUrl60 2시간 종료 연기: delayUrl120”

        ![요청 본문 JSON 스키마](./media/devtest-lab-auto-shutdown/email-options.png)
8. 도구 모음에서 **저장** 을 선택합니다. 이제 **HTTP POST URL** 을 복사할 수 있습니다. 복사 단추를 선택하여 URL을 클립보드에 복사합니다.

    ![웹후크 URL](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="next-steps"></a>다음 단계

모든 정책을 설정하는 방법에 대한 자세한 내용은 [Azure DevTest Labs에서 랩 정책 정의](devtest-lab-set-lab-policy.md)를 참조하세요.
