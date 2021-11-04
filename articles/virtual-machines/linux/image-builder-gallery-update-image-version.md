---
title: Azure Image Builder를 사용하여 기존의 이미지 버전에서 새로운 VM 이미지 버전을 만들기
description: Linux에서 Azure Image Builder를 사용하여 기존의 이미지 버전에서 새로운 VM 이미지 버전을 만듭니다.
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 03/02/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.openlocfilehash: c0348e159b14e400e7787da7e1c04de375f96815
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437147"
---
# <a name="create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder-in-linux"></a>Linux에서 Azure Image Builder를 사용하여 기존의 이미지 버전에서 새로운 VM 이미지 버전을 만듭니다.

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

이 문서에서는 [Azure Compute 갤러리](../shared-image-galleries.md) (이전의 공유 이미지 갤러리)에서 기존 이미지 버전을 가져와서 업데이트 하 고 갤러리에 새 이미지 버전으로 게시 하는 방법을 보여 줍니다.

이미지를 구성하는 데 샘플 .json 템플릿을 사용합니다. 사용할 .json 파일은 [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json)입니다. 


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

이미지 만들기를 사용 하 [고 Azure Compute 갤러리에 배포](image-builder-gallery.md) 하 여 Azure compute 갤러리를 만든 경우 필요한 변수 중 일부를 이미 만들었습니다. 그렇지 않은 경우라면 해당 예제에서 사용할 몇 가지 변수를 설정하세요.


```console
# Resource group name 
sigResourceGroup=ibLinuxGalleryRG
# Gallery location 
location=westus2
# Additional region to replicate the image version to 
additionalregion=eastus
# Name of the Azure Compute Gallery 
sigName=myIbGallery
# Name of the image definition to use
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibSIGLinuxUpdate
```

구독 ID에 대한 변수를 만듭니다.

```console
subscriptionID=$(az account show --query id --output tsv)
```

업데이트하려는 이미지 버전을 가져옵니다.

```azurecli
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o tsv)
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>사용자 할당 ID 만들기 및 리소스 그룹에 대한 사용 권한 설정
이전 예제에서 사용자 ID를 이미 설정하였기 때문에 템플릿에 추가할 리소스 ID만 가져오면 됩니다.

```azurecli-interactive
#get identity used previously
imgBuilderId=$(az identity list -g $sigResourceGroup --query "[?contains(name, 'aibBuiUserId')].id" -o tsv)
```

사용자 고유의 Azure Compute 갤러리가 이미 있고 이전 예제를 따르지 않은 경우 리소스 그룹에 액세스할 수 있도록 이미지 작성기에 대 한 사용 권한을 할당 해야 합니다. 그러면 갤러리에 액세스할 수 있습니다. [이미지 만들기 및 Azure 계산 갤러리에 배포](image-builder-gallery.md) 예제의 단계를 검토 하세요.


## <a name="modify-helloimage-example"></a>helloImage 예제 수정하기
[helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json)의 .json 파일을 [Image Builder 템플릿 참조](image-builder-json.md)와 함께 열어서 사용하려는 예제를 검토할 수 있습니다. 


해당 .json 예제를 다운로드하여 자신의 변수로 구성합니다. 

```console
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json -o helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIGfromSIG.json
```

## <a name="create-the-image"></a>이미지 만들기

이미지 구성을 VM Image Builder 서비스에 제출합니다.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

이미지 빌드를 시작합니다.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

다음 단계로 넘어가기 전에 이미지가 빌드되고 복제될 때까지 기다립니다.


## <a name="create-the-vm"></a>VM 만들기

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgVm001 \
  --admin-username azureuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

VM의 공용 IP 주소를 사용하여 VM과 SSH 연결을 만듭니다.

```console
ssh azureuser@<pubIp>
```

SSH 연결이 설정되는 즉시 “오늘의 메시지”로 이미지가 사용자 지정된 것을 볼 수 있을 것입니다.

```output
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

`exit`를 입력하여 SSH 연결을 닫습니다.

갤러리에서 현재 사용할 수 있는 이미지 버전을 나열할 수도 있습니다.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>다음 단계

이 문서에서 사용한 .json 파일 구성 요소에 대한 자세한 내용은 [Image Builder 템플릿 참조](../linux/image-builder-json.md)를 확인하세요.
