---
title: Microsoft 센티널 사용자 지정 커넥터를 만들기 위한 리소스 | Microsoft Docs
description: Microsoft 센티널 용 사용자 지정 커넥터를 만드는 데 사용할 수 있는 리소스에 대해 알아봅니다. 방법에는 Log Analytics 에이전트와 API, Logstash, Logic Apps, PowerShell 및 Azure Functions가 포함됩니다.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.openlocfilehash: 256743af821f1dc3cdb83d71246cf31ed2b9a7cc
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132721616"
---
# <a name="resources-for-creating-microsoft-sentinel-custom-connectors"></a>Microsoft 센티널 사용자 지정 커넥터를 만들기 위한 리소스

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft 센티널은 [Azure 서비스 및 외부 솔루션에 대 한 다양 한 기본 제공 커넥터](connect-data-sources.md)를 제공 하며, 전용 커넥터가 없는 일부 원본에서의 데이터 수집 지원 합니다.

사용 가능한 기존 솔루션 중 하나를 사용 하 여 데이터 원본을 Microsoft 센티널에 연결할 수 없는 경우 고유한 데이터 원본 커넥터를 만드는 것이 좋습니다.

지원 되는 커넥터의 전체 목록은 [Microsoft 센티널: 커넥터 그랜드 (CEF, Syslog, Direct, 에이전트, 사용자 지정 등)](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-the-connectors-grand-cef-syslog-direct-agent/ba-p/803891) 블로그 게시물을 참조 하세요.

## <a name="compare-custom-connector-methods"></a>사용자 지정 커넥터 방법 비교

다음 표에서는 이 문서에서 설명하는 사용자 지정 커넥터를 만들기 위한 각 방법에 대한 필수 정보를 비교합니다. 각 방법에 대한 자세한 내용을 보려면 표에서 링크를 선택합니다.

|방법 설명  |기능 | 서버를 사용하지 않음    |복잡성  |
|---------|---------|---------|---------|
|**[Log Analytics 에이전트](#connect-with-the-log-analytics-agent)** <br>온-프레미스 및 IaaS 원본에서 파일을 수집하는 데 가장 적합   | 파일 컬렉션만 해당  |   예      |낮음         |
|**[Logstash](#connect-with-logstash)** <br>온-프레미스 및 IaaS 원본, 플러그 인을 사용할 수 있는 원본 및 이미 Logstash에 익숙한 조직에 가장 적합합니다.  | 사용 가능한 플러그 인 및 사용자 지정 플러그 인, 기능은 상당한 유연성을 제공합니다.   |   아니요, VM 또는 VM 클러스터를 실행해야 합니다.           |   낮음, 플러그 인을 사용하는 많은 시나리오 지원      |
|**[Logic Apps](#connect-with-logic-apps)** <br>높은 비용, 대용량 데이터의 경우 방지 <br>볼륨이 낮은 클라우드 원본에 가장 적합  | 코드리스 프로그래밍을 사용하면 알고리즘 구현에 대한 지원 없이 유연성을 제한할 수 있습니다.<br><br> 사용 가능한 작업에서 요구 사항을 지원하지 않는 경우 사용자 지정 작업을 만들면 복잡성이 추가될 수 있습니다.    |    예         |   낮음, 간단한 코드리스 개발      |
|**[PowerShell](#connect-with-powershell)** <br>프로토타입 및 정기 파일 업로드에 가장 적합 | 파일 컬렉션에 대한 직접 지원. <br><br>PowerShell을 사용하여 더 많은 원본을 수집할 수 있지만 스크립트를 서비스로 코딩하고 구성해야 합니다.      |예               |  낮음       |
|**[Log Analytics API](#connect-with-the-log-analytics-api)** <br>ISV의 통합 구현 및 고유한 컬렉션 요구 사항에 가장 적합   | 코드에서 사용할 수 있는 모든 기능을 지원합니다.  | 구현에 따라 달라집니다.           |     높음    |
|**[Azure Functions](#connect-with-azure-functions)** 대용량 클라우드 원본 및 고유한 컬렉션 요구 사항에 가장 적합  | 코드에서 사용할 수 있는 모든 기능을 지원합니다.  |  예             |     높음, 프로그래밍 지식 필요    |
|     |         |                |

> [!TIP]
> 동일한 커넥터에 대해 Logic Apps 및 Azure Functions를 사용한 비교는 다음을 참조하세요.
>
> - [Microsoft 센티널로 Fastly 웹 응용 프로그램 방화벽 로그 수집](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-fastly-web-application-firewall-logs-into-azure-sentinel/ba-p/1238804)
> - Office 365 (Microsoft 센티널 GitHub community): [논리 앱 커넥터](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Get-O365Data)  |  [Azure 함수 커넥터](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data)
>

## <a name="connect-with-the-log-analytics-agent"></a>Log Analytics 에이전트로 연결

데이터 원본이 파일에 이벤트를 전달하는 경우 Azure Monitor Log Analytics 에이전트를 사용하여 사용자 지정 커넥터를 만드는 것이 좋습니다.

- 자세한 내용은 [Azure Monitor에서 사용자 지정 로그 수집](../azure-monitor/agents/data-sources-custom-logs.md)을 참조하세요.

- 이 방법의 예제는 [Azure Monitor에서 Linux용 Log Analytics 에이전트를 사용하여 사용자 지정 JSON 데이터 원본 수집](../azure-monitor/agents/data-sources-json.md)을 참조하세요.

## <a name="connect-with-logstash"></a>Logstash로 연결

[Logstash 태](https://www.elastic.co/logstash)를 잘 알고 있는 경우 [Microsoft 센티널 용 logstash 태 시 출력 플러그 인에서](connect-logstash.md) logstash를 사용 하 여 사용자 지정 커넥터를 만들 수 있습니다.

Microsoft 센티널 Logstash 출력 플러그 인을 사용 하면 Logstash 태 트 입력 및 필터링 플러그 인을 사용 하 고 Logstash 태 파이프라인에 대 한 출력으로 Microsoft 센티널을 구성할 수 있습니다. Logstash에는 Event Hubs, Apache Kafka, 파일, 데이터베이스 및 클라우드 서비스와 같은 다양한 원본에서 입력을 사용할 수 있도록 하는 많은 플러그 인 라이브러리가 있습니다. 필터링 플러그 인을 사용하여 이벤트를 구문 분석하고, 불필요한 이벤트를 필터링하고, 값을 난독 처리합니다.

Logstash를 사용자 지정 커넥터로 사용하는 예제는 다음을 참조하세요.

- Microsoft 센티널 (블로그)을 [사용 하 여 AWS 로그의 자본 1 위반 TTPs에 대 한 구하기](https://techcommunity.microsoft.com/t5/azure-sentinel/hunting-for-capital-one-breach-ttps-in-aws-logs-using-azure/ba-p/1019767)
- [Microsoft 센티널 구현 가이드 Radware](https://support.radware.com/ci/okcsFattach/get/1025459_3)

유용한 Logstash 플러그 인의 예제는 다음을 참조하세요.

- [Cloudwatch 입력 플러그 인](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-cloudwatch.html)
- [Azure Event Hubs 플러그 인](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-azure_event_hubs.html)
- [Google Cloud Storage 입력 플러그 인](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_cloud_storage.html)
- [Google_pubsub 입력 플러그 인](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_pubsub.html)

> [!TIP]
> Logstash는 클러스터를 사용하여 크기가 조정된 데이터 수집도 가능합니다. 자세한 내용은 [대규모로 부하가 분산된 Logstash VM 사용](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854)을 참조하세요.
>

## <a name="connect-with-logic-apps"></a>Logic Apps로 연결

[Azure Logic Apps](../logic-apps/index.yml) 를 사용 하 여 Microsoft 센티널 용 서버를 사용 하지 않는 사용자 지정 커넥터를 만듭니다.

> [!NOTE]
> Logic Apps를 사용하여 서버리스 커넥터를 만드는 것이 편리할 수 있지만, 커넥터에 대해 Logic Apps를 사용하면 대용량 데이터에 대해 비용이 많이 들 수 있습니다
>
> 이 방법은 볼륨이 낮은 데이터 원본이나 데이터 업로드 보강에만 사용하는 것이 좋습니다.
>

1. **다음 트리거 중 하나를 사용하여 Logic Apps를 시작합니다.**

    |트리거  |Description  |
    |---------|---------|
    |**되풀이 작업**     |   예를 들어 특정 파일, 데이터베이스 또는 외부 API에서 정기적으로 데이터를 검색하도록 논리 앱을 예약합니다. <br>자세히 알아보려면 [Azure Logic Apps에서 되풀이 작업 및 워크플로를 만들고 예약하고 실행하는 방법](../connectors/connectors-native-recurrence.md)을 참조하세요.      |
    |**주문형 트리거**     | 수동 데이터 수집 및 테스트를 위해 요청 시 논리 앱을 실행합니다. <br>자세한 내용은 [HTTPS 엔드포인트를 사용한 논리 앱 호출, 트리거 또는 중첩](../logic-apps/logic-apps-http-endpoint.md)을 참조하세요.        |
    |**HTTP/S 엔드포인트**     |  원본 시스템에서 데이터 전송을 시작할 수 있는 경우 스트리밍에 권장됩니다. <br>자세한 내용은 [HTTP 또는 HTTPS를 통한 서비스 엔드포인트 호출](../connectors/connectors-native-http.md)을 참조하세요.       |
    |     |         |

1. **정보를 읽는 논리 앱 커넥터를 사용하여 이벤트를 가져옵니다**. 예를 들면 다음과 같습니다.

    - [REST API에 연결](/connectors/custom-connectors/)
    - [SQL Server에 연결](/connectors/sql/)
    - [파일 시스템에 연결](/connectors/filesystem/)

    > [!TIP]
    > REST API, SQL Server 및 파일 시스템에 대한 사용자 지정 커넥터는 온-프레미스 데이터 원본에서 데이터 검색도 지원합니다. 자세한 내용은 [온-프레미스 데이터 게이트웨이 설치](/connectors/filesystem/) 문서를 참조하세요.
    >

1. **검색하려는 정보를 준비** 합니다.

    예를 들어 [JSON 구문 분석 작업](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action)을 사용하여 JSON 콘텐츠에서 속성에 액세스하면 논리 앱에 대한 입력을 지정할 때 동적 콘텐츠 목록에서 해당 속성을 선택할 수 있습니다.

    자세한 내용은 [Azure Logic Apps에서 데이터 작업 수행](../logic-apps/logic-apps-perform-data-operations.md)을 참조하세요.

1. **Log Analytics에 데이터를 씁니다**.

    자세한 내용은 [Azure Log Analytics 데이터 수집기](/connectors/azureloganalyticsdatacollector/) 설명서를 참조하세요.

Logic Apps를 사용 하 여 Microsoft 센티널 용 사용자 지정 커넥터를 만드는 방법의 예는 다음을 참조 하세요.

- [데이터 수집기 API를 사용하여 데이터 파이프라인 만들기](/connectors/azureloganalyticsdatacollector/)
- 웹 후크 [를 사용 하 앱 커넥터 Palo Alto의 sma 논리](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Ingest-Prisma) (Microsoft 센티널 GitHub 커뮤니티)
- [예약된 활성화로 Microsoft Teams 호출 보호](https://techcommunity.microsoft.com/t5/azure-sentinel/secure-your-calls-monitoring-microsoft-teams-callrecords/ba-p/1574600)(블로그)
- [Microsoft Sentinel에 IndicatorVault OTX 위협 지표를](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566) 스트리밍(블로그)

## <a name="connect-with-powershell"></a>PowerShell로 연결

[업로드-AzMonitorLog PowerShell 스크립트를](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) 사용하면 PowerShell을 사용하여 명령줄에서 이벤트 또는 컨텍스트 정보를 Microsoft Sentinel로 스트리밍할 수 있습니다. 이 스트리밍은 데이터 원본과 Microsoft Sentinel 간에 사용자 지정 커넥터를 효과적으로 만듭니다.

예를 들어 다음 스크립트는 CSV 파일을 Microsoft Sentinel에 업로드합니다.

``` PowerShell
Import-Csv .\testcsv.csv
| .\Upload-AzMonitorLog.ps1
-WorkspaceId '69f7ec3e-cae3-458d-b4ea-6975385-6e426'
-WorkspaceKey $WSKey
-LogTypeName 'MyNewCSV'
-AddComputerName
-AdditionalDataTaggingName "MyAdditionalField"
-AdditionalDataTaggingValue "Foo"
```

[Upload-AzMonitorLog PowerShell 스크립트](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) 스크립트는 다음 매개 변수를 사용합니다.

|매개 변수  |Description  |
|---------|---------|
|**WorkspaceId**     |   데이터를 저장할 Microsoft Sentinel 작업 영역 ID입니다.  [작업 영역 ID 및 키를 찾습니다](#find-your-workspace-id-and-key).  |
|**WorkspaceKey**     |   데이터를 저장할 Microsoft Sentinel 작업 영역의 기본 또는 보조 키입니다. [작업 영역 ID 및 키를 찾습니다](#find-your-workspace-id-and-key).  |
|**LogTypeName**     |    데이터를 저장할 사용자 지정 로그 테이블의 이름입니다. **_CL** 의 접미사가 테이블 이름의 끝에 자동으로 추가됩니다.  |
|**AddComputerName**     |   이 매개 변수가 있는 경우 스크립트는 **Computer** 라는 필드의 모든 로그 레코드에 현재 컴퓨터 이름을 추가합니다.      |
|**TaggedAzureResourceId**     | 이 매개 변수가 있는 경우 스크립트는 모든 업로드된 로그 레코드를 지정된 Azure 리소스와 연결합니다. <br><br>이 연결은 리소스 컨텍스트 쿼리에 대해 업로드된 로그 레코드를 사용하도록 설정하고, 리소스 중심의 역할 기반 액세스 제어를 준수합니다.       |
|**AdditionalDataTaggingName**     |      이 매개 변수가 있는 경우 스크립트는 구성된 이름과 **AdditionalDataTaggingValue** 매개 변수에 대해 구성된 값을 사용하여 모든 로그 레코드에 다른 필드를 추가합니다. <br><br>이 경우 **AdditionalDataTaggingValue** 는 비워 둘 수 없습니다. |
|**AdditionalDataTaggingValue**     |   이 매개 변수가 있는 경우 스크립트는 **AdditionalDataTaggingName** 매개 변수에 대해 구성된 필드 이름과 구성된 값을 사용하여 모든 로그 레코드에 다른 필드를 추가합니다. <br><br>**AdditionalDataTaggingName** 매개 변수가 비어 있지만 값이 구성된 경우 기본 필드 이름은 **DataTagging** 입니다.       |
|     |         |

### <a name="find-your-workspace-id-and-key"></a>작업 영역 ID 및 키 찾기

Microsoft Sentinel에서 **WorkspaceID** 및 **WorkspaceKey** 매개 변수에 대한 세부 정보를 찾습니다.

1. Microsoft Sentinel의 왼쪽에서 **설정** 선택한 다음, 작업 **영역 설정** 탭을 선택합니다.

1. **Log Analytics 시작하기** > **1 데이터 원본 연결** 에서 **Windows 및 Linux 에이전트 관리** 를 선택합니다.

1. **Windows 서버** 탭에서 작업 영역 ID, 기본 키 및 보조 키를 찾습니다.

## <a name="connect-with-the-log-analytics-api"></a>Log Analytics API로 연결

Log Analytics 데이터 수집기 API를 사용하여 RESTful 엔드포인트를 직접 호출하여 Microsoft Sentinel로 이벤트를 스트리밍할 수 있습니다.

RESTful 엔드포인트를 직접 호출하는 경우 더 많은 프로그래밍이 필요하지만 유연성이 향상됩니다.

자세한 내용은 [Log Analytics 데이터 수집기 API](../azure-monitor/logs/data-collector-api.md)를 참조하세요. 특히 다음 예제를 참조하세요.

- [C#](../azure-monitor/logs/data-collector-api.md#c-sample)
- [Python](../azure-monitor/logs/data-collector-api.md#python-sample)

## <a name="connect-with-azure-functions"></a>Azure Functions로 연결

RESTful API 및 [PowerShell](../azure-functions/functions-reference-powershell.md)과 같은 다양한 코딩 언어와 함께 Azure Functions를 사용하여 서버리스 사용자 지정 커넥터를 만듭니다.

이 방법의 예제는 다음을 참조하세요.

- [Azure Function을 사용하여 VMware Carbon Black Cloud Endpoint Standard를 Microsoft Sentinel에 커넥트](./data-connectors-reference.md#vmware-carbon-black-endpoint-standard-preview)
- [Azure Function을 사용하여 Microsoft Sentinel에 Okta Single Sign-On 커넥트](./data-connectors-reference.md#okta-single-sign-on-preview)
- [Azure Function을 사용하여 Proofpoint TAP을 Microsoft Sentinel에 커넥트](./data-connectors-reference.md#proofpoint-targeted-attack-protection-tap-preview)
- [Azure Function을 사용하여 Qualys VM을 Microsoft Sentinel에 커넥트](./data-connectors-reference.md#qualys-vulnerability-management-vm-preview)
- [XML, CSV 또는 다른 형식의 데이터를 수집합니다.](../azure-monitor/logs/create-pipeline-datacollector-api.md#ingesting-xml-csv-or-other-formats-of-data)
- [Microsoft Sentinel을 사용하여 Zoom](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516) 모니터링(블로그)
- [Microsoft Sentinel에 Office 365 관리 API 데이터를 얻기 위한 함수 앱 배포(Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data) GitHub 커뮤니티)

## <a name="parse-your-custom-connector-data"></a>사용자 지정 커넥터 데이터 구문 분석

사용자 지정 커넥터의 기본 제공 구문 분석 기술을 사용하여 관련 정보를 추출하고 Microsoft Sentinel에서 관련 필드를 채울 수 있습니다.

예를 들면 다음과 같습니다.

- **Logstash를 사용한 경우** [Grok](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html) 필터 플러그 인을 사용하여 데이터를 구문 분석합니다.
- **Azure 함수를 사용한 경우에는** 코드를 사용하여 데이터를 구문 분석합니다.

Microsoft Sentinel은 쿼리 시 구문 분석이 지원됩니다. 쿼리 시 구문 분석을 사용하면 데이터를 원래 형식으로 푸시한 다음, 필요할 때 구문 분석할 수 있습니다.

쿼리 시 구문 분석은 사용자 지정 커넥터를 만들 때 또는 추출해야 하는 정보까지도 데이터의 정확한 구조를 미리 알 필요가 없음을 의미하기도 합니다. 대신, 조사 중이라도 언제든지 데이터를 구문 분석합니다.

쿼리 시 파싱에 대한 자세한 내용은 [파서](normalization-about-parsers.md)를 참조하세요.

> [!NOTE]
> 파서 업데이트는 이미 Microsoft Sentinel에 수집한 데이터에도 적용됩니다.

## <a name="next-steps"></a>다음 단계

Microsoft Sentinel에 수집된 데이터를 사용하여 다음 프로세스 중 어떤 프로세스로도 환경을 보호합니다.

- [경고 표시](get-visibility.md)
- [데이터 시각화 및 모니터링](monitor-your-data.md)
- [인시던트 조사](investigate-cases.md)
- [위협 탐지](detect-threats-built-in.md)
- [위협 방지 자동화](tutorial-respond-threats-playbook.md)
- [위협에 대한 헌팅](hunting.md)

또한 Zoom을 모니터링하는 사용자 지정 커넥터를 만드는 한 가지 예인 [Microsoft Sentinel을 사용하여 Zoom 모니터링에](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516)대해 알아봅니다.
