---
title: 메인프레임 스토리지를 Azureob Storage 이동
description: 대규모 확장 가능한 Azureob Storage 리소스는 메인프레임 기반 조직에서 IBM z14 애플리케이션을 마이그레이션하고 현대화하는 데 도움이 될 수 있습니다.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 853fee40417bc09168fa1cfba1428b8e99bf9a86
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130072749"
---
# <a name="move-mainframe-storage-to-azure"></a>메인프레임 스토리지를 Azure로 이동

Microsoft Azure에서 메인프레임 워크로드를 실행하려면 메인프레임 기능이 Azure와 어떻게 비교되는지 알아야 합니다. 대규모 확장 가능한 스토리지 리소스를 사용하면 조직에서 사용하는 애플리케이션을 포기하지 않고도 현대화를 시작할 수 있습니다.

Azure는 IBM z14 기반 시스템(이 문서 작성 시점의 최신 모델)과 비교할 수 있는 메인프레임 기능 및 스토리지 용량을 제공합니다. 이 문서에서는 Azure에서 비슷한 결과를 얻는 방법을 설명합니다.

## <a name="mainframe-storage-at-a-glance"></a>메인프레임 스토리지 개요

IBM 메인프레임은 두 가지 방법으로 스토리지의 특징을 나타냅니다. 첫 번째는 DASD(직접 액세스 저장 디바이스)입니다. 두 번째는 순차적 스토리지입니다. 스토리지 관리를 위해 메인프레임은 DFSMS(데이터 기능 스토리지 관리 하위 시스템)를 제공합니다. 다양한 저장 디바이스에 대한 데이터 액세스를 관리합니다.

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device)는 데이터에 직접 액세스하는 데 고유한 주소를 사용할 수 있는 보조(메모리 내 아님) 스토리지를 위한 별도의 디바이스를 나타냅니다. 원래 DASD라는 용어는 회전 디스크, 자기 드럼 또는 데이터 셀에 적용되었습니다. 그러나 이제 이 용어는 SSD(반도체 스토리지 디바이스), SAN(저장 영역 네트워크), NAS(네트워크 연결 스토리지) 및 광학 드라이브에도 적용될 수 있습니다. 이 문서에서는 DASD가 회전 디스크, SAN 및 SSD를 나타냅니다.

DASD 스토리지와 달리 메인프레임의 순차 스토리지는 시작 지점에서 데이터에 액세스한 다음 한 줄로 읽거나 쓰는 테이프 드라이브와 같은 디바이스를 나타냅니다.

스토리지 디바이스는 일반적으로 파이버 연결(FICON)을 사용하여 연결되거나 하이퍼바이저가 있는 서버의 파티션 간 고속 통신을 위한 IBM 기술인 [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm)를 사용하여 메인프레임의 IO 버스에서 직접 액세스됩니다.

대부분의 메인프레임 시스템은 두 가지 유형으로 스토리지를 구분합니다.

- 매일의 작업에는 *온라인 스토리지*(핫 스토리지라고도 함)가 필요합니다. 이를 위해 일반적으로는 DASD 스토리지를 사용합니다. 그러나 매일 테이프 백업(논리적 또는 물리적)과 같은 순차 스토리지를 이를 위해 사용할 수도 있습니다.

- *보관 스토리지*(콜드 스토리지라고도 함)는 지정된 시간에 탑재되지 않을 수도 있습니다. 대신, 필요에 따라 탑재되고 액세스됩니다. 보관 스토리지에 대한 순차적 테이프 백업(논리적 또는 물리적)을 사용하여 구현되는 경우가 많습니다.

## <a name="mainframe-versus-io-latency-and-iops"></a>메인프레임 및 IO의 지연 시간 및 IOPS

메인프레임은 고성능 IO 및 낮은 IO 대기 시간이 필요한 애플리케이션에 자주 사용됩니다. IO 디바이스 및 HiperSocket에 대한 FICON 연결을 사용하여 이 작업을 수행할 수 있습니다. HiperSocket을 사용하여 애플리케이션과 디바이스를 메인프레임의 IO 채널에 직접 연결하면 마이크로 초 단위의 지연 시간을 달성할 수 있습니다.

## <a name="azure-storage-at-a-glance"></a>Azure Storage 개요

스토리지의 Azure [IaaS](https://azure.microsoft.com/overview/what-is-iaas/)(서비스 제공 인프라) 옵션은 비슷한 메인프레임 용량을 제공합니다.

Microsoft는 Azure에서 호스트되는 애플리케이션을 위해 페타바이트급의 스토리지를 제공하고, 몇 가지 스토리지 옵션을 제공합니다. 이 옵션의 범위는 고성능을 위한 SSD 스토리지부터 대용량 스토리지 및 보관을 위한 저렴한 Blob Storage까지입니다. 또한 Azure는 스토리지에 대한 데이터 중복성 옵션을 제공하며, 이는 메인프레임 환경에서 설정하는 데 더 많은 노력이 필요합니다.

다음 표에 요약된 대로 Azure Storage는 [Azure 디스크](../../../managed-disks-overview.md), [Azure Files](../../../../storage/files/storage-files-introduction.md) 및 [Azure Blob](../../../../storage/blobs/storage-blobs-overview.md)으로 사용할 수 있습니다. [각각의 사용 시기](../../../../storage/common/storage-introduction.md)에 대해 자세히 알아봅니다.

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>유형</th><th>Description</th><th>다음을 수행하려는 경우에 사용합니다.</th></tr>
</thead>
<tbody>
<tr><td>Azure 파일
</td>
<td>
어디에서나 저장된 파일에 액세스할 수 있는 SMB 인터페이스, 클라이언트 라이브러리 및 <a href="/rest/api/storageservices/file-service-rest-api">REST</a> 인터페이스를 제공합니다.
</td>
<td><ul>
<li>애플리케이션이 기본 파일 시스템 API를 사용할 때 클라우드로 애플리케이션을 전환하여 Azure에서 실행 중인 다른 애플리케이션과 데이터를 공유합니다.</li>
<li>여러 VM에서 액세스해야 하는 개발 및 디버깅 도구를 저장합니다.</li>
</ul>
</td>
</tr>
<tr><td>Azure Blob
</td>
<td>구조화되지 않은 데이터를 블록 Blob에서 대규모로 저장 및 액세스할 수 있도록 클라이언트 라이브러리 및 <a href="/rest/api/storageservices/blob-service-rest-api">REST</a> 인터페이스를 제공합니다. 엔터프라이즈 빅 데이터 분석 솔루션을 위한 <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a>도 지원합니다.
</td>
<td><ul>
<li>애플리케이션에서 스트리밍 및 임의 액세스 시나리오를 지원합니다.</li>
<li>어디에서나 애플리케이션 데이터에 액세스할 수 있습니다.</li>
<li>Azure에서 엔터프라이즈 Data Lake를 빌드하고 빅 데이터 분석 수행</li>
</ul></td>
</tr>
<tr><td>Azure 디스크
</td>
<td>데이터를 연결된 가상 하드 디스크에서 영구적으로 저장 및 액세스할 수 있도록 클라이언트 라이브러리 및 <a href="/rest/api/compute/disks">REST</a> 인터페이스를 제공합니다.
</td>
<td><ul>
<li>데이터를 영구 디스크에 읽고 쓰도록 기본 파일 시스템 API를 사용하는 애플리케이션을 전환합니다.</li>
<li>디스크가 연결된 VM 외부에서 액세스할 필요가 없는 데이터를 저장합니다.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure 핫(온라인) 및 콜드(보관) 스토리지

지정된 시스템의 스토리지 유형은 스토리지 크기, 처리량 및 IOPS를 비롯한 시스템의 요구 사항에 따라 달라집니다. Azure의 애플리케이션은 메인프레임의 DASD 유형 스토리지에 대해 일반적으로 Azure 디스크 드라이브 스토리지를 대신 사용합니다. 메인프레임 보관 스토리지의 경우 Azure에서 Blob Storage가 사용됩니다.

SSD는 Azure에서 가장 높은 스토리지 성능을 제공합니다. 사용할 수 있는 옵션은 다음과 같습니다(이 문서 작성 시점).

| 유형         | 크기           | IOPS                  |
|--------------|----------------|-----------------------|
| 울트라 SSD    | 4GB~64TB  | 1200 ~ 160000 IOPS |
| 프리미엄 SSD  | 32GB~32TB | 12 ~ 15000 IOPS     |
| 표준 SSD | 32GB~32TB | 12 ~ 2000 IOPS      |

Blob Storage는 Azure에서 가장 큰 스토리지 용량을 제공합니다. 스토리지 크기 외에도 Azure는 관리되는 스토리지 및 관리되지 않는 스토리지를 모두 제공합니다. Azure는 관리되는 스토리지를 사용하여 기본 스토리지 계정을 관리합니다. 관리되지 않는 스토리지를 사용하는 경우 사용자는 스토리지 요구 사항에 맞게 적절한 크기의 Azureob Storage 계정을 설정해야 합니다.

## <a name="next-steps"></a>다음 단계

- [메인프레임 마이그레이션](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Azure Virtual Machines에서 메인프레임 다시 호스팅](../overview.md)
- [메인프레임 컴퓨팅을 Azure로 이동](mainframe-compute-Azure.md)
- [Azure Blob, Azure 파일 또는 Azure 디스크를 사용할 시기 결정](../../../../storage/common/storage-introduction.md)
- [Azure VM 워크로드의 표준 SSD 관리 디스크](../../../disks-types.md#standard-ssds)

### <a name="ibm-resources"></a>IBM 리소스

- [IBM Z의 병렬 Sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS 및 결합 기능: 기본 이상](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Db2 pureScale 기능 설치에 대한 필수 사용자 만들기](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - 인스턴스 명령 만들기](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale 클러스터 데이터베이스 솔루션](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [메인프레임 애플리케이션의 Microsoft Azure Government 클라우드](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft 및 FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>추가 마이그레이션 리소스

- [Azure Virtual Data Center 리프트 앤 시프트 가이드](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://glusterdocs.readthedocs.io/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)