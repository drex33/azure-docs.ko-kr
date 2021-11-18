---
title: Azure CLI와 함께 Azure 비정상 스튜디오를 사용 하 여 서비스 직접 오류를 사용 하는 실험 만들기
description: Azure CLI에서 서비스 직접 오류를 사용 하는 실험 만들기
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: how-to
ms.date: 11/10/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: e6f1f215a96b6f651e344087c98a3a7d9be278db
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132718529"
---
# <a name="create-a-chaos-experiment-that-uses-a-service-direct-fault-with-the-azure-cli"></a>Azure CLI에서 서비스 직접 오류를 사용 하는 비정상 실험 만들기

비정상 실험을 사용 하 여 제어 되는 환경에서 오류를 발생 시켜 응용 프로그램이 오류에 탄력적으로 대처할 수 있는지 확인할 수 있습니다. 이 가이드에서는 비정상 실험 및 Azure 비정상 스튜디오를 사용 하 여 다중 읽기, 단일 쓰기 Azure Comos DB 장애 조치 (failover)를 발생 시킵니다. 이 실험을 실행 하면 장애 조치 이벤트가 발생할 때 데이터 손실을 방어할 수 있습니다.

이러한 동일한 단계를 사용 하 여 서비스 직접 오류에 대 한 실험을 설정 하 고 실행할 수 있습니다. 비정상 상태 에이전트를 설치 해야 하는 에이전트 기반 오류와는 달리 **서비스 직접** 오류는 Azure 리소스에 대해 직접 실행 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- Azure Cosmos DB 계정. Azure Cosmos DB 계정이 없는 경우 [다음 단계를 수행 하](../cosmos-db/sql/create-cosmosdb-resources-portal.md)여 계정을 만들 수 있습니다.
- Azure Cosmos DB 계정에 대 한 읽기 및 쓰기 지역 설정이 하나 이상 있습니다.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요** 를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/bash](https://shell.azure.com/bash)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 열 수도 있습니다. **복사** 를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, **Enter** 를 선택하여 실행합니다.

이 자습서에서 CLI를 로컬로 설치하여 사용하려면 Azure CLI 버전 2.0.30 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

> [!NOTE]
> 이러한 지침은 Azure Cloud Shell에서 Bash 터미널을 사용 합니다. CLI를 로컬로 실행 하거나 PowerShell 터미널에서 실행 하는 경우에 설명 된 대로 일부 명령은 작동 하지 않을 수 있습니다.

## <a name="enable-chaos-studio-on-your-azure-cosmos-db-account"></a>Azure Cosmos DB 계정에서 비정상 스튜디오 사용

비정상 스튜디오는 리소스를 비정상 스튜디오로 등록 하지 않는 한 리소스에 대 한 오류를 주입할 수 없습니다. 리소스에 대 한 [대상 및 기능](chaos-studio-targets-capabilities.md) 을 만들어 비정상 스튜디오에 리소스를 등록 합니다. Azure Cosmos DB 계정에는 대상 유형 (서비스 직접)과 기능 (장애 조치 (failover))이 하나씩만 있고 다른 리소스에는 최대 두 개의 대상 유형인

1. 온 보 딩 하는 리소스의 리소스 ID로 대체 하 여 대상을 만들고 `$RESOURCE_ID` , 등록 하는 `$TARGET_TYPE` [대상 유형을](chaos-studio-fault-providers.md)사용 합니다.

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/$TARGET_TYPE?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

    예를 들어 가상 머신을 서비스 직접 대상으로 온 보 딩 하는 경우:

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

2. 온 보 딩 하는 리소스의 리소스 ID로 대체 하 여 대상에 대 한 기능을 만들고 `$RESOURCE_ID` , 등록 `$TARGET_TYPE` 하는 [대상 유형과](chaos-studio-fault-providers.md) 사용 중인 `$CAPABILITY` [오류 기능의 이름](chaos-studio-fault-library.md)으로 바꿉니다.
    
    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/$TARGET_TYPE/capabilities/$CAPABILITY?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

    예를 들어 가상 머신 종료 기능을 사용 하도록 설정 하는 경우:

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine/capabilities/shutdown-1.0?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

이제 Azure Cosmos DB 계정을 비정상 스튜디오로 등록 했습니다.

## <a name="create-an-experiment"></a>실험 만들기
이제 Azure Cosmos DB 계정을 사용 하 여 등록 실험을 만들 수 있습니다. 비정상 실험은 순서 대로 실행 되는 단계, 병렬로 실행 되는 분기, 병렬로 실행 되는 대상 리소스에 대해 수행 하려는 작업을 정의 합니다.

1. 아래 JSON 샘플부터 실험 JSON을 작성 합니다. [Create 실험 API](/rest/api/chaosstudio/experiments/create-or-update) 및 [오류 라이브러리](chaos-studio-fault-library.md) 를 사용 하 여 실행 하려는 실험에 해당 하는 JSON을 수정 합니다.

    ```json
    {
      "location": "eastus",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "steps": [
          {
            "name": "Step1",
            "branches": [
              {
                "name": "Branch1",
                "actions": [
                  {
                    "type": "continuous",
                    "selectorId": "Selector1",
                    "duration": "PT10M",
                    "parameters": [
                      {
                        "key": "readRegion",
                        "value": "East US 2"
                      }
                    ],
                    "name": "urn:csci:microsoft:cosmosDB:failover/1.0"
                  }
                ]
              }
            ]
          }
        ],
        "selectors": [
          {
            "id": "Selector1",
            "type": "List",
            "targets": [
              {
                "type": "ChaosTarget",
                "id": "/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/chaosstudiodemo/providers/Microsoft.DocumentDB/databaseAccounts/myDB/providers/Microsoft.Chaos/targets/Microsoft-CosmosDB"
              }
            ]
          }
        ]
      }
    }
    ```
    
2. `$SUBSCRIPTION_ID`, `$RESOURCE_GROUP` 및를 `$EXPERIMENT_NAME` 실험의 속성으로 바꿔 Azure CLI를 사용 하 여 실험을 만듭니다. JSON 파일 이름과 함께 실험 JSON 및 업데이트를 저장 하 고 업로드 했는지 확인 `experiment.json` 합니다.

    ```azurecli-interactive
    az rest --method put --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME?api-version=2021-09-15-preview --body @experiment.json
    ```

    각 실험은 해당 하는 시스템 할당 관리 id를 만듭니다. `principalId`다음 단계에 대 한 응답에서이 id의를 확인 합니다.

## <a name="give-experiment-permission-to-your-azure-cosmos-db-account"></a>Azure Cosmos DB 계정에 실험 권한 제공
비정상 실험을 만들 때 비정상 스튜디오는 대상 리소스에 대해 오류를 실행 하는 시스템 할당 관리 id를 만듭니다. 실험을 성공적으로 실행 하려면이 id에 대상 리소스에 대 [한 적절 한 권한을](chaos-studio-fault-providers.md) 부여 해야 합니다.

아래의 명령을 사용 하 여 리소스에 대 한 실험 액세스를 제공 하 고,을 `$EXPERIMENT_PRINCIPAL_ID` 이전 단계의 principalid로 바꾸고,을 `$RESOURCE_ID` 대상 리소스의 리소스 id (이 경우에는 Cosmos DB 인스턴스 리소스 id)로 바꿉니다. [해당 리소스 종류에 대 한 적절 한 기본 제공 역할로](chaos-studio-fault-providers.md)역할을 변경 합니다. 실험에서 대상으로 지정 된 각 리소스에 대해이 명령을 실행 합니다. 

```azurecli-interactive
az role assignment create --role "Cosmos DB Operator" --assignee-object-id $EXPERIMENT_PRINCIPAL_ID --scope $RESOURCE_ID
```

## <a name="run-your-experiment"></a>실험 실행
이제 실험을 실행할 준비가 되었습니다. 영향을 확인 하려면 Azure Cosmos DB 계정 개요를 열고 별도의 브라우저 탭에서 **전역적으로 데이터를 복제** 하는 것이 좋습니다. 실험 중에 주기적으로 새로 고치면 지역 교환이 표시 됩니다.

1. Azure CLI를 사용 하 여 실험을 시작 하 고, `$SUBSCRIPTION_ID` `$RESOURCE_GROUP` 및를 `$EXPERIMENT_NAME` 실험의 속성으로 바꿉니다.

    ```azurecli-interactive
    az rest --method post --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME/start?api-version=2021-09-15-preview
    ```

2. 응답에는 실험을 실행할 때 실험 상태를 쿼리 하는 데 사용할 수 있는 상태 URL이 포함 되어 있습니다.

## <a name="next-steps"></a>다음 단계
Azure Cosmos DB service direct 실험을 실행 했으므로 다음 작업을 수행할 수 있습니다.
- [에이전트 기반 오류를 사용 하는 실험 만들기](chaos-studio-tutorial-agent-based-portal.md)
- [실험 관리](chaos-studio-run-experiment.md)
