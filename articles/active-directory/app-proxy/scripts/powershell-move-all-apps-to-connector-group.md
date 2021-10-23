---
title: PowerShell 샘플 - Azure Active Directory 애플리케이션 프록시 앱을 다른 그룹으로 이동
description: 현재 커넥터 그룹에 할당된 모든 애플리케이션을 다른 커넥터 그룹으로 이동하는 데 사용되는 Azure AD(Azure Active Directory) 애플리케이션 프록시 PowerShell 예제입니다.
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
ms.openlocfilehash: 0f40a4bbc6f3fabe3644e5a58ab7065ad40ba9b0
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988509"
---
# <a name="move-all-azure-active-directory-application-proxy-apps-assigned-to-a-connector-group-to-another-connector-group"></a>커넥터 그룹에 할당된 모든 Azure Active Directory 애플리케이션 프록시 앱을 다른 커넥터 그룹으로 이동

이 PowerShell 스크립트 예제에서는 현재 커넥터 그룹에 할당된 모든 Azure AD(Azure Active Directory) 애플리케이션 프록시 애플리케이션을 다른 커넥터 그룹으로 이동합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

이러한 샘플을 사용하려면 [Graph용 AzureAD V2 PowerShell 모듈](/powershell/azure/active-directory/install-adv2)(AzureAD) 또는 [Graph용 AzureAD V2 PowerShell 모듈 미리 보기 버전](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true)(AzureADPreview)이 필요합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/move-all-apps-to-a-connector-group.ps1 "Move all apps assigned to a connector group to another connector group")]

## <a name="script-explanation"></a>스크립트 설명

| 명령 | 메모 |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | 서비스 주체를 가져옵니다. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Azure AD 애플리케이션을 가져옵니다. |
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | 모든 커넥터 그룹 목록을 검색하거나 지정된 커넥터 그룹의 세부 정보를 검색합니다(지정된 경우). |
| [Set-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/set-azureadapplicationproxyapplicationconnectorgroup) | 지정된 커넥터 그룹을 지정된 애플리케이션에 할당합니다.|

## <a name="next-steps"></a>다음 단계

Azure AD PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 모듈 개요](/powershell/azure/active-directory/overview)를 참조하세요.

애플리케이션 프록시에 대한 다른 PowerShell 예제는 [Azure AD 애플리케이션 프록시에 대한 Azure AD PowerShell 예제](../application-proxy-powershell-samples.md)를 참조하세요.
