---
title: B2B 엔터프라이즈 통합 워크플로
description: Azure Logic Apps 및 엔터프라이즈 통합 팩을 사용하여 엔터프라이즈 통합을 위한 자동화된 B2B 워크플로를 빌드하는 방법을 알아봅니다.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: overview
ms.date: 09/14/2021
ms.openlocfilehash: 91ea9eb37e7c4e2e97513a1496a52d2521e9f4b2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128652145"
---
# <a name="b2b-enterprise-integration-workflows-with-azure-logic-apps-and-enterprise-integration-pack"></a>Azure Logic Apps 및 엔터프라이즈 통합 팩을 사용하는 B2B 엔터프라이즈 통합 워크플로

B2B(business-to-business) 솔루션 및 조직 간의 원활한 통신을 위해 EIP(엔터프라이즈 통합 팩)와 함께 [Azure Logic Apps](logic-apps-overview.md)를 사용하여 자동화되고 확장 가능한 엔터프라이즈 통합 워크플로를 빌드할 수 있습니다.

## <a name="what-is-the-enterprise-integration-pack"></a>엔터프라이즈 통합 팩이란?

Microsoft BizTalk Server 또는 Azure BizTalk Services에 익숙한 경우 EIP는 유사한 개념을 따르고 B2B 기능을 사용하기 쉽게 만듭니다. 그러나 한 가지 주요 차이점은 EIP가 아키텍처 측면에서 *통합 계정* 을 기반으로 한다는 것입니다. 이러한 계정은 B2B 통신을 위해 [거래 파트너](logic-apps-enterprise-integration-partners.md), [계약](logic-apps-enterprise-integration-agreements.md), [맵](logic-apps-enterprise-integration-maps.md), [스키마](logic-apps-enterprise-integration-schemas.md), [인증서](logic-apps-enterprise-integration-certificates.md) 등을 포함한 B2B 아티팩트를 저장, 관리 및 사용하는 방법을 간소화하는 Azure의 클라우드 기반 컨테이너입니다.

이러한 아티팩트를 사용하면 Azure Logic Apps를 [400개 이상의 기본 제공 작업 및 관리형 커넥터](/connectors/connector-reference/connector-reference-logicapps-connectors)와 함께 사용하여 Azure, Microsoft 및 기타 SaaS(Software as Service) 앱, 온-프레미스 시스템 및 사용자 지정 앱과 같은 클라우드 서비스를 포함하는 B2B 워크플로 및 통합 솔루션을 빌드할 수 있습니다. 예를 들어 기본 제공 코드 실행 작업 및 Azure Functions를 사용하여 워크플로에서 사용자 지정 코드를 만들고 실행할 수 있습니다. 다음 업계 표준을 지원하는 [엔터프라이즈 통합 커넥터](../connectors/managed.md#enterprise-connectors)를 사용할 수도 있습니다.

* EDI(전자 데이터 교환)
* EAI(Enterprise Application Integration)

조직에서 다양한 프로토콜과 형식을 B2B 통신에 사용하는 경우에도 다른 조직과 메시지를 전자적으로 교환할 수 있습니다. [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md), [EDIFACT](logic-apps-enterprise-integration-edifact.md) 및 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md)을 포함한 업계 표준 프로토콜을 지원하는 EIP를 사용하여 이러한 다양한 형식을 조직의 시스템에서 처리할 수 있는 형식으로 변환할 수 있습니다. 또한 암호화와 디지털 서명을 모두 사용하여 메시지 보안을 향상시킬 수 있습니다.

## <a name="what-do-i-need-to-get-started"></a>시작하려면 어떻게 해야 하나요?

* Azure 계정 및 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* 정의하고 사용하려는 B2B 아티팩트를 저장하기 위한 [통합 계정](logic-apps-enterprise-integration-create-integration-account.md)

* B2B 아티팩트(예: [거래 파트너](logic-apps-enterprise-integration-partners.md), [계약](logic-apps-enterprise-integration-agreements.md), [맵](logic-apps-enterprise-integration-maps.md), [스키마](logic-apps-enterprise-integration-schemas.md), [인증서](logic-apps-enterprise-integration-certificates.md) 등)

* 맵과 스키마를 만들려면 [Microsoft Azure Logic Apps 엔터프라이즈 통합 도구 확장](https://aka.ms/vsenterpriseintegrationtools) 및 Visual Studio 2019를 사용하면 됩니다. Visual Studio 2015를 사용하는 경우 [Visual Studio 2015용 Microsoft Azure Logic Apps 엔터프라이즈 통합 도구 2.0](https://aka.ms/vsmapsandschemas) 확장을 사용할 수 있습니다.

   > [!IMPORTANT]
   > 이 확장을 BizTalk Server 확장과 함께 설치하지 마세요. 두 확장이 있으면 예기치 않은 동작이 발생할 수 있습니다. 이러한 확장 중 하나만 설치되어 있는지 확인합니다.

   > [!NOTE]
   > 고해상도 모니터의 경우 Visual Studio의 [맵 디자이너에서 표시 문제](/visualstudio/designers/disable-dpi-awareness)가 발생할 수 있습니다. 이 표시 문제를 해결하려면 [DPI 인식 안 함 모드에서 Visual Studio를 다시 시작](/visualstudio/designers/disable-dpi-awareness#restart-visual-studio-as-a-dpi-unaware-process)하거나, [DPIUNAWARE 레지스트리 값](/visualstudio/designers/disable-dpi-awareness#add-a-registry-entry)을 추가합니다.

통합 계정을 만들고 아티팩트를 추가한 후에는 논리 앱을 만들어 B2B 워크플로 빌드를 시작할 수 있습니다. 논리 앱을 처음 접하는 경우 [기본 논리 앱 워크플로 예제를 만들어 보세요](quickstart-create-first-logic-app-workflow.md). [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md), [Visual Studio](quickstart-create-logic-apps-with-visual-studio.md) 또는 [PowerShell](/powershell/module/az.logicapp)을 사용하여 논리 앱을 만들고, 관리하고, 배포할 수도 있습니다.

> [!IMPORTANT]
> **논리 앱(소비)** 리소스 종류를 사용하는 경우 워크플로에서 사용할 B2B 아티팩트를 선택하려면 먼저 통합 계정을 논리 앱 리소스에 연결해야 합니다. 그러나 이러한 아티팩트를 정의하고 통합 계정에 추가하려면 논리 앱 리소스가 아직 필요하지 않습니다.
>
> **논리 앱(표준)** 리소스 종류를 사용하는 경우 스키마와 맵을 논리 앱 리소스에 직접 추가하고 *동일한 논리 앱 리소스* 내의 여러 워크플로에서 해당 아티팩트를 사용할 수 있습니다. 
> 파트너 및 계약과 같은 다른 아티팩트를 저장하려면 통합 계정이 여전히 필요하지만, 연결이 더 이상 필요하지 않으므로 이 기능이 존재하지 않습니다. 이러한 리소스 종류에 대한 자세한 내용은 [Azure Logic Apps란? - 리소스 종류 및 호스트 환경](logic-apps-overview.md#resource-type-and-host-environment-differences)을 참조하세요.

다음 다이어그램에서는 B2B 논리 앱 워크플로 빌드를 시작하는 개략적인 단계를 보여 줍니다.

![B2B 논리 앱 워크플로를 만들기 위한 필수 구성 요소 단계를 보여 주는 개념적 다이어그램](media/logic-apps-enterprise-integration-overview/overview.png)

## <a name="try-now"></a>지금 사용해보기

[AS2 메시지를 보내고 받는 완전히 작동하는 샘플 논리 앱 배포](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.logic/logic-app-as2-send-receive)

## <a name="next-steps"></a>다음 단계

* [거래 업체 만들기](logic-apps-enterprise-integration-partners.md)
* [규약 만들기](logic-apps-enterprise-integration-agreements.md)
* [스키마 추가](logic-apps-enterprise-integration-schemas.md)
* [맵 추가](logic-apps-enterprise-integration-maps.md)
* [BizTalk Services에서 마이그레이션](logic-apps-move-from-mabs.md)
