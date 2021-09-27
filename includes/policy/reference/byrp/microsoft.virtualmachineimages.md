---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 38ea05a7a604656eb74dd7f6d0af1fcbd6f8aaaa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128555181"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[VM Image Builder 템플릿은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 VM Image Builder에 매핑하여 리소스를 구성하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking#deploy-using-an-existing-vnet](../../../../articles/virtual-machines/linux/image-builder-networking.md#deploy-using-an-existing-vnet)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함, 거부 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |