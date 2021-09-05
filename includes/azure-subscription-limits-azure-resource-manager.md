---
title: 포함 파일
description: 포함 파일
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 06/04/2021
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 1197b15f313746c0a8542bfadeb4e0123818fca8
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112575637"
---
| 리소스 | 제한 |
| --- | --- |
| [Azure Active Directory 테넌트와 연결](../articles/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) 구독 | 제한 없음 |
| 구독당 [공동 관리자](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) |제한 없음 |
| 구독당 [리소스 그룹](../articles/azure-resource-manager/management/overview.md) |980 |
| Azure Resource Manager API 요청 크기 |4,194,304바이트 |
| 구독당 태그 수<sup>1</sup> |50 |
| 구독당 고유한 태그 계산<sup>1</sup> | 80,000 |
| 위치당 [구독 수준 배포](../articles/azure-resource-manager/templates/deploy-to-subscription.md) | 800<sup>2</sup> |
| [구독 수준 배포](../articles/azure-resource-manager/templates/deploy-to-subscription.md) 위치 | 10 |

<sup>1</sup>최대 50개의 태그를 구독에 직접 적용할 수 있습니다. 그러나 구독에는 구독 내의 리소스 그룹 및 리소스에 적용되는 태그를 무제한으로 포함할 수 있습니다. 리소스당 또는 리소스 그룹당 태그 수는 50개로 제한됩니다. Resource Manager는 태그 수가 80,000개 이하일 경우에만 구독에 [고유한 태그 이름 및 값의 목록](/rest/api/resources/tags)을 반환합니다. 개수가 80,000개를 초과하더라도 태그로 리소스를 찾을 수 있습니다.

<sup>2</sup>한도에 가까워지면 배포가 기록에서 자동으로 삭제됩니다. 자세한 내용은 [배포 기록에서 자동 삭제](../articles/azure-resource-manager/templates/deployment-history-deletions.md)를 참조하세요.