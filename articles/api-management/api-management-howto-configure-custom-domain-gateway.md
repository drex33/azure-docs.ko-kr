---
title: 자체 호스팅 Azure API Management 게이트웨이에 대한 사용자 지정 도메인 이름 구성 | Microsoft Docs
description: 이 항목에서는 자체 호스팅 Azure API Management 게이트웨이에 대한 사용자 지정 도메인 이름을 구성하는 단계를 설명합니다.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 03/31/2020
ms.author: danlep
ms.openlocfilehash: b2ddd4c4486930cd7e5327068a5fe3827ac0581a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128667638"
---
# <a name="configure-a-custom-domain-name-for-a-self-hosted-gateway"></a>자체 호스팅 게이트웨이에 대한 사용자 지정 도메인 이름 구성

[자체 호스팅 Azure API Management 게이트웨이](self-hosted-gateway-overview.md)를 프로비전할 때 호스트 이름이 할당되지 않으며 해당 IP 주소로 참조되어야 합니다. 이 문서에서는 기존 사용자 지정 DNS 이름(호스트 이름이라고도 함)을 자체 호스팅 게이트웨이에 매핑하는 방법을 보여줍니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에 설명한 단계를 수행하려면 다음 항목이 있어야 합니다.

-   활성화된 Azure 구독.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   API Management 인스턴스. 자세한 내용은 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 참조하세요.
- 자체 호스팅 게이트웨이. 자세한 내용은 [자체 호스팅 게이트웨이를 프로비전하는 방법](api-management-howto-provision-self-hosted-gateway.md)을 참조하세요.
-   사용자 또는 사용자의 조직이 소유하고 있는 사용자 지정 도메인 이름입니다. 이 항목은 사용자 지정 도메인 이름을 확보하는 방법에 대해서는 설명하지 않습니다.
-   사용자 지정 도메인 이름을 자체 호스팅 게이트웨이의 IP 주소에 매핑하는 DNS 서버에서 호스팅되는 DNS 레코드입니다. 이 항목은 DNS 레코드를 호스트하는 방법에 대해서는 설명하지 않습니다.
-   공개 및 프라이빗 키(.PFX)가 있는 유효한 인증서가 있어야 합니다. 주체 또는 SAN(주체 대체 이름)은 도메인 이름과 일치해야 합니다(이렇게 하면 API Management 인스턴스에서 TLS를 통해 URL을 안전하게 공개할 수 있음).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>API Management 서비스에 사용자 지정 도메인 인증서 추가

사용자 지정 도메인 인증서(.PFX) 파일을 API Management에 추가하거나 Azure Key Vault에 저장된 인증서를 참조합니다. [Azure API Management에서 클라이언트 인증서 인증을 사용한 백엔드 서비스 보호](api-management-howto-mutual-certificates.md)의 단계를 따릅니다.

> [!NOTE]
> 자체 호스팅 게이트웨이 도메인에는 키 자격 증명 모음 인증서를 사용하는 것이 좋습니다.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Azure Portal을 사용하여 자체 호스팅된 게이트웨이에 대한 사용자 지정 도메인 이름 설정

1. **배포 및 인프라** 에서 **게이트웨이** 를 선택합니다.
2. 도메인 이름을 구성하려는 자체 호스팅 게이트웨이를 선택합니다.
3. **설정** 에서 **호스트 이름** 을 선택합니다.
4. **+ 추가** 를 선택합니다.
5. **이름** 필드에 호스트 이름에 대한 리소스 이름을 입력합니다.
6. **호스트 이름** 필드에 도메인 이름을 입력합니다.
7. **인증서** 드롭다운에서 인증서를 선택합니다.
8. 이 게이트웨이를 통해 노출된 API가 클라이언트 인증서 인증을 사용하는 경우 **클라이언트 인증서 협상** 확인란을 선택합니다.
    > [!WARNING]
    > 이 설정은 게이트웨이에 대해 구성된 모든 도메인 이름으로 공유됩니다.
9. **추가** 를 선택하여 선택한 자체 호스팅 게이트웨이에 사용자 지정 도메인 이름을 할당합니다.

## <a name="next-steps"></a>다음 단계

[서비스 업그레이드 및 크기 조정](upgrade-and-scale.md)
