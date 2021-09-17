---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c79e4e0bd75fb99144760113ef93a8e05b01fa02
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123473933"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[\[미리 보기\]: 분산 추적을 사용하지 않는 Azure Spring Cloud 인스턴스 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f2d8593-4667-4932-acca-6a9f187af109) |Azure Spring Cloud의 분산 추적 도구를 사용하면 애플리케이션에서 마이크로서비스 간의 복잡한 상호 연결을 디버그하고 모니터링할 수 있습니다. 분산 추적 도구는 사용하도록 설정되고 정상 상태여야 합니다. |감사, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_DistributedTracing_Audit.json) |
|[Azure Spring Cloud는 네트워크 주입을 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf35e2a4-ef96-44e7-a9ae-853dd97032c4) |Azure Spring Cloud 인스턴스는 다음과 같은 용도로 가상 네트워크 주입을 사용해야 합니다. 1. Azure Spring Cloud를 인터넷에서 격리합니다. 2. Azure Spring Cloud를 사용하여 온-프레미스 데이터 센터의 시스템 또는 다른 가상 네트워크의 Azure 서비스와 상호 작용할 수 있습니다. 3. 고객이 Azure Spring Cloud에 대한 인바운드 및 아웃바운드 네트워크 통신을 제어할 수 있습니다. |감사, 사용 안 함, 거부 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_VNETEnabled_Audit.json) |
