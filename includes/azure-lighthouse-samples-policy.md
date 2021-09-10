---
title: 포함 파일
description: 포함 파일
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 08/13/2021
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: b55711662daa7d3e476cd8a3cd3c438c7893c424
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178170"
---
이러한 샘플은 Azure Lighthouse에 온보딩된 구독과 함께 Azure Policy를 사용하는 방법을 보여 줍니다.

| **템플릿** | **설명** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | 위임된 구독에 수정 효과를 사용하여 태그를 추가하거나 제거하는 정책을 할당합니다. 자세한 내용은 [위임된 구독 내에서 수정할 수 있는 정책 배포](../articles/lighthouse/how-to/deploy-policy-remediation.md)를 참조하세요. |
| [policy-allow-certain-managing-tenants](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-allow-certain-managing-tenants) | 특정 관리 테넌트에 Azure Lighthouse 위임을 제한하는 정책을 할당합니다. |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | 위임 할당을 감사하는 정책을 할당합니다. |
| [policy-delegate-management-groups](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups) | [관리 그룹 내의 구독이 관리 테넌트에 위임되었는지](../articles/lighthouse/how-to/onboard-management-group.md) 확인하는 정책을 할당하고, 그렇지 않은 경우 할당을 만듭니다.
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | deployIfNotExists 효과를 사용하여 위임된 구독에서 Azure Key Vault 리소스에 대한 진단을 사용하도록 설정하는 정책을 할당합니다. 자세한 내용은 [위임된 구독 내에서 수정할 수 있는 정책 배포](../articles/lighthouse/how-to/deploy-policy-remediation.md)를 참조하세요. |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | 위임된 구독에서 진단을 사용하도록 여러 정책을 할당하고 모든 Windows 및 Linux VM을 정책에 의해 생성된 Log Analytics 작업 영역에 연결합니다. 자세한 내용은 [위임된 구독 내에서 수정할 수 있는 정책 배포](../articles/lighthouse/how-to/deploy-policy-remediation.md)를 참조하세요. |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | 위임된 구독에 이니셔티브(여러 관련 정책 정의)를 적용합니다. |

