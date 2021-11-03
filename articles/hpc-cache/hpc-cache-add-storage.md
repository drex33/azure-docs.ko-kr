---
title: Azure HPC Cache에 스토리지 추가
description: Azure HPC Cache가 장기 파일 스토리지에 대해 온-프레미스 NFS 시스템 또는 Azure Blob 컨테이너를 사용할 수 있도록 스토리지 대상을 정의하는 방법
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/22/2021
ms.custom: subject-rbac-steps
ms.author: femila
ms.openlocfilehash: 29ecaecc579d1947d3e86ef6d9e080030a419331
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070606"
---
# <a name="add-storage-targets"></a>스토리지 대상 추가

'스토리지 대상'은 Azure HPC Cache를 통해 액세스되는 파일에 대한 백 엔드 스토리지입니다. NFS 스토리지(예: 온-프레미스 하드웨어 시스템)를 추가하거나 Azure Blob에 데이터를 저장할 수 있습니다.

모든 캐시에 대해 10개의 다른 스토리지 대상을 정의할 수 있으며, 더 큰 캐시는 [최대 20개의 스토리지 대상을 지원할](#size-your-cache-correctly-to-support-your-storage-targets)수 있습니다.

캐시는 하나의 [집계형 네임스페이스](hpc-cache-namespace.md)에서 모든 스토리지 대상을 제공합니다. 스토리지 대상을 추가하면 네임스페이스 경로가 별도로 구성됩니다.

스토리지 내보내기는 캐시의 가상 네트워크에서 액세스할 수 있어야 합니다. 온-프레미스 하드웨어 스토리지의 경우 NFS 스토리지 액세스를 위한 호스트 이름을 확인할 수 있는 DNS 서버를 설정해야 할 수 있습니다. 자세한 내용은 [DNS 액세스](hpc-cache-prerequisites.md#dns-access)를 참조하세요.

캐시를 만든 후 스토리지 대상을 추가합니다. 다음 프로세스를 따릅니다.

1. [캐시 만들기](hpc-cache-create.md)
1. 스토리지 대상 정의(이 문서의 정보)
1. [클라이언트 연결 경로 만들기](add-namespace-paths.md)([집계형 네임스페이스](hpc-cache-namespace.md)용)

스토리지 대상을 추가하는 절차는 대상이 사용하는 스토리지 유형에 따라 약간 다릅니다. 각 유형에 대한 세부 정보는 다음과 같습니다.

아래 이미지를 클릭하여 Azure Portal에서 캐시를 만들고 스토리지 대상을 추가하는 [동영상 데모](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)를 시청하세요.

[![동영상 썸네일: Azure HPC Cache: 설정(동영상 페이지를 방문하려면 클릭)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="size-your-cache-correctly-to-support-your-storage-targets"></a>스토리지 대상을 지원하도록 캐시 크기를 올바르게 조정

지원되는 스토리지 대상 수는 캐시를 만들 때 설정되는 캐시 크기에 따라 달라집니다. 캐시 용량은 처리량 용량(GB/초)과 스토리지 용량(TB)의 조합입니다.

* 최대 10개의 스토리지 대상 - 선택한 처리량에 대해 가장 작거나 중간 캐시 스토리지 값이 있는 표준 캐시에는 최대 10개의 스토리지 대상이 있을 수 있습니다.

  예를 들어, 2GB/초 처리량을 선택하고 가장 높은 캐시 스토리지 크기를 선택하지 않으면 캐시는 최대 10개의 스토리지 대상을 지원합니다.

* 최대 20개의 스토리지 대상 -

  * 모든 높은 처리량 캐시(미리 구성된 캐시 스토리지 크기 포함)는 최대 20개의 스토리지 대상을 지원할 수 있습니다.
  * 선택한 처리량 값에 대해 사용 가능한 가장 높은 캐시 크기를 선택하는 경우 표준 캐시는 최대 20개의 스토리지 대상을 지원할 수 있습니다. Azure CLI 사용하는 경우 캐시 SKU에 가장 높은 유효한 캐시 크기를 선택합니다.

처리량 및 캐시 크기 설정에 대한 자세한 내용은 [캐시 용량 설정](hpc-cache-create.md#set-cache-capacity)을 읽어보세요.

## <a name="choose-the-correct-storage-target-type"></a>올바른 스토리지 대상 유형 선택

세 가지 스토리지 대상 유형인 **NFS**, **Blob**, **ADLS-NFS** 중에서 선택할 수 있습니다. 이 HPC Cache 프로젝트 중에 파일을 저장하는 데 사용할 스토리지 시스템의 종류와 일치하는 유형을 선택합니다.

* **NFS** - NAS(네트워크 연결 스토리지) 시스템의 데이터에 액세스하기 위한 NFS 스토리지 대상을 만듭니다. 이 대상은 온-프레미스 스토리지 시스템 또는 NFS를 통해 액세스할 수 있는 다른 스토리지 유형일 수 있습니다.

  * 요구 사항: [NFS 스토리지 요구 사항](hpc-cache-prerequisites.md#nfs-storage-requirements)
  * 지침: [새 NFS 스토리지 대상 추가](#add-a-new-nfs-storage-target)

* **Blob** - Blob Storage 대상을 사용하여 새 Azure Blob 컨테이너에 작업 파일을 저장합니다. 이 컨테이너는 Azure HPC Cache에서만 읽거나 쓸 수 있습니다.

  * 필수 조건: [Blob Storage 요구 사항](hpc-cache-prerequisites.md#blob-storage-requirements)
  * 지침: [새 Azure Blob Storage 대상 추가](#add-a-new-azure-blob-storage-target)

* **ADLS-NFS** - ADLS-NFS 스토리지 대상은 [NFS 지원 Blob](../storage/blobs/network-file-system-protocol-support.md) 컨테이너의 데이터에 액세스합니다. 표준 NFS 명령을 사용하여 컨테이너를 미리 로드할 수 있으며 나중에 NFS를 사용하여 파일을 읽을 수 있습니다.

  * 필수 조건: [ADLS-NFS 스토리지 요구 사항](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements)
  * 지침: [새 ADLS-NFS 스토리지 대상 추가](#add-a-new-adls-nfs-storage-target)

## <a name="add-a-new-azure-blob-storage-target"></a>새 Azure Blob Storage 대상 추가

새 Blob Storage 대상에는 빈 Blob 컨테이너 또는 Azure HPC Cache 클라우드 파일 시스템 형식의 데이터로 채워지는 컨테이너가 필요합니다. [Azure Blob Storage로 데이터 이동](hpc-cache-ingest.md)에서 Blob 컨테이너를 미리 로드하는 방법을 자세히 알아보세요.

Azure Portal **스토리지 대상 추가** 페이지에는 스토리지 대상을 추가하기 직전에 새 Blob 컨테이너를 만드는 옵션이 포함되어 있습니다.

> [!NOTE]
>
> * NFS 탑재 Blob Storage의 경우 [ADLS-NFS 스토리지 대상](#add-a-new-adls-nfs-storage-target) 유형을 사용합니다.
> * [높은 처리량 캐시 구성](hpc-cache-create.md#choose-the-cache-type-for-your-needs)은 표준 Azure Blob Storage 대상을 지원하지 않습니다. 대신 NFS 지원 Blob Storage(ADLS-NFS)를 사용합니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 캐시 인스턴스를 열고 왼쪽 사이드바에서 **스토리지 대상** 을 클릭합니다.

![테이블에 두 개의 기존 스토리지 대상이 있고 테이블 위의 + 스토리지 대상 추가 단추가 강조 표시된 설정 > 스토리지 대상 페이지의 스크린샷](media/add-storage-target-button.png)

**스토리지 대상** 페이지에는 기존 대상이 모두 나열되며 새 대상을 추가할 수 있는 링크가 제공됩니다.

**스토리지 대상 추가** 단추를 클릭합니다.

![새 Azure Blob Storage 대상에 대한 정보로 채워진 스토리지 대상 추가 페이지의 스크린샷](media/hpc-cache-add-blob.png)

Azure Blob 컨테이너를 정의하려면 다음 정보를 입력합니다.

* **스토리지 대상 이름** - Azure HPC Cache에서 이 스토리지 대상을 식별하는 이름을 설정합니다.
* **대상 유형** - **Blob** 을 선택합니다.
* **스토리지 계정** - 사용할 스토리지 계정을 선택합니다.

  [액세스 역할 추가](#add-the-access-control-roles-to-your-account)에 설명된 대로 캐시 인스턴스에 스토리지 계정에 액세스할 수 있는 권한을 부여해야 합니다.

  사용할 수 있는 스토리지 계정 종류에 대한 자세한 내용은 [Blob Storage 요구 사항](hpc-cache-prerequisites.md#blob-storage-requirements)을 참조하세요.

* **스토리지 컨테이너** - 이 대상의 Blob 컨테이너를 선택하거나 **새로 만들기** 를 클릭합니다.

  ![새 컨테이너의 이름 및 액세스 수준(비공개)을 지정하는 대화 상자의 스크린샷](media/add-blob-new-container.png)

완료되면 **확인** 을 클릭하여 스토리지 대상을 추가합니다.

> [!NOTE]
> 스토리지 계정 방화벽이 '선택한 네트워크'로만 액세스를 제한하도록 설정된 경우에는 [Blob Storage 계정 방화벽 설정 해결](hpc-cache-blob-firewall-fix.md)에 설명된 임시 해결 방법을 사용합니다.

### <a name="add-the-access-control-roles-to-your-account"></a>계정에 액세스 제어 역할 추가

Azure HPC Cache는 [Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control/index.yml)를 사용하여 캐시 서비스가 Azure Blob Storage 대상의 스토리지 계정에 액세스할 수 있도록 권한을 부여합니다.

스토리지 계정 소유자는 'HPC Cache 리소스 공급자' 사용자를 위해 [스토리지 계정 참가자](../role-based-access-control/built-in-roles.md#storage-account-contributor) 및 [스토리지 Blob 데이터 참가자](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) 역할을 명시적으로 추가해야 합니다.

미리 이 작업을 수행할 수도 있고 Blob Storage 대상을 추가하는 포털 페이지에서 링크를 클릭할 수도 있습니다. 역할 설정이 Azure 환경을 통해 전파되는 데 최대 5분이 걸릴 수 있으므로 스토리지 대상을 만들려면 역할을 추가한 후 몇 분 정도 기다려야 합니다.

1. 스토리지 계정에 대한 **액세스 제어(IAM)** 를 엽니다.

1. **추가** > **역할 할당 추가** 를 선택하여 역할 할당 추가 페이지를 엽니다.

1. 다음 역할을 한 번에 하나씩 할당합니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.
    
    | 설정 | 값 |
    | --- | --- |
    | 역할 | [Storage 계정 기여자](../role-based-access-control/built-in-roles.md#storage-account-contributor) <br/>  [Storage Blob 데이터 기여자](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) |
    | 다음에 대한 액세스 할당 | HPC Cache 리소스 공급자 |

    ![역할 할당 추가 페이지](../../includes/role-based-access-control/media/add-role-assignment-page.png)

   > [!NOTE]
   > HPC Cache 리소스 공급자를 찾을 수 없는 경우 “storagecache” 문자열을 대신 검색해 보세요. 이는 서비스 주체의 GA 이전 이름이었습니다.

<!-- 
Steps to add the Azure roles:

1. Open the **Access control (IAM)** page for the storage account. (The link in the **Add storage target** page automatically opens this page for the selected account.)

1. Click the **+** at the top of the page and choose **Add a role assignment**.

1. Select the role "Storage Account Contributor" from the list.

1. In the **Assign access to** field, leave the default value selected ("Azure AD user, group, or service principal").  

1. In the **Select** field, search for "hpc".  This string should match one service principal, named "HPC Cache Resource Provider". Click that principal to select it.

   > [!NOTE]
   > If a search for "hpc" doesn't work, try using the string "storagecache" instead. Users who participated in previews (before GA) might need to use the older name for the service principal.

1. Click the **Save** button at the bottom.

1. Repeat this process to assign the role "Storage Blob Data Contributor".  

![screenshot of add role assignment GUI](media/hpc-cache-add-role.png) -->

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisite-storage-account-access"></a>필수 구성 요소: 스토리지 계정 액세스 권한

[Azure HPC Cache용 Azure CLI 설치](./az-cli-prerequisites.md)

Blob Storage 대상을 추가하기 전에 캐시에 스토리지 계정에 액세스할 수 있는 올바른 역할이 있는지, 방화벽 설정에서 스토리지 대상을 만들도록 허용하는지 확인합니다.

Azure HPC Cache는 [Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control/index.yml)를 사용하여 캐시 서비스가 Azure Blob Storage 대상의 스토리지 계정에 액세스할 수 있도록 권한을 부여합니다.

스토리지 계정 소유자는 'HPC Cache 리소스 공급자' 사용자를 위해 [스토리지 계정 참가자](../role-based-access-control/built-in-roles.md#storage-account-contributor) 및 [스토리지 Blob 데이터 참가자](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) 역할을 명시적으로 추가해야 합니다.

캐시에 이러한 역할이 없으면 스토리지 대상을 만들지 못합니다.

역할 설정이 Azure 환경을 통해 전파되는 데 최대 5분이 걸릴 수 있으므로 스토리지 대상을 만들려면 역할을 추가한 후 몇 분 정도 기다려야 합니다.

[Azure CLI를 사용하여 Azure 역할 할당 추가 또는 제거](../role-based-access-control/role-assignments-cli.md)를 참조하세요.

또한 스토리지 계정의 방화벽 설정을 확인합니다. 방화벽이 '선택한 네트워크'로만 액세스를 제한하도록 설정된 경우 스토리지 대상 만들기가 실패할 수 있습니다. [Blob Storage 계정 방화벽 설정 해결](hpc-cache-blob-firewall-fix.md)에 설명된 해결 방법을 사용합니다.

### <a name="add-a-blob-storage-target-with-azure-cli"></a>Azure CLI를 사용하여 Blob Storage 대상 추가

[az hpc-cache blob-storage-target add](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_add) 인터페이스를 사용하여 Azure Blob Storage 대상을 정의합니다.

> [!NOTE]
> 현재 Azure CLI 명령은 스토리지 대상을 추가할 때 네임스페이스 경로를 만들어야 합니다. 이는 Azure Portal 인터페이스에 사용되는 프로세스와 다릅니다.

표준 리소스 그룹 및 캐시 이름 매개 변수 외에도 스토리지 대상에 대해 다음과 같은 옵션을 제공해야 합니다.

* ``--name`` - Azure HPC Cache에서 이 스토리지 대상을 식별하는 이름을 설정합니다.

* ``--storage-account`` - 다음 형식의 계정 식별자입니다. /subscriptions/ *<subscription_id>* /resourceGroups/ *<storage_resource_group>* /providers/Microsoft.Storage/storageAccounts/ *<account_name>*

  사용할 수 있는 스토리지 계정 종류에 대한 자세한 내용은 [Blob Storage 요구 사항](hpc-cache-prerequisites.md#blob-storage-requirements)을 참조하세요.

* ``--container-name`` - 이 스토리지 대상에 사용할 컨테이너의 이름을 지정합니다.

* ``--virtual-namespace-path`` - 이 스토리지 대상의 클라이언트 연결 파일 경로를 설정합니다. 각 경로를 따옴표로 묶습니다. 가상 네임스페이스 기능에 대해 자세히 알아보려면 [집계형 네임스페이스 계획](hpc-cache-namespace.md)을 참조하세요.

예제 명령:

```azurecli
az hpc-cache blob-storage-target add --resource-group "hpc-cache-group" \
    --cache-name "cache1" --name "blob-target1" \
    --storage-account "/subscriptions/<subscriptionID>/resourceGroups/myrgname/providers/Microsoft.Storage/storageAccounts/myaccountname" \
    --container-name "container1" --virtual-namespace-path "/blob1"
```

---

## <a name="add-a-new-nfs-storage-target"></a>새 NFS 스토리지 대상 추가

이 스토리지 시스템의 데이터를 저장하는 방법을 캐시에 알려 주는 사용 모델 설정을 포함하여 NFS 스토리지 대상의 설정은 Blob Storage 대상과 다릅니다.

![NFS 대상이 정의된 스토리지 대상 추가 페이지의 스크린샷](media/add-nfs-target.png)

> [!NOTE]
> NFS 스토리지 대상을 만들기 전에 Azure HPC Cache가 스토리지 시스템에 액세스할 수 있고 권한 요구 사항을 충족하는지 확인합니다. 캐시에서 스토리지 시스템에 액세스할 수 없으면 스토리지 대상 만들기가 실패합니다. 자세한 내용은 [NFS 스토리지 요구 사항](hpc-cache-prerequisites.md#nfs-storage-requirements) 및 [NAS 구성 및 NFS 스토리지 대상 문제 해결](troubleshoot-nas.md)을 참조하세요.

### <a name="choose-a-usage-model"></a>사용 모델 선택

NFS를 사용하여 스토리지 시스템에 연결하는 스토리지 대상을 만드는 경우 해당 대상에 대한 사용 모델을 선택해야 합니다. 이 모델은 데이터를 캐시하는 방법을 결정합니다.

이러한 설정에 대한 자세한 내용은 [사용 모델 이해](cache-usage-models.md)를 참조하세요.

HPC Cache의 기본 제공 사용 모델을 사용하여 빠른 응답과 부실 데이터를 가져올 위험 간에 균형을 유지하는 방법을 선택할 수 있습니다. 파일 읽기 속도를 최적화하려면 캐시의 파일을 백 엔드 파일과 비교해서 확인하는지 여부는 중요하지 않을 수 있습니다. 반면에 파일을 원격 스토리지에서 항상 최신 상태로 유지하려면 자주 확인하는 모델을 선택합니다.

> [!NOTE]
> [높은 처리량 스타일 캐시](hpc-cache-create.md#choose-the-cache-type-for-your-needs)는 읽기 캐싱만 지원합니다.

다음 세 가지 옵션이면 대부분의 상황을 해결할 수 있습니다.

* **대량 읽기, 드문 쓰기** - 고정 상태이거나 거의 변경되지 않는 파일에 대한 읽기 액세스를 가속화합니다.

  이 옵션은 클라이언트 읽기에서 파일을 캐시하지만 클라이언트 쓰기를 즉시 백 엔드 스토리지로 전달합니다. 캐시에 저장된 파일은 NFS 스토리지 볼륨의 파일과 자동으로 비교되지 않습니다.

  파일을 캐시에 먼저 쓰지 않고 스토리지 시스템에서 직접 수정될 수 있는 위험 요소가 있으면 이 옵션을 사용하지 마세요. 이 경우 파일의 캐시된 버전이 백 엔드 파일과 동기화되지 않습니다.

* **15% 쓰기보다 큼** - 이 옵션을 선택하면 읽기 및 쓰기 성능이 향상됩니다.

  클라이언트 읽기와 클라이언트 쓰기가 모두 캐시됩니다. 캐시의 파일은 백 엔드 스토리지 시스템의 파일 보다 최신 버전으로 간주됩니다. 캐시된 파일은 8시간마다 자동으로 백 엔드 스토리지에 있는 파일과만 비교됩니다. 캐시에 있는 수정된 파일은 추가 변경 없이 20분 동안 캐시에 유지되면 백 엔드 스토리지 시스템에 기록됩니다.

  백 엔드 스토리지 볼륨을 직접 탑재하는 클라이언트가 있는 경우에는 이 옵션을 사용하지 마세요. 만료된 파일이 있을 위험이 있습니다.

* **클라이언트가 캐시를 무시하고 NFS 대상에 씀** - 워크플로의 클라이언트에서 먼저 캐시에 쓰지 않고 스토리지 시스템에 직접 데이터를 쓰거나 데이터 일관성을 최적화하려는 경우 이 옵션을 선택합니다.

  클라이언트에서 요청하는 파일이 캐시되지만 클라이언트에서 해당 파일에 수행한 변경 내용은 백 엔드 스토리지 시스템에 즉시 전달됩니다. 캐시의 파일은 업데이트를 위해 자주 백 엔드 버전과 비교됩니다. 이 확인은 파일이 캐시를 통하지 않고 스토리지 시스템에서 직접 변경될 때 데이터 일관성을 유지합니다.

다른 옵션에 대한 자세한 내용은 [사용 모델 이해](cache-usage-models.md)를 참조하세요.

이 표에는 모든 사용 모델의 차이점이 요약되어 있습니다.

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

> [!NOTE]
> **백 엔드 확인** 값은 캐시가 해당 파일을 원격 스토리지의 원본 파일과 자동으로 비교하는 경우를 보여 줍니다. 그러나 백 엔드 스토리지 시스템에 readdirplus 작업을 포함하는 클라이언트 요청을 전송하여 비교를 트리거할 수 있습니다. readdirplus는 디렉터리 메타데이터를 반환하는 표준 NFS API(확장된 읽기라고도 함)로, 캐시에서 파일을 비교하고 업데이트하도록 합니다.

### <a name="create-an-nfs-storage-target"></a>NFS 스토리지 대상 만들기

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 캐시 인스턴스를 열고 왼쪽 사이드바에서 **스토리지 대상** 을 클릭합니다.

![테이블에 두 개의 기존 스토리지 대상이 있고 테이블 위의 + 스토리지 대상 추가 단추가 강조 표시된 설정 > 스토리지 대상 페이지의 스크린샷](media/add-storage-target-button.png)

**스토리지 대상** 페이지에는 기존 대상이 모두 나열되며 새 대상을 추가할 수 있는 링크가 제공됩니다.

**스토리지 대상 추가** 단추를 클릭합니다.

![NFS 대상이 정의된 스토리지 대상 추가 페이지의 스크린샷](media/add-nfs-target.png)

NFS 지원 스토리지 대상에 대한 다음 정보를 제공합니다.

* **스토리지 대상 이름** - Azure HPC Cache에서 이 스토리지 대상을 식별하는 이름을 설정합니다.

* **대상 유형** - **NFS** 를 선택합니다.

* **호스트 이름** - NFS 스토리지 시스템의 IP 주소 또는 정규화된 도메인 이름을 입력합니다. (캐시가 이름을 확인할 수 있는 DNS 서버에 액세스할 수 있는 경우에만 도메인 이름을 사용합니다.) 스토리지 시스템이 여러 IP에서 참조되는 경우 여러 IP 주소를 입력할 수 있습니다.

* **사용 모델** - 위의 [사용 모델 선택](#choose-a-usage-model)에 설명된 대로 워크플로에 따라 데이터 캐싱 프로필 중 하나를 선택합니다.

완료되면 **확인** 을 클릭하여 스토리지 대상을 추가합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC Cache용 Azure CLI 설치](./az-cli-prerequisites.md)

Azure CLI 명령 [az hpc-cache nfs-storage-target add](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_add)를 사용하여 스토리지 대상을 만듭니다.

> [!NOTE]
> 현재 Azure CLI 명령은 스토리지 대상을 추가할 때 네임스페이스 경로를 만들어야 합니다. 이는 Azure Portal 인터페이스에 사용되는 프로세스와 다릅니다.

캐시 이름 및 캐시 리소스 그룹 외에 다음 값을 제공합니다.

* ``--name`` - Azure HPC Cache에서 이 스토리지 대상을 식별하는 이름을 설정합니다.
* ``--nfs3-target`` - NFS 스토리지 시스템의 IP 주소입니다. (캐시에서 이름을 확인할 수 있는 DNS 서버에 액세스할 수 있는 경우 여기에 정규화된 도메인 이름을 사용할 수 있습니다.)
* ``--nfs3-usage-model`` -위의 [사용 모델 선택](#choose-a-usage-model)에 설명된 데이터 캐싱 프로필 중 하나입니다.

  [az hpc-cache usage-model list](/cli/azure/hpc-cache/usage-model#az_hpc_cache_usage_model_list) 명령을 사용하여 사용 모델의 이름을 확인합니다.

* ``--junction`` - 연결 매개 변수는 클라이언트 연결 가상 파일 경로를 스토리지 시스템의 내보내기 경로에 연결합니다.

  각 경로가 동일한 스토리지 시스템의 다른 내보내기 또는 하위 디렉터리를 나타내는 한 NFS 스토리지 대상에는 여러 개의 가상 경로가 있을 수 있습니다. 하나의 스토리지 대상에서 하나의 스토리지 시스템에 대한 모든 경로를 만듭니다.

  언제든지 스토리지 대상에서 [네임스페이스 경로를 추가하고 편집](add-namespace-paths.md)할 수 있습니다.

  ``--junction`` 매개 변수는 다음 값을 사용합니다.

  * ``namespace-path`` - 클라이언트 연결 가상 파일 경로
  * ``nfs-export`` - 클라이언트 연결 경로와 연결할 스토리지 시스템 내보내기
  * ``target-path``(선택 사항) - 필요한 경우 내보내기의 하위 디렉터리

  예: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

  가상 네임스페이스 기능에 대해 자세히 알아보려면 [집계형 네임스페이스 구성](hpc-cache-namespace.md)을 참조하세요.

예제 명령:

```azurecli

az hpc-cache nfs-storage-target add --resource-group "hpc-cache-group" --cache-name "doc-cache0629" \
    --name nfsd1 --nfs3-target 10.0.0.4 --nfs3-usage-model WRITE_WORKLOAD_15 \
    --junction namespace-path="/nfs1/data1" nfs-export="/datadisk1" target-path=""
```

출력:
```azurecli

{- Finished ..
  "clfs": null,
  "id": "/subscriptions/<subscriptionID>/resourceGroups/hpc-cache-group/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "hpc-cache-group",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}

```

---

## <a name="add-a-new-adls-nfs-storage-target"></a>새 ADLS-NFS 스토리지 대상 추가

ADLS-NFS 스토리지 대상은 NFS(네트워크 파일 시스템) 3.0 프로토콜을 지원하는 Azure Blob 컨테이너를 사용합니다.

이 기능에 관한 자세한 내용은 [NFS 3.0 프로토콜 지원](../storage/blobs/network-file-system-protocol-support.md)을 참조하세요.

ADLS-NFS 스토리지 대상은 Blob Storage 대상 및 NFS 스토리지 대상과 약간씩 유사성이 있습니다. 예를 들면 다음과 같습니다.

* Blob Storage 대상과 마찬가지로 Azure HPC Cache 에 [스토리지 계정에 액세스](#add-the-access-control-roles-to-your-account)할 수 있는 권한을 제공해야 합니다.
* NFS 스토리지 대상과 마찬가지로 캐시 [사용 모델](#choose-a-usage-model)을 설정해야 합니다.
* NFS 사용 Blob 컨테이너에는 NFS 호환 계층 구조가 있기 때문에 데이터를 수집하기 위해 캐시를 사용할 필요가 없으며 컨테이너는 다른 NFS 시스템에서 읽을 수 있습니다.

  데이터를 ADLS-NFS 컨테이너에 미리 로드한 다음 HPC Cache에 스토리지 대상으로 추가하고 나중에 HPC Cache 외부에서 데이터에 액세스할 수 있습니다. 표준 Blob 컨테이너를 HPC Cache 스토리지 대상으로 사용하는 경우 데이터는 전용 형식으로 작성되며 다른 Azure HPC Cache 호환 제품에서만 액세스할 수 있습니다.

ADLS NFS 스토리지 대상을 만들려면 먼저 NFS 사용 스토리지 계정을 만들어야 합니다. [Azure HPC Cache의 필수 조건](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements)의 단계와 [NFS를 사용하여 Blob Storage 탑재](../storage/blobs/network-file-system-protocol-support-how-to.md)의 지침을 따릅니다. 캐시 및 스토리지 계정에 동일한 가상 네트워크를 사용하지 않는 경우 캐시의 VNet이 스토리지 계정의 VNet에 액세스할 수 있는지 확인합니다.

스토리지 계정을 설정한 후 스토리지 대상을 만들 때 새 컨테이너를 만들 수 있습니다.

이 구성에 대한 자세한 내용은 [Azure HPC Cache에서 탑재 Blob Storage 사용](nfs-blob-considerations.md)을 참조하세요.

ADLS-NFS 스토리지 대상을 만들려면 Azure Portal에서 **스토리지 대상 추가** 페이지를 엽니다. (추가 메서드는 개발 중입니다.)

![ADLS-NFS 대상이 정의된 스토리지 대상 추가 페이지의 스크린샷](media/add-adls-target.png)

다음 정보를 입력합니다.

* **스토리지 대상 이름** - Azure HPC Cache에서 이 스토리지 대상을 식별하는 이름을 설정합니다.
* **대상 유형** - **ADLS-NFS** 를 선택 합니다.
* **스토리지 계정** - 사용할 스토리지 계정을 선택합니다. NFS 사용 스토리지 계정이 목록에 표시되지 않으면 필수 구성 요소를 준수하는지, 캐시에서 액세스할 수 있는지 확인합니다.

  [액세스 역할 추가](#add-the-access-control-roles-to-your-account)에 설명된 대로 캐시 인스턴스에 스토리지 계정에 액세스할 수 있는 권한을 부여해야 합니다.

* **스토리지 컨테이너** - 이 대상의 NFS 사용 Blob 컨테이너를 선택하거나 **새로 만들기** 를 클릭합니다.

* **사용 모델** - 위의 [사용 모델 선택](#choose-a-usage-model)에 설명된 대로 워크플로에 따라 데이터 캐싱 프로필 중 하나를 선택합니다.

완료되면 **확인** 을 클릭하여 스토리지 대상을 추가합니다.

## <a name="view-storage-targets"></a>스토리지 대상 보기

Azure Portal 또는 Azure CLI를 사용하여 캐시에 대해 이미 정의된 스토리지 대상을 표시할 수 있습니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 캐시 인스턴스를 열고 왼쪽 사이드바의 설정 제목 아래에 있는 **스토리지 대상** 을 클릭합니다. 스토리지 대상 페이지에는 모든 기존 대상이 나열되고 대상을 추가 또는 제거하기 위한 컨트롤이 표시됩니다.

스토리지 대상의 이름을 클릭하여 세부 정보 페이지를 엽니다.

자세한 내용은 [저장소 대상 보기 및 관리](manage-storage-targets.md) 및 [저장소 대상 편집](hpc-cache-edit-storage.md) 을 참조 하세요.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC Cache용 Azure CLI 설치](./az-cli-prerequisites.md)

[az hpc-cache storage-target list](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage-target-list) 옵션을 사용하여 캐시에 대한 기존 스토리지 대상을 표시할 수 있습니다. 캐시 이름 및 리소스 그룹을 제공합니다(전역으로 설정하지 않은 경우).

```azurecli
az hpc-cache storage-target list --resource-group "scgroup" --cache-name "sc1"
```

[az hpc-cache storage-target show](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage-target-list)를 사용하여 특정 스토리지 대상의 세부 정보를 볼 수 있습니다. (이름으로 스토리지 대상을 지정합니다.)

예제:

```azurecli
$ az hpc-cache storage-target show --cache-name doc-cache0629 --name nfsd1

{
  "clfs": null,
  "id": "/subscriptions/<subscription_ID>/resourceGroups/scgroup/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "scgroup",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}
$
```

---

## <a name="next-steps"></a>다음 단계

스토리지 대상을 만든 후 다음 작업을 계속하여 캐시를 사용할 수 있도록 준비합니다.

* [집계형 네임스페이스 설정](add-namespace-paths.md)
* [Azure HPC Cache 마운트](hpc-cache-mount.md)
* [Azure Blob Storage로 데이터 마이그레이션](hpc-cache-ingest.md)

설정을 업데이트해야 하는 경우 [스토리지 대상을 편집](hpc-cache-edit-storage.md)할 수 있습니다.
