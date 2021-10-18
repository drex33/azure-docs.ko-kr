---
title: Azure Site Recovery를 사용한 Azure VM 재해 복구에 대한 일반적인 질문
description: 이 문서에서는 Azure Site Recovery를 사용할 경우의 Azure VM 재해 복구에 대한 일반적인 질문에 답변합니다.
author: sideeksh
manager: rochakm
ms.date: 07/25/2021
ms.topic: conceptual
ms.openlocfilehash: b7b99f1a5b4c52fb07b2e58c8952eb9ace1f1567
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130138257"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>일반적인 질문: Azure 간 재해 복구

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 다른 Azure 지역으로 Azure VM 재해 복구를 수행할 때 발생하는 일반적인 질문에 답변합니다.

## <a name="general"></a>일반

### <a name="how-is-site-recovery-priced"></a>Site Recovery 비용은 어떻게 책정하는가요?

Azure VM 재해 복구의 [비용](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)에 대해 알아봅니다.

### <a name="how-does-the-free-tier-work"></a>무료 계층은 어떻게 작동하나요?

Site Recovery로 보호되는 모든 인스턴스는 처음 31일 동안 무료로 보호됩니다. 이 기간이 지나면 각 인스턴스에 대한 보호에는 [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/site-recovery/)에 요약된 요율로 요금이 부과됩니다. [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=site-recovery)를 사용하여 비용을 예측할 수 있습니다.

### <a name="do-i-incur-other-azure-charges-in-the-first-31-days"></a>처음 31일 동안 다른 Azure 요금이 발생하나요?

예. 보호된 인스턴스를 처음으로 사용하는 31일 동안 Azure Site Recovery는 무료이지만, Azure Storage, Storage 트랜잭션 및 데이터 전송 요금이 부과될 수 있습니다. 복구된 VM도 Azure Compute 요금이 발생할 수 있습니다. G

### <a name="how-do-i-get-started-with-azure-vm-disaster-recovery"></a>Azure VM 재해 복구는 어떻게 시작하나요?

1. Azure VM 재해 복구 아키텍처를 [이해](azure-to-azure-architecture.md)합니다.
2. 지원 요구 사항을 [검토](azure-to-azure-support-matrix.md)합니다.
3. Azure VM에 대한 재해 복구를 [설정](azure-to-azure-how-to-enable-replication.md)합니다.
4. 테스트 장애 조치(failover)를 사용하여 [재해 복구 훈련을 실행](azure-to-azure-tutorial-dr-drill.md)합니다.
5. 보조 Azure 지역에 대한 [전체 장애 조치를 실행](azure-to-azure-tutorial-failover-failback.md)합니다.
6. 보조 지역에서 주 지역으로 [장애 복구(failback)](azure-to-azure-tutorial-failback.md)합니다.

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>대상 지역에서 용량을 확인하려면 어떻게 해야 하나요?

Site Recovery 팀과 Azure 용량 관리 팀이 충분한 인프라 용량을 계획합니다. 장애 조치를 시작할 때 팀은 Site Recovery로 보호되는 VM 인스턴스가 대상 지역에 배포되도록 하는 데도 도움을 줍니다.

## <a name="replication"></a>복제

### <a name="can-i-replicate-vms-with-disk-encryption"></a>디스크 암호화를 통해 VM을 복제할 수 있나요?

예. Site Recovery는 ADE(Azure Disk Encryption)를 사용하도록 설정한 VM의 재해 복구를 지원합니다. 복제를 사용하도록 설정하면 Azure는 필요한 모든 디스크 암호화 키와 암호를 원본 지역에서 사용자 컨텍스트의 대상 지역으로 복사합니다. 필요한 권한이 없는 경우 보안 관리자는 스크립트를 사용하여 키와 비밀을 복사할 수 있습니다.

- Site Recovery는 Windows를 실행하는 Azure VM에 대해 ADE를 지원합니다.
- Site Recovery는 다음을 지원합니다.
    - ADE 버전 0.1: Azure AD(Azure Active Directory)가 필요한 스키마가 있습니다.
    - ADE 버전 1.1: Azure AD가 필요하지 않습니다. 버전 1.1의 경우 Windows Azure VM에는 Managed Disks가 있어야 합니다.
    - [자세히 알아보기](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema). 확장 스키마 정보

암호화된 VM에 대해 복제를 사용하도록 설정하는 방법을 [자세히 알아보세요](azure-to-azure-how-to-enable-replication-ade-vms.md).

### <a name="can-i-select-an-automation-account-from-a-different-resource-group"></a>다른 리소스 그룹에서 Automation 계정을 선택할 수 있나요?

Site Recovery가 복제된 Azure VM에서 실행되는 Mobility Service 확장에 대한 업데이트를 관리할 수 있도록 허용하면 Azure Automation 계정을 통해 전역 Runbook(Azure 서비스에서 사용)을 배포합니다. Site Recovery가 만드는 Automation 계정을 사용하거나 기존 Automation 계정을 사용하도록 선택할 수 있습니다.

현재 Portal에서는 자격 증명 모음과 동일한 리소스 그룹에 있는 Automation 계정만 선택할 수 있습니다. PowerShell을 사용하여 다른 리소스 그룹에서 Automation 계정을 선택할 수 있습니다. [자세히 알아보기](azure-to-azure-autoupdate.md#enable-automatic-updates).

### <a name="if-i-use-a-customer-automation-account-thats-not-in-the-vault-resource-group-can-i-delete-the-default-runbook"></a>자격 증명 모음 리소스 그룹에 없는 고객 Automation 계정을 사용하는 경우 기본 Runbook을 삭제할 수 있나요?

예, 필요하지 않은 경우 삭제할 수 있습니다.

### <a name="can-i-replicate-vms-to-another-subscription"></a>VM을 다른 구독에 복제할 수 있나요?

예, Azure VM을 동일한 Azure AD 테넌트의 다른 어떤 구독에도 복제할 수 있습니다. VM에 재해 복구를 사용하도록 설정하면 기본적으로 표시된 대상 구독은 원본 VM의 구독입니다. 대상 구독을 수정할 수 있으며 다른 설정(예: 리소스 그룹 및 가상 네트워크)은 선택한 구독에서 자동으로 채워집니다.

### <a name="can-i-replicate-vms-in-an-availability-zone-to-another-region"></a>가용성 영역의 VM을 다른 지역에 복제할 수 있나요?

예, 가용성 영역에 있는 VM을 다른 Azure 지역에 복제할 수 있습니다.

### <a name="can-i-replicate-non-zone-vms-to-a-zone-within-the-same-region"></a>영역이 아닌 VM을 동일한 지역 내의 영역에 복제할 수 있나요?

Portal에서는 지원되지 않습니다. REST API/PowerShell을 사용하면 이 작업을 수행할 수 있습니다.

### <a name="can-i-replicate-zoned-vms-to-a-different-zone-in-the-same-region"></a>영역이 설정된 VM을 동일한 지역의 다른 영역으로 복제할 수 있나요?

이에 대한 지원은 일부 지역으로 제한됩니다. [자세히 알아보기](azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

### <a name="can-i-exclude-disks-from-replication"></a>복제에서 디스크를 제외할 수 있나요?

예, PowerShell을 사용하여 복제를 설정할 때 디스크를 제외할 수 있습니다. [자세히 알아보기](azure-to-azure-exclude-disks.md).

### <a name="can-i-replicate-new-disks-added-to-replicated-vms"></a>추가된 새 디스크를 복제된 VM으로 복제할 수 있나요?

Managed Disks가 있는 복제된 VM의 경우 새 디스크를 추가하고 해당 디스크에 대해 복제를 사용하도록 설정할 수 있습니다. 새 디스크를 추가하는 경우 복제된 VM은 VM에 있는 하나 이상의 디스크를 보호할 수 있다는 경고 메시지를 표시합니다.

- 추가된 디스크에 대한 보호를 사용하도록 설정하면 초기 복제 후에 경고가 사라집니다.
- 디스크에 대해 복제를 사용하도록 설정하지 않으려면 경고를 해제할 수 있습니다.
- 추가된 디스크가 있는 VM을 장애 조치하는 경우 복제 지점은 복구에 사용할 수 있는 디스크를 표시합니다. 예를 들어, 하나의 디스크가 있는 VM에 두 번째 디스크를 추가하는 경우 추가 전에 만든 복제 지점은 "1/2 디스크"로 표시됩니다.

Site Recovery는 복제된 VM에서 디스크의 "핫 제거"를 지원하지 않습니다. VM 디스크를 제거하는 경우 VM에 대한 복제를 사용하지 않도록 설정했다가 다시 사용하도록 설정해야 합니다.

### <a name="how-often-can-i-replicate-to-azure"></a>Azure에 얼마나 자주 복제할 수 있나요?

Azure VM을 다른 Azure 지역에 복제하는 경우에는 복제가 연속적입니다. 복제 작동 방식에 대해 [자세히 알아보세요](./azure-to-azure-architecture.md#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region"></a>한 지역 내에서 가상 머신을 복제할 수 있나요?

Site Recovery를 사용하여 지역 내에서 디스크를 복제할 수 없습니다.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>VM 인스턴스를 Azure 지역에 복제할 수 있나요?

두 지역 간에 VM을 복제 및 복구할 수 있습니다. 

### <a name="does-site-recovery-need-internet-connectivity"></a>Site Recovery에 인터넷 연결이 필요한가요?

아니요, 하지만 VM은 Site Recovery URL 및 IP 범위에 액세스할 수 있어야 합니다. [자세히 알아보기](./azure-to-azure-about-networking.md#outbound-connectivity-for-urls).

### <a name="can-i-replicate-an-application-tiered-across-resource-groups"></a>리소스 그룹 전체에 계층화된 애플리케이션을 복제할 수 있나요?

예, 앱을 복제하고 재해 복구 구성을 별도의 리소스 그룹에 유지할 수 있습니다.

예를 들어 앱의 다른 리소스 그룹에 3개의 계층(애플리케이션/데이터베이스/웹)이 있는 경우 모든 계층을 보호하기 위해 복제를 세 번 사용하도록 설정해야 합니다. Site Recovery는 이러한 세 계층을 세 개의 다른 리소스 그룹에 복제합니다.

### <a name="can-i-move-storage-accounts-across-resource-groups"></a>리소스 그룹에서 스토리지 계정을 이동할 수 있나요?

아니요, 지원되지 않습니다. 실수로 스토리지 계정을 다른 리소스 그룹으로 이동하고 원래 리소스 그룹을 삭제하는 경우 이전 리소스 그룹과 같은 이름으로 새 리소스 그룹을 만든 다음, 스토리지 계정을 이 리소스 그룹으로 이동할 수 있습니다.

## <a name="replication-policy"></a>복제 정책

### <a name="what-is-a-replication-policy"></a>복제 정책은 무엇인가요?

복제 정책은 복구 지점의 보존 기록 및 앱 일치 스냅샷 빈도를 정의합니다.  Site Recovery는 다음과 같이 기본 복제 정책을 만듭니다.

- 24시간 동안 복구 지점을 유지합니다.
- 앱 일치 스냅샷을 4시간마다 작성합니다.

복제 설정에 대해 [자세히 알아보세요](azure-to-azure-how-to-enable-replication.md#customize-target-resources).

### <a name="whats-a-crash-consistent-recovery-point"></a>크래시 일치 복구 지점이란 무엇인가요?

크래시 일치 복구 지점에는 마치 스냅샷 중에 서버에서 전원 코드가 빠진 경우의 디스크 데이터가 있습니다. 스냅샷을 만들 때 메모리에 있던 내용이 포함되지 않습니다.

오늘날 대부분의 앱은 크래시 일치 스냅샷에서도 제대로 복구할 수 있습니다. 크래시 일치 복구 지점은 데이터베이스가 없는 운영 체제와 파일 서버, DHCP 서버, 인쇄 서버 등의 앱에 일반적으로 적합합니다.

Site Recovery는 5분마다 크래시 일치 복구 지점을 자동으로 만듭니다.

### <a name="whats-an-application-consistent-recovery-point"></a>애플리케이션 일치 복구 지점이란 무엇인가요?

앱 일치 복구 지점은 앱 일치 스냅샷에서 생성됩니다. 이 복구 지점은 크래시 일치 스냅샷과 동일한 데이터를 캡처하고 메모리에 있는 데이터와 처리 중인 모든 트랜잭션을 추가로 캡처합니다.

추가 콘텐츠로 인해, 앱 일치 스냅샷은 가장 복잡하며 가장 오랜 시간이 걸립니다. 앱 일치 복구 지점은 데이터베이스 운영 체제와 SQL 서버 등의 앱에 권장됩니다. Windows의 경우 앱 일치 스냅샷은 VSS(볼륨 섀도 복사본 서비스)를 사용합니다.

### <a name="do-app-consistent-recovery-points-impact-performance"></a>앱 일치 복구 지점이 성능에 영향을 주나요?

 앱 일치 복구 지점은 메모리 및 프로세스의 모든 데이터를 캡처하기 때문에 자주 캡처하는 경우 워크로드가 이미 사용 중이라면 성능에 영향을 줄 수 있습니다. 데이터베이스 이외의 워크로드의 경우 너무 자주 캡처하지 않는 것이 좋습니다. 데이터베이스 워크로드의 경우에도 1시간으로 충분합니다.

### <a name="whats-the-minimum-frequency-for-generating-app-consistent-recovery-points"></a>앱 일치 복구 지점이 생성되는 최소 빈도는 얼마나 되나요?

Site Recovery는 최소 1시간의 빈도로 앱 일치 복구 지점을 생성할 수 있습니다.

### <a name="can-i-enable-app-consistent-replication-for-linux-vms"></a>Linux VM에 대한 앱 일치 복제를 사용하도록 설정할 수 있나요?

예. Linux용 모바일 에이전트는 앱 일치를 위한 사용자 지정 스크립트를 지원합니다. 사전 및 사후 옵션을 포함하는 사용자 지정 스크립트는 에이전트에서 사용됩니다. [자세한 정보](site-recovery-faq.yml)

### <a name="how-are-recovery-points-generated-and-saved"></a>복구 지점은 어떻게 생성 및 저장되나요?

Site Recovery에서 복구 지점을 생성하는 방식을 이해하기 위해 예제를 살펴보겠습니다.

- 복제 정책은 24시간 동안의 복구 지점을 유지하고 1시간마다 앱 일치 빈도 스냅샷을 사용합니다.
- Site Recovery는 5분마다 크래시 일치 복구 지점을 만듭니다. 사용자가 이 빈도를 변경할 수는 없습니다.
- Site Recovery 1시간 후에 복구 지점을 정리하여 시간당 하나의 지점을 저장합니다.

따라서 그래프에 표시된 것처럼 지난 1시간 동안의 12개 크래시 일치 지점과 1개 앱 일치 지점 중에서 선택할 수 있습니다.

   ![생성된 복구 지점 목록](./media/azure-to-azure-common-questions/recovery-points.png)

### <a name="how-far-back-can-i-recover"></a>복구할 수 있는 가장 오랜 복구 지점은 어떻게 되나요?

사용할 수 있는 가장 오래된 복구 지점은 72시간입니다.

### <a name="what-happens-if-site-recovery-cant-generate-recovery-points-for-more-than-24-hours"></a>Site Recovery가 24시간 넘게 복구 지점을 생성할 수 없으면 어떻게 되나요?

복제 정책이 24시간이고 Site Recovery가 24시간 넘게 복구 지점을 생성할 수 없는 경우 이전 복구 지점은 그대로 유지됩니다. Site Recovery는 새 지점을 생성하는 경우 가장 오래된 지점만 대체합니다. 새 복구 지점이 있을 때까지 보존 기간에 도달한 후에도 이전 지점은 모두 유지됩니다.

### <a name="can-i-change-the-replication-policy-after-replication-is-enabled"></a>복제를 사용하도록 설정한 후 복제 정책을 변경할 수 있나요?

예. 자격 증명 모음 > **Site Recovery 인프라** > **복제 정책** 에서 정책을 선택하고 편집합니다. 변경 내용은 기존 정책에도 적용됩니다.

### <a name="are-all-recovery-points-a-complete-vm-copy"></a>모든 복구 지점이 완전한 VM 복사본인가요?

생성되는 첫 번째 복구 지점에 전체 복사본이 있습니다. 연속 복구 지점에는 델타 변경 내용이 있습니다.

### <a name="do-increases-in-recovery-point-retention-increase-storage-costs"></a>복구 지점 보존이 증가하면 스토리지 비용도 증가하나요?

예. 보존 기간을 24시간에서 72시간으로 늘리면 Site Recovery에서 추가 48시간의 복구 지점을 저장합니다. 추가된 시간은 스토리지 변경을 초래합니다. 예를 들어, 단일 복구 지점의 델타 변경 내용이 10GB이며 매월 $0.16의 GB당 요금이 부과되는 경우 추가 요금은 월 $1.60 × 48이 됩니다.

## <a name="multi-vm-consistency"></a>다중 VM 일관성

### <a name="what-is-multi-vm-consistency"></a>다중 VM 일관성이란 무엇인가요?

다중 VM 일관성은 복구 지점이 복제된 가상 머신에서 일관되도록 합니다.

- 다중 VM 일관성을 사용하도록 설정하면 Site Recovery는 해당 옵션을 사용하도록 설정한 상태로 모든 머신의 복제 그룹을 만듭니다.
- 복제 그룹의 머신을 장애 조치하는 경우 공유 크래시 일치 및 앱 일치 복구 지점이 생성됩니다.

다중 VM 일관성을 사용하도록 설정하는 방법을 [알아봅니다](azure-to-azure-tutorial-enable-replication.md#enable-replication).

### <a name="can-i-fail-over-a-single-vm-in-a-replication-group"></a>복제 그룹에서 단일 VM을 장애 조치할 수 있나요?

아니요. 다중 VM 일관성을 사용하도록 설정하면 앱이 복제 그룹의 모든 VM에 대해 종속성을 가지며 단일 VM 장애 조치가 허용되지 않는 것으로 유추됩니다.

### <a name="how-many-vm-can-i-replicate-together-in-a-group"></a>그룹에서 함께 복제할 수 있는 VM 수는 몇 개인가요?

하나의 복제 그룹에서 16개의 VM을 함께 복제할 수 있습니다.

### <a name="when-should-i-enable-multi-vm-consistency"></a>다중 VM 일관성을 언제 사용하도록 설정해야 하나요?

CPU를 많이 사용하기 때문에 다중 VM 일관성을 사용하도록 설정하면 워크로드 성능에 영향을 줄 수 있습니다. VM이 동일한 워크로드를 실행 중이며 다중 머신에서 일관성이 필요한 경우에만 사용하도록 설정해야 합니다. 예를 들어, 한 애플리케이션에서 두 개의 SQL Server 인스턴스와 두 개의 웹 서버를 사용하는 경우 SQL Server 인스턴스에 대해서만 다중 VM 일관성을 사용하도록 설정합니다.

### <a name="can-i-add-a-replicating-vm-to-a-replication-group"></a>복제 중인 VM을 복제 그룹에 추가할 수 있나요?

VM에 대해 복제를 사용하도록 설정하는 경우 새 복제 그룹이나 기존 그룹에 추가할 수 있습니다. 이미 그룹에 복제 중인 VM은 추가할 수 없습니다.

## <a name="failover"></a>장애 조치

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>대상 지역에서 용량을 확인하려면 어떻게 해야 하나요?

Site Recovery 팀 및 Azure 용량 관리 팀은 충분한 인프라 용량을 계획하고 있습니다. 장애 조치를 시작할 때 팀은 Site Recovery로 보호되는 VM 인스턴스가 대상 지역에 배포되도록 하는 데도 도움을 줍니다.

### <a name="is-failover-automatic"></a>장애 조치(failover)는 자동입니까?

자동이 아닙니다. 포털에서 클릭 한 번으로 장애 조치를 시작하거나 [PowerShell](azure-to-azure-powershell.md)을 사용하여 장애 조치를 트리거할 수 있습니다.

### <a name="can-i-keep-a-public-ip-address-after--failover"></a>장애 조치 후 공용 IP 주소를 유지할 수 있나요?

장애 조치 후에는 프로덕션 앱의 공용 IP 주소를 유지할 수 없습니다.

장애 조치 프로세스의 일부로 워크로드를 가져오는 경우 Azure 공용 IP 주소 리소스를 할당해야 합니다. 리소스는 대상 지역에서 사용할 수 있어야 합니다. Azure 공용 IP 주소 리소스를 수동으로 할당하거나 복구 계획을 사용하여 자동화할 수 있습니다. 장애 조치 후 공용 IP 주소 설정 방법을 [알아보세요](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan).

### <a name="can-i-keep-a-private-ip-address-after-failover"></a>장애 조치 후 개인 IP 주소를 유지할 수 있나요?

예. 기본적으로 Azure VM에 재해 복구를 사용하도록 설정하면 Site Recovery는 원본 리소스 설정에 따라 대상 리소스를 만듭니다. 고정 IP 주소를 사용하여 구성된 Azure VM의 경우 Site Recovery는 대상 VM에 동일한 IP 주소(사용 중이 아닌 경우)를 프로비전하려고 시도합니다.
장애 조치 후 IP 주소 유지에 대해 [자세히 알아봅니다](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="why-is-a-vm-assigned-a-new-ip-address-after-failover"></a>장애 조치 후 VM에 새 IP 주소를 할당하는 이유는 무엇인가요?

Site Recovery는 장애 조치(failover) 시 IP 주소를 제공하려고 시도합니다. 다른 VM에서 해당 주소를 사용하는 경우 Site Recovery는 사용 가능한 다음 IP 주소를 대상으로 설정합니다.

[가상 네트워크에 대한 네트워크 매핑 및 IP 주소 지정 설정](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms)에 대해 자세히 알아보세요.

### <a name="whats-the-latest-recovery-point"></a>*최신* 복구 지점이란 무엇인가요?

*최신(가장 낮은 RPO)* 복구 지점 옵션은 다음을 수행합니다.

1. 먼저 Site Recovery로 전송된 모든 데이터를 먼저 처리합니다.
2. 서비스는 데이터를 처리한 후 VM으로 장애 조치하기 전에 각 VM에 대한 복구 지점을 만듭니다. 이 옵션은 가장 낮은 RPO(복구 지점 목표)를 제공합니다.
3. 장애 조치 후 생성된 VM의 모든 데이터는 장애 조치가 트리거될 때 Site Recovery로 복제됩니다.

### <a name="do-latest-recovery-points-impact-failover-rto"></a>*최신* 복구 지점이 장애 조치 RTO에 영향을 미치나요?

예. Site Recovery는 장애 조치 전에 모든 보류 중인 데이터를 처리하므로, 이 옵션은 다른 옵션보다 RTO(복구 시간 목표)가 더 높습니다.

### <a name="whats-the-latest-processed-recovery-option"></a>*가장 최근에 처리됨* 복구 옵션이란 무엇인가요?

*가장 최근에 처리됨* 옵션은 다음을 수행합니다.

1. Site Recovery에서 처리된 최신 복구 지점으로 모든 VM을 장애 조치합니다. 이 옵션은 처리되지 않은 데이터를 처리하는 데 시간이 투입되지 않으므로 낮은 RTO를 제공합니다.

### <a name="what-if-theres-an-unexpected-outage-in-the-primary-region"></a>주 지역에서 예기치 않은 중단이 발생하면 어떻게 되나요?

장애 조치를 시작할 수 있습니다. Site Recovery는 주 지역에서의 연결이 없어도 장애 조치를 수행할 수 있습니다.

### <a name="what-is-the-rto-of-a-vm-failover"></a>VM 장애 조치의 RTO는 무엇인가요?

Site Recovery의 RTO SLA는 [2시간](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)입니다. 대부분의 경우 Site Recovery는 몇 분 내에 VM을 장애 조치합니다. RTO를 계산하려면 장애 조치 작업을 검토하여 VM이 작동할 때까지 걸린 시간을 확인합니다.

## <a name="recovery-plans"></a>복구 계획

### <a name="whats-a-recovery-plan"></a>복구 플랜이란 무엇인가요?

Site Recovery의 [복구 플랜](site-recovery-create-recovery-plans.md)은 VM의 장애 조치 및 복구를 조정합니다. 복구 플랜을 통해 복구가 일관성 있게 정확하고, 반복 가능하며, 자동화되도록 유지할 수 있습니다. 메서드는 다음 작업을 수행합니다.

- 함께 장애 조치되는 VM 그룹을 정의합니다.
- 애플리케이션이 정확하게 작동하도록 VM 간의 종속성을 정의합니다.
- VM 장애 조치 이외의 태스크에 대한 사용자 지정 수동 작업 옵션을 통해 복구를 자동화합니다.


### <a name="how-does-sequencing-work"></a>작업 순서는 어떻게 지정하나요?

복구 계획에서는 시퀀싱을 위해 최대 7 개의 VM 그룹을 만들 수 있습니다. 그룹은 한 번에 하나씩 장애 조치되므로 동일한 그룹에 속한 VM이 함께 장애 조치됩니다. [자세히 알아보기](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>복구 플랜의 RTO를 확인하려면 어떻게 할까요?

복구 플랜의 RTO를 확인하려면 복구 플랜에 대해 테스트 장애 조치를 수행합니다. **Site Recovery 작업** 에서 테스트 장애 조치 기간을 확인합니다. 예제 스크린샷에서 **SAPTestRecoveryPlan** 테스트 장애 조치 작업에는 8분 59초가 소요되었습니다.

![RTO에 대한 테스트 장애 조치 기간을 보여주는 작업 나열](./media/azure-to-azure-common-questions/recovery-plan-rto.png)

### <a name="can-i-add-automation-runbooks-to-recovery-plans"></a>복구 플랜에 Automation Runbook을 추가할 수 있나요?

예. [자세히 알아보기](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>다시 보호 및 장애 복구

### <a name="after-failover-are-vms-in-the-secondary-region-protected-automatically"></a>장애 조치 후 보조 지역의 VM이 자동으로 보호되나요?

아니요. VM을 한 지역에서 다른 지역으로 장애 조치할 경우 VM은 대상 재해 복구 지역에서 보호되지 않는 상태로 시작됩니다. 보조 지역의 VM을 [다시 보호하려면](./azure-to-azure-how-to-reprotect.md) 다시 주 지역으로 복제되도록 설정합니다.

### <a name="when-i-reprotect-is-all-data-replicated-from-the-secondary-region-to-primary"></a>다시 보호하면 모든 데이터가 보조 지역에서 주 지역으로 복제되나요?

상황에 따라 다릅니다. 원본 지역 VM이 있는 경우 원본 디스크와 대상 디스크 사이의 변경 내용만 동기화됩니다. Site Recovery는 디스크를 달라진 내용과 비교한 다음, 데이터를 전송합니다. 이 프로세스는 일반적으로 몇 시간 정도 걸립니다. [자세히 알아보기](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection).

### <a name="how-long-does-it-take-fail-back"></a>장애 복구에는 얼마나 시간이 걸리나요?

다시 보호 이후의 장애 복구도 주 지역에서 보조 지역으로 장애 조치하는 데 걸리는 시간과 동일하게 소요됩니다.

## <a name="capacity"></a><a name="capacity"></a>용량

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>대상 지역에서 용량을 확인하려면 어떻게 해야 하나요?

Site Recovery 팀 및 Azure 용량 관리 팀은 충분한 인프라 용량을 계획하고 있습니다. 장애 조치를 시작할 때 팀은 Site Recovery로 보호되는 VM 인스턴스가 대상 지역에 배포되도록 하는 데도 도움을 줍니다.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Site Recovery를 예약 인스턴스에 사용할 수 있나요?

예, 재해 복구 지역에서 [예약된 Azure VM](https://azure.microsoft.com/pricing/reserved-vm-instances/)을 구매할 수 있으며, Site Recovery 장애 조치 작업에서 이 VM을 사용합니다. 추가 구성은 필요하지 않습니다.

## <a name="security"></a>보안

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>복제 데이터가 Site Recovery 서비스로 전송되나요?

아니요, Site Recovery는 복제된 데이터를 가로채지 않으며, VM에서 실행되는 항목에 대한 정보를 갖지 않습니다. 복제 오케스트레이션 및 장애 조치(failover)에 필요한 메타데이터만 Site Recovery 서비스로 전송됩니다.

Site Recovery는 ISO 27001:2013, 27018, HIPAA 및 DPA 인증을 받았습니다. 이 서비스에 대해 SOC2 및 FedRAMP JAB 평가가 진행 중입니다.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery는 복제를 암호화합니까?

예, 전송 중 암호화 및 [Azure의 미사용 데이터 암호화](../storage/common/storage-service-encryption.md)가 모두 지원됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure 간 지원 요구 사항을 검토합니다](azure-to-azure-support-matrix.md).
- [Azure 간 복제를 설정합니다](azure-to-azure-tutorial-enable-replication.md).
- 이 문서를 읽은 후 질문이 있으면 [Azure Recovery Services에 대한 Microsoft Q&A 질문 페이지](/answers/topics/azure-site-recovery.html)에 게시해 주세요.
