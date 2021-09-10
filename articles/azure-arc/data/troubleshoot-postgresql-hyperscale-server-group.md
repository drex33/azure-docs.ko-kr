---
title: PostgreSQL 하이퍼스케일 서버 그룹 문제 해결
description: Jupyter Notebook를 사용하여 PostgreSQL 하이퍼스케일 서버 그룹 문제 해결
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: b828d9e5765a180e1b42de9b96a0ee06eab085f8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528586"
---
# <a name="troubleshooting-postgresql-hyperscale-server-groups"></a>PostgreSQL 하이퍼스케일 서버 그룹 문제 해결
이 문서에서는 서버 그룹 문제를 해결하는 데 사용할 수 있는 몇 가지 기법을 설명합니다. 이 문서 외에도 [Kibana](monitor-grafana-kibana.md)를 사용하여 로그를 검색하거나 [Grafana](monitor-grafana-kibana.md)를 사용하여 서버 그룹의 메트릭을 시각화하는 방법을 알아두는 것이 좋습니다. 

## <a name="getting-more-details-about-the-execution-of-a-cli-command"></a>CLI 명령 실행에 대한 자세한 정보 가져오기
실행하는 모든 CLI 명령에 **--debug** 매개 변수를 추가할 수 있습니다. 추가하면 콘솔에 해당 명령의 실행에 대한 추가 정보가 표시됩니다. 이 매개 변수는 해당 명령의 동작을 이해하는 데 도움이 되는 세부 정보를 얻는 데 유용합니다.
예를 들어 다음을 실행할 수 있습니다.
```azurecli
az postgres arc-server create -n postgres01 -w 2 --debug --k8s-namespace <namespace> --use-k8s
```

또는
```azurecli
az postgres arc-server edit -n postgres01 --extension --k8s-namespace <namespace> --use-k8s SomeExtensionName --debug
```

또한 CLI 명령에 대해 매개 변수 --help를 사용하여 특정 명령에 대한 일부 도움말, 매개 변수 목록을 표시할 수 있습니다. 예를 들면 다음과 같습니다.
```azurecli
az postgres arc-server create --help
```


## <a name="collecting-logs-of-the-data-controller-and-your-server-groups"></a>데이터 컨트롤러와 서버 그룹의 로그 수집
[Azure Arc 지원 데이터 서비스에 대한 로그 가져오기](troubleshooting-get-logs.md)에 관한 문서를 참조하세요.



## <a name="interactive-troubleshooting-with-jupyter-notebooks-in-azure-data-studio"></a>Azure Data Studio에서 Jupyter Notebook을 사용하여 대화형 문제 해결

Notebook은 markdown 콘텐츠를 포함하여 수행할 작업 및 방법을 설명하는 절차를 문서화할 수 있습니다. 또한 절차를 자동화하는 실행 코드를 제공할 수 있습니다.  이 패턴은 표준 운영 절차에서 문제 해결 가이드에 이르기까지 모든 항목에 유용합니다.

예를 들어 Azure Data Studio를 사용하는 데 몇 가지 문제가 있을 수 있는 PostgreSQL 하이퍼스케일 서버 그룹의 문제를 해결해 보겠습니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

[!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

### <a name="install-tools"></a>도구 설치

Azure Data Studio에서 Notebook을 실행하는 데 사용하는 클라이언트 컴퓨터에 `arcdata` 확장이 있는 Azure Data Studio, `kubectl`, Azure(`az`) CLI를 설치합니다. 설치하려면 [클라이언트 도구 설치](install-client-tools.md)의 지침을 따르세요.

### <a name="update-the-path-environment-variable"></a>PATH 환경 변수 업데이트

이 클라이언트 머신의 아무 위치에서 이러한 도구를 호출할 수 있는지 확인합니다. 예를 들어 Windows 클라이언트 머신에서 PATH 시스템 환경 변수를 업데이트하고 kubectl을 설치한 폴더를 추가합니다.

### <a name="log-into-your-kubernetes-cluster-with-kubectl"></a>kubectl을 사용하여 Kubernetes 클러스터에 로그인

이렇게 하려면 [이](https://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/) 블로그 게시물에 제공 된 예제 명령을 사용하는 것이 좋습니다.
다음과 같은 명령을 실행합니다.

```console
kubectl config view
kubectl config set-credentials kubeuser/my_kubeuser --username=<your Arc Data Controller Admin user name> --password=<password>
kubectl config set-cluster my_kubeuser --server=https://<IP address>:<port>
kubectl config set-context default/my_kubeuser/ArcDataControllerAdmin --user=ArcDataControllerAdmin/my_kubeuser --namespace=arc --cluster=my_kubeuser
kubectl config use-context default/my_kubeuser/ArcDataControllerAdmin
```

#### <a name="the-troubleshooting-notebook"></a>문제 해결 Notebook

Azure Data Studio를 시작하고 문제 해결 Notebook을 엽니다. 

[033-manage-Postgres-with-AzureDataStudio.md](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md)에서 설명하는 단계를 구현합니다.

1. Arc 데이터 컨트롤러에 연결
2. Postgres 인스턴스를 마우스 오른쪽 단추로 선택하고 **[관리]** 를 선택합니다.
3. **[문제 진단 및 해결] 대시보드** 를 선택합니다.
4. **[문제 해결] 링크** 를 선택합니다.

:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook.jpg" alt-text="Azure Data Studio - PostgreSQL 문제 해결 Notebook 열기":::

**TSG100 - Azure Arc 지원 PostgreSQL 하이퍼스케일 문제 해결사 Notebook** 이 열립니다. :::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook2.jpg" alt-text="Azure Data Studio - PostgreSQL 문제 해결 Notebook":::

#### <a name="run-the-scripts"></a>스크립트 실행
맨 위에 있는 ‘모두 실행’ 단추를 선택하여 Notebook을 한꺼번에 실행하거나 각 코드 셀을 하나씩 단계별로 실행할 수 있습니다.

코드 셀 실행의 출력을 보면서 잠재적 문제가 있는지 살펴봅니다.

시간이 흐름에 따라 일반적인 문제를 인식하고 해결하는 방법에 대한 자세한 내용을 Notebook에 추가합니다.

## <a name="next-step"></a>다음 단계
- [Azure Arc 지원 데이터 서비스에 대한 로그 가져오기](troubleshooting-get-logs.md)에 대해 읽어보기
- [Kibana를 사용하여 로그 검색](monitor-grafana-kibana.md)에 대해 읽어보기
- [Grafana를 사용하여 모니터링](monitor-grafana-kibana.md)에 대해 읽어보기
- 나만의 Notebook 만들기
