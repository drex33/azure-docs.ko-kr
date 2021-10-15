---
title: 관리 ID를 사용하여 Power BI 출력에 대해 Azure Stream Analytics 작업 인증
description: 이 문서에서는 관리 ID를 사용하여 Azure SQL DB 출력에 대해 Azure Stream Analytics 작업을 인증하는 방법을 설명합니다.
ms.service: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.topic: how-to
ms.date: 05/30/2021
ms.openlocfilehash: ad8142bf5ab65bb6ae4ff49aeb7b4e661d2761a8
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130045865"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi"></a>관리 ID를 사용하여 Power BI에 Azure Stream Analytics 작업 인증

Power BI에 대한 출력에 [관리 ID 인증](../active-directory/managed-identities-azure-resources/overview.md)을 사용하면 Stream Analytics 작업에서 Power BI 계정 내의 작업 영역에 직접 액세스할 수 있습니다. 이 기능을 사용하면 사용자는 더 이상 Azure Portal을 통해 Power BI에 대화형으로 로그인할 필요가 없으므로 Stream Analytics 작업의 배포가 완전히 자동화될 수 있습니다. 또한 Power BI에 쓰는 장기 실행 작업을 주기적으로 다시 인증할 필요가 없으므로 보다 잘 지원됩니다.

이 문서에서는 Azure Portal 및 Azure Resource Manager 배포를 통해 Stream Analytics 작업의 Power BI 출력에 대해 관리 ID를 사용하도록 설정하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

이 기능을 사용하려면 다음 항목이 필요합니다.

- [Pro 라이선스](/power-bi/service-admin-purchasing-power-bi-pro)가 있는 Power BI 계정

- Power BI 계정 내의 업그레이드된 작업 영역. 자세한 내용은 이 기능에 대한 [Power BI 알림](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/)을 참조하세요.

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>Azure Portal을 사용하여 Stream Analytics 작업 만들기

1. 새 Stream Analytics 작업을 만들거나 Azure Portal에서 기존 작업을 엽니다. 화면 왼쪽에 있는 메뉴 모음에서 **구성** 아래에 있는 **관리 ID** 를 선택합니다. "시스템 할당 관리 ID 사용"이 선택되어 있는지 확인한 다음, 화면 아래쪽에 있는 **저장** 단추를 선택합니다.

   ![Stream Analytics 관리 ID 구성](./media/common/stream-analytics-enable-managed-identity.png)

2. 출력을 구성하기 전에 이 문서의 [Stream Analytics 작업에 Power BI 작업 영역에 대한 액세스 권한 부여](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) 섹션의 지침에 따라 Stream Analytics 작업에 Power BI 작업 영역에 대한 액세스 권한을 부여합니다.

3. Stream analytics 작업의 **출력** 섹션으로 이동하고 **+ 추가** 를 선택한 다음, **Power BI** 를 선택합니다. 그런 다음, **권한 부여** 단추를 선택하고 Power BI 계정으로 로그인합니다.

   ![Power BI 계정으로 권한 부여](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. 권한이 부여되면 사용자가 액세스할 수 있는 모든 작업 영역으로 드롭다운 목록이 채워집니다. 이전 단계에서 권한을 부여한 작업 영역을 선택합니다. 그런 다음, "인증 모드"로 **관리 ID** 를 선택합니다. 마지막으로 **저장** 단추를 선택합니다.

   ![관리 ID를 사용하여 Power BI 출력 구성](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Azure Resource Manager 배포

Azure Resource Manager를 사용하여 Stream Analytics 작업의 배포를 완전히 자동화할 수 있습니다. Azure PowerShell 또는 [Azure CLI](/cli/azure/)를 사용하여 Resource Manager 템플릿을 배포할 수 있습니다. 아래 예제에서는 Azure CLI를 사용합니다.


1. Resource Manager 템플릿의 리소스 섹션에 다음 속성을 포함하여 관리 ID를 통해 **Microsoft.StreamAnalytics/streamingjobs** 리소스를 만들 수 있습니다.

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   이 속성을 통해 Azure Resource Manager에서 Stream Analytics 작업에 대한 ID를 만들어서 관리합니다. 다음은 관리 ID를 사용하도록 설정한 Stream Analytics 작업과 관리 ID를 사용하는 Power BI 출력 싱크를 배포하는 Resource Manager 템플릿 예제입니다.

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "pbi_managed_id",
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
                                "datasource":{
                                    "type":"PowerBI",
                                    "properties":{
                                        "dataset": "dataset_name",
                                        "table": "table_name",
                                        "groupId": "01234567-89ab-cdef-0123-456789abcdef",
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

    아래 Azure CLI 명령을 사용하여 리소스 그룹 **ExampleGroup** 에 위의 작업을 배포합니다.

    ```azurecli
    az deployment group create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. 작업을 만든 후 Azure Resource Manager를 사용하여 작업의 전체 정의를 검색합니다.

    ```azurecli
    az resource show --ids /subscriptions/<subsription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/StreamingJobs/<resource-name>
    ```

    위의 명령은 다음과 같은 응답을 반환합니다.

    ```json
    {
        "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/streamingjobs/<resource-name>",
        "identity": {
            "principalId": "<principal-id>",
            "tenantId": "<tenant-id>",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "<resource-name>",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "<job-id>",
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
        "resourceGroup": "<resource-group>",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

    Power BI REST API 사용하여 Stream Analytics 작업을 Power BI 작업 영역에 추가하려는 경우 반환된 "principalId"를 기록해 둡다.

3. 이제 작업이 만들어졌으므로 이 문서의 [Stream Analytics 작업에 Power BI 작업 영역에 대한 액세스 권한 부여](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) 섹션으로 이동합니다.


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>Stream Analytics 작업에 Power BI 작업 영역에 대한 액세스 권한 부여

이제 Stream Analytics 작업이 만들어졌으므로 Power BI 작업 영역에 대한 액세스 권한을 부여할 수 있습니다. 작업 액세스 권한을 부여했으면 ID가 전파될 때까지 몇 분 정도 기다립니다.

### <a name="use-the-power-bi-ui"></a>Power BI UI 사용

   > [!Note]
   > UI를 사용하여 Power BI 작업 영역에 Stream Analytics 작업을 추가하려면 Power BI 관리자 포털의 **개발자 설정** 에서 서비스 주체 액세스를 사용하도록 설정해야 합니다. 자세한 내용은 [서비스 주체 시작](/power-bi/developer/embed-service-principal)을 참조하세요.

1. 작업 영역 액세스 설정으로 이동합니다. 자세한 내용은 [작업 영역에 대한 액세스 권한 부여](/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace) 문서를 참조하세요.

2. 텍스트 상자에 Stream Analytics 작업 이름을 입력하고 액세스 수준으로 **기여자** 를 선택합니다.

3. **추가** 를 선택하고 창을 닫습니다.

   ![Power BI 작업 영역에 Stream Analytics 작업 추가](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>Power BI PowerShell cmdlet 사용

1. Power BI `MicrosoftPowerBIMgmt` PowerShell cmdlet을 설치합니다.

   > [!Important]
   > cmdlet의 버전 1.0.821 이상을 사용하고 있는지 확인하세요.

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. Power BI에 로그인합니다.

```powershell
Login-PowerBI
```

3. 기여자 권한으로 작업 영역에 Stream Analytics 작업을 추가합니다.

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>Power BI REST API 사용

"그룹 사용자 추가" REST API를 직접 사용하여 기여자 권한으로 Stream Analytics 작업을 작업 영역에 추가할 수도 있습니다. 이 API에 대한 전체 설명서는 [그룹 - 그룹 사용자 추가](/rest/api/power-bi/groups/addgroupuser)에서 찾을 수 있습니다.

**샘플 요청**
```http
POST https://api.powerbi.com/v1.0/myorg/groups/{groupId}/users
```
요청 본문
```json
{
    "groupUserAccessRight": "Contributor",
    "identifier": "<principal-id>",
    "principalType": "App"
}
```

### <a name="use-a-service-principal-to-grant-permission-for-an-asa-jobs-managed-identity"></a>서비스 주체를 사용하여 ASA 작업의 관리 ID에 대한 사용 권한 부여

자동화된 배포의 경우 대화형 로그인을 사용하여 Power BI 작업 영역에 대한 ASA 작업 액세스 권한을 부여하는 것은 불가능합니다. 이는 서비스 주체를 사용하여 ASA 작업의 관리 ID에 대한 사용 권한을 부여함으로써 수행할 수 있습니다. 이 작업은 다음과 같은 PowerShell을 사용하여 수행할 수 있습니다.

```powershell
Connect-PowerBIServiceAccount -ServicePrincipal -TenantId "<tenant-id>" -CertificateThumbprint "<thumbprint>" -ApplicationId "<app-id>"
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

## <a name="remove-managed-identity"></a>관리 ID 제거

Stream Analytics 작업에 대해 생성된 관리 ID는 작업이 삭제된 경우에만 삭제됩니다. 작업을 삭제하지 않고 관리 ID를 삭제할 수는 없습니다. 관리 ID를 더 이상 사용하지 않으려는 경우에는 출력에 대한 인증 방법을 변경할 수 있습니다. 관리 ID는 작업이 삭제될 때까지 계속 존재하며, 관리 ID 인증을 다시 사용하기로 한 경우 사용됩니다.

## <a name="limitations"></a>제한 사항
이 기능의 제한 사항은 다음과 같습니다.

- 클래식 Power BI 작업 영역은 지원되지 않습니다.

- Azure Active Directory 없는 Azure 계정

- 다중 테넌트 액세스는 지원되지 않습니다. 주어진 Stream Analytics 작업에 대해 생성된 서비스 주체는 작업이 만들어진 동일한 Azure Active Directory 테넌트에 상주해야 하며 다른 Azure Active Directory 테넌트에 있는 리소스에 대해 사용할 수 없습니다.

- [사용자 할당 ID](../active-directory/managed-identities-azure-resources/overview.md)는 지원되지 않습니다. 즉, 자신의 Stream Analytics 작업에서 사용할 자체 서비스 주체를 입력할 수 없습니다. 서비스 주체는 Azure Stream Analytics에서 생성해야 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics를 사용하여 Power BI 대시보드 통합](./stream-analytics-power-bi-dashboard.md)
* [Azure Stream Analytics의 출력 이해](./stream-analytics-define-outputs.md)
