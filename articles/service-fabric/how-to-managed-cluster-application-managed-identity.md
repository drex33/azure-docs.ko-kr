---
title: Service Fabric 관리형 클러스터에서 애플리케이션 관리 ID 구성 및 사용
description: ARM(Azure Resource Manager) 템플릿 배포 Azure Service Fabric 관리 클러스터에서 애플리케이션 관리 ID를 구성하고 사용하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: e3fcae9a77a4fb559d8109dd2cdd1b2a9be77b73
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615261"
---
# <a name="deploy-an-application-with-managed-identity-to-a-service-fabric-managed-cluster"></a>관리 ID가 있는 애플리케이션을 Service Fabric 관리형 클러스터에 배포

관리 ID를 사용하여 Service Fabric 애플리케이션을 배포하려면 일반적으로 Azure Resource Manager 템플릿을 사용하여 Azure Resource Manager를 통해 애플리케이션을 배포해야 합니다. Azure Resource Manager를 통해 Service Fabric 애플리케이션을 배포하는 방법에 대한 자세한 내용은 [Azure Resource Manager를 사용하여 관리되는 클러스터에 애플리케이션 배포](how-to-managed-cluster-app-deployment-template.md)를 참조하세요.

> [!NOTE] 
> 
> Azure 리소스로 배포되지 않은 애플리케이션은 관리 ID를 가질 수 **없습니다**. 
>
> 관리 ID를 사용한 Service Fabric 애플리케이션 배포는 관리 클러스터의 API 버전 `"2021-05-01"`에서 지원됩니다.

샘플 관리 클러스터 템플릿은 [Service Fabric 관리 클러스터 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates)에서 제공됩니다.

## <a name="managed-identity-support-in-service-fabric-managed-cluster"></a>Service Fabric 관리 클러스터에서 관리 ID 지원

Service Fabric 애플리케이션을 [Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)로 구성하고 클러스터에 배포하면 Service Fabric 관리 클러스터에서 *관리 ID 토큰 서비스* 의 자동 구성이 트리거됩니다. 이 서비스는 관리 ID를 사용하여 Service Fabric 애플리케이션을 인증하고 사용자 대신 액세스 토큰을 가져오는 작업을 담당합니다. 서비스를 사용하도록 설정하면 왼쪽 창의 **시스템** 섹션 아래에 있는 Service Fabric Explorer에서 **fabric:/System/ManagedIdentityTokenService** 라는 이름으로 실행되는 것을 볼 수 있습니다.

>[!NOTE]
>관리 ID를 사용하여 애플리케이션을 처음 배포할 때에는 자동 클러스터 구성 변경으로 인해 일회성 배포가 더 길어질 것으로 예상해야 합니다. 영역 클러스터의 경우 15분 이상, 영역 확장 클러스터의 경우 최대 45분이 소요될 것을 예상해야 합니다. 다른 배포가 진행 중인 경우 MITS 구성은 해당 배포가 먼저 완료될 때까지 대기합니다.

애플리케이션 리소스는 SystemAssigned 또는 UserAssigned 모두 할당을 지원하며 할당은 아래 조각에 나온 것과 같이 수행할 수 있습니다.

```json
{
  "type": "Microsoft.ServiceFabric/managedclusters/applications",
  "apiVersion": "2021-05-01",
  "identity": {
    "type": "SystemAssigned",
    "userAssignedIdentities": {}
  },
}

```
[전체 JSON 참조](/azure/templates/microsoft.servicefabric/2021-05-01/managedclusters/applications?tabs=json)

## <a name="user-assigned-identity"></a>사용자가 할당한 ID

사용자 할당 ID로 애플리케이션을 사용하도록 설정하려면 먼저 **userAssigned** 형식과 참조된 사용자 할당 ID를 사용하여 애플리케이션 리소스에 **identity** 속성을 추가합니다. 그런 다음 **application** 리소스의 **properties** 섹션 내에 **managedIdentities** 섹션을 추가합니다. 여기에는 사용자가 할당한 각 ID의 principalID 매핑에 대한 식별 이름 목록이 포함됩니다. 사용자 할당 ID에 대한 자세한 내용은 [사용자 할당 관리 ID 만들기, 나열 또는 삭제](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)를 참조하세요.

### <a name="application-template"></a>애플리케이션 템플릿

사용자 할당 ID를 사용하여 애플리케이션을 사용하도록 설정하려면 먼저 애플리케이션 리소스에 **userAssigned** 형식과 참조된 사용자 할당 ID를 사용하여 **identity** 속성을 추가하고 **properties** 섹션 내에 **managedIdentities** 개체를 추가합니다. 여기에는 사용자가 할당한 각 ID의 principalId 매핑에 대한 식별 이름 목록이 포함되어 있습니다.

```json
{
  "apiVersion": "2021-05-01",
  "type": "Microsoft.ServiceFabric/managedclusters/applications",
  "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[parameters('applicationVersion')]",
    "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
  ],
  "identity": {
    "type" : "userAssigned",
    "userAssignedIdentities": {
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]": {}
    }
  },
  "properties": {
    "version": "[parameters('applicationVersion')]",
    "parameters": {
    },
    "managedIdentities": [
      {
        "name" : "[parameters('userAssignedIdentityName')]",
        "principalId" : "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName')), '2018-11-30').principalId]"
      }
    ]
  }
}
```

위의 예에서 사용자 할당 ID의 리소스 이름은 애플리케이션의 관리 ID의 식별 이름으로 사용됩니다. 다음 예에서는 실제 식별 이름이 "AdminUser"라고 가정합니다.

### <a name="application-package"></a>애플리케이션 패키지

1. Azure Resource Manager 템플릿의 `managedIdentities` 섹션에 정의된 각 ID에 대해 **Principals** 섹션 아래의 애플리케이션 매니페스트에 `<ManagedIdentity>` 태그를 추가합니다. `Name` 특성은 `managedIdentities` 섹션에 정의된 `name` 속성과 일치해야 합니다.

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. **ServiceManifestImport** 섹션에서 관리 ID를 사용하는 서비스에 대한 **IdentityBindingPolicy** 를 추가합니다. 이 정책은 `AdminUser` ID를 나중에 서비스 매니페스트에 추가해야 하는 서비스별 ID 이름에 매핑합니다.

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. 서비스 매니페스트를 업데이트하여 애플리케이션 매니페스트의 `IdentityBindingPolicy`에서 `ServiceIdentityRef`와 일치하는 이름으로 **Resources** 섹션 내에 **ManagedIdentity** 를 추가합니다.

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID

### <a name="application-template"></a>애플리케이션 템플릿

시스템 할당 관리 ID를 사용하여 애플리케이션을 사용하도록 설정하려면 아래 예제와 같이 **systemAssigned** 형식이 있는 **ID** 속성을 애플리케이션 리소스에 추가합니다.

```json
    {
      "apiVersion": "2021-05-01",
      "type": "Microsoft.ServiceFabric/managedclusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
      ],
      "identity": {
        "type" : "systemAssigned"
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        }
      }
    }
```
이 속성은 해당 리소스가 암시적(`system assigned`) 관리 ID를 포함해야 한다고 Azure Resource Manager 및 관리 ID와 Service Fabric 리소스 공급자에 각각 선언합니다.

### <a name="application-and-service-package"></a>애플리케이션 및 서비스 패키지

1. 애플리케이션 매니페스트를 업데이트하여 **ManagedIdentity** 요소를 아래와 같이 단일 항목을 포함하는 **보안 주체** 섹션에 추가합니다.

    **ApplicationManifest.xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    애플리케이션을 구성하는 서비스에 대한 추가 할당을 위해 애플리케이션에 리소스로 할당된 ID를 식별 이름으로 매핑합니다. 

2. 관리 ID가 할당되는 서비스에 해당하는 **ServiceManifestImport** 섹션에서 다음과 같이 **IdentityBindingPolicy** 요소를 추가합니다.

    **ApplicationManifest.xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    해당 요소는 애플리케이션의 ID를 서비스에 할당합니다. 이렇게 할당하지 않으면 서비스에서 애플리케이션의 ID에 액세스할 수 없습니다. 위의 코드 조각에서 `SystemAssigned` ID(예약된 키워드)는 식별 이름 `WebAdmin` 아래의 서비스 정의에 매핑됩니다.

3. 서비스 매니페스트를 업데이트하여 애플리케이션 매니페스트의 `IdentityBindingPolicy` 정의의 `ServiceIdentityRef` 설정 값과 일치하는 이름이 포함된 **리소스** 섹션 내에 **ManagedIdentity** 요소를 추가합니다.

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    위에 설명된 대로 서비스에 대한 ID의 동등한 매핑이지만 서비스 정의의 관점을 보여 줍니다. 여기서 ID는 애플리케이션 매니페스트에 선언된 대로 식별 이름(`WebAdmin`)으로 참조됩니다.

## <a name="next-steps"></a>다음 단계
* [Service Fabric 관리형 클러스터에서 Azure 리소스에 대한 Service Fabric 애플리케이션의 관리 ID 액세스 권한 부여](how-to-managed-cluster-grant-access-other-resources.md)
* [서비스 코드에서 Service Fabric 애플리케이션의 관리 ID 활용](how-to-managed-identity-service-fabric-app-code.md)
