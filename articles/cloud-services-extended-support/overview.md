---
title: Azure Cloud Services(추가 지원) 정보
description: 가상 네트워크 및 DNS 값을 지정하는 서비스 구성 파일의 네트워크 구성 요소의 자식 요소에 대해 알아봅니다.
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: ffea4e8edd9cc6c11644017e972db3ce3202c766
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567202"
---
# <a name="about-azure-cloud-services-extended-support"></a>Azure Cloud Services(추가 지원) 정보

Cloud Services(추가 지원)는  [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) 제품을 위한 새로운  [Azure Resource Manager](../azure-resource-manager/management/overview.md) 기반 배포 모델이며 현재 일반 공급됩니다. Cloud Services(추가 지원)는 Azure Service Manager를 사용하여 배포된 Azure Cloud Services와 기능 패리티와 함께 지역적 복원력을 제공하는 주요 이점이 있습니다. 또한 RBAC(역할 기반 액세스 및 제어), 태그, 정책과 같은 일부 ARM 기능을 제공하고 배포 템플릿을 지원합니다.  

이 변경으로 인해 Cloud Services용 Azure Service Manager 기반 배포 모델의 이름은 [Cloud Services(클래식)](../cloud-services/cloud-services-choose-me.md)로 변경됩니다. 웹 및 클라우드 애플리케이션과 서비스를 작성하고 신속하게 배포할 수 있게 됩니다. 현재 요청에 따라 클라우드 서비스 인프라의 크기를 조정하고 애플리케이션 성능을 유지하면서 동시에 비용을 절감할 수 있습니다.  

:::image type="content" source="media/inside-azure-for-iot.png" alt-text="Cloud Services(추가 지원)용 YouTube 동영상" link="https://youtu.be/H4K9xTUvNdw":::


## <a name="what-does-not-change"></a>변경 되지 않는 항목 
- 코드를 만들고 구성을 정의한 다음 Azure에 배포합니다. Azure는 컴퓨팅 환경을 설정하고 코드를 실행한 다음 모니터링 및 유지 관리합니다.
- Cloud Services(추가 지원)는 [웹 및 작업자](../cloud-services/cloud-services-choose-me.md)의 두 가지 역할 유형도 지원합니다. 웹 및 작업자 역할의 설계, 아키텍처 또는 구성 요소에는 변경 사항이 없습니다. 
- 클라우드 서비스의 세 가지 구성 요소인 서비스 정의(.csdef), 서비스 구성(.cscfg) 및 서비스 패키지(.cspkg)는 전달되며 [형식](cloud-services-model-and-package.md)에는 변경 사항이 없습니다. 
- 데이터 평면은 동일하고 컨트롤 플레인만 변경되기 때문에 런타임 코드를 변경할 필요가 없습니다. 
- Azure GuestOS 릴리스 및 관련 업데이트는 Cloud Services(클래식)에 맞춰 조정됩니다.
- 업데이트 도메인에 대한 기본 업데이트 프로세스, 업그레이드 진행 방식, 롤백 및 업데이트 중 허용된 서비스 변경 사항은 변경되지 않음

## <a name="changes-in-deployment-model"></a>배포 모델의 변경 사항

Cloud Services(추가 지원)를 배포하려면 서비스 구성(.cscfg) 및 서비스 정의(.csdef) 파일을 최소한으로 변경해야 합니다. 런타임 코드를 변경할 필요가 없습니다. 그러나 새로운 Azure Resource Manager 기반 API를 호출하려면 배포 스크립트를 업데이트해야 합니다. 

:::image type="content" source="media/overview-image-1.png" alt-text="템플릿 섹션이 추가된 기본 클라우드 서비스 구성을 보여 주는 이미지입니다.":::

배포와 관련하여 Cloud Services(클래식)와 Cloud Services(추가 지원) 간의 주요 차이점은 다음과 같습니다. 

- Azure Resource Manager 배포는 프로젝트의 인프라와 구성을 정의하는 JSON(JavaScript Object Notation) 파일인 [ARM 템플릿](../azure-resource-manager/templates/overview.md)을 사용합니다. 이 템플릿은 대상을 만들기 위한 프로그래밍 명령 시퀀스를 작성하지 않고도 배포하려는 대상을 설명할 수 있는 선언적 구문입니다. 서비스 구성 및 서비스 정의 파일은 Cloud Services(추가 지원)를 배포하는 동안 [ARM 템플릿](../azure-resource-manager/templates/overview.md)과 일치해야 합니다. 이는 [ARM 템플릿을 수동으로 생성](deploy-template.md)하거나 [PowerShell](deploy-powershell.md), [Portal](deploy-portal.md) 및 [Visual Studio](deploy-visual-studio.md)를 사용하여 수행할 수 있습니다.  

- 고객은 [Azure Key Vault](../key-vault/general/overview.md)를 사용하여 [Cloud Services(추가 지원)에서 인증서를 관리](certificates-and-key-vault.md)해야 합니다. Azure Key Vault를 사용하면 중앙의 안전한 클라우드 리포지토리에 비밀, 키 및 인증서와 같은 애플리케이션 자격 증명을 안전하게 저장하고 관리할 수 있습니다. 애플리케이션은 런타임에 Key Vault에 인증하여 자격 증명을 검색할 수 있습니다. 

- [Azure Resource Manager](../azure-resource-manager/templates/overview.md)를 통해 배포된 모든 리소스는 가상 네트워크 내에 있어야 합니다. 가상 네트워크 및 서브넷은 기존 Azure Resource Manager API를 사용하여 Azure Resource Manager에서 만들어지며 Cloud Services(추가 지원)를 배포할 때 .cscfg의 NetworkConfiguration 섹션 내에서 참조되어야 합니다.   

- 각 Cloud Services(추가 지원)는 단일 독립 배포입니다. Cloud Services(추가 지원)는 단일 클라우드 서비스 내에서 여러 슬롯을 지원하지 않습니다.  
    - VIP 스왑 기능을 사용하여 두 Cloud Services(추가 지원) 간에 교환할 수 있습니다. 클라우드 서비스의 새 릴리스를 테스트하고 스테이징하려면 Cloud Service(추가 지원)를 배포하고 다른 Cloud Service(추가 지원)로 스왑 가능한 VIP로 태그를 지정합니다.  

- DNS(Domain Name Service) 레이블은 Cloud Services(추가 지원)의 경우 선택 사항입니다. Azure Resource Manager에서 DNS 레이블은 클라우드 서비스와 연결된 공용 IP 리소스의 속성입니다. 

## <a name="migration-to-azure-resource-manager"></a>Azure Resource Manager로 마이그레이션

Cloud Services(추가 지원)는 [Azure Service Manager](/powershell/azure/servicemanagement/overview)에서 [Azure Resource Manager](../azure-resource-manager/management/overview.md)로 마이그레이션할 수 있는 두 가지 경로를 제공합니다. 
1) 고객은 Azure Resource Manager에서 직접 클라우드 서비스를 배포한 다음 Azure Service Manager에서 이전 클라우드 서비스를 삭제합니다. 
2) 현재 위치 마이그레이션은 가동 중지 시간을 최소화하거나 전혀 없이 Cloud Services(추가 지원)로 Cloud Services(클래식)를 마이그레이션하는 기능을 지원합니다. 

### <a name="additional-migration-options"></a>추가 마이그레이션 옵션

Cloud Services(클래식)에서 Cloud Services(추가 지원)로의 마이그레이션 계획을 평가할 때 [Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md), [App Service](../app-service/overview.md), [Azure Kubernetes Service](../aks/intro-kubernetes.md) 및 [Azure Service Fabric](../service-fabric/service-fabric-overview.md) 등의 추가 Azure 서비스를 조사할 수 있습니다. 이러한 서비스는 계속해서 추가 기능을 제공하는 반면 Cloud Services(추가 지원)는 기본적으로 Cloud Services(클래식)와 동일한 기능 패리티를 유지합니다. 

애플리케이션에 따라 Cloud Services(추가 지원)는 다른 옵션에 비해 Azure Resource Manager로 이동하는 데 훨씬 적은 노력이 필요할 수 있습니다. 애플리케이션이 발전하지 않는 경우 Cloud Services(추가 지원)는 빠른 마이그레이션 경로를 제공하므로 고려할 수 있는 실행 가능한 옵션입니다. 반대로, 애플리케이션이 지속적으로 발전하고 더 현대적인 기능 세트가 필요한 경우 현재 및 미래 요구 사항을 더 잘 처리하기 위해 다른 Azure 서비스를 탐색합니다. 

## <a name="next-steps"></a>다음 단계
- Cloud Services(추가 지원)에 대한 [배포 필수 구성 요소](deploy-prerequisite.md)를 검토합니다.
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [템플릿](deploy-template.md) 또는 [Visual Studio](deploy-visual-studio.md)를 사용하여 Cloud Service(추가 지원)를 배포합니다.
- Cloud Services(추가 지원)에 대한 [질문과 대답](faq.yml)을 검토합니다.
