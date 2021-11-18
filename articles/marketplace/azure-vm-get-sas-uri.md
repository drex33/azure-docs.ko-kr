---
title: VM 이미지에 대한 SAS URI 생성
description: Azure Marketplace에서 VHD(가상 하드 디스크)에 대한 SAS(공유 액세스 서명) URI를 생성합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: krsh
ms.date: 06/23/2021
ms.openlocfilehash: 36565dd38b74e8e03d44625f3dab765a26207c7c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132723041"
---
# <a name="generate-a-sas-uri-for-a-vm-image"></a>VM 이미지에 대한 SAS URI 생성

> [!NOTE]
> VM을 게시하는 데 SAS URI가 필요하지 않습니다. 파트너 센터 이미지를 공유할 수 있습니다. [승인된 기본을 사용하여 가상 머신 만들기](azure-vm-use-approved-base.md) 또는 [고유한 이미지를 사용하여 가상 머신 만들기](azure-vm-use-own-image.md) 지침을 참조하세요.

VHD에 대한 SAS URI를 생성할 때의 요구 사항은 다음과 같습니다.

- 목록 및 읽기 권한만 필요합니다. 쓰기 또는 삭제 권한을 제공하지 마세요.
- 액세스 기간(만료 날짜)은 SAS URI가 생성된 시점에서 3주 이상이어야 합니다.
- UTC 시간 변경을 방지하려면 시작 날짜를 현재 날짜 전으로 설정합니다. 예를 들어, 현재 날짜가 2020년 6월 16일인 경우 6/15/2020을 선택합니다.

## <a name="extract-vhd-from-a-vm"></a>VM에서 VHD 추출

> [!NOTE]
> 스토리지 계정에 업로드된 VHD가 이미 있는 경우 이 단계를 건너뛸 수 있습니다.

VM에서 VHD를 추출하려면 VM 디스크의 스냅샷을 만들고 스냅샷에서 VHD를 추출해야 합니다.

다음과 같이 VM 디스크의 스냅샷을 작성하여 시작합니다.

1. Azure Portal에 로그인합니다.
2. 왼쪽 상단에서 시작하여 리소스 만들기를 선택하고 스냅샷을 검색하여 선택합니다.
3. 스냅샷 블레이드에서 만들기를 선택합니다.
4. 스냅샷의 이름을 입력합니다.
5. 기존 리소스 그룹을 선택하거나 새 리소스 그룹 이름을 입력합니다.
6. 원본 디스크에서 스냅샷을 만들 관리 디스크를 선택합니다.
7. 스냅샷 저장에 사용할 계정 유형을 선택합니다. 고성능 SSD에 저장할 필요가 없다면 표준 HDD를 사용합니다.
8. 만들기를 선택합니다.

### <a name="extract-the-vhd"></a>VHD 추출

다음 스크립트를 사용하여 스냅샷을 스토리지 계정의 VHD로 내보냅니다.

```azurecli
#Provide the subscription Id where the snapshot is created
$subscriptionId=yourSubscriptionId

#Provide the name of your resource group where the snapshot is created
$resourceGroupName=myResourceGroupName

#Provide the snapshot name
$snapshotName=mySnapshot

#Provide Shared Access Signature (SAS) expiry duration in seconds (such as 3600)
#Know more about SAS here: https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1
$sasExpiryDuration=3600

#Provide storage account name where you want to copy the underlying VHD file. Currently, only general purpose v1 storage is supported.
$storageAccountName=mystorageaccountname

#Name of the storage container where the downloaded VHD will be stored.
$storageContainerName=mystoragecontainername

#Provide the key of the storage account where you want to copy the VHD 
$storageAccountKey=mystorageaccountkey

#Give a name to the destination VHD file to which the VHD will be copied.
$destinationVHDFileName=myvhdfilename.vhd

az account set --subscription $subscriptionId

$sas=$(az snapshot grant-access --resource-group $resourceGroupName --name $snapshotName --duration-in-seconds $sasExpiryDuration --query [accessSas] -o tsv)

az storage blob copy start --destination-blob $destinationVHDFileName --destination-container $storageContainerName --account-name $storageAccountName --account-key $storageAccountKey --source-uri $sas
```

### <a name="script-explanation"></a>스크립트 설명
이 스크립트에서는 다음 명령을 사용하여 스냅샷의 SAS URI를 생성하고 SAS URI를 사용하여 스토리지 계정에 기본 VHD를 복사합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.


|명령  |메모  |
|---------|---------|
| az disk grant-access    |     기본 VHD 파일을 스토리지 계정으로 복사하거나 온-프레미스로 다운로드하는 데 사용되는 읽기 전용 SAS를 생성합니다.    |
|  az storage blob copy start   |    한 스토리지 계정에서 다른 스토리지 계정으로 Blob을 비동기적으로 복사합니다. az storage blob show를 사용하여 새 Blob의 상태를 확인합니다.     |
|

## <a name="generate-the-sas-address"></a>SAS 주소 생성

SAS 주소(URL)를 만드는 데 사용되는 일반적인 두 가지 도구는 다음과 같습니다.

1. **Azure Storage Explorer** – Azure Portal에서 사용할 수 있습니다.
2. **Azure CLI** – Windows 외의 운영 체제 및 자동 또는 연속 통합 환경에 권장됩니다.

### <a name="using-tool-1-azure-storage-explorer"></a>도구 1 사용: Azure Storage Explorer

1. **스토리지 계정** 으로 이동합니다.
1. **Storage Explorer** 를 엽니다.

    :::image type="content" source="media/create-vm/storge-account-explorer.png" alt-text="스토리지 계정 창.":::

3. **컨테이너** 에서 VHD 파일을 마우스 오른쪽 단추로 클릭하고 **공유 액세스 서명 가져오기** 를 선택합니다.
4. **공유 액세스 서명** 대화 상자에서 다음 필드를 작성합니다.

    1. 시작 시간 – VHD 액세스 권한의 시작 날짜입니다. 현재 날짜보다 하루 전의 날짜를 제공합니다.
    2. 만료 시간 – VHD 액세스 권한의 만료 날짜입니다. 현재 날짜보다 최소 3주 후의 날짜를 제공합니다.
    3. 권한 – 읽기 및 목록 사용 권한을 선택합니다.
    4. 컨테이너 수준 – 컨테이너 수준 공유 액세스 서명 URI 생성 확인란을 선택합니다.

    ![공유 액세스 서명 대화 상자.](media/vm/create-sas-uri-storage-explorer.png)

5. 이 VHD에 연결되는 SAS URI를 만들려면 **만들기** 를 선택합니다.
6. URI를 복사하고, 안전한 위치에 텍스트 파일로 저장합니다. 이 생성된 SAS URI는 컨테이너 수준 액세스를 위한 것입니다. 구체화하려면 텍스트 파일을 편집하여 VHD 이름을 추가합니다.
7. SAS URI의 VHD 문자열 뒤에 VHD 이름을 삽입합니다(앞의 슬래시 포함). 최종 SAS URI는 다음과 같습니다.

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

8. 게시할 계획의 각 VHD에 대해 이 단계를 반복합니다.

### <a name="using-tool-2-azure-cli"></a>도구 2 사용: Azure CLI

1. [Microsoft Azure CLI](/cli/azure/install-azure-cli)를 다운로드하여 설치합니다. 버전은 Windows, macOS 및 다양한 Linux 배포판에서 사용할 수 있습니다.
2. PowerShell 파일(.ps1 파일 확장명)을 만들고, 다음 코드를 복사한 다음, 로컬로 저장합니다.

    ```azurecli-interactive
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <container-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. 파일을 편집하여 다음 매개 변수 값을 사용합니다. UTC 날짜/시간 형식(예: 2020-04-01T00:00:00Z)으로 날짜를 입력합니다.

    - account-name – Azure 스토리지 계정 이름.
    - account-key – Azure 스토리지 계정 키.
    - start-date – VHD 액세스 권한 시작 날짜. 현재 날짜보다 하루 전의 날짜를 제공합니다.
    - expiry-date – VHD 액세스 권한 만료 날짜. 현재 날짜 이후 최소 3주 후의 날짜를 제공합니다.

    다음은 적절한 매개 변수 값의 예시(이 문서의 작성 시점 기준)입니다.

    ```azurecli-interactive
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ON c+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name <container-name> -- permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

1. 변경 내용을 저장합니다.
2. 다음 방법 중 하나를 사용하여 이 스크립트를 관리자 권한으로 실행해 컨테이너 수준 액세스를 위한 SAS 연결 문자열을 만듭니다.

    - 콘솔에서 스크립트를 실행합니다. Windows에서 스크립트를 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행** 을 선택합니다.
    - [Windows PowerShell ISE](/powershell/scripting/components/ise/introducing-the-windows-powershell-ise)와 같은 PowerShell 스크립트 편집기에서 스크립트를 실행합니다. 이 화면은 이 편집기 내에서 SAS 연결 문자열을 만드는 것을 보여 줍니다.

    [![PowerShell 편집기 내에서 SAS 연결 문자열 만들기](media/vm/create-sas-uri-power-shell-ise.png)](media/vm/create-sas-uri-power-shell-ise.png#lightbox)

6. SAS 연결 문자열을 복사하고, 안전한 위치에 텍스트 파일로 저장합니다. 이 문자열을 편집하여 최종 SAS URI를 만들기 위한 VHD 위치 정보를 추가합니다.
7. Azure Portal에서 새 URI와 연결된 VHD를 포함하는 BLOB 스토리지로 이동합니다.
8. Blob 서비스 엔드포인트의 URL 복사:

    ![Blob 서비스 엔드포인트의 URL 복사하기.](media/vm/create-sas-uri-blob-endpoint.png)

9. 6단계의 SAS 연결 문자열을 사용하여 텍스트 파일을 편집합니다. 다음 형식을 사용하여 전체 SAS URI를 만듭니다.

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

## <a name="verify-the-sas-uri"></a>SAS URI 확인

파트너 센터에서 게시하기 전에 SAS URI를 확인하여 요청에 대한 SAS URI 전송 후 관련된 문제를 방지합니다. 이 프로세스는 선택 사항이지만 수행하는 것이 좋습니다.

- URI에는 `.vhd` 파일 이름 확장명이 있는 VHD 이미지 파일 이름이 포함됩니다.
- `Sp=rl`이 URI의 중간에 표시됩니다. 이 문자열은 지정된 읽기 및 목록 액세스 권한을 보여줍니다.
- `sr=c`이 표시되면 컨테이너 수준 액세스가 지정된 것입니다.
- Blob을 테스트 다운로드하려면 URI를 브라우저에 복사하여 붙여넣습니다(다운로드를 완료하기 전에 작업을 취소할 수 있음).

## <a name="next-steps"></a>다음 단계

- 문제가 발생하는 경우 [VM SAS 오류 메시지](azure-vm-sas-failure-messages.md)를 참조하세요.
- [파트너 센터에 로그인](https://go.microsoft.com/fwlink/?linkid=2165935)
- [Azure Marketplace에서 가상 머신 제품 만들기](azure-vm-offer-setup.md)