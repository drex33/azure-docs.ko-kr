---
title: 인스턴스 및 인증 설정(PowerShell)
titleSuffix: Azure Digital Twins
description: Azure PowerShell을 사용하여 Azure Digital Twins 서비스 인스턴스를 설정하는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 9/3/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c5af0d96b8e639494b556f875a23f13781cbfbec
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129996231"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-powershell"></a>Azure Digital Twins 인스턴스 및 인증 설정(PowerShell)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

이 문서에서는 인스턴스를 만들고 인증을 설정하는 등 **새 Azure Digital Twins 인스턴스를 설정** 하는 단계에 대해 설명합니다. 이 문서를 완료한 후에는 Azure Digital Twins 인스턴스에 대한 프로그래밍을 시작할 수 있습니다.

이 문서의 버전은 [Azure PowerShell](/powershell/azure/new-azureps-module-az)을 사용하여 이러한 단계를 하나씩 수동으로 진행합니다.

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]

## <a name="prepare-your-environment"></a>환경 준비

1. 먼저 이 문서의 명령을 실행할 위치를 선택합니다. Azure PowerShell의 로컬 설치를 사용하거나 [Azure Cloud Shell](https://shell.azure.com)을 사용하여 브라우저 창에서 Azure PowerShell 명령을 실행하도록 선택할 수 있습니다.
    * Azure PowerShell을 로컬로 사용하도록 선택하는 경우:
       1. [Az PowerShell 모듈을 설치합니다](/powershell/azure/install-az-ps).
       1. 컴퓨터에서 PowerShell 창을 엽니다.
       1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용하여 Azure 계정에 로그인합니다.
    * Azure Cloud Shell을 사용하도록 선택하는 경우:
        1. Cloud Shell에 대한 자세한 내용은 [Azure Cloud Shell 개요](../cloud-shell/overview.md)를 참조하세요.
        1. [브라우저에서 Cloud Shell 창을 엽니다](https://shell.azure.com).
        1. Cloud Shell 아이콘 모음에서 Cloud Shell이 PowerShell 버전을 실행하도록 설정되었는지 확인합니다.
    
            :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="PowerShell 버전 선택을 보여주는 Azure Portal의 Cloud Shell 창 스크린샷.":::
    
1. 여러 Azure 구독이 있는 경우 리소스에 대한 요금이 청구되는 적절한 구독을 선택합니다. [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet을 사용하여 특정 구독을 선택합니다.

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. 이 구독에서 Azure Digital Twins를 처음 사용하는 경우 **Microsoft.DigitalTwins** 리소스 공급자를 등록해야 합니다. (확실하지 않으면 이전에 수행한 적이 있더라도 다시 실행해도 됩니다.)

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.DigitalTwins
   ```

1. 다음 명령을 사용하여 **Az.DigitalTwins** PowerShell 모듈을 설치합니다.
    ```azurepowershell-interactive
    Install-Module -Name Az.DigitalTwins
    ```

> [!IMPORTANT]
> **Az.DigitalTwins** PowerShell 모듈이 미리 보기로 제공되는 동안 위에 설명된 대로 `Install-Module` cmdlet을 사용하여 별도로 설치해야 합니다. 이 PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스의 일부가 되며 기본적으로 Azure Cloud Shell 내에서 사용할 수 있습니다.

## <a name="create-the-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스 만들기

이 섹션에서는 Azure PowerShell을 사용하여 **Azure Digital Twins의 새 인스턴스를 만듭니다**.
다음을 제공해야 합니다.

* 인스턴스가 배포될 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md). 기존 리소스 그룹이 아직 없는 경우 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet을 사용하여 새 리소스 그룹을 만들 수 있습니다.

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <name-for-your-resource-group> -Location <region>
  ```

* 배포 지역. Azure Digital Twins를 지원하는 지역을 확인하려면 [지역별 사용 가능한 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)을 참조하세요.
* 인스턴스의 이름. 새 인스턴스의 이름은 구독에 대한 지역 내에서 고유해야 합니다. 구독에 이미 지정된 이름을 사용하고 있는 지역에 다른 Azure Digital Twins 인스턴스가 있는 경우 다른 이름을 선택하라는 메시지가 표시됩니다.

다음 명령의 값을 사용하여 인스턴스를 만듭니다.

```azurepowershell-interactive
New-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> -Location <region>
```

### <a name="verify-success-and-collect-important-values"></a>성공 확인 및 중요한 값 수집

인스턴스가 성공적으로 만들어진 경우 결과는 사용자가 만든 리소스에 대한 정보를 포함하는 다음 출력과 유사합니다.

```Output
Location   Name                                         Type
--------   ----                                         ----
<region>   <name-for-your-Azure-Digital-Twins-instance> Microsoft.DigitalTwins/digitalTwinsInstances
```

그런 다음, 다음과 같이 `Get-AzDigitalTwinsInstance`를 실행하고 `Select-Object -Property *`로 파이핑하여 새 인스턴스의 속성을 표시합니다.

```azurepowershell-interactive
Get-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> |
  Select-Object -Property *
```

> [!TIP]
> 이 명령을 사용하여 언제든지 인스턴스의 모든 속성을 볼 수 있습니다.

Azure Digital Twins 인스턴스의 **호스트 이름**, **이름** 및 **리소스 그룹** 을 확인합니다. 이 값은 Azure Digital Twins 인스턴스를 계속 사용하여 인증을 설정하고 관련 Azure 리소스를 설정하는 데 필요할 수 있는 중요한 값입니다. 다른 사용자가 인스턴스에 대한 프로그래밍을 수행하는 경우 다른 사용자와 이 값을 공유해야 합니다.

이제 Azure Digital Twins 인스턴스를 사용할 준비가 되었습니다. 다음으로, 적절한 Azure 사용자에게 관리 권한을 부여합니다.

## <a name="set-up-user-access-permissions"></a>사용자 액세스 권한 설정

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

### <a name="prerequisites-permission-requirements"></a>필수 조건: 권한 요구 사항
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

### <a name="assign-the-role"></a>역할 할당

사용자에게 Azure Digital Twins 인스턴스를 관리할 수 있는 권한을 부여하려면 인스턴스 내에서 _**Azure Digital Twins 데이터 소유자**_ 역할을 할당해야 합니다.

먼저 역할을 할당해야 하는 사용자의 Azure AD 계정에 대한 **ObjectId** 를 확인합니다. 이 값은 [Get-AzAdUser](/powershell/module/az.resources/get-azaduser) cmdlet을 사용하여 찾을 수 있습니다.이 cmdlet은 Azure AD 계정의 사용자 계정 이름을 전달하여 ObjectId(및 기타 사용자 정보)를 검색합니다. 대부분의 경우 사용자 계정 이름은 Azure AD 계정의 사용자 이메일과 일치합니다.

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName <Azure-AD-user-principal-name-of-user-to-assign>
```

다음으로, 다음 명령의 **ObjectId** 를 사용하여 역할을 할당합니다. 또한 이 명령을 사용하려면 인스턴스를 만들 때 이전에 선택한 것과 동일한 구독 ID, 리소스 그룹 이름 및 Azure Digital Twins 인스턴스 이름을 입력해야 합니다. 명령은 Azure 구독에서 [충분한 권한](#prerequisites-permission-requirements)이 있는 사용자가 실행해야 합니다.

```azurepowershell-interactive
$Params = @{
  ObjectId = '<Azure-AD-user-object-ID-of-user-to-assign>'
  RoleDefinitionName = 'Azure Digital Twins Data Owner'
  Scope = '/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<name-for-your-Azure-Digital-Twins-instance>'
}
New-AzRoleAssignment @Params
```

이 명령을 실행하면 만들어진 역할 할당에 대한 정보가 출력됩니다.

### <a name="verify-success"></a>성공 확인

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

이제 Azure Digital Twins 인스턴스를 사용할 준비가 되고 관리 권한이 할당되었습니다.

## <a name="next-steps"></a>다음 단계

인증 코드를 사용하여 인스턴스에 클라이언트 애플리케이션을 연결하는 방법을 참조하세요.
* [앱 인증 코드 쓰기](how-to-authenticate-client.md)
