---
title: Azure Migrate 서버 마이그레이션에 대한 일반적인 질문
description: Azure Migrate 서버 마이그레이션을 사용하여 컴퓨터를 마이그레이션하는 일반적인 질문에 대한 답변을 얻습니다.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 08/28/2020
ms.openlocfilehash: 8d5feae71fc6b901f761e6a52805a7ac5fad04a4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128673543"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure Migrate 서버 마이그레이션: 일반적인 질문

이 문서에서는 Azure Migrate: 서버 마이그레이션 도구에 대한 일반적인 질문에 답변합니다. 다른 질문이 있는 경우 다음 리소스를 확인하세요.

- Azure Migrate에 대한 [일반적인 질문](resources-faq.md)
- [Azure Migrate 어플라이언스](common-questions-appliance.md)에 대한 질문
- [검색, 평가, 종속성 시각화](common-questions-discovery-assessment.md)에 대한 질문
- [Azure Migrate 포럼](https://aka.ms/AzureMigrateForum)에서 질문과 대답 보기

## <a name="general-questions"></a>일반적인 질문


### <a name="what-are-the-migration-options-in-azure-migrate-server-migration"></a>Azure Migrate: 서버 마이그레이션의 마이그레이션 옵션에는 어떤 것이 있나요?

Azure Migrate: 서버 마이그레이션 도구는 원본 서버 및 가상 머신을 Azure로 마이그레이션하는 두 가지 옵션인 에이전트 없는 마이그레이션 및 에이전트 기반 마이그레이션을 제공합니다.

선택한 마이그레이션 옵션에 관계없이 Azure Migrate: 서버 마이그레이션을 사용하여 서버를 마이그레이션하는 첫 번째 단계는 서버에 대한 복제를 시작하는 것입니다. 이렇게 하면 Azure에 대한 VM/서버 데이터의 초기 복제가 수행됩니다. 초기 복제가 완료되면 증분 데이터를 Azure로 마이그레이션하기 위해 진행 중인 복제(진행 중인 델타 동기화)가 설정됩니다. 작업이 델타 동기화 단계에 도달하면 언제든지 Azure로 마이그레이션하도록 선택할 수 있습니다.  

다음은 마이그레이션 옵션을 결정하는 동안 유의해야 할 몇 가지 고려 사항입니다.

**에이전트 없는 마이그레이션** 에서는 마이그레이션되는 원본 VM/서버에 소프트웨어(에이전트)를 배포할 필요가 없습니다. 에이전트 없는 옵션은 가상화 공급자가 제공하는 기능과 통합하여 복제를 오케스트레이션합니다.
에이전트 없는 복제 옵션은 [VMware VM](./tutorial-migrate-vmware.md) 및 [Hyper-V VM](./tutorial-migrate-hyper-v.md)에 사용할 수 있습니다.

**에이전트 기반 마이그레이션** 을 사용하려면 마이그레이션할 원본 VM/컴퓨터에 Azure Migrate 소프트웨어(에이전트)를 설치해야 합니다. 에이전트 기반 옵션은 복제 기능을 위한 가상화 플랫폼에 의존하지 않습니다. 따라서 x86/x64 아키텍처를 실행하는 모든 서버와 에이전트 기반 복제 방법으로 지원되는 운영 체제 버전에서 사용할 수 있습니다.

에이전트 기반 마이그레이션 옵션은 [VMware VM](./tutorial-migrate-vmware-agent.md), [Hyper-V VM](./tutorial-migrate-physical-virtual-machines.md), [물리적 서버](./tutorial-migrate-physical-virtual-machines.md), [AWS에서 실행되는 VM](./tutorial-migrate-aws-virtual-machines.md), VM, GCP에서 실행되는 VM 또는 다른 가상화 공급자에서 실행되는 VM에 사용할 수 있습니다. 에이전트 기반 마이그레이션은 마이그레이션을 위해 컴퓨터를 물리적 서버로 처리합니다.

에이전트 없는 마이그레이션은 지원되는 시나리오(VMware 및 Hyper-V)에 대한 에이전트 기반 복제 옵션보다 편의성과 단순성을 추가로 제공하지만 다음과 같은 사용 사례에 에이전트 기반 시나리오를 사용하는 것이 좋습니다.

- IOPS 제한 환경: 에이전트 없는 복제는 스냅샷을 사용하고 스토리지 IOPS/대역폭을 사용합니다. 사용자 환경의 스토리지/IOPS에 대한 제약 조건이 있는 경우 에이전트 기반 마이그레이션 방법을 사용하는 것이 좋습니다.
- vCenter Server가 없는 경우 VMware VM을 물리적 서버로 처리하고 에이전트 기반 마이그레이션 워크플로를 사용할 수 있습니다.

자세히 알아보려면 이 [문서](./server-migrate-overview.md)를 검토하여 VMware 마이그레이션을 위한 마이그레이션 옵션을 비교합니다.

### <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>Azure Migrate로 마이그레이션에 대해 지원되는 지역은 어디인가요?

[퍼블릭](migrate-support-matrix.md#supported-geographies-public-cloud) 및 [정부 클라우드](migrate-support-matrix.md#supported-geographies-azure-government)에 대해 지원되는 지역을 검토합니다.

### <a name="can-i-use-the-same-azure-migrate-project-to-migrate-to-multiple-regions"></a>동일한 Azure Migrate 프로젝트를 사용하여 여러 지역으로 마이그레이션할 수 있나요?

Azure Migrate 프로젝트에서 여러 지역에 대한 평가를 만들 수 있지만 하나의 Azure Migrate 프로젝트를 사용하여 하나의 Azure 지역에만 서버를 마이그레이션할 수 있습니다. 마이그레이션해야 하는 각 지역에 대한 추가 Azure Migrate 프로젝트를 만들 수 있습니다.

- 에이전트 없는 VMware 마이그레이션의 경우 첫 번째 복제를 사용하도록 설정하면 대상 지역이 잠깁니다.
- 에이전트 기반 마이그레이션(VMware, 물리적 서버 및 다른 클라우드 서버)의 경우 복제 어플라이언스를 설정하는 동안 포털에서 “리소스 만들기” 단추를 클릭하면 대상 지역이 잠깁니다.
- 에이전트 없는 Hyper-V 마이그레이션의 경우 Hyper-V 복제 공급자를 설정하는 동안 포털에서 “리소스 만들기” 단추를 클릭하면 대상 지역이 잠깁니다.

### <a name="can-i-use-the-same-azure-migrate-project-to-migrate-to-multiple-subscriptions"></a>동일한 Azure Migrate 프로젝트를 사용하여 여러 구독으로 마이그레이션할 수 있나요? 

예, Azure Migrate 프로젝트에 대해 동일한 대상 지역의 여러 구독으로 마이그레이션할 수 있습니다. 컴퓨터 또는 컴퓨터 집합에 대한 복제를 사용하도록 설정하는 동안 대상 구독을 선택할 수 있습니다. 대상 지역은 에이전트 없는 VMware 마이그레이션의 경우에는 첫 번째 복제 후에 잠기며, 에이전트 기반 마이그레이션 및 에이전트 없는 Hyper-V 마이그레이션의 경우에는 복제 어플라이언스 및 Hyper-V 공급자 설치 중에 잠깁니다.

### <a name="how-is-the-data-transmitted-from-on-prem-environment-to-azure-is-it-encrypted-before-transmission"></a>온-프레임 환경에서 Azure로 데이터를 전송하는 방법은 무엇인가요? 전송 전에 암호화되나요?

에이전트 없는 복제 사례의 Azure Migrate 어플라이언스는 업로드하기 전에 데이터를 압축하고 암호화합니다. 데이터는 https를 통한 보안 통신 채널을 통해 전송되며 TLS 1.2 이상을 사용합니다. 또한 Azure Storage는 데이터를 클라우드에 유지할 때 자동으로 암호화합니다(미사용 시 암호화).  

### <a name="can-i-use-the-recovery-services-vault-created-by-azure-migrate-for-disaster-recovery-scenarios"></a>Azure Migrate에서 만든 복구 서비스 자격 증명 모음을 재해 복구 시나리오에 사용할 수 있나요?
재해 복구 시나리오에는 Azure Migrate에서 만든 복구 서비스 자격 증명 모음을 사용하지 않는 것이 좋습니다. 그렇게 하면 Azure Migrate에서 복제 시작이 실패할 수 있습니다.

### <a name="what-is-the-difference-between-the-test-migration-and-migrate-operations"></a>테스트 마이그레이션과 마이그레이션 작업의 차이는 무엇인가요?

테스트 마이그레이션은 실제 마이그레이션 전에 마이그레이션을 테스트하고 유효성을 검사하는 방법을 제공합니다. 테스트 마이그레이션은 Azure의 샌드박스 환경에서 VM을 복제하는 테스트 복사본을 만들 수 있도록 하여 작동합니다. 샌드박스 환경은 사용자가 지정하는 테스트 가상 네트워크로 구분됩니다. 테스트 마이그레이션 작업은 중단되지 않으며, 애플리케이션이 계속해서 원본에서 실행되며, 격리된 샌드박스 환경에서 복제된 복사본에 대해 테스트를 수행할 수 있습니다. 마이그레이션 유효성을 검사하고, 앱 테스트를 수행하고, 실제 마이그레이션 전에 모든 문제를 해결하기 위해 필요에 따라 여러 테스트를 수행할 수 있습니다.


### <a name="is-there-a-rollback-option-for-azure-migrate"></a>Azure Migrate에 대한 롤백 옵션이 있나요?

테스트 마이그레이션 옵션을 사용하여 Azure에서 애플리케이션 기능 및 성능에 대한 유효성을 검사할 수 있습니다. 테스트 마이그레이션은 횟수 제한 없이 수행할 수 있으며 테스트 마이그레이션 작업을 통해 확신을 얻은 후 최종 마이그레이션을 실행할 수 있습니다. 테스트 마이그레이션은 실제 마이그레이션을 수행할 때까지 계속 작동하고 복제하는 온-프레미스 컴퓨터에 영향을 주지 않습니다. 테스트 마이그레이션 UAT 동안 오류가 발생한 경우 최종 마이그레이션을 연기하고 원본 VM/서버를 계속 실행하고 Azure에 복제하도록 선택할 수 있습니다. 오류를 해결한 후 최종 마이그레이션을 다시 시도할 수 있습니다.  
참고: Azure로 최종 마이그레이션을 수행하고 온-프레미스 원본 컴퓨터가 종료되면 Azure에서 온-프레미스 환경으로 롤백을 수행할 수 없습니다.

### <a name="can-i-select-the-virtual-network-and-subnet-to-use-for-test-migrations"></a>테스트 마이그레이션에 사용할 가상 네트워크 및 서브넷을 선택할 수 있나요?

테스트 마이그레이션에 대한 가상 네트워크를 선택할 수 있습니다. 서브넷은 다음 논리에 따라 자동으로 선택됩니다.

- 복제를 사용하도록 설정하는 동안 대상 서브넷(기본값 이외)을 입력으로 지정한 경우 Azure Migrate는 테스트 마이그레이션에 대해 선택한 가상 네트워크에서 동일한 이름의 서브넷을 사용하여 우선 순위를 지정합니다.
- 이름이 같은 서브넷을 찾을 수 없는 경우 Azure Migrate는 게이트웨이/Application Gateway/방화벽/베스천 서브넷이 아닌 알파벳순으로 사용 가능한 첫 번째 서브넷을 선택합니다.

### <a name="why-is-the-test-migration-button-disabled-for-my-server"></a>내 서버에 대해 테스트 마이그레이션 단추를 사용할 수 없는 이유는 무엇인가요?

테스트 마이그레이션 단추는 다음 시나리오에서 사용 안 함 상태일 수 있습니다.

- VM에 대한 IR(초기 복제)가 완료될 때까지 테스트 마이그레이션을 시작할 수 없습니다. 테스트 마이그레이션 단추는 IR 프로세스가 완료될 때까지 사용하지 않도록 설정됩니다. VM이 델타 동기화 단계에 있으면 테스트 마이그레이션을 수행할 수 있습니다.
- 테스트 마이그레이션이 이미 완료되었지만 해당 VM에 대해 테스트 마이그레이션 정리가 수행되지 않은 경우 이 단추를 사용할 수 없습니다. 테스트 마이그레이션 정리를 수행하고 작업을 다시 시도합니다.

### <a name="what-happens-if-i-dont-clean-up-my-test-migration"></a>테스트 마이그레이션을 정리하지 않으면 어떻게 되나요?

테스트 마이그레이션은 복제된 데이터를 사용하여 테스트 Azure VM을 만들어 실제 마이그레이션을 시뮬레이션합니다. 서버는 “-test” 접미사가 있는 대상 리소스 그룹(복제를 사용하는 동안 선택됨)에 복제된 데이터의 지정 시간 복사본과 함께 배포됩니다. 테스트 마이그레이션은 마이그레이션 후 문제를 최소화할 수 있도록 서버 기능의 유효성을 검사하기 위한 것입니다. 테스트 마이그레이션이 테스트 후 정리되지 않으면 테스트 가상 머신은 Azure에서 계속 실행되며 요금이 발생합니다. 테스트 마이그레이션 후 정리하려면 서버 마이그레이션 도구의 컴퓨터 복제 보기로 이동하여 컴퓨터에서 '테스트 마이그레이션 정리' 작업을 사용합니다.


### <a name="how-do-i-know-if-my-vm-was-successfully-migrated"></a>내 VM이 성공적으로 마이그레이션되었는지 확인하려면 어떻게 해야 하나요?

VM/서버를 성공적으로 마이그레이션한 후에는 Virtual Machines 페이지에서 VM을 보고 관리할 수 있습니다. 마이그레이션된 VM에 연결하여 유효성을 검사합니다.
또는 작업의 ‘작업 상태’를 검토하여 마이그레이션이 성공적으로 완료되었는지 확인할 수 있습니다. 오류가 표시되면 해결한 후 마이그레이션 작업을 다시 수행합니다.

### <a name="what-happens-if-i-dont-stop-replication-after-migration"></a>마이그레이션 후 복제를 중지하지 않으면 어떻게 되나요?

복제를 중지하면 Azure Migrate: 서버 마이그레이션 도구는 복제용으로 만들어진 관리 디스크를 구독에서 정리합니다. 마이그레이션 후 복제를 중지하지 않으면 이러한 디스크에 대한 요금이 계속 청구됩니다. 복제 중지는 이전에 마이그레이션된 컴퓨터에 연결된 디스크에 영향을 주지 않습니다.

### <a name="how-can-i-migrate-uefi-based-machines-to-azure-as-azure-generation-1-vms"></a>UEFI 기반 컴퓨터를 Azure 1세대 VM으로 Azure로 마이그레이션하려면 어떻게 해야 하나요?
Azure Migrate: 서버 마이그레이션 도구는 UEFI 기반 컴퓨터를 Azure 2세대 VM으로 Azure로 마이그레이션합니다. Azure 1세대 VM으로 마이그레이션하려면 복제를 시작하기 전에 부팅 유형을 BIOS로 변환한 다음, Azure Migrate: 서버 마이그레이션 도구를 사용하여 Azure로 마이그레이션합니다.

### <a name="does-azure-migrate-convert-uefi-based-machines-to-bios-based-machines-and-migrate-them-to-azure-as-azure-generation-1-vms"></a>Azure Migrate가 UEFI 기반 컴퓨터를 BIOS 기반 컴퓨터로 변환하고 Azure 1세대 VM으로 Azure로 마이그레이션하나요?
Azure Migrate: 서버 마이그레이션 도구는 모든 UEFI 기반 컴퓨터를 Azure 2세대 VM으로 Azure로 마이그레이션합니다. UEFI 기반 VM을 BIOS 기반 VM으로 변환하는 것은 더 이상 지원되지 않습니다. 모든 BIOS 기반 머신은 Azure 1세대 VM으로만 Azure로 마이그레이션됩니다.

### <a name="which-operating-systems-are-supported-for-migration-of-uefi-based-machines-to-azure"></a>UEFI 기반 컴퓨터를 Azure로 마이그레이션하는 데 지원되는 운영 체제는 무엇인가요?

| **UEFI 기반 컴퓨터에 지원되는 운영 체제** | **에이전트 없는 VMware에서 Azure로**                                                                                                             | **에이전트 없는 Hyper-V에서 Azure로** | **Azure에 대한 에이전트 기반 VMware, 물리적 클라우드 및 기타 클라우드** |
| ------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ | ---------------------------------------------------------- |
| Windows Server 2019, 2016, 2012 R2, 2012                | Y                                                                                                                                         | Y                              | Y                                                          |
| Windows 10 Pro, Windows 10 Enterprise                   | Y                                                                                                                                         | Y                              | Y                                                          |
| SUSE Linux Enterprise Server 15 SP1                     | Y                                                                                                                                         | Y                              | Y                                                          |
| SUSE Linux Enterprise Server 12 SP4                     | Y                                                                                                                                         | Y                              | Y                                                          |
| Ubuntu Server 16.04, 18.04, 19.04, 19.10                | Y                                                                                                                                         | Y                              | Y                                                          |
| RHEL 8.1, 8.0, 7.8, 7.7, 7.6, 7.5, 7.4, 7.0, 6.x        | Y<br>                 _RHEL 8.x에는 [수동 준비](./prepare-for-migration.md#linux-machines)_ 가 필요합니다.   | Y                              | Y                                                          |
| Cent OS 8.1, 8.0, 7.7, 7.6, 7.5, 7.4, 6.x               | Y<br>_Cent OS 8.x에는 [수동 준비](./prepare-for-migration.md#linux-machines)_ 가 필요합니다. | Y                              | Y                                                          |
| Oracle Linux 7.7, 7.7-CI                                |  Y                                                                                                                                        | Y                              | Y                                                          |

### <a name="can-i-migrate-active-directory-domain-controllers-using-azure-migrate"></a>Azure Migrate를 사용하여 Active Directory 도메인 컨트롤러를 마이그레이션할 수 있나요?

서버 마이그레이션 도구는 애플리케이션에 의존하지 않으며 대부분의 애플리케이션에서 작동합니다. 서버 마이그레이션 도구를 사용하여 서버를 마이그레이션하면 서버에 설치된 모든 애플리케이션이 함께 마이그레이션됩니다. 그러나 일부 애플리케이션의 경우 서버 마이그레이션 이외의 다른 마이그레이션 방법이 마이그레이션에 더 적합할 수 있습니다.  Active Directory의 경우 온-프레미스 사이트가 Azure 환경에 연결된 하이브리드 환경에서는 Azure에 도메인 컨트롤러를 추가하고 Active Directory 복제를 설정하여 Directory를 Azure로 확장할 수 있습니다. 자체 도메인 컨트롤러를 요구하거나 샌드박스 환경에서 애플리케이션을 테스트하는 Azure의 격리된 환경으로 마이그레이션하는 경우 서버 마이그레이션 도구를 사용하여 서버를 마이그레이션할 수 있습니다.


### <a name="can-i-upgrade-my-os-while-migrating"></a>마이그레이션하는 동안 OS를 업그레이드할 수 있나요?

Azure Migrate: 서버 마이그레이션 도구는 현재와 유사한 마이그레이션만 지원합니다. 이 도구는 마이그레이션 중 OS 버전 업그레이드를 지원하지 않습니다. 마이그레이션된 컴퓨터는 원본 컴퓨터와 동일한 OS를 갖게 됩니다.

### <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>VMware VM을 마이그레이션하려면 VMware vCenter가 필요한가요?

VMware 에이전트 기반 또는 에이전트 없는 마이그레이션을 사용하여 [VMware VM을 마이그레이션](server-migrate-overview.md)하려면 VM이 있는 ESXi 호스트를 vCenter Server에서 관리해야 합니다. vCenter Server가 없는 경우 실제 서버로 마이그레이션하여 VMware VM을 마이그레이션할 수 있습니다. [자세히 알아보기](migrate-support-matrix-physical-migration.md).

### <a name="can-i-consolidate-multiple-source-vms-into-one-vm-while-migrating"></a>마이그레이션하는 동안 여러 원본 VM을 하나의 VM에 통합할 수 있나요?

Azure Migrate 서버 마이그레이션 기능은 현재와 유사한 마이그레이션을 지원합니다. 마이그레이션의 일부로 서버 통합 또는 운영 체제 업그레이드는 지원하지 않습니다. 

### <a name="will-windows-server-2008-and-2008-r2-be-supported-in-azure-after-migration"></a>마이그레이션 후 Windows Server 2008 및 2008 R2가 Azure에서 지원되나요?

온-프레미스 Windows Server 2008 및 2008 R2 서버를 Azure Virtual Machines로 마이그레이션하고 가상 머신 실행 비용을 초과하는 추가 비용 없이 지원 종료 날짜 이후 3년 동안 연장된 보안 업데이트를 받을 수 있습니다. Azure Migrate: 서버 마이그레이션 도구를 사용하여 Windows Server 2008 및 2008 R2 워크로드를 마이그레이션할 수 있습니다.

### <a name="how-do-i-migrate-windows-server-2003-running-on-vmwarehyper-v-to-azure"></a>VMware/Hyper-V에서 실행되는 Windows Server 2003을 Azure로 마이그레이션하려면 어떻게 해야 하나요?

[Windows Server 2003 추가 지원](/troubleshoot/azure/virtual-machines/run-win-server-2003#microsoft-windows-server-2003-end-of-support)은 2015년 7월 14일에 종료되었습니다.  Azure 지원 팀은 Azure에서 Windows Server 2003을 실행하는 것과 관련된 문제를 해결하기 위해 지원을 계속하고 있습니다. 하지만 이 지원은 OS 수준의 문제 해결 또는 패치가 필요하지 않은 문제로 제한됩니다.
최신 버전의 Windows Server를 실행하는 Azure 인스턴스로 애플리케이션을 마이그레이션하여 Azure 클라우드의 유연성과 안정성을 효과적으로 활용하는 것이 좋습니다.

그러나 Windows Server 2003을 Azure로 마이그레이션하기로 선택한 경우 Windows Server가 VMware 또는 Hyper-V에서 실행되는 VM이면 Azure Migrate: 서버 마이그레이션 도구를 사용할 수 있습니다. [마이그레이션을 위해 Windows Server 2003 컴퓨터 준비](./prepare-windows-server-2003-migration.md) 문서를 참조하세요.

## <a name="agentless-vmware-migration"></a>에이전트 없는 VMware 마이그레이션
### <a name="how-does-agentless-migration-work"></a>에이전트 없는 마이그레이션은 어떻게 작동하나요?

Azure Migrate: 서버 마이그레이션에서는 VMware 가상 머신 및 Windows 또는 Linux를 실행하는 Hyper-V 가상 머신의 마이그레이션에 대한 에이전트 없는 복제 옵션을 제공합니다. 이 도구는 또한 VMware, Hyper-V, AWS, GCP 등의 x86/x64 가상 머신뿐만 아니라 물리적 서버를 마이그레이션하는 데 사용할 수 있는 Windows 및 Linux 서버를 위해 추가 에이전트 기반 복제 옵션을 제공합니다. 에이전트 기반 복제 옵션은 마이그레이션되는 서버/가상 머신에 에이전트 소프트웨어를 설치해야 하는 반면, 에이전트 없는 옵션은 가상 머신 자체에 소프트웨어를 설치할 필요가 없으므로 에이전트 기반 복제 옵션보다 편의성과 단순성을 추가로 제공합니다.

에이전트 없는 복제 옵션은 가상화 공급자(VMware, Hyper-V)에서 제공하는 메커니즘을 사용하여 작동합니다. VMware 가상 머신의 경우 에이전트 없는 복제 메커니즘은 VMware 스냅샷과 VMware 변경된 블록 추적 기술을 사용하여 가상 머신 디스크에서 데이터를 복제합니다. 이 메커니즘은 여러 백업 제품에서 사용하는 메커니즘과 비슷합니다. Hyper-V 가상 머신의 경우 에이전트 없는 복제 메커니즘에서는 VM 스냅샷과 Hyper-V 복제본의 변경 내용 추적 기능을 사용하여 가상 머신 디스크의 데이터를 복제합니다.

가상 머신의 복제를 구성하는 경우 먼저 초기 복제 단계를 진행합니다. 초기 복제 중에 VM 스냅샷이 생성되고 스냅샷 디스크의 전체 데이터 사본이 구독의 관리 디스크에 복제됩니다. VM의 초기 복제가 완료되면 복제 프로세스가 증분 복제(델타 복제) 단계로 전환됩니다. 증분 복제 단계에서는 마지막으로 완료된 복제 주기 이후에 발생한 데이터 변경 내용이 주기적으로 복제되어 복제본 관리 디스크에 적용되므로 복제가 VM에서 발생하는 변경 내용과 동기화된 상태로 유지됩니다. VMware 가상 머신의 경우 VMware 변경된 블록 추적 기술을 사용하여 복제 주기 간의 변경 내용을 추적합니다. 복제 주기가 시작될 때 VM 스냅샷이 생성되며 변경된 블록 추적은 현재 스냅샷과 마지막으로 성공적으로 복제된 스냅샷 간의 변경 사항을 가져오는 데 사용됩니다. VM에 대한 복제를 동기화 상태로 유지하려면 마지막으로 완료된 복제 주기 이후 변경된 데이터만 복제해야 합니다. 각 복제 주기가 끝나면 스냅샷이 해제되고 가상 머신에 대해 스냅샷 통합이 수행됩니다. 마찬가지로 Hyper-V 가상 머신의 경우 Hyper-V 복제본 변경 내용 추적 엔진을 사용하여 연속 복제 주기 간의 변경 내용을 추적합니다.
복제 가상 머신에서 마이그레이션 작업을 수행할 때 온-프레미스 가상 머신을 종료하고 하나의 최종 증분 복제를 수행하여 데이터 손실이 없도록 할 수 있습니다. 마이그레이션 옵션을 수행할 때 가상 머신에 해당하는 복제본 관리 디스크를 사용하여 Azure에서 가상 머신을 만듭니다.

시작하려면 [VMware 에이전트 없는 마이그레이션](./tutorial-migrate-vmware.md) 및 [Hyper-V 에이전트 없는 마이그레이션](./tutorial-migrate-hyper-v.md) 자습서를 참조하세요.

### <a name="how-do-i-gauge-the-bandwidth-requirement-for-my-migrations"></a>마이그레이션에 필요한 대역폭을 어떻게 측정하나요?

Azure로의 데이터 복제에 대한 대역폭은 다양한 요인에 따라 달라지며 온-프레미스 Azure Migrate 어플라이언스에서 Azure로 데이터를 읽고 복제할 수 있는 속도의 기능입니다. 복제는 초기 복제 및 델타 복제의 두 단계로 이루어집니다.

VM에 대한 복제가 시작되면 초기 복제 주기가 발생하며 이때 디스크의 전체 복사본이 복제됩니다. 초기 복제를 완료한 후에는 증분 복제 주기(델타 주기)가 주기적으로 예약되어 이전 복제 주기 이후에 발생한 변경 내용을 전송합니다.

웨이브에서 이동해야 하는 데이터의 양과 초기 복제가 완료되기를 원하는 시간을 기반으로 대역폭 요구 사항을 계산할 수 있습니다(이상적으로는 실제 마이그레이션 기간보다 최소 3~4일 전에 초기 복제를 완료하여 실제 기간 이전에 테스트 마이그레이션을 수행하고 해당 기간 동안 가동 중지 시간을 최소로 유지하는 데 충분한 시간을 제공하는 것이 좋습니다).

다음 수식을 사용하여 에이전트 없는 VMware VM 마이그레이션에 필요한 대역폭 또는 시간을 예측할 수 있습니다.

초기 복제를 완료하는 데 걸리는 시간 = {디스크 크기(또는 가능한 경우 사용된 크기) * 0.7(30% 압축 평균 – 보수적으로 추정)} / 복제에 사용할 수 있는 대역폭.

### <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>에이전트 없는 VMware 복제에 Azure Migrate 어플라이언스를 사용할 때 복제를 제한하려면 어떻게 해야 하나요?  

NetQosPolicy를 사용하여 제한할 수 있습니다. 이 제한은 Azure Migrate 어플라이언스의 아웃 바운드 연결에만 적용 됩니다. 예를 들면 다음과 같습니다.

NetQosPolicy에서 사용할 AppNamePrefix는 "GatewayWindowsService.exe"입니다. 다음과 같은 정책을 만들어 어플라이언스에서 복제 트래픽을 제한하는 정책을 Azure Migrate 어플라이언스에서 만들 수 있습니다.

```powershell
New-NetQosPolicy -Name "ThrottleReplication" -AppPathNameMatchCondition "GatewayWindowsService.exe" -ThrottleRateActionBitsPerSecond 1MB
```

일정에 따라 복제 대역폭을 늘리고 줄이려면 Windows 예약 작업을 활용하여 필요에 따라 대역폭의 크기를 조정할 수 있습니다. 한 작업은 대역폭을 줄이는 데 사용되고 다른 작업은 대역폭을 늘리는 데 사용됩니다.
참고: 아래 명령을 실행하기 전에 위에서 설명한 NetQosPolicy를 만들어야 합니다.
```powershell
#Replace with an account part of the local Administrators group
$User = "localVmName\userName"

#Set the task names
$ThrottleBandwidthTask = "ThrottleBandwidth"
$IncreaseBandwidthTask = "IncreaseBandwidth"

#Create a directory to host PowerShell scaling scripts
if (!(Test-Path "C:\ReplicationBandwidthScripts"))
{
 New-Item -Path "C:\" -Name "ReplicationBandwidthScripts" -Type Directory
}

#Set your minimum bandwidth to be used during replication by changing the ThrottleRateActionBitsPerSecond parameter
#Currently set to 10 MBps
New-Item C:\ReplicationBandwidthScripts\ThrottleBandwidth.ps1
Set-Content C:\ReplicationBandwidthScripts\ThrottleBandwidth.ps1 'Set-NetQosPolicy -Name "ThrottleReplication" -ThrottleRateActionBitsPerSecond 10MB'
$ThrottleBandwidthScript = "C:\ReplicationBandwidthScripts\ThrottleBandwidth.ps1"

#Set your maximum bandwidth to be used during replication by changing the ThrottleRateActionBitsPerSecond parameter
#Currently set to 1000 MBps
New-Item C:\ReplicationBandwidthScripts\IncreaseBandwidth.ps1
Set-Content C:\ReplicationBandwidthScripts\IncreaseBandwidth.ps1 'Set-NetQosPolicy -Name "ThrottleReplication" -ThrottleRateActionBitsPerSecond 1000MB'
$IncreaseBandwidthScript = "C:\ReplicationBandwidthScripts\IncreaseBandwidth.ps1"

#Timezone set on the Azure Migrate Appliance (VM) will be used; change the frequency to meet your needs
#In this example, the bandwidth is being throttled every weekday at 8:00 AM local time
#The bandwidth is being increased every weekday at 6:00 PM local time
$ThrottleBandwidthTrigger = New-ScheduledTaskTrigger -Weekly -DaysOfWeek Monday,Tuesday,Wednesday,Thursday,Friday -At 8:00am
$IncreaseBandwidthTrigger = New-ScheduledTaskTrigger -Weekly -DaysOfWeek Monday,Tuesday,Wednesday,Thursday,Friday -At 6:00pm

#Setting the task action to execute the scripts
$ThrottleBandwidthAction = New-ScheduledTaskAction -Execute "PowerShell.exe" -Argument "-executionpolicy bypass -noprofile -file $ThrottleBandwidthScript" 
$IncreaseBandwidthAction = New-ScheduledTaskAction -Execute "PowerShell.exe" -Argument "-executionpolicy bypass -noprofile -file $IncreaseBandwidthScript" 

#Creating the Scheduled tasks
Register-ScheduledTask -TaskName $ThrottleBandwidthTask -Trigger $ThrottleBandwidthTrigger -User $User -Action $ThrottleBandwidthAction -RunLevel Highest -Force
Register-ScheduledTask -TaskName $IncreaseBandwidthTask -Trigger $IncreaseBandwidthTrigger -User $User -Action $IncreaseBandwidthAction -RunLevel Highest -Force
```

### <a name="how-does-churn-rate-affect-agentless-replication"></a>변동률은 에이전트 없는 복제에 어떤 영향을 주나요?

에이전트 없는 복제가 데이터를 접으므로 *변동 패턴* 이 *변동* 보다 더욱 중요합니다. 파일이 계속해서 쓰여질 때 속도는 큰 영향을 미치지 않습니다. 그러나 다른 모든 섹터가 기록되는 패턴으로 인해 다음 주기에서 높은 변동이 발생합니다. 전송하는 데이터의 양을 최소화하기 때문에 다음 주기를 예약하기 전에 데이터를 최대한 많이 접을 수 있습니다.

### <a name="how-frequently-is-a-replication-cycle-scheduled"></a>복제 주기는 얼마나 자주 예약하나요?

다음 복제 주기를 예약하는 공식은 (이전 주기 시간/2) 또는 1시간 중 더 큰 값입니다.

예를 들어 VM이 델타 주기에 4시간이 걸리는 경우 다음 주기는 다음 시간이 아니라 2시간 안에 예약됩니다. 첫 번째 델타 주기가 즉시 예약되는 경우 초기 복제 직후에 프로세스가 다르게 수행됩니다.

### <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliances"></a>vCenter Server에서 VM을 검색하기 위해 둘 이상의 어플라이언스를 배포했습니다. 그러나 VM을 마이그레이션하려고 하면 어플라이언스 중 하나에 해당하는 VM만 표시됩니다.

여러 어플라이언스를 설정하는 경우 제공된 vCenter 계정에 있는 VM 간에 겹치는 부분이 없어야 합니다. 이러한 겹치는 검색은 지원되지 않는 시나리오입니다.


### <a name="how-does-agentless-replication-affect-vmware-servers"></a>에이전트 없는 복제는 VMware 서버에 어떤 영향을 미치나요?

에이전트 없는 복제는 VMware vCenter Server 및 VMware ESXi 호스트에 성능에 어느 정도 영향을 미칩니다. 에이전트 없는 복제는 스냅샷을 사용하므로 일부 IOPS 스토리지 대역폭이 필요하도록 스토리지에 IOPS를 사용합니다. 사용자 환경의 스토리지 또는 IOP에 대한 제약 조건이 있는 경우 에이전트 없는 복제를 사용하지 않는 것이 좋습니다.


## <a name="agent-based-migration"></a>에이전트 기반 마이그레이션

### <a name="how-can-i-migrate-my-aws-ec2-instances-to-azure"></a>AWS EC2 인스턴스를 Azure로 마이그레이션하려면 어떻게 해야 하나요?

이 [문서](./tutorial-migrate-aws-virtual-machines.md)를 검토하여 AWS EC2 인스턴스를 검색, 평가 및 Azure로 마이그레이션합니다.

### <a name="how-does-agent-based-migration-work"></a>에이전트 기반 마이그레이션은 어떻게 작동하나요?

VMware 가상 머신 및 Hyper-V 가상 머신에 대한 에이전트 없는 마이그레이션 옵션 외에도 서버 마이그레이션 도구는 물리적 서버에서 실행되거나 VMware, Hyper-V, AWS, Google Cloud Platform 등에서 x86/x64 가상 머신으로 실행되는 Windows 및 Linux 서버를 마이그레이션하는 에이전트 기반 마이그레이션 옵션을 제공합니다.

에이전트 기반 마이그레이션 방법은 마이그레이션 중인 서버에 설치된 에이전트 소프트웨어를 사용하여 서버 데이터를 Azure에 복제합니다. 복제 프로세스는 에이전트가 복제 데이터를 복제 어플라이언스 또는 구성 서버라고 하는 전용 복제 서버(또는 스케일 아웃 프로세스 서버)로 릴레이하는 오프로드 아키텍처를 사용합니다. 에이전트 기반 마이그레이션 옵션의 작동 방식에 대해 [자세히 알아봅니다](./agent-based-migration-architecture.md). 

참고: 복제 어플라이언스는 Azure Migrate 검색 어플라이언스와 다르며 별도/전용 시스템에 설치해야 합니다.

### <a name="where-should-i-install-the-replication-appliance-for-agent-based-migrations"></a>에이전트 기반 마이그레이션을 위한 복제 어플라이언스는 어디에 설치해야 하나요?

복제 어플라이언스는 전용 컴퓨터에 설치해야 합니다. 복제하려는 원본 머신이나 이전에 설치했을 수 있는 Azure Migrate 어플라이언스(검색 및 평가에 사용)에 복제 어플라이언스를 설치하면 안 됩니다. 자세한 내용은 [가이드](./tutorial-migrate-physical-virtual-machines.md)를 따르세요.

### <a name="can-i-migrate-aws-vms-running-amazon-linux-operating-system"></a>Amazon Linux 운영 체제를 실행하는 AWS VM을 마이그레이션할 수 있나요?

Amazon Linux OS는 AWS에서만 지원되므로 Amazon Linux를 실행하는 VM은 있는 그대로 마이그레이션할 수 없습니다.
Amazon Linux에서 실행되는 워크로드를 마이그레이션하려면 Azure에서 CentOS/RHEL VM을 실행하고 관련 워크로드 마이그레이션 방법을 사용하여 AWS Linux 머신에서 실행되는 워크로드를 마이그레이션할 수 있습니다. 예를 들어 워크로드에 따라 웹 서버에 대한 데이터베이스 또는 배포 도구 등의 마이그레이션에 도움이 되는 워크로드별 도구가 있을 수 있습니다.

### <a name="how-do-i-gauge-the-bandwidth-requirement-for-my-migrations"></a>마이그레이션에 필요한 대역폭을 어떻게 측정하나요?

Azure로의 데이터 복제에 대한 대역폭은 다양한 요인에 따라 달라지며 온-프레미스 Azure Migrate 어플라이언스에서 Azure로 데이터를 읽고 복제할 수 있는 속도의 기능입니다. 복제는 초기 복제 및 델타 복제의 두 단계로 이루어집니다.

VM에 대한 복제가 시작되면 초기 복제 주기가 발생하며 이때 디스크의 전체 복사본이 복제됩니다. 초기 복제를 완료한 후에는 증분 복제 주기(델타 주기)가 주기적으로 예약되어 이전 복제 주기 이후에 발생한 변경 내용을 전송합니다.

에이전트 기반 복제 방법의 경우 Deployment Planner는 데이터 변동에 대한 환경을 프로파일링하고 필요한 대역폭 요구 사항을 예측하는 데 도움이 될 수 있습니다. 자세한 내용은 이 [문서](./agent-based-migration-architecture.md#plan-vmware-deployment)를 참조하세요.

## <a name="agentless-hyper-v-migration"></a>에이전트 없는 Hyper-V 마이그레이션

### <a name="how-does-agentless-migration-work"></a>에이전트 없는 마이그레이션은 어떻게 작동하나요?

Azure Migrate: 서버 마이그레이션에서는 VMware 가상 머신 및 Windows 또는 Linux를 실행하는 Hyper-V 가상 머신의 마이그레이션에 대한 에이전트 없는 복제 옵션을 제공합니다. 이 도구는 또한 VMware, Hyper-V, AWS, GCP 등의 x86/x64 가상 머신뿐만 아니라 물리적 서버를 마이그레이션하는 데 사용할 수 있는 Windows 및 Linux 서버를 위해 추가 에이전트 기반 복제 옵션을 제공합니다. 에이전트 기반 복제 옵션은 마이그레이션되는 서버/가상 머신에 에이전트 소프트웨어를 설치해야 하는 반면, 에이전트 없는 옵션은 가상 머신 자체에 소프트웨어를 설치할 필요가 없으므로 에이전트 기반 복제 옵션보다 편의성과 단순성을 추가로 제공합니다.

에이전트 없는 복제 옵션은 가상화 공급자(VMware, Hyper-V)에서 제공하는 메커니즘을 사용하여 작동합니다.  Hyper-V 가상 머신의 경우 에이전트 없는 복제 메커니즘에서는 VM 스냅샷과 Hyper-V 복제본의 변경 내용 추적 기능을 사용하여 가상 머신 디스크의 데이터를 복제합니다.

가상 머신의 복제를 구성하는 경우 먼저 초기 복제 단계를 진행합니다. 초기 복제 중에 VM 스냅샷이 생성되고 스냅샷 디스크의 전체 데이터 사본이 구독의 관리 디스크에 복제됩니다. VM의 초기 복제가 완료되면 복제 프로세스가 증분 복제(델타 복제) 단계로 전환됩니다. 증분 복제 단계에서는 마지막으로 완료된 복제 주기 이후에 발생한 데이터 변경 내용이 주기적으로 복제되어 복제본 관리 디스크에 적용되므로 복제가 VM에서 발생하는 변경 내용과 동기화된 상태로 유지됩니다. VMware 가상 머신의 경우 VMware 변경된 블록 추적 기술을 사용하여 복제 주기 간의 변경 내용을 추적합니다. 복제 주기가 시작될 때 VM 스냅샷이 생성되며 변경된 블록 추적은 현재 스냅샷과 마지막으로 성공적으로 복제된 스냅샷 간의 변경 사항을 가져오는 데 사용됩니다. VM에 대한 복제를 동기화 상태로 유지하려면 마지막으로 완료된 복제 주기 이후 변경된 데이터만 복제해야 합니다. 각 복제 주기가 끝나면 스냅샷이 해제되고 가상 머신에 대해 스냅샷 통합이 수행됩니다. 마찬가지로 Hyper-V 가상 머신의 경우 Hyper-V 복제본 변경 내용 추적 엔진을 사용하여 연속 복제 주기 간의 변경 내용을 추적합니다.

복제 가상 머신에서 마이그레이션 작업을 수행할 때 온-프레미스 가상 머신을 종료하고 하나의 최종 증분 복제를 수행하여 데이터 손실이 없도록 할 수 있습니다. 마이그레이션 옵션을 수행할 때 가상 머신에 해당하는 복제본 관리 디스크를 사용하여 Azure에서 가상 머신을 만듭니다.

시작하려면 [Hyper-V 에이전트 없는 마이그레이션](./tutorial-migrate-hyper-v.md) 자습서를 참조하세요.

### <a name="how-do-i-gauge-the-bandwidth-requirement-for-my-migrations"></a>마이그레이션에 필요한 대역폭을 어떻게 측정하나요?

Azure로의 데이터 복제에 대한 대역폭은 다양한 요인에 따라 달라지며 온-프레미스 Azure Migrate 어플라이언스에서 Azure로 데이터를 읽고 복제할 수 있는 속도의 기능입니다. 복제는 초기 복제 및 델타 복제의 두 단계로 이루어집니다.

VM에 대한 복제가 시작되면 초기 복제 주기가 발생하며 이때 디스크의 전체 복사본이 복제됩니다. 초기 복제를 완료한 후에는 증분 복제 주기(델타 주기)가 주기적으로 예약되어 이전 복제 주기 이후에 발생한 변경 내용을 전송합니다.

웨이브에서 이동해야 하는 데이터의 양과 초기 복제가 완료되기를 원하는 시간을 기반으로 대역폭 요구 사항을 계산할 수 있습니다(이상적으로는 실제 마이그레이션 기간보다 최소 3~4일 전에 초기 복제를 완료하여 실제 기간 이전에 테스트 마이그레이션을 수행하고 해당 기간 동안 가동 중지 시간을 최소로 유지하는 데 충분한 시간을 제공하는 것이 좋습니다).

## <a name="next-steps"></a>다음 단계

[Azure Migrate 개요](migrate-services-overview.md)를 참조하세요.
