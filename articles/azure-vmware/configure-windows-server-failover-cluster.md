---
title: Azure VMware Solution vSAN에서 Windows Server 장애 조치(Failover) 클러스터 구성
description: 네이티브 공유 디스크를 사용하여 Azure VMware Solution vSAN에서 WSFC(Windows Server 장애 조치(Failover) 클러스터)를 구성하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 05/04/2021
ms.openlocfilehash: fcde65b98b3774ee1ef9b15bfa6da3836aaa8a1b
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538674"
---
# <a name="configure-windows-server-failover-cluster-on-azure-vmware-solution-vsan"></a>Azure VMware Solution vSAN에서 Windows Server 장애 조치(Failover) 클러스터 구성

이 문서에서는 네이티브 공유 디스크를 사용하여 Azure VMware Solution vSAN의 [Windows Server에서 장애 조치(failover) 클러스터링](/windows-server/failover-clustering/failover-clustering-overview)을 구성하는 방법에 대해 알아봅니다. 

>[!IMPORTANT]
>이 문서의 구현은 개념 증명 및 파일럿 목적을 위한 것입니다. 배치 정책을 사용할 수 있을 때까지 CIB(Cluster-in-a-Box) 구성을 사용하는 것이 좋습니다.

이전에 MSCS(Microsoft Service Cluster Service)로 알려진 Windows Server 장애 조치(Failover) 클러스터는 Windows Server OS(운영 체제) 기능입니다. WSFC는 업무상 중요한 기능이며 많은 애플리케이션에 필요합니다. 예를 들어 WSFC는 다음과 같은 구성에 필요 합니다.

- 다음과 같이 구성된 SQL 서버:
  - 인스턴스 수준 고가용성을 위한 Always On FCI(장애 조치(Failover) 클러스터 인스턴스).
  - 데이터베이스 수준 고가용성을 위한 Always On AG(가용성 그룹).
- Windows 파일 서비스:
  - 활성 클러스터 노드에서 실행되는 일반 파일 공유.
  - CSV(클러스터 공유 볼륨)에 파일을 저장하는 SOFS(스케일 아웃 파일 서버).
  - S2D(스토리지 공간 다이렉트); 여러 클러스터 노드에서 스토리지 풀을 만드는 데 사용되는 로컬 디스크입니다.

WSFC 클러스터는 다양한 Azure VMware Solution 인스턴스(CAB(Cluster-Across-Box)라고도 함)에서 호스트할 수 있습니다. WSFC 클러스터를 단일 Azure VMware Solution 노드에 배치할 수도 있습니다. 이 구성을 CIB(Cluster-in-a-Box)라고 합니다. 프로덕션 구현에는 CIB 솔루션을 사용하지 않는 것이 좋습니다. 단일 Azure VMware Solution 노드가 실패하고 모든 WSFC 클러스터 노드의 전원이 꺼지거나 애플리케이션에서 가동 중지 시간이 발생합니다. Azure VMware Solution에는 프라이빗 클라우드 클러스터에서 최소 3개의 노드가 필요합니다.

지원되는 WSFC 구성을 배포하는 것이 중요합니다. Azure VMware Solution을 사용하여 vSphere에서 솔루션을 지원받고자 합니다. VMware는 vSphere 6.7, [장애 조치(Failover) 클러스터링 및 Microsoft 클러스터 서비스를 위한 설치](https://docs.vmware.com/en/VMware-vSphere/6.7/vsphere-esxi-vcenter-server-67-setup-mscs.pdf)에서 WSFC에 대한 자세한 문서를 제공합니다.

이 문서에서는 Windows Server 2016 및 Windows Server 2019의 WSFC에 초점을 맞춥니다. 안타깝게도 이전 Windows Server 버전은 [기본 지원을 제공](https://support.microsoft.com/lifecycle/search?alpha=windows%20server)하지 않으므로 여기서 고려하지 않습니다.

먼저 [WSFC를 만들어야 합니다](/windows-server/failover-clustering/create-failover-cluster). 그런 다음 이 문서에서 제공하는 정보를 사용하여 Azure VMware Solution의 WSFC 배포를 지정합니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure VMware Solution 환경
- Microsoft Windows Server OS 설치 미디어

## <a name="reference-architecture"></a>참조 아키텍처

Azure VMware Solution은 가상화된 WSFC에 대한 기본 지원을 제공합니다. 가상 디스크 수준에서 SCSI3PR(SCSI-3 영구 예약)을 지원합니다. WSFC는 노드 간 공유 디스크에 대한 액세스를 중재하기 위해 이 지원이 필요합니다. SCSI3PR 지원을 통해 vSAN 데이터 저장소에 있는 VM 간에 디스크 리소스를 공유하는 WSFC를 구성할 수 있습니다.

다음 다이어그램은 Azure VMware Solution 프라이빗 클라우드에서 WSFC 가상 노드의 아키텍처를 보여 줍니다. 광범위한 Azure 플랫폼과 관련하여 WSFC 가상 서버(빨간색 상자)를 비롯하여 Azure VMware Solution이 상주하는 위치를 보여 줍니다. 이 다이어그램은 일반적인 허브-스포크 아키텍처를 보여 주지만 Azure Virtual WAN을 사용하여 유사한 설정이 가능합니다. 둘 다 다른 Azure 서비스에서 가져올 수 있는 모든 값을 제공합니다.

:::image type="content" source="media/windows-server-failover-cluster/windows-server-failover-architecture.svg" alt-text="Azure VMware Solution 프라이빗 클라우드에 있는 Windows Server 장애 조치(Failover) 클러스터 가상 노드의 다이어그램." border="false" lightbox="media/windows-server-failover-cluster/windows-server-failover-architecture.svg":::

## <a name="supported-configurations"></a>지원되는 구성

현재 지원되는 구성은 다음과 같습니다.

- Microsoft Windows Server 2012 이상
- 클러스터당 최대 5개의 장애 조치(failover) 클러스터링 노드
- VM당 최대 4개의 PVSCSI 어댑터
- PVSCSI 어댑터당 최대 64개 디스크

## <a name="virtual-machine-configuration-requirements"></a>가상 머신 구성 요구 사항

### <a name="wsfc-node-configuration-parameters"></a>WSFC 노드 구성 매개 변수

- 각 WSFC 노드에서 최신 VMware 도구를 설치합니다.
- 단일 가상 SCSI 어댑터에서 비공유 및 공유 디스크를 혼합하는 것은 지원되지 않습니다. 예를 들어 시스템 디스크(드라이브 C:)가 SCSI0:0에 연결된 경우 첫 번째 공유 디스크는 SCSI1:0에 연결됩니다. WSFC의 VM 노드는 일반 VM과 최대 4개의 가상 SCSI 컨트롤러와 동일한 가상 SCSI 컨트롤러를 가집니다.
- 가상 디스크 SCSI ID는 동일한 WSFC의 노드를 호스트하는 모든 VM 간에 일치해야 합니다.

| **구성 요소** | **요구 사항** |
| --- | --- |
| VM 하드웨어 버전 | 11 이상에서 라이브 vMotion을 지원합니다. |
| 가상 NIC | VMXNET3 반가상화된 NIC(네트워크 인터페이스 카드); 가상 NIC에서 게스트 내 Windows RSS(수신측 배율)를 사용하도록 설정합니다. |
| 메모리 | WSFC 클러스터의 노드에 대해 전체 VM 예약 메모리를 사용합니다. |
| 각 WSFC 노드의 I/O 시간 제한을 늘립니다. | HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Services\Disk\TimeOutValueSet을 60초 이상으로 수정합니다. (클러스터를 다시 만들 경우 이 값이 기본값으로 다시 설정될 수 있으므로 다시 변경해야 합니다.) |
| Windows 클러스터 상태 모니터링 | 최소 10개의 하트비트를 허용하도록 Windows 클러스터 상태 모니터링의 SameSubnetThreshold 매개 변수 값을 수정해야 합니다. 이는 [Windows Server 2016의 기본값](https://techcommunity.microsoft.com/t5/failover-clustering/tuning-failover-cluster-network-thresholds/ba-p/371834)입니다. 이 권장 사항은 공유 및 비공유 디스크를 포함하여 WSFC를 사용하는 모든 애플리케이션에 적용됩니다. |

### <a name="wsfc-node---boot-disks-configuration-parameters"></a>WSFC 노드 - 부팅 디스크 구성 매개 변수


| **구성 요소** | **요구 사항** |
| --- | --- |
| SCSI 컨트롤러 형식 | LSI Logic SAS |
| 디스크 모드 | 가상 |
| SCSI 버스 공유 | None |
| 부팅 디바이스를 호스트하는 가상 SCSI 컨트롤러에 대한 고급 설정을 수정합니다. | 각 WSFC 노드에 다음과 같은 고급 설정을 추가합니다.<br /> scsiX.returnNoConnectDuringAPD = “TRUE”<br />scsiX.returnBusyOnNoConnectStatus = "FALSE"<br />여기서 X는 부팅 디바이스 SCSI 버스 컨트롤러 ID 번호입니다. 기본적으로 X는 0으로 설정됩니다. |

### <a name="wsfc-node---shared-disks-configuration-parameters"></a>WSFC 노드 - 공유 디스크 구성 매개 변수


| **구성 요소** | **요구 사항** |
| --- | --- |
| SCSI 컨트롤러 형식 | VMware Paravirtualize(PVSCSI) |
| 디스크 모드 | 독립 - 영구적(아래 그림의 2단계). 이 설정을 사용하여 모든 디스크가 스냅샷에서 제외되도록 합니다. WSFC 기반 VM에 대해서는 스냅샷이 지원되지 않습니다. |
| SCSI 버스 공유 | 물리적(아래 그림의 1단계) |
| 다중 기록기 플래그 | 사용되지 않음 |
| 디스크 형식 | Thick 프로비전됩니다. (vSAN에는 EZT(Eager Zeroed Thick)가 필요하지 않습니다.) |

:::image type="content" source="media/windows-server-failover-cluster/edit-settings-virtual-hardware.png" alt-text="가상 하드웨어의 설정 편집 페이지를 보여 주는 스크린샷.":::

## <a name="non-supported-scenarios"></a>지원되지 않는 시나리오

다음 기능은 Azure VMware Solution의 WSFC에 대해 지원되지 않습니다.

- NFS 데이터 저장소
- 스토리지 공간
- iSCSI 서비스를 사용하는 vSAN
- vSAN 스트레치 클러스터
- EVC(Enhanced vMotion Compatibility)
- vSphere FT(내결함성)
- 스냅샷
- 라이브(온라인) 스토리지 vMotion
- NPIV(N-포트 ID 가상화)

가상 머신 하드웨어를 즉시 변경하면 WSFC 노드 간 하트비트가 중단될 수 있습니다.

다음 작업은 지원되지 않으며 WSFC 노드 장애 조치(failover)가 발생할 수 있습니다.

- 메모리 핫 추가
- CPU 핫 추가
- 스냅샷 사용
- 공유 디스크의 크기 증가
- 가상 머신 상태 일시 중지 및 다시 시작
- ESXi 스와핑 또는 VM 메모리 벌루닝(ballooning)으로 이어지는 메모리 오버 커밋
- 로컬 VMDK 파일 핫 확장(SCSI 버스 공유 컨트롤러와 연결되지 않은 경우에도)

## <a name="configure-wsfc-with-shared-disks-on-azure-vmware-solution-vsan"></a>Azure VMware Solution vSAN에서 공유 디스크를 통한 WSFC 구성

1. Active Directory 환경을 사용할 수 있는지 확인합니다.
2. vSAN 데이터 저장소에서 VM(가상 머신)을 만듭니다.
3. 모든 VM의 전원을 켜고, 호스트 이름 및 IP 주소를 구성하고, 모든 VM을 Active Directory 도메인에 가입하고, 사용 가능한 최신 OS 업데이트를 설치합니다.
4. 최신 VMware 도구를 설치합니다.
5. 각 VM에서 Windows Server 장애 조치(Failover) 클러스터 기능을 사용하도록 설정하고 구성합니다.
6. 쿼럼에 대한 클러스터 감시를 구성합니다. 파일 공유 감시는 정상적으로 작동합니다.
7. WSFC 클러스터의 모든 노드 전원을 끕니다.
8. WSFC의 각 VM 부분에 하나 이상의 Paravirtual SCSI 컨트롤러(최대 4개)를 추가합니다. 이전 단락의 설정을 사용합니다.
9. 첫 번째 클러스터 노드에서 **새 디바이스 추가** > **하드 디스크** 를 사용하여 필요한 모든 공유 디스크를 추가합니다. 디스크 공유는 **지정되지 않음**(기본값)으로 유지하고 디스크 모드는 **독립적 - 영구적** 으로 유지합니다. 그런 다음 이전 단계에서 만든 컨트롤러에 연결합니다.
10. 나머지 WSFC 노드로 계속 진행합니다. **새 디바이스 추가** > **기존 하드 디스크** 를 선택하여 이전 단계에서 만든 디스크를 추가합니다. 모든 WSFC 노드에서 동일한 디스크 SCSI ID를 유지 관리해야 합니다.
11. 첫 번째 WSFC 노드의 전원을 켜고 로그인하여 디스크 관리 콘솔(mmc)을 엽니다. 추가된 공유 디스크를 OS에서 관리할 수 있고 초기화되었는지 확인합니다. 디스크를 포맷하고 드라이브 문자를 할당합니다.
12. 다른 WSFC 노드의 전원을 켭니다.
13. **디스크 추가 마법사** 를 사용하여 WSFC 클러스터에 디스크를 추가하고 클러스터 공유 볼륨에 추가합니다.
14. **디스크 이동 마법사** 를 사용하여 장애 조치(failover)를 테스트하고 공유 디스크가 있는 WSFC 클러스터가 제대로 작동하는지 확인합니다.
15. **클러스터 유효성 검사 마법사** 를 실행하여 클러스터 및 해당 노드가 제대로 작동하는지 확인합니다.

    클러스터 유효성 검사 테스트에서 다음과 같은 특정 항목을 염두에 두어야 합니다.

       - **스토리지 공간 영구 예약의 유효성을 검사** 합니다. 클러스터에서 스토리지 공간을 사용하지 않는 경우(예: Azure VMware Solution vSAN) 이 테스트는 적용되지 않습니다. 이 경고를 포함하여 스토리지 공간 영구 예약 테스트 유효성 검사의 결과를 무시할 수 있습니다. 경고를 방지하기 위해 이 테스트를 제외할 수 있습니다.
        
      - **네트워크 통신의 유효성을 검사** 합니다. 클러스터 유효성 검사 테스트에서는 클러스터 노드당 하나의 네트워크 인터페이스만 사용할 수 있다는 경고를 표시합니다. 이 경고는 무시해도 됩니다. 노드가 NSX-T 세그먼트 중 하나에 연결되어 있기 때문에 Azure VMware Solution은 필수 가용성과 필요한 성능을 제공합니다. 단, 이 항목을 클러스터 유효성 검사 테스트의 일부로 유지하면 네트워크 통신의 다른 측면에 대한 유효성을 검사할 수 있습니다.

16. WSFC VM을 동일한 Azure VMware Solution 노드에 배치하는 DRS 규칙을 만듭니다. 이렇게 하려면 호스트-VM 선호도 규칙이 필요합니다. 이러한 방식으로 클러스터 노드는 동일한 Azure VMware Solution 호스트에서 실행됩니다. 다시 말해서 이는 배치 정책을 사용할 수 있을 때까지 파일럿 용도로 사용됩니다.

    >[!NOTE]
    > 이를 위해서는 지원 요청 티켓을 만들어야 합니다. Azure 지원 조직에서 이에 관한 도움을 드릴 수 있습니다.

## <a name="related-information"></a>관련 정보

- [Windows Server에서의 장애 조치(failover) 클러스터링](/windows-server/failover-clustering/failover-clustering-overview)
- [vSphere의 Microsoft 클러스터링에 대한 지침(1037959)(vmware.com)](https://kb.vmware.com/s/article/1037959)
- [장애 조치(Failover) 클러스터링 및 Microsoft 클러스터 서비스에 대한 설치 정보(vmware.com)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.mscs.doc/GUID-1A2476C0-CA66-4B80-B6F9-8421B6983808.html)
- [vSAN 6.7 U3 - WSFC 공유 디스크 &amp; SCSI-3 영구 예약(vmware.com)](https://blogs.vmware.com/virtualblocks/2019/08/23/vsan67-u3-wsfc-shared-disksupport/)
- [Azure VMware Solution 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-vmware-solution-limits)

## <a name="next-steps"></a>다음 단계

이제 Azure VMware Solution의 WSFC 설정에 대해 살펴보았으므로 다음에 대해 알아볼 수 있습니다.

- WSFC 기능이 필요한 애플리케이션을 더 추가하여 새 WSFC를 설정합니다. 예를 들어 SQL Server 및 SAP ASCS가 있습니다.
- 백업 솔루션 설정.
  - [Azure VMware Solution에 대한 Azure Backup Server 설정](set-up-backup-server-for-azure-vmware-solution.md)
  - [Azure VMware Solution 가상 머신용 백업 솔루션](backup-azure-vmware-solution-virtual-machines.md)
