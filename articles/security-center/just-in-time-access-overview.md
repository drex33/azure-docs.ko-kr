---
title: Microsoft Defender for Cloud의 just-in-time 가상 컴퓨터 액세스 이해
description: 이 문서에서는 Microsoft Defender for Cloud의 just-in-time VM 액세스를 통해 Azure virtual machines에 대 한 액세스를 제어 하는 방법을 설명 합니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 20475db8bd2ca96ee95d4a1011ad8bdb1bec47cd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103121"
---
# <a name="understanding-just-in-time-jit-vm-access"></a>JIT(Just-In-Time) VM 액세스 이해

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 페이지에서는 클라우드의 JIT (just-in-time) VM 액세스 기능 및 권장 구성의 논리에 대 한 Microsoft Defender의 원칙을 설명 합니다.

Azure Portal (클라우드 또는 Azure Virtual Machines 용 Defender)를 사용 하 여 Vm에 JIT를 적용 하거나 프로그래밍 방식으로 관리 하는 방법을 알아보려면 JIT를 사용 하 여 [관리 포트를 보호 하는 방법](just-in-time-access-usage.md)을 참조 하세요.


## <a name="the-risk-of-open-management-ports-on-a-virtual-machine"></a>가상 머신에서 열린 관리 포트의 위험

위협 행위자는 RDP 또는 SSH와 같은 개방형 관리 포트를 사용하여 액세스 가능한 머신을 적극적으로 헌팅합니다. 모든 가상 머신은 공격의 대상이 될 수 있습니다. VM이 손상되면 사용자 환경 내 추가 리소스를 공격하는 진입점으로 사용됩니다.



## <a name="why-jit-vm-access-is-the-solution"></a>JIT VM 액세스가 솔루션인 이유 

모든 사이버 보안 방지 기술과 마찬가지로 목표는 공격 노출 영역을 줄이는 것입니다. 이 경우에는 열려 있는 포트, 특히 관리 포트의 수를 줄일 수 있습니다.

합법적인 사용자는 이러한 포트를 사용하기 때문에 이러한 포트를 계속 닫아 두는 것을 실용적이지 않습니다.

이 딜레마를 해결 하기 위해 클라우드 용 Microsoft Defender는 JIT를 제공 합니다. JIT를 통해 Azure VM에 대한 인바운드 트래픽을 차단함으로써 공격에 대한 노출을 줄이는 동시에 VM에 쉽게 액세스하여 필요할 때 연결할 수 있습니다.



## <a name="how-jit-operates-with-network-security-groups-and-azure-firewall"></a>네트워크 보안 그룹 및 Azure Firewall을 사용하여 JIT가 작동하는 방법

Just-In-Time VM 액세스를 사용하도록 설정하면 인바운드 트래픽이 차단될 VM의 포트를 선택할 수 있습니다. 클라우드 용 Defender는 nsg ( [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md#security-rules) ) 및 [Azure 방화벽 규칙](../firewall/rule-processing.md)에서 선택한 포트에 대해 "모든 인바운드 트래픽 거부" 규칙이 있는지 확인 합니다. 이러한 규칙은 Azure VM의 관리 포트에 대한 액세스를 제한하고 공격으로부터 보호합니다. 

선택한 포트에 대한 다른 규칙이 이미 존재하는 경우 기존 규칙은 새 "모든 인바운드 트래픽 거부" 규칙보다 우선적으로 적용됩니다. 선택한 포트에 기존 규칙이 없는 경우 새 규칙은 NSG 및 AAzure Firewall에서 가장 높은 우선 순위를 사용합니다.

사용자가 VM에 대 한 액세스를 요청 하면 Defender for Cloud는 사용자에 게 해당 VM에 대 한 azure [RBAC (역할 기반 액세스 제어)](../role-based-access-control/role-assignments-portal.md) 권한이 있는지 확인 합니다. 요청이 승인 되 면 Defender for Cloud는 지정 된 시간 동안 관련 IP 주소 (또는 범위)에서 선택한 포트에 대 한 인바운드 트래픽을 허용 하도록 NSGs 및 Azure 방화벽을 구성 합니다. 시간이 만료 되 면 Defender for Cloud는 NSGs를 이전 상태로 복원 합니다. 이미 설정된 연결은 중단되지 않습니다.

> [!NOTE]
> JIT는 [Azure Firewall Manager](../firewall-manager/overview.md)에서 제어하는 Azure Firewall로 보호되는 VM을 지원하지 않습니다.  Azure 방화벽은 규칙 (클래식)으로 구성 되어야 하며 방화벽 정책을 사용할 수 없습니다.




## <a name="how-defender-for-cloud-identifies-which-vms-should-have-jit-applied"></a>Defender for Cloud가 JIT를 적용 해야 하는 Vm을 식별 하는 방법

아래 다이어그램은 지원 되는 Vm을 범주화 하는 방법을 결정할 때 Defender for Cloud가 적용 하는 논리를 보여 줍니다. 

[![JIT(Just-In-Time) VM(가상 머신) 논리 흐름.](media/just-in-time-explained/jit-logic-flow.png)](media/just-in-time-explained/jit-logic-flow.png#lightbox)

Defender for Cloud가 JIT를 활용 하 여 이점을 누릴 수 있는 컴퓨터를 찾으면 해당 컴퓨터를 권장 구성의 **비정상 리소스** 탭에 추가 합니다. 

![JIT(Just-In-Time) VM(가상 머신) 액세스 권장 사항.](./media/just-in-time-explained/unhealthy-resources.png)


## <a name="faq---just-in-time-virtual-machine-access"></a>FAQ - Just-In-Time 가상 머신 액세스

### <a name="what-permissions-are-needed-to-configure-and-use-jit"></a>JIT를 구성하고 사용하는 데 필요한 권한은 무엇인가요?

JIT를 사용 하려면 구독에서 서버를 사용 하도록 설정 하는 [데 Microsoft Defender](defender-for-servers-introduction.md) 가 필요 합니다. 

**Reader** 및 **SecurityReader** 역할은 둘 다 JIT 상태와 매개 변수를 볼 수 있습니다.

JIT를 사용할 수 있는 사용자 지정 역할을 만들려면 아래 표의 세부 정보가 필요합니다.

> [!TIP]
> VM에 대 한 jit 액세스를 요청 해야 하는 사용자에 게 최소 권한 역할을 만들고 다른 JIT 작업을 수행 하지 않으려면 [JitLeastPrivilegedRole](https://github.com/Azure/Azure-Security-Center/tree/main/Powershell%20scripts/JIT%20Scripts/JIT%20Custom%20Role) for Cloud GitHub 커뮤니티 페이지를 사용 합니다.

| 사용자를 사용하도록 설정하려면 다음이 필요합니다. | 설정할 사용 권한|
| --- | --- |
|VM에 대한 JIT 정책 구성 또는 편집 | *역할에 다음 작업을 할당합니다.*  <ul><li>VM에 연결된 구독 또는 리소스 그룹의 범위에 다음을 할당합니다.<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> VM의 구독 또는 리소스 그룹의 범위에 다음을 할당합니다. <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|VM에 대한 JIT 액세스 요청 | *사용자에게 다음 작업을 할당합니다.*  <ul><li>VM에 연결된 구독 또는 리소스 그룹의 범위에 다음을 할당합니다.<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>VM에 연결된 구독 또는 리소스 그룹의 범위에 다음을 할당합니다.<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  구독, 리소스 그룹 또는 VM의 범위에 다음을 할당합니다.<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  구독, 리소스 그룹 또는 VM의 범위에 다음을 할당합니다.<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|
|JIT 정책 읽기| *사용자에게 다음 작업을 할당합니다.*  <ul><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/read`</li><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action`</li><li>`Microsoft.Security/policies/read`</li><li>`Microsoft.Security/pricings/read`</li><li>`Microsoft.Compute/virtualMachines/read`</li><li>`Microsoft.Network/*/read`</li>|
|||





## <a name="next-steps"></a>다음 단계

이 페이지에서는 JIT(Just-In-Time) VM(가상 머신) 액세스를 사용해야 하는 _이유_ 를 설명했습니다. JIT를 활성화하고 JIT 지원 VM에 대한 액세스를 요청하는 _방법_ 에 대해 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [JIT를 사용하여 관리 포트를 보호하는 방법](just-in-time-access-usage.md)
