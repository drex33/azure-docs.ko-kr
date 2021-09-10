---
title: Azure Arc 지원 Kubernetes의 사용자 지정 위치 만들기 및 관리
services: azure-arc
ms.service: azure-arc
ms.date: 05/25/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
ms.custom: references_regions, devx-track-azurecli
description: 사용자 지정 위치를 사용하여 Azure Arc 지원 Kubernetes 클러스터에 Azure PaaS 서비스 배포
ms.openlocfilehash: a4586f6f527bd98f0f347e51c787f2bcda7c6d8d
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768304"
---
# <a name="create-and-manage-custom-locations-on-azure-arc-enabled-kubernetes"></a>Azure Arc 지원 Kubernetes의 사용자 지정 위치 만들기 및 관리

Azure 위치 확장으로, *사용자 지정 위치* 를 통해 테넌트 관리자는 Azure Arc 지원 Kubernetes 클러스터를 Azure 서비스 인스턴스 배포를 위한 대상 위치로 사용할 수 있습니다. Azure 리소스의 예제에는 Azure Arc 지원 SQL Managed Instance 및 Azure Arc 지원 PostgreSQL Hyperscale이 포함됩니다.

Azure 위치와 마찬가지로 사용자 지정 위치에 대한 액세스 권한이 있는 테넌트 내의 최종 사용자는 회사의 프라이빗 컴퓨팅을 사용하여 리소스를 배포할 수 있습니다.

이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
> * Azure Arc 지원 Kubernetes 클러스터에 사용자 지정 위치를 사용합니다.
> * 클러스터에 Azure 서비스 인스턴스의 Azure 서비스 클러스터 확장을 배포합니다.
> * Azure Arc 지원 Kubernetes 클러스터에 사용자 지정 위치를 만듭니다.

이 기능에 대한 개념적인 개요는 [사용자 지정 위치 - Azure Arc 지원 Kubernetes](conceptual-custom-locations.md) 문서에서 확인할 수 있습니다.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

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

- 기존 [Azure Arc 지원 Kubernetes 연결 클러스터](quickstart-connect-cluster.md)가 있는지 확인합니다.
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

1. 클러스터에 최종적으로 원하는 Azure 서비스 인스턴스의 Azure 서비스 클러스터 확장을 배포합니다.

    * [Azure Arc 지원 Data Services](../data/create-data-controller-direct-cli.md#create-the-arc-data-services-extension)

        > [!NOTE]
        > 인증이 없는 아웃바운드 프록시 및 기본 인증이 있는 아웃바운드 프록시는 Arc 지원 Data Services 클러스터 확장에서 지원됩니다. 신뢰할 수 있는 인증서가 필요한 아웃바운드 프록시는 현재 지원되지 않습니다.


    * [Azure Arc의 Azure App Service](../../app-service/manage-create-arc-environment.md#install-the-app-service-extension)

    * [Kubernetes의 Event Grid](../../event-grid/kubernetes/install-k8s-extension.md)

1. 이후 단계에서 `connectedClusterId`로 참조되는 Azure Arc 지원 Kubernetes 클러스터의 Azure Resource Manager 식별자를 가져옵니다.

    ```azurecli
    az connectedk8s show -n <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. 이후 단계에서 `extensionId`로 참조되는 Azure Arc 지원 Kubernetes 클러스터 위에 배포된 클러스터 확장의 Azure Resource Manager 식별자를 가져옵니다.

    ```azurecli
    az k8s-extension show --name <extensionInstanceName> --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. Azure Arc 지원 Kubernetes 클러스터 및 확장을 참조하여 사용자 지정 위치를 만듭니다.

    ```azurecli
    az customlocation create -n <customLocationName> -g <resourceGroupName> --namespace arc --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionId>
    ```

## <a name="next-steps"></a>다음 단계

- [클러스터 연결](cluster-connect.md)을 사용하여 클러스터에 안전하게 연결
- 확장 설치, 사용자 지정 위치 만들기 및 App Service Kubernetes 환경 만들기에 대한 엔드투엔드 지침은 [Azure Arc의 Azure App Service](../../app-service/overview-arc-integration.md)를 계속 참조하세요. 
- [Kubernetes의 Event Grid](../../event-grid/kubernetes/overview.md)에 대한 Event Grid 토픽 및 이벤트 구독을 만듭니다.
- 현재 사용 가능한 [Azure Arc 지원 Kubernetes 확장](extensions.md#currently-available-extensions)에 대해 자세히 알아봅니다.