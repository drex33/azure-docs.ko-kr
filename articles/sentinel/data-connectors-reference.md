---
title: Azure Sentinel 데이터 커넥터 찾기 | Microsoft Docs
description: Azure Sentinel 데이터 커넥터의 특정 구성 단계에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: batamig
ms.service: azure-sentinel
ms.topic: reference
ms.date: 10/11/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8f9a02149cb7229a201f8dfb9c11381b19d8e8ec
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131023332"
---
# <a name="find-your-azure-sentinel-data-connector"></a>Azure Sentinel 데이터 커넥터 찾기

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서에서는 Azure 센티널에서 데이터 커넥터를 배포 하는 방법을 설명 합니다. 여기에는 지원 되는 모든 기본 데이터 커넥터와 일반 배포 절차에 대 한 링크 및 특정 커넥터에 필요한 추가 단계가 나열 되어 있습니다.

> [!TIP]
> 일부 데이터 커넥터는 솔루션을 통해서만 배포 됩니다. 자세한 내용은 [Azure Sentinel 솔루션 카탈로그](sentinel-solutions-catalog.md)를 참조하세요. [Azure 센티널 GitHub 리포지토리에서](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors)커뮤니티에서 빌드된 다른 데이터 커넥터를 찾을 수도 있습니다.
>

## <a name="how-to-use-this-guide"></a>이 가이드를 사용하는 방법

1. 먼저 오른쪽의 제목 메뉴에서 제품, 서비스 또는 디바이스에 대한 커넥터를 찾아 선택합니다.

    각 커넥터에 대해 가장 먼저 볼 수 있는 정보는 **데이터 수집 방법** 입니다. 표시되는 방법은 데이터 원본을 Azure Sentinel에 연결하는 데 필요한 대부분의 정보가 포함된 다음 일반 배포 절차 중 하나에 대한 링크입니다.

    | 데이터 수집 방법 | 지침이 있는 링크된 문서 |
    | --- | --- |
    | **Azure 서비스 간 통합** | [Azure, Windows, Microsoft 및 Amazon 서비스에 연결](connect-azure-windows-microsoft-services.md) |
    | **Syslog를 통한 CEF(Common Event Format)** | [디바이스 또는 어플라이언스에서 Azure Sentinel로 CEF 형식 로그 가져오기](connect-common-event-format.md) |
    | **Azure Sentinel Data Collector API** | [데이터 원본을 Azure 센티널 데이터 수집기 API에 커넥트 하 여 데이터 수집](connect-rest-api-template.md) |
    | **Azure Functions 및 REST API** | [Azure Functions를 사용하여 Azure Sentinel을 데이터 원본에 연결](connect-azure-functions-template.md) |
    | **Syslog** | [Syslog를 사용하여 Linux 기반 소스에서 데이터 수집](connect-syslog.md) |
    | **사용자 지정 로그** | [Log Analytics 에이전트를 사용하여 Azure Sentinel에 사용자 지정 로그 형식의 데이터 수집](connect-custom-logs.md) |
    |

    > [!NOTE]
    > **Azure 서비스 간 통합** 데이터 수집 방법은 커넥터 유형에 따라 해당 문서의 세 가지 섹션으로 연결됩니다. 아래의 각 커넥터 섹션은 링크가 연결되는 해당 문서 내의 섹션을 지정합니다.

1. 특정 커넥터를 배포 하는 경우 해당 **데이터 수집 방법** 에 연결 된 적절 한 문서를 선택 하 고 아래의 관련 섹션에서 정보 및 추가 지침을 사용 하 여 해당 문서의 정보를 보충 합니다.

> [!TIP]
> - 많은 데이터 커넥터를 관련 분석 규칙, 통합 문서 및 플레이북과 함께 [Azure Sentinel 솔루션](sentinel-solutions.md)의 일부로 배포할 수도 있습니다. 자세한 내용은 [Azure Sentinel 솔루션 카탈로그](sentinel-solutions-catalog.md)를 참조하세요.
>
>
> - 더 많은 데이터 커넥터는 Azure Sentinel 커뮤니티에서 제공하며 Azure Marketplace에서 찾을 수 있습니다. 커뮤니티 데이터 커넥터에 대한 문서는 커넥터를 만든 조직의 책임입니다.
>
>
> - 나열되지 않거나 현재 지원되지 않는 데이터 원본이 있는 경우 고유한 사용자 지정 커넥터를 만들 수도 있습니다. 자세한 내용은 [Azure Sentinel 사용자 지정 커넥터를 만들기 위한 리소스](create-custom-connector.md)를 참조하세요.
>

> [!IMPORTANT]
> 알려진 Azure Sentinel 데이터 커넥터는 현재 **미리 보기** 상태입니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.

## <a name="agari-phishing-defense-and-brand-protection-preview"></a>Agari Phishing Defense 및 Brand Protection(미리 보기)

| 커넥터 특성 | Description|
| --- | --- |
| **데이터 수집 방법** | [**Azure Functions 및 REST API**](connect-azure-functions-template.md) <br><br>**배포 전**: [Security Graph API를 사용하도록 설정합니다(선택 사항)](#enable-the-security-graph-api-optional). <br>**배포 후**: [함수 앱에 필요한 권한 할당](#assign-necessary-permissions-to-your-function-app)|
| **Log Analytics 테이블** | agari_bpalerts_log_CL<br>agari_apdtc_log_CL<br>agari_apdpolicy_log_CL |
| **Azure 함수 앱 코드** | https://aka.ms/sentinel-agari-functionapp |
| **API 자격 증명** | <li>클라이언트 ID<li>클라이언트 암호<li>(선택 사항: Graph 테넌트 ID, Graph 클라이언트 ID, Graph 클라이언트 암호) |
| **공급업체 문서/<br>설치 지침** | <li>[빠른 시작](https://developers.agari.com/agari-platform/docs/quick-start)<li>[Agari 개발자 사이트](https://developers.agari.com/agari-platform) |
| **커넥터 배포 지침** | <li>ARM(Azure Resource Manager) 템플릿을 통한 [단일 클릭 배포](connect-azure-functions-template.md?tabs=ARM)<li>[수동 배포](connect-azure-functions-template.md?tabs=MPS) |
| **애플리케이션 설정** | <li>clientID<li>clientSecret<li>workspaceID<li>workspaceKey<li>enableBrandProtectionAPI (true/false)<li>enablePhishingResponseAPI (true/false)<li>enablePhishingDefenseAPI (true/false)<li>resGroup(리소스 그룹 입력)<li>functionName<li>subId(구독 ID 입력)<li>enableSecurityGraphSharing (true/false; see below)<br>enableSecurityGraphSharing이 true로 설정된 경우 필수(아래 참조):<li>GraphTenantId<li>GraphClientId<li>GraphClientSecret<li>logAnalyticsUri(선택 사항) |
| **지원 요소** | [Agari](https://support.agari.com/hc/en-us/articles/360000645632-How-to-access-Agari-Support) |
| | |

### <a name="enable-the-security-graph-api-optional"></a>Security Graph API 사용(선택 사항)

> [!IMPORTANT]
> 이 단계를 수행하는 경우 데이터 커넥터를 배포하기 전에 이 작업을 수행합니다.
>
Agari 함수 앱을 사용하여 Security Graph API를 통해 Azure Sentinel과 위협 인텔리전스를 공유할 수 있습니다. 이 기능을 사용하려면 [Sentinel 위협 인텔리전스 플랫폼 커넥터](./connect-threat-intelligence-tip.md)를 사용하도록 설정하고 Azure Active Directory에 [애플리케이션을 등록](/graph/auth-register-app-v2)해야 합니다.

이 프로세스는 [함수 앱 배포](connect-azure-functions-template.md) 시 사용할 세 가지 정보인 **그래프 테넌트 ID**, **그래프 클라이언트 ID** 및 **그래프 클라이언트 암호** 를 제공합니다(위 표의 *애플리케이션 설정* 참조).

### <a name="assign-necessary-permissions-to-your-function-app"></a>함수 앱에 필요한 권한 할당

Agari 커넥터는 환경 변수를 사용하여 로그 액세스 타임스탬프를 저장합니다. 애플리케이션이 이 변수에 쓰려면 시스템 할당 ID에 사용 권한을 할당해야 합니다.

1. Azure Portal에서 **함수 앱** 으로 이동합니다.
1. **함수 앱** 페이지의 목록에서 함수 앱를 선택 하 고 함수 앱의 탐색 메뉴에서 **설정** 아래에서 **id** 를 선택 합니다.
1. **시스템 할당** 탭에서 **상태** 를 **켜기** 로 설정합니다.
1. **저장** 을 선택하면 **Azure 역할 할당** 단추가 나타납니다. 이 폴더를 선택합니다.
1. **Azure 역할 할당** 화면에서 **역할 할당 추가** 를 선택합니다. **범위** 를 **구독** 으로 설정하고, **구독** 드롭다운에서 구독을 선택하고, **역할** 을 **앱 구성 데이터 소유자** 로 설정합니다.
1. **저장** 을 선택합니다.


## <a name="ai-analyst-aia-by-darktrace-preview"></a>Darktrace의 AIA(AI Analyst)(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Syslog를 통한 **[CEF(Common Event Format)](connect-common-event-format.md)** <br><br>[AI Analyst에 대한 CEF 로그 전달 구성](#configure-cef-log-forwarding-for-ai-analyst) |
| **Log Analytics 테이블** | CommonSecurityLog |
| **지원 요소** | [Darktrace](https://customerportal.darktrace.com/) |
| | |

### <a name="configure-cef-log-forwarding-for-ai-analyst"></a>AI Analyst에 대한 CEF 로그 전달 구성

Log Analytics 에이전트를 통해 CEF 형식의 Syslog 메시지를 Azure 작업 영역으로 전달하도록 Darktrace를 구성합니다.

1. Darktrace Threat Visualizer 내에서 **관리자** 아래 기본 메뉴에 있는 **시스템 구성** 페이지로 이동합니다.
1. 왼쪽 메뉴에서 **모듈** 을 선택하고 사용 가능한 **워크플로 통합** 에서 **Azure Sentinel** 을 선택합니다.
1. 구성 창이 열립니다. **Azure Sentinel Syslog CEF** 를 찾아 **새로 만들기** 를 선택하여 이미 노출되지 않은 구성 설정을 표시합니다.
1. **서버 구성** 필드에 로그 전달자의 위치를 입력하고 선택적으로 통신 포트를 수정합니다. 선택한 포트가 514로 설정되어 있고 모든 중간 방화벽에서 허용되는지 확인합니다.
1. 필요에 따라 경고 임계값, 시간 오프셋 또는 추가 설정을 구성합니다.
1. Syslog 구문을 변경하는 데 사용할 수 있는 추가 구성 옵션을 검토합니다.
1. **알림 보내기** 를 사용하도록 설정하고 변경 사항을 저장합니다.

## <a name="ai-vectra-detect-preview"></a>AI Vectra Detect(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Syslog를 통한 **[CEF(Common Event Format)](connect-common-event-format.md)** <br><br>[AI Vectra Detect에 대한 CEF 로그 전달 구성](#configure-cef-log-forwarding-for-ai-vectra-detect)|
| **Log Analytics 테이블** | CommonSecurityLog |
| **지원 요소** | [Vectra AI](https://www.vectra.ai/support) |
| | |

### <a name="configure-cef-log-forwarding-for-ai-vectra-detect"></a>AI Vectra Detect에 대한 CEF 로그 전달 구성

Log Analytics 에이전트를 통해 Azure Sentinel 작업 영역에 CEF 형식의 Syslog 메시지를 전달하도록 Vectra(X 시리즈) 에이전트를 구성합니다.

Vectra 인터페이스에서 설정 > 알림으로 이동하고 Syslog 구성 편집을 선택합니다. 아래 지침에 따라 연결을 설정합니다,

- 새 대상 추가(로그 전달자의 호스트 이름)
- 포트를 **514** 로 설정
- 프로토콜을 **UDP** 로 설정
- 형식을 **CEF** 로 설정
- 로그 형식 설정(사용할 수 있는 모든 로그 형식 선택)
- **저장** 을 선택합니다.

**테스트** 단추를 선택하여 일부 테스트 이벤트를 로그 전달자에게 강제로 보낼 수 있습니다.

자세한 내용은 UI 검색의 리소스 페이지에서 다운로드할 수 있는 Cognito Syslog 검색 가이드를 참조하세요.

## <a name="akamai-security-events-preview"></a>Akamai Security Events(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Kusto 함수 파서가 있는 **Syslog를 통한 [CEF(Common Event Format)](connect-common-event-format.md)** |
| **Log Analytics 테이블** | CommonSecurityLog |
| **Kusto 함수 별칭:** | AkamaiSIEMEvent |
| **Kusto 함수 URL:** | https://aka.ms/sentinel-akamaisecurityevents-parser |
| **공급업체 문서/<br>설치 지침** | [SIEM(보안 정보 및 이벤트 관리) 통합 구성](https://developer.akamai.com/tools/integrations/siem)<br>[CEF 커넥터를 설정합니다](https://developer.akamai.com/tools/integrations/siem/siem-cef-connector). |
| **지원 요소** | [Akamai](https://www.akamai.com/us/en/support/) |
| | |

## <a name="alcide-kaudit"></a>Alcide kAudit

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Sentinel 데이터 수집기 API**](connect-rest-api-template.md) |
| **Log Analytics 테이블** | alcide_kaudit_activity_1_CL - Alcide kAudit 활동 로그<br>alcide_kaudit_detections_1_CL - Alcide kAudit 검색<br>alcide_kaudit_selections_count_1_CL - Alcide kAudit 활동 수<br>alcide_kaudit_selections_details_1_CL - Alcide kAudit 활동 세부 정보 |
| **공급업체 문서/<br>설치 지침** | [Alcide kAudit 설치 가이드](https://awesomeopensource.com/project/alcideio/kaudit?categoryPage=29#before-installing-alcide-kaudit) |
| **지원 요소** | [Alcide](https://www.alcide.io/company/contact-us/) |
| | |

## <a name="alsid-for-active-directory"></a>Active Directory용 Alsid

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Log Analytics 에이전트 - 사용자 지정 로그**](connect-custom-logs.md) <br><br>[Alsid에 대한 추가 구성](#extra-configuration-for-alsid)|
| **Log Analytics 테이블** | AlsidForADLog_CL |
| **Kusto 함수 별칭:** | afad_parser |
| **Kusto 함수 URL:** | https://aka.ms/sentinel-alsidforad-parser |
| **지원 요소** | [Alsid](https://www.alsid.com/contact-us/) |
| | |

### <a name="extra-configuration-for-alsid"></a>Alsid에 대한 추가 구성

1. **Syslog 서버 구성**

    먼저 Alsid for AD가 로그를 보낼 **linux Syslog** 서버가 필요합니다. 일반적으로 **Ubuntu** 에서 **rsyslog** 를 실행할 수 있습니다. 

    그런 다음 이 서버를 원하는 대로 구성할 수 있지만 AFAD 로그를 별도의 파일로 출력할 수 있도록 하는 것이 좋습니다. 또는 [빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/alsid-syslog-proxy/)을 사용하여 Syslog 서버와 Microsoft 에이전트를 배포할 수 있습니다. 템플릿을 사용하는 경우 [에이전트 설치 지침](connect-custom-logs.md#install-the-log-analytics-agent)을 건너뛸 수 있습니다.

1. **Syslog 서버에 로그를 보내도록 Alsid를 구성**

    **AD용 Alsid** 포털에서 **시스템,** **구성**, **Syslog** 로 이동합니다. 이 위치에서 Syslog 서버에 대한 새 Syslog 경고를 만들 수 있습니다.

    새 Syslog 경고를 만든 후에는 로그가 서버에 올바르게 수집되었는지 별도의 파일로 확인합니다. 예를 들어 로그를 확인하려면 AFAD의 Syslog 경고 *구성에서 구성 테스트* 단추를 사용할 수 있습니다. 빠른 시작 템플릿을 사용한 경우 Syslog 서버는 기본적으로 TLS 없이 UDP의 포트 514 및 TCP의 1514에서 수신 대기합니다.

## <a name="amazon-web-services---cloudtrail"></a>Amazon Web Services - CloudTrail

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | **Azure 서비스 간 통합: <br>[AWS CloudTrail을 Azure Sentinel에 연결](connect-aws.md)** (상위 커넥터 문서) |
| **Log Analytics 테이블** | AWSCloudTrail |
| **지원 요소** | Microsoft |
| | |

## <a name="apache-http-server"></a>Apache HTTP 서버

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Log Analytics 에이전트 - 사용자 지정 로그**](connect-custom-logs.md) |
| **Log Analytics 테이블** | ApacheHTTPServer_CL |
| **Kusto 함수 별칭:** | ApacheHTTPServer |
| **Kusto 함수 URL:** | https://aka.ms/sentinel-apachehttpserver-parser |
| **사용자 지정 로그 샘플 파일:** | access.log 또는 error.log |
| | |

## <a name="apache-tomcat"></a>Apache Tomcat

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Log Analytics 에이전트 - 사용자 지정 로그**](connect-custom-logs.md) |
| **Log Analytics 테이블** | Tomcat_CL |
| **Kusto 함수 별칭:** | TomcatEvent |
| **Kusto 함수 URL:** | https://aka.ms/sentinel-ApacheTomcat-parser |
| **사용자 지정 로그 샘플 파일:** | access.log 또는 error.log |
| | |

## <a name="aruba-clearpass-preview"></a>Aruba ClearPass(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Kusto 함수 파서가 있는 **Syslog를 통한 [CEF(Common Event Format)](connect-common-event-format.md)** |
| **Log Analytics 테이블** | CommonSecurityLog |
| **Kusto 함수 별칭:** | ArubaClearPass |
| **Kusto 함수 URL:** | https://aka.ms/sentinel-arubaclearpass-parser |
| **공급업체 문서/<br>설치 지침** | Aruba의 지침에 따라 [ClearPass를 구성](https://www.arubanetworks.com/techdocs/ClearPass/6.7/PolicyManager/Content/CPPM_UserGuide/Admin/syslogExportFilters_add_syslog_filter_general.htm)합니다. |
| **지원 요소** | Microsoft |
| | |

## <a name="atlassian-confluence-audit-preview"></a>Atlassian Confluence Audit(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Functions 및 REST API**](connect-azure-functions-template.md) |
| **Log Analytics 테이블** | Confluence_Audit_CL |
| **Azure 함수 앱 코드** | https://aka.ms/sentinel-confluenceauditapi-functionapp |
| **API 자격 증명** | <li>ConfluenceAccessToken<li>ConfluenceUsername<li>ConfluenceHomeSiteName |
| **공급업체 문서/<br>설치 지침** | <li>[API 문서](https://developer.atlassian.com/cloud/confluence/rest/api-group-audit/)<li>[자격 증명을 얻기 위한 요구 사항 및 지침](https://developer.atlassian.com/cloud/confluence/rest/intro/#auth)<li>[감사 로그 보기](https://support.atlassian.com/confluence-cloud/docs/view-the-audit-log/) |
| **커넥터 배포 지침** | <li>ARM(Azure Resource Manager) 템플릿을 통한 [단일 클릭 배포](connect-azure-functions-template.md?tabs=ARM)<li>[수동 배포](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 함수 별칭** | ConfluenceAudit |
| **Kusto 함수 URL/<br>파서 구성 지침** | https://aka.ms/sentinel-confluenceauditapi-parser |
| **애플리케이션 설정** | <li>ConfluenceUsername<li>ConfluenceAccessToken<li>ConfluenceHomeSiteName<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri(선택 사항) |
| **지원 요소** | Microsoft |
| | |

## <a name="atlassian-jira-audit-preview"></a>Atlassian Jira Audit(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Functions 및 REST API**](connect-azure-functions-template.md) |
| **Log Analytics 테이블** | Jira_Audit_CL |
| **Azure 함수 앱 코드** | https://aka.ms/sentinel-jiraauditapi-functionapp |
| **API 자격 증명** | <li>JiraAccessToken<li>JiraUsername<li>JiraHomeSiteName |
| **공급업체 문서/<br>설치 지침** | <li>[API 문서 - 감사 기록](https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-audit-records/)<li>[자격 증명을 얻기 위한 요구 사항 및 지침](https://developer.atlassian.com/cloud/jira/platform/rest/v3/intro/#authentication) |
| **커넥터 배포 지침** | <li>ARM(Azure Resource Manager) 템플릿을 통한 [단일 클릭 배포](connect-azure-functions-template.md?tabs=ARM)<li>[수동 배포](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 함수 별칭** | JiraAudit |
| **Kusto 함수 URL/<br>파서 구성 지침** | https://aka.ms/sentinel-jiraauditapi-parser |
| **애플리케이션 설정** | <li>JiraUsername<li>JiraAccessToken<li>JiraHomeSiteName<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri(선택 사항) |
| **지원 요소** | Microsoft |
| | |

## <a name="azure-active-directory"></a>Azure Active Directory

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | **Azure 서비스 간 통합: <br>[Azure Sentinel에 Azure Active Directory 데이터 연결](connect-azure-active-directory.md)** (상위 커넥터 문서) |
| **라이선스 필수 조건/<br>비용 정보** | <li>로그인 로그용 Azure Active Directory P1 또는 P2 라이선스<li>기타 로그 유형에 대한 모든 Azure AD 라이선스(무료/O365/P1/P2)<br>다른 요금이 부과될 수 있습니다. |
| **Log Analytics 테이블** | SigninLogs<br>AuditLogs<br>AADNonInteractiveUserSignInLogs<br>AADServicePrincipalSignInLogs<br>AADManagedIdentitySignInLogs<br>AADProvisioningLogs<br>ADFSSignInLogs |
| **지원 요소** | Microsoft |
| | |

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory ID 보호

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | **Azure 서비스 간 통합: <br>[API 기반 연결](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **라이선스 필수 조건/<br>비용 정보** | [Azure AD Premium P2 구독](https://azure.microsoft.com/pricing/details/active-directory/)<br>다른 요금이 부과될 수 있습니다. |
| **Log Analytics 테이블** | SecurityAlert |
| **지원 요소** | Microsoft |
| | |

## <a name="azure-activity"></a>Azure 활동

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | **Azure 서비스 간 통합: <br>[Azure Policy에서 관리하는 진단 설정 기반 연결](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)**<br><br>[새 Azure 활동 커넥터로 업그레이드](#upgrade-to-the-new-azure-activity-connector) |
| **Log Analytics 테이블** | AzureActivity |
| **지원 요소** | Microsoft |
| | |

### <a name="upgrade-to-the-new-azure-activity-connector"></a>새 Azure 활동 커넥터로 업그레이드

#### <a name="data-structure-changes"></a>데이터 구조 변경

이 커넥터는 최근에 활동 로그 이벤트를 수집하기 위한 백 엔드 메커니즘을 변경했습니다. 이제 **진단 설정** 파이프라인을 사용하고 있습니다. 이 커넥터에 기존 방식을 계속 사용하고 있다면 새 버전으로 *업그레이드* 하는 것이 좋습니다. 그러면 리소스 로그에 대한 일관성이 향상되고 더 나은 기능이 제공됩니다. 아래의 지침을 따르세요.

**AzureActivity** 테이블의 [구조가 일부 변경](../azure-monitor/essentials/activity-log.md#data-structure-changes)되었지만 **진단 설정** 메서드는 활동 로그 서비스에서 보낸 기존 메서드와 동일한 데이터를 보냅니다.

다음은 진단 설정 파이프라인으로의 이동으로 인한 몇 가지 주요 개선 사항입니다.
- 수집 대기 시간이 향상되었습니다(발생 후 15~20분이 아닌 2~3분 이내에 이벤트 수집).
- 향상된 안정성.
- 성능 개선.
- 활동 로그 서비스에 의해 로그된 이벤트의 모든 범주에 대한 지원(레거시 메커니즘은 하위 집합만 지원하며 예를 들어 Service Health 이벤트는 지원하지 않음).
- Azure Policy를 통한 대규모 관리.

[Azure 활동 로그](../azure-monitor/essentials/activity-log.md) 및 [진단 설정 파이프라인](../azure-monitor/essentials/diagnostic-settings.md)에 대한 자세한 내용은 [Azure Monitor 문서](../azure-monitor/logs/data-platform-logs.md)를 참조하세요.

#### <a name="disconnect-from-old-pipeline"></a>이전 파이프라인에서 연결 끊기

새 Azure 활동 로그 커넥터를 설정하기 전에 레거시 메서드에서 기존 구독의 연결을 끊어야 합니다.

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다. 커넥터 목록에서 **Azure 활동** 을 선택 하 고 오른쪽 아래에서 **커넥터 페이지 열기** 단추를 선택 합니다.

1. **지침** 탭 아래 **구성** 섹션의 1단계에서 레거시 메서드에 연결된 기존 구독 목록을 검토하여 새 메서드에 추가할 구독을 확인한 다음, 아래의 **모두 연결 끊기** 단추를 클릭하여 한 번에 모두 연결을 끊습니다.

1. [위 표에 링크된 지침](connect-azure-windows-microsoft-services.md#diagnostic-settings-based-connections)에 따라 새 커넥터를 계속 설정합니다.

## <a name="azure-ddos-protection"></a>Azure DDoS Protection

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | **Azure 서비스 간 통합: <br>[진단 설정 기반 연결](connect-azure-windows-microsoft-services.md?tabs=SA#diagnostic-settings-based-connections)** |
| **라이선스 필수 조건/<br>비용 정보** | <li>[Azure DDoS Standard 보호 계획](../ddos-protection/manage-ddos-protection.md#create-a-ddos-protection-plan)을 구성해야 합니다.<li>[Azure DDoS Standard를 사용하도록 설정한 가상 네트워크](../ddos-protection/manage-ddos-protection.md#enable-ddos-protection-for-a-new-virtual-network)가 구성되어 있어야 합니다.<br>다른 요금이 부과될 수 있습니다. |
| **Log Analytics 테이블** | AzureDiagnostics |
| **권장 진단** | DDoSProtectionNotifications<br>DDoSMitigationFlowLogs<br>DDoSMitigationReports |
| **지원 요소** | Microsoft |
| | |

## <a name="azure-defender"></a>Azure Defender

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | **Azure 서비스 간 통합:<br>[Azure Security Center에서 Azure Defender 경고 연결](connect-azure-security-center.md)** (상위 커넥터 문서) |
| **Log Analytics 테이블** | SecurityAlert |
| **지원 요소** | Microsoft |
| | |

## <a name="azure-defender-for-iot"></a>IoT용 Azure Defender

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | **Azure 서비스 간 통합: <br>[API 기반 연결](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **Log Analytics 테이블** | SecurityAlert |
| **지원 요소** | Microsoft |
| | |

## <a name="azure-firewall"></a>Azure Firewall

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | **Azure 서비스 간 통합: <br>[진단 설정 기반 연결](connect-azure-windows-microsoft-services.md?tabs=SA#diagnostic-settings-based-connections)** |
| **Log Analytics 테이블** | AzureDiagnostics |
| **권장 진단** | AzureFirewallApplicationRule<br>AzureFirewallNetworkRule<br>AzureFirewallDnsProxy |
| **지원 요소** | Microsoft |
| | |

## <a name="azure-information-protection"></a>Azure Information Protection

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure 서비스 간 통합**](connect-azure-windows-microsoft-services.md) |
| **Log Analytics 테이블** | InformationProtectionLogs_CL |
| **지원 요소** | Microsoft |
| | |

자세한 내용은 [Azure Information Protection 문서](/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)를 참조하세요.

## <a name="azure-key-vault"></a>Azure Key Vault

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | **Azure 서비스 간 통합: <br>[Azure Policy에서 관리하는 진단 설정 기반 연결](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)** |
| **Log Analytics 테이블** | KeyVaultData |
| **지원 요소** | Microsoft |
| | |

## <a name="azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | **Azure 서비스 간 통합: <br>[Azure Policy에서 관리하는 진단 설정 기반 연결](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)** |
| **Log Analytics 테이블** | kube-apiserver<br>kube-audit<br>kube-audit-admin<br>kube-controller-manager<br>kube-scheduler<br>cluster-autoscaler<br>가드 |
| **지원 요소** | Microsoft |
| | |

## <a name="azure-sql-databases"></a>Azure SQL Databases

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | **Azure 서비스 간 통합: <br>[Azure Policy에서 관리하는 진단 설정 기반 연결](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)** <br><br>[azure SQL 및 SQL PaaS 솔루션에 대 한 azure 센티널](sentinel-solutions-catalog.md#azure) 에서도 사용할 수 있습니다.|
| **Log Analytics 테이블** | SQLSecurityAuditEvents<br>SQLInsights<br>AutomaticTuning<br>QueryStoreWaitStatistics<br>오류<br>DatabaseWaitStatistics<br>시간 제한<br>블록<br>교착 상태<br>Basic<br>InstanceAndAppAdvanced<br>WorkloadManagement<br>DevOpsOperationsAudit |
| **지원 요소** | Microsoft |
| | |

## <a name="azure-storage-account"></a>Azure Storage 계정

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | **Azure 서비스 간 통합: <br>[진단 설정 기반 연결](connect-azure-windows-microsoft-services.md?tabs=SA#diagnostic-settings-based-connections)**<br><br>[스토리지 계정 진단 설정 구성에 대한 참고 사항](#notes-about-storage-account-diagnostic-settings-configuration) |
| **Log Analytics 테이블** | StorageBlobLogs<br>StorageQueueLogs<br>StorageTableLogs<br>StorageFileLogs |
| **권장 진단** | **계정 리소스**<li>트랜잭션<br>**Blob/큐/테이블/파일 리소스**<br><li>StorageRead<li>StorageWrite<li>StorageDelete<li>트랜잭션 |
| **지원 요소** | Microsoft |
| | |

### <a name="notes-about-storage-account-diagnostic-settings-configuration"></a>스토리지 계정 진단 설정 구성에 대한 참고 사항

스토리지 계정(부모) 리소스에는 파일, 테이블, 큐 및 Blob과 같은 각 스토리지 유형에 대한 다른(자식) 리소스가 있습니다.

스토리지 계정에 대한 진단을 구성할 때 다음을 차례로 선택하고 구성해야 합니다.
- 상위 계정 리소스, **트랜잭션** 메트릭 내보내기
- 모든 로그 및 메트릭을 내보내는 각 하위 스토리지 유형 리소스(위 표 참조)

실제로 리소스를 정의한 스토리지 유형만 표시됩니다.

## <a name="azure-web-application-firewall-waf"></a>Azure WAF(웹 애플리케이션 방화벽)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | **Azure 서비스 간 통합: <br>[진단 설정 기반 연결](connect-azure-windows-microsoft-services.md?tabs=SA#diagnostic-settings-based-connections)** |
| **Log Analytics 테이블** | AzureDiagnostics |
| **권장 진단** | **Application Gateway**<br><li>ApplicationGatewayAccessLog<li>ApplicationGatewayFirewallLog<br>**Front Door**<li>FrontdoorAccessLog<li>FrontdoorWebApplicationFirewallLog<br>**CDN WAF 정책**<li>WebApplicationFirewallLogs |
| **지원 요소** | Microsoft |
| | |


## <a name="barracuda-cloudgen-firewall"></a>Barracuda CloudGen Firewall

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**syslog**](connect-syslog.md) |
| **Log Analytics 테이블** | syslog |
| **Kusto 함수 별칭:** | CGFWFirewallActivity |
| **Kusto 함수 URL:** | https://aka.ms/sentinel-barracudacloudfirewall-function |
| **공급업체 문서/<br>설치 지침** | https://aka.ms/sentinel-barracudacloudfirewall-connector |
| **지원 요소** | [Barracuda](https://www.barracuda.com/support) |
| | |

## <a name="barracuda-waf"></a>Barracuda WAF

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Log Analytics 에이전트 - 사용자 지정 로그**](connect-custom-logs.md) |
| **Log Analytics 테이블** | syslog |
| **공급업체 문서/<br>설치 지침** | https://aka.ms/asi-barracuda-connector |
| **지원 요소** | [Barracuda](https://www.barracuda.com/support) |
| | |

## <a name="better-mobile-threat-defense-mtd-preview"></a>BETTER MTD(Mobile Threat Defense)(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Sentinel 데이터 수집기 API**](connect-rest-api-template.md) |
| **Log Analytics 테이블** | BetterMTDDeviceLog_CL<br>BetterMTDIncidentLog_CL<br>BetterMTDAppLog_CL<br>BetterMTDNetflowLog_CL |
| **공급업체 문서/<br>설치 지침** | [BETTER MTD 문서](https://mtd-docs.bmobi.net/integrations/azure-sentinel/setup-integration)<br><br>Azure Sentinel에 보고되는 인시던트를 정의하는 위협 정책 설정:<br><ol><li>**Better MTD Console** 의 사이드바에서 **정책** 을 선택합니다.<li>사용 중인 정책의 **편집** 단추를 선택합니다.<li>로그하려는 각 인시던트 유형에 대해 **통합으로 보내기** 필드로 이동하여 **Sentinel** 을 선택합니다. |
| **지원 요소** | [더 향상된 모바일](mailto:support@better.mobi) |
| | |


## <a name="beyond-security-besecure"></a>Beyond Security beSECURE

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Sentinel 데이터 수집기 API**](connect-rest-api-template.md) |
| **Log Analytics 테이블** | beSECURE_ScanResults_CL<br>beSECURE_ScanEvents_CL<br>beSECURE_Audit_CL |
| **공급업체 문서/<br>설치 지침** | **통합** 메뉴에 액세스:<br><ol><li>**자세히** 메뉴 옵션을 선택합니다.<li>**서버** 를 선택합니다.<li>**통합** 을 선택합니다.<li>Azure Sentinel 사용<li>beSECURE 구성에 **작업 영역 ID** 및 **기본 키** 값을 붙여넣습니다.<li>**수정** 을 선택합니다. |
| **지원 요소** | [Beyond Security](https://beyondsecurity.freshdesk.com/support/home) |
| | |


## <a name="blackberry-cylanceprotect-preview"></a>BlackBerry CylancePROTECT(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**syslog**](connect-syslog.md) |
| **Log Analytics 테이블** | syslog |
| **Kusto 함수 별칭:** | CylancePROTECT |
| **Kusto 함수 URL:** | https://aka.ms/sentinel-cylanceprotect-parser |
| **공급업체 문서/<br>설치 지침** | [Cylance Syslog 가이드](https://docs.blackberry.com/content/dam/docs-blackberry-com/release-pdfs/en/cylance-products/syslog-guides/Cylance%20Syslog%20Guide%20v2.0%20rev12.pdf) |
| **지원 요소** | Microsoft |
| | |

## <a name="broadcom-symantec-data-loss-prevention-dlp-preview"></a>Broadcom Symantec DLP(데이터 손실 방지)(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Kusto 함수 파서가 있는 **Syslog를 통한 [CEF(Common Event Format)](connect-common-event-format.md)** |
| **Log Analytics 테이블** | CommonSecurityLog |
| **Kusto 함수 별칭:** | SymantecDLP |
| **Kusto 함수 URL:** | https://aka.ms/sentinel-symantecdlp-parser |
| **공급업체 문서/<br>설치 지침** | [Syslog 서버 작업에 대한 로그 구성](https://help.symantec.com/cs/DLP15.7/DLP/v27591174_v133697641/Configuring-the-Log-to-a-Syslog-Server-action?locale=EN_US) |
| **지원 요소** | Microsoft |
| | |

## <a name="check-point"></a>Check Point

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Syslog를 통한 **[CEF(Common Event Format)](connect-common-event-format.md)** <br><br>[Check Point 솔루션에서](sentinel-solutions-catalog.md#check-point) 사용 가능|
| **Log Analytics 테이블** | CommonSecurityLog |
| **공급업체 문서/<br>설치 지침** | [로그 내보내기 - Check Point 로그 내보내기](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk122323) |
| **지원 요소** | [Check Point](https://www.checkpoint.com/support-services/contact-support/) |
| | |


## <a name="cisco-asa"></a>Cisco ASA

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Syslog를 통한 **[CEF(Common Event Format)](connect-common-event-format.md)** <br><br>[Cisco ASA 솔루션에서](sentinel-solutions-catalog.md#cisco) 사용 가능|
| **Log Analytics 테이블** | CommonSecurityLog |
| **공급업체 문서/<br>설치 지침** | [Cisco ASA 시리즈 CLI 구성 가이드](https://www.cisco.com/c/en/us/support/docs/security/pix-500-series-security-appliances/63884-config-asa-00.html) |
| **지원 요소** | Microsoft |
| | |


## <a name="cisco-firepower-estreamer-preview"></a>Cisco Firepower eStreamer(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Syslog를 통한 **[CEF(Common Event Format)](connect-common-event-format.md)** <br><br>[Cisco Firepower eStreamer에 대한 추가 구성](#extra-configuration-for-cisco-firepower-estreamer)|
| **Log Analytics 테이블** | CommonSecurityLog |
| **공급업체 문서/<br>설치 지침** | [Sentinel용 eStreamer eNcore 운영 가이드](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html) |
| **지원 요소** | [시스코](https://www.cisco.com/c/en/us/support/index.html)
| | |

### <a name="extra-configuration-for-cisco-firepower-estreamer"></a>Cisco Firepower eStreamer에 대한 추가 구성

1. **Firepower eNcore 클라이언트 설치**  
Firepower eNcore eStreamer 클라이언트를 설치하고 구성합니다. 자세한 내용은 전체 [Cisco 설치 가이드를 참조하세요.](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html)

1. **GitHub에서 Firepower Connector 다운로드**  
[Cisco GitHub 리포지토리](https://github.com/CiscoSecurity/fp-05-microsoft-sentinel-connector)에서 Azure Sentinel용 Firepower eNcore 커넥터의 최신 버전을 다운로드합니다. python3을 사용하려는 경우 [python3 eStreamer 커넥터](https://github.com/CiscoSecurity/fp-05-microsoft-sentinel-connector/tree/python3)를 사용합니다.

1. **Azure/VM IP 주소를 사용하여 pkcs12 파일 만들기**  
**시스템 > 통합 > eStreamer** 아래 Firepower에서 VM 인스턴스의 공용 IP를 사용하여 pkcs12 인증서를 만듭니다. 자세한 내용은 [설치 가이드](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html#_Toc527049443)를 참조하세요.

1. **Azure/VM 클라이언트와 FMC 간의 연결 테스트**  
FMC에서 Azure/VM 인스턴스로 pkcs12 파일을 복사하고 테스트 유틸리티(./encore.sh 테스트)를 실행하여 연결을 설정할 수 있는지 확인합니다. 자세한 내용은 [설치 가이드](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html#_Toc527049430)를 참조하세요.

1. **에이전트에 데이터를 스트림하도록 eNcore 구성**  
TCP를 통해 Log Analytics 에이전트로 데이터를 스트림하도록 eNcore를 구성합니다. 이 구성은 기본적으로 사용하도록 설정해야 하지만 네트워크 보안 태세에 따라 추가 포트 및 스트리밍 프로토콜을 구성할 수 있습니다. 파일 시스템에 데이터를 저장할 수도 있습니다. 자세한 내용은 [백업 구성](https://www.cisco.com/c/en/us/td/docs/security/firepower/670/api/eStreamer_enCore/eStreamereNcoreSentinelOperationsGuide_409.html#_Toc527049433)을 참조하세요.


## <a name="cisco-meraki-preview"></a>Cisco Meraki(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**syslog**](connect-syslog.md)<br><br> [Cisco ISE 솔루션에서](sentinel-solutions-catalog.md#cisco) 사용 가능|
| **Log Analytics 테이블** | syslog |
| **Kusto 함수 별칭:** | CiscoMeraki |
| **Kusto 함수 URL:** | https://aka.ms/sentinel-ciscomeraki-parser |
| **공급업체 문서/<br>설치 지침** | [Meraki 디바이스 보고 문서](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/Meraki_Device_Reporting_-_Syslog%2C_SNMP_and_API) |
| **지원 요소** | Microsoft |
| | |


## <a name="cisco-umbrella-preview"></a>Cisco Umbrella(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Functions 및 REST API**](connect-azure-functions-template.md) <br><br> [Cisco Umbrella 솔루션에서](sentinel-solutions-catalog.md#cisco) 사용 가능|
| **Log Analytics 테이블** | Cisco_Umbrella_dns_CL<br>Cisco_Umbrella_proxy_CL<br>Cisco_Umbrella_ip_CL<br>Cisco_Umbrella_cloudfirewall_CL |
| **Azure 함수 앱 코드** | https://aka.ms/sentinel-CiscoUmbrellaConn-functionapp |
| **API 자격 증명** | <li>AWS 액세스 키 ID<li>AWS 비밀 액세스 키<li>AWS S3 버킷 이름 |
| **공급업체 문서/<br>설치 지침** | <li>[Amazon S3에 로깅](https://docs.umbrella.com/deployment-umbrella/docs/log-management#section-logging-to-amazon-s-3) |
| **커넥터 배포 지침** | <li>ARM(Azure Resource Manager) 템플릿을 통한 [단일 클릭 배포](connect-azure-functions-template.md?tabs=ARM)<li>[수동 배포](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 함수 별칭** | Cisco_Umbrella |
| **Kusto 함수 URL/<br>파서 구성 지침** | https://aka.ms/sentinel-ciscoumbrella-function |
| **애플리케이션 설정** | <li>WorkspaceID<li>WorkspaceKey<li>S3Bucket<li>AWSAccessKeyId<li>AWSSecretAccessKey<li>logAnalyticsUri(선택 사항) |
| **지원 요소** | Microsoft |
| | |

## <a name="cisco-unified-computing-system-ucs-preview"></a>Cisco UCS(Unified Computing System)(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**syslog**](connect-syslog.md) |
| **Log Analytics 테이블** | syslog |
| **Kusto 함수 별칭:** | CiscoUCS |
| **Kusto 함수 URL:** | https://aka.ms/sentinel-ciscoucs-function |
| **공급업체 문서/<br>설치 지침** | [Cisco UCS에 대한 Syslog 설정 - Cisco](https://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110265-setup-syslog-for-ucs.html#configsremotesyslog) |
| **지원 요소** | Microsoft |
| | |


## <a name="citrix-analytics-security"></a>Citrix Analytics(보안)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Sentinel 데이터 수집기 API**](connect-rest-api-template.md) |
| **Log Analytics 테이블** | CitrixAnalytics_SAlerts_CL |
| **공급업체 문서/<br>설치 지침** | [Azure Sentinel에 Citrix 연결](https://aka.ms/Sentinel-Citrix-Connector) |
| **지원 요소** | [Citrix Systems](https://www.citrix.com/support/) |
| | |

## <a name="citrix-web-app-firewall-waf-preview"></a>Citrix WAF(Web App Firewall)(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Syslog를 통한 **[CEF(Common Event Format)](connect-common-event-format.md)** |
| **Log Analytics 테이블** | CommonSecurityLog |
| **공급업체 문서/<br>설치 지침** | WAF를 구성하려면 [WIKI 지원 - NetScaler를 사용한 WAF 구성](https://support.citrix.com/article/CTX234174)을 참조하세요.<br><br>CEF 로그를 구성하려면 [CEF Logging Support in the Application Firewall](https://support.citrix.com/article/CTX136146)을 참조하세요.<br><br>로그를 프록시에 전달하려면 [감사 로깅을 위한 Citrix ADC 어플라이언스 구성](https://docs.citrix.com/en-us/citrix-adc/current-release/system/audit-logging/configuring-audit-logging.html)을 참조하세요. |
| **지원 요소** | [Citrix Systems](https://www.citrix.com/support/) |
| | |


## <a name="cognni-preview"></a>Cognni(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Sentinel 데이터 수집기 API**](connect-rest-api-template.md) |
| **Log Analytics 테이블** | CognniIncidents_CL |
| **공급업체 문서/<br>설치 지침** | **Cognni에 연결**<br><ol><li>[Cognni 통합 페이지](https://intelligence.cognni.ai/integrations)로 이동합니다.<li>Azure Sentinel 상자에서 **연결** 을 선택합니다.<li>**workspaceId** 및 **sharedKey**(기본 키)를 Cognni 통합 화면의 필드에 붙여넣습니다.<li>**연결** 단추를 선택하여 구성을 완료합니다. |
| **지원 요소** | [Cognni](https://cognni.ai/contact-support/)
| | |

## <a name="continuous-threat-monitoring-for-sap-preview"></a>SAP용 지속적인 위협 모니터링(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [SAP 솔루션용 지속적인 위협 모니터링을](sentinel-solutions-catalog.md#sap) 설치한 후에만 사용할 수 있습니다.|
| **Log Analytics 테이블** | [Azure Sentinel SAP 솔루션 로그 참조를 참조하세요.](sap-solution-log-reference.md) |
| **공급업체 문서/<br>설치 지침** | [SAP 연속 위협 모니터링 배포](sap-deploy-solution.md) |
| **지원 요소** | Microsoft |
| | |


## <a name="cyberark-enterprise-password-vault-epv-events-preview"></a>CyberArk EPV(Enterprise Password Vault) Events(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Syslog를 통한 **[CEF(Common Event Format)](connect-common-event-format.md)** |
| **Log Analytics 테이블** | CommonSecurityLog |
| **공급업체 문서/<br>설치 지침** | [SIEM(보안 정보 및 이벤트 관리) 애플리케이션](https://docs.cyberark.com/Product-Doc/OnlineHelp/PAS/Latest/en/Content/PASIMP/DV-Integrating-with-SIEM-Applications.htm) |
| **지원 요소** | [CyberArk](https://www.cyberark.com/customer-support/) |
| | |


## <a name="cyberpion-security-logs-preview"></a>Cyberpion Security Logs(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Sentinel 데이터 수집기 API**](connect-rest-api-template.md) |
| **Log Analytics 테이블** | CyberpionActionItems_CL |
| **공급업체 문서/<br>설치 지침** | [Cyberpion 구독하기](https://azuremarketplace.microsoft.com/en/marketplace/apps/cyberpion1597832716616.cyberpion)<br>[Azure Sentinel에 Cyberpion 보안 경고 통합](https://www.cyberpion.com/resource-center/integrations/azure-sentinel/) |
| **지원 요소** | [Cyberpion](https://www.cyberpion.com/) |
| | |



## <a name="domain-name-server"></a>도메인 이름 서버

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | **Azure 서비스 간 통합: <br>[Log Analytics 에이전트 기반 연결](connect-azure-windows-microsoft-services.md#log-analytics-agent-based-connections)** |
| **Log Analytics 테이블** | DnsEvents<br>DnsInventory |
| **지원 요소** | Microsoft |
| | |

## <a name="dynamics-365"></a>Dynamics 365

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | **Azure 서비스 간 통합: <br>[API 기반 연결](connect-azure-windows-microsoft-services.md#api-based-connections)** <br><br> [Azure 센티널 4 Dynamics 365 솔루션](sentinel-solutions-catalog.md#azure) 의 일부로도 사용할 수 있습니다.|
| **라이선스 필수 조건/<br>비용 정보** | <li>[Microsoft Dynamics 365 프로덕션 라이선스](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description). 샌드박스 환경에서는 사용할 수 없습니다.<li>활동 로깅을 수행하려면 Microsoft 365 Enterprise [E3 또는 E5](/power-platform/admin/enable-use-comprehensive-auditing#requirements) 구독이 필요합니다.<br>다른 요금이 부과될 수 있습니다. |
| **Log Analytics 테이블** | Dynamics365Activity |
| **지원 요소** | Microsoft |
| | |

## <a name="eset-enterprise-inspector-preview"></a>ESET Enterprise Inspector(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Functions 및 REST API**](connect-azure-functions-template.md)<br><br>[API 사용자 만들기](#create-an-api-user) |
| **Log Analytics 테이블** | ESETEnterpriseInspector_CL |
| **API 자격 증명** | <li>EEI 사용자 이름<li>EEI 암호<li>기준 URL |
| **공급업체 문서/<br>설치 지침** | <li>[ESET Enterprise Inspector REST API 문서](https://help.eset.com/eei/1.5/en-US/api.html) |
| **커넥터 배포 지침** | ARM(Azure Resource Manager) 템플릿을 통한 [단일 클릭 배포](connect-azure-functions-template.md?tabs=ARM) |
| **지원 요소** | [ESET](https://support.eset.com/en) |
| | |
### <a name="create-an-api-user"></a>API 사용자 만들기

1. 관리자 계정으로 ESET Security Management Center/ESET PROTECT 콘솔에 로그인하고 **자세히** 탭과 **사용자** 하위 탭을 선택합니다.
1. **새로 추가** 단추를 선택하고 **네이티브 사용자** 를 추가합니다.
1. API 계정에 대한 새 사용자를 만듭니다. **선택 사항:** 수집되는 탐지 항목을 제한하려면 **모두** 이외의 **홈 그룹** 을 선택합니다.
1. **권한 집합** 탭에서 **Enterprise Inspector 검토자** 권한 집합을 할당합니다.
1. 관리자 계정에서 로그 아웃 하 고 유효성 검사를 위한 새 API 자격 증명을 사용 하 여 콘솔에 로그인 한 다음 API 계정에서 로그 아웃 합니다.

## <a name="eset-security-management-center-smc-preview"></a>ESET SMC(Security Management Center)(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**syslog**](connect-syslog.md)<br><br>[수집할 ESET SMC 로그 구성](#configure-the-eset-smc-logs-to-be-collected) <br>[OMS 에이전트가 Eset SMC 데이터를 API 형식으로 전달하도록 구성](#configure-oms-agent-to-pass-eset-smc-data-in-api-format)<br>[태그 oms.api.eset를 catch하고 정형 데이터를 구문 분석하도록 OMS 에이전트 구성 변경](#change-oms-agent-configuration-to-catch-tag-omsapieset-and-parse-structured-data)<br>[자동 구성을 사용하지 않도록 설정하고 에이전트를 다시 시작](#disable-automatic-configuration-and-restart-agent)|
| **Log Analytics 테이블** | eset_CL |
| **공급업체 문서/<br>설치 지침** | [ESET Syslog 서버 문서](https://help.eset.com/esmc_admin/70/en-US/admin_server_settings_syslog.html) |
| **지원 요소** | [ESET](https://support.eset.com/en) |
| | |

### <a name="configure-the-eset-smc-logs-to-be-collected"></a>수집할 ESET SMC 로그 구성

Eset SMC IP 주소의 로그를 수락하도록 rsyslog를 구성합니다.

```bash
    sudo -i
    # Set ESET SMC source IP address
    export ESETIP={Enter your IP address}

    # Create rsyslog configuration file
    cat > /etc/rsyslog.d/80-remote.conf << EOF
    \$ModLoad imudp
    \$UDPServerRun 514
    \$ModLoad imtcp
    \$InputTCPServerRun 514
    \$AllowedSender TCP, 127.0.0.1, $ESETIP
    \$AllowedSender UDP, 127.0.0.1, $ESETIP user.=alert;user.=crit;user.=debug;user.=emerg;user.=err;user.=info;user.=notice;user.=warning  @127.0.0.1:25224
    EOF

    # Restart rsyslog
    systemctl restart rsyslog
```

### <a name="configure-oms-agent-to-pass-eset-smc-data-in-api-format"></a>OMS 에이전트가 Eset SMC 데이터를 API 형식으로 전달하도록 구성

Eset 데이터를 쉽게 인식할 수 있도록 별도의 테이블에 푸시하고 에이전트에서 구문 분석하여 Azure Sentinel 쿼리를 단순화하고 속도를 높입니다. 

**/etc/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/conf/omsagent.conf** 파일에서 유형을 `out_oms_api`로 변경하여 데이터를 API 개체로 보내도록 `match oms.**` 섹션을 수정합니다.
    
다음 코드는 전체 섹션의 예입니다 `match oms.**` .

```bash
    <match oms.** docker.**>
      type out_oms_api
      log_level info
      num_threads 5
      run_in_background false

      omsadmin_conf_path /etc/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/conf/omsadmin.conf
      cert_path /etc/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/certs/oms.crt
      key_path /etc/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/certs/oms.key

      buffer_chunk_limit 15m
      buffer_type file
      buffer_path /var/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/state/out_oms_common*.buffer

      buffer_queue_limit 10
      buffer_queue_full_action drop_oldest_chunk
      flush_interval 20s
      retry_limit 10
      retry_wait 30s
      max_retry_wait 9m
    </match>
```

### <a name="change-oms-agent-configuration-to-catch-tag-omsapieset-and-parse-structured-data"></a>태그 oms.api.eset를 catch하고 정형 데이터를 구문 분석하도록 OMS 에이전트 구성 변경

**/etc/opt/microsoft/omsagent/{REPLACEyourworkspaceid}/conf/omsagent.d/syslog.conf** 파일을 수정합니다. 

예를 들면 다음과 같습니다.

```bash
    <source>
      type syslog
      port 25224
      bind 127.0.0.1
      protocol_type udp
      tag oms.api.eset
    </source>

    <filter oms.api.**>
      @type parser
      key_name message
      format /(?<message>.*?{.*})/
    </filter>

    <filter oms.api.**>
      @type parser
      key_name message
      format json
    </filter>
```
### <a name="disable-automatic-configuration-and-restart-agent"></a>자동 구성을 사용하지 않도록 설정하고 에이전트를 다시 시작

예를 들면 다음과 같습니다. 

```bash
    # Disable changes to configuration files from Portal
    sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'

    # Restart agent
    sudo /opt/microsoft/omsagent/bin/service_control restart

    # Check agent logs
    tail -f /var/opt/microsoft/omsagent/log/omsagent.log
```

### <a name="configure-eset-smc-to-send-logs-to-connector"></a>Eset SMC가 로그를 커넥터로 보내도록 구성

BSD 스타일 및 JSON 형식을 사용하여 Eset 로그를 구성합니다.

- Syslog 서버 구성으로 이동하여 호스트(커넥터), BSD 형식 및 TCP 전송을 구성합니다.
- 로깅 섹션으로 이동하여 JSON을 사용하도록 설정합니다.

자세한 내용은 Eset 설명서를 참조하세요.

## <a name="exabeam-advanced-analytics-preview"></a>Exabeam Advanced Analytics(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**syslog**](connect-syslog.md) |
| **Log Analytics 테이블** | syslog |
| **Kusto 함수 별칭:** | ExabeamEvent |
| **Kusto 함수 URL:** | https://aka.ms/sentinel-Exabeam-parser |
| **공급업체 문서/<br>설치 지침** | [고급 분석 시스템 활동 알림 구성](https://docs.exabeam.com/en/advanced-analytics/i54/advanced-analytics-administration-guide/113254-configure-advanced-analytics.html#UUID-7ce5ff9d-56aa-93f0-65de-c5255b682a08) |
| **지원 요소** | Microsoft |
| | |

## <a name="extrahop-revealx"></a>ExtraHop Reveal(x)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Syslog를 통한 **[CEF(Common Event Format)](connect-common-event-format.md)** |
| **Log Analytics 테이블** | CommonSecurityLog |
| **공급업체 문서/<br>설치 지침** | [ExtraHop Detection SIEM 커넥터](https://aka.ms/asi-syslog-extrahop-forwarding) |
| **지원 요소** | [ExtraHop](https://www.extrahop.com/support/) |
| | |

## <a name="f5-big-ip"></a>F5 BIG-IP

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Sentinel 데이터 수집기 API**](connect-rest-api-template.md) |
| **Log Analytics 테이블** | F5Telemetry_LTM_CL<br>F5Telemetry_system_CL<br>F5Telemetry_ASM_CL |
| **공급업체 문서/<br>설치 지침** | [F5 BIG-IP와 Azure Sentinel 통합](https://aka.ms/F5BigIp-Integrate) |
| **지원 요소** | [F5 Networks](https://support.f5.com/csp/home) |
| | |
## <a name="f5-networks-asm"></a>F5 네트워크(ASM)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Syslog를 통한 **[CEF(Common Event Format)](connect-common-event-format.md)** |
| **Log Analytics 테이블** | CommonSecurityLog |
| **공급업체 문서/<br>설치 지침** | [애플리케이션 보안 이벤트 로깅 구성](https://aka.ms/asi-syslog-f5-forwarding) |
| **지원 요소** | [F5 Networks](https://support.f5.com/csp/home) |
| | |



## <a name="forcepoint-cloud-access-security-broker-casb-preview"></a>Forcepoint CASB(클라우드 액세스 보안 브로커)(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Syslog를 통한 **[CEF(Common Event Format)](connect-common-event-format.md)** |
| **Log Analytics 테이블** | CommonSecurityLog |
| **공급업체 문서/<br>설치 지침** | [Forcepoint CASB 및 Azure Sentinel](https://forcepoint.github.io/docs/casb_and_azure_sentinel/) |
| **지원 요소** | [Forcepoint](https://support.forcepoint.com/) |
| | |

## <a name="forcepoint-cloud-security-gateway-csg-preview"></a>Forcepoint CSG(Cloud Security Gateway)(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Syslog를 통한 **[CEF(Common Event Format)](connect-common-event-format.md)** |
| **Log Analytics 테이블** | CommonSecurityLog |
| **공급업체 문서/<br>설치 지침** | [Forcepoint Cloud Security Gateway 및 Azure Sentinel](https://forcepoint.github.io/docs/csg_and_sentinel/) |
| **지원 요소** | [Forcepoint](https://support.forcepoint.com/) |
| | |

## <a name="forcepoint-data-loss-prevention-dlp-preview"></a>Forcepoint DLP(데이터 손실 방지)(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Sentinel 데이터 수집기 API**](connect-rest-api-template.md) |
| **Log Analytics 테이블** | ForcepointDLPEvents_CL |
| **공급업체 문서/<br>설치 지침** | [Forcepoint 데이터 손실 방지 및 Azure Sentinel](https://forcepoint.github.io/docs/dlp_and_azure_sentinel/) |
| **지원 요소** | [Forcepoint](https://support.forcepoint.com/) |
| | |

## <a name="forcepoint-next-generation-firewall-ngfw-preview"></a>Forcepoint NGFW(Next Generation Firewall)(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Syslog를 통한 **[CEF(Common Event Format)](connect-common-event-format.md)** |
| **Log Analytics 테이블** | CommonSecurityLog |
| **공급업체 문서/<br>설치 지침** | [Forcepoint 차세대 방화벽 및 Azure Sentinel](https://forcepoint.github.io/docs/ngfw_and_azure_sentinel/) |
| **지원 요소** | [Forcepoint](https://support.forcepoint.com/) |
| | |



## <a name="forgerock-common-audit-caud-for-cef-preview"></a>ForgeRock CAUD(Common Audit) for CEF(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Syslog를 통한 **[CEF(Common Event Format)](connect-common-event-format.md)** |
| **Log Analytics 테이블** | CommonSecurityLog |
| **공급업체 문서/<br>설치 지침** | [Azure Sentinel용 ForgeRock CAUD(Common Audit)를 먼저 설치하세요!](https://github.com/javaservlets/SentinelAuditEventHandler) |
| **지원 요소** | [ForgeRock](https://www.forgerock.com/support) |
| | |

## <a name="fortinet"></a>Fortinet

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Syslog를 통한 **[CEF(Common Event Format)](connect-common-event-format.md)** <br><br>[fortinet 로그를](#send-fortinet-logs-to-the-log-forwarder) br <로그 전달기로 보내기<br>[Fortinet Fortigate 솔루션에서](sentinel-solutions-catalog.md#fortinet-fortigate) 사용 가능|
| **Log Analytics 테이블** | CommonSecurityLog |
| **공급업체 문서/<br>설치 지침** | [Fortinet 문서 라이브러리](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary)<br>버전을 선택하고 *핸드북* 및 *로그 메시지 참조* 를 사용합니다. |
| **지원 요소** | [Fortinet](https://support.fortinet.com/) |
| | |

### <a name="send-fortinet-logs-to-the-log-forwarder"></a>로그 전달자에게 Fortigate 로그 보내기

Fortinet 어플라이언스에서 CLI를 열고 다음 명령을 실행합니다.

```Console
config log syslogd setting
set status enable
set format cef
set port 514
set server <ip_address_of_Forwarder>
end
```

- 서버 **IP 주소** 를 로그 전달자의 IP 주소로 바꿉니다.
- **syslog 포트** 를 **514** 또는 전달자의 Syslog 디먼에 설정된 포트로 설정합니다.
- 초기 FortiOS 버전에서 CEF 형식을 사용하도록 설정하려면 명령 집합 **csv disable** 을 실행해야 할 수 있습니다.



## <a name="google-workspace-g-suite-preview"></a>Google Workspace(G-Suite)(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Functions 및 REST API**](connect-azure-functions-template.md)<br><br>[Google Reports API에 대한 추가 구성](#extra-configuration-for-the-google-reports-api) |
| **Log Analytics 테이블** | GWorkspace_ReportsAPI_admin_CL<br>GWorkspace_ReportsAPI_calendar_CL<br>GWorkspace_ReportsAPI_drive_CL<br>GWorkspace_ReportsAPI_login_CL<br>GWorkspace_ReportsAPI_mobile_CL<br>GWorkspace_ReportsAPI_token_CL<br>GWorkspace_ReportsAPI_user_accounts_CL<br> |
| **Azure 함수 앱 코드** | https://aka.ms/sentinel-GWorkspaceReportsAPI-functionapp |
| **API 자격 증명** | <li>GooglePickleString |
| **공급업체 문서/<br>설치 지침** | <li>[API 문서](https://developers.google.com/admin-sdk/reports/v1/reference/activities)<li>[Google Workspace 도메인 전체 권한 위임 수행](https://developers.google.com/admin-sdk/reports/v1/guides/delegation)에서 자격 증명 받기<li>[token.pickle 파일을 pickle 문자열로 변환](https://aka.ms/sentinel-GWorkspaceReportsAPI-functioncode) |
| **커넥터 배포 지침** | <li>ARM(Azure Resource Manager) 템플릿을 통한 [단일 클릭 배포](connect-azure-functions-template.md?tabs=ARM)<li>[수동 배포](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 함수 별칭** | GWorkspaceActivityReports |
| **Kusto 함수 URL/<br>파서 구성 지침** | https://aka.ms/sentinel-GWorkspaceReportsAPI-parser |
| **애플리케이션 설정** | <li>GooglePickleString<li>WorkspaceID<li>workspaceKey<li>logAnalyticsUri(선택 사항) |
| **지원 요소** | Microsoft |
| | |

### <a name="extra-configuration-for-the-google-reports-api"></a>Google Reports API에 대한 추가 구성

[웹 애플리케이션 자격 증명](https://developers.google.com/workspace/guides/create-credentials#web)을 만드는 동안 **권한 있는 리디렉션 URI** 아래에 http://localhost:8081/ 을 추가합니다.

1. [지침에 따라](https://developers.google.com/admin-sdk/reports/v1/quickstart/python) credentials.json을 가져옵니다.
1. Google pickle 문자열을 얻으려면 이 python 스크립트(credentials.json과 동일한 경로)를 실행합니다. [](https://aka.ms/sentinel-GWorkspaceReportsAPI-functioncode)
1. 작은따옴표로 묶인 pickle 문자열 출력을 복사하고 저장합니다. 함수 앱을 배포하는 데 필요합니다.


## <a name="illusive-attack-management-system-ams-preview"></a>Illusive AMS(Attack Management System)(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Syslog를 통한 **[CEF(Common Event Format)](connect-common-event-format.md)** |
| **Log Analytics 테이블** | CommonSecurityLog |
| **공급업체 문서/<br>설치 지침** | [Illusive Networks 관리자 가이드](https://support.illusivenetworks.com/hc/en-us/sections/360002292119-Documentation-by-Version) |
| **지원 요소** | [Illusive Networks](https://www.illusivenetworks.com/technical-support/) |
| | |

## <a name="imperva-waf-gateway-preview"></a>Imperva WAF Gateway(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Syslog를 통한 **[CEF(Common Event Format)](connect-common-event-format.md)** <br><br>[Imperva Cloud WAF 솔루션에서](sentinel-solutions-catalog.md#imperva) 사용 가능|
| **Log Analytics 테이블** | CommonSecurityLog |
| **공급업체 문서/<br>설치 지침** | [Azure Sentinel에 대한 Imperva WAF 게이트웨이 경고 로깅을 사용하도록 설정하는 단계](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert) |
| **지원 요소** | [Imperva](https://www.imperva.com/support/technical-support/) |
| | |


## <a name="infoblox-network-identity-operating-system-nios-preview"></a>Infoblox NIOS(Network Identity Operating System)(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**syslog**](connect-syslog.md)<br><br> [InfoBlox Threat Defense 솔루션에서](sentinel-solutions-catalog.md#infoblox) 사용 가능 |
| **Log Analytics 테이블** | syslog |
| **Kusto 함수 별칭:** | InfobloxNIOS |
| **Kusto 함수 URL:** | https://aka.ms/sentinelgithubparsersinfoblox |
| **공급업체 문서/<br>설치 지침** | [NIOS SNMP 및 Syslog 배포 가이드](https://www.infoblox.com/wp-content/uploads/infoblox-deployment-guide-slog-and-snmp-configuration-for-nios.pdf) |
| **지원 요소** | Microsoft |
| | |




## <a name="juniper-srx-preview"></a>Juniper SRX(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**syslog**](connect-syslog.md) |
| **Log Analytics 테이블** | syslog |
| **Kusto 함수 별칭:** | JuniperSRX |
| **Kusto 함수 URL:** | https://aka.ms/sentinel-junipersrx-parser |
| **공급업체 문서/<br>설치 지침** | [SRX 분기 디바이스에 대한 트래픽 로깅(보안 정책 로그) 구성](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA)<br>[시스템 로깅 구성](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502) |
| **지원 요소** | [Juniper Networks](https://support.juniper.net/support/) |
| | |

## <a name="lookout-mobile-threat-defense-preview"></a>Lookout Mobile Threat Defense(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Functions 및 REST API**](connect-azure-functions-template.md) <br><br>[lookout Mobile Threat Defense for Azure Sentinel](sentinel-solutions-catalog.md#lookout) 솔루션을 설치한 후에만 사용할 수 있습니다. |
| **Log Analytics 테이블** | Lookout_CL |
| **API 자격 증명** | <li>Lookout 애플리케이션 키 |
| **공급업체 문서/<br>설치 지침** | <li>[설치 가이드(로그인](https://esupport.lookout.com/s/article/Lookout-with-Azure-Sentinel) 필요)<li>[API 설명서(로그인](https://esupport.lookout.com/s/article/Mobile-Risk-API-Guide) 필요)<li>[Lookout 모바일 엔드포인트 보안](https://www.lookout.com/products/mobile-endpoint-security) |
| **지원 요소** | [Lookout](https://www.lookout.com/support) |
| | |



## <a name="microsoft-365-defender"></a>Microsoft 365 Defender

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | **Azure 서비스 간 통합:<br>[Microsoft 365 Defender에서 Azure Sentinel로 데이터 연결](connect-microsoft-365-defender.md)** (상위 커넥터 문서) |
| **라이선스 필수 조건/<br>비용 정보** | [Microsoft 365 Defender의 유효한 라이선스](/microsoft-365/security/mtp/prerequisites)
| **Log Analytics 테이블** | **경고:**<br>SecurityAlert<br>SecurityIncident<br>**엔드포인트용 Defender 이벤트:**<br>DeviceEvents<br>DeviceFileEvents<br>DeviceImageLoadEvents<br>DeviceInfo<br>DeviceLogonEvents<br>DeviceNetworkEvents<br>DeviceNetworkInfo<br>DeviceProcessEvents<br>DeviceRegistryEvents<br>DeviceFileCertificateInfo<br>**defender for Office 365 이벤트:**<br>EmailAttachmentInfo<br>EmailUrlInfo<br>EmailEvents<br>EmailPostDeliveryEvents |
| **지원 요소** | Microsoft |
| | |

## <a name="microsoft-365-insider-risk-management-irm-preview"></a>Microsoft 365 IRM(Insider Risk Management)(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | **Azure 서비스 간 통합: <br>[API 기반 연결](connect-azure-windows-microsoft-services.md#api-based-connections)**<br><br>Microsoft 365 Insider [Risk Management 솔루션에서도](sentinel-solutions-catalog.md#domain-solutions) 사용할 수 있습니다. |
| **라이선스 및 기타 필수 조건** | <ul><li>Microsoft 365 E5/A5/G5 또는 함께 제공되는 준수 또는 IRM 추가 기능의 유효한 구독입니다.<li>[Microsoft 365 참가자 위험 관리가](/microsoft-365/compliance/insider-risk-management) 완전히 온보딩되고 [IRM 정책이](/microsoft-365/compliance/insider-risk-management-policies) 정의되고 경고가 생성됩니다.<li>Microsoft 365 Azure Sentinel 커넥터를 통해 경고를 수신하기 위해 IRM 경고를 Office 365 관리 활동 API로 내보낼 수 있도록 [구성된 IRM입니다.)](/microsoft-365/compliance/insider-risk-management-settings#export-alerts-preview)
| **Log Analytics 테이블** | SecurityAlert |
| **데이터 쿼리 필터** | `SecurityAlert`<br>`\| where ProductName == "Microsoft 365 Insider Risk Management"` |
| **지원 요소** | Microsoft |
| | |

## <a name="microsoft-cloud-app-security-mcas"></a>MCAS(Microsoft Cloud App Security)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | **Azure 서비스 간 통합: <br>[API 기반 연결](connect-azure-windows-microsoft-services.md#api-based-connections)**<br><br>Cloud Discovery 로그의 경우 [Microsoft Cloud App Security에서 Azure Sentinel을 SIEM으로 사용 설정](/cloud-app-security/siem-sentinel) |
| **Log Analytics 테이블** | SecurityAlert - 경고용<br>McasShadowItReporting - Cloud Discovery 로그용 |
| **지원 요소** | Microsoft |
| | |

## <a name="microsoft-defender-for-endpoint"></a>엔드포인트에 대한 Microsoft Defender

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | **Azure 서비스 간 통합: <br>[API 기반 연결](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **라이선스 필수 조건/<br>비용 정보** | [Endpoint용 Microsoft Defender 배포를 위한 유효한 라이선스](/microsoft-365/security/defender-endpoint/production-deployment)
| **Log Analytics 테이블** | SecurityAlert |
| **지원 요소** | Microsoft |
| | |

## <a name="microsoft-defender-for-identity"></a>Microsoft Defender for Identity

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | **Azure 서비스 간 통합: <br>[API 기반 연결](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **Log Analytics 테이블** | SecurityAlert |
| **지원 요소** | Microsoft |
| | |

## <a name="microsoft-defender-for-office-365"></a>Office 365용 Microsoft Defender

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | **Azure 서비스 간 통합: <br>[API 기반 연결](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **라이선스 필수 조건/<br>비용 정보** | [Office 365 ATP 플랜 2](/microsoft-365/security/office-365-security/office-365-atp#office-365-atp-plan-1-and-plan-2)에 대한 유효한 라이선스가 있어야 합니다.
| **Log Analytics 테이블** | SecurityAlert |
| **지원 요소** | Microsoft |
| | |

## <a name="microsoft-office-365"></a>Microsoft Office 365

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | **Azure 서비스 간 통합: <br>[API 기반 연결](connect-azure-windows-microsoft-services.md#api-based-connections)** |
| **라이선스 필수 조건/<br>비용 정보** | Office 365 배포는 Azure Sentinel 작업 영역과 동일한 테넌트에 있어야 합니다.<br>다른 요금이 부과될 수 있습니다. |
| **Log Analytics 테이블** | OfficeActivity |
| **지원 요소** | Microsoft |
| | |



## <a name="morphisec-utpp-preview"></a>Morphisec UTPP(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Kusto 함수 파서가 있는 **Syslog를 통한 [CEF(Common Event Format)](connect-common-event-format.md)** |
| **Log Analytics 테이블** | CommonSecurityLog |
| **Kusto 함수 별칭:** | Morphisec |
| **Kusto 함수 URL** | https://aka.ms/sentinel-Morphiescutpp-parser |
| **지원 요소** | [Morphisec](https://support.morphisec.com/support/home) |
| | |


## <a name="netskope-preview"></a>Netskope(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Functions 및 REST API**](connect-azure-functions-template.md) |
| **Log Analytics 테이블** | Netskope_CL |
| **Azure 함수 앱 코드** | https://aka.ms/sentinel-netskope-functioncode |
| **API 자격 증명** | <li>Netskope API 토큰 |
| **공급업체 문서/<br>설치 지침** | <li>[Netskop 클라우드 보안 플랫폼](https://www.netskope.com/platform)<li>[Netskop API 문서](https://innovatechcloud.goskope.com/docs/Netskope_Help/en/rest-api-v1-overview.html)<li>[API 토큰 가져오기](https://innovatechcloud.goskope.com/docs/Netskope_Help/en/rest-api-v2-overview.html) |
| **커넥터 배포 지침** | <li>ARM(Azure Resource Manager) 템플릿을 통한 [단일 클릭 배포](connect-azure-functions-template.md?tabs=ARM)<li>[수동 배포](connect-azure-functions-template.md?tabs=MPS) |
| **Kusto 함수 별칭** | Netskope |
| **Kusto 함수 URL/<br>파서 구성 지침** | https://aka.ms/sentinel-netskope-parser |
| **애플리케이션 설정** | <li>apikey<li>workspaceID<li>workspaceKey<li>uri(지역에 따라 다름, `https://<Tenant Name>.goskope.com` 스키마를 따름) <li>timeInterval(5로 설정)<li>logTypes<li>logAnalyticsUri(선택 사항) |
| **지원 요소** | Microsoft |
| | |

## <a name="nginx-http-server-preview"></a>NGINX HTTP Server(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Log Analytics 에이전트 - 사용자 지정 로그**](connect-custom-logs.md) |
| **Log Analytics 테이블** | NGINX_CL |
| **Kusto 함수 별칭:** | NGINXHTTPServer |
| **Kusto 함수 URL** | https://aka.ms/sentinel-NGINXHTTP-parser |
| **공급업체 문서/<br>설치 지침** | [모듈 ngx_http_log_module](https://nginx.org/en/docs/http/ngx_http_log_module.html) |
| **사용자 지정 로그 샘플 파일:** | access.log 또는 error.log |
| **지원 요소** | Microsoft |
| | |

## <a name="nxlog-basic-security-module-bsm-macos-preview"></a>NXLog BSM(Basic Security Module) macOS(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Sentinel 데이터 수집기 API**](connect-rest-api-template.md) |
| **Log Analytics 테이블** | BSMmacOS_CL |
| **공급업체 문서/<br>설치 지침** | [NXLog Azure Sentinel 사용자 가이드](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) |
| **지원 요소** | [NXLog](https://nxlog.co/community-forum) |
| | |


## <a name="nxlog-dns-logs-preview"></a>NXLog DNS Logs(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Sentinel 데이터 수집기 API**](connect-rest-api-template.md) |
| **Log Analytics 테이블** | DNS_Logs_CL |
| **공급업체 문서/<br>설치 지침** | [NXLog Azure Sentinel 사용자 가이드](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) |
| **지원 요소** | [NXLog](https://nxlog.co/community-forum) |
| | |


## <a name="nxlog-linuxaudit-preview"></a>NXLog LinuxAudit(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Sentinel 데이터 수집기 API**](connect-rest-api-template.md) |
| **Log Analytics 테이블** | LinuxAudit_CL |
| **공급업체 문서/<br>설치 지침** |  [NXLog Azure Sentinel 사용자 가이드](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) |
| **지원 요소** | [NXLog](https://nxlog.co/community-forum) |
| | |


## <a name="okta-single-sign-on-preview"></a>Okta Single Sign-On(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Functions 및 REST API**](connect-azure-functions-template.md) |
| **Log Analytics 테이블** | Okta_CL |
| **Azure 함수 앱 코드** | https://aka.ms/sentineloktaazurefunctioncodev2 |
| **API 자격 증명** | <li>API 토큰 |
| **공급업체 문서/<br>설치 지침** | <li>[Okta 시스템 로그 API 문서](https://developer.okta.com/docs/reference/api/system-log/)<li>[API 토큰 만들기](https://developer.okta.com/docs/guides/create-an-api-token/create-the-token/)<li>[Azure Sentinel에 Okta SSO 연결](#okta-single-sign-on-preview) |
| **커넥터 배포 지침** | <li>ARM(Azure Resource Manager) 템플릿을 통한 [단일 클릭 배포](connect-azure-functions-template.md?tabs=ARM)<li>[수동 배포](connect-azure-functions-template.md?tabs=MPS) |
| **애플리케이션 설정** | <li>apiToken<li>workspaceID<li>workspaceKey<li>uri(`https://<OktaDomain>/api/v1/logs?since=` 스키마를 따릅니다. [도메인 네임스페이스를 식별합니다](https://developer.okta.com/docs/reference/api-overview/#url-namespace).) <li>logAnalyticsUri(선택 사항) |
| **지원 요소** | Microsoft |
| | |


## <a name="onapsis-platform-preview"></a>Onapsis Platform(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Kusto 조회 및 보강 기능이 있는 **Syslog를 통한 [CEF(Common Event Format)](connect-common-event-format.md)**<br><br>[CEF 로그를 로그 전달자로 보내도록 Onapsis 구성](#configure-onapsis-to-send-cef-logs-to-the-log-forwarder) |
| **Log Analytics 테이블** | CommonSecurityLog |
| **Kusto 함수 별칭:** | incident_lookup |
| **Kusto 함수 URL** | https://aka.ms/sentinel-Onapsis-parser |
| **지원 요소** | [Onapsis](https://onapsis.force.com/s/login/) |
| | |

### <a name="configure-onapsis-to-send-cef-logs-to-the-log-forwarder"></a>CEF 로그를 로그 전달자로 보내도록 Onapsis 구성

Log Analytics 에이전트에 대한 로그 전달을 설정하려면 Onapsis 제품 내 도움말을 참조하세요.

1. **설정 > 타사 통합 > 경보 방어** 로 이동하여 Azure Sentinel에 대한 지침을 따릅니다.
1. Onapsis 콘솔이 에이전트가 설치된 로그 전달자 컴퓨터에 연결할 수 있는지 확인합니다. 로그는 TCP를 사용하여 포트 514로 보내야 합니다.

## <a name="one-identity-safeguard-preview"></a>One Identity Safeguard(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Syslog를 통한 **[CEF(Common Event Format)](connect-common-event-format.md)** |
| **Log Analytics 테이블** | CommonSecurityLog |
| **공급업체 문서/<br>설치 지침** | [권한 있는 세션을 위한 One Identity Safeguard 관리 가이드](https://aka.ms/sentinel-cef-oneidentity-forwarding) |
| **지원 요소** | [One Identity](https://support.oneidentity.com/) |
| | |



## <a name="oracle-weblogic-server-preview"></a>Oracle WebLogic Server(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Log Analytics 에이전트 - 사용자 지정 로그**](connect-custom-logs.md) |
| **Log Analytics 테이블** | OracleWebLogicServer_CL |
| **Kusto 함수 별칭:** | OracleWebLogicServerEvent |
| **Kusto 함수 URL:** | https://aka.ms/sentinel-OracleWebLogicServer-parser |
| **공급업체 문서/<br>설치 지침** | [Oracle WebLogic 서버 문서](https://docs.oracle.com/en/middleware/standalone/weblogic-server/14.1.1.0/index.html) |
| **사용자 지정 로그 샘플 파일:** | server.log |
| **지원 요소** | Microsoft |
| | |

## <a name="orca-security-preview"></a>Orca Security(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Sentinel 데이터 수집기 API**](connect-rest-api-template.md) |
| **Log Analytics 테이블** | OrcaAlerts_CL |
| **공급업체 문서/<br>설치 지침** | [Azure Sentinel 통합](https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-configuration) |
| **지원 요소** | [Orca Security](http://support.orca.security/) |
| | |


## <a name="ossec-preview"></a>OSSEC(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Kusto 함수 파서가 있는 **Syslog를 통한 [CEF(Common Event Format)](connect-common-event-format.md)** |
| **Log Analytics 테이블** | CommonSecurityLog |
| **Kusto 함수 별칭:** | OSSECEvent |
| **Kusto 함수 URL:** | https://aka.ms/sentinel-OSSEC-parser |
| **공급업체 문서/<br>설치 지침** | [OSSEC 문서](https://www.ossec.net/docs/)<br>[syslog를 통해 경고 보내기](https://www.ossec.net/docs/docs/manual/output/syslog-output.html) |
| **지원 요소** | Microsoft |
| | |


## <a name="palo-alto-networks"></a>Palo Alto Networks

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Syslog를 통한 **[CEF(Common Event Format)](connect-common-event-format.md)** <br><br>[Palo Alto PAN-OS 및 Prisma 솔루션에서도](sentinel-solutions-catalog.md#palo-alto) 사용할 수 있습니다.|
| **Log Analytics 테이블** | CommonSecurityLog |
| **공급업체 문서/<br>설치 지침** | [CEF(Common Event Format) 구성 가이드](https://aka.ms/asi-syslog-paloalto-forwarding)<br>[Syslog 모니터링 구성](https://aka.ms/asi-syslog-paloalto-configure) |
| **지원 요소** | [Palo Alto Networks](https://www.paloaltonetworks.com/company/contact-support) |
| | |


## <a name="perimeter-81-activity-logs-preview"></a>Perimeter 81 Activity Logs(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Sentinel 데이터 수집기 API**](connect-rest-api-template.md) |
| **Log Analytics 테이블** | Perimeter81_CL |
| **공급업체 문서/<br>설치 지침** | [Perimeter 81 문서](https://support.perimeter81.com/docs/360012680780) |
| **지원 요소** | [Perimeter 81](https://support.perimeter81.com/) |
| | |



## <a name="proofpoint-on-demand-pod-email-security-preview"></a>POD(Proofpoint On Demand) Email Security(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Functions 및 REST API**](connect-azure-functions-template.md) <br><br>[Proofpoint POD 솔루션에서도](sentinel-solutions-catalog.md#proofpoint) 사용할 수 있습니다. |
| **Log Analytics 테이블** | ProofpointPOD_message_CL<br>ProofpointPOD_maillog_CL |
| **Azure 함수 앱 코드** | https://aka.ms/sentinel-proofpointpod-functionapp |
| **API 자격 증명** | <li>ProofpointClusterID<li>ProofpointToken |
| **공급업체 문서/<br>설치 지침** | <li>[Proofpoint 커뮤니티에 로그인](https://proofpointcommunities.force.com/community/s/article/How-to-request-a-Community-account-and-gain-full-customer-access?utm_source=login&utm_medium=recommended&utm_campaign=public)<li>[Proofpoint API 문서 및 지침](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API) |
| **커넥터 배포 지침** | <li>ARM(Azure Resource Manager) 템플릿을 통한 [단일 클릭 배포](connect-azure-functions-template.md?tabs=ARM)<li>[수동 배포](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 함수 별칭** | ProofpointPOD |
| **Kusto 함수 URL/<br>파서 구성 지침** | https://aka.ms/sentinel-proofpointpod-parser |
| **애플리케이션 설정** | <li>ProofpointClusterID<li>ProofpointToken<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri(선택 사항) |
| **지원 요소** | Microsoft |
| | |

## <a name="proofpoint-targeted-attack-protection-tap-preview"></a>Proofpoint TAP(Targeted Attack Protection)(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Functions 및 REST API**](connect-azure-functions-template.md) <br><br>[Proofpoint TAP 솔루션에서도](sentinel-solutions-catalog.md#proofpoint) 사용할 수 있습니다. |
| **Log Analytics 테이블** | ProofPointTAPClicksPermitted_CL<br>ProofPointTAPClicksBlocked_CL<br>ProofPointTAPMessagesDelivered_CL<br>ProofPointTAPMessagesBlocked_CL |
| **Azure 함수 앱 코드** | https://aka.ms/sentinelproofpointtapazurefunctioncode |
| **API 자격 증명** | <li>API 사용자 이름<li>API 암호 |
| **공급업체 문서/<br>설치 지침** | <li>[Proofpoint SIEM API 문서](https://help.proofpoint.com/Threat_Insight_Dashboard/API_Documentation/SIEM_API) |
| **커넥터 배포 지침** | <li>ARM(Azure Resource Manager) 템플릿을 통한 [단일 클릭 배포](connect-azure-functions-template.md?tabs=ARM)<li>[수동 배포](connect-azure-functions-template.md?tabs=MPS) |
| **애플리케이션 설정** | <li>apiUsername<li>apiUsername<li>URI(`https://tap-api-v2.proofpoint.com/v2/siem/all?format=json&sinceSeconds=300`으로 설정)<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri(선택 사항) |
| **지원 요소** | Microsoft |
| | |

## <a name="pulse-connect-secure-preview"></a>Pulse Connect Secure(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**syslog**](connect-syslog.md) |
| **Log Analytics 테이블** | syslog |
| **Kusto 함수 별칭:** | PulseConnectSecure |
| **Kusto 함수 URL:** | https://aka.ms/sentinelgithubparserspulsesecurevpn |
| **공급업체 문서/<br>설치 지침** | [Syslog 구성](https://docs.pulsesecure.net/WebHelp/Content/PCS/PCS_AdminGuide_8.2/Configuring%20Syslog.htm) |
| **지원 요소** | Microsoft |
| | |


## <a name="qualys-vm-knowledgebase-kb-preview"></a>Qualys VM KB(기술 자료)(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Functions 및 REST API**](connect-azure-functions-template.md)<br><br>[Qualys VM KB에 대한 추가 구성](#extra-configuration-for-the-qualys-vm-kb) <br><br>[Qualys VM 솔루션에서도](sentinel-solutions-catalog.md#qualys) 사용할 수 있습니다.|
| **Log Analytics 테이블** | QualysKB_CL |
| **Azure 함수 앱 코드** | https://aka.ms/sentinel-qualyskb-functioncode |
| **API 자격 증명** | <li>API 사용자 이름<li>API 암호 |
| **공급업체 문서/<br>설치 지침** | <li>[QualysVM API 사용자 가이드](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf) |
| **커넥터 배포 지침** | <li>ARM(Azure Resource Manager) 템플릿을 통한 [단일 클릭 배포](connect-azure-functions-template.md?tabs=ARM)<li>[수동 배포](connect-azure-functions-template.md?tabs=MPS) |
| **Kusto 함수 별칭** | QualysKB |
| **Kusto 함수 URL/<br>파서 구성 지침** | https://aka.ms/sentinel-qualyskb-parser |
| **애플리케이션 설정** | <li>apiUsername<li>apiUsername<li>uri(지역별, [API 서버 목록](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf#G4.735348) 참조). `https://<API Server>/api/2.0` 스키마를 따릅니다.<li>WorkspaceID<li>WorkspaceKey<li>filterParameters(URI 끝에 추가, `&`로 구분). 공백 없음)<li>logAnalyticsUri(선택 사항) |
| **지원 요소** | Microsoft |
| | |

### <a name="extra-configuration-for-the-qualys-vm-kb"></a>Qualys VM KB에 대한 추가 구성

1. 관리자 계정으로 Qualys 취약성 관리 콘솔에 로그인하고 **사용자** 탭과 **사용자** 하위 탭을 선택합니다.
1. **새로 만들기** 드롭다운 메뉴를 선택하고 **사용자** 를 선택합니다.
1. API 계정의 사용자 이름과 암호를 만듭니다.
1. **사용자 역할** 탭에서 계정 역할이 **관리자** 로 설정되어 있고 **GUI** 및 **API** 에 대한 액세스가 허용되어 있는지 확인합니다.
1. 관리자 계정에서 로그아웃하고 유효성 검사를 위해 새 API 자격 증명을 사용하여 콘솔에 로그인한 다음, API 계정에서 로그아웃합니다.
1. 관리자 계정을 사용하여 콘솔에 다시 로그인하고 API 계정 사용자 역할을 수정하여 **GUI** 에 대한 액세스 권한을 제거합니다.
1. 모든 변경 내용을 저장합니다.

## <a name="qualys-vulnerability-management-vm-preview"></a>Qualys VM(취약성 관리)(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Functions 및 REST API**](connect-azure-functions-template.md)<br><br>[Qualys VM의 추가 구성](#extra-configuration-for-the-qualys-vm) <br>[수동 배포 - 함수 앱 구성 후](#manual-deployment---after-configuring-the-function-app)|
| **Log Analytics 테이블** | QualysHostDetection_CL |
| **Azure 함수 앱 코드** | https://aka.ms/sentinelqualysvmazurefunctioncode |
| **API 자격 증명** | <li>API 사용자 이름<li>API 암호 |
| **공급업체 문서/<br>설치 지침** | <li>[QualysVM API 사용자 가이드](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf) |
| **커넥터 배포 지침** | <li>ARM(Azure Resource Manager) 템플릿을 통한 [단일 클릭 배포](connect-azure-functions-template.md?tabs=ARM)<li>[수동 배포](connect-azure-functions-template.md?tabs=MPS) |
| **애플리케이션 설정** | <li>apiUsername<li>apiUsername<li>uri(지역별, [API 서버 목록](https://www.qualys.com/docs/qualys-api-vmpc-user-guide.pdf#G4.735348) 참조). `https://<API Server>/api/2.0/fo/asset/host/vm/detection/?action=list&vm_processed_after=` 스키마를 따릅니다.<li>WorkspaceID<li>WorkspaceKey<li>filterParameters(URI 끝에 추가, `&`로 구분). 공백 없음)<li>timeInterval(5로 설정됩니다. 수정하면 그에 따라 Function App 타이머 트리거를 변경합니다.)<li>logAnalyticsUri(선택 사항) |
| **지원 요소** | Microsoft |
| | |

### <a name="extra-configuration-for-the-qualys-vm"></a>Qualys VM에 대한 추가 구성

1. 관리자 계정으로 Qualys 취약성 관리 콘솔에 로그인하고 **사용자** 탭과 **사용자** 하위 탭을 선택합니다.
1. **새로 만들기** 드롭다운 메뉴를 선택하고 **사용자** 를 선택합니다.
1. API 계정의 사용자 이름과 암호를 만듭니다.
1. **사용자 역할** 탭에서 계정 역할이 **관리자** 로 설정되어 있고 **GUI** 및 **API** 에 대한 액세스가 허용되어 있는지 확인합니다.
1. 관리자 계정에서 로그아웃하고 유효성 검사를 위해 새 API 자격 증명을 사용하여 콘솔에 로그인한 다음, API 계정에서 로그아웃합니다.
1. 관리자 계정을 사용하여 콘솔에 다시 로그인하고 API 계정 사용자 역할을 수정하여 **GUI** 에 대한 액세스 권한을 제거합니다.
1. 모든 변경 내용을 저장합니다.

### <a name="manual-deployment---after-configuring-the-function-app"></a>수동 배포 - 함수 앱 구성 후

**host.json 파일 구성**

잠재적으로 많은 양의 Qualys 호스트 검색 데이터가 수집되기 때문에 실행 시간이 기본 함수 앱 시간 제한인 5분을 초과할 수 있습니다. 함수 앱이 실행되는 데 더 많은 시간을 허용하려면 기본 시간 제한 기간을 소비 계획에서 최대 10분으로 늘려야 합니다.

1. 함수 앱에서 함수 앱 이름을 선택하고 **App Service 편집기** 페이지를 선택합니다.
1. **이동** 을 선택하여 편집기를 연 다음 **wwwroot** 디렉터리에서 **host.json** 파일을 선택합니다.
1. `managedDependancy` 줄 위에 `"functionTimeout": "00:10:00",` 줄을 추가합니다.
1. 편집기의 오른쪽 위 모서리에 **SAVED가** 표시되는지 확인하고 편집기를 종료합니다.

더 긴 시간 초과 기간이 필요한 경우 [App Service 계획](../azure-functions/functions-scale.md)으로 업그레이드하는 것이 좋습니다.



## <a name="salesforce-service-cloud-preview"></a>Salesforce Service Cloud(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Functions 및 REST API**](connect-azure-functions-template.md) |
| **Log Analytics 테이블** | SalesforceServiceCloud_CL |
| **Azure 함수 앱 코드** | https://aka.ms/sentinel-SalesforceServiceCloud-functionapp |
| **API 자격 증명** | <li>Salesforce API 사용자 이름<li>Salesforce API 암호<li>Salesforce 보안 토큰<li>Salesforce 소비자 키<li>Salesforce 소비자 비밀 |
| **공급업체 문서/<br>설치 지침** | [Salesforce REST API 개발자 가이드](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm)<br>**권한 부여 설정** 에서 OAuth 대신 **세션 ID** 메서드를 사용합니다. |
| **커넥터 배포 지침** | <li>ARM(Azure Resource Manager) 템플릿을 통한 [단일 클릭 배포](connect-azure-functions-template.md?tabs=ARM)<li>[수동 배포](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 함수 별칭** | SalesforceServiceCloud |
| **Kusto 함수 URL/<br>파서 구성 지침** | https://aka.ms/sentinel-SalesforceServiceCloud-parser |
| **애플리케이션 설정** | <li>SalesforceUser<li>SalesforcePass<li>SalesforceSecurityToken<li>SalesforceConsumerKey<li>SalesforceConsumerSecret<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri(선택 사항) |
| **지원 요소** | Microsoft |
| | |


## <a name="security-events-via-legacy-agent-windows"></a>레거시 에이전트를 통한 보안 이벤트(Windows)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | **Azure 서비스 간 통합: <br>[Windows 서버에 연결하여 보안 이벤트 수집](connect-windows-security-events.md)** (상위 커넥터 문서) |
| **Log Analytics 테이블** | SecurityEvents |
| **지원 요소** | Microsoft |
| | |

자세한 내용은 [안전하지 않은 프로토콜 통합 문서 설정](./get-visibility.md#use-built-in-workbooks)을 참조하세요.

참고: [**AMA(Azure Monitor**](#windows-security-events-via-ama) Agent)를 기반으로 AMA 커넥터를 통해 이벤트 Windows 보안

## <a name="sentinelone-preview"></a>SentinelOne(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Functions 및 REST API**](connect-azure-functions-template.md) <br><br>[SentinelOne에 대한 추가 구성](#extra-configuration-for-sentinelone)|
| **Log Analytics 테이블** | SentinelOne_CL |
| **Azure 함수 앱 코드** | https://aka.ms/sentinel-SentinelOneAPI-functionapp |
| **API 자격 증명** | <li>SentinelOneAPIToken<li>SentinelOneUrl(`https://<SOneInstanceDomain>.sentinelone.net`) |
| **공급업체 문서/<br>설치 지침** | <li>https://`<SOneInstanceDomain>`.sentinelone.net/api-doc/overview<li>아래 지침을 참조하세요. |
| **커넥터 배포 지침** | <li>ARM(Azure Resource Manager) 템플릿을 통한 [단일 클릭 배포](connect-azure-functions-template.md?tabs=ARM)<li>[수동 배포](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 함수 별칭** | SentinelOne |
| **Kusto 함수 URL/<br>파서 구성 지침** | https://aka.ms/sentinel-SentinelOneAPI-parser |
| **애플리케이션 설정** | <li>SentinelOneAPIToken<li>SentinelOneUrl<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri(선택 사항) |
| **지원 요소** | Microsoft |
| | |

### <a name="extra-configuration-for-sentinelone"></a>SentinelOne에 대한 추가 구성

지침에 따라 자격 증명을 가져옵니다.

1. 관리자 사용자 자격 증명을 사용하여 SentinelOne 관리 콘솔에 로그인합니다.
1. 관리 콘솔에서 **설정** 을 선택합니다.
1. **설정** 보기에서 **사용자** 를 선택합니다.
1. **새 사용자** 를 선택합니다.
1. 새 콘솔 사용자에 대한 정보를 입력합니다.
1. 역할에서 **관리자** 를 선택합니다.
1. **저장** 을 선택합니다.
1. 데이터 커넥터에서 사용할 새 사용자의 자격 증명을 저장합니다.



## <a name="sonicwall-firewall-preview"></a>SonicWall Firewall(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Syslog를 통한 **[CEF(Common Event Format)](connect-common-event-format.md)** |
| **Log Analytics 테이블** | CommonSecurityLog |
| **공급업체 문서/<br>설치 지침** | [로그 > Syslog](http://help.sonicwall.com/help/sw/eng/7020/26/2/3/content/Log_Syslog.120.2.htm)<br>Syslog 형식으로 시설 local4 및 ArcSight를 선택합니다.  |
| **지원 요소** | [SonicWall](https://www.sonicwall.com/support/) |
| | | 


## <a name="sophos-cloud-optix-preview"></a>Sophos Cloud Optix(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Sentinel 데이터 수집기 API**](connect-rest-api-template.md) |
| **Log Analytics 테이블** | SophosCloudOptix_CL |
| **공급업체 문서/<br>설치 지침** | 첫 번째 단계를 건너뛰고 [Azure Sentinel과 통합](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/tasks/IntegrateAzureSentinel.html)합니다.<br>[Sophos 쿼리 샘플](https://docs.sophos.com/pcg/optix/help/en-us/pcg/optix/concepts/ExampleAzureSentinelQueries.html) |
| **지원 요소** | [Sophos](https://secure2.sophos.com/en-us/support.aspx) |
| | |




## <a name="sophos-xg-firewall-preview"></a>Sophos XG Firewall(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**syslog**](connect-syslog.md) |
| **Log Analytics 테이블** | syslog |
| **Kusto 함수 별칭:** | SophosXGFirewall |
| **Kusto 함수 URL:** | https://aka.ms/sentinelgithubparserssophosfirewallxg |
| **공급업체 문서/<br>설치 지침** | [Syslog 서버 추가](https://docs.sophos.com/nsg/sophos-firewall/18.5/Help/en-us/webhelp/onlinehelp/nsg/tasks/SyslogServerAdd.html) |
| **지원 요소** | Microsoft |
| | |

## <a name="squadra-technologies-secrmm"></a>Squadra 기술 secRMM

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Sentinel 데이터 수집기 API**](connect-rest-api-template.md) |
| **Log Analytics 테이블** | secRMM_CL |
| **공급업체 문서/<br>설치 지침** | [secRMM Azure Sentinel 관리자 가이드](https://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) |
| **지원 요소** | [Squadra Technologies](https://www.squadratechnologies.com/Contact.aspx) |
| | |


## <a name="squid-proxy-preview"></a>Squid Proxy(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Log Analytics 에이전트 - 사용자 지정 로그**](connect-custom-logs.md) |
| **Log Analytics 테이블** | SquidProxy_CL |
| **Kusto 함수 별칭:** | SquidProxy |
| **Kusto 함수 URL** | https://aka.ms/sentinel-squidproxy-parser |
| **사용자 지정 로그 샘플 파일:** | access.log 또는 cache.log |
| **지원 요소** | Microsoft |
| | |

## <a name="symantec-integrated-cyber-defense-exchange-icdx"></a>Symantec ICDx(Integrated Cyber Defense Exchange)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Sentinel 데이터 수집기 API**](connect-rest-api-template.md) |
| **Log Analytics 테이블** | SymantecICDx_CL |
| **공급업체 문서/<br>설치 지침** | [Microsoft Azure Sentinel(Log Analytics) 전달자 구성](https://techdocs.broadcom.com/us/en/symantec-security-software/integrated-cyber-defense/integrated-cyber-defense-exchange/1-4-3/Forwarders/configuring-forwarders-v131944722-d2707e17438.html) |
| **지원 요소** | [Broadcom Symantec](https://support.broadcom.com/security) |
| | |


## <a name="symantec-proxysg-preview"></a>Symantec ProxySG(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**syslog**](connect-syslog.md) |
| **Log Analytics 테이블** | syslog |
| **Kusto 함수 별칭:** | SymantecProxySG |
| **Kusto 함수 URL:** | https://aka.ms/sentinelgithubparserssymantecproxysg |
| **공급업체 문서/<br>설치 지침** | [Syslog 서버에 액세스 로그 보내기](https://knowledge.broadcom.com/external/article/166529/sending-access-logs-to-a-syslog-server.html) |
| **지원 요소** | Microsoft |
| | |


## <a name="symantec-vip-preview"></a>Symantec VIP(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**syslog**](connect-syslog.md) |
| **Log Analytics 테이블** | syslog |
| **Kusto 함수 별칭:** | SymantecVIP |
| **Kusto 함수 URL:** | https://aka.ms/sentinelgithubparserssymantecvip |
| **공급업체 문서/<br>설치 지침** | [syslog 구성](https://help.symantec.com/cs/VIP_EG_INSTALL_CONFIG/VIP/v134652108_v128483142/Configuring-syslog?locale=EN_US) |
| **지원 요소** | Microsoft |
| | |



## <a name="thycotic-secret-server-preview"></a>Thycotic Secret Server(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Syslog를 통한 **[CEF(Common Event Format)](connect-common-event-format.md)** |
| **Log Analytics 테이블** | CommonSecurityLog |
| **공급업체 문서/<br>설치 지침** | [보안 Syslog/CEF 로깅](https://thy.center/ss/link/syslog) |
| **지원 요소** | [Thycotic](https://thycotic.force.com/support/s/) |
| | |


## <a name="trend-micro-deep-security"></a>Trend Micro Deep Security

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Kusto 함수 파서가 있는 **Syslog를 통한 [CEF(Common Event Format)](connect-common-event-format.md)** |
| **Log Analytics 테이블** | CommonSecurityLog |
| **Kusto 함수 별칭:** | TrendMicroDeepSecurity |
| **Kusto 함수 URL** | https://aka.ms/TrendMicroDeepSecurityFunction |
| **공급업체 문서/<br>설치 지침** | [Syslog 또는 SIEM 서버로 Deep Security 이벤트 전달](https://aka.ms/Sentinel-trendMicro-connectorInstructions) |
| **지원 요소** | [Trend Micro](https://success.trendmicro.com/technical-support) |
| | |

## <a name="trend-micro-tippingpoint-preview"></a>Trend Micro TippingPoint(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Kusto 함수 파서가 있는 **Syslog를 통한 [CEF(Common Event Format)](connect-common-event-format.md)** |
| **Log Analytics 테이블** | CommonSecurityLog |
| **Kusto 함수 별칭:** | TrendMicroTippingPoint |
| **Kusto 함수 URL** | https://aka.ms/sentinel-trendmicrotippingpoint-function |
| **공급업체 문서/<br>설치 지침** | ArcSight CEF 형식 v4.2 형식으로 Syslog 메시지를 보냅니다. |
| **지원 요소** | [Trend Micro](https://success.trendmicro.com/technical-support) |
| | |

## <a name="trend-micro-vision-one-xdr-preview"></a>Trend Micro Vision One(XDR)(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Functions 및 REST API**](connect-azure-functions-template.md) |
| **Log Analytics 테이블** | TrendMicro_XDR_CL |
| **API 자격 증명** | <li>API 토큰 |
| **공급업체 문서/<br>설치 지침** | <li>[Trend Micro Vision One API](https://automation.trendmicro.com/xdr/home)<li>[타사 액세스를 위한 API 키 얻기](https://docs.trendmicro.com/en-us/enterprise/trend-micro-xdr-help/ObtainingAPIKeys) |
| **커넥터 배포 지침** | ARM(Azure Resource Manager) 템플릿을 통한 [단일 클릭 배포](connect-azure-functions-template.md?tabs=ARM) |
| **지원 요소** | [Trend Micro](https://success.trendmicro.com/technical-support) |
| | |



## <a name="vmware-carbon-black-endpoint-standard-preview"></a>VMware Carbon Black Endpoint Standard(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Functions 및 REST API**](connect-azure-functions-template.md) |
| **Log Analytics 테이블** | CarbonBlackEvents_CL<br>CarbonBlackAuditLogs_CL<br>CarbonBlackNotifications_CL |
| **Azure 함수 앱 코드** | https://aka.ms/sentinelcarbonblackazurefunctioncode |
| **API 자격 증명** | **API 액세스 수준**(*감사* 및 *이벤트* 로그용):<li>API ID<li>API 키<br><br>**SIEM 액세스 수준**(*알림* 이벤트용):<li>SIEM API ID<li>SIEM API 키 |
| **공급업체 문서/<br>설치 지침** | <li>[Carbon Black API 문서](https://developer.carbonblack.com/reference/carbon-black-cloud/cb-defense/latest/rest-api/)<li>[API 키 만들기](https://developer.carbonblack.com/reference/carbon-black-cloud/authentication/#creating-an-api-key) |
| **커넥터 배포 지침** | <li>ARM(Azure Resource Manager) 템플릿을 통한 [단일 클릭 배포](connect-azure-functions-template.md?tabs=ARM)<li>[수동 배포](connect-azure-functions-template.md?tabs=MPS) |
| **애플리케이션 설정** | <li>apiId<li>apiKey<li>WorkspaceID<li>WorkspaceKey<li>uri(지역별, [옵션 목록 참조](https://community.carbonblack.com/t5/Knowledge-Base/PSC-What-URLs-are-used-to-access-the-APIs/ta-p/67346), `https://<API URL>.conferdeploy.net` 스키마를 따릅니다.)<li>timeInterval(5로 설정)<li>SIEMapiId(*알림* 이벤트를 수집하는 경우)<li>SIEMapiKey(*알림* 이벤트를 수집하는 경우)<li>logAnalyticsUri(선택 사항) |
| **지원 요소** | Microsoft |
| | |

## <a name="vmware-esxi-preview"></a>VMware ESXi(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**syslog**](connect-syslog.md) |
| **Log Analytics 테이블** | syslog |
| **Kusto 함수 별칭:** | VMwareESXi |
| **Kusto 함수 URL:** | https://aka.ms/sentinel-vmwareesxi-parser |
| **공급업체 문서/<br>설치 지침** | [ESXi 3.5 및 4.x에서 syslog 사용](https://kb.vmware.com/s/article/1016621)<br>[ESXi 호스트에서 Syslog 구성](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html) |
| **지원 요소** | Microsoft |
| | |

## <a name="watchguard-firebox-preview"></a>WatchGuard Firebox(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**syslog**](connect-syslog.md) |
| **Log Analytics 테이블** | syslog |
| **Kusto 함수 별칭:** | WatchGuardFirebox |
| **Kusto 함수 URL:** | https://aka.ms/sentinel-watchguardfirebox-parser |
| **공급업체 문서/<br>설치 지침** | [Microsoft Azure Sentinel 통합 가이드](https://www.watchguard.com/help/docs/help-center/en-US/Content/Integration-Guides/General/Microsoft%20Azure%20Sentinel.html) |
| **지원 요소** | [WatchGuard Technologies](https://www.watchguard.com/wgrd-support/overview) |
| | |

## <a name="wirex-network-forensics-platform-preview"></a>WireX Network Forensics Platform(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Syslog를 통한 **[CEF(Common Event Format)](connect-common-event-format.md)** |
| **Log Analytics 테이블** | CommonSecurityLog |
| **공급업체 문서/<br>설치 지침** | CEF 형식으로 Syslog 메시지를 보내도록 NFP 솔루션을 구성하려면 [WireX 지원](https://wirexsystems.com/contact-us/)에 문의하세요. |
| **지원 요소** | [WireX Systems](mailto:support@wirexsystems.com) |
| | |


## <a name="windows-firewall"></a>Windows 방화벽

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | **Azure 서비스 간 통합: <br>[Log Analytics 에이전트 기반 연결](connect-azure-windows-microsoft-services.md#log-analytics-agent-based-connections)** |
| **Log Analytics 테이블** | WindowsFirewall |
| **지원 요소** | Microsoft |
| | |

## <a name="windows-security-events-via-ama"></a>AMA를 통해 보안 이벤트 Windows

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | **Azure 서비스 간 통합: <br>[Windows 서버에 연결하여 보안 이벤트 수집](connect-windows-security-events.md?tabs=AMA)** (상위 커넥터 문서) |
| **Log Analytics 테이블** | SecurityEvents |
| **지원 요소** | Microsoft |
| | |

참고 항목: [**레거시 에이전트 커넥터를 통한 보안 이벤트**](#security-events-via-legacy-agent-windows)

## <a name="workplace-from-facebook-preview"></a>Workplace from Facebook(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Functions 및 REST API**](connect-azure-functions-template.md)<br><br>[웹후크 구성](#configure-webhooks) <br>[웹후크 구성에 콜백 URL 추가](#add-callback-url-to-webhook-configuration)|
| **Log Analytics 테이블** | Workplace_Facebook_CL |
| **Azure 함수 앱 코드** | https://aka.ms/sentinel-WorkplaceFacebook-functionapp |
| **API 자격 증명** | <li>WorkplaceAppSecret<li>WorkplaceVerifyToken |
| **공급업체 문서/<br>설치 지침** | <li>[웹후크 구성](https://developers.facebook.com/docs/workplace/reference/webhooks)<li>[권한 구성](https://developers.facebook.com/docs/workplace/reference/permissions) |
| **커넥터 배포 지침** | <li>ARM(Azure Resource Manager) 템플릿을 통한 [단일 클릭 배포](connect-azure-functions-template.md?tabs=ARM)<li>[수동 배포](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 함수 별칭** | Workplace_Facebook |
| **Kusto 함수 URL/<br>파서 구성 지침** | https://aka.ms/sentinel-WorkplaceFacebook-parser |
| **애플리케이션 설정** | <li>WorkplaceAppSecret<li>WorkplaceVerifyToken<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri(선택 사항) |
| **지원 요소** | Microsoft |
| | |

### <a name="configure-webhooks"></a>웹후크 구성

1. 관리자 사용자 자격 증명을 사용 하 여 작업 공간에 로그인 합니다.
1. 관리자 패널에서 **통합** 을 선택합니다.
1. **모든 통합** 보기에서 **사용자 지정 통합 만들기** 를 선택합니다.
1. 이름과 설명을 입력하고 **만들기** 를 선택합니다.
1. **통합 세부 정보** 패널에서 **앱 비밀** 을 표시하고 복사합니다.
1. **통합 권한** 패널에서 모든 읽기 권한을 설정합니다. 자세한 내용은 [권한 페이지](https://developers.facebook.com/docs/workplace/reference/permissions)를 참조하세요.

### <a name="add-callback-url-to-webhook-configuration"></a>웹후크 구성에 콜백 URL 추가

1. 함수 앱의 페이지를 열고 **함수** 목록으로 이동한 다음 **함수 URL 가져오기** 를 선택 하 여 복사 합니다.
1. **Workplace from Facebook** 으로 돌아갑니다. **웹후크 구성** 패널의 각 탭에서 **콜백 URL** 을 마지막 단계에서 복사한 함수 URL로 설정하고 **인증 토큰** 을 자동 배포 중에 받았거나 수동 배포 중에 입력한 것과 동일한 값으로 설정합니다.
1. **저장** 을 선택합니다.



## <a name="zimperium-mobile-thread-defense-preview"></a>Zimperium Mobile Thread Defense(미리 보기)

Zimperium Mobile Threat Defense 데이터 커넥터는 Zimperium 위협 로그를 Azure Sentinel에 연결하여 대시보드를 확인하고, 사용자 지정 경고를 만들고, 조사를 개선합니다. 이 커넥터는 조직의 모바일 위협 환경에 대한 자세한 인사이트를 제공하고 보안 작업 기능을 향상시킵니다.

자세한 내용은 [커넥트 Zimperium to Azure 센티널을](#zimperium-mobile-thread-defense-preview)참조 하세요.

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Sentinel 데이터 수집기 API**](connect-rest-api-template.md)<br><br>[Zimperium MTD 구성 및 연결](#configure-and-connect-zimperium-mtd) |
| **Log Analytics 테이블** | ZimperiumThreatLog_CL<br>ZimperiumMitigationLog_CL |
| **공급업체 문서/<br>설치 지침** | [Zimperium 고객 지원 포털](https://support.zimperium.com/) (로그인 필요) |
| **지원 요소** | [Zimperium](https://www.zimperium.com/support) |
| | |

### <a name="configure-and-connect-zimperium-mtd"></a>Zimperium MTD 구성 및 연결

1. zConsole의 탐색 모음에서 **관리** 를 선택합니다.
1. **통합** 탭을 선택합니다.
1. **위협 보고** 단추를 클릭한 다음, **통합 추가** 단추를 선택합니다.
1. 통합 만들기:
    1. 사용 가능한 통합에서 **Microsoft Azure Sentinel** 을 선택합니다.
    1. *작업 영역 ID* 와 *기본 키* 를 입력하고 **다음** 을 선택합니다.
    1. Azure Sentinel 통합의 이름을 입력합니다.
    1. Azure Sentinel에 푸시할 위협 데이터에 대해 **필터 수준** 을 선택합니다.
    1. **완료** 를 선택합니다.

## <a name="zoom-reports-preview"></a>Zoom Reports(미리 보기)

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Azure Functions 및 REST API**](connect-azure-functions-template.md) |
| **Log Analytics 테이블** | Zoom_CL |
| **Azure 함수 앱 코드** | https://aka.ms/sentinel-ZoomAPI-functionapp |
| **API 자격 증명** | <li>ZoomApiKey<li>ZoomApiSecret |
| **공급업체 문서/<br>설치 지침** | <li>[Zoom에서 JWT를 사용하여 자격 증명 가져오기](https://marketplace.zoom.us/docs/guides/auth/jwt) |
| **커넥터 배포 지침** | <li>ARM(Azure Resource Manager) 템플릿을 통한 [단일 클릭 배포](connect-azure-functions-template.md?tabs=ARM)<li>[수동 배포](connect-azure-functions-template.md?tabs=MPY) |
| **Kusto 함수 별칭** | Zoom |
| **Kusto 함수 URL/<br>파서 구성 지침** | https://aka.ms/sentinel-ZoomAPI-parser |
| **애플리케이션 설정** | <li>ZoomApiKey<li>ZoomApiSecret<li>WorkspaceID<li>WorkspaceKey<li>logAnalyticsUri(선택 사항) |
| **지원 요소** | Microsoft |
| | |

## <a name="zscaler"></a>Zscaler

| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | Syslog를 통한 **[CEF(Common Event Format)](connect-common-event-format.md)** |
| **Log Analytics 테이블** | CommonSecurityLog |
| **공급업체 문서/<br>설치 지침** | [Zscaler 및 Microsoft Azure Sentinel 배포 가이드](https://aka.ms/ZscalerCEFInstructions) |
| **지원 요소** | [Zscaler](https://help.zscaler.com/submit-ticket-links) |
| | |


## <a name="zscaler-private-access-zpa-preview"></a>ZPA(Zscaler Private Access)(미리 보기)


| 커넥터 특성 | Description |
| --- | --- |
| **데이터 수집 방법** | [**Log Analytics 에이전트 - 사용자 지정 로그**](connect-custom-logs.md)<br><br>[Zscaler Private Access를 위한 추가 구성](#extra-configuration-for-zscaler-private-access) |
| **Log Analytics 테이블** | ZPA_CL |
| **Kusto 함수 별칭:** | ZPAEvent |
| **Kusto 함수 URL** | https://aka.ms/sentinel-zscalerprivateaccess-parser |
| **공급업체 문서/<br>설치 지침** | [Zscaler Private Access 문서](https://help.zscaler.com/zpa)<br>또한 아래를 참조하세요. |
| **지원 요소** | Microsoft |
| | |

### <a name="extra-configuration-for-zscaler-private-access"></a>Zscaler Private Access에 대한 추가 구성

Azure Sentinel에 Zscaler Private Access 로그를 가져오려면 아래 구성 단계를 따르세요. 자세한 내용은 Azure Monitor [설명서를 참조하세요.](../azure-monitor/agents/data-sources-json.md) Zscaler Private Access 로그는 LSS(로그 스트리밍 서비스)를 통해 전달됩니다. 자세한 내용은 [LSS 문서](https://help.zscaler.com/zpa/about-log-streaming-service)를 참조하세요.

1. [로그 수신기](https://help.zscaler.com/zpa/configuring-log-receiver)를 구성합니다. 로그 수신기를 구성하는 동안 **JSON** 을 **로그 템플릿** 으로 선택합니다.
1. 구성 파일 [zpa.conf](https://aka.ms/sentinel-zscalerprivateaccess-conf)를 다운로드합니다.

    ```bash
    wget -v https://aka.ms/sentinel-zscalerprivateaccess-conf -O zpa.conf
    ```

1. Azure Log Analytics 에이전트를 설치한 서버에 로그인합니다.
1. zpa.conf를 /etc/opt/microsoft/omsagent/`workspace_id`/conf/omsagent.d/ 폴더에 복사합니다.
1. 다음과 같이 zpa.conf를 편집합니다.
    1. 로그를 전달하도록 Zscaler 로그 수신기를 설정한 포트를 지정합니다(줄 4).
    1. `workspace_id`을 작업 영역 ID의 실제 값으로 바꿉니다(14,15,16,19행).
1. 다음 명령을 사용하여 변경 사항을 저장하고 Linux용 Azure Log Analytics 에이전트 서비스를 다시 시작합니다.

    ```bash
    sudo /opt/microsoft/omsagent/bin/service_control restart
    ```
ZScaler Private Access 커넥터 페이지 또는 Log Analytics 작업 영역의 에이전트 관리 페이지에서 작업 영역 ID 값을 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [Azure Sentinel 솔루션 카탈로그](sentinel-solutions-catalog.md)
- [Azure Sentinel에서 위협 인텔리전스 통합](threat-intelligence-integration.md)
