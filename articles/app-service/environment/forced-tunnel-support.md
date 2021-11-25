---
title: 강제 터널링 구성
description: 가상 네트워크에서 아웃바운드 트래픽이 강제 터널링될 때 App Service Environment가 작동하도록 설정하는 방법을 알아봅니다.
author: madsd
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.topic: quickstart
ms.date: 05/29/2018
ms.author: madsd
ms.custom: mvc, seodec18, mode-other
ms.openlocfilehash: bfc379657113f135f3c222a1dd09e97d615c6467
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133040506"
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>강제 터널링으로 App Service Environment 구성
> [!NOTE]
> 이 문서에서는 격리된 App Service 요금제와 함께 사용되는 App Service Environment v2에 관해 설명합니다.
> 

ASE(App Service Environment)는 고객의 Azure 가상 네트워크에 Azure App Service를 배포한 것입니다. 많은 고객이 VPN 또는 Azure ExpressRoute 연결을 통해 자사의 온-프레미스 네트워크의 확장이 되도록 Azure 가상 네트워크를 구성합니다. 인터넷 바인딩된 트래픽이 VPN 또는 가상 어플라이언스로 리디렉션하는 경우 강제 터널링됩니다. 가상 어플라이언스는 아웃바운드 네트워크 트래픽을 검사하고 감사하는 데 보통 사용됩니다. 

ASE에는 [App Service Environment 네트워크 아키텍처][network] 문서에서 설명된 많은 외부 종속성이 있습니다. 일반적으로 모든 ASE 아웃바운드 종속성 트래픽은 ASE를 사용해 프로비저닝된 VIP를 통과해야 합니다. 아래 정보를 따르지 않고 ASE 간의 트래픽 라우팅을 변경하면 ASE 작동이 중지됩니다.

Azure Virtual Network에서 라우팅은 LPM(Longest Prefix Match)을 기반으로 수행됩니다. LPM 일치가 동일한 경로가 두 개 이상 있으면 다음 순서대로 해당 원점에 따라 경로가 선택됩니다.

* UDR(사용자 정의 경로)
* BGP 경로(ExpressRoute를 사용하는 경우)
* 시스템 경로

가상 네트워크의 라우팅에 대한 자세한 내용은 [사용자 정의된 경로 및 IP 전달][routes]을 참조하세요. 

인터넷에 직접이 아닌 다른 곳에서 ASE 아웃바운드 트래픽을 라우팅하려고 하면 다음 옵션이 있습니다.

* ASE가 인터넷에 직접 액세스하도록 설정
* BGP 경로를 무시하도록 ASE 서브넷 구성
* Azure SQL 및 Azure Storage에 서비스 엔드포인트를 사용하려면 ASE 서브넷 구성
* ASE Azure SQL 방화벽에 고유한 IP 추가

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>App Service Environment에서 인터넷에 직접 액세스하도록 설정

Azure 가상 네트워크가 ExpressRoute를 통해 구성된 경우에도 인터넷에 직접 액세스해 ASE를 사용하려면 다음을 수행할 수 있습니다.

* 0\.0.0.0/0을 보급하도록 ExpressRoute를 구성합니다. 기본적으로 ExpressRoute는 온-프레미스의 모든 아웃바운드 트래픽을 라우팅합니다.
* 그리고 주소 접두사가 0.0.0.0/0이며 다음 홉 형식이 인터넷인 UDR을 만들어 ASE 서브넷에 적용합니다.

이러한 두 가지 사항을 변경하면 App Service Environment 서브넷에서 발생하는 인터넷용 트래픽이 ExpressRoute 연결로 강제 전송되지 않습니다.

네트워크가 온-프레미스에서 트래픽을 라우팅한 경우 서브넷을 만들어서 ASE를 호스팅하고 ASE를 배포하기 전에 UDR을 구성해야 합니다.  

> [!IMPORTANT]
> UDR에 정의된 경로는 ExpressRoute 구성을 통해 보급된 경로보다 우선하도록 충분히 구체적이어야 합니다. 이전 예제에서는 광범위한 0.0.0.0/0 주소 범위를 사용합니다. 따라서 더 구체적인 주소 범위를 사용하는 경로 보급 알림으로 인해 주소 범위가 잘못 재정의될 가능성이 있습니다.
>
> 공용 피어링 경로에서 프라이빗 피어링 경로로 경로의 교차 보급을 수행하는 ExpressRoute 구성에서는 App Service Environment가 지원되지 않습니다. 공용 피어링이 구성된 ExpressRoute 구성은 Microsoft에서 경로 보급 알림을 받습니다. 보급 알림에는 대규모 Microsoft Azure 주소 범위 집합이 포함되어 있습니다. 프라이빗 피어링 경로에서 주소 범위를 교차 보급하는 경우 App Service Environment 서브넷의 모든 아웃바운드 네트워크 패킷이 고객의 온-프레미스 네트워크 인프라로 라우팅됩니다. 이 네트워크 흐름은 App Service Environment에서 기본적으로 지원되지 않습니다. 이 문제를 해결하려면 공용 피어링 경로에서 프라이빗 피어링 경로로의 교차 보급 경로를 중지합니다. 다른 솔루션은 App Service Environment가 강제 터널 구성에서 작동하도록 설정하는 것입니다.

![직접 인터넷 액세스][1]

## <a name="configure-your-ase-subnet-to-ignore-bgp-routes"></a>BGP 경로를 무시하도록 ASE 서브넷 구성 ## 

BGP 경로를 무시하도록 ASE 서브넷을 구성할 수 있습니다.  BGP 경로를 무시하도록 구성된 경우 ASE는 문제 없이 해당 종속성에 액세스할 수 있습니다.  그러나 UDR을 만들어서 앱이 온-프레미스 리소스에 액세스할 수 있어야 합니다.

BGP 경로를 무시하도록 ASE 서브넷을 구성하려면:

* UDR이 없는 경우 만들고 ASE 서브넷에 할당합니다.
* Azure Portal에서 ASE 서브넷에 할당된 경로 테이블에 대한 UI를 엽니다.  구성을 선택합니다.  가상 네트워크 게이트웨이 경로 전파를 사용 안 함으로 설정합니다.  저장을 클릭합니다. 이 기능을 해제하는 방법에 대한 설명서는 [경로 테이블 만들기][routetable] 문서에 있습니다.

모든 BGP 경로를 무시하도록 ASE 서브넷을 구성하면 앱이 더 이상 온-프레미스에 연결할 수 없습니다. 앱이 온-프레미스 리소스에 액세스할 수 있도록 하려면 ASE 서브넷에 할당된 UDR을 편집하고 온-프레미스 주소 범위에 대한 경로를 추가합니다. 다음 홉 형식은 가상 네트워크 게이트웨이로 설정되어야 합니다. 


## <a name="configure-your-ase-with-service-endpoints"></a>서비스 엔드포인트를 사용하여 ASE 구성 ##

SQL Azure 및 Azure Storage로 이동 하는 트랙픽을 제외하고 ASE에서 모든 아웃바운드 트래픽을 라우팅하려면 다음 단계를 수행합니다.

1. 경로 테이블을 만들고 ASE 서브넷에 할당합니다. [App Service Environment 관리 주소][management]에서 지역과 일치하는 주소를 찾으세요. 인터넷의 다음 홉 형식을 사용해 이런 주소에 대한 경로를 만듭니다. 이러한 경로는 App Service Environment 인바운드 관리 트래픽이 전송된 동일한 주소에서 응답해야 하기 때문에 필요합니다.   

2. Azure SQL를 통해 서비스 엔드포인트 그리고 ASE 서브넷을 통해 Azure Storage를 사용하도록 설정합니다.  이 단계를 완료한 후에 강제 터널링을 사용하여 VNet을 구성할 수 있습니다.

템플릿 사용하여 ASE를 배포하는 방법에 대한 세부 정보는 [템플릿을 사용하여 App Service 환경 만들기][template]를 참조하세요.

서비스 엔드포인트를 사용하면 Azure 가상 네트워크 및 서브넷의 집합에 다중 테넌트 서비스에 대한 액세스를 제한할 수 있습니다. 서비스 엔드포인트에 대한 자세한 내용은 [Virtual Network 서비스 엔드포인트][serviceendpoints] 설명서에서 확인할 수 있습니다. 

리소스에서 서비스 엔드포인트를 사용하는 경우 다른 모든 경로에 우선해 만든 경로가 있습니다. 강제 터널링된 ASE를 통해 서비스 엔드포인트를 사용하는 경우 Azure SQL 및 Azure Storage 관리 트래픽은 강제 터널링되지 않습니다. 다른 ASE 종속성 트래픽은 강제 터널링되고 손실될 수 없습니다. 아니면 ASE가 제대로 기능하지 않습니다.

Azure SQL 인스턴스를 통해 서브넷에서 서비스 엔드포인트가 사용되는 경우 해당 서브넷에서 연결된 모든 Azure SQL 인스턴스는 서비스 엔드포인트를 사용할 수 있어야 합니다. 동일한 서브넷에서 여러 Azure SQL 인스턴스에 액세스하려는 경우 다른 Azure SQL 인스턴스가 아닌 한 Azure SQL 인스턴스에서 서비스 엔드포인트를 사용할 수 없습니다.  Azure Storage는 Azure SQL과 동일하게 동작하지 않습니다.  Azure Storage를 통해 서비스 엔드포인트를 사용하는 경우 사용자의 서브넷에서 해당 리소스에 대한 액세스를 잠글 수 있지만 서비스 엔드포인트를 사용할 수 없는 경우에도 여전히 다른 Azure Storage 계정에 액세스할 수 있습니다.  

네트워크 필터 어플라이언스를 사용해 강제 터널링을 구성하는 경우 ASE에는 Azure SQL 및 Azure Storage 외에도 종속성이 있습니다. 해당 종속성에 트래픽을 차단하면 ASE가 제대로 작동하지 않습니다.

![서비스 엔드포인트를 통한 강제 터널링][2]

## <a name="add-your-own-ips-to-the-ase-azure-sql-firewall"></a>ASE Azure SQL 방화벽에 고유한 IP 추가 ##

Azure Storage로 이동하는 트래픽을 제외하고 ASE에서 모든 아웃바운드 트래픽을 터널링하려면 다음 단계를 수행합니다.

1. 경로 테이블을 만들고 ASE 서브넷에 할당합니다. [App Service Environment 관리 주소][management]에서 지역과 일치하는 주소를 찾으세요. 인터넷의 다음 홉 형식을 사용해 이런 주소에 대한 경로를 만듭니다. 이러한 경로는 App Service Environment 인바운드 관리 트래픽이 전송된 동일한 주소에서 응답해야 하기 때문에 필요합니다. 

2. ASE 서브넷과 Azure Storage를 통해 서비스 엔드포인트 사용하도록 설정

3. App Service Environment에서 인터넷으로의 모든 아웃바운드 트래픽에 사용될 주소를 가져옵니다. 온-프레미스에서 트래픽을 라우팅하는 경우 이러한 주소는 NAT 또는 게이트웨이 IP입니다. App Service Environment 아웃바운드 트래픽을 NVA를 통해 라우팅하려면 송신 주소는 NVA의 공용 IP입니다.

4. _기존 App Service Environment에서 송신 주소를 설정하려면_ resources.azure.com으로 이동하여 Subscription/\<subscription id>/resourceGroups/\<ase resource group>/providers/Microsoft.Web/hostingEnvironments/\<ase name>로 이동합니다. 그러면 App Service Environment를 설명하는 JSON을 볼 수 있습니다. 상단에 **읽기/쓰기** 가 표시되는지 확인합니다. **편집** 을 선택합니다. 아래로 스크롤하십시오. **userWhitelistedIpRanges** 값을 **null** 에서 다음과 같은 값으로 변경합니다. 송신 주소 범위로 설정할 주소를 사용합니다. 

    ```json
    "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"]
    ```

   맨 위에서 **PUT** 을 선택합니다. 이 옵션은 App Service Environment에서 크기 조정 작업을 트리거하고 방화벽을 조정합니다.

_송신 주소를 사용하여 ASE를 만들려면_ [템플릿을 사용하여 App Service Environment 만들기][template]의 지침을 따르고 적절한 템플릿을 풀다운합니다.  "속성" 블록이 아니라 azuredeploy.json 파일에서 “리소스” 섹션을 편집하고 해당 값을 지닌 **userWhitelistedIpRanges** 에 대해 선을 포함합니다.

```json
"resources": [
    {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/hostingEnvironments",
        "name": "[parameters('aseName')]",
        "kind": "ASEV2",
        "location": "[parameters('aseLocation')]",
        "properties": {
            "name": "[parameters('aseName')]",
            "location": "[parameters('aseLocation')]",
            "ipSslAddressCount": 0,
            "internalLoadBalancingMode": "[parameters('internalLoadBalancingMode')]",
            "dnsSuffix" : "[parameters('dnsSuffix')]",
            "virtualNetwork": {
                "Id": "[parameters('existingVnetResourceId')]",
                "Subnet": "[parameters('subnetName')]"
            },
            "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]
        }
    }
]
```

이러한 변경은 ASE에서 직접 Azure Storage에 트래픽을 전송하며 ASE의 VIP 이외의 추가 주소에서 Azure SQL에 액세스를 허용합니다.

   ![SQL 허용 목록으로 강제 터널링][3]

## <a name="preventing-issues"></a>문제 방지 ##

ASE와 그 종속성 간 통신이 중단되는 경우 ASE는 비정상 상태가 됩니다.  비정상 상태가 너무 길어지면 ASE는 일시 중단됩니다. ASE를 일시 중단을 해제하려면 ASE 포털의 지침을 따르십시오.

단순한 통신 중단 외에 대기 시간이 너무 많아지면 ASE에 부정적인 영향을 미칠 수 있습니다. ASE가 온프레미스 네트워크에서 너무 멀리 있으면 대기 시간이 너무 길어질 수 있습니다.  너무 멀리 떨어진 경우의 예에는 대양이나 대륙을 가로질러 온프레미스 네트워크에 이르는 경우가 포함됩니다. 대기 시간은 인트라넷 정체 또는 아웃바운드 대역폭 제약 조건으로 인해 발생할 수도 있습니다.


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/asedependencies.png
[2]: ./media/forced-tunnel-support/forcedtunnelserviceendpoint.png
[3]: ./media/forced-tunnel-support/forcedtunnelexceptstorage.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[routetable]: ../../virtual-network/manage-route-table.md#create-a-route-table
