---
title: ILB(내부 부하 분산 장치) 엔드포인트 구성
titleSuffix: Azure Application Gateway
description: 이 문서에서는 개인 프런트 엔드 IP 주소를 사용하여 Application Gateway를 구성하는 방법에 대한 정보를 제공합니다.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 02/23/2021
ms.author: victorh
ms.openlocfilehash: 224cbe1e34e5915a7fa5fc1cf415c35f86c3abe4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711657"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>ILB(내부 부하 분산 장치) 엔드포인트를 사용하여 애플리케이션 게이트웨이 구성

Azure Application Gateway는 인터넷 연결 VIP 또는 인터넷에 노출되지 않는 내부 엔드포인트로 구성할 수 있습니다. 내부 엔드포인트는 *ILB(내부 부하 분산 장치) 엔드포인트* 라고도 하는 프런트 엔드용 개인 IP 주소를 사용합니다.

프런트 엔드 개인 IP 주소를 사용하여 게이트웨이를 구성하는 것은 인터넷에 노출되지 않는 내부 LOB(기간 업무) 애플리케이션에 유용합니다. 또한 인터넷에 노출되지 않지만 다음을 필요로 하는 보안 경계에 있는 다중 계층 애플리케이션 내의 서비스 및 계층에 유용합니다.

- 라운드 로빈 로드 분산
- 세션에 대한 고정력
- 또는 TLS(전송 계층 보안) 종료(이전의 SSL(Secure Sockets Layer)).

이 문서에서는 Azure Portal을 사용하여 프런트 엔드 개인 IP 주소로 애플리케이션 게이트웨이를 구성하는 단계를 안내합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Azure에 로그인

<https://portal.azure.com>에서 Azure Portal에 로그인합니다.

## <a name="create-an-application-gateway"></a>애플리케이션 게이트웨이 만들기

Azure가 사용자가 만든 리소스 간에 통신하려면 가상 네트워크가 필요합니다. 새 가상 네트워크를 만들거나 기존 가상 네트워크를 사용합니다. 

이 예제에서는 새 가상 네트워크를 만듭니다. 애플리케이션 게이트웨이를 만드는 동시에 가상 네트워크를 만들 수 있습니다. 별도의 서브넷으로 Application Gateway 인스턴스가 만들어집니다. 이 예제에서는 두 개의 서브넷이 있습니다. 하나는 애플리케이션 게이트웨이용이고, 다른 하나는 백 엔드 서버용입니다.

1. 포털 메뉴를 확장하고 **리소스 만들기** 를 선택합니다.
2. **네트워킹** 을 선택한 다음, 추천 목록에서 **Application Gateway** 를 선택합니다.
3. 애플리케이션 게이트웨이의 이름으로 *myAppGateway* 를 입력하고 새 리소스 그룹에 대해 *myResourceGroupAG* 를 입력합니다.
4. **지역** 으로 **미국 중부** 를 선택합니다.
5. **계층** 에 대해 **표준** 을 선택합니다.
6. **가상 네트워크 구성** 에서 **새로 만들기** 를 선택한 다음, 가상 네트워크의 다음 값을 입력합니다.
   - *myVNet* - 가상 네트워크 이름
   - *10.0.0.0/16* - 가상 네트워크 주소 공간
   - *myAGSubnet* - 서브넷 이름
   - *10.0.0.0/24* - 서브넷 주소 공간
   - *myBackendSubnet* - 백 엔드 서브넷 이름
   - *10.0.1.0/24* - 백 엔드 서브넷 주소 공간

    ![가상 네트워크 만들기](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. **확인** 을 선택하여 가상 네트워크 및 서브넷을 만듭니다.
7. **다음: 프런트 엔드** 를 선택합니다.
8. **프런트 엔드 IP 주소 유형** 에 대해 **개인** 을 선택합니다.

   기본적으로 동적 IP 주소 할당입니다. 구성된 서브넷의 사용 가능한 첫 번째 주소는 프런트 엔드 IP 주소로 할당됩니다.
   > [!NOTE]
   > 할당된 후에는 IP 주소 유형(정적 또는 동적)을 변경할 수 없습니다.
9. **다음: 백 엔드** 를 선택합니다.
10. **백 엔드 추가** 를 선택합니다.
11. **이름** 에 *appGatewayBackendPool* 을 입력합니다.
12. **대상 없이 백 엔드 풀 추가** 에 대해 **예** 를 선택합니다. 대상은 나중에 추가합니다.
13. **추가** 를 선택합니다.
14. **다음:구성** 을 선택합니다.
15. **라우팅 규칙** 에서 **라우팅 규칙 추가** 를 선택합니다.
16. **규칙 이름** 에 *Rrule-01* 을 입력합니다.
17. **수신기 이름** 에 *Listener-01* 을 입력합니다.
18. **프런트 엔드 IP** 에 대해 **프라이빗** 을 선택합니다.
19. 나머지 기본값을 수락하고 **백 엔드 대상** 탭을 선택합니다.
20. **대상 유형** 으로 **백 엔드 풀** 을 선택한 다음 **appGatewayBackendPool** 을 선택합니다.
21. **HTTP 설정** 에서 **새로 추가** 를 선택합니다.
22. **HTTP 설정 이름** 에 *http-setting-01* 을 입력합니다.
23. **백 엔드 프로토콜** 에 대해 **HTTP** 를 선택합니다.
24. **백 엔드 포트** 에 *80* 을 입력합니다.
25. 나머지 기본값을 수락하고 **추가** 를 선택합니다.
26. **라우팅 규칙 추가** 페이지에서 **추가** 를 선택합니다.
27. 완료되면 **다음: 태그** 를 선택합니다.
28. 완료되면 **다음: 리뷰 + 만들기** 를 클릭합니다.
29. 요약 페이지에서 설정을 검토한 다음, **만들기** 를 선택하여 네트워크 리소스와 애플리케이션 게이트웨이를 만듭니다. 애플리케이션 게이트웨이를 만들 때까지 몇 분 정도 걸릴 수 있습니다. 배포가 성공적으로 완료될 때까지 기다렸다가 다음 섹션으로 이동합니다.

## <a name="add-backend-pool"></a>백 엔드 풀 추가

백 엔드 풀은 요청을 처리하는 백 엔드 서버로 요청을 라우팅하는 데 사용됩니다. 백 엔드는 NIC, 가상 머신 확장 집합, 공용 IP 주소, 내부 IP 주소, FQDN(정규화된 도메인 이름) 및 다중 테넌트 백 엔드(예: Azure App Service)로 구성될 수 있습니다. 이 예제에서는 가상 머신을 대상 백 엔드로 사용합니다. 기존 가상 머신을 사용해도 되고 새로 만들어도 됩니다. 이 예제에서는 Azure에서 애플리케이션 게이트웨이의 백 엔드 서버로 사용할 두 개의 가상 머신을 만듭니다.

이렇게 하려면 다음을 수행합니다.

1. 백 엔드 서버로 사용되는 두 개의 새 가상 머신 *myVM* 과 *myVM2* 를 만듭니다.
2. 애플리케이션 게이트웨이가 성공적으로 만들어졌는지 확인하기 위해 가상 머신에 IIS를 설치합니다.
3. 백 엔드 서버를 백 엔드 풀에 추가합니다.

### <a name="create-a-virtual-machine"></a>가상 머신 만들기


1. **리소스 만들기** 를 선택합니다.
2. **컴퓨팅** 을 선택한 다음, **가상 머신** 을 선택합니다.
4. 가상 머신에 대해 다음 값을 입력합니다.
   - 구독을 선택합니다.
   - **리소스 그룹** 에 대해 *myResourceGroupAG* 를 선택합니다.
   - **가상 머신 이름** 에 *myVM* 을 입력합니다.
   - **이미지** 에 대해 **Windows Server 2019 Datacenter** 를 선택합니다.
   - 유효한 **사용자 이름** 을 입력합니다.
   - 유효한 **암호** 를 입력합니다.
1. 나머지 기본값을 수락하고 **다음: 디스크** 를 선택합니다.
1. 기본값을 적용하고, **다음: 네트워킹** 을 선택합니다.
1. 가상 네트워크에 대해 **myVNet** 이 선택되어 있고 서브넷이 **myBackendSubnet** 인지 확인합니다.
1. 나머지 기본값을 수락하고 **다음: 관리** 를 선택합니다.
1. **사용 안 함** 을 선택하여 부팅 진단을 비활성화합니다.
1. **검토 + 만들기** 를 선택합니다.
1. 요약 페이지에서 설정을 검토한 다음, **만들기** 를 선택합니다. VM을 만드는 데 몇 분 정도 걸릴 수 있습니다. 배포가 성공적으로 완료될 때까지 기다렸다가 다음 섹션으로 이동합니다.

### <a name="install-iis"></a>IIS 설치

1. Cloud Shell을 열고 **PowerShell** 로 설정되어 있는지 확인합니다.
    ![PowerShell을 사용하는 열린 Azure Cloud Shell 콘솔 창을 보여 주는 스크린샷.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. 다음 명령을 실행하여 가상 머신에 IIS를 설치합니다.

   ```azurepowershell
   Set-AzVMExtension `
        -ResourceGroupName myResourceGroupAG `
        -ExtensionName IIS `
        -VMName myVM `
        -Publisher Microsoft.Compute `
        -ExtensionType CustomScriptExtension `
        -TypeHandlerVersion 1.4 `
        -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
         -Location CentralUS 

   ```

3. 두 번째 가상 머신을 만들고, 방금 완료한 단계를 사용하여 IIS를 설치합니다. 가상 머신 이름 및 `Set-AzVMExtension`의 `VMName`에 myVM2를 사용합니다.

### <a name="add-backend-servers-to-backend-pool"></a>백 엔드 풀에 백 엔드 서버 추가

1. **모든 리소스** 를 선택한 다음, **myAppGateway** 를 선택합니다.
2. **백 엔드 풀** 을 선택한 다음 **appGatewayBackendPool** 을 선택합니다.
3. **대상 유형** 에서 **가상 머신** 을 선택하고 **대상** 에서 myVM과 연결된 vNIC를 선택합니다.
4. 반복하여 MyVM2를 추가합니다.
   ![대상 유형 및 대상이 강조 표시된 백 엔드 풀 창을 편집합니다.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. **저장** 을 선택합니다.

## <a name="create-a-client-virtual-machine"></a>클라이언트 가상 머신 만들기

클라이언트 가상 머신은 애플리케이션 게이트웨이 백 엔드 풀에 연결하는 데 사용됩니다.

- 이전 단계를 통해 세 번째 가상 머신을 만듭니다. 가상 머신 이름에 myVM3을 사용합니다.

## <a name="test-the-application-gateway"></a>애플리케이션 게이트웨이 테스트

1. myAppGateway 페이지에서 **프런트 엔드 IP 구성** 을 선택하여 프런트 엔드 개인 IP 주소를 기록해 둡니다.
    ![개인 유형이 강조 표시된 프런트 엔드 IP 구성 창.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. 개인 IP 주소를 복사한 다음 myVM3의 브라우저 주소 표시줄에 붙여넣어 애플리케이션 게이트웨이 백 엔드 풀에 액세스합니다.

## <a name="next-steps"></a>다음 단계

백 엔드 풀의 상태를 모니터링하려면 [Application Gateway의 백 엔드 상태 및 진단 로그](application-gateway-diagnostics.md)를 참조하세요.
