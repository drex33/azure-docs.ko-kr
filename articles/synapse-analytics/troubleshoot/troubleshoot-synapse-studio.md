---
title: Synapse Studio 문제 해결
description: Synapse Studio 문제 해결
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: troubleshooting
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: fd560856ab087727d73317eaef5de01950281db9
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399481"
---
# <a name="synapse-studio-troubleshooting"></a>Synapse Studio 문제 해결

이 문제 해결 가이드에서는 네트워크 연결 문제에 대한 지원 티켓을 열 때 제공할 정보를 설명합니다. 적절한 정보를 제공하면 문제를 더 신속하게 해결하는 데 도움이 됩니다.

## <a name="serverless-sql-pool-service-connectivity-issue"></a>서버리스 SQL 풀 서비스 연결 문제

### <a name="symptom-1"></a>증상 1

"연결 대상" 드롭다운에서 "서버리스 SQL 풀" 옵션이 회색으로 표시됩니다.

![symptom1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>증상 2

"서버리스 SQL 풀"을 사용하여 쿼리를 실행하면 "서버에 대한 연결을 설정하지 못했습니다"라는 오류 메시지가 표시됩니다.

![증상 2](media/troubleshooting-synapse-studio/symptom2.png)
 

### <a name="troubleshooting-steps"></a>문제 해결 단계

> [!NOTE] 
>    다음 문제 해결 단계는 Chromium Edge 및 Chrome 사용자를 위한 것입니다. 다른 브라우저(예: FireFox)에서도 동일한 문제 해결 단계를 수행할 수 있지만 "개발자 도구" 창의 레이아웃이 이 TSG의 스크린샷과 다를 수 있습니다. 특정 상황에서는 부정확한 정보가 표시될 수 있으므로 가능한 한 클래식 Edge는 문제 해결에 사용하지 마세요.

"진단 정보" 패널을 열고 "진단 다운로드" 단추를 선택합니다. 오류 보고를 위해 다운로드한 정보를 보관합니다. 대신 "세션 ID"를 복사하여 지원 티켓을 열 때 첨부할 수 있습니다.

![diagnostic-info](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

문제 해결을 시작하려면 Synapse Studio에서 수행한 작업을 다시 시도합니다.

- 증상 1의 경우, "SQL 스크립트" 탭에서 "데이터베이스 사용" 드롭다운의 오른쪽에 있는 "새로 고침" 단추를 선택하고 "서버리스 SQL 풀"이 나타나는지 확인합니다.
- 증상 2의 경우, 쿼리를 다시 실행하여 성공적으로 실행되는지 확인합니다.

문제가 계속되면 브라우저에서 F12 키를 눌러 "개발자 도구"(DevTool)를 엽니다.

"개발자 도구" 창에서 "네트워크" 패널로 전환합니다. 필요한 경우 "네트워크" 패널의 도구 모음에서 "지우기" 단추를 선택합니다.
"네트워크" 패널에서 "캐시 사용 안 함"이 선택되어 있는지 확인합니다.

Azure Synapse Studio에서 수행한 작업을 다시 시도합니다. "개발자 도구"의 "네트워크" 목록에 새 항목이 표시될 수 있습니다. 지원 티켓에 제공할 현재 시스템 시간을 적어둡니다.

![network-panel 1](media/troubleshooting-synapse-studio/network-panel.png)

Url 열이 다음 패턴과 일치하는 항목을 찾습니다.

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

여기서 [*A*]는 작업 영역 이름이고, "-ondemand"는 "-sqlod"이고, [*B*]는 "master"와 같은 데이터베이스 이름이어야 합니다. URL 값은 같지만 메서드 값은 다른 항목이 최대 두 개 있어야 합니다. 즉, OPTIONS와 POST가 여기에 해당합니다. 이 두 항목의 상태 열에 "200" 또는 "20x"가 있는지 확인합니다. 여기서 "x"는 임의의 단일 숫자일 수 있습니다.

둘 중 하나에 "20x"가 아닌 다른 값이 있는 경우:

- 그리고 상태가 "(failed)"로 시작하는 경우, "Status"(상태) 열을 확장하거나 상태 텍스트를 마우스 포인터를 가리켜 전체 텍스트를 표시합니다. 지원 티켓을 열 때 텍스트 및/또는 스크린샷을 포함합니다.

    ![status text](media/troubleshooting-synapse-studio/status-text.png)

    - ERR_NAME_NOT_RESOLVED가 표시되고 10분 이내에 작업 영역을 만든 경우, 10분 동안 기다린 후 다시 시도하여 문제가 계속되는지 확인합니다.
    - ERR_INTERNET_DISCONNECTED 또는 ERR_NETWORK_CHANGED가 표시되는 경우, PC 네트워크 연결에 문제가 있는 것일 수 있습니다. 네트워크 연결을 확인하고 작업을 다시 시도하세요.
    - ERR_CONNECTION_RESET, ERR_SSL_PROTOCOL_ERROR 또는 "SSL"을 포함하는 다른 오류 코드가 표시되는 경우, 로컬 SSL 구성에 문제가 있거나 네트워크 관리자가 서버리스 SQL 풀 서버에 대한 액세스를 차단한 것일 수 있습니다. 지원 티켓을 열고 설명에 오류 코드를 첨부합니다.
    - ERR_NETWORK_ACCESS_DENIED가 표시되는 경우, 관리자에게 문의하여 로컬 방화벽 정책에서 *.database.windows.net 도메인 또는 원격 포트 1443에 대한 액세스를 차단했는지 확인해야 할 수 있습니다.
    - 필요한 경우 다른 컴퓨터 및/또는 네트워크 환경에서 동일한 작업을 즉시 시도하여 PC의 네트워크 구성 문제를 배제합니다.

- 상태는 "40x", "50x" 또는 기타 숫자입니다. 세부 정보를 보려면 해당 항목을 선택합니다. 오른쪽에 항목 세부 정보가 표시됩니다. "Response Header”(응답 헤더) 섹션을 찾습니다. 그런 후 "access-control-allow-origin"이라는 항목이 있는지 확인합니다. 있다면 다음 값 중 하나가 있는지 확인합니다.

    - `*`(단일 별표)
    - https://web.azuresynapse.net/ (또는 브라우저 주소 표시줄의 텍스트가 시작되는 다른 값)

응답 헤더에 위의 값 중 하나가 포함된 경우, 오류 정보를 이미 수집했어야 한다는 의미입니다. 필요한 경우 지원 티켓을 열고 항목 세부 정보의 스크린샷을 첨부할 수 있습니다.

헤더가 보이지 않거나 헤더에 위에 나열된 값 중 하나가 없는 경우, 티켓을 열 때 항목 세부 정보의 스크린샷을 첨부합니다.

 
![항목 세부 정보](media/troubleshooting-synapse-studio/item-details.png)
 
위의 단계를 수행해도 문제가 해결되지 않으면 지원 티켓을 열어야 할 수 있습니다. 지원 티켓을 제출할 때는 이 가이드의 시작 부분에서 다운로드한 "세션 ID" 또는 "진단 정보"를 포함합니다.

문제를 보고할 때 필요한 경우 "개발자 도구"에서 "콘솔" 탭의 스크린샷을 만들어 지원 티켓에 첨부할 수 있습니다. 콘텐츠를 스크롤하고 필요한 경우 전체 메시지를 캡처하는 스크린샷을 둘 이상 만듭니다.

![developer tool console](media/troubleshooting-synapse-studio/developer-tool-console.png)

스크린샷을 첨부할 때는 스크린샷을 만든 시간(또는 예상 시간 범위)을 제공합니다. 그러면 문제를 확인하는 데 도움이 됩니다.

특정 브라우저에서는 "콘솔" 탭에서 타임스탬프를 표시하도록 지원합니다. Chromium Edge/Chrome의 경우, "개발자 도구"에서 "설정" 대화 상자를 열고 "기본 설정" 탭에서 "타임스탬프 표시"를 선택합니다.

![developer tool console settings](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![show time stamp](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="notebook-websocket-connection-issue"></a>Notebook websocket 연결 문제

### <a name="symptom"></a>증상
오류 메시지 표시: Notebook 연결이 예기치 않게 닫혔습니다. 연결을 다시 설정하려면 Notebook을 다시 실행합니다. 진단 정보: websocket_close_error(상관 관계 ID) 

![Notebook websocket 연결 문제](media/troubleshooting-synapse-studio/notebook-websocket-connection-issue.png)

### <a name="root-cause"></a>근본 원인: 
Notebook 실행은 다음 URL에 대한 WebSocket 연결 설정에 따라 달라집니다. 
``` 
wss://{workspace}.dev.azuresynapse.net/jupyterApi/versions/1/sparkPools/{spark-pool}/api/kernels/{kernel-id}/channels 
``` 

+ **{workspace}는** Synapse 작업 영역의 이름입니다. 
+ **{spark-pool}은** 현재 작업 중인 Spark 풀의 이름입니다. 
+ **{kernel-id}는** Notebook 세션을 구분하는 데 사용되는 GUID입니다. 

WebSocket 연결을 설정하는 경우 Synapse Studio WebSocket 요청의 Sec-WebSocket-Protocol 헤더에 액세스 토큰(JWT bearer token)을 포함합니다. 

경우에 따라 WebSocket 요청이 차단되거나 요청 헤더의 JWT 토큰이 네트워크 환경에서 수정될 수 있습니다. 이로 인해 Synapse Notebook에서 서버에 대한 연결을 설정하고 Notebook을 실행할 수 없습니다. 

### <a name="action"></a>작업: 

가능하면 회사 네트워크 내부/외부와 같은 네트워크 환경을 전환하거나 다른 워크스테이션에서 Synapse Notebook에 액세스하세요. 

+ 동일한 워크스테이션과 다른 네트워크 환경에서 Notebook을 실행할 수 있는 경우 네트워크 관리자에게 문의하여 WebSocket 연결이 차단되었는지 확인하세요. 

+ 다른 워크스테이션과 동일한 네트워크 환경에서 Notebook을 실행할 수 있는 경우 WebSocket 요청을 차단할 수 있는 브라우저 플러그 인을 설치하지 않았는지 확인하세요. 

그렇지 않으면 네트워크 관리자에게 문의하고 다음 URL 패턴이 있는 아웃바운드 WebSocket 요청이 허용되고 해당 요청 헤더가 수정되지 않았는지 확인하세요. 

``` 
wss://{workspace}.dev.azuresynapse.net/{path} 
``` 
+ **{workspace}는** Synapse 작업 영역 이름입니다. 

+ **{path}는** URI에 하위 경로(즉, 슬래시 문자가 포함됨)를 나타냅니다. 

이 URL 패턴은 향후 잠재적인 연결 문제 없이 Synapse에 새 WebSocket 종속 기능을 추가할 수 있기 때문에 "근본 원인" 섹션에 표시된 패턴보다 느려질 수 있습니다. 


## <a name="next-steps"></a>다음 단계
이전 단계가 문제를 해결하는 데 도움이 되지 않았다면 [지원 티켓을 만듭니다](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).
