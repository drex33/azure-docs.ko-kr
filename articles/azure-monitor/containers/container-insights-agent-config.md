---
title: Container Insights 에이전트 데이터 컬렉션 설정 | Microsoft Docs
description: 이 문서에서는 stdout/stderr 및 환경 변수 로그 컬렉션을 제어하도록 Container Insights 에이전트를 구성하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: c24b87cb35339cb0e400878579b35d5f6b963718
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129388060"
---
# <a name="configure-agent-data-collection-for-container-insights"></a>Container Insights에 대한 에이전트 데이터 컬렉션 구성

Container Insights는 컨테이너형 에이전트로부터 관리되는 Kubernetes 클러스터에 배포된 컨테이너 워크로드로부터 stdout, stderr 및 환경 변수를 수집합니다. 이 환경을 제어하는 사용자 지정 Kubernetes ConfigMap을 만들어 에이전트 데이터 컬렉션 설정을 구성할 수 있습니다. 

이 문서에서는 요구 사항에 따라 ConfigMap을 만들고 데이터 컬렉션을 구성하는 방법을 보여줍니다.

>[!NOTE]
>Azure Red Hat OpenShift V3의 경우 *openshift-azure-logging* 네임스페이스에 템플릿 configmap 파일이 만들어집니다. 
>

## <a name="configmap-file-settings-overview"></a>ConfigMap 파일 설정 개요

사용자 지정 항목을 처음부터 새로 만들지 않고도 쉽게 편집할 수 있도록 템플릿 ConfigMap 파일이 제공됩니다. 시작하기 전에 [ConfigMap](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/)에 대한 Kubernetes 설명서를 검토하고 ConfigMap 만들기, 구성 및 배포 방법을 숙지해야 합니다. 이렇게 하면 네임스페이스별로 또는 전체 클러스터에서 stderr 및 stdout와 클러스터의 모든 Pod/노드에서 실행되는 컨테이너에 대한 환경 변수를 필터링할 수 있습니다.

>[!IMPORTANT]
>컨테이너 워크로드에서의 stdout, stderr 및 환경 변수 수집이 지원되는 최소 에이전트 버전은 ciprod06142019 이상입니다. 에이전트 버전을 확인하려면 **노드** 탭에서 노드를 선택하고 속성 창에서 **에이전트 이미지 태그** 속성의 값을 확인합니다. 에이전트 버전 및 각 릴리스에 포함되는 내용에 대한 자세한 내용은 [에이전트 릴리스 정보](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)를 참조하세요.

### <a name="data-collection-settings"></a>데이터 컬렉션 설정

다음 표에서는 데이터 컬렉션을 제어하기 위해 구성할 수 있는 설정을 설명합니다.

| 키 | 데이터 형식 | 값 | 설명 |
|--|--|--|--|
| `schema-version` | 문자열(대/소문자 구분) | v1 | 이 ConfigMap을 구문 분석할 때<br> 에이전트가 사용하는 스키마 버전입니다.<br> 현재 지원되는 스키마 버전은 v1입니다.<br> 이 값의 수정은 지원되지 않으며<br> ConfigMap을 평가할 때 거부됩니다. |
| `config-version` | 문자열 |  | 원본 제어 시스템/리포지토리에서 이 구성 파일의 버전 추적을 지원합니다.<br> 허용되는 최대 문자 수는 10이고 다른 모든 문자는 잘립니다. |
| `[log_collection_settings.stdout] enabled =` | 부울 | true 또는 false | stdout 컨테이너 로그 컬렉션을 사용하는지 여부를 제어합니다. `true`로 설정된 경우 stdout 로그 컬렉션에서 네임스페이스가 제외되지 않고<br> (아래 `log_collection_settings.stdout.exclude_namespaces` 설정) 클러스터에 있는 모든 Pod/노드의 모든 컨테이너에서 stdout 로그를 수집합니다. ConfigMap에서 지정하지 않은 경우<br> 기본값은 `enabled = true`입니다. |
| `[log_collection_settings.stdout] exclude_namespaces =` | 문자열 | 쉼표로 구분된 배열 | stdout 로그가 수집되지 않을 경우 Kubernetes 네임스페이스의 배열입니다. 이 설정은<br> `log_collection_settings.stdout.enabled`<br> `true`로 설정된 경우에만 적용됩니다.<br> ConfigMap에서 지정하지 않은 경우 기본값은<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.stderr] enabled =` | 부울 | true 또는 false | stderr 컨테이너 로그 컬렉션을 사용할지 제어합니다.<br> `true`로 설정된 경우 stdout 로그 컬렉션에서 네임스페이스가 제외되지 않고<br> (`log_collection_settings.stderr.exclude_namespaces` 설정) 클러스터에 있는 모든 Pod/노드의 모든 컨테이너에서 stderr 로그를 수집합니다.<br> ConfigMap에서 지정하지 않은 경우 기본값은<br> `enabled = true`. |
| `[log_collection_settings.stderr] exclude_namespaces =` | 문자열 | 쉼표로 구분된 배열 | stderr 로그가 수집되지 않을 경우 Kubernetes 네임스페이스의 배열입니다.<br> 이 설정은<br> `log_collection_settings.stdout.enabled`이 `true`로 설정된 경우에만 적용됩니다.<br> ConfigMap에서 지정하지 않은 경우 기본값은<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` | 부울 | true 또는 false | 이 설정은 클러스터의 모든 Pod/노드에서<br> 환경 변수 수집을 제어하며 ConfigMap에서<br> 지정하지 않을 경우 기본값은 `enabled = true`<br> 입니다.<br> 환경 변수 컬렉션을 전역적으로 사용할 경우,<br> Dockerfile 설정을 사용하거나<br> **env:** 섹션 아래의 [Pod의 구성 파일](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/)에서 환경 변수 `AZMON_COLLECT_ENV`를 **False** 로 설정하여 이를 특정 컨테이너에 대해 사용하지 않도록 할 수 있습니다.<br> 환경 변수 컬렉션을 전역적으로 사용하지 않는 경우 특정 컨테이너에 대해 컬렉션을 사용하도록 설정할 수 없습니다. 즉, 컨테이너 수준에서 적용할 수 있는 유일한 재정의는 이미 전역적으로 사용하는 경우 컬렉션을 사용하지 않도록 설정하는 것입니다. |
| `[log_collection_settings.enrich_container_logs] enabled =` | 부울 | true 또는 false | 이 설정은 컨테이너 로그 보강을 제어하여<br> 클러스터의 모든 컨테이너 로그에 대한 ContainerLog 테이블에 쓴 모든 로그 레코드에 대해 이름 및 이미지 속성 값을 채웁니다.<br> ConfigMap에 지정되지 않은 경우 기본값은 `enabled = false`입니다. |
| `[log_collection_settings.collect_all_kube_events] enabled =` | 부울 | true 또는 false | 이 설정을 통해 모든 유형의 Kube 이벤트를 수집할 수 있습니다.<br> 기본적으로 *Normal* 유형의 Kube 이벤트는 수집되지 않습니다. 이 설정을 `true`로 설정하면 *Normal* 이벤트를 더 이상 필터링하지 않고 모든 이벤트를 수집합니다.<br> `enabled = false`ConfigMap에 지정되지 않은 경우 기본값은 로 설정됩니다. |

### <a name="metric-collection-settings"></a>메트릭 컬렉션 설정

다음 표에서는 메트릭 컬렉션을 제어하기 위해 구성할 수 있는 설정을 설명합니다.

| 키 | 데이터 형식 | 값 | 설명 |
|--|--|--|--|
| `[metric_collection_settings.collect_kube_system_pv_metrics] enabled =` | 부울 | true 또는 false | 이 설정을 사용하여 PV(영구 볼륨) 사용 현황 메트릭을 Kube 시스템 네임스페이스에서 수집할 수 있습니다. 기본적으로 kube 시스템 네임스페이스에서 영구 볼륨 클레임이 있는 영구 볼륨에 대한 사용 현황 메트릭은 수집되지 않습니다. 이 설정을 `true`로 설정하면 모든 네임스페이스에 대한 PV 사용 현황 메트릭이 수집됩니다. 이 매개 변수는 기본적으로 `false`로 설정됩니다. |

ConfigMap은 글로벌 목록으로, 에이전트에는 하나의 ConfigMap만 적용할 수 있습니다. 다른 ConfigMap을 컬렉션에 과도하게 사용할 수 없습니다.

## <a name="configure-and-deploy-configmaps"></a>ConfigMap 구성 및 배포

ConfigMap 구성 파일을 구성하고 클러스터에 배포하려면 다음 단계를 수행합니다.

1. [템플릿 ConfigMap YAML 파일](https://aka.ms/container-azm-ms-agentconfig)을 다운로드하고 container-azm-ms-agentconfig.yaml로 저장합니다. 

   > [!NOTE]
   > Azure Red Hat OpenShift V3을 사용하는 경우에는 ConfigMap 템플릿이 클러스터에 이미 있기 때문에 이 단계가 필요하지 않습니다.

2. 사용자 지정 항목으로 ConfigMap yaml 파일을 편집하여 stdout, stderr 및/또는 환경 변수를 수집합니다. Azure Red Hat OpenShift V3에 대한 ConfigMap yaml 파일을 편집하는 경우 먼저 `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` 명령을 실행하여 텍스트 편집기에서 파일을 엽니다.

    - stdout 로그 컬렉션에 대해 특정 네임스페이스를 제외하려면 다음 예를 사용하여 키/값을 구성합니다(`[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`).
    
    - 특정 컨테이너에 대해 환경 변수 컬렉션을 사용하지 않게 설정하려면 키/값 `[log_collection_settings.env_var] enabled = true`를 설정하여 전역적으로 변수 컬렉션을 사용하게 설정하고 [여기의](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) 단계에 따라 특정 컨테이너에 대한 구성을 완료합니다.
    
    - 클러스터 전체에서 stderr 로그 컬렉션을 사용하지 않게 설정하려면 다음 예를 사용하여 키/값을 구성합니다(`[log_collection_settings.stderr] enabled = false`).
    
    편집기에서 변경 내용을 저장합니다.

3. Azure Red Hat OpenShift V3 이외의 클러스터의 경우 다음 kubectl 명령을 실행하여 ConfigMap을 만듭니다. `kubectl apply -f <configmap_yaml_file.yaml>`. 
    
    예: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

구성 변경 내용을 적용하는 데 몇 분 정도 걸릴 수 있고 클러스터의 모든 omsagent Pod가 다시 시작됩니다. 다시 시작은 모두 동시에 다시 시작되는 것이 아니라, 모든 omsagent Pod에 대한 순환 방식의 다시 시작입니다. 다시 시작이 완료되면 결과를 포함하는 `configmap "container-azm-ms-agentconfig" created`와 유사한 메시지가 표시됩니다.

## <a name="verify-configuration"></a>구성 확인

구성이 Azure Red Hat OpenShift V3 이외의 클러스터에 성공적으로 적용되었는지 확인하려면 다음 명령을 사용하여 에이전트 Pod(`kubectl logs omsagent-fdf58 -n kube-system`)에서 로그를 검토합니다. omsagent Pod에 구성 오류가 있는 경우 출력에 다음과 유사한 오류가 표시됩니다.

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

구성 변경 내용 적용과 관련한 오류도 검토할 수 있습니다. 다음 옵션을 사용하여 구성 변경의 추가적인 문제 해결을 수행할 수 있습니다.

- 같은 `kubectl logs` 명령을 사용하여 에이전트 Pod 로그에서. 

    >[!NOTE]
    >이 명령은 Azure Red Hat OpenShift V3 클러스터에 적용되지 않습니다.
    > 

- 라이브 로그에서. 라이브 로그에 다음과 유사하게 오류가 표시됩니다.

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- Log Analytics 작업 영역에 있는 **KubeMonAgentEvents** 테이블에서. 구성 오류에 대해서 데이터가 *오류* 심각도로 1시간마다 전송됩니다. 오류가 없는 경우 테이블의 항목에 있는 데이터는 오류를 보고하지 않는 *정보* 심각도입니다. **Tags** 속성에는 오류가 발생한 Pod 및 컨테이너 ID에 대한 상세 정보와 지난 1시간 동안의 첫 번째 발생, 마지막 발생 및 횟수가 포함됩니다.

- Azure Red Hat OpenShift V3에서 **ContainerLog** 테이블 검색을 통해 omsagent 로그를 점검하여 openshift-azure-logging의 로그 컬렉션을 사용하는지 확인합니다.

Azure Red Hat OpenShift V3이 아닌 클러스터의 ConfigMap에서 오류를 수정한 후 yaml 파일을 저장하고 다음 명령을 실행하여 업데이트된 ConfigMap을 적용합니다: `kubectl apply -f <configmap_yaml_file.yaml`. Azure Red Hat OpenShift V3의 경우 다음 명령을 실행하여 업데이트된 ConfigMap을 편집 및 저장합니다.

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>업데이트된 ConfigMap 적용

Azure Red Hat OpenShift V3 이외의 클러스터에 ConfigMap을 이미 배포했으며 최신 구성으로 업데이트하려는 경우 이전에 사용한 ConfigMap 파일을 편집한 다음, 이전과 동일한 명령(`kubectl apply -f <configmap_yaml_file.yaml`)을 사용하여 적용할 수 있습니다. Azure Red Hat OpenShift V3의 경우 다음 명령을 실행하여 업데이트된 ConfigMap을 편집 및 저장합니다.

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

구성 변경 내용을 적용하는 데 몇 분 정도 걸릴 수 있고 클러스터의 모든 omsagent Pod가 다시 시작됩니다. 다시 시작은 모두 동시에 다시 시작되는 것이 아니라, 모든 omsagent Pod에 대한 순환 방식의 다시 시작입니다. 다시 시작이 완료되면 다음과 유사한 메시지가 표시되고 결과(`configmap "container-azm-ms-agentconfig" updated`)가 포함됩니다.

## <a name="verifying-schema-version"></a>스키마 버전 확인

지원되는 구성 스키마 버전은 omsagent Pod에서 Pod 주석(schema-versions) 형태로 사용할 수 있습니다. 다음 kubectl 명령을 사용하여 확인할 수 있습니다. `kubectl describe pod omsagent-fdf58 -n=kube-system`

출력은 주석 schema-versions를 포함하여 다음과 유사하게 표시됩니다.

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="next-steps"></a>다음 단계

- Container Insights에는 미리 정의된 경고 집합이 없습니다. [Container Insights로 성능 경고 만들기](./container-insights-log-alerts.md)를 검토하여 DevOps 또는 운영 프로세스 및 절차를 지원하기 위해 높은 CPU 및 메모리 사용률에 대한 권장 경고를 만드는 방법을 알아봅니다.

- AKS 또는 하이브리드 클러스터 그리고 여기에서 실행되는 워크로드의 상태 및 리소스 사용률을 모니터링하여 수집하려면 Container Insights [사용 방법](container-insights-analyze.md)을 알아보세요.

- 클러스터를 경고, 시각화 또는 분석하기 위해 평가하거나 사용자 지정하는 미리 정의된 쿼리 및 예제를 보려면 [로그 쿼리 예제](container-insights-log-query.md)를 확인하세요.
