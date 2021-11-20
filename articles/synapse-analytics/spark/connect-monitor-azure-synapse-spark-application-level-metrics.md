---
title: API를 사용하여 Apache Spark 애플리케이션 메트릭 수집
description: 자습서 - Synapse Prometheus 커넥터를 사용하여 거의 실시간으로 Azure Spark 애플리케이션 메트릭에 대해 기존 온-프레미스 Prometheus 서버를 Azure Synapse 작업 영역에 통합하는 방법을 알아봅니다.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: 3df6cac70df3ff80e48aa1e13374d9f0146262ca
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132862229"
---
#  <a name="collect-apache-spark-applications-metrics-using-apis"></a>API를 사용하여 Apache Spark 애플리케이션 메트릭 수집

## <a name="overview"></a>개요

이 자습서에서는 Synapse Prometheus 커넥터를 사용하여 거의 실시간으로 Apache Spark 애플리케이션 메트릭에 대해 기존 온-프레미스 Prometheus 서버를 Azure Synapse 작업 영역에 통합하는 방법을 알아봅니다. 

또한 Azure Synapse REST 메트릭 API도 소개합니다. REST API를 통해 Apache Spark 애플리케이션 메트릭 데이터를 가져와 고유한 모니터링 및 진단 도구 키트를 빌드하거나 모니터링 시스템과 통합할 수 있습니다.

## <a name="use-azure-synapse-prometheus-connector-for-your-on-premises-prometheus-servers"></a>온-프레미스 Prometheus 서버에 대해 Azure Synapse Prometheus 커넥터 사용

[Azure Synapse Prometheus 커넥터](https://github.com/microsoft/azure-synapse-spark-metrics)는 오픈 소스 프로젝트입니다. Synapse Prometheus 커넥터에서는 파일 기반 서비스 검색 방법을 사용하여 다음을 수행할 수 있습니다.
 - AAD 서비스 주체를 통해 Synapse 작업 영역에서 인증을 받습니다.
 - 작업 영역 Apache Spark 애플리케이션 목록을 가져옵니다. 
 - Prometheus 파일 기반 구성을 통해 Apache Spark 애플리케이션 메트릭을 끌어옵니다. 

### <a name="1-prerequisite"></a>1. 필수 요소

Linux VM에 Prometheus 서버가 배포되어 있어야 합니다.

### <a name="2-create-a-service-principal"></a>2. 서비스 주체 만들기

온-프레미스 Prometheus 서버에서 Azure Synapse Prometheus 커넥터를 사용하려면 다음 단계를 수행하여 서비스 주체를 만들어야 합니다.

#### <a name="21-create-a-service-principal"></a>2.1 서비스 주체 만들기:

```bash
az ad sp create-for-rbac --name <service_principal_name> --role Contributor
```

결과는 다음과 같습니다.

```json
{
  "appId": "abcdef...",
  "displayName": "<service_principal_name>",
  "name": "http://<service_principal_name>",
  "password": "abc....",
  "tenant": "<tenant_id>"
}
```

appId, 암호 및 테넌트 ID를 적어둡니다.

#### <a name="22-add-corresponding-permissions-to-the-service-principal-created-in-the-above-step"></a>2.2 위 단계에서 만든 서비스 주체에 해당 권한 추가

![스크린샷 권한 부여 srbac](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)

1. Synapse 관리자로 [Azure Synapse Analytics 작업 영역](https://web.azuresynapse.net/)에 로그인합니다.

2. Synapse Studio의 왼쪽 창에서 **관리 > 액세스 제어** 를 선택합니다.

3. 왼쪽 위에 있는 추가 단추를 클릭하여 **역할 할당을 추가** 합니다.

4. 범위로 **작업 영역** 을 선택합니다.

5. 역할로 **Synapse Compute 역할** 을 선택합니다.

6. 선택한 사용자에 대해 **<service_principal_name>** 을 입력하고 서비스 주체를 클릭합니다.

7. **적용** 을 클릭합니다(권한이 적용될 때까지 3분 대기).


### <a name="3-download-the-azure-synapse-prometheus-connector"></a>3. 3. Azure Synapse 프로메테우스 커넥터 다운로드

다음 명령을 사용하여 Azure Synapse Prometheus 커넥터를 설치합니다.

```bash
git clone https://github.com/microsoft/azure-synapse-spark-metrics.git
cd ./azure-synapse-spark-metrics/synapse-prometheus-connector/src
python pip install -r requirements.txt
```

### <a name="4-create-a-config-file-for-azure-synapse-workspaces"></a>4. Azure Synapse 작업 영역에 대한 구성 파일 만들기

config 폴더에 config.xml 파일을 만들고 workspace_name, tenant_id, service_principal_name 및 service_principal_password 필드를 채웁니다.
yaml 구성에서 여러 작업 영역을 추가할 수 있습니다.

```yaml
workspaces:
  - workspace_name: <your_workspace_name>
    tenant_id: <tenant_id>
    service_principal_name: <service_principal_app_id>
    service_principal_password: "<service_principal_password>"
```

### <a name="5-update-the-prometheus-config"></a>5. Prometheus 구성 업데이트

Prometheus scrape_config에서 다음 구성 섹션을 추가하고 <your_workspace_name>을 작업 영역 이름으로 바꾸고 <path_to_synapse_connector>를 복제된 synapse-prometheus-connector 폴더로 바꿉니다.

```yaml
- job_name: synapse-prometheus-connector
  static_configs:
  - labels:
      __metrics_path__: /metrics
      __scheme__: http
    targets:
    - localhost:8000
- job_name: synapse-workspace-<your_workspace_name>
  bearer_token_file: <path_to_synapse_connector>/output/workspace/<your_workspace_name>/bearer_token
  file_sd_configs:
  - files:
    - <path_to_synapse_connector>/output/workspace/<your_workspace_name>/application_discovery.json
    refresh_interval: 10s
  metric_relabel_configs:
  - source_labels: [ __name__ ]
    target_label: __name__
    regex: metrics_application_[0-9]+_[0-9]+_(.+)
    replacement: spark_$1
  - source_labels: [ __name__ ]
    target_label: __name__
    regex: metrics_(.+)
    replacement: spark_$1
```


### <a name="6-start-the-connector-in-the-prometheus-server-vm"></a>6. Prometheus 서버 VM에서 커넥터 시작

다음과 같이 Prometheus 서버 VM에서 커넥터 서버를 시작합니다.

```
python main.py
```

몇 초 동안 기다리면 커넥터가 작동합니다. 또한 Prometheus 서비스 검색 페이지에서 "synapse-prometheus-connector"가 표시됩니다.

## <a name="use-azure-synapse-prometheus-or-rest-metrics-apis-to-collect-metrics-data"></a>Azure Synapse Prometheus 또는 REST 메트릭 API를 사용하여 메트릭 데이터를 수집합니다.

### <a name="1-authentication"></a>1. 인증
클라이언트 자격 증명 흐름을 사용하여 액세스 토큰을 가져올 수 있습니다. 메트릭 API에 액세스하려면 API에 액세스할 수 있는 적절한 권한이 있는 서비스 주체에 대한 Azure AD 액세스 토큰을 가져와야 합니다.

| 매개 변수     | 필수 | Description                                                                                                   |
| ------------- | -------- | ------------------------------------------------------------------------------------------------------------- |
| tenant_id     | True     | Azure 서비스 주체(애플리케이션) 테넌트 ID입니다.                                                          |
| grant_type    | True     | 요청된 부여 유형을 지정합니다. 클라이언트 자격 증명 부여 흐름에서 값은 client_credentials이어야 합니다. |
| client_id     | True     | Azure Portal 또는 Azure CLI에 등록한 애플리케이션의 애플리케이션(서비스 주체) ID입니다.        |
| client_secret | True     | 애플리케이션에 대해 생성된 암호(서비스 주체)입니다.                                                  |
| resource      | True     | Synapse 리소스 URI로, 'https://dev.azuresynapse.net '이어야 합니다.                                                  |

```bash
curl -X GET -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'grant_type=client_credentials&client_id=<service_principal_app_id>&resource=<azure_synapse_resource_id>&client_secret=<service_principal_secret>' \
  https://login.microsoftonline.com/<tenant_id>/oauth2/token
```

응답은 다음과 같습니다.

```json
{
  "token_type": "Bearer",
  "expires_in": "599",
  "ext_expires_in": "599",
  "expires_on": "1575500666",
  "not_before": "1575499766",
  "resource": "2ff8...f879c1d",
  "access_token": "ABC0eXAiOiJKV1Q......un_f1mSgCHlA"
}
```

### <a name="2-list-running-applications-in-the-azure-synapse-workspace"></a>2. Azure Synapse 작업 영역에서 실행 중인 애플리케이션 나열

Synapse 작업 영역에 대한 Apache Spark 애플리케이션 목록을 가져오려면 이 문서 [모니터링 - Apache Spark 작업 목록 가져오기](/rest/api/synapse/data-plane/monitoring/getsparkjoblist)를 따라 이동하면 됩니다.


### <a name="3-collect-apache-spark-application-metrics-with-the-prometheus-or-rest-apis"></a>3. Prometheus 또는 REST API를 사용하여 Apache Spark 애플리케이션 메트릭 수집


#### <a name="collect-apache-spark-application-metrics-with-the-prometheus-api"></a>Prometheus API를 사용하여 Apache Spark 애플리케이션 메트릭 수집

Prometheus API로 지정된 Apache Spark 애플리케이션의 최신 메트릭 가져오기

```
GET https://{endpoint}/livyApi/versions/{livyApiVersion}/sparkpools/{sparkPoolName}/sessions/{sessionId}/applications/{sparkApplicationId}/metrics/executors/prometheus?format=html
```

| 매개 변수          | 필수 | Description                                                                                 |
| ------------------ | -------- | --------------------------------------------------------------------------------------------|
| 엔드포인트(endpoint)           | True     | 작업 영역 개발 엔드포인트(예: `https://myworkspace.dev.azuresynapse.net.`)입니다. |
| livyApiVersion     | True     | 요청에 대한 유효한 api-version입니다. 현재, 2019-11-01-preview입니다.                      |
| sparkPoolName      | True     | spark 세션의 이름입니다.                                                                     |
| sessionID          | True     | 세션의 식별자입니다.                                                                 |
| sparkApplicationId | True     | Spark 애플리케이션 ID입니다.                                                                        |

샘플 요청: 

```
GET https://myworkspace.dev.azuresynapse.net/livyApi/versions/2019-11-01-preview/sparkpools/mysparkpool/sessions/1/applications/application_1605509647837_0001/metrics/executors/prometheus?format=html
```

샘플 응답:

상태 코드: 200 응답은 다음과 같습니다.

```
metrics_executor_rddBlocks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_memoryUsed_bytes{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 74992
metrics_executor_diskUsed_bytes{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_totalCores{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_maxTasks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_activeTasks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 1
metrics_executor_failedTasks_total{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_completedTasks_total{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 2
...

```

#### <a name="collect-apache-spark-application-metrics-with-the-rest-api"></a>REST API를 사용하여 Apache Spark 애플리케이션 메트릭 수집

```
GET https://{endpoint}/livyApi/versions/{livyApiVersion}/sparkpools/{sparkPoolName}/sessions/{sessionId}/applications/{sparkApplicationId}/executors
```

| 매개 변수          | 필수 | Description                                                                                 |
| ------------------ | -------- | --------------------------------------------------------------------------------------------|
| 엔드포인트(endpoint)           | True     | 작업 영역 개발 엔드포인트(예: `https://myworkspace.dev.azuresynapse.net.`)입니다. |
| livyApiVersion     | True     | 요청에 대한 유효한 api-version입니다. 현재, 2019-11-01-preview입니다.                      |
| sparkPoolName      | True     | spark 세션의 이름입니다.                                                                     |
| sessionID          | True     | 세션의 식별자입니다.                                                                 |
| sparkApplicationId | True     | Spark 애플리케이션 ID입니다.                                                                        |

샘플 요청

```
GET https://myworkspace.dev.azuresynapse.net/livyApi/versions/2019-11-01-preview/sparkpools/mysparkpool/sessions/1/applications/application_1605509647837_0001/executors
```

샘플 응답 상태 코드: 200

```json
[
    {
        "id": "driver",
        "hostPort": "f98b8fc2aea84e9095bf2616208eb672007bde57624:45889",
        "isActive": true,
        "rddBlocks": 0,
        "memoryUsed": 75014,
        "diskUsed": 0,
        "totalCores": 0,
        "maxTasks": 0,
        "activeTasks": 0,
        "failedTasks": 0,
        "completedTasks": 0,
        "totalTasks": 0,
        "totalDuration": 0,
        "totalGCTime": 0,
        "totalInputBytes": 0,
        "totalShuffleRead": 0,
        "totalShuffleWrite": 0,
        "isBlacklisted": false,
        "maxMemory": 15845975654,
        "addTime": "2020-11-16T06:55:06.718GMT",
        "executorLogs": {
            "stdout": "http://f98b8fc2aea84e9095bf2616208eb672007bde57624:8042/node/containerlogs/container_1605509647837_0001_01_000001/trusted-service-user/stdout?start=-4096",
            "stderr": "http://f98b8fc2aea84e9095bf2616208eb672007bde57624:8042/node/containerlogs/container_1605509647837_0001_01_000001/trusted-service-user/stderr?start=-4096"
        },
        "memoryMetrics": {
            "usedOnHeapStorageMemory": 75014,
            "usedOffHeapStorageMemory": 0,
            "totalOnHeapStorageMemory": 15845975654,
            "totalOffHeapStorageMemory": 0
        },
        "blacklistedInStages": []
    },
    // ...
]
```


### <a name="4-build-your-own-diagnosis-and-monitoring-tools"></a>4. 고유한 진단 및 모니터링 도구 빌드

Prometheus API 및 REST API는 Apache Spark 애플리케이션 실행 정보에 대한 풍부한 메트릭 데이터를 제공합니다. Prometheus API 및 REST API를 통해 애플리케이션 관련 메트릭 데이터를 수집할 수 있습니다. 사용자의 요구에 보다 적합한 자체 진단 및 모니터링 도구를 작성합니다.
