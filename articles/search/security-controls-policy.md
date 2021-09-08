---
title: Azure Cognitive Search에 대한 Azure Policy 규정 준수 컨트롤
description: Azure Cognitive Search에 사용할 수 있는 Azure Policy 규정 준수 컨트롤을 나열합니다. 이러한 기본 제공 정책 정의는 Azure 리소스의 규정 준수를 관리하는 일반적인 방법을 제공합니다.
ms.date: 09/03/2021
ms.topic: sample
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.custom: subject-policy-compliancecontrols
ms.openlocfilehash: a0d572f974f64003ed20f85864c81e1dc7807231
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123478019"
---
# <a name="azure-policy-regulatory-compliance-controls-for-azure-cognitive-search"></a>Azure Cognitive Search에 대한 Azure Policy 규정 준수 컨트롤

[Azure Policy](../governance/policy/overview.md)를 사용하여 [Azure 보안 벤치마크](/azure/security/benchmarks/introduction)에서 권장 사항을 적용하는 경우 비규격 서비스를 찾아서 수정하기 위한 정책을 만들 수 있습니다. 이러한 정책은 사용자 지정 정책일 수도 있고, 잘 알고 있는 모범 사례에 대한 규정 준수 기준과 적절한 솔루션을 제공하는 기본 제공 정의를 기반으로 할 수도 있습니다.

Azure Cognitive Search의 경우 현재는 아래에 나열된 기본 제공 정의 하나가 있으며, 이 기본 제공 정의를 정책 할당에 사용할 수 있습니다. 이 기본 제공 정의는 로깅 및 모니터링용입니다. [만드는 정책](../governance/policy/assign-policy-portal.md)에 이 기본 제공 정의를 사용하면 시스템에서는 [진단 로깅](search-monitor-logs.md)이 없는 검색 서비스를 찾아서 적절하게 사용하도록 설정합니다.

[Azure Policy의 규정 준수](../governance/policy/concepts/regulatory-compliance.md)는 서로 다른 규정 준수 표준과 관련된 **규정 준수 도메인** 및 **보안 제어** 에 대해 _기본 제공_ 으로 알려진 Microsoft 생성 및 관리형 이니셔티브 정의를 제공합니다. 이 페이지에는 Azure Cognitive Search에 대한 **규정 준수 도메인** 및 **보안 제어** 가 나열되어 있습니다. **보안 제어** 에 대한 기본 제공 기능을 개별적으로 할당하여 Azure 리소스가 특정 표준을 준수하도록 할 수 있습니다.

[!INCLUDE [azure-policy-compliancecontrols-introwarning](../../includes/policy/standards/intro-warning.md)]

[!INCLUDE [azure-policy-compliancecontrols-search](../../includes/policy/standards/byrp/microsoft.search.md)]

## <a name="next-steps"></a>다음 단계

- [Azure Policy 규정 준수](../governance/policy/concepts/regulatory-compliance.md)에 대해 자세히 알아봅니다.
- [Azure Policy GitHub 리포지토리](https://github.com/Azure/azure-policy)의 기본 제공 기능을 참조하세요.
