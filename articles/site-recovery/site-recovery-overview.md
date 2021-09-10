---
title: Azure Site Recovery 정보
description: Azure Site Recovery 서비스의 개요를 제공하고 재해 복구 및 마이그레이션 배포 시나리오를 요약합니다.
ms.topic: overview
ms.date: 08/19/2021
ms.custom: MVC
ms.openlocfilehash: d5558930c77c115ba25cb4b35e88d470afc38a23
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122445693"
---
# <a name="about-site-recovery"></a>Site Recovery란?

Azure Site Recovery 서비스를 시작합니다. 이 문서에서는 빠른 서비스 개요를 제공합니다.

조직에서는 계획에 있거나 예기치 못한 중단이 발생했을 때 데이터를 안전하게 유지하고 앱과 워크로드를 온라인으로 유지하는 BCDR(비즈니스 연속성 및 재해 복구) 전략을 도입해야 합니다.

Azure Recovery Services는 BCDR 전략에 기여합니다.

- **Site Recovery 서비스**: Site Recovery는 중단 시간 중에도 비즈니스 앱과 워크로드를 지속적으로 실행하여 비즈니스 연속성을 보장합니다. Site Recovery는 물리적 컴퓨터와 VM(가상 머신)에서 실행되는 워크로드를 기본 사이트에서 보조 위치로 복제합니다. 기본 사이트에서 중단이 발생하면 보조 위치로 장애 조치(failover)하고 여기에서 앱에 액세스합니다. 기본 위치가 다시 실행되면 장애 복구(failback)할 수 있습니다.
- **백업 서비스**: [Azure Backup](../backup/index.yml) 서비스는 데이터를 안전하고 복구 가능한 상태로 유지합니다.

Site Recovery는 다음을 위해 복제를 관리할 수 있습니다.

- Azure 지역 간에 Azure VM 복제
- 온-프레미스 VM, Azure Stack VM 및 실제 서버.

## <a name="what-does-site-recovery-provide"></a>Site Recovery의 기능은 무엇입니까?

**기능** | **세부 정보**
--- | ---
**간단한 BCDR 솔루션** | Site Recovery를 사용하면 Azure Portal의 단일 위치로부터 복제, 장애 조치 및 장애 복구를 설정 및 관리할 수 있습니다.
**Azure VM 복제** | 기본 지역의 Azure VM을 보조 지역으로 재해 복구하도록 설정할 수 있습니다.
**VMware VM 복제** | 구성 서버보다 더 나은 보안 및 복원력을 제공하는 향상된 Azure Site Recovery 복제 어플라이언스를 사용하여 VMware VM을 Azure에 복제할 수 있습니다. 자세한 내용은 [VMware VM의 재해 복구](vmware-azure-about-disaster-recovery.md)을 참조하세요.
**온-프레미스 VM 복제** | 온-프레미스 VM 및 물리적 서버를 Azure 또는 보조 온-프레미스 데이터 센터에 복제할 수 있습니다. Azure에 복제하면 보조 데이터 센터를 유지하는 비용과 복잡성을 제거할 수 있습니다.
**워크로드 복제** | 지원되는 Azure VM, 온-프레미스 Hyper-V 및 VMware VM, Windows/Linux 물리적 서버에서 실행되는 모든 워크로드를 복제할 수 있습니다.
**데이터 복원력** | Site Recovery는 애플리케이션 데이터를 차단하지 않고 복제를 오케스트레이션합니다. Azure에 복제할 때 데이터는 제공되는 복원력을 통해 Azure Storage에 저장됩니다. 장애 조치(failover)가 발생하는 경우 복제된 데이터를 기반으로 하여 Azure VM을 만듭니다.
**RTO 및 RPO 대상** | 조직 제한 내에서 RTO(복구 시간 목표) 및 RPO(복구 지점 목표)를 유지합니다. Site Recovery는 Azure VM 및 VMware VM에 대해 연속 복제를 제공하고 Hyper-V에 대해서는 짧은 빈도(30초)의 복제를 제공합니다. [Azure Traffic Manager](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/)와 통합하여 RTO를 더 줄일 수 있습니다.
**장애 조치(Failover)를 통해 앱 일관성 유지** | 애플리케이션 일치 스냅샷으로 복구 지점을 사용하여 복제할 수 있습니다. 이러한 스냅샷은 디스크 데이터, 메모리의 모든 데이터, 프로세스의 모든 트랜잭션을 캡처합니다.
**중단 없는 테스트** | 진행 중인 복제에 영향을 주지 않고 손쉽게 재해 복구 훈련을 실행할 수 있습니다.
**유연한 장애 조치(failover)** | 데이터 손실 없이 예상된 중단에 대해 계획된 장애 조치(failover)를 실행할 수 있습니다. 또는 복제 빈도에 따라 예기치 않은 재해 발생 시 데이터 손상을 최소화하면서 계획되지 않은 장애 조치(failover)를 수행할 수 있습니다. 기본 사이트를 다시 사용할 수 있는 경우 손쉽게 이 사이트로 장애 복구할 수 있습니다.
**사용자 지정 복구 계획** | 복구 계획을 사용하여 여러 VM에서 실행되는 다중 계층 애플리케이션의 장애 조치(failover) 및 복구를 사용자 지정하고 순서를 정할 수 있습니다. 복구 계획에서 컴퓨터를 그룹으로 묶고 선택적으로 스크립트와 수동 작업을 추가합니다. 복구 계획은 Azure Automation runbook과 통합될 수 있습니다.
**BCDR 통합** | Site Recovery는 다른 BCDR 기술과 통합합니다. 예를 들어 Site Recovery를 사용하여 가용성 그룹의 장애 조치를 관리하는 AlwaysOn에 대한 SQL Server의 기본 지원을 통해 회사 워크로드의 SQL Server 백 엔드를 보호할 수 있습니다.
**Azure Automation 통합** | 다양한 Azure Automation 라이브러리는 Site Recovery를 다운로드하고 통합할 수 있는 프로덕션 준비된 애플리케이션 특정 스크립트를 제공합니다.
**네트워크 통합** | Site Recovery는 애플리케이션 네트워크 관리를 위해 Azure와 통합됩니다. 예를 들어 IP 주소를 예약하고, 부하 분산 장치를 구성하고, 효율적인 네트워크 전환을 위해 Azure Traffic Manager를 사용합니다.

## <a name="what-can-i-replicate"></a>무엇을 복제할 수 있습니까?

**지원됨** | **세부 정보**
--- | ---
**복제 시나리오** | 한 Azure 지역의 Azure VM을 다른 곳에 복제할 수 있습니다.<br/><br/>  온-프레미스 VMware VM, Hyper-V VM, 물리적 서버(Windows 및 Linux), Azure Stack VM을 Azure에 복제합니다.<br/><br/> AWS Windows 인스턴스를 Azure에 복제합니다.<br/><br/> 온-프레미스 VMware VM, Center VMM에서 관리하는 Hyper-V VM 및 물리적 서버를 보조 사이트에 복제합니다.
**지역** | Site Recovery가 [지원되는 지역](https://azure.microsoft.com/global-infrastructure/services/?products=site-recovery)을 검토합니다. |
**복제된 컴퓨터** | [Azure VM](azure-to-azure-support-matrix.md#replicated-machine-operating-systems) 복제, [온-프레미스 VMware VM 및 물리적 서버](vmware-physical-azure-support-matrix.md#replicated-machines) 및 [온-프레미스 Hyper-V VM](hyper-v-azure-support-matrix.md#replicated-vms)에 대한 복제 요구 사항을 검토합니다.
**작업** | 복제가 지원되는 컴퓨터에서 실행되는 모든 워크로드를 복제할 수 있습니다. 그리고 Site Recovery 팀은 [앱 수](site-recovery-workload.md#workload-summary)에 대해 앱별 테스트를 수행했습니다.

## <a name="next-steps"></a>다음 단계

- [워크로드 지원](site-recovery-workload.md)에 대해 자세히 알아봅니다.
- [지역 간 Azure VM 복제](azure-to-azure-quickstart.md)를 시작합니다.
- [VMware VM 복제](vmware-azure-enable-replication.md) 시작합니다.
