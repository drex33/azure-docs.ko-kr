---
title: Azure Managed Instance for Apache Cassandra에서 내보낸 메트릭을 시각화하도록 Grafana 구성
description: VM에 Grafana를 설치 및 구성하여 Azure Managed Instance for Apache Cassandra 클러스터에서 내보낸 메트릭을 시각화하는 방법을 알아봅니다.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 11/16/2021
ms.author: thvankra
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3b74d168f37391bf89bc7591b7263ba40f3d1074
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132723255"
---
# <a name="configure-grafana-to-visualize-metrics-emitted-from-the-managed-instance-cluster"></a>관리되는 인스턴스 클러스터에서 내보낸 메트릭을 시각화하도록 Grafana 구성

Apache Cassandra 클러스터용 Azure Managed Instance 배포하는 경우 서비스는 각 데이터 노드에 [Apache Cassandra 에이전트 소프트웨어용 메트릭 수집기](https://github.com/datastax/metric-collector-for-apache-cassandra) 소프트웨어를 프로비전합니다. 메트릭은 [Prometheus에서](https://prometheus.io/) 사용되며 Grafana를 통해 시각화할 수 있습니다. 이 문서에서는 Managed Instance 클러스터에서 내보낸 메트릭을 시각화하도록 Prometheus 및 Grafana를 구성하는 방법을 설명합니다. 

메트릭을 시각화하려면 다음 작업이 필요합니다.

* 관리되는 인스턴스가 있는 Azure Virtual Network 내에 Ubuntu Virtual Machine을 배포합니다.
* [VM에 Prometheus 대시보드를 설치합니다.](https://github.com/datastax/metric-collector-for-apache-cassandra#installing-the-prometheus-dashboards)

## <a name="deploy-an-ubuntu-server"></a>Ubuntu 서버 배포

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 관리되는 인스턴스 클러스터가 있는 리소스 그룹으로 이동합니다. **추가** 를 선택하고 **Ubuntu Server 18.04 LTS** 이미지를 검색합니다.

   :::image type="content" source="./media/visualize-prometheus-grafana/select-ubuntu-image.png" alt-text="Azure Portal에서 Ubuntu 서버 이미지를 찾습니다." border="true":::

1. 이미지를 선택하고 **만들기** 를 선택합니다.

1. **가상 머신 만들기** 블레이드에서 다음 필드에 값을 입력하고 그 외 필드에는 기본값을 그대로 둘 수 있습니다.

   * **가상 머신 이름** - VM의 이름을 입력합니다.
   * **지역** - Virtual Network가 배포된 동일한 지역을 선택합니다.

   :::image type="content" source="./media/visualize-prometheus-grafana/create-vm-ubuntu.png" alt-text="양식을 작성하여 Ubuntu 서버 이미지가 있는 VM을 만듭니다." border="true":::

1. **네트워킹** 탭에서 관리되는 인스턴스가 배포된 Virtual Network를 선택합니다.

   :::image type="content" source="./media/visualize-prometheus-grafana/configure-networking-details.png" alt-text="Ubuntu 서버의 네트워크 설정을 구성합니다." border="true":::

1. 마지막으로 **검토 + 만들기를** 선택하여 메트릭 서버를 만듭니다.

## <a name="install-prometheus-dashboards"></a>Prometheus 대시보드 설치

1. 먼저 새로 배포된 Ubuntu 서버에 대한 네트워킹 설정에 포트 및 를 허용하는 인바운드 포트 규칙이 있는지 `9090` `3000` 확인합니다. 이러한 내용은 나중에 Prometheus 및 Grafana에 각각 필요합니다. 

   :::image type="content" source="./media/visualize-prometheus-grafana/networking.png" alt-text="포트 허용" border="true":::

1. Azure CLI [또는](../virtual-machines/linux/ssh-from-windows.md#ssh-clients) 선호하는 클라이언트 도구를 사용하여 SSH를 통해 연결하여 Ubuntu 서버에 커넥트.

1. VM에 연결한 후 메트릭 수집기 소프트웨어를 설치해야 합니다. 먼저 파일을 다운로드하고 압축을 풀습니다.

   ```bash
    #install unzip utility (if not already installed)
    sudo apt install unzip
    
    #get dashboards
    wget https://github.com/datastax/metric-collector-for-apache-cassandra/releases/download/v0.3.0/datastax-mcac-dashboards-0.3.0.zip -O temp.zip
    unzip temp.zip
   ```

1. 다음으로 prometheus 디렉터리로 이동하고 vi를 사용하여 파일을 편집합니다. `tg_mcac.json`

   ```bash
    cd */prometheus
    vi tg_mcac.json    
   ```


1. 각 포트가 9443인 의 클러스터에 있는 각 노드의 IP 주소를 `targets` 추가합니다. `tg_mcac.json`파일은 아래와 같습니다.

   ```bash
    [
      {
        "targets": [
          "10.9.0.6:9443","10.9.0.7:9443","10.9.0.8:9443"
        ],
        "labels": {
    
        }
      }
    ]  
   ```

1. 파일을 저장합니다. 다음으로, `prometheus.yaml` 동일한 디렉터리에서 파일을 편집합니다. 다음 섹션을 찾습니다.

   ```bash
    file_sd_configs:
      - files:
        - 'tg_mcac.json'
   ```

1. 이 섹션 바로 아래에 다음을 추가합니다. 이는 메트릭이 https를 통해 노출되기 때문에 필요합니다.

   ```bash
    scheme: https
    tls_config:
            insecure_skip_verify: true
   ```

1. 이제 파일이 다음과 같이 표시됩니다. 각 줄의 탭이 아래와 같은지 확인합니다. 

   ```bash
    file_sd_configs:
      - files:
        - 'tg_mcac.json'
    scheme: https
    tls_config:
            insecure_skip_verify: true
   ```

1. 파일을 저장합니다. 이제 Prometheus 및 Grafana를 시작할 준비가 되었습니다. 먼저 Docker를 설치합니다.

    ```bash
    sudo apt install apt-transport-https ca-certificates curl software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu `lsb_release -cs` test"
    sudo apt update
    sudo apt install docker-ce
    ```

1. 그런 다음, docker compose를 설치합니다.

    ```bash
    sudo apt install docker-compose
    ```

1. 이제 가 있는 최상위 디렉터리로 이동하여 `docker-compose.yaml` 애플리케이션을 시작합니다.

    ```bash
    cd ..
    sudo docker-compose up
    ```

1. Prometheus는 `9090` 메트릭 서버의 포트에서 포트 , Grafana 대시보드에서 사용할 수 있어야 `3000` 합니다.

   :::image type="content" source="./media/visualize-prometheus-grafana/monitor-cassandra-metrics.png" alt-text="대시보드에서 Cassandra 관리되는 인스턴스 메트릭을 봅니다." border="true":::


## <a name="next-steps"></a>다음 단계

이 문서에서는 Grafana를 사용하여 Prometheus에서 메트릭을 시각화하도록 대시보드를 구성하는 방법을 배웠습니다. 다음 문서에서 Azure Managed Instance for Apache Cassandra에 대해 자세히 알아보세요.

* [Apache Cassandra용 Azure Managed Instance 개요](introduction.md)
* [Azure Databricks를 사용하여 관리형 Apache Spark 클러스터 배포](deploy-cluster-databricks.md)
