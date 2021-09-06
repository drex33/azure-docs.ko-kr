---
title: 구독 관리
description: 구독은 관리되는 커밋 디바이스로 구성되며 필요에 따라 온보딩 또는 오프보딩할 수 있습니다.
ms.date: 08/10/2021
ms.topic: how-to
ms.openlocfilehash: 41240bd3b05c08d8acd3484bbe32fc99b6590d49
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122531079"
---
# <a name="manage-defender-for-iot-subscriptions"></a>Defender for IoT 구독 관리

## <a name="about-subscriptions"></a>구독 정보

Defender for IoT 배포는 Azure Defender for IoT 계정 구독을 통해 관리됩니다.
[Azure Defender for IoT 포털](https://ms.portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)에서 Defender for IoT에 대한 구독을 온보딩, 편집 및 오프보딩할 수 있습니다.

각 구독에 대해 여러 *커밋된 디바이스* 를 정의하라는 메시지가 표시됩니다. 커밋된 디바이스는 기업에서 모니터링하는 대략적인 디바이스 수입니다. 

### <a name="subscription-billing"></a>구독 청구

각 구독과 연결된 커밋된 디바이스 수에 따라 요금이 청구됩니다.

Azure Defender for IoT 청구 주기는 달력 월을 따릅니다. 해당 월 동안 커밋된 디바이스에 대한 변경 내용은 업데이트를 확인한 후 1시간 후에 구현되며 월별 청구서에 반영됩니다. 구독 *오프보딩* 도 오프보딩을 확인한 후 1시간 후에 적용됩니다.

기업에는 두 개 이상의 결제 엔터티가 있을 수 있습니다. 이 경우 두 개 이상의 구독을 온보딩할 수 있습니다.

구독하기 전에 구독을 다루려는 디바이스의 수를 파악해야 합니다.

사용자는 30일 동안 제한된 수의 디바이스 모니터링을 지원하는 평가판 구독으로 작업할 수도 있습니다.
커밋된 디바이스 가격에 대한 [Azure Defender 가격 책정](https://azure.microsoft.com/pricing/details/azure-defender/) 정보를 참조하세요.

## <a name="requirements"></a>요구 사항

구독을 온보딩하기 전에 다음을 확인합니다.

- Azure 계정이 설정되었는지 여부
- 필요한 Azure 사용자 권한이 있는지 여부

### <a name="azure-account-subscription-requirements"></a>Azure 계정 구독 요구 사항

Azure Defender for IoT를 시작하려면 Microsoft Azure 계정 구독이 있어야 합니다.

구독이 없는 경우 체험 계정으로 등록할 수 있습니다. 자세한 내용은 https://azure.microsoft.com/free/를 참조하세요.

Azure 구독에 대한 액세스 권한이 이미 있지만 Defender for IoT에 구독할 때 목록에 없는 경우 계정 정보를 확인하고 구독 소유자에게 권한을 확인합니다. https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade을 참조하세요.

### <a name="user-permission-requirements"></a>사용자 권한 요구 사항

Azure **구독 소유자** 및 **구독 기여자** 는 Azure Defender for IoT 구독을 온보딩, 업데이트 및 오프보딩할 수 있습니다.

## <a name="onboard-a-trial-subscription"></a>평가판 구독 온보딩

Defender for IoT를 평가하려는 경우 평가판 구독을 사용할 수 있습니다. 평가판은 30일 동안 유효하며 커밋된 1,000개의 디바이스를 지원합니다. 평가판을 사용하면 네트워크에 Defender for IoT 센서를 하나 더 배포할 수 있습니다. 센서를 사용하여 트래픽을 모니터링하고, 데이터를 분석하고, 경고를 생성하고, 네트워크 위험 및 취약성에 대해 알아봅니다. 평가판을 사용하면 온-프레미스 관리 콘솔을 다운로드하여 센서에서 생성된 집계된 정보를 볼 수도 있습니다.

이 섹션에서는 센서에 대한 평가판 구독을 만드는 방법을 설명합니다.

**평가판 구독을 만들려면 다음을 수행합니다.**

1. [Azure Defender for IoT 포털](https://ms.portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)로 이동합니다.
1. **시작** 을 선택합니다.
1. **구독 등록** 을 선택합니다.
1. 가격 책정 페이지에서 **평가판 시작** 을 선택합니다.
1. 평가판 구독 온보딩 창에서 구독을 선택한 다음, **평가** 를 선택합니다.
1. 평가를 확인합니다.
1. 필요한 경우 센서를 온보딩하거나 센서를 설정합니다.

## <a name="onboard-a-subscription"></a>구독 온보딩

이 섹션에서는 구독을 온보딩하는 방법을 설명합니다.

**구독을 온보딩하려면 다음을 수행합니다.**

1. [Azure Defender for IoT 포털](https://ms.portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)로 이동합니다.
1. **시작** 을 선택합니다.
1. **구독 등록** 을 선택합니다.
1. 가격 책정 페이지에서 **구독** 을 선택합니다.
1. **구독 온보딩** 창에서 구독을 선택하고 드롭다운 메뉴에서 커밋된 디바이스 수를 선택합니다.

   :::image type="content" source="media/how-to-manage-subscriptions/onboard-subscription.png" alt-text="구독 및 커밋된 디바이스 수를 선택합니다.":::

1. **구독** 을 선택합니다.
1. 구독을 확인합니다.
1. 아직 수행하지 않은 경우 센서를 온보딩하거나 센서를 설정합니다.

## <a name="update-committed-devices-in-a-subscription"></a>구독에서 커밋된 디바이스 업데이트

더 많은 커밋된 디바이스 수 또는 더 적은 커밋된 디바이스 수로 구독을 업데이트해야 할 수 있습니다. 예를 들어 기존 사이트 범위를 늘리거나, 예상보다 더 많은 디바이스를 검색하거나, 스위치 추가와 같은 네트워크 변경이 있는 경우 더 많은 디바이스에서 모니터링이 필요할 수 있습니다.

**구독을 업데이트하려면 다음을 수행합니다.**
1. [Azure Defender for IoT 포털](https://ms.portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)로 이동합니다.
1. **구독 등록** 을 선택합니다.
1. 구독을 선택한 다음, 점 3개를 선택합니다. (...).
1. **편집** 을 선택합니다.
1. 커밋된 디바이스를 업데이트하고 **저장** 을 선택합니다.
2. 열리는 확인 대화 상자에서 **확인** 을 선택합니다.
디바이스 약정 변경 내용은 변경 내용을 확인한 후 1시간 후에 적용됩니다. 이러한 변경 내용에 대한 청구는 변경 내용을 확인한 후 월초에 반영됩니다.

온-프레미스 관리 콘솔에 새 활성화 파일을 업로드해야 합니다. 활성화 파일은 커밋된 디바이스의 새 수를 반영합니다. [활성화 파일 업로드](how-to-manage-the-on-premises-management-console.md#upload-an-activation-file)를 참조하세요.
## <a name="offboard-a-subscription"></a>구독 오프보딩

예를 들어 새 결제 엔터티로 작업해야 하는 경우 구독을 오프보딩해야 할 수 있습니다. 구독 오프보딩은 오프보딩을 확인한 후 1시간 후에 적용됩니다.
예정된 월별 청구서에는 이 변경 내용이 반영됩니다.

구독을 오프보딩하기 전에 구독과 연결된 모든 센서를 제거합니다. 센서를 삭제하는 방법에 대한 자세한 내용은 [센서 삭제](how-to-manage-sensors-on-the-cloud.md#delete-a-sensor)를 참조하세요.

**구독을 오프보딩하려면 다음을 수행합니다.**

1. [Azure Defender for IoT 포털](https://ms.portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)로 이동합니다.
1. 구독을 선택한 다음, 점 3개를 선택합니다. (...).

1. **구독 오프보딩** 을 선택합니다.

1. 확인 팝업에서 확인란을 선택하여 구독과 관련된 모든 센서를 삭제했는지 확인합니다.

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/offboard-popup.png" alt-text="확인란을 선택하고 오프보딩을 선택하여 센서를 오프보딩합니다.":::

1. **오프보딩** 을 선택합니다.

## <a name="apply-a-new-subscription"></a>새 구독 적용

비즈니스 고려 사항에 따라 현재 사용 중인 구독과 다른 구독을 배포에 적용해야 할 수 있습니다. 구독을 변경하는 경우 새 센서 활성화 파일을 업로드해야 합니다. 파일에는 구독 만료 날짜에 대한 정보가 포함되어 있습니다.

**새 구독을 적용하려면 다음을 수행합니다.**

1. 현재 사용 중인 구독을 삭제합니다.
1. 새 구독을 선택합니다.
1. 구독과 연결된 센서에 대한 활성화 파일을 다운로드합니다.
1. 활성화 파일을 센서에 업로드합니다.

## <a name="next-steps"></a>다음 단계

- [Defender for IoT 포털에서 센서 관리](how-to-manage-sensors-on-the-cloud.md)

- [온-프레미스 관리 콘솔 활성화 및 설정](how-to-activate-and-set-up-your-on-premises-management-console.md)
