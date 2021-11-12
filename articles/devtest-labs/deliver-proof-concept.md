---
title: 개념 증명 제공
description: Azure DevTest Labs 엔터프라이즈 환경에 성공적으로 통합될 수 있도록 개념 증명을 제공하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 06/2/2020
ms.openlocfilehash: fcd3ba364887fe26b13c74e16f3f50990d708549
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325185"
---
# <a name="deliver-a-proof-of-concept"></a>개념 증명 제공 

Azure DevTest Labs에 대한 주요 시나리오 중 하나는 클라우드 내에서 개발 및 테스트 환경을 사용하도록 설정하는 것입니다. 다음은 이러한 템플릿의 예입니다.

* 클라우드에서 개발자 데스크톱 만들기
* 테스트 환경 구성하기
* 가상 머신 및 기타 Azure 리소스에 대한 액세스를 사용하도록 설정하기
* 개발자가 학습하고 실험해 볼 수 있는 샌드박스 영역 설정하기

DevTest Labs 정책 보호책 및 비용 제어를 통해 기업은 개발자에게 회사 보안, 규정 및 규정 준수 정책을 준수하는 "셀프 서비스 Azure"를 제공합니다. 

기업마다 Azure DevTest Labs를 해당 환경에 성공적으로 통합하는 방법에 대한 요구 사항이 다릅니다. 이 문서는 성공적인 개념 증명을 위해 엔터프라이즈에서 완료해야 하는 가장 일반적인 단계에 대해 설명합니다. 개념 증명은 성공적인 엔드투엔드 배포를 위한 첫 번째 단계입니다. 

## <a name="getting-started"></a>시작 

개념 증명 제공을 시작하는 방법에 대해 알아봅니다. 시간을 내어 Azure 및 DevTest Labs에 대해 알아보는 것이 중요합니다.  다음은 몇 가지 시작 리소스입니다. 

* [Azure Portal 이해](https://azure.microsoft.com/features/azure-portal/)
* [DevTest Labs의 기본 사항](devtest-lab-overview.md)
* [DevTest Labs 지원 시나리오](devtest-lab-guidance-get-started.md)
* [DevTest Labs 엔터프라이즈 설명서](devtest-lab-guidance-prescriptive-adoption.md)
* [Azure 네트워킹 소개](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>필수 구성 요소 

DevTest Labs를 사용하여 파일럿 또는 개념 증명을 성공적으로 완료하는 데 필요한 몇 가지 구성 요소가 있습니다. 

* **Azure 구독**: 기업은 종종 Azure에 액세스할 수 있게 하는 기존의 [기업계약](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/)을 보유하고 있으며, DevTest Labs에 대한 기존 또는 새로운 구독을 사용할 수 있습니다. 또는 기업에서 파일럿 중에 [Visual Studio 구독](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/)을 사용할 수도 있습니다(무료 Azure 크레딧 활용). 이러한 옵션을 모두 사용할 수 없는 경우 기업은 [무료 Azure 계정](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ)을 만들고 사용할 수 있습니다. 기업계약 있는 경우 [Enterprise 개발/테스트 구독이](https://azure.microsoft.com/offers/ms-azr-0148p/) 좋은 옵션입니다. Windows 10/Windows 8.1 클라이언트 운영 체제 및 개발 및 테스트 워크로드에 대한 할인된 요금에 액세스할 수 있습니다. 
* **Azure Active Directory 테넌트:** 사용자 추가 또는 랩 소유자 추가와 같은 관리의 경우 사용자는 파일럿에서 사용하는 Azure 구독에 대한 [Azure Active Directory 테넌트](https://azure.microsoft.com/services/active-directory/) 중 일부여야 합니다. 종종 기업은 사용자가 클라우드에서 온-프레미스 [ID를](../active-directory/hybrid/whatis-hybrid-identity.md) 사용할 수 있도록 하이브리드 ID를 설정합니다. DevTest Labs 파일럿에는 하이브리드 ID가 필요하지 않습니다. 

## <a name="scoping-of-the-pilot"></a>파일럿의 범위 

구현을 시작하기 전에 파일럿을 계획해야 합니다. 리소스가 지속적으로 유지되지 않을 것이란 점을 미리 알면 파일럿 사용자들에게 적절한 기대치를 설정할 수 있습니다. 

> [!IMPORTANT]
> 파일럿을 철저히 살피고 기대치를 솔직하게 설정하는 것이 매우 중요합니다.

파일럿을 시작하기 전에 다음과 같은 주요 질문을 자문해봅니다. 

* 학습하고자 하는 내용이 무엇이며, 무엇이 파일럿의 성공인가요? 
* 파일럿에서 다룰 워크로드 또는 시나리오는 무엇인가요? 파일럿의 범위를 지정하고 신속하게 완료할 수 있도록 작은 집합만 정의하는 것이 중요합니다. 
* 랩에서 사용할 수 있어야 하는 리소스는 무엇인가요? 예: 사용자 지정 이미지, 마켓플레이스 이미지, 정책, 네트워크 토폴로지 
* 환경을 확인하기 위해 파일럿에 관여하는 사용자 및 팀은 누구인가요?  
* 파일럿 기간이란 무엇인가요? 2주 또는 1개월과 같이 계획된 범위에 잘 맞는 기간을 선택합니다. 
* 파일럿이 완료되면 파일럿 중에 사용된 할당된 리소스에는 어떤 일이 발생하나요? 파일럿 리소스를 삭제할 계획인가요? 다음과 같이 생각할 수도 있습니다.
   
   "파일럿이 끝날 때 가상 머신과 랩을 버리려면 파일럿에 대한 단일 구독을 설정하고 모든 작업을 수행할 수 있습니다. 규모 롤아웃에 대한 질문을 병렬로 해결할 수 있습니다." 

파일럿을 "완벽"하게 만드는 경향이 있으므로 회사에서 서비스를 출시한 후의 최종 상태와 동일합니다. 이 가정은 false입니다. “완벽하게” 될 수록, 파일럿을 시작하기 *전에* 완료해야 할 것이 많아집니다. 파일럿의 목적은 최종 서비스를 확장하고 롤아웃할 때 적절한 결정을 내릴 수 있도록 하는 것입니다. 

파일럿은 필요한 최소 워크로드 및 의존도를 선택하고 Azure DevTest Labs 기업에 적합한지 여부를 결정하는 데 중점을 두어야 합니다. 빠르고 깔끔하게 성공할 수 있도록 가장 단순한 워크로드를 가장 적은 의존성으로 선택하는 것이 좋습니다. 가능하지 않은 경우 잠재적 복잡성을 노출하는 가장 대표적인 워크로드를 선택합니다. 따라서 스케일 아웃 단계에서 파일럿 성공을 복제할 수 있습니다. 

다음 예제에서는 올바른 범위의 개념 증명을 보여줍니다. 

## <a name="example-proof-of-concept-plan"></a>예: 개념 증명 계획 

이 섹션은 DevTest Labs의 파일럿 개념 증명의 범위를 지정하는 데 사용할 샘플을 보여 줍니다. 

> [!TIP]
> 실패에 대한 프로젝트 설정의 가능성을 최소화하려면 이 섹션에 설명된 예제를 건너뛰지 않는 것이 좋습니다. 

### <a name="overview"></a>개요 

엔터프라이즈는 DevTest Labs를 기반으로 새 Azure 환경을 개발할 계획입니다. 이 환경은 회사 네트워크에서 격리됩니다. 솔루션이 요구 사항을 충족하는지 확인하기 위해 개념 증명을 개발하여 엔드투엔드 솔루션의 유효성을 검사합니다. 이를 사용해 보고 환경을 확인할 수 있는 몇 가지 공급 업체를 포함했습니다. 

### <a name="outcomes"></a>결과 

개념 증명을 빌드할 때, 우선 결과에 집중합니다(수행하려는 작업). 개념 증명이 종료되면 다음을 기대할 수 있습니다. 

* 공급업체가 Azure Active Directory(Azure AD)의 게스트 계정을 사용하여 Azure의 격리된 환경에 액세스할 수 있는 엔드투엔드 작업 솔루션입니다. 환경에는 생산성을 유지하는 데 필요한 리소스가 있습니다. 
* 광범위한 규모의 사용 및 도입에 영향을 주는 잠재적 차단 문제를 열거하고 이해합니다.
* 개념 증명을 개발하는 개인은 모든 코드를 잘 이해하고 있습니다. 또한 관련된 참고 자료를 이해하고 광범위하게 적용합니다.

### <a name="open-questions-and-prerequisites"></a>질문 및 필수 구성 요소 열기 

* 이 프로젝트에 사용할 수 있는 구독을 만들었나요? 
* Azure AD 테넌트와, Azure AD와 관련된 질문에 대한 도움말과 참고 자료를 제공할 수 있는 식별된 Azure AD 전역 관리자가 있나요? 
* 프로젝트에서 작업하는 개인들이 공동 작업을 수행할 수 있는 장소가 있나요? 

   * 소스 코드 및 스크립트(예: Azure Repos) 
   * 문서(예: Microsoft Teams 또는 SharePoint)  
   * 대화(예: Microsoft Teams) 
   * 작업 항목(예: Azure Boards) 
* 공급업체에 필요한 리소스는 무엇인가요? 리소스에는 가상 머신과 다른 필수 서버 모두에서 로컬로 네트워크에서 사용할 수 있는 애플리케이션이 포함됩니다. 
* 가상 머신이 Azure의 도메인에 조인되나요? 그렇다면 이는 Azure AD DS(Azure Active Directory Domain Services)나 혹은 다른 것인가요? 
* 개념 증명의 대상이 되는 팀 또는 공급업체를 식별했나요? 환경에 대한 고객은 누구인가요?
* 개념 증명에 사용되는 Azure 지역은 어디인가요? 
* DevTest Labs를 통해 공급업체가 IaaS(VM) 외에도 사용할 수 있는 서비스 목록이 있나요? 
* 랩을 사용하여 공급업체/사용자를 학습시키려면 어떻게 해야 하나요? 

### <a name="components-of-the-proof-of-concept-solution"></a>개념 증명 솔루션의 구성 요소 

솔루션에는 다음과 같은 구성 요소가 필요합니다. 

* 다양한 공급업체 팀이 Azure에서 랩 집합을 사용합니다.
* 랩은 요구 사항을 지원하는 네트워크 인프라에 연결됩니다.
* 공급업체는 Azure AD 및 역할 할당을 통해 랩에 액세스할 수 있습니다.
* 공급업체는 해당 리소스에 성공적으로 연결하는 방법을 제공합니다. 특히, 사이트 간 VPN을 사용하면 공용 IP 주소 없이 직접 가상 머신에 액세스할 수 있습니다.
* 아티팩트 집합에는 공급업체가 가상 머신에 필요로 하는 필수 소프트웨어가 포함됩니다.

## <a name="other-planning-and-design-decisions"></a>기타 계획 및 디자인 결정 

전체 DevTest Labs 솔루션을 릴리스하기 전에 몇 가지 중요한 계획과 설계 결정을 내려야 합니다. 개념 증명을 사용하여 작업하는 환경은 이러한 결정을 내리는 데 도움이 될 수 있습니다. 추가 고려 사항은 다음과 같습니다. 

* **구독 토폴로지**: Azure의 리소스에 대한 엔터프라이즈 수준의 요구 사항은 [단일 구독 내에서 사용 가능한 할당량](../azure-resource-manager/management/azure-subscription-service-limits.md)을 초과하여 확장할 수 있습니다. 초기 구독 제한을 늘리려면 여러 Azure 구독 또는 서비스 요청이 필요할 수 있습니다. 나중에 리소스를 다른 구독으로 이동하기 어렵기 때문에 구독 간에 리소스를 배포하는 방법을 미리 결정하는 것이 중요합니다. 예를 들어 랩을 만든 후에는 다른 구독으로 이동할 수 없습니다. 한 가지 중요한 리소스는 [구독 결정 가이드](/azure/architecture/cloud-adoption/decision-guides/subscriptions/)입니다.   
* **네트워크 토폴로지**: DevTest Labs에서 자동으로 만드는 [기본 네트워크 인프라](../app-service/networking-features.md)는 엔터프라이즈 사용자에 대한 요구 사항 및 제약 조건을 충족하기에 충분하지 않을 수 있습니다. 일반적으로 [Azure ExpressRoute 연결된 가상 네트워크](/azure/architecture/reference-architectures/hybrid-networking/), 구독 간 연결에 대한 [허브 및 스포크](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke), 온-프레미스 연결만을 확인하기 위한 [강제 라우팅](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)을 볼 수 있습니다. DevTest Labs는 랩에서 새 가상 머신을 만들 때 기존 가상 네트워크를 랩에 연결하여 사용할 수 있도록 합니다. 
* **가상 머신 원격 액세스**: DevTest Labs에 있는 가상 머신에 원격으로 액세스하는 다양한 옵션이 있습니다. 가장 쉬운 방법은 공용 IP 또는 공유 공용 IP를 사용하는 것입니다. [이러한 설정은](devtest-lab-shared-ip.md) 랩에서 사용할 수 있습니다. 이러한 옵션이 충분하지 않은 경우 원격 액세스 게이트웨이를 사용하는 것도 옵션입니다. [DevTest Labs 엔터프라이즈 참조 아키텍처에는](devtest-lab-reference-architecture.md) 이 옵션이 표시됩니다. 자세한 내용은 [원격 데스크톱 게이트웨이 사용하도록 랩 구성을](configure-lab-remote-desktop-gateway.md)참조하세요. 또한 기업에서는 ExpressRoute 또는 사이트 간 VPN을 사용하여 랩을 온-프레미스 네트워크에 연결할 수 있습니다. 이 옵션을 사용하면 개인 IP 주소를 기반으로 가상 머신에 대한 직접 원격 데스크톱 또는 SSH 연결을 사용할 수 있습니다. 인터넷에 노출되지 않습니다. 
* **권한 처리**: DevTest Labs에서 일반적으로 사용되는 두 가지 주요 권한은 [소유자 및 랩 사용자](devtest-lab-add-devtest-user.md)입니다. DevTest Labs를 배포하기 전에 랩의 각 액세스 수준을 위임할 사람을 광범위하게 결정하는 것이 중요합니다. 일반적인 모델은 예산 소유자(예: 팀 리더)를 랩 소유자로, 팀 멤버를 랩 사용자로 하는 것입니다. 예산을 담당하는 사람(팀 리더)은 이 모델을 통해 정책 설정을 조정하고 팀을 예산 내로 유지할 수 있습니다.  

## <a name="completing-the-proof-of-concept"></a>개념 증명 완료 

예상 학습을 다뤄낸 후에는 파일럿을 완료해야 합니다. 사용자로부터 피드백을 수집하고, 파일럿이 성공했는지 확인하고, 조직이 DevTest Labs의 엔터프라이즈 규모 출시를 진행할지 여부를 결정합니다. 확장 출시 전체에서 일관성을 유지하기 위해 DevTest Labs 및 관련 리소스의 배포를 자동화하는 것도 좋습니다. 

## <a name="next-steps"></a>다음 단계 

* [DevTest Labs 엔터프라이즈 설명서](devtest-lab-guidance-prescriptive-adoption.md)
* [엔터프라이즈용 참조 아키텍처](devtest-lab-reference-architecture.md)
* [DevTest Labs 배포 스케일 업](devtest-lab-guidance-orchestrate-implementation.md)
* [Azure DevTest Labs의 구현 오케스트레이션](devtest-lab-guidance-orchestrate-implementation.md)
