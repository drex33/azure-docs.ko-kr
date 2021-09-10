---
title: Azure Image Builder를 사용하여 기존 이미지 버전에서 새로운 Windows 이미지 버전 만들기
description: Azure Image Builder를 사용하여 기존 이미지 버전에서 새로운 Windows VM 이미지 버전을 만듭니다.
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.collection: windows
ms.openlocfilehash: 166e9b2b2ea98027c4ca9a8e13c5c26d68214d9a
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769078"
---
# <a name="create-a-new-windows-vm-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Azure Image Builder를 사용하여 기존 이미지 버전에서 새로운 Windows VM 이미지 버전 만들기

**적용 대상:** :heavy_check_mark: Windows VM

본 문서에서는 [Shared Image Gallery](../shared-image-galleries.md)에서 기존 이미지 버전을 가져오기, 해당 이미지 업데이트하기 및 업데이트한 이미지를 갤러리에 새 이미지 버전으로 게시하기에 대한 방법을 알려줍니다.

이미지를 구성하는 데 샘플 .json 템플릿을 사용합니다. 사용할 .json 파일은 다음 위치에 있습니다: [helloImageTemplateforSIGfromWinSIG.json](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json). 


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

Shared Image Gallery를 만들기 위하여 [이미지를 만들어 Shared Image Gallery에 배포하기](image-builder-gallery.md)를 사용한 경우, 필요한 변수를 이미 만들어 놓은 것입니다. 그렇지 않은 경우라면 해당 예제에서 사용할 몇 가지 변수를 설정하세요.

Image Builder는 원본 관리 이미지와 동일한 리소스 그룹에서 사용자 지정 이미지를 만드는 것만 지원합니다. 이 예제의 리소스 그룹 이름을 원본 관리 이미지와 동일한 리소스 그룹으로 업데이트합니다.

```azurecli-interactive
# Resource group name - we are using ibsigRG in this example
sigResourceGroup=myIBWinRG
# Datacenter location - we are using West US 2 in this example
location=westus
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=my22stSIG
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=winSvrimages
# image distribution metadata reference name
runOutputName=w2019SigRo
# User name and password for the VM
username="user name for the VM"
vmpassword="password for the VM"
```

구독 ID에 대한 변수를 만듭니다.

```azurecli-interactive
subscriptionID=$(az account show --query id --output tsv)
```

업데이트하려는 이미지 버전을 가져옵니다.

```azurecli-interactive
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

Shared Image Gallery가 이미 있으면서 이전 예제를 따르지 않은 경우에는 리소스 그룹에 액세스하여 갤러리에 액세스할 수 있도록 Image Builder에 대한 권한을 할당하여야 합니다. [이미지를 만들어 Shared Image Gallery에 배포하기](image-builder-gallery.md) 예제의 단계들을 검토하세요.


## <a name="modify-helloimage-example"></a>helloImage 예제 수정하기
[helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json)의 .json 파일을 [Image Builder 템플릿 참조](../linux/image-builder-json.md)와 함께 열어서 사용하려는 예제를 검토할 수 있습니다. 


해당 .json 예제를 다운로드하여 자신의 변수로 구성합니다. 

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json -o helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIGfromWinSIG.json
```

## <a name="create-the-image"></a>이미지 만들기

이미지 구성을 VM Image Builder 서비스에 제출합니다.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --location $location \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

이미지 빌드를 시작합니다.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

다음 단계로 넘어가기 전에 이미지가 빌드되고 복제될 때까지 기다립니다.


## <a name="create-the-vm"></a>VM 만들기

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm002 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```

## <a name="verify-the-customization"></a>사용자 지정 확인
VM을 만들 때 설정한 사용자 이름 및 암호를 사용하여 VM에 대한 원격 데스크톱 연결을 만듭니다. VM 내에서 cmd 프롬프트를 열고 다음을 입력합니다.

```console
dir c:\
```

이제 디렉터리가 다음과 같이 두 개로 표시됩니다.
- `buildActions`는 첫 번째 이미지 버전에서 만든 것입니다.
- `buildActions2`는 두 번째 이미지 버전을 만들기 위하여 첫 번째 이미지 버전에 약간의 업데이트를 가하여 만든 것입니다.


## <a name="next-steps"></a>다음 단계

이 문서에서 사용한 .json 파일 구성 요소에 대한 자세한 내용은 [Image Builder 템플릿 참조](../linux/image-builder-json.md)를 확인하세요.
