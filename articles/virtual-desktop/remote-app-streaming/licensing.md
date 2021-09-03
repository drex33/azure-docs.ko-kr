---
title: 원격 앱 스트리밍을 위한 사용자별 액세스 가격 책정 Azure Virtual Desktop 라이선스 부여 - Azure
description: 원격 앱 스트리밍을 위한 Azure Virtual Desktop 라이선스 고려 사항의 개요입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 2fb4ec6ebe5960a6a782b3d76fe2aaec91603302
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113799205"
---
# <a name="understanding-licensing-and-per-user-access-pricing"></a>라이선스 및 사용자별 액세스 가격 책정 이해

이 문서에서는 Azure Virtual Desktop을 사용하여 외부 사용자에게 원격 애플리케이션을 스트림하기 위한 라이선스 요구 사항을 설명합니다. 이 문서에서는 외부 사용자와 내부 사용자에 대한 Azure Virtual Desktop 라이선스 부여의 차이점, 사용자별 액세스 가격 책정의 자세한 작동 방식, Azure Virtual Desktop에서 사용할 계획인 다른 제품에 라이선스를 부여하는 방법을 알아봅니다.

## <a name="licensing-azure-virtual-desktop-for-external-users"></a>외부 사용자에 대해 Azure Virtual Desktop 라이선스 부여

두 가지 방법으로 Azure Virtual Desktop을 사용할 수 있습니다. 각 방법에는 서로 다른 라이선스 요구 사항이 있습니다.

- 내부 사용자에게 RemoteApp 및 데스크톱을 스트림하는 경우. 예를 들어, 제조 회사 Fabrikam, Inc.는 Azure Virtual Desktop을 사용하여 Fabrikam의 직원에게 가상 워크스테이션과 LOB(기간 업무) 앱에 대한 액세스 권한을 제공할 수 있습니다. 이 경우 Fabrikam은 Azure Virtual Desktop에 액세스할 각 직원을 위해 [Azure Virtual Desktop 가격 책정](https://azure.microsoft.com/pricing/details/virtual-desktop/)에 나열된 적격한 라이선스 중 하나를 구매해야 합니다.

- 외부 사용자에게 RemoteApp 및 데스크톱을 스트림하는 경우. 예를 들어, Contoso라는 소프트웨어 공급업체는 Azure Virtual Desktop을 사용하여 Contoso의 고객(Contoso 직원이 아닌 사용자)에게 Contoso 생산성 앱의 원격 스트림을 제공할 수 있습니다. 이 경우 Contoso는 Azure Virtual Desktop의 사용자별 액세스 가격 책정에 등록해야 합니다. 이 라이선스 유형을 사용하여 Contoso는 매달 Azure Virtual Desktop에 액세스하는 사용자 수를 기준으로 Azure 미터를 통해 해당 사용자를 대신하여 Azure Virtual Desktop 액세스 권한에 대한 요금을 납부할 수 있습니다. 배포에 있는 사용자는 Azure Virtual Desktop에 액세스하기 위해 Microsoft 365와 같은 별도의 라이선스가 필요하지 않습니다.

## <a name="per-user-access-pricing-for-azure-virtual-desktop"></a>Azure Virtual Desktop에 대한 사용자별 액세스 가격 책정

사용자별 액세스 가격 책정을 통해 외부 사용자를 대신하여 Azure Virtual Desktop 액세스 권한에 대한 요금을 납부할 수 있습니다. 외부 사용자를 위해 호환 배포를 빌드하려면 사용자별 액세스 가격 책정에 등록해야 합니다. 자세한 내용은 [사용자별 액세스 가격 책정에 등록](per-user-access-pricing.md)을 참조하세요.

가상 머신, 스토리지, 기타 Azure 서비스의 요금을 기반으로 등록된 Azure 구독을 통해 사용자별 액세스 가격 책정에 대한 요금을 납부합니다. 청구 주기별로, 실제로 서비스를 사용하는 사용자에 대한 요금만 납부합니다. 해당 월에 한 번 이상 Azure Virtual Desktop에 연결하는 사용자만 액세스 요금이 부과됩니다.

Azure Virtual Desktop 사용자별 액세스 가격 책정에는 두 가지 가격 책정 계층이 있습니다. 요금은 사용자가 연결된 [애플리케이션 그룹](../environment-setup.md#app-groups) 유형에 따라 청구 주기별로 자동으로 결정됩니다.

- 첫 번째 가격 계층을 “앱”이라고 합니다. 이 균일 가격은 하나 이상의 RemoteApp 애플리케이션 그룹과 제로 데스크톱 애플리케이션 그룹에 액세스하는 각 사용자에 대해 청구됩니다.
- 두 번째 계층은 “앱 + 데스크톱”입니다. 이 균일 가격은 하나 이상의 데스크톱 애플리케이션 그룹에 액세스하는 각 사용자에 대해 청구됩니다.
- 사용자가 애플리케이션 그룹에 액세스하지 않으면 요금이 부과되지 않습니다.

가격에 관한 자세한 내용은 [Azure Virtual Desktop 가격 책정](https://azure.microsoft.com/pricing/details/virtual-desktop/)을 참조하세요.

각 가격 계층에는 균일 사용자별 액세스 요금이 있습니다. 예를 들어, 사용자는 해당 청구 주기 동안 서비스를 사용하는 시기나 기간(시간)에 관계없이 구독에 대해 동일한 요금이 부과됩니다.

Azure Virtual Desktop은 Azure Virtual Desktop 액세스에 대한 자격을 부여하는 별도의 할당된 라이선스를 보유한 사용자에게도 요금을 부과합니다. 내부 사용자를 위해 적격 라이선스를 구매하는 경우 해당 사용자에 대해 요금을 실제로 두 번 납부하지 않도록 사용자별 액세스 가격 책정에 등록되지 않은 별도의 구독을 통해 Azure Virtual Desktop에 대한 액세스 권한을 부여하는 것이 좋습니다.

Azure Virtual Desktop은 지정된 청구 기간에 지정된 사용자에 대해 최대 하나의 액세스 요금을 부과합니다. 따라서 배포에서 사용자 Alice에게 동일한 테넌트의 두 가지 Azure 구독에 있는 Azure Virtual Desktop 리소스에 대한 액세스 권한을 부여하는 경우 Alice가 액세스하는 첫 번째 구독에만 사용 요금이 부과됩니다.

## <a name="comparing-licensing-options"></a>라이선스 옵션 비교

[Azure Virtual Desktop에 대한 사용자별 액세스 가격 책정](#per-user-access-pricing-for-azure-virtual-desktop)에 설명된 대로 Azure Virtual Desktop에 대해 선택할 수 있는 두 가지 라이선스 유형은 다음과 같습니다.

- Windows 또는 Microsoft 365 라이선스:
   - 내부 사용자에 대해서만 Azure Virtual Desktop 액세스 권한 부여
   - 구독을 통해 미리 납부함
   - 사용자 동작에 관계없이 매월 사용자별 동일한 비용
   - 일부 다른 Microsoft 제품과 서비스에 대한 자격을 포함합니다.

- 사용자별 액세스 가격 책정:
   - 외부 사용자에 대해서만 Azure Virtual Desktop 액세스 권한 부여
   - Azure 미터를 통해 종량제
   - 사용자 동작에 따라 매월 사용자별 동적 비용
   - Azure Virtual Desktop에 대한 액세스 권한만 포함

## <a name="licensing-other-products-and-services-for-use-with-azure-virtual-desktop"></a>Azure Virtual Desktop에서 사용할 다른 제품 및 서비스 라이선스 부여

Azure Virtual Desktop 사용자별 액세스 라이선스가 Windows 또는 Microsoft 365 라이선스를 완전히 대체하지는 않습니다. 사용자별 라이선스는 Azure Virtual Desktop에 대한 액세스 권한만 부여하며 Microsoft Office, Microsoft Defender 또는 유니버설 인쇄는 포함하지 않습니다. 즉, 사용자별 라이선스를 선택하는 경우 Azure Virtual Desktop 환경에서 사용자에게 액세스 권한을 부여하려면 다른 제품과 서비스의 라이선스를 별도로 부여해야 합니다.

## <a name="next-steps"></a>다음 단계

이제 라이선스 가격 책정 옵션에 익숙해졌으므로 Azure Virtual Desktop 환경 계획을 시작할 수 있습니다. 도움이 될 수 있는 몇 가지 문서는 다음과 같습니다.

- [사용자별 액세스 가격으로 구독 등록](per-user-access-pricing.md)
- [Azure Virtual Desktop의 사용자별 앱 스트리밍 비용 예상](streaming-costs.md)
- 첫 번째 배포 설정을 시작할 준비가 되면 [자습서](../create-host-pools-azure-marketplace.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)를 시작합니다.
