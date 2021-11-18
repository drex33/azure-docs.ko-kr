---
title: Microsoft 센티널 데이터 커넥터 | Microsoft Docs
description: Microsoft 365 Defender (이전의 Microsoft 위협 방지), Microsoft 365 및 Office 365, Azure AD, ATP 및 클라우드 앱 용 Defender와 같은 데이터 원본을 Microsoft 센티널에 연결 하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 4b7513641c4c7e375c26a959d3e05f7eaccc1135
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132721806"
---
# <a name="microsoft-sentinel-data-connectors"></a>Microsoft 센티널 데이터 커넥터

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Microsoft 센티널를 작업 영역에 온 보 딩 한 후 데이터 원본을 연결 하 여 데이터를 Microsoft 센티널로 수집 시작 합니다. Microsoft 센티널은 Microsoft 제품에 대 한 다양 한 커넥터와 함께 제공 되며, 기본적으로 제공 되며 실시간 통합을 제공 합니다. 예를 들어 서비스 간 커넥터에는 Microsoft 365 Defender 커넥터 및 Microsoft 365 원본 (예: Office 365, Azure Active Directory (Azure AD), id 용 microsoft defender 및 클라우드 앱 용 microsoft defender)이 포함 됩니다.

또한 타사 제품에 대 한 광범위 한 보안 에코 시스템에 대 한 기본 커넥터를 사용 하도록 설정할 수 있습니다. 예를 들어 [Syslog](#syslog), [cef (일반 이벤트 형식)](#common-event-format-cef)또는 [REST api](#rest-api-integration) 를 사용 하 여 데이터 원본을 Microsoft 센티널에 연결할 수 있습니다.

Microsoft 센티널 탐색 메뉴에서 액세스할 수 있는 **데이터 커넥터** 페이지에는 microsoft 센티널에서 제공 하는 커넥터의 전체 목록 및 작업 영역에서 해당 상태가 표시 됩니다. 연결하려는 커넥터를 선택하고 **커넥터 페이지 열기** 를 선택합니다.

![데이터 커넥터 갤러리](./media/collect-data/collect-data-page.png)

이 문서에서는 지원되는 데이터 연결 방법을 설명합니다. 자세한 내용은 [Microsoft 센티널 데이터 커넥터 참조](data-connectors-reference.md) 및 [microsoft 센티널 솔루션 카탈로그](sentinel-solutions-catalog.md)를 참조 하세요.

<a name="agent-options"></a>
<a name="data-connection-methods"></a>
<a name="map-data-types-with-microsoft-sentinel-connection-options"></a>

## <a name="enable-a-data-connector"></a>데이터 커넥터를 사용하도록 설정

Microsoft 센티널 탐색 메뉴에서 액세스할 수 있는 **데이터 커넥터** 페이지에는 microsoft 센티널에서 제공 하는 커넥터의 전체 목록 및 해당 상태가 표시 됩니다. 연결하려는 커넥터를 선택하고 **커넥터 페이지 열기** 를 선택합니다.

![데이터 커넥터 갤러리](./media/collect-data/collect-data-page.png)

모든 필수 구성 요소를 충족 해야 하며, 커넥터 페이지에 데이터를 Microsoft 센티널로 수집 하는 전체 지침이 표시 됩니다. 데이터 도착이 시작되는 데 다소 시간이 걸릴 수 있습니다. 연결되면 **데이터 수신됨** 그래프의 데이터 요약 및 데이터 형식의 연결 상태가 표시됩니다.

![데이터 커넥터 구성](./media/collect-data/opened-connector-page.png)

**다음 단계** 탭에는 위협을 감지 하 고 조사 하는 데 도움이 되는 특정 데이터 형식 샘플 쿼리, 시각화 문서 및 분석 규칙 템플릿에 대 한 Microsoft 센티널에서 제공 하는 추가 콘텐츠가 표시 됩니다.

![커넥터에 대한 다음 단계](./media/collect-data/data-insights.png)

자세한 내용은 [데이터 커넥터 참조](data-connectors-reference.md)에서 데이터 커넥터의 관련 섹션을 참조하세요.

## <a name="rest-api-integration"></a>REST API 통합

많은 보안 기술은 로그 파일을 검색 하기 위한 Api 집합을 제공 하며, 일부 데이터 소스는 이러한 Api를 사용 하 여 Microsoft 센티널에 연결할 수 있습니다.

API를 사용하는 데이터 커넥터는 다음 섹션에 설명된 대로 공급자 쪽에서 통합하거나 Azure Functions를 사용하여 통합합니다.

이 커넥터에 관한 전체 목록과 자세한 내용은 [데이터 커넥터 참조](data-connectors-reference.md)를 참조하세요.

### <a name="rest-api-integration-on-the-provider-side"></a>공급자 쪽에서 REST API 통합

공급자가 작성 하는 API 통합은 공급자 데이터 원본에 연결 하 고 [Azure Monitor 데이터 수집기 API](../azure-monitor/logs/data-collector-api.md)를 사용 하 여 Microsoft 센티널 사용자 지정 로그 테이블에 데이터를 푸시합니다.

자세한 내용은 공급자 설명서를 참조 하 고 데이터 [원본을 Microsoft 센티널의 REST API에 커넥트](connect-rest-api-template.md)하 여 데이터를 수집 합니다.

### <a name="rest-api-integration-using-azure-functions"></a>Azure Functions를 사용하는 REST API 통합

[Azure Functions](../azure-functions/index.yml) 를 사용 하 여 공급자 api와 연결 하는 통합에서는 먼저 데이터의 형식을 지정 하 고 [AZURE MONITOR 데이터 수집기 API](../azure-monitor/logs/data-collector-api.md)를 사용 하 여 Microsoft 센티널 사용자 지정 로그 테이블로 보냅니다.

공급자 API를 사용 하 여 연결 하 고 Microsoft 센티널에서 로그를 수집 하도록 이러한 데이터 커넥터를 구성 하려면 Microsoft 센티널의 각 데이터 커넥터에 대해 표시 된 단계를 따르세요.

자세한 내용은 [Azure Functions를 사용 하 여 데이터 원본을 Microsoft 센티널에 연결](connect-azure-functions-template.md)을 참조 하세요.

> [!IMPORTANT]
> Azure Functions를 사용하는 통합에서는 Azure 테넌트에서 Azure Functions를 호스트하기 때문에 추가 데이터 수집 비용이 발생할 수 있습니다. 자세한 내용은 [Azure Functions 가격 책정 페이지](https://azure.microsoft.com/pricing/details/functions/)를 참조하세요.

## <a name="agent-based-integration"></a>에이전트 기반 통합

Microsoft 센티널은 Syslog 프로토콜을 사용 하 여 에이전트를 통해 실시간 로그 스트리밍을 수행할 수 있는 모든 데이터 원본에 연결할 수 있습니다. 예를 들어 대부분의 온-프레미스 데이터 원본은 에이전트 기반 통합을 통해 연결됩니다.

다음 섹션에서는 다양 한 유형의 Microsoft 센티널 에이전트 기반 데이터 커넥터에 대해 설명 합니다. 각 Microsoft 센티널 데이터 커넥터 페이지의 단계에 따라 에이전트 기반 메커니즘을 사용 하 여 연결을 구성 합니다.

CEF 또는 Syslog를 통해 Microsoft 센티널에 연결 하는 방화벽, 프록시 및 끝점의 전체 목록은 [데이터 커넥터 참조](data-connectors-reference.md)를 참조 하세요.

### <a name="syslog"></a>syslog

이전에 OMS 에이전트 라고 하는 Linux 용 Log Analytics agent를 사용 하 여 Linux 기반 Syslog 지원 장치에서 Microsoft 센티널로 이벤트를 스트리밍할 수 있습니다. Log Analytics 에이전트는 디바이스에 직접 Log Analytics 에이전트를 설치할 수 있는 모든 디바이스에 대해 지원됩니다.

디바이스의 기본 제공 Syslog 디먼이 지정된 형식의 로컬 이벤트를 수집하여 에이전트에 로컬로 전달합니다. 에이전트는 이러한 이벤트를 Log Analytics 작업 영역으로 스트리밍합니다. 성공적으로 구성되면 데이터가 Log Analytics Syslog 테이블에 나타납니다.

디바이스 유형에 따라 에이전트는 디바이스에 직접 설치되거나 전용 Linux 기반 로그 전달자에 설치됩니다. Log Analytics 에이전트는 UDP를 통해 Syslog 디먼에서 이벤트를 수신합니다. Linux 머신이 대량의 Syslog 이벤트를 수집할 것으로 예상되는 경우 TCP를 통해 Syslog 디먼에서 에이전트로, 에이전트에서 Log Analytics로 이벤트를 전송합니다.

자세한 내용은 [Microsoft 센티널에 Syslog 기반 어플라이언스 커넥트를](connect-syslog.md)참조 하세요.

### <a name="common-event-format-cef"></a>CEF(Common Event Format)

로그 형식은 다양하지만 대부분의 원본은 CEF 기반 형식을 지원합니다. 실제로 Log Analytics 에이전트 인 Microsoft 센티널 에이전트는 CEF 형식의 로그를 Log Analytics 수집할 수 있는 형식으로 변환 합니다.

CEF에서 데이터를 내보내는 데이터 원본의 경우 Syslog 에이전트를 설정하고 CEF 데이터 흐름을 구성합니다. 성공적으로 구성되면 데이터가 **CommonSecurityLog** 테이블에 나타납니다.

자세한 내용은 [Microsoft 센티널로 cef 기반 어플라이언스 커넥트를](connect-common-event-format.md)참조 하세요.

### <a name="custom-logs"></a>사용자 지정 로그

일부 데이터 원본에는 Windows 또는 Linux에서 파일로 수집할 수 있는 로그가 있습니다. Log Analytics 사용자 지정 로그 수집 에이전트를 사용하여 이러한 로그를 수집할 수 있습니다.

각 Microsoft 센티널 데이터 커넥터 페이지의 단계에 따라 Log Analytics 사용자 지정 로그 수집 에이전트를 사용 하 여 연결 합니다. 성공적으로 구성되면 데이터가 사용자 지정 테이블에 나타납니다.

자세한 내용은 [Log Analytics 에이전트를 사용 하 여 사용자 지정 로그 형식으로 Microsoft 센티널에 데이터 수집](connect-custom-logs.md)을 참조 하세요.

## <a name="service-to-service-integration"></a>서비스 간 통합

Microsoft 센티널은 Azure foundation을 사용 하 여 Microsoft 서비스 및 Amazon Web Services에 대 한 서비스 간 기본 지원을 제공 합니다.

자세한 내용은 [Azure, Windows, Microsoft 및 Amazon 서비스에 연결](connect-azure-windows-microsoft-services.md) 및 [데이터 커넥터 참조](data-connectors-reference.md)를 참조하세요.

## <a name="deploy-as-part-of-a-solution"></a>솔루션의 일부로 배포

[Microsoft 센티널 솔루션](sentinel-solutions.md) 은 데이터 커넥터, 통합 문서, 분석 규칙, 플레이 북 등을 비롯 한 보안 콘텐츠 패키지를 제공 합니다. 데이터 커넥터를 사용하여 솔루션을 배포하는 경우 동일한 배포에서 관련 콘텐츠와 함께 데이터 커넥터를 가져옵니다.

자세한 내용은 [Microsoft 센티널의 중앙 검색 및 배포](sentinel-solutions-deploy.md) (영문) 및 [microsoft 센티널 솔루션 카탈로그](sentinel-solutions-catalog.md)를 참조 하세요.

## <a name="data-connector-support"></a>데이터 커넥터 지원

Microsoft와 다른 조직은 모두 Microsoft 센티널 데이터 커넥터를 작성 합니다. 각 데이터 커넥터에는 다음 지원 유형 중 하나가 있습니다.

| 지원 유형| 설명|
|-------------|------------|
|**Microsoft 지원**|적용 대상:<ul><li>Microsoft가 데이터 공급자이자 작성자인 데이터 원본에 대한 데이터 커넥터.</li><li>타사 데이터 원본에 대해 Microsoft가 작성한 일부 데이터 커넥터.</li></ul>Microsoft는 [Microsoft Azure 지원 플랜](https://azure.microsoft.com/support/options/#overview)에 따라 이 범주의 데이터 커넥터를 지원하고 유지 관리합니다.<br><br>Microsoft 이외의 당사자가 작성한 파트너 또는 커뮤니티 지원 데이터 커넥터입니다.|
|**파트너 지원**|Microsoft 이외의 당사자가 작성한 데이터 커넥터에 적용됩니다.<br><br>파트너 회사는 이러한 데이터 커넥터에 대한 지원 또는 유지 관리를 제공합니다. 파트너 회사는 독립 소프트웨어 공급 업체, 관리 서비스 공급자 (MSP/MSSP), SI (시스템 통합 업체) 또는 해당 데이터 커넥터용 Microsoft 센티널 페이지에서 연락처 정보를 제공 하는 조직 일 수 있습니다.<br><br>파트너 지원 데이터 커넥터와 관련된 문제는 지정된 데이터 커넥터 지원 담당자에게 문의하세요.|
|**커뮤니티 지원**|Microsoft 센티널의 지정 된 데이터 커넥터 페이지에서 데이터 커넥터 지원 및 유지 관리를 위해 나열 된 연락처가 없는 Microsoft 또는 파트너 개발자가 작성 한 데이터 커넥터에 적용 됩니다.<br><br>이러한 데이터 커넥터와 관련 된 질문이 나 문제가 있는 경우 [Microsoft 센티널 GitHub 커뮤니티](https://aka.ms/threathunters)에서 [문제](https://github.com/Azure/Azure-Sentinel/issues/new/choose) 를 확인할 수 있습니다.|

### <a name="find-the-support-contact-for-a-data-connector"></a>데이터 커넥터에 대한 지원 연락처 찾기

데이터 커넥터에 대한 지원 연락처 정보를 찾으려면 다음을 수행합니다.

1. Microsoft 센티널 왼쪽 메뉴에서 **데이터 커넥터** 를 선택 합니다.

1. 지원 정보를 찾으려는 커넥터를 선택합니다.

1. 데이터 커넥터에 대한 사이드 패널에서 **지원** 필드를 확인합니다.

   ![Microsoft 센티널의 데이터 커넥터에 대해 지원 되는 필드를 보여 주는 스크린샷](./media/collect-data/connectors.png)

   **지원** 필드에는 선택한 데이터 커넥터에 대한 지원 및 유지 관리에 액세스하는 데 사용할 수 있는 지원 연락처 링크가 있습니다.

## <a name="next-steps"></a>다음 단계

- Microsoft 센티널을 시작 하려면 Microsoft Azure 구독이 필요 합니다. 구독이 없는 경우 [평가판](https://azure.microsoft.com/free/)에 가입할 수 있습니다.
- [Microsoft 센티널에 데이터](quickstart-onboard.md) 를 등록 하 고 [데이터 및 잠재적 위협을 파악](get-visibility.md)하는 방법을 알아봅니다.
