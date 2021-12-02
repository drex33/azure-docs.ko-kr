---
title: Azure NetApp Files 스냅샷의 작동 방식 | Microsoft Docs
description: 스냅샷을 만드는 방법, 스냅샷을 복원하는 방법, 지역 간 복제 설정에서 스냅샷을 사용하는 방법을 비롯하여 Azure NetApp Files 스냅샷의 작동 방식을 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-hchen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/27/2021
ms.author: b-hchen
ms.openlocfilehash: 7313c0d092de2b9ce57d17c0cec0bdc880d6ea5d
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133484926"
---
# <a name="how-azure-netapp-files-snapshots-work"></a>Azure NetApp Files 스냅샷 작동 방식

이 문서에서는 Azure NetApp Files 스냅샷의 작동 방식을 설명합니다. Azure NetApp Files 스냅샷 기술은 성능에 영향을 주지 않고 안정성, 확장성 및 빠른 복구 기능을 제공합니다. 단일 파일 복원, 볼륨 복원 및 복제, 지역 간 복제 및 장기 보존을 포함 하 여 데이터 보호 솔루션에 대 한 토대를 제공 합니다. 

볼륨 스냅샷 사용에 대한 단계는 [Azure NetApp Files를 사용하여 스냅샷 관리](azure-netapp-files-manage-snapshots.md)를 참조하세요. 지역 간 복제의 스냅샷 관리에 대한 고려 사항은 [지역 간 복제 사용을 위한 요구 사항 및 고려 사항](cross-region-replication-requirements-considerations.md)을 참조하세요.

## <a name="what-volume-snapshots-are"></a>볼륨 스냅샷의 정의  

Azure NetApp Files 스냅샷은 특정 시점 파일 시스템(볼륨) 이미지입니다. 온라인 백업으로 제공 하는 것이 좋습니다. 스냅숏을 사용 하 여 [새 볼륨](snapshots-restore-new-volume.md) (복제)을 만들거나, 파일을 [복원](snapshots-restore-file-client.md)하거나, [볼륨을 되돌릴](snapshots-revert-volume.md)수 있습니다. Azure NetApp Files 볼륨에 저장된 특정 애플리케이션 데이터에서 애플리케이션 일관성을 보장하기 위해 추가 단계가 필요할 수 있습니다.  

Azure NetApp Files의 일부인 기본 볼륨 가상화 기술에 대한 고유 기능을 통해 오버헤드가 낮은 스냅샷을 만들 수 있습니다. 데이터베이스와 마찬가지로 이 계층은 디스크의 실제 데이터 블록에 대한 포인터를 사용합니다. 그러나 데이터베이스와 달리 기존 블록은 다시 작성 되지 않습니다. 업데이트 된 데이터를 새 블록에 쓰고 포인터를 변경 하 여 새 데이터와 이전 데이터를 유지 합니다. Azure NetApp Files 스냅샷은 단순히 블록 포인터를 조작하여 애플리케이션이 특별한 프로그래밍 없이 이전 버전의 파일 및 디렉터리 계층에 액세스할 수 있도록 하는 “고정된” 읽기 전용 볼륨 보기를 만듭니다. 실제 데이터 블록은 복사되지 않습니다. 따라서 스냅샷은 만드는 데 필요한 시간 면에서 효율적입니다. 볼륨 크기에 관계없이 거의 즉각적입니다. 스냅숏은 저장소 공간 에서도 효율적입니다. 스냅숏과 활성 볼륨 간의 델타 블록만 유지 됩니다.

다음 다이어그램은 개념을 보여 줍니다.  

![스냅샷의 주요 개념을 보여 주는 다이어그램](../media/azure-netapp-files/snapshot-concepts.png)

다이어그램에서 스냅샷은 그림 1a에 나와 있습니다. 그림 1b에서 변경 된 데이터는 *새 블록* B1에 기록 되 고 포인터는 업데이트 됩니다. 그러나 스냅숏 포인터는 여전히 *이전에 작성 된 블록* B를 가리키지만 데이터의 라이브 및 기록 보기를 제공 합니다. 다른 스냅샷은 그림 1c에 나와 있습니다. 이제 세 개의 전체 복사본에 필요한 볼륨 공간을 차지하지 않고도 세 개의 데이터 세대(라이브 데이터, 스냅샷 2 및 스냅샷 1)에 액세스할 수 있습니다. 

스냅샷은 볼륨 메타데이터(*inode 테이블*)의 복사본만 사용합니다. 볼륨 크기, 사용된 용량 또는 볼륨의 작업 수준에 관계없이 만드는 데 몇 초밖에 걸리지 않습니다. 따라서 100 TiB 볼륨의 스냅숏을 만드는 것은 100-GiB 볼륨의 스냅숏을 만드는 것과 동일한 시간 (0)을 사용 합니다. 스냅샷이 만들어진 후에는 데이터 파일에 대한 변경 내용이 정상적으로 파일의 활성 버전에 반영됩니다.

한편 스냅숏에서 가리키는 데이터 블록은 안정적이 고 변경할 수 없는 상태로 유지 됩니다. Azure NetApp Files 볼륨의 "리디렉션 쓰기" 특성 때문에 스냅숏에는 성능 오버 헤드가 발생 하지 않으며, 그 자체로는 어떠한 공간도 소비 하지 않습니다. 시간이 지남에 따라 볼륨당 최대 255개의 스냅샷을 저장할 수 있습니다. 모든 데이터는 읽기 전용 및 온라인 버전의 데이터로 액세스할 수 있으며 각 스냅샷 간에 변경된 블록 수만큼의 용량을 소비합니다. 수정된 블록은 활성 볼륨에 저장됩니다. 스냅샷에서 가리키는 블록은 안전한 보관을 위해 볼륨에서 읽기 전용으로 보관되며, 활성 볼륨과 스냅샷의 모든 포인터가 지워진 경우에만 용도가 변경됩니다. 따라서 볼륨 사용률은 스냅샷에 유지되는 새 데이터 블록 또는 (수정된) 데이터 블록으로 인해 시간이 지남에 따라 증가합니다.

 다음 다이어그램은 시간 경과에 따른 볼륨의 스냅샷 및 사용된 공간을 보여 줍니다. 

![시간 경과에 따른 볼륨 스냅샷 및 사용된 공간을 보여 주는 다이어그램](../media/azure-netapp-files/snapshots-used-space-over-time.png)

볼륨 스냅샷은 최신 스냅샷 이후 변경된 블록만 기록하기 때문에 다음과 같은 주요 이점을 제공합니다.

* 스냅샷은 ***스토리지 면에서 효율적*** 입니다.   
    스냅숏은 전체 볼륨의 데이터 블록을 복사 하지 않으므로 최소한의 저장소 공간을 사용 합니다. 시퀀스에서 생성되는 두 스냅샷은 두 스냅샷 사이의 시간 간격으로 추가되거나 변경된 블록에 의해서만 다릅니다. 이 블록 증분 동작은 관련 저장소 용량 소비를 최소화 합니다. 많은 대체 스냅샷 구현은 활성 파일 시스템과 동일한 스토리지 볼륨을 사용하므로 스토리지 용량 요구 사항이 높아집니다. 애플리케이션의 일일 *블록 수준* 변경률에 따라 Azure NetApp Files 스냅샷은 변경된 데이터에 대해서만 더 많거나 적은 용량을 차지합니다. 평균 일별 스냅샷 소비 범위는 많은 애플리케이션 볼륨에 대해 사용된 볼륨 용량의 1~5%, SAP HANA 데이터베이스 볼륨과 같은 볼륨의 경우 최대 20~30%입니다. 만들어지고 유지 관리되는 스냅샷 수와 관련된 스냅샷 용량 소비에 대해 [볼륨 및 스냅샷 사용량을 모니터링](azure-netapp-files-metrics.md#volumes)해야 합니다.   

* 스냅샷은 ***빠르게 만들고 복원 또는 복제할 수 있습니다***.   
    볼륨의 볼륨 크기와 활동 수준에 관계 없이 스냅숏을 생성, 복제, 복원 또는 복제 하는 데 몇 초 밖에 걸리지 않습니다. [요청 시 볼륨 스냅숏을 만들](azure-netapp-files-manage-snapshots.md)수 있습니다. 또한 [스냅샷 정책](snapshots-manage-policy.md)을 사용하여 Azure NetApp Files에서 자동으로 스냅샷을 만들어야 하는 시기와 볼륨에 대해 보관할 스냅샷 수를 지정할 수 있습니다.  예를 들어 SAP HANA용 [AzAcSnap 도구](azacsnap-introduction.md)를 사용하여 애플리케이션 계층으로 스냅샷을 조정하여 애플리케이션 일관성을 달성할 수 있습니다.

* 스냅샷은 볼륨 ***성능*** 에 영향을 미치지 않습니다.   
    기본 기술의 “쓰기 시 리디렉션” 특성으로 인해 Azure NetApp Files 스냅샷을 데이터 활동이 많을 때 저장하거나 유지해도 성능에는 아무 영향을 미치지 않습니다. 스냅샷을 삭제하는 것도 대부분의 경우 성능에 영향을 주지 않습니다. 

* 스냅숏은 자주 생성 될 수 있고 많은 것을 유지할 수 있기 때문에 ***확장성*** 을 제공 합니다.   
    Azure NetApp Files 볼륨은 볼륨당 최대 255 개의 스냅숏을 지원 합니다. 자주 생성 되는 많은 스냅숏을 저장 하는 기능을 통해 원하는 버전의 데이터가 성공적으로 복구 될 수 있는 가능성을 높일 수 있습니다.

* 스냅숏은 Azure storage에 ***vaulted*** 수 있습니다.   
    규정 준수 및 장기적인 데이터 보존 요구 사항에 대 한 [Azure NetApp Files 백업](backup-introduction.md) 기능을 사용 하 여 보호 되는 볼륨의 외부에서 비용 효율적이 고 ZRS 사용이 가능한 Azure storage에 대 한 스냅숏을 보관 합니다.  

* 스냅샷은 ***사용자 표시 유형** _ 및 _*_파일 복구 가능성_**을 제공합니다.   

Azure NetApp Files 스냅샷 기술의 높은 성능, 확장성 및 안정성은 사용자 기반 복구를 위한 이상적인 온라인 백업을 제공한다는 것을 의미합니다. 스냅샷은 파일, 디렉터리 또는 볼륨 복원 용도로 사용자가 액세스할 수 있도록 만들 수 있습니다. 추가 솔루션을 사용 하면 오프 라인 저장소에 백업을 복사 하거나 보존 또는 재해 복구를 위해 [지역 간 복제](cross-region-replication-introduction.md) 를 수행할 수 있습니다.

## <a name="ways-to-create-snapshots"></a>스냅샷을 만드는 방법   

여러 가지 방법을 사용하여 스냅샷을 만들고 유지 관리할 수 있습니다.

* 다음을 사용하여 수동으로(주문형):   
    * [Azure Portal](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume), [REST API](/rest/api/netapp/snapshots), [Azure CLI](/cli/azure/netappfiles/snapshot) 또는 [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshot) 도구
    * 스크립트([예](azure-netapp-files-solution-architectures.md#sap-tech-community-and-blog-posts) 참조)

* 다음을 사용하여 자동화됨:
    * [Azure Portal](snapshots-manage-policy.md), [REST API](/rest/api/netapp/snapshotpolicies), [Azure CLI](/cli/azure/netappfiles/snapshot/policy) 또는 [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshotpolicy) 도구를 통한 스냅샷 정책
    * [AzAcSnap](azacsnap-introduction.md)과 같은 애플리케이션 일치 스냅샷 도구

## <a name="how-volumes-and-snapshots-are-replicated-cross-region-for-dr"></a>DR을 위해 볼륨 및 스냅샷이 지역 간 복제되는 방식  

Azure NetApp Files는 DR (재해 복구)을 위해 [지역 간 복제](cross-region-replication-introduction.md) 를 지원 합니다. Azure NetApp Files의 지역 간 복제는 SnapMirror 기술을 사용합니다. 변경된 블록만이 압축된 효과적인 형태로 네트워크를 통해 전송됩니다. 볼륨 간에 영역 간 복제가 시작되면 전체 볼륨 내용(즉, 실제 저장된 데이터 블록)은 한 번만 전송됩니다. 이 작업을 *기준 전송* 이라고 합니다. 초기 전송 후에는 변경된 블록(스냅샷에서 캡처한 블록)만 전송됩니다. 결과는 모든 스냅샷을 포함한 원본 볼륨의 비동기 1:1 복제본입니다. 이 동작은 전체 및 증분-영구 복제 메커니즘을 따릅니다. 이 기술은 지역 간에 복제하는 데 필요한 데이터의 양을 최소화하여 데이터 전송 비용을 절감합니다. 또한 복제 시간을 단축합니다. 최소한의 데이터 전송으로 더 많은 스냅숏을 만들고 더 자주 전송할 수 있으므로 더 작은 RPO (복구 지점 목표)를 달성할 수 있습니다. 또한 가상 머신 및 소프트웨어 라이선스 비용을 방지하여 호스트 기반 복제 메커니즘의 필요성을 제거합니다.

다음 다이어그램은 지역 간 복제 시나리오의 스냅샷 트래픽을 보여 줍니다. 

![지역 간 복제 시나리오의 스냅샷 트래픽을 보여 주는 다이어그램](../media/azure-netapp-files/snapshot-traffic-cross-region-replication.png)

## <a name="how-snapshots-can-be-vaulted-for-long-term-retention-and-cost-savings"></a>장기 보존 및 비용 절감을 위해 스냅숏을 vaulted 하는 방법

설명 된 대로 스냅숏은 효율적이 고 신속 하 게 Azure NetApp Files 볼륨을 빠르고 효율적으로 백업 하는 데 사용 되며, 데이터 파일을 복원 하거나 볼륨을 완전히 효율적으로 복원 하는 수단을 제공 합니다. 이러한 온라인 스냅숏은 첫 번째 방어 줄로 제공 되며 대부분의 데이터 복구 작업을 다룹니다.   

스냅숏을 오랜 시간 동안 유지 하거나 최대 온라인 스냅숏 수 보다 더 많은 스냅숏을 유지 하려면 Azure NetApp Files 볼륨에서 ZRS 사용 Azure 저장소로 스냅숏을 자격 증명 모음 할 수 있습니다.  이는 [*Azure NetApp Files 백업*](backup-introduction.md) 기능으로 인해 촉진 됩니다.  기능은 확장 된 시간에 대 한 스냅숏을 유지 합니다 (최대 1 년 또는 그 이상). 백업은 Azure storage에 저장 되며, Azure NetApp Files 용량 풀 비용에 비해 비용을 절감 하 고 다른 저장소 플랫폼을 활용 하 여 종속성을 제거 하 고 보존 요구 사항을 준수 합니다.

Azure NetApp Files 볼륨에서 스냅숏 보관을 사용 하도록 설정 하려면 데이터 보호 섹션 아래에 있는 Azure NetApp Files 구독에서 [백업 정책을 구성](backup-configure-policy-based.md) 하 고 보관할 매일, 매주 및 매월 백업 수를 지정 합니다. 비용 효율적인 장기 저장소로 데이터 보호를 확장 하기 위해 수행 해야 하는 작업입니다.  

다음 다이어그램에서는 Azure NetApp Files 볼륨에서 Azure storage에 호스트 된 Azure NetApp Files 백업 저장소로 스냅숏 데이터를 전송 하는 방법을 보여 줍니다.

![Azure NetApp Files 볼륨에서 Azure NetApp Files 백업 저장소로 전송 되는 스냅숏 데이터를 보여 주는 다이어그램](../media/azure-netapp-files/snapshot-data-transfer-backup-storage.png)

이 간단한 예제에 표시 된 대로 백업에 대 한 더 긴 기록을 유지 하도록 Azure NetApp Files 백업 기능이 설계 되었습니다. 오른쪽의 백업 리포지토리에 보호 된 볼륨 및 왼쪽의 스냅숏 보다 더 오래 된 스냅숏이 포함 되어 있는지 확인 합니다. 

대부분의 사용 사례에서는 응용 프로그램 또는 사용자 오류로 인해 손실 된 데이터에 대 한 가장 일반적인 복구를 제공 하기 위해 상대적으로 짧은 시간 (일반적으로 몇 개월) 동안 Azure NetApp Files 볼륨에 온라인 스냅숏을 유지 해야 합니다. Azure NetApp Files 백업 기능은 비용 효율적인 Azure storage로 스냅숏을 전송 하 여 데이터 보호 기간을 1 년 이상으로 확장 하는 데 사용 됩니다. 다이어그램에서 파란색으로 표시 된 것 처럼 첫 번째 전송은 원본 Azure NetApp Files 볼륨 및 스냅숏에서 사용 되는 모든 데이터 블록을 복사 하는 기준선입니다. 연속 백업에서는 스냅숏 메커니즘을 사용 하 여 블록 증분 업데이트만으로 백업 리포지토리를 업데이트 합니다.

## <a name="ways-to-restore-data-from-snapshots"></a>스냅샷에서 데이터를 복원하는 방법  

Azure NetApp Files 스냅샷 기술은 백업의 빈도와 안정성을 크게 향상시킵니다. 성능 오버헤드가 최소화되며 활성 볼륨에서 안전하게 만들 수 있습니다. Azure NetApp Files 스냅샷은 거의 즉각적이고 안전하며 선택적으로 사용자 관리형 복원을 허용합니다. 이 섹션에서는 Azure NetApp Files 스냅샷에서 데이터에 액세스하거나 복원할 수 있는 다양한 방법에 대해 설명합니다.

### <a name="restoring-files-or-directories-from-online-snapshots"></a>온라인 스냅숏에서 파일이 나 디렉터리 복원

[스냅숏 경로 표시 유형이](snapshots-edit-hide-path.md) 로 설정 되지 않은 경우 `hidden` 스냅숏에 직접 액세스 하 여 실수로 삭제, 손상 또는 데이터 수정 작업을 복구할 수 있습니다. 파일 및 디렉터리의 보안은 스냅샷에 유지되고 스냅샷은 의도적으로 읽기 전용입니다. 따라서 복원은 안전하고 간단합니다. 스냅숏 경로 표시 유형이로 설정 된 경우 `hidden` 지원 티켓을 열어 백업 관리자 또는 시스템 관리자가 스냅숏에서 파일을 복원할 수 있습니다.

다음 다이어그램은 스냅샷에 대한 파일 또는 디렉터리 액세스를 보여 줍니다. 

![스냅샷에 대한 파일 또는 디렉터리 액세스를 보여 주는 다이어그램](../media/azure-netapp-files/snapshot-file-directory-access.png)

다이어그램에서 스냅샷 1은 활성 볼륨과 스냅샷이 만들어지는 순간 사이의 델타 블록만 사용합니다. 그러나 볼륨 스냅샷 경로를 통해 스냅샷에 액세스하면 데이터가 스냅샷이 만들어지는 당시의 전체 볼륨 용량인 것처럼 *나타납니다*. 스냅숏 폴더에 액세스 하 여 선택한 스냅숏에서 파일 및 디렉터리를 복사 하 여 데이터를 복원할 수 있습니다.

마찬가지로 대상 지역 간 복제 볼륨의 스냅샷은 DR 지역에서 데이터 복구를 위해 읽기 전용으로 액세스할 수 있습니다.  

다음 다이어그램은 지역 간 복제 시나리오에서 스냅샷 액세스를 보여 줍니다. 

![지역 간 복제의 스냅샷 액세스를 보여 주는 다이어그램](../media/azure-netapp-files/snapshot-access-cross-region-replication.png)

스냅샷에서 개별 파일 또는 디렉터리를 복원하는 방법은 [클라이언트를 사용하여 스냅샷에서 파일 복원](snapshots-restore-file-client.md)을 참조하세요.

### <a name="restoring-cloning-an-online-snapshot-to-a-new-volume"></a>온라인 스냅숏을 새 볼륨으로 복원 (복제)

별도의 독립 볼륨 (클론)으로 Azure NetApp Files 스냅숏을 복원할 수 있습니다. 볼륨 크기 및 사용되는 용량에 관계없이 이 작업은 거의 즉각적입니다. 실제 볼륨 및 스냅샷 데이터 블록을 복사하는 동안 새로 만든 볼륨은 거의 즉시 액세스할 수 있습니다. 볼륨 크기와 용량에 따라 이 프로세스는 상위 볼륨 및 스냅샷을 삭제할 수 없는 동안 상당한 시간이 소요될 수 있습니다. 그러나 백그라운드에서 복사 프로세스가 진행되는 동안 볼륨은 초기 생성 후 이미 액세스할 수 있습니다. 이 기능을 사용하면 테스트 및 개발을 위한 데이터 복구 또는 볼륨 복제를 위한 빠른 볼륨 만들기가 가능합니다. 데이터 복사 프로세스의 특성상 복원이 완료되면 스토리지 용량 풀 사용이 두 배가 되며 새 볼륨에 원래 스냅샷의 전체 활성 용량이 표시됩니다. 이 프로세스가 완료 되 면 볼륨이 독립적이 고 원래 볼륨에서 분리 되며 원본 볼륨 및 스냅숏은 새 볼륨과 독립적으로 관리 하거나 제거할 수 있습니다.

다음 다이어그램은 스냅샷을 복원(복제)하여 만든 새 볼륨을 보여 줍니다.   

![스냅샷을 복원하여 만든 새 볼륨을 보여 주는 다이어그램](../media/azure-netapp-files/snapshot-restore-clone-new-volume.png)

재해 복구 (DR) 볼륨에 복제 된 스냅숏에 대해 동일한 작업을 수행할 수 있습니다. 지역 간 복제가 활성 상태로 유지되거나 진행 중인 경우에도 모든 스냅샷을 새 볼륨으로 복원할 수 있습니다. 이 기능을 통해 DR 지역에서 테스트 및 개발 환경에 대 한 중단 없는 생성을 수행할 수 있습니다. 즉, 사용할 데이터를 저장 하는 반면 복제 된 볼륨은 DR 용도로만 사용 됩니다. 이 사용 사례를 통해 테스트 및 개발을 프로덕션에서 격리 하 여 프로덕션 환경에 대 한 잠재적 영향을 없앨 수 있습니다.  

다음 다이어그램에서는 지역 간 복제가 수행되는 동안 DR 대상 볼륨 스냅샷을 사용하여 볼륨 복원(복제)을 보여 줍니다.  

![DR 대상 볼륨 스냅샷을 사용하여 볼륨 복원을 보여 주는 다이어그램](../media/azure-netapp-files/snapshot-restore-clone-target-volume.png)

볼륨 복원 작업에 대해서는 [스냅샷을 새 볼륨으로 복원](snapshots-restore-new-volume.md)을 참조하세요.

### <a name="restoring-reverting-an-online-snapshot-in-place"></a>내부에서 온라인 스냅숏 복원 (되돌리기)

경우에 따라 새 볼륨에서 스토리지 용량을 사용하므로 스냅샷을 통해 새 볼륨을 만드는 것이 필요하지 않거나 적절하지 않을 수도 있습니다. 데이터 손상 으로부터 빠르게 복구 하기 위해 (예: 데이터베이스 손상 또는 랜 섬 웨어 공격) 볼륨 자체 내에서 스냅숏을 복원 하는 것이 더 적합할 수 있습니다. Azure NetApp Files [스냅숏 되돌리기](snapshots-revert-volume.md) 기능을 사용 하 여이 작업을 수행할 수 있습니다. 이 기능을 사용하면 볼륨을 특정 스냅샷을 만들 때의 상태로 신속하게 되돌릴 수 있습니다. 대부분의 경우 볼륨을 되돌리는 것이 스냅샷에서 활성 파일 시스템으로 개별 파일을 복원하는 것보다 훨씬 빠릅니다. 특히 다중 TiB 볼륨이 큰 경우에는 더욱 그렇습니다. 

볼륨 스냅샷 되돌리기는 거의 즉각적이며, 가장 큰 볼륨의 경우에도 완료하는 데 몇 초밖에 걸리지 않습니다. 활성 볼륨 메타데이터(*inode 테이블)는* 스냅샷이 만들어진 시점의 스냅샷 메타데이터로 대체되므로 볼륨을 특정 시점으로 롤백합니다. 되돌리기를 적용하기 위해 데이터 블록을 복사할 필요가 없습니다. 따라서 스냅숏을 새 볼륨으로 복원 하는 것 보다 더 효율적이 고 더 빠릅니다.

다음 다이어그램은 이전 스냅샷으로 되돌리는 볼륨을 보여 줍니다.  

![이전 스냅샷으로 되돌리는 볼륨을 보여 주는 다이어그램](../media/azure-netapp-files/snapshot-volume-revert.png)

> [!IMPORTANT]
> 작성 된 활성 파일 시스템 데이터와 선택한 스냅숏 이후에 만들어진 스냅숏이 손실 됩니다. 스냅숏 되돌리기 작업은 대상 볼륨의 모든 데이터를 선택한 스냅숏의 데이터로 바꿉니다. 스냅샷을 선택하는 경우 스냅샷 콘텐츠와 만든 날짜에 주의를 기울여야 합니다. 스냅샷 되돌리기 작업은 실행 취소할 수 없습니다.  

이 기능을 사용하는 방법은 [스냅샷 되돌리기를 사용하여 볼륨 되돌리기](snapshots-revert-volume.md)를 참조하세요.

### <a name="restoring-volume-backups-from-vaulted-snapshots"></a>Vaulted 스냅숏에서 볼륨 백업 복원

볼륨 수준 또는 NetApp 계정 수준에서 [백업을 검색할](backup-search.md) 수 있습니다. 스냅숏에 사용 되는 이름은 스냅숏이 백업 될 때 유지 되 고 "매일", "주별" 또는 "월별" 접두사를 포함 합니다. 또한 스냅숏 생성 시간 및 날짜의 타임 스탬프를 포함 합니다. 백업 기능을 사용 하는 경우 수행 되는 첫 번째 스냅숏을 기준 스냅숏 이라고 합니다. 기본 스냅숏은 보호 된 볼륨 및 스냅숏에 대 한 모든 데이터를 포함 합니다. 연속 vaulted 스냅숏은 블록 증분 업데이트이 고, 스냅숏은 항상 vaulted 스냅숏을 만들 때 볼륨의 완전 한 표현 이며 증분 업데이트를 사용 하 여 기준선을 쌓을 필요 *없이* 직접 복원할 수 있습니다. 

다음 다이어그램에서는 선택한 vaulted 스냅숏을 새 볼륨으로 복원 하는 작업을 보여 줍니다.  

![선택한 vaulted 스냅숏을 새 볼륨으로 복원 하는 것을 보여 주는 다이어그램](../media/azure-netapp-files/snapshot-restore-vaulted-new-volume.png)

### <a name="restoring-individual-files-or-directories-from-vaulted-snapshots"></a>Vaulted 스냅숏에서 개별 파일 또는 디렉터리 복원  

개별 파일이 나 디렉터리를 복원 하려면 전체 vaulted 스냅숏이 새 볼륨으로 복원 된 후 볼륨을 탑재 하 여 복원할 파일이 나 디렉터리를 찾아볼 수 있습니다. 복원 작업은 필요한 파일이 나 디렉터리를 새로 복원 된 볼륨에서 대상 위치로 복사 하 여 수행 됩니다. 복원이 완료 되 면 복원 된 볼륨을 삭제할 수 있습니다.  

볼륨을 삭제 하는 경우 볼륨의 일부 이며 볼륨을 삭제 하 고 삭제 되는 온라인 스냅숏과 달리 vaulted 스냅숏 (백업)은 계속 유지 됩니다. 응용 프로그램 또는 사용자 오류로 인해 부모 볼륨이 삭제 되거나 손실 된 경우에도 vaulted 백업에서 전체 볼륨 및 개별 디렉터리를 복원할 수 있습니다. 백업 목록에서 적절 한 vaulted 스냅숏을 선택 하 고 새 볼륨으로 복원 하 여이 작업을 수행할 수 있습니다. 자세한 내용은 [새 볼륨으로 백업 복원](backup-restore-new-volume.md) 을 참조 하세요.


## <a name="how-snapshots-are-deleted"></a>스냅샷을 삭제하는 방법  

이 섹션에서는 온라인 스냅숏 및 vaulted 스냅숏이 삭제 되는 방법에 대해 설명 합니다.

### <a name="deleting-online-snapshots"></a>온라인 스냅숏 삭제 

스냅숏은 저장소 용량을 사용 합니다. 따라서 일반적으로 무기한 유지되지 않습니다. 데이터 보호, 보존 및 복구 가능성을 위해 RPO, RTO 및 보존 SLA 요구 사항에 따라 여러 스냅샷(다양한 시점에 생성됨)이 일반적으로 특정 기간 동안 온라인 상태로 유지됩니다. 그러나 오래된 스냅샷은 스토리지 서비스에 보관하지 않아도 되고 공간을 확보하기 위해 삭제해야 할 수 있습니다. 언제든지 관리자가 모든 스냅샷을 삭제할 수 있습니다(반드시 생성 순서는 아님). 

> [!IMPORTANT]
> 스냅샷 삭제 작업은 실행 취소할 수 없습니다. 데이터 보호 및 보존을 위해 볼륨의 오프 라인 복사본 (vaulted 스냅숏)을 보존 해야 합니다. 

스냅샷이 삭제되면 해당 스냅샷과 기존 데이터 블록 사이의 모든 포인터가 제거됩니다. 데이터 블록에이를 가리키는 포인터 (활성 볼륨 또는 볼륨의 다른 스냅숏)가 더 이상 없는 경우에만 나중에 사용할 수 있도록 데이터 블록이 볼륨의 사용 가능한 공간으로 반환 됩니다. 따라서 스냅샷을 제거하면 이전에 만든 스냅샷에서 데이터 블록이 종종 캡처되므로 활성 볼륨에서 데이터를 삭제하는 경우보다 볼륨의 사용 가능한 용량이 더 늘어납니다. 

다음 다이어그램은 볼륨에서 스냅샷 3을 삭제하는 것이 스토리지 사용량에 미치는 영향을 보여 줍니다.  

![스냅샷 삭제가 스토리지 소비에 미치는 영향을 보여 주는 다이어그램](../media/azure-netapp-files/snapshot-delete-storage-consumption.png)

[볼륨 및 스냅샷 소비를 모니터링](azure-netapp-files-metrics.md#volumes)하고 애플리케이션, 활성 볼륨 및 스냅샷 소비가 상호 작용하는 방식을 이해해야 합니다. 

스냅샷 삭제를 관리하는 방법은 [스냅샷 삭제](snapshots-delete.md)를 참조하세요. 이 프로세스를 자동화하는 방법은 [스냅샷 정책 관리](snapshots-manage-policy.md)를 참조하세요.

### <a name="deleting-vaulted-snapshots"></a>Vaulted 스냅숏 삭제

볼륨에 대 한 백업을 사용 하지 않도록 설정 하면 해당 볼륨의 Azure storage에 저장 된 모든 vaulted 스냅숏 (백업)이 삭제 됩니다.

볼륨을 삭제 하기 전에 볼륨을 삭제 했지만 백업 정책을 사용 하지 않도록 설정한 경우에는 볼륨과 관련 된 모든 백업이 Azure storage에 유지 되 고 연결 된 NetApp 계정 아래에 나열 됩니다.

자세한 내용은 [Azure NetApp Files 볼륨에 대 한 백업 기능 사용 안 함](backup-disable.md) 을 참조 하세요. 

Vaulted 스냅숏 기록은 Vaulted snapshot (backup) 스케줄러에 의해 새 스냅숏이 추가 될 때 가장 오래 된 스냅숏이 삭제 되는 적용 된 스냅숏 정책에 의해 자동으로 관리 됩니다. Vaulted 스냅숏을 수동으로 제거할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

* [NetApp Azure Files를 사용하여 스냅샷 관리](azure-netapp-files-manage-snapshots.md)
* [볼륨 및 스냅샷 메트릭 모니터링](azure-netapp-files-metrics.md#volumes)
* [스냅샷 정책 문제 해결](troubleshoot-snapshot-policies.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [Azure NetApp Files 스냅샷 101 동영상](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Azure NetApp Files 스냅샷 개요](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)
* [Azure NetApp Files 백업 이해](backup-introduction.md)
* [정책 기반 백업 구성](backup-configure-policy-based.md)
* [수동 백업 구성](backup-configure-manual.md)
* [백업 정책 관리](backup-manage-policies.md)
* [백업 검색](backup-search.md)
* [새 볼륨으로 백업 복원](backup-restore-new-volume.md)
* [볼륨에 대 한 백업 기능 사용 안 함](backup-disable.md)
* [볼륨의 백업 삭제](backup-delete.md)

