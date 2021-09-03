---
title: '미리 보기: Azure Image Builder Service DevOps 작업'
description: 애플리케이션과 OS를 설치하고 구성할 수 있도록 VM 이미지에 빌드 아티팩트를 넣는 Azure DevOps 작업입니다.
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 01/27/2021
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2c3785f20c47656304f7d40b67c9d90c54cb3072
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114447083"
---
# <a name="azure-image-builder-service-devops-task-preview"></a>Azure Image Builder Service DevOps 작업(미리 보기)

이 문서에서는 애플리케이션과 OS를 설치하고 구성할 수 있도록 Azure DevOps 작업을 사용하여 VM 이미지에 빌드 아티팩트를 넣는 방법을 보여줍니다.

## <a name="devops-task-versions"></a>DevOps 작업 버전
다음과 같은 2개의 AIB(Azure VM Image Builder) DevOps 작업이 있습니다.

* ['안정' AIB 작업](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder): 테스트를 거친 안정적인 최신 빌드이며 원격 분석에 아무 문제가 없습니다. 


* ['불안정' AIB 작업](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder-canary): 최신 업데이트와 기능을 적용할 수 있으며 '안정' 작업으로 승격하기 전에 고객이 테스트할 수 있습니다. 보고된 문제가 없고 약 일주일 후에 원격 분석에서 문제가 보이지 않는 경우 작업 코드가 '안정'으로 승격됩니다. 

## <a name="prerequisites"></a>사전 요구 사항

> [!NOTE]
> AIB 작업은 현재 관리자로 상승된 명령을 실행하는 Windows 다시 시작을 지원하지 않습니다. 즉, 위 작업이 필요한 Windows Virtual Desktop 시나리오 또는 Windows 사용자 지정에는 적합하지 않습니다. Image Builder에서 DevOps를 사용하려는 경우 템플릿을 Azure Resource Manager 작업으로 중첩하고 AZ CLI 또는 PowerShell 작업을 사용해야 합니다.

* [Visual Studio Marketplace에서 안정적 DevOps 작업](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder)을 설치합니다.
* VSTS DevOps 계정이 있고 빌드 파이프라인이 만들어져 있어야 합니다.
* 파이프라인에서 사용하는 구독에서 Image Builder 기능 요구 사항을 등록하고 사용하도록 설정합니다.
    * [Az PowerShell](../windows/image-builder-powershell.md#register-features)
    * [Az CLI](../windows/image-builder.md#register-the-features)
    
* 원본 이미지 리소스 그룹에서 표준 Azure Storage 계정을 만들고 다른 리소스 그룹/스토리지 계정을 사용할 수 있습니다. 스토리지 계정은 DevOps 작업에서 이미지로 빌드 아티팩트를 전송하는 데 사용됩니다.

    ```powerShell
    # Az PowerShell
    $timeInt=$(get-date -UFormat "%s")
    $storageAccName="aibstorage"+$timeInt
    $location=westus
    # create storage account and blob in resource group
    New-AzStorageAccount -ResourceGroupName $strResourceGroup -Name $storageAccName -Location $location -SkuName Standard_LRS
    ```

    ```bash
    # Az CLI
    location=westus
    scriptStorageAcc=aibstordot$(date +'%s')
    # create storage account and blob in resource group
    az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS
    ```

## <a name="add-task-to-release-pipeline"></a>릴리스 파이프라인에 작업 추가

**릴리스 파이프라인** > **편집** 을 선택합니다.

사용자 에이전트에서 *+* 를 선택하여 **Image Builder** 를 검색합니다. **추가** 를 선택합니다.

다음 작업 속성을 설정합니다.

### <a name="azure-subscription"></a>Azure 구독

드롭다운 메뉴에서 Image Builder를 실행할 구독을 선택합니다. 원본 이미지가 있고 이미지를 배포할 동일한 구독을 사용합니다. 구독 또는 리소스 그룹에 대한 Image Builder 기여자 액세스 권한을 부여해야 합니다.

### <a name="resource-group"></a>리소스 그룹

임시 이미지 템플릿 아티팩트가 저장될 리소스 그룹을 사용합니다. 템플릿 아티팩트를 만들 때 추가 임시 Image Builder 리소스 그룹 `IT_<DestinationResourceGroup>_<TemplateName>_guid`이 만들어집니다. 임시 리소스 그룹은 스크립트와 같은 이미지 메타데이터를 저장합니다. 작업이 끝나면 이미지 템플릿 아티팩트와 임시 Image Builder 리소스 그룹이 삭제됩니다.
 
### <a name="location"></a>위치

위치는 Image Builder가 실행될 지역입니다. 설정된 수의 [지역](../image-builder-overview.md#regions)만 지원됩니다. 원본 이미지가 이 위치에 있어야 합니다. 예를 들어 Shared Image Gallery를 사용하는 경우 복제본이 해당 지역에 있어야 합니다.

### <a name="managed-identity-required"></a>관리 ID(필수)
Image Builder에는 원본 사용자 지정 이미지를 읽고, Azure Storage에 연결하고, 사용자 지정 이미지를 만드는 데 사용하는 관리 ID가 필요합니다. 자세한 내용은 [Azure Image Builder에 대한 자세한 정보](../image-builder-overview.md#permissions)를 참조하세요.

### <a name="vnet-support"></a>VNET 지원

현재 DevOps 작업은 기존 서브넷 지정을 지원하지 않습니다. 기존 서브넷이 로드맵에 있지만 기존 VNET을 활용하려는 경우 내부에 Image Builder 템플릿이 중첩되어 있는 ARM 템플릿을 사용할 수 있습니다. 이를 수행하는 방법에 대한 Windows Image Builder 템플릿 예제를 참조하거나 [AZ AIB PowerShell](../windows/image-builder-powershell.md)을 사용하세요.

### <a name="source"></a>원본

원본 이미지는 지원되는 Image Builder OS의 이미지여야 합니다. Image Builder가 실행 중인 지역과 동일한 지역에서 기존 사용자 지정 이미지를 선택할 수 있습니다.
* 관리 이미지 - resourceId를 전달해야 합니다. 예를 들면 다음과 같습니다.
    ```json
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```
* Azure Shared Image Gallery - 이미지 버전의 resourceId를 전달해야 합니다. 예를 들면 다음과 같습니다.
    ```json
    /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/<versionNumber>
    ```

    최신 Shared Image Gallery 버전을 가져와야 하는 경우 AZ PowerShell 또는 AZ CLI 작업을 통해 최신 버전을 가져오고 DevOps 변수를 설정할 수 있습니다. AZ VM Image Builder DevOps 작업에서 변수를 사용합니다. 자세한 내용은 [예제](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/8_Getting_Latest_SIG_Version_ResID#getting-the-latest-image-version-resourceid-from-shared-image-gallery)를 참조하세요.

* (마켓플레이스) 기본 이미지: 인기 이미지의 드롭다운 목록이 있으며 항상 지원되는 OS의 '최신' 버전을 사용합니다. 

    기본 이미지가 목록에 없는 경우 `Publisher:Offer:Sku`를 사용하여 정확한 이미지를 지정할 수 있습니다.

    기본 이미지 버전(선택 사항) - 사용할 이미지의 버전을 제공할 수 있습니다. 기본값은 `latest`입니다.

### <a name="customize"></a>사용자 지정

#### <a name="provisioner"></a>프로비저닝 프로그램

처음에는 2개의 사용자 지정자, 즉 **Shell** 및 **PowerShell** 이 지원됩니다. 인라인만 지원됩니다. 스크립트를 다운로드하려는 경우 인라인 명령을 전달하면 됩니다.

OS의 경우 PowerShell 또는 Shell을 선택합니다.

#### <a name="windows-update-task"></a>Windows 업데이트 작업

Windows의 경우에만 사용자 지정이 끝날 때 작업이 Windows 업데이트를 실행합니다. 또한 필요한 재부팅을 처리합니다.

다음 Windows 업데이트 구성이 실행됩니다.
```json
    "type": "WindowsUpdate",
    "searchCriteria": "IsInstalled=0",
    "filters": [
        "exclude:$_.Title -like '*Preview*'",
        "include:$true"
```
미리 보기가 아닌 중요 권장 Windows 업데이트가 설치됩니다.

#### <a name="handling-reboots"></a>다시 부팅 처리
현재 DevOps 작업은 Windows 빌드 다시 부팅을 지원하지 않습니다. PowerShell 코드로 다시 부팅하려고 하면 빌드가 실패합니다. 그러나 코드를 사용하여 Linux 빌드를 다시 부팅할 수 있습니다.

#### <a name="build-path"></a>빌드 경로

이 작업은 이미지에 DevOps 빌드 릴리스 아티팩트를 넣을 수 있도록 설계되었습니다. 이를 위해서는 빌드 파이프라인을 설정해야 합니다. 릴리스 파이프라인 설정에서 빌드 아티팩트의 리포지토리를 추가해야 합니다.

:::image type="content" source="./media/image-builder-devops-task/add-artifact.png" alt-text="릴리스 파이프라인에서 아티팩트 추가를 선택합니다.":::

**빌드 경로** 단추를 선택하여 이미지에 배치할 빌드 폴더를 선택합니다. Image Builder 작업은 그 안에 있는 모든 파일과 디렉터리를 복사합니다. 이미지를 만들 때 Image Builder는 OS에 따라 다른 경로에 파일과 디렉터리를 배포합니다.

> [!IMPORTANT]
> 리포지토리 아티팩트를 추가하는 경우 디렉터리에 밑줄 *_* 이 접두사로 붙을 수 있습니다. 밑줄은 인라인 명령 문제를 일으킬 수 있습니다. 명령에 적절한 따옴표를 사용합니다.
> 

다음 예제는 이것이 어떻게 작동하는지 설명합니다.

:::image type="content" source="./media/image-builder-devops-task/build-artifacts.png" alt-text="계층 구조를 보여주는 디렉터리 구조입니다.":::


* Windows - `C:\`에 파일이 있습니다. `webapp` 디렉터리를 포함하는 `buildArtifacts`라는 디렉터리가 생성됩니다.

* Linux - `/tmp`에 파일이 있습니다. 모든 파일과 디렉터리를 포함하는 `webapp` 디렉터리가 만들어집니다. 이 디렉터리에서 파일을 이동해야 합니다. 그렇지 않으면 파일이 임시 디렉터리에 있기 때문에 삭제됩니다.

#### <a name="inline-customization-script"></a>인라인 사용자 지정 스크립트

* Windows - 쉼표로 구분된 PowerShell 인라인 명령을 입력할 수 있습니다. 빌드 디렉터리에서 스크립트를 실행하려는 경우 다음을 사용할 수 있습니다.

    ```PowerShell
    & 'c:\buildArtifacts\webapp\webconfig.ps1'
    ```

   여러 스크립트를 참조하거나 더 많은 명령을 추가할 수 있습니다. 예를 들면 다음과 같습니다.

    ```PowerShell
    & 'c:\buildArtifacts\webapp\webconfig.ps1'
    & 'c:\buildArtifacts\webapp\installAgent.ps1'
    ```
* Linux - Linux 시스템에서는 빌드 아티팩트가 `/tmp` 디렉터리에 저장됩니다. 그러나 많은 Linux OS에서 다시 부팅할 때 /tmp 디렉터리 콘텐츠가 삭제됩니다. 이미지에 아티팩트가 존재하도록 하려면 다른 디렉터리를 만들고 아티팩트를 복사해야 합니다.  예를 들어 다음과 같은 가치를 제공해야 합니다.

    ```bash
    sudo mkdir /lib/buildArtifacts
    sudo cp -r "/tmp/_ImageBuilding/webapp" /lib/buildArtifacts/.
    ```
    
    "/tmp"디렉터리를 사용해도 괜찮다면 아래 코드를 사용하여 스크립트를 실행할 수 있습니다.
    
    ```bash
    # grant execute permissions to execute scripts
    sudo chmod +x "/tmp/_ImageBuilding/webapp/coreConfig.sh"
    echo "running script"
    sudo . "/tmp/AppsAndImageBuilderLinux/_WebApp/coreConfig.sh"
    ```
    
#### <a name="what-happens-to-the-build-artifacts-after-the-image-build"></a>이미지 빌드 후 빌드 아티팩트는 어떻게 되나요?

> [!NOTE]
> Image Builder는 빌드 아티팩트를 자동으로 제거하지 않습니다. 빌드 아티팩트를 제거하기 위한 코드가 항상 있어야 합니다.
> 

* Windows - Image Builder가 `c:\buildArtifacts` 디렉터리에 파일을 배포합니다. 디렉터리는 지속되므로 디렉터리를 직접 제거해야 합니다. 실행하는 스크립트에서 이를 제거할 수 있습니다. 예를 들어 다음과 같은 가치를 제공해야 합니다.

    ```PowerShell
    # Clean up buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts\*" -Force -Recurse
    
    # Delete the buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts" -Force 
    ```
    
* Linux - 빌드 아티팩트가 `/tmp` 디렉터리에 배치됩니다. 그러나 많은 Linux OS에서 다시 부팅할 때 `/tmp` 디렉터리 콘텐츠가 삭제됩니다. 컨텐츠를 제거하는 데 OS를 사용하는 대신 컨텐츠를 제거하는 코드가 있는 것이 좋습니다. 예를 들어 다음과 같은 가치를 제공해야 합니다.

    ```bash
    sudo rm -R "/tmp/AppsAndImageBuilderLinux"
    ```
    
#### <a name="total-length-of-image-build"></a>이미지 빌드의 총 길이

아직 DevOps 파이프라인 작업에서 총 길이를 변경할 수 없습니다. 기본값인 240분이 사용됩니다. [buildTimeoutInMinutes](./image-builder-json.md#properties-buildtimeoutinminutes)를 늘리려는 경우 릴리스 파이프라인에서 AZ CLI 작업을 사용할 수 있습니다. 템플릿을 복사하고 제출하도록 작업을 구성합니다. 예제는 이 [솔루션](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/4_Using_ENV_Variables#using-environment-variables-and-parameters-with-image-builder)을 참조하거나 Az PowerShell을 사용합니다.


#### <a name="storage-account"></a>스토리지 계정

사전 요구 사항에서 만든 스토리지 계정을 선택합니다. 목록에 표시되지 않는 경우 Image Builder에 이에 대한 권한이 없는 것입니다.

빌드가 시작되면 Image Builder가 `imagebuilder-vststask`라는 컨테이너를 만듭니다. 컨테이너는 리포지토리의 빌드 아티팩트가 저장되는 위치입니다.

> [!NOTE]
> 각 빌드 후 스토리지 계정 또는 컨테이너를 수동으로 삭제해야 합니다.
>

### <a name="distribute"></a>배포

3가지 배포 형식이 지원됩니다.

#### <a name="managed-image"></a>관리되는 이미지

* ResourceID:
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```

* 위치

#### <a name="azure-shared-image-gallery"></a>Azure Shared Image Gallery

Shared Image Gallery가 이미 **있어야** 합니다.

* ResourceID: 
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>
    ```

* 지역: 쉼표로 구분된 지역 목록입니다. 예: westus, eastus, centralus

#### <a name="vhd"></a>VHD

여기에 값을 전달할 수 없습니다. Image Builder는 *vhds* 컨테이너의 임시 Image Builder 리소스 그룹인 `IT_<DestinationResourceGroup>_<TemplateName>`에 VHD를 내보냅니다. 릴리스 빌드를 시작하면 Image Builder가 로그를 내보냅니다. 완료되면 VHD URL을 내보냅니다.

### <a name="optional-settings"></a>선택적 설정

* [VM 크기](image-builder-json.md#vmprofile) - 기본값 *Standard_D1_v2* 에서 VM 크기를 재정의할 수 있습니다. 전체 사용자 지정 시간을 줄이기 위해 재정의하거나 GPU/HPC 등의 특정 VM 크기에 따라 달라지는 이미지를 만들려고 할 수 있습니다.

## <a name="how-it-works"></a>작동 방식

릴리스를 만들면 스토리지 계정에 *imagebuilder-vststask* 라는 컨테이너가 만들어집니다. 빌드 아티팩트를 압축 및 업로드하고 zip 파일에 대한 SAS 토큰을 만듭니다.

작업은 작업에 전달된 속성을 사용하여 Image Builder 템플릿 아티팩트를 만듭니다. 작업은 다음을 수행합니다.
* 빌드 아티팩트 zip 파일과 기타 관련 스크립트를 다운로드합니다. 파일은 임시 Image Builder 리소스 그룹 `IT_<DestinationResourceGroup>_<TemplateName>`의 스토리지 계정에 저장됩니다.
* *t_* 접두사가 붙은 템플릿과 10자리 단조 정수를 만듭니다. 템플릿이 선택한 리소스 그룹에 저장됩니다. 리소스 그룹의 빌드 기간 동안 템플릿이 존재합니다. 

예제 출력:

```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
starting put template...
```

이미지 빌드가 시작되면 릴리스 로그에 실행 상태가 보고됩니다.

```text
starting run template...
```

이미지 빌드가 완료되면 다음 텍스트와 유사한 출력이 표시됩니다.

```text
2019-05-06T12:49:52.0558229Z starting run template...
2019-05-06T13:36:33.8863094Z run template:  Succeeded
2019-05-06T13:36:33.8867768Z getting runOutput for  SharedImage_distribute
2019-05-06T13:36:34.6652541Z ==============================================================================
2019-05-06T13:36:34.6652925Z ## task output variables ##
2019-05-06T13:36:34.6658728Z $(imageUri) =  /subscriptions/<subscriptionID>/resourceGroups/aibwinsig/providers/Microsoft.Compute/galleries/my22stSIG/images/winWAppimages/versions/0.23760.13763
2019-05-06T13:36:34.6659989Z ==============================================================================
2019-05-06T13:36:34.6663500Z deleting template t_1557146959485...
2019-05-06T13:36:34.6673713Z deleting storage blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip
2019-05-06T13:36:34.9786039Z blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip is deleted
2019-05-06T13:38:37.4884068Z delete template:  Succeeded
```

이미지 템플릿과 `IT_<DestinationResourceGroup>_<TemplateName>`이 삭제됩니다.

'$ (imageUri)' VSTS 변수를 가져와 다음 작업에서 사용하거나 값을 사용하고 VM을 빌드할 수 있습니다.

## <a name="output-devops-variables"></a>DevOps 변수 출력

원본 마켓플레이스 이미지의 게시자/제품/SKU/버전:
* $(pirPublisher)
* $(pirOffer)
* $(pirSku)
* $(pirVersion)

이미지 URI - 분산 이미지의 ResourceID:
* $(imageUri)

## <a name="faq"></a>FAQ

### <a name="can-i-use-an-existing-image-template-i-have-already-created-outside-of-devops"></a>이미 만든 기존 이미지 템플릿을 DevOps 외부에서 사용할 수 있나요?

현재는 사용할 수 없습니다.

### <a name="can-i-specify-the-image-template-name"></a>이미지 템플릿 이름을 지정할 수 있나요?

아니요. 고유 템플릿 이름이 사용되고 삭제됩니다.

### <a name="the-image-builder-failed-how-can-i-troubleshoot"></a>Image Builder가 실패했습니다. 어떻게 해결할 수 있나요?

빌드 오류가 발생하는 경우 DevOps 작업은 준비 리소스 그룹을 삭제하지 않습니다. 빌드 사용자 지정 로그가 포함된 준비 리소스 그룹에 액세스할 수 있습니다.

VM Image Builder 작업에 대한 DevOps 로그에 오류가 표시되고 customization.log 위치가 표시됩니다. 예를 들어 다음과 같은 가치를 제공해야 합니다.

:::image type="content" source="./media/image-builder-devops-task/devops-task-error.png" alt-text="실패를 표시하는 DevOps 작업 오류의 예":::

문제 해결에 대한 자세한 내용은 [Azure Image Builder Service 문제 해결](image-builder-troubleshoot.md)을 참조하세요. 

실패를 조사한 후에는 준비 리소스 그룹을 삭제할 수 있습니다. 먼저 이미지 템플릿 리소스 아티팩트를 삭제합니다. 아티팩트는 *t_* 접두사가 붙으며 DevOps 작업 빌드 로그에서 찾을 수 있습니다.

```text
...
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
...
template name:  t_1556938436xxx
...

```

이미지 템플릿 리소스 아티팩트는 작업에 처음 지정된 리소스 그룹에 있습니다. 문제 해결이 완료되면 아티팩트를 삭제합니다. Azure Portal 사용하여 삭제하는 경우 리소스 그룹 내에서 **숨겨진 형식 표시** 를 선택하여 아티팩트를 봅니다.


## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure Image Builder 개요](../image-builder-overview.md)를 참조하세요.

