---
title: App Service Environment 만들기
description: App Service Environment를 만드는 방법을 알아봅니다.
author: madsd
ms.topic: article
ms.date: 11/15/2021
ms.author: madsd
ms.openlocfilehash: ae243842f5201b7d8f2bfa3adcb42c5146c885d2
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525028"
---
# <a name="create-an-app-service-environment"></a>App Service Environment 만들기
> [!NOTE]
> 이 문서에서는 격리된 v2 App Service 요금제와 함께 사용되는 App Service Environment v3에 대해 설명합니다.
> 

[ASE(App Service Environment)][Intro]는 Azure VNet(Virtual Network)에서 실행되는 App Service의 단일 테넌트 배포입니다. ASE를 배포하려면 한 개의 서브넷을 사용해야 합니다. ASE 이외의 항목에는 서브넷을 사용할 수 없습니다. 

## <a name="before-you-create-your-ase"></a>ASE를 만들기 전에

ASE를 만든 후에는 다음 사항을 변경할 수 없습니다.

- 위치
- 구독
- Resource group
- 사용되는 Azure VNet(Virtual Network)
- 사용되는 서브넷
- 서브넷 크기
- ASE의 이름

서브넷은 ASE 크기를 스케일링하는 최대 크기를 수용할 만큼 충분히 커야 합니다. 만든 후에는 변경할 수 없으므로 최대 규모 요구량을 지원할 수 있는 충분한 서브넷을 선택합니다. 256개 주소를 사용하는 /24가 권장되는 크기입니다.

## <a name="deployment-considerations"></a>배포 고려 사항

ASE를 배포하기 전에 고려해야 할 두 가지 중요한 사항이 있습니다.

- VIP 유형
- 배포 유형

내부 및 외부의 두 가지 VIP 유형이 있습니다. 내부 VIP를 사용하면 ASE 서브넷의 주소에서 ASE에 앱이 연결되고 앱은 공용 DNS에 없습니다. 포털에서 만드는 동안 ASE에 대한 Azure 프라이빗 DNS 영역을 만드는 옵션이 있습니다. 외부 VIP를 사용하면 앱이 공용 인터넷 연결 주소에 있고 앱이 공용 DNS에 있습니다. 

세 가지 배포 유형이 있습니다.

- 단일 영역
- 영역 중복
- 호스트 그룹

단일 영역 ASE는 ASEv3을 사용할 수 있는 모든 지역에서 사용할 수 있습니다. 단일 영역 ASE가 있는 경우 Windows 격리 v2의 인스턴스 하나에 대한 최소 App Service 계획 인스턴스 요금이 부과됩니다. 하나 이상의 인스턴스가 있는 즉시 해당 요금이 사라집니다. 추가 요금이 아닙니다.

영역 중복 ASE에서 앱은 동일한 지역의 세 영역에 분산됩니다. 영역 중복 ASE는 주로 가용성 영역을 지원하는 지역에 의해 제한되는 ASE 지원 지역의 하위 집합에서 사용할 수 있습니다. 영역 중복 ASE가 있는 경우 App Service 계획의 가장 작은 크기는 세 개의 인스턴스입니다. 이를 통해 각 가용성 영역에 인스턴스가 있는지 확인합니다. App Service 계획은 한 번에 하나 이상의 인스턴스를 확장할 수 있습니다. 크기 조정은 3단위일 필요는 없지만 총 인스턴스가 3의 배수인 경우 앱은 모든 가용성 영역에서만 균형이 조정됩니다. 영역 중복 ASE는 인프라의 3배를 가지며, 세 영역 중 두 영역이 어떤 이유로든 중단되더라도 워크로드를 계속 사용할 수 있도록 영역 중복 구성 요소로 만들어집니다. 시스템 요구가 증가함에 따라 영역 중복 ASE에 대한 최소 요금은 9개 인스턴스입니다. ASEv3에 총 App Service 계획 인스턴스가 9개 미만인 경우 차이는 Windows I1v2로 청구됩니다. 인스턴스가 9개 이상인 경우 영역 중복 ASE에 추가 요금이 부과되지 않습니다. 영역 중복성에 대한 자세한 내용은 [지역 및 가용성 영역 을 읽어보세요.](./overview-zone-redundancy.md)

호스트 그룹 배포에서 앱은 전용 호스트 그룹에 배포됩니다. 전용 호스트 그룹은 영역 중복이 아닙니다. 전용 호스트 그룹 배포를 사용하면 전용 하드웨어에 ASE를 배포할 수 있습니다. 전용 호스트 그룹에서 ASE를 사용하기 위한 최소 인스턴스 요금은 없지만 ASE를 프로비전할 때 호스트 그룹에 대한 비용을 지불해야 합니다. 그 외에도 플랜을 만들고 규모를 확장할 때 할인된 App Service 플랜 요금을 지불합니다. App Service 계획과 인프라 역할 모두에 사용되는 전용 호스트 배포에서 사용할 수 있는 코어 수는 한정되어 있습니다. ASE의 전용 호스트 배포는 ASE에서 일반적으로 사용할 수 있는 총 인스턴스 수 200개에 도달할 수 없습니다. 가능한 총 인스턴스 수는 App Service 계획 인스턴스의 총 수와 인프라 역할의 부하 기반 수와 관련이 있습니다.

## <a name="creating-an-ase-in-the-portal"></a>포털에 ASE 생성

1. ASE를 만들려면 마켓플레이스에서 **App Service Environment v3** 을 검색합니다.

2. 기본 사항: 구독을 선택하고, 리소스 그룹을 선택하거나 만들고, ASE의 이름을 입력합니다.  가상 IP 유형의 유형을 선택합니다. 내부를 선택하면 인바운드 ASE 주소가 ASE 서브넷의 주소가 됩니다. 외부를 선택하면 인바운드 ASE 주소가 공용 인터넷 연결 주소가 됩니다. ASE 이름은 ASE의 도메인 접미사에도 사용됩니다. ASE 이름이 *contoso* 이고 내부 VIP ASE가 있는 경우 도메인 접미사는 *contoso.appserviceenvironment.net* 이 됩니다. ASE 이름이 *contoso* 이고 외부 VIP가 있는 경우 도메인 접미사는 *contoso.p.azurewebsites.net* 이 됩니다. 

    ![App Service Environment 만들기 기본 사항 탭](./media/creation/creation-basics.png)

3. 호스팅: 호스트 그룹 배포에 대해 *사용* 또는 *사용 안 함* 을 선택합니다. 호스트 그룹 배포는 전용 하드웨어 배포를 선택하는 데 사용됩니다. 사용을 선택하면 ASE가 전용 하드웨어에 배포됩니다. 전용 하드웨어로 배포하면 ASE가 생성되는 동안 전체 전용 호스트에 대해 요금이 청구된 다음, App Service 요금제 인스턴스 가격이 할인됩니다.

    ![App Service Environment 호스팅 선택](./media/creation/creation-hosting.png)

4. 네트워킹: Virtual Network를 선택하거나 만들고, 서브넷을 선택하거나 만듭니다. 내부 VIP ASE를 만드는 경우 도메인 접미사가 ASE를 가리키도록 Azure DNS 프라이빗 영역을 구성할 수 있습니다. DNS를 수동으로 구성하는 방법에 대한 자세한 내용은 [App Service Environment 사용][UsingASE] 아래의 DNS 섹션에있습니다.

    ![App Service Environment 네트워킹 선택 영역](./media/creation/creation-networking.png)

5. 검토 및 만들기: 구성이 올바른지 확인하고 만들기를 선택합니다. ASE를 만드는 데 최대 2시간이 걸릴 수 있습니다. 

ASE 만들기가 완료되면 앱을 만들 때 위치로 선택할 수 있습니다. 새 ASE에서 앱 만들기 또는 ASE 관리에 대한 자세한 내용은 [App Service Environment 사용][UsingASE]을 참조하세요

## <a name="dedicated-hosts"></a>전용 호스트

ASE는 일반적으로 다중 테넌트 하이퍼바이저에서 프로비저닝되는 VM에 배포됩니다. 하드웨어를 포함하여 전용 시스템에 배포해야 하는 경우 ASE를 전용 호스트에 프로비저닝할 수 있습니다. 전용 호스트는 중복성을 보장하기 위해 한 쌍으로 제공됩니다. 전용 호스트 기반 ASE 배포는 일반적인 경우와 다르게 가격이 책정됩니다. 전용 호스트에 대한 요금이 청구되고 각 App Service 요금제 인스턴스에 대한 추가의 요금이 청구됩니다. 호스트 그룹의 배포는 영역 중복이 아닙니다. 전용 호스트에 배포하려면 호스팅 탭에서 호스트 그룹 배포에 대해 **사용** 을 선택합니다.

<!--Links-->
[Intro]: ./overview.md
[UsingASE]: ./using.md
