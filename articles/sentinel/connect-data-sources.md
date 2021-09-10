---
title: Azure Sentinel에 데이터 원본 연결 | Microsoft Docs
description: Microsoft 365 Defender(이전의 Microsoft Threat Protection), Microsoft 365 및 Office 365, Azure AD, ATP 및 Cloud App Security와 같은 데이터 소스를 Azure Sentinel에 연결하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2021
ms.author: yelevin
ms.openlocfilehash: c9bce99f26d7d23d2bf655719373f43e78b5a8cb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567303"
---
# <a name="connect-data-sources"></a>데이터 원본 연결

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Azure Sentinel을 사용하도록 설정한 후에는 먼저 데이터 원본을 연결해야 합니다. Azure Sentinel에는 즉시 사용 가능하고 실시간 통합을 제공하는 다양한 Microsoft 제품용 커넥터가 포함되어 있습니다. 예를 들어 서비스 간 커넥터로는 Microsoft 365 Defender 커넥터 및 Microsoft 365 원본(예: Office 365, Azure AD(Azure Active Directory), Microsoft Defender for Identity 및 Microsoft Cloud App Security)이 있습니다.

타사 제품을 위한 광범위한 보안 에코시스템에 기본 제공 커넥터를 사용할 수도 있습니다. 예를 들어 [Syslog](#syslog), [CEF(Common Event Format)](#common-event-format-cef), [REST API](#rest-api-integration)를 사용하여 Azure Sentinel에 데이터 원본을 연결할 수 있습니다.

자세한 내용은 [Azure Sentinel 파트너 데이터 커넥터](partner-data-connectors.md) 및 [Azure Sentinel 솔루션 카탈로그](sentinel-solutions-catalog.md)를 참조하세요.

## <a name="enable-a-data-connector"></a>데이터 커넥터를 사용하도록 설정

Azure Sentinel에서 데이터 커넥터를 사용하도록 설정하려면 다음을 수행합니다.

1. Azure Sentinel 왼쪽 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다. **데이터 커넥터** 페이지에는 Azure Sentinel에서 제공하는 커넥터의 전체 목록 및 해당 상태가 표시됩니다. 연결하려는 커넥터를 선택하고 **커넥터 페이지 열기** 를 선택합니다. 

   ![데이터 커넥터 갤러리](./media/collect-data/collect-data-page.png)
   
   자세한 내용은 [서비스 간 통합](#service-to-service-integration) 및 [Azure Sentinel 파트너 데이터 커넥터](partner-data-connectors.md)를 참조하세요.
   

1. 커넥터 페이지에서 모든 필수 조건을 충족했는지 확인하고 지침에 따라 Azure Sentinel에 데이터를 연결합니다. 로그와 Azure Sentinel의 동기화를 시작하는 데 약간의 시간이 걸릴 수 있습니다. 연결되면 **데이터 수신됨** 그래프의 데이터 요약 및 데이터 형식의 연결 상태가 표시됩니다.

   ![데이터 커넥터 구성](./media/collect-data/opened-connector-page.png)
  
1. **다음 단계** 탭을 선택하여 특정 데이터 형식에 대해 Azure Sentinel에서 제공하는 콘텐츠를 확인합니다.

   ![커넥터에 대한 다음 단계](./media/collect-data/data-insights.png)

<a name="agent-options"></a>
## <a name="data-connection-methods"></a>데이터 연결 메서드
<a name="map-data-types-with-azure-sentinel-connection-options"></a>

Azure Sentinel은 다음과 같은 데이터 연결 방법을 지원합니다.

### <a name="service-to-service-integration"></a>서비스 간 통합

Azure Sentinel은 Azure 기반을 사용하여 다음 Microsoft 서비스와 Amazon Web Services를 위해 기본 제공 서비스 간 지원을 제공합니다.

- [Amazon Web Services - CloudTrail](connect-aws.md)
- [Azure Active Directory](connect-azure-active-directory.md)(감사 로그 및 로그인 로그 포함)
- [Azure Active Directory ID 보호](connect-azure-ad-identity-protection.md)
- [Azure 활동](connect-azure-activity.md)
- [Azure DDoS Protection](connect-azure-ddos-protection.md)
- Azure Security Center의 [Azure Defender](connect-azure-security-center.md) 경고
- [Azure Defender for IoT](connect-asc-iot.md)(이전의 Azure Security Center for IoT)
- [Azure Firewall](connect-azure-firewall.md)
- [Azure Information Protection](connect-azure-information-protection.md). 자세한 내용은 [보고서를 수정하고 사용자 지정 쿼리를 만드는 방법](/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)을 참조하세요.
- [Azure Key Vault](connect-azure-key-vault.md)
- [AKS(Azure Kubernetes Service)](connect-azure-kubernetes-service.md)
- [Azure SQL 데이터베이스](connect-azure-sql-logs.md)
- [Azure Storage 계정](connect-azure-storage-account.md)
- [WAF(Azure Web Application Firewall)](connect-azure-waf.md)(이전의 Microsoft WAF)
- [도메인 이름 서버](connect-dns.md)
- [Dynamics 365](connect-dynamics-365.md)
- [Microsoft 365 Defender](connect-microsoft-365-defender.md)(엔드포인트용 Microsoft 365 Defender의 Microsoft 365 Defender 인시던트 및 원시 데이터 포함)
- [MCAS(Microsoft Cloud App Security)](connect-cloud-app-security.md)
- [엔드포인트용 Microsoft Defender](connect-microsoft-defender-advanced-threat-protection.md)(이전의 Microsoft Defender Advanced Threat Protection)
- [Microsoft Defender for Identity](connect-azure-atp.md)(이전의 Azure Advanced Threat Protection)
- [Microsoft Defender for Office 365](connect-office-365-advanced-threat-protection.md)(이전의 Office 365 Advanced Threat Protection)
- [Office 365](connect-office-365.md)(Microsoft Teams 포함)
- [보안 이벤트(Windows)](connect-windows-security-events.md). 자세한 내용은 [안전하지 않은 프로토콜 통합 문서 설정](./get-visibility.md#use-built-in-workbooks)을 참조하세요.
- [Windows 방화벽](connect-windows-firewall.md)

### <a name="rest-api-integration"></a>REST API 통합

많은 보안 기술에서 로그 파일을 검색하기 위한 API 세트를 제공하며 일부 데이터 원본은 이러한 API를 사용하여 Azure Sentinel에 연결할 수 있습니다.

API를 사용하는 데이터 커넥터는 다음 섹션에 설명된 대로 공급자 쪽에서 통합하거나 Azure Functions를 사용하여 통합합니다.

이러한 커넥터에 대한 전체 목록 및 자세한 정보는 [Azure Sentinel 파트너 데이터 커넥터](partner-data-connectors.md)를 참조하세요.

#### <a name="rest-api-integration-on-the-provider-side"></a>공급자 쪽에서 REST API 통합

공급자에 의해 구축된 API 통합은 공급자 데이터 원본에 연결되고 [Azure Monitor 데이터 수집기 API](../azure-monitor/logs/data-collector-api.md)를 사용하여 Azure Sentinel 사용자 지정 로그 테이블에 데이터를 푸시합니다.

Azure Sentinel에 로그를 보내도록 이러한 데이터 커넥터를 구성하는 방법에 대한 자세한 내용은 관련 공급자 설명서를 참조하세요. 

#### <a name="rest-api-integration-using-azure-functions"></a>Azure Functions를 사용하는 REST API 통합

[Azure Functions](../azure-functions/index.yml)를 사용하여 공급자 API와 연결하는 통합에서는 먼저 데이터의 서식을 지정한 다음, [Azure Monitor 데이터 수집기 API](../azure-monitor/logs/data-collector-api.md)를 사용하여 Azure Sentinel 사용자 지정 로그 테이블로 데이터를 전송합니다.

공급자 API와 연결하고 Azure Sentinel에서 로그를 수집하도록 이러한 데이터 커넥터를 구성하는 방법에 대한 자세한 내용은 Azure Sentinel의 각 데이터 커넥터에 대해 표시된 단계를 참조하세요.

Azure Functions를 사용하는 통합의 Azure Sentinel 데이터 커넥터 페이지에는 **Azure에 배포** 단추도 있습니다. 이러한 통합에서는 다음을 수행하여 구성을 간소화합니다.

1. **Azure에 배포** 를 선택합니다.
1. 매개 변수 값을 입력하여 공급자 API와 연결합니다.
1. Azure Sentinel 작업 영역 ID 및 키를 입력하여 Log Analytics에 연결하고 Azure Sentinel로 데이터를 보냅니다.

> [!IMPORTANT]
> Azure Functions를 사용하는 통합에서는 Azure 테넌트에서 Azure Functions를 호스트하기 때문에 추가 데이터 수집 비용이 발생할 수 있습니다. 자세한 내용은 [Azure Functions 가격 책정 페이지](https://azure.microsoft.com/pricing/details/functions/)를 참조하세요.

### <a name="agent-based-integration"></a>에이전트 기반 통합

Azure Sentinel은 Syslog 프로토콜을 사용하여 실시간 로그 스트리밍을 수행할 수 있는 데이터 원본에 에이전트를 통해 연결할 수 있습니다. 예를 들어 대부분의 온-프레미스 데이터 원본은 에이전트 기반 통합을 통해 연결됩니다.
다음 섹션에서는 다양한 유형의 Azure Sentinel 에이전트 기반 데이터 커넥터에 대해 설명합니다. 각 Azure Sentinel 데이터 커넥터 페이지의 단계에 따라 에이전트 기반 메커니즘을 사용하는 연결을 구성합니다.

CEF 또는 Syslog를 통해 Azure Sentinel에 연결하는 방화벽, 프록시 및 엔드포인트의 전체 목록은 [Azure Sentinel 파트너 데이터 커넥터](partner-data-connectors.md)를 참조하세요.

#### <a name="syslog"></a>syslog

Linux 기반 Syslog 지원 디바이스에서 Linux용 Log Analytics 에이전트(이전의 OMS 에이전트)를 사용하여 Azure Sentinel로 이벤트를 스트리밍할 수 있습니다. Log Analytics 에이전트는 디바이스에 직접 Log Analytics 에이전트를 설치할 수 있는 모든 디바이스에 대해 지원됩니다.

디바이스의 기본 제공 Syslog 디먼이 지정된 형식의 로컬 이벤트를 수집하여 에이전트에 로컬로 전달합니다. 에이전트는 이러한 이벤트를 Log Analytics 작업 영역으로 스트리밍합니다. 성공적으로 구성되면 데이터가 Log Analytics Syslog 테이블에 나타납니다.

디바이스 유형에 따라 에이전트는 디바이스에 직접 설치되거나 전용 Linux 기반 로그 전달자에 설치됩니다. Log Analytics 에이전트는 UDP를 통해 Syslog 디먼에서 이벤트를 수신합니다. Linux 머신이 대량의 Syslog 이벤트를 수집할 것으로 예상되는 경우 TCP를 통해 Syslog 디먼에서 에이전트로, 에이전트에서 Log Analytics로 이벤트를 전송합니다.

자세한 내용은 [Azure Sentinel에 Syslog 기반 어플라이언스 연결](connect-syslog.md)을 참조하세요. 

#### <a name="common-event-format-cef"></a>CEF(Common Event Format)

로그 형식은 다양하지만 대부분의 원본은 CEF 기반 형식을 지원합니다. 실제로는 Log Analytics 에이전트인 Azure Sentinel 에이전트는 CEF 형식의 로그를 Log Analytics에서 수집할 수 있는 형식으로 변환합니다.

CEF에서 데이터를 내보내는 데이터 원본의 경우 Syslog 에이전트를 설정하고 CEF 데이터 흐름을 구성합니다. 성공적으로 구성되면 데이터가 **CommonSecurityLog** 테이블에 나타납니다.

자세한 내용은 [Azure Sentinel에 CEF 기반 어플라이언스 연결](connect-common-event-format.md)을 참조하세요.

#### <a name="custom-logs"></a>사용자 지정 로그

일부 데이터 원본에는 Windows 또는 Linux에서 파일로 수집할 수 있는 로그가 있습니다. Log Analytics 사용자 지정 로그 수집 에이전트를 사용하여 이러한 로그를 수집할 수 있습니다.

각 Azure Sentinel 데이터 커넥터 페이지의 단계에 따라 Log Analytics 사용자 지정 로그 수집 에이전트를 사용하여 연결합니다. 성공적으로 구성되면 데이터가 사용자 지정 테이블에 나타납니다.

자세한 내용은 [Log Analytics 에이전트를 사용하여 사용자지정 로그 수집](../azure-monitor/agents/data-sources-custom-logs.md)을 참조하세요.

## <a name="data-connector-support"></a>데이터 커넥터 지원

Microsoft와 다른 조직 모두 Azure Sentinel 데이터 커넥터를 작성합니다. 각 데이터 커넥터에는 다음 지원 유형 중 하나가 있습니다.

| 지원 유형| 설명|
|-------------|------------|
|**Microsoft 지원**|적용 대상:<ul><li>Microsoft가 데이터 공급자이자 작성자인 데이터 원본에 대한 데이터 커넥터.</li><li>타사 데이터 원본에 대해 Microsoft가 작성한 일부 데이터 커넥터.</li></ul>Microsoft는 [Microsoft Azure 지원 플랜](https://azure.microsoft.com/support/options/#overview)에 따라 이 범주의 데이터 커넥터를 지원하고 유지 관리합니다.<br><br>Microsoft 이외의 당사자가 작성한 파트너 또는 커뮤니티 지원 데이터 커넥터입니다.|
|**파트너 지원**|Microsoft 이외의 당사자가 작성한 데이터 커넥터에 적용됩니다.<br><br>파트너 회사는 이러한 데이터 커넥터에 대한 지원 또는 유지 관리를 제공합니다. 파트너 회사는 독립 소프트웨어 공급업체, 관리형 서비스 공급자(MSP/MSSP), SI(시스템 통합자) 또는 해당 데이터 커넥터의 Azure Sentinel 페이지에 연락처 정보가 제공되는 모든 조직일 수 있습니다.<br><br>파트너 지원 데이터 커넥터와 관련된 문제는 지정된 데이터 커넥터 지원 담당자에게 문의하세요.|
|**커뮤니티 지원**|Azure Sentinel의 지정된 데이터 커넥터 페이지에 데이터 커넥터 지원 및 유지 관리를 위한 연락처가 나열되지 않은 파트너 개발자 또는 Microsoft가 작성한 데이터 커넥터에 적용됩니다.<br><br>이러한 데이터 커넥터와 관련된 질문이나 문제가 있는 경우 [Azure Sentinel GitHub 커뮤니티](https://aka.ms/threathunters)에서 [문제를 제출](https://github.com/Azure/Azure-Sentinel/issues/new/choose)할 수 있습니다.|

### <a name="find-the-support-contact-for-a-data-connector"></a>데이터 커넥터에 대한 지원 연락처 찾기

데이터 커넥터에 대한 지원 연락처 정보를 찾으려면 다음을 수행합니다.

1. Azure Sentinel 왼쪽 메뉴에서 **데이터 커넥터** 를 선택합니다.
   
1. 지원 정보를 찾으려는 커넥터를 선택합니다.
   
1. 데이터 커넥터에 대한 사이드 패널에서 **지원** 필드를 확인합니다.
   
   ![Azure Sentinel의 데이터 커넥터에 대한 지원 필드를 보여 주는 스크린샷](./media/collect-data/connectors.png)
   
   **지원** 필드에는 선택한 데이터 커넥터에 대한 지원 및 유지 관리에 액세스하는 데 사용할 수 있는 지원 연락처 링크가 있습니다.



## <a name="next-steps"></a>다음 단계

- Azure Sentinel을 시작하려면 Microsoft Azure에 대한 구독이 필요합니다. 구독이 없는 경우 [평가판](https://azure.microsoft.com/free/)에 가입할 수 있습니다.
- [Azure Sentinel에 데이터를 온보딩](quickstart-onboard.md)하고 [데이터 및 잠재적 위협을 표시](get-visibility.md)하는 방법에 대해 알아봅니다.