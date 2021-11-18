---
title: Microsoft Sentinel 데이터 커넥터 | Microsoft Docs
description: Microsoft 365 Defender(이전의 Microsoft Threat Protection), Microsoft 365 및 Office 365, Azure AD, ATP 및 Cloud Apps용 Defender와 같은 데이터 원본을 Microsoft Sentinel에 연결하는 방법을 알아봅니다.
author: yelevin
ms.topic: conceptual
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 71462508b7b1b4414fce77691a65d3ca1b8bba43
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132757982"
---
# <a name="microsoft-sentinel-data-connectors"></a>Microsoft Sentinel 데이터 커넥터

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Microsoft Sentinel을 작업 영역에 온보딩한 후 데이터 원본을 연결하여 Microsoft Sentinel에 데이터 수집을 시작합니다. Microsoft Sentinel에는 Microsoft 제품에 대한 많은 커넥터가 함께 제공되며, 이 커넥터는 무료로 제공되며 실시간 통합을 제공합니다. 예를 들어 서비스 대 서비스 커넥터에는 Microsoft 365 Defender 커넥터 및 Microsoft 365 원본(예: Office 365, azure AD(Azure Active Directory), Microsoft Defender for Identity 및 Cloud Apps용 Microsoft Defender가 포함됩니다.

또한 타사 제품의 광범위한 보안 에코시스템에 대한 첫 번째 커넥터를 사용하도록 설정할 수도 있습니다. 예를 들어 [Syslog,](#syslog) [CEF(Common Event Format)](#common-event-format-cef)또는 [REST API를](#rest-api-integration) 사용하여 데이터 원본을 Microsoft Sentinel에 연결할 수 있습니다.

Microsoft Sentinel 탐색 메뉴에서 액세스할 수 있는 **데이터 커넥터** 페이지에는 Microsoft Sentinel에서 제공하는 커넥터의 전체 목록과 작업 영역의 상태가 표시됩니다. 연결하려는 커넥터를 선택하고 **커넥터 페이지 열기** 를 선택합니다.

![데이터 커넥터 갤러리](./media/collect-data/collect-data-page.png)

이 문서에서는 지원되는 데이터 연결 방법을 설명합니다. 자세한 내용은 [Microsoft Sentinel 데이터 커넥터 참조](data-connectors-reference.md) 및 Microsoft [Sentinel 솔루션 카탈로그 를 참조하세요.](sentinel-solutions-catalog.md)

<a name="agent-options"></a>
<a name="data-connection-methods"></a>
<a name="map-data-types-with-microsoft-sentinel-connection-options"></a>

## <a name="enable-a-data-connector"></a>데이터 커넥터를 사용하도록 설정

Microsoft Sentinel 탐색 메뉴에서 액세스할 수 있는 **데이터 커넥터** 페이지에는 Microsoft Sentinel에서 제공하는 커넥터의 전체 목록과 해당 상태가 표시됩니다. 연결하려는 커넥터를 선택하고 **커넥터 페이지 열기** 를 선택합니다.

![데이터 커넥터 갤러리](./media/collect-data/collect-data-page.png)

모든 필수 구성을 충족해야 하며, Microsoft Sentinel에 데이터를 수집하기 위한 전체 지침이 커넥터 페이지에 표시됩니다. 데이터 도착이 시작되는 데 다소 시간이 걸릴 수 있습니다. 연결되면 **데이터 수신됨** 그래프의 데이터 요약 및 데이터 형식의 연결 상태가 표시됩니다.

![데이터 커넥터 구성](./media/collect-data/opened-connector-page.png)

다음 **단계** 탭에는 위협을 감지하고 조사하는 데 도움이 되는 샘플 쿼리, 시각화 통합 문서 및 분석 규칙 템플릿과 같은 Microsoft Sentinel이 특정 데이터 형식에 대해 제공하는 추가 콘텐츠가 표시됩니다.

![커넥터에 대한 다음 단계](./media/collect-data/data-insights.png)

자세한 내용은 [데이터 커넥터 참조](data-connectors-reference.md)에서 데이터 커넥터의 관련 섹션을 참조하세요.

## <a name="rest-api-integration"></a>REST API 통합

많은 보안 기술은 로그 파일을 검색하기 위한 API 집합을 제공하며, 일부 데이터 원본은 이러한 API를 사용하여 Microsoft Sentinel에 연결할 수 있습니다.

API를 사용하는 데이터 커넥터는 다음 섹션에 설명된 대로 공급자 쪽에서 통합하거나 Azure Functions를 사용하여 통합합니다.

이 커넥터에 관한 전체 목록과 자세한 내용은 [데이터 커넥터 참조](data-connectors-reference.md)를 참조하세요.

### <a name="rest-api-integration-on-the-provider-side"></a>공급자 쪽에서 REST API 통합

공급자가 빌드한 API 통합은 공급자 데이터 원본과 연결하고 [Azure Monitor 데이터 수집기 API를](../azure-monitor/logs/data-collector-api.md)사용하여 Microsoft Sentinel 사용자 지정 로그 테이블에 데이터를 푸시합니다.

자세한 내용은 공급자 설명서를 참조하고 [Microsoft Sentinel의 REST-API에 데이터 원본을 커넥트 데이터를 수집합니다.](connect-rest-api-template.md)

### <a name="rest-api-integration-using-azure-functions"></a>Azure Functions를 사용하는 REST API 통합

[Azure Functions](../azure-functions/index.yml) 사용하여 공급자 API와 연결하는 통합은 먼저 데이터의 형식을 지정한 다음, [Azure Monitor 데이터 수집기 API를](../azure-monitor/logs/data-collector-api.md)사용하여 Microsoft Sentinel 사용자 지정 로그 테이블로 보냅니다.

공급자 API와 연결하고 Microsoft Sentinel에서 로그를 수집하도록 이러한 데이터 커넥터를 구성하려면 Microsoft Sentinel의 각 데이터 커넥터에 대해 표시된 단계를 수행합니다.

자세한 내용은 [Azure Functions 사용하여 Microsoft Sentinel에 데이터 원본 연결을 참조하세요.](connect-azure-functions-template.md)

> [!IMPORTANT]
> Azure Functions를 사용하는 통합에서는 Azure 테넌트에서 Azure Functions를 호스트하기 때문에 추가 데이터 수집 비용이 발생할 수 있습니다. 자세한 내용은 [Azure Functions 가격 책정 페이지](https://azure.microsoft.com/pricing/details/functions/)를 참조하세요.

## <a name="agent-based-integration"></a>에이전트 기반 통합

Microsoft Sentinel은 Syslog 프로토콜을 사용하여 에이전트를 통해 실시간 로그 스트리밍을 수행할 수 있는 모든 데이터 원본에 연결할 수 있습니다. 예를 들어 대부분의 온-프레미스 데이터 원본은 에이전트 기반 통합을 통해 연결됩니다.

다음 섹션에서는 다양한 유형의 Microsoft Sentinel 에이전트 기반 데이터 커넥터에 대해 설명합니다. 각 Microsoft Sentinel 데이터 커넥터 페이지의 단계에 따라 에이전트 기반 메커니즘을 사용하여 연결을 구성합니다.

CEF 또는 Syslog를 통해 Microsoft Sentinel에 연결하는 방화벽, 프록시 및 엔드포인트의 전체 목록은 [데이터 커넥터 참조를 참조하세요.](data-connectors-reference.md)

### <a name="syslog"></a>syslog

Linux용 Log Analytics 에이전트(이전의 OMS 에이전트)를 사용하여 Linux 기반 Syslog 지원 디바이스에서 Microsoft Sentinel로 이벤트를 스트리밍할 수 있습니다. Log Analytics 에이전트는 디바이스에 직접 Log Analytics 에이전트를 설치할 수 있는 모든 디바이스에 대해 지원됩니다.

디바이스의 기본 제공 Syslog 디먼이 지정된 형식의 로컬 이벤트를 수집하여 에이전트에 로컬로 전달합니다. 에이전트는 이러한 이벤트를 Log Analytics 작업 영역으로 스트리밍합니다. 성공적으로 구성되면 데이터가 Log Analytics Syslog 테이블에 나타납니다.

디바이스 유형에 따라 에이전트는 디바이스에 직접 설치되거나 전용 Linux 기반 로그 전달자에 설치됩니다. Log Analytics 에이전트는 UDP를 통해 Syslog 디먼에서 이벤트를 수신합니다. Linux 머신이 대량의 Syslog 이벤트를 수집할 것으로 예상되는 경우 TCP를 통해 Syslog 디먼에서 에이전트로, 에이전트에서 Log Analytics로 이벤트를 전송합니다.

자세한 내용은 [Microsoft Sentinel에 Syslog 기반 어플라이언스 커넥트 참조하세요.](connect-syslog.md)

### <a name="common-event-format-cef"></a>CEF(Common Event Format)

로그 형식은 다양하지만 대부분의 원본은 CEF 기반 형식을 지원합니다. 실제로 Log Analytics 에이전트인 Microsoft Sentinel 에이전트는 CEF 형식 로그를 Log Analytics에서 스트리밍할 수 있는 형식으로 변환합니다.

CEF에서 데이터를 내보내는 데이터 원본의 경우 Syslog 에이전트를 설정하고 CEF 데이터 흐름을 구성합니다. 성공적으로 구성되면 데이터가 **CommonSecurityLog** 테이블에 나타납니다.

자세한 내용은 [Microsoft Sentinel에 CEF 기반 어플라이언스 커넥트 참조하세요.](connect-common-event-format.md)

### <a name="custom-logs"></a>사용자 지정 로그

일부 데이터 원본에는 Windows 또는 Linux에서 파일로 수집할 수 있는 로그가 있습니다. Log Analytics 사용자 지정 로그 수집 에이전트를 사용하여 이러한 로그를 수집할 수 있습니다.

각 Microsoft Sentinel 데이터 커넥터 페이지의 단계에 따라 Log Analytics 사용자 지정 로그 수집 에이전트를 사용하여 연결합니다. 성공적으로 구성되면 데이터가 사용자 지정 테이블에 나타납니다.

자세한 내용은 [Log Analytics 에이전트를 사용하여 Microsoft Sentinel에 사용자 지정 로그 형식으로 데이터 수집을 참조하세요.](connect-custom-logs.md)

## <a name="service-to-service-integration"></a>서비스-서비스 통합

Microsoft Sentinel은 Azure Foundation을 사용하여 Microsoft 서비스 및 Amazon Web Services 대한 기본 서비스 지원을 제공합니다.

자세한 내용은 [Azure, Windows, Microsoft 및 Amazon 서비스에 연결](connect-azure-windows-microsoft-services.md) 및 [데이터 커넥터 참조](data-connectors-reference.md)를 참조하세요.

## <a name="deploy-as-part-of-a-solution"></a>솔루션의 일부로 배포

[Microsoft Sentinel 솔루션은](sentinel-solutions.md) 데이터 커넥터, 통합 문서, 분석 규칙, 플레이북 등을 포함한 보안 콘텐츠 패키지를 제공합니다. 데이터 커넥터를 사용하여 솔루션을 배포하는 경우 동일한 배포에서 관련 콘텐츠와 함께 데이터 커넥터를 가져옵니다.

자세한 내용은 [중앙에서 Microsoft Sentinel](sentinel-solutions-deploy.md) 콘텐츠 및 솔루션 검색 및 배포 및 [Microsoft Sentinel 솔루션 카탈로그를 참조하세요.](sentinel-solutions-catalog.md)

## <a name="data-connector-support"></a>데이터 커넥터 지원

Microsoft와 다른 조직 모두 Microsoft Sentinel 데이터 커넥터를 작성합니다. 각 데이터 커넥터에는 다음 지원 유형 중 하나가 있습니다.

| 지원 유형| 설명|
|-------------|------------|
|**Microsoft 지원**|적용 대상:<ul><li>Microsoft가 데이터 공급자이자 작성자인 데이터 원본에 대한 데이터 커넥터.</li><li>타사 데이터 원본에 대해 Microsoft가 작성한 일부 데이터 커넥터.</li></ul>Microsoft는 [Microsoft Azure 지원 플랜](https://azure.microsoft.com/support/options/#overview)에 따라 이 범주의 데이터 커넥터를 지원하고 유지 관리합니다.<br><br>Microsoft 이외의 당사자가 작성한 파트너 또는 커뮤니티 지원 데이터 커넥터입니다.|
|**파트너 지원**|Microsoft 이외의 당사자가 작성한 데이터 커넥터에 적용됩니다.<br><br>파트너 회사는 이러한 데이터 커넥터에 대한 지원 또는 유지 관리를 제공합니다. 파트너 회사는 독립 소프트웨어 공급업체, MSP/MSSP(관리 서비스 공급자), SI(시스템 통합자) 또는 해당 데이터 커넥터에 대한 Microsoft Sentinel 페이지에 연락처 정보가 제공되는 모든 조직일 수 있습니다.<br><br>파트너 지원 데이터 커넥터와 관련된 문제는 지정된 데이터 커넥터 지원 담당자에게 문의하세요.|
|**커뮤니티 지원**|Microsoft Sentinel의 지정된 데이터 커넥터 페이지에서 데이터 커넥터 지원 및 유지 관리를 위한 연락처가 나열되지 않은 Microsoft 또는 파트너 개발자가 작성한 데이터 커넥터에 적용됩니다.<br><br>이러한 데이터 커넥터에 대한 질문이나 문제는 [Microsoft Sentinel GitHub 커뮤니티에서](https://aka.ms/threathunters) [문제를 제출할](https://github.com/Azure/Azure-Sentinel/issues/new/choose) 수 있습니다.|

### <a name="find-the-support-contact-for-a-data-connector"></a>데이터 커넥터에 대한 지원 연락처 찾기

데이터 커넥터에 대한 지원 연락처 정보를 찾으려면 다음을 수행합니다.

1. Microsoft Sentinel 왼쪽 메뉴에서 **데이터 커넥터를 선택합니다.**

1. 지원 정보를 찾으려는 커넥터를 선택합니다.

1. 데이터 커넥터에 대한 사이드 패널에서 **지원** 필드를 확인합니다.

   ![Microsoft Sentinel의 데이터 커넥터에 대한 지원됨 필드를 보여주는 스크린샷.](./media/collect-data/connectors.png)

   **지원** 필드에는 선택한 데이터 커넥터에 대한 지원 및 유지 관리에 액세스하는 데 사용할 수 있는 지원 연락처 링크가 있습니다.

## <a name="next-steps"></a>다음 단계

- Microsoft Sentinel을 시작하려면 Microsoft Azure 구독이 필요합니다. 구독이 없는 경우 [평가판](https://azure.microsoft.com/free/)에 가입할 수 있습니다.
- [Microsoft Sentinel에 데이터를 온보딩하고 데이터](quickstart-onboard.md) [및 잠재적 위협에 대한 가시성을 얻는](get-visibility.md)방법을 알아봅니다.
