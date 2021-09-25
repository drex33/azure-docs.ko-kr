---
title: Azure NetApp Files 스냅샷의 작동 방식 | Microsoft Docs
description: 스냅샷을 만드는 방법, 스냅샷을 복원하는 방법, 지역 간 복제 설정에서 스냅샷을 사용하는 방법을 비롯하여 Azure NetApp Files 스냅샷의 작동 방식을 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: b-juche
ms.openlocfilehash: 080cf9dac0733fd1797164ddf87e52a800771d18
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614118"
---
# <a name="how-azure-netapp-files-snapshots-work"></a>Azure NetApp Files 스냅샷 작동 방식

이 문서에서는 Azure NetApp Files 스냅샷의 작동 방식을 설명합니다. Azure NetApp Files 스냅샷 기술은 성능에 영향을 주지 않고 안정성, 확장성 및 빠른 복구 기능을 제공합니다. Azure NetApp Files 스냅샷 기술은 단일 파일 복원, 볼륨 복원 및 클론, 지역 간 복제를 포함하여 데이터 보호 솔루션에 대한 토대를 제공합니다. 

볼륨 스냅샷 사용에 대한 단계는 [Azure NetApp Files를 사용하여 스냅샷 관리](azure-netapp-files-manage-snapshots.md)를 참조하세요. 지역 간 복제의 스냅샷 관리에 대한 고려 사항은 [지역 간 복제 사용을 위한 요구 사항 및 고려 사항](cross-region-replication-requirements-considerations.md)을 참조하세요.

## <a name="what-volume-snapshots-are"></a>볼륨 스냅샷의 정의  

Azure NetApp Files 스냅샷은 특정 시점 파일 시스템(볼륨) 이미지입니다. 이상적인 온라인 백업으로 사용할 수 있습니다. 스냅샷을 사용하여 [새 볼륨 만들기](snapshots-restore-new-volume.md), [파일 복원](snapshots-restore-file-client.md) 또는 [볼륨 되돌리기](snapshots-revert-volume.md)를 수행할 수 있습니다. Azure NetApp Files 볼륨에 저장된 특정 애플리케이션 데이터에서 애플리케이션 일관성을 보장하기 위해 추가 단계가 필요할 수 있습니다.  

Azure NetApp Files의 일부인 기본 볼륨 가상화 기술에 대한 고유 기능을 통해 오버헤드가 낮은 스냅샷을 만들 수 있습니다. 데이터베이스와 마찬가지로 이 계층은 디스크의 실제 데이터 블록에 대한 포인터를 사용합니다. 그러나 데이터베이스와 달리 기존 블록은 다시 작성되지 않습니다. 업데이트된 데이터를 새 블록에 쓰고 포인터를 변경합니다. Azure NetApp Files 스냅샷은 단순히 블록 포인터를 조작하여 애플리케이션이 특별한 프로그래밍 없이 이전 버전의 파일 및 디렉터리 계층에 액세스할 수 있도록 하는 “고정된” 읽기 전용 볼륨 보기를 만듭니다. 실제 데이터 블록은 복사되지 않습니다. 따라서 스냅샷은 만드는 데 필요한 시간 면에서 효율적입니다. 볼륨 크기에 관계없이 거의 즉각적입니다. 스냅샷은 스토리지 공간 면에서도 효율적입니다. 스냅샷 자체는 공간을 사용하지 않으며 스냅샷과 활성 볼륨 간의 델타 블록만 유지됩니다. 

다음 다이어그램은 개념을 보여 줍니다.  

![스냅샷의 주요 개념을 보여 주는 다이어그램](../media/azure-netapp-files/snapshot-concepts.png)

다이어그램에서 스냅샷은 그림 1a에 나와 있습니다. 그림 1b에서 변경된 데이터는 *새 블록* 에 기록되고 포인터가 업데이트됩니다. 그러나 스냅샷 포인터는 여전히 *이전에 작성된 블록* 을 가리키므로 데이터의 실시간 및 기록 보기를 제공합니다. 다른 스냅샷은 그림 1c에 나와 있습니다. 이제 세 개의 전체 복사본에 필요한 볼륨 공간을 차지하지 않고도 세 개의 데이터 세대(라이브 데이터, 스냅샷 2 및 스냅샷 1)에 액세스할 수 있습니다. 

스냅샷은 볼륨 메타데이터(*inode 테이블*)의 복사본만 사용합니다. 볼륨 크기, 사용된 용량 또는 볼륨의 작업 수준에 관계없이 만드는 데 몇 초밖에 걸리지 않습니다. 따라서 100TiB 볼륨의 스냅샷을 만드는 데는 100GiB 볼륨의 스냅샷을 만드는 것과 동일한 시간(거의 0)이 소요됩니다. 스냅샷이 만들어진 후에는 데이터 파일에 대한 변경 내용이 정상적으로 파일의 활성 버전에 반영됩니다.

한편 스냅샷에서 가리키는 데이터 블록은 안정적이고 변경할 수 없는 상태로 유지됩니다. Azure NetApp Files 볼륨의 “쓰기 시 리디렉션” 특성으로 인해 스냅샷은 성능 오버헤드를 발생시키지 않으며 스냅샷 자체로서는 공간을 사용하지 않습니다. 시간이 지남에 따라 볼륨당 최대 255개의 스냅샷을 저장할 수 있습니다. 모든 데이터는 읽기 전용 및 온라인 버전의 데이터로 액세스할 수 있으며 각 스냅샷 간에 변경된 블록 수만큼의 용량을 소비합니다. 수정된 블록은 활성 볼륨에 저장됩니다. 스냅샷에서 가리키는 블록은 안전한 보관을 위해 볼륨에서 읽기 전용으로 보관되며, 활성 볼륨과 스냅샷의 모든 포인터가 지워진 경우에만 용도가 변경됩니다. 따라서 볼륨 사용률은 스냅샷에 유지되는 새 데이터 블록 또는 (수정된) 데이터 블록으로 인해 시간이 지남에 따라 증가합니다.

 다음 다이어그램은 시간 경과에 따른 볼륨의 스냅샷 및 사용된 공간을 보여 줍니다. 

![시간 경과에 따른 볼륨 스냅샷 및 사용된 공간을 보여 주는 다이어그램](../media/azure-netapp-files/snapshots-used-space-over-time.png)

볼륨 스냅샷은 최신 스냅샷 이후 변경된 블록만 기록하기 때문에 다음과 같은 주요 이점을 제공합니다.

* 스냅샷은 ***스토리지 면에서 효율적*** 입니다.   
    스냅샷은 전체 볼륨의 데이터 블록을 복사하지 않으므로 최소한의 스토리지 공간을 사용합니다. 순서대로 찍은 두 개의 스냅샷은 둘 사이의 시간 간격에 추가되거나 변경된 블록만 다릅니다. 이 블록 증분 동작은 관련 스토리지 용량 소비를 제한합니다. 많은 대체 스냅샷 구현은 활성 파일 시스템과 동일한 스토리지 볼륨을 사용하므로 스토리지 용량 요구 사항이 높아집니다. 애플리케이션의 일일 *블록 수준* 변경률에 따라 Azure NetApp Files 스냅샷은 변경된 데이터에 대해서만 더 많거나 적은 용량을 차지합니다. 평균 일별 스냅샷 소비 범위는 많은 애플리케이션 볼륨에 대해 사용된 볼륨 용량의 1~5%, SAP HANA 데이터베이스 볼륨과 같은 볼륨의 경우 최대 20~30%입니다. 만들어지고 유지 관리되는 스냅샷 수와 관련된 스냅샷 용량 소비에 대해 [볼륨 및 스냅샷 사용량을 모니터링](azure-netapp-files-metrics.md#volumes)해야 합니다.   

* 스냅샷은 ***빠르게 만들고 복원 또는 복제할 수 있습니다***.   
    볼륨 크기와 활동 수준에 관계없이 스냅샷을 만들고 복원 또는 복제하는 데에는 몇 초밖에 걸리지 않습니다. [주문형](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume) 볼륨 스냅샷을 만들 수 있습니다. 또한 [스냅샷 정책](snapshots-manage-policy.md)을 사용하여 Azure NetApp Files에서 자동으로 스냅샷을 만들어야 하는 시기와 볼륨에 대해 보관할 스냅샷 수를 지정할 수 있습니다.  예를 들어 SAP HANA용 [AzAcSnap 도구](azacsnap-introduction.md)를 사용하여 애플리케이션 계층으로 스냅샷을 조정하여 애플리케이션 일관성을 달성할 수 있습니다.

* 스냅샷은 볼륨 ***성능*** 에 영향을 미치지 않습니다.   
    기본 기술의 “쓰기 시 리디렉션” 특성으로 인해 Azure NetApp Files 스냅샷을 데이터 활동이 많을 때 저장하거나 유지해도 성능에는 아무 영향을 미치지 않습니다. 스냅샷을 삭제하는 것도 대부분의 경우 성능에 영향을 주지 않습니다. 

* 스냅샷은 자주 만들 수 있고 많이 유지할 수 있기 때문에 ***확장성*** 을 제공합니다.   
    Azure NetApp Files 볼륨은 최대 255개의 스냅샷을 지원합니다. 자주 만들어지는 낮은 영향의 대량 스냅샷을 저장하는 기능을 통해 원하는 버전의 데이터가 성공적으로 복구될 수 있는 가능성을 높일 수 있습니다.

* 스냅샷은 ***사용자 표시 유형** _ 및 _*_파일 복구 가능성_**을 제공합니다.   
Azure NetApp Files 스냅샷 기술의 높은 성능, 확장성 및 안정성은 사용자 기반 복구를 위한 이상적인 온라인 백업을 제공한다는 것을 의미합니다. 스냅샷은 파일, 디렉터리 또는 볼륨 복원 용도로 사용자가 액세스할 수 있도록 만들 수 있습니다. 추가 솔루션을 사용하면 백업을 오프라인 스토리지에 복사하거나 보존 또는 재해 복구 목적으로 [지역 간 복제](cross-region-replication-introduction.md)할 수 있습니다.

## <a name="ways-to-create-snapshots"></a>스냅샷을 만드는 방법   

여러 가지 방법을 사용하여 스냅샷을 만들고 유지 관리할 수 있습니다.

* 다음을 사용하여 수동으로(주문형):   
    * [Azure Portal](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume), [REST API](/rest/api/netapp/snapshots), [Azure CLI](/cli/azure/netappfiles/snapshot) 또는 [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshot) 도구
    * 스크립트([예](azure-netapp-files-solution-architectures.md#sap-tech-community-and-blog-posts) 참조)

* 다음을 사용하여 자동화됨:
    * [Azure Portal](snapshots-manage-policy.md), [REST API](/rest/api/netapp/snapshotpolicies), [Azure CLI](/cli/azure/netappfiles/snapshot/policy) 또는 [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshotpolicy) 도구를 통한 스냅샷 정책
    * [AzAcSnap](azacsnap-introduction.md)과 같은 애플리케이션 일치 스냅샷 도구

## <a name="how-volumes-and-snapshots-are-replicated-cross-region-for-dr"></a>DR을 위해 볼륨 및 스냅샷이 지역 간 복제되는 방식  

Azure NetApp Files는 DR(재해 복구) 목적으로 [지역 간 복제](cross-region-replication-introduction.md)를 지원합니다. Azure NetApp Files의 지역 간 복제는 SnapMirror 기술을 사용합니다. 변경된 블록만이 압축된 효과적인 형태로 네트워크를 통해 전송됩니다. 볼륨 간에 영역 간 복제가 시작되면 전체 볼륨 내용(즉, 실제 저장된 데이터 블록)은 한 번만 전송됩니다. 이 작업을 *기준 전송* 이라고 합니다. 초기 전송 후에는 변경된 블록(스냅샷에서 캡처한 블록)만 전송됩니다. 결과는 모든 스냅샷을 포함한 원본 볼륨의 비동기 1:1 복제본입니다. 이 동작은 전체 및 증분-영구 복제 메커니즘을 따릅니다. 이 기술은 지역 간에 복제하는 데 필요한 데이터의 양을 최소화하여 데이터 전송 비용을 절감합니다. 또한 복제 시간을 단축합니다. 제한된 데이터 전송으로 더 많은 스냅샷을 더 자주 만들고 전송할 수 있으므로 더 작은 RPO(복구 지점 목표)를 달성할 수 있습니다. 또한 가상 머신 및 소프트웨어 라이선스 비용을 방지하여 호스트 기반 복제 메커니즘의 필요성을 제거합니다.

다음 다이어그램은 지역 간 복제 시나리오의 스냅샷 트래픽을 보여 줍니다. 

![지역 간 복제 시나리오의 스냅샷 트래픽을 보여 주는 다이어그램](../media/azure-netapp-files/snapshot-traffic-cross-region-replication.png)

## <a name="ways-to-restore-data-from-snapshots"></a>스냅샷에서 데이터를 복원하는 방법  

Azure NetApp Files 스냅샷 기술은 백업의 빈도와 안정성을 크게 향상시킵니다. 성능 오버헤드가 최소화되며 활성 볼륨에서 안전하게 만들 수 있습니다. Azure NetApp Files 스냅샷은 거의 즉각적이고 안전하며 선택적으로 사용자 관리형 복원을 허용합니다. 이 섹션에서는 Azure NetApp Files 스냅샷에서 데이터에 액세스하거나 복원할 수 있는 다양한 방법에 대해 설명합니다.

### <a name="restoring-files-or-directories-from-snapshots"></a>스냅샷에서 파일 또는 디렉터리 복원 

[스냅샷 경로 표시 유형](snapshots-edit-hide-path.md)이 `hidden`으로 설정되지 않았다면 사용자는 스냅샷에 직접 액세스하여 실수로 데이터를 삭제하거나 손상시키거나 수정한 경우 복구할 수 있습니다. 파일 및 디렉터리의 보안은 스냅샷에 유지되고 스냅샷은 의도적으로 읽기 전용입니다. 따라서 복원은 안전하고 간단합니다. 

다음 다이어그램은 스냅샷에 대한 파일 또는 디렉터리 액세스를 보여 줍니다. 

![스냅샷에 대한 파일 또는 디렉터리 액세스를 보여 주는 다이어그램](../media/azure-netapp-files/snapshot-file-directory-access.png)

다이어그램에서 스냅샷 1은 활성 볼륨과 스냅샷이 만들어지는 순간 사이의 델타 블록만 사용합니다. 그러나 볼륨 스냅샷 경로를 통해 스냅샷에 액세스하면 데이터가 스냅샷이 만들어지는 당시의 전체 볼륨 용량인 것처럼 *나타납니다*. 사용자는 스냅샷 폴더에 액세스하고 선택한 스냅샷에서 파일 및 디렉터리를 복사하여 데이터를 자체 복원할 수 있습니다.

마찬가지로 대상 지역 간 복제 볼륨의 스냅샷은 DR 지역에서 데이터 복구를 위해 읽기 전용으로 액세스할 수 있습니다.  

다음 다이어그램은 지역 간 복제 시나리오에서 스냅샷 액세스를 보여 줍니다. 

![지역 간 복제의 스냅샷 액세스를 보여 주는 다이어그램](../media/azure-netapp-files/snapshot-access-cross-region-replication.png)

스냅샷에서 개별 파일 또는 디렉터리를 복원하는 방법은 [클라이언트를 사용하여 스냅샷에서 파일 복원](snapshots-restore-file-client.md)을 참조하세요.

### <a name="restoring-cloning-a-snapshot-to-a-new-volume"></a>스냅샷을 새 볼륨으로 복원(복제)

별도의 독립적인 볼륨으로 Azure NetApp Files 스냅샷을 복원할 수 있습니다. 볼륨 크기 및 사용되는 용량에 관계없이 이 작업은 거의 즉각적입니다. 실제 볼륨 및 스냅샷 데이터 블록을 복사하는 동안 새로 만든 볼륨은 거의 즉시 액세스할 수 있습니다. 볼륨 크기와 용량에 따라 이 프로세스는 상위 볼륨 및 스냅샷을 삭제할 수 없는 동안 상당한 시간이 소요될 수 있습니다. 그러나 백그라운드에서 복사 프로세스가 진행되는 동안 볼륨은 초기 생성 후 이미 액세스할 수 있습니다. 이 기능을 사용하면 테스트 및 개발을 위한 데이터 복구 또는 볼륨 복제를 위한 빠른 볼륨 만들기가 가능합니다. 데이터 복사 프로세스의 특성상 복원이 완료되면 스토리지 용량 풀 사용이 두 배가 되며 새 볼륨에 원래 스냅샷의 전체 활성 용량이 표시됩니다. 이 프로세스가 완료되면 볼륨은 독립되고 원래 볼륨과의 연결이 끊어지며 원본 볼륨과 스냅샷은 새 볼륨과 독립적으로 관리하거나 제거할 수 있습니다.

다음 다이어그램은 스냅샷을 복원(복제)하여 만든 새 볼륨을 보여 줍니다.   

![스냅샷을 복원하여 만든 새 볼륨을 보여 주는 다이어그램](../media/azure-netapp-files/snapshot-restore-clone-new-volume.png)

DR(재해 복구) 볼륨에 복제된 스냅샷에 대해 동일한 작업을 수행할 수 있습니다. 지역 간 복제가 활성 상태로 유지되거나 진행 중인 경우에도 모든 스냅샷을 새 볼륨으로 복원할 수 있습니다. 이 기능을 사용하면 DR 지역의 테스트 및 개발 환경을 중단 없이 만들어 사용할 데이터를 배치할 수 있지만 복제된 볼륨은 DR 용도로만 사용됩니다. 이 사용 사례를 통해 테스트 및 개발을 프로덕션에서 격리하여 프로덕션 환경에 미치는 잠재적 영향을 없앨 수 있습니다.  

다음 다이어그램에서는 지역 간 복제가 수행되는 동안 DR 대상 볼륨 스냅샷을 사용하여 볼륨 복원(복제)을 보여 줍니다.  

![DR 대상 볼륨 스냅샷을 사용하여 볼륨 복원을 보여 주는 다이어그램](../media/azure-netapp-files/snapshot-restore-clone-target-volume.png)

볼륨 복원 작업에 대해서는 [스냅샷을 새 볼륨으로 복원](snapshots-restore-new-volume.md)을 참조하세요.

### <a name="restoring-reverting-a-snapshot-in-place"></a>스냅샷 현재 위치 복원(되돌리기)

경우에 따라 새 볼륨에서 스토리지 용량을 사용하므로 스냅샷을 통해 새 볼륨을 만드는 것이 필요하지 않거나 적절하지 않을 수도 있습니다. 데이터 손상(예: 데이터베이스 손상 또는 랜섬웨어 공격)에서 신속하게 복구하려면 볼륨 자체 내에서 스냅샷을 복원하는 것이 더 적합할 수 있습니다. Azure NetApp Files 스냅샷 되돌리기 기능을 사용하여 이 작업을 수행할 수 있습니다. 이 기능을 사용하면 볼륨을 특정 스냅샷을 만들 때의 상태로 신속하게 되돌릴 수 있습니다. 대부분의 경우 볼륨을 되돌리는 것이 스냅샷에서 활성 파일 시스템으로 개별 파일을 복원하는 것보다 훨씬 빠릅니다. 특히 다중 TiB 볼륨이 큰 경우에는 더욱 그렇습니다. 

볼륨 스냅샷 되돌리기는 거의 즉각적이며, 가장 큰 볼륨의 경우에도 완료하는 데 몇 초밖에 걸리지 않습니다. 활성 볼륨 메타데이터(*inode 테이블)는* 스냅샷이 만들어진 시점의 스냅샷 메타데이터로 대체되므로 볼륨을 특정 시점으로 롤백합니다. 되돌리기를 적용하기 위해 데이터 블록을 복사할 필요가 없습니다. 따라서 스냅샷을 새 볼륨으로 복원하는 것보다 공간 면에서 더 효율적입니다. 

다음 다이어그램은 이전 스냅샷으로 되돌리는 볼륨을 보여 줍니다.  

![이전 스냅샷으로 되돌리는 볼륨을 보여 주는 다이어그램](../media/azure-netapp-files/snapshot-volume-revert.png)

> [!IMPORTANT]
> 작성 된 활성 파일 시스템 데이터와 선택한 스냅숏 이후에 만들어진 스냅숏이 손실 됩니다. 스냅샷 되돌리기 작업은 대상 볼륨의 모든 데이터를 선택한 스냅샷의 데이터로 바꿉니다. 스냅샷을 선택하는 경우 스냅샷 콘텐츠와 만든 날짜에 주의를 기울여야 합니다. 스냅샷 되돌리기 작업은 실행 취소할 수 없습니다.  

이 기능을 사용하는 방법은 [스냅샷 되돌리기를 사용하여 볼륨 되돌리기](snapshots-revert-volume.md)를 참조하세요.

## <a name="how-snapshots-are-deleted"></a>스냅샷을 삭제하는 방법 

스냅샷은 스토리지 용량을 사용합니다. 따라서 일반적으로 무기한 유지되지 않습니다. 데이터 보호, 보존 및 복구 가능성을 위해 RPO, RTO 및 보존 SLA 요구 사항에 따라 여러 스냅샷(다양한 시점에 생성됨)이 일반적으로 특정 기간 동안 온라인 상태로 유지됩니다. 그러나 오래된 스냅샷은 스토리지 서비스에 보관하지 않아도 되고 공간을 확보하기 위해 삭제해야 할 수 있습니다. 언제든지 관리자가 모든 스냅샷을 삭제할 수 있습니다(반드시 생성 순서는 아님). 

> [!IMPORTANT]
> 스냅샷 삭제 작업은 실행 취소할 수 없습니다. 데이터 보호 및 보존을 위해 볼륨의 오프라인 복사본을 유지해야 합니다. 

스냅샷이 삭제되면 해당 스냅샷과 기존 데이터 블록 사이의 모든 포인터가 제거됩니다. 데이터 블록에 더 이상 가리키는 포인터가 없으면(활성 볼륨 또는 볼륨의 다른 스냅샷에 의해) 데이터 블록은 나중에 사용할 수 있도록 볼륨 여유 공간으로 반환됩니다. 따라서 스냅샷을 제거하면 이전에 만든 스냅샷에서 데이터 블록이 종종 캡처되므로 활성 볼륨에서 데이터를 삭제하는 경우보다 볼륨의 사용 가능한 용량이 더 늘어납니다. 

다음 다이어그램은 볼륨에서 스냅샷 3을 삭제하는 것이 스토리지 사용량에 미치는 영향을 보여 줍니다.  

![스냅샷 삭제가 스토리지 소비에 미치는 영향을 보여 주는 다이어그램](../media/azure-netapp-files/snapshot-delete-storage-consumption.png)

[볼륨 및 스냅샷 소비를 모니터링](azure-netapp-files-metrics.md#volumes)하고 애플리케이션, 활성 볼륨 및 스냅샷 소비가 상호 작용하는 방식을 이해해야 합니다. 

스냅샷 삭제를 관리하는 방법은 [스냅샷 삭제](snapshots-delete.md)를 참조하세요. 이 프로세스를 자동화하는 방법은 [스냅샷 정책 관리](snapshots-manage-policy.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [NetApp Azure Files를 사용하여 스냅샷 관리](azure-netapp-files-manage-snapshots.md)
* [볼륨 및 스냅샷 메트릭 모니터링](azure-netapp-files-metrics.md#volumes)
* [스냅샷 정책 문제 해결](troubleshoot-snapshot-policies.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [Azure NetApp Files 스냅샷 101 동영상](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Azure NetApp Files 스냅샷 개요](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)



