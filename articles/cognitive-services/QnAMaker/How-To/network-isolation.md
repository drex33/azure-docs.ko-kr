---
title: 네트워크 격리
description: 사용자는 QnA Maker 리소스에 대한 퍼블릭 액세스를 제한할 수 있습니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 87726db1f0747c3f9383168321bc3054c685c8a9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131038724"
---
# <a name="recommended-settings-for-network-isolation"></a>네트워크 격리에 대한 권장 설정

QnA Maker 리소스에 대한 공용 액세스를 제한하려면 다음 단계를 수행합니다. [가상 네트워크를 구성](../../cognitive-services-virtual-networks.md?tabs=portal)하여 퍼블릭 액세스에서 Cognitive Services 리소스를 보호합니다.

## <a name="restrict-access-to-app-service-qna-runtime"></a>App Service(QnA 런타임)에 대한 액세스 제한

ServiceTag `CognitiveServicesMangement`를 사용하여 App Service 또는 ASE(App Service Environment) 네트워크 보안 그룹 인바운드 규칙에 대한 인바운드 액세스를 제한할 수 있습니다. [가상 네트워크 서비스 태그 문서](../../../virtual-network/service-tags-overview.md)에서 서비스 태그에 대한 자세한 정보를 확인하세요. 

### <a name="regular-app-service"></a>일반 앱 서비스

1. Azure Portal에서 Cloud Shell(PowerShell)을 엽니다.
2. 페이지 하단의 PowerShell 창에서 다음 명령을 실행합니다.

```ps
Add-AzWebAppAccessRestrictionRule -ResourceGroupName "<resource group name>" -WebAppName "<app service name>" -Name "cognitive services Tag" -Priority 100 -Action Allow -ServiceTag "CognitiveServicesManagement" 
```
3.  추가된 액세스 규칙이 **네트워킹** 탭의 **액세스 제한** 섹션에 있는지 확인합니다.  

    > [!div class="mx-imgBorder"]
    > [![액세스 제한 규칙의 스크린샷.]( ../media/network-isolation/access-restrictions.png)](  ../media/network-isolation/access-restrictions.png#lightbox)

4. https://qnamaker.ai 포털의 **테스트 창** 에 액세스하려면 포털에 액세스하려는 **컴퓨터의 공용 IP 주소** 를 추가합니다. **액세스 제한** 페이지에서 **규칙 추가** 를 선택하고 클라이언트 IP에 대한 액세스를 허용합니다. 

    > [!div class="mx-imgBorder"]
    > [![공용 IP 주소가 추가된 액세스 제한 규칙의 스크린샷.]( ../media/network-isolation/public-address.png)](  ../media/network-isolation/public-address.png#lightbox)

### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>QnA Maker App Service를 호스트하도록 App Service Environment 구성

App Service Environment(ASE)는 QnA Maker App Service 인스턴스를 호스트하는 데 사용할 수 있습니다. 아래 단계를 따릅니다.

1. [새 Azure Cognitive Search 리소스](https://ms.portal.azure.com/#create/Microsoft.Search) 만들기
2. App Service를 사용하여 외부 ASE를 만듭니다.
    - 지침을 보려면 이 [App Service 빠른 시작](../../../app-service/environment/create-external-ase.md#create-an-ase-and-an-app-service-plan-together)을 따르세요. 이 프로세스는 최대 1~2시간까지 걸릴 수 있습니다.
    - 마지막으로 다음과 유사한 App Service 엔드포인트를 갖게 됩니다. `https://<app service name>.<ASE name>.p.azurewebsite.net` . 
    - 예: `https:// mywebsite.myase.p.azurewebsite.net`  
3. 다음 App Service 구성을 추가합니다.
    
    | Name                       | 값                                                     |
    |:---------------------------|:----------------------------------------------------------| 
    | PrimaryEndpointKey         | `<app service name>-PrimaryEndpointKey`                   | 
    | AzureSearchName            | `<Azure Cognitive Search Resource Name from step #1>`     | 
    | AzureSearchAdminKey        | `<Azure Cognitive Search Resource admin Key from step #1>`| 
    | QNAMAKER_EXTENSION_VERSION | `latest`                                                  |
    | DefaultAnswer              | `no answer found`                                         |

4. https://qnamaker.ai 포털 테스트 창에 대한 액세스를 허용하려면 App Service에 CORS 원본 "*"를 추가합니다. **CORS** 는 App Service 창의 API 헤더 아래에 있습니다.

    > [!div class="mx-imgBorder"]
    > [![App Service UI 내의 CORS 인터페이스 스크린샷.]( ../media/network-isolation/cross-orgin-resource-sharing.png)](  ../media/network-isolation/cross-orgin-resource-sharing.png#lightbox)

5. Azure Resource Manager를 사용하여 QnA Maker Cognitive Services 인스턴스(Microsoft.CognitiveServices/accounts)를 만듭니다. QnA Maker 엔드포인트는 위에서 만든 App Service 엔드포인트(`https:// mywebsite.myase.p.azurewebsite.net`)로 설정해야 합니다. 다음은 [참조용으로 사용할 수 있는 샘플 Azure Resource Manager 템플릿](https://github.com/pchoudhari/QnAMakerBackupRestore/tree/master/QnAMakerASEArmTemplate)입니다.

### <a name="related-questions"></a>관련 질문

#### <a name="can-qna-maker-be-deployed-to-an-internal-ase"></a>QnA Maker를 내부 ASE에 배포할 수 있나요?

외부 ASE를 사용하는 주된 이유는 QnAMaker 서비스 백 엔드(저작 API)가 인터넷을 통해 App Service에 연결할 수 있기 때문입니다. 그러나 `CognitiveServicesManagement` 서비스 태그와 연결된 주소의 연결만 허용하도록 인바운드 액세스 제한을 추가하여 여전히 보호할 수 있습니다.

내부 ASE를 계속 사용하려면 앱 게이트웨이 DNS TLS/SSL 인증서를 통해 공개 도메인의 ASE에 있는 특정 QnA Maker 앱을 노출해야 합니다. 자세한 내용은 이 [App Services의 엔터프라이즈 배포에 대한 문서](/azure/architecture/reference-architectures/enterprise-integration/ase-standard-deployment)를 참조하세요.

## <a name="restrict-access-to-cognitive-search-resource"></a>Cognitive Search 리소스에 대한 액세스 제한

Cognitive Search 인스턴스는 QnA Maker 리소스를 만든 후 프라이빗 엔드포인트를 통해 격리할 수 있습니다. 액세스를 잠그려면 다음 단계를 따르세요.

1. 새 [VNet(가상 네트워크)](https://portal.azure.com/#create/Microsoft.VirtualNetwork-ARM)을 만들거나 ASE(App Service Environment)의 기존 VNet을 사용합니다.
2. VNet 리소스를 연 다음 **서브넷** 탭에서 두 개의 서브넷을 만듭니다. 하나는 App Service용 **(appservicesubnet)** 이고 다른 서브넷은 위임 없는 Cognitive Search 리소스용 **(searchservicesubnet)** 입니다. 

    > [!div class="mx-imgBorder"]
    > [![가상 네트워크 서브넷 UI 인터페이스의 스크린샷.]( ../media/network-isolation/subnets.png)](  ../media/network-isolation/subnets.png#lightbox)

3. Cognitive Search Service 인스턴스의 **네트워킹** 탭에서 엔드포인트 연결 데이터를 퍼블릭에서 프라이빗으로 전환합니다. 이 작업은 장기 실행 프로세스이며, 완료하는 데 **최대 30분이 걸릴 수 있습니다**.

    > [!div class="mx-imgBorder"]
    > [![공개/프라이빗 전환 단추가 있는 네트워킹 UI 스크린샷.]( ../media/network-isolation/private.png)](  ../media/network-isolation/private.png#lightbox)

4. 검색 리소스가 프라이빗으로 전환되면 **프라이빗 엔드포인트** 추가를 선택합니다.
    - **기본 사항 탭**: 검색 리소스와 동일한 지역에 엔드포인트를 만들고 있는지 확인합니다.
    - **리소스 탭**: `Microsoft.Search/searchServices` 유형의 필수 검색 리소스를 선택합니다.

    > [!div class="mx-imgBorder"]
    > [ ![프라이빗 엔드포인트 만들기, UI 창의 스크린샷]( ../media/network-isolation/private-endpoint.png) ](  ../media/network-isolation/private-endpoint.png#lightbox)

    - **구성 탭**: 2단계에서 만든 VNet, 서브넷(searchservicesubnet)을 사용합니다. 그런 다음 **프라이빗 DNS 통합** 섹션에서 해당 구독을 선택하고 **privatelink.search.windows.net** 이라는 새 프라이빗 DNS 영역을 만듭니다.

     > [!div class="mx-imgBorder"]
     > [![서브넷 필드가 채워진 프라이빗 엔드포인트 UI 만들기 창의 스크린샷.]( ../media/network-isolation/subnet.png)](  ../media/network-isolation/subnet.png#lightbox)

5. 일반 App Service에 대해 VNET 통합을 사용하도록 설정합니다. ASE는 이미 VNET에 액세스할 수 있으므로 이 단계를 건너뛸 수 있습니다.
    - App Service **네트워킹** 섹션으로 이동하여 **VNet 통합** 을 엽니다.
    - 2단계에서 만든 전용 App Service VNet, 서브넷(appservicevnet)에 연결합니다.
    
     > [!div class="mx-imgBorder"]
     > [![VNET 통합 UI 스크린샷.]( ../media/network-isolation/integration.png)](  ../media/network-isolation/integration.png#lightbox)

Azure Search 리소스에 대한 [프라이빗 엔드포인트를 만듭니다](../reference-private-endpoint.md).

QnA Maker 리소스에 대한 공용 액세스를 제한하려면 다음 단계를 수행합니다. [가상 네트워크를 구성](../../cognitive-services-virtual-networks.md?tabs=portal)하여 퍼블릭 액세스에서 Cognitive Services 리소스를 보호합니다.

VNet을 기반으로 Cognitive Service 리소스에 대한 액세스를 제한한 후 온-프레미스 네트워크 또는 로컬 브라우저에서 https://qnamaker.ai 포털의 기술 자료를 탐색합니다.
- [온-프레미스 네트워크](../../cognitive-services-virtual-networks.md?tabs=portal#configuring-access-from-on-premises-networks)에 대한 액세스 권한을 부여합니다.
- [로컬 브라우저/컴퓨터](../../cognitive-services-virtual-networks.md?tabs=portal#managing-ip-network-rules)에 대한 액세스 권한을 부여합니다.
- **네트워킹** 탭의 **방화벽 섹션에서 컴퓨터의 공용 IP 주소** 를 추가합니다. 기본적으로 `portal.azure.com`은 현재 검색 시스템의 공용 IP를 표시하고(이 항목 선택) **저장** 을 선택합니다.

     > [!div class="mx-imgBorder"]
     > [![방화벽 및 가상 네트워크 구성 UI 스크린샷.]( ../media/network-isolation/firewall.png)](  ../media/network-isolation/firewall.png#lightbox)
