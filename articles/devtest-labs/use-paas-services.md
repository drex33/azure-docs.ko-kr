---
title: PaaS(Platform-as-a-Service) 서비스 사용
description: Azure DevTest Labs에서 PaaS(Platform as a Service) 서비스를 사용하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 8d4bcf98d425e322c18e13ec99b525cf1442bc49
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128548130"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Azure DevTest Labs에서 PaaS(Platform as a Service) 서비스 사용
PaaS는 환경 기능을 통해 DevTest Labs에서 지원됩니다. DevTest Labs의 환경은 Git 리포지토리에서 미리 구성된 Azure Resource Manager 템플릿을 통해 지원됩니다. 환경에는 PaaS 및 IaaS 리소스가 둘 다 포함될 수 있습니다. 환경에서는 함께 작동하도록 사용자 지정되는 가상 머신, 데이터베이스, 가상 네트워크, 웹앱과 같은 Azure 리소스를 포함할 수 있는 복잡한 시스템을 만들 수 있습니다. 이 템플릿을 통해 소스 코드 제어를 사용하여 일관성 있게 배포하고 환경 관리를 개선할 수 있습니다. 

제대로 설정된 시스템에서는 다음 시나리오가 가능합니다. 

- 독립적인 다양한 환경을 사용하는 개발자
- 비동기적으로 다양한 구성에서 테스트
- 템플릿 변경 없이 스테이징 및 프로덕션 파이프라인에 통합
- 개발 머신와 환경을 둘 다 동일한 랩 내에 포함하면 관리 및 비용 보고의 용이성이 향상됩니다.  

DevTest Labs 리소스 공급자는 랩 사용자를 대신하여 리소스를 만들기 때문에, PaaS 리소스를 사용할 수 있도록 랩 사용자에게 추가 권한을 부여할 필요가 없습니다. 다음 이미지는 Service Fabric 클러스터를 랩에서 환경으로 보여 줍니다.

![환경으로서 Service Fabric 클러스터](./media/create-environment-service-fabric-cluster/cluster-created.png)

환경 설정에 관한 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 다중 VM 환경 및 PaaS 리소스 만들기](devtest-lab-create-environment-from-arm.md)를 참조하세요. DevTest Labs에 있는 Azure Resource Manager 템플릿의 퍼블릭 리포지토리를 사용하면 외부 GitHub 원본에 직접 연결하지 않고도 환경을 만들 수 있습니다. 퍼블릭 환경에 관한 자세한 내용은 [Azure DevTest Labs에서 퍼블릭 환경 구성 및 사용](devtest-lab-configure-use-public-environments.md)을 참조하세요.

대규모 조직에서 개발 팀은 일반적으로 사용자 지정/격리된 테스트 환경과 같은 환경을 제공합니다. 비즈니스 단위나 부서 내의 모든 팀에서 사용하는 환경이 있을 수 있습니다. IT 그룹은 조직의 모든 팀에서 사용할 수 있는 환경을 제공하려고 할 수 있습니다.  

## <a name="customizations"></a>사용자 지정

#### <a name="sandbox"></a>샌드박스 
랩 소유자는 랩 환경을 사용자 지정하여 리소스 그룹 내에서 사용자 역할을 **읽기 권한자** 에서 **기여자** 로 변경할 수 있습니다. 이 기능은 랩의 **구성 및 정책** 아래 **랩 설정** 페이지에 있습니다. 이 역할 변경을 통해 사용자는 해당 환경 내에서 리소스를 추가하거나 제거할 수 있습니다. 액세스를 추가로 제한하려면 Azure 정책을 사용합니다. 이 기능을 사용하여 구독 수준에서 액세스 권한 없이 리소스 또는 구성을 사용자 지정할 수 있습니다.

#### <a name="custom-tokens"></a>사용자 지정 토큰
리소스 그룹 외부에 있고 템플릿이 액세스할 수 있는 환경에만 관련된 몇 가지 사용자 지정 랩 정보가 있습니다. 다음은 몇 가지 예입니다. 

- 랩 네트워크 식별
- Location
- Resource Manager 템플릿 파일이 저장되는 스토리지 계정입니다. 
 
#### <a name="lab-virtual-network"></a>랩 가상 네트워크
[랩 가상 네트워크에 환경 연결](connect-environment-lab-virtual-network.md) 문서에서는 `$(LabSubnetId)` 토큰을 사용하도록 Resource Manager 템플릿을 수정하는 방법을 설명합니다. 환경이 생성되면 `$(LabSubnetId)` 토큰은 **가상 머신 만들기에서 사용** 옵션이 **true** 로 설정된 첫 번째 서브넷 표시로 바뀝니다. 그러면 이전에 만든 네트워크를 해당 환경에서 사용할 수 있습니다. 스테이징 및 프로덕션과 동일한 Resource Manager 템플릿을 테스트에서 사용하려면 Resource Manager 템플릿 매개 변수에서 `$(LabSubnetId)`를 기본값으로 사용합니다. 

#### <a name="environment-storage-account"></a>환경 스토리지 계정
DevTest Labs에서는 [중첩된 Resource Manager 템플릿](../azure-resource-manager/templates/linked-templates.md) 사용을 지원합니다. [[테스트 환경을 위한 중첩된 Azure Resource Manager 템플릿 배포](deploy-nested-template-environments.md) 문서에서는 `_artifactsLocation` 및 `_artifactsLocationSasToken` 토큰을 사용하여 기본 템플릿의 중첩된 폴더와 동일한 폴더에 Resource Manager 템플릿의 URI를 만드는 방법을 설명합니다. 두 토큰에 관한 자세한 내용은 [Azure Resource Manager - 모범 사례 가이드](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)의 **배포 아티팩트** 섹션을 참조하세요.

## <a name="user-experience"></a>사용자 환경

## <a name="developer"></a>Developer
개발자는 VM 만들기의 동일한 워크플로를 사용하여 특정 환경을 만듭니다. 환경 또는 머신 이미지 중에서 선택하고 템플릿에 필요한 정보를 입력합니다. 환경을 보유한 각 개발자는 변경 내용을 배포하고 내부 루프 디버깅을 개선할 수 있습니다. 최신 템플릿을 사용하여 언제든지 환경을 만들 수 있습니다.  이 기능을 사용하면 환경을 제거하고 다시 만들 수 있어 수동으로 시스템을 만들거나 오류 테스트에서 복구해야 하는 가동 중지 시간을 줄이는 데 도움이 됩니다.  

### <a name="testing"></a>테스트
DevTest Labs 환경에서는 비동기적으로 특정 코드와 구성을 개별 테스트할 수 있습니다. 일반적인 사례는 개별 끌어오기 요청의 코드를 사용하여 환경을 설정하고 자동화된 테스트를 시작하는 것입니다. 자동화된 테스트가 완료될 때까지 실행되면 특정 환경에 대해 수동 테스트를 실행할 수 있습니다. 일반적으로 이 프로세스는 CI/CD 파이프라인의 일부로 수행됩니다. 

## <a name="management-experience"></a>관리 환경

### <a name="cost-tracking"></a>비용 추적
비용 추적 기능에는 전체 비용 추세의 일부로 다양한 환경 내에 있는 Azure 리소스가 포함됩니다. 리소스별 비용은 환경 내의 다양한 리소스를 구분하지 않지만 환경을 단일 비용으로 표시합니다.

### <a name="security"></a>보안
DevTest Labs를 사용하여 제대로 구성된 Azure 구독은 [랩을 통해서만 Azure 리소스에 대한 액세스를 제한](devtest-lab-add-devtest-user.md)할 수 있습니다. 환경을 사용하면 랩 소유자는 다른 Azure 리소스에 대한 액세스를 허용하지 않고 사용자가 승인된 구성으로 PaaS 리소스에 액세스하도록 허용할 수 있습니다. 랩 사용자가 환경을 사용자 지정하는 시나리오에서 랩 소유자는 기여자 액세스를 허용할 수 있습니다. 기여자 액세스를 통해 랩 사용자는 관리되는 리소스 그룹 내에서만 Azure 리소스를 추가하거나 제거할 수 있습니다. 이렇게 하면 사용자 기여자에게 구독 액세스를 허용하는 것보다 더 쉽게 추적하고 관리할 수 있습니다.

### <a name="automation"></a>Automation
Automation은 효과적인 대규모 에코시스템의 핵심 구성 요소입니다. Automation은 구독 및 랩 간에 여러 환경의 관리와 추적을 처리하는 데 필요합니다.

### <a name="cicd-pipeline"></a>CI/CD 파이프라인
DevTest Labs의 PaaS 서비스는 랩에서 액세스를 제어하는 집중 배포를 포함하여 CI/CD 파이프라인을 개선하는 데 도움이 될 수 있습니다.

## <a name="next-steps"></a>다음 단계
환경에 관한 자세한 내용은 다음 문서를 참조하세요. 

- 
- [Azure Resource Manager 템플릿으로 다중 VM 환경 및 PaaS 리소스 만들기](devtest-lab-create-environment-from-arm.md)
- [Azure DevTest Labs에서 공용 환경 구성 및 사용](devtest-lab-configure-use-public-environments.md)
- [Azure DevTest Labs에서 자체 포함 Service Fabric 클러스터로 환경 만들기](create-environment-service-fabric-cluster.md)
- [Azure DevTest Labs에서 랩의 가상 네트워크에 환경 연결](connect-environment-lab-virtual-network.md)
- [Azure DevOps CI/CD 파이프라인에 환경 통합](integrate-environments-devops-pipeline.md)
 
