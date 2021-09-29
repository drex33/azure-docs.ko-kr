---
title: Purview 계정에 대한 프라이빗 엔드포인트 구성 문제 해결
description: 이 문서에서는 프라이빗 엔드포인트 구성과 관련된 Purview 계정 문제를 해결하는 방법을 설명합니다.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 073b4bf8c1be14aa26141e20d5f6d6f4abdf9ff1
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129208797"
---
# <a name="troubleshooting-private-endpoint-configuration-for-purview-accounts"></a>Purview 계정에 대한 프라이빗 엔드포인트 구성 문제 해결

> [!IMPORTANT]
> **15:30 2021 년 9 월 27 일 이전** 에 부서의 범위 계정에 대 한 _포털_ 개인 끝점을 만든 경우 [포털 전용 끝점에 대해 DNS를 다시 구성](./catalog-private-link.md#reconfigure-dns-for-portal-private-endpoints)에 설명 된 대로 필요한 작업을 수행 해야 합니다. **이러한 작업은 2021 년 10 월 11 일 이전에 완료 해야 합니다. 이렇게 하지 않으면 기존 포털 개인 끝점의 작동이 중지 됩니다**.

이 가이드는 Azure Purview용 프라이빗 엔드포인트 사용과 관련된 알려진 제한 사항을 요약하고 가장 일반적인 관련 문제를 해결하기 위한 단계 및 솔루션 목록을 제공합니다. 

## <a name="known-limitations"></a>알려진 제한 사항

- 현재 AWS 원본에서 작동하는 수집 프라이빗 엔드포인트를 지원하지 않습니다.
- 자체 호스팅 통합 런타임을 사용하여 Azure 다중 원본 검색은 지원되지 않습니다.
- Azure Integration Runtime을 사용하여 프라이빗 엔드포인트 뒤의 데이터 원본을 검사하는 것은 지원되지 않습니다.
- Azure Portal을 사용하면 이전 단계에서 설명한 Azure Purview 포털 환경을 통해 수집 프라이빗 엔드포인트를 만들 수 있습니다. Private Link 센터에서는 만들 수 없습니다.
- Azure 프라이빗 DNS 영역이 프라이빗 엔드포인트와 다른 구독에 있는 동안 기존 Azure DNS 영역 내부의 수집 프라이빗 엔드포인트에 대한 DNS A 레코드 만들기는 Azure Purview 포털 환경을 통해 지원되지 않습니다. 다른 구독의 대상 DNS 영역에 레코드를 수동으로 추가할 수 있습니다. 
- 자체 호스팅 통합 런타임 컴퓨터는 Azure Purview 수집 프라이빗 엔드포인트가 배포된 동일한 VNet에 배포되어야 합니다.
- 현재 공용 액세스가 차단된 프라이빗 엔드포인트가 구성되어 있는 Power BI 테넌트의 검사는 지원하지 않습니다.
- Private Link 서비스와 관련된 제한 사항은 [Azure Private Link 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits)을 참조하세요.

## <a name="recommended-troubleshooting-steps"></a>권장되는 문제 해결 단계  

1. Purview 계정에 대한 프라이빗 엔드포인트를 배포한 후에는 Azure 환경을 검토하여 프라이빗 엔드포인트 리소스가 성공적으로 배포되었는지 확인합니다. 시나리오에 따라 다음 Azure 프라이빗 엔드포인트 중 하나 이상이 Azure 구독에 배포되어야 합니다.

    |프라이빗 엔드포인트  |프라이빗 엔드포인트 할당 대상 | 예|
    |---------|---------|---------|
    |계정  |Azure  Purview 계정         |mypurview-private-account  |
    |포털     |Azure  Purview 계정         |mypurview-private-portal  |
    |수집     |관리형 스토리지 계정(Blob)         |mypurview-ingestion-blob  |
    |수집     |관리형 스토리지 계정(큐)         |mypurview-ingestion-queue  |
    |수집     |관리형 Event Hubs 네임스페이스         |mypurview-ingestion-namespace  |

2. 포털 프라이빗 엔드포인트가 배포된 경우 계정 프라이빗 엔드포인트도 배포해야 합니다.

3. Azure 부서의 범위 계정에서 포털 개인 끝점이 배포 되 고 공용 네트워크 액세스가 거부로 설정 된 경우 내부 네트워크에서 [Azure 부서의 범위 Studio](https://web.purview.azure.com/resource/) 를 시작 해야 합니다.
  <br>
    - 올바른 이름 확인을 검증하려면 **NSlookup.exe** 명령줄 도구를 사용하여 `web.purview.azure.com`을 쿼리할 수 있습니다. 결과는 포털 프라이빗 엔드포인트에 속하는 개인 IP 주소를 반환해야 합니다. 
    - 네트워크 연결을 확인하려면 모든 네트워크 테스트 도구를 사용하여 `web.purview.azure.com` 엔드포인트에서 포트 **443** 으로의 아웃바운드 연결을 테스트할 수 있습니다. 연결이 성공해야 합니다.    

3. Azure 프라이빗 DNS 영역을 사용하는 경우 필요한 Azure DNS 영역이 배포되고 각 프라이빗 엔드포인트에 대한 DNS(A) 레코드가 있는지 확인합니다.

4. 관리 컴퓨터에서 Purview 엔드포인트 및 purview 웹 URL에 대한 네트워크 연결 및 이름 확인을 테스트합니다. 계정 및 포털 프라이빗 엔드포인트가 배포된 경우 개인 IP 주소를 통해 엔드포인트를 확인해야 합니다.


    ```powershell
    Test-NetConnection -ComputerName web.purview.azure.com -Port 443
    ```

    개인 IP 주소를 통한 성공적인 아웃바운드 연결의 예:

    ```
    ComputerName     : web.purview.azure.com
    RemoteAddress    : 10.9.1.7
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.9.0.10
    TcpTestSucceeded : True
    ```

    ```powershell
    Test-NetConnection -ComputerName purview-test01.purview.azure.com -Port 443
    ```

    개인 IP 주소를 통한 성공적인 아웃바운드 연결의 예:

    ```
    ComputerName     : purview-test01.purview.azure.com
    RemoteAddress    : 10.9.1.8
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.9.0.10
    TcpTestSucceeded : True
    ```
    
5. 2021년 8월 18일 이후에 Azure Purview 계정을 만든 경우 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=39717)에서 최신 버전의 자체 호스팅 통합 런타임을 다운로드하여 설치해야 합니다.
   
6. 자체 호스팅 통합 런타임 VM에서 Purview 엔드포인트에 대한 네트워크 연결 및 이름 확인을 테스트합니다.

7. 자체 호스팅 통합 런타임에서 포트 443 및 개인 IP 주소를 통해 Blob 큐 및 이벤트 허브와 같은 Azure Purview 관리되는 리소스에 대한 네트워크 연결 및 이름 확인을 테스트합니다. 관리 되는 저장소 계정 및 Event Hubs 네임 스페이스를 Azure 부서의 범위 계정에 할당 된 해당 관리 되는 리소스 이름으로 바꿉니다.

    ```powershell
    Test-NetConnection -ComputerName `scansoutdeastasiaocvseab`.blob.core.windows.net -Port 443
    ```
    개인 IP 주소를 통해 관리되는 Blob Storage에 대한 성공적인 아웃바운드 연결의 예:

    ```
    ComputerName     : scansoutdeastasiaocvseab.blob.core.windows.net
    RemoteAddress    : 10.15.1.6
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.15.0.4
    TcpTestSucceeded : True
    ```

    ```powershell
    Test-NetConnection -ComputerName `scansoutdeastasiaocvseab`.queue.core.windows.net -Port 443
    ```
    개인 IP 주소를 통한 관리형 Queue Storage에 대한 성공적인 아웃바운드 연결의 예:

    ```
    ComputerName     : scansoutdeastasiaocvseab.blob.core.windows.net
    RemoteAddress    : 10.15.1.5
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.15.0.4
    TcpTestSucceeded : True
    ```

    ```powershell
    Test-NetConnection -ComputerName `Atlas-1225cae9-d651-4039-86a0-b43231a17a4b`.servicebus.windows.net -Port 443
    ```
    개인 IP 주소를 통한 eventhub 네임스페이스에 대한 성공적인 아웃바운드 연결의 예:

    ```
    ComputerName     : Atlas-1225cae9-d651-4039-86a0-b43231a17a4b.servicebus.windows.net
    RemoteAddress    : 10.15.1.4
    RemotePort       : 443
    InterfaceAlias   : Ethernet 2
    SourceAddress    : 10.15.0.4
    TcpTestSucceeded : True
    ```

8. 데이터 원본이 있는 네트워크에서 Purview 엔드포인트 및 관리되는 리소스 엔드포인트에 대한 네트워크 연결 및 이름 확인을 테스트합니다.

9.  데이터 원본이 온-프레미스 네트워크에 있는 경우 DNS 전달자 구성을 검토합니다. 데이터 원본이 있는 동일한 네트워크 내에서 자체 호스팅 통합 런타임, Azure Purview 엔드포인트 및 관리되는 리소스에 대한 이름 확인을 테스트합니다. 각 엔드포인트에 대한 DNS 쿼리에서 유효한 개인 IP 주소를 얻어야 합니다.
    
    자세한 내용은 [Azure Private Endpoint DNS 구성](../private-link/private-endpoint-dns.md)에서 [사용자 지정 DNS 서버가 없는 가상 네트워크 워크로드](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server) 및 [DNS 전달자를 사용하는 온-프레미스 워크로드](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder) 시나리오를 참조하세요.

10. 관리 컴퓨터 및 자체 호스팅 통합 런타임 VM이 온-프레미스 네트워크에 배포되고 환경에서 DNS 전달자를 설정한 경우 환경에서 DNS 및 네트워크 설정을 확인합니다. 

11. 수집 개인 끝점을 사용 하는 경우 자체 호스팅 통합 런타임이 부서의 범위 계정 내에 성공적으로 등록 되 고 자체 호스팅 통합 런타임 VM 및 [부서의 범위 Studio](https://web.purview.azure.com/resource/) 내에서 실행 되는 것으로 표시 되는지 확인 합니다.

## <a name="common-errors-and-messages"></a>일반적인 오류 및 메시지

### <a name="issue"></a>문제 
검사를 실행할 때 다음 오류 메시지가 나타납니다.

`Internal system error. Please contact support with correlationId:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx System Error, contact support.`

### <a name="cause"></a>원인 
이는 자체 호스팅 통합 런타임을 실행하는 VM과 Azure Purview의 관리되는 리소스 스토리지 계정 또는 이벤트 허브 간의 연결 또는 이름 확인과 관련된 문제를 나타낼 수 있습니다.

### <a name="resolution"></a>해결 방법 
자체 호스팅 통합 런타임을 실행하는 VM 간의 이름 확인의 유효성을 검사합니다.


### <a name="issue"></a>문제 
새 검사를 실행할 때 다음 오류 메시지가 나타날 수 있습니다.

  `message: Unable to setup config overrides for this scan. Exception:'Type=Microsoft.WindowsAzure.Storage.StorageException,Message=The remote server returned an error: (404) Not Found.,Source=Microsoft.WindowsAzure.Storage,StackTrace= at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndExecuteAsync[T](IAsyncResult result)`

### <a name="cause"></a>원인 
이는 자체 호스팅 통합 런타임의 이전 버전을 실행 중임을 나타낼 수 있습니다. 2021년 8월 18일 이후에 Azure Purview 계정을 만든 경우 자체 호스팅 통합 런타임 버전 5.9.7885.3을 사용해야 합니다.

### <a name="resolution"></a>해결 방법 
자체 호스팅 통합 런타임을 5.9.7885.3으로 업그레이드합니다.


### <a name="issue"></a>문제 
배포 중 Azure Policy 유효성 검사 오류로 인해 프라이빗 엔드포인트를 사용하는 Azure Purview 계정 배포가 실패했습니다.

### <a name="cause"></a>원인
이 오류는 Azure 구독에 필요한 Azure 리소스의 배포를 방해하는 기존 Azure Policy 할당이 있을 수 있음을 나타냅니다. 

### <a name="resolution"></a>해결 방법 
기존 Azure Policy 할당을 검토하고 Azure 구독에서 다음 Azure 리소스 배포가 허용되는지 확인합니다. 
   
> [!NOTE]
> 시나리오에 따라 다음 Azure 리소스 유형 중 하나 이상을 배포해야 할 수 있습니다. 
>  -   Azure Purview(Microsoft.Purview/Accounts)
>  -   프라이빗 엔드포인트(Microsoft.Network/privateEndpoints)
>  -   프라이빗 DNS 영역(Microsoft.Network/privateDnsZones)
>  -   이벤트 허브 네임스페이스(Microsoft.EventHub/namespaces)
>  -   스토리지 계정(Microsoft.Storage/StorageAccounts)


### <a name="issue"></a>문제
이 Purview 계정에 액세스할 수 있는 권한이 없습니다. 이 Purview 계정은 프라이빗 엔드포인트 뒤에 있습니다. Purview 계정의 프라이빗 엔드포인트에 대해 구성된 동일한 VNet(가상 네트워크)의 클라이언트에서 계정에 액세스하세요.

### <a name="cause"></a>원인 
사용자가 공개 엔드포인트에서 또는 **공용 네트워크 액세스** 가 **거부** 로 설정된 Azure Purview 공개 엔드포인트를 사용하여 Azure Purview에 연결하려고 합니다.

### <a name="resolution"></a>해결 방법
이 경우 Azure Purview Studio를 열려면 Azure Purview 포털 프라이빗 엔드포인트와 동일한 가상 네트워크에 배포된 컴퓨터를 사용하거나 하이브리드 연결이 허용되는 CorpNet에 연결된 VM을 사용합니다.

## <a name="next-steps"></a>다음 단계

해당 문서에 나열되어 있지 않거나 해결할 수 없는 문제의 경우 다음 채널 중 하나를 방문하여 지원을 받으세요.

- [Microsoft Q&A](/answers/topics/azure-purview.html)를 통해 전문가의 답변을 받으세요.
- [@AzureSupport](https://twitter.com/azuresupport)에 연결합니다. Twitter의 해당 공식 Microsoft Azure 리소스는 Azure 커뮤니티를 올바른 답변, 지원 및 전문가에게 연결하여 고객 환경을 개선하는 데 도움이 됩니다.
- 여전히 도움이 필요한 경우 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동하여 **지원 요청 제출** 을 선택합니다.
