---
title: Azure Logic Apps에 대한 커넥터 개요
description: 커넥터에 대해 알아보고 커넥터를 통해 Azure Logic Apps를 사용하여 자동화된 통합 워크플로를 빠르고 쉽게 구축하는 방법을 알아봅니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 09/13/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: cccd744e8c123cd9441ff9aca47d2341ea9d80fb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128679866"
---
# <a name="about-connectors-in-azure-logic-apps"></a>Azure Logic Apps의 커넥터 정보

Azure Logic Apps를 사용하여 워크플로를 빌드할 때 *커넥터* 를 사용하면 종종 코드를 작성하지 않고도 다른 앱, 서비스, 시스템, 프로토콜 및 플랫폼의 데이터, 이벤트 및 리소스에 빠르고 쉽게 액세스할 수 있습니다. 커넥터는 워크플로의 단계로 사용할 수 있는 미리 빌드된 작업을 제공합니다. Azure Logic Apps는 사용할 수 있는 수백 개의 커넥터를 제공합니다. 액세스하려는 리소스에 사용할 수 있는 커넥터가 없는 경우 일반 HTTP 작업을 사용하여 서비스와 통신하거나 [사용자 지정 커넥터를 만들 수 있습니다](#custom-apis-and-connectors).

이 개요에서는 커넥터, 일반 작동 방식 및 Azure Logic Apps에서 더 많이 사용되는 커넥터를 소개합니다. 자세한 내용은 다음 설명서를 검토하세요.

* [Azure Logic Apps, Microsoft Power Automate 및 Microsoft Power Apps에 대한 커넥터 개요](/connectors)
* [Azure Logic Apps에 대한 커넥터 참조](/connectors/connector-reference/connector-reference-logicapps-connectors)
* [Azure Logic Apps의 가격 책정 및 청구 모델](../logic-apps/logic-apps-pricing.md)
* [Azure Logic Apps 가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/logic-apps/)

## <a name="what-are-connectors"></a>커넥터란 무엇인가요?

기술적으로 커넥터는 기본 서비스가 Azure Logic Apps와 통신하는 데 사용하는 API 주변의 프록시 또는 래퍼입니다. 이 커넥터는 작업을 수행하기 위해 워크플로에서 사용하는 작업을 제공합니다. 작업은 구성할 수 있는 속성이 있는 *트리거* 또는 *작업* 으로 사용할 수 있습니다. 또한 일부 트리거 및 작업에서는 사용자 계정에 대한 액세스를 인증할 수 있도록 먼저 기본 서비스 또는 시스템에 대한 [연결을 만들고 구성](#connection-configuration)해야 합니다.

### <a name="triggers"></a>트리거

*트리거* 는 워크플로를 시작하는 이벤트를 지정하며 항상 워크플로의 첫 번째 단계입니다. 또한 각 트리거는 해당 트리거가 이벤트를 모니터링하고 응답하는 방법을 제어하는 특정 발생 패턴을 따릅니다. 일반적으로 트리거는 *폴링* 패턴 또는 *푸시* 패턴을 따르지만 때로는 두 버전 모두에서 트리거를 사용할 수 있습니다.

- *폴링 트리거* 는 지정된 일정에 따라 특정 서비스 또는 시스템을 정기적으로 확인하여 새 데이터나 특정 이벤트를 확인합니다. 새 데이터를 사용할 수 있거나 특정 이벤트가 발생하는 경우 이러한 트리거는 워크플로의 새 인스턴스를 만들고 실행합니다. 그러면 이 새 인스턴스가 입력으로 전달된 데이터를 사용할 수 있습니다.
- *푸시 트리거* 는 폴링 없이 새 데이터 또는 이벤트가 발생하는지 수신 대기합니다. 새 데이터를 사용할 수 있거나 이벤트가 발생하는 경우 푸시 트리거는 워크플로의 새 인스턴스를 만들고 실행합니다. 그러면 이 새 인스턴스가 입력으로 전달된 데이터를 사용할 수 있습니다.

예를 들어 파일이 FTP 서버에 업로드될 때 작업을 수행하는 워크플로를 빌드할 수 있습니다. 워크플로의 첫 번째 단계로 **파일을 추가하거나 수정할 때** 라는 FTP 트리거를 사용할 수 있으며, 이는 폴링 패턴을 따릅니다. 그런 다음 정기적으로 업로드 이벤트를 확인하는 일정을 지정할 수 있습니다.

트리거는 모든 입력 및 기타 필수 데이터를 워크플로에 전달하여 이후 작업에서 워크플로 전체에서 해당 데이터를 참조하고 사용할 수도 있습니다. 예를 들어 **새 이메일이 도착했을 때** 라는 Office 365 Outlook 트리거를 사용하여 새 이메일을 받을 때 워크플로를 시작한다고 가정해 보겠습니다. 보낸 사람, 제목 줄, 본문, 첨부 파일 등의 새 이메일 내용을 함께 전달하도록 이 트리거를 구성할 수 있습니다. 그런 다음 워크플로는 다른 작업을 사용하여 해당 정보를 처리할 수 있습니다.

### <a name="actions"></a>동작

*작업*(action)은 트리거를 따르고 워크플로에서 일종의 작업(task)을 수행하는 작업(operation)입니다. 워크플로에서 여러 작업을 사용할 수 있습니다. 예를 들어 SQL 데이터베이스에서 새 고객 데이터를 검색하는 SQL 트리거를 사용하여 워크플로를 시작할 수 있습니다. 트리거에 따라 워크플로에 고객 데이터를 가져오는 SQL 작업이 있을 수 있습니다. SQL 작업 후에는 워크플로에서 데이터를 처리하는 다른 작업(반드시 SQL이 아님)을 사용할 수 있습니다.

## <a name="connector-categories"></a>커넥터 범주

Azure Logic Apps 대부분의 트리거 및 작업은 기본 제공 버전 또는 *관리되는 커넥터* *버전에서* 사용할 수 있습니다. 두 버전 모두에서 몇 가지 트리거와 작업을 사용할 수 있습니다. 사용 가능한 버전은 현재 [단일 테넌트 Azure Logic Apps](../logic-apps/single-tenant-overview-compare.md)에서만 사용할 수 있는 다중 테넌트 논리 앱을 만드는지 아니면 단일 테넌트 논리 앱을 만드는지에 따라 다릅니다.

[내장된 트리거 및 작업](built-in.md)은 기본적으로 Logic Apps 런타임에서 실행되며 연결을 만들 필요가 없으며 다음과 같은 유형의 작업을 수행합니다.

- [워크플로에서 코드 실행](built-in.md#run-code-from-workflows).
- [데이터 구성 및 제어](built-in.md#control-workflow).
- [데이터를 관리하거나 조작합니다](built-in.md#manage-or-manipulate-data).

[관리 커넥터](managed.md)는 Microsoft에서 배포, 호스팅 및 관리합니다. 이러한 커넥터는 클라우드 서비스, 온-프레미스 시스템 또는 둘 다에 대한 트리거 및 작업을 제공합니다. 관리 커넥터는 다음 범주에서 사용할 수 있습니다.

- 온-프레미스 시스템의 데이터 및 리소스에 액세스하는 데 도움이 되는 [온-프레미스 커넥터](managed.md#on-premises-connectors)입니다.
- 엔터프라이즈 시스템에 대한 액세스를 제공하는 [엔터프라이즈 커넥터](managed.md#enterprise-connectors)입니다.
- B2B(Business-to-Business) 통신 시나리오를 지원하는 [통합 계정 커넥터](managed.md#integration-account-connectors)입니다.
- [ISE(통합 서비스 환경) 커넥터](managed.md#ise-connectors)는 [ISE에만 사용할 수 있는 관리 커넥터](#ise-and-connectors)의 소규모 그룹입니다.

<a name="connection-configuration"></a>

## <a name="connection-configuration"></a>연결 구성

논리 앱 리소스 및 연결을 만들거나 관리하려면 [Azure RBAC(Azure 역할 기반 액세스 제어)를](../role-based-access-control/role-assignments-portal.md)사용하여 역할을 통해 제공되는 특정 권한이 필요합니다. Azure 구독에 액세스할 수 있는 멤버에게 기본 제공 또는 사용자 지정된 역할을 할당할 수 있습니다. Azure Logic Apps 다음과 같은 특정 역할이 있습니다.

* [논리 앱 참가자](../role-based-access-control/built-in-roles.md#logic-app-contributor): 논리 앱을 관리할 수 있지만 앱에 대한 액세스는 변경할 수 없습니다.

* [논리 앱 운영자](../role-based-access-control/built-in-roles.md#logic-app-operator): 논리 앱을 읽고 사용하거나 사용하지 않도록 설정할 수는 있지만 편집하거나 업데이트할 수 없습니다.

* [기여자:](../role-based-access-control/built-in-roles.md#contributor)모든 리소스를 관리할 수 있는 모든 권한을 부여하지만 Azure RBAC에서 역할을 할당하거나, Azure Blueprints 할당을 관리하거나, 이미지 갤러리를 공유할 수는 없습니다.

  예를 들어 해당 논리 앱의 워크플로에서 사용하는 연결을 만들고 인증하지 않은 논리 앱으로 작업해야 하는 경우를 가정해 보겠습니다. Azure 구독에는 해당 논리 앱 리소스가 포함된 리소스 그룹에 대한 기여자 권한이 필요합니다. 논리 앱 리소스를 만드는 경우 자동으로 기여자 액세스 권한이 있습니다.

워크플로에서 커넥터의 트리거 또는 작업을 사용하려면 대부분의 커넥터에서 먼저 대상 서비스 또는 시스템에 *대한 연결을* 만들어야 합니다. 논리 앱 워크플로 내에서 연결을 만들려면 계정 자격 증명 및 경우에 따라 다른 연결 정보를 사용하여 ID를 인증해야 합니다. 예를 들어 워크플로가 Office 365 Outlook 이메일 계정에 액세스하고 사용할 수 있도록 하려면 먼저 해당 계정에 대한 연결에 권한을 부여해야 합니다. 적은 수의 기본 제공 작업 및 관리 커넥터의 경우 자격 증명을 제공하는 대신 [인증에 관리 ID를 설정하고 사용할](../logic-apps/create-managed-service-identity.md#triggers-actions-managed-identity)수 있습니다.

<a name="connection-security-encyrption"></a>

### <a name="connection-security-and-encryption"></a>연결 보안 및 암호화

서버 주소, 사용자 이름, 암호, 자격 증명 및 비밀과 같은 연결 구성 세부 정보는 [암호화되어 보안 Azure 환경에 저장됩니다.](../security/fundamentals/encryption-overview.md) 이 정보는 논리 앱 리소스 및 연결된 액세스 검사를 사용하여 적용되는 연결 리소스에 대한 권한이 있는 클라이언트에서만 사용할 수 있습니다. Office 365, Salesforce 및 GitHub 같은 Azure Active Directory Open Authentication(Azure AD OAuth)을 사용하는 연결은 로그인해야 하지만 Azure Logic Apps 액세스 및 새로 고침 토큰만 로그인 자격 증명이 아닌 비밀로 저장해야 합니다.

설정된 연결은 해당 서비스 또는 시스템이 허용하는 한 대상 서비스 또는 시스템에 액세스할 수 있습니다. Office 365 및 Dynamics와 같은 Azure AD OAuth 연결을 사용하는 서비스의 경우 Logic Apps 서비스는 액세스 토큰을 무기한으로 새로 고칩니다. 다른 서비스의 경우 Logic Apps를 새로 고치지 않고 토큰을 사용할 수 있는 기간에 제한이 있을 수 있습니다. 일부 작업(예: 암호 변경)은 모든 액세스 토큰을 무효화합니다.

워크플로 내에서 연결을 만드는 경우에도 연결은 자체 리소스 정의가 포함된 별도의 Azure 리소스입니다. 이러한 연결 리소스 정의를 검토하려면 [Azure에서 Visual Studio로 논리 앱을 다운로드하세요](../logic-apps/manage-logic-apps-with-visual-studio.md). 이 방법은 대부분 배포 준비가 되어 유효하고 매개 변수화된 논리 앱 템플릿을 만드는 가장 쉬운 방법입니다.

> [!TIP]
> 조직에서 Logic Apps 커넥터를 통해 특정 리소스에 액세스하는 것을 허용하지 않는 경우 [Azure Policy](../governance/policy/overview.md)를 사용하여 [이러한 연결을 만드는 기능을 차단](../logic-apps/block-connections-connectors.md)할 수 있습니다.

논리 앱 및 연결 보안에 대한 자세한 내용은 [Azure Logic Apps 액세스 및 데이터 보안을](../logic-apps/logic-apps-securing-a-logic-app.md)검토하세요.

<a name="firewall-access"></a>

### <a name="firewall-access-for-connections"></a>연결을 위한 방화벽 액세스

트래픽을 제한하는 방화벽을 사용하고 논리 앱 워크플로가 해당 방화벽을 통해 통신해야 하는 경우 논리 앱 워크플로가 있는 Azure 지역의 Logic Apps 서비스 또는 런타임에서 사용하는 [인바운드](../logic-apps/logic-apps-limits-and-config.md#inbound) 및 [아웃바운드](../logic-apps/logic-apps-limits-and-config.md#outbound) IP 주소 모두에 대한 액세스를 허용하도록 방화벽을 설정해야 합니다. 워크플로에서 Office 365 Outlook 커넥터 또는 SQL 커넥터와 같은 관리형 커넥터를 사용하거나 사용자 지정 커넥터를 사용하는 경우 방화벽은 논리 앱의 Azure 지역에 있는 [관리형 커넥터 아웃바운드 IP 주소](/connectors/common/outbound-ip-addresses#azure-logic-apps) *모두* 에 대한 액세스도 허용해야 합니다. 자세한 내용은 [방화벽 구성](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses-and-service-tags)을 검토합니다.

## <a name="recurrence-behavior"></a>되풀이 동작

[되풀이 트리거](connectors-native-recurrence.md)와 같은 기본 제공 트리거를 되풀이하면 Logic Apps 런타임에서 기본적으로 실행되며, 이는 먼저 연결을 만들어야 하는 Office 365 Outlook 커넥터 트리거와 같은 연결 기반 트리거를 되풀이하는 것과 다릅니다.

두 종류의 트리거에서 모두 되풀이에서 특정 시작 날짜 및 시간을 지정하지 않는 경우 트리거의 되풀이 설정에도 불구하고 논리 앱을 저장하거나 배포할 때 첫 번째 되풀이가 즉시 실행됩니다. 이 동작을 방지하려면 첫 번째 되풀이를 실행할 시작 날짜와 시간을 제공합니다.

### <a name="recurrence-for-built-in-triggers"></a>기본 제공 트리거의 되풀이

되풀이되는 기본 제공 트리거는 지정된 표준 시간대를 포함하여 사용자가 설정한 일정을 따릅니다. 그러나 되풀이에 향후 실행할 되풀이의 특정 시간과 같은 다른 고급 예약 옵션을 지정하지 않는 경우 이러한 되풀이는 마지막 트리거 실행을 기반으로 실행됩니다. 결과적으로 해당 되풀이에 대한 시작 시간은 스토리지 호출 중의 대기 시간과 같은 요소로 인해 드리프트될 수 있습니다.

자세한 내용은 다음 설명서를 검토하세요.

* [Azure Logic Apps를 사용하여 반복적인 자동화된 작업, 프로세스 및 워크플로 예약 및 실행](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
* [되풀이 트리거를 사용하여 되풀이 작업 및 워크플로 만들기, 예약, 실행](connectors-native-recurrence.md)
* [되풀이 문제 해결](#recurrence-issues)

### <a name="recurrence-for-connection-based-triggers"></a>연결 기반 트리거의 되풀이

Office 365 Outlook과 같은 되풀이 연결 기반 트리거에서 일정으로만 실행을 제어할 수 있는 것은 아닙니다. 표준 시간대는 초기 시작 시간을 결정합니다. 후속 실행은 되풀이 일정, 마지막 트리거 실행 및 런타임 시 예기치 않은 동작을 발생시킬 수 있는 기타 요인에 따라 달라지며, 예를 들면 다음과 같습니다.

* 트리거가 더 많은 데이터가 있는 서버에 액세스하는지 여부에 따라 트리거가 즉시 페치를 시도합니다.
* 트리거가 발생하는 오류 또는 다시 시도.
* 스토리지 호출 동안의 대기 시간.
* DST(일광 절약 시간)가 시작 및 종료될 때 지정된 일정을 유지하지 않습니다.
* 다음 런타임이 발생할 때 영향을 줄 수 있는 다른 요인.

자세한 내용은 다음 설명서를 검토하세요.

* [Azure Logic Apps를 사용하여 반복적인 자동화된 작업, 프로세스 및 워크플로 예약 및 실행](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
* [되풀이 문제 해결](#recurrence-issues)

<a name="recurrence-issues"></a>

### <a name="troubleshooting-recurrence-issues"></a>되풀이 문제 해결

워크플로가 지정된 시작 시간에 실행되고 되풀이를 누락하지 않도록 하려면(특히 실행 주기가 며칠 이상일 때) 다음 해결 방법을 시도해보세요.

* DST가 적용되면 워크플로가 예상 시간에 계속 실행되도록 되풀이를 수동으로 조정합니다. 그렇지 않으면 DST가 시작될 때 시작 시간이 1시간 앞으로 이동하고, DST가 종료되면 1시간 뒤로 이동합니다. 자세한 내용과 예는 [일광 절약 시간 및 표준 시간에 대한 반복](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time)을 검토하세요.

* **되풀이** 트리거를 사용하는 경우 시간대, 시작 날짜 및 시작 시간을 지정합니다. 또한 **시간 선택** 및 **분 선택** 속성에서 이후 되풀이를 실행할 특정 시간을 구성합니다. 여기서는 **일** 및 **주** 빈도만 가능합니다. 그러나 시간이 이동하면 일부 기간에서 문제가 발생할 수 있습니다.

* 되풀이 누락을 방지하려면 **되풀이** 트리거 대신 [**슬라이딩 윈도우** 트리거](connectors-native-sliding-window.md)를 사용하는 것이 좋습니다.

## <a name="custom-apis-and-connectors"></a>사용자 지정 API 및 커넥터

사용자 지정 코드를 실행하거나 커넥터로 사용할 수 없는 API를 호출하려면 [사용자 지정 API Apps를 만들어](../logic-apps/logic-apps-create-api-app.md) Logic Apps 플랫폼을 확장할 수 있습니다. 또한 모든 REST 또는 SOAP 기반 API에 대한 [사용자 지정 커넥터를 만들면](../logic-apps/custom-connector-overview.md) Azure 구독의 모든 논리 앱에서 해당 API를 사용할 수 있습니다. Azure에서 모든 사용자가 사용할 수 있도록 사용자 지정 API 앱 또는 커넥터를 공개하려면 [Microsoft 인증을 위해 커넥터를 제출](/connectors/custom-connectors/submit-certification)할 수 있습니다.

## <a name="ise-and-connectors"></a>ISE 및 커넥터

Azure 가상 네트워크의 리소스에 직접 액세스해야 하는 워크플로의 경우 전용 리소스에서 워크플로를 빌드, 배포 및 실행할 수 있는 전용 [ISE(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)를 만들 수 있습니다. ISE 만들기에 대한 자세한 내용은 [Azure Logic Apps에서 Azure 가상 네트워크에 연결](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)을 참조하세요.

ISE 내부에서 만든 사용자 지정 커넥터는 온-프레미스 데이터 게이트웨이에서 작동하지 않습니다. 하지만 이러한 커넥터는 ISE를 호스트하는 Azure 가상 네트워크에 연결된 온-프레미스 데이터 원본에 직접 액세스할 수 있습니다. 따라서 ISE의 논리 앱은 이러한 리소스와 통신할 때 데이터 게이트웨이가 필요하지 않을 수 있습니다. 온-프레미스 데이터 게이트웨이를 필요로 하는 ISE 외부에서 만든 사용자 지정 커넥터가 있는 경우 ISE의 논리 앱이 이러한 커넥터를 사용할 수 있습니다.

Logic Apps 디자이너에서 ISE의 논리 앱에 사용할 기본 제공 트리거 및 작업 또는 관리형 커넥터를 찾으면 **CORE** 레이블이 기본 제공 트리거 및 작업에 표시되며 **ISE** 레이블은 ISE와 함께 작동하도록 설계된 관리 커넥터에 표시됩니다.

:::row:::
    :::column:::
        ![CORE 커넥터 예시](./media/apis-list/example-core-connector.png)
        \
        \
        **CORE**
        \
        \
        이 레이블이 포함된 기본 제공 트리거는 논리 앱과 동일한 ISE에서 실행됩니다.
    :::column-end:::
    :::column:::
        ![ISE 커넥터 예시](./media/apis-list/example-ise-connector.png)
        \
        \
        **ISE**
        \
        \
        이 레이블이 포함된 관리되는 커넥터는 논리 앱과 동일한 ISE에서 실행됩니다. 
        \
        \
        Azure 가상 네트워크에 연결된 온-프레미스 시스템이 있는 경우 ISE를 사용하면 [온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-connection.md)를 사용하지 않고도 워크플로가 해당 시스템에 직접 액세스할 수 있습니다. 대신 사용 가능한 경우 해당 시스템의 **ISE** 커넥터를 사용하거나, HTTP 작업을 사용하거나, [사용자 지정 커넥터](#custom-apis-and-connectors)를 사용할 수 있습니다.
        \
        \
        **ISE** 커넥터가 없는 온-프레미스 시스템의 경우 온-프레미스 데이터 게이트웨이를 사용합니다. 사용 가능한 ISE 커넥터를 찾으려면 [ISE 커넥터](#ise-and-connectors)를 검토하세요.
    :::column-end:::
    :::column:::
        ![비 ISE 커넥터 예](./media/apis-list/example-multi-tenant-connector.png)
        \
        \
        레이블 없음     \
        \
        계속 사용할 수 있고 레이블이 없는 다른 모든 커넥터는 글로벌 다중 테넌트 Logic Apps 서비스에서 실행됩니다.
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="known-issues"></a>알려진 문제

다음 표에는 Logic Apps 커넥터에 대한 알려진 문제가 나와 있습니다.

| 오류 메시지| Description | 해결 방법 |
|--------------|-------------|------------|
| `Error: BadGateway. Client request id: '{GUID}'` | 이 오류는 하나 이상의 연결이 SFTP AD SQL과 같은 Azure Active Directory(Azure AD) OAuth 인증을 지원하지 않아 해당 연결을 중단하는 논리 앱의 태그를 업데이트하면서 발생합니다. | 이 동작을 방지하려면 해당 태그의 업데이트를 방지합니다. |
||||

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Logic Apps에서 호출할 수 있는 사용자 지정 API 만들기](../logic-apps/logic-apps-create-api-app.md)
