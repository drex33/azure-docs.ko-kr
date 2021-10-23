---
title: PowerShell 샘플 - Azure Active Directory 애플리케이션 프록시 앱에 대한 사용자 및 그룹 나열
description: 특정 Azure AD(Azure Active Directory) 애플리케이션 프록시 애플리케이션에 할당된 모든 사용자 및 그룹을 나열하는 PowerShell 예제입니다.
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
ms.openlocfilehash: 8b07e9673c9305826a1e02e03eac1d17b519772c
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988319"
---
# <a name="display-users-and-groups-assigned-to-an-application-proxy-application"></a>애플리케이션 프록시 애플리케이션에 할당된 사용자 및 그룹 표시

이 PowerShell 스크립트 예제에서는 특정 Azure AD(Azure Active Directory) 애플리케이션 프록시 애플리케이션에 할당된 사용자 및 그룹을 나열합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

이러한 샘플을 사용하려면 [Graph용 AzureAD V2 PowerShell 모듈](/powershell/azure/active-directory/install-adv2)(AzureAD) 또는 [Graph용 AzureAD V2 PowerShell 모듈 미리 보기 버전](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true)(AzureADPreview)이 필요합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/display-users-group-of-an-app.ps1 "Display users and groups assigned to an Application Proxy application")]

## <a name="script-explanation"></a>스크립트 설명

| 명령 | 메모 |
|---|---|
| [Get-AzureADUser](/powershell/module/AzureAD/get-azureaduser)| 사용자를 가져옵니다. |
| [Get-AzureADGroup](/powershell/module/AzureAD/get-azureadgroup)| 그룹을 가져옵니다. |
| [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | 서비스 주체를 가져옵니다. |
| [Get-AzureADUserAppRoleAssignment](/powershell/module/AzureAD/get-azureaduserapproleassignment) | 사용자 애플리케이션 역할 할당을 가져옵니다. |
| [Get-AzureADGroupAppRoleAssignment](/powershell/module/AzureAD/get-azureadgroupapproleassignment) | 그룹 애플리케이션 역할 할당을 가져옵니다. |

## <a name="next-steps"></a>다음 단계

Azure AD PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 모듈 개요](/powershell/azure/active-directory/overview)를 참조하세요.

애플리케이션 프록시에 대한 다른 PowerShell 예제는 [Azure AD 애플리케이션 프록시에 대한 Azure AD PowerShell 예제](../application-proxy-powershell-samples.md)를 참조하세요.
