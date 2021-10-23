---
title: PowerShell 샘플 - Azure Active Directory 애플리케이션 프록시 앱에 그룹 할당
description: Azure AD(Azure Active Directory) 애플리케이션 프록시 애플리케이션에 그룹을 할당하는 PowerShell 예제입니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: sample
ms.date: 04/29/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: 66d6213f85f58ce736b0be926e90f24e701b666a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988414"
---
# <a name="assign-a-group-to-a-specific-azure-ad-application-proxy-application"></a>특정 Azure AD 애플리케이션 프록시 애플리케이션에 그룹 할당

이 PowerShell 스크립트 예제를 사용하면 Azure AD(Azure Active Directory) 애플리케이션 프록시 애플리케이션에 특정 그룹을 할당할 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

이러한 샘플을 사용하려면 [Graph용 AzureAD V2 PowerShell 모듈](/powershell/azure/active-directory/install-adv2)(AzureAD) 또는 [Graph용 AzureAD V2 PowerShell 모듈 미리 보기 버전](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true)(AzureADPreview)이 필요합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/assign-group-to-app.ps1 "Assign a group to a specific Azure AD Application Proxy application")]

## <a name="script-explanation"></a>스크립트 설명

| 명령 | 메모 |
|---|---|
| [New-AzureADGroupAppRoleAssignment](/powershell/module/AzureAD/New-azureadgroupapproleassignment) | 애플리케이션 역할에 그룹을 할당합니다. |

## <a name="next-steps"></a>다음 단계

Azure AD PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 모듈 개요](/powershell/azure/active-directory/overview)를 참조하세요.

애플리케이션 프록시에 대한 다른 PowerShell 예제는 [Azure AD 애플리케이션 프록시에 대한 Azure AD PowerShell 예제](../application-proxy-powershell-samples.md)를 참조하세요.
