---
title: Azure의 SAP 모니터링(미리 보기) | Microsoft Docs
description: Azure의 SAP를 모니터링하는 방법을 알아보려면 여기에서 시작합니다.
author: Ajayan1008
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.custom: subject-monitoring
ms.date: 10/13/2021
ms.author: v-hborys
ms.openlocfilehash: 4f90fc00dc895617796cc478835690c931a55f51
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130004866"
---
# <a name="monitor-sap-on-azure-preview"></a>Azure의 SAP 모니터링(미리 보기)

Azure 리소스를 사용하는 중요한 애플리케이션 및 비즈니스 프로세스가 있는 경우 해당 리소스의 가용성, 성능 및 작업을 모니터링해야 합니다. 

이 문서에서는 SAP 솔루션을 위한 Azure Monitor를 사용하여 Azure에서 실행되는 SAP를 모니터링하는 방법을 설명합니다. SAP 솔루션을 위한 Azure Monitor는 [Azure Monitor](../../../azure-monitor/overview.md) 인프라의 특정 부분을 사용합니다.

## <a name="overview"></a>개요

SAP 솔루션을 위한 Azure Monitor는 Azure의 SAP 환경을 실행하는 모든 사용자를 위한 Azure-native 모니터링 제품입니다. Azure의 SAP Virtual Machines 및 [Azure의 SAP](./hana-get-started.md) 큰 인스턴스 모두에서 [작동합니다.](./hana-overview-architecture.md)

SAP 솔루션을 위한 Azure Monitor 사용하면 Azure 인프라 및 데이터베이스의 원격 분석 데이터를 하나의 중앙 위치에 수집하고 더 빠른 문제 해결을 위해 데이터를 시각적으로 상호 연결합니다.

해당 구성 요소에 대해 관련 **공급자** 를 추가하여 Azure VM(가상 머신), 고가용성 클러스터, SAP HANA 데이터베이스, SAP NetWeaver 등의 SAP 환경을 이루는 다양한 구성 요소를 모니터링할 수 있습니다. 자세한 내용은 [Azure Portal을 사용하여 SAP 솔루션을 위한 Azure Monitor 배포](azure-monitor-sap-quickstart.md)를 참조하세요.

지원되는 인프라:

- Azure 가상 머신
- Azure 대규모 인스턴스

지원되는 데이터베이스:
- SAP HANA 데이터베이스
- Microsoft SQL Server:

SAP 솔루션을 위한 Azure Monitor는 [Log Analytics](../../../azure-monitor/logs/log-analytics-overview.md) 및 [통합 문서](../../../azure-monitor/visualize/workbooks-overview.md)의 [Azure Monitor](../../../azure-monitor/overview.md) 기능을 사용합니다. 이를 통해 다음을 수행할 수 있습니다.

- SAP 솔루션을 위한 Azure Monitor에서 제공하는 기본 통합 문서를 편집하여 [사용자 지정 시각화](../../../azure-monitor/visualize/workbooks-overview.md#getting-started)를 만듭니다. 
- [사용자 지정 쿼리](../../../azure-monitor/logs/log-analytics-tutorial.md)를 작성합니다.
- Azure Log Analytics 작업 영역을 사용하여 [사용자 지정 경고](../../../azure-monitor/alerts/alerts-log.md)를 만듭니다. 
- Azure Monitor Logs/Log Analytics의 [유연한 보관 기간](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)을 활용합니다. 
- 모니터링 데이터를 해당 티켓 시스템과 연결합니다.

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>SAP 솔루션을 위한 Azure Monitor에서 어떤 데이터를 수집하나요?

SAP 솔루션을 위한 Azure Monitor는 다른 많은 Azure 리소스처럼 Azure Monitor 메트릭 또는 리소스 로그 데이터를 수집하지 않습니다. 대신 사용자 지정 로그가 Azure Monitor Logs 시스템에 직접 전송되면 Log Analytics의 기본 제공 기능을 사용할 수 있습니다.

SAP 솔루션을 위한 Azure Monitor에서 수집하는 데이터는 구성된 공급자에 따라 다릅니다. 공개 미리 보기 중에는 다음 데이터가 수집됩니다.

고가용성 Pacemaker 클러스터 원격 분석:
- 노드, 리소스 및 SBD(STONITH 블록 디바이스) 상태
- Pacemaker 위치 제약 조건
- 쿼럼 투표 및 링 상태
- [기타](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

SAP HANA 원격 분석:
- CPU, 메모리, 디스크 및 네트워크 사용
- HSR(HANA 시스템 복제)
- HANA 백업
- HANA 호스트 상태
- 인덱스 서버 및 이름 서버 역할
- 데이터베이스 증가
- 상위 테이블
- 파일 시스템 사용

Microsoft SQL Server 원격 분석:
- CPU, 메모리, 디스크 사용
- 호스트 이름, SQL 인스턴스 이름, SAP 시스템 ID
- 시간에 따른 일괄 처리 요청, 컴파일 및 페이지 예상 수명
- 시간에 따라 가장 많은 비용이 드는 상위 10개 SQL 문
- SAP 시스템에서 가장 큰 상위 12개 테이블
- SQL Server 오류 로그에 기록된 문제
- 시간에 따른 차단 프로세스와 SQL 대기 통계

운영 체제 원격 분석(Linux): 
- CPU 사용, 포크 수, 실행 및 차단된 프로세스 
- 사용, 캐시, 버퍼링된 메모리 사용 및 배포 
- 스왑 사용, 페이징 및 스왑 비율 
- 파일 시스템 사용량, 블록 디바이스당 읽고 쓴 바이트 수 
- 블록 디바이스당 읽기/쓰기 대기 시간 
- 지속 I/O 수, 영구 메모리 읽기/쓰기 바이트 
- 네트워크 패킷 입출력, 네트워크 바이트 입출력 

SAP NetWeaver 원격 분석:

- 디스패처, ICM, 게이트웨이, 메시지 서버, 큐에 넣기 서버, IGS 워치독의 인스턴스 프로세스 가용성을 포함한 SAP 시스템 및 애플리케이션 서버 가용성
- 업무 프로세스 사용 통계 및 추세
- 큐에 넣은 잠금 통계 및 추세
- 큐 사용 통계 및 추세

## <a name="data-sharing-with-microsoft"></a>Microsoft와 데이터 공유

SAP 솔루션을 위한 Azure Monitor는 시스템 메타데이터를 수집하여 Azure의 SAP에 대한 향상된 지원을 제공합니다. PII/EUII는 수집되지 않습니다.

드롭다운에서 *공유* 를 선택하여 SAP 솔루션을 위한 Azure Monitor 리소스를 만들 때 Microsoft와 데이터 공유를 사용하도록 설정할 수 있습니다. 데이터 공유를 사용하도록 설정하는 것이 좋습니다. 데이터 공유는 Microsoft 지원 및 엔지니어링 팀에 사용자 환경에 대한 정보를 제공하여 중요 업무용 Azure의 SAP 솔루션에 대한 더 나은 지원을 제공하는 데 도움이 됩니다.

## <a name="architecture-overview"></a>아키텍처 개요

다음 다이어그램은 SAP 솔루션을 위한 Azure Monitor가 SAP HANA 데이터베이스에서 원격 분석 데이터를 수집하는 방법을 개략적으로 보여 줍니다. 아키텍처는 SAP HANA가 Azure VM에 배포되든 Azure 대규모 인스턴스에 배포되든 동일합니다.

![SAP 솔루션을 위한 Azure Monitor 아키텍처](https://user-images.githubusercontent.com/75772258/115046700-62ff3280-9ef5-11eb-8d0d-cfcda526aeeb.png)

해당 아키텍처의 중요 구성 요소는 다음과 같습니다.
- **Azure Portal** – 시작점입니다. Azure Portal 내에서 Marketplace로 이동하여 SAP 솔루션을 위한 Azure Monitor를 검색할 수 있습니다.
- **SAP 솔루션을 위한 Azure Monitor** - 리소스는 모니터링 원격 분석을 볼 수 있는 기본 위치입니다.
- **관리되는 리소스 그룹** – SAP 솔루션을 위한 Azure Monitor 리소스 배포의 일부로 자동으로 배포됩니다. 관리되는 리소스 그룹 내에 배포된 리소스는 원격 분석 데이터를 수집하는 데 도움이 됩니다. 배포된 주요 리소스와 그 용도는 다음과 같습니다.
   - **Azure 가상 머신**: *수집기 VM* 이라고도 하며 Standard_B2ms VM입니다. 해당 VM의 주 용도는 *모니터링 페이로드* 를 호스트하는 것입니다. 페이로드 모니터링은 원본 시스템에서 원격 분석 데이터를 수집하고, 해당 데이터를 모니터링 프레임워크로 전송하는 논리를 나타냅니다. 위의 다이어그램에서 모니터링 페이로드에는 SQL 포트를 통해 SAP HANA 데이터베이스에 연결하는 논리가 포함되어 있습니다.
   - **[Azure Key Vault](../../../key-vault/general/basic-concepts.md)** : 이 리소스는 SAP HANA 데이터베이스 자격 증명을 안전하게 보관하고 [공급자](./azure-monitor-providers.md)에 대한 정보를 저장하기 위해 배포됩니다.
   - **Log Analytics 작업 영역**: 원격 분석 데이터가 저장되는 대상입니다.
      - 시각화는 [Azure 통합 문서](../../../azure-monitor/visualize/workbooks-overview.md)를 사용하여 Log Analytics에서 원격 분석을 기반으로 빌드됩니다. 시각화를 사용자 지정할 수 있습니다. 자동 새로 고침을 위해 통합 문서 또는 통합 문서 내의 특정 시각화를 Azure 대시보드에 고정할 수도 있습니다. 새로 고침의 최대 빈도는 30분마다입니다.
      - 배포 시 해당 옵션을 선택하여 SAP 모니터 리소스와 동일한 구독 내에 위치한 기존의 작업 영역을 사용할 수 있습니다.
      - KQL(Kusto 쿼리 언어)을 사용하여 Log Analytics 작업 영역 내의 원시 테이블에 대해 [쿼리](../../../azure-monitor/logs/log-query-overview.md)를 실행할 수 있습니다. *사용자 지정 로그* 확인

> [!Note]
> 관리되는 리소스 그룹 내에 배포된 VM의 패치와 유지를 담당합니다.

> [!Tip]
> 원격 분석 컬렉션을 위한 기존의 Log Analytics 작업 영역이 SAP 솔루션을 위한 Azure Monitor용 리소스로서 동일한 Azure 구독 내에 배포된 경우 이를 사용할 수 있습니다.

### <a name="architecture-highlights"></a>아키텍처 주요 내용

아키텍처의 주요 내용은 다음과 같습니다.
 - **다중 인스턴스** - SAP 솔루션을 위한 Azure Monitor용 단일 리소스를 이용해 단일 VNET 내의 여러 SAP SID에 걸쳐 주어진 구성 요소 유형(예: HANA 데이터베이스, HA(고가용성) 클러스터, Microsoft SQL Server, SAP NetWeaver)의 여러 인스턴스를 모니터링할 수 있습니다.
 - **다중 공급자** - 앞의 아키텍처 다이어그램은 SAP HANA 공급자를 예로써 보여 줍니다. 마찬가지로 해당 구성 요소에 대해 더 많은 공급자를 구성하여 해당 구성 요소에서 데이터를 수집할 수 있습니다. 예를 들어 HANA DB, HA 클러스터, Microsoft SQL Server 및 SAP NetWeaver가 있습니다. 
 - **오픈 소스** - SAP 솔루션을 위한 Azure Monitor의 소스 코드는 [GitHub](https://github.com/Azure/AzureMonitorForSAPSolutions)에서 이용할 수 있습니다. 공급자 코드를 참조하여 제품 및 기여에 대한 자세한 정보를 확인하고 피드백을 공유할 수 있습니다.
 - **확장 가능한 쿼리 프레임워크** - 원격 분석 데이터 수집을 위한 SQL 쿼리는 [JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json)으로 작성됩니다. 원격 분석 데이터를 더 많이 수집하기 위해 SQL 쿼리를 쉽게 추가할 수 있습니다. SAP 솔루션을 위한 Azure Monitor에 추가할 특정 원격 분석 데이터를 요청할 수 있습니다. 이 문서 끝에 있는 링크를 사용하여 피드백을 남겨주세요. 계정 팀에 연락하여 피드백을 남길 수도 있습니다.

## <a name="analyzing-metrics"></a>메트릭 분석

SAP 솔루션을 위한 Azure Monitor는 메트릭을 지원하지 않습니다. 

## <a name="analyzing-logs"></a>로그 분석

SAP 솔루션을 위한 Azure Monitor는 리소스 로그 또는 활동 로그를 지원하지 않습니다. Log Analytics에서 쿼리할 수 있는 Azure Monitor Logs에서 사용하는 테이블 목록은 [Azure의 SAP 모니터링 데이터 참조](monitor-sap-on-azure-reference.md#azure-monitor-logs-tables)를 참조하세요. 

### <a name="sample-kusto-queries"></a>샘플 Kusto 쿼리

> [!IMPORTANT]
> SAP 솔루션을 위한 Azure Monitor 메뉴에서 **로그** 를 선택하면 현재 SAP 솔루션을 위한 Azure Monitor로 설정된 쿼리 범위와 함께 Log Analytics가 열립니다. 즉, 로그 쿼리에는 해당 리소스의 데이터만 포함됩니다. 다른 Azure 서비스의 데이터 또는 다른 계정의 데이터를 포함하는 쿼리를 실행하려면 **Azure Monitor** 메뉴에서 **로그** 를 선택합니다. 자세한 내용은 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](../../../azure-monitor/logs/scope.md)를 참조하세요.

Kusto 쿼리를 사용하여 Azure의 SAP 모니터링 리소스를 모니터링할 수 있습니다. 다음은 지정된 시간 범위에 대한 사용자 지정 로그의 데이터를 제공하는 샘플 쿼리입니다. 시간 범위와 행 수를 지정합니다. 이 예에서는 선택한 시간 범위에 대한 5개의 데이터 행을 가져옵니다. 

```Kusto
custom log name 
| take 5

```

## <a name="alerts"></a>경고

Azure Monitor 경고는 모니터링 데이터에서 중요한 조건이 발견될 때 사용자에게 사전에 알립니다. 이를 통해 고객이 알기 전에 시스템 문제를 식별하고 해결할 수 있습니다.

Azure Portal에서 SAP 솔루션을 위한 Azure Monitor에서 경고를 구성할 수 있습니다. 자세한 내용은 [Azure Portal을 사용하여 SAP 솔루션을 위한 Azure Monitor에서 경고 구성](azure-monitor-alerts-portal.md)을 참조하세요.

## <a name="create-azure-monitor-for-sap-solutions-resources"></a>SAP 솔루션을 위한 Azure Monitor 리소스 만들기

SAP 솔루션을 위한 Azure Monitor를 배포하고 공급자를 구성하는 몇 가지 옵션이 있습니다. 
- Azure Portal에서 바로 SAP 솔루션을 위한 Azure Monitor를 배포할 수 있습니다. 자세한 내용은 [Azure Portal을 사용하여 SAP 솔루션을 위한 Azure Monitor 배포](azure-monitor-sap-quickstart.md)를 참조하세요.
- Azure PowerShell 사용 자세한 내용은 [Azure PowerShell을 사용하여 SAP 솔루션을 위한 Azure Monitor 배포](azure-monitor-sap-quickstart-powershell.md)를 참조하세요.
- CLI 확장을 사용합니다. 자세한 내용은 [SAP HANA 명령 모듈](https://github.com/Azure/azure-hanaonazure-cli-extension#sapmonitor)을 참조하세요.

## <a name="pricing"></a>가격 책정
SAP 솔루션을 위한 Azure Monitor는 무상 제품입니다(라이선스 요금이 없음). 관리되는 리소스 그룹 내의 기본 구성 요소에 대한 비용을 지불할 책임이 있습니다. 데이터 사용 및 보존과 관련된 사용 비용도 사용자가 부담해야 합니다. 자세한 내용은 표준 Azure 가격 책정 문서를 참조하세요.
- [Azure VM 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)
- [Azure Key Vault 가격 책정](https://azure.microsoft.com/pricing/details/key-vault/)
- [Azure Storage 계정 가격 책정](https://azure.microsoft.com/pricing/details/storage/queues/)
- [Azure Log Analytics 및 경고 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)

## <a name="next-steps"></a>다음 단계

- SAP 솔루션을 위한 Azure Monitor와 관련된 사용자 지정 로그 목록 및 관련 데이터 유형에 대한 정보는 [Azure의 SAP 모니터링 데이터 참조](monitor-sap-on-azure-reference.md)를 참조하세요.
- SAP 솔루션을 위한 Azure Monitor에 사용할 수 있는 공급자에 대한 자세한 내용은 [SAP 솔루션을 위한 Azure Monitor 공급자](azure-monitor-providers.md)를 참조하세요.
