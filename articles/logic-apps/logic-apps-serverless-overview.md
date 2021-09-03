---
title: 앱 개발 증가, 인프라 관리 감소
description: Azure 서버리스는 Azure Logic Apps와 Azure Functions를 사용할 때 인프라 관리 비용을 줄이면서 클라우드 기반 앱 생성에 집중하는 데 도움이 됩니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 07/15/2021
ms.openlocfilehash: 335a553897f2487eef317852f23cd475a92b2f81
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114362683"
---
# <a name="azure-serverless-overview-create-cloud-based-apps-and-solutions-with-azure-logic-apps-and-azure-functions"></a>Azure 서버리스 개요: Azure Logic Apps 및 Azure Functions를 사용하여 클라우드 기반 앱과 솔루션 만들기

서버리스가 “서버 없음”을 의미하지는 않지만 Azure 서버리스는 인프라 관리 비용을 줄이는 데 도움이 됩니다. 기존 앱 개발에서는 앱 요구 사항과 요청에 맞는 호스팅, 스케일링, 모니터링 솔루션을 논의하고 해결하는 데 많은 시간과 에너지가 사용될 수 있습니다. 서버리스 앱과 솔루션을 사용하면 해당 문제를 앱이나 솔루션의 일부로 더 쉽게 처리할 수 있습니다. 서버리스는 개발 가속화, 코드 감소, 단순함, 스케일링 유연성과 같은 다른 이점도 제공합니다. 이 모든 기능을 통해 비즈니스 논리에 더 집중할 수 있게 됩니다. 또한 서버리스는 일반적으로 사용량을 기준으로 요금이 청구되거나 부과됩니다. 따라서 사용하지 않으면 요금도 발생하지 않습니다. 자세한 내용은 [Azure 서버리스](https://azure.microsoft.com/solutions/serverless/)를 참조하세요.

이 문서에서는 Azure의 핵심 서버리스 제공인 Azure Logic Apps와 Azure Functions에 대해 간략하게 설명합니다. 두 서비스는 앞서 설명한 원칙을 따르며, 최소한의 코드로 강력한 클라우드 앱과 솔루션을 빌드하는 데 도움이 됩니다.

기본적인 내용은 [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)와 [Azure Functions](https://azure.microsoft.com/services/functions/)에 대한 Azure 페이지를 참조하세요. 자세한 내용은 [Azure Logic Apps란?](logic-apps-overview.md) 및 [Azure Functions란?](../azure-functions/functions-overview.md) 설명서 페이지를 참조하세요.

## <a name="azure-logic-apps"></a>Azure Logic Apps

이 서비스는 클라우드에서 실행되고 스케일링되는 자동화된 이벤트 기반 통합 워크플로를 설계, 개발, 오케스트레이션하는 간단한 방법을 제공합니다. Azure Logic Apps에서 비주얼 디자이너를 사용하여 비즈니스 프로세스를 워크플로로 신속하게 모델링할 수 있습니다. 워크플로는 항상 트리거를 첫 번째 단계로 사용하여 시작됩니다. 트리거 다음에 하나 이상의 동작이 워크플로의 후속 작업을 실행합니다. 작업에는 조건부 논리와 데이터 변환을 포함하여 동작의 다양한 조합이 포함될 수 있습니다.

코드를 작성하지 않고 워크플로를 다른 Azure 서비스, Microsoft 서비스, 클라우드 기반 환경, 온-프레미스 환경에 연결하려는 경우 Microsoft에서 모두 관리하는 [수백 개의 커넥터](/connectors/connector-reference/connector-reference-logicapps-connectors/) 중에서 선택하여 미리 빌드된 트리거와 동작을 워크플로에 추가할 수 있습니다. 각 커넥터는 실제로 API의 프록시나 래퍼로, 기본 서비스가 Azure Logic Apps와 통신할 수 있도록 합니다. 예를 들어 Office 365 Outlook 커넥터는 **새 전자 메일이 도착했을 때** 라는 트리거를 제공합니다. 서버리스 앱과 솔루션의 경우 Azure Logic Apps를 사용하여 Azure Functions에서 만든 여러 함수를 오케스트레이션할 수 있습니다. 이렇게 하면 특히 프로세스에서 외부 API 또는 시스템 작업이 필요할 때 다양한 함수를 단일 프로세스로 쉽게 호출할 수 있습니다.

요구 사항에 맞는 커넥터를 사용할 수 없는 경우 기본 제공 HTTP 작업이나 요청 트리거를 사용하여 서비스 엔드포인트와 통신할 수 있습니다. 또는 기존 API를 사용하여 사용자 고유의 커넥터를 만들 수 있습니다.

선택한 논리 앱 리소스 종류에 따라 다중 테넌트 Azure Logic Apps, 단일 테넌트 Azure Logic Apps 또는 전용 ISE(통합 서비스 환경)에서 연결된 워크플로가 실행됩니다. 각각 고유한 기능, 혜택, 청구 모델이 있습니다. Azure Portal은 논리 앱 워크플로 생성을 시작하는 가장 빠른 방법을 제공합니다. 그러나 Visual Studio Code, Visual Studio, Azure PowerShell 등의 다른 도구도 사용할 수 있습니다. 자세한 내용은 [Azure Logic Apps란?](logic-apps-overview.md)을 참조하세요.

Azure Logic Apps를 시작하려면 [빠른 시작: Azure Portal에서 첫 번째 논리 앱 워크플로 만들기](quickstart-create-first-logic-app-workflow.md)를 수행합니다. 또는 [Visual Studio에서 Azure Logic Apps와 Azure Functions를 사용하여 예제 서버리스 앱을 만드는 단계](create-serverless-apps-visual-studio.md)를 수행합니다.

자세한 내용은 다음 설명서를 참조하세요.

* [Azure Logic Apps란?](logic-apps-overview.md)
* [Azure Logic Apps의 커넥터 정보](../connectors/apis-list.md)
* [커넥터 - Azure Logic Apps, Microsoft Power Automate, Microsoft Power Apps](/connectors/connectors)
* [Azure Logic Apps의 단일 테넌트 및 다중 테넌트와 통합 서비스 환경](single-tenant-overview-compare.md)
* [Azure Logic Apps의 사용량 계량, 청구, 가격 책정 모델](logic-apps-pricing.md)

## <a name="azure-functions"></a>Azure 기능

이 서비스는 클라우드에서 코드 조각이나 ‘함수’를 작성하여 실행하는 간단한 방법을 제공합니다. 완전한 앱이나 필수 인프라를 설정하지 않고도 현재 문제에 필요한 코드만 작성할 수 있으므로 개발 속도가 향상되고 생산성이 증대됩니다. C#, Java, JavaScript, PowerShell, Python, TypeScript 등의 선택한 개발 언어를 사용합니다. 코드가 실행되는 기간에 대해서만 요금이 청구되고 필요에 따라 Azure가 스케일링됩니다.

Azure Functions를 시작하려면 [Azure Portal에서 첫 번째 Azure 함수 만들기](../azure-functions/functions-create-function-app-portal.md)를 수행합니다.

자세한 내용은 다음 설명서를 참조하세요.

* [Azure Functions란?](../azure-functions/functions-overview.md)
* [Azure Functions 시작](../azure-functions/functions-get-started.md)
* [Azure Functions에서 지원되는 언어](../azure-functions/supported-languages.md)
* [Azure Functions 호스팅 옵션](../azure-functions/functions-scale.md)
* [Azure Functions 가격](../azure-functions/pricing.md)

## <a name="get-started-with-serverless-apps-in-azure"></a>Azure에서 서버리스 앱 시작

Azure는 서버리스 앱을 개발, 배포, 관리하기 위한 다양한 도구를 제공합니다. Azure Portal, Visual Studio 또는 [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)를 사용하여 서버리스 앱을 만들 수 있습니다. 앱을 빌드한 후 [Azure Resource Manager 템플릿을 통해 해당 앱을 신속하게 배포](logic-apps-deploy-azure-resource-manager-templates.md)할 수 있습니다. 또한 Azure는 Azure Portal, API 또는 SDK를 통해 액세스하거나 Azure Monitor 로그와 Application Insights에 대한 통합 도구를 통해 액세스할 수 있는 모니터링을 제공합니다.

## <a name="next-steps"></a>다음 단계

* [Visual Studio에서 Azure Logic Apps와 Azure Functions를 사용하여 예제 서버리스 앱 만들기](create-serverless-apps-visual-studio.md)
* [서버리스 Customer Insights 대시보드 만들기](logic-apps-scenario-social-serverless.md)