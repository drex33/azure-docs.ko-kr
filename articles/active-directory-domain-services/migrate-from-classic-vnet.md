---
title: 클래식 가상 네트워크의 Azure AD Domain Services 마이그레이션 | Microsoft Docs
description: 클래식 가상 네트워크 모델의 기존 Azure AD Domain Services 관리되는 도메인을 Resource Manager 기반 가상 네트워크로 마이그레이션하는 방법을 알아봅니다.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 08/11/2021
ms.author: justinha
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4492824c77a8a97810c5849c221c400560db4bad
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535911"
---
# <a name="migrate-azure-active-directory-domain-services-from-the-classic-virtual-network-model-to-resource-manager"></a>클래식 가상 네트워크 모델에서 Resource Manager로 Azure Active Directory Domain Services 마이그레이션

Azure AD DS(Azure Active Directory Domain Services)는 현재 클래식 가상 네트워크 모델을 사용하는 고객이 Resource Manager 가상 네트워크 모델로 이동하는 것을 한 번 지원합니다. Resource Manager 배포 모델을 사용하는 Azure AD DS 관리되는 도메인은 세밀한 암호 정책, 감사 로그, 계정 잠금 보호 등의 추가 기능을 제공합니다.

이 문서에서는 마이그레이션 시 고려할 사항을 설명한 다음, 기존 관리되는 도메인을 성공적으로 마이그레이션하기 위해 필요한 단계를 설명합니다. 이점에 대한 내용은 [클래식 배포 모델에서 Azure AD DS의 Resource Manager 배포 모델로 마이그레이션하면 좋은 점][migration-benefits]을 참조하세요.

> [!NOTE]
> 2017년에 Azure AD Domain Services를 Azure Resource Manager 네트워크에 호스트할 수 있게 되었습니다. 그 후로 Azure Resource Manager의 최신 기능을 사용하여 더욱 안전한 서비스를 구축할 수 있었습니다. Azure Resource Manager 배포가 클래식 배포를 완전히 대체하므로 Azure AD DS 클래식 가상 네트워크 배포는 2023년 3월 1일에 사용 중지됩니다.
>
> 자세한 내용은 [공식 사용 중단 알림](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)을 참조하세요.

## <a name="overview-of-the-migration-process"></a>마이그레이션 프로세스 개요

마이그레이션 프로세스는 클래식 가상 네트워크에서 실행되는 기존 관리되는 도메인을 가져와서 기존 Resource Manager 가상 네트워크로 이동합니다. 마이그레이션은 PowerShell을 사용하여 수행되며, 주요 실행 단계는 *준비* 와 *마이그레이션* 입니다.

![Azure AD DS 마이그레이션 프로세스 개요](media/migrate-from-classic-vnet/migration-overview.png)

*준비* 단계에서 Azure AD DS는 도메인을 백업하여 관리되는 도메인과 동기화된 사용자, 그룹 및 암호의 최신 스냅샷을 얻습니다. 그 후 동기화가 비활성화되고, 관리되는 도메인을 호스트하는 클라우드 서비스가 삭제됩니다. 준비 단계 동안 관리되는 도메인은 사용자를 인증할 수 없습니다.

![Azure AD DS 마이그레이션의 준비 단계](media/migrate-from-classic-vnet/migration-preparation.png)

*마이그레이션* 단계에서는 클래식 관리되는 도메인의 도메인 컨트롤러에 대한 기본 가상 디스크를 복사하여 Resource Manager 배포 모델을 사용하는 VM을 만듭니다. 그런 다음, 관리되는 도메인을 다시 만듭니다. 여기에는 LDAPS 및 DNS 구성이 포함됩니다. Azure AD와 동기화가 다시 시작되고 LDAP 인증서가 복원됩니다. 머신을 관리되는 도메인에 다시 조인할 필요가 없습니다. 머신은 아무 변화 없이 관리되는 도메인에 계속 조인되고 실행됩니다.

![Azure AD DS 마이그레이션](media/migrate-from-classic-vnet/migration-process.png)

## <a name="example-scenarios-for-migration"></a>마이그레이션 예제 시나리오

다음 예제는 관리되는 도메인을 마이그레이션하는 일반적인 시나리오입니다.

> [!NOTE]
> 마이그레이션이 성공한 것을 확인하기 전에는 클래식 가상 네트워크를 변환하지 마세요. 가상 네트워크를 변환하면 마이그레이션 및 확인 단계에서 문제가 발생할 경우 관리되는 도메인을 롤백하거나 복원할 수 있는 옵션이 제거됩니다.

### <a name="migrate-azure-ad-ds-to-an-existing-resource-manager-virtual-network-recommended"></a>Azure AD DS를 기존 Resource Manager 가상 네트워크로 마이그레이션(권장)

대표적인 시나리오는 다른 기존 리소스를 이미 Resource Manager 배포 모델 및 가상 네트워크로 이동했으며, 그 후 Resource Manager 가상 네트워크에서 Azure AD DS를 계속 실행하는 클래식 가상 네트워크로 피어링을 사용하는 것입니다. 이 접근 방식에서는 Resource Manager 애플리케이션 및 서비스가 클래식 가상 네트워크 관리되는 도메인의 인증 및 관리 기능을 사용할 수 있습니다. 마이그레이션된 후에는 모든 리소스가 Resource Manager 배포 모델 및 가상 네트워크를 사용하여 실행됩니다.

![Azure AD DS를 기존 Resource Manager 가상 네트워크로 마이그레이션](media/migrate-from-classic-vnet/migrate-to-existing-vnet.png)

이 마이그레이션 예제 시나리오와 관련된 개략적인 단계는 다음과 같은 부분으로 구성됩니다.

1. 클래식 가상 네트워크에 구성된 기존 VPN 게이트웨이 또는 가상 네트워크 피어링을 제거합니다.
1. 이 문서에 설명된 단계에 따라 관리되는 도메인을 마이그레이션합니다.
1. 마이그레이션을 테스트하여 성공한 것을 확인한 다음, 클래식 가상 네트워크를 삭제합니다.

### <a name="migrate-multiple-resources-including-azure-ad-ds"></a>Azure AD DS를 포함한 여러 리소스 마이그레이션

이 예제 시나리오에서는 클래식 배포 모델의 Azure AD DS 및 기타 관련 리소스를 Resource Manager 배포 모델로 마이그레이션합니다. 일부 리소스가 관리되는 도메인과 함께 클래식 가상 네트워크에서 계속 실행되는 경우 Resource Manager 배포 모델로 마이그레이션하면 모든 이점을 누릴 수 있습니다.

![여러 리소스를 Resource Manager 배포 모델로 마이그레이션](media/migrate-from-classic-vnet/migrate-multiple-resources.png)

이 마이그레이션 예제 시나리오와 관련된 개략적인 단계는 다음과 같은 부분으로 구성됩니다.

1. 클래식 가상 네트워크에 구성된 기존 VPN 게이트웨이 또는 가상 네트워크 피어링을 제거합니다.
1. 이 문서에 설명된 단계에 따라 관리되는 도메인을 마이그레이션합니다.
1. 클래식 가상 네트워크와 Resource Manager 네트워크 간에 가상 네트워크 피어링을 설정합니다.
1. 마이그레이션을 테스트하여 성공했는지 확인합니다.
1. [VM과 같은 추가 클래식 리소스를 이동][migrate-iaas]합니다.

### <a name="migrate-azure-ad-ds-but-keep-other-resources-on-the-classic-virtual-network"></a>Azure AD DS를 마이그레이션하지만 다른 리소스는 클래식 가상 네트워크에 유지

이 예제 시나리오는 한 세션에서 최소 가동 중지 시간이 발생합니다. Azure AD DS만 Resource Manager 가상 네트워크로 마이그레이션하고, 기존 리소스를 클래식 배포 모델 및 가상 네트워크에 그대로 둡니다. 필요에 따라 다음 유지 관리 기간에 클래식 배포 모델 및 가상 네트워크의 리소스를 추가로 마이그레이션할 수 있습니다.

![Azure AD DS만 Resource Manager 배포 모델로 마이그레이션](media/migrate-from-classic-vnet/migrate-only-azure-ad-ds.png)

이 마이그레이션 예제 시나리오와 관련된 개략적인 단계는 다음과 같은 부분으로 구성됩니다.

1. 클래식 가상 네트워크에 구성된 기존 VPN 게이트웨이 또는 가상 네트워크 피어링을 제거합니다.
1. 이 문서에 설명된 단계에 따라 관리되는 도메인을 마이그레이션합니다.
1. 클래식 가상 네트워크와 새 Resource Manager 가상 네트워크 간에 가상 네트워크 피어링을 설정합니다.
1. 나중에 필요에 따라 클래식 가상 네트워크의 [리소스를 추가로 마이그레이션][migrate-iaas]합니다.

## <a name="before-you-begin"></a>시작하기 전에

관리되는 도메인을 준비하고 마이그레이션할 때 인증 및 관리 서비스의 가용성과 관련하여 몇 가지 고려할 사항이 있습니다. 마이그레이션하는 동안 관리되는 도메인을 일정 기간 사용할 수 없습니다. Azure AD DS를 사용하는 애플리케이션과 서비스는 마이그레이션 동안 가동 중지됩니다.

> [!IMPORTANT]
> 마이그레이션 프로세스를 시작하기 전에 이 마이그레이션 문서와 지침을 모두 읽어 보세요. 마이그레이션 프로세스는 일정 기간 동안 Azure AD DS 도메인 컨트롤러의 가용성에 영향을 줍니다. 마이그레이션 프로세스가 진행되는 동안에는 사용자, 서비스 및 애플리케이션이 관리되는 도메인에 인증할 수 없습니다.

### <a name="ip-addresses"></a>IP 주소

마이그레이션 후에는 관리되는 도메인의 도메인 컨트롤러 IP 주소가 변경됩니다. 이때 보안 LDAP 엔드포인트의 공용 IP 주소도 변경됩니다. 새 IP 주소는 Resource Manager 가상 네트워크의 새 서브넷 주소 범위 내에 있습니다.

롤백해야 하는 경우 롤백 후 IP 주소가 변경될 수 있습니다.

Azure AD DS는 일반적으로 주소 범위에서 처음 두 개의 사용 가능한 IP 주소를 사용하지만, 반드시 그런 것은 아닙니다. 현재는 마이그레이션 후 사용할 IP 주소를 지정할 수 없습니다.

### <a name="downtime"></a>가동 중지 시간

마이그레이션 프로세스 중에는 도메인 컨트롤러가 일정 기간 동안 오프라인 상태로 유지됩니다. Azure AD DS가 Resource Manager 배포 모델 및 가상 네트워크로 마이그레이션되는 동안에는 도메인 컨트롤러에 액세스할 수 없습니다.

평균적으로 가동 중지 시간은 약 1~3시간입니다. 이 시간은 도메인 컨트롤러가 오프라인 상태로 전환되는 시점부터 첫 번째 도메인 컨트롤러가 다시 온라인 상태로 전환되는 시점까지입니다. 이 평균에는 두 번째 도메인 컨트롤러를 복제하는 데 걸리는 시간 또는 추가 리소스를 Resource Manager 배포 모델로 마이그레이션하는 데 걸리는 시간은 포함되지 않습니다.

### <a name="account-lockout"></a>계정 잠금

클래식 가상 네트워크에서 실행되는 관리되는 도메인에는 AD 계정 잠금 정책이 없습니다. VM이 인터넷에 노출되는 경우 공격자는 암호-스프레이 방법을 사용하여 계정에 강제로 액세스할 수 있습니다. 이러한 시도를 중지할 계정 잠금 정책이 없습니다. Resource Manager 배포 모델 및 가상 네트워크를 사용하는 관리되는 도메인의 경우 AD 계정 잠금 정책이 이러한 암호-스프레이 공격을 방어합니다.

기본적으로 2분 내에 잘못된 암호가 5회 입력되면 계정이 30분동안 잠깁니다.

잠긴 계정은 로그인에 사용할 수 없으며, 이는 관리되는 도메인 또는 계정에서 관리하는 애플리케이션을 관리하는 기능을 방해할 수 있습니다. 관리되는 도메인을 마이그레이션한 후, 반복적인 로그인 실패로 인해 계정이 영구적으로 잠긴 것과 비슷한 상황이 발생할 수 있습니다. 마이그레이션 후 다음과 같은 두 가지 시나리오가 자주 발생합니다.

* 서비스 계정에서 사용하는 암호가 만료되었습니다.
    * 서비스 계정에서 만료된 암호를 사용하여 반복적으로 로그인을 시도하며, 이로 인해 계정이 잠깁니다. 이 문제를 해결하려면 자격 증명이 만료된 애플리케이션 또는 VM을 찾아 암호를 업데이트합니다.
* 악성 엔터티가 무차별 암호 대입을 사용하여 계정에 로그인하려고 시도합니다.
    * VM이 인터넷에 노출되면 공격자가 일반적인 사용자 이름 및 암호 조합을 사용하여 로그인을 시도하는 경우가 자주 있습니다. 이러한 반복적인 로그인 실패로 인해 계정이 잠길 수 있습니다. 여러 이유로(예: 관리 계정이 잠기는 것을 최소화하기 위해) 관리자 계정에는 *admin* 또는 *administrator* 와 같은 일반적인 이름을 사용하지 않는 것이 좋습니다.
    * 인터넷에 노출되는 VM 수를 최소화합니다. [Azure Bastion][azure-bastion]을 사용하면 Azure Portal을 통해 VM에 안전하게 연결할 수 있습니다.

마이그레이션 후 일부 계정이 잠길 가능성이 있는 것으로 의심되는 경우 최종 마이그레이션 단계에서 감사를 사용하도록 설정하거나 세밀한 암호 정책 설정을 변경하는 방법에 대한 간략한 설명이 제공됩니다.

### <a name="roll-back-and-restore"></a>롤백 및 복원

마이그레이션이 실패하는 경우 관리되는 도메인을 롤백하거나 복원하는 프로세스가 있습니다. 롤백은 관리되는 도메인의 상태를 마이그레이션 이전으로 즉시 되돌리는 셀프 서비스 옵션입니다. Azure 지원 엔지니어가 마지막 수단으로 백업에서 관리되는 도메인을 복원할 수도 있습니다. 자세한 내용은 [실패한 마이그레이션에서 롤백 또는 복원하는 방법](#roll-back-and-restore-from-migration)을 참조하세요.

### <a name="restrictions-on-available-virtual-networks"></a>사용 가능한 가상 네트워크에 대한 제한 사항

관리되는 도메인을 마이그레이션할 수 있는 가상 네트워크에 대한 몇 가지 제한 사항이 있습니다. 대상 Resource Manager 가상 네트워크는 다음 요구 사항을 충족해야 합니다.

* Resource Manager 가상 네트워크는 Azure AD DS가 현재 배포된 클래식 가상 네트워크와 동일한 Azure 구독에 있어야 합니다.
* Resource Manager 가상 네트워크는 Azure AD DS가 현재 배포된 클래식 가상 네트워크와 동일한 지역에 있어야 합니다.
* Resource Manager 가상 네트워크의 서브넷에는 3-5개 이상의 사용 가능한 IP 주소가 있어야 합니다.
* Resource Manager 가상 네트워크의 서브넷은 Azure AD DS 전용 서브넷이어야 하며, 다른 워크로드를 호스트하면 안 됩니다.

가상 네트워크 요구 사항에 대한 자세한 내용은 [가상 네트워크 디자인 고려 사항 및 구성 옵션][network-considerations]을 참조하세요.

또한 네트워크 보안 그룹을 만들어 관리되는 도메인에 대한 가상 네트워크의 트래픽을 제한해야 합니다. 이러한 규칙을 적용해야 하는 Azure 표준 부하 분산 장치가 마이그레이션 프로세스 중에 생성됩니다. 이 네트워크 보안 그룹은 Azure AD DS를 보호하며, 관리되는 도메인이 제대로 작동하는 데 꼭 필요합니다.

필요한 규칙에 대한 자세한 내용은 [Azure AD DS 네트워크 보안 그룹 및 필요한 포트](network-considerations.md#network-security-groups-and-required-ports)를 참조하세요.

### <a name="ldaps-and-tlsssl-certificate-expiration"></a>LDAPS 및 TLS/SSL 인증서 만료

관리되는 도메인에 LDAPS가 구성된 경우 현재 TLS/SSL 인증서가 30일 넘게 유효한지 확인합니다. 30일 내에 만료되는 인증서는 마이그레이션 프로세스가 실패하는 원인이 됩니다. 필요한 경우 인증서를 갱신하고 관리되는 도메인에 적용한 다음, 마이그레이션 프로세스를 시작합니다.

## <a name="migration-steps"></a>마이그레이션 단계

Resource Manager 배포 모델 및 가상 네트워크로의 마이그레이션은 다음과 같은 5가지 주요 단계로 구성됩니다.

| 단계    | 수행 방법  | 예상 시간  | 가동 중지 시간  | 롤백/복원 |
|---------|--------------------|-----------------|-----------|-------------------|
| [1단계 - 새 가상 네트워크 업데이트 및 찾기](#update-and-verify-virtual-network-settings) | Azure portal | 15분 | 가동 중지 시간 없음 | 해당 없음 |
| [2단계 - 마이그레이션할 관리되는 도메인 준비](#prepare-the-managed-domain-for-migration) | PowerShell | 평균적으로 15~30분 | 이 명령이 완료된 후 Azure AD DS의 가동 중지 시간이 시작됩니다. | 롤백 및 복원을 사용할 수 있습니다. |
| [3단계 - 관리되는 도메인을 기존 가상 네트워크로 이동](#migrate-the-managed-domain) | PowerShell | 평균적으로 1~3시간 | 이 명령이 완료되면 하나의 도메인 컨트롤러를 사용할 수 있습니다. | 이 명령이 실패하면 롤백(셀프 서비스) 및 복원을 사용할 수 있습니다. |
| [4단계 - 복제본 도메인 컨트롤러 테스트 및 대기](#test-and-verify-connectivity-after-the-migration)| PowerShell 및 Azure Portal | 테스트 수에 따라 1시간 이상 | 두 개의 도메인 컨트롤러가 모두 사용 가능하고 정상적으로 작동하며, 가동 중지 시간이 종료됩니다. | 해당 없음. 첫 번째 VM이 성공적으로 마이그레이션되면 롤백 또는 복원 옵션이 없습니다. |
| [5단계 - 선택적 구성 단계](#optional-post-migration-configuration-steps) | Azure Portal 및 VM | 해당 없음 | 가동 중지 시간 없음 | 해당 없음 |

> [!IMPORTANT]
> 가동 중지 시간이 길어지지 않도록 마이그레이션 프로세스를 시작하기 전에 이 마이그레이션 문서와 지침을 모두 읽어 보세요. 마이그레이션 프로세스는 일정 기간 동안 Azure AD DS 도메인 컨트롤러의 가용성에 영향을 줍니다. 마이그레이션 프로세스가 진행되는 동안에는 사용자, 서비스 및 애플리케이션이 관리되는 도메인에 인증할 수 없습니다.

## <a name="update-and-verify-virtual-network-settings"></a>가상 네트워크 설정 업데이트 및 확인

마이그레이션 프로세스를 시작하기 전에 다음 초기 검사 및 업데이트를 완료합니다. 다음 단계는 마이그레이션 전에 언제든지 수행할 수 있으며 관리되는 도메인의 운영에 영향을 주지 않습니다.

1. 로컬 Azure PowerShell 환경을 최신 버전으로 업데이트합니다. 마이그레이션 단계를 완료하려면 *2.3.2* 이상 버전이 필요합니다.

    PowerShell 버전을 확인하고 업데이트하는 방법에 대한 자세한 내용은 [Azure PowerShell 개요][azure-powershell]를 참조하세요.

1. 기존 Resource Manager 가상 네트워크를 만들거나 선택합니다.

    네트워크 설정이 Azure AD DS에 필요한 포트를 차단하지 않는지 확인합니다. 클래식 가상 네트워크와 Resource Manager 가상 네트워크에서 포트를 열어야 합니다. 이러한 설정에는 경로 테이블(사용하지 않는 것이 좋음) 및 네트워크 보안 그룹이 포함됩니다.

    Azure AD DS에는 관리되는 도메인에 필요한 포트를 보호하고 들어오는 모든 트래픽을 차단하는 네트워크 보안 그룹이 필요합니다. 이 네트워크 보안 그룹은 관리되는 도메인에 대한 액세스를 잠그는 추가 보호 계층으로 작동합니다. 필요한 포트를 보려면 [네트워크 보안 그룹 및 필요한 포트][network-ports]를 참조하세요.

    보안 LDAP를 사용하는 경우 *TCP* 포트 *636* 의 수신 트래픽을 허용하는 규칙을 네트워크 보안 그룹에 추가합니다. 자세한 내용은 [인터넷을 통한 보안 LDAP 액세스 잠금](tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet)을 참조하세요.

    이 대상 리소스 그룹, 대상 가상 네트워크 및 대상 가상 네트워크 서브넷을 기록해 둡니다. 이러한 리소스 이름은 마이그레이션 프로세스 중에 사용됩니다.

1. Azure Portal에서 관리되는 도메인 상태를 확인합니다. 관리되는 도메인에 대한 경고가 있는 경우 마이그레이션 프로세스를 시작하기 전에 해결합니다.
1. 필요에 따라 다른 리소스를 Resource Manager 배포 모델 및 가상 네트워크로 이동하려는 경우 해당 리소스를 마이그레이션할 수 있는지 확인합니다. 자세한 내용은 [클래식에서 Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션][migrate-iaas]을 참조하세요.

    > [!NOTE]
    > 클래식 가상 네트워크를 Resource Manager 가상 네트워크로 변환하지 마세요. 변환할 경우 관리되는 도메인을 롤백하거나 복원하는 옵션이 없습니다.

## <a name="prepare-the-managed-domain-for-migration"></a>마이그레이션할 관리되는 도메인 준비

Azure PowerShell은 마이그레이션할 관리되는 도메인을 준비하는 데 사용됩니다. 이러한 단계에는 백업을 수행하고, 동기화를 일시 중지하고, Azure AD DS를 호스트하는 클라우드 서비스를 삭제하는 작업이 포함됩니다. 이 단계가 완료되면 Azure AD DS가 일정 기간 동안 오프라인으로 전환됩니다. 준비 단계가 실패하면 [이전 상태로 롤백](#roll-back)할 수 있습니다.

마이그레이션할 관리되는 도메인을 준비하려면 다음 단계를 완료합니다.

1. [PowerShell 갤러리][powershell-script]에서 `Migrate-Aaads` 스크립트를 설치합니다. 이 PowerShell 마이그레이션 스크립트는 Azure AD 엔지니어링 팀의 디지털 서명을 받았습니다.

    ```powershell
    Install-Script -Name Migrate-Aadds
    ```

1. [Get-Credential][get-credential] cmdlet을 사용하여 마이그레이션 스크립트의 자격 증명을 보관할 변수를 만듭니다.

    사용자가 지정하는 사용자 계정에는 Azure AD DS를 사용하도록 설정할 수 있는 Azure AD 테넌트의 *전역 관리자* 권한과 필요한 Azure AD DS 리소스를 만들 수 있는 *기여자* 권한이 있어야 합니다.

    사용자 계정 및 암호를 입력하라는 메시지가 표시되면 적절한 사용자 계정 및 암호를 입력합니다.

    ```powershell
    $creds = Get-Credential
    ```
    
1. Azure 구독 ID에 대한 변수를 정의합니다. 필요한 경우 [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) cmdlet을 사용하여 구독 ID를 나열하고 볼 수 있습니다. 다음 명령에서 구독 ID를 입력합니다.

   ```powershell
   $subscriptionId = 'yourSubscriptionId'
   ```

1. 이제 *-Prepare* 매개 변수를 사용하여 `Migrate-Aadds` cmdlet을 실행합니다. 관리되는 도메인의 *-ManagedDomainFqdn*(예: *aaddscontoso.com*)을 입력합니다.

    ```powershell
    Migrate-Aadds `
        -Prepare `
        -ManagedDomainFqdn aaddscontoso.com `
        -Credentials $creds `
        -SubscriptionId $subscriptionId
    ```

## <a name="migrate-the-managed-domain"></a>관리되는 도메인 마이그레이션

관리되는 도메인을 준비하고 백업한 후에는 도메인을 마이그레이션할 수 있습니다. 이 단계에서는 Resource Manager 배포 모델을 사용하여 Azure AD DS 도메인 컨트롤러 VM을 다시 만듭니다. 이 단계를 완료하는 데 1~3시간이 걸릴 수 있습니다.

*-Commit* 매개 변수를 사용하여 `Migrate-Aadds` cmdlet을 실행합니다. *aaddscontoso.com* 와 같은 이전 섹션에서 준비한 관리되는 도메인의 *-ManagedDomainFqdn* 을 입력합니다.

Azure AD DS를 마이그레이션할 가상 네트워크를 포함하고 있는 대상 리소스 그룹(예: *myResourceGroup*)을 지정합니다. 대상 가상 네트워크(예: *myVnet*) 및 서브넷(*DomainServices*)을 입력합니다.

이 명령을 실행된 후에는 롤백할 수 없습니다.

```powershell
Migrate-Aadds `
    -Commit `
    -ManagedDomainFqdn aaddscontoso.com `
    -VirtualNetworkResourceGroupName myResourceGroup `
    -VirtualNetworkName myVnet `
    -VirtualSubnetName DomainServices `
    -Credentials $creds `
    -SubscriptionId $subscriptionId
```

스크립트가 관리되는 도메인의 마이그레이션 준비가 완료된 것을 확인하면 *Y* 를 입력하여 마이그레이션 프로세스를 시작합니다.

> [!IMPORTANT]
> 마이그레이션 프로세스 중에 클래식 가상 네트워크를 Resource Manager 가상 네트워크로 변환하지 마세요. 가상 네트워크를 변환하는 경우 원래 가상 네트워크가 더 이상 존재하지 않으므로 관리되는 도메인을 롤백 또는 복원할 수 없습니다.

마이그레이션 프로세스가 진행되는 동안 2분마다 다음 예제 출력과 같이 진행률 표시기가 현재 상태를 보고합니다.

![Azure AD DS 마이그레이션의 진행률 표시기](media/migrate-from-classic-vnet/powershell-migration-status.png)

PowerShell 스크립트를 닫아도 마이그레이션 프로세스는 계속 실행됩니다. Azure Portal에서 관리되는 도메인의 상태는 *마이그레이션 중* 으로 보고됩니다.

마이그레이션이 성공적으로 완료되면 Azure Portal 또는 Azure PowerShell에서 첫 번째 도메인 컨트롤러의 IP 주소를 볼 수 있습니다. 두 번째 도메인 컨트롤러를 사용할 수 있을 것으로 예상되는 시간도 표시됩니다.

이 단계에서는 필요에 따라 클래식 배포 모델 및 가상 네트워크의 다른 기존 리소스를 이동할 수 있습니다. 또는 리소스를 클래식 배포 모델에 유지하고 Azure AD DS 마이그레이션이 완료된 후에 가상 네트워크를 서로 피어링할 수도 있습니다.

## <a name="test-and-verify-connectivity-after-the-migration"></a>마이그레이션 후 연결 테스트 및 확인

두 번째 도메인 컨트롤러를 배포하고 관리되는 도메인에서 사용할 수 있게 될 때까지 다소 시간이 걸릴 수 있습니다. 두 번째 도메인 컨트롤러는 마이그레이션 cmdlet이 완료된 후 1~2시간 후에 사용할 수 있습니다. Resource Manager 배포 모델을 사용하면 관리되는 도메인의 네트워크 리소스가 Azure Portal 또는 Azure PowerShell에 표시됩니다. 두 번째 도메인 컨트롤러를 사용할 수 있는지 확인하려면 Azure Portal에서 관리되는 도메인의 **속성** 페이지를 확인합니다. 두 개의 IP 주소가 표시되면 두 번째 도메인 컨트롤러가 준비된 것입니다.

두 번째 도메인 컨트롤러를 사용할 수 있게 되면 VM의 네트워크 연결에 대한 다음 구성 단계를 완료합니다.

* **DNS 서버 설정 업데이트** Resource Manager 가상 네트워크의 다른 리소스가 관리되는 도메인을 확인하고 사용할 수 있도록 하려면 DNS 설정을 새 도메인 컨트롤러의 IP 주소로 업데이트합니다. Azure Portal에서 자동으로 이러한 설정을 구성할 수 있습니다.

    Resource Manager 가상 네트워크를 구성하는 방법에 대한 자세한 내용은 [Azure 가상 네트워크에 대한 DNS 설정 업데이트][update-dns]를 참조하세요.
* **도메인 조인 VM 다시 시작(선택 사항)** Azure AD DS 도메인 컨트롤러의 DNS 서버 IP 주소가 변경되면 도메인 조인 VM을 다시 시작하여 DNS 서버 설정을 사용할 수 있습니다. 애플리케이션 또는 VM에서 수동으로 DNS 설정을 구성한 경우 Azure Portal에 표시되는 도메인 컨트롤러의 새 DNS 서버 IP 주소를 사용하여 수동으로 업데이트합니다. 도메인 조인 VM을 다시 부팅하면 IP 주소를 새로 고치지 않아 발생하는 연결 문제가 방지됩니다.

이제 가상 네트워크 연결 및 이름 확인을 테스트합니다. Resource Manager 가상 네트워크에 연결된 또는 피어링된 VM에서 다음 네트워크 통신 테스트를 시도합니다.

1. 도메인 컨트롤러 중 하나의 IP 주소를 ping할 수 있는지 확인합니다(예: `ping 10.1.0.4`).
    * 도메인 컨트롤러의 IP 주소는 Azure Portal의 관리되는 도메인에 대한 **속성** 페이지에 표시됩니다.
1. 관리되는 도메인의 이름 확인을 검증합니다(예: `nslookup aaddscontoso.com`).
    * 관리되는 도메인의 DNS 이름을 지정하여 DNS 설정이 올바르고 확인되는지 검증합니다.

다른 네트워크 리소스에 대해 자세히 알아보려면 [Azure AD DS에서 사용하는 네트워크 리소스][network-resources]를 참조하세요.

## <a name="optional-post-migration-configuration-steps"></a>선택적 마이그레이션 후 구성 단계

마이그레이션 프로세스가 성공적으로 완료되면 선택적 구성 단계로 감사 로그 또는 이메일 알림을 사용하도록 설정하거나 세밀한 암호 정책을 업데이트합니다.

### <a name="subscribe-to-audit-logs-using-azure-monitor"></a>Azure Monitor를 사용하여 감사 로그 구독

Azure AD DS는 도메인 컨트롤러의 문제를 해결하고 이벤트를 살펴볼 수 있도록 감사 로그를 노출합니다. 자세한 내용은 [감사 로그 활성화 및 사용][security-audits]을 참조하세요.

템플릿을 사용하여 로그에 노출되는 중요한 정보를 모니터링할 수 있습니다. 예를 들어 감사 로그 통합 문서 템플릿은 관리되는 도메인에서 발생 가능한 계정 잠금을 모니터링할 수 있습니다.

### <a name="configure-email-notifications"></a>전자 메일 알림 구성

관리되는 도메인에서 문제가 발견될 때 알림을 받으려면 Azure Portal에서 이메일 알림 설정을 업데이트합니다. 자세한 내용은 [알림 설정 구성][notifications]을 참조하세요.

### <a name="update-fine-grained-password-policy"></a>세밀한 암호 정책 업데이트

필요한 경우 기본 구성보다 제한이 적도록 세밀한 암호 정책을 업데이트할 수 있습니다. 감사 로그를 사용하여 덜 제한적인 설정이 적합한지 확인한 다음, 정책을 필요한 대로 구성할 수 있습니다. 개략적인 다음 단계에 따라 마이그레이션 후 반복적으로 잠기는 계정의 정책 설정을 검토하고 업데이트합니다.

1. 관리되는 도메인에 대한 제한을 줄이도록 [암호 정책을 구성][password-policy]하고 감사 로그의 이벤트를 관찰합니다.
1. 감사 로그에 만료된 것으로 나오는 암호를 서비스 계정에서 사용 중인 경우 해당 계정을 올바른 암호로 업데이트합니다.
1. VM이 인터넷에 노출되는 경우 *administrator*, *user* 또는 *guest* 와 같은 일반적인 이름으로 로그인 시도가 많이 이루어지는지 검토합니다. 되도록이면 일반적이지 않은 계정을 사용하도록 해당 VM을 업데이트합니다.
1. VM에서 네트워크 추적을 사용하여 공격 출처를 찾고 해당 IP 주소에서 로그인을 시도할 수 없도록 차단합니다.
1. 잠금 문제가 최소화되면 세밀한 암호 정책의 제한 수준을 필요한 대로 업데이트합니다.

## <a name="roll-back-and-restore-from-migration"></a>마이그레이션에서 롤백 및 복원

마이그레이션 프로세스의 특정 시점까지는 관리되는 도메인을 롤백하거나 복원할 수 있습니다.

### <a name="roll-back"></a>롤백

마이그레이션을 준비하는 2단계 또는 마이그레이션 자체를 진행하는 3단계에서 PowerShell cmdlet을 실행하는 동안 오류가 발생하는 경우 관리되는 도메인을 원래 구성으로 롤백할 수 있습니다. 이 롤백에는 원래 클래식 가상 네트워크가 필요합니다. 롤백 후에도 IP 주소는 변경될 수 있습니다.

*-Abort* 매개 변수를 사용하여 `Migrate-Aadds` cmdlet을 실행합니다. 이전 섹션에서 준비한 관리되는 도메인의 *-ManagedDomainFqdn*(예: *Aaddscontoso.com*) 및 클래식 가상 네트워크 이름(예: *myClassicVnet*)을 입력합니다.

```powershell
Migrate-Aadds `
    -Abort `
    -ManagedDomainFqdn aaddscontoso.com `
    -ClassicVirtualNetworkName myClassicVnet `
    -Credentials $creds `
    -SubscriptionId $subscriptionId
```

### <a name="restore"></a>복원

마지막 수단으로 Azure AD Domain Services를 사용 가능한 마지막 백업으로 복원할 수 있습니다. 최신 백업을 사용할 수 있도록 마이그레이션의 1단계에서 백업이 수행됩니다. 이 백업은 30일 동안 저장됩니다.

백업에서 관리되는 도메인을 복원하려면 [Azure Portal을 사용하여 지원 사례 티켓을 엽니다][azure-support]. 복원할 디렉터리 ID, 도메인 이름 및 복원 이유를 입력합니다. 지원 및 복원 프로세스를 완료하는 데 며칠이 걸릴 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

Resource Manager 배포 모델로 마이그레이션한 후 문제가 발생하면 다음과 같은 일반적인 문제 해결 영역을 검토합니다.

* [도메인 조인 문제 해결][troubleshoot-domain-join]
* [계정 잠금 문제 해결][troubleshoot-account-lockout]
* [계정 로그인 문제 해결][troubleshoot-sign-in]
* [보안 LDAP 연결 문제 해결][tshoot-ldaps]

## <a name="next-steps"></a>다음 단계

관리되는 도메인을 Resource Manager 배포 모델로 마이그레이션한 후에는 [Windows VM을 만들고 도메인 조인][join-windows]한 다음, [관리 도구를 설치][tutorial-create-management-vm]합니다.

<!-- INTERNAL LINKS -->
[azure-bastion]: ../bastion/bastion-overview.md
[network-considerations]: network-considerations.md
[azure-powershell]: /powershell/azure/
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Set-AzContext]: /powershell/module/az.accounts/set-azcontext
[Get-AzResource]: /powershell/module/az.resources/get-azresource
[Set-AzResource]: /powershell/module/az.resources/set-azresource
[network-resources]: network-considerations.md#network-resources-used-by-azure-ad-ds
[update-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[security-audits]: security-audit-events.md
[notifications]: notifications.md
[password-policy]: password-policy.md
[secure-ldap]: tutorial-configure-ldaps.md
[migrate-iaas]: ../virtual-machines/migration-classic-resource-manager-overview.md
[join-windows]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[troubleshoot-domain-join]: troubleshoot-domain-join.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[tshoot-ldaps]: tshoot-ldaps.md
[get-credential]: /powershell/module/microsoft.powershell.security/get-credential
[migration-benefits]: concepts-migration-benefits.md

<!-- EXTERNAL LINKS -->
[powershell-script]: https://www.powershellgallery.com/packages/Migrate-Aadds/
