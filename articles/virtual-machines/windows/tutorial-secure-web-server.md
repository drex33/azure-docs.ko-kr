---
title: '자습서: Azure에서 TLS/SSL 인증서로 Windows 웹 서버 보호'
description: 이 자습서에서는 Azure PowerShell을 사용하여 Azure Key Vault에 저장된 TLS/SSL 인증서로 IIS 웹 서버를 실행하는 Windows 가상 머신을 보호하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.subservice: security
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: a4fe089672b1d62d321c1306983acfb66f5c0579
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688619"
---
# <a name="tutorial-secure-a-web-server-on-a-windows-virtual-machine-in-azure-with-tlsssl-certificates-stored-in-key-vault"></a>자습서: Key Vault에 저장된 TLS/SSL 인증서로 Azure에서 Windows 가상 머신의 웹 서버 보호
**적용 대상:** :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 

> [!NOTE]
> 현재 이 문서는 일반화된 이미지에만 적용됩니다. 특수 디스크를 사용하여 이 자습서를 시도하면 오류가 발생합니다. 

웹 서버를 보호하기 위해 이전에 SSL(Secure Sockets Layer)로 알려진 TLS(Transport Layer Security) 인증서를 사용하여 웹 트래픽을 암호화할 수 있습니다. 이러한 TLS/SSL 인증서는 Azure Key Vault에 저장될 수 있으며 Azure에서 Windows VM(가상 머신)에 인증서의 보안 배포를 허용합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Key Vault 만들기
> * Key Vault에 인증서 생성 또는 업로드
> * VM 만들기 및 IIS 웹 서버 설치
> * VM에 인증서 삽입 및 TLS 바인딩으로 IIS 구성


## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요** 를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사** 를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.


## <a name="overview"></a>개요
Azure Key Vault는 암호화 키 및 비밀 정보(인증서 또는 암호)를 보호합니다. Key Vault를 사용하면 인증서 관리 프로세스를 간소화하고 해당 인증서에 액세스하는 키의 제어를 유지할 수 있습니다. Key Vault 내에 자체 서명된 인증서를 만들거나 이미 소유하고 있는 기존의 신뢰할 수 있는 인증서를 업로드할 수 있습니다.

내재된 인증서를 포함하는 사용자 지정 VM 이미지를 사용하는 대신 실행 중인 VM에 인증서를 삽입합니다. 이 프로세스를 통해 배포하는 동안 가장 최신 인증서가 웹 서버에 설치됩니다. 인증서를 갱신하거나 바꾸는 경우 새 사용자 지정 VM 이미지를 만들 필요가 없습니다. 최신 인증서는 추가 VM을 만들 때 자동으로 삽입됩니다. 전체 프로세스 동안 인증서는 Azure 플랫폼에서 벗어나거나 스크립트, 명령줄 기록 또는 템플릿에 노출되지 않습니다.


## <a name="create-an-azure-key-vault"></a>Azure Key Vault 만들기
Key Vault 및 인증서를 만들려면 먼저 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *미국 동부* 위치에 *myResourceGroupSecureWeb* 이라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

다음으로 [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault)를 사용하여 Key Vault를 만듭니다. 각 Key Vault에는 고유한 이름이 필요하며 모두 소문자여야 합니다. 다음 예제에서 `mykeyvault`를 사용자 고유의 Key Vault 이름으로 바꿉니다.

```azurepowershell-interactive
$keyvaultName="mykeyvault"
New-AzKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>인증서 생성 및 Key Vault에 저장
프로덕션 사용을 위해 [Import-AzKeyVaultCertificate](/powershell/module/az.keyvault/import-azkeyvaultcertificate)를 사용하여 신뢰할 수 있는 공급자가 서명한 유효한 인증서를 가져와야 합니다. 이 자습서의 다음 예제는 [New-AzKeyVaultCertificatePolicy](/powershell/module/az.keyvault/new-azkeyvaultcertificatepolicy)에서 기본 인증서 정책을 사용하는 자체 서명된 인증서를 [Add-AzKeyVaultCertificate](/powershell/module/az.keyvault/add-azkeyvaultcertificate)를 사용하여 생성하는 방법을 보여 줍니다. 

```azurepowershell-interactive
$policy = New-AzKeyVaultCertificatePolicy `
    -SubjectName "CN=www.contoso.com" `
    -SecretContentType "application/x-pkcs12" `
    -IssuerName Self `
    -ValidityInMonths 12

Add-AzKeyVaultCertificate `
    -VaultName $keyvaultName `
    -Name "mycert" `
    -CertificatePolicy $policy 
```


## <a name="create-a-virtual-machine"></a>가상 머신 만들기
[Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)을 사용하여 VM의 관리자 사용자 이름과 암호를 설정합니다.

```azurepowershell-interactive
$cred = Get-Credential
```

이제 [New-AzVM](/powershell/module/az.compute/new-azvm)을 사용하여 VM을 만들 수 있습니다. 다음 예제에서는 *EastUS* 위치에 *myVM* 이라는 VM을 만듭니다. 아직 없는 경우 지원 네트워크 리소스가 만들어집니다. 보안 웹 트래픽을 허용하기 위해 cmdlet에서 *443* 포트도 엽니다.

```azurepowershell-interactive
# Create a VM
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred `
    -OpenPorts 443

# Use the Custom Script Extension to install IIS
Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server -IncludeManagementTools"}'
```

VM을 만드는 데 몇 분 정도 걸립니다. 마지막 단계는 [Set-AzVmExtension](/powershell/module/az.compute/set-azvmextension)을 통해 Azure 사용자 지정 스크립트 확장을 사용하여 IIS 웹 서버를 설치하는 것입니다.


## <a name="add-a-certificate-to-vm-from-key-vault"></a>Key Vault에서 VM에 인증서 추가
Key Vault에서 VM으로 인증서를 추가하려면 [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret)을 사용하여 인증서의 ID를 가져옵니다. [Add-AzVMSecret](/powershell/module/az.compute/add-azvmsecret)을 사용하여 VM에 인증서를 추가합니다.

```azurepowershell-interactive
$certURL=(Get-AzKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>인증서를 사용하도록 IIS 구성
다시 [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension)을 통해 사용자 지정 스크립트 확장을 사용하여 IIS 구성을 업데이트합니다. 이 업데이트는 Key Vault에서 가져온 인증서를 IIS에 적용하고 웹 바인딩을 구성합니다.

```azurepowershell-interactive
$PublicSettings = '{
    "fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/secure-iis.ps1"],
    "commandToExecute":"powershell -ExecutionPolicy Unrestricted -File secure-iis.ps1"
}'

Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString $publicSettings
```


### <a name="test-the-secure-web-app"></a>보안 웹앱 테스트
[Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress)를 사용하여 VM의 공용 IP 주소를 가져옵니다. 다음 예제에서는 앞서 만든 `myPublicIP`의 IP 주소를 가져옵니다.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIPAddress" | select "IpAddress"
```

이제 웹 브라우저를 열고 주소 표시줄에 `https://<myPublicIP>`를 입력할 수 있습니다. 자체 서명된 인증서를 사용하는 경우 보안 경고를 받으려면 **세부 정보**, **웹 페이지로 이동** 을 차례로 선택합니다.

![웹 브라우저 보안 경고 허용](./media/tutorial-secure-web-server/browser-warning.png)

그러면 보안 IIS 웹 사이트가 다음 예제와 같이 표시됩니다.

![실행 중인 보안 IIS 사이트 보기](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Key Vault에 저장된 TLS/SSL 인증서를 사용하여 IIS 웹 서버를 보호했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure Key Vault 만들기
> * Key Vault에 인증서 생성 또는 업로드
> * VM 만들기 및 IIS 웹 서버 설치
> * VM에 인증서 삽입 및 TLS 바인딩으로 IIS 구성

미리 빌드된 가상 머신 스크립트 샘플을 보려면 이 링크를 따릅니다.

> [!div class="nextstepaction"]
> [Windows 가상 컴퓨터 스크립트 샘플 ](https://github.com/Azure/azure-docs-powershell-samples/tree/master/virtual-machine)
