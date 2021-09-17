---
title: Azure 파일 동기화로 StorSimple 1200 마이그레이션
description: StorSimple 1200 시리즈 가상 어플라이언스를 Azure 파일 동기화로 마이그레이션하는 방법을 알아봅니다.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 1208beab46a9ceb29abd75eec587dc7628e07801
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435387"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>Azure 파일 동기화로 StorSimple 1200 마이그레이션

StorSimple 1200 시리즈는 온-프레미스 데이터 센터에서 실행되는 가상 어플라이언스입니다. 이 어플라이언스에서 Azure 파일 동기화 환경으로 데이터를 마이그레이션할 수 있습니다. Azure 파일 동기화는 StorSimple 어플라이언스를 마이그레이션할 수 있는 기본 및 전략적 장기 Azure 서비스입니다.

StorSimple 1200 시리즈는 2022년 12월에 [지원 종료](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series)될 예정입니다.  가능한 한 빨리 마이그레이션 계획을 시작하는 것이 중요합니다. 이 문서에서는 Azure 파일 동기화로 성공적으로 마이그레이션하는 데 필요한 배경 지식과 마이그레이션 단계를 제공합니다. 

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/no-icon.png) |

## <a name="azure-file-sync"></a>Azure 파일 동기화

Azure 파일 동기화는 두 가지 주요 구성 요소를 기반으로 하는 Microsoft Cloud Service)입니다.

* 파일 동기화 및 클라우드 계층화
* SMB 및 파일 REST 등의 여러 프로토콜을 통해 액세스할 수 있는 Azure의 기본 스토리지인 파일 공유 Azure 파일 공유는 기본적으로 네트워크 드라이브로 탑재할 수 있는 Windows 서버의 파일 공유와 비교할 수 있습니다. 특성, 권한 및 타임스탬프와 같이 중요한 파일 충실도 측면을 지원합니다. StorSimple과 달리 클라우드에 저장된 파일 및 폴더를 해석하는 데 애플리케이션/서비스가 필요하지 않습니다. 범용 파일 서버 데이터와 일부 애플리케이션 데이터를 클라우드에 저장하는 이상적이고 가장 유연한 방식입니다.

이 문서에서는 마이그레이션 단계에 초점을 두고 설명합니다. 마이그레이션하기 전에 Azure 파일 동기화에 대해 자세히 알아보려면 다음 문서를 참조하는 것이 좋습니다.

* [Azure 파일 동기화 - 개요](../file-sync/file-sync-planning.md "개요")
* [Azure 파일 동기화 - 배포 가이드](../file-sync/file-sync-deployment-guide.md)

## <a name="migration-goals"></a>마이그레이션 목표

목표는 생산 데이터 무결성을 보장하고 가용성을 보장하는 것입니다. 후자는 가동 중지 시간이 정기적인 유지 관리 기간에 맞거나 약간 초과할 수 있도록 가동 중지 시간을 최소로 유지해야 합니다.

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>Azure 파일 동기화로의 StorSimple 1200 마이그레이션 경로

Azure 파일 동기화 에이전트를 실행하려면 로컬 Windows 서버가 필요합니다. Windows Server는 2012R2 서버 이상이면 되지만 Windows Server 2019가 가장 좋습니다.

대체 마이그레이션 경로가 매우 많이 있으므로 모든 경로를 문서화하고 이 문서에서 모범 사례로 권장하는 경로에 대해 위험 또는 단점이 있는 이유를 설명하기에는 너무 긴 문서를 작성해야 합니다.

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="이 문서 아래에 있는 단계에 대한 마이그레이션 경로 개요":::

이전 이미지에서는 이 문서의 섹션에 해당하는 단계를 보여 줍니다.

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>1단계: 온-프레미스 Windows 서버 및 스토리지 프로비저닝

1. 가상 머신 또는 물리적 서버로 Windows Server 2019(최소 2012R2 이상)를 만듭니다. Windows Server 장애 조치(failover) 클러스터도 지원됩니다.
2. DAS(Direct Attached Storage, 지원되지 않는 NAS와 비교)를 프로비저닝하거나 추가합니다. Windows 서버 스토리지 크기는 가상 StorSimple 1200 어플라이언스의 사용 가능한 용량 크기보다 크거나 같아야 합니다.

### <a name="step-2-configure-your-windows-server-storage"></a>2단계: Windows 서버 스토리지 구성

이 단계에서는 StorSimple 스토리지 구조(볼륨 및 공유)를 Windows 서버 스토리지 구조에 매핑합니다.
이제 스토리지 구조(즉, 볼륨 수, 볼륨에 대한 데이터 폴더 연결 또는 현재 SMB/NFS 공유 위 또는 아래의 하위 폴더 구조)를 변경하려는 경우 이러한 변경 사항을 고려해야 합니다.
Azure 파일 동기화를 구성한 후 파일 및 폴더 구조를 변경하는 것은 복잡할 수 있으며 피해야 합니다.
이 문서에서는 1:1 매핑을 가정하므로 이 문서의 단계를 수행할 때 매핑 변경 사항을 고려해야 합니다.

* 프로덕션 데이터는 Windows 서버 시스템 볼륨에 없어야 합니다. 클라우드 계층화는 시스템 볼륨에서 지원되지 않습니다. 그러나 이 기능은 StorSimple 교체와 같은 연속 작업뿐 아니라 마이그레이션에도 필요합니다.
* StorSimple 1200 가상 어플라이언스와 동일한 수의 볼륨을 Windows 서버에 프로비저닝합니다.
* 필요한 Windows 서버 역할, 기능 및 설정을 구성합니다. OS를 안전하게 최신 상태로 유지하려면 Windows 서버 업데이트를 선택하는 것이 좋습니다. 마찬가지로 Azure 파일 동기화 에이전트를 포함하여 Microsoft 애플리케이션을 최신 상태로 유지하기 위해 Microsoft 업데이트를 선택하는 것이 좋습니다.
* 다음 단계를 읽기 전에 폴더 또는 공유를 구성하지 마세요.

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>3단계: 첫 번째 Azure 파일 동기화 클라우드 리소스 배포

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>4단계: 로컬 볼륨 및 폴더 구조를 Azure 파일 동기화 및 Azure 파일 공유 리소스와 일치

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>5단계: Azure 파일 공유 프로비저닝

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

#### <a name="storage-account-settings"></a>스토리지 계정 설정

스토리지 계정에는 다양한 구성을 수행할 수 있습니다. 스토리지 계정 구성에서는 다음 체크리스트를 고려해야 합니다. 마이그레이션이 완료된 후에는 네트워킹 구성의 인스턴스를 변경할 수 있습니다. 

> [!div class="checklist"]
> * 대량 파일 공유: 사용 - 대량 파일 공유를 사용하면 성능을 높이고 한 공유에 최대 100TiB를 저장할 수 있습니다.
> * 방화벽 및 가상 네트워크: 사용 안 함 - IP 제한이나 특정 VNET에 대한 스토리지 계정 액세스 제한을 구성하지 않습니다. 스토리지 계정의 퍼블릭 엔드포인트를 마이그레이션 중에 사용합니다. Azure VM의 모든 IP 주소가 허용되어야 합니다. 마이그레이션 후 스토리지 계정에 대한 방화벽 규칙을 구성하는 것이 좋습니다.
> * 프라이빗 엔드포인트 지원 - 프라이빗 엔드포인트를 사용할 수는 있으나 퍼블릭 엔드포인트를 마이그레이션에 사용하고, 사용 가능한 상태로 유지해야 합니다.

### <a name="step-6-configure-windows-server-target-folders"></a>6단계: Windows 서버 대상 폴더 구성

이전 단계에서는 동기화 토폴로지의 구성 요소를 결정하는 모든 측면을 고려했습니다. 이제 업로드할 파일을 수신할 서버를 준비해야 합니다.

각 폴더를 자체 Azure 파일 공유에 동기화할 **모든** 폴더를 만듭니다.
이전에 문서화한 폴더 구조를 따르는 것이 중요합니다. 예를 들어 여러 로컬 SMB 공유를 단일 Azure 파일 공유로 동기화하기로 결정한 경우 볼륨의 공통 루트 폴더 아래에 배치해야 합니다. 이제 볼륨에서 이 대상 루트 폴더를 만듭니다.

프로비저닝한 Azure 파일 공유의 수는 이 단계에서 만든 폴더 수와 루트 수준에서 동기화할 볼륨 수를 더한 수와 일치해야 합니다.

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>7단계: Azure 파일 동기화 에이전트 배포

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>8단계 - 동기화 구성

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **클라우드 계층화를 켜야 합니다!** 이는 StorSimple 클라우드 스토리지에 있는 데이터의 전체 크기를 저장하는 데 충분한 공간이 로컬 서버에 없는 경우에 필요합니다. 일시적으로 마이그레이션에 대한 계층화 정책을 99%의 사용 가능한 볼륨 공간으로 설정합니다.

동기화하기 위해 구성해야 하는 모든 Azure 파일 공유/서버 위치에 대해 동기화 그룹 생성 및 일치하는 서버 폴더를 서버 엔드포인트로 추가하는 단계를 반복합니다.

### <a name="step-9-copy-your-files"></a>9단계: 파일 복사

기본 마이그레이션 방식은 StorSimple 가상 어플라이언스에서 Windows Server로 RoboCopy 및 Azure 파일 공유로 Azure 파일 동기화입니다.

Windows Server 대상 폴더로 첫 번째 로컬 복사본을 실행합니다.

* 가상 StorSimple 어플라이언스의 첫 번째 위치를 식별합니다.
* 이미 Azure 파일 동기화가 구성된 Windows Server에서 일치하는 폴더를 식별합니다.
* RoboCopy를 사용하여 복사 시작

다음 RoboCopy 명령은 StorSimple Azure Storage에서 로컬 StorSimple로 파일을 회수한 다음 Windows Server 대상 폴더로 이동합니다. Windows Server가 이를 Azure 파일 공유로 동기화합니다. 로컬 Windows Server 볼륨이 가득 차면 클라우드 계층화가 시작되고 이미 동기화된 파일을 계층화합니다. 클라우드 계층화는 StorSimple 클라우드 어플라이언스에서 복사를 계속하기에 충분한 공간을 생성합니다. 클라우드 계층화는 한 시간에 한 번씩 동기화 내용 및 99%의 볼륨 여유 공간에 도달할 수 있도록 디스크 공간 확보를 확인합니다.

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

RoboCopy 명령을 처음 실행하는 경우 사용자와 애플리케이션은 계속해서 StorSimple 파일 및 폴더에 액세스하며 변경할 수도 있습니다. RoboCopy가 디렉터리를 처리하고 다음으로 이동한 후 원본 위치(StorSimple)의 사용자가 파일을 추가, 변경 또는 삭제할 수 있으며, 이것은 현재 RoboCopy 실행에서는 처리되지 않습니다. 괜찮습니다.

첫 번째 실행은 대량의 데이터를 Windows Server의 온-프레미스로 다시 이동하고 Azure 파일 동기화를 통해 클라우드로 백업하는 것입니다. 다음에 따라 시간이 오래 걸릴 수 있습니다.

* 다운로드 대역폭
* StorSimple 클라우드 서비스의 회수 속도
* 업로드 대역폭
* 각 서비스에서 처리해야 하는 항목(파일 및 폴더) 수

초기 실행이 완료되면 명령을 다시 실행합니다.

마지막 실행 후에 발생한 변경 내용만 전송하면 되기 때문에 두 번째 실행은 더 빨리 완료됩니다. 이러한 변경 사항은 최근이기 때문에 이미 StorSimple에 로컬일 가능성이 있습니다. 이렇게 하면 클라우드에서 회수할 필요가 감소하기 때문에 시간이 더 줄어듭니다. 두 번째 실행 중에도 새 변경 내용이 누적될 수 있습니다.

완료하는 데 걸리는 시간이 허용되는 가동 중지 시간을 만족할 때까지 이 프로세스를 반복합니다.

허용할 수 있는 가동 중지 시간을 고려하여 StorSimple 위치를 오프라인 상태로 전환할 준비가 되면 이제는 이 작업을 수행합니다. 예를 들어, 사용자가 폴더에 액세스하거나 StorSimple의 이 폴더에서 콘텐츠를 변경하지 못하게 하는 다른 적절한 단계를 수행할 수 없도록 SMB 공유를 제거합니다.

마지막 RoboCopy 라운드를 한 번 실행합니다. 그러면 놓쳤을 수도 있는 변경 내용이 복사됩니다.
이 마지막 단계에 드는 시간은 RoboCopy 검색 속도에 따라 달라집니다. 이전 실행에 걸린 시간을 측정하여 시간(가동 중지 시간)을 예상할 수 있습니다.

Windows Server 폴더에 공유를 만들고 DFS-N 배포를 조정하여 이를 가리키도록 할 수 있습니다. StorSimple SMB 공유와 동일한 공유 수준의 권한을 설정해야 합니다.

공유/공유 그룹을 공통 루트 또는 볼륨으로 마이그레이션했습니다. (동일한 Azure 파일 공유로 이동하기 위해 매핑하여 결정한 항목에 따라 다릅니다.)

해당 복사본 중 일부를 병렬로 실행할 수 있습니다. 한 번에 하나의 Azure 파일 공유 범위를 처리하는 것이 좋습니다.

> [!WARNING]
> 모든 데이터를 StorSimple에서 Windows Server로 이동하고 마이그레이션이 완료되고 나면 Azure Portal의 ***모든*** 동기화 그룹으로 돌아가서 클라우드 계층화 볼륨의 사용 가능한 공간(%) 값을 캐시 사용률에 더 적합한 값(예: 20%)으로 조정합니다. 

클라우드 계층화 볼륨 사용 가능한 공간 정책은 잠재적으로 여러 서버 엔드포인트가 동기화되는 볼륨 수준에서 작동합니다. 하나의 서버 엔드포인트에서라도 사용 가능한 공간을 조정하지 않으면, 동기화는 가장 제한적인 규칙을 계속 적용하고 99%의 사용 가능한 디스크 공간을 유지하여 로컬 캐시가 정상적으로 작동하지 않도록 합니다. 목표가 거의 액세스하지 않는 보관 데이터만 포함하는 볼륨에 대한 네임스페이스만 갖는 것이 아닌 경우.

## <a name="troubleshoot"></a>문제 해결

발생할 가능성이 높은 문제는 RoboCopy 명령이 Windows Server의 “볼륨 가득 참”으로 인해 실패하는 것입니다. 이 경우 다운로드 속도가 업로드 속도보다 더 좋을 수 있습니다. 클라우드 계층화는 동기화된 로컬 Windows Server 디스크에서 콘텐츠를 비우기 위해 매시간 한 번씩 작동합니다.

동기화 진행 및 클라우드 계층화를 통해 디스크 공간을 확보합니다. Windows 서버의 파일 탐색기에서 이를 확인할 수 있습니다.

Windows 서버가 사용 가능한 충분한 용량을 갖춘 경우 명령을 다시 실행하면 문제를 해결합니다. 이와 같은 상황이 발생하더라도 아무 문제가 없으며 자신감을 가지고 작업을 진행하세요. 불편한 점이라고는 명령을 다시 실행하는 것뿐입니다.

다른 Azure 파일 동기화 문제를 실행할 수도 있습니다.
가능성이 희박하지만 발생한 경우 **LINK Azure 파일 동기화 문제 해결 가이드** 를 참조하세요.

[!INCLUDE [storage-files-migration-robocopy-optimize](../../../includes/storage-files-migration-robocopy-optimize.md)]

---

> [!NOTE]
> 여전히 궁금한 점이 있거나 문제가 있나요?</br>
> 이에 대 한 도움말은 다음을 참조 하세요. :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-migration-email.png" alt-text="전자 메일 주소: microsoft 도트 com에서 Azure Files 마이그레이션":::

## <a name="relevant-links"></a>관련 링크

마이그레이션 콘텐츠:

* [StorSimple 8000 시리즈 마이그레이션 가이드](storage-files-migration-storsimple-8000.md)

Azure 파일 동기화 콘텐츠:

* [Azure 파일 동기화 개요](../file-sync/file-sync-planning.md)
* [Azure 파일 동기화 배포](../file-sync/file-sync-deployment-guide.md)
* [Azure 파일 동기화 문제 해결 가이드](../file-sync/file-sync-troubleshoot.md)