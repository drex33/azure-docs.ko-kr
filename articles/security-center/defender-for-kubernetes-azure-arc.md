---
title: Azure Defender for Kubernetes를 사용하여 하이브리드 및 다중 클라우드 Kubernetes 배포 보호
description: 온-프레미스 및 다중 클라우드 Kubernetes 클러스터에서 Azure Defender for Kubernetes 사용
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 09/14/2021
ms.author: memildin
ms.openlocfilehash: 2fc8c04cbe86737d924a1e61e8a2b3808082b7c9
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129351726"
---
# <a name="defend-azure-arc-enabled-kubernetes-clusters-running-in-on-premises-and-multi-cloud-environments"></a>온-프레미스 및 다중 클라우드 환경에서 실행되는 Azure Arc 지원 Kubernetes 클러스터 방어

**Azure Defender for Kubernetes 클러스터 확장** 은 Azure Kubernetes Service 클러스터에 제공되는 것과 동일한 위협 탐지 기능을 사용하여 온-프레미스 클러스터를 방어할 수 있습니다. 클러스터에서 [Azure Arc 지원 Kubernetes](../azure-arc/kubernetes/overview.md)를 사용하도록 설정하고 이 페이지에 설명된 대로 확장을 배포합니다. 

또한 확장을 사용하여 관리되는 Kubernetes 서비스가 아닌 다른 클라우드 공급자의 Kubernetes 클러스터를 보호할 수 있습니다.

> [!TIP]
> [GitHub의 설치 예제](https://aka.ms/kubernetes-extension-installation-examples)에서 설치 프로세스에 도움이 되는 몇 가지 샘플 파일이 있습니다.

## <a name="availability"></a>가용성

| 양상 | 세부 정보 |
|--------|---------|
| 릴리스 상태 | **미리 보기**<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
| 필요한 역할 및 권한 | [보안 관리자](../role-based-access-control/built-in-roles.md#security-admin)는 경고를 해제할 수 있습니다.<br>[보안 읽기 권한자](../role-based-access-control/built-in-roles.md#security-reader)는 발견 사항을 볼 수 있습니다. |
| 가격 책정 | 무료 (미리 보기 중) |
| 지원되는 Kubernetes 배포 | [Azure Stack HCI에서 Azure Kubernetes Service](/azure-stack/aks-hci/overview)<br>[Kubernetes](https://kubernetes.io/docs/home/)<br> [AKS 엔진](https://github.com/Azure/aks-engine)<br> [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)<br> [Red Hat OpenShift](https://www.openshift.com/learn/topics/kubernetes/)(버전 4.6 이상)<br> [VMware Tanzu Kubernetes 그리드](https://tanzu.vmware.com/kubernetes-grid)<br> [Rancher Kubernetes Engine](https://rancher.com/docs/rke/latest/en/) |
| 제한 사항 | Azure Arc 지원 Kubernetes 및 Azure Defender 확장은 Google Kubernetes Engine 및 탄력적 Kubernetes 서비스와 같은 관리되는 Kubernetes 제품을 **지원하지 않습니다**. [Azure Defender는 기본적으로 AKS(Azure Kubernetes Service)에서 사용할 수 있으며](defender-for-kubernetes-introduction.md), 클러스터를 Azure Arc에 연결할 필요가 없습니다. |
| 환경 및 지역 | 이 확장에 대한 가용성은 [Azure Arc 지원 Kubernetes](../azure-arc/kubernetes/overview.md)와 동일합니다.|

## <a name="architecture-overview"></a>아키텍처 개요

AKS가 아닌 모든 Kubernetes 클러스터의 경우 클러스터를 Azure Arc에 연결해야 합니다. 일단 연결되면 Azure Defender for Kubernetes를 [Azure Arc 지원 Kubernetes](../azure-arc/kubernetes/overview.md) 리소스에 [클러스터 확장](../azure-arc/kubernetes/extensions.md)으로 배포할 수 있습니다.

확장 구성 요소는 클러스터의 모든 컨트롤 플레인 노드에서 Kubernetes 감사 로그 데이터를 수집하고 추가 분석을 위해 클라우드의 Azure Defender for Kubernetes 백 엔드로 보냅니다. 확장은 데이터 파이프라인으로 사용되는 Log Analytics 작업 영역에 등록되지만 감사 로그 데이터는 Log Analytics 작업 영역에 저장되지 않습니다.

이 다이어그램에서는 Azure Defender for Kubernetes와 Azure Arc 지원 Kubernetes 클러스터 간의 상호 작용을 보여 줍니다.

:::image type="content" source="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png" alt-text="고급 아키텍처 다이어그램에서는 Azure Defender for Kubernetes와 Azure Arc 지원 Kubernetes 클러스터 간의 상호 작용을 보여 줍니다." lightbox="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png":::

## <a name="prerequisites"></a>필수 구성 요소

확장을 배포 하기 전에 다음을 확인 합니다.
- [Kubernetes 클러스터를 Azure Arc에 커넥트](../azure-arc/kubernetes/quickstart-connect-cluster.md)
- [일반 클러스터 확장 설명서에 나열 된 필수](../azure-arc/kubernetes/extensions.md#prerequisites)구성 요소를 완료 합니다.
- [투구 3](https://helm.sh/docs/intro/install) 버전 3.7.0 이상
- 아웃 바운드 액세스를 위해 다음 끝점에서 **포트 443** 를 구성 합니다.
    - Azure Government 클라우드의 클러스터:
        - *.ods.opinsights.azure.us
        - *.oms.opinsights.azure.us
        - :::no-loc text="login.microsoftonline.us":::
    - 다른 Azure 클라우드 배포의 클러스터:
        - *.ods.opinsights.azure.com
        - *.oms.opinsights.azure.com
        - :::no-loc text="login.microsoftonline.com":::

## <a name="deploy-the-azure-defender-extension"></a>Azure Defender 확장 배포

다양한 방법을 사용하여 Azure Defender 확장을 배포할 수 있습니다. 자세한 단계는 관련 탭을 선택합니다.

### <a name="azure-portal"></a>[**Azure portal**](#tab/k8s-deploy-asc)

### <a name="use-the-fix-button-from-the-security-center-recommendation"></a>Security Center 권장 사항에서 수정 단추 사용

Azure Security Center의 전용 권장 사항은 다음을 제공합니다.

- Defender for Kubernetes 확장이 배포된 클러스터에 대한 **가시성**
- 확장 없이 해당 클러스터에 배포하는 **수정** 단추

1. Azure Security Center의 권장 사항 페이지에서 **Azure Defender 사용** 보안 제어를 엽니다.

1. 필터를 사용하여 **Azure Arc 지원 Kubernetes 클러스터에 Azure Defender의 확장이 설치되어 있어야 합니다** 라는 권장 사항을 찾습니다.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Arc 지원 Kubernetes 클러스터용 Azure Defender 확장 배포에 대한 Azure Security Center의 권장 사항" lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

    > [!TIP]
    > 작업 열에서 **수정** 아이콘을 확인합니다.

1. 확장을 선택하여 정상 및 비정상 리소스(확장을 포함하거나 포함하지 않는 클러스터)의 세부 사항을 확인합니다.

1. 비정상 리소스 목록에서 클러스터를 선택하고 **재구성** 을 선택하여 수정 옵션이 있는 창을 엽니다.

1. 관련 Log Analytics 작업 영역을 선택하고 **x 리소스 재구성** 을 선택합니다.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/security-center-deploy-extension.gif" alt-text="Security Center의 수정 옵션을 사용하여 Azure Arc용 Azure Defender 확장을 배포합니다.":::


### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-deploy-cli)

### <a name="use-azure-cli-to-deploy-the-azure-defender-extension"></a>Azure CLI를 사용하여 Azure Defender 확장 배포

1. Azure에 로그인합니다.

    ```azurecli
    az login
    az account set --subscription <your-subscription-id>
    ```

    > [!IMPORTANT]
    > 클러스터를 Azure Arc에 연결하는 데 사용된 것과 동일한 구독 ID를 ``<your-subscription-id>``에 대해 사용해야 합니다.

1. 다음 명령을 실행하여 Azure Arc 지원 Kubernetes 클러스터 위에 확장을 배포합니다.

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group> --extension-type microsoft.azuredefender.kubernetes
    ```

    Azure Defender 확장 유형에서 지원되는 모든 구성 설정에 대한 설명은 다음과 같습니다.

    | 속성 | Description |
    |----------|-------------|
    | logAnalyticsWorkspaceResourceID | **선택 사항입니다**. 사용자 고유의 Log Analytics 작업 영역에 대한 전체 리소스 ID입니다.<br>제공되지 않은 경우 해당 지역의 기본 작업 영역이 사용됩니다.<br><br>전체 리소스 ID를 가져오려면 다음 명령을 실행하여 구독의 작업 영역 목록을 기본 JSON 형식으로 표시합니다.<br>```az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json```<br><br>Log Analytics 작업 영역 리소스 ID의 구문은 다음과 같습니다.<br>/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.OperationalInsights/workspaces/{your-workspace-name}. <br>[Log Analytics 작업 영역에 대해 자세히 알아봅니다](../azure-monitor/logs/data-platform-logs.md#log-analytics-and-workspaces). |
    | auditLogPath |**선택 사항입니다**. 감사 로그 파일에 대한 전체 경로입니다.<br>제공되지 않은 경우 기본 경로 ``/var/log/kube-apiserver/audit.log``가 사용됩니다.<br>AKS 엔진의 경우 표준 경로는 ``/var/log/kubeaudit/audit.log``입니다. |

    아래 명령은 모든 선택적 필드를 사용하는 예제를 보여 줍니다.

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --extension-type microsoft.azuredefender.kubernetes --configuration-settings logAnalyticsWorkspaceResourceID=<log-analytics-workspace-resource-id> auditLogPath=<your-auditlog-path>
    ```

### <a name="resource-manager"></a>[**리소스 관리자**](#tab/k8s-deploy-resource-manager)

### <a name="use-azure-resource-manager-to-deploy-the-azure-defender-extension"></a>Azure Resource Manager를 사용하여 Azure Defender 확장 배포

Azure Resource Manager를 사용하여 Azure Defender 확장을 배포하려면 구독에 Log Analytics 작업 영역이 필요합니다. [Log Analytics 작업 영역](../azure-monitor/logs/data-platform-logs.md#log-analytics-and-workspaces)에 대해 자세히 알아봅니다.

Resource Manager 템플릿에서 **azure-defender-extension-arm-template.json** 을 Security Center의 [설치 예제](https://aka.ms/kubernetes-extension-installation-examples)에서 사용할 수 있습니다.

> [!TIP]
> Resource Manager 템플릿을 처음 접하는 경우 [Azure Resource Manager 템플릿이란?](../azure-resource-manager/templates/overview.md)을 참조하세요.

### <a name="rest-api"></a>[**REST API**](#tab/k8s-deploy-api)

### <a name="use-rest-api-to-deploy-the-azure-defender-extension"></a>REST API를 사용하여 Azure Defender 확장 배포 

REST API를 사용하여 Azure Defender 확장을 배포하려면 구독에 Log Analytics 작업 영역이 필요합니다. [Log Analytics 작업 영역](../azure-monitor/logs/data-platform-logs.md#log-analytics-and-workspaces)에 대해 자세히 알아봅니다.

> [!TIP]
> API를 사용하여 Azure Defender 확장을 배포하는 가장 간단한 방법은 Security Center의 [설치 예제](https://aka.ms/kubernetes-extension-installation-examples)에 제공된 **Postman Collection JSON** 예제를 사용하는 것입니다.
- Postman Collection JSON을 수정하거나 REST API를 사용하여 확장을 수동으로 배포하려면 다음 PUT 명령을 실행합니다.

    ```rest
    PUT https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

    매개 변수에 대한 설명은 다음과 같습니다.

    | 이름            | In(다음 안에)   | 필수 | Type   | 설명                                  |
    |-----------------|------|----------|--------|----------------------------------------------|
    | 구독 ID | 경로 | True     | 문자열 | Azure Arc 지원 Kubernetes 리소스의 구독 ID |
    | 리소스 그룹  | 경로 | True     | 문자열 | Azure Arc 지원 Kubernetes 리소스를 포함하는 리소스 그룹의 이름 |
    | 클러스터 이름    | 경로 | True     | 문자열 | Azure Arc 지원 Kubernetes 리소스의 이름  |


    **인증** 을 위해 헤더에는 다른 Azure API와 마찬가지로 전달자 토큰이 있어야 합니다. 전달자 토큰을 가져오려면 다음 명령을 실행합니다.

    `az account get-access-token --subscription <your-subscription-id>` 메시지 본문에 대해 다음 구조를 사용합니다.
    ```json
    { 
    "properties": { 
        "extensionType": "microsoft.azuredefender.kubernetes",
    "con    figurationSettings": { 
            "logAnalytics.workspaceId":"YOUR-WORKSPACE-ID"
        // ,    "auditLogPath":"PATH/TO/AUDITLOG"
        },
        "configurationProtectedSettings": {
            "logAnalytics.key":"YOUR-WORKSPACE-KEY"
        }
        }
    } 
    ```

    속성에 대한 설명은 다음과 같습니다.

    | 속성 | Description | 
    | -------- | ----------- |
    | logAnalytics.workspaceId | Log Analytics 리소스의 작업 영역 ID |
    | logAnalytics.key         | Log Analytics 리소스의 키 | 
    | auditLogPath             | **선택 사항입니다**. 감사 로그 파일에 대한 전체 경로입니다. 기본값은 ``/var/log/kube-apiserver/audit.log`` |

---

## <a name="verify-the-deployment"></a>배포 확인

클러스터에 Azure Defender 확장이 설치되어 있는지 확인하려면 아래 탭 중 하나에 있는 단계를 따릅니다.

### <a name="azure-portal---security-center"></a>[**Azure Portal - Security Center**](#tab/k8s-verify-asc)

### <a name="use-security-center-recommendation-to-verify-the-status-of-your-extension"></a>Security Center 권장 사항을 사용하여 확장의 상태를 확인합니다.

1. Azure Security Center의 권장 사항 페이지에서 **Azure Defender 사용** 보안 제어를 엽니다.

1. **Azure Arc 지원 Kubernetes 클러스터에 Azure Defender의 확장이 설치되어 있어야 합니다** 라는 권장 사항을 선택합니다.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Arc 지원 Kubernetes 클러스터용 Azure Defender 확장 배포에 대한 Azure Security Center의 권장 사항" lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

1. 확장을 배포한 클러스터가 **정상** 으로 표시되는지 확인합니다.


### <a name="azure-portal---azure-arc"></a>[**Azure Portal - Azure Arc**](#tab/k8s-verify-arc)

### <a name="use-the-azure-arc-pages-to-verify-the-status-of-your-extension"></a>Azure Arc 페이지를 사용하여 확장의 상태를 확인합니다.

1. Azure Portal에서 **Azure Arc** 를 엽니다.
1. 인프라 목록에서 **Kubernetes 클러스터** 를 선택한 다음, 특정 클러스터를 선택합니다.
1. 확장 페이지를 엽니다. 클러스터의 확장이 나열됩니다. **설치 상태** 열을 확인하여 Azure Defender 확장이 올바르게 설치되었는지 확인합니다.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png" alt-text="Kubernetes 클러스터에 설치된 모든 확장의 상태를 확인하기 위한 Azure Arc 페이지" lightbox="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png":::

1. 자세한 내용은 확장을 선택합니다.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-details-page.png" alt-text="Kubernetes 클러스터의 Azure Arc 확장에 대한 전체 세부 정보입니다.":::


### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-verify-cli)

### <a name="use-azure-cli-to-verify-that-the-extension-is-deployed"></a>Azure CLI를 사용하여 확장이 배포되었는지 확인합니다.

1. Azure CLI에서 다음 명령을 실행합니다.

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

1. 응답에서 “extensionType”: “microsoft.azuredefender.kubernetes” 및 “installState”: “Installed”를 찾습니다.

    > [!NOTE]
    > 처음 몇 분 동안 “installState”: “Pending”이 표시될 수 있습니다.
    
1. 상태가 **설치됨** 으로 표시되는 경우 클러스터를 가리키는 `kubeconfig` 파일이 있는 머신에서 다음 명령을 실행하여 “azuredefender-XXXXX”라는 Pod가 ‘실행 중’ 상태인지 확인합니다.
    
    ```console
    kubectl get pods -n azuredefender
    ```

### <a name="rest-api"></a>[**REST API**](#tab/k8s-verify-api)

### <a name="use-the-rest-api-to-verify-that-the-extension-is-deployed"></a>REST API를 사용하여 확장이 배포되었는지 확인합니다.

성공적으로 배포되었는지 확인하거나 언제든지 확장의 상태를 확인하려면 다음을 수행합니다.

1. 다음 GET 명령을 실행합니다.

    ```rest
    GET https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

1. 응답에서 “installState”: “Installed”에 대해 “extensionType”: “microsoft.azuredefender.kubernetes”를 찾습니다.

    > [!TIP]
    > 처음 몇 분 동안 “installState”: “Pending”이 표시될 수 있습니다.
    
1. 상태가 **설치됨** 으로 표시되는 경우 클러스터를 가리키는 `kubeconfig` 파일이 있는 머신에서 다음 명령을 실행하여 “azuredefender-XXXXX”라는 Pod가 ‘실행 중’ 상태인지 확인합니다.
    
    ```console
    kubectl get pods -n azuredefender
    ```
---

## <a name="simulate-security-alerts-from-azure-defender-for-kubernetes"></a>Azure Defender for Kubernetes에서 보안 경고 시뮬레이트

지원되는 경고의 전체 목록은 [Azure Security Center의 모든 보안 경고에 대한 참조 테이블](alerts-reference.md#alerts-k8scluster)에서 확인할 수 있습니다.

1. Azure Defender 경고를 시뮬레이트하려면 다음 명령을 실행합니다.

    ```console
    kubectl get pods --namespace=asc-alerttest-662jfi039n
    ```

    예상되는 응답은 “리소스를 찾을 수 없음”입니다.

    30분 이내에 Azure Defender는 이 작업을 검색하고 보안 경고를 트리거합니다.

1. Azure Portal에서 Azure Security Center의 보안 경고 페이지를 열고 관련 리소스에 대한 경고를 찾습니다.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png" alt-text="Azure Defender for Kubernetes의 샘플 경고입니다." lightbox="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png":::

## <a name="removing-the-azure-defender-extension"></a>Azure Defender 확장 제거

아래 탭에 설명된 대로 Azure Portal, Azure CLI 또는 REST API를 사용하여 확장을 제거할 수 있습니다.

### <a name="azure-portal---arc"></a>[**Azure Portal - Arc**](#tab/k8s-remove-arc)

### <a name="use-azure-portal-to-remove-the-extension"></a>Azure Portal을 사용하여 확장 제거

1. Azure Portal에서 Azure Arc를 엽니다.
1. 인프라 목록에서 **Kubernetes 클러스터** 를 선택한 다음, 특정 클러스터를 선택합니다.
1. 확장 페이지를 엽니다. 클러스터의 확장이 나열됩니다.
1. 클러스터를 선택하고 **제거** 를 선택합니다.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png" alt-text="Arc 지원 Kubernetes 클러스터에서 확장을 제거합니다." lightbox="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png":::

### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-remove-cli)

### <a name="use-azure-cli-to-remove-the-azure-defender-extension"></a>Azure CLI를 사용하여 Azure Defender 확장 제거

1. 다음 명령을 사용하여 Azure Defender for Kubernetes Arc 확장을 제거합니다.

    ```azurecli
    az login
    az account set --subscription <subscription-id>
    az k8s-extension delete --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes --yes
    ```

    확장을 제거하는 데 몇 분 정도 걸릴 수 있습니다. 성공 여부를 확인하기 전에 기다리는 것이 좋습니다.

1. 확장이 성공적으로 제거되었는지 확인하려면 다음 명령을 실행합니다.

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

    Azure Resource Manager에서 확장 리소스를 삭제하는 데 지연이 없어야 합니다. 그런 다음, 클러스터를 가리키는 `kubeconfig` 파일로 다음 명령을 실행하여 클러스터에 “azuredefender-XXXXX”라는 Pod가 없는지 확인합니다. 

    ```console
    kubectl get pods -n azuredefender
    ```

    Pod를 삭제하는 데 몇 분 정도 걸릴 수 있습니다.

### <a name="rest-api"></a>[**REST API**](#tab/k8s-remove-api)

### <a name="use-rest-api-to-remove-the-azure-defender-extension"></a>REST API를 사용하여 Azure Defender 확장 제거 

REST API를 사용하여 확장을 제거하려면 다음 DELETE 명령을 실행합니다.

```rest
DELETE https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
```

| Name            | In(다음 안에)   | 필수 | Type   | 설명                                           |
|-----------------|------|----------|--------|-------------------------------------------------------|
| 구독 ID | 경로 | True     | 문자열 | Arc 지원 Kubernetes 클러스터의 구독 ID |
| 리소스 그룹  | 경로 | True     | 문자열 | Arc 지원 Kubernetes 클러스터의 리소스 그룹  |
| 클러스터 이름    | 경로 | True     | 문자열 | Arc 지원 Kubernetes 클러스터의 이름            |

**인증** 을 위해 헤더에는 다른 Azure API와 마찬가지로 전달자 토큰이 있어야 합니다. 전달자 토큰을 가져오려면 다음 명령을 실행합니다.

```azurecli
az account get-access-token --subscription <your-subscription-id>
```

요청을 완료하는 데는 몇 분 정도 걸릴 수 있습니다.

---

## <a name="next-steps"></a>다음 단계

이 페이지에서는 Azure Arc 지원 Kubernetes 클러스터에 대한 Azure Defender 확장을 배포하는 방법을 설명했습니다. 다음 페이지에서 Azure Defender 및 Azure Security Center의 컨테이너 보안 기능에 대해 자세히 알아보세요.

- [Security Center의 컨테이너 보안](container-security.md)
- [Azure Defender for Kubernetes 소개](defender-for-kubernetes-introduction.md)
- [Kubernetes 워크로드 보호](kubernetes-workload-protections.md)
