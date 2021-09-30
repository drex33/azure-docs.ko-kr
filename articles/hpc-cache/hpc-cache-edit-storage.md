---
title: Azure HPC Cache 스토리지 대상 업데이트
description: Azure HPC Cache 스토리지 대상을 편집하는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 06/30/2021
ms.author: v-erkel
ms.openlocfilehash: 151186d3d42ad799707f4a352e76cd154d3443bb
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129276192"
---
# <a name="edit-storage-targets"></a>스토리지 대상 편집

Azure Portal 또는 Azure CLI를 사용하여 스토리지 대상을 수정할 수 있습니다. 예를 들어, 기존 스토리지 대상에 대한 액세스 정책, 사용 모델 및 네임스페이스 경로를 변경할 수 있습니다.

> [!TIP]
> 저장소 대상 [보기 및 관리](manage-storage-targets.md) 를 읽고 저장소 대상을 삭제 하거나 일시 중단 하거나 백 엔드 저장소에 캐시 된 데이터를 쓰도록 하는 방법을 알아보세요.

스토리지 유형에 따라 다음과 같은 스토리지 대상 값을 수정할 수 있습니다.

* Blob 스토리지 대상의 경우 네임스페이스 경로 및 액세스 정책을 변경할 수 있습니다.

* NFS 스토리지 대상의 경우 다음 값을 변경할 수 있습니다.

  * 네임스페이스 경로
  * 액세스 정책
  * 네임스페이스 경로와 연결된 스토리지 내보내기 또는 내보내기 하위 디렉터리
  * 사용 모델

* ADLS-NFS 스토리지 대상의 경우 네임스페이스 경로, 액세스 정책 및 사용 모델을 변경할 수 있습니다.

스토리지 대상의 이름, 유형 또는 백 엔드 스토리지 시스템은 편집할 수 없습니다. 이러한 속성을 변경해야 하는 경우 스토리지 대상을 삭제하고 새 값으로 바꿉니다.

[Azure HPC Cache 관리 비디오](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)는 Azure Portal에서 스토리지 대상을 편집하는 방법을 보여 줍니다.

## <a name="change-a-blob-storage-targets-namespace-path-or-access-policy"></a>Blob 스토리지 대상의 네임스페이스 경로 또는 액세스 정책 변경

네임스페이스 경로는 클라이언트가 이 스토리지 대상을 탑재하는 데 사용하는 경로입니다. 자세한 내용은 [집계형 네임스페이스 계획](hpc-cache-namespace.md) 및 [집계형 네임 스페이스 설정](add-namespace-paths.md)을 읽어보세요.

Azure Portal 또는 Azure CLI를 사용하여 네임스페이스 경로나 액세스 정책을 변경합니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure HPC Cache의 **네임스페이스** 페이지를 사용하여 네임스페이스 경로 또는 클라이언트 액세스 정책을 업데이트합니다. 네임스페이스 페이지는 [집계형 네임스페이스 설정](add-namespace-paths.md)문서에 좀 더 자세히 설명되어 있습니다.

1. 변경할 경로를 클릭합니다.
   ![네임스페이스 경로 열(왼쪽의 첫 번째 열)에 있는 항목 위에 커서가 있는 네임스페이스 페이지의 스크린샷. 이름은 하이퍼링크로 형식이 지정되며 커서는 클릭할 수 있음을 나타냅니다.](media/edit-select-namespace.png)

1. 편집 창을 사용하여 새 가상 경로를 입력하거나 액세스 정책을 업데이트합니다.

   ![Blob 네임스페이스 경로를 클릭한 이후의 네임스페이스 페이지 스크린샷 - 오른쪽 창에 편집 필드가 표시됩니다.](media/update-namespace-blob.png)

변경 후에는 **확인** 을 클릭하여 스토리지 대상을 업데이트하거나 **취소** 를 클릭하여 변경 내용을 취소합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC Cache용 Azure CLI 설치](./az-cli-prerequisites.md).

Azure CLI를 사용하여 Blob 스토리지 대상의 네임스페이스를 변경하려면 [az hpc-cache blob-storage-target update](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_update) 명령을 사용합니다. `--virtual-namespace-path` 값만 변경할 수 있습니다.

  ```azurecli
  az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --virtual-namespace-path "/new-path"
  ```

---

## <a name="update-an-nfs-storage-target"></a>NFS 스토리지 대상 업데이트

NFS 스토리지 대상의 경우 가상 네임스페이스 경로를 변경 또는 추가하고, 네임스페이스 경로가 가리키는 NFS 내보내기 또는 하위 디렉터리 값을 변경하고, 사용 모델을 변경할 수 있습니다.

일부 유형의 사용자 지정 DNS 설정으로 캐시의 스토리지 대상에도 해당 IP 주소를 새로 고치는 컨트롤이 있습니다. 이러한 종류의 구성은 드물게 발생 합니다. [저장소 대상 보기 및 관리](manage-storage-targets.md#update-ip-address-custom-dns-configurations-only)에서 DNS 설정을 새로 고치는 방법에 대해 알아봅니다.

자세한 내용은 다음과 같습니다.

* [집계형 네임스페이스 값 변경](#change-aggregated-namespace-values)(가상 네임스페이스 경로, 액세스 정책, 내보내기 및 하위 디렉터리 내보내기)
* [사용 모델 변경](#change-the-usage-model)

### <a name="change-aggregated-namespace-values"></a>집계형 네임스페이스 값 변경

Azure Portal 또는 Azure CLI를 사용하여 클라이언트 연결 네임스페이스 경로, 스토리지 내보내기 및 내보내기 하위 디렉터리(사용되는 경우)를 변경할 수 있습니다. 액세스 정책을 변경해야 하는 경우 Azure Portal을 사용합니다.

하나의 스토리지 대상에서 유효한 경로를 여러 개 만드는 방법에 대한 미리 알림이 필요한 경우 [NFS 네임스페이스 경로 추가](add-namespace-paths.md#nfs-namespace-paths)의 지침을 참조하세요.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure HPC Cache의 **네임스페이스** 페이지를 사용하여 클라이언트 액세스 정책을 포함한 네임스페이스 값을 업데이트합니다. 이 페이지는 [집계형 네임스페이스 설정](add-namespace-paths.md) 문서에 자세히 설명되어 있습니다.

![NFS 업데이트 페이지가 오른쪽에 열려 있는 포털 네임스페이스 페이지 스크린샷](media/update-namespace-nfs.png)

1. 변경할 경로의 이름을 클릭합니다.
1. 편집 창을 사용하여 새 가상 경로, 내보내기 또는 하위 디렉터리 값을 입력하거나 다른 액세스 정책을 선택할 수 있습니다.
1. 변경 후에는 **확인** 을 클릭하여 스토리지 대상을 업데이트하거나 **취소** 를 클릭하여 변경 내용을 취소합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC Cache용 Azure CLI 설치](./az-cli-prerequisites.md).

[az hpc-cache nfs-storage-target update](/cli/azure/hpc-cache/nfs-storage-target) 명령의 ``--junction`` 옵션을 사용하여 네임스페이스 경로, NFS 내보내기 또는 내보내기 하위 디렉터리를 변경할 수 있습니다.

``--junction`` 매개 변수는 다음 값을 사용합니다.

* ``namespace-path`` - 클라이언트 연결 가상 파일 경로
* ``nfs-export`` - 클라이언트 연결 경로와 연결할 스토리지 시스템 내보내기
* ``target-path``(선택 사항) - 필요한 경우 내보내기의 하위 디렉터리

예: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

``--junction`` 문의 각 경로에 대해 세 가지 값을 모두 제공해야 합니다. 변경하지 않으려는 값의 경우 기존 값을 사용합니다.

캐시 이름, 스토리지 대상 이름 및 리소스 그룹도 모든 업데이트 명령에 필요합니다.

예제 명령:

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
  --name st-name --resource-group doc-rg0619 \
  --junction namespace-path="/new-path" nfs-export="/my-export" target-path="my-subdirectory"
```

---

### <a name="change-the-usage-model"></a>사용 모델 변경

사용 모델은 캐시가 데이터를 보존하는 방법에 영향을 줍니다. 자세한 내용은 [캐시 사용 모델 이해](cache-usage-models.md)를 읽어보세요.

> [!NOTE]
> **읽기 작업량이 적은 읽기, 자주 발생하지 않는 쓰기** 와 기타 사용 모델 간에 변경할 수 없습니다. 자세한 내용은 [캐시 사용 모델 이해](cache-usage-models.md#change-usage-models)를 참조하세요.

NFS 스토리지 대상에 대한 사용 모델을 변경하려면 다음 방법 중 하나를 사용합니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 **스토리지 대상** 페이지를 엽니다. 목록에서 스토리지 대상의 이름을 클릭하여 편집 페이지를 엽니다.

![NFS 스토리지 대상에 대한 편집 페이지의 스크린샷](media/edit-storage-nfs.png)

드롭다운 선택기를 사용하여 새 사용 모델을 선택합니다. **확인** 을 클릭하여 스토리지 대상을 업데이트하거나 **취소** 를 클릭하여 변경 내용을 취소합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure HPC Cache용 Azure CLI 설치](./az-cli-prerequisites.md).

[az hpc-cache-storage-target update](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_update) 명령을 사용합니다.

update 명령은 NFS 스토리지 대상을 추가하는 데 사용하는 명령과 거의 동일합니다. 자세한 내용과 예제는 [NFS 스토리지 대상 만들기](hpc-cache-add-storage.md#create-an-nfs-storage-target)를 참조하세요.

사용 모델을 변경하려면 ``--nfs3-usage-model`` 옵션을 업데이트합니다. 예: ``--nfs3-usage-model WRITE_WORKLOAD_15``

캐시 이름, 스토리지 대상 이름 및 리소스 그룹 값도 필요합니다.

사용 모델의 이름을 확인하려는 경우에는 [az hpc-cache usage-model list](/cli/azure/hpc-cache/usage-model#az_hpc_cache_usage-model-list) 명령을 사용합니다.

캐시가 중지되었거나 정상 상태가 아니면 캐시가 정상 상태가 된 후 업데이트가 적용됩니다.

---

## <a name="update-an-adls-nfs-storage-target"></a>ADLS-NFS 스토리지 대상 업데이트

NFS 대상과 마찬가지로 ADLS-NFS 스토리지 대상에 대한 네임스페이스 경로 및 사용 모델을 변경할 수 있습니다.

### <a name="change-an-adls-nfs-namespace-path"></a>ADLS-NFS 네임스페이스 경로 변경

Azure HPC Cache의 **네임스페이스** 페이지를 사용하여 네임스페이스 값을 업데이트합니다. 이 페이지는 [집계형 네임스페이스 설정](add-namespace-paths.md) 문서에 자세히 설명되어 있습니다.

![ADS-NFS 업데이트 페이지가 오른쪽에 열려 있는 포털 네임스페이스 페이지 스크린샷](media/update-namespace-adls.png)

1. 변경할 경로의 이름을 클릭합니다.
1. 편집 창을 사용하여 새 가상 경로를 입력하거나 액세스 정책을 업데이트합니다.
1. 변경 후에는 **확인** 을 클릭하여 스토리지 대상을 업데이트하거나 **취소** 를 클릭하여 변경 내용을 취소합니다.

### <a name="change-adls-nfs-usage-models"></a>ADLS-NFS 사용 모델 변경

ADLS-NFS 사용 모델에 대한 구성은 NFS 사용 모델 선택과 동일합니다. 위의 NFS 섹션에서 [사용 모델 변경](#change-the-usage-model)의 포털 지침을 읽습니다. ADLS-NFS 스토리지 대상을 업데이트하기 위한 추가 도구는 개발 중입니다.

## <a name="next-steps"></a>다음 단계

* 개별 [스토리지 대상을](manage-storage-targets.md) 중지, 삭제 및 플러시하는 자세한 내용은 스토리지 대상 보기 및 관리를 읽어보십시오.
* 스토리지 대상 옵션에 대한 자세한 내용은 [스토리지 대상 추가](hpc-cache-add-storage.md)를 참조하세요.
* 가상 경로 사용에 대한 자세한 팁은 [집계형 네임스페이스 계획](hpc-cache-namespace.md)을 참조하세요.
