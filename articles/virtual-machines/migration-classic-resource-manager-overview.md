---
title: 클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션 개요
description: 플랫폼 지원 방식으로 클래식에서 Azure Resource Manager로 리소스를 마이그레이션하는 과정을 안내합니다.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 575ef3f6df84c62e1ac62d1981b6c2e9ed60aae2
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538599"
---
# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션

> [!IMPORTANT]
> 현재 IaaS VM의 90%가 [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)를 사용하고 있습니다. 2020년 2월 28일부터 클래식 VM은 더 이상 사용되지 않으며 2023년 3월 1일에 완전히 사용 중지될 예정입니다. 사용 중단 및 [그 영향](classic-vm-deprecation.md#how-does-this-affect-me)에 대해 [자세히 알아보세요]( https://aka.ms/classicvmretirement).



이 문서에서는 플랫폼 지원 마이그레이션 도구에 대한 개요를 제공하고, 클래식이라고도 하는 ASM(Azure Service Manager)에서 ARM(Azure Resource Manager) 배포 모델로 마이그레이션하는 방법과 가상 네트워크 사이트 간 게이트웨이를 사용하여 구독에 있는 두 배포 모델의 리소스를 연결하는 방법을 설명합니다. [Azure Resource Manager 기능 및 이점](../azure-resource-manager/management/overview.md)에 대해 자세히 알아볼 수 있습니다. 

ASM은 IaaS VM이라도도 하는 Azure Virtual Machines(클래식) 및 PaaS VM 또는 웹/작업자 역할이라고도 하는 [Azure Cloud Services(클래식)](../cloud-services/index.yml)의 두 가지 컴퓨팅 제품을 지원합니다. 이 문서에서는 Azure Virtual Machines(클래식) 마이그레이션에 대해서만 설명합니다.

## <a name="goal-for-migration"></a>마이그레이션 목표
Resource Manager는 템플릿을 사용하여 복잡한 애플리케이션을 배포할 수 있도록 지원하며 VM 확장을 사용하여 가상 머신을 구성하고 액세스 관리와 태깅을 통합합니다. Azure Resource Manager는 가용성 집합에 가상 머신에 대해 확장성 있는 병렬 배포를 포함합니다. 그뿐 아니라 새로운 배포 모델에서는 컴퓨팅, 네트워크, 스토리지의 수명 주기를 독립적으로 관리할 수 있습니다. 마지막으로 가상 네트워크에 가상 머신을 적용하여 보안 구현을 기본적으로 중요시합니다.

클래식 배포 모델의 거의 모든 기능이 Azure Resource Manager의 컴퓨팅, 네트워크 및 스토리지에 대해 지원됩니다. Azure Resource Manager의 새로운 기능을 제대로 활용하려는 경우 클래식 배포 모델에서 기존 배포를 마이그레이션할 수 있습니다.

## <a name="supported-resources--configurations-for-migration"></a>마이그레이션에 지원되는 리소스 및 구성

### <a name="supported-resources-for-migration"></a>마이그레이션에 지원되는 리소스
* Virtual Machines(VM이 있는 클라우드 서비스)
* [Cloud Services(웹/작업자 역할 포함)](../cloud-services-extended-support/in-place-migration-overview.md)
* 가용성 집합
* 스토리지 계정
* 가상 네트워크
* VPN Gateway
* [Express Route 게이트웨이](../expressroute/expressroute-howto-move-arm.md)(Virtual Network와 동일한 구독에서만)
* 네트워크 보안 그룹
* 경로 테이블
* 예약된 IP

## <a name="supported-configurations-for-migration"></a>마이그레이션에 지원되는 구성
이들 클래식 IaaS 리소스는 마이그레이션 시 지원됩니다.

| 서비스 | 구성 |
| --- | --- |
| Azure AD Domain Services | [Azure AD Domain Services가 포함된 가상 네트워크](../active-directory-domain-services/migrate-from-classic-vnet.md) |

## <a name="supported-scopes-of-migration"></a>지원되는 마이그레이션 범위
컴퓨팅, 네트워크 및 스토리지 리소스의 마이그레이션을 완료하는 데는 다음 4가지 방법이 있습니다.

* [가상 머신 마이그레이션(가상 네트워크가 아님)](#migration-of-virtual-machines-not-in-a-virtual-network)
* [가상 머신 마이그레이션(가상 네트워크에서)](#migration-of-virtual-machines-in-a-virtual-network)
* [스토리지 계정 마이그레이션](#migration-of-storage-accounts)
* [연결되지 않은 리소스 마이그레이션](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>가상 머신 마이그레이션(가상 네트워크가 아님)
Resource Manager 배포 모델에서는 기본적으로 애플리케이션 보안이 적용되어 있습니다. 모든 VM은 Resource Manager 모델의 가상 네트워크에 있어야 합니다. Azure 플랫폼은 마이그레이션의 일부로 VM을 다시 시작합니다(`Stop`, `Deallocate` 및 `Start`). Virtual Machines이 마이그레이션될 가상 네트워크에 대해서는 두 가지 옵션이 있습니다.

* 플랫폼에서 새 가상 네트워크를 만들도록 요청하고 가상 머신을 새 가상 네트워크로 마이그레이션할 수 있습니다.
* 가상 머신을 Resource Manager의 기존 가상 네트워크로 마이그레이션할 수 있습니다.

> [!NOTE]
> 이 마이그레이션 범위에서는 마이그레이션 중 특정 시간 동안 관리 평면 작업 및 데이터 평면 작업이 허용되지 않을 수 있습니다.
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>가상 머신 마이그레이션(가상 네트워크에서)
대부분의 VM 구성에서는 클래식과 Resource Manager 배포 모델 간에 메타데이터만 마이그레이션됩니다. 기본 VM은 동일한 네트워크의 동일한 하드웨어에서 동일한 스토리지로 실행됩니다. 마이그레이션 중 특정 시간 동안 관리 평면 작업이 허용되지 않을 수 있습니다. 그러나 데이터 평면은 계속 작동합니다. 즉, 마이그레이션 중 VM(클래식) 상에서 실행되는 애플리케이션에 가동 중지 시간이 발생하지 않습니다.

현재 지원되지 않는 구성은 다음과 같습니다. 향후 이에 대한 지원을 추가할 경우 이 구성의 일부 VM에서 가동 중지 시간(VM 작업 중지, 할당 취소, 다시 시작 진행)이 발생할 수 있습니다.

* 단일 클라우드 서비스에 둘 이상의 가용성 집합이 있습니다.
* 단일 클라우드 서비스에서 하나 이상의 가용성 집합과 가용성 집합에 없는 VM이 있습니다.

> [!NOTE]
> 이 마이그레이션 범위에서는 마이그레이션 중 특정 시간 동안 관리 평면이 허용되지 않을 수 있습니다. 앞서 설명한 특정 구성의 경우 데이터 평면 가동 중지 시간이 발생합니다.
>

### <a name="migration-of-storage-accounts"></a>스토리지 계정 마이그레이션
원활한 마이그레이션을 위해 클래식 스토리지 계정에 Resource Manager VM을 배포할 수 있습니다. 이 기능을 사용하면 컴퓨팅 및 네트워크 리소스를 스토리지 계정과 상관없이 마이그레이션할 수 있으며 이러한 방식이 바람직합니다. Virtual Machines 및 Virtual Network에 대해 마이그레이션한 후에는 스토리지 계정에 대해 마이그레이션을 수행하여 마이그레이션 프로세스를 완료해야 합니다.

스토리지 계정에 연결된 디스크 또는 Virtual Machines 데이터가 없고 Blob, 파일, 테이블 및 큐만 있는 경우, Azure Resource Manager로의 마이그레이션을 종속성 없는 독립형 마이그레이션으로 수행할 수 있습니다.

> [!NOTE]
> Resource Manager 배포 모델에는 기본 이미지 및 디스크 개념이 적용되지 않습니다. 스토리지 계정이 마이그레이션되면 클래식 이미지와 디스크가 더 이상 Azure Portal에 표시되지 않지만 백업 VHD는 스토리지 계정에 남아 있습니다.

다음 스크린샷은 Azure Portal을 사용하여 클래식 스토리지 계정을 Azure Resource Manager 스토리지 계정으로 업그레이드하는 방법을 보여 줍니다.
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 스토리지 계정으로 이동합니다.
3. **설정** 섹션에서 **Azure Resource Manager로 마이그레이션** 을 클릭합니다.
4. 마이그레이션 가능성을 확인하려면 **유효성 검사** 를 클릭합니다.
5. 유효성 검사를 통과하면 **준비** 를 클릭하여 마이그레이션된 스토리지 계정을 만듭니다.
6. **예** 를 입력하여 마이그레이션을 확인하고 **커밋** 을 클릭하여 마이그레이션을 완료합니다.

    ![스토리지 계정 유효성 검사](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-1.png)
    
    ![스토리지 계정 준비](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-2.png)
    
    ![스토리지 계정 마이그레이션 완료](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-3.png)

### <a name="migration-of-unattached-resources"></a>연결되지 않은 리소스 마이그레이션
연결된 디스크 또는 Virtual Machines 데이터가 없는 스토리지 계정은 독립적으로 마이그레이션할 수 있습니다.

Virtual Machines 및 Virtual Network에 연결되지 않은 네트워크 보안 그룹, 경로 테이블 및 예약 IP도 독립적으로 마이그레이션할 수 있습니다.

<br>

## <a name="unsupported-features-and-configurations"></a>지원되지 않는 기능 및 구성
일부 기능 및 구성은 현재 지원되지 않습니다. 다음 섹션에서는 이러한 항목에 대한 권장 사항을 설명합니다.

### <a name="unsupported-features"></a>지원되지 않는 기능
현재 지원되지 않는 기능은 다음과 같습니다. 이러한 설정을 제거하고 VM을 마이그레이션한 다음 Resource Manager 배포 모델에서 설정을 다시 사용하도록 지정할 수 있습니다(선택 사항).

| 리소스 공급자 | 기능 | 권장 |
| --- | --- | --- |
| 컴퓨팅 | 연결되지 않은 가상 컴퓨터 디스크 | 이들 디스크 뒤에 있는 VHD Blob은 Storage 계정을 마이그레이션할 때 마이그레이션됩니다. |
| 컴퓨팅 | 가상 머신 이미지 | 이들 디스크 뒤에 있는 VHD Blob은 Storage 계정을 마이그레이션할 때 마이그레이션됩니다. |
| 네트워크 | 엔드포인트 ACL. | 엔드포인트 ACL을 제거하고 마이그레이션을 다시 시도합니다. |
| 네트워크 | Application Gateway | 마이그레이션을 시작하기 전에 Application Gateway를 제거한 후 마이그레이션이 완료되면 Application Gateway를 다시 만듭니다. |
| 네트워크 | VNet 피어링을 사용하는 가상 네트워크 | Virtual Network를 리소스 관리자로 마이그레이션한 다음 피어를 마이그레이션합니다. [VNet 피어링](../virtual-network/virtual-network-peering-overview.md)에 대해 자세히 알아보세요. |

### <a name="unsupported-configurations"></a>지원되지 않는 구성
현재 지원되지 않는 구성은 다음과 같습니다.

| 서비스 | 구성 | 권장 |
| --- | --- | --- |
| 리소스 관리자 |클래식 리소스에 대한 RBAC(역할 기반 액세스 제어) |마이그레이션 후 리소스의 URI가 수정되므로 마이그레이션 후에 수행되어야 하는 RBAC 정책 업데이트를 계획하는 것이 좋습니다. |
| 컴퓨팅 |VM과 연결된 여러 서브넷 |한 서브넷만 참조하도록 서브넷 구성을 업데이트합니다. 이를 위해 VM에서 보조 NIC(다른 서브넷 의미)를 제거하고 마이그레이션이 완료되면 다시 연결해야 할 수 있습니다. |
| 컴퓨팅 |가상 네트워크에 속하지만 명시적 서브넷이 할당되지 않은 가상 머신 |VM을 삭제할 수 있습니다(선택 사항). |
| 컴퓨팅 |경고, 자동 크기 조정 정책이 있는 가상 머신 |마이그레이션이 진행되고 이러한 설정은 삭제됩니다. 따라서 마이그레이션 전에 환경을 평가하는 것이 좋습니다. 또는 마이그레이션이 완료된 다음 경고 설정을 다시 구성할 수 있습니다. |
| 컴퓨팅 |XML VM 확장(BGInfo 1.*, Visual Studio 디버거, 웹 배포 및 원격 디버깅) |이는 지원되지 않습니다. 마이그레이션을 계속하려면 가상 머신에서 이러한 확장을 제거하는 것이 좋습니다. 그러지 않으면 마이그레이션 프로세스 중에 자동으로 삭제됩니다. |
| 컴퓨팅 |Premium Storage를 사용한 부팅 진단 |마이그레이션을 계속하기 전에 VM에 대한 부팅 진단 기능을 비활성화합니다. 마이그레이션이 완료된 후에 Resource Manager 스택에서 부팅 진단을 재활성화할 수 있습니다. 또한 스크린샷 및 직렬 로그에 대해 사용되는 blob은 그러한 blob에 대해 요금이 부과되지 않도록 삭제해야 합니다. |
| 컴퓨팅 | 둘 이상의 가용성 집합 또는 다중 가용성 집합을 포함하는 클라우드 서비스입니다. |현재는 지원되지 않습니다. 마이그레이션하기 전에 Virtual Machines를 동일한 가용성 집합으로 이동하세요. |
| 컴퓨팅 | Azure Security Center 확장이 있는 VM | Azure Security Center는 보안을 모니터링하고 경고를 발생시키기 위한 확장을 Virtual Machines에 자동으로 설치합니다. 이러한 확장은 일반적으로 구독에서 Azure Security Center가 사용되도록 설정되면 자동으로 설치됩니다. Virtual Machines를 마이그레이션하려면 구독에 대해 Virtual Machines에서 Security Center 모니터링 확장을 제거하는 Security Center 정책을 사용하지 않도록 설정합니다. |
| 컴퓨팅 | 백업 또는 스냅샷 확장이 있는 VM | 이러한 확장은 Azure Backup 서비스를 사용하여 구성된 Virtual Machine에 설치됩니다. 이러한 VM의 마이그레이션은 지원되지 않지만 [클래식에서 Azure Resource Manager로의 마이그레이션에 관한 질문과 대답](/azure/virtual-machines/migration-classic-resource-manager-faq#i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault)에 따라 마이그레이션 전에 수행된 백업을 유지합니다.  |
| 컴퓨팅 | Azure Site Recovery 확장이 있는 VM | 해당 확장은 Azure Site Recovery 서비스를 사용하여 구성된 가상 머신에 설치됩니다. Site Recovery에 사용되는 스토리지 마이그레이션은 작동하지만, 현재 복제에 영향을 미칩니다. 스토리지 마이그레이션 후 VM 복제를 사용하지 않도록 설정한 다음 다시 사용하도록 설정해야 합니다. |
| 네트워크 |가상 머신과 웹/작업자 역할이 포함된 가상 네트워크 |현재는 지원되지 않습니다. 마이그레이션하기 전에 웹/작업자 역할을 자체 Virtual Network로 이동하세요. 클래식 Virtual Network가 마이그레이션되면 마이그레이션된 Azure Resource Manager Virtual Network가 이전과 비슷한 구성을 얻기 위해 클래식 Virtual Network와 페어링될 수 있습니다.|
| 네트워크 | 클래식 ExpressRoute 회로 |현재는 지원되지 않습니다. 이러한 회로는 IaaS 마이그레이션을 시작하기 전에 Azure Resource Manager로 마이그레이션해야 합니다. 자세한 내용은 [클래식에서 Resource Manager 배포 모델로 ExpressRoute 회로 이동](../expressroute/expressroute-move.md)을 참조하세요.|
| Azure App Service |App Service 환경이 포함된 가상 네트워크 |현재는 지원되지 않습니다. |
| Azure HDInsight |HDInsight Services가 포함된 가상 네트워크 |현재는 지원되지 않습니다. |
| Microsoft Dynamics Lifecycle Services |Dynamics Lifecycle Services에서 관리하는 가상 머신이 포함된 가상 네트워크 |현재는 지원되지 않습니다. |
| Azure API Management |Azure API Management 배포가 포함된 가상 네트워크 |현재는 지원되지 않습니다. IaaS VNET을 마이그레이션하려면 API Management 배포의 VNET을 변경합니다. 이 작업은 가동 중지 시간이 없습니다. |

## <a name="next-steps"></a>다음 단계

* [클래식에서 Azure Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보](migration-classic-resource-manager-deep-dive.md)
* [클래식에서 Azure Resource Manager로 IaaS 리소스의 마이그레이션 계획](migration-classic-resource-manager-plan.md)
* [PowerShell을 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](migration-classic-resource-manager-ps.md)
* [CLI를 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](migration-classic-resource-manager-cli.md)
* [클래식에서 Azure Resource Manager로의 IaaS 리소스 마이그레이션을 지원하기 위한 커뮤니티 도구](migration-classic-resource-manager-community-tools.md)
* [가장 일반적인 마이그레이션 오류 검토](migration-classic-resource-manager-errors.md)
* [클래식에서 Azure Resource Manager로의 IaaS 리소스 마이그레이션과 관련된 가장 자주 묻는 질문 검토](migration-classic-resource-manager-faq.yml)
