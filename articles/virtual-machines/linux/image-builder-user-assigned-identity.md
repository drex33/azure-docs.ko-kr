---
title: 가상 머신 이미지를 만들고 사용자 할당 관리형 ID를 사용하여 Azure Storage의 파일에 액세스하기
description: 사용자 할당 관리형 ID를 사용하여 Azure Storage에 저장한 파일에 액세스할 수 있는 Azure Image Builder를 사용하여 가상 머신 이미지를 만듭니다.
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.openlocfilehash: 588e32e2a531f08319a3120a99ca70048c4c24b2
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770413"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>이미지를 만들고 사용자 할당 관리형 ID를 사용하여 Azure Storage의 파일에 액세스하기 

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

Azure Image Builder는 GitHub 및 Azure Storage 등 다양한 위치에서 스크립트를 사용하거나 파일을 복사하도록 지원합니다. 해당 기능을 사용하려면 스크립트나 파일이 외부에서 Azure Image Builder에 액세스할 수 있어야 하지만, Azure Storage blob을 SAS 토큰을 사용하여 보호할 수 있습니다.

본 문서에서는 Azure VM Image Builder를 사용하여 사용자 지정 이미지를 만드는 방법을 보여 주며, 이 때 서비스는 [사용자 할당 관리형 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 사용하여 직접 파일을 공개적으로 액세스할 수 있는 상태로 만들거나 SAS 토큰을 설정하지 않고도 이미지 사용자 지정을 위하여 Azure Storage의 이미지에 액세스합니다.

아래 예제에서는 사용자 지정 이미지에 사용할 리소스 그룹 하나와 Azure Storage 계정을 호스트할, 스크립트 파일이 들어 있는 리소스 그룹 하나로 된 총 두 개의 리소스 그룹을 만듭니다. Image Builder 외부에 위치한 각기 다른 스토리지 계정에 빌드 아티팩트나 이미지 파일이 있는 경우의 실제 시나리오를 시뮬레이션합니다. 사용자 할당 ID를 만든 뒤 여기에 스크립트 파일에 대한 읽기 권한을 주되 해당 파일에 대한 퍼블릭 액세스는 설정하지 않습니다. 이후, 셸 사용자 지정자를 사용하여 해당 스크립트를 해당 스토리지 계정에서 다운로드하여 실행합니다.


## <a name="register-the-features"></a>기능 등록
Azure Image Builder를 사용하려면 해당 기능을 등록해야 합니다.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

기능 등록 상태를 확인합니다.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

등록을 확인합니다.


```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.KeyVault | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
az provider show -n Microsoft.Network | grep registrationState
```

등록되지 않은 경우 다음을 실행합니다.

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Network
```


## <a name="create-a-resource-group"></a>리소스 그룹 만들기

일부 정보를 반복해서 사용하게 되며, 해당 정보를 저장하기 위해 몇 가지 변수를 만듭니다.


```console
# Image resource group name 
imageResourceGroup=aibmdimsi
# storage resource group
strResourceGroup=aibmdimsistor
# Location 
location=WestUS2
# name of the image to be created
imageName=aibCustLinuxImgMsi01
# image distribution metadata reference name
runOutputName=u1804ManImgMsiro
```

구독 ID에 대한 변수를 만듭니다.

```console
subscriptionID=$(az account show --query id --output tsv)
```

이미지와 스크립트 스토리지 둘 모두를 위한 리소스 그룹을 만듭니다.

```console
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```

사용자 할당 ID를 만들고 리소스 그룹에 대한 권한을 설정합니다.

Image Builder는 해당 리소스 그룹에 이미지를 삽입하기 위하여 주어진 [사용자 ID](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity)를 이용합니다. 본 예제에서는 이미지 배포를 위하여 세분화된 작업을 갖춘 Azure 역할 정의를 만듭니다. 그러면 역할 정의가 user-identity에 할당됩니다.

```console
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $sigResourceGroup -n $identityName --query clientId -o tsv)

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Azure Image Builder Service Image Creation Role" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

스토리지를 만들어 샘플 스크립트를 GitHub에서 여기로 복사합니다.

```azurecli-interactive
# script storage account
scriptStorageAcc=aibstorscript$(date +'%s')

# script container
scriptStorageAccContainer=scriptscont$(date +'%s')

# script url
scriptUrl=https://$scriptStorageAcc.blob.core.windows.net/$scriptStorageAccContainer/customizeScript.sh

# create storage account and blob in resource group
az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS

az storage container create -n $scriptStorageAccContainer --fail-on-exist --account-name $scriptStorageAcc

# copy in an example script from the GitHub repo 
az storage blob copy start \
    --destination-blob customizeScript.sh \
    --destination-container $scriptStorageAccContainer \
    --account-name $scriptStorageAcc \
    --source-uri https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```

해당 이미지 리소스 그룹에 리소스를 만들 수 있게 Image Builder 권한을 부여합니다. `--assignee` 값이 사용자 ID입니다.

```azurecli-interactive
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
```




## <a name="modify-the-example"></a>해당 예제를 수정하기

.json 예제 파일을 다운로드하여 직접 만든 변수로 구성합니다.

```console
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>이미지 만들기

Azure Image Builder 서비스에 이미지 구성을 제출합니다.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

이미지 빌드를 시작합니다.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

빌드가 완료될 때까지 기다립니다. 15분 정도 걸릴 수 있습니다.

## <a name="create-a-vm"></a>VM 만들기

이미지에서 VM을 만듭니다. 

```azurecli
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

VM은 만든 다음, 해당 VM을 가지고 SSH 세션을 시작합니다.

```console
ssh aibuser@<publicIp>
```

SSH 연결이 설정되는 즉시 오늘의 메시지로 이미지가 사용자 지정된 것을 볼 수 있을 것입니다.

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>정리

작업이 완료되면 필요 없어진 리소스는 삭제할 수 있습니다.

```azurecli-interactive

az role definition delete --name "$imageRoleDefName"
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>다음 단계

Azure Image Builder 작동과 관련된 문제는 [문제 해결](image-builder-troubleshoot.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json)을 참조하세요.
