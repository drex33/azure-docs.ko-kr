---
title: PowerShell 샘플 - 인증서가 없는 Azure Active Directory 애플리케이션 프록시 앱
description: 사용자 지정 도메인을 사용 중이지만 유효한 TLS/SSL 인증서가 업로드되지 않은 모든 Azure AD(Azure Active Directory) 애플리케이션 프록시 애플리케이션을 나열하는 PowerShell 예제입니다.
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
ms.openlocfilehash: 11cc7116690cc5eb5d71ad61ab9af9ff2ceace0a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988047"
---
# <a name="get-all-application-proxy-apps-published-with-no-certificate-uploaded"></a>인증서를 업로드하지 않고 게시된 모든 애플리케이션 프록시 앱 가져오기

이 PowerShell 스크립트 예제에서는 사용자 지정 도메인을 사용 중이지만 유효한 TLS/SSL 인증서가 업로드되지 않은 모든 Azure AD(Azure Active Directory) 애플리케이션 프록시 앱을 나열합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

이러한 샘플을 사용하려면 [Graph용 AzureAD V2 PowerShell 모듈](/powershell/azure/active-directory/install-adv2)(AzureAD) 또는 [Graph용 AzureAD V2 PowerShell 모듈 미리 보기 버전](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true)(AzureADPreview)이 필요합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-custom-domain-no-cert.ps1 "Get all Azure AD Proxy application apps published with no certificate uploaded")]

## <a name="script-explanation"></a>스크립트 설명

| 명령 | 메모 |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | 서비스 주체를 가져옵니다. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Azure AD 애플리케이션을 가져옵니다. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Azure AD에서 애플리케이션 프록시에 대해 구성된 애플리케이션을 검색합니다. |

## <a name="next-steps"></a>다음 단계

Azure AD PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 모듈 개요](/powershell/azure/active-directory/overview)를 참조하세요.

애플리케이션 프록시에 대한 다른 PowerShell 예제는 [Azure AD 애플리케이션 프록시에 대한 Azure AD PowerShell 예제](../application-proxy-powershell-samples.md)를 참조하세요.
