---
title: Azure 파일 동기화 소개 | Microsoft Docs
description: 업계 표준 SMB 프로토콜을 사용하여 클라우드에서 네트워크 파일 공유를 만들고 사용할 수 있게 해주는 서비스인 Azure 파일 동기화를 간략히 설명합니다.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 04/19/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e20b911b16ee7ebfe1af4984c9fe701a54b855cd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664601"
---
# <a name="what-is-azure-file-sync"></a>Azure 파일 동기화란?

Azure 파일 동기화를 사용하면 Windows 파일 서버의 유연성, 성능, 호환성을 유지하면서 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. 일부 사용자는 해당 데이터의 전체 복사본을 로컬로 유지하도록 선택할 수 있지만 Azure 파일 동기화에는 Windows Server를 Azure 파일 공유의 빠른 캐시로 변환하는 기능도 있습니다. SMB, NFS 및 FTPS를 포함하여 로컬로 데이터에 액세스하기 위해 Windows Server에서 사용할 수 있는 모든 프로토콜을 사용할 수 있습니다. 전 세계에서 필요한 만큼 많은 캐시를 가질 수 있습니다.

## <a name="videos"></a>동영상

| Azure 파일 동기화 소개 | 동기화된 Azure Files(Ignite 2019)  |
|-|-|
| [![Azure 파일 동기화 소개 비디오의 동영상 가이드 - 재생하려면 클릭하세요.](../files/media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Sync를 사용하는 Azure Files 프레젠테이션의 동영상 가이드 - 재생하려면 클릭하세요.](../files/media/storage-files-introduction/ignite-2018-video.png)](https://www.youtube.com/embed/6E2p28XwovU) |

## <a name="benefits-of-azure-file-sync"></a>Azure 파일 동기화의 이점

### <a name="cloud-tiering"></a>클라우드 계층화

클라우드 계층화를 사용하도록 설정하면 가장 자주 액세스하는 파일이 로컬 서버에 캐시되고 거의 액세스하지 않는 파일이 클라우드로 계층화됩니다. 캐싱에 사용되는 로컬 디스크 공간의 크기를 제어할 수 있습니다. 계층화된 파일은 주문형으로 신속하게 회수할 수 있으므로 온-프레미스에 데이터의 일부만 저장하면 되므로 비용을 줄일 수 있습니다. 클라우드 계층화에 대한 자세한 내용은 [클라우드 계층화 개요](file-sync-cloud-tiering-overview.md)를 참조하세요.

### <a name="multi-site-access-and-sync"></a>다중 사이트 액세스 및 동기화

Azure 파일 동기화는 분산 액세스 시나리오에 적합합니다. 각 사무실에 대해 Azure 파일 동기화 배포의 일부로 로컬 Windows Server를 프로비저닝할 수 있습니다. 한 사무실의 서버에 대한 변경 내용은 다른 모든 사무실의 서버와 자동으로 동기화됩니다.

### <a name="business-continuity-and-disaster-recovery"></a>비즈니스 연속성 및 재해 복구

Azure 파일 동기화는 항상 사용 가능한 스토리지에 대한 여러 중복도 옵션을 제공하는 Azure Files에서 지원됩니다. Azure는 데이터에 대한 복원력 있는 복사본을 포함하기 때문에 로컬 서버는 삭제 가능한 캐싱 디바이스가 되며 Azure 파일 동기화 배포에 새 서버를 추가하여 실패한 서버에서 복구하는 작업을 수행할 수 있습니다. 로컬 백업에서 복원하는 대신 다른 Windows Server를 프로비저닝하고 Azure 파일 동기화 에이전트를 설치한 다음, Azure 파일 동기화 배포에 추가합니다. Azure 파일 동기화는 데이터를 다운로드하기 전에 파일 네임스페이스를 다운로드하므로 최대한 빨리 서버가 실행될 수 있습니다. 신속한 복구를 위해 웜 대기 서버를 배포의 일부로 포함하거나 Windows 클러스터링과 함께 Azure 파일 동기화를 사용할 수 있습니다.

## <a name="cloud-side-backup"></a>클라우드 쪽 백업

Azure Backup을 사용해서 클라우드에서 중앙 집중식 백업을 수행하여 온-프레미스 백업 소비를 줄입니다. Azure Files SMB 공유는 기본 스냅샷 기능을 포함하며, Azure Backup을 사용하여 백업을 예약하고 보존을 관리하는 프로세스를 자동화할 수 있습니다. Azure Backup은 온-프레미스 서버와도 통합되므로 클라우드로 복원할 때 이러한 변경 내용이 자동으로 Windows Server에 다운로드됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure 파일 동기화 배포 계획](file-sync-planning.md)
- [클라우드 계층화 개요](file-sync-cloud-tiering-overview.md)
- [Azure 파일 동기화 모니터링](file-sync-monitoring.md)
