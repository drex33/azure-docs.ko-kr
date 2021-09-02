---
title: Azure Security Center에 대한 기본 제공 정책 정의
description: Azure Security Center에 대한 Azure Policy 기본 제공 정책 정의를 나열합니다. 이러한 기본 제공 정책 정의는 Azure 리소스를 관리하는 일반적인 방법을 제공합니다.
ms.date: 08/27/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: c8928a3a4d19aa7fecb751eb0a40a23c9f72f592
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123100281"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>Azure Security Center에 대한 Azure Policy 기본 제공 정의

이 페이지는 Azure Security Center와 관련된 [Azure Policy](../governance/policy/overview.md) 기본 제공 정책 정의의 인덱스입니다. 다음 정책 정의 그룹을 사용할 수 있습니다.

- [이니셔티브](#azure-security-center-initiatives) 그룹은 "Security Center" 범주에 Azure Policy 이니셔티브 정의를 나열합니다.
- [기본 이니셔티브](#azure-security-center-initiatives) 그룹은 Security Center의 기본 이니셔티브인 [Azure Security Benchmark](/security/benchmark/azure/introduction)의 일부인 모든 Azure Policy 정의를 나열합니다. Microsoft가 제작하고 널리 인정받는 이 벤치마크는 클라우드 중심 보안에 초점을 맞춘 [CIS(Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) 및 [NIST(National Institute of Standards and Technology)](https://www.nist.gov/)의 컨트롤을 기반으로 합니다.
- [범주](#azure-security-center-category) 그룹은 "Security Center" 범주에 모든 Azure Policy 정의를 나열합니다.

보안 정책에 대한 자세한 내용은 [보안 정책 작업](./tutorial-security-policy.md)을 참조하세요. 다른 서비스에 대한 추가 Azure Policy 기본 제공 기능은 [Azure Policy 기본 제공 정의](../governance/policy/samples/built-in-policies.md)를 참조하세요.

Azure Portal의 정책 정의에 대한 각 기본 제공 정책 정의 링크의 이름입니다. **Version** 열의 링크를 사용하여 [Azure Policy GitHub 리포지토리](https://github.com/Azure/azure-policy)에서 원본을 봅니다.

## <a name="azure-security-center-initiatives"></a>Azure Security Center 이니셔티브

Security Center에서 모니터링하는 기본 제공 이니셔티브에 대한 자세한 내용은 다음 표를 참조하세요.

[!INCLUDE [azure-policy-reference-policyset-security-center](../../includes/policy/reference/bycat/policysets-security-center.md)]

## <a name="security-centers-default-initiative-azure-security-benchmark"></a>Security Center의 기본 이니셔티브(Azure Security Benchmark)

Security Center에서 모니터링하는 기본 제공 정책에 대한 자세한 내용은 다음 표를 참조하세요.

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Azure Security Center 범주

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>다음 단계

이 문서에서는 Security Center의 Azure Policy 보안 정책 정의에 대해 알아보았습니다. 이니셔티브, 정책 및 보안 센터의 권장 사항과 관련되는 방식에 대한 자세한 내용은 [보안 정책, 이니셔티브 및 권장 사항이란?](security-policy-concept.md)을 참조하세요.
