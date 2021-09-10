---
title: Azure VM에 대한 WinRM 액세스 설정
description: Resource Manager 배포 모델에서 만든 Azure 가상 머신에 대한 WinRM 액세스를 설정합니다.
author: mimckitt
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/16/2016
ms.author: mimckitt
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 32241529dc97f97fe3dfce2f1b7cb015dc952475
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688479"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Azure Resource Manager에서 Virtual Machines에 대한 WinRM 액세스 설정
**적용 대상:** :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 


다음은 WinRM 연결을 사용하여 VM을 설정하는 데 필요한 단계입니다.

1. 주요 자격 증명 모음 만들기
2. 자체 서명된 인증서 만들기
3. 자체 서명된 인증서를 주요 자격 증명 모음에 업로드
4. 주요 자격 증명 모음에 자체 서명된 인증서에 대한 URL 가져오기
5. VM을 만드는 동안 자체 서명된 인증서 URL 참조



## <a name="step-1-create-a-key-vault"></a>1단계: 주요 자격 증명 모음 만들기
아래 명령을 사용하여 주요 자격 증명 모음을 만들 수 있습니다.

```azurepowershell
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>2단계: 자체 서명된 인증서 만들기
이 PowerShell 스크립트를 사용하여 자체 서명된 인증서를 만들 수 있습니다.

```azurepowershell
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>3단계: 주요 자격 증명 모음에 자체 서명된 인증서 업로드
1단계에서 만든 주요 자격 증명 모음에 인증서를 업로드하기 전에 먼저 Microsoft.Compute 리소스 공급자가 이해할 수 있는 형식으로 변환해야 합니다. 아래 PowerShell 스크립트를 사용하면 그러한 형식으로 변환할 수 있습니다.

```azurepowershell
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

[System.Collections.HashTable]$TableForJSON = @{
    "data"     = $fileContentEncoded;
    "dataType" = "pfx";
    "password" = "<password>";
}
[System.String]$jsonObject = $TableForJSON | ConvertTo-Json
$jsonEncoded = [System.Convert]::ToBase64String($jsonObject)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>4단계: 주요 자격 증명 모음에 자체 서명된 인증서에 대한 URL 가져오기
Microsoft.Compute 리소스 공급자는 VM을 프로비전하는 동안 주요 자격 증명 모음 내에 포함된 암호에 대한 URL이 필요합니다. 이룰 통해 Microsoft.Compute 리소스 공급자는 암호를 다운로드하고 VM에서 해당 인증서를 만들 수 있습니다.

> [!NOTE]
> 암호의 URL에는 버전도 포함되어야 합니다. 예제 URL은 아래의 https: \//contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve와 같은 형태입니다.

#### <a name="templates"></a>템플릿
아래 코드를 사용하여 템플릿의 URL에 대한 링크를 가져올 수 있습니다.

```json
"certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"
```

#### <a name="powershell"></a>PowerShell
아래의 PowerShell 명령을 사용하여 이 URL을 가져올 수 있습니다.

```azurepowershell
$secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
```

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>5단계: VM을 만드는 동안 자체 서명된 인증서 URL 참조
#### <a name="azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿
템플릿을 통해 VM을 만드는 동안 인증서가 아래와 같이 암호 섹션 및 winRM 섹션에서 참조됩니다.

```json
"osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "<resource id of the Key Vault containing the secret>"
          },
          "vaultCertificates": [
            {
              "certificateUrl": "<URL for the certificate you got in Step 4>",
              "certificateStore": "<Name of the certificate store on the VM>"
            }
          ]
        }
      ],
      "windowsConfiguration": {
        ...
        "winRM": {
          "listeners": [
            {
              "protocol": "http"
            },
            {
              "protocol": "https",
              "certificateUrl": "<URL for the certificate you got in Step 4>"
            }
          ]
        },
        ...
      }
    },
```

위 항목에 대한 샘플 템플릿은 [vm-winrm-keyvault-windows](https://azure.microsoft.com/resources/templates/vm-winrm-keyvault-windows/)에서 찾을 수 있습니다.

이 템플릿의 소스 코드는 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
```azurepowershell
$vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
$credential = Get-Credential
$secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
$vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -ProvisionVMAgent -WinRMCertificateUrl $secretURL
$sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
$CertificateStore = "My"
$vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL
```

## <a name="step-6-connecting-to-the-vm"></a>6단계: VM에 연결
VM에 연결하려면 먼저 컴퓨터가 WinRM 원격 관리에 맞게 구성되어 있는지 확인해야 합니다. 관리자 권한으로 PowerShell을 시작하고 아래 명령을 실행하여 제대로 설정되었는지 확인합니다.

```azurepowershell
Enable-PSRemoting -Force
```

> [!NOTE]
> 위 작업이 제대로 수행되지 않으면 WinRM 서비스가 실행되고 있는지 확인해야 합니다. 이 작업은 `Get-Service WinRM`
>
>

설정이 끝나면 아래 명령을 사용하여 VM에 연결할 수 있습니다.

```azurepowershell
Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
```
