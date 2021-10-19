---
title: FCI에 대한 가상 머신 준비
description: Azure 가상 머신에서 FCI(장애 조치(failover) 클러스터 인스턴스) 및 SQL Server와 함께 사용하도록 Azure 가상 머신을 준비합니다.
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: ddea24c95e6a9b1303d504ea4fda2b9a3ee0cd03
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130163935"
---
# <a name="prepare-virtual-machines-for-an-fci-sql-server-on-azure-vms"></a>FCI용 가상 머신 준비(Azure VM의 SQL Server)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 SQL Server FCI(장애 조치(failover) 클러스터 인스턴스)와 함께 사용하도록 Azure VM(가상 머신)을 준비하는 방법을 설명합니다. 구성 설정은 FCI 스토리지 솔루션에 따라 다르므로 환경과 비즈니스에 적합한 올바른 구성을 선택하고 있는지 확인합니다. 

자세한 내용은 [Azure VM에서 SQL Server를 사용하는 FCI](failover-cluster-instance-overview.md) 및 [클러스터 모범 사례](hadr-cluster-best-practices.md)의 개요를 참조하세요. 

> [!NOTE]
> Azure Migrate를 사용하여 Azure VM의 SQL Server에 대한 장애 조치(failover) 클러스터 인스턴스 솔루션을 리프트 앤 시프트할 수 있습니다. 자세한 내용은 [장애 조치(failover) 클러스터 인스턴스 마이그레이션](../../migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md)을 참조하세요. 

## <a name="prerequisites"></a>사전 요구 사항 

- Microsoft Azure 구독. [무료](https://azure.microsoft.com/free/)로 시작할 수 있습니다. 
- Azure 가상 머신의 Windows 도메인 또는 가상 네트워크 페어링을 통해 Azure로 확장된 온-프레미스 데이터 센터
- Azure 가상 머신과 Active Directory에서 개체를 만들 수 있는 권한이 있는 계정
- 다음 구성 요소를 위해 충분한 IP 주소 공간이 있는 Azure 가상 네트워크 및 서브넷:
   - 두 가상 머신
   - Windows 장애 조치(failover) 클러스터의 IP 주소
   - 각 FCI에 대한 IP 주소
- 도메인 컨트롤러를 가리키는 Azure 네트워크에 구성된 DNS

## <a name="choose-an-fci-storage-option"></a>FCI 스토리지 옵션 선택

가상 머신의 구성 설정은 SQL Server 장애 조치(failover) 클러스터 인스턴스에 사용할 스토리지 옵션에 따라 다릅니다. 가상 머신을 준비하기 전에 [사용 가능한 FCI 스토리지 옵션](failover-cluster-instance-overview.md#storage)을 검토하고 환경 및 비즈니스 요구에 가장 적합한 옵션을 선택하세요. 그런 다음 스토리지 선택에 따라 이 문서 전체에서 적절한 VM 구성 옵션을 신중하게 선택합니다. 

## <a name="configure-vm-availability"></a>VM 가용성 구성 

장애 조치(failover) 클러스터 기능을 사용하려면 가상 머신을 [가용성 집합](../../../virtual-machines/linux/tutorial-availability-sets.md) 또는 [가용성 영역](../../../availability-zones/az-overview.md#availability-zones)에 배치해야 합니다. 가용성 집합을 선택하는 경우 [근접 배치 그룹](../../../virtual-machines/co-location.md#proximity-placement-groups)을 사용하여 VM을 더 가깝게 찾을 수 있습니다. 실제로 근접 배치 그룹은 Azure 공유 디스크를 사용하기 위한 필수 구성 요소입니다. 

원하는 클러스터 구성과 일치하는 VM 가용성 옵션을 신중하게 선택합니다. 

- **Azure 공유 디스크**: 프리미엄 SSD 또는 UltraDisk를 사용하는 경우 가용성 옵션이 다릅니다.
   - 프리미엄 SSD: [근접 배치 그룹](../../../virtual-machines/windows/proximity-placement-groups-portal.md) 내에 배치된 프리미엄 SSD에 대한 여러 오류/업데이트 도메인에 [가용성 설정](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set).
   - UltraDisk: [가용성 영역](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address)이지만 VM은 클러스터의 가용성을 99.9%로 줄이는 동일한 가용성 영역에 배치되어야 합니다. 
- **프리미엄 파일 공유**: [가용성 집합](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) 또는 [가용성 영역](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address).
- **스토리지 공간 다이렉트**: [가용성 설정](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set).

> [!IMPORTANT]
> 가상 머신을 만든 후에는 가용성 집합을 설정하거나 변경할 수 없습니다.

## <a name="create-the-virtual-machines"></a>가상 머신 만들기

VM 가용성을 구성하면 가상 머신을 만들 준비가 된 것입니다. SQL Server가 이미 설치되어 있거나 설치되어 있지 않은 Azure Marketplace 이미지를 사용하도록 선택할 수 있습니다. 그러나 Azure VM에서 SQL Server용 이미지를 선택하는 경우 장애 조치(failover) 클러스터 인스턴스를 구성하기 전에 가상 머신에서 SQL Server를 제거해야 합니다. 

### <a name="considerations"></a>고려 사항

Azure VM 게스트 장애 조치(failover) 클러스터의 경우 서버(클러스터 노드)당 하나의 NIC 및 단일 서브넷을 사용하는 것이 좋습니다. Azure 네트워킹에는 Azure IaaS VM 게스트 클러스터에서 추가 NIC 및 서브넷을 불필요하게 만드는 물리적 중복성이 있습니다. 클러스터 유효성 검사 보고서는 노드가 단일 네트워크에서만 연결 가능하다는 경고를 표시하지만, Azure IaaS VM 게스트 장애 조치(failover) 클러스터에서는 이 경고를 무시해도 됩니다.

두 가상 머신을 다음에 배치합니다.

- 가용성 집합을 사용하는 경우 가용성 집합과 동일한 Azure 리소스 그룹에 있습니다.
- 도메인 컨트롤러와 동일한 가상 네트워크 또는 도메인 컨트롤러에 적절한 연결이 있는 가상 네트워크에 있습니다.
- 최종적으로 클러스터에서 사용할 수 있는 가상 머신 및 모든 FCI에 충분한 IP 주소 공간이 있는 서브넷에.
- Azure 가용성 집합 또는 가용성 영역에 있습니다.

사전 설치된 SQL Server를 [포함하는 이미지](sql-vm-create-portal-quickstart.md) 또는 [포함하지 않는 이미지](../../../virtual-machines/windows/quick-create-portal.md)를 사용하여 Azure 가상 머신을 만들 수 있습니다. SQL Server 이미지를 선택하는 경우 장애 조치(failover) 클러스터 인스턴스를 설치하기 전에 SQL Server 인스턴스를 수동으로 제거해야 합니다. 


## <a name="uninstall-sql-server"></a>SQL Server 제거

FCI 생성 프로세스의 일부로 SQL Server를 장애 조치(failover) 클러스터에 클러스터된 인스턴스로 설치합니다. *SQL Server 없이 Azure Marketplace 이미지가 있는 가상 머신을 배포한 경우 이 단계를 건너뛸 수 있습니다.* SQL Server가 사전 설치된 이미지를 배포한 경우 SQL IaaS 에이전트 확장에서 SQL Server VM을 등록 취소한 다음 SQL Server를 제거해야 합니다. 

### <a name="unregister-from-the-sql-iaas-agent-extension"></a>SQL IaaS 에이전트 확장에서 등록 취소

Azure Marketplace의 SQL Server VM 이미지는 SQL IaaS 에이전트 확장에 자동으로 등록됩니다. 사전 설치된 SQL Server 인스턴스를 제거하기 전에 먼저 [SQL IaaS 에이전트 확장에서 각 SQL Server VM을 등록 취소](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension)해야 합니다. 

### <a name="uninstall-sql-server"></a>SQL Server 제거

확장에서 등록을 취소한 후 SQL Server를 제거할 수 있습니다. 각 가상 머신에서 다음 단계를 따르세요. 

1. RDP를 사용하여 가상 머신에 연결

   RDP를 사용하여 가상 머신에 처음 연결하는 경우 네트워크에서 PC를 검색할 수 있도록 허용할지 묻는 메시지가 표시됩니다. **예** 를 선택합니다.

1. SQL Server 기반 가상 머신 이미지 중 하나를 사용하고 있는 경우 SQL Server 인스턴스를 제거합니다.

   1. **프로그램 및 기능** 에서 **Microsoft SQL Server 201_(64비트)** 을 마우스 오른쪽 단추로 클릭하고 **제거/변경** 을 선택합니다.
   1. **제거** 를 선택합니다.
   1. 기본 인스턴스를 선택합니다.
   1. **데이터베이스 엔진 서비스** 에서 모든 기능을 제거합니다. **공유 기능** 아래에 있는 항목을 제거하지 마세요. 다음 스크린샷과 같은 출력이 표시됩니다.

      ![기능 선택](./media/failover-cluster-instance-prepare-vm/03-remove-features.png)

   1. **다음** 을 선택한 다음, **제거** 를 선택합니다.
   1. 인스턴스가 성공적으로 제거되면 가상 머신을 다시 시작합니다. 

## <a name="open-the-firewall"></a>방화벽 열기 

각 가상 머신에서 SQL Server가 사용하는 Windows 방화벽 TCP 포트를 엽니다. 기본 포트는 1433입니다. 그러나 Azure VM 배포에서 SQL Server 포트를 변경할 수 있으므로 SQL Server가 환경에서 사용하는 포트를 엽니다. 이 포트는 Azure Marketplace에서 배포된 SQL Server 이미지에서 자동으로 열립니다. 

[부하 분산 장치](failover-cluster-instance-vnn-azure-load-balancer-configure.md)를 사용하는 경우 상태 프로브가 사용하는 포트도 열어야 합니다. 기본 포트는 59999입니다. 그러나 부하 분산 장치를 만들 때 지정하는 모든 TCP 포트를 사용할 수 있습니다. 

다음 표에서는 FCI 구성에 따라 열어야 할 수 있는 포트에 대해 자세히 설명합니다. 

   | 목적 | 포트 | 메모
   | ------ | ------ | ------
   | SQL Server | TCP 1433 | SQL Server의 기본 인스턴스에 대한 표준 포트입니다. 갤러리에서 이미지를 사용한 경우 이 포트는 자동으로 열립니다. </br> </br> **사용 대상**: 모든 FCI 구성. |
   | 상태 프로브 | TCP 59999 | 모든 공개 TCP 포트입니다. 이 포트를 사용하려면 부하 분산 장치 [상태 프로브](failover-cluster-instance-vnn-azure-load-balancer-configure.md#configure-health-probe) 및 클러스터를 구성합니다. </br> </br> **사용 대상**: 부하 분산 장치가 있는 FCI. |
   | 파일 공유 | UDP 445 | 파일 공유 서비스에서 사용하는 포트입니다. </br> </br> **사용 대상**: 프리미엄 파일 공유를 사용하는 FCI. |

## <a name="join-the-domain"></a>도메인 가입

또한 가상 머신을 도메인에 가입시켜야 합니다. [빠른 시작 템플릿](../../../active-directory-domain-services/join-windows-vm-template.md#join-an-existing-windows-server-vm-to-a-managed-domain)을 사용하면 됩니다. 

## <a name="review-storage-configuration"></a>스토리지 구성 검토

Azure Marketplace에서 만든 가상 머신은 연결된 스토리지와 함께 제공됩니다. 프리미엄 파일 공유 또는 Azure 공유 디스크를 사용하여 FCI 스토리지를 구성하려는 경우 로컬 스토리지가 장애 조치(failover) 클러스터 인스턴스에 사용되지 않으므로 연결된 스토리지를 제거하여 비용을 절감할 수 있습니다. 그러나 스토리지 공간 다이렉트 FCI 솔루션에 연결된 스토리지를 사용할 수 있으므로 이 경우 제거하는 것은 도움이 되지 않을 수 있습니다. FCI 스토리지 솔루션을 검토하여 연결된 스토리지 제거가 비용 절감에 가장 적합한 것인지 확인합니다. 


## <a name="next-steps"></a>다음 단계

이제 가상 머신 환경을 준비했으므로 장애 조치(failover) 클러스터 인스턴스를 구성할 준비가 되었습니다. 

다음 가이드 중 하나를 선택하여 비즈니스에 적합한 FCI 환경을 구성합니다. 
- [Azure 공유 디스크로 FCI 구성](failover-cluster-instance-azure-shared-disks-manually-configure.md)
- [프리미엄 파일 공유로 FCI 구성](failover-cluster-instance-premium-file-share-manually-configure.md)
- [스토리지 공간 다이렉트로 FCI 구성](failover-cluster-instance-storage-spaces-direct-manually-configure.md)


자세한 내용은 다음을 참조하세요.

- [Azure VM에서 SQL Server를 사용하는 Windows Server 장애 조치(failover) 클러스터](hadr-windows-server-failover-cluster-overview.md)
- [Azure VM에서 SQL Server를 사용하는 장애 조치(failover) 클러스터 인스턴스](failover-cluster-instance-overview.md)
- [장애 조치(failover) 클러스터 인스턴스 개요](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
- [Azure VM의 SQL Server에 대한 HADR 설정](hadr-cluster-best-practices.md)
