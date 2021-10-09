---
title: Azure Arc에 Azure API Management 게이트웨이 배포
description: Azure Arc를 사용하여 자체 호스팅 Azure API Management 게이트웨이를 배포합니다.
author: dlepow
ms.author: danlep
ms.service: api-management
ms.topic: article
ms.date: 05/25/2021
ms.openlocfilehash: ee2b0fc20111fddd55a987bef4d454d5b63b8e4e
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129708485"
---
# <a name="deploy-an-azure-api-management-gateway-on-azure-arc-preview"></a>Azure Arc에 Azure API Management 게이트웨이 배포(미리 보기)

[Kubernetes에서](../azure-arc/kubernetes/overview.md)azure API Management와 azure arc를 통합 하 여 [Azure arc 사용 Kubernetes 클러스터에서](../azure-arc/kubernetes/extensions.md)API Management 게이트웨이 구성 요소를 확장으로 배포할 수 있습니다. 

Azure Arc 사용 Kubernetes 클러스터에 API Management 게이트웨이를 배포 하면 하이브리드 및 다중 클라우드 환경에 대 한 API Management 지원이 확장 됩니다. 클러스터 확장을 사용 하 여 배포를 사용 하도록 설정 하 여 Azure Arc 사용 클러스터에 대 한 정책을 관리 하 고 적용 하는 것이 일관 된 환경을 제공 합니다.

[!INCLUDE [preview](./includes/preview/preview-callout-self-hosted-gateway-azure-arc.md)]

> [!NOTE]
> 자체 호스팅 게이트웨이를 [Kubernetes에 직접](./how-to-deploy-self-hosted-gateway-azure-kubernetes-service.md) 배포할 수도 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* [지원되는 Azure Arc 지역](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc) 내에서 [Kubernetes 클러스터를 연결](../azure-arc/kubernetes/quickstart-connect-cluster.md)합니다.
* `k8s-extension` Azure CLI 확장을 설치합니다.

    ```azurecli
    az extension add --name k8s-extension
    ```
    `k8s-extension` 모듈을 이미 설치한 경우 최신 버전으로 업데이트합니다.

    ```azurecli
    az extension update --name k8s-extension
    ```
* [Azure API Management 인스턴스 만들기](./get-started-create-service-instance.md)
* [Azure API Management 인스턴스에서 게이트웨이 리소스 프로비전](./api-management-howto-provision-self-hosted-gateway.md)

## <a name="deploy-the-api-management-gateway-extension-using-azure-cli"></a>Azure CLI를 사용하여 API Management 게이트웨이 확장 배포

1. Azure Portal에서 API Management 인스턴스로 이동합니다.
1. 측면 탐색 메뉴에서 **게이트웨이** 를 선택합니다.
1. 목록에서 프로비전된 게이트웨이 리소스를 선택하여 엽니다.
1. 프로비전된 게이트웨이 리소스의 측면 탐색 메뉴에서 **배포** 를 클릭합니다.
1. 다음 단계에 대한 **토큰** 및 **구성 URL** 값을 기록해 둡니다.
1. Azure CLI에서 `az k8s-extension create` 명령을 사용하여 게이트웨이 확장을 배포합니다. `token` 및 `configuration URL` 값을 입력합니다.
    * 다음 예제에서는 `service.type='LoadBalancer'` 확장 구성을 사용합니다. [사용 가능한 확장 구성](#available-extension-configurations)을 참조하세요.

    ```azurecli
    az k8s-extension create --cluster-type connectedClusters --cluster-name <cluster-name> \
      --resource-group <rg-name> --name <extension-name> --extension-type Microsoft.ApiManagement.Gateway \
      --scope namespace --target-namespace <namespace> \
      --configuration-settings gateway.endpoint='<Configuration URL>' \
      --configuration-protected-settings gateway.authKey='<token>' \
      --configuration-settings service.type='LoadBalancer' --release-train preview
    ```

    > [!TIP]
    > `authKey`에 대한 `-protected-` 플래그는 선택 사항이지만 권장합니다. 

1. 다음 CLI 명령을 사용하여 배포 상태를 확인합니다.
    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <rg-name> --name <extension-name>
    ```
1. **게이트웨이** 목록으로 다시 이동하여 게이트웨이 상태가 노드 수를 포함하는 녹색 확인 표시를 표시하는지 확인합니다. 이 상태는 배포된 자체 호스팅 게이트웨이 Pod를 의미합니다.
    * API Management 서비스와 성공적으로 통신하고 있습니다.
    * 일반 "하트비트"를 갖습니다.

## <a name="deploy-the-api-management-gateway-extension-using-azure-portal"></a>Azure Portal을 사용하여 API Management 게이트웨이 확장 배포

1. Azure Portal에서 Azure Arc 연결 클러스터로 이동 합니다.
1. 왼쪽 메뉴에서 **확장(미리 보기)**  >  **+ 추가** > **API Management 게이트웨이(미리 보기)** 를 선택합니다.
1. **만들기** 를 선택합니다.
1. **API Management 게이트웨이 설치** 창에서 게이트웨이 확장을 구성합니다.
    * API Management 인스턴스에 대한 구독 및 리소스 그룹을 선택합니다.
    * **게이트웨이 세부 정보** 에서 **API Management 인스턴스** 및 **게이트웨이 이름** 을 선택합니다. 확장에 대한 **네임스페이스** 범위를 입력하고 필요에 따라 API Management 서비스 계층에서 지원되는 경우 여러 **복제본** 을 입력합니다.
    * **Kubernetes 구성** 에서 클러스터에 대한 기본 구성 또는 다른 구성을 선택합니다. 옵션은 [사용 가능한 확장 구성](#available-extension-configurations)을 참조하세요.

    :::image type="content" source="./media/how-to-deploy-self-hosted-gateway-azure-arc/deploy-gateway-extension-azure-arc.png" alt-text="Azure Portal에서 확장을 배포하는 스크린샷":::

1. **모니터링** 탭에서 필요에 따라 모니터링을 사용하도록 설정하여 메트릭 추적 요청을 게이트웨이 및 백 엔드에 업로드합니다. 사용하도록 설정된 경우 기존 **Log Analytics** 작업 영역을 선택합니다.
1. **검토 + 설치** 를 선택한 다음, **설치** 를 선택합니다.

## <a name="available-extension-configurations"></a>사용 가능한 확장 구성

다음 확장 구성이 **필요합니다**.

| 설정 | 설명 |
| ------- | ----------- | 
| `gateway.endpoint` | 게이트웨이 엔드포인트의 구성 URL입니다. |
| `gateway.authKey` | 게이트웨이에 액세스하기 위한 토큰입니다. | 
| `service.type` | 게이트웨이에 대한 Kubernetes 서비스 구성: `LoadBalancer`, `NodePort` 또는 `ClusterIP` |

### <a name="log-analytics-settings"></a>Log Analytics 설정

자체 호스팅 게이트웨이의 모니터링을 사용하도록 설정하려면 다음 Log Analytics 설정을 구성합니다.

| 설정 | 설명 |
| ------- | ----------- | 
| `monitoring.customResourceId` | API Management 인스턴스의 Azure Resource Manager 리소스 ID |
| `monitoring.workspaceId` | Log Analytics의 작업 영역 ID | 
| `monitoring.ingestionKey` | Log Analytics의 수집 키가 포함된 비밀 |

> [!NOTE]
> Log Analytics를 사용하도록 설정하지 않은 경우: 
> 1. [Log Analytics 작업 영역 만들기](../azure-monitor/logs/quick-create-workspace.md) 빠른 시작을 안내합니다. 
> 1. [Log Analytics 에이전트 설정](../azure-monitor/agents/log-analytics-agent.md)을 찾을 수 있는 위치를 알아봅니다.

## <a name="next-steps"></a>다음 단계

* 자체 호스팅 게이트웨이에 대한 자세한 내용은 [Azure API Management 자체 호스팅 게이트웨이 개요](self-hosted-gateway-overview.md)를 참조하세요.
* 모든 [Azure Arc 사용 Kubernetes 확장](../azure-arc/kubernetes/extensions.md)을 검색 합니다. 
* [Azure Arc 사용 Kubernetes](../azure-arc/kubernetes/overview.md)에 대해 자세히 알아보세요.
