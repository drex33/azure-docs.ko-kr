---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/16/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a49bd6384a85580503bd5a9fc27f8a2986d7e650
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114387990"
---
|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[관리형 애플리케이션에 대한 애플리케이션 정의는 고객이 제공한 스토리지 계정을 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9db7917b-1607-4e7d-a689-bca978dd0633) |규제 또는 규정 준수 요구 사항인 경우 사용자 고유의 스토리지 계정을 사용하여 애플리케이션 정의 데이터를 제어합니다. 관리형 애플리케이션 정의를 만드는 중에 사용자가 제공한 스토리지 계정 내에 저장하도록 선택하여 규정 준수 요구 사항을 충족하도록 해당 위치 및 액세스를 완전히 관리할 수 있습니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/ApplicationDefinition_Missing_StorageAccount_Deny.json) |
|[관리형 애플리케이션에 대한 연결 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17763ad9-70c0-4794-9397-53d765932634) |선택한 리소스 종류를 지정된 관리형 애플리케이션에 연결하는 연결 리소스를 배포합니다.  이 정책 배포는 중첩된 리소스 유형을 지원하지 않습니다. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/AssociationForManagedApplication_Deploy.json) |
