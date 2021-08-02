---
title: 애플리케이션의 HA에 대한 트래픽 라우팅 - Azure PowerShell - Traffic Manager
description: Azure PowerShell 스크립트 샘플 - 애플리케이션 고가용성을 위한 트래픽 라우팅
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
manager: kumudD
editor: ''
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0fa40fd4c7f5313b44384d7ca9fd268f4249e7f7
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110700025"
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-powershell"></a>Azure PowerShell을 사용하여 애플리케이션 고가용성을 위해 트래픽 라우팅

이 스크립트는 리소스 그룹, 2개 App Service 계획, 2개 웹앱, Traffic Manager 프로필 및 2개 Traffic Manager 엔드포인트를 만듭니다. Traffic Manager는 주 지역인 한 지역의 애플리케이션 및 주 지역의 애플리케이션을 사용할 수 없을 때 보조 지역으로 트래픽을 전달합니다. 스크립트를 실행하기 전에 MyWebApp, MyWebAppL1 및 MyWebAppL2 값을 Azure에서 고유한 값으로 변경해야 합니다. 스크립트를 실행한 후에는 mywebapp.trafficmanager.net URL을 사용하여 주 지역의 응용 프로그램에 액세스할 수 있습니다.

필요한 경우 [Azure PowerShell 가이드](/powershell/azure)에 있는 지침을 사용하여 Azure PowerShell을 설치한 다음, `Connect-AzAccount`를 실행하여 Azure에 연결합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.ps1 "Route traffic for high availability")]


다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name myResourceGroup1
Remove-AzResourceGroup -Name myResourceGroup2
```


## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 웹앱, Traffic Manager 프로필 및 모든 관련된 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)  | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | App Service 계획을 만듭니다. Azure 웹앱에 대한 서버 팜과 비슷합니다. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | App Service 계획 내에서 Azure 웹앱을 만듭니다. |
| [집합 AzResource](/powershell/module/az.resources/new-azresource) | App Service 계획 내에서 Azure 웹앱을 만듭니다. |
| [새 AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) | Azure Traffic Manager 프로필을 만듭니다. |
| [새 AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) | Azure Traffic Manager 프로필에 엔드포인트를 추가합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

추가 네트워킹 PowerShell 스크립트 샘플은 [Azure 네트워킹 개요 설명서](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json)에서 찾을 수 있습니다.
