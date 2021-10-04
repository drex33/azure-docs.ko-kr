---
title: 스토리지 계정 액세스 키 변경
titleSuffix: Azure Machine Learning
description: 작업 영역에서 사용하는 Azure Storage 계정의 액세스 키를 변경하는 방법을 알아봅니다. Azure Machine Learning은 Azure Storage 계정을 사용하여 데이터 및 모델을 저장합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/19/2020
ms.openlocfilehash: 4108c905148eba0bb718fe372be87e5ec6a342d2
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129425661"
---
# <a name="regenerate-storage-account-access-keys"></a>스토리지 계정 액세스 키 다시 생성


Azure Machine Learning에서 사용하는 Azure Storage 계정의 액세스 키를 변경하는 방법을 알아봅니다. Azure Machine Learning은 스토리지 계정을 사용하여 데이터 또는 학습된 모델을 저장합니다.

보안을 위해 Azure Storage 계정에 대한 액세스 키를 변경해야 할 수 있습니다. 액세스 키를 다시 생성하는 경우 새 키를 사용하도록 Azure Machine Learning을 업데이트해야 합니다. Azure Machine Learning은 모델 스토리지 및 데이터 스토리지에 대한 스토리지 계정을 사용할 수 있습니다.

> [!IMPORTANT]
> 데이터 저장소에 등록된 자격 증명은 작업 영역과 연결된 Azure Key Vault에 저장됩니다. Key Vault에 대해 [일시 삭제](../key-vault/general/soft-delete-overview.md)를 사용하도록 설정한 경우 이 문서에서는 자격 증명을 업데이트하는 지침을 제공합니다. 데이터 저장소의 등록을 취소하고 같은 이름으로 다시 등록하려는 경우 이 작업은 실패합니다. 이 시나리오에서 일시 삭제를 사용하도록 설정하는 방법은 [기존 키 자격 증명 모음에 대해 일시 삭제 설정](../key-vault/general/soft-delete-change.md#turn-on-soft-delete-for-an-existing-key-vault)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 자세한 내용은 [작업 영역 만들기](how-to-manage-workspace.md) 문서를 참조하세요.

* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install)

* [Azure Machine Learning CLI 확장](reference-azure-machine-learning-cli.md).

> [!NOTE]
> 이 문서의 코드 조각은 Python SDK의 1.0.83 버전으로 테스트되었습니다.

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>업데이트해야 하는 사항

스토리지 계정은 Azure Machine Learning 작업 영역(로그, 모델, 스냅샷 등 저장)에서, 그리고 데이터 저장소로 사용할 수 있습니다. 작업 영역을 업데이트하는 프로세스는 단일 Azure CLI 명령이며, 스토리지 키를 업데이트한 후에 실행할 수 있습니다. 데이터 저장소를 업데이트하는 프로세스는 더욱 복잡하고, 현재 스토리지 계정을 사용하는 데이터 저장소를 검색한 다음, 다시 등록해야 합니다.

> [!IMPORTANT]
> Azure CLI를 사용하여 작업 영역을 업데이트하고, 동시에 Python을 사용하여 데이터 저장소를 업데이트합니다. 하나만 업데이트하는 것은 충분하지 않으며, 둘 다 업데이트할 때까지 오류가 발생할 수 있습니다.

데이터 저장소에서 사용하는 스토리지 계정을 검색하려면 다음 코드를 사용합니다.

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + default_ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob":
        print("Blob store - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
    if ds.datastore_type == "AzureFile":
        print("File share - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

이 코드는 Azure Storage를 사용하는 등록된 모든 데이터 저장소를 검색하고 다음 정보를 나열합니다.

* 데이터 저장소 이름: 스토리지 계정이 등록된 데이터 저장소의 이름입니다.
* 스토리지 계정 이름: Azure Storage 계정의 이름입니다.
* 컨테이너: 이 등록에 사용되는 스토리지 계정의 컨테이너입니다.

또한 각 유형의 데이터 저장소를 다시 등록하는 다른 방법이 있으므로 데이터 저장소가 Azure BLOB 또는 Azure 파일 공유용인지 여부를 나타냅니다.

액세스 키를 다시 생성하려는 스토리지 계정에 대한 항목이 있는 경우 데이터 저장소 이름, 스토리지 계정 이름 및 컨테이너 이름을 저장합니다.

## <a name="update-the-access-key"></a>액세스 키 업데이트

새 키를 사용하도록 Azure Machine Learning을 업데이트하려면 다음 단계를 사용합니다.

> [!IMPORTANT]
> 모든 단계를 수행하여 CLI를 사용하여 작업 영역을 업데이트하고 Python을 사용하여 데이터 저장소도 업데이트합니다. 둘 중 하나만 업데이트하면 둘 다 업데이트될 때까지 오류가 발생할 수 있습니다.

1. 키를 다시 생성합니다. 액세스를 키를 다시 생성하는 것에 대한 자세한 내용은 [스토리지 계정 액세스 키 관리](../storage/common/storage-account-keys-manage.md)를 참조하세요. 새 키를 저장합니다.

1. Azure Machine Learning 작업 영역은 새 키를 자동으로 동기화하고 1시간 후에 사용하기 시작합니다. 작업 영역과 새 키를 즉시 동기화하려면 다음 단계를 사용합니다.

    1. 다음 Azure CLI 명령을 사용하여 작업 영역이 포함된 Azure 구독에 로그인하려면 다음을 수행합니다.

        ```azurecli-interactive
        az login
        ```

        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

    1. 새 키를 사용하도록 작업 영역을 업데이트하려면 다음 명령을 사용합니다. `myworkspace`를 Azure Machine Learning 작업 영역 이름으로 바꾸고, `myresourcegroup`을 작업 영역을 포함한 Azure 리소스 그룹의 이름으로 바꿉니다.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

        이 명령은 작업 영역에서 사용하는 Azure Storage 계정의 새 키를 자동으로 동기화합니다.

1. SDK 또는 [Azure Machine Learning 스튜디오](https://ml.azure.com)를 통해 스토리지 계정을 사용하는 데이터 저장소를 다시 등록할 수 있습니다.
    1. **Python SDK를 통해 데이터 저장소를 다시 등록** 하려면 다음 코드에서 [업데이트해야 하는 사항](#whattoupdate) 섹션의 값과 1단계의 키를 사용합니다. 
    
        `overwrite=True`가 지정되었기 때문에 이 코드는 기존 등록을 덮어쓰고 새 키를 사용하도록 업데이트합니다.
    
        ```python
        # Re-register the blob container
        ds_blob = Datastore.register_azure_blob_container(workspace=ws,
                                                  datastore_name='your datastore name',
                                                  container_name='your container name',
                                                  account_name='your storage account name',
                                                  account_key='new storage account key',
                                                  overwrite=True)
        # Re-register file shares
        ds_file = Datastore.register_azure_file_share(workspace=ws,
                                              datastore_name='your datastore name',
                                              file_share_name='your container name',
                                              account_name='your storage account name',
                                              account_key='new storage account key',
                                              overwrite=True)
        
        ```
    
    1. **스튜디오를 통해 데이터 저장소를 다시 등록** 하려면 스튜디오의 왼쪽 창에서 **데이터 저장소** 를 선택합니다. 
        1. 업데이트하려는 데이터 저장소를 선택합니다.
        1. 왼쪽 상단에서 **자격 증명 업데이트** 단추를 선택합니다. 
        1. 1단계의 새 액세스 키를 사용하여 양식을 작성하고 **저장** 을 클릭합니다.
        
            **기본 데이터 저장소** 에 대한 자격 증명을 업데이트하는 경우 이 단계를 완료하고 2b 단계를 반복하여 새 키를 작업 영역의 기본 데이터 저장소와 다시 동기화합니다. 

## <a name="next-steps"></a>다음 단계

데이터 저장소 등록에 대한 자세한 내용은 [`Datastore`](/python/api/azureml-core/azureml.core.datastore%28class%29) 클래스 참조를 확인하세요.