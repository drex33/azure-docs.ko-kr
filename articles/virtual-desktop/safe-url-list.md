---
title: Azure Virtual Desktop 필수 URL 목록 - Azure
description: Azure Virtual Desktop 배포가 의도한 대로 작동하도록 차단해야 하는 URL 목록입니다.
author: Heidilohr
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 95081b661d4f33577df07420ab194ec48488221a
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768088"
---
# <a name="required-url-list"></a>필수 URL 목록

Azure Virtual Desktop을 배포하고 사용하려면 VM(가상 머신)에서 언제든지 액세스할 수 있도록 특정 URL을 차단 해제해야 합니다. 이 문서에는 Azure Virtual Desktop이 제대로 작동하기 위해 차단을 해제해야 하는 필수 URL이 나열되어 있습니다. 

>[!IMPORTANT]
>Azure Virtual Desktop은 이 문서에 나열된 URL을 차단하는 배포를 지원하지 않습니다.

## <a name="required-url-check-tool"></a>필수 URL 확인 도구

필수 URL 확인 도구는 URL의 유효성을 검사하고 가상 머신이 작동하는 데 필요한 URL에 액세스할 수 있는지 여부를 표시합니다. 그렇지 않은 경우 도구는 필요한 경우 액세스할 수 없는 URL을 차단 해제할 수 있도록 해당 URL을 나열합니다.

주의할 사항은 다음과 같습니다.

- 상용 클라우드에 배포할 경우에는 필수 URL 확인 도구만 사용할 수 있습니다.
- 필수 URL 확인 도구는 와일드카드가 있는 URL을 확인할 수 없으므로 먼저 해당 URL을 차단 해제해야 합니다.

### <a name="requirements"></a>요구 사항

필수 URL 확인 도구를 사용하려면 다음 요구 사항을 충족해야 합니다.

- VM에 .NET 4.6.2 프레임워크가 있어야 합니다.
- RDAgent 버전 1.0.2944.400 이상
- WVDAgentUrlTool.exe 파일은 WVDAgentUrlTool.config 파일과 동일한 폴더에 있어야 합니다.

### <a name="how-to-use-the-required-url-check-tool"></a>필수 URL 확인 도구를 사용하는 방법

필수 URL 확인 도구를 사용하려면 다음을 수행합니다.

1. VM에서 관리자 권한으로 명령 프롬프트를 엽니다.
2. 다음 명령을 실행하여 디렉터리를 현재 빌드 에이전트와 동일한 폴더(이 예제에서는 RDAgent_1.0.2944.1200)로 변경합니다.

    ```console
    cd C:\Program Files\Microsoft RDInfra\RDAgent_1.0.2944.1200
    ```

3. 다음 명령을 실행합니다.

    ```console
    WVDAgentUrlTool.exe
    ```
 
4. 파일을 실행하면 액세스할 수 있는 URL 및 액세스할 수 없는 URL 목록이 표시됩니다.

    예를 들어 다음 스크린샷은 와일드카드가 없는 필수 URL 두 개를 차단 해제해야 하는 시나리오를 보여 줍니다.

    > [!div class="mx-imgBorder"]
    > ![액세스할 수 없는 URL 출력의 스크린샷.](media/noaccess.png)
    
    필요한 모든 비 와일드카드 URL의 차단을 해제하면 출력은 다음과 같이 표시됩니다.

    > [!div class="mx-imgBorder"]
    > ![액세스 가능한 URL 출력의 스크린샷.](media/access.png)

## <a name="virtual-machines"></a>가상 머신

Azure Virtual Desktop용으로 생성한 Azure 가상 머신은 Azure 상용 클라우드에서 다음 URL에 액세스할 수 있어야 합니다.

|주소|아웃바운드 TCP 포트|목적|서비스 태그|
|---|---|---|---|
|*.wvd.microsoft.com|443|서비스 트래픽|WindowsVirtualDesktop|
|gcs.prod.monitoring.core.windows.net|443|에이전트 트래픽|AzureCloud|
|production.diagnostics.monitoring.core.windows.net|443|에이전트 트래픽|AzureCloud|
|*xt.blob.core.windows.net|443|에이전트 트래픽|AzureCloud|
|*eh.servicebus.windows.net|443|에이전트 트래픽|AzureCloud|
|*xt.table.core.windows.net|443|에이전트 트래픽|AzureCloud|
|*xt.queue.core.windows.net|443|에이전트 트래픽|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Windows 정품 인증|인터넷|
|mrsglobalsteus2prod.blob.core.windows.net|443|에이전트 및 SXS 스택 업데이트|AzureCloud|
|wvdportalstorageblob.blob.core.windows.net|443|Azure Portal 지원|AzureCloud|
| 169.254.169.254 | 80 | [Azure Instance Metadata Service 엔드포인트](../virtual-machines/windows/instance-metadata-service.md) | 해당 없음 |
| 168.63.129.16 | 80 | [세션 호스트 상태 모니터링](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | 해당 없음 |

>[!IMPORTANT]
>이제 Azure Virtual Desktop에서 FQDN 태그를 지원합니다. 자세한 내용은 [Azure Firewall을 사용하여 Windows Virtual Desktop 배포 보호](../firewall/protect-windows-virtual-desktop.md)를 참조하세요.
>
>서비스 문제를 방지하기 위해 URL 대신 FQDN 태그를 사용하는 것이 좋습니다. 나열된 URL과 태그는 Azure Virtual Desktop 사이트 및 리소스에만 해당합니다. Azure Active Directory와 같은 다른 서비스에 대한 URL은 포함되지 않습니다.

Azure Virtual Desktop용으로 생성한 Azure 가상 머신은 Azure Government 클라우드에서 다음 URL에 액세스할 수 있어야 합니다.

|주소|아웃바운드 TCP 포트|목적|서비스 태그|
|---|---|---|---|
|*.wvd.azure.us|443|서비스 트래픽|WindowsVirtualDesktop|
|gcs.monitoring.core.usgovcloudapi.net|443|에이전트 트래픽|AzureCloud|
|monitoring.core.usgovcloudapi.net|443|에이전트 트래픽|AzureCloud|
|fairfax.warmpath.usgovcloudapi.net|443|에이전트 트래픽|AzureCloud|
|*xt.blob.core.usgovcloudapi.net|443|에이전트 트래픽|AzureCloud|
|*.servicebus.usgovcloudapi.net|443|에이전트 트래픽|AzureCloud|
|*xt.table.core.usgovcloudapi.net|443|에이전트 트래픽|AzureCloud|
|Kms.core.usgovcloudapi.net|1688|Windows 정품 인증|인터넷|
|mrsglobalstugviffx.blob.core.usgovcloudapi.net|443|에이전트 및 SXS 스택 업데이트|AzureCloud|
|wvdportalstorageblob.blob.core.usgovcloudapi.net|443|Azure Portal 지원|AzureCloud|
| 169.254.169.254 | 80 | [Azure Instance Metadata Service 엔드포인트](../virtual-machines/windows/instance-metadata-service.md) | 해당 없음 |
| 168.63.129.16 | 80 | [세션 호스트 상태 모니터링](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | 해당 없음 |

다음 표에는 Azure 가상 머신에서 액세스할 수 있는 선택적 URL이 나열되어 있습니다.

|주소|아웃바운드 TCP 포트|목적|Azure Gov|
|---|---|---|---|
|*.microsoftonline.com|443|Microsoft Online Services에 대한 인증|login.microsoftonline.us|
|*.events.data.microsoft.com|443|원격 분석 서비스|None|
|www.msftconnecttest.com|443|OS가 인터넷에 연결되어 있는지 검색합니다.|None|
|*.prod.do.dsp.mp.microsoft.com|443|Windows 업데이트|None|
|login.windows.net|443|Microsoft Online Services, Microsoft 365에 로그인|login.microsoftonline.us|
|*.sfx.ms|443|OneDrive 클라이언트 소프트웨어에 대한 업데이트|oneclient.sfx.ms|
|*.digicert.com|443|인증서 해지 확인|None|
|*.azure-dns.com|443|Azure DNS 확인|없음|
|*.azure-dns.net|443|Azure DNS 확인|없음|

>[!NOTE]
>Azure Virtual Desktop에는 현재 네트워크 트래픽을 허용하기 위해 차단을 해제할 수 있는 IP 주소 범위 목록이 없습니다. 현재 특정 URL 차단 해제만 지원합니다.
>
>NGFW(차세대 방화벽)를 사용하는 경우 Azure IP용으로 특별히 만들어진 동적 목록을 사용하여 연결할 수 있는지 확인해야 합니다.
>
>필수 Azure Active Directory 관련 URL을 비롯한 안전한 Office 관련 URL 목록은 [Office 365 URL 및 IP 주소 범위](/office365/enterprise/urls-and-ip-address-ranges)를 참조하세요.
>
>서비스 트래픽과 관련된 URL에는 와일드카드 문자(*)를 사용해야 합니다. 에이전트 관련 트래픽에 *를 사용하지 않으려는 경우 와일드 카드 없이 URL을 찾는 방법은 다음과 같습니다.
>
>1. Azure Virtual Desktop 호스트 풀에 가상 머신을 등록합니다.
>2. **이벤트 뷰어** 를 연 다음, **Windows 로그** > **애플리케이션** > **WVD-Agent** 로 이동하여 이벤트 ID 3701을 찾습니다.
>3. 이벤트 ID 3701 아래에 있는 URL을 차단 해제합니다. 이벤트 ID 3701 아래의 URL은 지역별로 다릅니다. 가상 머신을 배포하려는 각 지역의 관련 URL을 사용하여 차단 해제 프로세스를 반복해야 합니다.

## <a name="remote-desktop-clients"></a>원격 데스크톱 클라이언트

사용하는 모든 원격 데스크톱 클라이언트는 다음 URL에 액세스할 수 있어야 합니다.

|주소|아웃바운드 TCP 포트|목적|클라이언트|Azure Gov|
|---|---|---|---|---|
|*.wvd.microsoft.com|443|서비스 트래픽|모두|*.wvd.microsoft.us|
|\*.servicebus.windows.net|443|데이터 문제 해결|모두|*.servicebus.usgovcloudapi.net|
|go.microsoft.com|443|Microsoft FWLinks|모두|없음|
|aka.ms|443|Microsoft URL 단축기|모두|없음|
|docs.microsoft.com|443|설명서|모두|없음|
|privacy.microsoft.com|443|개인정보처리방침|모두|없음|
|query.prod.cms.rt.microsoft.com|443|클라이언트 업데이트|Windows Desktop|없음|

>[!IMPORTANT]
>이러한 URL을 여는 것은 신뢰할 수 있는 클라이언트 환경을 위해 필수적입니다. 이러한 URL에 대한 액세스를 차단하는 것은 지원되지 않으며 서비스 기능에 영향을 줍니다.
>
>이러한 URL은 클라이언트 사이트 및 리소스에만 해당합니다. 이 목록에는 Azure Active Directory와 같은 다른 서비스에 대한 URL이 포함되지 않습니다. Azure Active Directory URL은 [Office 365 URL 및 IP 주소 범위](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)의 ID 56, 59 및 125에서 찾을 수 있습니다.
