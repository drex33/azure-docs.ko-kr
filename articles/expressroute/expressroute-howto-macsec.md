---
title: 'Azure ExpressRoute: MACsec 구성'
description: 이 문서에서는 사용자 에지 라우터와 Microsoft 에지 라우터 간 연결을 보호하도록 MACsec를 구성하는 방법을 설명합니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 10/22/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 445010e345e92913f32705fc801a6521703ba84f
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122538246"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>ExpressRoute Direct 포트에서 MACsec 구성

이 문서에서는 PowerShell을 사용하여 사용자 에지 라우터와 Microsoft 에지 라우터 간 연결을 보호하도록 MACsec를 구성하는 방법을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

구성을 시작하기 전에 다음을 확인합니다.

* [ExpressRoute Direct 프로비저닝 워크플로](expressroute-erdirect-about.md)를 이해합니다.
* [ExpressRoute Direct 포트 리소스](expressroute-howto-erdirect.md)를 만들었습니다.
* PowerShell을 로컬로 실행하려면 최신 버전의 Azure PowerShell이 머신에 설치되어 있는지 확인합니다.

### <a name="working-with-azure-powershell"></a>Azure PowerShell 작업

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>로그인 및 올바른 구독 선택

구성을 시작하려면 Azure 계정에 로그인하고 사용할 구독을 선택합니다.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. Azure Key Vault, MACsec 비밀, 사용자 ID 만들기

1. 새 리소스 그룹에 MACsec 비밀을 저장하려면 Key Vault 인스턴스를 만듭니다.

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    키 자격 증명 모음 또는 리소스 그룹이 이미 있는 경우 재사용할 수 있습니다. 그러나 기존 키 자격 증명 모음에서 [**일시 삭제** 기능](../key-vault/general/soft-delete-overview.md)을 사용하도록 설정하는 것이 중요합니다. 일시 삭제를 사용할 수 없는 경우에는 다음 명령을 통해 사용하도록 설정할 수 있습니다.

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. 사용자 ID를 만듭니다.

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    New-AzUserAssignedIdentity가 유효한 PowerShell cmdlet으로 인식되지 않는 경우 다음 모듈(관리자 모드)을 설치하고 위의 명령을 다시 실행합니다.

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. CAK(연결 관련 키) 및 CKN(연결 관련 키 이름)을 만들어 키 자격 증명 모음에 저장합니다.

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
   > [!NOTE]
   > CKN은 64자리의 16진수(0-9, A-f)까지의 짝수 길이 문자열이어야 합니다.
   >
   > CAK 길이는 지정된 암호 제품군에 따라 달라집니다.
   >
   > * GcmAes128의 경우 CAK는 32자리의 16진수(0-9, A-f)까지의 짝수 길이 문자열이어야 합니다.
   >
   > * GcmAes256의 경우 CAK는 64자리의 16진수(0-9, A-f)까지의 짝수 길이 문자열이어야 합니다.
   >

4. 사용자 ID에 GET 권한을 할당합니다.

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   이제 이 ID는 키 자격 증명 모음에서 비밀(예: CAK 및 CKN)을 가져올 수 있습니다.
5. 이 사용자 ID를 ExpressRoute에서 사용하도록 설정합니다.

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. ExpressRoute Direct 포트에서 MACsec 구성

### <a name="to-enable-macsec"></a>MACsec를 사용하도록 설정하려면

각 ExpressRoute Direct 인스턴스에는 두 개의 물리적 포트가 있습니다. 두 포트에서 MACsec를 동시에 사용하도록 설정하거나 한 번에 한 포트에서 MACsec를 사용하도록 설정할 수 있습니다. 다른 포트를 서비스하는 동안 트래픽을 활성 포트로 전환하여 한 번에 한 포트에서 작업을 수행하면 ExpressRoute Direct를 이미 사용하고 있는 경우 중단을 최소화할 수 있습니다.

1. ExpressRoute 관리 코드가 필요한 경우 MACsec 비밀에 액세스할 수 있도록 MACsec 비밀과 암호화를 설정하고 사용자 ID를 포트에 연결합니다.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[0]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[1]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.identity = $erIdentity
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```
2. (선택 사항) 포트가 관리 중단 상태인 경우 다음 명령을 실행하여 포트를 실행할 수 있습니다.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    이때 MACsec는 Microsoft 쪽의 ExpressRoute Direct 포트에서 사용하도록 설정됩니다. 에지 디바이스에서 구성하지 않은 경우 동일한 MACsec 비밀과 암호화를 사용하여 계속 구성할 수 있습니다.

### <a name="to-disable-macsec"></a>MACsec를 사용하지 않도록 설정하려면

ExpressRoute Direct 인스턴스에서 MACsec가 더 이상 필요하지 않은 경우 다음 명령을 실행하여 사용하지 않도록 설정할 수 있습니다.

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

이때 MACsec는 Microsoft 쪽의 ExpressRoute Direct 포트에서 사용하지 않도록 설정됩니다.

### <a name="test-connectivity"></a>연결 테스트
ExpressRoute Direct 포트에서 MACsec(MACsec 키 업데이트 포함)를 구성한 후에 회로의 BGP 세션이 시작되어 실행되고 있는지 [확인](expressroute-troubleshooting-expressroute-overview.md)합니다. 포트에 아직 회로가 없는 경우 먼저 회로를 만들고 회로의 Azure 개인 피어링 또는 Microsoft 피어링을 설정합니다. MACsec 키 불일치를 포함하여 MACsec가 잘못 구성된 경우, 사용자 네트워크 디바이스와 Microsoft 네트워크 디바이스 간에 계층 2의 ARP 해상도와 계층 3의 BGP 설정이 표시되지 않습니다. 모든 항목이 올바르게 구성되면 BGP 경로가 양방향으로 올바르게 보급되고 애플리케이션 데이터가 ExpressRoute를 통해 적절하게 전달됩니다.

## <a name="next-steps"></a>다음 단계
1. [ExpressRoute Direct에서 ExpressRoute 회로 만들기](expressroute-howto-erdirect.md)
2. [Azure Virtual Network에 ExpressRoute 회로 연결](expressroute-howto-linkvnet-arm.md)
3. [ExpressRoute 연결 확인](expressroute-troubleshooting-expressroute-overview.md)
