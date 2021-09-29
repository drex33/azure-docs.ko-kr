---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 15e8728d83d55d79e5913c2b37d566b66dcfbd54
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128655341"
---
|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[공유 대시보드에는 인라인 콘텐츠가 포함된 markdown 타일이 없어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |markdown 타일에 인라인 콘텐츠를 포함하는 공유 대시보드를 만들 수 없도록 하고, 콘텐츠를 온라인으로 호스팅되는 markdown 파일로 저장되어야 합니다. markdown 타일에서 인라인 콘텐츠를 사용하는 경우 콘텐츠의 암호화를 관리할 수 없습니다. 사용자 고유의 스토리지를 구성하여 암호화와 이중 암호화하고 사용자 고유의 키도 가져올 수 있습니다. 이 정책을 사용하도록 설정하면 사용자가 2020-09-01-preview 이상 버전의 공유 대시보드 REST API를 사용하도록 제한됩니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
