---
title: Azure DDoS Protection 표준에 대한 기본 제공 정책 정의
description: Azure DDoS Protection 표준에 대한 Azure Policy 기본 제공 정책 정의를 나열합니다. 이러한 기본 제공 정책 정의는 Azure 리소스를 관리하는 일반적인 방법을 제공합니다.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2021
ms.author: yitoh
ms.custom: subject-policy-reference
ms.topic: include
ms.openlocfilehash: 5519d72d4d6d33381ce4381c7fd4e65c4d94d01f
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "130555867"
---
# <a name="azure-policy-built-in-definitions-for-azure-ddos-protection-standard"></a>Azure DDoS Protection 표준에 대한 Azure Policy 기본 제공 정의

본 페이지는 Azure DDoS Protection 표준에 대한 [Azure Policy](../governance/policy/overview.md) 기본 제공 정책 정의 색인입니다. 다른 서비스에 대한 추가 Azure Policy 기본 제공 기능은 [Azure Policy 기본 제공 정의](../governance/policy/samples/built-in-policies.md)를 참조하세요.

Azure Portal의 정책 정의에 대한 각 기본 제공 정책 정의 링크의 이름입니다. **Version** 열의 링크를 사용하여 [Azure Policy GitHub 리포지토리](https://github.com/Azure/azure-policy)에서 원본을 봅니다.

## <a name="azure-ddos-protection-standard"></a>Azure DDoS Protection 표준

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[가상 네트워크는 Azure DDoS Protection 표준으로 보호되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94de2ad3-e0c1-4caf-ad78-5d47bbc83d3d)|Azure DDoS Protection Standard를 사용하여 볼륨 및 프로토콜 공격으로부터 가상 네트워크를 보호합니다. 자세한 내용은 [https://aka.ms/ddosprotectiondocs](./ddos-protection-overview.md)를 방문하세요.|수정, 감사, 사용 안 함|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkDdosStandard_Audit.json)|
|[공용 IP 주소는 Azure DDoS Protection 표준에 대해 리소스 로그를 사용하도록 설정되어 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F752154a7-1e0f-45c6-a880-ac75a7e4f648)|Log Analytics 작업 영역으로 스트리밍하려면 진단 설정에서 공용 IP 주소의 리소스 로그를 사용하도록 설정합니다. 알림, 보고서 및 흐름 로그를 통해 DDoS 공격을 완화하기 위해 수행된 공격 트래픽 및 작업에 대한 자세한 정보를 얻습니다.|AuditIfNotExists, DeployIfNotExists, Disabled|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/PublicIpDdosLogging_Audit.json)|


## <a name="next-steps"></a>다음 단계

- [Azure Policy GitHub 리포지토리](https://github.com/Azure/azure-policy)의 기본 제공 기능을 참조하세요.
- [Azure Policy 정의 구조](../governance/policy/concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해](../governance/policy/concepts/effects.md)를 검토합니다.
