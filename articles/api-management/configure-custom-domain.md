---
title: Azure API Management 인스턴스에 대한 사용자 지정 도메인 이름 구성
titleSuffix: Azure API Management
description: 이 항목에서는 Azure API Management 인스턴스에 대해 사용자 지정 도메인 이름을 구성하는 방법에 대해 설명합니다.
services: api-management
documentationcenter: ''
author: dlepow
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/24/2021
ms.author: danlep
ms.openlocfilehash: 4cdd269c0acb7568695cbc63b47422ad9b42085b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598768"
---
# <a name="configure-a-custom-domain-name-for-your-azure-api-management-instance"></a>Azure API Management 인스턴스에 대한 사용자 지정 도메인 이름 구성

Azure API Management 서비스 인스턴스를 만들면 Azure에서는 `azure-api.net` 하위 도메인(예: `apim-service-name.azure-api.net`)에 이 인스턴스를 할당합니다. **`contoso.com`** 등의 고유한 사용자 지정 도메인 이름을 사용하여 API Management 엔드포인트를 노출할 수도 있습니다. 이 자습서에서는 기존 사용자 지정 DNS 이름을 API Management 인스턴스에 공개된 엔드포인트에 매핑하는 방법을 보여 줍니다.

> [!IMPORTANT]
> API Management는 [호스트 헤더](https://tools.ietf.org/html/rfc2616#section-14.23) 값이 다음과 일치하는 요청만 허용합니다.
>
>* 기본 도메인 이름
>* 구성된 모든 사용자 지정 도메인 이름

> [!WARNING]
> 인증서 고정을 사용하여 애플리케이션의 보안을 강화하려면 기본 인증서가 아니라 관리하는 인증서와 사용자 지정 도메인 이름을 사용해야 합니다. 기본 인증서를 고정하면 관리하지 않는 인증서의 속성에 강하게 종속되므로 권장되지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

-   활성화된 Azure 구독. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
-   API Management 인스턴스. 자세한 내용은 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 참조하세요.
-   사용자 또는 사용자의 조직에서 소유하고 있는 사용자 지정 도메인 이름입니다. 이 항목은 사용자 지정 도메인 이름을 확보하는 방법에 대해서는 설명하지 않습니다.
-   사용자 지정 도메인 이름을 API Management 인스턴스의 기본 도메인 이름에 매핑하는 [DNS 서버에서 호스트되는 CNAME 레코드](#dns-configuration). 이 항목은 CNAME 레코드를 호스트하는 방법에 대한 지침은 제공하지 않습니다.
-   퍼블릭 및 프라이빗 키(.PFX)가 있는 유효한 인증서. 주체 또는 SAN(주체 대체 이름)이 도메인 이름과 일치해야 합니다(이렇게 하면 API Management 인스턴스에서 TLS를 통해 URL을 안전하게 공개할 수 있음).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Azure Portal을 사용하여 사용자 지정 도메인 이름 설정

1. [Azure Portal](https://portal.azure.com/)에서 API Management 인스턴스로 이동합니다.
1. 사용자 지정 도메인**을 선택합니다.

    사용자 지정 도메인 이름을 할당할 수 있는 여러 가지 엔드포인트가 있습니다. 현재는 다음 엔드포인트를 사용할 수 있습니다.

    | 엔드포인트 | 기본값 |
    | -------- | ----------- |
    | **게이트웨이** | 기본값은 `<apim-service-name>.azure-api.net`입니다. 게이트웨이는 소비 계층의 구성에 사용할 수 있는 유일한 엔드포인트입니다. |
    | **개발자 포털(레거시)** | 기본값은 `<apim-service-name>.portal.azure-api.net`입니다. |
    | **개발자 포털** | 기본값은 `<apim-service-name>.developer.azure-api.net`입니다. |
    | **관리** | 기본값은 `<apim-service-name>.management.azure-api.net`입니다. |
    | **SCM** | 기본값은 `<apim-service-name>.scm.azure-api.net`입니다. |

    > [!NOTE]
    > 모든 엔드포인트를 업데이트할 수 있습니다. 일반적으로 고객은 **게이트웨이**(이 URL은 API Management를 통해 노출된 API를 호출하는 데 사용됨) 및 **포털**(개발자 포털 URL)을 업데이트합니다.
    > 
    > API Management 인스턴스 소유자만 내부적으로 **관리** 및 **SCM** 엔드포인트를 사용할 수 있습니다. 해당 엔드포인트에는 사용자 지정 도메인 이름이 자주 할당되지 않습니다.
    >
    > **프리미엄** alc **개발자** 계층은 **게이트웨이** 엔드포인트에 대한 여러 호스트 이름 설정을 지원합니다.

1. **+추가** 를 선택하거나 업데이트하려는 기존 엔드포인트를 선택합니다.
1. 오른쪽 창에서 사용자 지정 도메인의 엔드포인트 **유형** 을 선택합니다.
1. **호스트 이름** 필드에서 사용할 이름을 지정합니다. 예: `api.contoso.com`
1. **인증서** 에서 **Key Vault** 또는 **사용자 지정** 을 선택합니다.
    - **Key Vault**
        - **선택** 을 클릭합니다.
        - 드롭다운 목록에서 **구독** 을 선택합니다.
        - 드롭다운 목록에서 **키 자격 증명 모음** 을 선택합니다.
        - 인증서가 로드된 후 드롭다운 목록에서 **인증서** 를 선택합니다.
        - **선택** 을 클릭합니다.
    - **사용자 지정**
        - **인증서 파일** 필드를 선택하여 인증서를 선택하고 업로드합니다.
        - 유효한 .PFX 파일을 업로드하고 인증서가 암호로 보호되는 경우 해당 **암호** 를 제공합니다.
1. 게이트웨이 엔드포인트를 구성할 때 **클라이언트 인증서 협상** 또는 **기본 SSL 바인딩** 과 같은 [다른 옵션을 필요에 따라](#clients-calling-with-server-name-indication-sni-header) 선택하거나 선택 취소합니다.
1. **업데이트** 를 선택합니다.

    > [!NOTE]
    > `*.contoso.com`과 같은 와일드카드 도메인 이름은 소비 계층을 제외한 모든 계층에서 지원됩니다.

    > [!TIP]
    > [Azure Key Vault를 사용하여 인증서를 관리](../key-vault/certificates/about-certificates.md)하고 이를 `autorenew`로 설정하는 것이 좋습니다.
    >
    > Azure Key Vault를 사용하여 사용자 지정 도메인 TLS/SSL 인증서를 관리하는 경우 인증서가 비밀 이 아닌 [_인증서_](/rest/api/keyvault/createcertificate/createcertificate) 로 Key Vault에 삽입되었는지 확인합니다.
    >
    > TLS/SSL 인증서를 가져오려면 API Management는 인증서를 포함하는 Azure Key Vault에 대한 목록 및 암호 가져오기 권한이 있어야 합니다. 
    >
    >* Azure Portal을 사용하는 경우 필요한 모든 구성 단계가 자동으로 완료됩니다. 
    >* 명령줄 도구 또는 관리 API를 사용하는 경우 다음 두 단계에서 해당 권한을 수동으로 부여해야 합니다.
    >    * API Management 인스턴스의 **관리 ID** 페이지를 사용하여 관리 ID가 사용하도록 설정되었는지 확인하고 해당 페이지에 있는 보안 주체 ID를 기록해 둡니다. 
    >    * 인증서를 포함하는 Azure Key Vault에 대한 권한 목록 및 비밀 가져오기 권한을 이 보안 주체 ID에 제공합니다.
    >
    > 인증서가 `autorenew`로 설정되고 API Management 계층(개발자 계층을 제외한 모든 계층)에 SLA가 있는 경우 API Management는 서비스 가동 중지 없이 자동으로 최신 버전을 선택합니다.

1. 적용을 클릭합니다.

    > [!NOTE]
    > 배포 크기에 따라, 인증서를 할당하는 과정에 15분 이상 소요될 수 있습니다. 개발자 SKU는 가동 중지되지만 기본 이상의 SKU는 가동 중지되지 않습니다.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>DNS 구성

사용자 지정 도메인 이름에 대해 DNS를 구성하는 경우 다음 중 하나를 수행할 수 있습니다.

-   구성된 사용자 지정 도메인 이름의 엔드포인트를 가리키는 CNAME 레코드를 구성합니다. 또는
-   API Management 게이트웨이 IP 주소를 가리키는 A 레코드를 구성합니다.

CNAME 레코드(또는 별칭 레코드)와 A 레코드는 둘 다 도메인 이름을 특정 서버 또는 서비스에 연결할 수 있지만 서로 다르게 작동합니다. 

### <a name="cname-or-alias-record"></a>CNAME 또는 별칭 레코드
CNAME 레코드는 ‘특정’ 도메인(예: `contoso.com` 또는 www\.contoso.com)을 정식 도메인 이름에 매핑합니다. 레코드 생성 후 CNAME은 도메인의 별칭을 만듭니다. CNAME 항목은 자동으로 사용자 지정 도메인 서비스의 IP 주소로 확인되기 때문에 IP 주소가 변경될 경우 아무 조치도 수행할 필요가 없습니다.

> [!NOTE]
> 일부 도메인 등록 기관에서는 CNAME 레코드를 사용할 때 contoso.com과 같은 루트 이름이 아니라 www\.contoso.com과 같은 하위 도메인만 매핑할 수 있습니다. CNAME 레코드에 관한 자세한 내용은 등록 기관에서 제공하는 설명서인 [CNAME 레코드에 대한 Wikipedia 항목](https://en.wikipedia.org/wiki/CNAME_record) 또는 [IETF 도메인 이름 - 구현 및 사양](https://tools.ietf.org/html/rfc1035) 문서를 참조하세요.

### <a name="a-record"></a>A 레코드
A 레코드는 `contoso.com` 또는 **www\.contoso.com** 과 같은 도메인이나 **\*.contoso.com** 과 같은 ‘와일드카드 도메인’을 IP 주소에 매핑합니다. A 레코드는 고정 IP 주소에 매핑되므로 변경 내용을 IP 주소로 자동으로 확인할 수 없습니다. A 레코드 대신 보다 안정적인 CNAME 레코드를 사용하는 것이 좋습니다.

> [!NOTE]
> API Management 인스턴스 IP 주소는 고정이지만 몇 가지 시나리오에서 변경될 수 있습니다. DNS 구성 방법을 선택하는 경우 사용자 지정 도메인을 구성할 때 CNAME 레코드를 사용하는 것이 좋습니다. IP가 변경되는 경우 A 레코드보다 더 안정적이기 때문입니다. 자세한 내용은 [IP 설명서 문서](api-management-howto-ip-addresses.md#changes-to-the-ip-addresses) 및 [API Management FAQ](./api-management-faq.yml#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services-)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[서비스 업그레이드 및 크기 조정](upgrade-and-scale.md)