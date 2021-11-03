---
title: Azure Managed Instance for Apache Cassandra에서 내보낸 메트릭을 시각화하도록 Grafana 구성
description: VM에 Grafana를 설치 및 구성하여 Azure Managed Instance for Apache Cassandra 클러스터에서 내보낸 메트릭을 시각화하는 방법을 알아봅니다.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 11/02/2021
ms.author: thvankra
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4464db95637511339e0000235b85da86306b4cef
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131051024"
---
# <a name="configure-grafana-to-visualize-metrics-emitted-from-the-managed-instance-cluster"></a>관리되는 인스턴스 클러스터에서 내보낸 메트릭을 시각화하도록 Grafana 구성

Azure Managed Instance for Apache Cassandra를 배포할 때 서비스는 다양한 클라이언트 도구에서 사용할 수 있는 [Prometheus](https://prometheus.io/)를 호스팅하는 서버를 프로비저닝합니다. Prometheus는 오픈 소스 모니터링 솔루션입니다. 관리되는 인스턴스는 메트릭을 내보내고 10분 또는 10GB의 데이터를 유지합니다(먼저 도달하는 임계값을 기준으로 함). 이 문서에서는 관리되는 인스턴스 클러스터에서 내보낸 메트릭을 시각화하도록 Grafana를 구성하는 방법을 설명합니다. 메트릭을 시각화하려면 다음 작업이 필요합니다.

* 관리되는 인스턴스가 있는 Azure Virtual Network 내부에 Ubuntu 가상 머신을 배포합니다.
* 오픈 소스 [Grafana 도구](https://grafana.com/grafana/)를 설치하여 대시보드를 빌드하고 Prometheus에서 내보낸 메트릭을 시각화합니다.

## <a name="deploy-a-ubuntu-server"></a>Ubuntu 서버 배포

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

1. 마지막으로 **검토 + 만들기** 를 선택하여 Grafana 서버를 만듭니다.

## <a name="install-grafana"></a>Grafana 설치

1. Azure Portal에서 관리되는 인스턴스와 Grafana 서버를 배포한 Virtual Network를 엽니다. **cassandra-jump(인스턴트 0)** 라는 가상 머신 확장 집합 인스턴스가 표시되어야 합니다. 이 Prometheus 메트릭은 이 가상 머신 확장 집합에서 호스팅됩니다. 이 인스턴스의 IP 주소를 기록해 둡니다.

   :::image type="content" source="./media/visualize-prometheus-grafana/prometheus-instance-address.png" alt-text="Prometheus 인스턴스의 IP 주소를 가져옵니다." border="true":::

1. [Azure CLI](../virtual-machines/linux/ssh-from-windows.md#ssh-clients)를 사용하여 새로 만들어진 Ubuntu 서버에 연결하거나 SSH를 통해 연결하기 위해 선호하는 클라이언트 도구를 사용하여 연결합니다.

1. VM에 연결한 후 메트릭이 호스팅되는 가상 머신 확장 집합에 연결하도록 Grafana를 설치하고 구성해야 합니다. 명령 프롬프트를 열고 `nano` 명령을 입력하여 Nano 텍스트 편집기를 엽니다. 다음 스크립트를 텍스트 편집기에 붙여넣고 `<prometheus IP address>`를 이전 단계에서 기록해 놓은 IP 주소로 바꿔야 합니다.

   ```bash
   #!/bin/bash
   
   echo "Installing Grafana..."
   
   if ! $SSH dpkg -s grafana prometheus > /dev/null; then
       echo "Installing packages."
       echo 'deb https://packages.grafana.com/oss/deb stable main' | $SSH sudo tee /etc/apt/sources.list.d/grafana.list > /dev/null
       curl https://packages.grafana.com/gpg.key | $SSH sudo apt-key add -
       $SSH sudo apt-get update
       $SSH sudo apt-get install -y grafana prometheus
   else
       echo "Skipping package installation"
   fi
   
   echo "Configuring grafana"
   cat <<EOF | $SSH sudo tee /etc/grafana/provisioning/datasources/prometheus.yml
   apiVersion: 1
   datasources:
     - name: Prometheus
       type: prometheus
       url: https://<prometheus IP address>:9443
       jsonData:
         tlsSkipVerify: true
   EOF
   
   echo "Restarting Grafana"
   $SSH sudo systemctl enable grafana-server
   $SSH sudo systemctl restart grafana-server
   
   echo "Installing Grafana plugins"
   $SSH sudo grafana-cli plugins install natel-discrete-panel
   $SSH sudo grafana-cli plugins install grafana-polystat-panel
   $SSH sudo systemctl restart grafana-server
   ```

1. `ctrl + X`를 입력하여 파일을 저장합니다. 파일 이름을 `grafana.sh`로 지정할 수 있습니다.

1. 명령 프롬프트에 `./grafana.sh` 명령을 입력하여 Grafana를 설치합니다.

1. 설치가 완료되면 다음 스크린샷과 같이 서버 IP 주소의 **포트 3000** 에서 Grafana를 사용할 수 있습니다.

   :::image type="content" source="./media/visualize-prometheus-grafana/open-grafana-port.png" alt-text="포트 3000에서 Grafana를 실행합니다." border="true":::

1. [클러스터 개요](https://github.com/TheovanKraay/cassandra-exporter/blob/master/grafana/instaclustr/cluster-overview.json) JSON 파일과 같이 Grafana에서 Apache Cassandra용으로 만든 오픈 소스 대시보드에서 선택할 수 있습니다. 대시보드의 JSON 정의를 다운로드하여 Grafana로 가져옵니다.

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-import.png" alt-text="Grafana JSON 정의를 가져옵니다." border="true":::

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-upload-json.png" alt-text="Grafana JSON 정의를 업로드합니다." border="true":::

1. 그런 다음 선택한 대시보드로 Cassandra 관리되는 인스턴스 클러스터를 모니터링할 수 있습니다.

   :::image type="content" source="./media/visualize-prometheus-grafana/monitor-cassandra-metrics.gif" alt-text="대시보드에서 Cassandra 관리되는 인스턴스 메트릭을 봅니다." border="true":::

## <a name="next-steps"></a>다음 단계

이 문서에서는 Grafana를 사용하여 Prometheus에서 메트릭을 시각화하도록 대시보드를 구성하는 방법을 배웠습니다. 다음 문서에서 Azure Managed Instance for Apache Cassandra에 대해 자세히 알아보세요.

* [Apache Cassandra용 Azure Managed Instance 개요](introduction.md)
* [Azure Databricks를 사용하여 관리형 Apache Spark 클러스터 배포](deploy-cluster-databricks.md)
