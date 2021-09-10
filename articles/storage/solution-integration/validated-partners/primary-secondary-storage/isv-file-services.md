---
title: Azure에서 ISV 파일 서비스를 실행할 때 고려 사항
titleSuffix: Azure Storage
description: Azure에서 파일 서비스를 실행하는 다양한 ISV 옵션에 대한 기본 지침
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 05/24/2021
ms.service: storage
ms.subservice: common
ms.openlocfilehash: 8b49504fdda0b065b28b90f946dd203716933745
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114446139"
---
# <a name="running-isv-file-services-in-azure"></a>Azure에서 ISV 파일 서비스 실행

Azure는 파일 데이터를 저장하는 다양한 옵션을 제공합니다. Azure 네이티브 서비스는 다음과 같습니다.
- [Azure Files](https://azure.microsoft.com/services/storage/files/) - 클라우드의 완전 관리형 파일 공유이며 산업 표준 SMB 및 NFS 프로토콜을 통해 액세스할 수 있습니다. Azure Files는 성능 특성이 다른 두 가지 유형(표준 및 프리미엄)을 제공합니다.
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) – 엔터프라이즈 LOB(기간 업무) 애플리케이션에 대한 성능 요구 사항을 충족하도록 설계된 클라우드의 완전 관리형 파일 공유입니다. Azure NetApp Files는 성능 제한이 다른 여러 서비스 수준(표준, 프리미엄 및 울트라)을 제공합니다.
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) – 비정형 데이터를 저장하는 대규모 개체 스토리지 플랫폼입니다. Azure Blob Storage는 성능 특성이 다른 두 가지 유형(표준 및 프리미엄)을 제공합니다. 
  
네이티브 파일 서비스의 차이점과 서비스 선택 시 권장 사항에 대해 설명하는 여러 가지 문서가 있습니다. 다음 내용에 대해 자세히 알아볼 수 있습니다.
- [기본 흐름도](../../../common/storage-migration-overview.md#choose-a-target-storage-service)를 설명하는 마이그레이션 가이드
- [Azure Files와 Azure NetApp Files의 자세한 비교](../../../files/storage-files-netapp-comparison.md)

Azure에서 파일 서비스를 제공하는 많은 ISV(독립 소프트웨어 공급업체) 솔루션이 있습니다. 이 문서에서는 다음 두 가지 토픽을 다룹니다.
- 파일 서비스 선택 시 일반적인 고려 사항을 제공합니다.
- ISV 솔루션 간의 차이점을 간략하게 설명합니다.
  
확인된 ISV 솔루션의 전체 목록은 [기본 및 보조 스토리지를 위한 Azure Storage 파트너](./partner-overview.md)에서 확인할 수 있습니다.

## <a name="considerations"></a>고려 사항

Azure에서 파일 서비스를 실행하는 데 가장 적합한 솔루션을 선택할 때 여러 가지 중요한 사항을 고려해야 합니다.

### <a name="basic-considerations"></a>기본 고려 사항

기본 기능 고려 단계에서는 기본 스토리지 플랫폼의 가장 일반적인 기능을 검사합니다. 이 단계는 처음에 요구 사항을 충족하는 데 필요한 기본 기능을 제공하는 솔루션을 살펴보기 위해 수행됩니다. 중요한 기본 기능은 상황에 따라 다릅니다. 가장 일반적인 기능은 프로토콜 지원, 네임스페이스 크기, 자동 계층화, 암호화, WORM 지원 및 인증입니다. ISV 솔루션에서 지원하는 기본 기능 목록은 [기능 비교](#isv-solutions-comparison)에서 확인할 수 있습니다.

### <a name="performance-considerations"></a>성능 고려 사항

기본 기능을 충족하는 솔루션을 선택한 후에는 필요한 성능을 고려해야 합니다. 성능 적합성 예측은 다음과 같은 세 가지 기본 성능 특성에 따라 달라집니다.
  - 대기 시간
  - 대역폭
  - IOPS 또는 TPS

기본 성능 특성의 중요성은 워크로드의 동시성(동시 요청 수) 및 블록 크기(요청 크기)에 따라 달라집니다.

| 워크로드 유형 | 권장 사항 |
| -------------------- | --------------- |
| **낮은 동시성** | 대기 시간이 가장 중요한 고려 사항입니다. 대기 시간이 짧을수록 더 높은 성능을 달성할 수 있습니다. 동시성이 낮은 워크로드에서는 IOPS 및 대역폭 제한을 초과하는 경우가 거의 없습니다. |
| **높은 동시성** | 높은 동시성으로 인해 대기 시간의 영향이 훨씬 작습니다. IOPS 및/또는 대역폭을 고려해야 합니다. |
| **블록 크기** | 워크로드의 블록 크기가 작은 경우에는 IOPS 제한이 더 중요하지만, 워크로드의 블록 크기가 큰 경우에는 대역폭 제한이 더 중요합니다. |

이러한 특성을 염두에 두고 모든 스토리지 워크로드를 설명할 수 있습니다. 예를 들어 OLTP 워크로드는 일반적으로 동시성이 높고 블록 크기가 작습니다. HPC 워크로드는 일반적으로 동시성이 높지만 블록 크기는 작은 크기부터 큰 크기까지 다양합니다. 
    
다음은 항상 권장되는 몇 가지 일반적인 규칙입니다.
  - 프로토콜 선택: 되도록이면 다중 채널을 지원하는 SMB3 또는 nconnect를 지원하는 NFSv3를 사용합니다. 보다 강력한 성능을 제공하기 때문입니다. 성능뿐 아니라 보안의 관점에서도 레거시 프로토콜을 사용하지 말아야 합니다. 또한 클라이언트를 튜닝할 수 있으며 성능을 최대화하는 것이 좋습니다.
  - 네트워킹: VM 유형에서 지원하는 경우 가속 네트워킹을 사용합니다. 대기 시간이 감소하며 항상 성능에 긍정적인 영향을 줍니다. 
  - VM 유형: 워크로드에 가장 적합한 VM 유형을 선택합니다. 워크로드의 클라이언트 수가 적은 경우 적은 수의 큰 VM에서 파일 서비스를 실행하는 것이 더 적합합니다. 반대로 클라이언트 수가 많은 경우에는 많은 수의 작은 VM에서 파일 서비스를 실행하는 것이 좋습니다.
  - 동시성이 낮고 블록 크기가 작은 워크로드의 경우 다음과 같은 솔루션이 좋습니다.
    - 관리 디스크(프리미엄 또는 울트라 SSD 디스크)를 사용하는 솔루션 또는
    - 적절한 캐싱 알고리즘이 있는 솔루션
  - 동시성이 높고 블록 크기가 큰 워크로드의 경우 Azure Blob Storage를 백 엔드로 사용하는 솔루션이 좋습니다.

## <a name="isv-solutions-overview-and-example-use-cases"></a>ISV 솔루션: 개요 및 사용 사례 예제

이 문서에서는 Azure에서 파일 서비스를 제공하는 여러 ISV 솔루션을 비교합니다.

| 솔루션 | 개요 | 예제 사용 사례 |
| -------- | ----------- | ----------------- |
| **Nasuni** | **UniFS** 는 Microsoft Azure를 기반으로 하는 간단하고 저렴한 클라우드 대체 기능이 있는 엔터프라이즈 파일 서비스입니다. | - 기본 파일 스토리지 <br> - 부서별 파일 공유 <br> - 중앙 집중식 파일 관리 <br> - 전역 파일 잠금을 통해 다중 사이트 협업 <br> - Windows Virtual Desktop <br> - 원격 작업/VDI 파일 공유 |
| **NetApp** | **클라우드 볼륨 ONTAP** 는 데이터 보호, 보안 및 규정 준수를 향상하면서도 클라우드 스토리지 비용과 성능을 최적화합니다. 엔터프라이즈급 데이터 관리, 가용성 및 내구성을 제공합니다. | - 비즈니스 애플리케이션 <br> - 관계형 데이터베이스 및 NoSQL 데이터베이스 <br> - 빅 데이터 및 Analytics <br> - 컨테이너에 대한 영구 데이터 <br> - CI/CD 파이프라인 <br> - 온-프레미스 NetApp 솔루션의 재해 복구 |
| **Panzura**| **CloudFS** 는 복원력과 고성능이 추가된 엔터프라이즈 전역 파일 시스템입니다. 랜섬웨어를 차단합니다. | - 간단한 레거시 스토리지 교체 <br> - 세분화된 복구 기능을 통한 백업 및 재해 복구 <br> - Analytics, AI/ML의 비정형 데이터에 대한 클라우드 네이티브 액세스 <br> - 자동 파일 잠금 및 실시간 전역 파일 일관성을 갖춘 다중 사이트 파일 협업 <br> - 클라우드 VDI를 사용하여 글로벌 원격 작업 <br> - 레거시 워크로드의 신속한 클라우드 마이그레이션 |
| **Tiger Technology** | **Tiger Bridge** 는 데이터 관리 소프트웨어 솔루션입니다. NTFS 파일 시스템과 Azure Blob Storage 또는 Azure 관리 디스크 간에 계층화를 제공합니다. 로컬 파일 잠금을 통해 단일 네임스페이스를 만듭니다. | - 클라우드 보관<br> - CDP(연속 데이터 보호) <br> - Windows 서버의 재해 복구 <br> - 다중 사이트 동기화 및 협업 <br> - 원격 워크플로(VDI)<br> - Analytics, AI, ML의 클라우드 데이터에 대한 기본 액세스 |
| **XenData** | **Cloud File Gateway** 는 Windows 파일 서버를 사용하여 확장성이 뛰어난 전역 파일 시스템을 만듭니다. | - 엔지니어링 및 과학적 파일의 전역 공유 <br> - 비디오 편집 협업 |

## <a name="isv-solutions-comparison"></a>ISV 솔루션 비교

### <a name="supported-protocols"></a>지원되는 프로토콜

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **SMB 2.1**                                         | 예                  | 예                            | 예                       | 예                   | 예                   |
| **SMB 3.0**                                         | 예                  | 예                            | 예                       | 예                   | 예                   |
| **SMB 3.1**                                         | 예                  | 예                            | 예                       | 예                   | 예                   |
| **NFS v3**                                          | 예                  | 예                            | 예                       | 예                   | 예                   |
| **NFS v4.1**                                        | 예                  | 예                            | 예                       | 예                   | 예                   |
| **iSCSI**                                           | 아니요                   | 예                            | 아니요                        | 예                   | 아니요                    |

### <a name="supported-services-for-persistent-storage"></a>영구 스토리지를 지원하는 서비스

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **관리 디스크**                                   | 아니요                   | 예                            | 예                       | 예                   | 아니요                    |
| **관리되지 않는 디스크**                                 | 아니요                   | 아니요                             | 아니요                        | 예                   | 아니요                    |
| **Azure Storage Block Blob**                       | Yes                  | 예(계층화)                  | 예                       | 예                   | 예                   |
| **Azure Storage 페이지 Blob**                        | No                   | 예(HA의 경우)                   | 예                       | 아니요                    | 아니요                    |
| **Azure 보관 계층 지원**                      | 아니요                   | 아니요                             | 예                       | 예                   | 예                   |
| **불투명하지 않은 형식으로 액세스할 수 있는 파일**           | 아니요                   | 아니요                             | 아니요                        | 예                   | 예                   |

### <a name="extended-features"></a>확장된 기능

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **작동 환경**                           | UniFS                | ONTAP                          | PFOS                      | Windows Server        | Windows Server              |
| **고가용성**                               | 예                  | 예                            | 예                       | 예(설정 필요)  | Yes                   |
| **클러스터의 노드 간 자동 장애 조치(failover)** | 예                  | 예                            | 예                       | 예(Windows 클러스터) | 예(Windows 클러스터) |
| **가용성 영역 간 자동 장애 조치(failover)**    | 예                  | 아니요                             | 예                       | 예(Windows 클러스터) | 예(Windows 클러스터) |
| **지역 간 자동 장애 조치(failover)**               | 예(Nasuni 지원 포함)| 아니요                         | 아니요                        | 예(Windows 클러스터) | 예(Windows 클러스터) |
| **스냅샷 지원**                                | 예                  | 예                            | 예                       | 예                   | 아니요                    |
| **일치 스냅샷 지원**:                     | 예                  | 예                            | 예                       | 예                   | 아니요                    |
| **통합 백업**                               | Yes                  | 예(추가 기능)                   | 아니요                        | 예                   | 예                   |
| **버전 관리**                                      | 예                  | 예                            | 아니요                        | 예                   | 예                   |
| **파일 수준 복원**                              | 예                  | 예                            | 예                       | 예                   | 예                   |
| **볼륨 수준 복원**                            | 예                  | 예                            | 예                       | 예                   | 예                   |
| **WORM**                                            | 예                  | 예                            | 아니요                        | 예                   | 아니요                    |
| **자동 계층화**                               | 예                  | 예                            | 아니요                        | 예                   | 예                   |
| **전역 파일 잠금**                             | Yes                  | 예(NetApp 전역 파일 캐시) | 예                       | 예                   | 예                   |
| **백 엔드 원본에 대한 네임스페이스 집계**      | 예                  | 예                            | 아니요                        | 예                   | 예                   |
| **활성 데이터 캐싱**                          | 예                  | 예                            | 예                       | 예                   | 예                   |
| **지원되는 캐싱 모드**                         | LRU, 수동으로 고정 | LRU                            | LRU, 수동으로 고정      | LRU                   | LRU                   |
| **미사용 암호화**                              | 예                  | 예                            | 예                       | 예                   | 아니요                    |
| **중복 제거**                                  | 예                  | 예                            | 예                       | 아니요                    | 아니요                    |
| **압축**                                     | 예                  | 예                            | 예                       | 아니요                    | 아니요                    |

### <a name="authentication-sources"></a>인증 원본

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **Azure AD 지원**                                | 예(ADDS를 통해)       | 예(ADDS를 통해)                 | 예(ADDS를 통해)            | 예(ADDS를 통해)        | 예(ADDS를 통해)        |
| **Active Directory 지원**                        | 예                  | 예                            | 예                       | 예                   | 예                   |
| **LDAP 지원**                                    | 예                  | 예                            | 아니요                        | 예                   | 예                   |

### <a name="management"></a>관리

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **REST API**                                        | 예                  | 예                            | 예                       | 예                   | 아니요                    |
| **웹 GUI**                                         | 예                  | 예                            | 예                       | 아니요                    | 아니요                    |

### <a name="scalability"></a>확장성

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **단일 클러스터의 최대 노드 수**     | 100                  | 2(HA)                         | 최대 60개 노드에서 테스트    | 해당 없음                 | 해당 없음                 |
| **최대 볼륨 수**                       | 800                  | 1024                           | 제한 없음                 | 해당 없음                 | 1                     |
| **최대 스냅샷 수**                     | 제한 없음            | 제한 없음                      | 제한 없음                 | 해당 없음                 | 해당 없음                 |
| **단일 네임스페이스의 최대 크기**              | 제한 없음            | 인프라에 따라 다름      | 제한 없음                 | 해당 없음                 | 해당 없음                 |

### <a name="licensing"></a>라이선스

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **BYOL**                                            | 예                  | 예                            | 예                       | 예                   | 예                   |
| **Azure 혜택 자격**                          | 아니요                   | 예                            | 예                       | 아니요                    | 아니요                    |
| **배포 모델(IaaS, SaaS)**                   | IaaS                 | IaaS                           | IaaS                      | IaaS                  | IaaS                  |

### <a name="other-features"></a>기타 기능

**Nasuni**
- [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/nasunicorporation.nasuni-nea-90-prod?tab=Overview)
- 데이터 캐싱에 관리 디스크 사용 가능
- 모든 Edge 어플라이언스의 중앙 집중식 관리
- 무제한 파일 버전 관리 기록
- 무제한 파일 시스템 크기
- 무제한 파일 크기
- HTTPS, REST API 및 FTP를 통해 파일에 액세스
- Azure Load Balancer를 사용하여 여러 파일의 부하 분산
- 고객 관리형 키로 암호화
- 타사 보안 및 감사 도구(예: Varonis, Stealthbits) 지원
- 화이트 글러브 배포 및 전문 서비스

**NetApp**
- [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/netapp.netapp-ontap-cloud?tab=Overview)
- 중복을 제거하여 인프라 사용량이 감소하므로 고객의 비용 절감

**Panzura**
- [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/panzura-file-system.panzura-freedom-filer)
- 바이트 수준 잠금(여러 개의 동시 R/W 열기)

**Tiger Technology**
- 애플리케이션에 표시되지 않음
- 부분 복원
- Windows 셸 통합(오버레이, 바로 가기 메뉴, 속성 시트)
- Azure 일시 삭제 및 삭제 취소 지원
- 클라우드 대상에 이름 바꾸기를 적용하는 옵션
- 개체에 부분 쓰기
- 랜섬웨어 보호

**XenData**
- Cosmos DB 서비스는 글로벌 협업을 위한 애플리케이션별 소유자 파일을 포함하여 여러 게이트웨이의 빠른 동기화 제공 
- 각 게이트웨이는 로컬로 캐시된 콘텐츠를 매우 세밀하게 제어
- 비디오 스트리밍 및 부분 파일 복원 지원
- Azure Data Box 업로드 지원
- Azure Blob Storage에서 제공하는 암호화

## <a name="next-steps"></a>다음 단계

자세한 정보:

- [Azure 디스크](../../../../virtual-machines/managed-disks-overview.md)
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)
- [기본 및 보조 스토리지에 대해 검증된 파트너](./partner-overview.md)
- [스토리지 마이그레이션 개요](../../../common/storage-migration-overview.md)
