---
title: Azure API Management 인스턴스에 대한 사용자 지정 도메인 이름 구성
titleSuffix: Azure API Management
description: 이 항목에서는 Azure API Management 인스턴스에 대해 사용자 지정 도메인 이름을 구성하는 방법에 대해 설명합니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: d0e8dfaf4c03ee3cfd47271b2044a7d0d9480643
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538579"
---
# <a name="configure-a-custom-domain-name-for-your-azure-api-management-instance"></a>Azure API Management 인스턴스에 대한 사용자 지정 도메인 이름 구성

Azure API Management 서비스 인스턴스를 만들 때 Azure에서는 `azure-api.net`의 하위 도메인에 이를 할당합니다(예: `apim-service-name.azure-api.net`). 그러나 **contoso.com** 등의 고유한 도메인 이름을 사용하여 API Management 엔드포인트를 공개할 수 있습니다. 이 자습서에서는 기존 사용자 지정 DNS 이름을 API Management 인스턴스에 공개된 엔드포인트에 매핑하는 방법을 보여 줍니다.

> [!IMPORTANT]
> API Management는 기본 도메인 이름이나 구성된 사용자 지정 도메인 이름과 일치하는 [호스트 헤더](https://tools.ietf.org/html/rfc2616#section-14.23) 값이 포함된 요청만 수락합니다.

> [!WARNING]
> 인증서 고정을 사용하여 애플리케이션의 보안을 개선하고자 하는 고객은 사용자 지정 도메인 이름 및 관리하는 인증서(기본 인증서 아님)를 사용해야 합니다. 대신 기본 인증서를 고정하는 고객은 자신이 제어하지 않는 인증서의 속성에 대한 강한 종속성을 갖게 되므로 해당 방법은 권장하지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서에 설명한 단계를 수행하려면 다음 항목이 있어야 합니다.

-   활성화된 Azure 구독.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   API Management 인스턴스. 자세한 내용은 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 참조하세요.
-   사용자 또는 사용자의 조직에서 소유하고 있는 사용자 지정 도메인 이름입니다. 이 항목은 사용자 지정 도메인 이름을 확보하는 방법에 대한 지침은 제공하지 않습니다.
-   사용자 지정 도메인 이름을 API Management 인스턴스의 기본 도메인 이름에 매핑하는 DNS 서버에서 호스트되는 CNAME 레코드. 이 항목은 CNAME 레코드를 호스트하는 방법에 대한 지침은 제공하지 않습니다.
-   공개 및 프라이빗 키(.PFX)가 있는 유효한 인증서가 있어야 합니다. 주체 또는 SAN(주체 대체 이름)이 도메인 이름과 일치해야 합니다(이렇게 하면 API Management 인스턴스에서 TLS를 통해 URL을 안전하게 공개할 수 있음).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Azure Portal을 사용하여 사용자 지정 도메인 이름 설정

1. [Azure Portal](https://portal.azure.com/)에서 API Management 인스턴스로 이동합니다.
1. 사용자 지정 도메인**을 선택합니다.

    사용자 지정 도메인 이름을 할당할 수 있는 여러 가지 엔드포인트가 있습니다. 현재는 다음 엔드포인트를 사용할 수 있습니다.

    - **게이트웨이**(기본값: `<apim-service-name>.azure-api.net`)
    - **개발자 포털(레거시)**(기본값: `<apim-service-name>.portal.azure-api.net`),
    - **개발자 포털**(기본값: `<apim-service-name>.developer.azure-api.net`),
    - **관리**(기본값: `<apim-service-name>.management.azure-api.net`),
    - **SCM**(기본값: `<apim-service-name>.scm.azure-api.net`).

    > [!NOTE]
    > **게이트웨이** 엔드포인트만 소비 계층의 구성에 사용할 수 있습니다.
    > 이들 중 일부 또는 모든 엔드포인트를 업데이트할 수 있습니다. 일반적으로 고객이 **게이트웨이**(이 URL은 API Management를 통해 노출된 API를 호출하는 데 사용됨) 및 **포털**(개발자 포털 URL)을 업데이트합니다.
    > **관리** 및 **SCM** 엔드포인트는 API Management 인스턴스 소유자만 내부적으로 사용하며 따라서 사용자 지정 도메인 이름은 자주 할당되지 않습니다.
    > **프리미엄** alc **개발자** 계층은 **게이트웨이** 엔드포인트에 대한 여러 호스트 이름 설정을 지원합니다.

1. 업데이트하려는 엔드포인트를 선택합니다.
1. 오른쪽 창에서 **사용자 지정** 을 클릭합니다.

    - **사용자 지정 도메인 이름** 에서 사용하려는 이름을 지정합니다. 예들 들어 `api.contoso.com`입니다.
    - **인증서** 의 Key Vault에서 인증서를 선택합니다. 유효한 .PFX 파일을 업로드하고 인증서가 암호로 보호되는 경우 해당 **암호** 를 제공할 수도 있습니다.

    > [!NOTE]
    > 와일드카드 도메인 이름(예: `*.contoso.com`)은 소비 계층을 제외한 모든 계층에서 지원됩니다.

    > [!TIP]
    > [Azure Key Vault를 사용하여 인증서를 관리](../key-vault/certificates/about-certificates.md)하고 이를 자동 갱신되도록 설정하는 것이 좋습니다.
    > Azure Key Vault를 사용하여 사용자 지정 도메인 TLS/SSL 인증서를 관리하는 경우 인증서가 비밀 이 아닌 [_인증서_](/rest/api/keyvault/createcertificate/createcertificate) 로 Key Vault에 삽입되었는지 확인합니다.
    >
    > TLS/SSL 인증서를 가져오려면 API Management는 인증서를 포함하는 Azure Key Vault에 대한 목록 및 암호 가져오기 권한이 있어야 합니다. Azure Portal을 사용하는 경우 필요한 모든 구성 단계가 자동으로 완료됩니다. 명령줄 도구 또는 관리 API를 사용하는 경우 이러한 권한을 수동으로 부여해야 합니다. 이 작업은 다음 두 단계로 수행됩니다. 먼저 API Management 인스턴스의 관리 ID 페이지를 사용하여 관리 ID가 사용하도록 설정되었는지 확인하고 해당 페이지에 표시된 보안 주체 ID를 기록해 둡니다. 그런 다음 인증서를 포함하는 Azure Key Vault에 대한 권한 목록 및 암호 가져오기 권한을 이 보안 주체 ID에 제공합니다.
    >
    > 인증서가 자동 갱신되도록 설정된 경우 API Management는 서비스의 가동 중지 시간 없이 자동으로 최신 버전을 선택합니다(API Management 계층에 SLA가 있는 경우 - 예: 개발자 계층을 제외한 모든 계층에서).

1. 적용을 클릭합니다.

    > [!NOTE]
    > 배포 크기에 따라, 인증서를 할당하는 과정에 15분 이상 소요될 수 있습니다. 개발자 SKU는 가동 중지되고, 기본 이상의 SKU는 가동 중지되지 않습니다.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>DNS 구성

사용자 지정 도메인 이름에 대한 DNS를 구성하는 경우 다음 두 가지 옵션이 있습니다.

-   구성된 사용자 지정 도메인 이름의 엔드포인트를 가리키는 CNAME 레코드를 구성합니다.
-   API Management 게이트웨이 IP 주소를 가리키는 A 레코드를 구성합니다.

> [!NOTE]
> API Managment 인스턴스 IP 주소는 정적이지만 몇 가지 시나리오에서 변경될 수 있습니다. 따라서 사용자 지정 도메인을 구성하는 경우 CNAME를 사용하는 것이 좋습니다. DNS 구성 방법을 선택할 때 이를 고려하십시오. 자세한 내용은 [IP 설명서 문서](api-management-howto-ip-addresses.md#changes-to-the-ip-addresses) 및 [API Management FAQ](api-management-faq.yml#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services-)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[서비스 업그레이드 및 크기 조정](upgrade-and-scale.md)
