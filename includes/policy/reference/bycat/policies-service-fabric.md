---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/16/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 9dfd93cd2c53747939db793509343e5c89dadb08
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114389009"
---
|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Service Fabric 클러스터는 ClusterProtectionLevel 속성을 EncryptAndSign으로 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |Service Fabric은 기본 클러스터 인증서를 사용하여 노드 간 통신을 위한 3단계 보호(None, Sign 및 EncryptAndSign)를 제공합니다. 모든 노드 간 메시지가 암호화되고 디지털로 서명될 수 있게 보호 수준을 설정합니다. |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |
|[Service Fabric 클러스터는 클라이언트 인증에 대해서만 Azure Active Directory를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Service Fabric에서 Azure Active Directory를 통한 클라이언트 인증의 사용만 감사 |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
