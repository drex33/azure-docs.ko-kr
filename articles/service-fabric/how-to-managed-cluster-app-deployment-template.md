---
title: Azure Resource Manager를 사용하여 애플리케이션을 관리형 클러스터 배포
description: Azure Resource Manager를 사용하여 Azure Service Fabric 관리형 클러스터에서 Service Fabric 애플리케이션을 배포, 업그레이드 또는 삭제하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 8/23/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f2f2f47e9cdcef54be9c78513fbb57cd20ddde5f
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864779"
---
# <a name="manage-application-lifecycle-on-a-managed-cluster-using-azure-resource-manager"></a>Azure Resource Manager를 사용하여 관리형 클러스터에서 애플리케이션 수명 주기 관리

Service Fabric 관리형 클러스터에 Azure Service Fabric 애플리케이션을 배포하기 위한 여러 옵션이 있습니다. Azure Resource Manager를 사용하는 것이 좋습니다. Resource Manager를 사용하는 경우 애플리케이션과 서비스를 JSON 형식으로 설명한 다음, 클러스터와 동일한 Resource Manager 템플릿에 배포할 수 있습니다. PowerShell 또는 Azure CLI를 사용하여 애플리케이션을 배포하고 관리하는 것과 달리, Resource Manager를 사용하는 경우 클러스터가 준비될 때까지 기다릴 필요가 없습니다. 애플리케이션 등록, 프로비전 및 배포를 모두 한 단계로 수행할 수 있습니다. 클러스터에서 애플리케이션 수명 주기를 관리하는 가장 좋은 방법은 Resource Manager를 사용하는 것입니다. 자세한 내용은 [모범 사례: IaC(Infrastructure as Code)](service-fabric-best-practices-infrastructure-as-code.md#service-fabric-resources)를 참조하세요.

Resource Manager의 리소스로 애플리케이션을 관리하면 다음과 같은 영역에서 향상된 기능을 얻을 수 있습니다.

* 감사 내역: Resource Manager가 모든 작업을 감사하고 자세한 활동 로그를 유지합니다. 활동 로그는 애플리케이션 및 클러스터에 대한 변경 내용을 추적하는 데 도움이 될 수 있습니다.
* 역할 기반 액세스 제어: 동일한 Resource Manager 템플릿을 사용하여 클러스터 및 클러스터에 배포된 애플리케이션에 대한 액세스를 관리할 수 있습니다.
* 관리 효율성: Resource Manager를 사용하면 클러스터 및 중요한 애플리케이션 배포를 관리할 수 있는 단일 위치(Azure Portal)가 제공됩니다.

이 문서에서 학습할 내용은 다음과 같습니다.

> [!div class="checklist"]
>
> * Resource Manager를 사용하여 서비스 패브릭 애플리케이션 리소스를 배포합니다.
> * Resource Manager를 사용하여 서비스 패브릭 애플리케이션 리소스를 업그레이드합니다.
> * 서비스 패브릭 애플리케이션 리소스를 삭제합니다.

## <a name="deploy-service-fabric-application-resources"></a>Service Fabric 애플리케이션 리소스 배포

Resource Manager 애플리케이션 리소스 모델을 사용하여 애플리케이션 및 해당 서비스를 배포하기 위해 수행하는 상위 레벨 단계는 다음과 같습니다.
1. 애플리케이션 코드를 패키징합니다.
1. 패키지를 업로드합니다.
1. Resource Manager 템플릿에서 패키지의 위치를 애플리케이션 리소스로 참조합니다. 

자세한 내용은 [애플리케이션 패키지](service-fabric-package-apps.md#create-an-sfpkg)를 참조하세요.

그런 다음, Resource Manager 템플릿을 만들고, 애플리케이션 세부 정보를 사용하여 매개 변수 파일을 업데이트하고, Service Fabric 관리형 클러스터에 템플릿을 배포합니다. [샘플을 살펴보세요](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/voting-sample-no-reverse-proxy/ARM-Managed-Cluster).

### <a name="create-a-storage-account"></a>스토리지 계정 만들기

Resource Manager 템플릿에서 애플리케이션을 배포하려면 스토리지 계정이 있어야 합니다. 스토리지 계정은 애플리케이션 이미지 파일을 저장하는데 사용됩니다. 

기존 스토리지 계정을 다시 사용하거나 애플리케이션을 준비하기 위한 새 스토리지 계정을 만들 수 있습니다. 기존 스토리지 계정을 사용하는 경우에는 이 단계를 건너뛰면 됩니다. 

![스토리지 계정 만들기][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>스토리지 계정 구성

스토리지 계정을 만든 후에는 애플리케이션이 준비될 수 있는 Blob 컨테이너를 만듭니다. Azure Portal에서 애플리케이션을 저장할 Azure Storage 계정으로 이동합니다. **Blobs** > **컨테이너 추가** 를 선택합니다. 

공용 액세스 수준을 **개인** 으로 설정하여 클러스터의 리소스를 보호할 수 있습니다. 액세스 권한은 여러 가지 방법으로 부여할 수 있습니다.

* [Azure Active Directory](../storage/common/storage-auth-aad-app.md)를 사용하여 Blob 및 큐에 대한 액세스 권한을 부여합니다.
* [Azure Portal에서 Azure RBAC](../storage/blobs/assign-azure-role-data-access.md)를 사용하여 Azure Blob 및 큐 데이터에 대한 액세스 권한을 부여합니다.
* [공유 액세스 서명](/rest/api/storageservices/delegate-access-with-shared-access-signature)을 사용하여 액세스를 위임합니다.

다음 스크린샷 예제에서는 Blob에 대한 익명 읽기 액세스를 사용합니다.

![Blob 만들기][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>스토리지 계정에서 애플리케이션 준비

애플리케이션을 배포하려면 먼저 Blob 스토리지에서 애플리케이션을 준비해야 합니다. 이 자습서에서는 애플리케이션 패키지를 수동으로 만듭니다. 이 단계는 자동화할 수 있습니다. 자세한 내용은 [애플리케이션 패키징](service-fabric-package-apps.md#create-an-sfpkg)을 참조하세요. 

이 자습서에서는 [투표 샘플 애플리케이션](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart)을 사용합니다.

1. Visual Studio에서 프로젝트 **투표** 를 마우스 오른쪽 단추로 클릭한 다음, **패키지** 를 선택합니다.

   ![애플리케이션 패키징][PackageApplication]  
1. *.\service-fabric-dotnet-quickstart\Voting\pkg\Debug* 디렉터리로 이동합니다. 콘텐츠를 *Voting.zip* 이라는 파일로 압축합니다. *ApplicationManifest.xml* 파일은 zip 파일의 루트에 있어야 합니다.

   ![애플리케이션 압축][ZipApplication]  
1. 파일 이름을 바꿔서 확장명을 .zip에서 *.sfpkg* 로 변경합니다.

1. Azure Portal의 스토리지 계정에 대한 **앱** 컨테이너에서 **업로드** 를 선택한 후 **Voting.sfpkg** 를 업로드합니다. 

   ![앱 패키지 업로드][UploadAppPkg]

이제 애플리케이션이 준비되었으며 Resource Manager 템플릿을 만들어 애플리케이션을 배포할 수 있습니다.

### <a name="create-the-resource-manager-template"></a>리소스 관리자 템플릿 만들기

샘플 애플리케이션에는 애플리케이션을 배포하는 데 사용할 수 있는 [Azure Resource Manager 템플릿](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM)이 포함되어 있습니다. 템플릿 파일 이름은 *UserApp.json* 및 *UserApp.Parameters.json* 입니다.

> [!NOTE]
> *UserApp.Parameters.json* 파일은 클러스터의 이름을 사용하여 업데이트해야 합니다.
>
>

| 매개 변수              | 설명                                 | 예제                                                      | 주석                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | 배포하는 클러스터의 이름 | sf-cluster123                                                |                                                              |
| 애플리케이션            | 애플리케이션의 이름                 | Voting                                                       |
| 버전    | 앱의 리소스 ID, 애플리케이션 유형 및 버전입니다.       | /providers/Microsoft.ServiceFabric/managedClusters/sf-cluster-123/applicationTypes/VotingType/versions/1.0.0                                               | ApplicationManifest.xml과 일치해야 함                 |
| serviceName            | 서비스의 이름         | VotingWeb                                             | ServiceType 형식이어야 함            |
| serviceTypeName        | 서비스 형식 이름                | VotingWebType                                                    | ServiceManifest.xml과 일치해야 함                 |
| appPackageUrl          | 애플리케이션의 Blob Storage URL     | https:\//servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | Blob Storage에 있는 애플리케이션 패키지의 URL(URL을 설정하는 절차는 문서의 후반부에 설명되어 있음) |

```json
{
    "apiVersion": "2021-05-01",
    "type": "Microsoft.ServiceFabric/managedclusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-05-01",
    "type": "Microsoft.ServiceFabric/managedclusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-05-01",
    "type": "Microsoft.ServiceFabric/managedclusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-05-01",
    "type": "Microsoft.ServiceFabric/managedclusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-service-fabric-application"></a>Service Fabric 애플리케이션 배포

**AzResourceGroupDeployment** cmdlet을 실행하여 클러스터를 포함하는 리소스 그룹에 애플리케이션을 배포합니다.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>Resource Manager를 사용하여 Service Fabric 애플리케이션 업그레이드

> [!IMPORTANT]
> ARM(Azure Resource Manager) 템플릿을 통해 배포되는 모든 서비스는 해당 ApplicationManifest.xml 파일의 DefaultServices 섹션에서 제거해야 합니다.


다음과 같은 이유 중 하나로 Service Fabric 클러스터에 이미 배포된 애플리케이션을 업그레이드할 수 있습니다.

* 새 서비스가 애플리케이션에 추가됩니다. 서비스가 애플리케이션에 추가되는 경우 *service-manifest.xml* 및 *application-manifest.xml* 파일에 서비스 정의를 추가해야 합니다. 또한 애플리케이션의 새 버전을 반영하려면 애플리케이션 유형 버전을 [UserApp.Parameters.js](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json)의 1.0.0에서 1.0.1로 변경해야 합니다.

    ```json
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```

* 기존 서비스의 새 버전이 애플리케이션에 추가됩니다. 애플리케이션 코드 변경 내용 및 앱 형식 버전 및 이름에 대한 업데이트를 예로 들 수 있습니다. 이 업그레이드의 경우 다음과 같이 UserApp.Parameters.json을 업데이트합니다.

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```
## <a name="delete-service-fabric-application-resources"></a>Service Fabric 애플리케이션 리소스 삭제
> [!NOTE]
> 개별 리소스를 정리하는 선언적 방법이 없으므로 ARM(Azure Resource Manager) 템플릿을 통해 애플리케이션을 삭제하면 안 됩니다.

Resource Manager에서 애플리케이션 리소스 모델을 사용하여 배포된 서비스 패브릭 애플리케이션을 삭제하려면 다음을 수행합니다.

1. [Get-AzResource](/powershell/module/az.resources/get-azresource) cmdlet을 사용하여 애플리케이션의 리소스 ID를 가져옵니다.

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. [Remove-AzResource](/powershell/module/az.resources/remove-azresource) cmdlet을 사용하여 애플리케이션 리소스를 삭제합니다.

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```


## <a name="migration-from-classic-to-managed-clusters"></a>클래식에서 관리형 클러스터로 마이그레이션

클래식에서 관리형 클러스터로 애플리케이션을 마이그레이션하는 경우 형식이 올바르게 지정되었는지 확인해야 하며 그렇지 않으면 오류가 발생합니다. 

다음 항목은 사용 빈도로 인해 특별히 호출되지만 전용 차이점 목록으로는 사용되지 않습니다. 

* upgradeReplicaSetCheckTimeout은 이제 관리되는 정수이지만 클래식 SFRP에서는 문자열입니다. 

속성 및 형식의 전체 목록은 [관리형 클러스터 애플리케이션 리소스 형식](/azure/templates/microsoft.servicefabric/managedclusters/applications?tabs=json)을 참조하세요.

## <a name="next-steps"></a>다음 단계

관리형 클러스터 애플리케이션 배포에 대해 자세히 알아봅니다.

* [관리형 클러스터 애플리케이션 비밀 배포](how-to-managed-cluster-application-secrets.md)
* [관리 ID를 사용하여 관리형 클러스터 애플리케이션 배포](how-to-managed-cluster-application-managed-identity.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png