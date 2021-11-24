---
title: '빠른 시작: Azure Front Door 표준/프리미엄 프로필 만들기 - Azure Portal'
description: 이 빠른 시작에서는 Azure Portal을 사용하여 Azure Front Door 표준/프리미엄 서비스를 고가용성, 고성능 글로벌 웹 애플리케이션에 사용하는 방법을 보여줍니다.
services: frontdoor
author: duongau
manager: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2021
ms.author: duau
ms.custom: mode-other
ms.openlocfilehash: d15120cb22c68d7e576cfc9a6483ed9573b95150
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133041704"
---
# <a name="quickstart-create-an-azure-front-door-standardpremium-profile---azure-portal"></a>빠른 시작: Azure Front Door 표준/프리미엄 프로필 만들기 - Azure Portal

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [Azure Front Door 문서](../front-door-overview.md)를 참조하세요.

이 빠른 시작에서는 Azure Portal을 사용하여 Azure Front Door 표준/프리미엄 프로필을 만드는 방법을 알아봅니다. 기본 구성을 사용하는 *빨리 만들기* 또는 고급 구성을 사용하는 *사용자 지정 만들기* 를 통해 Azure Front Door 표준/프리미엄 프로필을 만들 수 있습니다. *사용자 지정 만들기* 를 사용하는 경우 두 개의 Web Apps를 배포합니다. 다음으로 두 개의 Web Apps를 원본으로 사용하여 Azure Front Door 표준/프리미엄 프로필을 만듭니다. 그런 다음, Azure Front Door 표준/프리미엄 프런트 엔드 호스트 이름을 사용하여 Web Apps에 대한 연결을 확인합니다.

> [!IMPORTANT]
> Azure Front Door 표준/프리미엄(미리 보기)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-front-door-profile---quick-create"></a>Front Door 프로필 만들기 - 빨리 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 홈 페이지 또는 Azure 메뉴에서  **+ 리소스 만들기** 를 선택합니다.  *Front Door 표준/프리미엄(미리 보기)* 을 검색합니다. 그런 다음 **만들기** 를 선택합니다.

1. **제품 비교** 페이지에서 **빨리 만들기** 를 선택합니다. 그런 다음, **계속 Front Door 만들기** 를 선택합니다.

   :::image type="content" source="../media/create-front-door-portal/front-door-quick-create.png" alt-text="제품 비교 스크린샷":::

1. **Front Door 프로필 만들기** 페이지에서 다음 설정을 입력하거나 선택합니다.

    :::image type="content" source="../media/create-front-door-portal/front-door-quick-create-2.png" alt-text="Front Door 빠른 만들기 페이지의 스크린샷":::    

    | 설정 | 값 |
    | --- | --- |
    | **구독**  | 구독을 선택합니다. |
    | **리소스 그룹**  | **새로 만들기** 를 선택하고, 텍스트 상자에 *contoso-appservice* 를 입력합니다.|
    | **이름** | 프로젝트 이름을 지정합니다. 이 예제에서는 **contoso-afd-quickcreate** 를 사용합니다. |
    | **계층** | 표준 또는 프리미엄 SKU를 선택합니다. 표준 SKU는 콘텐츠 전송에 최적화되어 있습니다. 프리미엄 SKU는 표준 SKU를 기반으로 하며 보안에 초점을 맞추고 있습니다. [계층 비교](tier-comparison.md)를 참조하세요.  |
    | **엔드포인트 이름** | 엔드포인트의 전역적으로 고유한 이름을 입력합니다. |
    | **원본 형식** | 원본의 리소스 종류를 선택합니다. 이 예제에서는 Private Link가 설정된 원본으로 앱 서비스를 선택합니다. |
    | **원래 호스트 이름** | 원본의 호스트 이름을 입력합니다. |
    | **Private Link 사용** | Azure Front Door와 원본 간에 프라이빗 연결을 원하는 경우에 선택합니다. 자세한 내용은 [프라이빗 링크 지침](concept-private-link.md) 및 [프라이빗 링크 사용](./how-to-enable-private-link-web-app.md)을 참조하세요.
    | **캐싱** | Azure Front Door의 에지 POP 및 Microsoft 네트워크를 사용하여 콘텐츠를 사용자와 더 가깝게 전역적으로 캐시하려면 이 확인란을 선택합니다. |
    | **WAF 정책** | 이 기능을 사용하려면 **새로 만들기** 를 선택하거나 드롭다운에서 기존 WAF 정책을 선택합니다. |

    > [!NOTE]
    > Azure Front Door 표준/프리미엄 프로필을 만들 때 Front Door를 만든 구독에서 원본을 선택해야 합니다.

1. **검토 + 만들기** 를 선택하여 Front Door 프로필을 가동합니다.

   > [!NOTE]
   > 구성이 모든 에지 POP에 전파될 때까지 몇 분 정도 걸릴 수 있습니다.

1. 그런 다음, **만들기** 를 클릭하여 Front Door 프로필을 배포하고 실행합니다.

1. Private Link를 사용하도록 설정한 경우 원본(이 예제에서는 앱 서비스)으로 이동합니다. **네트워킹** > **Private Link 구성** 을 선택합니다. 그런 다음, Azure Front Door에서 보류 중인 요청을 선택하고 [승인]을 클릭합니다. 몇 초 후 Azure Front Door를 통해 안전한 방식으로 애플리케이션에 액세스할 수 있습니다.

## <a name="create-front-door-profile---custom-create"></a>Front Door 프로필 만들기 - 사용자 지정 만들기

### <a name="create-a-web-app-with-two-instances-as-the-origin"></a>두 개의 인스턴스를 원본으로 사용하여 웹앱 만들기

원본 또는 원본 그룹이 이미 구성되어 있으면 애플리케이션의 Front Door 표준/프리미엄(미리 보기) 만들기로 건너뜁니다.

이 예제에서는 서로 다른 Azure 지역에서 실행되는 두 개의 인스턴스를 사용하여 웹 애플리케이션을 만듭니다. 두 웹 애플리케이션 인스턴스는 모두 *활성/활성* 모드로 실행되므로 두 인스턴스 중 하나에서 트래픽을 수행할 수 있습니다. 이 구성은 장애 조치로 작동하는 *활성/대기* 구성과 다릅니다.

웹앱이 아직 없는 경우 다음 단계에 따라 예제 웹앱을 설정합니다.

1. [https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.

1. 화면 왼쪽 상단에서 **리소스 만들기** > **WebApp** 을 차례로 선택합니다.

1. **웹앱 만들기** 페이지의 **기본 사항** 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정                 | 값                                              |
    | ---                     | ---                                                |
    | **구독**               | 구독을 선택합니다. |
    | **리소스 그룹**       | **새로 만들기** 를 선택하고 텍스트 상자에 *FrontDoorQS_rg1* 을 입력합니다.|
    | **이름**                   | 웹앱에 대한 고유한 **이름** 을 입력합니다. 이 예제에서는 *WebAppContoso-001* 을 사용합니다. |
    | **게시** | **코드** 를 선택합니다. |
    | **런타임 스택**         | **.NET Core 2.1(LTS)** 을 선택합니다. |
    | **운영 체제**          | **Windows** 를 선택합니다. |
    | **지역**           | **미국 중부** 를 선택합니다. |
    | **Windows 플랜** | **새로 만들기** 를 선택하고 텍스트 상자에 *myAppServicePlanCentralUS* 를 입력합니다. |
    | **SKU 및 크기** | **Standard S1 100 총 ACU, 1.75GB 메모리** 를 선택합니다. |

     :::image type="content" source="../media/create-front-door-portal/create-web-app.png" alt-text="Azure Portal에서 Front Door 프리미엄 SKU 빨리 만들기":::

1. **검토 + 만들기** 를 선택하고 요약을 검토한 다음, **만들기** 를 선택합니다. 배포하는 데 몇 분 정도 걸릴 수 있습니다.

배포가 완료되면 두 번째 웹앱을 만듭니다. 다음 설정을 제외하고 위와 동일한 설정을 사용합니다.

| 설정          | 값     |
| ---              | ---  |
| **리소스 그룹**   | **새로 만들기** 를 선택하고 *FrontDoorQS_rg2* 를 입력합니다. |
| **이름**             | 웹앱의 고유한 이름을 입력합니다(이 예제에서는 *WebAppContoso-002*).  |
| **지역**           | 다른 지역(이 예에서는 *미국 중남부*) |
| **App Service 계획** > **Windows 플랜**         | **새로 만들기** 를 선택하고 *myAppServicePlanSouthCentralUS* 를 입력한 다음, **확인** 을 선택합니다. |

### <a name="create-a-front-door-standardpremium-preview-for-your-application"></a>애플리케이션의 Front Door 표준/프리미엄(미리 보기) 만들기

두 웹앱 서버 간의 가장 짧은 대기 시간을 기준으로 사용자 트래픽을 보내도록 Azure Front Door 표준/프리미엄(미리 보기)을 구성합니다. 또한 Web Application Firewall로 Front Door를 보호합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
 
1. 홈 페이지 또는 Azure 메뉴에서  **+ 리소스 만들기** 를 선택합니다.  *Front Door 표준/프리미엄(미리 보기)* 을 검색합니다. 그런 다음 **만들기** 를 선택합니다.

1. **제품 비교** 페이지에서 **사용자 지정 만들기** 를 선택합니다. 그런 다음, **계속 Front Door 만들기** 를 선택합니다.

1.  **기본 사항**  탭에서 다음 정보를 입력하거나 선택하고  **다음: 비밀** 을 선택합니다. 

    | 설정 | 값 |
    | --- | --- |
    | **구독** | 구독을 선택합니다. |
    | **리소스 그룹** | **새로 만들기** 를 선택하고 텍스트 상자에 *FrontDoorQS_rg0* 을 입력합니다. |
    | **리소스 그룹 위치** | **미국 동부** 를 선택합니다. |
    | **프로필 이름** | 이 구독의 고유한 이름 **Webapp-Contoso-AFD** 입력 |
    | **계층** | **프리미엄** 을 선택합니다. |
    
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-2.png" alt-text="Front Door 프로필 만들기":::

1. *선택 사항*: **비밀**. 관리형 인증서를 사용하려는 경우 이 단계는 선택 사항입니다. 사용자 지정 도메인의 Bring Your Own Certificate로 사용하려는 기존 Key Vault가 Azure에 있는 경우 **인증서 추가** 를 선택합니다. 만든 후에 관리 환경에서 인증서를 추가할 수도 있습니다.

    > [!NOTE]
    > Azure Key Vault에서 사용자로 인증서를 추가할 수 있는 권한이 있어야 합니다. 

    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-secret.png" alt-text="사용자 지정 만들기에서 비밀 추가의 스크린샷":::

1. **엔드포인트** 탭에서 **엔드포인트 추가** 를 선택하고 전역적으로 고유한 엔드포인트 이름을 지정합니다. 환경 만들기를 마친 후 Azure Front Door 표준/프리미엄 프로필에서 여러 엔드포인트를 만들 수 있습니다. 이 예에서는 *contoso-frontend* 를 사용합니다. 원본 응답 제한 시간(초) 및 상태를 기본값으로 둡니다. **추가** 를 선택하여 엔드포인트를 추가합니다.
    
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-endpoint.png" alt-text="엔드포인트 추가의 스크린샷":::

1. 다음으로 두 개의 웹앱을 포함하는 원본 그룹을 추가합니다.  **+ 추가** 를 선택하여  **원본 그룹 추가** 페이지를 엽니다. 이름의 경우  *myOriginGroup* 을 입력한 다음,  **+ 원본 추가** 를 선택합니다.
 
     :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-group.png" alt-text="원본 그룹 추가의 스크린샷":::

1.  **원본 추가**  페이지에서 아래 정보를 입력하거나 선택합니다. 그런 다음, **추가** 를 선택합니다.

    | 설정 | 값 |
    | --- | --- |
    | **이름** | **webapp1** 입력 |
    | **원본 형식** | **앱 서비스** 선택 |
    | **호스트 이름** | `WebAppContoso-001.azurewebsites.net` 선택 |
    | **원본 호스트 헤더** | `WebAppContoso-001.azurewebsites.net` 선택 |
    | **기타 필드** | 그 외의 모든 필드는 기본값으로 둡니다. |

    > [!NOTE]
    > Azure Front Door 표준/프리미엄 프로필을 만들 때 Azure Front Door 표준/프리미엄을 만든 구독에서 원본을 선택해야 합니다.
   
   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-1.png" alt-text="더 많은 원본 추가의 스크린샷":::

1. 8단계를 반복하여 두 번째 원본 webapp002를 추가합니다. **원본 호스트 이름** 및 **원본 호스트 헤더** 로 `webappcontoso-002.azurewebsite.net`을 선택합니다.

1. **원본 그룹 추가** 페이지에 두 개의 원본이 추가된 것을 볼 수 있습니다. 나머지 필드는 모두 기본값으로 둡니다.
  
   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-group-2.png" alt-text="원본 그룹 추가 페이지의 스크린샷":::

1. 다음으로 프런트 엔드 엔드포인트를 원본 그룹에 매핑하도록 경로를 추가합니다. 이 경로는 엔드포인트에서 myOriginGroup으로 요청을 전달합니다. 경로에서 **+ 추가** 를 선택하여 경로를 구성합니다.  

1. **경로 추가** 페이지에서 아래 정보를 입력하거나 선택합니다. 그런 다음, **추가** 를 선택합니다.
  
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-route-without-caching.png" alt-text="캐싱 없이 경로 추가":::

    | 설정 | 값 |
    | --- | --- |
    | **이름** | **MyRoute** 입력 |    
    | **도메인** | `contoso-frontend.z01.azurefd.net` 선택 | 
    | **호스트 이름** | `WebAppContoso-001.azurewebsites.net` 선택 |
    | **일치할 패턴** | 기본값으로 둡니다. |
    | **허용되는 프로토콜** | 기본값으로 둡니다. | 
    | **리디렉션** | **HTTPS를 사용하도록 모든 트래픽 리디렉션** 을 기본값으로 둡니다. | 
    | **원본 그룹** | **MyOriginGroup** 을 선택합니다. | 
    | **원본 경로** | 기본값으로 둡니다. | 
    | **전달 프로토콜** | **들어오는 요청 일치** 를 선택합니다. | 
    | **캐싱** | 이 빠른 시작에서는 선택하지 않습니다. 콘텐츠를 에지에서 캐시하려면 **캐싱 사용** 확인란을 선택합니다. |
    | **규칙.** | 기본값으로 둡니다. Front Door 프로필을 만든 후에는 사용자 지정 규칙을 만들어 경로에 적용할 수 있습니다. |  
       
    >[!WARNING]
    > 엔드포인트마다 경로가 있는지 **확인** 합니다. 경로가 없으면 엔드포인트에서 오류가 발생할 수 있습니다.

1. 다음으로 보안에서 **+ 추가** 를 선택하여 WAF 정책을 추가합니다. **새로 추가** 를 선택하고 정책의 고유한 이름을 지정합니다. **봇 보호 추가** 의 확인란을 선택합니다. **도메인** 에서 엔드포인트를 선택한 다음, **추가** 를 선택합니다.

   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-waf-policy-2.png" alt-text="WAF 정책 추가":::

1.  **검토 + 만들기** 를 선택한 다음,  **만들기** 를 선택합니다. 구성이 모든 에지 POP에 전파될 때까지 몇 분 정도 걸립니다. 드디어 첫 번째 Front Door 프로필과 엔드포인트가 생겼습니다.

   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-review.png" alt-text="사용자 지정 만들기 검토":::

## <a name="verify-azure-front-door"></a>Azure Front Door 확인

Azure Front Door 표준/프리미엄 프로필을 만들 때 구성이 전역적으로 배포되는 데 몇 분 정도 걸립니다. 배포가 완료되면 앞에서 만든 프런트 엔드 호스트에 액세스할 수 있습니다. 브라우저에서 `contoso-frontend.z01.azurefd.net`으로 이동합니다. 원본 그룹에 지정된 서버와 가장 가까운 서버로 요청이 자동 라우팅됩니다.

이 빠른 시작에서 이러한 앱을 만든 경우 정보 페이지가 표시됩니다.

즉각적인 글로벌 장애 조치(failover)를 테스트하기 위해 다음 단계를 수행합니다.

1. 위에서 설명한 대로 브라우저를 열고, 프런트 엔드 주소(`contoso-frontend.azurefd.net`)로 이동합니다.

1. Azure Portal에서 *앱 서비스* 를 검색하여 선택합니다. 아래로 스크롤하여 웹앱 중 하나(이 예제에서는 **WebAppContoso-001**)를 찾습니다.

1. 웹앱을 선택한 다음, **중지**, **예** 를 차례로 선택하여 확인합니다.

1. 브라우저를 새로 고칩니다. 동일한 정보 페이지가 표시됩니다.

   >[!TIP]
   >이러한 작업에는 약간의 지연이 있습니다. 다시 새로 고쳐야 할 수도 있습니다.

1. 다른 웹앱도 찾아서 중지합니다.

1. 브라우저를 새로 고칩니다. 이번에는 오류 메시지가 표시됩니다.

    :::image type="content" source="../media/create-front-door-portal/web-app-stopped-message.png" alt-text="웹앱의 두 인스턴스가 모두 중지됨":::

## <a name="clean-up-resources"></a>리소스 정리

작업을 완료한 후에는 생성한 모든 항목을 제거할 수 있습니다. 리소스 그룹을 삭제하면 해당 콘텐츠도 삭제됩니다. 이 Front Door를 사용하지 않으려면 필요한 요금이 청구되지 않도록 리소스를 제거해야 합니다.

1. Azure Portal에서 **리소스 그룹** 을 검색하여 선택하거나 Azure Portal 메뉴에서 **리소스 그룹** 을 선택합니다.

1. 필터링하거나 아래로 스크롤하여 리소스 그룹(예: **FrontDoorQS_rg0**)을 찾습니다.

1. 리소스 그룹을 선택한 다음, **리소스 그룹 삭제** 를 선택합니다.

   >[!WARNING]
   >이 작업은 되돌릴 수 없습니다.

1. 확인할 리소스 그룹 이름을 입력한 다음, **삭제** 를 선택합니다.

다른 두 그룹에 대해 이 절차를 반복합니다.

## <a name="next-steps"></a>다음 단계

사용자 지정 도메인을 Front Door에 추가하는 방법을 알아보려면 다음 문서로 계속 진행하세요.
> [!div class="nextstepaction"]
> [사용자 지정 도메인 추가](how-to-add-custom-domain.md)
