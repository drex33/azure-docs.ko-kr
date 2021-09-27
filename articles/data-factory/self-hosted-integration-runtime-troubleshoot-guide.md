---
title: 자체 호스팅 Integration Runtime 문제 해결
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics 파이프라인에서 자체 호스팅 통합 런타임 문제를 해결하는 방법을 알아봅니다.
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.custom: synapse
ms.topic: troubleshooting
ms.date: 09/09/2021
ms.author: lle
ms.openlocfilehash: d1b3770d236c7f88090840720e8f88fd453e70cf
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124755941"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>자체 호스팅 Integration Runtime 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Synapse 작업 영역에서 자체 호스팅 IR(통합 런타임)에 대한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="gather-self-hosted-ir-logs"></a>자체 호스팅 IR 로그 수집

자체 호스팅 IR 또는 공유 IR에서 실행되는 작업이 실패한 경우 서비스는 오류 로그 보기 및 업로드를 지원합니다. 오류 보고서 ID를 얻으려면 여기에 설명된 지침을 따르고 보고서 ID를 입력하여 관련된 알려진 문제를 검색합니다.

1. 서비스 UI의 모니터 페이지에서 **파이프라인 실행** 을 선택합니다.

1. 다음 스크린샷에 표시된 것처럼 **작업 실행** 아래의 **오류** 열에서 강조 표시된 단추를 선택하여 활동 로그를 표시합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
    
    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png" alt-text="&quot; &quot; &quot; 모든 파이프라인 실행 창에서 작업 실행 섹션의 스크린샷 &quot;":::
    
    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
    
    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page-synapse.png" alt-text="&quot; &quot; &quot; 모든 파이프라인 실행 창에서 작업 실행 섹션의 스크린샷 &quot;":::
    
    ---
    
    활동 로그는 실패한 활동 실행에 대해 표시됩니다.
    
    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png" alt-text="실패한 작업에 대한 활동 로그의 스크린샷"::: 
    
3. 추가 지원이 필요하면 **로그 보내기** 를 선택합니다.
 
   **Microsoft로 자체 호스팅 IR(통합 런타임) 로그 공유** 창이 열립니다.

    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png" alt-text="&quot;Microsoft window를 사용 하 여 자체 호스팅 IR (통합 런타임) 로그를 공유 하는 스크린샷 &quot;":::

1. 보내려는 로그를 선택합니다. 
    * '자체 호스팅 IR'의 경우 실패한 작업과 관련된 로그 또는 자체 호스팅 IR 노드의 모든 로그를 업로드할 수 있습니다. 
    * '공유 IR'의 경우 실패한 작업과 관련된 로그만 업로드할 수 있습니다.

1. 로그를 업로드할 때 보고서 ID를 기록해 둡니다. 그러면 문제를 해결하기 위해 추가 지원이 필요할 경우 사용할 수 있습니다.

    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png" alt-text="IR 로그의 업로드 진행률 창에 표시된 보고서 ID의 스크린샷":::

> [!NOTE]
> 로그 보기 및 업로드 요청은 모든 온라인 자체 호스팅 IR 인스턴스에서 실행됩니다. 로그가 없는 경우 모든 자체 호스팅 IR 인스턴스가 온라인 상태인지 확인합니다. 


## <a name="self-hosted-ir-general-failure-or-error"></a>자체 호스팅 IR 일반 실패 또는 오류

### <a name="out-of-memory-issue"></a>메모리 부족 문제

#### <a name="symptoms"></a>증상

OOM(OutOfMemoryException) 오류는 연결된 IR 또는 자체 호스팅 IR을 사용하여 조회 작업을 실행하려고 할 때 발생합니다.

#### <a name="cause"></a>원인

IR 컴퓨터에서 일시적으로 메모리가 너무 많이 사용되는 경우 새 작업에서 OOM 오류가 발생할 수 있습니다. 이 문제는 많은 수의 동시 작업으로 인해 발생할 수 있으며, 의도적 오류입니다.

#### <a name="resolution"></a>해결 방법

IR 노드에서 리소스 사용량 및 동시 작업 실행을 확인합니다. 단일 IR 노드에서 너무 많은 작업이 동시에 실행되지 않도록 활동 실행의 내부 및 트리거 시간을 조정합니다.

### <a name="concurrent-jobs-limit-issue"></a>동시 작업 제한 문제

#### <a name="symptoms"></a>증상

UI에서 동시 작업 제한을 늘리려고 하면 프로세스가 ‘업데이트 중’ 상태에서 중단됩니다.

예제 시나리오: 최대 동시 작업 값이 현재 24로 설정되어 있으며 작업을 더 빠르게 실행할 수 있도록 개수를 늘리려고 합니다. 입력할 수 있는 최솟값은 3이고 최댓값은 32입니다. 값을 24에서 32으로 높인 다음 **업데이트** 단추를 선택합니다. 다음 스크린샷에 표시된 것처럼 프로세스가 '업데이트 중' 상태에서 중단됩니다. 페이지를 새로 고치면 값이 계속 24로 표시됩니다. 예상과 달리 32로 업데이트되지 않았습니다.

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png" alt-text="상태 업데이트 중 중단 된 프로세스를 표시 하는 통합 런타임의 노드 창 스크린샷 &quot; &quot;":::

#### <a name="cause"></a>원인

동시 작업 수에 대한 제한은 컴퓨터의 논리 코어 및 메모리에 따라 달라집니다. 값을 24 등으로 낮추고 결과를 확인합니다.

> [!TIP] 
> -    논리 코어 수 및 컴퓨터에서 논리 코어 수를 확인하는 방법에 대한 자세한 내용은 [Windows 10에서 CPU의 코어 수를 확인하는 4가지 방법](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/)을 참조하세요.
> -    math.log를 계산하는 방법을 알아보려면 [로그 계산기](https://www.rapidtables.com/calc/math/Log_Calculator.html)로 이동합니다.


### <a name="self-hosted-ir-high-availability-ha-ssl-certificate-issue"></a>자체 호스팅 IR HA(고가용성) SSL 인증서 문제

#### <a name="symptoms"></a>증상

자체 호스팅 IR 작업 노드에서 다음 오류를 보고했습니다.

"주 노드 net.tcp://abc.cloud.corp.Microsoft.com:8060/ExternalService.svc/에서 공유 상태를 끌어오지 못했습니다. 활동 ID: XXXXX X.509 인증서 CN=abc.cloud.corp.Microsoft.com, OU=test, O=Microsoft 체인 빌드가 실패했습니다. 사용한 인증서에 확인할 수 없는 신뢰 체인이 포함되어 있습니다. 인증서를 바꾸거나 certificateValidationMode를 변경하십시오. 해지 서버가 오프라인 상태이므로 해지 함수가 해지를 확인할 수 없습니다."

#### <a name="cause"></a>원인

SSL/TLS 핸드셰이크와 관련된 사례를 처리할 때 인증서 체인 확인과 관련된 몇 가지 문제가 발생할 수 있습니다. 

#### <a name="resolution"></a>해결 방법

- 다음은 X.509 인증서 체인 빌드 실패 문제를 해결하는 빠르고 직관적인 방법입니다.
 
    1. 확인해야 하는 인증서를 내보냅니다. 이렇게 하려면 다음을 수행합니다.
    
       a. Windows에서 **시작** 을 선택하고 **인증서** 를 입력한 다음 **컴퓨터 인증서 관리** 를 선택합니다.
       
       b. 파일 탐색기의 왼쪽 창에서 확인할 인증서를 검색하고 마우스 오른쪽 단추를 클릭한 다음 **모든 작업** > **내보내기** 를 선택합니다.
    
        :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png" alt-text="&quot; &quot;  >  &quot; &quot; &quot; 컴퓨터 인증서 관리 창에서 인증서에 대 한 모든 작업 내보내기 컨트롤 &quot; 의 스크린샷":::

    2. 내보낸 인증서를 클라이언트 컴퓨터에 복사합니다. 
    3. 클라이언트 쪽 명령 프롬프트 창에서 다음 명령을 실행합니다. *\<certificate path>* 및 *\<output txt file path>* 을 실제 경로로 바꾸어야 합니다.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        예를 들면 다음과 같습니다.

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. 출력 TXT 파일에서 오류를 확인합니다. TXT 파일의 끝에서 오류 요약을 찾을 수 있습니다.

        예를 들면 다음과 같습니다. 

        :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png" alt-text="TXT 파일 끝의 오류 요약을 보여 주는 스크린샷":::

        다음 스크린샷에 표시된 것처럼 로그 파일의 끝에 오류가 표시되지 않으면 클라이언트 컴퓨터에서 인증서 체인이 성공적으로 빌드된 것으로 간주할 수 있습니다.
        
        :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png" alt-text="오류가 표시되지 않은 로그 파일의 스크린샷":::      

- 인증서 파일에서 AIA(기관 정보 액세스), CDP(CRL 배포 지점) 또는 OCSP(온라인 인증서 상태 프로토콜) 파일 이름 확장명이 구성된 경우 보다 직관적인 방법으로 확인할 수 있습니다.
 
    1. 다음 스크린샷에 표시된 것처럼 인증서 세부 정보를 확인하여 이 정보를 가져옵니다.
    
        :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png" alt-text="certificate-details의 스크린샷":::
    
    1. 다음 명령을 실행합니다. *\<certificate path>* 을 인증서의 실제 경로로 바꿔야 합니다.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    
        URL 검색 도구가 열립니다. 
        
    1. AIA, CDP 및 OCSP 파일 이름 확장명을 사용하여 인증서를 확인하려면 **검색** 을 선택합니다.

        :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png" alt-text="URL 검색 도구 및 검색 단추의 스크린샷":::
 
        AIA의 인증서 상태가 '확인됨'이고 CDP 또는 OCSP의 인증서 상태가 '확인됨'인 경우 인증서 체인을 성공적으로 빌드한 것입니다.

        AIA 또는 CDP 검색 시도가 실패하는 경우 네트워크 팀과 협력하여 클라이언트 컴퓨터를 대상 URL에 연결할 준비를 합니다. HTTP 경로 또는 LDAP(Lightweight Directory Access Protocol) 경로를 확인할 수 있으면 충분합니다.

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>자체 호스팅 IR에서 파일 또는 어셈블리를 로드할 수 없음

#### <a name="symptoms"></a>증상

다음과 같은 오류 메시지가 발생합니다.

"파일 또는 어셈블리 'XXXXXXXXXXXXXXXX, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' 또는 해당 종속성 중 하나를 로드할 수 없습니다. 시스템은 지정된 파일을 찾을 수 없습니다. 활동 ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3"
 
다음은 보다 구체적인 오류 메시지입니다. 

"파일 또는 어셈블리 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' 또는 해당 종속성 중 하나를 로드할 수 없습니다. 시스템은 지정된 파일을 찾을 수 없습니다. 활동 ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3"

#### <a name="cause"></a>원인

프로세스 모니터에서 다음 결과를 볼 수 있습니다.

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox" lightbox="media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png" alt-text="프로세스 모니터의 경로 목록을 보여 주는 스크린샷":::

> [!TIP] 
> 프로세스 모니터에서 다음 스크린샷에 표시된 것처럼 필터를 설정할 수 있습니다.
>
> 위의 오류 메시지에는 DLL System.ValueTuple이 관련 *GAC*(전역 어셈블리 캐시) 폴더, *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* 폴더 또는 *C:\Program Files\Microsoft Integration Runtime\4.0\Shared* 폴더에 있지 않음이 표시됩니다.
>
> 기본적으로 프로세스는 먼저 *GAC* 폴더에서, 그런 다음 *Shared* 폴더에서, 마지막으로 *Gateway* 폴더에서 DLL을 로드합니다. 따라서 유용한 경로에서 DLL을 로드할 수 있습니다.

<br>

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png" alt-text="&quot; &quot; DLL에 대 한 필터를 나열 하는 프로세스 모니터 필터 페이지의 스크린샷":::

#### <a name="resolution"></a>해결 방법

*System.ValueTuple.dll* 파일은 *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway\DataScan* 폴더에서 찾을 수 있습니다. 이 문제를 해결하려면 *System.ValueTuple.dll* 파일을 *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* 폴더에 복사합니다.

동일한 방법을 사용하여 다른 누락된 파일 또는 어셈블리 문제를 해결할 수 있습니다.

#### <a name="more-information-about-this-issue"></a>이 문제에 대한 자세한 정보

*%windir%\Microsoft.NET\assembly* 및 *%windir%\assembly* 아래에 *System.ValueTuple.dll* 표시되는 이유는 이것이 .NET 동작이기 때문입니다. 

다음 오류에서 *System.ValueTuple* 어셈블리가 누락된 것을 분명히 알 수 있습니다. 이 문제는 애플리케이션이 *System.ValueTuple.dll* 어셈블리를 확인하려고 할 때 발생합니다.
 
"\<LogProperties>\<ErrorInfo>[{"Code":0,"Message":"'Npgsql.PoolManager'의 형식 이니셜라이저에서 예외를 throw했습니다.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.TypeInitializationException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[{"Code":0,"Message":"파일 또는 어셈블리 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' 또는 해당 종속성 중 하나를 로드할 수 없습니다. 시스템이 지정된 파일을 찾을 수 없습니다.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.IO.FileNotFoundException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[]}]}]\</ErrorInfo>\</LogProperties>"
 
GAC에 대한 자세한 내용은 [전역 어셈블리 캐시](/dotnet/framework/app-domains/gac)를 참조하세요.


### <a name="self-hosted-integration-runtime-authentication-key-is-missing"></a>자체 호스팅 통합 런타임 인증 키가 없음

#### <a name="symptoms"></a>증상

자체 호스팅 통합 런타임이 인증 키 없이 갑자기 오프라인 상태가 되고 이벤트 로그에 다음과 같은 오류 메시지가 표시됩니다. 

“인증 키가 아직 할당되지 않았습니다.”

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png" alt-text="인증 키가 아직 할당되지 않았음을 보여 주는 통합 런타임 이벤트 창의 스크린샷":::

#### <a name="cause"></a>원인

- Azure Portal에서 자체 호스팅 IR 노드 또는 논리적 자체 호스팅 IR이 삭제되었습니다.
- 완전한 제거가 수행되었습니다.

#### <a name="resolution"></a>해결 방법

위의 원인이 모두 적용되지 않는 경우 *%programdata%\Microsoft\Data Transfer\DataManagementGateway* 폴더로 이동하여 '구성' 파일이 삭제되었는지 여부를 확인할 수 있습니다. 파일이 삭제된 경우 Netwrix 문서 [Windows 파일 서버에서 파일을 삭제한 사람 검색](https://www.netwrix.com/how_to_detect_who_deleted_file.html)의 지침을 따릅니다.

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png" alt-text="구성 파일을 확인하기 위한 이벤트 로그 세부 정보 창의 스크린샷":::


### <a name="cant-use-self-hosted-ir-to-bridge-two-on-premises-datastores"></a>자체 호스팅 IR을 사용하여 두 개의 온-프레미스 데이터 저장소를 연결할 수 없음

#### <a name="symptoms"></a>증상

원본 및 대상 데이터 저장소 모두에 대해 자체 호스팅 IR을 만든 후 두 IR을 연결하여 하나의 복사 작업을 완료하려고 합니다. 데이터 저장소가 서로 다른 가상 네트워크에 구성되어 있거나 데이터 저장소가 게이트웨이 메커니즘을 인식할 수 없는 경우 다음 오류 중 하나가 표시됩니다. 

* "대상 IR에서 원본 드라이버를 찾을 수 없습니다."
* "대상 IR이 원본에 액세스할 수 없습니다."
 
#### <a name="cause"></a>원인

자체 호스팅 IR이 각 데이터 저장소에 대해 설치해야 하는 클라이언트 에이전트가 아니라 복사 작업의 중앙 노드로 설계되었습니다.
 
이 경우 각 데이터 저장소에 대한 연결된 서비스는 동일한 IR을 사용하여 만들어야 하며 IR은 네트워크를 통해 두 데이터 저장소에 모두 액세스할 수 있어야 합니다. IR이 원본 데이터 저장소 또는 대상 데이터 저장소 또는 제3의 컴퓨터에 설치되어 있는지 여부는 중요하지 않습니다. 두 개의 연결된 서비스를 서로 다른 IR을 사용하여 만들었지만 동일한 복사 작업에서 사용하는 경우 대상 IR이 사용되며 대상 IR 컴퓨터에 두 데이터 저장소에 대한 드라이버를 설치해야 합니다.

#### <a name="resolution"></a>해결 방법

대상 IR에 원본 및 대상 데이터 저장소 모두에 대한 드라이버를 설치하고 원본 데이터 저장소에 액세스할 수 있는지 확인합니다.
 
두 데이터 저장소 간에 네트워크를 통해 트래픽을 전달할 수 없는 경우(예: 두 개의 가상 네트워크에 구성된 경우) IR이 설치된 경우에도 단일 작업으로 복사를 완료할 수 없습니다. 단일 작업으로 복사를 완료할 수 없는 경우 한 VNET에 하나씩 두 개의 IR을 사용하여 두 개의 복사 작업을 만들 수 있습니다. 
* 한 IR을 데이터 저장소 1에서 Azure Blob Storage로 복사합니다.
* 다른 IR을 Azure Blob Storage에서 데이터 저장소 2로 복사합니다. 

이 해결 방법은 IR을 사용하여 연결이 끊어진 두 데이터 저장소를 연결하는 브리지를 만드는 데 필요한 요구 사항을 시뮬레이션할 수 있습니다.


### <a name="credential-sync-issue-causes-credential-loss-from-ha"></a>자격 증명 동기화 문제로 인해 HA에서 자격 증명이 손실됨

#### <a name="symptoms"></a>증상

데이터 원본 자격 증명 'XXXXXXXXXX'가 페이로드를 사용하는 현재 통합 런타임 노드에서 삭제되면 다음과 같은 오류 메시지가 나타납니다.

“Azure Portal에서 링크 서비스를 삭제할 때 작업에 잘못된 페이로드가 있습니다. 자격 증명을 사용하여 새 링크 서비스를 다시 만드세요.”

#### <a name="cause"></a>원인

자체 호스팅 IR이 두 노드를 사용하는 기본 제공 HA 모드이지만 노드가 자격 증명 동기화 상태가 아닙니다. 즉, 디스패처 노드에 저장된 자격 증명이 다른 작업자 노드와 동기화되지 않습니다. 디스패처 노드에서 작업자 노드로 장애 조치(failover)가 발생하지만 자격 증명이 이전 디스패처 노드에만 존재하는 경우 자격 증명에 액세스하려고 하면 작업이 실패하고 위의 오류가 발생합니다.

#### <a name="resolution"></a>해결 방법

이 문제를 방지하는 유일한 방법은 두 노드가 자격 증명 동기화 상태에 있도록 하는 것입니다. 동기화되지 않은 경우 새 디스패처에 대한 자격 증명을 다시 입력해야 합니다.


### <a name="cant-choose-the-certificate-because-the-private-key-is-missing"></a>프라이빗 키가 없어서 인증서를 선택할 수 없음

#### <a name="symptoms"></a>증상

* PFX 파일을 인증서 저장소에 가져왔습니다.
* IR 구성 관리자 UI를 통해 인증서를 선택하면 다음과 같은 오류 메시지가 표시됩니다.

   “인트라넷 통신 암호화 모드를 변경하지 못했습니다. 인증서 '\<*certificate name*>'에 키 교환이 가능한 프라이빗 키가 없거나 프로세스에 프라이빗 키에 대한 액세스 권한이 없는 것일 수 있습니다. 자세한 내용은 내부 예외를 참조하세요.”

    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png" alt-text="&quot;개인 키 누락 오류 메시지를 표시 하는 Integration Runtime Configuration Manager 설정 창의 &quot; 스크린샷":::

#### <a name="cause"></a>원인

- 사용자 계정이 권한 수준이 낮아 프라이빗 키에 액세스할 수 없습니다.
- 인증서가 키 교환이 아닌 서명으로 생성되었습니다.

#### <a name="resolution"></a>해결 방법

* UI를 작동하려면 프라이빗 키에 액세스할 수 있는 적절한 권한이 있는 계정을 사용합니다.  
* 다음 명령을 실행하여 인증서를 가져옵니다.
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```

### <a name="self-hosted-integration-runtime-nodes-out-of-the-sync-issue"></a>동기화 이외의 자체 호스팅 통합 런타임 노드 문제

#### <a name="symptoms"></a>증상

자체 호스팅 통합 런타임 노드는 노드 간에 자격 증명을 동기화하려고 하지만 프로세스에서 중단되고 잠시 후 아래 오류 메시지가 발생합니다.

"통합 런타임(자체 호스팅) 노드가 노드 간에 자격 증명을 동기화하려고 합니다. 이 작업은 몇 분 정도 걸릴 수 있습니다."

>[!Note]
>이 오류가 10분 넘게 표시되면 디스패처 노드와의 연결을 확인하세요.

#### <a name="cause"></a>원인

그 이유는 작업자 노드가 프라이빗 키에 액세스할 수 없기 때문입니다. 이는 아래 자체 호스팅 통합 런타임 로그에서 확인할 수 있습니다.

`[14]0460.3404::05/07/21-00:23:32.2107988 [System] A fatal error occurred when attempting to access the TLS server credential private key. The error code returned from the cryptographic module is 0x8009030D. The internal error state is 10001.`

연결된 서비스에서 서비스 주체 인증을 사용하는 경우에는 동기화 프로세스에 문제가 없습니다. 그러나 인증 유형을 계정 키로 전환하면 동기화 문제가 시작됩니다. 자체 호스팅 통합 런타임 서비스가 서비스 계정(NT SERVICE\DIAHostService)에서 실행되고 프라이빗 키 권한에 추가되어야 하기 때문입니다.
 

#### <a name="resolution"></a>해결 방법

이 문제를 해결하려면 프라이빗 키 권한에 자체 호스팅 통합 런타임 서비스 계정(NT SERVICE\DIAHostService)을 추가해야 합니다. 다음 단계를 적용할 수 있습니다.

1. MMC(Microsoft Management Console) 실행 명령을 엽니다.

    :::image type="content" source="./media/self-hosted-integration-runtime-troubleshoot-guide/management-console-run-command.png" alt-text="MMC 실행 명령을 보여 주는 스크린샷":::

1. MMC 창에서 다음 단계를 적용합니다.

    :::image type="content" source="./media/self-hosted-integration-runtime-troubleshoot-guide/add-service-account-to-private-key-1.png" alt-text="프라이빗 키 권한에 자체 호스팅 IR 서비스 계정을 추가하는 두 번째 단계를 보여 주는 스크린샷" lightbox="./media/self-hosted-integration-runtime-troubleshoot-guide/add-service-account-to-private-key-1-expanded.png":::

    1. **파일** 을 선택합니다.
    1. 드롭다운 메뉴에서 **스냅인 추가/제거** 를 선택합니다.
    1. "사용 가능한 스냅인" 창에서 **인증서** 를 선택합니다.
    1. **추가** 를 선택합니다.
    1. "인증서 스냅인" 팝업 창에서 **컴퓨터 계정** 을 선택합니다.
    1. **다음** 을 선택합니다.
    1. "컴퓨터 선택" 창에서 **로컬 컴퓨터: 이 콘솔이 실행되고 있는 컴퓨터** 를 선택합니다.
    1. **마침** 을 선택합니다.
    1. "스냅인 추가 또는 제거" 창에서 **확인** 을 선택합니다.

1. MMC 창에서 다음 단계를 진행합니다.

    :::image type="content" source="./media/self-hosted-integration-runtime-troubleshoot-guide/add-service-account-to-private-key-2.png" alt-text="프라이빗 키 권한에 자체 호스팅 IR 서비스 계정을 추가하는 세 번째 단계를 보여 주는 스크린샷" lightbox="./media/self-hosted-integration-runtime-troubleshoot-guide/add-service-account-to-private-key-2-expanded.png":::

    1. 왼쪽 폴더 목록에서 **콘솔 루트 -> 인증서(로컬 컴퓨터) -> 개인 -> 인증서** 를 선택합니다.
    1. **Microsoft Intune Beta MDM** 을 마우스 오른쪽 단추로 클릭합니다.
    1. 드롭다운 목록에서 **모든 작업** 을 선택합니다.
    1. **프라이빗 키 관리** 를 선택합니다.
    1. "그룹 또는 사용자 이름"에서 **추가** 를 선택합니다.
    1. **NT SERVICE\DIAHostService** 를 선택하여 이 인증서에 대한 모든 제어 액세스 권한을 부여하고 적용하여 안전하게 합니다. 
    1. **이름 확인** 을 선택한 다음, **확인** 을 선택합니다.
    1. "권한" 창에서 **적용** 을 선택한 다음 **확인** 을 선택합니다.

## <a name="self-hosted-ir-setup"></a>자체 호스팅 IR 설정

### <a name="integration-runtime-registration-error"></a>통합 런타임 등록 오류 

#### <a name="symptoms"></a>증상

경우에 따라 다음과 같은 이유 중 하나로 다른 계정에서 자체 호스팅 IR을 실행할 수 있습니다.
- 회사 정책은 서비스 계정을 허용하지 않습니다.
- 일부 인증이 필요합니다.

서비스 창에서 서비스 계정을 변경한 후에 통합 런타임의 작동이 중지되고 다음과 같은 오류 메시지가 표시될 수 있습니다.

“등록하는 동안 Integration Runtime(자체 호스팅) 노드에 오류가 발생했습니다. Integration Runtime(자체 호스팅) 호스트 서비스에 연결할 수 없습니다.”

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png" alt-text="IR 등록 오류를 표시하는 Integration Runtime 구성 관리자 창의 스크린샷":::

#### <a name="cause"></a>원인

대부분의 리소스는 서비스 계정에만 부여됩니다. 서비스 계정을 다른 계정으로 변경하는 경우 모든 종속 리소스의 권한은 그대로 유지됩니다.

#### <a name="resolution"></a>해결 방법

통합 런타임 이벤트 로그로 이동하여 오류를 확인합니다.

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png" alt-text="런타임 오류가 발생했음을 보여 주는 IR 이벤트 로그의 스크린샷":::

* 이벤트 로그의 오류가 'UnauthorizedAccessException'인 경우 다음을 수행합니다.

    1. Windows 서비스 패널에서 *DIAHostService* 로그온 서비스 계정을 확인합니다.

        :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png" alt-text="로그온 서비스 계정 속성 창의 스크린샷":::

    1. 로그온 서비스 계정에 *%programdata%\Microsoft\DataTransfer\DataManagementGateway* 폴더에 대한 읽기/쓰기 권한이 있는지 확인합니다.

        - 기본적으로 서비스 로그온 계정을 변경하지 않은 경우 읽기/쓰기 권한이 있어야 합니다.

            :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png" alt-text="권한 추가 창의 스크린샷":::

        - 서비스 로그온 계정을 변경한 경우 다음을 수행하여 문제를 완화합니다.
 
            a. 현재 자체 호스팅 IR을 완전히 제거합니다.   
            b. 자체 호스팅 IR 비트를 설치합니다.  
            다. 다음을 수행하여 서비스 계정을 변경합니다.  

             i. 자체 호스팅 IR 설치 폴더로 이동한 다음 *Microsoft Integration Runtime\4.0\Shared* 폴더로 전환합니다.  
             ii. 상승된 권한을 사용하여 명령 프롬프트 창을 엽니다. *\<user>* 및 *\<password>* 를 사용자의 이름 및 암호로 바꾸고 다음 명령을 실행합니다.   
                `dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"`  
             iii. LocalSystem 계정으로 변경하려면 이 계정에 올바른 형식 `dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""`을 사용해야 합니다.  
                `dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""` 형식은 사용하지 마세요.  
             iv. 로컬 시스템의 권한이 관리자보다 높기 때문에 필요에 따라 '서비스'에서 직접 변경할 수도 있습니다.  
             v. IR 서비스 로그온 계정의 로컬/도메인 사용자를 사용할 수 있습니다.            

            d. 통합 런타임을 등록합니다.

* 오류가 "서비스 'Integration Runtime 서비스'(DIAHostService)를 시작하지 못했습니다. 시스템 서비스를 시작할 수 있는 권한이 있는지 확인하세요."인 경우 다음을 수행합니다.

    1. Windows 서비스 패널에서 *DIAHostService* 로그온 서비스 계정을 확인합니다.
    
        :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png" alt-text="&quot; &quot; 서비스 계정에 대 한 로그온 창의 스크린샷":::

    1. 로그온 서비스 계정에 Windows 서비스를 시작할 수 있는 **서비스로 로그온** 권한이 있는지 확인합니다.

        :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png" alt-text="&quot;서비스로 로그온 &quot; 속성 창의 스크린샷":::

#### <a name="more-information"></a>자세한 정보

위의 두 해결 패턴이 모두 적용되지 않는다면 다음 Windows 이벤트 로그를 수집해 봅니다. 
- 애플리케이션 및 서비스 로그 > Integration Runtime
- Windows 로그 > 애플리케이션

### <a name="cant-find-the-register-button-to-register-a-self-hosted-ir"></a>자체 호스팅 IR을 등록하기 위한 등록 단추를 찾을 수 없음    

#### <a name="symptoms"></a>증상

자체 호스팅 IR을 등록할 때 구성 관리자 창에 **등록** 단추가 표시되지 않습니다.

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png" alt-text="통합 런타임 노드가 등록되지 않은 메시지를 표시하는 구성 관리자 창의 스크린샷":::

#### <a name="cause"></a>원인

Integration Runtime 3.0 릴리스부터 기존 통합 런타임 노드에 대한 **등록** 단추가 제거되어 더 깔끔하고 안전하게 환경을 사용할 수 있습니다. 노드가 온라인 상태인지 여부에 관계없이 통합 런타임에 등록된 경우 이전 노드를 제거한 다음 노드를 설치하고 등록하여 다른 통합 런타임에 다시 등록합니다.

#### <a name="resolution"></a>해결 방법

1. 제어판에서 기존 통합 런타임을 제거합니다.

    > [!IMPORTANT] 
    > 다음 프로세스에서 **예** 를 선택합니다. 제거 프로세스 동안 데이터를 유지하지 마세요.

    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png" alt-text="&quot; &quot; 통합 런타임에서 모든 사용자 데이터를 삭제 하는 예 단추의 스크린샷":::

1. 통합 런타임 설치 관리자 MSI 파일이 없는 경우 [다운로드 센터](https://www.microsoft.com/en-sg/download/details.aspx?id=39717)로 이동하여 최신 통합 런타임을 다운로드합니다.
1. MSI 파일을 설치하고 통합 런타임을 등록합니다.


### <a name="unable-to-register-the-self-hosted-ir-because-of-localhost"></a>localhost로 인해 자체 호스팅 IR을 등록할 수 없음    

#### <a name="symptoms"></a>증상

get_LoopbackIpOrName을 사용하는 경우 새 컴퓨터에 자체 호스팅 IR을 등록할 수 없습니다.

**디버그:** 런타임 오류가 발생했습니다.
'Microsoft.DataTransfer.DIAgentHost.DataSourceCache'의 형식 이니셜라이저가 예외를 throw했습니다.
데이터베이스를 조회하는 동안 복구할 수 없는 오류가 발생했습니다.
 
**예외 세부 정보:** System.TypeInitializationException: 'Microsoft.DataTransfer.DIAgentHost.DataSourceCache'의 형식 이니셜라이저에서 예외를 throw했습니다. ---> System.Net.Sockets.SocketException: System.Net.Dns.GetAddrInfo(String name)에서 데이터베이스를 조회하는 동안 복구할 수 없는 오류가 발생했습니다.

#### <a name="cause"></a>원인

이 문제는 일반적으로 localhost를 확인할 때 발생합니다.

#### <a name="resolution"></a>해결 방법

localhost IP 주소 127.0.0.1을 사용하여 파일을 호스트하고 문제를 해결합니다.

### <a name="self-hosted-setup-failed"></a>자체 호스팅 설치 실패    

#### <a name="symptoms"></a>증상

기존 IR을 제거하거나, 새 IR을 설치하거나, 기존 IR을 새 IR로 업그레이드할 수 없습니다.

#### <a name="cause"></a>원인

통합 런타임 설치는 Windows Installer 서비스에 따라 달라집니다. 다음과 같은 이유로 설치 문제가 발생할 수 있습니다.
- 사용 가능한 디스크 공간이 부족합니다.
- 권한이 부족합니다.
- Windows NT 서비스가 잠겨 있습니다.
- CPU 사용률 너무 높습니다.
- MSI 파일은 저속 네트워크 위치에서 호스트됩니다.
- 일부 시스템 파일 또는 레지스트리가 실수로 변경되었습니다.

### <a name="the-ir-service-account-failed-to-fetch-certificate-access"></a>IR 서비스 계정이 인증서 액세스 권한을 가져오지 못함

#### <a name="symptoms"></a>증상

Microsoft Integration Runtime 구성 관리자를 통해 자체 호스팅 IR을 설치하면 신뢰할 수 있는 CA(인증 기관)가 포함된 인증서가 생성됩니다. 두 노드 간의 통신을 암호화하기 위해 인증서를 적용할 수 없으며 다음과 같은 오류 메시지가 표시됩니다. 

"인트라넷 통신 암호화 모드를 변경하지 못함: 인증서 '\<*certificate name*>'에 대한 액세스를 Integration Runtime 서비스 계정에 부여하지 못했습니다. 오류 코드 103"

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-account-certificate-error.png" alt-text="오류 메시지를 표시 하는 &quot; 스크린샷 ... Integration Runtime 서비스 계정 인증서 액세스를 부여 하지 못했습니다 &quot; .":::

#### <a name="cause"></a>원인

인증서가 아직 지원되지 않는 KSP(키 스토리지 공급자) 스토리지를 사용하고 있습니다. 현재는 자체 호스팅 IR이 CSP(암호화 서비스 공급자) 스토리지만 지원합니다.

#### <a name="resolution"></a>해결 방법

이 경우 CSP 인증서를 사용하는 것이 좋습니다.

**해결 방법 1** 

인증서를 가져오려면 다음 명령을 실행합니다.

`Certutil.exe -CSP "CSP or KSP" -ImportPFX FILENAME.pfx`

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/use-certutil.png" alt-text="인증서를 가져오기 위한 certutil 명령의 스크린샷":::

**해결 방법 2** 

인증서를 변환하려면 다음 명령을 실행합니다.

`openssl pkcs12 -in .\xxxx.pfx -out .\xxxx_new.pem -password pass: <EnterPassword>`
`openssl pkcs12 -export -in .\xxxx_new.pem -out xxxx_new.pfx`

변환 전후:

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/before-certificate-change.png" alt-text="인증서 변환 전 스크린샷":::

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/after-certificate-change.png" alt-text="인증서 변환 후 스크린샷":::

### <a name="self-hosted-integration-runtime-version-5x"></a>자체 호스팅 통합 런타임 버전 5.x
자체 호스팅 통합 런타임 버전 5.x로 업그레이드하려면 **.NET Framework 런타임 4.7.2** 이상이 필요합니다. 다운로드 페이지에서 최신 4.x 버전 및 두 개의 최신 5.x 버전에 대한 다운로드 링크를 찾을 수 있습니다. 

Azure Data Factory v2 및 Azure Synapse 고객의 경우:
- 자동 업데이트가 설정되어 있고 이미 .NET Framework 런타임을 4.7.2 이상으로 업그레이드한 경우 자체 호스팅 통합 런타임이 최신 5.x 버전으로 자동 업그레이드됩니다.
- 자동 업데이트가 설정되어 있지만 아직 .NET Framework 런타임을 4.7.2 이상으로 업그레이드하지 않은 경우 자체 호스팅 통합 런타임이 최신 5.x 버전으로 자동 업그레이드되지 않습니다. 자체 호스팅 통합 런타임은 현재 4.x 버전 그대로 유지됩니다. 포털 및 자체 호스팅 통합 런타임 클라이언트에서 .NET Framework 런타임 업그레이드에 대한 경고를 볼 수 있습니다.
- 자동 업데이트가 해제되어 있고 이미 .NET Framework 런타임을 4.7.2 이상으로 업그레이드한 경우에는 수동으로 최신 5.x를 다운로드하여 컴퓨터에 설치할 수 있습니다.
- 자동 업데이트가 해제되어 있고 .NET Framework 런타임을 4.7.2 이상으로 업그레이드하지 않은 경우, 자체 호스팅 통합 런타임 5.x를 수동으로 설치하고 키를 등록하려고 하면 .NET Framework 런타임 버전을 먼저 업그레이드해야 합니다.


Azure Data Factory v1 고객의 경우:
- 자체 호스팅 통합 런타임 5.x는 Azure Data Factory v1을 지원하지 않습니다.
- 자체 호스팅 통합 런타임은 최신 4.x 버전으로 자동 업그레이드됩니다. 최신 버전의 4.x는 만료되지 않습니다. 
- 자체 호스팅 통합 런타임 5.x를 수동으로 설치하고 키를 등록하는 경우 자체 호스팅 통합 런타임 5.x는 Azure Data Factory v1을 지원하지 않는다는 알림이 표시됩니다.


## <a name="self-hosted-ir-connectivity-issues"></a>자체 호스팅 IR 연결 문제

### <a name="self-hosted-integration-runtime-cant-connect-to-the-cloud-service"></a>자체 호스팅 통합 런타임이 클라우드 서비스에 연결할 수 없음

#### <a name="symptoms"></a>증상

자체 호스팅 통합 런타임을 등록하려고 하면 구성 관리자에 다음 오류 메시지가 표시됩니다.

“등록하는 동안 Integration Runtime(자체 호스팅) 노드에 오류가 발생했습니다.”

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png" alt-text="&quot;등록 메시지를 실행 하는 동안 Integration Runtime(자체 호스팅) 노드의 스크린샷에 오류가 발생 했습니다 &quot; .":::

#### <a name="cause"></a>원인 

자체 호스팅 IR이 서비스 백 엔드에 연결할 수 없습니다. 이 문제는 일반적으로 방화벽의 네트워크 설정 때문에 발생합니다.

#### <a name="resolution"></a>해결 방법

1. 통합 런타임 서비스가 실행 중인지 확인합니다. 그렇다면 2단계로 이동합니다.
    
   :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png" alt-text="자체 호스팅 IR 서비스가 실행 중임을 보여 주는 스크린샷":::
    
1. 자체 호스팅 IR에 프록시가 구성(기본 설정)되지 않은 경우 자체 호스팅 통합 런타임이 설치된 컴퓨터에서 다음 PowerShell 명령을 실행합니다.

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```      

   > [!NOTE]     
   > 서비스 URL은 데이터 팩터리 또는 Synapse 작업 영역 인스턴스의 위치에 따라 다를 수 있습니다. 서비스 URL을 찾으려면 데이터 팩터리 또는 Azure Synapse 인스턴스에 있는 UI의 관리 페이지를 사용하여 **통합 런타임** 을 찾고 자체 호스팅 IR을 클릭하여 편집합니다.  여기에서 **노드** 탭을 선택하고 **서비스 URL 보기** 를 클릭합니다.
            
    예상되는 응답은 다음과 같습니다.
            
    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png" alt-text="PowerShell 명령 응답의 스크린샷":::
            
1. 예상한 응답을 받지 못한 경우 상황에 따라 다음 방법 중 하나를 사용합니다.
            
    * "원격 이름을 확인할 수 없습니다"라는 메시지가 표시되는 경우 DNS(Domain Name System) 문제가 있는 것입니다. 문제를 해결하려면 네트워크 팀에 문의하세요.
    * "ssl/tls 인증서를 신뢰할 수 없습니다."라는 메시지가 표시되면 [인증서를 검사](https://wu2.frontend.clouddatahub.net/)하여 컴퓨터에서 인증서를 신뢰할 수 있는지 확인하고 인증서 관리자를 사용하여 공용 인증서를 설치합니다. 이 작업을 수행하면 문제가 완화됩니다.
    * **Windows** > **이벤트 뷰어(로그)**  > **애플리케이션 및 서비스 로그** > **Integration Runtime** 으로 이동하고 DNS, 방화벽 규칙 또는 회사 네트워크 설정으로 인해 발생한 오류가 있는지 확인합니다. 이러한 오류가 발생하면 연결을 강제로 닫습니다. 모든 회사는 고유하게 네트워크 설정을 사용자 지정하므로 네트워크 팀에 문의하여 이러한 문제를 해결하세요.

1. 자체 호스팅 Integration Runtime에 "프록시"가 구성된 경우 프록시 서버에서 서비스 엔드포인트에 액세스할 수 있는지 확인합니다. 샘플 명령은 [PowerShell, 웹 요청 및 프록시](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies)를 참조하세요.    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

예상되는 응답은 다음과 같습니다.
            
:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png" alt-text="필요한 Powershell 명령 응답의 스크린샷":::

> [!NOTE] 
> 프록시 고려 사항:
> * 수신 허용 - 받는 사람 목록에 프록시 서버를 추가해야 하는지 여부를 확인합니다. 추가해야 하는 경우 [이러한 도메인](./data-movement-security-considerations.md#firewall-requirements-for-on-premisesprivate-network)이 수신 허용 - 받는 사람 목록에 있는지 확인합니다.
> * 프록시 서버에서 SSL/TLS 인증서 "wu2.frontend.clouddatahub.net/"을 신뢰할 수 있는지 확인합니다.
> * 프록시에서 Active Directory 인증을 사용하는 경우 서비스 계정을 프록시에 "Integration Runtime Service"로 액세스할 수 있는 사용자 계정으로 변경합니다.

### <a name="error-message-self-hosted-integration-runtime-nodelogical-self-hosted-ir-is-in-inactive-running-limited-state"></a>오류 메시지: 자체 호스팅 통합 런타임 노드/논리적 자체 호스팅 IR이 비활성/'실행 중(제한됨)' 상태입니다.

#### <a name="cause"></a>원인 

다음 스크린샷에 표시된 것처럼 자체 호스팅 IR 노드가 **비활성** 상태일 수 있습니다.

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png" alt-text="비활성 상태의 자체 호스팅 IR 노드를 보여 주는 스크린샷":::

이 동작은 노드가 서로 통신할 수 없는 경우에 발생합니다.

#### <a name="resolution"></a>해결 방법

1. 노드 호스팅 VM(가상 머신)에 로그인합니다. **애플리케이션 및 서비스 로그** > **Integration Runtime** 에서 이벤트 뷰어를 열고 오류 로그를 필터링합니다.

1. 오류 로그에 다음 오류가 포함되어 있는지 확인합니다. 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. 이 오류가 표시되면 명령 프롬프트 창에서 다음 명령을 실행합니다. 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. 다음 스크린샷에 표시된 "호스트에 대한 연결을 열 수 없습니다." 명령줄 오류를 수신하는 경우 이 문제를 해결하려면 IT 부서에 문의하세요. 성공적으로 텔넷한 후에도 통합 런타임 노드 상태에 문제가 있는 경우 Microsoft 지원에 문의하세요.
        
   :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png" alt-text="&quot;호스트 명령줄 오류에 대 한 연결을 열 수 없습니다 &quot; .":::
        
1. 오류 로그에 다음 항목이 포함되어 있는지 확인합니다.

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. 이 문제를 해결하려면 다음 방법을 하나 또는 둘 다 시도해 보세요.
    - 모든 노드를 동일한 도메인에 배치합니다.
    - 모든 호스트된 VM의 호스트 파일에 있는 호스트에 IP를 추가합니다.

### <a name="connectivity-issue-between-the-self-hosted-ir-and-your-data-factory-or-azure-synapse-instance-or-the-self-hosted-ir-and-the-data-source-or-sink"></a>자체 호스팅 IR과 데이터 팩터리 또는 Azure Synapse 인스턴스 간 또는 자체 호스팅 IR과 데이터 원본 또는 싱크 간 연결 문제

네트워크 연결 문제를 해결하려면 자체 호스팅 IR의 실제 사례에 Netmon(Microsoft 네트워크 모니터) 도구를 적용하기 전에 네트워크 추적을 수집하고, 이를 사용하는 방법을 이해하고, [Netmon 추적을 분석](#analyze-the-netmon-trace)하는 방법을 알고 있어야 합니다.

#### <a name="symptoms"></a>증상

다음 스크린샷에 표시된 것과 같이 자체 호스팅 IR과 데이터 팩터리 또는 Azure Synapse 인스턴스 사이나 자체 호스팅 IR과 데이터 원본 또는 싱크 사이의 특정 연결 문제를 해결해야 하는 경우도 있습니다. 

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png" alt-text="&quot;처리 된 HTTP 요청 실패 메시지 스크린샷 &quot;":::

두 경우 모두 다음과 같은 오류가 발생한 것일 수 있습니다.

* "다음 오류가 발생하여 복사하지 못함:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: 'IP address'"

* "하나 이상의 오류가 발생했습니다. 요청을 보내는 중 오류가 발생했습니다. 기본 연결이 닫혔습니다. 수신 중에 예상치 못한 오류가 발생했습니다. 전송 연결에서 데이터를 읽을 수 없습니다. 원격 호스트가 기존 연결을 강제로 닫았습니다. 기존 연결은 원격 호스트 활동 ID에 의해 강제로 닫혔습니다."

#### <a name="resolution"></a>해결 방법

위의 오류가 발생하면 이 섹션의 지침에 따라 문제를 해결합니다.

- 분석을 위해 Netmon 추적을 수집합니다. 

    1. 필터를 설정하여 서버에서 클라이언트 쪽으로 다시 설정을 확인할 수 있습니다. 다음 예제 스크린샷에서 서버 쪽이 Data Factory 서버임을 확인할 수 있습니다.

        :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png" alt-text="Data factory 서버의 스크린샷.":::

    1. 다시 설정 패키지를 가져오는 경우 TCP(Transmission Control Protocol)를 따라 대화를 찾을 수 있습니다.

        :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png" alt-text="TCP 대화의 스크린샷":::

    1. 필터를 제거하여 클라이언트와 아래 Data Factory 서버 간의 대화를 가져옵니다.

        :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png" alt-text="대화 세부 정보의 스크린샷":::

- 수집한 Netmon 추적을 분석하면 TTL(Time to Live) 합계가 64임을 알 수 있습니다. [IP TTL(Time To Live) 및 홉 제한 기본 사항](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) 문서에 설명된 대로, 다음 목록에서 추출된 값에 따라 Linux 시스템이 패키지를 다시 설정하고 연결을 끊는 것임을 확인할 수 있습니다.

    기본 TTL 및 홉 제한 값은 다음과 같이 운영 체제마다 다릅니다.
    - Linux 커널 2.4(2001년경): 255(TCP, UDP(사용자 데이터 그램 프로토콜) 및 ICMP(Internet Control Message Protocol))
    - Linux 커널 4.10(2015): 64(TCP, UDP, ICMP)
    - Windows XP(2001): 128(TCP, UDP, ICMP)
    - Windows 10(2015): 128(TCP, UDP, ICMP)
    - Windows Server 2008: 128(TCP, UDP, ICMP)
    - Windows Server 2019(2018): 128(TCP, UDP, ICMP)
    - macOS(2001): 64(TCP, UDP, ICMP)

    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png" alt-text="TTL 값 61을 보여 주는 스크린샷":::
    
    위의 예제에서 TTL은 64가 아닌 61로 표시됩니다. 네트워크 패키지가 대상에 도달하면 라우터 또는 네트워크 디바이스와 같은 다양한 홉을 통과해야 하기 때문입니다. 라우터 또는 네트워크 디바이스 수를 공제하여 최종 TTL을 계산합니다.
    
    이 경우 TTL이 64인 Linux 시스템에서 다시 설정이 전송될 수 있다는 것을 알 수 있습니다.

-  다시 설정 디바이스의 위치를 확인하려면 자체 호스팅 IR에서 네 번째 홉을 확인합니다.
 
    Linux 시스템 A의 네트워크 패키지(TTL 64) -> B TTL 64 - 1 = 63 -> C TTL 63 - 1 = 62 -> TTL 62 - 1 = 61 자체 호스팅 IR

- 이상적인 상황에서 TTL 홉 수는 128입니다. 이는 Windows 운영 체제에서 데이터 팩터리 인스턴스를 실행하고 있음을 의미합니다. 다음 예제와 같이 '128 - 107 = 21홉'입니다. 이는 패키지에 대한 21개의 홉이 TCP 3 핸드셰이크 중에 데이터 팩터리 인스턴스에서 자체 호스팅 IR로 전송되었음을 의미합니다.
 
    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png" alt-text="TTL 값 107을 보여 주는 스크린샷":::

    따라서 네트워크 팀과 협력하여 자체 호스팅 IR에서 네 번째 홉이 무엇인지 확인해야 합니다. 네 번째 홉이 방화벽인 경우 Linux 시스템과 마찬가지로 로그를 확인하여 TCP 3 핸드셰이크 이후 디바이스에서 패키지를 다시 설정하는 이유를 확인합니다. 
    
    조사할 위치를 모를 경우 문제가 발생하는 시간에 자체 호스팅 IR 및 방화벽 모두에서 Netmon 추적을 가져오려고 시도합니다. 이 방법은 패키지를 다시 설정하고 연결을 끊은 디바이스를 파악하는 데 도움이 됩니다. 이 경우에도 네트워크 팀과의 협력이 필요합니다.

### <a name="analyze-the-netmon-trace"></a>Netmon 추적 분석

> [!NOTE] 
> 다음 지침은 Netmon 추적에 적용됩니다. 현재는 Netmon 추적이 지원되지 않기 때문에 이를 위해 Wireshark를 사용할 수 있습니다.

수집된 Netmon 추적을 사용하여 **8.8.8.8 888** 을 텔넷하려고 하면 다음 스크린샷과 같은 추적이 표시됩니다.

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png" alt-text="&quot;포트 888 오류 메시지에서 호스트에 대 한 연결을 열 수 없음을 보여 주는 스크린샷 &quot;":::

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png" alt-text="Netmon 추적에 대한 설명을 보여 주는 스크린샷":::
 

위의 이미지는 포트 **888** 에서 **8.8.8.8** 서버 쪽에 TCP 연결을 만들 수 없다는 것을 보여 주기 때문에 두 개의 **SynReTransmit** 추가 패키지를 볼 수 있습니다. 원본 **SELF-HOST2** 는 첫 번째 패키지를 사용하여 **8.8.8.8** 에 연결할 수 없으므로 계속 연결을 시도합니다.

> [!TIP]
> 이 연결을 완료하려면 다음 해결 방법을 시도해 보세요.
> 1. **필터 로드** > **표준 필터** > **주소** > **IPv4 주소** 를 선택합니다.
> 1. 필터를 적용하려면 **IPv4.Address == 8.8.8.8** 을 입력한 다음 **적용** 을 선택합니다. 그런 다음 로컬 컴퓨터에서 대상 **8.8.8.8** 로의 통신을 확인해야 합니다.

:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png" alt-text="필터 주소 보여 주는 스크린샷":::
        
:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png" alt-text="더 많은 필터 주소를 보여 주는 스크린샷":::

성공적인 시나리오는 다음 예제에 나와 있습니다. 

- 문제 없이 **8.8.8.8 53** 을 텔넷할 수 있는 경우 TCP 3 핸드셰이크가 성공하고 세션이 TCP 4 핸드셰이크로 완료됩니다.

    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png" alt-text="성공적인 연결 시나리오를 보여 주는 스크린샷":::
     
    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png" alt-text="성공적인 연결 시나리오의 세부 정보를 보여 주는 스크린샷":::

- 위의 TCP 3 핸드셰이크는 다음 워크플로를 생성합니다.

    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png" alt-text="TCP 3 핸드셰이크 워크플로 다이어그램":::
 
- 세션을 완료하는 TCP 4 핸드셰이크는 다음 워크플로에 설명되어 있습니다.

    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png" alt-text="TCP 4 핸드셰이크 세부 정보의 스크린샷":::

    :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png" alt-text="TCP 4 핸드셰이크 워크플로 다이어그램"::: 

### <a name="microsoft-email-notification-about-updating-your-network-configuration"></a>네트워크 구성 업데이트에 대한 Microsoft 이메일 알림

다음과 같이 Azure Data Factory의 새 IP 주소를 사용하여 통신할 수 있도록 2020년 11월 8일까지 네트워크 구성을 업데이트할 것을 권장하는 이메일 알림을 받을 수 있습니다.

   :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png" alt-text="네트워크 구성 업데이트를 요청하는 Microsoft 이메일 알림의 스크린샷":::

#### <a name="determine-whether-this-notification-affects-you"></a>이 알림의 영향을 받는지 여부 확인

이 알림은 다음 시나리오에 적용됩니다.

##### <a name="scenario-1-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-premises-behind-a-corporate-firewall"></a>시나리오 1: 회사 방화벽 뒤에서 온-프레미스로 실행되는 자체 호스팅 통합 런타임과의 아웃바운드 통신

영향을 받는지 여부를 확인하는 방법:

- [IP 주소에 대한 방화벽 구성 및 허용 목록 설정](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-addresses)에 설명된 방법을 사용하는 FQDN(정규화된 도메인 이름)을 기반으로 하는 방화벽 규칙을 정의하는 경우에는 영향을 ‘받지 않습니다’.

- 회사 방화벽에서 아웃바운드 IP 허용 목록을 명시적으로 사용하는 경우에는 영향을 ‘받습니다’.

   영향을 받는 경우 다음 작업을 수행합니다. 네트워크 인프라 팀에 2020년 11월 8일까지 최신 데이터 팩터리 IP 주소를 사용하도록 네트워크 구성을 업데이트하라고 알립니다. 최신 IP 주소를 다운로드하려면 [다운로드 가능한 JSON 파일을 사용하여 서비스 태그 검색](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)으로 이동합니다.

##### <a name="scenario-2-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-an-azure-vm-inside-a-customer-managed-azure-virtual-network"></a>시나리오 2: 고객 관리형 Azure 가상 네트워크 내에서 Azure VM에서 실행되는 자체 호스팅 통합 런타임과의 아웃바운드 통신

영향을 받는지 여부를 확인하는 방법:

- 자체 호스팅 통합 런타임을 포함하는 프라이빗 네트워크에 아웃바운드 NSG(네트워크 보안 그룹) 규칙이 있는지 확인합니다. 아웃바운드 제한이 없는 경우에는 영향을 받지 않습니다.

- 아웃바운드 규칙 제한이 있는 경우 서비스 태그를 사용하고 있는지 확인하세요. 서비스 태그를 사용하는 경우에는 영향을 받지 않습니다. 새 IP 범위는 기존 서비스 태그 아래에 있기 때문에 아무것도 변경하거나 추가할 필요가 없습니다. 

  :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png" alt-text="Data Factory를 대상으로 표시하는 대상 검사의 스크린샷":::

- Azure 가상 네트워크에 대한 NSG 규칙에서 아웃바운드 IP 주소 허용 목록을 명시적으로 사용하는 경우 영향을 ‘받습니다’.

   영향을 받는 경우 다음 작업을 수행합니다. 네트워크 인프라 팀에 2020년 11월 8일까지 최신 데이터 팩터리 IP 주소를 사용하도록 Azure 가상 네트워크에 대한 NSG 규칙을 업데이트하라고 알립니다. 최신 IP 주소를 다운로드하려면 [다운로드 가능한 JSON 파일을 사용하여 서비스 태그 검색](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)으로 이동합니다.

##### <a name="scenario-3-outbound-communication-from-ssis-integration-runtime-in-a-customer-managed-azure-virtual-network"></a>시나리오 3: 고객 관리형 Azure 가상 네트워크에서 SSIS Integration Runtime으로부터 아웃바운드 통신

영향을 받는지 여부를 확인하는 방법:

- SSIS(SQL Server Integration Services) Integration Runtime을 포함하는 프라이빗 네트워크에 아웃바운드 NSG 규칙이 있는지 확인합니다. 아웃바운드 제한이 없는 경우에는 영향을 받지 않습니다.

- 아웃바운드 규칙 제한이 있는 경우 서비스 태그를 사용하고 있는지 확인하세요. 서비스 태그를 사용하는 경우에는 영향을 받지 않습니다. 새 IP 범위는 기존 서비스 태그 아래에 있기 때문에 아무것도 변경하거나 추가할 필요가 없습니다.

- Azure 가상 네트워크에 대한 NSG 규칙에서 아웃바운드 IP 주소 허용 목록을 명시적으로 사용하는 경우 영향을 ‘받습니다’.

  영향을 받는 경우 다음 작업을 수행합니다. 네트워크 인프라 팀에 2020년 11월 8일까지 최신 데이터 팩터리 IP 주소를 사용하도록 Azure 가상 네트워크에 대한 NSG 규칙을 업데이트하라고 알립니다. 최신 IP 주소를 다운로드하려면 [다운로드 가능한 JSON 파일을 사용하여 서비스 태그 검색](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)으로 이동합니다.

### <a name="couldnt-establish-a-trust-relationship-for-the-ssltls-secure-channel"></a>SSL/TLS 보안 채널에 대해 트러스트 관계를 설정할 수 없음 

#### <a name="symptoms"></a>증상

자체 호스팅 IR이 Azure Data Factory 또는 Azure Synapse 서비스에 연결할 수 없습니다.

CustomLogEvent 테이블에서 자체 호스팅 IR 이벤트 로그 또는 클라이언트 알림 로그를 확인하면 다음과 같은 오류 메시지가 나타납니다.

"기본 연결이 닫혔습니다. SSL/TLS 보안 채널에 대한 트러스트 관계를 설정할 수 없습니다. 유효성 검사 절차에 따르면 원격 인증서가 잘못되었습니다."

서비스의 서버 인증서를 확인하는 가장 간단한 방법은 브라우저에서 서비스 URL을 여는 것입니다. 예를 들어 자체 호스팅 IR이 설치된 컴퓨터에서 [서버 인증서 확인 링크](https://eu.frontend.clouddatahub.net/)를 열고 서버 인증서 정보를 확인합니다.

  :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/server-certificate.png" alt-text="Azure Data Factory 서비스의 서버 인증서 확인 창을 보여 주는 스크린샷":::

  :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/certificate-path.png" alt-text="서버 인증 경로를 확인하는 창의 스크린샷":::

#### <a name="cause"></a>원인

이 문제의 경우 두 가지 가능한 이유가 있습니다.

- 이유 1: 자체 호스팅 IR이 설치된 머신에서 서비스 서버 인증서의 루트 CA를 신뢰할 수 없습니다. 
- 이유 2: 사용자 환경에서 프록시를 사용하고 서비스의 서버 인증서가 프록시로 대체되었는데, 자체 호스팅 IR이 설치된 머신에서 대체된 서버 인증서를 신뢰할 수 없습니다.

#### <a name="resolution"></a>해결 방법

- 이유 1: 자체 호스팅 IR이 설치된 머신에서 서비스의 서버 인증서 및 해당 인증서 체인을 신뢰하도록 합니다.
- 이유 2: 자체 호스팅 IR 머신에서 대체된 루트 CA를 신뢰하거나 서비스의 서버 인증서를 대체하지 않도록 프록시를 구성합니다.

Windows에서 인증서를 신뢰하는 방법에 대한 자세한 내용은 [신뢰할 수 있는 루트 인증서 설치](/skype-sdk/sdn/articles/installing-the-trusted-root-certificate)를 참조하세요.

#### <a name="additional-information"></a>추가 정보
DigiCert에서 서명된 새 SSL 인증서를 출시했습니다. DigiCert Global Root G2가 신뢰할 수 있는 루트 CA에 있는지 확인하세요.

  :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/trusted-root-ca-check.png" alt-text="신뢰할 수 있는 루트 인증 기관 디렉터리에 있는 DigiCert Global Root G2 폴더를 보여 주는 스크린샷":::

신뢰할 수 있는 루트 CA에 없으면 [여기에서 다운로드](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt )합니다. 


## <a name="next-steps"></a>다음 단계

문제 해결에 대한 도움이 필요한 경우 다음 리소스를 참조하세요.

*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](/answers/topics/azure-data-factory.html)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 페이지](/answers/topics/azure-data-factory.html)
*  [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
*  [매핑 데이터 흐름 성능 가이드](concepts-data-flow-performance.md)
