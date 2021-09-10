---
title: Azure Front Door Standard/Premium SKU 구성에서 사용자 지정 도메인에 대해 HTTPS 구성
description: 이 문서에서는 Azure Front Door Standard/Premium SKU에 사용자 지정 도메인을 온보딩하는 방법을 알아봅니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/26/2021
ms.author: amsriva
ms.openlocfilehash: 79674febe7f43f8ae57d8c0420d6c8500506506e
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122965677"
---
# <a name="configure-https-on-a-front-door-standardpremium-sku-preview-custom-domain-using-the-azure-portal"></a>Azure Portal을 사용하여 Front Door Standard/Premium SKU(미리 보기) 사용자 지정 도메인에서 HTTPS 구성

> [!NOTE]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

Azure Front Door Standard/Premium을 사용하면 사용자 지정 도메인이 추가될 때 기본적으로 애플리케이션에 대한 보안 TLS 전송이 가능합니다. 사용자 지정 도메인에서 HTTPS 프로토콜을 사용하여 인터넷을 통해 전송되는 중요한 데이터가 TLS/SSL 암호화를 통해 안전하게 전달되도록 합니다. 웹 브라우저가 HTTPS를 통해 웹 사이트에 연결되면 웹 사이트 보안 인증서의 유효성을 검사하고 합법적인 인증 기관에서 발급되었는지를 확인합니다. 이 프로세스는 보안을 제공하며 공격으로부터 웹 애플리케이션을 보호합니다.

Azure Front Door Standard/Premium은 Azure 관리형 인증서와 고객 관리형 인증서를 모두 지원합니다. 기본적으로 Azure Front Door는 Azure 관리형 인증서를 사용하여 모든 사용자 지정 도메인에 대한 HTTPS를 자동으로 설정합니다. Azure 관리형 인증서를 얻기 위해 필요한 추가 단계는 없습니다. 도메인 유효성 검사 프로세스 중에 인증서가 생성됩니다. Key Vault와 Azure Front Door Standard/Premium을 통합하여 사용자 고유의 인증서를 사용할 수도 있습니다.

> [!IMPORTANT]
> Azure Front Door 표준/프리미엄(미리 보기)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [**Microsoft Azure Preview에 대한 추가 사용 약관**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* 사용자 지정 도메인에 대해 HTTPS를 구성하려면 먼저 Azure Front Door Standard/Premium 프로필을 만들어야 합니다. 자세한 내용은 [빠른 시작: Azure Front Door 표준/프리미엄 프로필 만들기](create-front-door-portal.md)를 참고하세요.

* 사용자 지정 도메인이 없으면 먼저 도메인 공급자를 통해 구매해야 합니다. 예를 들어 [사용자 지정 도메인 이름 구매](../../app-service/manage-custom-dns-buy-domain.md)를 참조하세요.

* Azure를 사용하여 [DNS 도메인](../../dns/dns-overview.md)을 호스트하는 경우 도메인 공급자의 DNS(Domain Name System)를 Azure DNS에 위임해야 합니다. 자세한 내용은 [Azure DNS에 도메인 위임](../../dns/dns-delegate-domain-azure-dns.md)을 참조하세요. 그렇지 않으면 도메인 공급자를 사용하여 DNS 도메인을 처리하는 경우 DNS TXT 레코드를 입력하여 도메인의 유효성을 수동으로 확인해야 합니다.

## <a name="azure-managed-certificates"></a>Azure 관리형 인증서

1. Azure Front Door Standard/Premium 프로필에 대한 설정에서 **도메인** 을 선택한 다음, **+ 추가** 를 선택하여 새 도메인을 추가합니다.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-new-custom-domain.png" alt-text="도메인 구성 방문 페이지 스크린샷":::

1. **도메인 추가** 페이지의 ‘DNS 관리’에서 **Azure 관리형 DNS** 옵션을 선택합니다. 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-domain-azure-managed.png" alt-text="Azure 관리형 DNS가 선택된 도메인 추가 페이지 스크린샷":::

1. [사용자 지정 도메인](how-to-add-custom-domain.md)을 사용하도록 설정하는 단계에 따라 사용자 지정 도메인의 유효성을 검사하고 엔드포인트에 연결합니다.

1. 사용자 지정 도메인이 엔드포인트에 연결되면 Azure 관리형 인증서가 Front Door에 배포됩니다. 이 프로세스를 완료하는 데 몇 분에서 1시간까지 걸릴 수 있습니다.

## <a name="using-your-own-certificate"></a>사용자 고유의 인증서 사용

사용자 고유의 TLS 인증서를 사용하도록 선택할 수도 있습니다.  TLS/SSL 인증서를 만들 때 [Microsoft 신뢰할 수 있는 CA 목록](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT)에 있는 허용된 CA(인증 기관)를 사용하여 전체 인증서 체인을 만들어야 합니다. 허용되지 않는 CA를 사용하면 요청이 거부됩니다.  인증서에는 리프 및 중간 인증서를 포함하는 전체 인증서 체인이 있어야 하며, 루트 CA가 [Microsoft 신뢰할 수 있는 CA 목록](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT)에 있어야 합니다. 전체 체인이 없는 인증서가 표시되는 경우 해당 인증서와 관련된 요청이 예상대로 작동하지 않을 수 있습니다. Azure Front Door Standard/Premium에서 사용하려면 먼저 이 인증서를 Azure Key Vault로 가져와야 합니다. Azure Key Vault로 [인증서 가져오기](../../key-vault/certificates/tutorial-import-certificate.md)를 참조하세요.

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Azure 키 자격 증명 모음 계정 및 인증서 준비
 
1. 사용자 지정 HTTPS를 사용하도록 설정하려는 Azure Front Door Standard/Premium과 동일한 구독에서 실행 중인 Azure Key Vault 계정이 있어야 합니다. 아직 Azure Key Vault 계정이 없는 경우 새로 하나 만듭니다.

    > [!WARNING]
    > Azure Front Door는 현재 Front Door 구성과 동일한 구독의 Key Vault 계정만 지원합니다. Azure Front Door Standard/Premium과 다른 구독의 Key Vault를 선택하면 실패하게 됩니다.

1. 이미 인증서가 있는 경우에는 Azure Key Vault 계정에 직접 업로드할 수 있습니다. 그렇지 않으면 Azure Key Vault가 통합되는 파트너 인증 기관 중 하나의 Azure Key Vault를 통해 직접 새 인증서를 만듭니다. 인증서를 **비밀** 대신 **인증서** 개체로 업로드합니다.

    > [!NOTE]
    > 자체 TLS/SSL 인증서의 경우, Front Door는 EC 암호화 알고리즘을 사용하는 인증서를 지원하지 않습니다. 인증서에는 리프 및 중간 인증서를 포함하는 전체 인증서 체인이 있어야 하며, 루트 CA가 [Microsoft 신뢰할 수 있는 CA 목록](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT)에 있어야 합니다. 

#### <a name="register-azure-front-door"></a>Azure Front Door 등록

PowerShell을 통해 Azure Active Directory에서 Azure Front Door의 서비스 사용자를 앱으로 등록합니다.

> [!NOTE]
> 이 작업을 수행하려면 글로벌 관리자 권한이 필요하며, 테넌트 당 **한 번** 만 수행하면 됩니다.

1. 필요한 경우 로컬 컴퓨터의 PowerShell에 [Azure PowerShell](/powershell/azure/install-az-ps)을 설치합니다.

1. PowerShell에서 다음 명령을 실행합니다.

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`              

#### <a name="grant-azure-front-door-access-to-your-key-vault"></a>키 자격 증명 모음에 Azure Front Door 액세스 권한 부여
 
Azure Key Vault 계정의 인증서에 액세스하기 위한 Azure Front Door 사용 권한을 부여합니다.

1. 키 자격 증명 모음 계정의 설정에서 **액세스 정책** 을 선택합니다. 그런 다음, **새로 추가** 를 선택하여 새 정책을 만듭니다.

1. **보안 주체 선택** 에서 **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** 을 검색하고 **Microsoft.AzureFrontDoor-Cdn** 을 선택합니다. **선택** 을 클릭합니다.

1. **비밀 사용 권한** 에서 **가져오기** 를 선택하여 Front Door가 인증서를 검색하도록 허용합니다.

1. **인증서 사용 권한** 에서 **가져오기** 를 선택하여 Front Door가 인증서를 검색하도록 허용합니다.

1. **확인** 을 선택합니다. 

#### <a name="select-the-certificate-for-azure-front-door-to-deploy"></a>배포할 Azure Front Door 인증서 선택
 
1. 포털에서 Azure Front Door Standard/Premium으로 돌아갑니다.

1. ‘설정’에서 **비밀** 로 이동한 다음, **인증서 추가** 를 선택합니다.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate.png" alt-text="Azure Front Door 비밀 방문 페이지 스크린샷":::

1. **인증서 추가** 페이지에서 Azure Front Door Standard/Premium에 추가하려는 인증서의 확인란을 선택합니다. 버전 선택을 “최신”으로 두고 **추가** 를 선택합니다. 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate-page.png" alt-text="인증서 추가 페이지 스크린샷":::

1. 인증서가 프로비저닝되면 새 사용자 지정 도메인을 추가할 때 사용할 수 있습니다.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/successful-certificate-provisioned.png" alt-text="비밀에 추가된 인증서 스크린샷":::

1. ‘설정’에서 **도메인** 으로 이동한 다음, **+ 추가** 를 선택하여 새 사용자 지정 도메인을 추가합니다. **도메인 추가** 페이지의 *HTTPS* 에서 “BYOC(Bring Your Own Certificate)”를 선택합니다. ‘비밀’의 드롭다운에서 사용할 인증서를 선택합니다. 

    > [!NOTE]
    > 선택한 인증서의 CN(일반 이름)은 추가되는 사용자 지정 도메인과 같아야 합니다.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-custom-domain-https.png" alt-text="HTTPS를 사용한 사용자 지정 도메인 추가 페이지 스크린샷":::

1. 화면에 표시된 단계에 따라 인증서의 유효성을 검사합니다. 그런 다음, [사용자 지정 도메인 만들기](how-to-add-custom-domain.md) 가이드에 설명된 대로 새로 만든 사용자 지정 도메인을 엔드포인트에 연결합니다.

#### <a name="change-from-azure-managed-to-bring-your-own-certificate-byoc"></a>Azure 관리형에서 BYOC(Bring Your Own Certificate)로 변경

1. 인증서 상태를 선택하여 **인증서 세부 정보** 페이지를 열면 기존 Azure 관리형 인증서를 사용자 관리형 인증서로 변경할 수 있습니다.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/domain-certificate.png" alt-text="도메인 방문 페이지의 인증서 상태 스크린샷" lightbox="../media/how-to-configure-https-custom-domain/domain-certificate-expanded.png":::

1. **인증서 세부 정보** 페이지에서 “Azure 관리형”을 “BYOC(Bring Your Own Certificate)” 옵션으로 변경할 수 있습니다. 그런 다음, 이전과 동일한 단계에 따라 인증서를 선택합니다. **업데이트** 를 선택하여 도메인과 연결된 인증서를 변경합니다.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/certificate-details-page.png" alt-text="인증서 세부 정보 페이지 스크린샷":::

## <a name="next-steps"></a>다음 단계

[Azure Front Door Standard/Premium을 사용한 캐싱](concept-caching.md)에 대해 알아봅니다.
