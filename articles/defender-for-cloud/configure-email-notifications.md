---
title: 클라우드 경고에 대해 Microsoft Defender에 대한 이메일 알림 구성
description: 클라우드 보안 경고 이메일에 대해 Microsoft Defender를 미세 조정하는 방법을 알아봅니다.
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: quickstart
ms.date: 11/09/2021
ms.author: memildin
ms.custom: mode-other
ms.openlocfilehash: fcbf3ee37252f50fe2ca96cce070402f670bbc94
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133042027"
---
# <a name="configure-email-notifications-for-security-alerts"></a>보안 경고에 대한 이메일 알림 구성 

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

보안 경고는 조직의 적절한 사람에게 전달되어야 합니다. 기본적으로 클라우드용 Microsoft Defender는 구독에 대해 심각도가 높은 경고가 트리거될 때마다 구독 소유자에게 이메일을 보냅니다. 이 페이지에서는 이러한 알림을 사용자 지정하는 방법을 설명합니다.

Defender for Cloud의 **이메일 알림** 설정 페이지를 사용하여 다음을 포함함 알림 이메일에 대한 기본 설정을 정의합니다.

- **알려야 하는 *사람*** - 특정 개인 또는 구독에 대해 지정된 Azure 역할이 있는 사람에게 이메일을 보낼 수 있습니다. 
- **알려야 하는 *내용*** - Defender for Cloud에서 보내야 하는 알림의 심각도 수준을 수정합니다.

경고 피로 방지를 위해 Defender for Cloud는 보내는 메일의 볼륨을 제한합니다. 각 구독에 대해 Defender for Cloud는 다음을 전송합니다.

- **높은 심각도** 경고에 대해 **6시간** 마다 최대 1개의 이메일(하루에 4개 이메일)
- **중간 심각도** 경고에 대해 **12시간** 마다 최대 1개의 이메일(하루에 2개 이메일)
- **낮은 심각도** 경고에 대해 **24시간** 마다 최대 1개의 이메일

:::image type="content" source="./media/configure-email-notifications/email-notification-settings.png" alt-text="보안 경고에 대한 이메일을 받을 연락처의 세부 정보 구성" :::
 
## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|Free|
|필요한 역할 및 권한:|**보안 관리자**<br>**구독 소유자** |
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 국가(Azure Government, Azure 중국 21Vianet)|
|||


## <a name="customize-the-security-alerts-email-notifications-via-the-portal"></a>포털을 통해 보안 경고 이메일 알림 사용자 지정<a name="email"></a>
개인 또는 특정 Azure 역할을 가진 모든 사용자에게 이메일 알림을 보낼 수 있습니다.

1. Defender for Cloud의 **환경 설정** 영역에서 관련 구독을 선택하고 **이메일 알림** 을 엽니다.

1. 다음 옵션 중 하나 또는 둘 모두를 사용하여 알림 수신자를 정의합니다.

    - 드롭다운 목록에서 사용 가능한 역할 중 하나를 선택합니다.
    - 특정 이메일 주소를 쉼표로 구분하여 입력합니다. 입력할 수 있는 이메일 주소 수에는 제한이 없습니다.

1. 보안 연락처 정보를 구독에 적용하려면 **저장** 을 선택합니다.

## <a name="customize-the-alerts-email-notifications-through-the-api"></a>API를 통해 경고 이메일 알림 사용자 지정
제공된 REST API를 통해 이메일 알림을 관리할 수도 있습니다. 자세한 내용은 [SecurityContacts API 설명서](/rest/api/securitycenter/securitycontacts)를 참조하세요.

다음은 보안 연락처 구성을 만들 때 PUT 요청의 본문 예제입니다.

URI: `https://management.azure.com/subscriptions/<SubscriptionId>/providers/Microsoft.Security/securityContacts/default?api-version=2020-01-01-preview`

```json
{
    "properties": {
        "emails": admin@contoso.com;admin2@contoso.com,
        "notificationsByRole": {
            "state": "On",
            "roles": ["AccountAdmin", "Owner"]
        },
        "alertNotifications": {
            "state": "On",
            "minimalSeverity": "Medium"
        },
        "phone": ""
    }
}
```


## <a name="see-also"></a>참고 항목
보안 알림에 대한 자세한 내용은 다음 페이지를 참조하세요.

- [보안 경고 - 참조 가이드](alerts-reference.md)--Microsoft Defender for Cloud의 위협 보호 모듈에서 볼 수 있는 보안 경고에 대해 알아보기
- [Microsoft Defender for Cloud에서 보안 경고 관리 및 대응](managing-and-responding-alerts.md)--보안 경고를 관리하고 대응하는 방법 알아보기
- [워크플로 자동화](workflow-automation.md)--사용자 지정 알림 논리를 사용하여 경고에 대한 응답 자동화
