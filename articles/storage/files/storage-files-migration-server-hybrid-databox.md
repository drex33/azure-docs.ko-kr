---
title: Azure Data Box를 사용하여 데이터를 Azure 파일 동기화로 마이그레이션
description: Azure 파일 동기화와 호환되는 대량 데이터를 오프라인으로 마이그레이션합니다. 파일 충돌을 방지하고, 가동 중지 시간이 0인 클라우드 마이그레이션을 위해 파일 공유를 서버의 최신 변경 내용으로 캐치업합니다.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 06/01/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 5b888ca7655f4de267ed89229559e4b0b1785f6c
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113799497"
---
# <a name="migrate-data-offline-to-azure-file-sync-with-azure-data-box"></a>Azure Data Box를 사용하여 데이터를 Azure 파일 동기화로 오프라인 마이그레이션

이 마이그레이션 문서는 Azure 파일 동기화 및 Azure Data Box라는 키워드에 적용되는 여러 문서 중 하나입니다. 이 문서가 다음 시나리오에 적용되는지 확인합니다.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-server-hybrid-databox/file-sync-data-box-concept.png" alt-text="이 마이그레이션 가이드에서 설명하는 세 가지 순차적 단계를 보여 줍니다. 이에 대해 이미지 옆의 열에서 자세히 설명하고 있습니다." lightbox="media/storage-files-migration-server-hybrid-databox/file-sync-data-box-concept-expanded.png":::
    :::column-end:::
    :::column:::
        > [!div class="checklist"]
        > * 데이터 원본: Azure 파일 동기화가 설치되고 원본 파일 세트를 가리키는 Windows Server 2012 R2 이상입니다.
        > * 마이그레이션 경로: Windows Server 2012 R2 이상 &rArr; Data Box &rArr; Azure 파일 공유 &rArr; Windows Server 원본 파일 위치와 동기화입니다.
        > * 온-프레미스 파일 캐싱: 예, 최종 목표는 현재 위치에서 파일을 동기화하는 Azure 파일 동기화 배포입니다. 
        :::column-end:::
:::row-end:::

Azure Data Box를 사용하는 것은 온-프레미스 Windows Server에서 대량의 데이터를 이동하여 Azure 파일 공유를 분리한 다음, 필요에 따라 원래 원본 서버에서 Azure 파일 동기화를 추가하는 실행 가능한 경로입니다.

다양한 마이그레이션 경로를 사용할 수 있으므로 올바른 마이그레이션 경로를 따르는 것이 중요합니다.

* 데이터가 Windows Server 2012 R2 이상에 있고, AFS를 해당 서버에 설치하고, 원래 위치를 동기화하려고 합니다. 이 시나리오에서는 모든 파일을 업로드하지 않고, Data Box를 대신 사용한 다음, 파일 동기화를 지속적인 변경에 사용하려고 합니다. 이 시나리오에 해당하는 경우 이 문서에서 마이그레이션 경로에 대해 설명합니다.
* 데이터가 AFS를 설치하지 *않거나* 설치할 수 없는 원본에 있습니다. 예를 들어 NAS(네트워크 연결 스토리지) 또는 다른 서버가 있습니다. 대신 비어 있는 새 서버를 만들고 해당 서버에서 Azure 파일 동기화를 사용합니다. 이 시나리오에 해당하는 경우 이 문서는 적절한 마이그레이션 가이드가 아닙니다. 대신 [Data Box를 통해 NAS에서 Azure 파일 동기화로 마이그레이션](../files/storage-files-migration-nas-hybrid-databox.md)을 확인하거나 [마이그레이션 개요](../files/storage-files-migration-overview.md) 페이지에서 이 시나리오에 가장 적합한 가이드를 찾습니다.
* 다른 모든 시나리오의 경우 [Azure 파일 공유 마이그레이션 가이드의 표](../files/storage-files-migration-overview.md)를 확인합니다. 이 개요 페이지는 모든 마이그레이션 시나리오에 적합한 시작점을 제공합니다.
 
## <a name="migration-overview"></a>마이그레이션 개요

마이그레이션 프로세스는 몇 가지 단계로 구성됩니다. 다음 작업이 필요합니다.
- 스토리지 계정 및 파일 공유를 배포합니다.
- 하나 이상의 Azure Data Box 디바이스를 배포하여 Windows Server 2012 R2 이상에서 데이터를 이동합니다. 
- 신뢰할 수 있는 업로드를 사용하여 Azure 파일 동기화를 구성합니다.

다음 섹션에서는 마이그레이션 프로세스의 단계에 대해 자세히 설명합니다.

> [!TIP]
> 이 문서로 돌아가려면 화면 오른쪽의 탐색 영역을 사용하여 이전에 중단한 마이그레이션 단계로 건너뛰세요.

## <a name="phase-1-determine-how-many-azure-file-shares-you-need"></a>1단계: 필요한 Azure 파일 공유 수 결정

이 마이그레이션 가이드에서는 파일이 포함된 온-프레미스 DAS(직접 연결 스토리지)를 계속 사용해야 합니다. Data Box는 해당 위치에서 공급되고, Azure 파일 동기화가 해당 위치에도 설정됩니다. NAS(네트워크 연결 스토리지)는 이 마이그레이션 경로에서 작동하지 않습니다.

각각 파일 세트가 동기화되는 위치를 결정하는 Azure 파일 동기화 *동기화 그룹* 을 설정하여 동기화할 항목을 결정합니다. 각 동기화 그룹에는 *서버 엔드포인트* 라고 하는 하나 이상의 서버 위치와 *클라우드 엔드포인트* 라고 하는 하나의 Azure 파일 공유 위치가 있습니다. 

파일 세트의 하위 경로를 각각의 고유한 Azure 파일 공유에 동기화할 수 있습니다. 즉, 파일 세트를 완전히 포함하도록 여러 동기화 그룹을 설정합니다. 섹션의 나머지 부분에서는 옵션에 대해 설명합니다. 데이터를 재구성해야 하는 경우 이 가이드를 계속 진행하거나 Data Box를 주문하거나 동기화를 설정하기 전에 첫 번째 단계로 재구성해야 합니다. 

> [!CAUTION]
> 마이그레이션을 시작하기 전에 파일 및 폴더 구조를 장기적으로 유지하도록 구성해야 합니다. 마이그레이션하는 동안 불필요한 폴더를 재구성하지 않도록 방지합니다. 이렇게 하면 파일을 초기에 Azure로 대량 전송하는 데 Azure Data Box를 사용하는 경우의 긍정적인 효과가 줄어듭니다.

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

## <a name="phase-4-copy-files-onto-your-data-box"></a>4단계: Data Box에 파일 복사

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

## <a name="phase-5-deploy-the-azure-file-sync-cloud-resource"></a>5단계: Azure 파일 동기화 클라우드 리소스 배포

이 가이드를 계속하기 전에 모든 파일이 올바른 Azure 파일 공유에 도착할 때까지 기다립니다. Data Box 데이터를 배송하고 수집하는 프로세스에는 시간이 걸립니다.

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-6-deploy-the-azure-file-sync-agent"></a>6단계: Azure 파일 동기화 에이전트 배포

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-7-configure-azure-file-sync-on-the-existing-windows-server"></a>7단계: 기존 Windows Server에서 Azure 파일 동기화 구성

이 프로세스를 진행하려면 등록된 온-프레미스 Windows Server 인스턴스가 준비되어 있고 인터넷에 연결되어 있어야 합니다.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

:::row:::
    :::column:::
        [![서버 엔드포인트 만들기 마법사의 Azure Portal 섹션을 보여 줍니다. 데이터를 Azure 파일 공유에 시드하는 시나리오에 해당하는 확인란이 강조 표시되어 있습니다. AFS를 이전에 Data Box에 복사한 것과 동일한 온-프레미션 위치에 연결하는 경우 이 확인란을 선택합니다.](media/storage-files-migration-server-hybrid-databox/enable-authoritative-upload-top-checkbox.png)](media/storage-files-migration-server-hybrid-databox/enable-authoritative-upload-top-checkbox-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        **서버 엔드포인트 만들기** 마법사에 있으면 폴더 경로 아래에 제공된 확인란을 활용합니다. Azure 파일 공유(Data Box에서 이 네임스페이스에 대한 파일과 폴더를 이동함)에서 찾을 수 있는 것과 동일한 파일 및 폴더 구조를 가리키는 경로를 입력한 경우에만 이 항목을 선택합니다. </br> </br> 폴더 계층 구조가 일치하지 않으면 자동으로 해결할 수 없는 차이로 표시됩니다. 불일치를 방지하거나 Data Box 프로세스에 투자하면 아무런 이점이 없습니다. 모든 데이터는 Azure 파일 공유에서 삭제됩니다. 모든 데이터는 로컬 서버에서 업로드해야 합니다. Azure Data Box를 사용하여 대량 마이그레이션의 이점을 얻고 클라우드 공유를 서버의 최신 변경 내용으로 원활하게 업데이트하려면 디렉터리 구조가 일치해야 합니다.
:::column-end:::
:::row-end:::

> [!NOTE]
> 이 확인란을 사용하도록 설정하면 **초기 동기화** 모드가 *Azure 파일 공유의 파일 및 폴더를 이 서버의 경로에 있는 콘텐츠로 명령적으로 덮어쓰도록* 설정됩니다. 이 옵션은 동기화 그룹의 첫 번째 서버 엔드포인트에만 사용할 수 있습니다.

이 새 서버 엔드포인트에 대해 신뢰할 수 있는 업로드가 구성되면 필요에 따라 클라우드 계층화를 사용하도록 설정할 수 있습니다.

클라우드 계층화는 로컬 서버가 클라우드에 저장되는 것보다 적은 스토리지 용량을 갖지만 전체 네임스페이스를 사용할 수 있도록 하는 Azure 파일 동기화 기능입니다. 또한 로컬에서 원하는 데이터는 빠른 액세스 성능을 위해 로컬로 캐시됩니다. 클라우드 계층화는 선택 사항입니다. 각 Azure 파일 동기화 서버 엔드포인트에 대해 개별적으로 설정할 수 있습니다. 이 기능을 사용하여 온-프레미스에 고정된 스토리지 공간을 확보하면서도 여전히 사용자에게 로컬 성능 캐시를 제공하고 더 나은 쿨 데이터를 클라우드에 저장합니다.

[클라우드 계층화 개요](../file-sync/file-sync-cloud-tiering-overview.md)를 확인하여 자세히 알아보거나 로컬 서버에서 캐시/계층화되는 항목을 미세 조정하는 데 사용할 수 있는 다양한 [클라우드 계층화 정책](../file-sync/file-sync-cloud-tiering-policy.md)을 자세히 살펴보세요.

## <a name="complete-your-migration"></a>마이그레이션 완료

서버 엔드포인트가 만들어지면 동기화가 작동합니다. 그러나 동기화에서 Azure Data Box를 통해 Azure 파일 공유로 이동한 파일과 폴더를 열거(검색)해야 합니다. 네임스페이스의 크기에 따라 최신 서버 변경 내용이 클라우드에 동기화될 때까지 시간이 오래 걸릴 수 있습니다. 사용자는 영향을 받지 않으며 서버의 데이터를 계속 사용할 수 있습니다. 이 전략은 가동 중지 시간이 0인 클라우드 마이그레이션을 달성합니다.

동기화를 위해 구성해야 하는 모든 Azure 파일 공유/서버 위치에 대해 동기화 그룹을 만들고, 일치하는 서버 폴더를 서버 엔드포인트로 추가하는 단계를 반복합니다. Azure Data Box를 사용하여 파일을 여러 Azure 파일 공유로 이동했습니다. 온-프레미스 데이터를 이러한 Azure 파일 공유에 연결하는 모든 서버 엔드포인트가 만들어지면 마이그레이션이 완료됩니다.

## <a name="next-steps"></a>다음 단계

Azure 파일 공유 및 Azure 파일 동기화에 대해 더 자세히 알아볼 수 있습니다. 다음 문서는 고급 옵션과 모범 사례를 이해하는 데 도움이 됩니다. 또한 문제 해결에 대한 도움말도 제공합니다. 다음 문서에는 해당하는 경우 [Azure 파일 공유 설명서](storage-files-introduction.md)에 대한 링크가 포함되어 있습니다.

* [마이그레이션 개요](storage-files-migration-overview.md)
* [Azure 파일 동기화 배포에 대한 계획](../file-sync/file-sync-planning.md)
* [파일 공유 만들기](storage-how-to-create-file-share.md)
* [Azure 파일 동기화 문제 해결](../file-sync/file-sync-troubleshoot.md)
