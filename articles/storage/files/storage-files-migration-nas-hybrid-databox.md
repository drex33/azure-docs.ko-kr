---
title: Data Box를 통해 온-프레미스 NAS를 Azure 파일 동기화로 마이그레이션
description: Azure Data Box를 통해 Azure 파일 동기화를 사용하여 온-프레미스 NAS(Network Attached Storage) 위치에서 하이브리드 클라우드 배포로 파일을 마이그레이션하는 방법을 알아봅니다.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/5/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fb7132e0a7b9dc59ac6b047d431acf0e740aba0a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430831"
---
# <a name="use-data-box-to-migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-by-using-azure-file-sync"></a>Data Box를 통해 Azure 파일 동기화를 사용하여 NAS(Network Attached Storage)에서 하이브리드 클라우드 배포로 마이그레이션

이 마이그레이션 문서는 NAS, Azure 파일 동기화 및 Azure Data Box라는 키워드에 적용되는 여러 문서 중 하나입니다. 이 문서가 다음 시나리오에 적용되는지 확인합니다.

> [!div class="checklist"]
> * 데이터 원본: NAS(Network Attached Storage)
> * 마이그레이션 경로: NAS &rArr; Data Box &rArr; Azure 파일 공유 &rArr; Windows Server와 동기화
> * 온-프레미스에서 파일 캐싱: 그렇습니다. 최종 목표는 Azure 파일 동기화 배포입니다.

다른 시나리오의 경우 [마이그레이션 가이드 표](storage-files-migration-overview.md#migration-guides)를 참조하세요.

Azure 파일 동기화는 DAS(직접 연결된 스토리지) 위치에서 작동합니다. NAS(네트워크 연결 스토리지) 위치와 동기화를 지원하지 않습니다.
따라서 파일을 마이그레이션해야 합니다. 이 문서에서는 이러한 마이그레이션의 계획 및 구현 과정을 안내합니다.

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |

## <a name="migration-goals"></a>마이그레이션 목표

목표는 NAS 어플라이언스에 있는 공유를 Windows Server로 이동하는 것입니다. 그런 다음, 하이브리드 클라우드 배포에 Azure 파일 동기화를 사용합니다. 이 마이그레이션은 마이그레이션하는 동안 프로덕션 데이터의 무결성과 가용성을 보장하는 방식으로 수행해야 합니다. 후자는 정기적인 유지 관리 기간을 충족하거나 살짝만 초과하도록 가동 중지 시간을 최소로 유지해야 합니다.

## <a name="migration-overview"></a>마이그레이션 개요

마이그레이션 프로세스는 몇 가지 단계로 구성됩니다. 다음 작업이 필요합니다.
- Azure 스토리지 계정 및 파일 공유를 배포합니다.
- Windows Server를 실행하는 온-프레미스 컴퓨터를 배포합니다. 
- Azure 파일 동기화를 구성합니다.
- Robocopy를 사용하여 파일을 마이그레이션합니다.
- 컷오버를 수행합니다.

다음 섹션에서는 마이그레이션 프로세스의 단계에 대해 자세히 설명합니다.

> [!TIP]
> 이 문서로 돌아가려면 화면 오른쪽의 탐색 영역을 사용하여 이전에 중단한 마이그레이션 단계로 건너뛰세요.

## <a name="phase-1-determine-how-many-azure-file-shares-you-need"></a>1단계: 필요한 Azure 파일 공유 수 결정

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>2단계: Azure 스토리지 리소스 배포

이 단계에서는 1단계의 매핑 테이블을 참조하여 정확한 수의 Azure Storage 계정과 해당 계정 내의 파일 공유를 프로비저닝합니다.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-determine-how-many-azure-data-box-appliances-you-need"></a>3단계: 필요한 Azure Data Box 어플라이언스 수 결정

이 단계는 이전 단계를 완료한 후에만 시작합니다. 이 시점에서 Azure 스토리지 리소스(스토리지 계정 및 파일 공유)를 만들어야 합니다. Data Box를 주문하는 경우 Data Box에서 데이터를 이동하는 스토리지 계정을 지정해야 합니다.

이 단계에서는 이전 단계의 마이그레이션 계획 결과를 사용 가능한 Data Box 옵션의 제한에 매핑해야 합니다. 이러한 고려 사항은 선택할 Data Box 옵션과 NAS 공유를 Azure 파일 공유로 이동하는 데 필요한 옵션의 수를 계획하는 데 도움이 됩니다.

필요한 디바이스의 수와 해당 유형을 결정하려면 다음과 같은 중요한 제한 사항을 고려합니다.

* 모든 Azure Data Box 어플라이언스에서 데이터를 최대 10개의 스토리지 계정으로 이동할 수 있습니다. 
* 각 Data Box 옵션은 사용 가능한 자체 용량과 함께 제공됩니다. [Data Box 옵션](#data-box-options)을 참조하세요.

만들기로 결정한 스토리지 계정의 수와 각 계정의 공유를 확인하려면 마이그레이션 계획을 참조합니다. 그런 다음, NAS에서 각 공유의 크기를 확인합니다. 이 정보를 결합하면 어떤 어플라이언스에서 어떤 스토리지 계정으로 데이터를 보내야 하는지 결정하고 최적화할 수 있습니다. 두 개의 Data Box 디바이스에서 파일을 동일한 스토리지 계정으로 이동할 수 있지만, 단일 파일 공유의 콘텐츠는 두 개의 Data Box에 분할하지 않습니다.

### <a name="data-box-options"></a>Data Box 옵션

표준 마이그레이션의 경우 다음 Data Box 옵션 중 하나 또는 조합을 선택합니다. 

* **Data Box Disk**.
  Microsoft는 각각 8TiB 용량인 1~5개의 SSD 디스크(최대 총 40TiB)를 보냅니다. 암호화 및 파일 시스템 오버헤드로 인해 사용 가능한 용량이 약 20% 줄어듭니다. 자세한 내용은 [Data Box Disk 설명서](../../databox/data-box-disk-overview.md)를 참조하세요.
* **Data Box**.
  이 옵션은 가장 일반적인 옵션입니다. Microsoft는 NAS와 비슷하게 작동하는 러기드 Data Box 어플라이언스를 보냅니다. 사용 가능한 용량은 80TiB입니다. 자세한 내용은 [Data Box 설명서](../../databox/data-box-overview.md)를 참조하세요.
* **Data Box Heavy**.
  이 옵션은 NAS와 비슷하게 작동하는 휠 방식의 러기드 Data Box 어플라이언스를 제공합니다. 용량은 1PiB입니다. 암호화 및 파일 시스템 오버헤드로 인해 사용 가능한 용량이 약 20% 줄어듭니다. 자세한 내용은 [Data Box Heavy 설명서](../../databox/data-box-heavy-overview.md)를 참조하세요.

## <a name="phase-4-provision-a-suitable-windows-server-instance-on-premises"></a>4단계: 온-프레미스에서 적절한 Windows Server 프로비저닝

Azure Data Box가 도착하기를 기다리는 동안 Azure 파일 동기화에서 사용할 하나 이상의 Windows Server 요구 사항 검토를 시작할 수 있습니다.

* 가상 머신 또는 물리적 서버로 Windows Server 2019 인스턴스(최소한 Windows Server 2012 R2)를 만듭니다. Windows Server 장애 조치(failover) 클러스터도 지원됩니다.
* DAS(직접 연결된 스토리지)를 프로비저닝하거나 추가합니다. (NAS와 반대로 지원되지 않는 DAS)

배포하는 Windows Server 인스턴스의 리소스 구성(컴퓨팅 및 RAM)에 가장 큰 영향을 미치는 것은 동기화할 파일 및 폴더의 수입니다. 문제가 있는 경우 더 높은 성능을 구성하는 것이 좋습니다.

[동기화해야 하는 항목의 수에 따라 Windows Server 인스턴스 크기를 조정하는 방법을 알아보세요.](../file-sync/file-sync-planning.md#recommended-system-resources)

> [!NOTE]
> 위의 링크 문서에서는 서버 메모리(RAM) 범위가 포함된 표를 제공합니다. 현재 서버에 대한 범위에서 가장 낮은 값을 사용해도 되지만, 초기 동기화가 훨씬 오래 걸립니다.

## <a name="phase-5-copy-files-onto-your-data-box"></a>5단계: Data Box에 파일 복사

Data Box가 도착하면 NAS 어플라이언스가 보이는 곳에 설정해야 합니다. 주문한 Data Box 유형에 대한 설정 설명서를 따릅니다.

* [Data Box 설정](../../databox/data-box-quickstart-portal.md)
* [Data Box Disk 설정](../../databox/data-box-disk-quickstart-portal.md)
* [Data Box Heavy 설정](../../databox/data-box-heavy-quickstart-portal.md)

Data Box 유형에 따라 Data Box 복사 도구를 사용할 수 있습니다. 이 시점에서 이러한 도구는 파일을 완전히 충실하게 Data Box에 복사하지 않으므로 Azure 파일 공유로 마이그레이션하는 데 사용하지 않는 것이 좋습니다. 대신 Robocopy를 사용하세요.

Data Box가 도착하면 주문할 때 지정한 각 스토리지 계정에 사용할 수 있는 미리 프로비전된 SMB 공유가 있습니다.

* 파일이 프리미엄 Azure 파일 공유로 이동하는 경우 프리미엄 "파일 스토리지" 스토리지 계정당 하나의 SMB 공유가 있습니다.
* 파일이 표준 스토리지 계정으로 이동하는 경우 표준(GPv1 및 GPv2) 스토리지 계정당 세 개의 SMB 공유가 있습니다. `_AzFiles`로 끝나는 파일 공유만 마이그레이션과 관련이 있습니다. 모든 블록 및 페이지 Blob 공유는 무시합니다.

Azure Data Box 설명서의 단계를 따릅니다.

1. [Data Box에 연결](../../databox/data-box-deploy-copy-data.md)합니다.
1. 데이터를 Data Box에 복사합니다.
1. [Azure에 업로드할 Data Box를 준비](../../databox/data-box-deploy-picked-up.md)합니다.

연결된 Data Box 설명서에는 Robocopy 명령이 지정되어 있습니다. 이 명령은 전체 파일 및 폴더 충실도를 유지하는 데 적합하지 않습니다. 대신 다음 명령을 사용하세요.

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]


## <a name="phase-6-deploy-the-azure-file-sync-cloud-resource"></a>6단계: Azure 파일 동기화 클라우드 리소스 배포

이 가이드를 계속하기 전에 모든 파일이 올바른 Azure 파일 공유에 도착할 때까지 기다립니다. Data Box 데이터를 배송하고 수집하는 프로세스에는 시간이 걸립니다.

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-7-deploy-the-azure-file-sync-agent"></a>7단계: Azure 파일 동기화 에이전트 배포

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-8-configure-azure-file-sync-on-the-windows-server-instance"></a>8단계: Windows Server 인스턴스에서 Azure 파일 동기화 구성

이 프로세스를 진행하려면 등록된 온-프레미스 Windows Server 인스턴스가 준비되어 있고 인터넷에 연결되어 있어야 합니다.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

클라우드 계층화 기능을 켜고 초기 다운로드 섹션에서 **네임스페이스만** 을 선택합니다.

> [!IMPORTANT]
> 클라우드 계층화는 로컬 서버가 클라우드에 저장되는 것보다 적은 스토리지 용량을 갖지만 전체 네임스페이스를 사용할 수 있도록 하는 Azure 파일 동기화 기능입니다. 또한 로컬에서 원하는 데이터는 빠른 액세스 성능을 위해 로컬로 캐시됩니다. 클라우드 계층화는 선택 사항입니다. 각 Azure 파일 동기화 서버 엔드포인트에 대해 개별적으로 설정할 수 있습니다. Windows Server 인스턴스에 모든 클라우드 데이터를 저장할 수 있을 정도로 로컬 디스크 용량이 충분하지 않고 클라우드에서 모든 데이터를 다운로드하지 않으려면 이 기능을 사용해야 합니다!

동기화를 위해 구성해야 하는 모든 Azure 파일 공유/서버 위치에 대해 동기화 그룹을 만들고, 일치하는 서버 폴더를 서버 엔드포인트로 추가하는 단계를 반복합니다. 네임스페이스 동기화가 완료될 때까지 기다립니다. 다음 섹션에서는 동기화가 반드시 완료되도록 하는 방법을 설명합니다.

> [!NOTE]
> 서버 엔드포인트가 만들어지면 동기화가 작동합니다. 그러나 동기화에서는 Data Box를 통해 Azure 파일 공유로 이동한 파일과 폴더를 열거(검색)해야 합니다. 네임스페이스의 크기에 따라 클라우드의 네임스페이스가 서버에 표시되는 시간이 오래 걸릴 수 있습니다.

## <a name="phase-9-wait-for-the-namespace-to-fully-appear-on-the-server"></a>9단계: 네임스페이스가 서버에 완전히 나타날 때까지 대기

서버가 클라우드 공유에서 네임스페이스를 완전히 다운로드할 때까지 기다렸다가 마이그레이션의 다음 단계를 계속 진행합니다. 파일을 서버로 너무 일찍 이동하면 불필요한 업로드 및 파일 동기화 충돌의 위험이 있습니다.

서버에서 초기 다운로드 동기화를 완료했는지를 확인하려면 동기화하는 Windows Server 인스턴스에서 이벤트 뷰어를 열고 Azure 파일 동기화 원격 분석 이벤트 로그를 사용합니다.
원격 분석 이벤트 로그는 Applications 및 Services\Microsoft\FileSync\Agent 아래의 이벤트 뷰어에 있습니다.

가장 최근의 9102 이벤트를 검색합니다. 동기화 시스템이 완료되면 이벤트 ID 9102가 기록됩니다. 이벤트 텍스트에는 다운로드 동기화 방향에 대한 필드가 있습니다. (`HResult`는 0이어야 하고, 파일을 다운로드해야 합니다.)
 
이 콘텐츠가 포함된 이 유형의 연속 이벤트 두 개를 확인하여 서버가 네임스페이스 다운로드를 완료했는지 확인하는 것이 좋습니다. 두 9102 이벤트 사이에 다른 이벤트가 있어도 괜찮습니다.

## <a name="phase-10-run-robocopy-from-your-nas"></a>10단계: NAS에서 Robocopy 실행

서버가 클라우드 공유에서 전체 네임스페이스의 초기 동기화를 완료하면 이 단계를 진행할 수 있습니다. 이 단계를 진행하려면 먼저 초기 동기화가 완료되어야 합니다. 자세한 내용은 이전 섹션을 참조하세요.

이 단계에서는 Robocopy 작업을 실행하여 클라우드 공유를, 공유를 Data Box에 포크한 이후에 발생한 NAS의 최신 변경 내용과 동기화합니다.
이 Robocopy는 NAS 공유에서 발생한 변동의 양에 따라 빠르게 완료될 수도 있고 다소 시간이 걸릴 수도 있습니다.

> [!WARNING]
> Windows Server 2019에서 되돌아간 Robocopy 동작 때문에 Robocopy `/MIR` 스위치는 계층화된 대상 디렉터리와 호환되지 않습니다. 이 마이그레이션 단계에는 Windows Server 2019 또는 Windows 10 클라이언트를 사용할 수 없습니다. 중간 Windows Server 2016 인스턴스에서 Robocopy를 사용하세요.

기본 마이그레이션 방법은 다음과 같습니다.
 - NAS 어플라이언스에서 Robocopy를 실행하여 Windows Server 인스턴스를 동기화합니다. 
 - Azure 파일 동기화를 사용하여 Windows Server의 Azure 파일 공유를 동기화합니다.

Windows Server 대상 폴더로 첫 번째 로컬 복사본을 실행합니다.

1. NAS 어플라이언스의 첫 번째 위치를 식별합니다.
1. Azure 파일 동기화가 이미 구성되어 있는 Windows Server 인스턴스에서 일치하는 폴더를 식별합니다.
1. Robocopy를 사용하여 복사를 시작합니다.

다음 Robocopy 명령은 NAS 스토리지에서 Windows Server 대상 폴더로 차이점(업데이트된 파일 및 폴더)만 복사합니다. 그러면 Windows Server 인스턴스가 이것을 Azure 파일 공유에 동기화합니다. 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

NAS 어플라이언스에서 파일이 사용하는 양보다 적은 스토리지를 Windows Server 인스턴스에 프로비저닝한 경우에는 클라우드 계층화가 구성된 것입니다. 로컬 Windows Server 볼륨이 가득 차면 [클라우드 계층화](../file-sync/file-sync-cloud-tiering-overview.md)가 시작되고 이미 동기화된 파일이 계층화됩니다. 클라우드 계층화는 NAS 어플라이언스에서 계속 복사할 수 있도록 충분한 공간을 생성합니다. 클라우드 계층화는 한 시간에 한 번씩 무엇이 동기화되었는지 확인하고 99%의 볼륨 여유 공간에 도달할 수 있도록 디스크 공간을 확보합니다.

Windows Server 인스턴스에 로컬로 저장할 수 있는 양보다 많은 파일을 Robocopy가 이동해야 할 수도 있습니다. Azure 파일 동기화가 파일을 업로드하고 로컬 볼륨에서 계층화할 수 있는 속도보다 더 빠르게 Robocopy가 이동할 것으로 예상할 수 있습니다. 이 경우 Robocopy는 실패합니다. 이 시나리오를 방지하는 시퀀스로 공유를 처리하는 것이 좋습니다. 예를 들어 Windows Server 인스턴스에서 사용 가능한 여유 공간에 맞는 공유만 이동합니다. 또는 모든 공유에 대해 Robocopy 작업을 동시에 시작하지 않습니다. 다행히 `/MIR` 스위치는 델타만 이동하도록 합니다. 델타를 이동된 후, 다시 시작된 작업은 파일을 다시 이동할 필요가 없습니다.

### <a name="do-the-cutover"></a>컷오버 수행

Robocopy 명령을 처음 실행하는 경우에도 사용자와 애플리케이션에서 여전히 NAS의 파일에 액세스하고 있으며, 이러한 파일을 잠재적으로 변경할 수 있습니다. Robocopy는 디렉터리를 처리한 후 다음 디렉터리로 이동합니다. 그러면 NAS의 사용자는 현재 Robocopy 실행 중에 처리되지 않는 첫 번째 디렉터리의 파일을 추가, 변경 또는 삭제할 수 있습니다. 이는 정상적인 동작입니다.

첫 번째 실행은 Azure 파일 동기화를 통해 Windows Server 인스턴스 및 클라우드로 대량의 변경된 데이터를 이동하는 것입니다. 이 첫 번째 복사는 다음과 같은 조건에 따라 시간이 오래 걸릴 수 있습니다.

* 업로드 대역폭
* 로컬 네트워크 속도 및 이 속도에 가장 적합한 Robocopy 스레드 수
* Robocopy 및 Azure 파일 동기화에서 처리해야 하는 항목(파일 및 폴더) 수

초기 실행이 완료된 후 명령을 다시 실행합니다.

Robocopy는 공유를 위해 두 번째로 실행할 때 더 빠르게 완료됩니다. 마지막으로 실행한 이후에 변경된 내용만 전송하면 되기 때문입니다. 동일한 공유에 대해 반복되는 작업을 실행할 수 있습니다.

가동 중지 시간을 허용할 수 있다고 생각하면 NAS 기반 공유에 대한 사용자 액세스를 제거해야 합니다. 이 작업은 사용자가 파일 및 폴더 구조와 콘텐츠를 변경하지 못하는 방식으로 수행할 수 있습니다. 예를 들어 DFS 네임스페이스가 존재하지 않는 위치를 가리키게 하거나 공유의 루트 ACL을 변경할 수 있습니다.

Robocopy를 마지막으로 실행합니다. 그러면 Robocopy는 누락된 변경 내용을 선택합니다.
마지막 단계에 걸리는 시간은 Robocopy 검색 속도에 따라 달라집니다. 이전 실행에 걸린 시간을 측정하여 시간(가동 중지 시간과 동일)을 예측할 수 있습니다.

Windows Server 폴더에 공유를 만들고 DFS-N 배포를 조정하여 이를 가리키도록 할 수 있습니다. NAS SMB 공유와 동일한 공유 수준의 권한을 설정해야 합니다. 엔터프라이즈급 도메인 조인 NAS가 있는 경우 사용자가 Active Directory에 있고 Robocopy는 파일과 메타데이터를 있는 그대로 복사하므로 사용자 SID가 자동으로 일치합니다. NAS에서 로컬 사용자를 사용한 경우 다음을 수행해야 합니다. 
- 사용자를 Windows Server 로컬 사용자로 다시 만듭니다. 
- Robocopy가 Windows Server 인스턴스로 이동한 기존 SID를 새 Windows Server 로컬 사용자의 SID에 매핑합니다.

공유 또는 공유 그룹을 공통 루트 또는 볼륨으로 마이그레이션하는 작업이 완료되었습니다(1단계의 매핑에 따라 다름).

해당 복사본 중 일부를 병렬로 실행할 수 있습니다. Azure 파일 공유 범위를 한 번에 하나씩 처리하는 것이 좋습니다.

## <a name="deprecated-option-offline-data-transfer"></a>사용되지 않은 옵션: "오프라인 데이터 전송"

Azure 파일 동기화 에이전트 버전 13이 릴리스되기 전에 "오프라인 데이터 전송"이라는 프로세스를 통해 Data Box 통합이 수행되었습니다. 이 프로세스는 더 이상 사용되지 않습니다. 에이전트 버전 13에서는 이 문서에 설명된 훨씬 더 간단하고 빠른 단계로 대체되었습니다. 더 이상 사용되지 않는 "오프라인 데이터 전송" 기능을 사용하려는 경우에도 계속 사용할 수 있습니다. 특정 [이전 AFS PowerShell 모듈을](https://www.powershellgallery.com/packages/Az.StorageSync/1.4.0)사용하여 계속 사용할 수 있습니다.

```powershell
Install-Module Az.StorageSync -RequiredVersion 1.4.0
Import-module Az.StorageSync -RequiredVersion 1.4.0
# Verify the specific version is loaded:
Get-module Az.StorageSync
```
그런 다음 동일한 PowerShell 모듈을 사용 하 여 서버 끝점을 계속 만들고 프로세스에서 준비 공유를 지정할 수 있습니다.
오프 라인 데이터 전송 프로세스를 진행 하는 마이그레이션이 있는 경우 마이그레이션은 계획 대로 진행 되며 마이그레이션이 완료 된 후에도이 설정을 사용 하지 않도록 설정 해야 합니다.
이 사용 되지 않는 프로세스를 사용 하 여 새 마이그레이션을 시작 하는 기능은 예정 된 에이전트 릴리스부터 제거 될 예정입니다.

## <a name="troubleshooting"></a>문제 해결

가장 일반적인 문제는 Windows Server 쪽의 "볼륨이 가득 차서" Robocopy 명령이 실패하는 것입니다. 클라우드 계층화는 1시간 한 번씩 작동하여 동기화된 로컬 Windows Server 디스크에서 콘텐츠를 비웁니다. 목표는 볼륨의 여유 공간이 99%에 도달하는 것입니다.

동기화 진행 및 클라우드 계층화를 통해 디스크 공간을 확보합니다. Windows Server 인스턴스의 파일 탐색기에서 이를 확인할 수 있습니다.

Windows Server 인스턴스의 가용 용량이 충분한 경우 명령을 다시 실행하면 문제가 해결됩니다. 이 경우에는 아무 문제도 발생하지 않습니다. 안심하고 진행하면 됩니다. 단지 명령을 다시 실행해야 하는 불편함이 있을 뿐입니다.

Azure 파일 동기화 문제를 해결하려면 다음 섹션에 나열된 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure 파일 공유 및 Azure 파일 동기화에 대해 더 자세히 알아볼 수 있습니다. 다음 문서는 고급 옵션과 모범 사례를 이해하는 데 도움이 됩니다. 또한 문제 해결에 대한 도움말도 제공합니다. 다음 문서에는 해당하는 경우 [Azure 파일 공유 설명서](storage-files-introduction.md)에 대한 링크가 포함되어 있습니다.

* [마이그레이션 개요](storage-files-migration-overview.md)
* [Azure 파일 동기화 배포에 대한 계획](../file-sync/file-sync-planning.md)
* [파일 공유 만들기](storage-how-to-create-file-share.md)
* [Azure 파일 동기화 문제 해결](../file-sync/file-sync-troubleshoot.md)