---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/27/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: fd58768332794f2cd2f08d6e5069735a9793773c
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123123620"
---
|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[네트워크 보안 그룹을 사용하여 인터넷 연결 가상 머신을 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |NSG(네트워크 보안 그룹)를 통해 가상 머신에 대한 액세스를 제한하여 잠재적인 위협으로부터 가상 머신을 보호합니다. [https://aka.ms/nsg-doc](../../../../../articles/virtual-network/network-security-groups-overview.md)에서 NSG를 통한 트래픽 제어에 대해 자세히 알아보기 |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[가상 머신에서 IP 전달을 사용하지 않도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |가상 머신의 NIC에서 IP 전달을 사용하도록 설정하면 머신이 다른 대상으로 주소가 지정된 트래픽을 수신할 수 있습니다. IP 전달은 거의 필요하지 않으므로(예: VM을 네트워크 가상 어플라이언스로 사용하는 경우), 네트워크 보안 팀에서 검토해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[가상 머신에서 관리 포트를 닫아야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |열려 있는 원격 관리 포트는 위험도가 높은 인터넷 기반 공격에 VM을 노출시킵니다. 이러한 공격은 자격 증명을 무차별적으로 대입하여 머신에 대한 관리자 액세스 권한을 획득하려고 시도합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |