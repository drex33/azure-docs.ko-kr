---
title: Azure Purview에 연결하고 비공개로 안전하게 데이터 원본 검색
description: 이 문서에서는 Purview 계정에 연결하고 엔드투엔드 격리를 위해 제한된 네트워크의 데이터 원본을 검사하도록 프라이빗 엔드포인트를 설정하는 방법을 설명합니다.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 4f15d39da8f97897498f07892eb90d37a1bd8574
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209276"
---
# <a name="connect-to-your-azure-purview-and-scan-data-sources-privately-and-securely"></a>Azure Purview에 연결하고 비공개로 안전하게 데이터 원본 검색

이 가이드에서는 Azure Purview 계정에 대한 _계정_, _포털_ 및 _수집_ 프라이빗 엔드포인트를 배포하여 purview 계정에 액세스하고 자체 호스팅 통합 런타임을 사용하여 데이터 원본을 안전하고 비공개로 검사하여 엔드투엔드 네트워크 격리를 가능하게 하도록 하는 방법을 알아봅니다.

Azure Purview _계정_ 프라이빗 엔드포인트는 가상 네트워크 내에서 시작된 클라이언트 호출만 Azure Purview 계정에 액세스할 수 있는 시나리오를 사용하도록 설정하여 다른 보안 계층을 추가하는 데 사용됩니다. 이 프라이빗 엔드포인트는 포털 프라이빗 엔드포인트의 필수 조건이기도 합니다.

Azure 부서의 범위 _portal_ 개인 끝점은 개인 네트워크를 사용 하 여 [azure 부서의 범위 Studio](https://web.purview.azure.com/resource/) 에 대 한 연결을 사용 하도록 설정 하는 데 필요 합니다.

Azure Purview는 _수집_ 프라이빗 엔드포인트를 사용하여 Azure 또는 온-프레미스 환경에서 데이터 원본을 검사할 수 있습니다. 수집 프라이빗 엔드포인트를 배포할 때 3가지 프라이빗 엔드포인트 리소스가 배포되고 Azure Purview 관리되는 리소스에 연결되어야 합니다.

 - Blob 프라이빗 엔드포인트는 Azure Purview 관리형 스토리지 계정에 연결됩니다.
 - 큐 프라이빗 엔드포인트는 Azure Purview 관리형 스토리지 계정에 연결됩니다.
 - 네임스페이스 프라이빗 엔드포인트는 Azure Purview 관리형 이벤트 허브 네임스페이스에 연결됩니다.

  :::image type="content" source="media/catalog-private-link/purview-private-link-architecture.png" alt-text="Azure Purview 및 Private Link 아키텍처를 보여 주는 다이어그램.":::

## <a name="deployment-checklist"></a>배포 검사 목록
이 가이드에서 자세히 설명하는 배포 옵션 중 하나를 사용하여 _계정_, _포털_ 및 _수집_ 프라이빗 엔드포인트를 사용하는 새 Azure Purview 계정을 배포하거나 기존 Azure Purview 계정에 대해 다음 프라이빗 엔드포인트를 배포하도록 선택할 수 있습니다.

1. Azure Purview 프라이빗 엔드포인트를 배포할 적절한 Azure 가상 네트워크 및 서브넷을 선택합니다. 다음 옵션 중 하나를 선택합니다.
   - Azure 구독에 [새 가상 네트워크](../virtual-network/quick-create-portal.md)를 배포합니다.
   - Azure 구독에서 기존 Azure 가상 네트워크 및 서브넷을 찾습니다.
  
2. Azure Purview 계정에 액세스하고 개인 네트워크를 사용하여 데이터 원본을 검사할 수 있도록 적절한 [DNS 이름 확인 방법](./catalog-private-link-name-resolution.md#deployment-options)을 정의합니다. 다음 옵션 중 하나를 사용할 수 있습니다.
   - 이 가이드에서 자세히 설명하는 단계를 사용하여 새 Azure DNS 영역을 배포합니다.
   - 이 가이드에서 자세히 설명하는 단계를 사용하여 기존 Azure DNS 영역에 필요한 DNS 레코드를 추가합니다.
   - 이 가이드의 단계를 완료한 후 기존 DNS 서버에 필요한 DNS A 레코드를 수동으로 추가합니다.
3. 계정, 포털 및 수집 프라이빗 엔드포인트가 있는 [새 Purview 계정](#option-1---deploy-a-new-azure-purview-account-with-account-portal-and-ingestion-private-endpoints)을 배포하거나 [기존 Purview 계정](#option-2---enable-account-portal-and-ingestion-private-endpoint-on-existing-azure-purview-accounts)에 대한 프라이빗 엔드포인트를 배포합니다.
4. 개인 네트워크에 모든 공개 인터넷 트래픽을 거부하도록 설정된 네트워크 보안 그룹 규칙이 있는 경우 [Azure Active Directory에 대한 액세스를 사용하도록 설정](#enable-access-to-azure-active-directory)합니다.
5. Azure Purview 수집 프라이빗 엔드포인트가 배포된 동일한 VNet 내에 [자체 호스팅 통합 런타임](#deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources)을 배포하고 등록합니다.
6. 이 가이드를 완료한 후 필요한 경우 DNS 구성을 조정합니다.
7. Azure Purview에 대한 관리 컴퓨터, 자체 호스팅 IR VM 및 데이터 원본 간의 네트워크 및 이름 확인의 유효성을 검사합니다. 

## <a name="option-1---deploy-a-new-azure-purview-account-with-_account_-_portal_-and-_ingestion_-private-endpoints"></a>옵션 1 - _계정_, _포털_ 및 _수집_ 프라이빗 엔드포인트를 사용하는 새 Azure Purview 계정 배포

1. [Azure Portal](https://portal.azure.com)로 이동한 다음 **Purview 계정** 페이지로 이동합니다. **+ 만들기** 를 선택하여 새 Azure Purview 계정을 만듭니다.

2. 기본 정보를 입력하고 **네트워킹** 탭에서 연결 방법을 **프라이빗 엔드포인트** 로 설정합니다. 프라이빗 엔드포인트 사용을 **계정, 포털 및 수집** 으로 설정합니다.

3. **계정 및 포털** 에서 **+ 추가** 를 선택하여 Azure Purview 계정에 대한 프라이빗 엔드포인트를 추가합니다.

   :::image type="content" source="media/catalog-private-link/purview-pe-deploy-end-to-end.png" alt-text="프라이빗 엔드포인트 만들기 엔드투엔드 페이지 선택을 보여 주는 스크린샷":::

4. **프라이빗 엔드포인트 만들기** 페이지에서 **Purview 하위 리소스** 에 대해 위치를 선택하고 _계정_ 프라이빗 엔드포인트의 이름을 제공한 다음 **계정** 을 선택합니다. **네트워킹** 에서 가상 네트워크와 서브넷을 선택하고 선택적으로 **프라이빗 DNS 영역과 통합** 을 선택하여 새 Azure 프라이빗 DNS 영역을 만듭니다. 

   :::image type="content" source="media/catalog-private-link/purview-pe-deploy-account.png" alt-text="계정 프라이빗 엔드포인트 만들기 페이지를 보여 주는 스크린샷.":::

   > [!NOTE]
   > 기존 Azure 프라이빗 DNS 영역을 사용하거나 나중에 수동으로 DNS 서버에서 DNS 레코드를 만들 수도 있습니다. 자세한 내용은 [프라이빗 엔드포인트에 대한 DNS 이름 확인 구성](./catalog-private-link-name-resolution.md)을 참조하세요.

5. **확인** 을 선택합니다.
   
6. **계정 및 포털** 마법사에서 **+추가** 를 다시 선택하여 _포털_ 프라이빗 엔드포인트를 추가합니다. 
  
7. **프라이빗 엔드포인트 만들기** 페이지에서 **Purview 하위 리소스** 에 대해 위치를 선택하고 _포털_ 프라이빗 엔드포인트의 이름을 제공하고 **포털** 을 선택합니다. **네트워킹** 에서 가상 네트워크와 서브넷을 선택하고 선택적으로 **프라이빗 DNS 영역과 통합** 을 선택하여 새 Azure 프라이빗 DNS 영역을 만듭니다. 

   :::image type="content" source="media/catalog-private-link/purview-pe-deploy-portal.png" alt-text="포털 개인 끝점 만들기 페이지를 보여 주는 스크린샷":::
   
   > [!NOTE]
   > 기존 Azure 프라이빗 DNS 영역을 사용하거나 나중에 수동으로 DNS 서버에서 DNS 레코드를 만들 수도 있습니다. 자세한 내용은 [프라이빗 엔드포인트에 대한 DNS 이름 확인 구성](./catalog-private-link-name-resolution.md)을 참조하세요.

8. **확인** 을 선택합니다.

9.  **수집** 에서 프라이빗 엔드포인트와 페어링하려는 **구독**, **가상 네트워크** 및 **서브넷** 에 대한 세부 정보를 제공하여 수집 프라이빗 엔드포인트를 설정합니다.

10. 선택적으로 **프라이빗 DNS 통합** 을 선택하여 Azure 프라이빗 DNS 영역을 사용합니다.
   
      :::image type="content" source="media/catalog-private-link/purview-pe-deploy-ingestion.png" alt-text="프라이빗 엔드포인트 만들기 개요 페이지를 보여 주는 스크린샷":::

      > [!IMPORTANT]
      > Azure Purview와 데이터 원본 간에 올바른 이름 확인을 허용하려면 올바른 Azure 프라이빗 DNS 영역을 선택하는 것이 중요합니다. 기존 Azure 프라이빗 DNS 영역을 사용하거나 나중에 수동으로 DNS 서버에서 DNS 레코드를 만들 수도 있습니다. 자세한 내용은 [프라이빗 엔드포인트에 대한 DNS 이름 확인 구성](./catalog-private-link-name-resolution.md)을 참조하세요.

11. **검토 + 만들기** 를 선택합니다. **검토 + 만들기** 페이지에서 Azure가 구성의 유효성을 검사합니다.

12. "유효성 검사 통과" 메시지가 표시되면 **만들기** 를 선택합니다.
   

## <a name="option-2---enable-_account_-_portal_-and-_ingestion_-private-endpoint-on-existing-azure-purview-accounts"></a>옵션 2 - 기존 Azure Purview 계정에서 _계정_, _포털_ 및 _수집_ 프라이빗 엔드포인트를 사용하도록 설정

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
   
9. Azure Purview 계정의 **설정** 에서 **네트워킹** 을 선택한 다음 **수집 프라이빗 엔드포인트 연결** 을 선택합니다.

10. 수집 프라이빗 엔드포인트 연결에서 **+ 새로 만들기** 를 선택하여 새 수집 프라이빗 엔드포인트를 만듭니다.

      :::image type="content" source="media/catalog-private-link/purview-pe-add-ingestion-to-existing.png" alt-text="기존 계정에 프라이빗 엔드포인트 추가를 보여 주는 스크린샷.":::

11. 기본 정보를 입력하고 기존 가상 네트워크와 서브넷 세부 정보를 선택합니다. 선택적으로 **프라이빗 DNS 통합** 을 선택하여 Azure 프라이빗 DNS 영역을 사용합니다. 각 목록에서 올바른 Azure 프라이빗 DNS 영역을 선택합니다.

      > [!NOTE]
      > 기존 Azure 프라이빗 DNS 영역을 사용하거나 나중에 수동으로 DNS 서버에서 DNS 레코드를 만들 수도 있습니다. 자세한 내용은 [프라이빗 엔드포인트에 대한 DNS 이름 확인 구성](./catalog-private-link-name-resolution.md)을 참조하세요.

12. **만들기** 를 선택하여 설정을 완료합니다.

## <a name="enable-access-to-azure-active-directory"></a>Azure Active Directory에 대한 액세스 사용

> [!NOTE]
> VM, VPN Gateway 또는 VNet 피어링 게이트웨이에 공용 인터넷 액세스가 있는 경우 Azure Purview 포털 및 프라이빗 엔드포인트를 사용하는 Azure Purview 계정에 액세스할 수 있습니다. 이러한 이유로 나머지 지침을 따를 필요가 없습니다. 개인 네트워크의 네트워크 보안 그룹 규칙이 모든 퍼블릭 인터넷 트래픽을 거부하도록 설정된 경우 Azure AD(Azure Active Directory) 액세스를 사용하도록 설정하는 몇 가지 규칙을 추가해야 합니다. 그렇게 하려면 지침을 따릅니다.

이 지침은 Azure VM에서 Azure Purview에 안전하게 액세스하기 위해 제공됩니다. VPN 또는 다른 VNet 피어링 게이트웨이를 사용하는 경우에도 유사한 단계를 수행해야 합니다.

1. Azure Portal에서 VM으로 이동하고 **설정** 에서 **네트워킹** 을 선택합니다. 그런 다음 **아웃바운드 포트 규칙** > **아웃바운드 포트 규칙 추가** 를 선택합니다.

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

## <a name="deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources"></a>자체 호스팅 IR(통합 런타임)을 배포하고 데이터 원본을 검사합니다.
Azure Purview에 대한 수집 프라이빗 엔드포인트를 배포한 후에는 하나 이상의 자체 호스팅 IR(통합 런타임)을 설정하고 등록해야 합니다.

- Microsoft SQL Server, Oracle, SAP 등의 모든 온-프레미스 원본 유형은 현재 자체 호스팅 IR 기반 검사를 통해서만 지원됩니다. 자체 호스팅 IR은 개인 네트워크 내에서 실행된 다음, Azure에서 가상 네트워크와 피어링되어야 합니다. 
   
- Azure Blob Storage 및 Azure SQL Database와 같은 모든 Azure 원본 유형의 경우 Azure Purview 수집 프라이빗 엔드포인트와 동일한 VNet에 배포되는 자체 호스팅 통합 런타임을 사용하여 검사를 실행하도록 명시적으로 선택해야 합니다. 

자체 호스팅 IR을 설정하려면 [자체 호스팅 통합 런타임 만들기 및 관리](manage-integration-runtimes.md)의 단계를 수행합니다. 그런 다음, **Integration Runtime을 통해 연결** 드롭다운 목록에서 자체 호스트 IR을 선택하여 네트워크 격리를 보장하도록 Azure 원본에 대해 검사를 설정합니다.
    
   :::image type="content" source="media/catalog-private-link/shir-for-azure.png" alt-text="자체 호스팅 IR을 사용하여 Azure 검사를 실행하는 것을 보여 주는 스크린샷":::

> [!IMPORTANT]
> 2021년 8월 18일 이후에 Azure Purview 계정을 만든 경우 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=39717)에서 최신 버전의 자체 호스팅 통합 런타임을 다운로드하여 설치해야 합니다.

## <a name="firewalls-to-restrict-public-access"></a>퍼블릭 액세스를 제한하는 방화벽

퍼블릭 인터넷에서 Azure Purview 계정에 대한 액세스를 완전히 차단하려면 아래 단계를 수행합니다. 이 설정은 프라이빗 엔드포인트 및 수집 프라이빗 엔드포인트 연결 둘 다에 적용됩니다.

1. Azure Portal에서 Azure Purview 계정으로 이동하고 **설정** > **네트워킹** 에서 **프라이빗 엔드포인트 연결** 을 선택합니다.

1. **방화벽** 탭으로 이동하여 토글이 **거부** 로 설정되어 있는지 확인합니다.

   :::image type="content" source="media/catalog-private-link/private-endpoint-firewall.png" alt-text="프라이빗 엔드포인트 방화벽 설정을 보여 주는 스크린샷.":::

## <a name="next-steps"></a>다음 단계

-  [프라이빗 엔드포인트에 대한 해결 확인](./catalog-private-link-name-resolution.md)
-  [Azure Purview에서 데이터 원본 관리](./manage-data-sources.md)
-  [Azure Purview 계정의 프라이빗 엔드포인트 구성 문제 해결](./catalog-private-link-troubleshoot.md)
