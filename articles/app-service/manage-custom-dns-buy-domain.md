---
title: 사용자 지정 도메인 이름 구매
description: App Service 도메인을 사서 앱의 Azure App Service용 사용자 지정 도메인으로 사용하는 방법을 알아봅니다.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/30/2020
ms.custom: seodec18
ms.openlocfilehash: 03dd990fdc6d5e2d274a0ea858d1d91feb5a0b07
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129659890"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Azure App Service의 사용자 지정 도메인 이름 구입

App Service 도메인은 Azure에서 직접 관리하는 사용자 지정 도메인입니다. 이 도메인을 통해 [Azure App Service](overview.md)의 사용자 지정 도메인을 쉽게 관리할 수 있습니다. 이 자습서에서는 App Service 도메인을 구입하고 DNS 이름을 Azure App Service에 할당하는 방법을 보여 줍니다.

Azure VM 또는 Azure Storage의 경우 [Azure VM 또는 Azure Storage에 App Service 도메인 할당](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage)을 참조하세요. Cloud Services의 경우 [Azure 클라우드 서비스에 대한 사용자 지정 도메인 이름 구성](../cloud-services/cloud-services-custom-domain-name-portal.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음이 필요합니다.

* [App Service 앱을 만들거나](./index.yml) 다른 자습서에서 만든 앱을 사용합니다. 해당 앱이 Azure 퍼블릭 지역에 있어야 합니다. 현재로서는 Azure 국가별 클라우드는 지원하지 않습니다.
* [구독에 대한 지출 한도를 삭제합니다](../cost-management-billing/manage/spending-limit.md#remove). 체험 구독 크레딧으로는 App Service 도메인을 구입할 수 없습니다.

## <a name="buy-an-app-service-domain"></a>App Service 도메인 구매

App Service 도메인의 가격 책정 정보를 확인하려면 [App Service 가격 책정 페이지](https://azure.microsoft.com/pricing/details/app-service/windows/)를 방문하여 App Service 도메인이 나올 때까지 스크롤을 내립니다.

1. [Azure 포털](https://portal.azure.com)에서 Azure 계정으로 로그인합니다.

1. 검색 창에서, **App Service 도메인** 을 찾아 선택합니다.

    ![Azure App Service 도메인으로 포털 탐색](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. **App Service 도메인** 보기에서, **추가** 를 클릭합니다.

    ![App Service 도메인의 앱을 클릭합니다.](./media/app-service-web-tutorial-custom-domain/add-app-service-domain.png)

1. **클릭하여 App Service 도메인 생성 환경 최신 버전 사용하여 보기** 를 선택합니다.

    ![새로운 환경의 App Service 도메인 만들기](./media/app-service-web-tutorial-custom-domain/select-new-create-experience.png)

### <a name="basics-tab"></a>기본 사항 탭

1. **기본** 탭에서 다음 테이블에 따라 설정을 구성합니다.  

   | 설정  | 설명 |
   | -------- | ----------- |
   | **구독** | 도메인 구매에 사용할 구독입니다. |
   | **리소스 그룹** | 도메인을 배치할 리소스 그룹입니다. 예를 들면, 앱이 있는 리소스 그룹입니다. |
   | **도메인** | 원하는 도메인을 입력합니다. 예를 들어 **contoso.com** 입니다. 원하는 도메인을 사용할 수 없는 경우 사용할 수 있는 도메인에 대한 제안 목록에서 선택하거나 다른 도메인을 시도하여 볼 수 있습니다. |

    > [!NOTE]
    > App Service 도메인에서 지원되는 [최상위 도메인](https://wikipedia.org/wiki/Top-level_domain)은 _com_, _net_, _co.uk_, _org_, _nl_, _in_, _biz_, _org.uk_ 및 _co.in_ 입니다.
    >
    >
    
2. 완료되면 **다음: 연락처 정보** 를 클릭하세요.

### <a name="contact-information-tab"></a>연락처 정보 탭

1. [ICANN](https://go.microsoft.com/fwlink/?linkid=2116641)에서 요구하는대로 도메인 등록에 필요한 정보를 제공합니다. 

    모든 필수 필드를 가능한 한 정확하게 기입해야 합니다. 연락처 정보가 정확하지 않으면 도메인을 구매하지 못하게 될 수도 있습니다.

1. 완료되면, **다음: 고급** 을 클릭합니다.

### <a name="advanced-tab"></a>고급 탭

1. **고급** 탭에서 다음 옵션 설정을 구성합니다.  

   | 설정  | Description |
   | -------- | ----------- |
   | **자동 갱신** | 기본적으로 사용하도록 설정되어 있습니다. App Service 도메인은 1년 간격으로 등록됩니다. 자동 갱신은 도메인 등록이 만료되지 않도록 하여 해당 도메인의 소유권을 유지시킵니다. 갱신 시점에 연간 도메인 등록 요금이 Azure 구독에 자동으로 청구됩니다. 옵트아웃 하려면 **사용하지 않음** 을 선택합니다. 자동 갱신을 사용하지 않도록 설정한 경우 [수동으로 갱신](#renew-the-domain)할 수 있습니다. |
   | **개인 정보 보호** | 기본적으로 사용하도록 설정되어 있습니다. 개인 정보 보호는 WHOIS 데이터베이스에서 도메인 등록 연락 정보를 숨깁니다. 개인 정보 보호는 이미 연간 도메인 등록 요금에 포함되어 있습니다. 옵트아웃 하려면 **사용하지 않음** 을 선택합니다. |

2. 완료하면 **다음: 태그** 를 클릭합니다.

### <a name="finish"></a>Finish

1. **태그** 탭에서, App Service 도메인에 설정하려는 태그를 설정합니다. 태그 설정이 App Service 도메인 이용에 필수적인 것은 아니지만, [Azure에서 리소스 관리를 도와주는 기능이긴 합니다](../azure-resource-manager/management/tag-resources.md).

1. **다음: 검토+생성** 을 클릭합니다.

1. **검토+생성** 탭에서 도메인 순서를 검토합니다. 작업을 마쳤으면 **만들기** 를 클릭합니다.

    > [!NOTE]
    > App Service 도메인은 GoDaddy를 사용하여 등록하며 Azure DNS를 사용하여 해당 도메인을 호스트합니다. 연간 도메인 등록 요금 이외에, Azure DNS 사용량에 대한 요금도 적용됩니다. 자세한 내용은 [Azure DNS 가격 책정](https://azure.microsoft.com/pricing/details/dns/)을 참조하세요.
    >
    >

1. 도메인 등록이 완료되면 **리소스로 이동** 단추가 표시됩니다. 선택하여 관리 페이지를 확인합니다.

    ![App Service 도메인이 완성되었습니다. 리소스로 이동](./media/app-service-web-tutorial-custom-domain/deployment-complete.png)

이제 사용자 지정 도메인에 App Service 앱을 할당할 준비가 되었습니다.

> [!NOTE]
> 구독 유형에 따라 App Service 도메인을 만들기 전에 충분한 지불 기록이 필요할 수 있습니다. 
> 
> 결제를 했고 이 오류가 계속 발생하면 지원 담당자에게 문의하여 지불 증명을 제공할 수 있습니다.

## <a name="prepare-the-app"></a>앱 준비

사용자 지정 DNS 이름을 웹앱에 매핑하려면 웹앱의 [App Service 요금제](https://azure.microsoft.com/pricing/details/app-service/)가 유료 계층(공유, 기본, 표준, 프리미엄 또는 Azure Functions의 경우 사용량)이어야 합니다. 이 단계에서는 App Service 앱이 지원되는 가격 책정 계층에 속하는지 확인해야 합니다.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Azure Portal에서 앱으로 이동합니다.

1. 위쪽 검색 창에서 **App Services** 를 찾아 선택합니다.

    ![App Services 검색](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. 해당 앱의 이름을 선택합니다.

    ![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/select-app.png)

    App Service 앱의 관리 페이지가 표시됩니다.  

### <a name="check-the-pricing-tier"></a>가격 책정 계층 확인

1. 앱 페이지의 왼쪽 탐색 영역에서 **설정** 섹션으로 스크롤하고 **강화(App Service 계획)** 를 선택합니다.

    ![강화 메뉴](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. 앱의 현재 계층은 파란색 테두리로 강조 표시됩니다. 앱이 **F1** 계층에 속해 있지 않은지 확인합니다. 사용자 지정 DNS는 **F1** 계층에서는 지원되지 않습니다. 

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png" alt-text="스케일 업(App Service 플랜)을 선택한 상태의 앱 페이지 왼쪽 탐색 메뉴의 스크린샷":::

1. App Service 계획이 **F1** 계층이 아닌 경우 **스케일업** 페이지를 닫고 [도메인 구입](#buy-an-app-service-domain)으로 건너뜁니다.

### <a name="scale-up-the-app-service-plan"></a>강화 - App Service 계획

1. 유료 계층(**D1**, **B1**, **B2**, **B3** 또는 **프로덕션** 범주의 모든 계층) 중 하나를 선택합니다. 추가 옵션을 보려면 **추가 옵션 보기** 를 클릭합니다.

1. **적용** 을 클릭합니다.

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png" alt-text="프로덕션 범주에서 프로덕션 탭, B1 플랜, 적용 단추가 강조되어 있는 사용자 지정 가격 책정 계층 스크린샷":::

    다음 알림이 표시되면 강화 작업이 완료됩니다.

    ![크기 조정 작업 확인](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="map-app-service-domain-to-your-app"></a>앱에 App Service 도메인 매핑하기

동일한 구독에 속하는 동안에는 App Service 도메인의 호스트 이름을 App Service 앱에 간단히 매핑할 수 있습니다. App Service 도메인이나 그 하위 도메인을 앱에 바로 매핑하면 Azure에서 필요한 DNS 레코드를 만들어 줍니다.

> [!NOTE]
> 도메인과 앱이 서로 다른 구독에 속하는 경우, [외부에서 구매한 도메인을 매핑](app-service-web-tutorial-custom-domain.md)하는 것과 같은 방식으로 App Service 도메인을 앱에 매핑합니다. 이 때, Azure DNS가 외부 도메인 공급자가 되며 [필요한 DNS 레코드를 수동으로 추가](#manage-custom-dns-records)하여야 합니다.
>

### <a name="map-the-domain"></a>도메인 매핑

1. 앱 페이지 왼쪽의 탐색 영역에서 **설정** 섹션으로 스크롤한 다음 **사용자 지정 도메인** 을 선택합니다.

    ![사용자 지정 도메인 메뉴를 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. **사용자 지정 도메인 추가** 를 선택합니다.

    ![호스트 이름 추가 항목을 보여주는 스크린샷](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. App Service 도메인(**contoso.com** 등) 또는 하위 도메인(**www.contoso.com** 등)을 입력하고 **유효성 검사** 를 클릭합니다.

    > [!NOTE]
    > App Service 도메인 이름의 철자가 잘못되면, 일부 DNS 레코드를 누락하였다는 확인 오류가 페이지 하단에 표시됩니다. App Service 도메인에 대하여 해당 레코드를 손수 추가할 필요는 없습니다. 도메인 이름을 정확하게 입력하였는지 확인한 다음 **유효성 검사** 를 한 번 더 클릭하기만 하면 됩니다.
    >
    > ![확인 오류를 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

1. **호스트 이름 레코드 형식** 을 수락하고 **사용자 지정 도메인** 을 클릭합니다.

    ![사용자 지정 도메인 추가 단추를 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

1. 새 사용자 지정 도메인이 앱의 **사용자 지정 도메인** 페이지에 반영될 때까지 약간의 시간이 걸릴 수 있습니다. 데이터를 업데이트하려면 브라우저를 새로 고칩니다.

    ![CNAME 레코드 추가를 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > 사용자 지정 도메인에 붙은 **안전하지 않음** 레이블은 아직 TLS/SSL 인증서에 바인딩되지 않았다는 뜻입니다. 브라우저에서 사용자 지정 도메인으로 전송되는 HTTPS 요청은 브라우저에 따라 오류 또는 경고를 수신합니다. TLS 바인딩을 추가하려면 [Azure App Service에서 TLS/SSL 바인딩으로 사용자 지정 DNS 이름 보호](configure-ssl-bindings.md)를 참조하세요.
    
### <a name="test-the-custom-domain"></a>사용자 지정 도메인 테스트

사용자 지정 도메인을 테스트하려면 브라우저에서 해당 도메인으로 이동합니다.

## <a name="renew-the-domain"></a>도메인 갱신

구입한 App Service 도메인은 구입 시점으로부터 1년 동안 유효합니다. 도메인은 기본적으로 다음 해의 결제 방법을 부과하여 자동으로 갱신되도록 구성됩니다. 도메인 이름을 수동으로 갱신할 수 있습니다.

자동 갱신을 해제하거나 도메인을 수동으로 갱신하려면 다음 단계를 따릅니다.

1. 검색 창에서, **App Service 도메인** 을 찾아 선택합니다.

    ![Azure App Service 도메인으로 포털 탐색](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. **App Service 도메인** 섹션에서 구성하려는 도메인을 선택합니다.

1. 도메인의 왼쪽 탐색 영역에서 **도메인 갱신** 을 선택합니다. 도메인 자동 갱신을 중지하려면 **끄기** 를 선택합니다. 설정은 즉시 적용됩니다.

    ![도메인 자동 갱신 옵션을 보여 주는 스크린샷](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

    > [!NOTE]
    > 페이지에서 나가는 경우에는 **확인** 을 클릭하여 “저장하지 않은 편집 내용은 삭제됩니다” 오류를 무시합니다.
    >

도메인을 수동으로 갱신하려면 **도메인 갱신** 을 선택합니다. 그러나, [해당 도메인의 만료로부터 90일 전](#when-domain-expires)까지 해당 단추는 활성화되지 않습니다.

도메인 갱신이 성공하면 24시간 내에 메일 알림을 받게 됩니다.

## <a name="when-domain-expires"></a>도메인이 만료될 때

Azure는 만료되어 가거나 만료된 App Service 도메인에 대하여 다음과 같이 처리합니다.

* 자동 갱신 사용 중이 아닐 경우: 도메인 만료 90일 전에 메일로 갱신 알림이 전달되며 포털에 **도메인 갱신** 단추가 활성화됩니다.
* 자동 갱신 사용 중일 경우: 도메인 만료일 다음날에 Azure에서 해당 도메인 이름 갱신에 대한 청구서 전달을 시도합니다.
* 자동 갱신 중에 오류가 발생한 경우(예를 들어, 파일 상의 카드가 만료된 경우)나 자동 갱신 사용 중이 아니면서 해당 도메인이 만료되도록 놔둘 경우, Azure에서는 해당 도메인의 만료에 대하여 알리고 도메인 이름을 파킹합니다. 도메인은 [수동으로 갱신](#renew-the-domain)할 수도 있습니다.
* 만료 후 4일과 12일째 되는 날에 Azure에서는 메일을 통하여 추가적으로 알림을 보냅니다. 도메인은 [수동으로 갱신](#renew-the-domain)할 수도 있습니다. 만료 후 5일째 되는 날에는 만료된 도메인에 대한 DNS 확인이 중지됩니다.
* 만료 후 19일째 되는 날에는 도메인을 보류 상태로 유지하되 상환 요금이 적용됩니다. 고객 지원에 문의하여 적용되는 갱신 및 상환 요금에 따라 도메인 이름을 갱신할 수 있습니다.
* 만료 후 25일째 되는 날에는 Azure에서 도메인 이름 업계 경매 서비스를 통하여 도메인을 경매에 올립니다. 고객 지원에 문의하여 적용되는 갱신 및 상환 요금에 따라 도메인 이름을 갱신할 수 있습니다.
* 만료 후 30일째가 되면 더이상 도메인을 사용할 수 없게 됩니다.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>사용자 지정 DNS 레코드 관리

Azure에서 App Service 도메인에 대한 DNS 레코드는 [Azure DNS](https://azure.microsoft.com/services/dns/)를 사용하여 관리됩니다. 외부에서 구매한 도메인처럼 DNS 레코드를 추가, 제거 및 업데이트할 수 있습니다. 사용자 지정 DNS 레코드를 관리하려면:

1. 검색 창에서, **App Service 도메인** 을 찾아 선택합니다.

    ![Azure App Service 도메인으로 포털 탐색](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. **App Service 도메인** 섹션에서 구성하려는 도메인을 선택합니다.

1. **개요** 페이지에서, **DNS 레코드 관리** 를 선택합니다.

    ![DNS 레코드에 액세스할 수 있는 위치를 보여 주는 스크린샷](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

DNS 레코드를 편집하는 방법에 대한 자세한 내용은 [Azure Portal에서 DNS 영역을 관리하는 방법](../dns/dns-operations-dnszones-portal.md)을 참조하세요.

## <a name="cancel-purchase-delete-domain"></a>구매 취소(도메인 삭제)

App Service 도메인을 구매한 후 5일 이내에 구매를 취소하고 전액 환불 받을 수 있습니다. 5일이 지나면 App Service 도메인을 삭제할 수 있지만 환불은 불가능합니다.

1. 검색 창에서, **App Service 도메인** 을 찾아 선택합니다.

    ![Azure App Service 도메인으로 포털 탐색](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. **App Service 도메인** 섹션에서 구성하려는 도메인을 선택합니다.

1. 도메인 왼쪽 탐색 영역에서 **잠금** 을 선택합니다. 

    도메인에 대한 삭제 잠금이 생성되었습니다. 삭제 잠금이 존재하는 한 App Service 도메인을 삭제할 수 없습니다.

1. **삭제** 를 클릭하여 잠금을 제거합니다.

1. 도메인 왼쪽 탐색 영역에서 **개요** 를 선택합니다. 

1. 구매한 도메인에 대한 취소 기간이 경과되지 않은 경우 **구매 취소** 를 선택합니다. 그렇지 않으면 **삭제** 단추가 대신 표시됩니다. 환불하지 않고 도메인을 삭제하려면 **삭제** 를 선택합니다.

    ![구매한 도메인을 삭제 또는 취소할 수 있는 위치를 보여 주는 스크린샷](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

1. **예** 를 선택하여 작업을 확인합니다.

    작업이 완료되면 구독에서 도메인이 해제되어 누구든지 다시 구매할 수 있게 됩니다. 

## <a name="direct-default-url-to-a-custom-directory"></a>사용자 지정 디렉터리로 기본 URL 전달

App Service는 기본적으로 웹 요청을 앱 코드의 루트 디렉터리로 보냅니다. `public` 등의 하위 디렉터리로 보내려면 [사용자 지정 디렉터리로 리디렉션하기](app-service-web-tutorial-custom-domain.md#redirect-to-a-custom-directory)를 참조하세요.

## <a name="next-steps"></a>다음 단계

사용자 지정 TLS/SSL 인증서를 App Service에 바인딩하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [사용자 지정 DNS 이름을 TLS 바인딩을 통하여 Azure App Service에서 보호하기](configure-ssl-bindings.md)
