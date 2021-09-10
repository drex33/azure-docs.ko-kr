---
title: Azure(큰 인스턴스)의 SAP HANA에 대한 재해 복구 원칙 및 준비 | Microsoft Docs
description: Azure(Large Instances)의 SAP HANA에 대한 재해 복구 원칙 및 준비를 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: gwallace
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 7/01/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 79991bcdd5174d2655f3c917d0898b0b87b0ca50
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113217074"
---
# <a name="disaster-recovery-principles-and-preparation"></a>재해 복구 원칙 및 준비

이 문서에서는 HANA Large Instances(BareMetal 인프라라고도 함)에 대한 중요한 재해 복구(DR) 원칙을 설명합니다. 재해 복구를 준비하는 데 필요한 단계를 알아봅니다. 또한 재해에서 복구 시간 목표(RTO) 및 복구 지점 목표(RPO)를 달성하는 방법도 확인할 수 있습니다. 

## <a name="dr-principles-for-hana-large-instances"></a>HANA Large Instances에 대한 DR 원칙

HANA Large Instance는 서로 다른 Azure 지역의 HANA Large Instance 스탬프 간에 재해 복구 기능을 제공합니다. 예를 들어, Azure의 미국 서부 지역에서 HANA Large Instance를 배포하는 경우를 가정해 보겠습니다. 그리고 미국 동부 지역에 있는 HANA Large Instance를 재해 복구 단위로 사용할 수 있습니다. 재해 복구는 자동으로 구성되지 않습니다. DR 지역의 다른 HANA Large Instance에 대한 비용이 부과되기 때문입니다. 재해 복구 설치는 강화는 물론 스케일 아웃 설치가 둘 다 가능합니다. 

대부분의 고객이 설치된 HANA 인스턴스를 사용하는 비프로덕션 시스템을 실행하기 위해 DR 지역의 단위를 사용했습니다. 이 HANA Large Instance는 프로덕션 용도로 사용되는 SKU와 동일한 SKU여야 합니다. 다음 이미지에서는 Azure 프로덕션 지역의 서버 단위와 재해 복구 지역 간의 디스크 구성을 보여줍니다.

![디스크 관점의 DR 설치 구성](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

이 개요 그래픽에서 볼 수 있듯이 두 번째 디스크 볼륨 세트를 주문해야 합니다. DR 사이트의 HANA Large Instance 서버와 연결된 대상 디스크 볼륨의 크기는 프로덕션 볼륨의 크기와 동일합니다. 

다음 볼륨은 프로덕션 지역에서 DR 사이트로 복제됩니다.

- /hana/data
- /hana/logbackups 
- /hana/shared (/usr/sap 포함)

/Hana/log 볼륨이 복제되지 않습니다. 볼륨에서 복원할 때 SAP HANA 트랜잭션 로그가 필요하지 않습니다.

## <a name="hana-large-instance-storage-replication"></a>HANA Large Instance 스토리지 복제 

HANA Large Instance 인프라의 DR 기능을 기반으로 스토리지를 복제할 수 있습니다. 스토리지 측에서 사용되는 기능은 스토리지 볼륨에 변경 사항이 발생할 때 비동기 방식으로 복제되는 지속적인 변경 스트리밍이 아닙니다. 대신, 정기적으로 생성되는 볼륨의 스냅샷에 의존하는 메커니즘입니다. 이미 복제된 스냅샷과 아직 복제되지 않은 새 스냅샷 간의 델타는 DR 사이트를 거쳐 대상 디스크 볼륨으로 전송됩니다. 이러한 스냅숏은 볼륨에 저장됩니다. 재해 복구 장애 조치가 있는 경우, 해당 볼륨에서 복원되어야 합니다.  

볼륨의 전체 데이터를 처음 전송하는 것은 데이터 양이 스냅샷 간의 델타보다 작아지기 전이어야 합니다. DR 사이트의 볼륨에는 프로덕션 사이트에서 수행되는 모든 볼륨 스냅샷이 포함됩니다. 결과적으로 프로덕션 시스템으로 롤백하지 않고도 손실된 데이터를 복구하기 위해 해당 DR 시스템을 사용하여 이전 상태로 돌아갈 수 있습니다.

하나의 HANA Large Instance에 여러 개의 독립된 SAP HANA 인스턴스가 있는 MCOD 배포가 있는 경우, 모든 SAP HANA 인스턴스에서 스토리지가 DR 쪽으로 복제되어야 합니다.

프로덕션 사이트에서 HANA 시스템 복제를 고가용성으로 사용하고 DR 사이트에 스토리지 기반 복제를 사용하는 경우, 기본 사이트에서 DR 인스턴스로 두 노드의 볼륨이 모두 복제됩니다. 기본 및 보조 노드에서 DR로의 복제를 수용하려면, DR 사이트에서 추가 스토리지(기본 노드와 동일한 크기)를 구매합니다. 

>[!NOTE]
>HANA Large Instance 스토리지 복제 기능은 스토리지 스냅샷을 미러링하고 복제합니다. [백업 및 복원](hana-backup-restore.md)에 설명된 대로 스토리지 스냅숏을 사용하지 않는 경우, DR 사이트에 복제할 수 없습니다. 재해 복구 사이트로 스토리지를 복제하려면 스토리지 스냅샷을 반드시 실행해야 합니다.

## <a name="preparation-of-the-disaster-recovery-scenario"></a>재해 복구 시나리오 준비
이 DR 시나리오에서 프로덕션 Azure 지역의 HANA Large Instance에서 프로덕션 시스템을 실행하고 있습니다. 다음 단계에 대해서는 해당 HANA 시스템의 SID가 "PRD"라고 가정합니다. 또한 DR Azure 지역 내의 HANA Large Instance에서 비프로덕션 시스템이 실행되고 있습니다. SID는 "TST"입니다. 다음 이미지에서는 이 구성을 보여줍니다.

![DR 설치 시작](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

서버 인스턴스가 아직 추가 스토리지 볼륨 세트를 사용하여 정렬되지 않은 것으로 가정합니다. 그런 다음 Azure 서비스 관리에서 SAP HANA 추가된 볼륨을 연결합니다. TST HANA 인스턴스를 실행하는 HANA Large 인스턴스에 대한 프로덕션 복제본의 대상입니다. 프로덕션 HANA 인스턴스의 SID를 제공해야 합니다. Azure Service Management의 SAP HANA가 해당 볼륨의 연결을 확인하면 해당 볼륨을 HANA Large Instance에 탑재해야 합니다.

![DR 설치 다음 단계](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

다음 단계는 TST HANA 인스턴스를 실행하는 DR Azure 지역의 HANA Large Instance에 두 번째 SAP HANA 인스턴스를 설치하는 것입니다. 새로 설치된 SAP HANA 인스턴스는 SID가 동일해야 합니다. 생성된 사용자는 프로덕션 인스턴스와 UID 및 그룹 ID가 동일해야 합니다. 자세한 내용은 [백업 및 복원](hana-backup-restore.md)을 참조하세요. 설치가 성공하면 다음을 수행해야 합니다.

- [백업 및 복원](hana-backup-restore.md)에 설명된 스토리지 스냅샷 준비의 2단계를 실행합니다.
- 이 단계를 실행하지 않은 경우, HANA Large Instance의 DR 단위에 대한 공개 키를 생성합니다. [백업 및 복원](hana-backup-restore.md)에 설명된 스토리지 스냅샷 준비의 3단계를 참조하세요.
- 새 HANA 인스턴스를 사용하여 *HANABackupCustomerDetails.txt* 를 유지 관리하고 스토리지에 대한 연결이 올바르게 작동하는지 테스트합니다.  
- DR Azure 지역의 HANA Large Instance에 새로 설치된 SAP HANA 인스턴스를 중지합니다.
- 이 PRD 볼륨을 분리하고 Azure Service Management의 SAP HANA에 문의하십시오. 스토리지 복제 대상으로 작동하는 동안은 액세스할 수 없으므로 볼륨을 단위에 탑재된 상태로 유지할 수 없습니다.  

![다이어그램은 프로덕션 Azure 지역의 PRD 볼륨과 DR Azure 지역의 PRD 볼륨 간에 복제 관계를 보여 줍니다.](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

운영 팀에서는 프로덕션 지역의 PRD 볼륨과 DR 지역의 PRD 볼륨 간에 복제 관계를 설정합니다.

>[!IMPORTANT]
>재해 복구 사이트에서 복제된 SAP HANA 데이터베이스를 일관된 상태로 복원할 필요가 없으므로 /hana/log 볼륨은 복제되지 않습니다.

재해가 발생 한 경우, RTO 및 RPO를 달성할 수 있도록 스토리지 스냅숏 백업 일정을 설정합니다. RPO를 최소화하려면 HANA Large Instance 서비스에서 다음 복제 간격을 설정합니다.
- 결합된 스냅샷(스냅샷 형식 **hana**)을 적용하는 볼륨의 경우 15분마다 재해 복구 사이트의 동일한 스토리지 볼륨 대상에 복제되도록 설정합니다.
- 트랜잭션 로그 백업 볼륨(스냅샷 형식 **logs**)의 경우 3분마다 재해 복구 사이트의 동일한 스토리지 볼륨 대상에 복제되도록 설정합니다.

RPO를 최소화하려면 다음을 수행합니다.
- **hana** 형식의 스토리지 스냅샷을 30분~1시간마다 수행합니다. 자세한 정보는 [Azure Application Consistent Snapshot Tool을 사용한 백업](../../../azure-netapp-files/azacsnap-cmd-ref-backup.md)을 참조하세요.
- SAP HANA 트랜잭션 로그 백업을 5분마다 수행합니다.
- **logs** 형식의 스토리지 스냅샷을 5~15분마다 수행합니다. 이 간격 주기로 약 15-25분의 RPO를 설정합니다.

이 설정을 사용하면 트랜잭션 로그 백업의 시퀀스, 스토리지 스냅샷 및 HANA 트랜잭션 로그 백업 볼륨 복제와 /hana/data, /hana/shared(/usr/sap 포함)가 다음 그래픽의 데이터와 같이 표시될 수 있습니다.

 ![시간 축에서 트랜잭션 로그 백업 스냅샷과 스냅 미러 간 관계](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

재해 복구 사례에서 RPO를 향상시키려면 HANA 트랜잭션 로그 백업을 Azure의 SAP HANA(대규모 인스턴스)에서 다른 Azure 지역에 복사할 수 있습니다. RPO를 더 감소하기 위해서는 다음과 같은 단계를 수행합니다.

1. HANA 트랜잭션 로그를 가능한 한 자주 /hana/logbackups에 백업합니다.
1. rsync를 사용하여 트랜잭션 로그 백업을 NFS 공유 호스팅 Azure 가상 머신에 복사합니다. 가상 머신(VM)은 Azure 프로덕션 지역과 DR 지역의 Azure 가상 네트워크에 있습니다. 프로덕션 HANA Large Instance를 Azure에 연결하는 회로에 두 Azure 가상 네트워크를 연결합니다. 자세한 사항은 [HANA Large Instance를 사용한 재해 복구의 네트워크 고려 사항](hana-overview-high-availability-disaster-recovery.md#network-considerations-for-disaster-recovery-with-hana-large-instances)을 참조하세요. 
1. 해당 지역의 트랜잭션 로그 백업을 VM이 연결된 NFS에서 내보낸 스토리지에 유지합니다.
1. 재해 장애 조치의 경우, /hana/logbackups 볼륨에서 찾은 트랜잭션 로그 백업을 최근에 DR 사이트의 NFS 공유에 생성한 트랜잭션 로그 백업으로 보완합니다. 
1. 트랜잭션 로그 백업을 시작하여 DR 영역에 저장할 수 있는 최신 백업으로 복원합니다.

HANA Large Instance 작업에서 복제 관계 설정을 확인하고 실행 스토리지 스냅샷 백업을 시작하면 데이터 복제가 시작됩니다.

![복제를 설정하기 전 DR 설치 단계](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

복제가 진행되면서 DR Azure 지역의 PRD 볼륨에 있는 스냅샷이 복원되지 않습니다. 스냅숏은 저장됩니다. 볼륨이 이러한 상태로 탑재되어 있다면 DR Azure 지역의 서버에 PRD SAP HANA 인스턴스를 설치한 후에 해당 볼륨을 분리한 상태를 나타냅니다. 또한 아직 복원되지 않은 스토리지 백업을 나타냅니다.

장애 조치가 있는 경우 최신 스토리지 스냅샷 대신 이전 스토리지 스냅샷으로 복원하도록 선택할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

재해 복구의 장애 조치 프로세스에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [재해 복구 장애 조치(failover) 프로시저](hana-failover-procedure.md)
