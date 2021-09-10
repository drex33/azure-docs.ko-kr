---
title: 포함 파일
description: 포함 파일
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: ee564accd0eaf53f0089e0dba3bb00e3eb1665e1
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112575650"
---
| 리소스 | 제한 |
| --- | --- |
| Azure AD 테넌트당 관리 그룹 | 10000 |
| 관리 그룹당 구독 | 무제한. |
| 관리 그룹 계층 구조의 수준 | 루트 수준 + 6 수준<sup>1</sup> |
| 관리 그룹당 직접 부모 관리 그룹 | 하나 |
| 위치당 [관리 그룹 수준 배포](../articles/azure-resource-manager/templates/deploy-to-management-group.md) | 800<sup>2</sup> |
| [관리 그룹 수준 배포](../articles/azure-resource-manager/templates/deploy-to-management-group.md) 위치 | 10 |

<sup>1</sup>6 수준에는 구독 수준이 포함되지 않습니다.

<sup>2</sup>800개 배포 제한에 도달하면 기록에서 더 이상 필요하지 않은 배포를 삭제합니다. 관리 그룹 수준 배포를 삭제하려면 [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) 또는 [az deployment mg delete](/cli/azure/deployment/mg#az_deployment_mg_delete)를 사용합니다.
