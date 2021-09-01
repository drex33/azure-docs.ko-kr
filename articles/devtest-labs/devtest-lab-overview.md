---
title: Azure DevTest Lab 정보 | Microsoft Docs
description: DevTest Lab에서 Azure 가상 머신을 쉽게 만들고 관리하고 모니터링할 수 있는 방법 알아보기
ms.topic: article
ms.date: 08/20/2021
ms.openlocfilehash: 2d15bfd34e37036e95e06529e6c14588da15b34d
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122969865"
---
# <a name="about-azure-devtest-labs"></a>Azure DevTest Labs 정보
Azure DevTest Labs를 통해 팀의 개발자는 승인을 기다리지 않고 VM(가상 머신) 및 PaaS 리소스를 효율적으로 자체 관리할 수 있습니다.

DevTest Labs는 미리 구성된 기본 또는 Azure Resource Manager 템플릿으로 구성된 랩을 만듭니다. 여기에는 환경을 만드는 데 사용할 수 있는 모든 필수 도구와 소프트웨어가 있습니다. 몇 시간이나 며칠이 아닌 몇 분 내에 환경을 만들 수 있습니다.

DevTest Labs에서 다음 작업을 수행하여 최신 버전의 애플리케이션을 테스트할 수 있습니다.

- 재사용 가능한 템플릿 및 아티팩트를 사용하여 Windows 및 Linux 환경을 빠르게 프로비저닝합니다.
- 배포 파이프라인과 DevTest Lab을 쉽게 통합하여 주문형 환경으로 프로비전할 수 있습니다.
- 여러 개의 테스트 에이전트를 프로비저닝하여 부하 테스트를 확장하고 교육 및 데모를 위해 미리 프로비저닝된 환경을 만듭니다.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>기능
DevTest Labs는 VM으로 작업하는 개발자에게 다음과 같은 기능을 제공합니다.

- 5개 미만의 간단한 단계에 따라 VM을 신속하게 만듭니다.
- 팀 리더 또는 중앙 IT에서 구성, 승인 및 권한을 부여한 조정된 VM 기본 목록에서 선택합니다.
- 모든 소프트웨어 및 도구가 이미 설치된 미리 만든 사용자 지정 이미지에서 VM을 만듭니다. 
- VM을 만들 때 설치된 소프트웨어의 최신 빌드와 기본적으로 조합된 사용자 지정 이미지에 해당하는 수식에서 VM을 만듭니다. 
- 프로비저닝된 후 VM에 배포된 확장에 해당하는 아티팩트를 설치합니다.
- VM에 대해 자동 종료 및 자동 시작 일정을 설정합니다.
- 만들기 프로세스를 거치지 않고 미리 만든 VM을 클레임합니다.

DevTest Labs는 PaaS 환경에서 작업하는 개발자에게 다음과 같은 기능을 제공합니다.

- 3개 미만의 간단한 단계를 수행하여 PaaS 환경을 빠르게 만들려면 Resource Manager를 사용합니다.
- 팀 리더 또는 중앙 IT에서 구성하고 권한을 부여한 Resource Manager 템플릿의 조정된 목록에서 선택합니다.
- 랩 컨텍스트 내에서 Resource Manager 템플릿을 통해 Azure를 탐색하여 빈 리소스 그룹(샌드박스)을 실행합니다.

또한 DevTest Labs를 사용하여 중앙 IT 부서에서는 다음 작업을 수행하여 낭비를 제어하고 리소스에 대한 비용을 최적화하고 예산을 벗어나지 않을 수 있습니다.  

- VM에 대해 자동 종료 및 자동 시작 일정 설정
- 사용자가 만들 수 있는 VM 수에 대한 정책 설정
- 사용자가 선택하는 VM의 크기 및 갤러리 이미지에 대한 정책 설정
- 랩에서 비용 추적 및 목표 설정
- 랩에 대한 예상 비용이 높을 경우 필요한 작업을 수행할 수 있도록 알림 받기

DevTest Labs는 클라우드에서 환경을 만들고 구성하고 관리하는 데 다음과 같은 이점을 제공합니다.

## <a name="cost-control-and-governance"></a>비용 제어 및 거버넌스
DevTest Labs를 사용하면 다음 작업을 통해 비용을 더 쉽게 제어할 수 있습니다.

- 랩에 대해 사용자 또는 랩당 VM 수와 같은 [정책을 설정](devtest-lab-set-lab-policy.md)합니다. 
- VM을 자동으로 종료 및 시작하는 [정책](devtest-lab-set-lab-policy.md)을 만듭니다.
- 랩 내에서 실행되는 VM 및 PaaS 리소스에 대한 비용을 추적하여 [예산](devtest-lab-configure-cost-management.md)을 벗어나지 않도록 합니다.
- 외부 리소스를 실행하지 않도록 랩의 컨텍스트 내에서 유지합니다.

## <a name="quickly-get-to-ready-to-test"></a>빠른 테스트 준비
DevTest Labs를 사용하여 팀이 애플리케이션 개발 및 테스트를 시작하는 데 필요한 모든 사항이 장착된 미리 프로비전된 환경을 만들 수 있습니다. 애플리케이션의 마지막 빌드가 설치된 [환경을 클레임](devtest-lab-add-claimable-vm.md)하고 바로 작업을 시작합니다. 또는 더 빠르고 간결한 환경을 만들기 위해 컨테이너를 사용합니다.

## <a name="create-once-use-everywhere"></a>한 번 만들어서 어디에나 사용
팀 또는 조직 내에서 PaaS [환경 템플릿](devtest-lab-create-environment-from-arm.md) 및 [아티팩트](add-artifact-repository.md)(모두 소스 제어 유지)를 캡처하고 공유하여 개발자 환경 및 테스트 환경을 쉽게 만들 수 있습니다.

## <a name="worry-free-self-service"></a>걱정할 필요가 없는 셀프 서비스
DevTest Labs를 통해 개발자와 테스터는 미리 구성된 리소스 세트를 사용하여 [IaaS VM](devtest-lab-add-vm.md) 및 [PaaS 리소스](devtest-lab-create-environment-from-arm.md)를 빠르고 쉽게 만들 수 있습니다.

## <a name="use-iaas-and-paas-resources"></a>IaaS 및 PaaS 리소스 사용 
또한 개발자는 Resource Manager 템플릿을 사용하여 Azure Service Fabric 클러스터, Azure App Service의 Web Apps 기능 및 SharePoint 팜을 비롯한 PaaS 리소스를 실행할 수 있습니다. 랩에서 PaaS를 시작하려면 [퍼블릭 환경 리포지토리](devtest-lab-configure-use-public-environments.md)의 템플릿을 사용하거나 [고유한 Git 리포지토리에 랩을 연결](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories)합니다. 이러한 리소스에 대한 비용을 추적하여 예산을 벗어나지 않도록 할 수도 있습니다.

## <a name="integrate-with-your-existing-toolchain"></a>기존 도구 체인과 통합
미리 만들어져 있는 플러그 인 또는 Microsoft의 API를 활용하여 원하는 [CI(연속 통합) 도구](devtest-lab-integrate-ci-cd.md), IDE(통합 개발 환경) 또는 자동화된 릴리스 파이프라인에서 직접 개발/테스트 환경을 프로비저닝합니다. 포괄적인 명령줄 도구를 사용할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- DevTest Labs에 대해 자세히 알아보려면 [DevTest Labs 개념](devtest-lab-concepts.md)을 참조하세요.
- 단계별 지침이 포함된 연습은 [자습서: Azure DevTest Labs를 사용하여 랩 설정](tutorial-create-custom-lab.md)을 참조하세요.
