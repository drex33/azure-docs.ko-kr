---
title: Azure Arc 지원 Kubernetes에서 사용자 지정 위치 만들기 및 관리
ms.service: azure-arc
ms.date: 10/19/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
ms.custom: references_regions, devx-track-azurecli
description: 사용자 지정 위치를 사용하여 Azure Arc 지원 Kubernetes 클러스터에 Azure PaaS 서비스 배포
ms.openlocfilehash: f241ec384fc9ed7ee96d7415074e009cea486811
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130273823"
---
# <a name="create-and-manage-custom-locations-on-azure-arc-enabled-kubernetes"></a>Azure Arc 지원 Kubernetes에서 사용자 지정 위치 만들기 및 관리

 *사용자 지정 위치는* 테넌트 또는 클러스터 관리자가 Azure Arc 지원 Kubernetes 클러스터를 Azure 서비스 인스턴스를 배포하기 위한 대상 위치로 구성하는 방법을 제공합니다.  Azure Arc 지원 SQL Managed Instance 및 Azure Arc 지원 PostgreSQL 하이퍼스케일과 같은 리소스 Azure Arc 지원 Kubernetes 클러스터에서 사용자 지정 위치는 Azure Arc 지원 Kubernetes 클러스터 내에서 네임스페이스의 추상화입니다. 테넌트 또는 클러스터 관리자는 애플리케이션 개발자 또는 데이터베이스 관리자에게 RBAC(역할 기반 액세스 제어) 권한을 할당하여 사용자 지정 위치에 Azure Arc 지원 SQL Managed Instance, Azure Arc 지원 PostgreSQL 하이퍼스케일 인스턴스 또는 Azure 웹앱과 같은 리소스를 배포할 수 있습니다. 
 
이 기능에 대한 개념적 개요는 [사용자 지정 위치 - Azure Arc 지원 Kubernetes](conceptual-custom-locations.md) 문서에서 확인할 수 있습니다. 

이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
> * Azure Arc 지원 Kubernetes 클러스터에서 사용자 지정 위치를 사용하도록 설정합니다.
> * 사용자 지정 위치를 만듭니다.


## <a name="prerequisites"></a>필수 구성 요소

- 버전 >= 2.16.0으로 [Azure CLI를 설치하거나 업그레이드](/cli/azure/install-azure-cli)합니다.

- 다음과 같은 Azure CLI 확장을 설치합니다.
    - `connectedk8s`(버전 1.1.0 이상)
    - `k8s-extension`(버전 0.2.0 이상)
    - `customlocation`(버전 0.1.0 이상) 
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    az extension add --name customlocation
    ```
    
    `connectedk8s`, `k8s-extension` 및 `customlocation` 확장을 이미 설치한 경우 다음 명령을 사용하여 최신 버전으로 업데이트할 수 있습니다.

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    az extension update --name customlocation
    ```
    >[!NOTE]
    >최신 버전의 CLI 확장을 사용하여 최신 기능을 얻는 것이 좋습니다.  

- `Microsoft.ExtendedLocation`에 대한 완료된 공급자 등록을 확인합니다.
    1. 다음 명령을 입력합니다.
    
        ```azurecli
        az provider register --namespace Microsoft.ExtendedLocation
        ```

    2. 등록 프로세스를 모니터링합니다. 등록은 10분 정도 걸릴 수 있습니다.
    
        ```azurecli
        az provider show -n Microsoft.ExtendedLocation -o table
        ```

        등록된 후에는 `RegistrationState` 상태가 `Registered` 값을 가지게 됩니다.

- 기존 Azure Arc 지원 [Kubernetes 연결 클러스터](quickstart-connect-cluster.md)가 있는지 확인합니다.
    - 버전 1.1.0 이상으로 [에이전트를 업그레이드](agent-upgrade.md#manually-upgrade-agents)합니다.

## <a name="enable-custom-locations-on-cluster"></a>클러스터에서 사용자 지정 위치 사용

Azure AD 사용자 권한으로 Azure CLI에 로그인한 경우 클러스터에서 이 기능을 사용하도록 설정하려면 다음 명령을 실행합니다.

```azurecli
az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features cluster-connect custom-locations
```

서비스 주체를 사용하여 Azure CLI에 로그인한 경우 클러스터에서 이 기능을 사용하도록 설정하려면 다음 단계를 실행합니다.

1. Azure Arc 서비스에서 사용하는 Azure AD 애플리케이션의 개체 ID를 가져옵니다.

    ```azurecli
    az ad sp show --id 'bc313c14-388c-4e7d-a58e-70017303ee3b' --query objectId -o tsv
    ```

1. 위 단계의 `<objectId>` 값을 사용하여 클러스터에서 사용자 지정 위치 기능을 사용하도록 설정합니다.

    ```azurecli
    az connectedk8s enable-features -n <cluster-name> -g <resource-group-name> --custom-locations-oid <objectId> --features cluster-connect custom-locations
    ```

> [!NOTE]
> 1. 사용자 지정 위치 기능은 클러스터 연결 기능에 따라 달라집니다. 따라서 사용자 지정 위치가 작동하려면 두 기능을 모두 사용하도록 설정해야 합니다.
> 2. `kubeconfig` 파일이 기능을 활성화할 클러스터를 가리키는 머신에서 `az connectedk8s enable-features`를 실행해야 합니다.

## <a name="create-custom-location"></a>사용자 지정 위치 만들기

1. 클러스터에 설치하려는 Azure 서비스 인스턴스의 Azure 서비스 클러스터 확장을 배포합니다.

    * [Azure Arc 사용 Data Services](../data/create-data-controller-direct-cli.md#create-the-arc-data-services-extension)

        > [!NOTE]
        > 인증이 없는 아웃바운드 프록시 및 기본 인증을 사용하는 아웃바운드 프록시는 Azure Arc 지원되는 Data Services 클러스터 확장에서 지원됩니다. 신뢰할 수 있는 인증서가 필요한 아웃바운드 프록시는 현재 지원되지 않습니다.


    * [Azure Arc의 Azure App Service](../../app-service/manage-create-arc-environment.md#install-the-app-service-extension)

    * [Kubernetes의 Event Grid](../../event-grid/kubernetes/install-k8s-extension.md)

2. 이후 단계에서 로 참조되는 Azure Arc 지원 Kubernetes 클러스터의 Azure Resource Manager 식별자를 확인합니다. `connectedClusterId`

    ```azurecli
    az connectedk8s show -n <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

3. 이후 단계에서 로 참조되는 Azure Arc 지원 Kubernetes 클러스터 위에 배포된 클러스터 확장의 Azure Resource Manager 식별자를 가져옵니다. `extensionId`

    ```azurecli
    az k8s-extension show --name <extensionInstanceName> --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

4. Azure Arc 지원 Kubernetes 클러스터 및 확장을 참조하여 사용자 지정 위치를 만듭니다.

    ```azurecli
    az customlocation create -n <customLocationName> -g <resourceGroupName> --namespace <name of namespace> --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionIds> 
    ```

**필수 매개 변수**

| 매개 변수 이름 | Description |
|----------------|------------|
| `--name, --n` | 사용자 지정 위치의 이름 |
| `--resource-group, --g` | 사용자 지정 위치의 리소스 그룹  | 
| `--namespace` | 생성되는 사용자 지정 위치에 바인딩된 클러스터의 네임스페이스 |
| `--host-resource-id` | Azure Arc 지원 Kubernetes 클러스터(연결된 클러스터)의 Azure Resource Manager 식별자 |
| `--cluster-extension-ids` | 연결된 클러스터에 설치된 클러스터 확장 인스턴스의 식별자를 Azure Resource Manager. 클러스터 확장 ID의 공백으로 분리된 목록 제공  |

**선택적 매개 변수**

| 매개 변수 이름 | Description |
|--------------|------------|
| `--assign-identity` | 기본값은 `None`입니다. 매개 변수가 "SystemAssigned"로 설정된 경우 [시스템 할당 관리 ID를](../../active-directory/managed-identities-azure-resources/overview.md) 만듭니다. |
| `--location, --l` | Azure에서 사용자 지정 위치 Azure Resource Manager 리소스의 위치입니다. 기본적으로 연결된 클러스터의 위치(또는 Azure 지역)로 설정됩니다. |
| `--tags` | 공백으로 구분된 태그 목록: key[=value] [key[=value] ...]. ''을 사용하여 기존 태그 지우기 |
| `--kubeconfig` | 클러스터의 관리자 Kubeconfig. 클러스터가 AAD 사용하도록 설정되지 않은 클러스터인 경우 파일로 전달해야 합니다. |


## <a name="show-details-of-a-custom-location"></a>사용자 지정 위치의 세부 정보 표시

사용자 지정 위치의 세부 정보 표시

```azurecli
    az customlocation show -n <customLocationName> -g <resourceGroupName> 
```

**필수 매개 변수**

| 매개 변수 이름 | Description |
|----------------|------------|
| `--name, --n` | 사용자 지정 위치의 이름 |
| `--resource-group, --g` | 사용자 지정 위치의 리소스 그룹  | 

## <a name="list-custom-locations"></a>사용자 지정 위치 나열

리소스 그룹의 모든 사용자 지정 위치를 나열합니다.

```azurecli
    az customlocation show -g <resourceGroupName> 
```

**필수 매개 변수**

| 매개 변수 이름 | Description |
|----------------|------------|
| `--resource-group, --g` | 사용자 지정 위치의 리소스 그룹  | 


## <a name="update-a-custom-location"></a>사용자 지정 위치 업데이트

`update`새 태그를 추가하려면 명령을 사용하고, 기존 태그 및 연결된 클러스터 확장을 유지하면서 새 클러스터 확장 ID를 사용자 지정 위치에 연결합니다. `--cluster-extension-ids`, `--tags` , 를  `assign-identity` 업데이트할 수 있습니다. 

```azurecli
    az customlocation update -n <customLocationName> -g <resourceGroupName> --namespace <name of namespace> --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionIds> 
```
**필수 매개 변수**

| 매개 변수 이름 | Description |
|----------------|------------|
| `--name, --n` | 사용자 지정 위치의 이름 |
| `--resource-group, --g` | 사용자 지정 위치의 리소스 그룹  | 
| `--namespace` | 생성되는 사용자 지정 위치에 바인딩된 클러스터의 네임스페이스 |
| `--host-resource-id` | Azure Arc 지원 Kubernetes 클러스터(연결된 클러스터)의 Azure Resource Manager 식별자 |

**선택적 매개 변수**

| 매개 변수 이름 | Description |
|--------------|------------|
| `--assign-identity` | `None`시스템 할당 관리 `"SystemAssigned` [ID를](../../active-directory/managed-identities-azure-resources/overview.md) 사용자 지정 위치에 할당하려는 경우 또는 로 업데이트할 수 있습니다. |
| `--cluster-extension-ids` | 연결된 클러스터에 설치된 클러스터 확장 인스턴스의 Azure Resource Manager 식별자를 제공하여 새 클러스터 확장을 이 사용자 지정 위치에 연결합니다. 클러스터 확장 ID의 공백으로 분리된 목록 제공 |
| `--tags` | 기존 태그 외에도 새 태그를 추가합니다. 공백으로 구분된 태그 목록: key[=value] [key[=value] ...]. |

## <a name="patch-a-custom-location"></a>사용자 지정 위치 패치

`patch`기존 태그, 클러스터 확장 ID를 새 태그, 클러스터 확장 ID로 바꾸려면 명령을 사용합니다. `--cluster-extension-ids`, `assign-identity` , 를 `--tags` 패치할 수 있습니다. 

```azurecli
    az customlocation patch -n <customLocationName> -g <resourceGroupName> --namespace <name of namespace> --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionIds> 
```

**필수 매개 변수**

| 매개 변수 이름 | Description |
|----------------|------------|
| `--name, --n` | 사용자 지정 위치의 이름 |
| `--resource-group, --g` | 사용자 지정 위치의 리소스 그룹  | 

**선택적 매개 변수**

| 매개 변수 이름 | Description |
|--------------|------------|
| `--assign-identity` | `None` `"SystemAssigned` 사용자 지정 위치에 [시스템 할당 관리 id](../../active-directory/managed-identities-azure-resources/overview.md) 를 할당 하려는 경우 또는으로 업데이트할 수 있습니다. |
| `--cluster-extension-ids` | 연결 된 클러스터에 설치 된 클러스터 확장 인스턴스의 Azure Resource Manager 식별자를 제공 하 여 새 클러스터 확장을이 사용자 지정 위치에 연결 합니다. 클러스터 확장 Id의 공백으로 구분 된 목록을 제공 합니다. |
| `--tags` | 기존 태그 외에 새 태그를 추가 합니다. 공백으로 구분 된 태그 목록: 키 [= 값] [키 [= 값] ...]. |

## <a name="delete-a-custom-location"></a>사용자 지정 위치 삭제

 ```azurecli
    az customlocation delete -n <customLocationName> -g <resourceGroupName> --namespace <name of namespace> --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionIds> 
   ```

## <a name="next-steps"></a>다음 단계

- [클러스터 연결](cluster-connect.md)을 사용하여 클러스터에 안전하게 연결
- 확장 설치, 사용자 지정 위치 만들기 및 App Service Kubernetes 환경 만들기에 대한 엔드투엔드 지침은 [Azure Arc의 Azure App Service](../../app-service/overview-arc-integration.md)를 계속 참조하세요. 
- [Kubernetes의 Event Grid](../../event-grid/kubernetes/overview.md)에 대 한 event grid 토픽 및 이벤트 구독을 만듭니다.
- 현재 사용할 수 있는 [Azure Arc 사용 Kubernetes 확장](extensions.md#currently-available-extensions)에 대해 자세히 알아보세요.
