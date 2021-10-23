---
title: 빠른 시작 - PowerShell을 사용하여 Azure Analysis Services 만들기 | Azure Analysis Services | Microsoft Docs
description: 이 빠른 시작에서는 PowerShell을 사용하여 Azure Analysis Services 서버를 만드는 방법을 설명합니다.
author: minewiskan
ms.author: owend
ms.reviewer: minewiskan
ms.date: 10/12/2021
ms.topic: quickstart
ms.service: azure-analysis-services
ms.custom: devx-track-azurepowershell - references_regions - devx-track-azurepowershell - mode-api
ms.openlocfilehash: 13eaddb60d6f7811d11f3fac42c4d8b384645820
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130006644"
---
# <a name="quickstart-create-a-server---powershell"></a>빠른 시작: 서버 만들기 - PowerShell

이 빠른 시작은 명령줄에서 PowerShell을 사용하여 Azure 구독에 Azure Analysis Services 서버를 만드는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure 구독**: [Azure 평가판](https://azure.microsoft.com/offers/ms-azr-0044p/)으로 이동하여 계정을 만들 수 있습니다.
- **Azure Active Directory**: 구독이 Azure Active Directory 테넌트와 연결되어 있고 해당 디렉터리에 계정이 있어야 합니다. 자세한 내용은 [인증 및 사용자 권한](analysis-services-manage-users.md)을 참조하세요.
- **Azure PowerShell**. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 설치 또는 업그레이드하려면 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요.

## <a name="import-azanalysisservices-module"></a>Az.AnalysisServices 모듈 가져오기

구독에서 서버를 만들려면 [Az.AnalysisServices](/powershell/module/az.analysisservices) 모듈을 사용합니다. PowerShell 세션으로 Az.AnalysisServices 모듈을 로드합니다.

```powershell
Import-Module Az.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 명령을 사용하여 Azure 구독에 로그인합니다. 화면에 나타나는 지침에 따릅니다.

```powershell
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[Azure 리소스 그룹](../azure-resource-manager/management/overview.md)은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다. 서버를 만들 때 구독에서 리소스 그룹을 지정해야 합니다. 아직 리소스 그룹이 없는 경우 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 명령을 사용하여 새 리소스 그룹을 만들 수 있습니다. 다음 예제에서는 미국 서부 지역의 `myResourceGroup`라는 리소스 그룹을 만듭니다.

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

## <a name="create-a-server"></a>서버 만들기

[New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) 명령을 사용하여 새 서버를 만듭니다. 다음 예제에서는 D1(체험) 계층, 미국 서부 지역, myResourceGroup에서 myServer라는 서버를 만들고 서버 관리자로 philipc@adventureworks.com을 지정합니다.

```powershell
New-AzAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myserver" -Location WestUS -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>리소스 정리

[Remove-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) 명령을 사용하여 구독에서 서버를 제거할 수 있습니다. 이 컬렉션의 다른 빠른 시작 및 자습서로 계속하는 경우에는 서버를 제거하지 마세요. 다음 예제에서는 이전 단계에서 만든 서버를 제거합니다.


```powershell
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 PowerShell을 사용하여 Azure 구독에서 서버를 만드는 방법을 알아보았습니다. 이제 서버가 생겼으니, 서버 방화벽을 구성하여(선택 사항) 보안을 강화할 수 있습니다. 또한 포털에서 바로 기본 샘플 데이터 모델을 서버에 추가할 수 있습니다. 샘플 모델이 있으면 model 데이터베이스 역할을 구성하고 클라이언트 연결을 테스트하는 방법을 알아볼 때 유용합니다. 자세히 알아보려면 샘플 모델 추가를 위한 자습서를 계속합니다.

> [!div class="nextstepaction"]
> [빠른 시작: 서버 방화벽 구성 - Portal](analysis-services-qs-firewall.md)      
