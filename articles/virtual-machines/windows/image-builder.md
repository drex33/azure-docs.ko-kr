---
title: Azure 이미지 작성기로 Windows VM 만들기
description: Azure Image Builder로 Windows VM을 만듭니다.
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 04/23/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.openlocfilehash: 651a67414c34bcfae45663dd1bcfbd9d97e63598
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122531071"
---
# <a name="create-a-windows-vm-with-azure-image-builder"></a>Azure 이미지 작성기로 Windows VM 만들기

본 문서에서는 Azure VM Image Builder를 사용하여 사용자 지정된 Windows 이미지를 만드는 방법을 보여 줍니다. 본 문서의 예제는 이미지 사용자 지정에 [사용자 지정자](../linux/image-builder-json.md#properties-customize)를 사용합니다.
- PowerShell(ScriptUri) - [PowerShell 스크립트](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1)를 다운로드하여 실행합니다.
- Windows 다시 시작 - VM을 다시 시작합니다.
- PowerShell(인라인) - 특정 명령을 실행합니다. 본 예제에서는, `mkdir c:\\buildActions`를 사용하여 VM에 디렉터리를 만듭니다.
- 파일 - GitHub에서 VM으로 파일을 복사합니다. 본 예제에서는 [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html)를 VM의 `c:\buildArtifacts\index.html`에 복사합니다.
- buildTimeoutInMinutes - 빌드가 더 오래 실행되도록 빌드 시간을 연장합니다. 기본값은 240분이며 빌드를 더 오래 실행하려면 빌드 시간을 연장할 수 있습니다.
- vmProfile - vmSize 및 네트워크 속성을 지정합니다.
- osDiskSizeGB - 이미지 크기를 늘릴 수 있습니다.
- ID - Azure Image Builder가 빌드 중에 사용할 ID를 제공합니다.


`buildTimeoutInMinutes`도 지정할 수 있습니다. 기본값은 240분이며 빌드를 더 오래 실행하려면 빌드 시간을 연장할 수 있습니다. 최소 허용 값은 6분입니다. 이 값이 더 짧으면 오류가 발생합니다.

이미지를 구성하는 데 샘플 .json 템플릿을 사용합니다. 사용할 .json 파일은 [helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json)입니다. 



> [!NOTE]
> Windows 사용자의 경우 아래 Azure CLI 예는 Bash를 사용하여 [Azure Cloud Shell](https://shell.azure.com)에서 실행할 수 있습니다.


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


## <a name="set-variables"></a>변수 설정

일부 정보를 반복해서 사용하게 되며, 해당 정보를 저장하기 위해 몇 가지 변수를 만듭니다.


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Name for the image 
imageName=myWinBuilderImage
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
```

구독 ID에 대한 변수를 만듭니다. `az account show | grep id`를 사용하여 만들 수 있습니다.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>리소스 그룹 만들기
해당 리소스 그룹은 이미지 구성 템플릿 아티팩트와 해당 이미지를 저장하는 데에 사용됩니다.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>사용자 할당 ID 만들기 및 리소스 그룹에 대한 사용 권한 설정
Image Builder는 이미지를 해당 리소스 그룹에 삽입하기 위하여 제공된 [사용자 ID](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity)를 사용합니다. 본 예제에서는 이미지 배포를 위하여 세분화된 작업을 갖춘 Azure 역할 정의를 만듭니다. 그러면 역할 정의가 user-identity에 할당됩니다.

## <a name="create-user-assigned-managed-identity-and-grant-permissions"></a>사용자 할당 관리 ID 만들고 사용 권한 부여하기 
```bash
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```



## <a name="download-the-image-configuration-template-example"></a>이미지 구성 템플릿 예제 다운로드

매개 변수가 있는 이미지 구성 템플릿을 사용하여 볼 수 있게 만들어 두었습니다. 예제 .json 파일을 다운로드하여 이전에 설정하여 둔 변수로 구성합니다.

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateWin.json

```

`vi` 같은 텍스트 편집기를 사용하는 터미널에서 해당 예제를 수정할 수 있습니다.

```azurecli-interactive
vi helloImageTemplateWin.json
```

> [!NOTE]
> 소스 이미지의 경우, 언제나 [버전을 지정](../linux/image-builder-troubleshoot.md#build--step-failed-for-image-version)하여야 하며, `latest`는 사용할 수 없습니다.
> 이미지를 배포할 리소스 그룹을 추가하거나 변경하는 경우, 리소스 그룹에서 [사용 권한을 설정](#create-a-user-assigned-identity-and-set-permissions-on-the-resource-group)하여야 합니다.
 
## <a name="create-the-image"></a>이미지 만들기

이미지 구성을 VM Image Builder 서비스에 제출하기

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

완료 시, 이러한 작업을 통하여 성공 메시지를 콘솔로 돌려보내며, `Image Builder Configuration Template`을 `$imageResourceGroup`에 만듭니다. ‘숨겨진 형식 표시’를 사용하도록 설정하였다면 Azure Portal의 리소스 그룹에서 해당 리소스를 볼 수 있습니다.

Image Builder는 백그라운드에서 구독에 준비 리소스 그룹을 만들기도 합니다. 해당 리소스 그룹은 이미지 빌드에 사용됩니다. `IT_<DestinationResourceGroup>_<TemplateName>`와 같은 형식입니다.

> [!Note]
> 준비 리소스 그룹을 바로 삭제하면 안됩니다. 이미지 템플릿 아티팩트를 먼저 삭제하면 준비 리소스 그룹도 따라 삭제됩니다.

이미지 구성 템플릿 제출 중에 서비스에서 오류를 보고하면 다음을 수행합니다.
-  이 [문제 해결](../linux/image-builder-troubleshoot.md#troubleshoot-image-template-submission-errors) 단계를 검토하세요. 
- 제출을 다시 시도하기 전에 다음의 코드 조각을 사용하여 템플릿을 삭제하여야 합니다.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>이미지 빌드 시작하기
[az 리소스 호출 작업](/cli/azure/resource#az_resource_invoke_action)을 사용하여 이미지 빌드 프로세스를 시작합니다.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

작업이 완료될 때까지 기다리세요. 15분 정도 걸릴 수 있습니다.

오류가 발생하면, [문제 해결](../linux/image-builder-troubleshoot.md#troubleshoot-common-build-errors) 단계를 검토하세요.


## <a name="create-the-vm"></a>VM 만들기

빌드한 이미지를 사용하여 VM을 만듭니다. *\<password>* 를 VM의 `aibuser`에서 자신의 암호로 바꿉니다.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>사용자 지정 확인

VM을 만들 때 설정한 사용자 이름 및 암호를 사용하여 VM에 대한 원격 데스크톱 연결을 만듭니다. VM 내에서 cmd 프롬프트를 열고 다음을 입력합니다.

```console
dir c:\
```

이미지 사용자 지정 중에 생성되는 다음 두 개의 디렉터리를 확인하여야 합니다.
- buildActions
- buildArtifacts

## <a name="clean-up"></a>정리

작업이 완료되면 리소스를 삭제합니다.

### <a name="delete-the-image-builder-template"></a>Image Builder 템플릿 삭제

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-role-assignment-role-definition-and-user-identity"></a>역할 할당, 역할 정의, 사용자 ID를 삭제합니다.
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

### <a name="delete-the-image-resource-group"></a>이미지 리소스 그룹 삭제

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>다음 단계

이 문서에서 사용한 .json 파일 구성 요소에 대한 자세한 내용은 [Image Builder 템플릿 참조](../linux/image-builder-json.md)를 확인하세요.
