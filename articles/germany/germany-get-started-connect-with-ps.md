---
title: PowerShell을 사용하여 Azure 독일에 연결 | Microsoft Docs
description: PowerShell을 사용하여 Azure 독일에서 구독을 관리하는 방법에 대한 정보
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs, devx-track-azurepowershell
ms.openlocfilehash: 3f5caa51b992d348fa077a0dbb96e210aa18f4de
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "122535454"
---
# <a name="connect-to-azure-germany-by-using-powershell"></a>PowerShell을 사용하여 Azure 독일에 연결

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

Azure 독일로 Azure PowerShell을 사용하려면 글로벌 Azure 대신 Azure 독일에 연결해야 합니다. Azure PowerShell을 사용하여 스크립트를 통해 많은 구독을 관리하거나 Azure Portal에서 현재 사용할 수 없는 기능에 액세스할 수 있습니다. 글로벌 Azure에서 PowerShell을 사용한 적이 있다면 대부분 동일합니다. Azure 독일의 차이점은 다음과 같습니다.

* 계정 연결
* 지역 이름

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!NOTE]
> 아직 PowerShell을 사용하지 않은 경우 [Azure PowerShell 소개](/powershell/azure/)를 참조하세요.

PowerShell을 시작할 때 환경 매개 변수를 지정하여 Azure 독일에 연결하라고 Azure PowerShell에 알려야 합니다. 이 매개 변수는 PowerShell이 올바른 엔드포인트에 연결하도록 보장합니다. 엔드포인트 컬렉션은 계정에 연결할 때 결정됩니다. 다양한 API만큼 다양한 환경 스위치 버전이 필요합니다.

| 연결 형식 | 명령 |
| --- | --- |
| [Azure(클래식 배포 모델)](/powershell/azure) 명령 |`Add-AzureAccount -Environment AzureGermanCloud` |
| [Azure(Resource Manager 배포 모델)](/powershell/azure) 명령 |`Connect-AzAccount -EnvironmentName AzureGermanCloud` |
| [Azure Active Directory(클래식 배포 모델)](/previous-versions/azure/jj151815(v=azure.100)) 명령 |`Connect-MsolService -AzureEnvironment AzureGermanyCloud` |
| [Azure Active Directory(Resource Manager 배포 모델)](../azure-resource-manager/management/deployment-models.md) 명령 |`Connect-AzureAD -AzureEnvironmentName AzureGermanyCloud` |

`New-AzStorageContext`를 사용하여 스토리지 계정에 연결하는 경우 `Environment` 스위치를 사용한 다음, `AzureGermanCloud`를 지정할 수도 있습니다.

## <a name="determining-region"></a>지역 확인
연결되면 서비스를 대상으로 하는 데 사용되는 지역이라는 또 하나의 차이점이 있습니다. Azure 클라우드 서비스마다 지역이 서로 다릅니다. 지역은 서비스 가용성 페이지에서 확인할 수 있습니다. 일반적으로 명령의 `Location` 매개 변수에 지역을 사용합니다.


| 일반 이름 | 표시 이름 | 위치 이름 |
| --- | --- | --- |
| 독일 중부 |`Germany Central` | `germanycentral` |
| 독일 북동부 |`Germany Northeast` | `germanynortheast` |


> [!NOTE]
> 글로벌 Azure용 PowerShell에 해당되므로 `Location` 매개 변수에 대한 표시 이름 또는 위치 이름을 사용할 수 있습니다.
>
>

Azure 독일에서 사용 가능한 지역을 확인하려면 다음 명령을 실행하고 현재 목록을 인쇄합니다. 클래식 배포의 경우 첫 번째 명령을 사용합니다. Resource Manager 배포의 경우 두 번째 명령을 사용합니다.

```azurepowershell
Get-AzureLocation
Get-AzLocation
```

Azure에서 사용 가능한 환경을 알아보려면 다음 명령을 실행합니다.

```azurepowershell
Get-AzureEnvironment
Get-AzEnvironment
```

## <a name="next-steps"></a>다음 단계
Azure 독일 연결에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [Azure CLI를 사용하여 Azure 독일에 연결](./germany-get-started-connect-with-cli.md)
* [Visual Studio를 사용하여 Azure 독일에 연결](./germany-get-started-connect-with-vs.md)
* [Azure Portal을 사용하여 Azure 독일에 연결](./germany-get-started-connect-with-portal.md)
