---
title: Azure Files, Blob Storage, Azure NetApp Files에 대한 NFS 액세스 비교
description: Azure Files, Azure Blob Storage, Azure NetApp Files에 대한 NFS 액세스를 비교합니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: rogarana
ms.openlocfilehash: 6f179fe2e91da9e68a0809763bd9f38a5947e8a4
ms.sourcegitcommit: 9caa850a2b26773e238f8ba6f4ca151c47260915
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2021
ms.locfileid: "113600607"
---
# <a name="compare-access-to-azure-files-blob-storage-and-azure-netapp-files-with-nfs"></a>NFS를 사용한 Azure Files, Blob Storage, Azure NetApp Files에 대한 액세스 비교

이 문서에서는 [NFS(네트워크 파일 시스템)](https://en.wikipedia.org/wiki/Network_File_System) 프로토콜을 통해 액세스하는 경우 이러한 각 제품을 비교하여 설명합니다. 다른 메서드를 통해 액세스하는 경우에는 이 비교가 적용되지 않습니다.

보다 일반적인 비교는 Azure Blob Storage와 Azure Files를 비교하려면 [이 문서 ](storage-introduction.md)를 참조하고, Azure Files와 Azure NetApp Files를 비교하려면 [이 문서](../files/storage-files-netapp-comparison.md)를 참조하세요.

## <a name="comparison"></a>비교

|범주  |Azure Blob Storage  |Azure 파일  |Azure NetApp Files  |
|---------|---------|---------|---------|
|사용 사례     |Blob Storage는 데이터를 한 번만 수집하고 최소로 수정하는 대규모 읽기 작업이 많은 순차 액세스 워크로드에 가장 적합합니다.<br></br>유지 관리가 거의 없거나 전혀 없는 경우 Blob Storage는 가장 낮은 총 소유 비용을 제공합니다.<br></br>몇 가지 예제 시나리오는 대규모 분석 데이터, 처리량에 민감한 고성능 컴퓨팅, 백업/보관, 자치 구동, 미디어 렌더링 또는 게놈 시퀀싱입니다.         |Azure Files는 임의 액세스 워크로드에 가장 적합한 고가용성 서비스입니다.<br></br>NFS 공유의 경우 Azure Files는 전체 POSIX 파일 시스템을 지원하며 VM 기반 플랫폼 외에도 기본 제공 CSI 드라이버를 통해 ACI(Azure Container Instance) 및 AKS(Azure Kubernetes Service)와 같은 컨테이너 플랫폼에서 쉽게 사용할 수 있습니다.<br></br>몇 가지 예제 시나리오에는 공유 파일, 데이터베이스, 홈 디렉터리, 기존 애플리케이션, ERP, CMS, 고급 관리가 필요 없는 NAS 마이그레이션, 스케일 아웃 파일 스토리지가 필요한 사용자 지정 애플리케이션 등이 있습니다.         |고급 관리 기능을 통해 NetApp에서 구동하는 클라우드의 완전 관리형 파일 서비스입니다.<br></br>NetApp 파일은 임의 액세스가 필요한 워크로드에 적합하며 광범위한 프로토콜 지원 및 데이터 보호 기능을 제공합니다.<br></br>몇 가지 예제 시나리오에는 다양한 관리 기능이 필요한 온-프레미스 엔터프라이즈 NAS 마이그레이션, SAP HANA와 같은 대기 시간에 민감한 워크로드, 대기 시간에 민감한 또는 IOPS 집약적 고성능 컴퓨팅, 동시 멀티 프로토콜 액세스를 필요로 하는 워크로드 등이 있습니다.         |
|사용 가능한 프로토콜     |NFS 3.0<br></br>REST (영문)<br></br>Data Lake Storage Gen2         |SMB<br><br>NFS 4.1(미리 보기)<br></br> (두 프로토콜 간의 상호 운용성 없음)         |NFS 3.0 및 4.1<br></br>SMB         |
|주요 특징     | 짧은 대기 시간 워크로드를 위해 HPC 캐시와 통합됩니다. <br> </br> 수명 주기, 변경이 불가능한 Blob, 데이터 장애 조치(failover) 및 메타데이터 인덱스를 비롯한 통합 관리         | 고가용성을 위한 영역 중복 <br></br> 일관된 한 자리 밀리초 대기 시간 <br></br>용량으로 스케일링하는 예측 가능한 성능 및 비용         |매우 짧은 대기 시간(하위 밀리초만큼 낮음)<br></br>클라우드의 SnapMirror와 같은 다양한 NetApp ONTAP 관리 기능<br></br>일관된 하이브리드 클라우드 환경         |
|성능(볼륨당)     |최대 2만 IOPS, 최대 100GiB/s 처리량         |최대 10만 IOPS, 최대 80GiB/s 처리량         |최대 46만 IOPS, 최대 36GiB/s 처리량         |
|확장     | 단일 볼륨에 대해 최대 2PiB <br></br> 단일 파일에 대해 최대 ~4.75TiB<br></br>최소 용량 요구 사항이 없음         |단일 파일 공유에 대해 최대 100TiB<br></br>단일 파일에 대해 최대 4TiB<br></br>100GiB 최소 용량         |단일 볼륨에 대해 최대 100TiB<br></br>단일 파일에 대해 최대 16TiB<br></br>일관된 하이브리드 클라우드 환경         |
|가격 책정     |[Azure Blob Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/)         |[Azure Files 가격 책정](https://azure.microsoft.com/pricing/details/storage/files/)         |[Azure NetApp Files 가격 책정](https://azure.microsoft.com/pricing/details/netapp/)         |


## <a name="next-steps"></a>다음 단계

- NFS를 사용하여 Blob 스토리지에 액세스하려면 [Azure Blob Storage에서 NFS(Network File System) 3.0 프로토콜 지원(미리 보기)](../blobs/network-file-system-protocol-support.md)을 참조하세요.
- NFS를 사용하여 Azure Files에 액세스하려면 [Azure Files의 NFS 파일 공유](../files/files-nfs-protocol.md)를 참조하세요.
- NFS를 사용하여 Azure NetApp Files에 액세스하려면 [빠른 시작: Azure NetApp Files 설정 및 NFS 볼륨 만들기](../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md)를 참조하세요.
