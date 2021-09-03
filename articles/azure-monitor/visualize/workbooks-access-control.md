---
title: Azure Monitor 통합 문서 액세스 제어
description: 역할 기반 액세스 제어를 통해 미리 빌드되고 사용자 지정 매개 변수가 있는 통합 문서를 사용하여 복잡한 보고 간소화
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/16/2021
ms.openlocfilehash: e1f5b06d40ca6883092193493143f668ec999b1c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114452458"
---
# <a name="access-control"></a>Access Control

통합 문서에서 액세스 제어는 다음 두 가지를 의미합니다.

* 통합 문서의 데이터를 읽는 데 필요한 액세스 이 액세스 권한은 통합 문서에 사용되는 리소스에 대한 표준 [Azure 역할](../../role-based-access-control/overview.md)에 의해 제어됩니다. 통합 문서는 해당 리소스에 대한 액세스를 지정하거나 구성하지 않습니다. 사용자는 일반적으로 해당 리소스에 대한 [모니터링 읽기 권한자](../../role-based-access-control/built-in-roles.md#monitoring-reader) 역할을 사용하여 이 액세스 권한을 얻습니다.

* 통합 문서를 저장하는 데 필요한 액세스

    - 통합 문서를 저장하려면 통합 문서를 저장할 리소스 그룹에 대한 쓰기 권한이 필요합니다. 이러한 권한은 일반적으로 [모니터링 참가자](../../role-based-access-control/built-in-roles.md#monitoring-contributor) 역할에 의해 지정되지만 *통합 문서 참가자* 역할을 통해 설정할 수도 있습니다.
    
## <a name="standard-roles-with-workbook-related-privileges"></a>통합 문서 관련 권한이 있는 표준 역할

[모니터링 읽기 권한자](../../role-based-access-control/built-in-roles.md#monitoring-reader)에는 모니터링 도구(통합 문서 포함)가 리소스에서 데이터를 읽는 데 사용되는 표준/읽기 권한이 포함됩니다.

[모니터링 참가자](../../role-based-access-control/built-in-roles.md#monitoring-contributor)에는 항목 저장을 위해 다양한 모니터링 도구에서 사용하는 일반 `/write` 권한(공유 통합 문서를 저장할 `workbooks/write` 권한 포함)이 포함됩니다.
"통합 문서 참가자"는 공유 통합 문서를 저장하기 위해 개체에 "통합 문서/쓰기" 권한을 추가합니다.

사용자 지정 역할:

통합 문서를 저장하려면 `microsoft.insights/workbooks/write`를 추가합니다. 자세한 내용은 [통합 문서 참가자](../../role-based-access-control/built-in-roles.md#monitoring-contributor) 역할을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [시작하여](./workbooks-overview.md#visualizations) 다양한 시각화 옵션을 갖춘 통합 문서에 대해 알아봅니다.