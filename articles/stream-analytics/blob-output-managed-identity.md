---
title: 관리 ID Azure Stream Analytics로 Blob 출력 인증
description: 이 문서에서는 관리 ID를 사용하여 Azure Blob storage 출력에 대해 Azure Stream Analytics 작업을 인증하는 방법을 설명합니다.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 07/07/2021
ms.openlocfilehash: 708871f620614f893a641f20098f6ed58af464f5
ms.sourcegitcommit: 0fd913b67ba3535b5085ba38831badc5a9e3b48f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113486106"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage"></a>관리 ID를 사용하여 Azure Blob Storage에 Azure Stream Analytics 작업 인증

Azure Blob Storage에 출력하기 위한 [관리 ID 인증](../active-directory/managed-identities-azure-resources/overview.md)을 사용하면 Stream Analytics 작업은 연결 문자열을 사용하는 대신 스토리지 계정에 직접 액세스할 수 있습니다. 이 기능을 사용하면 보안이 개선되는 것 이외에도 Azure 내의 VNET(가상 네트워크)에서 스토리지 계정에 데이터를 쓰는 것이 가능해집니다.

이 문서에서는 Azure Portal 및 Azure Resource Manager 배포를 통해 Stream Analytics 작업의 Blob 출력에 대해 관리 ID를 사용하도록 설정하는 방법을 표시합니다.

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>Azure Portal을 사용하여 Stream Analytics 작업 만들기

먼저 Azure Stream Analytics 작업의 관리 ID를 만듭니다.  

1. Azure Portal에서 Azure Stream Analytics 작업을 엽니다.  

2. 왼쪽 탐색 메뉴에서  *구성* 아래에 있는  **관리 ID** 를 선택합니다. 그런 다음,  **시스템이 할당한 관리 ID 사용** 옆에 있는 확인란을 선택하고  **저장** 을 선택합니다.

   :::image type="content" source="media/event-hubs-managed-identity/system-assigned-managed-identity.png" alt-text="시스템 할당 관리 ID":::  

3. Azure Active Directory에서 Stream Analytics 작업의 ID에 대한 서비스 주체가 생성됩니다. 새로 생성된 ID의 수명 주기는 Azure에서 관리합니다. Stream Analytics 작업을 삭제하면 연결된 ID(즉, 서비스 주체)는 Azure에서 자동으로 삭제합니다.  

   구성을 저장하면 서비스 주체의 OID(개체 ID)가 아래와 같이 보안 주체 ID로 나열됩니다.  

   :::image type="content" source="media/event-hubs-managed-identity/principal-id.png" alt-text="보안 주체 ID":::

   서비스 주체에는 Stream Analytics 작업과 동일한 이름이 사용됩니다. 예를 들어, 작업 이름이  `MyASAJob`이면 서비스 주체 이름도  `MyASAJob`입니다. 


## <a name="azure-resource-manager-deployment"></a>Azure Resource Manager 배포

Azure Resource Manager를 사용 하면 Stream Analytics 작업 배포를 완벽하게 자동화할 수 있습니다. Azure PowerShell 또는 [Azure CLI](/cli/azure/)를 사용하여 Resource Manager 템플릿을 배포할 수 있습니다. 아래 예제에서는 Azure CLI를 사용합니다.


1. Resource Manager 템플릿의 리소스 섹션에 다음 속성을 포함하여 관리 ID를 통해 **Microsoft.StreamAnalytics/streamingjobs** 리소스를 만들 수 있습니다.

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   이 속성을 통해 Azure Resource Manager에서 Stream Analytics 작업에 대한 ID를 만들고 관리합니다. 아래는 관리 ID를 사용하는 Stream Analytics 작업과 관리 ID를 사용하는 Blob 출력 싱크를 배포하는 예제 Resource Manager 템플릿입니다.

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "MyStreamingJob",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "serialization": {
                                    "type": "JSON",
                                    "properties": {
                                        "encoding": "UTF8"
                                    }
                                },
                                "datasource":{
                                    "type":"Microsoft.Storage/Blob",
                                    "properties":{
                                        "storageAccounts": [
                                            { "accountName": "MyStorageAccount" }
                                        ],
                                        "container": "test",
                                        "pathPattern": "segment1/{date}/segment2/{time}",
                                        "dateFormat": "yyyy/MM/dd",
                                        "timeFormat": "HH",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    위의 작업은 아래 Azure CLI 명령을 사용하여 리소스 그룹 **ExampleGroup** 에 배포할 수 있습니다.

    ```azurecli
    az deployment group create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. 작업을 만든 후 Azure Resource Manager를 사용하여 작업의 전체 정의를 검색할 수 있습니다.

    ```azurecli
    az resource show --ids /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/StreamingJobs/{RESOURCE_NAME}
    ```

    위의 명령은 아래와 같은 응답을 반환합니다.

    ```json
    {
        "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/streamingjobs/{RESOURCE_NAME}",
        "identity": {
            "principalId": "{PRINCIPAL_ID}",
            "tenantId": "{TENANT_ID}",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "{RESOURCE_NAME}",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "{JOB_ID}",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "{RESOURCE_GROUP}",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

   작업 정의에서 Azure Active Directory 내에서 작업의 관리 ID를 식별하고 다음 단계에서 스토리지 계정에 대한 Stream Analytics 작업 액세스 권한을 부여하는 데 사용되는 **principalId** 를 기록해 둡니다.

3. 작업이 만들어졌으므로 이 문서의 [스토리지 계정에 대한 Stream Analytics 작업 액세스 권한 부여](#give-the-stream-analytics-job-access-to-your-storage-account) 섹션을 참조하세요.


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>스토리지 계정에 Stream Analytics 작업 액세스 권한 부여

Stream Analytics 작업에 부여할 수 있는 액세스 수준은 두 가지입니다.

1. **컨테이너 수준 액세스:** 이 옵션은 특정 기존 컨테이너에 대한 작업 액세스 권한을 제공합니다.
2. **계정 수준 액세스:** 이 옵션은 새 컨테이너를 만드는 기능을 포함하여 스토리지 계정에 대한 작업 일반 액세스 권한을 제공합니다.

**컨테이너 수준 액세스** 옵션은 작업에 필요한 최소 액세스 수준을 부여하기 때문에 사용자를 대신하여 컨테이너를 만드는 작업이 필요하지 않을 경우 이 옵션을 선택해야 합니다. Azure Portal 및 명령줄에 대한 두 옵션이 아래에 설명되어 있습니다.

> [!NOTE]
> 글로벌 복제 또는 캐싱 대기 시간으로 인해 권한이 취소되거나 부여될 때 지연이 발생할 수 있습니다. 변경 내용은 8분 내에 반영되어야 합니다.

### <a name="grant-access-via-the-azure-portal"></a>Azure Portal를 통해 액세스 권한 부여

#### <a name="container-level-access"></a>컨테이너 수준 액세스

1. 스토리지 계정 내 컨테이너의 구성 창으로 이동합니다.

2. 왼쪽에서 **Access Control(IAM)** 을 선택합니다.

3. "역할 할당 추가" 섹션에서 **추가** 를 클릭합니다.

4. 역할 할당 창:

    1. **역할** 을 "Storage Blob 데이터 기여자"로 설정합니다.
    2. 드롭다운으로 **액세스 할당** 이 "Azure AD 사용자, 그룹 또는 서비스 보안 주체"로 설정되어 있는지 확인합니다.
    3. 검색 필드에 Stream Analytics 작업 이름을 입력합니다.
    4. Stream Analytics 작업을 선택하 고 **저장** 을 클릭합니다.

   ![컨테이너 액세스 권한 부여](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>계정 수준 액세스

1. 스토리지 계정으로 이동합니다.

2. 왼쪽에서 **Access Control(IAM)** 을 선택합니다.

3. "역할 할당 추가" 섹션에서 **추가** 를 클릭합니다.

4. 역할 할당 창:

    1. **역할** 을 "Storage Blob 데이터 기여자"로 설정합니다.
    2. 드롭다운으로 **액세스 할당** 이 "Azure AD 사용자, 그룹 또는 서비스 보안 주체"로 설정되어 있는지 확인합니다.
    3. 검색 필드에 Stream Analytics 작업 이름을 입력합니다.
    4. Stream Analytics 작업을 선택하 고 **저장** 을 클릭합니다.

   ![계정 액세스 권한 부여](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>명령줄을 통해 액세스 권한 부여

#### <a name="container-level-access"></a>컨테이너 수준 액세스

특정 컨테이너에 대한 액세스 권한을 부여하려면 Azure CLI를 사용하여 다음 명령을 실행합니다.

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>계정 수준 액세스

전체 계정에 대한 액세스 권한을 부여하려면 Azure CLI를 사용하여 다음 명령을 실행합니다.

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```
   
## <a name="create-a-blob-input-or-output"></a>Blob 입력 또는 출력 만들기  

관리 ID를 구성했으므로 이제 Blob 리소스를 Stream Analytics 작업에 입력이나 출력으로 추가할 수 있습니다.

1. Azure Blob Storage 출력 싱크의 출력 속성 창에서 인증 모드 드롭다운을 선택하고 **관리 ID** 를 선택합니다. 다른 출력 속성에 대한 자세한 정보는 [Azure Stream Analytics 출력 이해](./stream-analytics-define-outputs.md)를 참조하세요. 작업을 마쳤으면 **저장** 을 클릭합니다.

   ![Azure Blob Storage 출력 구성](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)


## <a name="enable-vnet-access"></a>VNET 액세스 사용

스토리지 계정의 **방화벽 및 가상 네트워크** 를 구성할 때 필요에 따라 기타 신뢰할 수 있는 Microsoft 서비스의 네트워크 트래픽을 허용할 수 있습니다. Stream Analytics가 관리 ID를 사용하여 인증할 경우, 요청이 신뢰할 수 있는 서비스에서 시작되었다는 증명을 제공합니다. 아래는 이 VNET 액세스 예외를 사용하도록 하는 지침입니다.

1.    스토리지 계정의 구성 창 내에서 "방화벽 및 가상 네트워크" 창으로 이동합니다.
2.    "신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용" 옵션이 사용하도록 설정되어 있는지 확인합니다.
3.    사용하도록 설정한 경우 **저장** 을 클릭합니다.

   ![VNET 액세스 사용](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="remove-managed-identity"></a>관리 ID 제거

Stream Analytics 작업에 대해 생성된 관리 ID는 작업이 삭제된 경우에만 삭제됩니다. 작업을 삭제하지 않고 관리 ID를 삭제할 수는 없습니다. 관리 ID를 더 이상 사용하지 않으려는 경우에는 출력에 대한 인증 방법을 변경할 수 있습니다. 관리 ID는 작업이 삭제될 때까지 계속 존재하며, 관리 ID 인증을 다시 사용하기로 한 경우 사용됩니다.

## <a name="limitations"></a>제한 사항
이 기능의 현재 제한은 다음과 같습니다.

1. 클래식 Azure Storage 계정

2. Azure Active Directory가 없는 Azure 계정

3. 다중 테넌트 액세스는 지원되지 않습니다. 주어진 Stream Analytics 작업에 대해 생성된 서비스 주체는 작업이 만들어진 동일한 Azure Active Directory 테넌트에 상주해야 하며 다른 Azure Active Directory 테넌트에 있는 리소스에 대해 사용할 수 없습니다.

4. [사용자 할당 ID](../active-directory/managed-identities-azure-resources/overview.md)는 지원되지 않습니다. 즉, 사용자는 자신의 Stream Analytics 작업에서 사용할 자체 서비스 보안 주체를 입력할 수 없습니다. 서비스 보안 주체는 Azure Stream Analytics에서 생성해야 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics의 출력 이해](./stream-analytics-define-outputs.md)
* [Azure Stream Analytics 사용자 지정 Blob 출력 분할](./stream-analytics-custom-path-patterns-blob-storage-output.md)
