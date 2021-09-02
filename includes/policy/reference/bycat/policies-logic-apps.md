---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/27/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ace7679be8d234db712186308aa17e306cd16555
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123103587"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Logic Apps 통합 서비스 환경은 고객 관리형 키로 암호화되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fafeaf6-7927-4059-a50a-8eb2a7a6f2b5) |통합 서비스 환경에 배포하여 고객 관리형 키를 사용하여 미사용 Logic Apps 데이터의 암호화를 관리합니다. 기본적으로 고객 데이터는 서비스 관리형 키를 사용하여 암호화되지만, 일반적으로 규정 준수 표준을 충족하려면 고객 관리형 키가 필요합니다. 고객 관리형 키를 사용하면 사용자가 만들고 소유한 Azure Key Vault 키를 사용하여 데이터를 암호화할 수 있습니다. 순환 및 관리를 포함하여 키의 수명 주기를 고객이 모두 제어하고 책임져야 합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_ISEWithCustomerManagedKey_AuditDeny.json) |
|[통합 서비스 환경에 Logic Apps를 배포해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdc595cb1-1cde-45f6-8faf-f88874e1c0e1) |가상 네트워크에서 통합 서비스 환경에 Logic Apps를 배포하면 고급 Logic Apps 네트워킹 및 보안 기능이 잠금 해제되고 네트워크 구성을 보다 강력하게 제어할 수 있습니다. [https://aka.ms/integration-service-environment](../../../../articles/logic-apps/connect-virtual-network-vnet-isolated-environment.md)에서 자세히 알아보세요. 통합 서비스 환경에 배포하면 암호화 키를 관리할 수 있도록 하여 향상된 데이터 보호 기능을 제공하는 고객 관리형 키로 암호화할 수도 있습니다. 이는 주로 규정 준수 요구 사항을 충족하기 위한 것입니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_LogicAppsInISE_AuditDeny.json) |
|[Logic Apps의 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |