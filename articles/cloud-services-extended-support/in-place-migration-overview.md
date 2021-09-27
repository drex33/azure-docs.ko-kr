---
title: Azure Cloud Services(클래식)를 Azure Cloud Services(확장 지원)로 마이그레이션
description: Cloud Services(클래식)에서 Cloud Service(확장 지원)로의 마이그레이션 개요
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 1542a8dd6c36c2a623f11179c0871a9fef2e3619
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128665514"
---
# <a name="migrate-azure-cloud-services-classic-to-azure-cloud-services-extended-support"></a>Azure Cloud Services(클래식)를 Azure Cloud Services(확장 지원)로 마이그레이션

이 문서에서는 Cloud Services(클래식)를 Cloud Services(추가 지원)로 마이그레이션하는 방법을 간략하게 설명합니다.     

[Cloud Services(확장 지원)](overview.md)는 Azure Service Manager를 사용하여 배포된 Azure Cloud Services와 기능 패리티와 함께 지역적 복원력을 제공하는 주요 이점이 있습니다. 또한 RBAC(역할 기반 액세스 제어), 태그, 정책과 같은 일부 Azure Resource Manager 기능을 제공하고 배포 템플릿, 프라이빗 링크를 지원합니다. 두 배포 모델(확장 지원 및 클래식)은 [유사한 가격 책정 구조](https://azure.microsoft.com/pricing/details/cloud-services/)에서 사용할 수 있습니다.

Cloud Services(확장 지원)는 고객이 Azure Service Manager에서 Azure Resource Manager로 마이그레이션할 수 있도록 다시 배포와 현재 위치 마이그레이션이라는 두 가지 경로를 제공합니다. 

아래 표에는 이러한 두 옵션 간의 비교가 강조 표시됩니다.  


| 재배포 | 현재 위치 마이그레이션 | 
|---|---|
| 고객은 Azure Resource Manager에서 직접 새 클라우드 서비스를 배포한 다음, 유효성 검사를 통해 Azure Service Manager에서 이전 클라우드 서비스를 삭제합니다. | 현재 위치 마이그레이션 도구를 사용하면 기존 Cloud Services(클래식) 배포를 Cloud Services(확장 지원)로 원활하게 플랫폼 오케스트레이션 마이그레이션할 수 있습니다. | 
| 다시 배포를 통해 고객은 다음을 수행할 수 있습니다. <br><br> - 리소스 이름을 정의합니다. <br><br> - 리소스를 기본 설정으로 구성하거나 재사용합니다. <br><br> - 최소한의 변경으로 서비스 구성 및 정의 파일을 다시 사용합니다. | 현재 위치 마이그레이션의 경우 플랫폼은 다음을 수행합니다. <br><br> - 리소스 이름을 정의합니다. <br><br> - 개별 리소스 그룹에서 각 배포 및 관련 리소스를 구성합니다. <br><br> - Azure Resource Manager에 대한 기존 구성 및 정의 파일을 수정합니다. | 
| 고객은 새 배포에 대한 트래픽을 오케스트레이션해야 합니다. | 마이그레이션은 IP 주소를 유지하고 데이터 경로는 동일하게 유지됩니다. | 
| 고객은 Azure Resource Manager에서 이전 클라우드 서비스를 삭제해야 합니다. | 플랫폼은 마이그레이션 후 Cloud Services(클래식) 리소스를 삭제합니다. | 
| 이는 더 많은 유연성을 제공하지만 마이그레이션하는 데 추가 시간이 필요한 리프트 앤 시프트 마이그레이션입니다. | 이는 빠른 마이그레이션을 제공하지만 유연성은 낮은 자동화된 마이그레이션입니다. | 

Cloud Services(클래식)에서 Cloud Services(추가 지원)로의 마이그레이션 계획을 평가할 때 [Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md), [App Service](../app-service/overview.md), [Azure Kubernetes Service](../aks/intro-kubernetes.md) 및 [Azure Service Fabric](../service-fabric/overview-managed-cluster.md) 등의 추가 Azure 서비스를 조사할 수 있습니다. 이러한 서비스는 계속해서 추가 기능을 제공하는 반면 Cloud Services(추가 지원)는 기본적으로 Cloud Services(클래식)와 동일한 기능 패리티를 유지합니다.

애플리케이션에 따라 Cloud Services(추가 지원)는 다른 옵션에 비해 Azure Resource Manager로 이동하는 데 훨씬 적은 노력이 필요할 수 있습니다. 애플리케이션이 발전하지 않는 경우 Cloud Services(추가 지원)는 빠른 마이그레이션 경로를 제공하므로 고려할 수 있는 실행 가능한 옵션입니다. 반대로, 애플리케이션이 지속적으로 발전하고 더 현대적인 기능 세트가 필요한 경우 현재 및 미래 요구 사항을 더 잘 처리하기 위해 다른 Azure 서비스를 탐색합니다.

## <a name="redeploy-overview"></a>다시 배포 개요

[Cloud Services(확장 지원)](overview.md)를 사용하여 서비스를 다시 배포하면 다음과 같은 이점이 있습니다. 

- Cloud Services(클래식)와 유사한 웹 및 작업자 역할을 지원합니다.
- 웹 및 작업자 역할의 설계, 아키텍처 또는 구성 요소에는 변경 사항이 없습니다. 
- 데이터 평면은 클라우드 서비스와 동일하기 때문에 런타임 코드를 변경할 필요가 없습니다. 
- Azure GuestOS 릴리스 및 관련 업데이트는 Cloud Services(클래식)에 맞춰 조정됩니다. 
- 업데이트 도메인에 대한 기본 업데이트 프로세스, 업그레이드 진행 방식, 롤백 및 업데이트 중 허용된 서비스 변경 사항은 변경되지 않습니다.

새 클라우드 서비스(확장 지원)는 다음 클라이언트 도구를 사용하여 Azure Resource Manager에서 직접 배포할 수 있습니다.

- [클라우드 서비스 배포 - 포털](deploy-portal.md)
- [클라우드 서비스 배포 - PowerShell](deploy-powershell.md)
- [클라우드 서비스 배포 - 템플릿](deploy-template.md)
- [클라우드 서비스 배포 - SDK](deploy-sdk.md)
- [클라우드 서비스 배포 - Visual Studio](/visualstudio/azure/cloud-services-extended-support?context=%2fazure%2fcloud-services-extended-support%2fcontext%2fcontex)


## <a name="migration-tool-overview"></a>마이그레이션 도구 개요

플랫폼 지원 마이그레이션의 주요 이점은 다음과 같습니다.

- 대부분의 시나리오에서 가동 중지 시간 없이 원활한 플랫폼 오케스트레이션 마이그레이션을 사용하도록 설정합니다. [지원되는 시나리오](in-place-migration-technical-details.md)에 대해 자세히 알아봅니다.  
- 유효성 검사, 준비, 커밋(또는 중단)의 세 가지 간단한 단계로 기존 클라우드 서비스를 마이그레이션합니다. [마이그레이션 도구의 작동](in-place-migration-overview.md#migration-steps)방식에 대해 자세히 알아보세요.
- 성공적인 준비 후에 마이그레이션된 배포를 테스트하는 기능을 제공합니다. 중단이 마이그레이션을 롤백하는 동안 마이그레이션을 커밋하고 종료합니다.

마이그레이션 도구는 동일한 API를 활용하고, [가상 머신(클래식) 마이그레이션](../virtual-machines/migration-classic-resource-manager-overview.md)과 동일한 환경을 제공합니다. 

## <a name="setup-access-for-migration"></a>마이그레이션을 위한 액세스 설정

이 마이그레이션을 수행하려면 사용자는 구독에 대한 공동 관리자로 추가되고, 필요한 공급자를 등록해야 합니다. 

1. Azure Portal에 로그인합니다.
3. 허브 메뉴에서 구독을 선택합니다. 표시되지 않으면 모든 서비스를 선택합니다.
3. 적절한 구독 항목을 찾은 다음, [내 역할] 필드를 확인합니다. 공동 관리자의 경우 값은 계정 관리자여야 합니다. 공동 관리자를 추가할 수 없는 경우 구독에 대한 서비스 관리자 또는 공동 관리자에게 문의하여 사용자 자신을 추가합니다.

4. [Portal](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [PowerShell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) 또는 [CLI](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)를 사용하여 Microsoft.ClassicInfrastructureMigrate 네임스페이스에 대한 구독을 등록합니다.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate 
    ```
 
5. 등록 상태를 확인합니다. 등록을 완료하는 데 몇 분 정도 걸릴 수 있습니다. 

    ```powershell
    Get-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

## <a name="how-is-migration-for-cloud-services-classic-different-from-virtual-machines-classic"></a>Cloud Services(클래식) 마이그레이션은 Virtual Machines(클래식)와 어떻게 다른가요?
Azure Service Manager는 [Azure Virtual Machines(클래식)](/previous-versions/azure/virtual-machines/windows/classic/tutorial-classic) 및 [Azure Cloud Services(클래식)](../cloud-services/cloud-services-choose-me.md) 또는 웹/작업자 역할의 두 가지 컴퓨팅 제품을 지원합니다. 두 제품은 Cloud Service 내의 배포 유형에 따라 달라집니다. Azure Cloud Services(클래식)는 웹/작업자 역할을 사용하는 배포가 포함된 Cloud Service를 사용합니다. Azure Virtual Machines(클래식)는 IaaS VM을 사용하는 배포가 포함된 클라우드 서비스를 사용합니다.

지원되는 시나리오 목록은 배포 유형의 차이로 인해 Cloud Services(클래식)와 Virtual Machines(클래식) 간에 다릅니다.

## <a name="migration-steps"></a>마이그레이션 단계
 
고객은 Virtual Machines(클래식)를 마이그레이션하는 데 사용되는 것과 동일한 4가지 작업을 사용하여 Cloud Services(클래식) 배포를 마이그레이션할 수 있습니다. 

1. **마이그레이션 유효성 검사** - 지원되지 않는 일반적인 시나리오에서 마이그레이션을 차단하지 않는지 확인합니다.
2. **마이그레이션 준비** – Azure Resource Manager에서 리소스 메타데이터를 복제합니다. 리소스 메타데이터가 Azure 서버 관리자와 Azure Resource Manager 간에 동기화되도록 만들기/업데이트/삭제 작업에 대한 모든 리소스가 잠깁니다. 모든 읽기 작업은 Cloud Services(클래식) 및 Cloud Services(확장 지원) API를 모두 사용하여 작동합니다.
3. **마이그레이션 중단** - Azure Resource Manager에서 리소스 메타데이터를 제거합니다. 만들기/업데이트/삭제 작업에 대한 모든 리소스의 잠금을 해제합니다.
4. **마이그레이션 커밋** - Azure Service Manager에서 리소스 메타데이터를 제거합니다. 만들기/업데이트/삭제 작업에 대한 리소스의 잠금을 해제합니다. 커밋을 시도한 후에는 중단이 더 이상 허용되지 않습니다.

>[!NOTE]
> 준비, 중단 및 커밋은 멱등적(idempotent)이므로 실패한 경우 다시 시도하여 문제를 해결해야 합니다.

:::image type="content" source="media/in-place-migration-1.png" alt-text="마이그레이션과 관련된 단계의 다이어그램을 보여 주는 이미지":::

자세한 내용은 [클래식에서 Azure Resource Manager로 IaaS 리소스 플랫폼 지원 마이그레이션 개요](../virtual-machines/migration-classic-resource-manager-overview.md)를 참조하세요.

## <a name="supported-resources-and-features-available-for-migration-associated-with-cloud-services-classic"></a>Cloud Services(클래식)와 관련된 마이그레이션에 사용할 수 있는 지원되는 리소스 및 기능
-   스토리지 계정
-   가상 네트워크
-   네트워크 보안 그룹
-   예약된 공용 IP 주소
-   엔드포인트 액세스 제어 목록
-   사용자 정의 경로
-   내부 부하 분산 장치
-   키 자격 증명 모음으로 인증서 마이그레이션
-   플러그 인 및 확장(XML 및 Json 기반)
-   시작/중지 시 작업
-   가속화된 네트워킹을 사용하는 배포
-   단일 또는 여러 역할을 사용하는 배포
-   기본 부하 분산 장치
-   입력, 인스턴스 입력, 내부 엔드포인트
-   동적 공용 IP 주소
-   DNS 이름 
-   네트워크 트래픽 규칙

## <a name="supported-configurations--migration-scenarios"></a>지원되는 구성/마이그레이션 시나리오
이러한 시나리오는 리소스, 기능 및 Cloud Services의 조합과 관련된 상위 수준 시나리오입니다. 이 목록은 전체 목록이 아닙니다.

| 서비스 | 구성 | 의견 | 
|---|---|---|
| [Azure AD Domain Services](../active-directory-domain-services/migrate-from-classic-vnet.md) | Azure Active Directory Domain Services가 포함된 가상 네트워크 | Cloud Service 배포 및 Azure AD Domain Services가 모두 포함된 가상 네트워크를 지원합니다. 고객은 먼저 Azure AD Domain Services를 별도로 마이그레이션한 다음, Cloud Service 배포만 남아 있는 가상 네트워크를 마이그레이션해야 합니다. |
| 클라우드 서비스 | 단일 슬롯에만 배포가 포함된 Cloud Service | 프로덕션 또는 스테이징 슬롯 배포가 포함된 Cloud Services를 마이그레이션할 수 있습니다. |
| 클라우드 서비스 | 공개적으로 표시되는 가상 네트워크에 없는 배포(기본 가상 네트워크 배포) | Cloud Service는 공개적으로 표시되는 가상 네트워크, 숨겨진 가상 네트워크에 있을 수 있거나 가상 네트워크에 없을 수 있습니다.  숨겨진 가상 네트워크 및 공개적으로 표시되는 가상 네트워크의 Cloud Services는 마이그레이션에 지원됩니다. 고객은 유효성 검사 API를 사용하여 배포가 기본 가상 네트워크 내에 있는지 여부를 파악할 수 있습니다. 그러면 마이그레이션할 수 있는지 여부를 결정할 수 있습니다. |
|클라우드 서비스 | XML 확장(BGInfo, Visual Studio 디버거, 웹 배포 및 원격 디버깅) | 모든 xml 확장이 마이그레이션에 지원됩니다. 
| Virtual Network | 여러 Cloud Services가 포함된 가상 네트워크 가상 네트워크 | 여러 클라우드 서비스가 포함된 가상 네트워크가 마이그레이션에 지원됩니다. 가상 네트워크와 해당 네트워크 내의 모든 Cloud Services가 모두 Azure Resource Manager로 마이그레이션됩니다. |
| Virtual Network | Portal을 통해 만든 가상 네트워크의 마이그레이션(.cscfg 파일에서 "Group Resource-group-name VNet-Name"을 사용해야 함)  | 마이그레이션의 일부로 cscfg의 가상 네트워크 이름이 가상 네트워크의 Azure Resource Manager ID를 사용하도록 변경됩니다. (subscription/subscription-id/resource-group/resource-group-name/resource/vnet-name) <br><br>마이그레이션 후 배포를 관리하려면 가상 네트워크 이름 대신 Azure Resource Manager ID 사용을 시작하도록 .cscfg 파일의 로컬 복사본을 업데이트합니다. <br><br>이전 명명 체계를 사용하는 .cscfg 파일은 유효성 검사를 통과하지 못합니다. 
| Virtual Network | 다른 서브넷의 역할을 사용하는 배포 마이그레이션 | 다른 서브넷의 다른 역할을 사용하는 클라우드 서비스는 마이그레이션에 지원됩니다. |

## <a name="next-steps"></a>다음 단계
- [클래식에서 Azure Resource Manager로 IaaS 리소스 플랫폼 지원 마이그레이션 개요](../virtual-machines/migration-classic-resource-manager-overview.md)
- [Azure Portal](in-place-migration-portal.md)을 사용하여 Cloud Services(확장 지원)로 마이그레이션
- [PowerShell](in-place-migration-powershell.md)을 사용하여 Cloud Services(확장 지원)로 마이그레이션
