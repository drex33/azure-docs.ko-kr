---
title: 개인 데이터를 삭제하고 내보내는 방법
description: GDPR(일반 데이터 보호 규정)에 따라 사용자의 의무를 지원하기 위해 Azure DevLast Labs 서비스에서 개인 데이터 삭제 및 내보내는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 6b9d96e21d01e5e2c91768e7162fcb0621c1e006
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600611"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Azure DevTest Labs에서 개인 데이터 내보내기 또는 삭제
이 문서에서는 Azure DevTest Labs 서비스에서 개인 데이터를 삭제하고 내보내는 단계를 제공합니다. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>DevTest Labs에서는 어떤 개인 데이터를 수집하나요?
DevTest Labs에서는 사용자로부터 두 가지 주요 개인 데이터를 수집합니다. 이는 사용자 이메일 주소와 사용자 개체 ID입니다. 이 정보는 서비스가 랩 관리자 및 랩 사용자에게 서비스 중인 기능을 제공하는 데 중요합니다.

### <a name="user-email-address"></a>사용자 이메일 주소
DevTest Labs에서는 사용자 이메일 주소를 사용하여 랩 사용자에게 자동 종료 이메일 알림을 보냅니다. 이메일은 사용자에게 머신이 종료되었음을 알립니다. 원하는 경우 사용자는 종료를 연기하거나 건너뛸 수 있습니다. 랩 수준 또는 VM 수준에서 이메일 주소를 구성합니다.

**랩에서 이메일 설정:**

![랩 수준에서 이메일 설정](./media/personal-data-delete-export/lab-user-email.png)

**VM에서 이메일 설정:**

![VM 수준에서 이메일 설정](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>사용자 개체 ID
DevTest Labs는 사용자 개체 ID를 사용하여 랩 관리자에게 월별 비용 추세와 리소스별 비용을 보여줍니다. 이를 통해 비용을 추적하고 랩에 대한 임계값을 관리할 수 있습니다. 

**당월의 예상 비용 추세:** 
![당월의 예상 비용 추세](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**리소스별 예상 월간 누계 비용:** 
![리소스별 예상 월간 누계 비용](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>이 개인 데이터가 필요한 이유는?
DevTest Labs 서비스는 작업 목적을 위해 개인 데이터를 사용합니다. 이 데이터는 서비스에서 주요 기능을 제공하는 데 중요합니다. 사용자 이메일 주소에 보존 정책을 설정하면 랩 사용자는 이메일 주소를 삭제한 후에는 적시에 자동 종료 이메일 알림을 받지 못합니다. 마찬가지로, 보존 정책에 따라 사용자 개체 ID가 삭제되는 경우 랩 관리자는 랩에 있는 머신의 월별 비용 추세 및 리소스별 비용을 볼 수 없습니다. 따라서 이 데이터는 사용자의 리소스가 랩에서 활성 상태인 동안 유지되어야 합니다.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>어떻게 하면 시스템에서 내 개인 데이터를 잊어버릴 수 있나요?
랩 사용자로서 이 개인 데이터를 삭제하려는 경우 랩에서 해당 리소스를 삭제하면 됩니다. DevTest Labs 서비스는 삭제된 개인 데이터를 사용자가 삭제한 후 30일 동안 익명화합니다.

예를 들어 VM을 삭제하거나 이메일 주소를 제거하는 경우 DevTest Labs 서비스는 리소스가 삭제된 후 30일 동안 이 데이터를 익명화합니다. 삭제 후 30일 보존 정책은 랩 관리자에게 월별 정확한 비용 예측을 제공하는지 확인하는 것입니다.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>내 개인 데이터에 대한 내보내기는 어떻게 요청할 수 있나요?
Azure Portal 또는 PowerShell을 사용하여 개인 및 랩 사용량 데이터를 내보낼 수 있습니다. 데이터는 두 개의 다른 CSV 파일로 내보내 집니다:

- **disks.csv** - 다른 VM에서 사용중인 디스크 정보를 포함합니다
- **virtualmachines.csv** - 랩의 VM 정보 포함합니다.

### <a name="azure-portal"></a>Azure portal
랩 사용자인 경우 DevTest Labs 서비스가 저장하는 개인 데이터에 대한 내보내기를 요청할 수 있습니다. 내보내기를 요청하려면 랩의 **개요** 페이지에 있는 **개인 데이터** 옵션으로 이동합니다. **내보내기 요청** 단추를 선택하면 랩 관리자의 스토리지 계정에서 다운로드 가능한 Excel 파일 생성이 시작됩니다. 그런 다음, 랩 관리자에게 문의하여 이 데이터를 볼 수 있습니다.

1. 왼쪽 메뉴에서 **개인 데이터** 를 선택합니다. 

    ![개인 데이터 페이지](./media/personal-data-delete-export/personal-data-page.png)
2. 랩을 포함하는 **리소스 그룹** 을 선택합니다.

    ![리소스 그룹 선택](./media/personal-data-delete-export/select-resource-group.png)
3. 리소스 그룹에서 **스토리지 계정** 을 선택합니다.
4. **스토리지 계정** 페이지에서 **Blob** 을 선택합니다.

    ![Blob 타일 선택](./media/personal-data-delete-export/select-blobs-tile.png)
5. 컨테이너를 목록에서 **labresourceusage** 라는 컨테이너를 선택합니다.

    ![Blob 컨테이너 선택](./media/personal-data-delete-export/select-blob-container.png)
6. 랩 이름을 딴 **폴더** 를 선택합니다. 이 폴더의 랩에서 **디스크** 및 **가상 머신** 에 대한 **csv** 파일을 찾습니다. 이러한 csv 파일을 다운로드하고, 액세스를 요청하는 랩 사용자의 콘텐츠를 필터링하고, 공유할 수 있습니다.

    ![CSV 파일 다운로드](./media/personal-data-delete-export/download-csv-file.png)

### <a name="azure-powershell&quot;></a>Azure PowerShell

```powershell
Param (
    [Parameter (Mandatory=$true, HelpMessage=&quot;The storage account name where to store usage data")]
    [string] $storageAccountName,

    [Parameter (Mandatory=$true, HelpMessage="The storage account key")]
    [string] $storageKey,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab name to get usage data from")]
    [string] $labName,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab subscription")]
    [string] $labSubscription
    )

#Login
Login-AzureRmAccount

# Set the subscription for the lab
Get-AzureRmSubscription -SubscriptionId $labSubscription  | Select-AzureRmSubscription

# DTL will create this container in the storage when invoking the action, cannot be changed currently
$containerName = "labresourceusage"

# Get the storage context
$Ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageKey 
$SasToken = New-AzureStorageAccountSASToken -Service Blob, File -ResourceType Container, Service, Object -Permission rwdlacup -Protocol HttpsOnly -Context $Ctx

# Generate the storage blob uri
$blobUri = $Ctx.BlobEndPoint + $SasToken

# blobStorageAbsoluteSasUri and usageStartDate are required
$actionParameters = @{
    'blobStorageAbsoluteSasUri' = $blobUri    
}

$startdate = (Get-Date).AddDays(-7)

$actionParameters.Add('usageStartDate', $startdate.Date.ToString())

# Get the lab resource group
$resourceGroupName = (Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $labName}).ResourceGroupName
    
# Create the lab resource id
$resourceId = "/subscriptions/" + $labSubscription + "/resourceGroups/" + $resourceGroupName + "/providers/Microsoft.DevTestLab/labs/" + $labName + "/"

# !!!!!!! this is the new resource action to get the usage data.
$result = Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
 
# Finish up cleanly
if ($result.Status -eq "Succeeded") {
    Write-Output "Telemetry successfully downloaded for " $labName
    return 0
}
else
{
    Write-Output "Failed to download lab: " + $labName
    Write-Error $result.toString()
    return -1
}
```

위 예제의 주요 구성 요소는 다음과 같습니다:

- Invoke-AzureRmResourceAction 명령입니다.
   
    ```
    Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
    ```
- 두 개의 작업 매개 변수
    - **blobStorageAbsoluteSasUri** - SAS(공유 액세스 서명) 토큰이 포함된 스토리지 계정 URI입니다. PowerShell 스크립트에서 이 값은 스토리지 키 대신 전달할 수 있습니다.
    - **usageStartDate** - 데이터를 끌어올 시작 날짜이며 종료 날짜는 작업이 실행된 현재 날짜입니다. 세분성은 일 수준이므로 시간 정보를 추가하더라도 무시됩니다.

### <a name="exported-data---a-closer-look"></a>내보낸 데이터 - 자세히 보기
이제 내보낸 데이터를 좀 더 자세히 살펴보겠습니다. 앞서 설명한 것처럼 데이터를 성공적으로 내보내면 두 개의 CSV 파일이 생성됩니다. 

**virtualmachines.csv** 에는 다음의 데이터 열이 포함되어 있습니다:

| 열 이름 | Description |
| ----------- | ----------- | 
| SubscriptionId | 랩이 있는 구독 식별자입니다. |
| LabUId | 랩의 고유 GUID 식별자입니다. |
| LabName | 랩의 이름 |
| LabResourceId | 정규화된 랩 리소스 ID입니다. |
| ResourceGroupName | VM을 포함하는 리소스 그룹 이름 | 
| ResourceId | VM에 대한 정규화된 리소스 ID입니다. |
| ResourceUId | VM에 대한 GUID |
| 이름 | 가상 머신 이름입니다. |
| CreatedTime | VM을 만든 날짜 및 시간입니다. |
| DeletedDate | VM을 삭제한 날짜 및 시간입니다. 비어 있으면 아직 삭제되지 않은 것입니다. |
| ResourceOwner | VM의 소유자입니다. 값이 비어 있으면 클레임 할 수 있는 VM이거나 서비스 주체가 만든 것입니다. |
| PricingTier | VM의 가격 책정 계층 |
| ResourceStatus | VM의 가용성 상태입니다. 아직 있으면 활성으로, VM이 삭제된 경우는 비활성으로 표시됩니다. |
| ComputeResourceId | 정규화된 가상 머신 컴퓨팅 리소스 식별자입니다. |
| Claimable | VM이 클레임 할 수 있는 경우 True로 설정 | 
| EnvironmentId | 가상 머신을 만든 환경 리소스 식별자입니다. VM이 환경 리소스의 일부로 만들어지지 않은 경우 비어 있습니다. |
| ExpirationDate | VM의 만료 날짜입니다. 만료 날짜가 설정되지 않은 경우 Empty로 설정합니다.
| GalleryImageReferenceVersion |  VM 기본 이미지의 버전입니다. |
| GalleryImageReferenceOffer | VM 기본 이미지의 제안입니다. |
| GalleryImageReferencePublisher | VM 기본 이미지의 게시자입니다. |
| GalleryImageReferenceSku | VM 기본 이미지의 SKU입니다 |
| GalleryImageReferenceOsType | VM 기본 이미지의 OS 유형입니다 |
| CustomImageId | VM 기본 사용자 지정 이미지의 정규화된 ID입니다. |

**disks.csv** 에는 다음의 데이터 열이 포함되어 있습니다:

| 열 이름 | Description | 
| ----------- | ----------- | 
| SubscriptionId | 랩이 포함된 구독 ID |
| LabUId | 랩에 대한 GUID |
| LabName | 랩 이름 | 
| LabResourceId | 랩에 대한 정규화된 리소스 ID | 
| ResourceGroupName | 랩을 포함하는 리소스 그룹 이름 | 
| ResourceId | VM에 대한 정규화된 리소스 ID입니다. |
| ResourceUId | VM에 대한 GUID |
 |이름 | 연결된 디스크 이름 |
| CreatedTime |데이터 디스크가 만들어진 날짜 및 시간입니다. |
| DeletedDate | 데이터 디스크가 삭제된 날짜 및 시간입니다. |
| ResourceStatus | 리소스의 상태입니다. 리소스가 있으면 활성 상태입니다. 삭제된 경우는 비활성으로 표시됩니다. |
| DiskBlobName | 데이터 디스크에 대한 BLOB 이름입니다. |
| DiskSizeGB | 데이터 디스크의 크기입니다. |
| DiskType | 데이터 디스크의 형식입니다. 표준의 경우 0, 프리미엄의 경우 1입니다. |
| LeasedByVmId | 데이터 디스크가 연결된 VM의 리소스 ID입니다. |


> [!NOTE]
> 여러 랩을 처리하고 전체적인 정보를 얻으려는 경우 두 개의 키 열은 구독 전체에서 고유한 ID인 **LabUID** 및 **ResourceUId** 입니다.

내보낸 데이터는 SQL Server, Power BI 등과 같은 도구를 사용하여 조작하고 시각화 할 수 있습니다. 이 기능은 동일한 Azure 구독을 사용하지 않을 수 있는 관리팀에 랩 사용량을 보고하려는 경우 특히 유용합니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요. 

- [랩에 대한 정책 설정](devtest-lab-set-lab-policy.md)
- [자주 묻는 질문](devtest-lab-faq.yml)
