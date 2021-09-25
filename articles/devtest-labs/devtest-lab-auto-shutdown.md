---
title: Azure DevTest Labs 및 계산 vm에서 자동 종료 정책 관리
description: 가상 머신이 사용되지 않을 때 자동으로 종료되도록 랩에 대한 자동 종료 정책을 설정하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 75fa8f07c7e3d67ea029413d427046e20d45cdea
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128648585"
---
# <a name="configure-autoshutdown-for-lab-and-compute-virtual-machines-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩과 컴퓨팅 가상 머신에 대한 자동 종료 구성

이 문서에서는 DevTest Labs의 랩 VM과 컴퓨팅 VM에 대한 자동 종료 설정을 구성하는 방법을 설명합니다.

## <a name="configure-autoshutdown-for-lab-vms-devtest-labs"></a>랩 VM에 대한 자동 종료 구성(DevTest Labs)

Azure DevTest Labs를 통해 각 랩에 대한 정책(설정)을 관리하여 비용을 제어하고 랩에서의 낭비를 최소화할 수 있습니다. 이 문서에서는 랩에 대한 자동 종료 정책을 구성하는 방법을 보여 줍니다.  랩 VM에 대한 자동 종료 설정을 구성하는 방법도 보여 줍니다. 모든 랩 정책을 설정하는 방법을 보려면 [Azure DevTest Labs에서 랩 정책 정의](devtest-lab-set-lab-policy.md)를 참조하세요.  

### <a name="set-autoshutdown-policy-for-a-lab"></a>랩에 대한 자동 종료 정책 설정

랩 소유자로서 랩에서 모든 VM에 대한 종료 일정을 구성할 수 있습니다. 이 작업을 수행하면 사용되지 않는(유휴 상태) 머신의 실행 비용을 절약할 수 있습니다. 중앙에서 모든 랩 VM에 종료 정책을 적용하는 것은 물론, 랩 사용자가 개별 머신의 일정을 설정하는 수고를 덜어줄 수도 있습니다. 이 기능을 사용하면 랩 사용자가 VM의 종료 일정을 완전히 제어할 수 있거나 VM의 종료를 제어할 수 없도록 랩 일정에 대한 정책을 설정할 수 있습니다. 랩 소유자로서 다음 단계를 수행하여 이 정책을 구성할 수 있습니다.

1. 랩의 홈 페이지에서 **구성 및 정책** 을 선택합니다.
2. 왼쪽 메뉴의 **일정** 섹션에서 **자동 종료 정책** 을 선택합니다.
3. 옵션 중 하나를 선택합니다. 다음 섹션에서는 이러한 옵션에 대해 자세히 설명합니다.

    ![자동 종료 정책 옵션](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

> [!IMPORTANT]
> 종료 정책을 변경하는 경우 랩에서 새로 생성된 VM에만 적용되고 기존 VM에는 적용되지 않습니다.

### <a name="configure-autoshutdown-settings"></a>자동 종료 설정 구성

자동 종료 정책을 사용하면 랩의 VM이 종료되는 시간을 지정하여 랩 낭비를 최소화할 수 있습니다.

랩에 대한 정책을 보거나 변경하려면 다음 단계를 수행합니다.

1. 랩의 홈 페이지에서 **구성 및 정책** 을 선택합니다.
2. 왼쪽 메뉴의 **일정** 섹션에서 **자동 종료** 를 선택합니다.
3. 이 정책을 사용하도록 설정하려면 **설정** 을 선택하고 사용하지 않도록 설정하려면 **해제** 를 선택합니다.
     ![자동 종료 세부 정보](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
4. 이 정책을 사용하도록 설정한 경우 현재 랩의 모든 VM을 종료하는 시간(및 표준 시간대)을 지정합니다.
5. 지정된 자동 종료 시간 30분 전에 알림을 보내는 옵션에서 **예** 또는 **아니요** 를 지정합니다. **예** 를 선택할 경우 알림을 게시하거나 보낼 위치를 지정하는 이메일 주소 또는 웹후크 URL 엔드포인트를 입력합니다. 사용자에게 알림이 전송되고 종료를 지연할 수 있는 옵션이 제공됩니다. 자세한 내용은 [알림](#notifications) 섹션을 참조하세요.
6. **저장** 을 선택합니다.

    기본적으로 이 정책을 사용하도록 설정하면 현재 랩의 모든 VM에 적용됩니다. 특정 VM에서 이 설정을 제거하려면 VM의 관리 창을 열고 해당 **자동 종료** 설정을 변경합니다.

> [!NOTE]
> 현재 예약된 시간의 30분 이내에 랩 또는 특정 랩 가상 머신의 자동 종료 일정을 업데이트하는 경우 업데이트된 종료 시간은 다음날 일정에 적용됩니다.

### <a name="user-sets-a-schedule-and-can-opt-out"></a>사용자가 일정을 설정하고 옵트아웃할 수 있음

이 정책에 랩을 설정한 경우 랩 사용자는 랩 일정을 재정의하거나 옵트아웃할 수 있습니다. 이 옵션은 랩 사용자에게 해당 VM의 자동 종료 일정에 대한 모든 권한을 부여합니다. 랩 사용자는 해당 VM 자동 종료 일정 페이지에 변경 내용이 표시되지 않습니다.

![자동 종료 정책 옵션 - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>사용자가 일정을 설정하지만 옵트아웃할 수 없음

이 정책에 랩을 설정한 경우 랩 사용자는 랩 일정을 재정의할 수 있습니다. 그러나 자동 종료 정책을 옵트아웃할 수는 없습니다. 이 옵션은 랩의 모든 머신에 자동 종료 일정이 적용되도록 합니다. 랩 사용자는 해당 VM의 자동 종료 일정을 업데이트하고 종료 알림을 설정할 수 있습니다.

![자동 종료 정책 옵션 - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>랩 관리자가 설정한 일정을 사용자가 제어할 수 없음

이 정책에 랩을 설정한 경우 랩 사용자는 랩 일정을 재정의하거나 옵트아웃할 수 없습니다. 이 옵션은 랩 관리자에게 랩의 모든 머신에 대한 일정의 완전한 권한을 제공합니다. 랩 사용자는 해당 VM에 대한 자동 종료 알림만 설정할 수 있습니다.

![자동 종료 정책 옵션 - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="configure-autoshutdown-for-compute-vms"></a>컴퓨팅 VM에 대한 자동 종료 구성

1. **가상 머신** 페이지의 **작업** 섹션에서 왼쪽 메뉴에 있는 **자동 종료** 를 선택합니다.
2. **자동 종료** 페이지에서 이 정책을 사용하도록 설정하려면 **켜기** 를 선택하고, 사용하지 않도록 설정하려면 **끄기** 를 선택합니다.
3. 이 정책을 사용하도록 설정한 경우 VM을 종료해야 하는 **시간** 과 **표준 시간대** 를 지정합니다.
4. 지정된 자동 종료 시간 30분 전에 알림을 보내는 옵션에서 **예** 또는 **아니요** 를 선택합니다. **예** 를 선택할 경우 알림을 게시하거나 보낼 위치를 지정하는 이메일 주소 또는 웹후크 URL 엔드포인트를 입력합니다. 사용자에게 알림이 전송되고 종료를 지연할 수 있는 옵션이 제공됩니다. 자세한 내용은 [알림](#notifications) 섹션을 참조하세요.
5. **저장** 을 선택합니다.

    ![컴퓨팅 VM에 대한 자동 종료 구성](./media/devtest-lab-auto-shutdown/comnpute-auto-shutdown.png)

### <a name="view-activity-logs-for-auto-shutdown-updates"></a>자동 종료 업데이트에 대한 활동 로그 보기

자동 종료 설정을 업데이트하면 VM의 활동 로그에 활동이 로깅됩니다.

1. [Azure Portal](https://portal.azure.com)에서 VM의 홈페이지로 이동합니다.
2. 왼쪽 메뉴에서 **활동 로그** 를 선택합니다.
3. 필터에서 **리소스: mycomputevm** 을 제거합니다.
4. 활동 로그에 **일정 추가 또는 수정** 작업이 표시되는지 확인합니다. 표시되지 않는 경우 잠시 기다렸다가 활동 로그를 새로 고칩니다.

    ![활동 로그 항목](./media/devtest-lab-auto-shutdown/activity-log-entry.png)
5. **일정 추가 또는 수정** 작업을 선택하여 **요약** 페이지에서 다음 정보를 확인합니다.

    - 작업 이름(일정 추가 또는 수정)
    - 자동 종료 설정이 업데이트된 날짜와 시간
    - 설정을 업데이트한 사용자의 메일 주소

        ![활동 로그 항목 요약](./media/devtest-lab-auto-shutdown/activity-log-entry-summary.png)
6. **일정 추가 또는 수정** 페이지에서 **변경 기록** 탭으로 전환하면 설정 변경 기록이 표시됩니다. 다음 예제에서는 2020년 4월 10일 15:18:47 EST에 종료 시간이 오후 7시에서 오후 6시로 변경되었습니다. 그리고 15:25:09 EST에 설정이 해제되었습니다.

    ![활동 로그 - 변경 기록](./media/devtest-lab-auto-shutdown/activity-log-entry-change-history.png)
7. 작업에 대한 자세한 내용을 보려면 **일정 추가 또는 수정** 페이지의 **JSON** 탭으로 전환합니다.

## <a name="notifications"></a>공지

자동 종료를 구성하면, VM이 영향을 받는 경우 자동 종료가 트리거되기 30분 전에 랩 사용자에게 알림이 전송됩니다. 이 옵션을 사용하면 랩 사용자가 종료되기 전에 작업을 저장할 수 있습니다. VM에서 작업을 계속해야 하는 경우를 위해 알림에서 각 VM에 대한 다음 작업 링크도 제공합니다.

- 이번에는 자동 종료 건너뛰기
- 1시간 동안 자동 종료 다시 알림
- 2시간 동안 자동 종료 다시 알림

웹후크를 지정한 경우 웹후크 URL로 알림이 전송됩니다.  자동 종료 설정에서 메일 주소를 지정한 경우 해당 메일 주소로 메일이 전송됩니다. 웹후크를 사용하면 특정 이벤트를 구독하는 통합을 빌드하거나 설정할 수 있습니다. 해당 이벤트 중 하나가 트리거되면 DevTest Labs는 웹후크에 구성된 URL로 HTTP POST 페이로드를 보냅니다. 웹후크에 응답하는 방법에 대한 자세한 내용은 [Azure Functions HTTP 트리거 및 바인딩 개요](../azure-functions/functions-bindings-http-webhook.md) 또는 [Azure Logic Apps에 대한 HTTP 트리거 추가](../connectors/connectors-native-http.md#add-an-http-trigger)를 참조하세요.

웹후크는 Azure Logic Apps, Slack 등의 다양한 앱에서 광범위하게 지원되므로 웹후크를 사용하는 것이 좋습니다.  웹후크를 사용하면 알림을 보내는 고유한 방법을 구현할 수 있습니다. 예를 들어 이 문서에서는 Azure Logic Apps를 사용하여 VM 소유자에게 메일을 보내도록 자동 종료 알림을 구성하는 방법을 설명합니다. 먼저 랩에서 자동 종료 알림을 사용하도록 설정하는 기본 단계를 빠르게 살펴보겠습니다.

### <a name="create-a-logic-app-that-sends-email-notifications"></a>메일 알림을 보내는 논리 앱 생성하기

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)는 Office 365, Twitter 등의 다른 클라이언트와 서비스를 쉽게 통합할 수 있도록 많은 커넥터를 제공합니다. 메일 알림에 사용할 논리 앱을 설정하는 단계는 개괄적으로 다음 네 단계로 나뉠 수 있습니다.

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
