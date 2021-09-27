---
title: Azure DevTest Labs를 사용하기 위한 일반 시나리오
description: 이 문서에서는 Azure DevTest Labs를 사용하기 위한 기본 시나리오와 조직에서 서비스를 사용하기 위한 두 가지 일반적인 경로를 제공합니다.
ms.topic: conceptual
ms.date: 06/20/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: fc4c7ca1cdbc8e503ad6c0d94fcec2d9697baae4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128657484"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Azure DevTest Labs를 사용하기 위한 일반 시나리오
엔터프라이즈의 요구에 따라 다양한 요구 사항을 충족하도록 DevTest Labs를 구성할 수 있습니다.  이 문서에서는 일반 시나리오에 대해 설명합니다. 각 시나리오에서는 DevTest Labs를 사용하여 얻은 이점과 이러한 시나리오를 구현하는 데 사용할 리소스를 다룹니다.  

- 개발자 데스크톱
- 테스트 환경
- 교육 세션, 실습 랩 및 해커톤
- 샌드박스 조사
- 클래스룸 랩

## <a name="developer-desktops"></a>개발자 데스크톱
개발자가 각 프로젝트에 사용하는 개발 컴퓨터의 요구 사항은 서로 다른 경우가 많습니다. DevTest Labs를 사용하면 개발자가 가장 일반적인 시나리오에 맞게 구성된 주문형 가상 머신에 액세스할 수 있습니다. DevTest Labs에서는 다음과 같은 이점을 제공합니다.

- 조직은 팀 간의 일관성을 유지하는 공통 개발 컴퓨터를 제공할 수 있습니다.
- 개발자는 필요에 따라 개발 컴퓨터를 신속하게 프로비전하거나 [미리 구성된 기존 컴퓨터를 요청](devtest-lab-add-claimable-vm.md)할 수 있습니다.
- 개발자는 구독 수준 권한 없이도 셀프 서비스 방식으로 리소스를 프로비전할 수 있습니다.
- IT 담당자나 관리자는 [네트워킹 토폴로지를 미리 정의](devtest-lab-configure-vnet.md)할 수 있으며, 개발자는 특별한 액세스 권한 없이도 단순하고 직관적인 방식으로 이 토폴로지를 직접 활용할 수 있습니다.
- 개발자는 필요할 때마다 개발 컴퓨터를 쉽게 [사용자 지정](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm)할 수 있습니다.
- 관리자는 다음을 확인하여 비용을 제어할 수 있습니다.
    - 개발자는 개발에 필요한 것보다 [더 많은 VM을 가져올 수 없습니다](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user).
    - VM은 사용하지 않을 때 [종료](devtest-lab-set-lab-policy.md#set-auto-shutdown)됩니다.
    - 특정 랩에 대해 [VM 인스턴스 크기의 하위 집합만 사용하도록 허용](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes)합니다.
    - 각 랩에 대한 [비용 목표 및 알림을 관리](devtest-lab-configure-cost-management.md)합니다.

자세한 내용은 [개발자를 위한 Azure DevTest Labs 사용](devtest-lab-developer-lab.md)을 참조하세요. 

## <a name="test-environments"></a>테스트 환경
기업 전반에서 테스트 환경을 만들고 관리하려면 많은 작업을 수행해야 합니다. DevTest Labs를 사용하면 쉽게 테스트 환경을 만들거나, 업데이트하거나, 복제할 수 있습니다. 이를 통해 팀은 테스트 환경이 필요할 때 완전히 구성된 환경에 액세스할 수 있습니다. 이 시나리오에서 DevTest Labs는 다음과 같은 이점을 제공합니다.

- 조직은 팀 간의 일관성을 유지하는 공통 테스팅 환경을 제공할 수 있습니다.
- 테스터는 재사용 가능한 템플릿으로 Windows와 Linux 환경을 빠르게 프로비전하여 최신 버전의 애플리케이션을 테스트할 수 있습니다.
- 관리자는 랩을 Azure DevOps에 연결하여 DevOps 시나리오를 사용하도록 설정할 수 있습니다.
- 랩 소유자는 다음을 확인하여 비용을 제어할 수 있습니다.
    - 사용하지 않을 때 환경의 VM은 [종료](devtest-lab-set-lab-policy.md#set-auto-shutdown)됩니다.
    - 특정 랩에 대해 [VM 인스턴스 크기의 하위 집합만 사용하도록 허용](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes)합니다.
    - 각 랩에 대한 [비용 목표 및 알림을 관리](devtest-lab-configure-cost-management.md)합니다.

자세한 내용은 [VM 및 PaaS 테스트 환경에 Azure DevTest Labs 사용](devtest-lab-test-env.md)을 참조하세요.

## <a name="sandboxed-investigations"></a>샌드박스 조사
개발자는 종종 다른 기술 또는 인프라 디자인을 조사합니다. 기본적으로 DevTest Labs를 사용하여 만든 모든 환경은 자체 리소스 그룹에 만들어집니다. DevTest Labs 사용자는 해당 리소스에 대한 읽기 액세스 권한만 가져옵니다. 그러나 더 많은 제어를 해야 하는 개발자의 경우에는 랩 차원의 설정을 업데이트하여 자신이 만드는 모든 환경에 대해 원래 DevTest Labs 사용자에게 [참가자 권한](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/)을 부여할 수 있습니다.  DevTest Labs를 사용하여 개발자는 랩에서 만드는 환경에 대한 참가자 권한을 자동으로 부여할 수 있습니다.  이러한 경우 개발자는 개발 또는 테스트 환경에 필요한 Azure 리소스를 추가 및/또는 변경할 수 있습니다. [리소스 비용](devtest-lab-configure-cost-management.md#view-cost-by-resource) 페이지에서 랩 소유자는 조사에 사용되는 각 환경의 비용을 추적할 수 있습니다.

자세한 내용은 [환경 리소스 그룹에 대한 액세스 권한 설정](https://aka.ms/dtl-sandbox)을 참조하세요.

## <a name="trainings-hands-on-labs-and-hackathons"></a>교육, 실습 랩 및 해커톤 
Azure DevTest Labs의 랩은 워크샵, 실습 랩, 교육 또는 해커톤과 같은 일시적인 활동에 적합한 컨테이너로 사용할 수 있습니다.  이 서비스에서는 각 실습생이 동일한 격리형 학습용 환경을 만드는 데 사용할 수 있는 사용자 지정 템플릿을 제공할 수 있는 랩을 만들 수 있습니다. 이 시나리오에서 DevTest Labs는 다음과 같은 이점을 제공합니다.

- [정책](devtest-lab-set-lab-policy.md)을 통해 실습생은 필요한 리소스(예: 가상 머신)의 수만 얻을 수 있습니다.
- 미리 구성된 컴퓨터와 만든 컴퓨터는 실습생의 단일 작업으로 [요청](devtest-lab-add-claimable-vm.md)됩니다.
- 랩은 [랩의 URL](/azure/devtest-labs/devtest-lab-faq#how-do-i-share-a-direct-link-to-my-lab)에 액세스하여 실습생과 공유됩니다.
- 가상 머신의 [만료 날짜](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs)는 더 이상 필요하지 않은 컴퓨터를 삭제하도록 합니다.
- 교육이 끝나면 랩 및 모든 [관련 리소스](/azure/devtest-labs/devtest-lab-faq#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab)를 쉽게 [삭제](devtest-lab-delete-lab-vm.md#delete-a-lab)할 수 있습니다.

자세한 내용은 [교육에 DevTest Labs 사용](devtest-lab-training-lab.md)을 참조하세요.  

## <a name="proof-of-concept-vs-scaled-deployment"></a>개념 증명 및 크기가 조정된 배포 비교
DevTest Labs 탐색하기로 한 경우, 대개 개념 증명 또는 크기가 조정된 배포의 두 가지 일반 경로 중 하나를 선택합니다.  

**크기가 조정된 배포** 에서는 개발자 수가 매우 많은 기업 전체에 DevTest Labs를 배포하기 위해 몇 주/몇 달간 검토 및 계획을 진행합니다.

**개념 증명** 배포에서는 조직 가치를 설정하기 위한 단일 팀의 집중적인 노력에 초점을 맞추고 있습니다. 크기가 조정된 배포가 더 쉬워 보일 수도 있지만 이 방식은 개념 증명 옵션보다 실패 빈도가 더 높습니다. 따라서 일단은 소규모로 배포를 시작하고 첫 번째 팀에서 필요한 정보를 파악한 다음 2~3개 팀을 추가해 동일한 방식을 반복한 후에 확인된 사실을 바탕으로 크기가 조정된 배포를 계획하는 것이 좋습니다. 개념을 적절하게 증명하려면 1~2개 팀을 선택하여 해당 시나리오(개발 환경과 테스트 환경용 시나리오)를 파악하고 현재 사용 사례를 문서로 작성한 다음 DevTest Labs를 배포하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 읽어보세요.

- [DevTest Lab 개념](devtest-lab-concepts.md)
- [DevTest Labs FAQ](devtest-lab-faq.yml)