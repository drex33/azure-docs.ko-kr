---
title: 리소스 태그 지정에 대한 정책 정의
description: 태그 준수를 보장하기 위해 할당할 수 있는 Azure Policy 정의에 대해 설명합니다.
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: f7af799423b0aceac66dfa97e92c92d25c3916db
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114456260"
---
# <a name="assign-policy-definitions-for-tag-compliance"></a>태그 준수에 대한 정책 정의 할당

[Azure Policy](../../governance/policy/overview.md)를 사용하여 태그 지정 규칙을 적용합니다. 정책을 만들어 조직에 필요한 태그가 없는 리소스가 구독에 배포되는 시나리오를 방지합니다. 수동으로 태그를 적용하거나 비규격 리소스를 검색하는 대신 배포 중에 필요한 태그를 자동으로 적용하는 정책을 만듭니다. 이제 새로운 [수정](../../governance/policy/concepts/effects.md#modify) 효과와 [수정 작업](../../governance/policy/how-to/remediate-resources.md)을 사용하여 기존 리소스에 태그를 적용할 수도 있습니다. 다음 섹션은 태그에 대한 예제 정책 정의를 보여줍니다.

## <a name="policy-definitions"></a>정책 정의

[!INCLUDE [Tag policies](../../../includes/policy/reference/bycat/policies-tags.md)]

## <a name="next-steps"></a>다음 단계

* 리소스에 태그를 지정하는 방법에 대해 알아보려면 [태그를 사용하여 Azure 리소스 구성](tag-resources.md)을 참조하세요.
* 일부 리소스 유형은 태그를 지원하지 않습니다. 리소스 유형에 태그를 적용할 수 있는지 확인하려면 [Azure 리소스에 대한 태그 지원](tag-support.md)을 참조하세요.
