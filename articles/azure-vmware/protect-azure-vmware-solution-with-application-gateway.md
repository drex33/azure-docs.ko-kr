---
title: Azure Application Gateway를 사용하여 Azure VMware Solution에서 웹앱 보호
description: Azure VMware Solution에서 실행되는 웹앱을 안전하게 노출하도록 Azure Application Gateway를 구성합니다.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 897b5788d3bb4cb76fee44cb29e8c73d5f7f34c7
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123257012"
---
# <a name="protect-web-apps-on-azure-vmware-solution-with-azure-application-gateway"></a>Azure Application Gateway를 사용하여 Azure VMware Solution에서 웹앱 보호

[Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/)는 웹 애플리케이션에 대한 트래픽을 관리할 수 있게 해주는 레이어 7 웹 트래픽 부하 분산 장치입니다. Azure VMware Solution v1.0 및 v2.0 모두에서 제공됩니다. 두 버전 모두 Azure VMware Solution에서 실행되는 웹앱으로 테스트되었습니다.

기능은 다음과 같습니다. 
- 쿠키 기반 세션 선호도
- URL 기반 라우팅
- WAF(웹 애플리케이션 방화벽)

전체 기능 목록은 [Azure Application Gateway 기능](../application-gateway/features.md)을 참조하세요. 

이 문서에서는 웹 서버 팜 앞에 Application Gateway를 사용하여 Azure VMware Solution에서 실행되는 웹앱을 보호하는 방법을 보여 줍니다. 

## <a name="topology"></a>토폴로지
이 다이어그램에서는 Application Gateway를 사용하여 Azure IaaS VM(가상 머신), Azure 가상 머신 확장 집합 또는 온-프레미스 서버를 보호하는 방법을 보여 줍니다. Application Gateway는 Azure VMware Solution VM을 온-프레미스 서버로 처리합니다. 

:::image type="content" source="media/application-gateway/app-gateway-protects.png" alt-text="Application Gateway에서 Azure IaaS VM(가상 머신), Azure 가상 머신 확장 집합 또는 온-프레미스 서버를 보호하는 방법을 보여 주는 다이어그램." border="false":::

> [!IMPORTANT]
> Azure Application Gateway는 현재 Azure VMware Solution VM에서 실행되는 웹앱을 노출하는 데 지원되는 유일한 방법입니다.

이 다이어그램에서는 Azure VMware Solution 웹 애플리케이션으로 Application Gateway의 유효성을 검사하는 데 사용되는 테스트 시나리오를 보여 줍니다.

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="Azure VMware Solution 웹 애플리케이션으로 Application Gateway의 유효성을 검사하는 데 사용되는 테스트 시나리오를 보여 주는 다이어그램." border="false":::

Application Gateway 인스턴스는 Azure 공용 IP 주소를 사용하여 전용 서브넷의 허브에 배포됩니다. 가상 네트워크에 대한 [Azure DDoS Protection 표준](../ddos-protection/ddos-protection-overview.md)을 활성화하는 것이 좋습니다. 웹 서버는 NSX T0 및 T1 게이트웨이 뒤에 있는 Azure VMware Solution 프라이빗 클라우드에서 호스트됩니다. 또한 Azure VMware Solution [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md)를 사용하여 허브 및 온-프레미스 시스템과 통신할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정.    
- 배포되어 실행 중인 Azure VMware Solution 프라이빗 클라우드.

## <a name="deployment-and-configuration"></a>배포 및 구성

1. Azure Portal에서 **Application Gateway** 를 검색하고 **애플리케이션 게이트웨이 만들기** 를 선택합니다.

2. 다음 그림에 나와 있는 것처럼, 기본 세부 정보를 제공한 후 **다음: 프런트 엔드>** 를 선택합니다. 

    :::image type="content" source="media/application-gateway/create-app-gateway.png" alt-text="Azure Portal의 애플리케이션 게이트웨이 만들기 페이지를 보여 주는 스크린샷.":::

3. 프런트 엔드 IP 주소 유형을 선택합니다. 공용 IP 주소의 경우 기존 공용 IP 주소를 선택하거나 새 공용 IP 주소를 만듭니다. **다음: 백 엔드>** 를 선택합니다.

    > [!NOTE]
    > 프라이빗 프런트 엔드에는 표준 및 WAF(Web Application Firewall) SKU만 지원됩니다.

4. Azure VMware Solution 인프라에서 실행되는 VM의 백 엔드 풀을 추가합니다. Azure VMware Solution 프라이빗 클라우드에서 실행되는 웹 서버에 대한 세부 정보를 제공하고 **추가** 를 선택합니다.  그리고 **다음: 구성>** 을 선택합니다.

5. **구성** 탭에서 **라우팅 규칙 추가** 를 선택합니다.

6. **수신기** 탭에서 수신기에 대한 세부 정보를 제공합니다. HTTPS를 선택한 경우 PFX 파일의 인증서 또는 기존 Azure Key Vault 인증서를 제공해야 합니다. 

7. **백 엔드 대상** 탭을 선택하고 이전에 만든 백 엔드 풀을 선택합니다. **HTTP 설정** 필드에서 **새로 추가** 를 선택합니다.

8. HTTP 설정에 대한 매개 변수를 구성합니다. **추가** 를 선택합니다.

9. 경로 기반 규칙을 구성하려면 **여러 대상을 추가하여 경로 기반 규칙 만들기** 를 선택합니다. 

10. 경로 기반 규칙을 추가하고 **추가** 를 선택합니다. 이 단계를 반복하여 경로 기반 규칙을 더 추가합니다. 

11. 경로 기반 규칙을 추가하는 작업이 완료되면 **추가** 를 다시 선택한 후 **다음: 태그>** 를 선택합니다. 

12. 태그를 추가한 후 **다음: 검토 + 만들기>** 를 선택합니다.

13. Application Gateway에서 유효성 검사가 실행됩니다. 성공하면 **만들기** 를 선택하여 배포합니다.

## <a name="configuration-examples"></a>구성 예제

이제 다음 사용 사례에서 Azure VMware Solution VM을 백 엔드 풀로 사용하여 Application Gateway를 구성합니다. 

- [여러 사이트 호스팅](#hosting-multiple-sites)
- [URL로 라우팅](#routing-by-url)

### <a name="hosting-multiple-sites"></a>여러 사이트 호스팅

이 절차에서는 기존 애플리케이션 게이트웨이의 Azure VMware Solution 프라이빗 클라우드에서 실행되는 VM을 사용하여 백 엔드 주소 풀을 정의하는 방법을 보여 줍니다. 

>[!NOTE]
>이 절차에서는 여러 도메인이 있다고 가정하므로 www.contoso.com 및 www.fabrikam.com의 예제를 사용합니다.


1. 프라이빗 클라우드에서 VM의 두 가지 풀을 만듭니다. 첫 번째는 Contoso를 나타내고, 두 번째는 Fabrikam을 나타냅니다. 

    :::image type="content" source="media/application-gateway/app-gateway-multi-backend-pool.png" alt-text="VSphere 클라이언트의 웹 서버 세부 정보에 대한 요약을 보여 주는 스크린샷.":::

    IIS(인터넷 정보 서비스) 역할이 설치된 Windows Server 2016을 사용했습니다. VM이 설치되면 다음 PowerShell 명령을 실행하여 각 VM에서 IIS를 구성합니다. 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. 기존 애플리케이션 게이트웨이 인스턴스의 왼쪽 메뉴에서 **백 엔드 풀** 을 선택하고 **추가** 를 선택한 다음, 새 풀의 세부 정보를 입력합니다. 오른쪽 창에서 **추가** 를 선택합니다.

    :::image type="content" source="media/application-gateway/app-gateway-multi-backend-pool-02.png" alt-text="백 엔드 풀을 추가하기 위한 백 엔드 풀 페이지의 스크린샷" lightbox="media/application-gateway/app-gateway-multi-backend-pool-02.png":::

3. **수신기** 섹션에서 각 웹 사이트에 대한 새 수신기를 만듭니다. 각 수신기에 대한 세부 정보를 입력하고 **추가** 를 선택합니다.

4. 왼쪽에서 **HTTP 설정** 을 선택하고 왼쪽 창에서 **추가** 를 선택합니다. 세부 정보를 입력하여 새 HTTP 설정을 만들고 **저장** 을 선택합니다.

    :::image type="content" source="media/application-gateway/app-gateway-multi-backend-pool-03.png" alt-text="새 HTTP 설정을 만들기 위한 HTTP 설정 페이지의 스크린샷" lightbox="media/application-gateway/app-gateway-multi-backend-pool-03.png":::

5. 왼쪽 메뉴의 **규칙** 섹션에서 규칙을 만듭니다. 각 규칙을 해당 수신기와 연결합니다. **추가** 를 선택합니다.

6. 해당하는 백 엔드 풀 및 HTTP 설정을 구성합니다. **추가** 를 선택합니다.

7. 연결을 테스트합니다. 선호하는 브라우저를 열고 Azure VMware Solution 환경에서 호스트되는 다른 웹 사이트로 이동합니다(예: http://www.fabrikam.com ).

    :::image type="content" source="media/application-gateway/app-gateway-multi-backend-pool-07.png" alt-text="성공한 연결 테스트를 보여 주는 브라우저 페이지의 스크린샷.":::

### <a name="routing-by-url"></a>URL로 라우팅

다음 단계는 Azure VMware Solution 프라이빗 클라우드에서 실행되는 VM을 사용하여 백 엔드 주소 풀을 정의합니다. 이 프라이빗 클라우드는 기존 애플리케이션 게이트웨이에 있습니다. 그런 다음, 웹 트래픽이 풀의 적절한 서버에 도착하도록 하는 라우팅 규칙을 만듭니다.

1. 프라이빗 클라우드에서 웹 팜을 나타내는 가상 머신 풀을 만듭니다. 

    :::image type="content" source="media/application-gateway/app-gateway-url-route-backend-pool.png" alt-text="다른 VM의 요약을 보여 주는 VMSphere 클라이언트 페이지의 스크린샷.":::

    이 자습서를 설명하기 위해 IIS 역할이 설치된 Windows Server 2016이 사용되었습니다. VM이 설치되면 다음 PowerShell 명령을 실행하여 각 VM 자습서에서 사용할 IIS를 구성합니다. 

    첫 번째 가상 머신 contoso-web-01은 기본 웹 사이트를 호스트합니다.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

    두 번째 가상 머신 contoso-web-02는 이미지 사이트를 호스트합니다.
 
    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "images" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\images\test.htm -Value $($env:computername)
    ```

    세 번째 가상 머신 contoso-web-03은 비디오 사이트를 호스트합니다.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "video" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\video\test.htm -Value $($env:computername)
    ```

2. 기존 애플리케이션 게이트웨이 인스턴스에 새 백 엔드 풀 3개를 추가합니다. 

   1. 왼쪽 메뉴에서 **백 엔드 풀** 을 선택합니다. 
   1. **추가** 를 선택하고 첫 번째 풀 **contoso-web** 의 세부 정보를 입력합니다. 
   1. VM 하나를 대상으로 추가합니다. 
   1. **추가** 를 선택합니다. 
   1. **contoso-images** 및 **contoso-video** 에 대해 이 프로세스를 반복하여 고유한 VM 하나를 대상으로 추가합니다. 

    :::image type="content" source="media/application-gateway/app-gateway-url-route-backend-pool-02.png" alt-text="새 백 엔드 풀 3개를 추가하는 것을 보여 주는 백 엔드 풀 페이지의 스크린샷." lightbox="media/application-gateway/app-gateway-url-route-backend-pool-02.png":::

3. **수신기** 섹션에서 포트 8080을 사용하여 기본 유형의 새 수신기를 만듭니다.

4. 왼쪽 탐색 메뉴에서 **HTTP 설정** 을 선택하고 왼쪽 창에서 **추가** 를 선택합니다. 세부 정보를 입력하여 새 HTTP 설정을 만들고 **저장** 을 선택합니다.

    :::image type="content" source="media/application-gateway/app-gateway-url-route-backend-pool-04.png" alt-text="HTTP 설정 구성을 보여 주는 HTTP 설정 추가 페이지의 스크린샷.":::

5. 왼쪽 메뉴의 **규칙** 섹션에서 규칙을 만들고 각 규칙을 이전에 만든 수신기와 연결합니다. 그런 다음, 기본 백 엔드 풀과 HTTP 설정을 구성하고 **추가** 를 선택합니다.

    :::image type="content" source="media/application-gateway/app-gateway-url-route-backend-pool-07.png" alt-text="라우팅 규칙을 백 엔드 대상으로 구성하는 라우팅 규칙 추가 페이지의 스크린샷.":::

6. 구성을 테스트합니다. Azure Portal에서 애플리케이션 게이트웨이에 액세스하고 **개요** 섹션에서 공용 IP 주소를 복사합니다. 

   1. 새 브라우저 창을 열고 URL `http://<app-gw-ip-address>:8080`을 입력합니다. 

      :::image type="content" source="media/application-gateway/app-gateway-url-route-backend-pool-08.png" alt-text="성공한 구성 테스트를 보여 주는 브라우저 페이지의 스크린샷.":::

   1. URL을 `http://<app-gw-ip-address>:8080/images/test.htm`로 변경합니다.

      :::image type="content" source="media/application-gateway/app-gateway-url-route-backend-pool-09.png" alt-text="새 URL을 사용하여 성공한 다른 테스트의 스크린샷.":::

   1. URL을 다시 `http://<app-gw-ip-address>:8080/video/test.htm`으로 변경합니다.

      :::image type="content" source="media/application-gateway/app-gateway-url-route-backend-pool-10.png" alt-text="최종 URL을 사용하여 성공한 테스트의 스크린샷.":::

## <a name="next-steps"></a>다음 단계

지금까지 Application Gateway를 사용하여 Azure VMware Solution에서 실행되는 웹앱을 보호하는 방법을 살펴보았으므로 다음에 대해 알아볼 수 있습니다.

- [여러 시나리오에 맞게 Azure Application Gateway 구성](../application-gateway/configuration-overview.md)
- [Traffic Manager를 배포하여 Azure VMware Solution 워크로드 균형 조정](deploy-traffic-manager-balance-workloads.md)
- [Azure VMware Solution 기반 워크로드와 Azure NetApp Files 통합](netapp-files-with-azure-vmware-solution.md)
- [가상 네트워크에서 Azure 리소스 보호](../ddos-protection/ddos-protection-overview.md)
