---
title: 리소스 로그 수집 및 분석
description: Azure Container Instances의 컨테이너 그룹에서 리소스 로그 및 이벤트 데이터를 Azure Monitor 로그에 보내는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 07/13/2020
ms.openlocfilehash: 4c43d16c7df7ef54e401966e0c114de4d79cbdac
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123112068"
---
# <a name="container-group-and-instance-logging-with-azure-monitor-logs"></a>Azure Monitor 로그를 사용하여 컨테이너 그룹 및 인스턴스 로깅

Log Analytics 작업 영역은 Azure 리소스뿐만 아니라 온-프레미스 리소스 및 다른 클라우드의 리소스에서도 로그 데이터를 저장 및 쿼리할 수 있는 중앙 집중식 위치를 제공합니다. Azure Container Instances는 Azure Monitor 로그로 로그 및 이벤트 데이터를 전송할 수 있는 기능을 기본 제공합니다.

Azure Monitor 로그로 컨테이너 그룹 로그 및 이벤트 데이터를 전송하려면 컨테이너 그룹을 구성할 때 기존의 Log Analytics 작업 영역 ID와 작업 영역 키를 지정합니다. 

다음 섹션은 로깅 사용 컨테이너 그룹을 만드는 방법과 로그를 쿼리하는 방법에 대해 설명합니다. 작업 영역 ID 및 작업 영역 키를 사용하여 [컨테이너 그룹을 업데이트](container-instances-update.md)하여 로깅을 사용하도록 설정할 수도 있습니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> 현재는 Linux 컨테이너 인스턴스에서 Log Analytics로만 이벤트 데이터를 보낼 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

컨테이너 인스턴스에 로그인을 사용하도록 설정하려면 다음이 필요합니다.

* [Log Analytics 작업 영역](../azure-monitor/logs/quick-create-workspace.md)
* [Azure CLI](/cli/azure/install-azure-cli)(또는 [Cloud Shell](../cloud-shell/overview.md))

## <a name="get-log-analytics-credentials"></a>Log Analytics 자격 증명 가져오기

Azure Container Instances에 Log Analytics 작업 영역에 데이터를 전송할 권한이 있어야 합니다. 이 권한을 부여하고 로깅을 사용하도록 설정하려면 컨테이너 그룹을 만들 때 Log Analytics 작업 영역 ID와 키(기본 키 또는 보조 키 중 하나)를 제공해야 합니다.

로그 분석 작업 영역 ID 및 기본 키를 가져오려면:

1. Azure Portal에서 Log Analytics 작업 영역으로 이동
1. **설정** 에서 **에이전트 관리** 를 선택합니다.
1. 다음을 기록해 둡니다.
   * **작업 영역 ID**
   * **기본 키**

## <a name="create-container-group"></a>컨테이너 그룹 만들기

로그 분석 작업 영역 ID와 기본 키를 알고 있으므로 로깅을 사용하도록 설정된 컨테이너 그룹을 만들 수 있습니다.

다음 예제에서는 단일 [fluentd][fluentd] 컨테이너로 구성된 컨테이너 그룹을 만드는 두 가지 방법인 Azure CLI 및 YAML 템플릿이 있는 Azure CLI를 보여 줍니다. Fluentd 컨테이너는 기본 구성에서 여러 줄의 출력을 생성합니다. 이 출력은 Log Analytics 작업 영역으로 전송되므로 로그를 보고 쿼리하는 방법을 보여주기에 효과적입니다.

### <a name="deploy-with-azure-cli"></a>Azure CLI를 사용하여 배포

Azure CLI를 사용하여 배포하려면 [az container create][az-container-create] 명령에서 `--log-analytics-workspace` 및 `--log-analytics-workspace-key` 매개 변수를 지정합니다. 다음 명령을 실행하기 전에 이전 단계에서 얻은 값으로 두 작업 영역 값을 바꾸고 리소스 그룹 이름을 업데이트합니다.

> [!NOTE]
> 다음 예제에서는 Docker 허브에서 공용 컨테이너 이미지를 가져옵니다. 익명의 풀 요청을 하는 대신 Docker Hub 계정을 사용하여 인증하도록 풀 비밀을 설정하는 것이 좋습니다. 공용 콘텐츠를 사용할 때 신뢰성을 향상시키려면 개인 Azure Container Registry에서 이미지를 가져오고 관리하세요. [공용 이미지 사용에 대해 자세히 알아봅니다](../container-registry/buffer-gate-public-content.md).

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainergroup001 \
    --image fluent/fluentd \
    --log-analytics-workspace <WORKSPACE_ID> \
    --log-analytics-workspace-key <WORKSPACE_KEY>
```

### <a name="deploy-with-yaml"></a>YAML을 사용하여 배포

YAML을 사용하여 컨테이너 그룹을 배포하려는 경우 이 메서드를 사용합니다. 다음 YAML은 단일 컨테이너로 컨테이너 그룹을 만듭니다. 새 파일에 YAML을 복사한 다음, `LOG_ANALYTICS_WORKSPACE_ID` 및 `LOG_ANALYTICS_WORKSPACE_KEY`를 이전 단계에서 구한 값으로 바꿉니다. 파일을 **deploy-aci.yaml** 로 저장합니다.

> [!NOTE]
> 다음 예제에서는 Docker 허브에서 공용 컨테이너 이미지를 가져옵니다. 익명의 풀 요청을 하는 대신 Docker Hub 계정을 사용하여 인증하도록 풀 비밀을 설정하는 것이 좋습니다. 공용 콘텐츠를 사용할 때 신뢰성을 향상시키려면 개인 Azure Container Registry에서 이미지를 가져오고 관리하세요. [공용 이미지 사용에 대해 자세히 알아봅니다](../container-registry/buffer-gate-public-content.md).

```yaml
apiVersion: 2019-12-01
location: eastus
name: mycontainergroup001
properties:
  containers:
  - name: mycontainer001
    properties:
      environmentVariables: []
      image: fluent/fluentd
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
  diagnostics:
    logAnalytics:
      workspaceId: LOG_ANALYTICS_WORKSPACE_ID
      workspaceKey: LOG_ANALYTICS_WORKSPACE_KEY
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

다음으로, 다음 명령을 실행하여 컨테이너 그룹을 배포합니다. `myResourceGroup`은 구독의 리소스 그룹으로 바꿉니다(또는 먼저 "myResourceGroup"이라는 리소스 그룹 만들기).

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

명령 실행 즉시 Azure에서 배포 세부 정보가 포함된 응답이 수신되어야 합니다.

## <a name="view-logs"></a>로그 보기

컨테이너 그룹을 배포한 후 첫 번째 로그 항목이 Azure Portal에 표시되기 까지 몇 분(최대 10분)이 걸릴 수 있습니다. 

`ContainerInstanceLog_CL` 테이블에서 컨테이너 그룹의 로그를 보려면 다음을 수행합니다.

1. Azure Portal에서 Log Analytics 작업 영역으로 이동
1. **일반** 아래에서 **로그** 선택  
1. 다음 쿼리 입력: `ContainerInstanceLog_CL | limit 50`
1. **실행** 을 선택합니다.

쿼리에서 몇 가지 결과를 표시해야 합니다. 처음에 아무 결과도 표시되지 않으면 몇 분 정도 기다린 다음, **실행** 단추를 선택하여 쿼리를 다시 실행합니다. 기본적으로 로그 항목이 **테이블** 형식으로 표시됩니다. 그런 다음, 행을 확장하여 개별 로그 항목의 내용을 볼 수 있습니다.

![Azure Portal의 로그 검색 결과][log-search-01]

## <a name="view-events"></a>이벤트 보기

Azure Portal에서 컨테이너 인스턴스에 대한 이벤트를 볼 수도 있습니다. 이벤트에는 인스턴스가 생성된 시간 및 인스턴스가 시작된 시간이 포함됩니다. `ContainerEvent_CL` 테이블에서 이벤트 데이터를 보려면 다음을 수행합니다.

1. Azure Portal에서 Log Analytics 작업 영역으로 이동
1. **일반** 아래에서 **로그** 선택  
1. 다음 쿼리 입력: `ContainerEvent_CL | limit 50`
1. **실행** 을 선택합니다.

쿼리에서 몇 가지 결과를 표시해야 합니다. 처음에 아무 결과도 표시되지 않으면 몇 분 정도 기다린 다음, **실행** 단추를 선택하여 쿼리를 다시 실행합니다. 기본적으로 항목이 **테이블** 형식으로 표시됩니다. 그런 다음, 행을 확장하여 개별 항목의 콘텐츠를 볼 수 있습니다.

![Azure Portal의 이벤트 검색 결과][log-search-02]

## <a name="query-container-logs"></a>쿼리 컨테이너 로그

Azure Monitor 로그에는 약 수천 줄의 로그 출력에서 정보를 가져오기 위한 광범위한 [쿼리 언어][query_lang]가 포함되어 있습니다.

쿼리의 기본 구조는 원본 테이블(이 문서에서는 `ContainerInstanceLog_CL` 또는 `ContainerEvent_CL`)이며 그 뒤에 파이프 문자(`|`)로 구분된 일련의 연산자가 있습니다. 여러 연산자를 묶어 결과를 구체화하고 고급 기능을 수행할 수 있습니다.

예제 쿼리 결과를 보려면 다음 쿼리를 쿼리 텍스트 상자에 붙여넣고 **실행** 단추를 선택하여 쿼리를 실행합니다. 이 쿼리는 "메시지" 필드에 "warn"이라는 단어가 포함된 모든 로그 항목을 표시합니다.

```query
ContainerInstanceLog_CL
| where Message contains "warn"
```

더 복잡한 쿼리도 지원됩니다. 예를 들어 이 쿼리는 지난 1시간 이내에 생성된 "mycontainergroup001" 컨테이너 그룹의 로그 항목만 표시합니다.

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="log-schema"></a>로그 스키마

> [!NOTE]
> 아래 나열된 열 중 일부는 스키마의 일부로만 존재하며 내보낸 데이터가 로그에 없습니다. 이 열은 아래에 ‘Empty’의 설명과 함께 표시됩니다.

### <a name="containerinstancelog_cl"></a>ContainerInstanceLog_CL

|Column|형식|Description|
|-|-|-|
|Computer|문자열|Empty|
|ContainerGroup_s|문자열|레코드와 연결된 컨테이너 그룹의 이름입니다.|
|ContainerID_s|문자열|레코드와 연결된 컨테이너의 고유 식별자입니다.|
|ContainerImage_s|문자열|레코드와 연결된 컨테이너 이미지의 이름입니다.|
|Location_s|문자열|레코드와 연결된 리소스의 위치입니다.|
|메시지|문자열|해당하는 경우 컨테이너의 메시지입니다.|
|OSType_s|문자열|컨테이너의 기반이 되는 운영 체제의 이름입니다.|
|RawData|문자열|Empty|
|ResourceGroup|문자열|레코드가 연결된 리소스 그룹의 이름입니다.|
|Source_s|문자열|로깅 구성 요소 “LoggingAgent”의 이름입니다.|
|SubscriptionId|문자열|레코드가 연결된 구독의 고유 식별자입니다.|
|TimeGenerated|Datetime|이벤트에 해당하는 요청을 처리한 Azure 서비스에 의해 이벤트가 생성된 타임스탬프입니다.|
|Type|문자열|테이블의 이름입니다.|
|_ResourceId|문자열|레코드가 연결된 리소스의 고유 식별자입니다.|
|_SubscriptionId|문자열|레코드가 연결된 구독의 고유 식별자입니다.|

### <a name="containerevent_cl"></a>ContainerEvent_CL

|Column|형식|Description|
|-|-|-|
|Computer|문자열|Empty|
|ContainerGroupInstanceId_g|문자열|레코드와 연결된 컨테이너 그룹의 고유 식별자입니다.|
|ContainerGroup_s|문자열|레코드와 연결된 컨테이너 그룹의 이름입니다.|
|ContainerName_s|문자열|레코드와 연결된 컨테이너의 이름입니다.|
|Count_d|real|마지막 폴링 이후 이벤트가 발생한 횟수입니다.|
|FirstTimestamp_t|Datetime|이벤트가 처음 발생한 타임스탬프입니다.|
|Location_s|문자열|레코드와 연결된 리소스의 위치입니다.|
|메시지|문자열|해당하는 경우 컨테이너의 메시지입니다.|
|OSType_s|문자열|컨테이너의 기반이 되는 운영 체제의 이름입니다.|
|RawData|문자열|Empty|
|Reason_s|문자열|Empty|
|ResourceGroup|문자열|레코드가 연결된 리소스 그룹의 이름입니다.|
|SubscriptionId|문자열|레코드가 연결된 구독의 고유 식별자입니다.|
|TimeGenerated|Datetime|이벤트에 해당하는 요청을 처리한 Azure 서비스에 의해 이벤트가 생성된 타임스탬프입니다.|
|Type|문자열|테이블의 이름입니다.|
|_ResourceId|문자열|레코드가 연결된 리소스의 고유 식별자입니다.|
|_SubscriptionId|문자열|레코드가 연결된 구독의 고유 식별자입니다.|

## <a name="next-steps"></a>다음 단계

### <a name="azure-monitor-logs"></a>Azure Monitor 로그

Azure Monitor 로그에서 로그를 쿼리하고 경고를 구성하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Monitor 로그의 로그 검색 이해](../azure-monitor/logs/log-query-overview.md)
* [Azure Monitor의 통합 경고](../azure-monitor/alerts/alerts-overview.md)


### <a name="monitor-container-cpu-and-memory"></a>컨테이너 CPU 및 메모리 모니터링

컨테이너 인스턴스 CPU 및 메모리 리소스 모니터링에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Container Instances에서 컨테이너 리소스 모니터링](container-instances-monitor.md)

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png
[log-search-02]: ./media/container-instances-log-analytics/portal-query-02.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: /azure/data-explorer/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create