---
title: Azure Security Center의 보안 정책, 이니셔티브 및 권장 사항 이해
description: Azure Security Center의 보안 정책, 이니셔티브 및 권장 사항에 관해 알아봅니다.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: e065aa7d3d269632c77a810917ec913c43e518db
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122605275"
---
# <a name="what-are-security-policies-initiatives-and-recommendations"></a>보안 정책, 이니셔티브 및 권장 사항은 무엇인가요?

Security Center는 보안 이니셔티브를 구독에 적용합니다. 이 이니셔티브에는 하나 이상의 보안 정책이 포함됩니다. 각 정책에서는 보안 상태를 개선하기 위한 보안 권장 사항이 생성됩니다. 이 페이지에서는 관련 아이디어를 각각 자세히 설명합니다.


## <a name="what-is-a-security-policy"></a>보안 정책이란?

Azure Policy에서 만든 Azure Policy 정의는 제어하려는 특정 보안 조건에 관한 규칙입니다. 기본 제공 정의에는 배포할 수 있는 리소스 종류를 제어하거나 모든 리소스에서 태그 사용을 적용하는 등의 작업이 포함됩니다. 자체적인 사용자 지정 정책 정의를 만들 수도 있습니다.

기본 제공 또는 사용자 지정인지 여부에 관계없이 정책 정의를 구현하려면 해당 정책 정의를 할당해야 합니다. Azure Portal, PowerShell 또는 Azure CLI를 통해 이러한 정책 중 하나를 할당할 수 있습니다.

Azure Policy에는 다양한 유형의 정책이 있습니다. Security Center는 주로 특정 조건 및 구성을 확인한 후 규정 준수에 관해 보고하는 ‘감사’ 정책을 사용합니다. 보안 설정을 적용하는 데 사용할 수 있는 “적용” 정책도 있습니다.

## <a name="what-is-a-security-initiative"></a>보안 이니셔티브란?

Azure Policy 이니셔티브는 특정 목적이나 의도에 맞게 그룹화되는 Azure Policy 정의 또는 규칙의 컬렉션입니다. Azure 이니셔티브는 정책 세트를 단일 항목으로 논리적으로 그룹화하여 정책 관리를 간소화합니다.

보안 이니셔티브는 원하는 워크로드 구성을 정의하며 회사 또는 규제 당국의 보안 요구 사항을 준수하는지 확인하는 데 도움이 됩니다.

보안 정책처럼 Security Center 이니셔티브도 Azure Policy에서 생성됩니다. [Azure Policy](../governance/policy/overview.md)를 사용하여 정책을 관리하고, 이니셔티브를 빌드하고, 이니셔티브를 여러 구독에 할당하거나 전체 관리 그룹용으로 할당할 수 있습니다.

Azure Security Center의 모든 구독에 자동으로 할당되는 기본 이니셔티브는 Azure Security Benchmark입니다. Azure Security Benchmark는 일반적인 규정 준수 프레임워크를 기반으로 하는 보안 및 규정 준수 모범 사례에 관해 Microsoft에서 작성한 Azure 관련 지침 세트입니다. 널리 인정받는 이 벤치마크는 클라우드 중심 보안에 초점을 맞춘 [CIS(Center for Internet Security)](https://www.cisecurity.org/benchmark/azure/) 및 [NIST(National Institute of Standards and Technology)](https://www.nist.gov/)의 컨트롤을 기반으로 합니다. [Azure 보안 벤치마크](/security/benchmark/azure/introduction)에 대해 자세히 알아보세요.

Security Center는 보안 이니셔티브 및 정책을 사용하기 위한 다음 옵션을 제공합니다.

- **기본 제공 기본 이니셔티브 보기 및 편집** - Security Center를 사용하는 경우 ‘Azure Security Benchmark’라는 이니셔티브가 모든 Security Center 등록된 구독에 자동으로 할당됩니다. 이 이니셔티브를 사용자 지정하기 위해 정책의 매개 변수를 편집하여 이니셔티브 내에서 개별 정책을 사용하거나 사용하지 않도록 설정할 수 있습니다. [기본 제공 보안 정책](./policy-reference.md) 목록을 참조하여 기본적으로 제공되는 옵션을 이해합니다.

- **자체 사용자 지정 이니셔티브 추가** - 구독에 적용되는 보안 이니셔티브는 Security Center 내에서 사용자 지정할 수 있습니다. 이후 머신이 사용자가 만든 정책을 준수하지 않으면 권장 사항이 제공됩니다. 사용자 지정 정책 빌드 및 할당에 관한 지침은 [사용자 지정 보안 이니셔티브 및 정책 사용](custom-security-policies.md)을 참조하세요.

- **규정 준수 표준을 이니셔티브로 추가** - Security Center의 규정 준수 대시보드에는 특정 표준 또는 규정(예: Azure CIS, NIST SP 800-53 R4, SWIFT CSP CSCF-v2020)의 컨텍스트에서 환경 내에 있는 모든 평가의 상태가 표시됩니다. 자세한 내용은 [규정 준수 개선](security-center-compliance-dashboard.md)을 참조하세요.

## <a name="what-is-a-security-recommendation"></a>보안 권장 사항이란?

정책을 사용하면 Security Center는 리소스의 규정 준수 상태를 주기적으로 분석하여 보안과 관련된 잠재적인 잘못된 구성 및 약점을 식별합니다. 그런 다음, 해당 문제를 수정하는 방법에 관한 권장 사항을 제공합니다. 권장 사항은 관련 정책에 대해 리소스를 평가하고 정의된 요구 사항을 충족하지 않는 리소스를 식별한 결과입니다.

Security Center는 선택한 정책에 따라 보안 권장 사항을 만듭니다. 이니셔티브의 정책을 리소스와 비교하고 호환되지 않는 리소스를 하나 이상 찾으면 Security Center의 권장 사항으로 제공됩니다.

권장 사항은 리소스를 보호하고 강화하기 위해 수행할 수 있는 작업입니다. 각 권장 사항은 다음 정보를 제공합니다.

- 문제의 간단한 설명
- 권장 사항을 구현하기 위해 수행할 수정 단계
- 영향을 받는 리소스

실제로 다음과 같이 작동합니다.

1. Azure Security Benchmark는 ***이니셔티브*** 입니다.
1. 여기에는 모든 Azure Storage 계정이 네트워크 액세스를 제한하여 공격 표면을 줄이도록 요구하는 ***정책*** 이 포함됩니다. 이 정책은 “스토리지 계정은 가상 네트워크 규칙을 사용하여 네트워크 액세스를 제한해야 함”이라고 하며, Azure Policy에서 사용하지 않거나 사용하도록 설정할 수 있습니다.
1. Azure Security Center가 보호된 구독에서 Azure Storage 계정을 찾은 경우 해당 계정을 평가하여 계정이 가상 네트워크 규칙으로 보호되는지 확인합니다. 보호되지 않는 경우 이 상황을 해결하고 관련 리소스의 보안을 강화하는 ***권장 사항*** 을 표시합니다. 

따라서 이니셔티브(1)에는 적절한 경우(3) 권장 사항을 생성하는 정책(2)이 포함됩니다. 

## <a name="viewing-the-relationship-between-a-recommendation-and-a-policy"></a>권장 사항과 정책 간 관계 보기

위에서 설명한 것처럼 Security Center의 기본 제공 권장 사항은 Azure Security Benchmark를 기반으로 합니다. 거의 모든 권장 사항에는 벤치마크의 요구 사항에서 파생되는 기본 정책이 있습니다.

권장 사항에 대한 세부 정보를 검토할 때 기본 정책을 볼 수 있으면 도움이 되는 경우가 많습니다. 정책에서 지원하는 모든 권장 사항에 대해 권장 사항 세부 정보 페이지의 **정책 정의 보기** 링크를 사용하여 관련 정책의 Azure Policy 항목으로 직접 이동합니다.

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="권장 사항을 지원하는 특정 정책에 대한 Azure Policy 페이지로 연결됩니다.":::

이 링크를 사용하여 정책 정의를 확인하고 평가 논리를 검토합니다. 

[보안 권장 사항 참조 가이드](recommendations-reference.md)에서 권장 사항 목록을 검토하는 경우 정책 정의 페이지에 대한 링크도 표시됩니다.

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Azure Security Center 권장 사항 참조 페이지에서 직접 특정 정책에 대한 Azure Policy 페이지에 액세스합니다.":::


## <a name="next-steps"></a>다음 단계

이 페이지에서는 기본 개념과 정책, 이니셔티브 및 권장 사항 간 관계를 개략적으로 설명했습니다. 관련 정보는 다음을 참조하세요.

- [사용자 지정 이니셔티브 만들기](custom-security-policies.md)
- [권장 사항을 사용하지 않으려면 보안 정책 사용 안 함](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)
- [Azure Policy에서 보안 정책을 편집하는 방법](../governance/policy/tutorials/create-and-manage.md)