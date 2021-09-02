---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/27/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b599d32c2d51aa95a69d2f6c322aad4050cd2a6c
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123103329"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Active Directory Domain Services 관리되는 도메인은 TLS 1.2 전용 모드를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3aa87b5a-7813-4b57-8a43-42dd9df5aaa7) |관리되는 도메인에 TLS 1.2 전용 모드를 사용합니다. 기본적으로 Azure AD Domain Services는 NTLM v1 및 TLS v1과 같은 암호화를 사용할 수 있습니다. 이러한 암호화는 일부 레거시 애플리케이션에 필요할 수 있지만 약한 암호화로 간주되며 필요하지 않은 경우 사용하지 않도록 설정할 수 있습니다. TLS 1.2 전용 모드를 사용하는 경우 TLS 1.2를 사용하지 않는 요청을 만드는 클라이언트는 실패합니다. [https://docs.microsoft.com/azure/active-directory-domain-services/secure-your-domain](../../../../articles/active-directory-domain-services/secure-your-domain.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Active%20Directory/AADDomainServices_TLS_Audit.json) |