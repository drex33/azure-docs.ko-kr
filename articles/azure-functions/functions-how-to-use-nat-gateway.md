---
title: Azure 가상 네트워크 NAT 게이트웨이를 사용하여 Azure Functions 아웃바운드 IP 제어
description: Azure 가상 네트워크에 연결된 함수에 대해 NAT를 구성하는 방법을 보여 주는 단계별 자습서입니다.
ms.topic: tutorial
ms.author: kyburns
ms.date: 2/26/2021
ms.openlocfilehash: 0223ebe7401745fc9dfff9b3935b5f2252cfa39d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130265416"
---
# <a name="tutorial-control-azure-functions-outbound-ip-with-an-azure-virtual-network-nat-gateway"></a>자습서: Azure 가상 네트워크 NAT 게이트웨이를 사용하여 Azure Functions 아웃바운드 IP 제어

가상 NAT(Network Address Translation)는 가상 네트워크에 대한 아웃바운드 전용 인터넷 연결을 간소화합니다. 서브넷에 구성되는 경우 모든 아웃바운드 연결에서 지정된 고정 공용 IP 주소를 사용합니다. NAT는 IP 주소 허용 목록을 보안 조치로 사용하는 타사 서비스를 사용해야 하는 Azure Functions 또는 Web Apps에 유용할 수 있습니다. 자세한 내용은 [Virtual Network NAT란?](../virtual-network/nat-gateway/nat-overview.md)을 참조하세요.

이 자습서에서는 가상 네트워크 NAT를 사용하여 HTTP 트리거 함수에서 아웃바운드 트래픽을 라우팅하는 방법을 보여 줍니다. 이 함수를 사용하면 자체의 아웃바운드 IP 주소를 확인할 수 있습니다. 이 자습서에서 다음을 수행합니다.

> [!div class="checklist"]
> * 가상 네트워크 만들기
> * 프리미엄 플랜 함수 앱 만들기
> * 공용 IP 주소 만들기
> * NAT 게이트웨이 만들기
> * NAT 게이트웨이를 통해 아웃바운드 트래픽을 라우팅하도록 함수 앱 구성

## <a name="topology"></a>토폴로지

다음 다이어그램에서는 만드는 솔루션의 아키텍처를 보여 줍니다.

![NAT 게이트웨이 통합에 대한 UI](./media/functions-how-to-use-nat-gateway/topology.png)

프리미엄 플랜에서 실행되는 함수는 VNet 통합 기능을 포함하는 Azure App Service의 웹앱과 동일한 호스팅 기능을 제공합니다. 문제 해결 및 고급 구성을 포함하여 VNet 통합에 대한 자세한 내용은 [Azure 가상 네트워크에 앱 통합](../app-service/overview-vnet-integration.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에서는 IP 주소 지정 및 서브넷 구성을 이해하는 것이 중요합니다. [주소 지정 및 서브넷 구성의 기본 사항을 설명하는 이 문서](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics)로 시작할 수 있습니다. 온라인에서 더 많은 문서와 비디오를 사용할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

이미 [Azure 가상 네트워크와 Functions 통합](./functions-create-vnet.md) 자습서를 완료한 경우 [HTTP 트리거 함수 만들기](#create-function)로 건너뛸 수 있습니다.

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

1. Azure Portal 메뉴에서 **리소스 만들기** 를 선택합니다. Azure Marketplace에서 **네트워킹** > **가상 네트워크** 를 선택합니다.

1. **가상 네트워크 만들기** 에서 다음 표에서 표시한 대로 지정된 설정을 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | Subscription | 구독을 선택합니다.|
    | Resource group | **새로 만들기** 를 선택하고 *myResourceGroup* 을 입력한 다음, **확인** 을 선택합니다. |
    | Name | *myResourceGroup-vnet* 을 입력합니다. |
    | 위치 | **미국 동부** 를 선택합니다.|

1. 완료되면 **다음: IP 주소** 를 선택하고, **IPv4 주소 공간** 에 대해 *10.10.0.0/16* 을 입력합니다.

1. **서브넷 추가** 를 선택한 다음, **서브넷 이름** 에 대해 *Tutorial-Net* 을 입력하고, **서브넷 주소 범위** 에 대해 *10.10.1.0/24* 를 입력합니다.

    ![vnet을 만들기 위한 IP 주소 탭](./media/functions-how-to-use-nat-gateway/create-vnet-2-ip-space.png)

1. **추가** 를 선택한 다음, **검토 + 만들기** 를 선택합니다. 나머지 항목은 기본값으로 유지하고 **만들기** 를 선택합니다.

1. **가상 네트워크 만들기** 에서 **만들기** 를 선택합니다.

다음으로, [프리미엄 플랜](functions-premium-plan.md)에서 함수 앱을 만듭니다. 이 플랜은 가상 네트워크 통합을 지원하는 동시에 서버리스 크기 조정을 제공합니다.

## <a name="create-a-function-app-in-a-premium-plan"></a>프리미엄 플랜에서 함수 앱 만들기

이 자습서에서는 [프리미엄 플랜](functions-premium-plan.md)에서 함수 앱을 만드는 방법을 보여 줍니다. [전용(App Service) 플랜](dedicated-plan.md)을 사용할 때도 동일한 기능을 사용할 수 있습니다.

> [!NOTE]  
> 이 자습서에 가장 적합한 환경을 위해 런타임 스택으로 .NET을 선택하고, 운영 체제로 Windows를 선택합니다. 또한 함수 앱을 가상 네트워크와 동일한 지역에 만듭니다.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-the-virtual-network"></a>가상 네트워크에 함수 앱 연결

이제 함수 앱을 가상 네트워크에 연결할 수 있습니다.

1. 함수 앱의 왼쪽 메뉴에서 **네트워킹** 을 선택한 다음, **VNet 통합** 아래에서 **구성하려면 여기를 클릭** 을 선택합니다.

    :::image type="content" source="./media/functions-how-to-use-nat-gateway/networking-0.png" alt-text="함수 앱에서 네트워킹 선택":::

1. **VNET 통합** 페이지에서 **VNet 추가** 를 선택합니다.

1. **네트워크 기능 상태** 에서 이미지 아래의 표에 나와 있는 설정을 사용합니다.

    ![함수 앱 가상 네트워크 정의](./media/functions-how-to-use-nat-gateway/networking-3.png)

    | 설정      | 제안 값  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **Virtual Network** | MyResourceGroup-vnet | 이 가상 네트워크는 이전에 만든 가상 네트워크입니다. |
    | **서브넷** | 새 서브넷 만들기 | 함수 앱에서 사용할 서브넷을 가상 네트워크에 만듭니다. 빈 서브넷을 사용하도록 VNet 통합을 구성해야 합니다. |
    | **서브넷 이름** | Function-Net | 새 서브넷의 이름입니다. |
    | **가상 네트워크 주소 블록** | 10.10.0.0/16 | 하나의 주소 블록만 정의해야 합니다. |
    | **서브넷 주소 블록** | 10.10.2.0/24   | 서브넷 크기는 프리미엄 플랜 함수 앱에서 확장할 수 있는 총 인스턴스 수를 제한합니다. 이 예에서는 254개의 사용 가능한 호스트 주소가 있는 `/24` 서브넷을 사용합니다. 이 서브넷은 과도하게 프로비저닝되지만 계산하기 쉽습니다. |

1. **확인** 을 선택하여 바인딩을 추가합니다. **VNet 통합** 및 **네트워크 기능 상태** 페이지를 닫아 함수 앱 페이지로 돌아갑니다.

이제 함수 앱에서 가상 네트워크에 액세스할 수 있습니다. 다음으로 HTTP 트리거 함수를 함수 앱에 추가합니다.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>HTTP 트리거 함수 만들기

1. **Functions** 창의 왼쪽 메뉴에서 **Functions** 를 선택한 다음, 맨 위 메뉴에서 **추가** 를 선택합니다. 
 
1. **새 함수** 창에서 **Http 트리거** 를 선택하고, **새 함수** 에 대한 기본 이름을 적용하거나 새 이름을 입력합니다. 

1. **코드 + 테스트** 에서 템플릿 생성 C# 스크립트(.csx) 코드를 다음 코드로 바꿉니다. 

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
    
        var client = new HttpClient();
        var response = await client.GetAsync(@"https://ifconfig.me");
        var responseMessage = await response.Content.ReadAsStringAsync();
    
        return new OkObjectResult(responseMessage);
    }
    ```

    이 코드는 호출자의 IP 주소를 반환하는 외부 웹 사이트(이 경우에는 이 함수)를 호출합니다. 이 메서드를 사용하면 함수 앱에서 사용하는 아웃바운드 IP 주소를 쉽게 확인할 수 있습니다.

이제 함수를 실행하고 현재 아웃바운드 IP를 확인할 준비가 되었습니다.

## <a name="verify-current-outbound-ips"></a>현재 아웃바운드 IP 확인

이제 함수를 실행할 수 있습니다. 그러나 먼저 포털을 체크 인하고, 함수 앱에서 사용하는 아웃바운드 IP를 확인합니다.  

1. 함수 앱에서 **속성** 을 선택하고, **아웃바운드 IP 주소** 필드를 검토합니다.

    ![함수 앱 아웃바운드 IP 주소 보기](./media/functions-how-to-use-nat-gateway/function-properties-ip.png)

1. 이제 HTTP 트리거 함수로 돌아가서 **코드 + 테스트**, **테스트/실행** 을 차례로 선택합니다.

    ![함수 테스트](./media/functions-how-to-use-nat-gateway/function-code-test.png)

1. **실행** 을 선택하여 함수를 실행한 다음, **출력** 으로 전환합니다. 

    ![함수 테스트 출력](./media/functions-how-to-use-nat-gateway/function-test-1-output.png)

1. HTTP 응답 본문의 IP 주소가 앞에서 확인한 아웃바운드 IP 주소의 값 중 하나인지 확인합니다.

이제 공용 IP를 만들고, NAT 게이트웨이를 사용하여 이 아웃바운드 IP 주소를 수정할 수 있습니다.

## <a name="create-public-ip"></a>공용 IP 만들기

1. 리소스 그룹에서 **추가** 를 선택하고, Azure Marketplace에서 **공용 IP 주소** 를 검색하고, **만들기** 를 선택합니다. 이미지 아래의 표에서 설명하는 설정을 사용합니다.

    ![공용 IP 주소 만들기](./media/functions-how-to-use-nat-gateway/create-public-ip.png)

    | 설정      | 제안 값  |
    | ------------ | ---------------- |
    | **IP 버전** | IPv4 |
    | **SKU** | 표준 |
    | **계층** | 지역 |
    | **이름** | Outbound-IP |
    | **구독** | 구독이 표시되는지 확인 | 
    | **리소스 그룹** | myResourceGroup(또는 리소스 그룹에 할당한 이름) |
    | **위치** | 미국 동부(또는 다른 리소스에 할당한 위치) |
    | **가용성 영역** | 영역 없음 |

1. **만들기** 를 선택하여 배포를 제출합니다.

1. 배포가 완료되면 새로 만든 공용 IP 주소 리소스로 이동하여 **개요** 에서 IP 주소를 확인합니다.

    ![공용 IP 주소 보기](./media/functions-how-to-use-nat-gateway/public-ip-overview.png)

## <a name="create-nat-gateway"></a>NAT 게이트웨이 만들기

이제 NAT 게이트웨이를 만들어 보겠습니다. [이전의 가상 네트워킹 자습서](functions-create-vnet.md)에서 시작할 때 `Function-Net`은 제안된 서브넷 이름이고, `MyResourceGroup-vnet`은 해당 자습서에서 제안된 가상 네트워크 이름이었습니다.

1. 리소스 그룹에서 **추가** 를 선택하고, Azure Marketplace에서 **NAT 게이트웨이** 를 검색하고, **만들기** 를 선택합니다. 이미지 아래의 표에 나와 있는 설정을 사용하여 **기본** 탭을 채웁니다.

    ![NAT 게이트웨이 만들기](./media/functions-how-to-use-nat-gateway/create-nat-1-basics.png)

    | 설정      | 제안 값  |
    | ------------ | ---------------- |  
    | **구독** | 사용자의 구독 | 
    | **리소스 그룹** | myResourceGroup(또는 리소스 그룹에 할당한 이름) |
    | **NAT 게이트웨이 이름** | myNatGateway |
    | **지역** | 미국 동부(또는 다른 리소스에 할당한 위치) |
    | **가용성 영역** | 없음 |

1. **다음: 아웃바운드 IP** 를 선택합니다. **공용 IP 주소** 필드에서 이전에 만든 공용 IP 주소를 선택합니다. **공용 IP 접두사** 를 선택하지 않은 상태로 둡니다.

1. **다음: 서브넷** 을 선택합니다. **가상 네트워크** 필드 및 *Function-Net* 서브넷에서 *myResourceGroup-vnet* 리소스를 선택합니다.

    ![서브넷 선택](./media/functions-how-to-use-nat-gateway/create-nat-3-subnet.png)

1. **검토 + 만들기**, **만들기** 를 차례로 선택하여 배포를 제출합니다.

배포가 완료되면 NAT 게이트웨이가 트래픽을 함수 앱 서브넷에서 인터넷으로 라우팅할 준비가 됩니다.

## <a name="update-function-configuration"></a>함수 구성 업데이트

이제 `1` 값으로 설정된 `WEBSITE_VNET_ROUTE_ALL` 애플리케이션 설정을 추가해야 합니다.  이 설정은 가상 네트워크 및 연결된 NAT 게이트웨이를 통해 아웃바운드 트래픽을 강제로 수행합니다. 이 설정이 없으면 인터넷 트래픽이 통합 가상 네트워크를 통해 라우팅되지 않으며 동일한 아웃바운드 IP가 표시됩니다. 

1. Azure Portal에서 함수 앱으로 이동하여 왼쪽 메뉴에서 **구성** 을 선택합니다.

1. **애플리케이션 설정** 아래에서 **+ 새 애플리케이션 설정** 을 선택하고, 다음 값을 사용하여 필드를 채웁니다.

    |필드 이름  |값 |
    |---|---|
    |**이름**    |WEBSITE_VNET_ROUTE_ALL|
    |**값**   |1|

1. **확인** 을 선택하여 새 애플리케이션 설정 대화 상자를 닫습니다.

1. **저장** 을 선택한 다음, **계속** 을 선택하여 설정을 저장합니다.

이제 함수 앱이 연결된 가상 네트워크를 통해 트래픽을 라우팅하도록 구성됩니다.

## <a name="verify-new-outbound-ips"></a>새 아웃바운드 IP 확인

[이전 단계](#verify-current-outbound-ips)를 반복하여 함수를 다시 실행합니다. 이제 함수 출력에 표시된 NAT에서 구성한 아웃바운드 IP 주소가 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서를 완료하기 위해 리소스를 만들었습니다. 이러한 리소스에 대한 요금이 [계정 상태](https://azure.microsoft.com/account/) 및 [서비스 가격 책정](https://azure.microsoft.com/pricing/)에 따라 청구될 수 있습니다. 추가 비용이 발생하지 않도록 방지하려면 더 이상 필요하지 않은 리소스를 삭제합니다. 

[!INCLUDE [functions-quickstart-cleanup-inner](../../includes/functions-quickstart-cleanup-inner.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 네트워킹 옵션](functions-networking-options.md)