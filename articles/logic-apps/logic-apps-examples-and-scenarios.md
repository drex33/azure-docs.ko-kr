---
title: 예제 및 일반적인 시나리오
description: Azure Logic Apps에 대한 예제, 일반적인 시나리오, 자습서, 연습을 찾습니다.
services: logic-apps
ms.suite: integration
ms.reviewer: azla
ms.topic: conceptual
ms.date: 07/20/2021
ms.openlocfilehash: 4d10cc4dab0b390ce5c22996a5f0a8bc5174440e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449248"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Azure Logic Apps의 일반적인 시나리오, 예제, 자습서 및 연습

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)는 SQL Server 또는 SAP에서 Azure Cognitive Services에 이르는 [수백 개의 바로 사용 가능한 커넥터](../connectors/apis-list.md)를 제공하기 때문에 다양한 서비스를 오케스트레이션하고 통합하는 데 도움이 됩니다. Logic Apps 서비스는 "서버가 없으므로" 규모 나 인스턴스를 걱정할 필요가 없습니다. 트리거가 있는 워크플로와 워크플로가 수행하는 작업을 정의하기만 하면 됩니다. 기본 플랫폼에서 규모, 가용성 및 성능을 처리합니다. Logic Apps는 여러 시스템 및 서비스에서 작업을 조정해야 하는 사용 사례와 시나리오에 특히 유용합니다.

Azure Logic Apps에서 지원하는 기능과 패턴에 대한 자세한 내용을 배울 수 있도록 이 문서에서는 일반적인 시작점, 예제, 시나리오를 설명합니다.

## <a name="common-starting-points-for-logic-app-workflows"></a>논리 앱 워크플로의 일반적인 시작점

모든 논리 앱은 하나의 [*트리거*](../logic-apps/logic-apps-overview.md#logic-app-concepts)로 시작하며 논리 앱 워크플로를 시작하고 트리거의 일부로 데이터를 전달합니다. 일부 커넥터는 다음과 같은 유형의 트리거를 제공합니다.

* *폴링 트리거*: 서비스 엔드포인트에서 정기적으로 새 데이터를 확인합니다. 새 데이터가 존재하면 트리거는 해당 데이터를 입력으로 사용하여 새 워크플로 인스턴스를 만들고 실행합니다.

* *푸시 트리거*: 서비스 엔드포인트에서 데이터를 수신 대기하고 특정 이벤트가 발생할 때까지 대기합니다. 이벤트가 발생하면 트리거가 즉시 실행되어 사용 가능한 데이터를 입력으로 사용하는 새 워크플로 인스턴스를 만들고 실행합니다.

일반적으로 사용되는 트리거를 설명하는 예제는 다음과 같습니다.

* *폴링* 트리거:

  * [**되풀이** 트리거](../connectors/connectors-native-recurrence.md)를 사용하면 논리 앱을 실행하는 시작 날짜와 시간 및 되풀이를 설정할 수 있습니다. 예를 들어 논리 앱을 트리거하는 요일과 시간을 선택할 수 있습니다. 자세한 내용은 다음 항목을 참조하세요.<p>

    * [Azure Logic Apps를 사용하여 반복적인 자동화된 작업, 프로세스 및 워크플로 예약 및 실행](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [자습서: Azure Logic Apps를 사용하여 자동화된 일정 기반 되풀이 워크플로 만들기](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * **메일이 수신될 때** 트리거를 사용하면 Logic Apps에 지원되는 모든 메일 공급자(예: [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](/connectors/gmail/), [Outlook.com](/connectors/outlook/) 등)의 새 메일을 논리 앱에서 확인할 수 있습니다.

    > [!IMPORTANT]
    > Gmail 커넥터를 사용하려는 경우 G Suite 비즈니스 계정만 논리 앱에서 제한 없이 이 커넥터를 사용할 수 있습니다. Gmail 소비자 계정이 있는 경우 특정 Google 승인 서비스에서만 이 커넥터를 사용하거나 [Gmail 커넥터 인증에 사용할 Google 클라이언트 앱을 만들](/connectors/gmail/#authentication-and-bring-your-own-application) 수 있습니다. 자세한 내용은 [Azure Logic Apps의 Google 커넥터에 대한 데이터 보안 및 개인정보처리방침](../connectors/connectors-google-data-security-privacy-policy.md)을 참조하세요.

    자세한 내용은 다음 항목을 참조하세요.<p>

    * [자습서: Azure Logic Apps를 사용하여 자동화된 승인 기반 워크플로 만들기](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [자습서: Azure Logic Apps, Azure Functions 및 Azure Storage를 사용하여 이메일을 처리하는 작업 자동화](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * [**HTTP** 트리거](../connectors/connectors-native-http.md)는 HTTP 또는 HTTPS를 통해 서비스 엔드포인트를 호출할 수 있습니다. 자세한 내용은 [HTTP 엔드포인트를 사용하여 워크플로 호출, 트리거 또는 중첩](../logic-apps/logic-apps-http-endpoint.md)을 참조하세요.

* *푸시* 트리거:

  * [**요청** 트리거](../connectors/connectors-native-reqres.md)는 들어오는 HTTPS 요청을 수신할 수 있습니다.

  * [**HTTP 웹후크** 트리거](../connectors/connectors-native-webhook.md)는 해당 서비스에 *콜백 URL* 을 등록하여 서비스 엔드포인트를 구독합니다. 서비스가 이런 방식으로 지정된 이벤트가 발생할 때 트리거에 알릴 수 있으므로 트리거가 서비스를 폴링할 필요가 없습니다.

지정한 이벤트가 발생하면 트리거가 실행되어 새 논리 앱 워크플로 인스턴스가 만들어지고 워크플로에서 작업이 실행됩니다. 워크플로 전체 트리거의 모든 데이터에 액세스할 수 있습니다. 예를 들어 Twitter **새 트윗에서** 트리거는 트윗 콘텐츠를 논리 앱 실행으로 전달합니다. Azure Logic Apps를 시작하려면 다음과 같은 빠른 시작 항목을 사용해 보세요.

* [빠른 시작: Azure Logic Apps를 사용하여 첫 번째 자동화된 워크플로 만들기 - Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [빠른 시작: Azure Logic Apps를 사용하여 자동화된 작업, 프로세스 및 워크플로 만들기 - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [빠른 시작: Visual Studio Code를 사용하여 자동화된 논리 앱 워크플로 만들기 및 관리](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="control-flow-and-error-handling-capabilities"></a>제어 흐름 및 오류 처리 기능

논리 앱은 조건, 스위치, 루프 및 범위와 같은 고급 제어 흐름에 대한 다양한 기능을 포함합니다. 복원력 있는 솔루션을 보장하기 위해 워크플로에서 오류 및 예외 처리를 구현할 수도 있습니다.

* [조건 문](../logic-apps/logic-apps-control-flow-conditional-statement.md)과 [Switch 문](../logic-apps/logic-apps-control-flow-switch-statement.md)을 기반으로 다양한 작업 수행
* [루프를 사용하여 배열 및 컬렉션의 항목을 처리하거나 단계를 반복](../logic-apps/logic-apps-control-flow-loops.md)
* [범위와 함께 작업 그룹화](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [워크플로에 오류 및 예외 처리 추가](../logic-apps/logic-apps-exception-handling.md)
* [사용 사례: 의료 회사에서 HL7 FHIR 워크플로에 대해 논리 앱 예외 처리를 사용하는 방법](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="create-custom-apis-and-connectors"></a>사용자 지정 API및 커넥터 만들기

게시된 커넥터를 포함하지 않는 시스템 및 서비스에 대해 논리 앱을 확장할 수도 있습니다.

* [Azure Logic Apps에서 호출할 사용자 지정 API 만들기](../logic-apps/logic-apps-create-api-app.md)
* [폴링 트리거 패턴을 사용하여 새 데이터 또는 이벤트가 있는지 정기적으로 확인](../logic-apps/logic-apps-create-api-app.md#polling-triggers)
* [웹후크 트리거 패턴을 사용하여 새 데이터 또는 이벤트 대기 및 수신 대기](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)
* [폴링 동작 패턴을 사용하여 장기 실행 작업 수행](../logic-apps/logic-apps-create-api-app.md#async-pattern)
* [웹후크 동작 패턴을 사용하여 장기 실행 작업 수행](../logic-apps/logic-apps-create-api-app.md#webhook-actions)
* [Azure Logic Apps의 사용자 지정 커넥터](../logic-apps/custom-connector-overview.md)

## <a name="build-business-to-business-b2b-solutions"></a>B2B(기업 간) 솔루션 빌드

엔터프라이즈 통합 솔루션과 조직 간의 원활한 통신을 위해 EIP(엔터프라이즈 통합 팩)를 Azure Logic Apps와 함께 사용하여 이와 같은 시나리오를 위한 자동화된 스케일링 가능 워크플로를 빌드할 수 있습니다. 조직에서 서로 다른 프로토콜 및 형식을 사용하더라도 전자 방식으로 메시지를 교환할 수 있습니다. EIP는 여러 형식을 조직의 시스템에서 처리할 수 있는 형식으로 변환하며, AS2, X12, EDIFACT, RosettaNet을 비롯한 업계 표준 프로토콜을 지원합니다. 이러한 솔루션을 빌드하려면 통합 계정을 만듭니다. 통합 계정은 정의하여 논리 앱 워크플로에서 사용할 수 있는 아티팩트에 안전하고, 스케일링 가능하며, 관리 가능한 컨테이너를 제공하는 별도의 Azure 리소스입니다. 예를 들어, 아티팩트에는 거래 업체, 규약, 맵, 스키마, 인증서, 배치 구성 등이 있습니다.

* [개요: Azure Logic Apps 및 엔터프라이즈 통합 팩이 포함된 B2B 엔터프라이즈 통합 솔루션](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Azure Logic Apps에서 B2B 엔터프라이즈 통합에 대한 통합 계정 만들기 및 관리](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

## <a name="access-azure-virtual-network-resources"></a>Azure 가상 네트워크 리소스에 액세스

논리 앱과 통합 계정이 Azure 가상 네트워크의 VM(가상 머신)과 기타 시스템 또는 서비스와 같이 보호된 리소스에 액세스해야 하는 경우가 있습니다. 이러한 액세스를 설정하기 위해 논리 앱을 빌드하고 실행할 수 있는 ISE(통합 서비스 환경)를 만들 수 있습니다. ISE는 전용 리소스(예: 스토리지)를 사용하고 퍼블릭 “전역” 다중 테넌트 Logic Apps 서비스와 별도로 실행되는 Logic Apps 서비스의 격리된 프라이빗 인스턴스입니다. 또한 격리된 프라이빗 인스턴스와 전역 퍼블릭 인스턴스를 분리하면 다른 Azure 테넌트가 앱의 성능에 미치는 영향을 줄일 수 있습니다. 이를 “방해” 효과라고도 합니다.

* [개요: Azure Logic Apps에서 Azure 가상 네트워크 리소스에 액세스](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Azure Logic Apps에서 Azure 가상 네트워크에 연결](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="deploy-manage-and-monitor-logic-apps"></a>논리 앱 배포, 관리와 모니터링

Visual Studio, Azure DevOps 또는 기타 소스 제어 및 자동화된 빌드 도구를 사용하여 논리 앱을 완전하게 개발 및 배포할 수 있습니다. 리소스 템플릿에서 워크플로 및 종속 연결에 대한 배포를 지원하기 위해 논리 앱은 Azure 리소스 배포 템플릿을 사용합니다. Visual Studio 도구는 이러한 템플릿을 자동으로 생성하므로 버전 관리를 위해 소스 제어에 체크인할 수 있습니다. 워크플로 실행 상태에 대한 알림 및 진단 로그를 위해 Azure Logic Apps에서는 모니터링 및 경고도 제공합니다.

### <a name="deploy"></a>배포:

* [빠른 시작: Azure Logic Apps를 사용하여 자동화된 작업, 프로세스 및 워크플로 만들기 - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [개요: 논리 앱 배포 자동화](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Azure Logic Apps에 대한 배포를 자동화하는 Azure Resource Manager 템플릿 만들기](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Azure Logic Apps에 대한 Azure Resource Manager 템플릿 배포](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
* [샘플: Azure Logic Apps에 대한 API Management 작업 설정](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/api-management-actions)
* [샘플: Azure Logic Apps를 사용하여 Azure Pipelines 오케스트레이션](https://github.com/Azure-Samples/azure-logic-apps-pipeline-orchestration)
* [샘플: Azure Logic Apps에서 Azure Storage 계정에 연결 및 Azure DevOps에서 Azure Pipelines를 사용하여 배포](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/storage-account-connections)
* [샘플: Azure Logic Apps에서 Azure Service Bus 큐에 연결 및 Azure DevOps에서 Azure Pipelines를 사용하여 배포](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/service-bus-connections)
* [샘플: Azure Logic Apps에 대한 Azure Functions 작업 설정 및 Azure DevOps에서 Azure Pipelines를 사용하여 배포](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/function-app-actions)
* [샘플: Azure Logic Apps에서 통합 계정에 연결 및 Azure DevOps에서 Azure Pipelines를 사용하여 배포](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/integration-account-connections)

### <a name="manage"></a>관리

* [Visual Studio를 사용하여 논리 앱 관리](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [B2B 엔터프라이즈 통합을 위한 통합 계정 만들기 및 관리](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)
* [Azure Logic Apps에서 ISE(통합 서비스 환경) 관리](../logic-apps/ise-manage-integration-service-environment.md)

### <a name="monitor"></a>모니터

* [실행 상태 모니터링, 트리거 기록 검토 및 Azure Logic Apps에 대한 경고 설정](../logic-apps/monitor-logic-apps.md)
* [Azure Monitor 로그를 설정하고 Azure Logic Apps에 대한 진단 데이터 수집](../logic-apps/monitor-logic-apps-log-analytics.md)
* [Azure Logic Apps에서 B2B 메시지에 대한 Azure Monitor 로그 설정 및 진단 데이터 수집](../logic-apps/monitor-b2b-messages-log-analytics.md)
* [Azure Logic Apps에 대한 Azure Monitor 로그에서 모니터링 및 추적을 위한 쿼리 보기 및 만들기](../logic-apps/create-monitoring-tracking-queries.md)

## <a name="handle-content-types-conversions-and-transformations"></a>콘텐츠 형식, 변환 및 변형 처리

Azure Logic Apps [워크플로 정의 언어](./logic-apps-workflow-definition-language.md)의 다양한 함수를 사용하여 여러 콘텐츠 유형을 액세스, 변환 및 변형시킬 수 있습니다. 예를 들어, `@json()` 및 `@xml()` 워크플로 식을 사용하여 문자열, JSON 및 XML 간에 변환할 수 있습니다. Logic Apps 엔진은 서비스 간에 무손실 방식으로 콘텐츠 전송을 지원하는 콘텐츠 형식을 유지합니다.

* [Azure Logic Apps에서 콘텐츠 형식 처리](../logic-apps/logic-apps-content-type.md)(예: `application/`, `application/octet-stream`, `multipart/formdata`)
* [Azure Logic Apps 및 Power Automate용 식의 함수 사용에 대한 참조 가이드](../logic-apps/workflow-definition-language-functions-reference.md)
* [Azure Logic Apps에 대한 워크플로 정의 언어 스키마](../logic-apps/logic-apps-workflow-definition-language.md)

## <a name="other-integrations-and-capabilities"></a>기타 통합 및 기능

Azure Logic Apps는 다양한 서비스(예: Azure Functions, Azure API Management, Azure App Service), 사용자 지정 HTTP 엔드포인트(예: REST, SOAP)와 통합합니다.

* [Azure Logic Apps에서 Azure Functions 호출](../logic-apps/logic-apps-azure-functions.md)
* [자습서: Azure Functions와 Azure Service Bus를 사용하여 논리 앱 호출 및 트리거](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [자습서: Azure Logic Apps 및 Azure Functions를 사용하여 스트리밍 Customer Insights 대시보드 만들기](../logic-apps/logic-apps-scenario-social-serverless.md)
* [자습서: Azure Logic Apps 및 Azure Cognitive Services와 통합하여 Twitter 게시물 감정을 분석하는 함수 만들기](../azure-functions/functions-twitter-email.md)
* [자습서: Power BI 및 Azure Logic Apps를 사용하여 AI 기반 소셜 대시보드 빌드](https://aka.ms/logicappsdemo)
* [자습서: Azure Event Grid 및 Logic Apps를 사용하여 가상 머신 변경 모니터링](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [자습서: Azure Logic Apps로 IoT Hub와 사서함을 연결하여 IoT 원격 모니터링 및 알림](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [블로그: Azure Logic Apps을 사용하여 SOAP 서비스 호출](/archive/blogs/logicapps/using-soap-services-with-logic-apps)

## <a name="end-to-end-scenarios"></a>엔드투엔드 시나리오

* [백서: Azure 서비스(예: Logic Apps)와 엔드투엔드 사례 관리 통합](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>고객 사례

Azure Logic Apps가 다른 Azure 서비스 및 Microsoft 제품과 함께 복잡한 프로세스를 간소화하고 구성하고 자동화하고 오케스트레이션하여 [기업](https://aka.ms/logic-apps-customer-stories)의 민첩성을 향상시키고 핵심 비즈니스에 집중하도록 도울 수 있는 방법을 알아봅니다.

## <a name="next-steps"></a>다음 단계

* [Logic Apps용 커넥터](../connectors/apis-list.md)에 대해 알아보기
* [Azure Logic Apps를 사용하는 B2B 엔터프라이즈 통합 시나리오](../logic-apps/logic-apps-enterprise-integration-overview.md)에 대해 알아보기