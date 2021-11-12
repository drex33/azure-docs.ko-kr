---
title: 랩 및 가상 머신에 대한 자동 종료 정책 구성
description: 매일 특정 시간에 VM을 종료하도록 Azure DevTest Labs 또는 개별 VM(가상 머신)에 대한 자동 종료 일정 및 정책을 설정하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 11/01/2021
ms.openlocfilehash: 622d9c2da013ad9eb8c3a0eef46a21999f54ee76
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286738"
---
# <a name="configure-auto-shutdown-for-labs-and-vms-in-devtest-labs"></a>DevTest Labs에서 랩 및 VM에 대한 자동 종료 구성

Azure DevTest Labs 랩 소유자는 특정 시간 또는 야간에 랩의 모든 VM(가상 머신)을 종료하도록 일정을 구성할 수 있습니다. 사용되지 않는 머신을 실행하는 비용을 절감할 수 있습니다.

랩 사용자가 자신의 개별 VM에 대해 자동 종료를 예약할 수 있는지 여부를 제어하는 중앙 자동 종료 정책을 설정할 수도 있습니다. 자동 종료 정책은 VM 소유자가 VM의 종료 일정을 완전히 제어할 수 있도록 허용하는 것부터 일정을 제어할 수 없도록 하는 것까지 다양합니다.

이 문서에서는 DevTest Labs 랩 및 개별 랩 VM에 대한 자동 종료 일정을 설정하는 방법을 설명합니다. 이 문서에서는 랩 자동 종료 정책을 설정하는 방법 및 자동 종료 알림을 구성하는 방법도 설명합니다.

## <a name="configure-lab-auto-shutdown-schedule"></a>랩 자동 종료 일정 구성

자동 종료는 특정 시간 또는 야간에 랩의 모든 VM을 종료하여 랩 낭비를 최소화하는 데 도움이 됩니다. 랩의 자동 종료 일정을 보거나 변경하려면 다음 단계를 수행합니다.

1. 랩의 홈 페이지에서 **구성 및 정책** 을 선택합니다.
1. 왼쪽 메뉴의 **일정** 섹션에서 자동 **종료를** 선택합니다.
1. 자동 **종료** 화면의 **사용에서** **켜기를** 선택하여 자동 종료를 사용하도록 설정하고 **끄기를** 선택하여 사용하지 않도록 설정합니다.
1. **예약된 종료** 및 **표준 시간대** 의 경우 자동 종료를 설정한 경우 모든 랩 VM을 종료할 표준 시간대를 지정합니다.
1. **자동 종료 전에 알림 보내기?** 의 경우 지정된 자동 종료 시간 30분 전에 알림을 보내는 옵션에 대해 **예** 또는 **아니요를** 선택합니다. **예를** 선택하는 경우 알림을 게시하거나 보낼 이메일 주소 아래에 웹후크 URL 또는 세미콜론으로 구분된 **이메일 주소** 아래에 **웹후크 URL** 엔드포인트를 입력합니다. 자세한 내용은 자동 [종료 알림 섹션을 참조하세요.](#auto-shutdown-notifications)
1. **저장** 을 선택합니다.

   ![랩에 대한 자동 종료 세부 정보 설정을 보여 주는 스크린샷](media/devtest-lab-auto-shutdown/auto-shutdown.png)

기본적으로 이 일정은 랩의 모든 VM에 적용됩니다. 특정 VM에서 이 설정을 제거하려면 정책에서 허용되는 경우 VM의 관리 창을 열고 **자동 종료** 설정을 변경합니다.

> [!NOTE]
> 이전에 예약된 종료 시간에서 30분 이내에 랩 또는 VM에 대한 자동 종료 일정을 업데이트하면 새 종료 시간이 다음 날에 적용됩니다.

## <a name="configure-lab-auto-shutdown-policy"></a>랩 자동 종료 정책 구성

랩 소유자는 랩에 대한 자동 종료 정책 설정을 관리하여 비용을 제어하고 랩의 낭비를 최소화할 수 있습니다. 모든 랩 정책을 설정하는 방법을 보려면 [Azure DevTest Labs 랩 정책 정의를](devtest-lab-set-lab-policy.md)참조하세요.

> [!IMPORTANT]
> 자동 종료 정책 변경 내용은 랩에서 만든 새 VM에만 적용됩니다. 기존 VM에는 적용되지 않습니다.

1. 랩의 홈 페이지에서 **구성 및 정책** 을 선택합니다.

1. 왼쪽 메뉴의 **일정** 섹션에서 자동 **종료 정책** 을 선택합니다.

1. 옵션 중 하나를 선택합니다.

   ![자동 종료 정책 옵션을 보여 주는 스크린샷.](./media/devtest-lab-auto-shutdown/policy-options.png)

   - **사용자가 일정을 설정하고 옵트아웃할 수 있습니다.** 랩 사용자는 랩 일정을 재정의하거나 옵트아웃할 수 있습니다. 이 옵션은 VM 소유자에게 VM의 자동 종료 일정을 설정할 수 있는 모든 권한을 부여합니다.

   - **사용자가 일정을 설정하고 옵트아웃할 수 없음:** 랩 사용자는 랩 일정을 재정의할 수 있지만 자동 종료 정책을 옵트아웃할 수는 없습니다. 이 옵션을 사용하면 모든 랩 VM이 자동 종료 일정에 따라 진행됩니다. VM 소유자는 일정 시간을 업데이트하고 종료 알림을 설정할 수 있습니다.

   - **사용자는 랩 관리자가 설정한 일정을 제어할** 수 없습니다. 랩 사용자는 랩 자동 종료 일정을 변경하거나 옵트아웃할 수 없습니다. 이 옵션을 사용하면 랩 관리자가 모든 랩 VM에 대한 일정을 완전히 제어할 수 있습니다. VM 소유자는 VM에 대한 자동 종료 알림을 설정할 수 있습니다.

1. **저장** 을 선택합니다.

## <a name="configure-vm-auto-shutdown-settings"></a>VM 자동 종료 설정 구성

자동 종료 정책에 따라 개별 랩 VM에 대한 자동 종료 일정을 설정할 수도 있습니다.

1. VM 홈페이지의 왼쪽 메뉴에 있는 **작업** 섹션에서 자동 **종료를** 선택합니다.
1. 자동 **종료** 화면의 **사용에서** **켜기를** 선택하여 자동 종료를 사용하도록 설정하고 **끄기를** 선택하여 사용하지 않도록 설정합니다.
1. **예약된 종료** 및 **표준 시간대** 의 경우 자동 종료를 설정한 경우 모든 랩 VM을 종료할 표준 시간대를 지정합니다.
1. **자동 종료 전에 알림 보내기?** 의 경우 지정된 자동 종료 시간 30분 전에 알림을 보내는 옵션에 대해 **예** 또는 **아니요를** 선택합니다. **예를** 선택하는 경우 웹후크 URL **아래에** 웹후크 URL 엔드포인트를 입력하거나 알림을 게시하거나 보낼 이메일 주소 아래에 이메일 **주소를** 입력합니다. 자세한 내용은 자동 [종료 알림 섹션을 참조하세요.](#auto-shutdown-notifications)
1. **저장** 을 선택합니다.

   ![VM에 대한 자동 종료 세부 정보 설정을 보여 주는 스크린샷](media/devtest-lab-auto-shutdown/compute-auto-shutdown.png)

### <a name="view-activity-logs-for-auto-shutdown-updates"></a>자동 종료 업데이트에 대한 활동 로그 보기

자동 종료 설정을 업데이트한 후 VM의 활동 로그에 기록된 활동을 볼 수 있습니다.

1. VM 홈페이지의 왼쪽 메뉴에서 **활동 로그를** 선택합니다.
1. **리소스** 필터를 제거하고, 적절한 **리소스 그룹** 필터를 적용하고, 일정 추가 또는 수정에 대한 항목을 **확인합니다.**

   ![활동 로그에서 일정 추가 또는 수정을 보여 주는 스크린샷](media/devtest-lab-auto-shutdown/activity-log-entry.png)

1. 일정 **추가 또는 수정** 작업을 선택하여 작업에 대한 자세한 정보를 보여 주는 요약 페이지를 엽니다.

## <a name="auto-shutdown-notifications"></a>자동 종료 알림

자동 종료 구성에서 알림을 사용하도록 설정하면 랩 사용자는 VM이 영향을 받는 경우 자동 종료 30분 전에 알림을 받습니다. 이 알림은 사용자가 종료하기 전에 작업을 저장할 수 있는 기회를 제공합니다. 자동 종료 설정에서 이메일 주소를 지정하면 알림이 해당 이메일 주소로 전송됩니다. 설정에서 웹후크를 지정하면 알림이 웹후크 URL로 전송됩니다.

알림은 다른 사용자가 계속 작업해야 하는 경우 각 VM에 대해 다음 작업을 허용하는 링크를 제공할 수도 있습니다.

- 이번에는 자동 종료를 건너뜁니다.
- 1시간 동안 자동 종료를 다시 알림합니다.
- 2시간 동안 자동 종료를 다시 알림.

webhook를 사용하여 사용자 고유의 알림을 구현할 수 있습니다. 특정 이벤트를 구독하는 통합을 설정합니다. 이러한 이벤트 중 하나가 발생하면 HTTP POST 페이로드가 웹후크의 URL로 전송됩니다.

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 Slack과 같은 앱은 webhook를 광범위하게 지원합니다. 웹후크에 응답하는 자세한 내용은 [Azure Functions HTTP 트리거 및 바인딩 개요 및 Azure Logic Apps](../azure-functions/functions-bindings-http-webhook.md) HTTP 트리거 [추가를](../connectors/connectors-native-http.md#add-an-http-trigger)참조하세요.

다음 예제에서는 Logic Apps 사용하여 VM 소유자에게 이메일을 보내는 자동 종료 알림을 구성하는 방법을 보여줍니다.

### <a name="create-a-logic-app-that-sends-email-notifications"></a>메일 알림을 보내는 논리 앱 생성하기

Logic Apps Office 365 및 Twitter와 같은 다른 클라이언트와 서비스를 쉽게 통합할 수 있는 많은 커넥터를 제공합니다. 높은 수준에서 이메일 알림에 대한 논리 앱을 설정하는 단계는 다음과 같습니다.

1. 논리 앱을 만듭니다.
1. 기본 제공 템플릿을 구성합니다.
1. 이메일 클라이언트와 통합합니다.
1. 자동 종료 알림 설정에 사용할 웹후크 URL을 가져옵니다.

시작하려면 다음 단계를 사용하여 Azure에서 논리 앱을 만듭니다.

1. Azure Portal 맨 위 검색 필드에 *논리 앱을* 입력한 다음 논리 **앱을** 선택합니다.

1. **논리 앱** 페이지의 맨 위에서 **추가를** 선택합니다.

1. 논리 **앱 만들기** 페이지에서 다음을 수행합니다.

   - Azure **구독** 을 선택합니다.
   - 리소스 **그룹을** 선택하거나 새 리소스 그룹을 만듭니다.
   - 논리 **앱 이름** 를 입력합니다.
   - 논리 앱에 대한 **지역을** 선택합니다.

   ![논리 앱 만들기 페이지를 보여 주는 스크린샷.](media/devtest-lab-auto-shutdown/new-logic-app-page.png)

1. **검토 + 만들기** 를 선택하고 유효성 검사를 통과하면 **만들기** 를 선택합니다.

1. 배포가 완료되면 **리소스로 이동** 을 선택합니다.

다음으로, 기본 제공 템플릿을 구성합니다.

1. 논리 앱 페이지의 왼쪽 탐색 창에 있는 **배포 도구** 아래에서 **논리 앱 디자이너를** 선택합니다.

1. 상단 메뉴에서 **템플릿을** 선택합니다.

1. **템플릿에서** HTTP **요청/응답을** 선택합니다.

   ![HTTP 요청 응답 템플릿을 보여 주는 스크린샷.](media/devtest-lab-auto-shutdown/select-http-request-response-option.png)

1. **HTTP 요청-응답** 페이지에서 **이 템플릿 사용** 을 선택합니다.

   ![이 템플릿 사용을 선택하는 것을 보여 주는 스크린샷.](./media/devtest-lab-auto-shutdown/select-use-this-template.png)

1. 요청 본문 JSON 스키마 섹션에 다음 **JSON** 코드를 붙여넣습니다.

   ![디자이너의 요청 본문 JSON 스키마를 보여 주는 스크린샷.](media/devtest-lab-auto-shutdown/request-json.png)

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

이제 이메일 클라이언트와 통합합니다.

1. 디자이너에서 새 **단계** 를 선택합니다.

   ![디자이너의 새 단계를 보여 주는 스크린샷.](media/devtest-lab-auto-shutdown/new-step.png)

1. 작업 **선택** 페이지의 검색 필드에 *Office 365 Outlook - 이메일 보내기를* 입력한 다음, **작업에서** **이메일 보내기(V2)를** 선택합니다.

   ![이메일 V2 보내기 옵션을 보여 주는 스크린샷.](media/devtest-lab-auto-shutdown/select-send-email.png)

1. 이메일 **보내기(V2)** 양식에서 **받는 사람,** **제목** 및 **본문** 필드를 입력합니다.

   **동적 콘텐츠 추가를** 선택하여 앱 및 커넥터에서 사용하는 값으로 알림을 자동으로 채웁니다. 예를 들어 **To** 에 **대해 소유자** 를 선택합니다. **제목을** **vmName** 및 **labName** 으로 채웁합니다. **skipUrl** 및 **delayUrl** 값과 같은 콘텐츠를 메시지 본문에 추가합니다.

   ![알림 이메일 예제를 보여 주는 스크린샷.](media/devtest-lab-auto-shutdown/email-options.png)

1. 도구 모음에서 **저장** 을 선택합니다.

이제 웹후크 URL을 복사할 수 있습니다. HTTP **요청을 받은 경우** 단계를 선택한 다음 복사 단추를 선택하여 HTTP POST URL을 클립보드에 복사합니다. 이 웹후크 URL을 자동 종료 알림 설정에 붙여넣습니다.

![웹후크 URL 복사를 보여 주는 스크린샷](media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="next-steps"></a>다음 단계

- [랩 가상 머신 자동 시작](devtest-lab-auto-startup-vm.md)
- [Azure DevTest Labs에서 랩 정책 정의](devtest-lab-set-lab-policy.md)
- [Azure Logic Apps에서 인바운드 HTTPS 요청 수신 및 응답](/azure/connectors/connectors-native-reqres)
