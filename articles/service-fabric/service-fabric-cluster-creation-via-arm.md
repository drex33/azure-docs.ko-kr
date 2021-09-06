---
title: Azure Service Fabric 클러스터 만들기
description: Azure Resource Manager를 사용하여 Azure에 보안 Service Fabric 클러스터를 설정하는 방법에 대해 알아봅니다.  기본 템플릿을 사용하거나 사용자 고유의 클러스터 템플릿을 사용하여 클러스터를 만들 수 있습니다.
ms.topic: conceptual
ms.date: 08/16/2018
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7323f6762fe6c55f1ab548f1c8196ee761014a2d
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112463880"
---
# <a name="create-a-service-fabric-cluster-using-azure-resource-manager"></a>Azure Resource Manager를 사용하여 Service Fabric 클러스터 만들기 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
>
>

[Azure Service Fabric 클러스터](service-fabric-deploy-anywhere.md)는 마이크로 서비스가 배포되고 관리되는 네트워크로 연결된 가상 머신 집합입니다.  Azure에서 실행 중인 Service Fabric 클러스터는 Azure 리소스이며, Azure Resource Manager를 사용하여 배포됩니다. 이 문서에서는 Resource Manager를 사용하여 Azure에 보안 Service Fabric 클러스터를 설정하는 방법을 설명합니다. 기본 클러스터 템플릿 또는 사용자 지정 템플릿을 사용할 수 있습니다.  사용자 지정 템플릿이 아직 없는 경우 [템플릿 만드는 방법을 알아볼](service-fabric-cluster-creation-create-template.md) 수 있습니다.

클러스터 보안을 위해 선택한 보안 유형(예: Windows ID, X509 등)은 클러스터를 처음으로 만들 때 지정해야 하며 이후에는 변경할 수 없습니다. 클러스터를 설정하기 전에 [Service Fabric 클러스터 보안 시나리오][service-fabric-cluster-security]를 읽어보세요. Azure에서 Service Fabric은 x509 인증서를 사용하여 클러스터 및 해당 엔드포인트를 보호하고, 클라이언트를 인증하고, 데이터를 암호화합니다. 관리 엔드포인트에 대한 액세스를 보호하려면 Azure Active Directory를 사용하는 것도 좋습니다. 자세한 내용은 [클라이언트를 인증하도록 Azure AD 설정](service-fabric-cluster-creation-setup-aad.md)을 읽어보세요.

프로덕션 워크로드를 실행할 프로덕션 클러스터를 만들려면 먼저 [프로덕션 준비 검사 목록](service-fabric-production-readiness-checklist.md)을 읽어보는 것이 좋습니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항 
이 문서에서는 Service Fabric RM Powershell 또는 Azure CLI 모듈을 사용하여 클러스터를 배포합니다.

* [Azure PowerShell 4.1 이상][azure-powershell]
* [Azure CLI 버전 2.0 이상][azure-CLI]

Service Fabric 모듈에 대한 참조 설명서를 여기에서 찾을 수 있습니다.
* [Az.ServiceFabric](/powershell/module/az.servicefabric)
* [az SF CLI module](/cli/azure/sf)

### <a name="sign-in-to-azure"></a>Azure에 로그인

이 문서의 명령을 실행하기 전에 먼저 Azure에 로그인합니다.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subscriptionId>
```

```azurecli
az login
az account set --subscription $subscriptionId
```

## <a name="create-a-new-cluster-using-a-system-generated-self-signed-certificate"></a>시스템 생성 자체 서명된 인증서를 사용하여 새 클러스터 만들기

시스템 생성 자체 서명된 인증서로 보호되는 클러스터를 만들려면 다음 명령을 사용합니다. 이 명령은 클러스터 보안을 위해 사용되는 기본 클러스터 인증서를 설정하고, 해당 인증서를 사용하여 관리 작업을 수행할 관리자 액세스를 설정합니다.  자체 서명된 인증서는 테스트 클러스터를 보호하는 데 유용합니다.  프로덕션 클러스터는 CA(인증 기관)의 인증서로 보호되어야 합니다.

### <a name="use-the-default-cluster-template-that-ships-in-the-module"></a>모듈에 제공되는 기본 클러스터 템플릿 사용

다음 PowerShell 또는 Azure CLI 명령을 사용하여 기본 템플릿으로 클러스터를 신속하게 만들 수 있습니다.

여기에서 [Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG)용, 여기에서 [Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)용 기본 템플릿을 사용할 수 있습니다.

다음 명령은 OS 매개 변수를 지정하는 방법에 따라 Windows 또는 Linux 클러스터를 만들 수 있습니다. PowerShell/CLI 명령은 모두 지정된 *CertificateOutputFolder* 인증서를 출력합니다(명령을 실행하기 전에 지정한 인증서 폴더 위치가 이미 있는지 확인).

> [!NOTE]
> 다음 PowerShell 명령은 Azure PowerShell `Az` 모듈에만 사용할 수 있습니다. Azure Resource Manager PowerShell 버전의 현재 버전을 확인하려면 다음 PowerShell 명령 "Get-Module Az"를 실행합니다. Azure Resource Manager PowerShell 버전을 업그레이드하려면 [다음 링크](/powershell/azure/install-Az-ps)를 따릅니다.

PowerShell을 사용하여 클러스터 배포

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password123!@#" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
```

Azure CLI를 사용하여 클러스터 배포

```azurecli
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!4321"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser
```

### <a name="use-your-own-custom-template"></a>사용자 고유의 사용자 지정 템플릿 사용

필요에 맞게 사용자 지정 템플릿을 작성해야 하는 경우 [Azure Service Fabric 템플릿 샘플](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)에서 사용할 수 있는 템플릿 중 하나로 시작하는 것이 좋습니다. [클러스터 템플릿을 사용자 지정][customize-your-cluster-template]하는 방법에 대해 알아봅니다.

이미 사용자 지정 템플릿이 있는 경우 템플릿 및 매개 변수 파일에 있는 모든 세 개의 인증서 관련 매개 변수 이름이 다음과 같고 값은 다음과 같이 Null인지 두 번 확인합니다.

```json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

PowerShell을 사용하여 클러스터 배포

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Azure CLI를 사용하여 클러스터 배포

```azurecli
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath
```

## <a name="create-a-new-cluster-using-your-own-x509-certificate"></a>사용자 고유의 X.509 인증서를 사용하여 새 클러스터 만들기

다음 명령을 사용하여 새 클러스터를 만들고 보호할 기존 인증서를 지정할 수 있습니다.

결국 다른 용도로 사용할 CA 서명 인증서인 경우 키 자격 증명 모음에 특별히 고유한 리소스 그룹을 제공하는 것이 좋습니다. Key Vault를 자체 리소스 그룹에 배치하는 것이 좋습니다. 이렇게 하면 키 및 비밀은 유실하지 않고 Service Fabric 클러스터가 있는 리소스 그룹과 같은 컴퓨팅 및 스토리지 리소스 그룹을 제거할 수 있습니다. **키 자격 증명 모음을 포함하는 리소스 그룹은 해당 그룹을 사용하는 클러스터와 *동일한 지역에 있어야* 합니다.**

### <a name="use-the-default-five-node-one-node-type-template-that-ships-in-the-module"></a>모듈에 제공되는 기본 5개 노드 1개 노드 형식 템플릿 사용

여기에서 [Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG)용, 여기에서 [Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)용 기본 템플릿을 사용할 수 있습니다.

PowerShell을 사용하여 클러스터 배포

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
```

Azure CLI를 사용하여 클러스터 배포

```azurecli
declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser
```

### <a name="use-your-own-custom-cluster-template"></a>사용자 고유의 사용자 지정 클러스터 템플릿 사용
필요에 맞게 사용자 지정 템플릿을 작성해야 하는 경우 [Azure Service Fabric 템플릿 샘플](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)에서 사용할 수 있는 템플릿 중 하나로 시작하는 것이 좋습니다. [클러스터 템플릿을 사용자 지정][customize-your-cluster-template]하는 방법에 대해 알아봅니다.

이미 사용자 지정 템플릿이 있는 경우 템플릿 및 매개 변수 파일에 있는 모든 세 개의 인증서 관련 매개 변수 이름이 다음과 같고 값에는 다음과 같이 Null인지 두 번 확인하도록 합니다.

```json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

PowerShell을 사용하여 클러스터 배포

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
```

Azure CLI를 사용하여 클러스터 배포

```azurecli
declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

### <a name="use-a-pointer-to-a-secret-uploaded-into-a-key-vault"></a>키 자격 증명 모음에 업로드된 비밀에 대해 포인터 사용

기존 Key Vault를 사용하려면 컴퓨팅 리소스 공급자가 인증서를 가져와 클러스터 노드에 설치할 수 있도록 Key Vault가 [배포에 대해 사용하도록 설정](../key-vault/general/manage-with-cli2.md#bkmk_KVperCLI)되어야 합니다.

PowerShell을 사용하여 클러스터 배포

```powershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretID -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Azure CLI를 사용하여 클러스터 배포

```azurecli
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier $secretID  \
    --template-file $templateFilePath --parameter-file $parameterFilePath 
```

## <a name="next-steps"></a>다음 단계
이 시점에 Azure에서 실행되는 보안 클러스터가 생깁니다. 다음으로, [클러스터에 연결](service-fabric-connect-to-secure-cluster.md)하고 [애플리케이션 비밀을 관리](service-fabric-application-secret-management.md)하는 방법을 알아봅니다.

템플릿을 사용하는 데 필요한 JSON 구문 및 속성은 [Microsoft.ServiceFabric/clusters 템플릿 참조](/azure/templates/microsoft.servicefabric/clusters)에서 확인하세요.

<!-- Links -->
[azure-powershell]:/powershell/azure/install-Az-ps
[azure-CLI]:/cli/azure/get-started-with-azure-cli
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[customize-your-cluster-template]: service-fabric-cluster-creation-create-template.md
