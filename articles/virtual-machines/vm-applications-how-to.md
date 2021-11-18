---
title: VM 응용 프로그램 패키지 만들기 및 배포 (미리 보기)
description: Azure Compute 갤러리를 사용 하 여 VM 응용 프로그램을 만들고 배포 하는 방법을 알아봅니다.
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/02/2021
ms.reviewer: amjads
ms.custom: ''
ms.openlocfilehash: b98d8da852ed280638af33c6e9c52e1ea6963fbb
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709029"
---
# <a name="create-and-deploy-vm-applications-preview"></a>VM 응용 프로그램 만들기 및 배포 (미리 보기)

VM 응용 프로그램은 가상 컴퓨터용 응용 프로그램의 관리, 공유 및 글로벌 배포를 간소화 하는 Azure Compute 갤러리 (이전의 공유 이미지 갤러리)의 리소스 유형입니다.


> [!IMPORTANT]
> **Azure 계산 갤러리의 VM 응용 프로그램** 은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


## <a name="prerequisites"></a>사전 요구 사항

시작 하기 전에 다음이 있는지 확인 합니다.


이 문서에서는 Azure Compute 갤러리가 이미 있다고 가정 합니다. 갤러리가 아직 없는 경우 먼저 만듭니다. 자세히 알아보려면 [리소스를 저장 및 공유 하기 위한 갤러리 만들기](create-gallery.md)를 참조 하세요.

응용 프로그램을 Azure 저장소 계정의 컨테이너에 업로드 해야 합니다. 응용 프로그램은 블록 또는 페이지 blob에 저장할 수 있습니다. 페이지 blob을 사용 하도록 선택 하는 경우 파일을 업로드 하기 전에 파일에 바이트 맞춤을 수행 해야 합니다. 다음은 파일을 바이트 정렬 하는 샘플입니다.

```azurepowershell-interactive
$inputFile = <the file you want to pad>

$fileInfo = Get-Item -Path $inputFile

$remainder = $fileInfo.Length % 512

if ($remainder -ne 0){

    $difference = 512 - $remainder

    $bytesToPad = [System.Byte[]]::CreateInstance([System.Byte], $difference)

    Add-Content -Path $inputFile -Value $bytesToPad -Encoding Byte
    }
```

파일이 공개적으로 사용 가능한 지 확인 해야 합니다. 그렇지 않으면 저장소 계정의 파일에 대 한 SAS URI가 필요 합니다. 아직 없는 경우 [Storage Explorer](../vs-azure-tools-storage-explorer-blobs.md) 를 사용 하 여 SAS URI를 빠르게 만들 수 있습니다.

## <a name="create-the-vm-application"></a>VM 응용 프로그램 만들기

VM 응용 프로그램 정의 및 버전을 만들려면 아래 옵션을 선택 합니다.

### <a name="portal"></a>[포털](#tab/portal)


1. [Azure Portal](https://portal.azure.com)로 이동 하 여 **Azure Compute 갤러리** 를 검색 하 고 선택 합니다.
1. 목록에서 사용 하려는 갤러리를 선택 합니다.
1. 갤러리의 페이지에서 페이지 맨 위에 있는 **추가** 를 선택 하 고 드롭다운 메뉴에서 **VM 이미지 정의** 를 선택 합니다. **VM 응용 프로그램 정의 만들기** 페이지가 열립니다.
1. **기본 사항** 탭에서 응용 프로그램에 대 한 이름을 입력 하 고 응용 프로그램이 Linux를 실행 하는 vm 용 인지 또는 Windows 인지를 선택 합니다.
1. VM 응용 프로그램 정의에 대해 다음과 같은 선택적 설정을 지정 하려면 **게시 옵션** 탭을 선택 합니다.
    - VM 응용 프로그램 정의에 대 한 설명입니다.
    - 수명 주기 끝
    - Eula에 연결
    - 개인 정보 취급 방침의 URI
    - 릴리스 정보에 대 한 URI
1. 완료되면 **검토 + 만들기** 를 선택합니다.
1. 유효성 검사가 완료 되 면 **만들기** 를 선택 하 여 정의를 배포 합니다.
1. 배포가 완료되면 **리소스로 이동** 을 선택합니다.


이제 VM을 만들고 포털을 사용 하 여 vm 응용 프로그램을 배포할 수 있습니다. 일반적인 방법으로 VM을 만들고 **고급** 탭에서 **설치할 Vm 응용 프로그램 선택** 을 선택 합니다.

:::image type="content" source="media/vmapps/advanced-tab.png" alt-text="VM 응용 프로그램을 설치 하도록 선택할 수 있는 고급 탭의 스크린샷":::

목록에서 VM 응용 프로그램을 선택 하 고 페이지 아래쪽에서 **저장** 을 선택 합니다.

:::image type="content" source="media/vmapps/select-app.png" alt-text="VM에 설치할 VM 응용 프로그램 선택을 보여 주는 스크린샷":::

둘 이상의 VM 응용 프로그램을 설치할 경우 **고급 탭** 에서 각 vm 응용 프로그램의 설치 순서를 다시 설정할 수 있습니다.

### <a name="cli"></a>[CLI](#tab/cli)

VM 응용 프로그램에 [Azure CLI](/cli/azure/install-azure-cli) 버전 2.30.0 이상이 필요 합니다.

[Az sig gallery-application create](/cli/azure/sig/gallery-application#az_sig_gallery_application_create)를 사용 하 여 VM 응용 프로그램 정의를 만듭니다. 이 예제에서는 Linux 기반 Vm에 대해 *myApp* 라는 vm 응용 프로그램 정의를 만듭니다.

```azurecli-interactive
az sig gallery-application create \
    --application-name myApp \
    --gallery-name myGallery \
    --resource-group myResourceGroup \
    --os-type Linux \
    --location "East US"
```

[Az sig gallery-application version create](/cli/azure/sig/gallery-application/version#az_sig_gallery_application_version_create)를 사용 하 여 VM 응용 프로그램 버전을 만듭니다. 버전에 허용 되는 문자는 숫자와 마침표입니다. 숫자는 32비트 정수 범위 내에 포함되어야 합니다. 형식: *MajorVersion*.*MinorVersion*.*Patch*.

매개 변수 값을 사용자 고유의 값으로 바꿉니다.

```azurecli-interactive
az sig gallery-application version create \
   --version-name 1.0.0 \
   --application-name myApp \
   --gallery-name myGallery \
   --location "East US" \
   --resource-group myResourceGroup \
   --package-file-link "https://<storage account name>.blob.core.windows.net/<container name>/<filename>" \
   --install-command "mv myApp .\myApp\myApp" \
   --remove-command "rm .\myApp\myApp" \
   --update-command  "mv myApp .\myApp\myApp \
   --default-configuration-file-link "https://<storage account name>.blob.core.windows.net/<container name>/<filename>"\
```


### <a name="powershell"></a>[PowerShell](#tab/powershell)

을 사용 하 여 VM 응용 프로그램 정의를 만듭니다 `New-AzGalleryApplication` . 이 예제 *에서는 mygallery Azure Compute* 갤러리에서 *mygallery* 리소스 그룹에 *myApp* 라는 Linux 앱을 만들고, 자체 사용을 위한 VM 응용 프로그램에 대 한 간단한 설명을 제공 했습니다. 필요에 따라 값을 바꿉니다.

```azurepowershell-interactive
$galleryName = myGallery
$rgName = myResourceGroup
$applicationName = myApp
New-AzGalleryApplication `
  -ResourceGroupName $rgName `
  -GalleryName $galleryName `
  -Name $applicationName `
  -SupportedOSType Linux `
  -Description "Backend Linux application for finance."
```

을 사용 하 여 응용 프로그램의 버전을 만듭니다 `New-AzGalleryApplicationVersion` . 버전에 허용 되는 문자는 숫자와 마침표입니다. 숫자는 32비트 정수 범위 내에 포함되어야 합니다. 형식: *MajorVersion*.*MinorVersion*.*Patch*.

이 예제에서는 버전 번호 *1.0.0* 을 만듭니다. 필요에 따라 변수 값을 바꿉니다.

```azurepowershell-interactive
$version = 1.0.0
New-AzGalleryApplicationVersion `
   -ResourceGroupName $rgName `
   -GalleryName $galleryName `
   -GalleryApplicationName $applicationName `
   -Name $version `
   -PackageFileLink "https://<storage account name>.blob.core.windows.net/<containder name>/<filename>" `
   -Location "East US" `
   -Install myApp.exe /silent `
   -Remove myApp.exe /uninstall `
```


응용 프로그램을 기존 VM에 추가 하려면 응용 프로그램 버전을 가져온 다음이를 사용 하 여 VM 응용 프로그램 버전 ID를 가져옵니다. ID를 사용 하 여 VM 구성에 응용 프로그램을 추가 합니다.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName $rgname -Name myVM
$vmapp = Get-AzGalleryApplicationVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryname `
   -ApplicationName $applicationname `
   -Version $version

$vm = Add-AzVmGalleryApplication `
   -VM $vm `
   -Id $vmapp.Id

Update-AzVm -ResourceGroupName $rgname -VM $vm
```


### <a name="rest"></a>[REST (영문)](#tab/rest2)

응용 프로그램 정의를 만듭니다.


```rest
PUT
/subscriptions/\<**subscriptionId**\>/resourceGroups/\<**resourceGroupName**\>/providers/Microsoft.Compute/galleries/\<**galleryName**\>/applications/\<**applicationName**\>?api-version=2019-03-01

{
    "location": "West US",
    "name": "myApp",
    "properties": {
        "supportedOSType": "Windows | Linux",
        "endOfLifeDate": "2020-01-01"
    }
}

```

| 필드 이름 | Description | 제한 사항 |
|--|--|--|
| name | 갤러리 내에서 VM 응용 프로그램의 고유 이름 | 최대 길이는 117 자입니다. 허용 되는 문자는 대/소문자, 숫자, 하이픈 (-), 마침표 (.), 밑줄 (_)입니다. 이름은 마침표 (.)로 끝날 수 없습니다. |
| supportedOSType | Windows 또는 Linux 응용 프로그램 인지 여부 | "Windows" 또는 "Linux" |
| endOfLifeDate | 응용 프로그램의 향후 수명 종료 날짜입니다. 참조용 으로만 사용할 수 있으며이는 적용 되지 않습니다. | 유효한 미래 날짜 |

VM 응용 프로그램 버전을 만듭니다.

```rest
PUT
/subscriptions/\<**subscriptionId**\>/resourceGroups/\<**resourceGroupName**\>/providers/Microsoft.Compute/galleries/\<**galleryName**\>/applications/\<**applicationName**\>/versions/\<**versionName**\>?api-version=2019-03-01

{
  "location": "$location",
  "properties": {
    "publishingProfile": {
      "source": {
        "mediaLink": "$mediaLink",
        "defaultConfigurationLink": "$configLink"
      },
      "manageActions": {
        "install": "echo installed",
        "remove": "echo removed",
        "update": "echo update"
      },
      "targetRegions": [
        {
          "name": "$location1",
          "regionalReplicaCount": 1 
        },
        { "name": "$location1" }
      ]
    },
    "endofLifeDate": "datetime",
    "excludeFromLatest": "true | false"
  }
}

```

| 필드 이름 | Description | 제한 사항 |
|--|--|--|
| 위치 | VM 응용 프로그램 버전의 원본 위치 | 유효한 Azure 지역 |
| mediaLink | 응용 프로그램 버전 패키지를 포함 하는 url입니다. | 유효한 및 기존 저장소 url |
| defaultConfigurationLink | 선택 사항입니다. 배포 시 재정의 될 수 있는 기본 구성이 포함 된 url입니다. | 유효한 및 기존 저장소 url |
| 설치 | 응용 프로그램을 설치 하는 명령 | 지정 된 OS에 대 한 유효한 명령 |
| 제거 | 응용 프로그램을 제거 하는 명령 | 지정 된 OS에 대 한 유효한 명령 |
| 업데이트 | 선택 사항입니다. 응용 프로그램을 업데이트 하는 명령입니다. 지정 하지 않은 경우 업데이트가 필요 하면 이전 버전이 제거 되 고 새 버전이 설치 됩니다. | 지정 된 OS에 대 한 유효한 명령 |
| targetRegions/이름 | 복제할 영역의 이름입니다. | Azure 지역 유효성 검사 |
| targetRegions/regionalReplicaCount | 선택 사항입니다. 만들 영역의 복제본 수입니다. 기본값은 1입니다. | 1에서 3 까지의 정수입니다. |
| endOfLifeDate | 응용 프로그램 버전에 대 한 향후 수명의 종료 날짜입니다. 이는 고객 참조용 이며 적용 되지 않습니다. | 유효한 미래 날짜 |
| excludeFromLatest | 이 버전을 지정 하면 최신 버전으로 간주 되지 않습니다. | True 또는 False |




Vm에 vm 응용 프로그램 버전을 추가 하려면 vm에 대해 PUT을 수행 합니다.

```rest
PUT
/subscriptions/\<**subscriptionId**\>/resourceGroups/\<**resourceGroupName**\>/providers/Microsoft.Compute/virtualMachines/\<**VMName**\>?api-version=2019-03-01

{
  "properties": {
    "applicationProfile": {
      "galleryApplications": [
        {
          "order": 1,
          "packageReferenceId": "/subscriptions/{subscriptionId}/resourceGroups/<resource group>/providers/Microsoft.Compute/galleries/{gallery name}/applications/{application name}/versions/{version}",
          "configurationReference": "{path to configuration storage blob}"
        }
      ]
    }
  },
  "name": "{vm name}",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resource group}/providers/Microsoft.Compute/virtualMachines/{vm name}",
  "location": "{vm location}"
}
```


단일 확장 집합에 VM 응용 프로그램을 적용 하려면 다음을 수행 합니다.

```rest
PUT
/subscriptions/\<**subscriptionId**\>/resourceGroups/\<**resourceGroupName**\>/providers/Microsoft.Compute/
virtualMachineScaleSets/\<**VMSSName**\>?api-version=2019-03-01

{
  "properties": {
    "virtualMachineProfile": {
      "applicationProfile": {
        "galleryApplications": [
          {
            "order": 1,
            "packageReferenceId": "/subscriptions/{subscriptionId}/resourceGroups/<resource group>/providers/Microsoft.Compute/galleries/{gallery name}/applications/{application name}/versions/{version}",
            "configurationReference": "{path to configuration storage blob}"
          }
        ]
      }
    }
  },
  "name": "{vm name}",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resource group}/providers/Microsoft.Compute/virtualMachines/{vm name}",
  "location": "{vm location}"
}
```


| 필드 이름 | Description | 제한 사항 |
|--|--|--|
| 순서 | 선택 사항입니다. 응용 프로그램을 배포 해야 하는 순서입니다. 아래 내용을 참조하세요. | 정수 유효성 검사 |
| packageReferenceId | 갤러리 응용 프로그램 버전을 참조 합니다. | 유효한 응용 프로그램 버전 참조 |
| configurationReference | 선택 사항입니다. 이 배포에 대 한 구성을 포함 하는 저장소 blob의 전체 url입니다. 이렇게 하면 이전에 defaultConfiguration에 제공 된 모든 값이 재정의 됩니다. | 올바른 저장소 blob 참조 |

순서 필드를 사용 하 여 응용 프로그램 간의 종속성을 지정할 수 있습니다. 주문에 대 한 규칙은 다음과 같습니다.

| 사례 | 설치 의미 | 오류 의미 |
|--|--|--|
| 순서를 지정 하지 않음 | 순서가 지정 되지 않은 응용 프로그램은 순서가 지정 된 응용 프로그램에 설치 됩니다. 순서가 지정 되지 않은 응용 프로그램에 대 한 설치 순서는 보장 되지 않습니다. | 다른 응용 프로그램의 설치 오류는 순서가 지정 되거나 순서가 지정 되지 않은 응용 프로그램의 설치에 영향을 주지 않습니다. |
| 중복 된 순서 값 | 응용 프로그램은 순서에 관계 없이 다른 응용 프로그램에 비해 순서에 관계 없이 설치 됩니다. 순서가 낮은 모든 응용 프로그램은 주문이 낮은 모든 응용 프로그램 뒤에 설치 됩니다. | 낮은 주문의 이전 응용 프로그램을 설치 하지 못한 경우에는이 순서로 응용 프로그램을 설치할 수 없습니다. 이 주문의 응용 프로그램을 설치 하지 못하는 경우에는 더 높은 순서로 응용 프로그램을 설치 하지 않습니다. |
| 주문 높이기 | 응용 프로그램은 더 낮은 주문이 있는 응용 프로그램을 설치 하 고 주문이 더 높은 이전에 설치 됩니다. | 낮은 주문의 이전 응용 프로그램을 설치 하지 못한 경우이 응용 프로그램은 설치 되지 않습니다. 이 응용 프로그램을 설치 하지 못하는 경우에는 더 높은 순서로 응용 프로그램을 설치 하지 않습니다. |

응답에는 전체 VM 모델이 포함 됩니다. 다음은 관련 파트입니다.

```rest
{
  "name": "{vm name}",
  "id": "{vm id}",
  "type": "Microsoft.Compute/virtualMachines",
  "location": "{vm location}",
  "properties": {
    "applicationProfile": {
      "galleryApplications": ""
    },
    "provisioningState": "Updating"
  },
  "resources": [
    {
      "name": "VMAppExtension",
      "id": "{extension id}",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "centraluseuap",
      "properties": "@{autoUpgradeMinorVersion=True; forceUpdateTag=7c4223fc-f4ea-4179-ada8-c8a85a1399f5; provisioningState=Creating; publisher=Microsoft.CPlat.Core; type=VMApplicationManagerLinux; typeHandlerVersion=1.0; settings=}"
    }
  ]
}

```

Vm에 vm 응용 프로그램이 아직 설치 되지 않은 경우이 값은 비어 있습니다. 

---



## <a name="next-steps"></a>다음 단계
[VM 응용 프로그램](vm-applications.md)에 대해 자세히 알아보세요.