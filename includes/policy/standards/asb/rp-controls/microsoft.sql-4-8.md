---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/27/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 78e89b5d7193a665bb09a104a1d490cf183dffe6
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123122725"
---
|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Managed Instance는 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |자체 키를 사용하여 TDE(투명한 데이터 암호화)를 구현하면 TDE 보호기에 대한 투명성과 제어력이 향상되고, HSM 지원 외부 서비스를 통한 보안이 강화되며, 업무 분리 프로모션을 제공합니다. 이 권장 사항은 관련 규정 준수 요구 사항이 있는 조직에 적용됩니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[SQL 서버는 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |자체 키를 사용하여 TDE(투명한 데이터 암호화)를 구현하면 TDE 보호기에 대한 투명성과 제어력이 향상되고, HSM 지원 외부 서비스를 통해 보안이 강화되며, 업무 분리 프로모션을 제공합니다. 이 권장 사항은 관련 규정 준수 요구 사항이 있는 조직에 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[SQL 데이터베이스에 투명한 데이터 암호화를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |저장 데이터를 보호하고 규정 준수 요구 사항을 충족하려면 투명한 데이터 암호화를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |
