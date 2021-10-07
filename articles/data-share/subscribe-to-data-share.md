---
title: '자습서: 데이터 수락 및 수신 - Azure Data Share'
description: 자습서 - Azure Data Share를 사용하여 데이터 수락 및 받기
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 09/10/2021
ms.openlocfilehash: 035b6c9b12df69395409c1c0aa70be219bc2533e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124760197"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>자습서: Azure Data Share를 사용하여 데이터 수락 및 받기  

이 자습서에서는 Azure Data Share를 사용하여 데이터 공유 초대를 수락하는 방법에 대해 알아봅니다. 공유되는 데이터를 받는 방법과 정기적인 새로 고침 간격을 사용하도록 설정하여 공유되는 데이터의 최신 스냅샷을 항상 유지하는 방법에 대해 알아봅니다. 

> [!div class="checklist"]
> * Azure Data Share 초대를 수락하는 방법
> * Azure Data Share 계정 만들기
> * 데이터 대상 지정
> * 예약된 새로 고침을 위해 데이터 공유에 대한 구독 만들기

## <a name="prerequisites"></a>사전 요구 사항
데이터 공유 초대를 수락하려면 먼저 아래에 나열된 여러 Azure 리소스를 프로비저닝해야 합니다. 

모든 필수 조건이 충족되었는지 확인한 후에 데이터 공유 초대를 수락합니다. 

* Azure 구독: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* 데이터 공유 초대: " **<yourdataprovider@domain.com>** 이(가) 보낸 Azure Data Share 초대"라는 제목이 있는 Microsoft Azure의 초대입니다.
* 데이터 공유 리소스를 만들 Azure 구독과 대상 Azure 데이터 저장소가 있는 Azure 구독에 [Microsoft.DataShare 리소스 공급자](concepts-roles-permissions.md#resource-provider-registration)를 등록합니다.

### <a name="receive-data-into-a-storage-account"></a>스토리지 계정으로 데이터 수신

* Azure Storage 계정: 아직 없는 경우 [Azure Storage 계정](../storage/common/storage-account-create.md)을 만들 수 있습니다. 
* 스토리지 계정에 쓸 수 있는 권한으로, *Microsoft.Storage/storageAccounts/write* 에 있습니다. 이 권한은 기여자 역할에 있습니다. 
* 스토리지 계정에 역할 할당을 추가할 수 있는 권한입니다. 이 권한은 *Microsoft.Authorization/role assignments/write* 에 있습니다. 이 권한은 소유자 역할에 있습니다.  

### <a name="receive-data-into-a-sql-based-target"></a>SQL 기반 대상으로 데이터 수신
Azure SQL Database, Azure Synapse Analytics로 데이터를 받도록 선택하는 경우 아래는 필수 구성 요소 목록입니다. 

#### <a name="prerequisites-for-receiving-data-into-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Azure SQL Database 또는 Azure Synapse Analytics(이전의 Azure SQL DW)로 데이터를 받기 위한 필수 구성 요소

* Azure SQL Database 또는 Azure Synapse Analytics(이전의 Azure SQL DW).
* SQL 서버의 데이터베이스를 쓸 수 있는 권한으로, *Microsoft.Sql/servers/databases/write* 에 있습니다. 이 권한은 **기여자** 역할에 있습니다. 
* SQL 서버의 **Azure Active Directory 관리자**
* SQL Server Firewall 액세스. 이 작업은 다음 단계를 통해 수행할 수 있습니다. 
    1. Azure Portal의 SQL 서버에서 *방화벽 및 가상 네트워크* 로 이동합니다.
    1. *Azure 서비스 및 리소스가 이 서버에 액세스할 수 있도록 허용* 에 대해 **예** 를 클릭합니다.
    1. **+클라이언트 IP 추가** 를 클릭합니다. 클라이언트 IP 주소는 변경될 수 있습니다. 이 프로세스는 다음에 Azure Portal에서 SQL 데이터를 공유할 때 반복해야 할 수도 있습니다. IP 범위를 추가할 수도 있습니다.
    1. **저장** 을 클릭합니다. 
 
#### <a name="prerequisites-for-receiving-data-into-azure-synapse-analytics-workspace-sql-pool"></a>Azure Synapse Analytics(작업 영역) SQL 풀로 데이터를 받기 위한 필수 구성 요소

* Azure Synapse Analytics(작업 영역) 전용 SQL 풀. 서버리스 SQL 풀로 데이터를 받는 것은 현재 지원되지 않습니다.
* *Microsoft.Synapse/workspaces/sqlPools/write* 에 있는 Synapse 작업 영역의 SQL 풀에 쓸 수 있는 권한입니다. 이 권한은 **기여자** 역할에 있습니다.
* Data Share 리소스의 관리 ID가 Synapse 작업 영역 SQL 풀에 액세스할 수 있는 권한입니다. 이 작업은 다음 단계를 통해 수행할 수 있습니다. 
    1. Azure Portal에서 Synapse 작업 영역으로 이동합니다. 왼쪽 탐색에서 SQL Active Directory 관리자를 선택하고 자신을 **Azure Active Directory 관리자** 로 설정합니다.
    1. Synapse Studio를 열고 왼쪽 탐색에서 *관리* 를 선택합니다. 보안에서 *액세스 제어* 를 선택합니다. 자신에게 **SQL 관리자** 또는 **작업 영역 관리자** 역할을 할당합니다.
    1. Synapse Studio의 왼쪽 탐색에서 *개발* 을 선택합니다. SQL 풀에서 다음 스크립트를 실행하여 Data Share 리소스 관리 ID를 'db_datareader, db_datawriter, db_ddladmin'으로 추가합니다. 
    
        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```                   
       *<share_acc_name>* 은 Data Share 리소스의 이름입니다. Data Share 리소스를 아직 만들지 않은 경우 나중에 이 필수 조건으로 다시 돌아올 수 있습니다.  

* Synapse 작업 영역 Firewall 액세스. 이 작업은 다음 단계를 통해 수행할 수 있습니다. 
    1. Azure Portal에서 Synapse 작업 영역으로 이동합니다. 왼쪽 탐색에서 *방화벽* 을 선택합니다.
    1. **Azure 서비스 및 리소스가 이 작업 영역에 액세스할 수 있도록 허용** 하려면 *켜기* 를 클릭합니다.
    1. **+클라이언트 IP 추가** 를 클릭합니다. 클라이언트 IP 주소는 변경될 수 있습니다. 이 프로세스는 다음에 Azure Portal에서 SQL 데이터를 공유할 때 반복해야 할 수도 있습니다. IP 범위를 추가할 수도 있습니다.
    1. **저장** 을 클릭합니다. 

### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Azure Data Explorer 클러스터로 데이터를 받습니다. 

* 데이터 공급자의 데이터 탐색기 클러스터와 동일한 Azure 데이터 센터에 있는 Azure Data Explorer 클러스터: 아직 없는 경우 [Azure Data Explorer 클러스터](/azure/data-explorer/create-cluster-database-portal)를 만들 수 있습니다. 데이터 공급자 클러스터의 Azure 데이터 센터를 모르는 경우 나중에 프로세스에서 클러스터를 만들 수 있습니다.
* Azure Data Explorer 클러스터에 쓸 수 있는 권한으로, *Microsoft.Kusto/clusters/write* 에 있습니다. 이 권한은 기여자 역할에 있습니다. 

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="open-invitation"></a>초대 열기

### <a name="portal"></a>[포털](#tab/azure-portal)

1. 이메일 또는 Azure Portal에서 직접 초대를 열 수 있습니다. 

   이메일에서 초대를 열려면 받은 편지함에서 데이터 공급자의 초대를 확인합니다. 이 초대는 Microsoft Azure에서 **<yourdataprovider@domain.com>이(가) 보낸 Azure Data Share 초대** 라는 제목으로 보낸 것입니다. **초대 보기** 를 클릭하여 Azure에서 초대를 확인합니다. 

   Azure Portal에서 직접 초대를 열려면 Azure Portal에서 **데이터 공유 초대** 를 검색합니다. 이 작업을 수행하면 Data Share 초대 목록으로 이동됩니다.

   테넌트의 게스트 사용자인 경우 처음으로 Data Share 초대를 보기 전에 테넌트 이메일 주소를 확인하라는 메시지가 표시됩니다. 확인되면 12개월 동안 유효합니다.

   ![초대 목록](./media/invitations.png "초대 목록") 

1. 확인하려는 초대를 선택합니다. 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 환경을 준비한 다음, 초대를 확인합니다.

Azure CLI에 대한 환경 준비하는 것으로 시작합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[az datashare consumer invitation list](/cli/azure/datashare/consumer/invitation#az_datashare_consumer_invitation_list) 명령을 실행하여 현재 초대를 확인합니다.

```azurecli
az datashare consumer invitation list --subscription 11111111-1111-1111-1111-111111111111
```

다음 섹션에서 사용할 초대 ID를 복사합니다.

---

## <a name="accept-invitation"></a>초대 수락

### <a name="portal"></a>[포털](#tab/azure-portal)

1. **사용 약관** 을 포함하여 모든 필드를 검토해야 합니다. 사용 약관에 동의하는 경우 해당 확인란을 선택하여 동의함을 표시해야 합니다. 

   ![사용 약관](./media/terms-of-use.png "사용 약관") 

1. *대상 데이터 공유 계정* 에서 Data Share를 배포할 대상의 구독 및 리소스 그룹을 선택합니다. 

   기존 Data Share 계정이 없는 경우 **데이터 공유 계정** 필드에서 **새로 만들기** 를 선택합니다. 그렇지 않으면 데이터 공유를 수락할 기존 Data Share 계정을 선택합니다. 

   **받은 공유 이름** 필드에 대해 데이터 공급자가 지정한 기본값을 그대로 두거나 받은 공유에 대해 새 이름을 지정할 수 있습니다. 

   사용 약관에 동의하고 받은 공유를 관리할 데이터 공유 계정을 지정했으면 **수락 및 구성** 을 선택합니다. 공유 구독이 만들어집니다. 

   ![수락 옵션](./media/accept-options.png "수락 옵션") 

   이 작업을 수행하면 Data Share 계정에서 받은 공유로 이동합니다. 

   초대를 수락하지 않으려면 *거부* 를 선택합니다. 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az datashare consumer share-subscription create](/cli/azure/datashare/consumer/share-subscription#az_datashare_consumer_share_subscription_create) 명령을 사용하여 Data Share를 만듭니다.

```azurecli
az datashare consumer share-subscription create --resource-group share-rg \
  --name "Fabrikam Solutions" --account-name FabrikamDataShareAccount \
  --invitation-id 89abcdef-0123-4567-89ab-cdef01234567 \
  --source-share-location "East US 2" --subscription 11111111-1111-1111-1111-111111111111
```

---

## <a name="configure-received-share"></a>수신된 공유 구성

### <a name="portal"></a>[포털](#tab/azure-portal)

아래 단계에 따라 데이터를 받을 위치를 구성합니다.

1. **데이터 세트** 탭을 선택합니다. 대상을 할당하려는 데이터 세트 옆에 있는 상자를 선택합니다. **+ 대상에 매핑** 을 선택하여 대상 데이터 저장소를 선택합니다. 

   ![대상에 매핑](./media/dataset-map-target.png "대상에 매핑") 

1. 데이터를 가져올 대상 데이터 저장소 유형을 선택합니다. 경로와 이름이 동일한 대상 데이터 저장소에 있는 모든 데이터 파일 또는 테이블을 덮어씁니다. Azure SQL Database 또는 Azure Synapse Analytics(이전의 Azure SQL DW)로 데이터를 수신하는 경우 **데이터 공유를 허용하여 사용자 대신 위의 '사용자 만들기' 스크립트 실행** 확인란을 선택합니다.

   내부 공유의 경우 지정된 위치에서 데이터 저장소를 선택합니다. 위치는 데이터 공급자의 원본 데이터 저장소가 있는 Azure 데이터 센터입니다. 데이터 세트가 매핑되면 대상 경로의 링크를 따라 데이터에 액세스할 수 있습니다.

   ![대상 스토리지 계정](./media/dataset-map-target-sql.png "대상 스토리지") 

1. 스냅샷 기반 공유의 경우 데이터 공급자가 데이터에 정기적인 업데이트를 제공하기 위해 스냅샷 일정을 만든 경우 **스냅샷 일정** 탭을 선택하여 스냅샷 일정을 사용하도록 설정할 수도 있습니다. 스냅샷 일정 옆의 확인란을 선택하고 **+ 사용** 을 선택합니다. 첫 번째 예약된 스냅샷은 예약 시간의 1분 이내에 시작되고 후속 스냅샷은 예약 시간(초) 이내에 시작됩니다.

   ![스냅샷 일정 사용](./media/enable-snapshot-schedule.png "스냅샷 일정 사용")

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

이러한 명령을 사용하여 데이터를 받을 위치를 구성합니다.

1. [az datashare consumer share-subscription list-source-dataset](/cli/azure/datashare/consumer/share-subscription#az_datashare_consumer_share_subscription_list_source_dataset) 명령을 실행하여 데이터 세트 ID를 가져옵니다.

   ```azurecli
   az datashare consumer share-subscription list-source-dataset \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "[0].dataSetId"
   ```

1. [az storage account create](/cli/azure/storage/account#az_storage_account_create) 명령을 실행하여 이 Data Share에 대한 스토리지 계정을 만듭니다.

   ```azurecli
   az storage account create --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. [az storage account show](/cli/azure/storage/account#az_storage_account_show) 명령을 사용하여 스토리지 계정 ID를 가져옵니다.

   ```azurecli
   az storage account show --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "id"
   ```

1. 다음 명령을 사용하여 계정 주체 ID를 가져옵니다.

   ```azurecli
   az datashare account show --resource-group "share-rg" --name "cli_test_consumer_account" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "identity.principalId"
   ```

1. [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) 명령을 사용하여 계정 주체에 대한 역할 할당을 만듭니다.

   ```azurecli
   az role assignment create --role "01234567-89ab-cdef-0123-456789abcdef" \
     --assignee-object-id 6789abcd-ef01-2345-6789-abcdef012345 
     --assignee-principal-type ServicePrincipal --scope 456789ab-cdef-0123-4567-89abcdef0123 \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. 데이터 세트 ID를 기반으로 매핑을 위한 변수를 만듭니다.

   ```azurecli
   $mapping='{\"data_set_id\":\"' + $dataset_id + '\",\"container_name\":\"newcontainer\",
     \"storage_account_name\":\"datashareconsumersa\",\"kind\":\"BlobFolder\",\"prefix\":\"consumer\"}'
   ```

1. [az datashare consumer dataset-mapping create](/cli/azure/datashare/consumer/dataset-mapping#az_datashare_consumer_dataset_mapping_create) 명령을 사용하여 데이터 세트 매핑을 만듭니다.

   ```azurecli
   az datashare consumer dataset-mapping create --resource-group "share-rg" \
     --name "consumer-data-set-mapping" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --mapping $mapping \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. [az datashare consumer share-subscription synchronization start](/cli/azure/datashare/consumer/share-subscription/synchronization#az_datashare_consumer_share_subscription_synchronization_start) 명령을 실행하여 데이터 세트 동기화를 시작합니다.

   ```azurecli
   az datashare consumer share-subscription synchronization start \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount"  \
     --share-subscription-name "Fabrikam Solutions" --synchronization-mode "Incremental" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   [az datashare consumer share-subscription synchronization list](/cli/azure/datashare/consumer/share-subscription/synchronization#az_datashare_consumer_share_subscription_synchronization_list) 명령을 실행하여 동기화 목록을 확인합니다.

   ```azurecli
   az datashare consumer share-subscription synchronization list \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   [az datashare consumer share-subscription list-source-share-synchronization-setting](/cli/azure/datashare/consumer/share-subscription#az_datashare_consumer_share_subscription_list_source_share_synchronization_setting) 명령을 사용하여 공유에 설정된 동기화 설정을 확인합니다.

   ```azurecli
   az datashare consumer share-subscription list-source-share-synchronization-setting \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --subscription 11111111-1111-1111-1111-111111111111
   ```

---

## <a name="trigger-a-snapshot"></a>스냅샷 트리거

### <a name="portal"></a>[포털](#tab/azure-portal)

이 단계는 스냅샷 기반 공유에만 적용됩니다.

1. **세부 정보** 탭 다음에 **스냅샷 트리거** 를 선택하여 스냅샷을 트리거할 수 있습니다. 여기서는 데이터의 전체 또는 증분 스냅샷을 트리거할 수 있습니다. 데이터를 데이터 공급자로부터 처음 받는 경우 전체 복사본을 선택합니다. 

   ![스냅샷 트리거](./media/trigger-snapshot.png "스냅샷 트리거") 

1. 마지막 실행 상태가 *성공* 인 경우 대상 데이터 저장소로 이동하여 받은 데이터를 확인합니다. **데이터 세트** 를 선택하고 대상 경로에서 링크를 클릭합니다. 

   ![소비자 데이터 세트](./media/consumer-datasets.png "소비자 데이터 세트 매핑") 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az datashare consumer trigger create](/cli/azure/datashare/consumer/trigger#az_datashare_consumer_trigger_create) 명령을 실행하여 스냅샷을 트리거합니다.

```azurecli
az datashare consumer trigger create --resource-group "share-rg" \
  --name "share_test_trigger" --account-name "FabrikamDataShareAccount" \
  --share-subscription-name "Fabrikam Solutions" --recurrence-interval "Day" \
  --synchronization-time "2020-04-23 18:00:00 +00:00" --kind ScheduleBased \
  --subscription 11111111-1111-1111-1111-111111111111
```

> [!NOTE]
> 스냅샷 기반 공유에만 이 명령을 사용합니다.

---

## <a name="view-history"></a>기록 보기
이 단계는 스냅샷 기반 공유에만 적용됩니다. 스냅샷의 기록을 보려면 **기록** 탭을 선택합니다. 여기서는 지난 30일 동안 생성된 모든 스냅샷의 기록을 확인할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

리소스가 더 이상 필요하지 않으면 **Data Share 개요** 페이지로 이동하고 **삭제** 를 선택하여 제거합니다.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Data Share를 수락하고 받는 방법을 알아보았습니다. Azure Data Share 개념에 대해 자세히 알아보려면 Azure Data Share 용어로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Data Share 개념](terminology.md)