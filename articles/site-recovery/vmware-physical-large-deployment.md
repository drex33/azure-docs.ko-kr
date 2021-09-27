---
title: Azure Site Recovery를 사용하여 VMware/물리적 재해 복구 크기 조정
description: Azure Site Recovery를 사용하여 많은 수의 온-프레미스 VMware VM 또는 물리적 서버에 대한 재해 복구를 Azure에 설정하는 방법을 알아봅니다.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 6a77b91b49009964ba6e20f76e20e4739da6ea75
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124823771"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>VMware VM/물리적 서버에 대한 대규모 재해 복구 설정

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 프로덕션 환경에서 많은 수(1,000개 초과)의 온-프레미스 VMware VM 또는 물리적 서버에 대한 재해 복구를 Azure에 설정하는 방법에 대해 설명합니다.


## <a name="define-your-bcdr-strategy"></a>BCDR 전략 정의

BCDR(비즈니스 연속성 및 재해 복구) 전략의 일부로 비즈니스 앱 및 워크로드에 대한 RPO(복구 지점 목표) 및 RTO(복구 시간 목표)를 정의합니다. RTO는 연속성 문제를 방지하기 위해 비즈니스 앱 또는 프로세스를 복원하고 사용할 수 있어야 하는 기간 및 서비스 수준을 측정합니다.
- Site Recovery는 VMware VM 및 물리적 서버에 대한 지속적인 복제와 RTO에 대한 [SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)를 제공합니다.
- VMware VM에 대한 대규모 재해 복구를 계획하고 필요한 Azure 리소스를 파악할 때 용량 계산에 사용할 RTO 값을 지정할 수 있습니다.


## <a name="best-practices"></a>모범 사례

대규모 재해 복구를 위한 몇 가지 일반적인 모범 사례입니다. 이러한 모범 사례는 문서의 다음 섹션에서 자세히 설명합니다.

- **대상 요구 사항 식별**: 재해 복구를 설정하기 전에 Azure의 용량 및 리소스 요구 사항을 예측합니다.
- **Site Recovery 구성 요소 계획**: 예상 용량을 충족하는 데 필요한 Site Recovery 구성 요소(구성 서버, 프로세스 서버)를 파악합니다.
- **하나 이상의 스케일 아웃 프로세스 서버 설정**: 구성 서버에서 기본적으로 실행되는 프로세스 서버를 사용하지 않습니다. 
- **최신 업데이트 실행**: Site Recovery 팀에서 정기적으로 새 버전의 Site Recovery 구성 요소를 릴리스하므로 사용자는 최신 버전을 실행하고 있는지 확인해야 합니다. 이를 지원하기 위해 업데이트의 [새로운 기능](site-recovery-whats-new.md)을 추적하고, 업데이트가 릴리스될 때 [해당 업데이트를 사용하도록 설정하고 설치](service-updates-how-to.md)합니다.
- **사전 모니터링**: 재해 복구를 시작하고 실행할 때 복제된 컴퓨터 및 인프라 리소스의 상태를 사전에 모니터링해야 합니다.
- **재해 복구 훈련**: 재해 복구 훈련을 정기적으로 실행해야 합니다. 이러한 훈련은 프로덕션 환경에 영향을 주지 않지만, 필요한 경우 Azure로의 장애 조치(failover)가 예상대로 작동하는지 확인하는 데 도움이 됩니다.



## <a name="gather-capacity-planning-information"></a>용량 계획 정보 수집

온-프레미스 환경에 대한 정보를 수집하여 대상(Azure) 용량 요구 사항을 평가하고 예측합니다.
- VMware의 경우 이 작업은 VMware VM에 대한 Deployment Planner를 실행하여 수행합니다.
- 물리적 서버의 경우 수동으로 정보를 수집합니다.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>VMware VM에 대한 Deployment Planner 실행

Deployment Planner는 VMware 온-프레미스 환경에 대한 정보를 수집하는 데 도움이 됩니다.

- VM의 일반적인 변동을 나타내는 기간 동안 Deployment Planner를 실행합니다. 이렇게 하면 더 정확한 예측 및 권장 사항이 생성됩니다.
- Deployment Planner는 실행되는 서버에서 처리량을 계산하므로 구성 서버 컴퓨터에서 Deployment Planner를 실행하는 것이 좋습니다. 처리량 측정에 대해 [자세히 알아보세요](site-recovery-vmware-deployment-planner-run.md#get-throughput).
- 아직 구성 서버를 설정하지 않은 경우
    - Site Recovery 구성 요소에 대한 [개요를 살펴봅니다](vmware-physical-azure-config-process-server-overview.md).
    - Deployment Planner를 실행하도록 [구성 서버를 설정합니다](vmware-azure-deploy-configuration-server.md).

그런 다음, Deployment Planner를 다음과 같이 실행합니다.

1. Deployment Planner에 대해 [알아봅니다](site-recovery-deployment-planner.md). 포털에서 최신 버전을 다운로드하거나 [직접 다운로드](https://aka.ms/asr-deployment-planner)할 수 있습니다.
2. Deployment Planner에 대한 [필수 구성 요소](site-recovery-deployment-planner.md#prerequisites) 및 [최신 업데이트](site-recovery-deployment-planner-history.md)를 검토하고, 도구를 [다운로드하여 압축을 풉니다](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool).
3. 구성 서버에서 [Deployment Planner를 실행](site-recovery-vmware-deployment-planner-run.md)합니다.
4. [보고서를 생성](site-recovery-vmware-deployment-planner-run.md#generate-report)하여 예측 및 권장 사항을 요약합니다.
5. [보고서 권장 사항](site-recovery-vmware-deployment-planner-analyze-report.md) 및 [비용 예측](site-recovery-vmware-deployment-planner-cost-estimation.md)을 분석합니다.

>[!NOTE]
> 기본적으로 이 도구는 VM에 대한 최대 1,000개의 보고서를 프로파일링하고 생성하도록 구성되어 있습니다. 이 제한은 ASRDeploymentPlanner.exe.config 파일에서 MaxVMsSupported 키 값을 늘려서 변경할 수 있습니다.

## <a name="plan-target-azure-requirements-and-capacity"></a>대상(Azure) 요구 사항 및 용량 계획

수집된 예측 및 권장 사항을 사용하여 대상 리소스 및 용량을 계획할 수 있습니다. VMware VM에 대한 Deployment Planner를 실행한 경우 다양한 [보고서 권장 사항](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations)을 사용하여 도움을 받을 수 있습니다.

- **호환 가능한 VM 수**: 이 숫자를 사용하여 재해 복구를 Azure로의 수행할 준비가 된 VM 수를 식별합니다. 네트워크 대역폭 및 Azure 코어 수에 대한 권장 사항은 이 숫자를 기반으로 합니다.
- **필요한 네트워크 대역폭**: 호환 가능한 VM의 델타 복제에 필요한 대역폭을 확인합니다. 
    - Deployment Planner를 실행하는 경우 원하는 RPO를 분 단위로 지정합니다. 권장 사항에는 100% 및 90%의 시간 동안 해당 RPO를 충족하는 데 필요한 대역폭이 표시됩니다. 
    - 네트워크 대역폭 권장 사항은 Deployment Planner에서 권장하는 총 구성 서버 및 프로세스 서버 수에 필요한 대역폭을 고려합니다.
- **필요한 Azure 코어 수**: 호환 가능한 VM 수에 따라 대상 Azure 지역에 필요한 코어 수를 확인합니다. 코어가 충분하지 않으면 장애 조치(failover) 시 Site Recovery에서 필요한 Azure VM을 만들 수 없습니다.
- **권장되는 VM 일괄 처리 크기**: 권장되는 일괄 처리 크기는 100%의 RPO를 충족하면서 일괄 처리에 대한 초기 복제를 기본적으로 72시간 이내에 완료하는 기능을 기반으로 합니다. 시간 값은 수정할 수 있습니다.

이러한 권장 사항을 사용하여 Azure 리소스, 네트워크 대역폭 및 VM 일괄 처리를 계획할 수 있습니다.

## <a name="plan-azure-subscriptions-and-quotas"></a>Azure 구독 및 할당량 계획

대상 구독에서 사용 가능한 할당량이 장애 조치(failover)를 처리하는 데 충분한지 확인하려고 합니다.

**Task** | **세부 정보** | **동작**
--- | --- | ---
**코어 수 확인** | 사용 가능한 할당량의 코어 수가 장애 조치(failover) 시 총 대상 수와 같거나 초과하지 않는 경우 장애 조치(failover)가 실패합니다. | VMware VM의 경우 대상 구독에 Deployment Planner의 코어 권장 사항을 충족하는 데 충분한 코어가 있는지 확인합니다.<br/><br/> 물리적 서버의 경우 Azure 코어 수가 수동 예측 값을 충족하는지 확인합니다.<br/><br/> 할당량을 확인하려면 Azure Portal > **구독** 에서 **사용량 + 할당량** 을 클릭합니다.<br/><br/> 할당량을 늘리는 방법에 대해 [자세히 알아보세요](../azure-portal/supportability/regional-quota-requests.md).
**장애 조치(failover) 제한 확인** | 장애 조치(failover) 수는 Site Recovery 장애 조치(failover) 제한을 초과하지 않아야 합니다. |  장애 조치(failover)가 제한을 초과하는 경우 구독을 추가하고 여러 구독으로 장애 조치(failover)하거나 구독 할당량을 늘릴 수 있습니다. 


### <a name="failover-limits"></a>장애 조치(failover) 제한

이 제한은 컴퓨터당 세 개의 디스크를 가정하여 Site Recovery에서 1시간 내에 지원하는 장애 조치(failover) 수를 나타냅니다.

무엇을 의미할까요? Azure VM을 시작하려면 Azure에서 일부 드라이버가 부팅 시작 상태이고 DHCP와 같은 서비스가 자동으로 시작되도록 설정해야 합니다.
- 이를 준수하는 컴퓨터에는 이미 이러한 설정이 적용되어 있습니다.
- Windows를 실행하는 컴퓨터의 경우 규정 준수를 사전에 확인하고, 필요한 경우 규정 준수 상태로 만들 수 있습니다. [자세한 정보를 알아보세요](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010).
- Linux 컴퓨터는 장애 조치(failover) 시에만 규정 준수 상태로 전환됩니다.

**컴퓨터에서 Azure를 준수하나요?** | **Azure VM 제한(관리 디스크 장애 조치(failover))**
--- | --- 
예 | 2000
예 | 1000

- 제한에서는 최소한의 다른 작업이 구독 대상 지역에서 진행 중이라고 가정합니다.
- 일부 Azure 지역은 더 작고 제한이 약간 낮을 수 있습니다.

## <a name="plan-infrastructure-and-vm-connectivity"></a>인프라 및 VM 연결 계획

Azure로 장애 조치(failover)되면 워크로드가 온-프레미스에서와 같이 작동하고 사용자가 Azure VM에서 실행되는 워크로드에 액세스할 수 있도록 지원해야 합니다.

- Active Directory 또는 DNS 온-프레미스 인프라를 Azure로 장애 조치(failover)하는 방법에 대해 [자세히 알아보세요](site-recovery-active-directory.md#test-failover-considerations).
- 장애 조치(failover) 후 Azure VM 연결을 준비하는 방법에 대해 [자세히 알아보세요](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).



## <a name="plan-for-source-capacity-and-requirements"></a>원본 용량 및 요구 사항 계획

용량 요구 사항을 충족하는 데 충분한 구성 서버와 스케일 아웃 프로세스 서버가 있어야 합니다. 대규모 배포를 시작할 때 단일 구성 서버 및 단일 스케일 아웃 프로세스 서버를 사용하여 시작합니다. 지정된 제한에 도달하면 서버를 추가합니다.

>[!NOTE]
> VMware VM의 경우 Deployment Planner에서 필요한 구성 및 프로세스 서버에 대한 몇 가지 권장 사항을 제공합니다. Deployment Planner 권장 사항을 따르는 대신 다음 절차에 포함된 표를 사용하는 것이 좋습니다. 


## <a name="set-up-a-configuration-server"></a>구성 서버 설정
 
구성 서버 용량은 데이터 이동 속도가 아니라 복제하는 컴퓨터 수에 따라 달라집니다. 추가 구성 서버가 필요한지 여부를 확인하려면 다음과 같이 정의된 VM 제한을 사용합니다.

**CPU** | **메모리** | **캐시 디스크** | **복제되는 컴퓨터 제한**
 --- | --- | --- | ---
vCPU 8대<br> 2개 소켓 * 4개 코어 @ 2.5GHz | 16GB | 600GB | 최대 550개 컴퓨터<br> 각 컴퓨터에 세 개의 100GB 디스크가 있다고 가정합니다.

- 이러한 제한은 OVF 템플릿을 사용하여 설정된 구성 서버를 기반으로 합니다.
- 제한은 구성 서버에서 기본적으로 실행되는 프로세스 서버를 사용하지 않는다고 가정합니다.

새 구성 서버를 추가해야 하는 경우 다음 지침을 따르세요.

- VMware VM 재해 복구의 경우 OVF 템플릿을 사용하여 [구성 서버를 설정](vmware-azure-deploy-configuration-server.md)합니다.
- 물리적 서버 또는 OVF 템플릿을 사용할 수 없는 VMware 배포 환경의 경우 수동으로 [구성 서버를 설정](physical-azure-set-up-source.md)합니다.

구성 서버를 설정할 때 다음 사항에 유의하세요.

- 구성 서버를 설정하는 경우 설정 후에는 구성 서버가 상주하는 구독 및 자격 증명 모음을 변경할 수 없으므로 이러한 항목을 고려해야 합니다. 자격 증명 모음을 변경해야 하는 경우 자격 증명 모음에서 구성 서버의 연결을 끊고 다시 등록해야 합니다. 이렇게 하면 자격 증명 모음에서 VM 복제가 중지됩니다.
- 여러 네트워크 어댑터를 사용하여 구성 서버를 설정하려는 경우 이 작업은 설정 중에 수행해야 합니다. 구성 서버를 자격 증명 모음에 등록한 후에는 이 작업을 수행할 수 없습니다.

## <a name="set-up-a-process-server"></a>프로세스 서버 설정

프로세스 서버 용량은 복제에 사용하도록 설정된 컴퓨터 수가 아니라 데이터 변동률의 영향을 받습니다.

- 대규모 배포의 경우 항상 하나 이상의 스케일 아웃 프로세스 서버가 있어야 합니다.
- 추가 서버가 필요한지 여부를 파악하려면 다음 표를 사용합니다.
- 사양이 가장 높은 서버를 추가하는 것이 좋습니다. 


**CPU** | **메모리** | **캐시 디스크** | **변동률**
 --- | --- | --- | --- 
vCPU 12대<br> 2개 소켓 * 6개 코어 @ 2.5GHz | 24GB | 1GB | 하루 최대 2TB

프로세스 서버를 다음과 같이 설정합니다.

1. [필수 구성 요소](vmware-azure-set-up-process-server-scale.md#prerequisites)를 검토하세요.
2. [포털](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)또는 [명령줄](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line)에서 서버를 설치합니다.
3. 새 서버를 사용하도록 복제된 컴퓨터를 구성합니다. 이미 복제 중인 컴퓨터가 있는 경우 다음과 같습니다.
    - 전체 프로세스 서버 워크로드를 새 프로세스 서버로 [이동](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server)할 수 있습니다.
    - 또는 특정 VM을 새 프로세스 서버로 [이동](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load)할 수 있습니다.



## <a name="enable-large-scale-replication"></a>대규모 복제 사용

용량이 계획되고 필요한 구성 요소와 인프라가 배포되면 많은 수의 VM에 대한 복제를 사용하도록 설정합니다.

1. 컴퓨터를 일괄 처리로 정렬합니다. 일괄 처리 내에서 VM에 대한 복제를 사용하도록 설정한 후 다음 일괄 처리로 이동합니다.

    - VMware VM의 경우 Deployment Planner 보고서의 [권장되는 VM 일괄 처리 크기](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication)를 사용할 수 있습니다.
    - 물리적 컴퓨터의 경우 데이터 크기와 양이 비슷한 컴퓨터 및 사용 가능한 네트워크 처리량을 기준으로 일괄 처리를 식별하는 것이 좋습니다. 이는 초기 복제를 거의 동일한 시간 내에 완료할 가능성이 높은 컴퓨터를 일괄 처리하기 위한 것입니다.
    
2. 컴퓨터에 대한 디스크 변동이 높거나 Deployment Planner의 제한을 초과하는 경우 복제할 필요가 없는 중요하지 않은 파일(예: 로그 덤프 또는 임시 파일)을 컴퓨터에서 이동할 수 있습니다. VMware VM의 경우 이러한 파일을 별도의 디스크로 이동한 다음, [해당 디스크를 복제에서 제외](vmware-azure-exclude-disk.md)할 수 있습니다.
3. 복제를 사용하도록 설정하기 전에 컴퓨터가 [복제 요구 사항](vmware-physical-azure-support-matrix.md#replicated-machines)을 충족하는지 확인합니다.
4. [VMware VM](vmware-azure-set-up-replication.md#create-a-policy) 또는 [물리적 서버](physical-azure-disaster-recovery.md#create-a-replication-policy)에 대한 복제 정책을 구성합니다.
5. [VMware VM](vmware-azure-enable-replication.md) 또는 [물리적 서버](physical-azure-disaster-recovery.md#enable-replication)에 대한 복제를 사용하도록 설정합니다. 이렇게 하면 선택한 컴퓨터에 대한 초기 복제가 시작됩니다.

## <a name="monitor-your-deployment"></a>배포 모니터링

첫 번째 VM 일괄 처리에 대한 복제가 시작되면 다음과 같이 배포 모니터링을 시작합니다.  

1. 복제된 컴퓨터의 상태를 모니터링할 재해 복구 관리자를 지정합니다.
2. 복제된 항목 및 인프라에 대한 [이벤트를 모니터링](site-recovery-monitor-and-troubleshoot.md)합니다.
3. 스케일 아웃 프로세스 서버의 [상태를 모니터링](vmware-physical-azure-monitor-process-server.md)합니다.
4. 더 쉬운 모니터링을 위해 이벤트에 대한 [이메일 알림](./site-recovery-monitor-and-troubleshoot.md#subscribe-to-email-notifications)을 받으려면 가입합니다.
5. 정기적인 [재해 복구 훈련](site-recovery-test-failover-to-azure.md)을 수행하여 모든 것이 예상대로 작동하는지 확인합니다.


## <a name="plan-for-large-scale-failovers"></a>대규모 장애 조치(failover) 계획

재해가 발생하는 경우 많은 수의 컴퓨터/워크로드를 Azure로 장애 조치(failover)해야 할 수 있습니다. 이러한 유형의 이벤트에 대해 다음과 같이 준비합니다.

장애 조치(failover)는 다음과 같이 미리 준비할 수 있습니다.

- 장애 조치(failover) 후 워크로드를 사용할 수 있고 사용자가 Azure VM에 액세스할 수 있도록 [인프라와 VM을 준비](#plan-infrastructure-and-vm-connectivity)합니다.
- 이 문서의 앞부분에 있는 [장애 조치(failover) 제한](#failover-limits)을 확인합니다. 장애 조치(failover)가 이러한 제한 내에 있는지 확인합니다.
- 정기적인 [재해 복구 훈련](site-recovery-test-failover-to-azure.md)을 실행합니다. 훈련을 통해 다음을 수행할 수 있습니다.
    - 장애 조치(failover) 전에 배포의 결함을 찾습니다.
    - 앱에 대한 엔드투엔드 RTO를 예측합니다.
    - 워크로드에 대한 엔드투엔드 RPO를 예측합니다.
    - IP 주소 범위 충돌을 식별합니다.
    - 훈련을 실행할 때 프로덕션 네트워크를 훈련에 사용하지 않고, 훈련이 완료될 때마다 테스트 장애 조치(failover)를 정리하는 것이 좋습니다.

대규모 장애 조치(failover)를 실행하려면 다음을 수행하는 것이 좋습니다.

1. 워크로드 장애 조치(failover)에 대한 복구 계획을 만듭니다.
    - 각 복구 계획은 최대 100개의 컴퓨터에 대한 장애 조치(failover)를 트리거할 수 있습니다.
    - [자세히 알아봅니다](recovery-plan-overview.md) .
2. Azure에서 수동 작업을 자동화하기 위해 Azure Automation Runbook 스크립트를 복구 계획에 추가합니다. 일반적인 작업에는 부하 분산 장치 구성, DNS 업데이트 등이 포함됩니다. [자세한 정보](site-recovery-runbook-automation.md)
2. 장애 조치(failover)하기 전에 Azure 환경 규정을 준수하도록 Windows 컴퓨터를 준비합니다. 규정을 준수하는 컴퓨터의 경우 [장애 조치(failover) 제한](#plan-azure-subscriptions-and-quotas)이 더 높습니다. Runbook에 대해 [자세히 알아보세요](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010).
4.  복구 계획과 함께 [Start-AzRecoveryServicesAsrPlannedFailoverJob](/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob) PowerShell cmdlet을 사용하여 장애 조치(failover)를 트리거합니다.



## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Site Recovery 모니터링](site-recovery-monitor-and-troubleshoot.md)