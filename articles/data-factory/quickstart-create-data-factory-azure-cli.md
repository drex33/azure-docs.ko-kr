---
title: '빠른 시작: Azure CLI를 사용하여 Azure Data Factory 만들기'
description: 이 빠른 시작에서는 연결된 서비스, 데이터 세트 및 파이프라인을 비롯한 Azure Data Factory를 만듭니다. 파이프라인을 실행하여 파일 복사 작업을 수행할 수 있습니다.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: data-movement
ms.topic: quickstart
ms.date: 10/14/2021
ms.custom: template-quickstart, devx-track-azurecli
ms.openlocfilehash: 44c76955faf4c0d96c0e6ef502d00b61ff4c99ea
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130232946"
---
# <a name="quickstart-create-an-azure-data-factory-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Azure Data Factory 만들기

이 빠른 시작에서는 Azure CLI를 사용하여 Azure Data Factory를 만드는 방법을 설명합니다. 이 데이터 팩터리에서 만든 파이프라인은 Azure Blob Storage 내 한 폴더에서 다른 폴더로 데이터를 복사합니다. Azure Data Factory를 사용하여 데이터를 변환하는 방법에 대한 자세한 내용은 [Azure Data Factory에서 데이터 변환](transform-data.md)을 참조하세요.

Azure Data Factory 서비스 소개는 [Azure Data Factory 소개](introduction.md)를 참조하세요.

Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

[!INCLUDE [azure-cli-prepare-your-environment](../../includes/azure-cli-prepare-your-environment.md)]

> [!NOTE]
> 데이터 팩터리 인스턴스를 만들려면 Azure에 로그인하는 데 사용할 사용자 계정이 참여자 또는 소유자 역할의 구성원이거나, Azure 구독의 관리자여야 합니다. 자세한 내용은 [Azure 역할](quickstart-create-data-factory-powershell.md#azure-roles)을 참조하세요.

## <a name="prepare-a-container-and-test-file"></a>컨테이너 및 테스트 파일 준비

이 빠른 시작에서는 파일이 있는 컨테이너를 포함하는 Azure Storage 계정을 사용합니다.

1. `ADFQuickStartRG`라는 리소스 그룹을 만들려면 [az group create](/cli/azure/group#az_group_create) 명령을 사용합니다.

   ```azurecli
   az group create --name ADFQuickStartRG --location eastus
   ```

1. [az storage account create](/cli/azure/storage/container#az_storage_container_create) 명령을 사용하여 스토리지 계정을 만듭니다.

   ```azurecli
   az storage account create --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --location eastus
   ```

1. [az storage container create](/cli/azure/storage/container#az_storage_container_create) 명령을 사용하여 `adftutorial`이라는 컨테이너를 만듭니다.

   ```azurecli
   az storage container create --resource-group ADFQuickStartRG --name adftutorial \
       --account-name adfquickstartstorage --auth-mode key
   ```

1. 로컬 디렉터리에서 업로드할 `emp.txt`라는 파일을 만듭니다. Azure Cloud Shell에서 작업하는 경우 `echo $PWD` Bash 명령을 사용하여 현재 작업 디렉터리를 찾을 수 있습니다. `cat`과 같은 표준 Bash 명령을 사용하여 파일을 만들 수 있습니다.

   ```console
   cat > emp.txt
   This is text.
   ```

   **Ctrl + D** 를 사용하여 새 파일을 저장합니다.

1. 새 파일을 Azure Storage 컨테이너에 업로드하려면 [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) 명령을 사용합니다.

   ```azurecli
   az storage blob upload --account-name adfquickstartstorage --name input/emp.txt \
       --container-name adftutorial --file emp.txt --auth-mode key
   ```

   이 명령은 `input`이라는 새 폴더에 업로드합니다.

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

Azure Data Factory를 만들려면 [az datafactory create](/cli/azure/datafactory#az_datafactory_create) 명령을 실행합니다.

```azurecli
az datafactory create --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

> [!IMPORTANT]
> `ADFTutorialFactory`를 ADFTutorialFactorySP1127과 같은 전역적으로 고유한 데이터 팩터리 이름으로 바꿉니다.

[az datafactory show](/cli/azure/datafactory#az_datafactory_factory_show) 명령을 사용하여 만든 데이터 팩터리를 볼 수 있습니다.

```azurecli
az datafactory show --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

## <a name="create-a-linked-service-and-datasets"></a>연결된 서비스 및 데이터 세트 만들기

다음으로, 연결된 서비스 및 두 개의 데이터 세트를 만듭니다.

1. [az storage account show-connection-string](/cli/azure/datafactory#az_datafactory_factory_show) 명령을 사용하여 스토리지 계정에 대한 연결 문자열을 가져옵니다.

   ```azurecli
   az storage account show-connection-string --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --key primary
   ```

1. 작업 디렉터리에서 이 콘텐츠가 포함된 JSON 파일을 만듭니다. 여기에는 이전 단계의 고유한 연결 문자열이 포함됩니다. 파일 이름을 `AzureStorageLinkedService.json`으로 지정합니다.

    ```json
    {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net"
        }
    }
    ```

1. [az datafactory linked-service create](/cli/azure/datafactory/linked-service#az_datafactory_linked_service_create) 명령을 사용하여 `AzureStorageLinkedService`라는 연결된 서비스를 만듭니다.

   ```azurecli
   az datafactory linked-service create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --linked-service-name AzureStorageLinkedService \
       --properties @AzureStorageLinkedService.json
   ```

1. 작업 디렉터리에서 이 콘텐츠가 포함된 `InputDataset.json`이라는 JSON 파일을 만듭니다.

    ```json
    {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "Binary",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "emp.txt",
                "folderPath": "input",
                "container": "adftutorial"
            }
        }
    }
    ```

1. [az datafactory dataset create](/cli/azure/datafactory/dataset#az_datafactory_dataset_create) 명령을 사용하여 `InputDataset`라는 입력 데이터 세트를 만듭니다.

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name InputDataset --factory-name ADFTutorialFactory \
       --properties @InputDataset.json
   ```

1. 작업 디렉터리에서 이 콘텐츠가 포함된 `OutputDataset.json`이라는 JSON 파일을 만듭니다.

    ```json
    {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "Binary",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "folderPath": "output",
                "container": "adftutorial"
            }
        }
    }
    ```

1. [az datafactory dataset create](/cli/azure/datafactory/dataset#az_datafactory_dataset_create) 명령을 사용하여 `OutputDataset`라는 출력 데이터 세트를 만듭니다.

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name OutputDataset --factory-name ADFTutorialFactory \
       --properties @OutputDataset.json
   ```

## <a name="create-and-run-the-pipeline"></a>파이프라인 만들기 및 실행

마지막으로, 파이프라인을 만들고 실행합니다.

1. 작업 디렉터리에서 이 콘텐츠가 포함된 `Adfv2QuickStartPipeline.json`이라는 JSON 파일을 만듭니다.

    ```json
    {
        "name": "Adfv2QuickStartPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToBlob",
                    "type": "Copy",
                    "dependsOn": [],
                    "policy": {
                        "timeout": "7.00:00:00",
                        "retry": 0,
                        "retryIntervalInSeconds": 30,
                        "secureOutput": false,
                        "secureInput": false
                    },
                    "userProperties": [],
                    "typeProperties": {
                        "source": {
                            "type": "BinarySource",
                            "storeSettings": {
                                "type": "AzureBlobStorageReadSettings",
                                "recursive": true
                            }
                        },
                        "sink": {
                            "type": "BinarySink",
                            "storeSettings": {
                                "type": "AzureBlobStorageWriteSettings"
                            }
                        },
                        "enableStaging": false
                    },
                    "inputs": [
                        {
                            "referenceName": "InputDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "OutputDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ],
            "annotations": []
        }
    }
    ```

1. [az datafactory pipeline create](/cli/azure/datafactory/pipeline#az_datafactory_pipeline_create) 명령을 사용하여 `Adfv2QuickStartPipeline`이라는 파이프라인을 만듭니다.

   ```azurecli
   az datafactory pipeline create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --name Adfv2QuickStartPipeline \
       --pipeline @Adfv2QuickStartPipeline.json
   ```

1. [az datafactory pipeline create-run](/cli/azure/datafactory/pipeline#az_datafactory_pipeline_create_run) 명령을 사용하여 파이프라인을 실행합니다.

   ```azurecli
   az datafactory pipeline create-run --resource-group ADFQuickStartRG \
       --name Adfv2QuickStartPipeline --factory-name ADFTutorialFactory
   ```

   이 명령은 실행 ID를 반환합니다. 다음 단계에서 사용할 수 있도록 복사합니다.

1. [az datafactory pipeline-run show](/cli/azure/datafactory/pipeline-run#az_datafactory_pipeline_run_show) 명령을 사용하여 파이프라인 실행이 성공했는지 확인합니다.

   ```azurecli
   az datafactory pipeline-run show --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --run-id 00000000-0000-0000-0000-000000000000
   ```

[Azure Portal](https://portal.azure.com/)을 사용하여 파이프라인이 예상대로 실행되었는지 확인할 수도 있습니다. 자세한 내용은 [배포된 리소스 검토](quickstart-create-data-factory-powershell.md#review-deployed-resources)를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작의 모든 리소스는 동일한 리소스 그룹의 일부입니다. 리소스 그룹을 모두 제거하려면 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용합니다.

```azurecli
az group delete --name ADFQuickStartRG
```

다른 항목에 대해 이 리소스 그룹을 사용하는 경우 대신 개별 리소스를 삭제합니다. 예를 들어 연결된 서비스를 제거하려면 [az datafactory linked-service delete](/cli/azure/datafactory/linked-service#az_datafactory_linked_service_delete) 명령을 사용합니다.

이 빠른 시작에서는 다음 JSON 파일을 만들었습니다.

- AzureStorageLinkedService.json
- InputDataset.json
- OutputDataset.json
- Adfv2QuickStartPipeline.json

표준 Bash 명령을 사용하여 삭제합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Data Factory의 파이프라인 및 작업](concepts-pipelines-activities.md)
- [Azure Data Factory의 연결된 서비스](concepts-linked-services.md)
- [Azure 데이터 팩터리의 데이터 세트](concepts-datasets-linked-services.md)
- [Azure Data Factory의 데이터 변환](transform-data.md)
