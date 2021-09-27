---
title: SAP 솔루션을 위한 Azure Monitor 공급자 | Microsoft Docs
description: 본 문서에서는 SAP 솔루션을 위한 Azure Monitor 공급자에 대해 자주 묻는 질문에 대한 답변을 제공합니다.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.date: 07/06/2021
ms.author: radeltch
ms.openlocfilehash: 5061ee2131ee708c38a68dca02c18a21085b5430
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616862"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>SAP 솔루션을 위한 Azure Monitor 공급자(미리 보기)

## <a name="overview"></a>개요

이 문서에서는 현재 SAP 솔루션을 위한 Azure Monitor에 사용할 수 있는 다양한 공급자에 대해 설명합니다.

SAP 솔루션을 위한 Azure Monitor라는 컨텍스트에서, *공급자 유형* 은 특정 *공급자* 를 의미합니다. 예를 들면, SAP HANA 데이터베이스와 같이 SAP 환경 내의 특정 구성 요소를 위해 구성된 *SAP HANA* 가 있습니다. 공급자는 해당 구성 요소에 대한 연결 정보를 가지고 있으며 그 구성 요소에서 원격 분석 데이터를 수집하는 데 도움을 줍니다. SAP 모니터 리소스라고도 알려져 있는, SAP 솔루션을 위한 Azure Monitor 리소스는 동일한 유형의 다수 공급자 또는 다양한 유형의 다수 공급자로 하나를 구성할 수 있습니다.
   
SAP 환경에서 대응하는 구성 요소로부터 데이터를 수집할 수 있도록 다양한 공급자 유형을 구성하게 선택할 수 있습니다. 예를 들어 하나의 공급자를 SAP HANA 공급자 유형을 위해 구성하고 다른 하나를 고가용성 클러스터 공급자 유형을 위해 구성하는 등으로 구성할 수 있습니다.  

뿐만 아니라 특정 공급자 유형의 공급자를 여럿 구성하도록 하여 동일한 SAP 모니터 리소스와 관련된 관리 그룹을 다시 사용할 수도 있습니다. 관리되는 리소스 그룹에 대한 자세한 내용은 [Azure Portal을 사용하여 Azure Resource Manager 리소스 그룹 관리](../../../azure-resource-manager/management/manage-resource-groups-portal.md)를 참조하세요.

퍼블릭 미리 보기의 경우 다음 공급자 유형이 지원됩니다.   
- SAP NetWeaver
- SAP HANA
- Microsoft SQL Server
- 고가용성 클러스터
- OS(운영 체제)

![SAP 솔루션을 위한 Azure Monitor 공급자](https://user-images.githubusercontent.com/75772258/115047655-5a5b2c00-9ef6-11eb-9e0c-073e5e1fcd0e.png)

SAP 모니터 리소스를 배포할 때 사용 가능한 공급자 유형에서 하나 이상의 공급자를 구성하는 것이 좋습니다. 공급자 구성을 통해 공급자를 구성할 해당 구성 요소에서 데이터를 수집하기 시작합니다.   

SAP 모니터 리소스 배포 시 공급자를 구성하지 않은 경우, SAP 모니터 리소스가 성공적으로 배포되더라도 원격 분석 데이터가 수집되지 않습니다. Azure Portal 내의 SAP 모니터 리소스를 통해 배포 후 공급자를 추가할 수 있습니다. 언제라도 SAP 모니터 리소스에서 공급자를 추가하거나 삭제할 수 있습니다.

## <a name="provider-type-sap-netweaver"></a>공급자 유형: SAP NetWeaver

하나 이상의 공급자를 SAP NetWeaver 공급자 유형으로 구성하여 SAP NetWeaver 계층에서 데이터를 수집하도록 설정할 수 있습니다. AMS NetWeaver 공급자는 기존 [SAPControl 웹 서비스](https://www.sap.com/documents/2016/09/0a40e60d-8b7c-0010-82c7-eda71af511fa.html) 인터페이스를 사용하여 적절한 원격 분석 정보를 검색합니다.

현재 릴리스의 경우 다음 SOAP 웹 메서드는 AMS에서 호출하는 기본 제공 메서드입니다.

![image1](https://user-images.githubusercontent.com/75772258/114600036-820d8280-9cb1-11eb-9f25-d886ab1d5414.png)

공개 미리 보기에서 SAP NetWeaver 공급자를 통해 다음 데이터를 확인할 수 있습니다. 
- 시스템 및 인스턴스 가용성
- 작업 프로세스 사용
- 큐 사용
- 큐에 넣은 잠금 통계

![이미지](https://user-images.githubusercontent.com/75772258/114581825-a9f2eb00-9c9d-11eb-8e6f-79cee7c5093f.png)

## <a name="provider-type-sap-hana"></a>공급자 유형: SAP HANA

하나 이상의 공급자를 *SAP HANA* 공급자 유형으로 구성하여 SAP HANA 데이터베이스로부터 데이터를 수집하도록 설정할 수 있습니다. SAP HANA 공급자는 SQL 포트를 통해 SAP HANA 데이터베이스와 연결되며 해당 데이터베이스에서 원격 분석 데이터를 가져와 구독의 Log Analytics 작업 영역으로 보냅니다. SAP HANA 공급자는 SAP HANA 데이터베이스에서 1분마다 데이터를 수집합니다.  

공개 미리 보기에서 SAP HANA 공급자와 함께 다음 데이터를 볼 수 있습니다.
- 기본 인프라 사용
- SAP HANA 호스트 상태
- SAP HANA 시스템 복제
- SAP HANA 백업 원격 측정 데이터. 

SAP HANA 공급자를 구성하려면 다음이 필요합니다.
- 호스트 IP 주소, 
- HANA SQL 포트 번호
- SYSTEMDB 사용자 이름 및 암호

SYSTEMDB에 대해 SAP HANA 공급자를 구성하는 것이 좋지만, 다른 데이터베이스 테넌트에 대해 더욱 많은 공급자를 구성할 수 있습니다.

![SAP 솔루션을 위한 Azure Monitor 공급자 - SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-microsoft-sql-server"></a>공급자 유형: Microsoft SQL Server

하나 이상의 공급자를 *Microsoft SQL Server* 공급자 유형으로 구성하여 [SQL Server on Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/)로부터 데이터를 수집하도록 설정할 수 있습니다. SQL Server 공급자는 SQL 포트를 통해 Microsoft SQL Server에 연결합니다. 그런 다음 데이터베이스에서 원격 분석 데이터를 가져와 구독의 Log Analytics 작업 영역으로 푸시합니다. SQL 인증 및 SQL Server 사용자 이름 및 암호로 로그인하도록 SQL Server를 구성합니다. SAP 데이터베이스를 공급자의 기본 데이터베이스로 설정합니다. SQL Server 공급자는 SQL Server에서 60초~1시간마다 데이터를 수집합니다.  

공개 미리 보기에서 SQL Server 공급자를 통해 다음 데이터를 볼 수 있습니다.
- 기본 인프라 사용
- 상위 SQL 문
- 가장 큰 테이블
- SQL Server 오류 로그에 기록된 문제
- 차단 프로세스 및 기타  

Microsoft SQL Server 공급자를 구성하려면 다음이 필요합니다.
- SAP 시스템 ID
- 호스트 IP 주소
- SQL Server 포트 번호
- SQL Server 사용자 이름 및 암호

![SAP 솔루션을 위한 Azure Monitor 공급자 - SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="provider-type-high-availability-cluster"></a>공급자 유형: 고가용성 클러스터
하나 이상의 공급자를 *고가용성 클러스터* 공급자 유형으로 구성하여 SAP 환경의 Pacemaker 클러스터에서 데이터를 수집하도록 설정할 수 있습니다. 고가용성 클러스터 공급자는 **SUSE** 기반 클러스터의 경우 [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter)를 사용하고 **RHEL** 기반 클러스터의 경우 [Performance co-pilot](https://access.redhat.com/articles/6139852)을 사용하여 Pacemaker에 연결합니다. 그런 다음 AMS는 데이터베이스에서 원격 분석 데이터를 가져와 구독의 Log Analytics 작업 영역으로 푸시합니다. 고가용성 클러스터 공급자는 Pacemaker에서 60초마다 데이터를 수집합니다.  

공개 미리 보기에서 고가용성 클러스터 공급자의 다음 데이터를 확인할 수 있을 것으로 기대합니다.   
 - 노드 및 리소스 상태의 롤업으로 표시된 클러스터 상태 
 - 위치 제약 조건
 - 추세
 - [기타](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![SAP 솔루션을 위한 Azure Monitor 공급자 - 고가용성 클러스터](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

고가용성 클러스터 공급자를 구성하는 데에는 다음 두 가지의 기본 단계가 포함됩니다.

1. [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter)를 Pacemaker 클러스터 내의 *각* 노드에 설치합니다.

   ha_cluster_exporter 설치에는 두 가지 옵션이 있습니다.
   
   - 고가용성 클러스터 배포를 위해 Azure Automation 스크립트 활용하기 스크립트는 [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter)를 각각의 클러스터 노드에 설치합니다.  
   - [수동 설치하기](https://github.com/ClusterLabs/ha_cluster_exporter#manual-clone--build) 

2. Pacemaker 클러스터 내에서 *각* 노드에 대한 고가용성 클러스터 공급자를 구성합니다.

   고가용성 클러스터 공급자 구성에는 다음 정보가 필요합니다.
   
   - **이름**. 해당 공급자의 이름입니다. SAP 솔루션을 위한 Azure Monitor의 해당 인스턴스에 고유한 것이어야 합니다.
   - **Prometheus 엔드포인트**. `http://<servername or ip address>:9664/metrics`.
   - **SID**. SAP 시스템에서는 SAP SID를 사용합니다. 다른 시스템(예를 들면, NFS 클러스터)의 경우 클러스터에 세 글자 이름을 사용합니다. SID는 모니터링되는 다른 클러스터와 구별되어야 합니다.   
   - **클러스터 이름**. 클러스터를 만들 때 사용되는 클러스터 이름입니다. 클러스터 이름은 클러스터 속성 `cluster-name`에서 찾을 수 있습니다.
   - **호스트 이름**. 가상 머신(VM)의 Linux 호스트 이름입니다.  

## <a name="provider-type-os-linux"></a>공급자 유형: OS(Linux)
하나 이상의 공급자를 OS(Linux) 공급자 유형으로 구성하여 BareMetal 또는 VM 노드로부터 데이터를 수집하도록 설정할 수 있습니다. OS(Linux) 공급자는 [Node_Exporter](https://github.com/prometheus/node_exporter)  엔드포인트를 사용하여 BareMetal 또는 VM 노드에 연결합니다. 그런 다음 노드에서 원격 분석 데이터를 가져와 구독의 Log Analytics 작업 영역으로 푸시합니다. OS(Linux) 클러스터 공급자는 노드에서 60초마다 메트릭의 대부분에 대한 데이터를 수집합니다. 

공개 미리 보기에서 OS(Linux) 공급자의 다음 데이터를 확인할 수 있을 것으로 기대합니다. 
   - CPU 사용량, 프로세스별 CPU 사용량 
   - 디스크 사용량, I/O 읽기 및 쓰기 
   - 메모리 배포, 메모리 사용량, 스왑 메모리 사용량 
   - 네트워크 사용량, 네트워크 인바운드 및 아웃바운드 트래픽 세부 정보 

OS(Linux) 공급자를 구성하는 데에는 다음 두 가지의 기본 단계가 포함됩니다.
1.  [Node_Exporter](https://github.com/prometheus/node_exporter) 를 BareMetal 또는 VM 노드 각각에 설치합니다.
   [Node_exporter](https://github.com/prometheus/node_exporter) 설치에는 두 가지 옵션이 있습니다. 
      - Ansible를 통해 자동으로 설치하려면 [Node_Exporter](https://github.com/prometheus/node_exporter)를 BareMetal 또는 VM 노드 각각에 사용해 OS(Linux) 공급자를 설치합니다.  
      -  [수동 설치](https://prometheus.io/docs/guides/node-exporter/)를 실시합니다.

2. 사용자 환경에서 BareMetal 또는 VM 노드 인스턴스 각각에 대해 OS(Linux) 공급자를 구성합니다. 
   OS(Linux) 공급자 구성에는 다음 정보가 필요합니다. 
      - 이름. 해당 공급자의 이름입니다. SAP 솔루션을 위한 Azure Monitor의 해당 인스턴스에 고유한 것이어야 합니다. 
      - 노드 수출업자 엔드포인트 일반적으로 `http://<servername or ip address>:9100/metrics` 입니다. 

> [!NOTE]
> 9100은 Node_Exporter 엔드포인트용으로 노출되는 포트입니다.

> [!Warning]
> 노드 재부팅 후에는 Node Exporter가 실행 중인지 확인합니다. 

## <a name="next-steps"></a>다음 단계

Azure Portal에서 SAP 솔루션을 위한 Azure Monitor를 배포하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Portal을 사용하여 SAP 솔루션을 위한 Azure Monitor 배포](./azure-monitor-sap-quickstart.md)
