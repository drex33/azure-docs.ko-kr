---
title: Azure DevTest Labs란?
description: DevTest Lab에서 Azure 가상 머신을 쉽게 만들고 관리하고 모니터링할 수 있는 방법 알아보기
ms.topic: overview
ms.date: 10/20/2021
ms.openlocfilehash: f521b1fcd886d56387c9b6c44451d1858f95d62f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229060"
---
# <a name="what-is-azure-devtest-labs"></a>Azure DevTest Labs란?
Azure DevTest Labs는 개발자가 승인을 기다리지 않고 VM(가상 머신) 및 PaaS(Platform as a Service) 리소스를 효율적으로 자체 관리할 수 있도록 하는 서비스입니다. DevTest Labs는 미리 구성된 기본 또는 Azure Resource Manager 템플릿으로 구성된 랩을 만듭니다. 이러한 랩에는 환경을 만드는 데 사용할 수 있는 모든 필수 도구와 소프트웨어가 있습니다.

DevTest Labs에서 다음 작업을 수행하여 최신 버전의 애플리케이션을 테스트할 수 있습니다.

- 재사용 가능한 템플릿 및 아티팩트를 사용하여 Windows 및 Linux 환경을 빠르게 만듭니다.
- 배포 파이프라인과 DevTest Labs를 쉽게 통합하여 주문형 환경을 만듭니다.
- 학습 및 데모를 위해 여러 테스트 에이전트와 미리 준비된 환경을 만들어 부하 테스트를 스케일 업합니다.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="cost-control-and-governance"></a>비용 제어 및 거버넌스
DevTest Labs를 사용하면 다음 작업을 통해 비용을 더 쉽게 제어할 수 있습니다.

- 랩에 대해 사용자 또는 랩당 VM 수와 같은 [정책을 설정](devtest-lab-set-lab-policy.md)합니다. 
- VM을 자동으로 종료 및 시작하는 [정책](devtest-lab-set-lab-policy.md)을 만듭니다.
- 랩 내에서 실행되는 VM 및 PaaS 리소스에 대한 비용을 추적하여 [예산](devtest-lab-configure-cost-management.md)을 벗어나지 않도록 합니다. 필요한 작업을 수행할 수 있도록 높은 랩 예상 비용에 대한 알림을 받습니다.
- 외부 리소스를 실행하지 않도록 랩의 컨텍스트 내에서 유지합니다.

## <a name="quickly-get-to-ready-to-test"></a>빠른 테스트 준비
DevTest Labs를 사용하면 미리 프로비전된 환경을 만들어 애플리케이션을 개발하고 테스트할 수 있습니다. 애플리케이션의 마지막 빌드에 대한 [환경을 클레임](devtest-lab-add-claimable-vm.md)하고 작업을 시작합니다. 또는 더 빠르고 간결한 환경을 만들기 위해 컨테이너를 사용합니다.

## <a name="create-once-use-everywhere"></a>한 번 만들어서 어디에나 사용
팀 또는 조직 내에서 PaaS [환경 템플릿](devtest-lab-create-environment-from-arm.md) 및 [아티팩트](add-artifact-repository.md)(모두 소스 제어 유지)를 캡처하고 공유하여 개발자 환경 및 테스트 환경을 쉽게 만들 수 있습니다.

## <a name="worry-free-self-service"></a>걱정할 필요가 없는 셀프 서비스
DevTest Labs를 통해 개발자와 테스터는 미리 구성된 리소스 세트를 사용하여 [IaaS VM](devtest-lab-add-vm.md) 및 [PaaS 리소스](devtest-lab-create-environment-from-arm.md)를 빠르고 쉽게 만들 수 있습니다.

## <a name="use-iaas-and-paas-resources"></a>IaaS 및 PaaS 리소스 사용 
Resource Manager 템플릿을 사용하여 Azure Service Fabric 클러스터 또는 SharePoint 팜과 같은 리소스를 가동합니다. 템플릿은 [퍼블릭 환경 리포지토리](devtest-lab-configure-use-public-environments.md)에서 가져오거나 [랩을 사용자 고유의 Git 리포지토리에 연결](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories)합니다. Resource Manager 템플릿을 통해 랩 컨텍스트 내에서 Azure를 검색하여 빈 리소스 그룹(샌드박스)을 가동할 수도 있습니다.

## <a name="integrate-with-your-existing-toolchain"></a>기존 도구 체인과 통합
미리 만들어져 있는 플러그 인 또는 API를 활용하여 기본 설정 [CI(연속 통합) 도구](devtest-lab-integrate-ci-cd.md), IDE(통합 개발 환경) 또는 자동화된 릴리스 파이프라인에서 직접 개발/테스트 환경을 만듭니다. 포괄적인 명령줄 도구를 사용할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- DevTest Labs에 대해 자세히 알아보려면 [DevTest Labs 개념](devtest-lab-concepts.md)을 참조하세요.
- 단계별 지침이 포함된 연습은 [자습서: Azure DevTest Labs를 사용하여 랩 설정](tutorial-create-custom-lab.md)을 참조하세요.
