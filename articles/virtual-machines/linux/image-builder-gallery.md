---
title: Linux VM용 Azure Image Builder 및 Shared Image Gallery 사용
description: Azure Image Builder 및 Azure CLI를 사용하여 Shared Image Gallery에서 이미지 버전을 만든 다음, 전 세계에 이미지를 배포하는 방법을 알아봅니다.
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.openlocfilehash: ead4462c6dcff231b6127830ca24732bd7e3ef45
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769096"
---
# <a name="create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>Linux 이미지를 만들어 공유 이미지 갤러리에 배포

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합

이 문서에서는 Azure Image Builder 및 Azure CLI를 사용하여 [Shared Image Gallery](../shared-image-galleries.md)에서 이미지 버전을 만든 다음, 전 세계에 이미지를 배포하는 방법을 보여 줍니다. [Azure PowerShell](../windows/image-builder-gallery.md)를 사용하여 이 작업을 수행할 수도 있습니다.


이미지를 구성하는 데 샘플 .json 템플릿을 사용합니다. 사용할 .json 파일은 [helloImageTemplateforSIG.json](https://github.com/azure/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json)입니다. 

Shared Image Gallery에 이미지를 배포하기 위해 이 템플릿에서는 [sharedImage](image-builder-json.md#distribute-sharedimage)를 템플릿의 `distribute` 섹션 값으로 사용합니다.


## <a name="register-the-features"></a>기능 등록

Azure Image Builder를 사용하려면 이 기능을 등록해야 합니다.

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

## <a name="set-variables-and-permissions"></a>변수 및 사용 권한 설정 

일부 정보를 반복해서 사용하게 되며, 해당 정보를 저장하기 위해 몇 가지 변수를 만듭니다.

Image Builder는 원본 관리 이미지와 동일한 리소스 그룹에서 사용자 지정 이미지를 만드는 것만 지원합니다. 이 예제의 리소스 그룹 이름을 원본 관리 이미지와 동일한 리소스 그룹으로 업데이트합니다.

```azurecli-interactive
# Resource group name - we are using ibLinuxGalleryRG in this example
sigResourceGroup=ibLinuxGalleryRG
# Datacenter location - we are using West US 2 in this example
location=westus2
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=myIbGallery
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibLinuxSIG
```

구독 ID에 대한 변수를 만듭니다.

```azurecli-interactive
subscriptionID=$(az account show --query id --output tsv)
```

리소스 그룹을 만듭니다.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>사용자 할당 ID 만들기 및 리소스 그룹에 대한 사용 권한 설정
Image Builder는 제공된 [user-identity](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity)를 사용하여 Azure SIG(Shared Image Gallery)에 이미지를 삽입합니다. 이 예제에서는 SIG에 이미지를 배포하는 세분화된 작업을 포함하는 Azure 역할 정의를 만듭니다. 그러면 역할 정의가 user-identity에 할당됩니다.

```bash
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $sigResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $sigResourceGroup -n $identityName --query clientId -o tsv)

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$sigResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# this command will download an Azure role definition template, and update the template with the parameters specified earlier.
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="create-an-image-definition-and-gallery"></a>이미지 정의 및 갤러리 만들기

Shared Image Gallery에서 Image Builder를 사용하려면 기존 이미지 갤러리 및 이미지 정의가 있어야 합니다. Image Builder는 이미지 갤러리 및 이미지 정의를 만들지 않습니다.

사용할 갤러리 및 이미지 정의가 아직 없는 경우 만들어야 합니다. 먼저 이미지 갤러리를 만듭니다.

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

그런 다음 이미지 정의를 만듭니다.

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>.json 다운로드 및 구성

.json 템플릿을 다운로드하고 변수로 구성합니다.

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>이미지 버전 만들기

다음으로 갤러리에서 이미지 버전을 만듭니다. 

Azure Image Builder 서비스에 이미지 구성을 제출합니다.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

이미지 빌드를 시작합니다.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

이미지를 만들고 두 지역에 복제하는 데 다소 시간이 걸릴 수 있습니다. VM 만들기를 계속 진행하기 전에 이 단계가 완료될 때까지 기다립니다.


## <a name="create-the-vm"></a>VM 만들기

Azure Image Builder에서 만든 이미지 버전에서 VM을 만듭니다.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

VM에 SSH합니다.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

SSH 연결이 설정되는 즉시 오늘의 메시지로 이미지가 사용자 지정된 것을 볼 수 있을 것입니다.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>리소스 정리

이제 이미지 버전을 다시 사용자 지정하여 동일한 이미지의 새 버전을 만들려면 다음 단계를 건너뛰고 [Azure Image Builder를 사용하여 다른 이미지 버전 만들기](image-builder-gallery-update-image-version.md)를 계속 진행합니다.


그러면 생성된 이미지와 다른 모든 리소스 파일이 삭제됩니다. 리소스를 삭제하기 전에 이 배포를 완료했는지 확인합니다.

이미지 갤러리 리소스를 삭제하는 경우 먼저 모든 이미지 버전을 삭제해야 해당 이미지 버전을 만드는 데 사용된 이미지 정의를 삭제할 수 있습니다. 갤러리를 삭제하려면 먼저 갤러리에서 이미지 정의를 모두 삭제해야 합니다.

Image Builder 템플릿을 삭제합니다.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

권한 할당, 역할 및 ID 삭제
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

Image Builder에서 만든 이미지 버전(항상 `0.`으로 시작)을 가져와 이미지 버전을 삭제합니다.

```azurecli-interactive
sigDefImgVersion=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'name' -o json | grep 0. | tr -d '"')
az sig image-version delete \
   -g $sigResourceGroup \
   --gallery-image-version $sigDefImgVersion \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```   


이미지 정의를 삭제합니다.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

갤러리를 삭제합니다.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

해당 리소스 그룹을 삭제합니다.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>다음 단계

[Azure Shared Image Gallery](../shared-image-galleries.md)에 대해 자세히 알아보세요.
