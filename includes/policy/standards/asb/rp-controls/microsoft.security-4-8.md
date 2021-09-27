---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 386109e6a28514c6d57f201bdc6097f4d969cdad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128910213"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[가상 머신은 Compute 및 Storage 리소스 간에 임시 디스크, 캐시 및 데이터 흐름을 암호화해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |기본적으로 가상 컴퓨터의 OS 및 데이터 디스크는 플랫폼 관리 키를 사용 하 여 미사용으로 암호화 됩니다. 임시 디스크, 데이터 캐시 및 계산과 저장소 간의 데이터 흐름이 암호화 되지 않습니다. 다음의 경우 권장 사항 무시: 1. 호스트에서 암호화 또는 2를 사용 합니다. Managed Disks의 서버 쪽 암호화는 보안 요구 사항을 충족합니다. [Azure Disk Storage의 서버 쪽 암호화](../../../../../articles/virtual-machines/disk-encryption.md)에서 자세히 알아봅니다. 그리고 [다른 디스크 암호화](../../../../../articles/virtual-machines/disk-encryption-overview.md#comparison)기능을 제공 합니다. |AuditIfNotExists, 사용 안 함 |[2.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |