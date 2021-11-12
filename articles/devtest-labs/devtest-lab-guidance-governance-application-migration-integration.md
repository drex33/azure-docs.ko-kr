---
title: 애플리케이션 마이그레이션 및 통합
description: 이 문서에서는 Azure DevTest Labs 인프라를 위한 거 버 넌 스 지침을 제공 합니다. 컨텍스트는 응용 프로그램 마이그레이션 및 통합입니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 0c80db4ef4d9fa6b3b58d84d663a84b89f93219f
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397626"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>Azure DevTest Labs 인프라의 거버넌스 - 애플리케이션 마이그레이션 및 통합
개발/테스트 랩 환경을 설정한 후에는 다음 사항을 고려해야 합니다.

- 프로젝트 팀 내에서 환경을 사용 하는 방법
- 조직의 필수 정책을 준수하면서 지속적으로 애플리케이션에 유용한 기능을 신속하게 추가할 방법

## <a name="azure-marketplace-images-vs-custom-images"></a>Azure Marketplace 이미지와 사용자 지정 이미지 비교

### <a name="question"></a>질문
Azure Marketplace 이미지와 고유한 사용자 지정 조직 이미지는 각각 어떤 경우에 사용해야 하나요?

### <a name="answer"></a>답변
구체적인 문제나 조직 요구 사항이 없다면 기본적으로 Azure Marketplace 이미지를 사용해야 합니다. 사용자 지정 조직 이미지를 사용해야 하는 몇 가지 일반적인 예는 다음과 같습니다.

- 기본 이미지의 일부분으로 애플리케이션을 포함해야 하는 복잡한 애플리케이션 설정
- 애플리케이션 설치와 설정에 몇 시간이 걸릴 수 있습니다. 이는 Azure Marketplace 이미지에 추가되는 컴퓨팅 시간을 효율적으로 사용하는 것이 아닙니다.
- 개발자 및 테스터가 가상 머신에 빠르게 액세스해야 하며 새 가상 머신 설정 시간을 최소화하려는 경우
- 모든 시스템에 대해 규정 준수 또는 규제 조건(예: 보안 정책)을 적용해야 하는 경우

사용자 지정 이미지를 주의 해 서 사용 하십시오. 사용자 지정 이미지는 이제 기본 이미지에 대 한 VHD 파일을 관리 해야 하므로 더 복잡 합니다. 그리고 소프트웨어 업데이트를 사용하여 해당 기본 이미지를 정기적으로 패치해야 합니다. 이러한 업데이트에는 새 OS(운영 체제) 업데이트와 소프트웨어 패키지 자체에 필요한 업데이트 또는 구성 변경 내용이 포함됩니다.

## <a name="formula-vs-custom-image"></a>수식과 사용자 지정 이미지 비교

### <a name="question"></a>질문
수식과 사용자 지정 이미지는 각각 어떤 경우에 사용해야 하나요?

### <a name="answer"></a>답변
일반적으로는 비용과 재사용 여부에 따라 사용할 항목을 결정합니다.

다음과 같은 경우 사용자 지정 이미지를 만들어 비용을 줄일 수 있습니다.
- 많은 사용자 또는 랩에서 이미지를 요구 합니다.
- 필수 이미지에는 기본 이미지 위에 많은 소프트웨어가 있습니다.

이 솔루션은 이미지를 한 번만 만드는 것을 의미 합니다. 사용자 지정 이미지는 가상 머신의 설치 시간을 단축 합니다. 설치 하는 동안 가상 컴퓨터를 실행 하는 비용을 초래 하지 않습니다.

또 다른 요소는 소프트웨어 패키지에 대 한 변경 빈도입니다. 매일 빌드를 실행 하 고 해당 소프트웨어가 사용자의 가상 컴퓨터에 있어야 하는 경우 사용자 지정 이미지 대신 수식을 사용 하는 것이 좋습니다.

## <a name="use-custom-organizational-images"></a>사용자 지정 조직 이미지 사용

### <a name="question"></a>질문
사용자 지정 조직 이미지를 DevTest Labs 환경으로 가져오기 위해 손쉽게 반복 가능한 프로세스를 설정하려면 어떻게 해야 하나요?

### <a name="answer"></a>답변
[이 비디오에서 Image Factory 패턴](./devtest-lab-faq.yml#blog-post)을 확인해 보세요. 이 시나리오는 고급 방식이며 샘플 스크립트만 제공합니다. 스크립트를 변경해야 하는 경우 환경에서 사용되는 스크립트를 직접 유지하고 관리해야 합니다.

DevTest Labs를 사용하여 Azure Pipelines에서 사용자 지정 이미지 파이프라인 만들기:

- [소개: Azure DevTest Labs에서 이미지 팩터리를 설정하여 몇 분 내에 VM 준비](./devtest-lab-faq.yml#blog-post)
- [Image Factory – Part 2! VM 생성을 위한 Azure Pipelines 및 Factory Lab 설정](./devtest-lab-faq.yml#blog-post)
- [Image Factory – 3부: 사용자 지정 이미지 저장 및 여러 랩에 배포](./devtest-lab-faq.yml#blog-post)
- [비디오: Azure DevTest Labs를 통해 사용자 지정 Image Factory 만들기](./devtest-lab-faq.yml#blog-post)

## <a name="patterns-to-set-up-network-configuration"></a>네트워크 구성을 설정하는 패턴

### <a name="question"></a>질문
개발 및 테스트 가상 머신이 공용 인터넷에 연결하지 못하도록 하려면 어떻게 해야 하나요? 네트워크 구성 설정을 위한 권장 패턴이 있나요?

### <a name="answer"></a>답변
예. 인바운드 트래픽과 아웃바운드 트래픽의 두 가지 측면을 고려해야 합니다.

**인바운드 트래픽** – 가상 머신에 공용 IP 주소가 없는 경우 인터넷에서 연결할 수 없습니다. 일반적인 방법은 사용자가 공용 IP 주소를 만들 수 없는 구독 수준 정책을 설정 하는 것입니다.

**아웃 바운드 트래픽** – 가상 머신이 공용 인터넷으로 직접 이동 하지 못하도록 하 고 회사 방화벽을 통해 트래픽을 강제로 적용 하려면 강제 라우팅을 사용 하 여 Azure express 경로 또는 VPN을 통해 온-프레미스에서 트래픽을 라우팅할 수 있습니다.

> [!NOTE]
> 프록시 설정을 사용하지 않고 트래픽을 차단하는 프록시 서버가 있다면 랩의 아티팩트 스토리지 계정에 예외를 추가해야 합니다.

가상 머신이나 서브넷용 네트워크 보안 그룹을 사용할 수도 있습니다. 이 단계에서는 트래픽을 허용 하거나 차단 하는 또 다른 보호 계층을 추가 합니다.

## <a name="new-vs-existing-virtual-network"></a>신규 가상 네트워크와 기존 가상 네트워크 비교

### <a name="question"></a>질문
DevTest Labs 환경용으로 신규 가상 네트워크를 생성해야 하는 경우와 기존 가상 네트워크를 사용해야 하는 경우는 각각 언제인가요?

### <a name="answer"></a>답변
Vm이 기존 인프라와 상호 작용 해야 하는 경우 DevTest Labs 환경 내에서 기존 가상 네트워크를 사용 하는 것을 고려해 야 합니다. Express 경로를 사용 하는 경우 구독에 할당 된 IP 주소 공간을 조각화 하지 않도록 가상 네트워크 및 서브넷 수를 최소화 합니다. 여기에서 가상 네트워크 피어 링 패턴 (허브-스포크 모델)을 사용 하는 것도 고려 합니다. 이 방법을 사용 하면 지정 된 지역 내에서 구독 간에 가상 네트워크 및 서브넷을 통신할 수 있습니다.

각 DevTest Labs 환경에는 자체 가상 네트워크가 있을 수 있지만 구독 당 가상 네트워크 수에는 [제한이](../azure-resource-manager/management/azure-subscription-service-limits.md) 있습니다. 기본 수는 50이지만 100까지 높일 수 있습니다.

## <a name="shared-public-or-private-ip"></a>공유, 공용 또는 프라이빗 IP

### <a name="question"></a>질문
공유 IP, 공용 IP, 프라이빗 IP는 각각 어떠한 경우에 사용해야 하나요?

### <a name="answer"></a>답변
사이트 간 VPN 또는 Express 경로를 사용하는 경우에는 내부 네트워크에서만 머신에 액세스할 수 있고 공용 인터넷을 통해서는 액세스할 수 없도록 프라이빗 IP 사용을 고려합니다.

> [!NOTE]
> 랩 소유자는 사용자가 자신의 VM에 대해 공용 IP 주소를 실수로 만들지 못하도록 이 서브넷 정책을 변경할 수 있습니다. 구독 소유자는 공용 IP 만들기를 금지하는 구독 정책을 만들어야 합니다.

공유 공용 IP 사용 시에는 랩의 가상 머신이 공용 IP 주소를 공유합니다. 지정된 구독의 공용 IP 주소 제한 위반을 방지해야 하는 경우 이 방식을 사용하면 유용할 수 있습니다.

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>사용자 또는 랩당 가상 머신 수 제한

### <a name="question"></a>질문
사용자 당 또는 랩 당 설정 해야 하는 가상 컴퓨터의 수에 대 한 규칙이 있나요?

### <a name="answer"></a>답변
사용자/랩당 가상 머신 수를 고려할 때는 다음의 세 가지 요소를 주로 파악해야 합니다.

- 팀이 랩의 리소스에 재출할 수 있는 **전체 비용**. 여러 컴퓨터를 스핀업하는 것 자체는 간단합니다. 비용을 제어 하기 위한 한 가지 메커니즘은 사용자 당 또는 랩 당 Vm 수를 제한 하는 것입니다.
- 랩의 총 가상 머신 수는 사용 가능한 [구독 수준 할당량](../azure-resource-manager/management/azure-subscription-service-limits.md)에 따라 달라집니다. 예를 들어 구독당 리소스 그룹 800개 등의 상한이 적용될 수 있습니다. 공유 공용 IP를 사용하는 경우가 아니면 DevTest Labs는 현재 각 VM에 대해 새 리소스 그룹을 만듭니다. 구독에 10개의 랩이 있는 경우 각 랩에 약 79개의 가상 머신이 포함될 수 있습니다(상한 800개 – 10개 랩 자체의 리소스 그룹 10개) = 랩당 79개의 가상 머신.
- 랩이 Express 경로 등을 통해 온-프레미스에 연결되는 경우에는 VNet/서브넷용으로 **정의된 사용 가능한 IP 주소 공간** 이 있습니다. ‘IP 주소를 가져올 수 없음’ 오류로 인해 랩에서 VM 생성이 실패하는 일이 없도록 하려는 경우 랩 소유자는 사용 가능한 IP 주소 공간에 맞게 랩당 최대 VM 수를 적절하게 지정할 수 있습니다.

## <a name="use-resource-manager-templates"></a>Resource Manager 템플릿 사용

### <a name="question"></a>질문
DevTest Labs 환경 내에서 Resource Manager 템플릿을 사용하려면 어떻게 해야 하나요?

### <a name="answer"></a>답변
[테스트 환경에 대해 Azure DevTest Labs 사용](devtest-lab-test-env.md)의 단계를 사용 하 여 리소스 관리자 템플릿을 배포 합니다. 기본적으로 리소스 관리자 템플릿은 Azure Repos 또는 GitHub Git 리포지토리에 체크 인하고 [템플릿에 대 한 개인 리포지토리](devtest-lab-test-env.md) 를 랩에 추가 합니다.

DevTest Labs를 사용 하 여 개발 컴퓨터를 호스트 하는 경우에는이 시나리오가 유용 하지 않을 수 있습니다. 이 시나리오를 사용 하 여 프로덕션을 대표 하는 스테이징 환경을 구축 합니다.

랩 당 가상 머신 수 또는 사용자 단위 옵션은 기본적으로 랩 자체에서 생성 되는 컴퓨터의 수만 제한 합니다. 이 옵션은 리소스 관리자 템플릿이 있는 환경에서 만들기를 제한 하지 않습니다.

## <a name="next-steps"></a>다음 단계
[DevTest Labs에서 환경 사용](devtest-lab-test-env.md)을 참조하세요.