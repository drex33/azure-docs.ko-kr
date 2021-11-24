---
title: 빠른 시작 - PowerShell을 사용하여 Azure API Management 인스턴스 만들기 | Microsoft Docs
description: Azure PowerShell을 사용하여 새 Azure API Management 인스턴스를 만듭니다.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: quickstart
ms.custom: mvc, devx-track-azurepowershell, mode-api
ms.date: 09/14/2020
ms.author: danlep
ms.openlocfilehash: f868a1162efc49bd222ff9085fb4db5903dd0fab
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133041062"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-powershell"></a>빠른 시작: PowerShell을 사용하여 새 Azure API Management 서비스 인스턴스 만들기

Azure APIM(API Management)을 사용하여 조직은 외부, 파트너 및 내부의 개발자에게 API를 게시하여 데이터 및 서비스의 잠재성을 활용할 수 있습니다. API Management는 개발자 참여, 비즈니스 통찰력, 분석, 보안과 보호 등을 통해 성공적인 API 프로그램을 보장하는 핵심적인 역량을 제공합니다. APIM을 사용하면 어디서든 호스팅되는 기존 백 엔드 서비스를 위한 최신 API 게이트웨이를 만들고 관리할 수 있습니다. 자세한 내용은 [개요](api-management-key-concepts.md)를 참조하세요.

이 빠른 시작에서는 Azure PowerShell cmdlet을 사용하여 새 API Management 인스턴스를 만드는 단계를 설명합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에는 Azure PowerShell 모듈 버전 1.0 이상이 필요합니다. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 명령은 미국 서부 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location WestUS
```

## <a name="create-an-api-management-service"></a>API Management 서비스 만들기

이제 리소스 그룹이 있으므로 API Management 서비스 인스턴스를 만들 수 있습니다. [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement)를 사용하여 만들고 서비스 이름과 게시자 세부 정보를 제공합니다. 서비스 이름은 Azure 내에서 고유해야 합니다.

다음 예제에서는 *myapim* 이 서비스 이름에 사용됩니다. 이름을 고유한 값으로 업데이트합니다. 또한 API 게시자의 조직 이름과 알림을 받을 관리자 이메일 주소를 업데이트합니다.

기본적으로 이 명령은 Azure API Management를 평가하는 경제적인 옵션인 개발자 계층에서 인스턴스를 만듭니다. 이 계층은 프로덕션 용도가 아닙니다. API Management 계층 크기 조정에 대한 자세한 내용은 [업그레이드 및 크기 조정](upgrade-and-scale.md)을 참조하세요.

> [!NOTE]
> 이 작업은 장기 실행 작업입니다. 이 계층에서 API Management 서비스를 만들고 활성화하는 데 30~40분 정도 걸릴 수 있습니다.

```azurepowershell-interactive
New-AzApiManagement -Name "myapim" -ResourceGroupName "myResourceGroup" `
  -Location "West US" -Organization "Contoso" -AdminEmail "admin@contoso.com" 
```

명령이 반환되면 [Get-AzApiManagement](/powershell/module/az.apimanagement/get-azapimanagement)를 실행하여 Azure API Management 서비스의 속성을 봅니다. 활성화 후 프로비저닝 상태는 성공함이고 서비스 인스턴스에는 여러 개의 연결된 URL이 있습니다. 예를 들면 다음과 같습니다.

```azurepowershell-interactive
Get-AzApiManagement -Name "myapim" -ResourceGroupName "myResourceGroup" 
```

예제 출력:

```console
PublicIPAddresses                     : {203.0.113.1}
PrivateIPAddresses                    :
Id                                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.ApiManagement/service/myapim
Name                                  : myapim
Location                              : West US
Sku                                   : Developer
Capacity                              : 1
CreatedTimeUtc                        : 9/9/2020 9:07:43 PM
ProvisioningState                     : Succeeded
RuntimeUrl                            : https://myapim.azure-api.net
RuntimeRegionalUrl                    : https://myapi-westus-01.regional.azure-api.net
PortalUrl                             : https://myapim.portal.azure-api.net
DeveloperPortalUrl                    : https://myapim.developer.azure-api.net
ManagementApiUrl                      : https://myapim.management.azure-api.net
ScmUrl                                : https://myapim.scm.azure-api.net
PublisherEmail                        : admin@contoso.com
OrganizationName                      : Contoso
NotificationSenderEmail               : apimgmt-noreply@mail.windowsazure.com
VirtualNetwork                        :
VpnType                               : None
PortalCustomHostnameConfiguration     :
ProxyCustomHostnameConfiguration      : {myapim.azure-api.net}
ManagementCustomHostnameConfiguration :
ScmCustomHostnameConfiguration        :
DeveloperPortalHostnameConfiguration  :
SystemCertificates                    :
Tags                                  : {}
AdditionalRegions                     : {}
SslSetting                            : Microsoft.Azure.Commands.ApiManagement.Models.PsApiManagementSslSetting
Identity                              :
EnableClientCertificate               :
ResourceGroupName                     : myResourceGroup

```

API Management 서비스 인스턴스가 배포되면 사용할 준비가 된 것입니다. 자습서를 시작하여 [첫 번째 API를 가져오고 게시](import-and-publish.md)하세요.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [첫 번째 API 가져오기 및 게시](import-and-publish.md)
