---
title: Kibana 및 Grafana를 사용하여 로그 및 메트릭 보기
description: Kibana 및 Grafana를 사용하여 로그 및 메트릭 보기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 27f338d093d2084df5ca00b61a8c258d04067b69
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563362"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>Kibana 및 Grafana를 사용하여 로그 및 메트릭 보기

Kibana 및 Grafana 웹 대시보드는 Azure Arc 지원 데이터 서비스에서 사용되는 Kubernetes 네임스페이스에 대한 통찰력 및 명확성을 위해 제공됩니다.



## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>Azure Arc에서 Azure SQL Managed Instance 모니터링

Azure Arc 사용 SQL Managed Instance의 로그 및 모니터링 대시보드에 액세스 하려면 다음 CLI 명령을 실행 합니다. `azdata`

```azurecl
az sql mi-arc endpoint list -n <name of SQL instance> --use-k8s

```
관련 Grafana 대시보드는 다음과 같습니다.

* “Azure SQL Managed Instance 메트릭”
* “호스트 노드 메트릭”
* “호스트 Pod 메트릭”


> [!NOTE]
>  사용자 이름 및 암호를 입력하라는 메시지가 표시되면 Azure Arc 데이터 컨트롤러를 만들 때 제공한 사용자 이름 및 암호를 입력합니다.

> [!NOTE]
>  인증서가 자체 서명된 인증서이므로 인증서 경고 메시지가 표시됩니다.


## <a name="monitor-azure-database-for-postgresql-hyperscale-on-azure-arc"></a>Azure Arc에서 Azure Database for PostgreSQL 하이퍼스케일 모니터링

PostgreSQL 하이퍼스케일에 대한 로그 및 모니터링 대시보드에 액세스하려면 다음 `azdata` CLI 명령을 실행합니다.

```bash

az postgres arc-server endpoint list -n <name of postgreSQL instance> --k8s-namespace <namespace> --use-k8s

```

관련 postgreSQL 대시보드는 다음과 같습니다.

* “Postgres 메트릭”
* “Postgres 테이블 메트릭”
* “호스트 노드 메트릭”
* “호스트 Pod 메트릭”


## <a name="additional-firewall-configuration"></a>추가 방화벽 구성

데이터 컨트롤러가 배포된 위치에 따라 Kibana 및 Grafana 엔드포인트에 액세스하려면 방화벽에서 포트를 열어야 할 수 있습니다.

다음은 Azure VM에 대해 이 작업을 수행하는 방법의 예제입니다. 스크립트를 사용하여 Kubernetes를 배포한 경우 이 작업을 수행해야 합니다.

아래는 Kibana 및 Grafana 엔드포인트에 대한 NSG 규칙을 만드는 단계를 강조 표시합니다.

### <a name="find-the-name-of-the-nsg"></a>NSG의 이름 찾기

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

### <a name="add-the-nsg-rule"></a>NSG 규칙 추가

NSG의 이름을 보유한 경우 다음 명령을 사용하여 규칙을 추가할 수 있습니다.

```azurecli
az network nsg rule create -n ports_30777 --nsg-name azurearcvmNSG --priority 600 -g azurearcvm-rg --access Allow --description 'Allow Kibana and Grafana ports' --destination-address-prefixes '*' --destination-port-ranges 30777 --direction Inbound --protocol Tcp --source-address-prefixes '*' --source-port-ranges '*'
```


## <a name="next-steps"></a>다음 단계
- [Azure Monitor에 메트릭 및 로그 업로드](upload-metrics-and-logs-to-azure-monitor.md)를 시도해 보세요.
- Grafana에 대해 읽어 보세요.
   - [시작](https://grafana.com/docs/grafana/latest/getting-started/getting-started)
   - [Grafana 기본 사항](https://grafana.com/tutorials/grafana-fundamentals/#1)
   - [Grafana 자습서](https://grafana.com/tutorials/grafana-fundamentals/#1)
- Kibana에 대해 읽어 보세요.
   - [소개](https://www.elastic.co/webinars/getting-started-kibana?baymax=default&elektra=docs&storm=top-video)
   - [Kibana 가이드](https://www.elastic.co/guide/en/kibana/current/index.html)
   - [Kibana의 데이터 시각화를 사용하는 대시보드 드릴다운 소개](https://www.elastic.co/webinars/dashboard-drilldowns-with-data-visualizations-in-kibana/)
   - [Kibana 대시보드를 빌드하는 방법](https://www.elastic.co/webinars/how-to-build-kibana-dashboards/)
