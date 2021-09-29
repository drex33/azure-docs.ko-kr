---
title: Purview 계정에 비공개로 안전하게 연결
description: 이 문서는 제한된 네트워크에서 Purview 계정에 연결하기 위해 프라이빗 엔드포인트를 설정하는 방법을 설명합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 0da3f53c41296f3cb467c00bb13649288ebd53c6
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129213844"
---
# <a name="connect-privately-and-securely-to-your-purview-account"></a>Purview 계정에 비공개로 안전하게 연결
이 가이드에서는 VNet 및 개인 네트워크에서만 Azure Purview 계정에 연결할 수 있도록 Purview 계정에 대한 프라이빗 엔드포인트를 배포하는 방법을 배웁니다. 이러한 목표를 달성 하려면 Azure 부서의 범위 계정에 대 한 _계정_ 및 _포털_ 개인 끝점을 배포 해야 합니다.

Azure Purview _계정_ 프라이빗 엔드포인트는 가상 네트워크 내에서 시작된 클라이언트 호출만 Azure Purview 계정에 액세스할 수 있는 시나리오를 사용하도록 설정하여 다른 보안 계층을 추가하는 데 사용됩니다. 이 프라이빗 엔드포인트는 포털 프라이빗 엔드포인트의 필수 조건이기도 합니다.

Azure 부서의 범위 _portal_ 개인 끝점은 개인 네트워크를 사용 하 여 [azure 부서의 범위 Studio](https://web.purview.azure.com/resource/) 에 대 한 연결을 사용 하도록 설정 하는 데 필요 합니다.

> [!NOTE]
> _계정_ 및 _포털_ 전용 끝점을 만드는 경우에는 검색을 실행할 수 없습니다. 개인 네트워크에서 검색을 사용 하도록 설정 하려면 수집 [개인 끝점도 만들어야](catalog-private-link-end-to-end.md)합니다.

   :::image type="content" source="media/catalog-private-link/purview-private-link-account-portal.png" alt-text="Azure Purview 및 Private Link 아키텍처를 보여 주는 다이어그램.":::

Azure Private Link 서비스에 대한 자세한 내용은 [프라이빗 링크 및 프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)를 참조하세요.

## <a name="deployment-checklist"></a>배포 검사 목록
이 가이드의 배포 옵션 중 하나를 사용하여 _계정_ 및 _포털_ 프라이빗 엔드포인트를 사용하는 새 Azure Purview 계정을 배포하거나 기존 Azure Purview 계정에 대해 다음 프라이빗 엔드포인트를 배포하도록 선택할 수 있습니다.

1. Azure Purview 프라이빗 엔드포인트를 배포할 적절한 Azure 가상 네트워크 및 서브넷을 선택합니다. 다음 옵션 중 하나를 선택합니다.
   - Azure 구독에 [새 가상 네트워크](../virtual-network/quick-create-portal.md)를 배포합니다.
   - Azure 구독에서 기존 Azure 가상 네트워크 및 서브넷을 찾습니다.
  
2. 개인 IP 주소를 통해 Azure Purview 계정 및 웹 포털에 액세스할 수 있도록 적절한 [DNS 이름 확인 방법](./catalog-private-link-name-resolution.md#deployment-options)을 정의합니다. 다음 옵션 중 하나를 사용할 수 있습니다.
   - 이 가이드에서 자세히 설명하는 단계를 사용하여 새 Azure DNS 영역을 배포합니다.
   - 이 가이드에서 자세히 설명하는 단계를 사용하여 기존 Azure DNS 영역에 필요한 DNS 레코드를 추가합니다.
   - 이 가이드의 단계를 완료한 후 기존 DNS 서버에 필요한 DNS A 레코드를 수동으로 추가합니다.
3. 계정 및 포털 프라이빗 엔드포인트를 사용하는 [새 Purview 계정](#option-1---deploy-a-new-azure-purview-account-with-account-and-portal-private-endpoints)을 배포하거나 [기존 Purview 계정](#option-2---enable-account-and-portal-private-endpoint-on-existing-azure-purview-accounts)에 대한 계정 및 포털 프라이빗 엔드포인트를 배포합니다.
4. 개인 네트워크에 모든 공개 인터넷 트래픽을 거부하도록 설정된 네트워크 보안 그룹 규칙이 있는 경우 [Azure Active Directory에 대한 액세스를 사용하도록 설정](#enable-access-to-azure-active-directory)합니다.
5. 이 가이드를 완료한 후 필요한 경우 DNS 구성을 조정합니다.
6. 관리 컴퓨터에서 Azure Purview에 대한 네트워크 및 이름 확인의 유효성을 검사합니다. 

## <a name="option-1---deploy-a-new-azure-purview-account-with-_account_-and-_portal_-private-endpoints"></a>옵션 1 - _계정_ 및 _포털_ 프라이빗 엔드포인트를 사용하는 새 Azure Purview 계정 배포

1. [Azure Portal](https://portal.azure.com)로 이동한 다음 **Purview 계정** 페이지로 이동합니다. **+ 만들기** 를 선택하여 새 Azure Purview 계정을 만듭니다.

2. 기본 정보를 입력하고 **네트워킹** 탭에서 연결 방법을 **프라이빗 엔드포인트** 로 설정합니다. 프라이빗 엔드포인트 사용을 **계정 및 포털 전용** 으로 설정합니다.

3. **계정 및 포털** 에서 **+ 추가** 를 선택하여 Azure Purview 계정에 대한 프라이빗 엔드포인트를 추가합니다.

   :::image type="content" source="media/catalog-private-link/purview-pe-deploy-account-portal.png" alt-text="계정 및 포털 페이지 선택을 위한 프라이빗 엔드포인트 만들기를 보여 주는 스크린샷":::

4. **프라이빗 엔드포인트 만들기** 페이지에서 **Purview 하위 리소스** 에 대해 위치를 선택하고 _계정_ 프라이빗 엔드포인트의 이름을 제공한 다음 **계정** 을 선택합니다. **네트워킹** 에서 가상 네트워크와 서브넷을 선택하고 선택적으로 **프라이빗 DNS 영역과 통합** 을 선택하여 새 Azure 프라이빗 DNS 영역을 만듭니다. 
   
   :::image type="content" source="media/catalog-private-link/purview-pe-deploy-account.png" alt-text="계정 프라이빗 엔드포인트 만들기 페이지를 보여 주는 스크린샷.":::

      > [!NOTE]
      > 기존 Azure 프라이빗 DNS 영역을 사용하거나 나중에 수동으로 DNS 서버에서 DNS 레코드를 만들 수도 있습니다. 자세한 내용은 [프라이빗 엔드포인트에 대한 DNS 이름 확인 구성](./catalog-private-link-name-resolution.md)을 참조하세요.

5. **확인** 을 선택합니다.
   
6. **Purview 계정 만들기** 마법사에서 **+추가** 를 다시 선택하여 _포털_ 프라이빗 엔드포인트를 추가합니다.
     
7. **프라이빗 엔드포인트 만들기** 페이지에서 **Purview 하위 리소스** 에 대해 위치를 선택하고 _포털_ 프라이빗 엔드포인트의 이름을 제공하고 **포털** 을 선택합니다. **네트워킹** 에서 가상 네트워크와 서브넷을 선택하고 선택적으로 **프라이빗 DNS 영역과 통합** 을 선택하여 새 Azure 프라이빗 DNS 영역을 만듭니다. 

   :::image type="content" source="media/catalog-private-link/purview-pe-deploy-portal.png" alt-text="포털 개인 끝점 만들기 페이지를 보여 주는 스크린샷":::
   
   > [!NOTE]
   > 기존 Azure 프라이빗 DNS 영역을 사용하거나 나중에 수동으로 DNS 서버에서 DNS 레코드를 만들 수도 있습니다. 자세한 내용은 [프라이빗 엔드포인트에 대한 DNS 이름 확인 구성](./catalog-private-link-name-resolution.md)을 참조하세요.

8.  **확인** 을 선택합니다.
   
9.  **검토 + 만들기** 를 선택합니다. **검토 + 만들기** 페이지에서 Azure가 구성의 유효성을 검사합니다.
      
      :::image type="content" source="media/catalog-private-link/purview-pe-deploy-account-portal-2.png" alt-text="프라이빗 엔드포인트 만들기 검토 페이지를 보여 주는 스크린샷.":::


10. "유효성 검사 통과" 메시지가 표시되면 **만들기** 를 선택합니다.

## <a name="option-2---enable-_account_-and-_portal_-private-endpoint-on-existing-azure-purview-accounts"></a>옵션 2 - 기존 Azure Purview 계정에서 _계정_ 및 _포털_ 프라이빗 엔드포인트를 사용하도록 설정

기존 Azure Purview 계정에 대해 Azure Purview _계정_ 및 _포털_ 프라이빗 엔드포인트를 추가할 수 있는 두 가지 방법이 있습니다.

- Azure Portal(Azure Purview 계정)을 사용합니다.
- Private Link 센터를 사용합니다.

### <a name="use-the-azure-portal-azure-purview-account"></a>Azure Portal(Azure Purview 계정) 사용

1. [Azure Portal](https://portal.azure.com)로 이동한 다음, Azure 부서의 범위 계정을 선택 하 고 **설정** 에서 **네트워킹** 을 선택 하 고 **개인 끝점 연결** 을 선택 합니다.

    :::image type="content" source="media/catalog-private-link/purview-pe-add-to-existing.png" alt-text="계정 프라이빗 엔드포인트 만들기를 보여 주는 스크린샷":::

2. **+ 프라이빗 엔드포인트** 를 선택하여 새 프라이빗 엔드포인트를 만듭니다.

3. 기본 정보를 입력합니다.

4. **리소스** 탭에서 **리소스 종류** 로 **Microsoft.Purview/accounts** 를 선택합니다.

5. **리소스** 에 대해 Azure Purview 계정을 선택하고 **대상 하위 리소스** 에 대해 **계정** 을 선택합니다.

6. **구성** 탭에서 가상 네트워크를 선택하고 선택적으로 Azure 프라이빗 DNS 영역을 선택하여 새 Azure DNS 영역을 만듭니다.
   
   > [!NOTE]
   > DNS 구성의 경우 드롭다운 목록에서 기존 Azure 프라이빗 DNS 영역을 사용하거나 나중에 필요한 DNS 레코드를 DNS 서버에 수동으로 추가할 수도 있습니다. 자세한 내용은 [프라이빗 엔드포인트에 대한 DNS 이름 확인 구성](./catalog-private-link-name-resolution.md)을 참조하세요.

7. 요약 페이지로 이동하고 **만들기** 를 선택하여 포털 프라이빗 엔드포인트를 만듭니다.
   
8. **대상 하위 리소스** 에 대해 **포털** 을 선택할 때도 동일한 단계를 따릅니다.

### <a name="use-the-private-link-center"></a>Private Link 센터 사용

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

1. 페이지 상단의 검색 창에서 **프라이빗 링크** 를 검색하고 첫 번째 옵션을 선택하여 **Private Link** 창으로 이동합니다.

1. **+ 추가** 를 선택하고 기본 세부 정보를 입력합니다.

   :::image type="content" source="media/catalog-private-link/private-link-center.png" alt-text="Private Link 센터에서 프라이빗 엔드포인트 만들기를 보여 주는 스크린샷":::

1. **리소스** 에 대해 이미 만들어진 Azure Purview 계정을 선택합니다. **대상 하위 리소스** 에 대해 **계정** 을 선택합니다.

1. **구성** 탭에서 가상 네트워크 및 프라이빗 DNS 영역을 선택합니다. 요약 페이지로 이동하고 **만들기** 를 선택하여 계정 프라이빗 엔드포인트를 만듭니다.

> [!NOTE]
> **대상 하위 리소스** 에 대해 **포털** 을 선택할 때도 동일한 단계를 따릅니다.

## <a name="enable-access-to-azure-active-directory"></a>Azure Active Directory에 대한 액세스 사용

> [!NOTE]
> VM, VPN Gateway 또는 VNet 피어링 게이트웨이에 공용 인터넷 액세스가 있는 경우 Azure Purview 포털 및 프라이빗 엔드포인트를 사용하는 Azure Purview 계정에 액세스할 수 있습니다. 이러한 이유로 나머지 지침을 따를 필요가 없습니다. 개인 네트워크의 네트워크 보안 그룹 규칙이 모든 퍼블릭 인터넷 트래픽을 거부하도록 설정된 경우 Azure AD(Azure Active Directory) 액세스를 사용하도록 설정하는 몇 가지 규칙을 추가해야 합니다. 그렇게 하려면 지침을 따릅니다.

이 지침은 Azure VM에서 Azure Purview에 안전하게 액세스하기 위해 제공됩니다. VPN 또는 다른 VNet 피어링 게이트웨이를 사용하는 경우에도 유사한 단계를 수행해야 합니다.

1. Azure Portal에서 VM으로 이동하고 **설정** 에서 **네트워킹** 을 선택합니다. **아웃바운드 포트 규칙** 을 선택하고 **아웃바운드 포트 규칙 추가** 를 선택합니다.

   :::image type="content" source="media/catalog-private-link/outbound-rule-add.png" alt-text="아웃바운드 규칙 추가를 보여 주는 스크린샷.":::

1. **아웃바운드 보안 규칙 추가** 창에서 다음을 수행합니다.

   1. **대상** 에서 **서비스 태그** 를 선택합니다.
   1. **대상 서비스 태그** 에서 **AzureActiveDirectory** 를 선택합니다.
   1. **대상 포트 범위** 아래에서 *를 선택합니다.
   1. **동작** 에서 **허용** 을 선택합니다.
   1. **우선 순위** 에서 값은 모든 인터넷 트래픽을 거부한 규칙보다 높아야 합니다.
   
   규칙을 만듭니다.

   :::image type="content" source="media/catalog-private-link/outbound-rule-details.png" alt-text="아웃바운드 규칙 세부 정보 추가를 보여 주는 스크린샷.":::

1. 동일한 단계에 따라 **AzureResourceManager** 서비스 태그를 허용하는 다른 규칙을 만듭니다. Azure Portal에 액세스해야 하는 경우 **AzurePortal** 서비스 태그에 대한 규칙을 추가할 수도 있습니다.

1. VM에 연결하고 브라우저를 엽니다. Ctrl+Shift+J를 선택하여 브라우저 콘솔로 이동하고 네트워크 탭으로 전환하여 네트워크 요청을 모니터링합니다. URL 상자에 web.purview.azure.com을 입력하고 Azure AD 자격 증명을 사용하여 로그인을 시도합니다. 로그인이 실패했을 수 있으며, 콘솔의 **네트워크** 탭에서 Azure AD가 aadcdn.msauth.net에 액세스하려고 시도하지만 차단된 것을 확인할 수 있습니다.

   :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="로그인 실패 세부 정보를 보여 주는 스크린샷":::

1. 이 경우 VM에서 명령 프롬프트를 열고, aadcdn.msauth.net을 ping하고, IP를 가져온 다음, VM의 네트워크 보안 규칙에서 IP에 대한 아웃바운드 포트 규칙을 추가합니다. **대상** 을 **IP 주소** 로 설정하고 **대상 IP 주소** 를 aadcdn의 IP로 설정합니다. Azure Load Balancer 및 Azure Traffic Manager로 인해 Azure AD Content Delivery Network IP가 동적일 수 있습니다. 해당 IP를 얻은 후에는 VM의 호스트 파일에 추가하여 브라우저가 해당 IP를 방문하여 Azure AD Content Delivery Network를 가져오도록 하는 것이 좋습니다.

   :::image type="content" source="media/catalog-private-link/ping.png" alt-text="테스트 ping을 보여 주는 스크린샷":::

   :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="Azure AD Content Delivery Network 규칙을 보여 주는 스크린샷.":::

1. 새 규칙을 만든 후 VM으로 돌아가서 Azure AD 자격 증명을 다시 사용하여 로그인을 시도합니다. 로그인에 성공하면 Azure Purview 포털을 사용할 준비가 된 것입니다. 그러나 경우에 따라 Azure AD는 고객의 계정 유형에 따라 로그인하기 위해 다른 도메인으로 리디렉션됩니다. 예를 들어 live.com 계정의 경우 Azure AD는 로그인을 위해 live.com으로 리디렉션한 다음 해당 요청을 다시 차단합니다. Microsoft 직원 계정의 경우 Azure AD는 로그인 정보를 위해 msft.sts.microsoft.com에 액세스합니다.

   브라우저 **네트워킹** 탭에서 네트워킹 요청을 확인하여 차단되는 도메인의 요청을 확인하고 이전 단계를 다시 실행하여 해당 IP를 가져온 다음 네트워크 보안 그룹에 아웃바운드 포트 규칙을 추가하여 해당 IP에 대한 요청을 허용합니다. 가능하면 URL과 IP를 VM의 호스트 파일에 추가하여 DNS 확인을 수정합니다. 정확한 로그인 도메인의 IP 범위를 알고 있으면 네트워킹 규칙에 직접 추가할 수도 있습니다.

1. 이제 Azure AD 로그인에 성공해야 합니다. Azure Purview 포털은 성공적으로 로드되지만 모든 Azure Purview 계정을 나열하는 것은 특정 Azure Purview 계정에만 액세스할 수 있기 때문에 작동하지 않습니다. `web.purview.azure.com/resource/{PurviewAccountName}`을 입력하여 프라이빗 엔드포인트를 설정한 Azure Purview 계정을 직접 방문합니다.

## <a name="next-steps"></a>다음 단계

-  [프라이빗 엔드포인트에 대한 해결 확인](./catalog-private-link-name-resolution.md)
-  [Azure Purview에서 데이터 원본 관리](./manage-data-sources.md)
-  [Azure Purview 계정의 프라이빗 엔드포인트 구성 문제 해결](./catalog-private-link-troubleshoot.md)
