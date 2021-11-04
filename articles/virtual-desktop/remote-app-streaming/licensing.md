---
title: 원격 앱 스트리밍에 대한 사용자별 Azure Virtual Desktop 액세스 가격 책정 이해 - Azure
description: 원격 앱 스트리밍을 위한 Azure Virtual Desktop 라이선스 고려 사항의 개요입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 563d49ff66bdcd81a37a0bf1b12e910f16f2f5ff
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131036697"
---
# <a name="understanding-licensing-and-per-user-access-pricing"></a>라이선스 및 사용자별 액세스 가격 책정 이해

이 문서에서는 Azure Virtual Desktop을 사용하여 외부 사용자에게 원격 애플리케이션을 스트림하기 위한 라이선스 요구 사항을 설명합니다. 이 문서에서는 외부 사용자와 내부 사용자에 대한 Azure Virtual Desktop 라이선스 부여의 차이점, 사용자별 액세스 가격 책정의 자세한 작동 방식, Azure Virtual Desktop에서 사용할 계획인 다른 제품에 라이선스를 부여하는 방법을 알아봅니다.

## <a name="internal-users-and-external-users"></a>내부 사용자 및 외부 사용자

Azure Virtual Desktop을 사용하여 가상화된 인프라를 제공하는 컨텍스트에서 **내부 사용자는** 조직의 구성원(예: 비즈니스 직원 또는 학교 학생)을 가리킵니다. **외부 사용자는** 조직의 구성원이 아닙니다(예: 비즈니스 고객).

>[!NOTE]
>외부 *사용자를* 외부 ID 와 혼동하지 않도록 *주의하십시오.* Azure Virtual Desktop은 현재 게스트 계정 또는 B2B(Business-to-Business) ID를 포함한 외부 ID를 지원하지 않습니다. 내부 사용자 또는 외부 사용자에게 Azure Virtual Desktop을 제공하는지 여부에 관계없이 해당 사용자에 대한 ID를 직접 만들고 관리해야 합니다. 사용자별 액세스 가격 책정은 Azure Virtual Desktop에서 게스트 사용자 계정을 사용하도록 설정하는 방법이 아닙니다. 자세한 내용은 [아키텍처 권장 사항을 참조하세요.](architecture-recs.md)

Azure Virtual Desktop 라이선스는 내부 사용자와 외부 사용자에 대해 다르게 작동합니다. 다음 예제를 살펴보세요.

- Fabrikam, Inc.라는 제조 회사는 Azure Virtual Desktop을 사용하여 Fabrikam의 직원(내부 사용자)에게 가상 워크스테이션 및 기간 업무 앱에 대한 액세스 권한을 제공할 수 있습니다. Fabrikam은 내부 사용자에게 서비스를 제공하므로 Fabrikam은 Azure Virtual [Desktop에](https://azure.microsoft.com/pricing/details/virtual-desktop/) 액세스할 각 직원에 대해 Azure Virtual Desktop 가격 책정에 나열된 적격 라이선스 중 하나를 구매해야 합니다.

- Contoso라는 소프트웨어 공급업체는 Azure Virtual Desktop을 사용하여 Contoso의 고객(외부 사용자)에게 Contoso 생산성 앱의 원격 스트림을 판매할 수 있습니다. Contoso는 외부 사용자에게 서비스를 제공하므로 Contoso는 Azure Virtual Desktop의 사용자별 액세스 가격 책정에 등록해야 합니다. 이를 통해 Contoso는 Contoso의 배포에 연결하는 외부 사용자를 대신하여 Azure Virtual Desktop 액세스 권한을 지불할 수 있습니다. 사용자는 Azure Virtual Desktop에 액세스하기 위해 Microsoft 365 같은 별도의 라이선스가 필요하지 않습니다. Contoso는 여전히 이러한 외부 사용자에 대한 ID를 만들고 관리해야 합니다.

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

다음은 선택할 수 있는 Azure Virtual Desktop에 대한 두 가지 유형의 라이선스에 대한 요약입니다.

- 적격 Windows 또는 Microsoft 365 라이선스:
  - 내부 사용자에게만 Azure Virtual Desktop 액세스 권한 부여
  - 구독을 통해 미리 지불
  - 사용자 동작에 관계없이 매월 사용자별 동일한 비용
  - 일부 다른 Microsoft 제품과 서비스에 대한 자격을 포함합니다.

- 사용자별 액세스 가격 책정:
  - 외부 사용자에 대해서만 Azure Virtual Desktop 액세스 권한 부여
  - Azure 미터를 통해 종량제
  - 매월 사용자당 비용은 사용자 동작에 따라 달라집니다.
  - Azure Virtual Desktop에 대한 액세스 권한만 포함

## <a name="licensing-other-products-and-services-for-use-with-azure-virtual-desktop"></a>Azure Virtual Desktop에서 사용할 다른 제품 및 서비스 라이선스 부여

Azure Virtual Desktop 사용자별 액세스 라이선스가 Windows 또는 Microsoft 365 라이선스를 완전히 대체하지는 않습니다. 사용자별 라이선스는 Azure Virtual Desktop에 대한 액세스 권한만 부여하며 Microsoft Office, Microsoft Defender 또는 유니버설 인쇄는 포함하지 않습니다. 즉, 사용자별 라이선스를 선택하는 경우 Azure Virtual Desktop 환경에서 사용자에게 액세스 권한을 부여하려면 다른 제품과 서비스의 라이선스를 별도로 부여해야 합니다.

## <a name="next-steps"></a>다음 단계

이제 라이선스 가격 책정 옵션에 익숙해졌으므로 Azure Virtual Desktop 환경 계획을 시작할 수 있습니다. 도움이 될 수 있는 몇 가지 문서는 다음과 같습니다.

- [사용자별 액세스 가격으로 구독 등록](per-user-access-pricing.md)
- [Azure Virtual Desktop의 사용자별 앱 스트리밍 비용 예상](streaming-costs.md)
- 첫 번째 배포 설정을 시작할 준비가 되면 [자습서](../create-host-pools-azure-marketplace.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)를 시작합니다.
