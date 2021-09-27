---
title: 사용자 지정 CA 인증서 추가 - Azure API Management | Microsoft Docs
description: Azure API Management에서 사용자 지정 CA 인증서를 추가하는 방법을 알아봅니다. 지침을 참조하여 인증서를 삭제할 수도 있습니다.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 06/01/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7e92d4a6c81376a9dbd004ac7fee978fa4d67e9a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128628822"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Azure API Management에서 사용자 지정 CA 인증서를 추가하는 방법

Azure API Management를 통해 신뢰할 수 있는 루트 및 중간 인증서 저장소 내의 머신에 CA 인증서를 설치할 수 있습니다. 서비스에 사용자 지정 CA 인증서가 필요한 경우 이 기능을 사용해야 합니다.

이 문서에서는 Azure Portal에서 Azure API Management 서비스 인스턴스의 CA 인증서를 관리하는 방법을 보여줍니다. 예를 들어 자체 서명된 클라이언트 인증서를 사용하는 경우 API Management에 신뢰할 수 있는 사용자 지정 루트 인증서를 업로드할 수 있습니다. 

API Management에 업로드된 CA 인증서는 관리형 API Management 게이트웨이별 인증서의 유효성 검사에만 사용할 수 있습니다. [자체 호스팅 게이트웨이](self-hosted-gateway-overview.md)를 사용하는 경우 이 문서의 뒷부분에 있는 [자체 호스팅 게이트웨이에 대한 사용자 지정 CA를 만드](#create-custom-ca-for-self-hosted-gateway)는 방법에 대해 알아봅니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upload-a-ca-certificate"></a><a name="step1"> </a>CA 인증서 업로드

:::image type="content" source="media/api-management-howto-ca-certificates/00.png" alt-text="Azure Portal의 CA 인증서":::

새 CA 인증서를 업로드하려면 다음 단계를 수행합니다. 아직 API Management 서비스 인스턴스를 만들지 않은 경우 자습서 [API Management 서비스 인스턴스 만들기](get-started-create-service-instance.md)를 참조하세요.

1. Azure Portal에서 Azure API Management 서비스 인스턴스로 이동합니다.

1. 메뉴의 **보안** 아래에서 **인증서 > CA 인증서 > + 추가** 를 선택합니다.

1. 인증서 .cer 파일을 찾고 인증서 저장소를 결정합니다. 공개 키만 필요하므로 암호는 선택 사항입니다.

    :::image type="content" source="media/api-management-howto-ca-certificates/02.png" alt-text="Azure Portal에서 CA 인증서 추가"::: 

1. **저장** 을 선택합니다. 이 작업은 몇 분 정도 걸릴 수 있습니다.

> [!NOTE]
> `New-AzApiManagementSystemCertificate` PowerShell 명령을 사용하여 CA 인증서를 업로드할 수도 있습니다.

## <a name="delete-a-ca-certificate"></a><a name="step1a"> </a>CA 인증서 삭제

인증서를 선택하고 바로 가기 메뉴( **...** )에서 **삭제** 를 선택합니다.

## <a name="create-custom-ca-for-self-hosted-gateway"></a>자체 호스팅 게이트웨이에 대한 사용자 지정 CA 만들기 

[자체 호스팅 게이트웨이](self-hosted-gateway-overview.md)를 사용하는 경우 API Management 서비스에 업로드된 CA 루트 인증서를 사용하는 서버 및 클라이언트 인증서의 유효성 검사는 지원되지 않습니다. 트러스트를 설정하려면 게이트웨이에서 사용자 지정 인증 기관으로 신뢰할 수 있도록 특정 클라이언트 인증서를 구성합니다.

[게이트웨이 인증 기관](/rest/api/apimanagement/2021-01-01-preview/gateway-certificate-authority) REST API를 사용하여 자체 호스팅 게이트웨이에 대한 사용자 지정 CA를 만들고 관리합니다. 사용자 지정 CA를 만들려면 다음을 수행합니다.

1. [인증서](api-management-howto-mutual-certificates.md) .pfx 파일을 API Management 인스턴스에 추가합니다.
1. [게이트웨이 인증 기관 - 만들기 또는 업데이트](/rest/api/apimanagement/2021-01-01-preview/gateway-certificate-authority/create-or-update) REST API를 사용하여 인증서를 자체 관리형 게이트웨이와 연결합니다.

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
