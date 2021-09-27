---
title: 포털을 사용하여 엔드투엔드 TLS 암호화 구성
titleSuffix: Azure Application Gateway
description: Azure Portal를 사용하여 엔드투엔드 TLS 암호화를 사용하는 애플리케이션 게이트웨이를 만드는 방법을 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/14/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 2d19ccb3ea40d916ba6fbf53e87a3d9f2f2e1c2a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744825"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-the-portal"></a>포털에서 Application Gateway를 사용하여 엔드투엔드 TLS 구성

이 문서에서는 Azure Portal 사용하여 Azure Application Gateway v1 SKU를 통해 엔드투엔드 TLS(Transport Layer Security) 암호화를 구성하는 방법을 설명합니다(이전에는 SSL(Secure Sockets Layer)(SSL) 암호화로 알려짐).

> [!NOTE]
> Application Gateway v2 SKU에는 엔드투엔드 구성을 사용하기 위해 신뢰할 수 있는 루트 인증서가 필요합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

애플리케이션 게이트웨이로 엔드투엔드 TLS를 구성하려면 게이트웨이에 대한 인증서가 필요합니다. 백 엔드 서버에도 인증서가 필요합니다. 게이트웨이 인증서는 TLS 프로토콜 사양에 따라 대칭 키를 파생하는 데 사용됩니다. 이렇게 파생된 대칭 키는 게이트웨이로 전송되는 트래픽을 암호화하고 암호를 해독하는 데 사용됩니다. 

엔드투엔드 TLS 암호화의 경우 애플리케이션 게이트웨이에서 올바른 백 엔드 서버를 허용해야 합니다. 이 액세스를 허용하려면 애플리케이션 게이트웨이에 대한 인증 인증서(v1) 또는 신뢰할 수 있는 루트 인증서(v2)라고도 하는 백 엔드 서버의 공용 인증서를 업로드합니다. 인증서를 추가하면 애플리케이션 게이트웨이가 알려진 백 엔드 인스턴스하고만 통신하게 됩니다. 이 구성은 추가적으로 엔드투엔드 통신의 보안을 유지합니다.

자세한 내용은 [Application Gateway를 사용한 TLS 종료 및 엔드투엔드 TLS 개요](./ssl-overview.md)를 참조하세요.

## <a name="create-a-new-application-gateway-with-end-to-end-tls"></a>엔드투엔드 TLS를 사용한 새 애플리케이션 게이트웨이 만들기

엔드투엔드 TLS 암호화를 사용하여 새 애플리케이션 게이트웨이를 만들려면 먼저 새 애플리케이션 게이트웨이를 만드는 동안 TLS 종료를 사용하도록 설정해야 합니다. 이 작업을 통해 클라이언트와 애플리케이션 게이트웨이 간의 통신에 TLS 암호화를 사용할 수 있습니다. 그런 다음 HTTP 설정에서 백 엔드 서버에 대한 인증서를 안전한 수신자 목록에 배치해야 합니다. 이 구성을 통해 애플리케이션 게이트웨이와 백 엔드 서버 간 통신에 TLS 암호화를 사용할 수 있습니다. 이렇게 엔드투엔드 TLS 암호화를 수행합니다.

### <a name="enable-tls-termination-while-creating-a-new-application-gateway"></a>새 애플리케이션 게이트웨이를 만드는 동안 TLS 종료를 사용하도록 설정

자세히 알아보려면 [새 애플리케이션 게이트웨이를 만드는 동안 TLS 종료 사용](./create-ssl-portal.md)을 참조하세요.

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>백 엔드 서버의 인증/루트 인증서 추가

1. **모든 리소스** 를 선택한 다음, **myAppGateway** 를 선택합니다.

2. 왼쪽 메뉴에서 **HTTP 설정** 을 선택합니다. 앞에서 애플리케이션 게이트웨이를 만들 때 Azure가 자동으로 기본 HTTP 설정 **appGatewayBackendHttpSettings** 을 만들었습니다. 

3. **appGatewayBackendHttpSettings** 을 선택합니다.

4. **프로토콜** 에서 **HTTPS** 를 선택합니다. **백 엔드 인증 인증서 또는 신뢰할 수 있는 루트 인증서** 창이 표시됩니다.

5. **새로 생성** 를 선택합니다.

6. **이름** 필드에 적절한 이름을 입력합니다.

7. **CER 인증서 업로드** 상자에서 인증서 파일을 선택합니다.

   표준 및 WAF(v1) 애플리케이션 게이트웨이의 경우 백 엔드 서버 인증서의 공개 키를 .cer 형식으로 업로드해야 합니다.

   ![인증서 추가](./media/end-to-end-ssl-portal/addcert.png)

   표준_v2 및 v2 애플리케이션 게이트웨이의 경우 백 엔드 서버 인증서의 루트 인증서를 .cer 형식으로 업로드해야 합니다. 잘 알려진 CA(인증 기관)에서 백 엔드 인증서를 발급한 경우에는 **잘 알려진 CA 인증서 사용** 확인란을 선택한 다음 인증서를 업로드할 필요가 없습니다.

   ![신뢰할 수 있는 루트 인증서](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![루트 인증서](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. **저장** 을 선택합니다.

## <a name="enable-end-to-end-tls-for-an-existing-application-gateway"></a>기존 애플리케이션 게이트웨이에 대한 엔드투엔드 TLS 사용

엔드투엔드 TLS 암호화를 사용하여 기존 애플리케이션 게이트웨이를 구성하려면 먼저 수신기에서 TLS 종료를 사용하도록 설정해야 합니다. 이 작업을 통해 클라이언트와 애플리케이션 게이트웨이 간의 통신에 TLS 암호화를 사용할 수 있습니다. 그런 다음, 안전한 수신자 목록의 HTTP 설정에 백 엔드 서버용 인증서를 저장합니다. 이 구성을 통해 애플리케이션 게이트웨이와 백 엔드 서버 간 통신에 TLS 암호화를 사용할 수 있습니다. 이렇게 엔드투엔드 TLS 암호화를 수행합니다.

TLS 종료를 사용하도록 설정하려면 HTTPS 프로토콜 및 인증서를 사용하여 수신기를 사용해야 합니다. 이러한 조건을 충족하는 기존 수신기를 사용하거나 새 수신기를 만들 수 있습니다. 이전 옵션을 선택하는 경우 다음 "기존 애플리케이션 게이트웨이에서 TLS 종료를 사용하도록 설정" 섹션을 무시하 고 "백 엔드 서버에 대한 인증/신뢰할 수 있는 루트 인증서 추가" 섹션으로 바로 이동할 수 있습니다.

후자 옵션을 선택하는 경우 다음 절차의 단계를 적용합니다.
### <a name="enable-tls-termination-in-an-existing-application-gateway"></a>기존 애플리케이션 게이트웨이에서 TLS 종료를 사용하도록 설정

1. **모든 리소스** 를 선택한 다음, **myAppGateway** 를 선택합니다.

2. 왼쪽 메뉴에서 **수신기** 를 선택합니다.

3. 요구 사항에 따라 **기본** 또는 **다중 사이트** 수신기 중 하나를 선택합니다.

4. **프로토콜** 에서 **HTTPS** 를 선택합니다. **인증서** 에 대한 창이 표시됩니다.

5. 클라이언트와 애플리케이션 게이트웨이 간의 TLS 종료에 사용하려는 PFX 인증서를 업로드합니다.

   > [!NOTE]
   > 테스트용으로 자체 서명된 인증서를 만들 수 있습니다. 그러나 이 방법은 관리가 어렵고 완전히 안전하지 않으므로 프로덕션 워크로드에는 권장되지 않습니다. 자세한 내용은 [자체 서명된 인증서 만들기](./create-ssl-portal.md#create-a-self-signed-certificate)를 참조하세요.

6. 요구 사항에 따라 **수신기** 에 대한 다른 필수 설정을 추가합니다.

7. **확인** 을 선택하여 저장합니다.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>백 엔드 서버의 인증/신뢰할 수 있는 루트 인증서 추가

1. **모든 리소스** 를 선택한 다음, **myAppGateway** 를 선택합니다.

2. 왼쪽 메뉴에서 **HTTP 설정** 을 선택합니다. 안전한 수신자 목록의 기존 백 엔드 HTTP 설정에 인증서를 추가하거나 새 HTTP 설정을 만들 수 있습니다. (다음 단계에서는 기본 HTTP 설정인 **appGatewayBackendHttpSettings** 에 대한 인증서가 안전한 수신자 목록에 추가됩니다.)

3. **appGatewayBackendHttpSettings** 을 선택합니다.

4. **프로토콜** 에서 **HTTPS** 를 선택합니다. **백 엔드 인증 인증서 또는 신뢰할 수 있는 루트 인증서** 창이 표시됩니다. 

5. **새로 생성** 를 선택합니다.

6. **이름** 필드에 적절한 이름을 입력합니다.

7. **CER 인증서 업로드** 상자에서 인증서 파일을 선택합니다.

   표준 및 WAF(v1) 애플리케이션 게이트웨이의 경우 백 엔드 서버 인증서의 공개 키를 .cer 형식으로 업로드해야 합니다.

   ![인증서 추가](./media/end-to-end-ssl-portal/addcert.png)

   표준_v2 및 v2 애플리케이션 게이트웨이의 경우 백 엔드 서버 인증서의 루트 인증서를 .cer 형식으로 업로드해야 합니다. 잘 알려진 CA(인증 기관)에서 백 엔드 인증서를 발급한 경우에는 **잘 알려진 CA 인증서 사용** 확인란을 선택한 다음 인증서를 업로드할 필요가 없습니다.

   ![신뢰할 수 있는 루트 인증서](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. **저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 애플리케이션 게이트웨이로 웹 트래픽 관리](./tutorial-manage-web-traffic-cli.md)