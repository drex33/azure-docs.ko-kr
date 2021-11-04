---
title: Azure CDN의 모니터링, 메트릭 및 원시 로그
description: 이 문서에서는 Azure CDN 모니터링, 메트릭 및 원시 로그를 설정하고 사용하는 방법을 설명합니다.
services: cdn
author: duongau
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/23/2020
ms.author: yuajia
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3c169a63a39f26174cf6c39ef73c95ae5e545b2c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450479"
---
# <a name="real-time-monitoring-metrics-and-access-logs-for-azure-cdn"></a>Azure CDN의 실시간 모니터링, 메트릭 및 액세스 로그
Microsoft의 Azure CDN에서는 문제를 해결, 추적 및 디버그할 수 있도록 다음과 같은 방법으로 리소스를 모니터링할 수 있습니다. 

* 원시 로그 - CDN이 수신하는 모든 요청에 대한 다양한 정보를 제공합니다. 원시 로그는 활동 로그와 다릅니다. 활동 로그는 Azure 리소스에서 수행된 작업에 대한 가시성을 제공합니다.
* 메트릭 - 바이트 적중률, 요청 수, 응답 크기, 총 대기 시간 등 CDN에 대한 4개의 주요 메트릭을 표시합니다. 또한 메트릭을 분해하는 다양한 차원을 제공합니다.
* 경고 - 고객이 주요 메트릭에 대한 경고를 설정할 수 있습니다.
* 추가 메트릭 - 고객이 Azure Log Analytics를 사용하여 가치 있는 추가 메트릭을 구현할 수 있습니다. Azure Log Analytics에서 몇 가지 다른 메트릭에 대한 쿼리 샘플도 제공합니다.

> [!IMPORTANT]
> HTTP 원시 로그 기능은 Microsoft의 Azure CDN에서 사용할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="configuration---azure-portal"></a>구성 - Azure Portal

Microsoft 프로필에서 Azure CDN의 원시 로그를 구성하려면: 

1. Azure Portal 메뉴에서 **모든 리소스** >>  **\<your-CDN-profile>** 를 선택합니다.

2. **모니터링** 에서 **진단 설정** 을 선택합니다.

3. **+ 진단 설정 추가** 를 선택합니다.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-01.png" alt-text="CDN 프로필에 대한 진단 설정을 추가합니다." border="true":::
    
    > [!IMPORTANT]
    > 원시 로그는 프로필 수준에서만 사용할 수 있지만 집계된 HTTP 상태 코드 로그는 엔드포인트 수준에서 사용할 수 있습니다.

4. **진단 설정** 에서 **진단 설정 이름** 아래에 진단 설정의 이름을 입력합니다.

5. **AzureCdnAccessLog** 를 선택하고 보존 기간(일)을 설정합니다.

6. **대상 세부 정보** 를 선택합니다. 대상 옵션은 다음과 같습니다.
    * **Log Analytics에 보내기**
        * **구독** 및 **Log Analytics 작업 영역** 을 선택합니다.
    * **스토리지 계정에 보관**
        * **구독** 및 **스토리지 계정** 을 선택합니다.
    * **이벤트 허브로 스트림**
        * **구독**, **이벤트 허브 네임스페이스**, **이벤트 허브 이름(선택 사항)** 및 **이벤트 허브 정책 이름** 을 선택합니다.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-02.png" alt-text="로그 설정의 대상을 구성합니다." border="true":::

7. **저장** 을 선택합니다.

## <a name="configuration---azure-powershell"></a>구성 - Azure PowerShell

[Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting)을 사용하여 원시 로그에 대한 진단 설정을 구성합니다.

보존 데이터는 명령의 **-RetentionInDays** 옵션으로 정의됩니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>스토리지 계정에서 진단 로그 사용

1. Azure PowerShell에 로그인합니다.

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. 스토리지 계정에서 진단 로그를 사용하려면 다음 명령을 입력합니다. 변수를 사용자의 값으로 바꿉니다.

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Log Analytics 작업 영역에서 진단 로그 사용

1. Azure PowerShell에 로그인합니다.

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Log Analytics 작업 영역에서 진단 로그를 사용하려면 다음 명령을 입력합니다. 변수를 사용자의 값으로 바꿉니다.

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>이벤트 허브 네임스페이스에서 진단 로그 사용

1. Azure PowerShell에 로그인합니다.

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. 이벤트 허브 네임스페이스에서 진단 로그를 사용하려면 다음 명령을 입력합니다. 변수를 사용자의 값으로 바꿉니다.

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $evthubnamespace = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $eventhub = Get-AzEventHubNamespace -ResourceGroupName $rsg -Name $eventhubname

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhub.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

## <a name="raw-logs-properties"></a>원시 로그 속성

Microsoft 서비스의 Azure CDN은 현재 원시 로그를 제공합니다. 원시 로그는 각 항목에 다음 스키마가 포함된 개별 API 요청을 제공합니다. 

| 속성  | Description |
| ------------- | ------------- |
| BackendHostname | 요청이 백 엔드에 전달되는 경우 이 필드는 백 엔드의 호스트 이름을 나타냅니다. 요청이 리디렉션되거나 지역 캐시(라우팅 규칙에 캐싱이 설정됨)로 전달되는 경우 이 필드는 비어 있습니다. |
| CacheStatus | 캐싱 시나리오의 경우 이 필드는 POP에서 캐시 적중/누락을 정의합니다. |
| ClientIp | 요청한 클라이언트의 IP 주소입니다. 요청에 X-Forwarded-For 헤더가 있는 경우 클라이언트 IP는 동일한 곳에서 선택됩니다. |
| ClientPort | 요청한 클라이언트의 IP 포트입니다. |
| HttpMethod | 요청에서 사용된 HTTP 메서드 |
| HttpStatusCode | 프록시에서 반환된 HTTP 상태 코드입니다. |
| HttpStatusDetails | 요청의 결과 상태입니다. 이 문자열 값의 의미는 상태 참조 테이블에서 찾을 수 있습니다. |
| HttpVersion | 요청 또는 연결의 유형입니다. |
| POP | 요청이 도달한 에지의 약식 이름입니다. |
| RequestBytes | 요청 헤더 및 요청 본문을 포함하여 HTTP 요청 메시지의 크기(바이트)입니다. |
| RequestUri | 받은 요청의 URI |
| ResponseBytes | 백 엔드 서버에서 응답으로 보낸 바이트 수입니다.  |
| RoutingRuleName | 요청과 일치하는 라우팅 규칙의 이름입니다. |
| RulesEngineMatchNames | 요청과 일치하는 규칙의 이름입니다. |
| SecurityProtocol | 요청에 사용되는 TLS/SSL 프로토콜 버전이거나, 암호화가 없는 경우 null입니다. |
| SentToOriginShield </br> (사용되지 않음) * **다음 섹션에서 사용 중단에 대한 참고 사항을 참조하세요.**| True이면 요청이 에지 POP가 아닌 원본 보호 캐시에서 응답되었음을 의미합니다. 원본 보호는 캐시 적중률을 향상하는 데 사용되는 부모 캐시입니다. |
| isReceivedFromClient | True인 경우 요청이 클라이언트에서 나온 것을 의미합니다. False인 경우 요청이 에지(자식 POP)에서 누락되고 원본 보호(부모 POP)에서 응답됩니다. |
| TimeTaken | 요청의 첫 번째 바이트부터 마지막 응답 바이트까지의 시간(초)입니다. |
| TrackingReference | Front Door에서 제공하는 요청을 식별하는 고유한 참조 문자열로, 클라이언트에 X-Azure-Ref 헤더로 전송됩니다. 특정 요청의 액세스 로그에서 세부 정보를 검색하는 데 필요합니다. |
| UserAgent | 클라이언트에서 사용한 브라우저 유형입니다. |
| ErrorInfo | 이 필드에는 문제 해결 영역의 범위를 좁히기 위한 특정 유형의 오류가 포함되어 있습니다. </br> 가능한 값은 다음과 같습니다. </br> **NoError**: 발견된 오류가 없음을 나타냅니다. </br> **CertificateError**: 제네릭 SSL 인증서 오류입니다.</br> **CertificateNameCheckFailed**: SSL 인증서의 호스트 이름이 잘못되었거나 일치하지 않습니다. </br> **ClientDisconnected**: 클라이언트 네트워크 연결 때문에 요청이 실패했습니다. </br> **UnspecifiedClientError**: 제네릭 클라이언트 오류입니다. </br> **InvalidRequest**: 요청이 잘못되었습니다. 이 오류는 잘못된 형식의 헤더, 본문 및 URL 때문에 발생할 수 있습니다. </br> **DNSFailure**: DNS 오류가 발생했습니다. </br> **DNSNameNotResolved**: 서버 이름이나 주소를 확인할 수 없습니다. </br> **OriginConnectionAborted**: 원본과의 연결이 갑자기 중지되었습니다. </br> **OriginConnectionError**: 제네릭 원본 연결 오류입니다. </br> **OriginConnectionRefused**: 원본과의 연결을 설정할 수 없습니다. </br> **OriginError**: 제네릭 원본 오류입니다. </br> **OriginInvalidResponse**: 원본이 잘못되었거나 인식할 수 없는 응답을 반환했습니다. </br> **OriginTimeout**: 원본 요청에 대한 시간 제한이 만료되었습니다. </br> **ResponseHeaderTooBig**: 원본에서 너무 많은 응답 헤더를 반환했습니다. </br> **RestrictedIP**: 제한된 IP로 인해 요청이 차단되었습니다. </br> **SSLHandshakeError**: SSL 핸드셰이크 오류로 인해 원본과의 연결을 설정할 수 없습니다. </br> **UnspecifiedError**: 테이블의 오류에 맞지 않는 오류가 발생했습니다. |
| TimeToFirstByte | Microsoft CDN이 요청을 수신한 시점에서 클라이언트에 첫 번째 바이트가 전송된 시점까지의 시간(밀리초)입니다. 이 시간은 Microsoft 쪽에서만 측정됩니다. 클라이언트 쪽 데이터는 측정하지 않습니다. |
> [!NOTE]
> 쿼리를 실행하여 Log Analytics 프로필에서 로그를 볼 수 있습니다. 샘플 쿼리는 다음과 같습니다.
    ```
    AzureDiagnostics | where Category == "AzureCdnAccessLog"
    ```

### <a name="sent-to-origin-shield-deprecation"></a>원본 보호로 전송됨 사용 중단
원시 로그 속성 **isSentToOriginShield** 는 더 이상 사용되지 않으며 새 필드 **isReceivedFromClient** 로 바뀌었습니다. 사용 중단된 필드를 이미 사용하고 있는 경우 새 필드를 사용하세요. 

원시 로그는 CDN 에지(자식 POP)와 원본 보호 모두에서 생성된 로그를 포함합니다. 원본 보호는 전 세계에 전략적으로 배치되는 부모 노드를 말합니다. 이러한 노드는 원본 서버와 통신하여 원본에 대한 트래픽 부하를 줄입니다. 

원본 보호로 이동하는 모든 요청에는 두 가지 로그 항목이 있습니다.

* 에지 노드에 대한 항목 하나
* 원본 보호에 대한 항목 하나 

에지 노드 및 원본 보호에 대한 송신 또는 응답을 구분하려면 **isReceivedFromClient** 필드를 사용하여 올바른 데이터를 가져올 수 있습니다. 

값이 false인 경우 요청이 원본 보호에서 에지 노드로 응답되는 것을 의미합니다. 이 방법은 원시 로그를 청구 데이터와 비교하는 데 효과적입니다. 원본 보호에서 에지 노드로의 송신에는 요금이 부과되지 않습니다. 에지 노드에서 클라이언트로의 송신에는 요금이 부과됩니다. 

**Log Analytics에서 원본 보호에 생성된 로그를 제외하는 Kusto 쿼리 샘플**

```kusto
AzureDiagnostics 
| where OperationName == "Microsoft.Cdn/Profiles/AccessLog/Write" and Category == "AzureCdnAccessLog"  
| where isReceivedFromClient == true

```

> [!IMPORTANT]
> HTTP 원시 로그 기능은 **2020년 2월 25일** 이후에 만들어지거나 업데이트된 모든 프로필에 자동으로 사용할 수 있습니다. 이전에 만든 CDN 프로필의 경우 로깅을 설정한 후 CDN 엔드포인트를 업데이트해야 합니다. 예를 들어 CDN 엔드포인트에서 지역 필터링으로 이동하고, 해당 워크로드와 관련이 없는 모든 국가/지역을 차단하고, 저장을 누를 수 있습니다.


## <a name="metrics"></a>메트릭
Microsoft의 Azure CDN은 Azure Monitor와 통합되며 문제를 추적, 해결 및 디버그할 수 있도록 4개의 CDN 메트릭을 게시합니다. 

메트릭은 차트로 표시되며 PowerShell, CLI 및 API를 통해 액세스할 수 있습니다. CDN 메트릭은 무료로 제공됩니다.

Microsoft의 Azure CDN은 메트릭을 60초 간격으로 측정하여 전송합니다. 메트릭은 포털에 표시되는 데 최대 3분 정도 걸릴 수 있습니다. 

자세한 내용은 [Azure Monitor 메트릭](../azure-monitor/essentials/data-platform-metrics.md)을 참조하세요.

**Microsoft의 Azure CDN에서 지원하는 메트릭**

| metrics  | 설명 | 차원 |
| ------------- | ------------- | ------------- |
| 바이트 적중률* | 총 송신에 대해 계산된 CDN 캐시의 송신 백분율입니다. | 엔드포인트 |
| RequestCount | CDN에서 제공하는 클라이언트 요청 수입니다. | 엔드포인트 </br> 클라이언트 국가입니다. </br> 클라이언트 지역입니다. </br> HTTP 상태입니다. </br> HTTP 상태 그룹입니다. |
| ResponseSize | CDN 에지에서 클라이언트로 응답으로 전송된 바이트 수입니다. |엔드포인트 </br> 클라이언트 국가입니다. </br> 클라이언트 지역입니다. </br> HTTP 상태입니다. </br> HTTP 상태 그룹입니다. |
| TotalLatency | CDN이 수신한 클라이언트 요청으로부터 **CDN이 클라이언트로 전송하는 마지막 응답 바이트까지** 의 총 시간입니다. |엔드포인트 </br> 클라이언트 국가입니다. </br> 클라이언트 지역입니다. </br> HTTP 상태입니다. </br> HTTP 상태 그룹입니다. |

***바이트 적중률 = (에지로부터 송신 - 원본으로부터 송신)/에지로부터 송신**

바이트 적중률 계산에서 제외되는 시나리오:

* 규칙 엔진 또는 쿼리 문자열 캐싱 동작을 통해 캐시를 명시적으로 구성하지 않습니다.
* 저장소 또는 프라이빗 캐시를 사용하지 않고 캐시 제어 지시문을 명시적으로 구성합니다.

### <a name="metrics-configuration"></a>메트릭 구성

1. Azure Portal 메뉴에서 **모든 리소스** >>  **\<your-CDN-profile>** 를 선택합니다.

2. **모니터링** 에서 **메트릭** 을 선택합니다.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-03.png" alt-text="CDN 프로필에 대한 메트릭." border="true":::

3. **메트릭 추가** 를 선택하고 추가할 메트릭을 선택합니다.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-04.png" alt-text="CDN 프로필에 대한 메트릭을 추가하고 선택합니다." border="true":::

4. **필터 추가** 를 선택하여 필터를 추가합니다.
    
    :::image type="content" source="./media/cdn-raw-logs/raw-logs-05.png" alt-text="메트릭에 필터를 적용합니다." border="true":::

5. 분할 **적용** 을 선택하여 다양한 차원으로 추세를 확인합니다.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-06.png" alt-text="메트릭에 분할을 적용합니다." border="true":::

6. **새 차트** 를 선택하여 새 차트를 추가합니다.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-07.png" alt-text="메트릭 보기에 새 차트를 추가합니다." border="true":::

### <a name="alerts"></a>경고

**모니터링** >> **경고** 를 선택하여 Microsoft CDN에 대한 경고를 설정할 수 있습니다.

메트릭 섹션에 나열된 메트릭에 대해 **새 경고 규칙** 을 선택합니다.

:::image type="content" source="./media/cdn-raw-logs/raw-logs-08.png" alt-text="CDN 엔드포인트에 대한 경고를 구성합니다." border="true":::

경고는 Azure Monitor를 기반으로 청구됩니다. 경고에 대한 자세한 내용은 [Azure Monitor 경고](../azure-monitor/alerts/alerts-overview.md)를 참조하세요.

### <a name="additional-metrics"></a>추가 메트릭
추가 비용으로 Azure Log Analytics 및 원시 로그를 사용하여 추가 메트릭을 설정할 수 있습니다.

1. 위의 진단 사용 단계를 따라 Log Analytics로 원시 로그를 보냅니다.

2. 만든 Log Analytics 작업 영역을 선택합니다.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-09.png" alt-text="Log Analytics 작업 영역 선택" border="true":::   

3. Log Analytics 작업 영역에서 **일반** 아래에 있는 **로그** 를 선택합니다.  그런 다음 **시작하기** 를 선택합니다.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-10.png" alt-text="Log Analytics 리소스 작업 영역." border="true":::   
 
4. **CDN 프로필** 을 선택합니다.  예제 쿼리를 선택하여 실행하거나 예제 화면을 닫고 사용자 지정 쿼리를 입력합니다.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-11.png" alt-text="예제 쿼리 화면." border="true":::   

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-12.png" alt-text="쿼리 실행." border="true":::   

4. 데이터를 차트로 보려면 **차트** 를 선택합니다.  **대시보드에 고정** 을 선택하여 차트를 Azure 대시보드에 고정합니다.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-13.png" alt-text="대시보드에 차트를 고정합니다." border="true"::: 

## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft CDN 서비스의 HTTP 원시 로그를 사용하도록 설정했습니다.

Azure CDN 및 이 문서에 언급된 다른 Azure 서비스에 대한 자세한 내용은 다음을 참조하세요.

* Azure CDN 사용 패턴을 [분석](cdn-log-analysis.md)합니다.

* [Azure Monitor](../azure-monitor/overview.md)에 대해 자세히 알아봅니다.

* [Azure Monitor의 Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)를 구성합니다.
