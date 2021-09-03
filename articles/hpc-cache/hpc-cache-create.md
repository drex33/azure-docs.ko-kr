---
title: Azure HPC Cache 만들기
description: Azure HPC Cache 인스턴스를 만드는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/15/2021
ms.author: v-erkel
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 26272090d3ec18328df2ac553b15e53abc824708
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114294915"
---
# <a name="create-an-azure-hpc-cache"></a>Azure HPC Cache 만들기

Azure Portal 또는 Azure CLI를 사용하여 캐시를 만듭니다.

![맨 아래에 만들기 단추가 있는, Azure Portal의 캐시 개요 스크린샷](media/hpc-cache-home-page.png)

캐시 만들기 및 스토리지 대상 추가 [동영상 데모](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)를 시청하려면 아래 이미지를 클릭하세요.

[![동영상 썸네일: Azure HPC Cache: 설정(동영상 페이지를 방문하려면 클릭)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="portal"></a>[포털](#tab/azure-portal)

## <a name="define-basic-details"></a>기본 세부 정보 정의

![Azure Portal의 프로젝트 세부 정보 페이지 스크린샷](media/hpc-cache-create-basics.png)

**프로젝트 세부 정보** 에서 캐시를 호스트할 구독 및 리소스 그룹을 선택합니다.

**서비스 세부 정보** 에서 캐시 이름과 다음 기타 특성을 설정합니다.

* 위치 - [지원되는 지역](hpc-cache-overview.md#region-availability) 중 하나를 선택합니다.
* 가상 네트워크 - 기존 가상 네트워크를 선택하거나 새 가상 네트워크를 만들 수 있습니다.
* 서브넷 - IP 주소가 64개 이상인 서브넷을 선택하거나 만듭니다(/24). 이 서브넷은 이 Azure HPC Cache 인스턴스에만 사용해야 합니다.

## <a name="set-cache-capacity"></a>캐시 용량 설정
<!-- referenced from GUI - update aka.ms/hpc-cache-iops link if you change this header text -->

**캐시** 페이지에서 캐시의 용량을 설정해야 합니다. 여기에 설정된 값은 캐시가 클라이언트 요청을 처리할 수 있는 속도와 보유할 수 있는 데이터의 양을 결정합니다.

용량은 캐시 비용 및 지원할 수 있는 스토리지 대상 수에도 영향을 줍니다.

캐시 용량은 다음 두 값의 조합입니다.

* 캐시의 최대 데이터 전송률(처리량)(GB/초)
* 캐시된 데이터에 할당되는 스토리지의 양(TB)

![Azure Portal의 캐시 크기 조정 페이지 스크린샷.](media/hpc-cache-create-capacity.png)

### <a name="understand-throughput-and-cache-size"></a>처리량 및 캐시 크기 이해

여러 요인이 HPC Cache의 효율성에 영향을 줄 수 있지만 적절한 처리량 값과 캐시 스토리지 크기를 선택하는 것이 가장 중요한 요인 중 하나입니다.

처리량 값을 선택할 때 실제 데이터 전송률은 워크로드, 네트워크 속도 및 스토리지 대상 유형에 따라 달라집니다.

선택한 값은 전체 캐시 시스템의 최대 처리량을 설정하지만 일부는 오버헤드 작업에 사용됩니다. 예를 들어 클라이언트가 캐시에 아직 저장되지 않은 파일을 요청하거나 파일이 오래된 것으로 표시된 경우 캐시는 일부 처리량을 사용하여 백 엔드 스토리지에서 가져옵니다.

Azure HPC Cache는 캐시 적중률을 최대화하기 위해 캐시되고 미리 로드되는 파일을 관리합니다. 캐시 콘텐츠는 지속적으로 평가되며, 자주 액세스하지 않는 파일은 장기 스토리지로 이동됩니다.

메타데이터와 기타 오버헤드를 위한 추가 공간과 함께 활성 작업 파일 세트를 여유 있게 저장할 수 있는 캐시 스토리지 크기를 선택합니다.

처리량 및 캐시 크기는 특정 캐시에 대해 지원되는 스토리지 대상 수에도 영향을 줍니다. 캐시에 10개보다 많은 스토리지 대상을 사용하려면 처리량 크기에 사용할 수 있는 가장 높은 사용 가능한 캐시 스토리지 크기 값을 선택하거나 높은 처리량 읽기 전용 구성 중 하나를 선택해야 합니다. [스토리지 대상 추가](hpc-cache-add-storage.md#size-your-cache-correctly-to-support-your-storage-targets)에서 자세히 알아보세요.

캐시 크기를 올바르게 조정하는 데 도움이 필요한 경우 Microsoft 서비스 및 지원에 문의하세요.

### <a name="choose-the-cache-type-for-your-needs"></a>필요에 따라 캐시 유형 선택

캐시 용량을 선택할 때 일부 처리량 값에는 고정된 캐시 크기가 있고 다른 값에는 여러 캐시 크기 옵션 중에서 선택할 수 있습니다. 이는 두 가지 다른 스타일의 캐시 인프라가 있기 때문입니다.

* 표준 캐시 - 처리량 메뉴의 **읽기-쓰기 캐싱** 아래에 나열됨

  표준 캐시를 사용하면 여러 캐시 크기 값 중에서 선택할 수 있습니다. 이러한 캐시는 읽기 전용 또는 읽기 및 쓰기 캐싱용으로 구성할 수 있습니다.

* 높은 처리량 캐시 - 처리량 메뉴의 **읽기 전용 캐싱** 아래에 나열됨

  높은 처리량 구성은 NVME 디스크로 미리 구성되어 있기 때문에 캐시 크기를 설정했습니다. 파일 읽기 액세스만 최적화하도록 설계되었습니다.

![포털의 최대 처리량 메뉴 스크린샷. "읽기-쓰기 캐싱" 제목 및 "읽기 전용" 제목 아래에 각각 여러 크기의 옵션이 있습니다.](media/rw-ro-cache-sizing.png)

이 표에서는 두 옵션 간의 몇 가지 중요한 차이점을 설명합니다.

| attribute | 표준 캐시 | 높은 처리량 캐시 |
|--|--|--|
| 처리량 메뉴 범주 |"읽기-쓰기 캐싱"| "읽기 전용 캐싱"|
| 처리량 크기 | 2, 4 또는 8GB/초 | 4.5, 9 또는 16GB/초 |
| 캐시 크기 | 2GB/초의 경우 3, 6 또는 12TB<br/> 4GB/초의 경우 6, 12 또는 24TB<br/> 8GB/초의 경우 12, 24 또는 48TB| 4\.5GB/초의 경우 21TB <br/> 9GB/초의 경우 42TB <br/> 16GB/초의 경우 84TB |
| 최대 스토리지 대상 수 | 캐시 크기 선택에 따라 [10 또는 20](hpc-cache-add-storage.md#size-your-cache-correctly-to-support-your-storage-targets) | 20 |
| 호환되는 스토리지 대상 유형 | Azure Blob, 온-프레미스 NFS 스토리지, NFS 지원 Blob | 온-프레미스 NFS 스토리지 <br/>NFS 지원 Blob Storage는 이 조합에 대한 미리 보기입니다. |
| 캐싱 스타일 | 읽기 캐싱 또는 읽기-쓰기 캐싱 | 읽기 전용 캐싱 |
| 필요하지 않을 때 비용을 절약하기 위해 캐시를 중지할 수 있음 | 예 | 아니요 |

이러한 옵션에 대한 자세한 내용을 알아봅니다.

* [최대 스토리지 대상 수](hpc-cache-add-storage.md#size-your-cache-correctly-to-support-your-storage-targets)
* [읽기 및 쓰기 캐싱 모드](cache-usage-models.md#basic-file-caching-concepts)

## <a name="enable-azure-key-vault-encryption-optional"></a>Azure Key Vault 암호화 사용(선택 사항)

캐시 스토리지에 사용되는 암호화 키를 관리하려면 **디스크 암호화 키** 페이지에서 Azure Key Vault 정보를 제공합니다. 키 자격 증명 모음이 속한 지역과 구독은 캐시와 동일해야 합니다.

고객 관리형 키가 필요하지 않은 경우 이 섹션을 건너뛸 수 있습니다. Azure는 기본적으로 Microsoft 관리형 키를 사용하여 데이터를 암호화합니다. 자세한 내용은 [Azure Storage 암호화](../storage/common/storage-service-encryption.md)를 참조하세요.

> [!NOTE]
> 캐시를 만든 후에는 Microsoft 관리형 키와 고객 관리형 키 간에 변경할 수 없습니다.

고객 관리형 키 암호화 프로세스에 대한 자세한 내용은 [Azure HPC Cache에 고객 관리형 암호화 키 사용](customer-keys.md)을 참조하세요.

!["고객 관리"가 선택되고 "고객 키 설정" 및 "관리 ID" 구성 양식이 표시된 암호화 키 페이지의 스크린샷.](media/create-encryption.png)

**고객 관리형** 을 선택하여 고객 관리형 키 암호화를 선택합니다. 키 자격 증명 모음 사양 필드가 표시됩니다. 사용할 Azure Key Vault를 선택한 다음 이 캐시에 사용할 키 및 버전을 선택합니다. 키는 2048비트 RSA 키여야 합니다. 이 페이지에서 새 키 자격 증명 모음, 키 또는 키 버전을 만들 수 있습니다.

[자동 키 순환](../virtual-machines/disk-encryption.md#automatic-key-rotation-of-customer-managed-keys-preview)을 사용하려면 **항상 현재 키 버전 사용** 확인란을 선택합니다.

이 캐시에 특정 관리 ID를 사용하려면 **관리 ID** 섹션에서 구성합니다. 자세한 내용은 [Azure 리소스에 대한 관리 ID란 무엇인가요?](../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

> [!NOTE]
> 캐시를 만든 후에는 할당된 ID를 변경할 수 없습니다.

시스템 할당 관리 ID 또는 키 자격 증명 모음에 대한 액세스 권한이 아직 없는 사용자 할당 ID를 사용하는 경우 캐시를 만든 후 수행해야 하는 추가 단계가 있습니다. 이 수동 단계에서는 키 자격 증명 모음을 사용하도록 캐시의 관리 ID에 권한을 부여합니다.

* 관리 ID 설정의 차이점을 이해하려면 [캐시에 대한 관리 ID 옵션 선택](customer-keys.md#choose-a-managed-identity-option-for-the-cache)을 읽습니다.
* 수동 단계에 대해 알아보려면 [캐시에서 Azure Key Vault 암호화 승인](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache-if-needed)을 읽습니다.

## <a name="add-resource-tags-optional"></a>리소스 태그 추가(선택 사항)

**태그** 페이지에서는 Azure HPC Cache 인스턴스에 [리소스 태그](../azure-resource-manager/management/tag-resources.md)를 추가할 수 있습니다.

## <a name="finish-creating-the-cache"></a>캐시 만들기 완료

새 캐시를 구성한 후 **검토 + 만들기** 탭을 클릭합니다. 포털에서 선택 항목의 유효성이 검사되고 선택 항목을 검토할 수 있습니다. 모든 선택 항목이 올바르면 **만들기** 를 클릭합니다.

캐시를 만드는 데 10분 정도 걸립니다. Azure Portal의 알림 패널에서 진행 상황을 추적할 수 있습니다.

![포털의 캐시 만들기 "배포 진행 중" 및 "알림" 페이지의 스크린샷](media/hpc-cache-deploy-status.png)

만들기가 완료되면 새 Azure HPC Cache 인스턴스에 대한 링크가 포함된 알림이 표시되고, 해당 캐시가 구독의 **리소스** 목록에 표시됩니다.

![Azure Portal의 Azure HPC Cache 인스턴스 스크린샷](media/hpc-cache-new-overview.png)

> [!NOTE]
> 캐시가 고객 관리형 암호화 키를 사용하고, 만든 후 수동 권한 부여 단계가 필요한 경우 배포 상태가 완료로 변경되기 전에 캐시가 리소스 목록에 나타날 수 있습니다. 캐시의 상태가 **키 대기 중** 이 되는 즉시 키 자격 증명 모음을 사용하도록 [권한을 부여](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache-if-needed)할 수 있습니다.

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="create-the-cache-with-azure-cli"></a>Azure CLI를 사용하여 캐시 만들기

[Azure HPC Cache용 Azure CLI 설치](./az-cli-prerequisites.md)

> [!NOTE]
> 현재 Azure CLI는 고객 관리형 암호화 키를 사용한 캐시 만들기를 지원하지 않습니다. Azure 포털을 사용합니다.

[az hpc-cache create](/cli/azure/hpc-cache#az_hpc_cache_create) 명령을 사용하여 새 Azure HPC Cache를 만듭니다.

다음 값을 제공합니다.

* 캐시 리소스 그룹 이름
* 캐시 이름
* Azure 지역
* 다음 형식의 캐시 서브넷:

  ``--subnet "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/subnets/<cache_subnet_name>"``

  캐시 서브넷에는 최소 64개의 IP 주소(/24)가 필요하며 다른 리소스를 저장할 수 없습니다.

* 캐시 용량. Azure HPC Cache의 최대 처리량을 설정하는 두 값은 다음과 같습니다.

  * 캐시 크기(GB)
  * 캐시 인프라에서 사용되는 가상 머신의 SKU

  [az hpc-cache sku 목록](/cli/azure/hpc-cache/skus)에는 사용할 수 있는 SKU와 각 SKU에 유효한 캐시 크기 옵션이 나와 있습니다. 캐시 크기 옵션은 3TB~48TB이지만 일부 값만 지원됩니다.

  이 차트에는 이 문서가 작성되는 시점(2020년 7월)에 유효한 캐시 크기 및 SKU 조합이 나와 있습니다.

  | 캐시 크기 | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072GB    | 예         | 아니요          | 아니요          |
  | 6144GB    | 예         | 예         | 아니요          |
  | 12288GB   | 예         | 예         | 예         |
  | 24576GB   | 아니요          | 예         | 예         |
  | 49152GB   | 아니요          | 아니요          | 예         |

  캐시에서 10개가 넘는 스토리지 대상을 사용하려면 SKU에 대해 사용 가능한 가장 높은 캐시 크기 값을 선택합니다. 이러한 구성은 최대 20개의 스토리지 대상을 지원합니다.

  가격 책정과 처리량 및 워크플로에 적절하게 캐시 크기를 조정하는 방법에 대한 중요한 정보는 포털 지침 탭의 **캐시 용량 설정** 섹션을 참조하세요.

캐시 생성 예제:

```azurecli
az hpc-cache create --resource-group doc-demo-rg --name my-cache-0619 \
    --location "eastus" --cache-size-gb "3072" \
    --subnet "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default" \
    --sku-name "Standard_2G"
```

캐시 생성에는 몇 분 정도 걸립니다. 성공할 경우 create 명령은 다음과 같은 출력을 반환합니다.

```azurecli
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },
  "id": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619",
  "location": "eastus",
  "mountAddresses": [
    "10.3.0.17",
    "10.3.0.18",
    "10.3.0.19"
  ],
  "name": "my-cache-0619",
  "provisioningState": "Succeeded",
  "resourceGroup": "doc-demo-rg",
  "sku": {
    "name": "Standard_2G"
  },
  "subnet": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default",
  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.42",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-04-01T15:19:54.068299+00:00",
    "pendingFirmwareVersion": null
  }
}
```

메시지에는 다음 항목을 포함한 몇 가지 유용한 정보가 포함되어 있습니다.

* 클라이언트 탑재 주소 - 캐시에 클라이언트를 연결할 준비가 되면 이러한 IP 주소를 사용하세요. 자세한 내용은 [Azure HPC Cache 탑재](hpc-cache-mount.md)를 참조하세요.
* 업그레이드 상태 - 소프트웨어 업데이트가 릴리스되면 이 메시지가 변경됩니다. 편한 시간에 수동으로 [캐시 소프트웨어를 업그레이드](hpc-cache-manage.md#upgrade-cache-software)할 수 있고, 업그레이드하지 않아도 며칠 후에 자동으로 적용됩니다.

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

> [!CAUTION]
> Az.HPCCache PowerShell 모듈은 현재 공개 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공됩니다. 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능은 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="requirements"></a>요구 사항

PowerShell을 로컬로 사용하도록 선택하는 경우 이 문서에서는 Az PowerShell 모듈을 설치하고 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용하여 Azure 계정에 연결해야 합니다. Az PowerShell 모듈을 설치하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요. Cloud Shell을 사용하도록 선택하는 경우 자세한 내용은 [Azure Cloud Shell 개요](../cloud-shell/overview.md)를 참조하세요.

> [!IMPORTANT]
> **Az.HPCCache** PowerShell 모듈은 미리 보기로 제공되는 동안 `Install-Module` cmdlet을 사용하여 별도로 설치해야 합니다. 이 PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스의 일부가 되며, 기본적으로 Azure Cloud Shell 내에서 사용할 수 있습니다.

```azurepowershell-interactive
Install-Module -Name Az.HPCCache
```

## <a name="create-the-cache-with-azure-powershell"></a>Azure PowerShell을 사용하여 캐시 만들기

> [!NOTE]
> 현재 Azure PowerShell은 고객 관리형 암호화 키를 사용한 캐시 만들기를 지원하지 않습니다. Azure 포털을 사용합니다.

[New-AzHpcCache](/powershell/module/az.hpccache/new-azhpccache) cmdlet을 사용하여 새 Azure HPC Cache를 만듭니다.

다음 값을 제공합니다.

* 캐시 리소스 그룹 이름
* 캐시 이름
* Azure 지역
* 다음 형식의 캐시 서브넷:

  `-SubnetUri "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/subnets/<cache_subnet_name>"`

  캐시 서브넷에는 최소 64개의 IP 주소(/24)가 필요하며 다른 리소스를 저장할 수 없습니다.

* 캐시 용량. Azure HPC Cache의 최대 처리량을 설정하는 두 값은 다음과 같습니다.

  * 캐시 크기(GB)
  * 캐시 인프라에서 사용되는 가상 머신의 SKU

  [Get-AzHpcCacheSku](/powershell/module/az.hpccache/get-azhpccachesku)는 사용할 수 있는 SKU와 각 SKU에 유효한 캐시 크기 옵션을 보여 줍니다. 캐시 크기 옵션은 3TB~48TB이지만 일부 값만 지원됩니다.

  이 차트에는 이 문서가 작성되는 시점(2020년 7월)에 유효한 캐시 크기 및 SKU 조합이 나와 있습니다.

  | 캐시 크기 | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072GB    | 예         | 아니요          | 아니요          |
  | 6144GB    | 예         | 예         | 아니요          |
  | 12,288GB   | 예         | 예         | 예         |
  | 24,576GB   | 아니요          | 예         | 예         |
  | 49,152GB   | 아니요          | 아니요          | 예         |

  가격 책정과 처리량 및 워크플로에 적절하게 캐시 크기를 조정하는 방법에 대한 중요한 정보는 포털 지침 탭의 **캐시 용량 설정** 섹션을 참조하세요.

캐시 생성 예제:

```azurepowershell-interactive
$cacheParams = @{
  ResourceGroupName = 'doc-demo-rg'
  CacheName = 'my-cache-0619'
  Location = 'eastus'
  cacheSize = '3072'
  SubnetUri = "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default"
  Sku = 'Standard_2G'
}
New-AzHpcCache @cacheParams
```

캐시 생성에는 몇 분 정도 걸립니다. 성공할 경우 create 명령은 다음 출력을 반환합니다.

```Output
cacheSizeGb       : 3072
health            : @{state=Healthy; statusDescription=The cache is in Running state}
id                : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619
location          : eastus
mountAddresses    : {10.3.0.17, 10.3.0.18, 10.3.0.19}
name              : my-cache-0619
provisioningState : Succeeded
resourceGroup     : doc-demo-rg
sku               : @{name=Standard_2G}
subnet            : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default
tags              :
type              : Microsoft.StorageCache/caches
upgradeStatus     : @{currentFirmwareVersion=5.3.42; firmwareUpdateDeadline=1/1/0001 12:00:00 AM; firmwareUpdateStatus=unavailable; lastFirmwareUpdate=4/1/2020 10:19:54 AM; pendingFirmwareVersion=}
```

메시지에는 다음 항목을 포함한 몇 가지 유용한 정보가 포함되어 있습니다.

* 클라이언트 탑재 주소 - 캐시에 클라이언트를 연결할 준비가 되면 이러한 IP 주소를 사용하세요. 자세한 내용은 [Azure HPC Cache 탑재](hpc-cache-mount.md)를 참조하세요.
* 업그레이드 상태 - 소프트웨어 업데이트가 릴리스되면 이 메시지가 변경됩니다. 편한 시간에 수동으로 [캐시 소프트웨어를 업그레이드](hpc-cache-manage.md#upgrade-cache-software)할 수 있고, 업그레이드하지 않아도 며칠 후에 자동으로 적용됩니다.

---

## <a name="next-steps"></a>다음 단계

**리소스** 목록에 캐시가 표시되면 다음 단계로 이동할 수 있습니다.

* [스토리지 대상을 정의](hpc-cache-add-storage.md)하여 데이터 원본에 대한 액세스 권한을 캐시에 부여합니다.
* 고객 관리형 암호화 키를 사용하고 캐시 개요 페이지에서 [Azure Key Vault 암호화 권한을 부여](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache-if-needed)하여 캐시 설정을 완료해야 하는 경우 [고객 관리형 암호화 키 사용](customer-keys.md)의 지침을 따릅니다. 이 단계는 스토리지를 추가하기 전에 수행해야 합니다.
