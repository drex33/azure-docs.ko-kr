---
title: Azure Site Recovery를 사용한 VMware 재해 복구에 대한 일반적인 질문
description: Azure Site Recovery를 사용하여 Azure로 온-프레미스 VMware VM 재해 복구 시 발생하는 일반적인 질문에 관한 답변을 확인하세요.
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: 8480598cc872a051b6b81f928e92740e39353598
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128621245"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>VMware에서 Azure로 복제에 대한 일반적인 질문

이 문서에서는 온-프레미스 VMware VM(가상 머신)의 재해 복구를 Azure에 배포할 때 나타날 수 있는 일반적인 질문에 답변합니다.

## <a name="general"></a>일반

### <a name="how-do-i-use-the-classic-experience-in-the-recovery-services-vault-rather-than-the-preview-experience"></a>미리 보기 환경 대신 Recovery Services 자격 증명 모음에서 클래식 환경을 사용하려면 어떻게 하나요? 

Azure Site Recovery 복제 어플라이언스를 사용하여 VMware 가상 머신을 보호하는 새롭고 더 안정적인 방법이 이제 [공개 미리 보기](https://support.microsoft.com/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094)로 제공됩니다. 새 Recovery Services 자격 증명 모음이 만들어지면 기본적으로 미리 보기 환경이 선택됩니다. 

환경을 바꾸려면 - 

1. Azure Portal에서 자격 증명 모음을 엽니다. 
2. **시작하기** 섹션에서 **Site Recovery** 를 클릭합니다. 
3. 이 페이지 상단의 배너를 클릭합니다. 
    
    [![VMware 스택 수정 1단계](./media/vmware-azure-common-questions/change-stack-step-1.png)](./media/vmware-azure-common-questions/change-stack-step-1.png#lightbox)

4. 그러면 환경 선택 블레이드가 열립니다. 구성 서버를 사용하려면 기본 환경을 선택한 다음 **확인** 을 클릭합니다. 그렇지 않은 경우 창을 닫습니다. 

    [![VMware 스택 수정 2단계](./media/vmware-azure-common-questions/change-stack-step-2.png)](./media/vmware-azure-common-questions/change-stack-step-2.png#lightbox)

> [!NOTE]
> 환경 유형이 미리 보기에서 클래식으로 전환되면 동일한 Recovery Services 자격 증명 모음에서 다시 전환할 수 없습니다. 이 변경 사항을 저장하기 전에 원하는 환경이 선택되었는지 확인합니다.


### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>VMware VM 재해 복구에는 무엇이 필요한가요?

VMware VM의 재해 복구와 [관련된 구성 요소에 대해 알아봅니다](vmware-azure-architecture.md).

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Site Recovery를 사용하여 VMware VM을 Azure로 마이그레이션할 수 있나요?

예. Site Recovery를 사용하여 VMware VM에 대한 전체 재해 복구를 설정하는 것뿐만 아니라 온-프레미스 VMware VM을 Azure로 마이그레이션할 수 있습니다. 이 시나리오에서는 온-프레미스 VMware VM을 Azure Storage에 복제합니다. 그런 다음, 온-프레미스에서 Azure로 장애 조치합니다. 장애 조치(failover) 후 Azure VM에서 앱과 워크로드가 실행되며 사용할 수 있습니다. 이 과정은 전체 재해 복구를 설정하는 것과 비슷하지만 마이그레이션 시에는 Azure에서 장애 복구(failback)할 수 없습니다.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>내 Azure 계정에 VM을 만들 수 있는 권한이 필요한가요?

구독 관리자인 경우 필요한 복제 권한을 갖고 있습니다. 관리자가 아닌 경우 다음 작업을 수행할 수 있는 권한이 필요합니다.

- Site Recovery를 구성할 때 지정한 리소스 그룹 및 가상 네트워크에서 Azure VM을 만듭니다.
- 구성에 따라 선택한 스토리지 계정 또는 관리 디스크에 씁니다.

필요한 권한에 대해 [자세히 알아보세요](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="what-applications-can-i-replicate"></a>어떤 애플리케이션을 복제할 수 있나요?

[복제 요구 사항](vmware-physical-azure-support-matrix.md#replicated-machines)을 충족하는 VMware VM에서 실행되는 모든 앱 또는 워크로드를 복제할 수 있습니다.

- Site Recovery는 애플리케이션 인식 복제를 지원하므로 앱을 지능형 상태로 장애 조치(failover) 및 장애 복구(failback)할 수 있습니다.
- Site Recovery는 SharePoint, Exchange, Dynamics, SQL Server 및 Active Directory와 같은 Microsoft 애플리케이션과 통합됩니다. Oracle, SAP, IBM 및 Red Hat을 비롯한 주요 공급업체와도 긴밀히 협력합니다.

[자세히 알아봅니다](site-recovery-workload.md) .

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Azure에서 게스트 OS 서버 라이선스를 사용할 수 있나요?

예, Microsoft Software Assurance 고객은 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 활용해서 Azure로 마이그레이션되는 Windows Server 컴퓨터의 라이선스 비용을 절감하거나 Azure를 재해 복구에 사용할 수 있습니다.

## <a name="security"></a>보안

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Site Recovery에서 VMware 서버에 액세스하는 데 필요한 작업은 무엇인가요?

Site Recovery에서는 다음 작업을 위해 VMware 서버에 액세스해야 합니다.

- Site Recovery 구성 서버를 실행할 VMware VM을 설정합니다.
- 복제할 VM을 자동으로 검색합니다.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Site Recovery에서 VMware VM에 액세스하는 데 필요한 작업은 무엇인가요?

- 복제를 진행하려면 먼저 VMware VM에 Site Recovery Mobility Service를 설치하고 실행해야 합니다. VM에 대한 복제를 사용하도록 설정할 때 Site Recovery에서 서비스를 강제 설치하도록 지정하거나, 도구를 수동으로 배포할 수 있습니다.
- 복제 중에 VM은 Site Recovery와 다음과 같이 통신합니다.
    - VM은 복제 관리를 위해 HTTPS 443 포트에서 구성 서버와 통신합니다.
    - VM은 복제 데이터를 HTTPS 9443 포트에서 프로세스 서버에 전송합니다. (이 설정은 수정할 수 있습니다.)
    - 다중 VM 일관성을 사용하도록 설정하면 VM에서 20004 포트를 통해 서로 통신합니다.

### <a name="is-replication-data-sent-to-site-recovery"></a>복제 데이터를 Site Recovery로 보내는가요?

아니요, Site Recovery는 복제된 데이터를 가로채지 않으며, VM에서 실행 중인 항목에 관해 어떠한 정보도 없습니다. 복제 데이터는 VMware 하이퍼바이저와 Azure Storage 간에 교환됩니다. Site Recovery는 데이터를 가로챌 수 있는 기능이 없습니다. 복제 오케스트레이션 및 장애 조치(failover)에 필요한 메타데이터만 Site Recovery 서비스로 전송됩니다.

Site Recovery는 ISO 27001:2013, 27018, HIPAA 및 DPA 인증을 받았으며 SOC2 및 FedRAMP JAB 평가 중에 있습니다.

## <a name="pricing"></a>가격 책정

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>VMware 재해 복구에 대한 대략적인 요금은 어떻게 계산하나요?

Site Recovery를 사용하는 동안 [가격 책정 계산기](https://aka.ms/asr_pricing_calculator)를 사용하여 비용을 예측할 수 있습니다.

비용을 상세히 예측하려면 [VMware](./site-recovery-deployment-planner.md)용 배포 플래너 도구를 실행하고 [비용 예측 보고서](./site-recovery-vmware-deployment-planner-cost-estimation.md)를 사용합니다.

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>저장소로 복제하는 비용할 때와 관리 디스크에 직접 복제할 때 비용 차이가 있나요?

관리 디스크는 스토리지 계정보다 조금 더 비용이 청구됩니다. 관리 디스크 가격 책정에 대해 [자세히 알아보세요](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>범용 v2 스토리지 계정으로 복제할 때 비용 차이가 있나요?

Azure Site Recovery는 트랜잭션이 많기 때문에 일반적으로 GPv2 스토리지 계정에서 발생하는 트랜잭션 비용이 증가하는 것을 볼 수 있습니다. 변경되는 비용을 예측하려면 [자세히 알아보세요](../storage/common/storage-account-upgrade.md#pricing-and-billing).

## <a name="mobility-service"></a>모바일 서비스

### <a name="where-can-i-find-the-mobility-service-installers"></a>모바일 서비스 설치 관리자는 어디서 찾을 수 있나요?

설치 프로그램은 구성 서버의 %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository 폴더에 있습니다.

## <a name="how-do-i-install-the-mobility-service"></a>모바일 서비스를 설치하려면 어떻게 할까요?

복제하려는 각 VM에서 다음 몇 가지 방법 중 하나로 서비스를 설치합니다.

- [강제 설치](vmware-physical-mobility-service-overview.md#push-installation)
- UI 또는 PowerShell에서 [수동 설치](vmware-physical-mobility-service-overview.md#install-the-mobility-service-using-ui-classic)
- [Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)와 같은 배포 도구를 사용하여 배포

## <a name="managed-disks"></a>관리 디스크

### <a name="where-does-site-recovery-replicate-data-to"></a>Site Recovery는 데이터를 어디에 복제하나요?

Site Recovery는 Azure의 관리 디스크에 온-프레미스 VMware VM 및 물리적 서버를 복제합니다.

- Site Recovery 프로세스 서버는 대상 지역의 캐시 스토리지 계정에 복제 로그를 씁니다.
- 이러한 로그는 **asrseeddisk** 접두사가 있는 Azure 관리 디스크에서 복구 지점을 만드는 데 사용됩니다.
- 선택한 복구 지점은 장애 조치(failover)가 발생할 때 새 대상 관리 디스크를 만드는 데 사용됩니다. 이 관리 디스크는 Azure의 VM에 연결되어 있습니다.
- 2019년 3월 이전에 스토리지 계정으로 복제된 VM은 해당되지 않습니다.

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>스토리지 계정에 새 컴퓨터를 복제할 수 있나요?

아니요. 2019년 3월부터 Azure Portal에서는 Azure 관리 디스크로만 복제할 수 있습니다.

PowerShell([Az.RecoveryServices 모듈 버전 1.4.5](https://www.powershellgallery.com/packages/Az.RecoveryServices/1.4.5)) 또는 REST API(버전 2018-01-10 또는 2016-08-10)를 사용하는 경우에만 스토리지 계정에 새 VM을 복제할 수 있습니다. PowerShell 명령을 사용하여 복제를 설정하는 [방법을 알아봅니다](./vmware-azure-disaster-recovery-powershell.md).

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>관리 디스크로 복제할 때의 이점은 무엇인가요?

Site Recovery에서 관리 디스크를 사용하여 재해 복구를 간소화하는 [방법을 알아봅니다](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/).

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>컴퓨터가 보호된 후 관리 디스크 유형을 변경할 수 있나요?

예, [관리 디스크 유형을 쉽게 변경](../virtual-machines/windows/convert-disk-storage.md)하여 계속 복제를 진행할 수 있습니다. 유형을 변경하기 전에 관리 디스크에서 공유 액세스 서명 URL이 생성되지 않도록 합니다.

1. Azure Portal에서 **관리 디스크** 리소스로 이동하여 **개요** 블레이드에 공유 액세스 서명 URL 배너가 있는지 확인합니다.
1. 배너가 있는 경우 해당 배너를 선택하여 진행 중인 내보내기를 취소합니다.
1. 몇 분 내에 디스크 유형을 변경합니다. 관리 디스크 유형을 변경하는 경우 Azure Site Recovery에서 새 복구 지점을 생성할 때까지 기다립니다.
1. 이후부터는 모든 테스트 장애 조치(failover) 또는 장애 조치(failover)에 새 복구 지점을 사용합니다.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>관리 디스크에서 비관리 디스크로 복제를 전환할 수 있나요?

아니요. 관리 디스크에서 비관리 디스크로의 전환은 지원되지 않습니다.

## <a name="replication"></a>복제

### <a name="what-are-the-replicated-vm-requirements"></a>복제된 VM에 대한 요구 사항은 무엇인가요?

VMware VM 및 물리적 서버의 지원 요구 사항에 대해 [자세히 알아보세요](vmware-physical-azure-support-matrix.md#replicated-machines).

### <a name="how-often-can-i-replicate-to-azure"></a>Azure에 얼마나 자주 복제할 수 있나요?

VMware VM을 Azure에 복제하는 경우에는 복제가 계속됩니다.

### <a name="can-i-extend-replication"></a>복제를 확장할 수 있나요?

확장 복제 또는 체인으로 연결된 복제는 지원되지 않습니다. [사용자 의견 포럼](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)을 통해 이 기능에 대한 의견을 보내 주세요.

### <a name="how-can-i-track-progress-of-initial-replicationsynchronization"></a>초기 복제/동기화 진행률을 어떻게 추적하나요?

이 기능은 최근에 Site Recovery 서비스에 적용되었습니다. 정확한 세부 정보를 확인하려면 Site Recovery 인프라(구성 서버, 스케일 아웃 프로세스 서버) 및 모바일 에이전트를 버전 9.36 이상으로 업데이트합니다. [여기](vmware-azure-enable-replication.md#monitor-initial-replication)에서 진행률을 추적하는 방법에 대해 자세히 알아보세요.

### <a name="can-i-do-an-offline-initial-replication"></a>오프라인 초기 복제를 수행할 수 있나요?

오프라인 복제는 지원되지 않습니다. [사용자 의견 포럼](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)을 통해 이 기능에 대한 의견을 보내 주세요.

### <a name="what-is-asrseeddisk"></a>asrseeddisk란 무엇인가요?

모든 원본 디스크의 경우, 데이터는 Azure의 관리 디스크에 복제됩니다. 이 디스크는 **asrseeddisk** 접두사가 있으며 원본 디스크의 복사본과 모든 복구 지점 스냅샷을 저장합니다.

### <a name="can-i-exclude-disks-from-replication"></a>복제에서 디스크를 제외할 수 있나요?

예. 디스크를 제외할 수 있습니다.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>동적 디스크가 있는 VM을 복제할 수 있나요?

예, 동적 디스크는 복제할 수 있습니다. 운영 체제 디스크는 기본 디스크여야 합니다.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>다중 VM 일관성에 대해 복제 그룹을 사용하는 경우 새 VM을 기존 복제 그룹에 추가할 수 있나요?

예, 복제를 활성화하면 새 VM을 기존 복제 그룹에 추가할 수 있습니다. 단,

- 복제가 시작된 후에는 기존 복제 그룹에 VM을 추가할 수 없습니다.
- 기존 VM에 대한 복제 그룹을 만들 수 없습니다.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>디스크를 추가하거나 크기를 조정하여 복제 중인 VM을 수정할 수 있나요?

Azure로 VMware를 복제하는 경우 원본 VM의 디스크 크기를 수정할 수 있습니다. 새 디스크를 추가하려면 디스크를 추가하고 VM에 대한 보호를 다시 활성화해야 합니다.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>진행 중인 복제에 영향을 주지 않고 온-프레미스 컴퓨터를 새 vCenter Server에 마이그레이션할 수 있나요?

새 vCenter로 컴퓨터를 마이그레이션하려면 [지침](vmware-azure-manage-vcenter.md#migrate-all-vms-to-a-new-server)을 참조하세요.

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>가상 네트워크(Azure Firewall 포함)가 구성된 캐시 또는 대상 스토리지 계정에 복제할 수 있나요?

아니요, Site Recovery는 가상 네트워크에서 Azure Storage로의 복제를 지원하지 않습니다.

### <a name="what-is-the-frequency-of-generation-of-crash-consistent-recovery-points"></a>크래시 일관성이 있는 복구 지점을 생성하는 빈도는 어느 정도인가요?

Site Recovery는 5분 마다 크래시 일관성이 있는 복구 지점을 만듭니다.

## <a name="component-upgrade"></a>구성 요소 업그레이드

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>현재 사용 중인 버전의 Mobility Service 에이전트 또는 구성 서버가 오래되어 업그레이드할 수 없습니다. 어떻게 해야 합니까?

Site Recovery는 N-4 지원 모델을 따릅니다. 아주 오래된 버전을 업그레이드하는 방법에 대해 [자세히 알아보세요](./service-updates-how-to.md#support-statement-for-azure-site-recovery).

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>Azure Site Recovery의 릴리스 정보 및 업데이트 롤업을 어디에서 찾을 수 있나요?

[새 업데이트 사항에 대해 알아보고](site-recovery-whats-new.md) [롤업 정보를 얻습니다](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Azure로의 재해 복구에 대한 업그레이드 정보는 어디에서 찾을 수 있나요?

[업그레이드에 대해 알아보세요](./service-updates-how-to.md#vmware-vmphysical-server-disaster-recovery-to-azure).

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>업그레이드마다 원본 컴퓨터를 다시 부팅해야 하나요?

다시 부팅하는 것이 좋지만 업그레이드마다 해야 하는 것은 아닙니다. [자세히 알아봅니다](./service-updates-how-to.md#reboot-after-mobility-service-upgrade).

## <a name="configuration-server"></a>구성 서버

### <a name="what-does-the-configuration-server-do"></a>구성 서버에서 수행하는 작업은 무엇인가요?

구성 서버는 다음을 포함하여 온-프레미스 Site Recovery 구성 요소를 실행합니다.

- 구성 서버 자체. 서버는 온-프레미스 구성 요소와 Azure 간의 통신을 조정하여 데이터 복제본을 관리합니다.
- 프로세스 서버. 복제 게이트웨이의 역할을 합니다. 이 서버:
    1. 복제 데이터를 받습니다.
    2. 캐싱, 압축 및 암호화를 사용하여 데이터를 최적화합니다.
    3. Azure Storage에 데이터를 보냅니다.
  또한 프로세스 서버는 VM에 Mobility Service를 강제 설치하고 온-프레미스 VMware VM에서 자동 검색을 수행합니다.
- 마스터 대상 서버. Azure에서 장애 복구(failback)를 하는 동안 복제 데이터를 처리합니다.

구성 서버 구성 요소 및 프로세스에 대해 [자세히 알아보세요](vmware-azure-architecture.md).

### <a name="where-do-i-set-up-the-configuration-server"></a>구성 서버를 설정해야 하는 위치는 어떻게 되나요?

구성 서버에는 단일 고가용성 온-프레미스 VMware VM이 필요합니다. 물리적 서버 재해 복구의 경우 물리적 컴퓨터에 구성 서버를 설치합니다.

### <a name="what-do-i-need-for-the-configuration-server"></a>구성 서버에는 무엇이 필요한가요?

[필수 구성 요소](vmware-azure-deploy-configuration-server.md#prerequisites)를 검토하세요.

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>구성 서버는 템플릿을 사용하는 대신 수동으로 설정할 수 있나요?

최신 버전의 OVF(Open Virtualization Format) 템플릿을 사용하여 [구성 서버 VM을 만드는](vmware-azure-deploy-configuration-server.md) 것이 좋습니다. 템플릿을 사용할 수 없는 경우(예: VMware 서버에 대한 액세스 권한이 없을 때) 포털에서 설치 파일을 [다운로드](physical-azure-set-up-source.md)하고 구성 서버를 설정합니다.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>구성 서버에서 둘 이상의 지역에 복제할 수 있나요?

아니요. 둘 이상의 지역에 복제하려면 각 지역에 구성 서버가 있어야 합니다.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Azure에서 구성 서버를 호스팅할 수 있나요?

가능하지만, 구성 서버를 실행하는 Azure VM에서 온-프레미스 VMware 인프라 및 VM과 통신해야 합니다. 이 통신은 대기 시간을 늘어나게 하고 진행 중인 복제에 영향을 줍니다.

### <a name="how-do-i-update-the-configuration-server"></a>구성 서버를 업데이트하려면 어떻게 할까요?

구성 서버를 업데이트하는 방법을 [알아보세요](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server).

- 최신 업데이트 정보는 [Azure 업데이트 페이지](https://azure.microsoft.com/updates/?product=site-recovery)에서 확인할 수 있습니다.
- 최신 버전은 포털에서 다운로드할 수 있습니다. 또는 [Microsoft 다운로드 센터](https://aka.ms/asrconfigurationserver)에서 최신 버전의 구성 서버를 직접 다운로드할 수 있습니다.
- 최신 버전이 사용 중인 버전보다 4번 이상 업그레이드된 버전인 경우, [지원 설명](./service-updates-how-to.md#support-statement-for-azure-site-recovery)에서 업그레이드 지침을 참조하세요.

### <a name="should-i-back-up-the-configuration-server"></a>구성 서버를 백업해야 하나요?

구성 서버의 예약된 정기 백업을 수행하는 것이 좋습니다.

- 장애 복구(failback)를 성공적으로 하려면 장애 복구(failback)인 가상 머신이 구성 서버 데이터베이스에 있어야 합니다.
- 구성 서버가 실행 중이고 연결된 상태여야 합니다.
- 일반적인 구성 서버 관리 작업에 대해 [자세히 알아보세요](vmware-azure-manage-configuration-server.md).

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>구성 서버를 설정할 때 MySQL을 수동으로 다운로드하여 설치할 수 있나요?

예. MySQL을 다운로드하여 C:\Temp\ASRSetup 폴더에 넣습니다. 그런 다음 수동으로 설치합니다. 구성 서버 VM을 설정하고 조건에 동의하면 MySQL이 **다운로드 및 설치** 에 **이미 설치됨** 으로 표시됩니다.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>MySQL을 다운로드하지 않고 Site Recovery에서 설치할 수 있나요?

예. MySQL 설치 관리자를 다운로드하여 C:\Temp\ASRSetup 폴더에 넣습니다. 구성 서버 VM을 설정하는 경우, 사용 약관에 동의한 후 **다운로드 및 설치** 를 선택합니다. 포털에서 추가된 설치 프로그램을 사용하여 MySQL을 설치합니다.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>다른 용도로 구성 서버 VM을 사용할 수 있나요?

아니요. 구성 서버에만 VM을 사용합니다.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>구성 서버를 복제하고 오케스트레이션에 사용할 수 있나요?

아니요. 등록 문제를 방지하기 위해 새 구성 서버를 설정해야 합니다.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>구성 서버가 등록된 자격 증명 모음을 변경할 수 있나요?

아니요. 구성 서버에 연결된 후에는 자격 증명 모음을 변경할 수 없습니다. 다른 자격 증명 모음을 사용하여 구성 서버를 등록하는 방법에 관해 [알아보세요](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault).

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>VMware VM의 재해 복구와 물리적 서버의 재해 복구에 동일한 구성 서버를 사용할 수 있나요?

예. 하지만 물리적 컴퓨터는 VMware VM으로만 장애 복구(failback)할 수 있습니다.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>구성 서버의 암호는 어디서 다운로드할 수 있나요?

암호를 다운로드하는 방법을 [알아보세요](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase).

### <a name="where-can-i-download-vault-registration-keys"></a>자격 증명 모음 등록 키는 어디서 다운로드할 수 있나요?

Recovery Services 자격 증명 모음에서 **Site Recovery 인프라** > **관리** 에 있는 **구성 서버** 를 선택합니다. **서버** 에서 **등록 키 다운로드** 를 선택하여 자격 증명 모음의 자격 증명 파일을 다운로드합니다.

### <a name="can-a-single-configuration-server-be-used-to-protect-multiple-vcenter-instances"></a>단일 구성 서버를 사용하여 여러 vCenter 인스턴스를 보호할 수 있나요?

예. 단일 구성 서버는 여러 vCenter를 통해 VM을 보호할 수 있습니다.  구성 서버에 추가할 수 있는 vCenter 인스턴스 수에는 제한이 없지만 단일 구성 서버가 보호할 수 있는 VM수는 제한적입니다.

### <a name="can-a-single-configuration-server-protect-multiple-clusters-within-vcenter"></a>단일 구성 서버가 vCenter 내에서 여러 클러스터를 보호할 수 있나요?

예. Azure Site Recovery는 여러 클러스터를 통해 VM을 보호할 수 있습니다.

## <a name="process-server"></a>프로세스 서버

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>복제를 사용하도록 설정할 때 프로세스 서버를 선택할 수 없는 이유는 무엇인가요?

9\.24 이상 버전부터는 [복제를 사용하도록 설정할 때 프로세스 서버의 상태](vmware-azure-enable-replication.md#enable-replication)를 표시하도록 업데이트되었습니다. 이 기능을 통해 프로세스 서버 제한을 방지하고 비정상 프로세스 서버 사용을 최소화할 수 있습니다.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>정확한 상태 정보를 볼 수 있는 프로세스 서버 9.24 이상 버전으로 업데이트하려면 어떻게 해야 하나요?

[버전 9.24](service-updates-how-to.md#links-to-currently-supported-update-rollups)부터 프로세스 서버의 상태를 표시하는 경고가 더 추가되었습니다. 모든 경고가 생성되도록 [Site Recovery 구성 요소를 9.24 이상 버전으로 업데이트](service-updates-how-to.md#links-to-currently-supported-update-rollups)합니다.

### <a name="how-can-i-ensure-high-availability-of-the-process-server"></a>프로세스 서버의 고가용성을 보장하려면 어떻게 해야 하나요?

두 개 이상의 프로세스 서버를 구성하면 비정상 프로세스 서버에서 작업 중인 프로세스 서버로 보호된 컴퓨터를 유연하게 이동할 수 있습니다. 한 프로세스 서버에서 다른 프로세스 서버로 컴퓨터를 이동하려면 [프로세스 서버 간에 VM 이동](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load)에 정의된 단계를 따라 명시적으로 또는 수동으로 시작해야 합니다.

## <a name="failover-and-failback"></a>장애 조치(failover) 및 장애 복구(failback)

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>장애 복구(failback)를 위해 온-프레미스 프로세스 서버를 사용할 수 있나요?

데이터 전송 대기 시간이 발생하지 않도록 Azure에서 장애 복구(failback)용 프로세스 서버를 만드는 것이 좋습니다. 또한 원본 VM 네트워크를 구성 서버에 있는 Azure 연결 네트워크와 분리한 경우, Azure에서 만든 장애 복구(failback)용 프로세스 서버를 사용해야 합니다.

### <a name="can-i-keep-the-ip-address-on-failover"></a>장애 조치(failover) 시 IP 주소를 유지할 수 있나요?

예. 장애 조치(failover) 시 IP 주소를 유지할 수 있습니다. 장애 조치(failover) 전에 VM에 대한 **컴퓨팅 및 네트워크** 설정에서 대상 IP 주소를 지정했는지 확인합니다. 또한 장애 복구(failback) 중 IP 주소 충돌이 발생하지 않도록 장애 조치(failover) 시 컴퓨터를 종료합니다.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>장애 조치(failover) 전에 대상 VM 크기 또는 VM 유형을 변경할 수 있나요?

예. 장애 조치(failover) 전에 언제든지 VM 유형 또는 크기를 변경할 수 있습니다. 포털에서 복제된 VM에 대한 **컴퓨팅 및 네트워크** 설정을 사용합니다.

### <a name="how-far-back-can-i-recover"></a>복구할 수 있는 가장 오랜 복구 지점은 어떻게 되나요?

VMware에서 Azure로 복구하는 경우 사용할 수 있는 가장 오래된 복구 지점은 72시간입니다.

### <a name="how-do-i-access-azure-vms-after-failover"></a>장애 조치 후 Azure VM에 액세스하려면 어떻게 할까요?

장애 조치(failover) 후에 보안 인터넷 연결, 사이트 간 VPN 또는 Azure ExpressRoute를 통해 Azure VM에 액세스할 수 있습니다. 연결하려면 여러 항목을 준비해야 합니다. [자세히 알아봅니다](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

### <a name="is-failed-over-data-resilient"></a>장애 조치(failover)된 데이터는 복원되나요?

Azure는 복원을 위해 디자인되었습니다. Site Recovery는 Azure SLA(서비스 수준 계약)에서 요구하는 대로 보조 Azure 데이터 센터에 장애 조치(failover)하도록 설계되었습니다. 장애 조치(failover)가 발생하면 메타데이터와 자격 증명 모음이 자격 증명 모음에 대해 선택한 지리적 지역과 동일한 지역에 유지되고 있는지 확인합니다.

### <a name="is-failover-automatic"></a>장애 조치(failover)는 자동입니까?

[장애 조치](site-recovery-failover.md)는 자동이 아닙니다. 포털에서 한 번만 선택하면 장애 조치(failover)를 시작하거나 [PowerShell](/powershell/module/az.recoveryservices)을 사용하여 장애 조치(failover)를 트리거할 수 있습니다.

### <a name="can-i-fail-back-to-a-different-location"></a>다른 위치로 장애 조치할 수 있나요?

예. Azure에 장애 조치(failover)한 경우, 원본을 사용할 수 없을 때 다른 위치로 장애 복구(failback)할 수 있습니다. [자세히 알아봅니다](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>장애 복구(failback)를 위해 개인 피어링이 있는 VPN 또는 ExpressRoute를 사용해야 하는 이유는 무엇인가요?

Azure에서 장애 복구(failback)하는 경우 Azure의 데이터가 온-프레미스 VM으로 다시 복사되므로 프라이빗 액세스가 필요합니다.


## <a name="automation-and-scripting"></a>자동화 및 스크립팅

### <a name="can-i-set-up-replication-with-scripting"></a>스크립팅을 사용하여 복제를 설정할 수 있나요?

예. Rest API, PowerShell 또는 Azure SDK를 사용하여 Site Recovery 워크플로를 자동화할 수 있습니다. [자세히 알아봅니다](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>성능 및 용량

### <a name="can-i-throttle-replication-bandwidth"></a>복제 대역폭을 제한할 수 있나요?

예. [자세히 알아보기](site-recovery-plan-capacity-vmware.md).

## <a name="next-steps"></a>다음 단계

- 지원 요구 사항을 [검토](vmware-physical-azure-support-matrix.md)합니다.
- VMware에서 Azure로의 복제를 [설정](vmware-azure-tutorial.md)합니다.
