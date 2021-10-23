---
title: PowerShell 샘플 - Azure Active Directory 애플리케이션 프록시 앱에 대한 확장 정보 나열
description: 모든 Azure AD(Azure Active Directory) 애플리케이션 프록시 애플리케이션과 애플리케이션 ID(AppId), 이름(DisplayName), 외부 URL(ExternalUrl), 내부 URL(InternalUrl) 및 인증 유형(ExternalAuthenticationType)을 나열하는 PowerShell 예제입니다.
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
ms.openlocfilehash: 16fd5ac821f09363249f904b14ac836b063e1b25
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988129"
---
# <a name="get-all-application-proxy-apps-and-list-extended-information"></a>모든 애플리케이션 프록시 앱을 가져오고 자세한 정보 나열

이 PowerShell 스크립트 예제에서는 애플리케이션 ID(AppId), 이름(DisplayName), 외부 URL(ExternalUrl), 내부 URL(InternalUrl), 인증 유형(ExternalAuthenticationType), SSO 모드 및 추가 설정을 비롯한 모든 Azure AD(Azure Active Directory) Application Proxy 애플리케이션에 대한 정보를 나열합니다.

$ssoMode 변수의 값을 변경하면 SSO 모드로 필터링된 출력을 사용할 수 있습니다. 자세한 내용은 스크립트에 설명되어 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

이 샘플을 사용하려면 [Graph 모듈에 대한 AzureAD V2 PowerShell](/powershell/azure/active-directory/install-adv2)(AzureAD)이 필요합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-extended.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>스크립트 설명

| 명령 | 메모 |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | 서비스 주체를 가져옵니다. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Azure AD 애플리케이션을 가져옵니다. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Azure AD에서 애플리케이션 프록시에 대해 구성된 애플리케이션을 검색합니다. |

## <a name="next-steps"></a>다음 단계

Azure AD PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 모듈 개요](/powershell/azure/active-directory/overview)를 참조하세요.

애플리케이션 프록시에 대한 다른 PowerShell 예제는 [Azure AD 애플리케이션 프록시에 대한 Azure AD PowerShell 예제](../application-proxy-powershell-samples.md)를 참조하세요.
