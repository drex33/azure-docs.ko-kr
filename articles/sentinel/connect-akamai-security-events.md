---
title: Akamai 보안 이벤트 수집기를 Azure Sentinel에 연결 | Microsoft Docs
description: Akamai 보안 이벤트 커넥터를 사용하여 Akamai 솔루션의 보안 로그를 Azure Sentinel로 가져오는 방법에 대해 알아봅니다. 통합 문서에서 Akamai 데이터를 보고, 경고를 만들고, 조사를 개선합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: a2fbd53f612fbca737a318cec0f4e7f970b21ce5
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697537"
---
# <a name="connect-your-akamai-security-events-collector-to-azure-sentinel"></a>Akamai 보안 이벤트 수집기를 Azure Sentinel에 연결

> [!IMPORTANT]
> Akamai 보안 이벤트 커넥터는 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

이 문서에서는 Akamai 보안 이벤트 수집기를 Azure Sentinel에 연결하는 방법을 설명합니다. Akamai 보안 이벤트 데이터 커넥터를 사용하면 Azure Sentinel에 Akamai 로그를 쉽게 연결할 수 있으므로, 통합 문서에서 데이터를 보거나 해당 데이터를 쿼리하여 사용자 지정 알림을 만들고, 데이터를 통합하여 조사를 개선할 수 있습니다. Akamai 보안 이벤트 수집기와 Azure Sentinel을 통합하면 CEF 형식의 Syslog, Linux 기반 로그 전달자 및 Log Analytics 에이전트를 사용하게 됩니다. 또한 Kusto 함수를 기반으로 하는 사용자 지정으로 빌드된 로그 파서를 사용합니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.

- 작업 영역 공유 키에 대한 읽기 권한이 있어야 합니다. [작업 영역 키에 대해 자세히 알아보세요](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-akamai-security-events-logs-to-azure-sentinel"></a>Akamai 보안 이벤트 로그를 Azure Sentinel에 전송하기

Azure Sentinel로 로그를 가져오려면 Akamai 보안 이벤트 수집기가 CEF 형식의 Syslog 메시지를 Linux 기반 로그 전달 서버(rsyslog 또는 Syslog를 실행)로 전송하도록 구성합니다. 이 서버에는 Log Analytics 에이전트가 설치되어 있으며, 에이전트는 Azure Sentinel 작업 영역으로 로그를 전달합니다.

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. **데이터 커넥터** 갤러리에서 **Akamai 보안 이벤트(미리 보기)** 를 선택한 다음 **커넥터 페이지를 엽니다**.

1. **지침** 탭의 **구성** 아래에 있는 지침을 따르세요.

    1. **1. Linux Syslog 에이전트 구성** - 실행 중인 로그 전달자가 없는 경우, 또는 다른 로그 전달자가 필요한 경우 이 단계를 수행합니다. 크기 조정 정보, 자세한 지침 및 심도 있는 설명은 Azure Sentinel 설명서의 [1단계: 로그 전달자 배포](connect-cef-agent.md)를 참조하세요.

    1. **2. Common Event Format(CEF) 로그를 Syslog 에이전트로 전달** - Akamai 지침에 따라 [SIEM 통합을 구성](https://developer.akamai.com/tools/integrations/siem)하고 [CEF 커넥터를 설정](https://developer.akamai.com/tools/integrations/siem/siem-cef-connector)합니다. 해당 커넥터는 SIEM OPEN API를 사용하여 Akamai 솔루션의 보안 이벤트를 거의 실시간으로 수신하고 JSON에서 CEF 형식으로 변환합니다.
    
        이 구성은 다음 요소를 포함해야 합니다.
    
        - 로그 대상 – 로그 전달 서버의 호스트 이름 및/또는 IP 주소
        - 프로토콜 및 포트 – TCP 514(다른 프로토콜 및 포트가 권장되는 경우 로그 전달 서버의 syslog 데몬에서 병렬 변경을 수행해야 함)
        - 로그 형식 – CEF
        - 로그 유형 - 모두 사용 가능

    1. **3. 연결 유효성 검사** - 커넥터 페이지에서 명령을 복사하고 로그 전달자에서 실행하여 데이터 수집을 확인합니다. 자세한 지침 및 설명은 Azure Sentinel 설명서에서 [CEF 연결 유효성 검사](troubleshooting-cef-syslog.md#validate-cef-connectivity)를 참조하세요.

        로그가 Log Analytics에 표시될 때까지 최대 20분 정도 소요될 수 있습니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 설정된 후 *CommonSecurityLog* 테이블에서 **Azure Sentinel** 섹션의 **로그** 에 데이터가 표시됩니다.

이 데이터 커넥터는 정상적으로 작동하기 위해 Kusto 함수를 기반으로 하는 파서를 사용합니다. 다음 단계를 따라 쿼리 및 통합 문서에서 사용할 **AkamaiSIEMEvent** Kusto 함수를 설정합니다.

1. Azure Sentinel 탐색 메뉴에서 **로그** 를 선택합니다.

1. 다음 쿼리를 복사하여 쿼리 창에 붙여넣습니다.
    ```kusto
    CommonSecurityLog 
    | where DeviceVendor == 'Akamai'
    | where DeviceProduct == 'akamai_siem'
    | extend EventVendor = 'Akamai'
    | extend EventProduct = 'akamai_siem'
    | extend EventProductVersion = '1.0'
    | extend EventId = DeviceEventClassID
    | extend EventCategory = Activity
    | extend EventSeverity = LogSeverity
    | extend DvcAction = DeviceAction
    | extend NetworkApplicationProtocol = ApplicationProtocol
    | extend Ipv6Src = DeviceCustomIPv6Address2
    | extend RuleName = DeviceCustomString1
    | extend RuleMessages = DeviceCustomString2
    | extend RuleData = DeviceCustomString3
    | extend RuleSelectors = DeviceCustomString4
    | extend ClientReputation = DeviceCustomString5
    | extend ApiId = DeviceCustomString6
    | extend RequestId = DevicePayloadId
    | extend DstDvcHostname = DestinationHostName
    | extend DstPortNumber = DestinationPort
    | extend ConfigId = FlexString1
    | extend PolicyId = FlexString2
    | extend NetworkBytes = SentBytes
    | extend UrlOriginal = RequestURL
    | extend HttpRequestMethod = RequestMethod
    | extend SrcIpAddr = SourceIP
    | extend EventStartTime = datetime(1970-01-01) + tolong(extract(@'.*start=(.*?);', 1, AdditionalExtensions)) * 1s 
    | extend SlowPostAction = extract(@'.*AkamaiSiemSlowPostAction=(.*?);', 1, AdditionalExtensions)
    | extend SlowPostRate = extract(@'.*AkamaiSiemSlowPostRate=(.*?);', 1, AdditionalExtensions)
    | extend RuleVersions = extract(@'.*AkamaiSiemRuleVersions=,?(.*?);', 1, AdditionalExtensions)
    | extend RuleTags = extract(@'.*AkamaiSiemRuleTags=(.*?);', 1, AdditionalExtensions)
    | extend ApiKey = extract(@'.*AkamaiSiemApiKey=(.*?);', 1, AdditionalExtensions)
    | extend Tls = extract(@'.*AkamaiSiemTLSVersion=(.*?);', 1, AdditionalExtensions)
    | extend RequestHeaders = extract(@'.*AkamaiSiemRequestHeaders=;?(.*?);', 1, AdditionalExtensions)
    | extend ResponseHeaders = extract(@'.*AkamaiSiemResponseHeaders=(.*?);', 1, AdditionalExtensions)
    | extend HttpStatusCode = extract(@'.*AkamaiSiemResponseStatus=(.*?);', 1, AdditionalExtensions)
    | extend GeoContinent = extract(@'.*AkamaiSiemContinent=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCountry = extract(@'.*AkamaiSiemCountry=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCity = extract(@'.*AkamaiSiemCity=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoRegion = extract(@'.*AkamaiSiemRegion=(.*?);', 1, AdditionalExtensions)
    | extend GeoAsn = extract(@'.*AkamaiSiemASN=(\d+)', 1, AdditionalExtensions)
    | extend Custom = extract(@'.*AkamaiSiemCusomData=(.*?)', 1, AdditionalExtensions)
    | project TimeGenerated
            , EventVendor
            , EventProduct
            , EventProductVersion
            , EventStartTime
            , EventId
            , EventCategory
            , EventSeverity
            , DvcAction
            , NetworkApplicationProtocol
            , Ipv6Src
            , RuleName
            , RuleMessages
            , RuleData
            , RuleSelectors
            , ClientReputation
            , ApiId
            , RequestId
            , DstDvcHostname
            , DstPortNumber
            , ConfigId
            , PolicyId
            , NetworkBytes
            , UrlOriginal
            , HttpRequestMethod
            , SrcIpAddr
            , SlowPostAction
            , SlowPostRate
            , RuleVersions
            , RuleTags
            , ApiKey
            , Tls
            , RequestHeaders
            , ResponseHeaders
            , HttpStatusCode
            , GeoContinent
            , SrcGeoCountry
            , SrcGeoCity
            , SrcGeoRegion
            , GeoAsn
            , Custom
    ```

1. **저장** 드롭다운을 클릭한 다음, **저장** 을 클릭합니다. **저장** 패널에서

    1. **이름** 으로 **AkamaiSIEMEvent** 를 입력합니다.

    1. **다른 이름으로 저장** 에서 **함수** 를 선택합니다.

    1. **함수 별칭** 으로 **AkamaiSIEMEvent** 를 입력합니다.

    1. **범주** 로 **함수** 를 입력합니다.

    1. **저장** 을 클릭합니다.

    일반적으로 함수 앱이 활성화되려면 10~15분이 걸립니다.

이제 Akamai 데이터를 쿼리할 준비가 되었습니다. 쿼리 창 상단에 `AkamaiSIEMEvent`를 입력하면 됩니다.

더 많은 쿼리 샘플은 커넥터 페이지의 **다음 단계** 탭을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Akamai 보안 이벤트를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 및 잠재적 위협에 대한 가시성을 확보](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.