---
title: 백 엔드 서버를 허용하는 데 필요한 인증서
titleSuffix: Azure Application Gateway
description: 이 문서에서는 TLS/SSL 인증서를 Azure Application Gateway의 백 엔드 인스턴스를 허용하는 데 필요한 인증 인증서 및 신뢰할 수 있는 루트 인증서로 변환할 수 있는 방법의 예를 제공합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/30/2021
ms.author: victorh
ms.openlocfilehash: ca2f66e60440f2e852c21c196548ace8813099b0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837065"
---
# <a name="create-certificates-to-allow-the-backend-with-azure-application-gateway"></a>Azure Application Gateway를 사용하여 백 엔드를 허용하기 위한 인증서 만들기

엔드투엔드 TLS를 수행하려면 Application Gateway는 인증/신뢰할 수 있는 루트 인증서를 업로드하여 백 엔드 인스턴스를 허용해야 합니다. 인증서를 허용하려면 v1 SKU의 경우 인증 인증서가 필요하고, v2 SKU의 경우 신뢰할 수 있는 루트 인증서가 필요합니다.

이 문서에서는 다음 방법을 설명합니다.


- 백 엔드 인증서에서 인증 인증서 내보내기(v1 SKU용)
- 백 엔드 인증서에서 신뢰할 수 있는 루트 인증서 내보내기(v2 SKU용)

## <a name="prerequisites"></a>사전 요구 사항

Application Gateway의 백 엔드 인스턴스를 허용하는 데 필요한 인증 인증서 또는 신뢰할 수 있는 루트 인증서를 생성하려면 기존 백 엔드 인증서가 필요합니다. 백 엔드 인증서는 TLS/SSL 인증서와 동일하거나 보안 강화를 위해 다를 수 있습니다. Application Gateway는 TLS/SSL 인증서를 만들거나 구매하기 위한 메커니즘을 제공하지 않습니다. 테스트를 위해 자체 서명된 인증서를 만들 수 있지만 이를 프로덕션 워크로드에 사용하면 안 됩니다. 

## <a name="export-authentication-certificate-for-v1-sku"></a>인증 인증서 내보내기(v1 SKU용)

Application Gateway v1 SKU에서 백 엔드 인스턴스를 허용하려면 인증 인증서가 필요합니다. 인증 인증서는 Base-64로 인코딩된 X.509(CER) 형식의 백 엔드 서버 인증서 공개 키입니다. 이 예에서는 백 엔드 인증서에 TLS/SSL 인증서를 사용하고 인증 인증서로 사용할 공개 키를 내보냅니다. 또한 이 예에서는 Windows 인증서 관리자 도구를 사용하여 필요한 인증서를 내보냅니다. 다른 편리한 도구를 선택하여 사용할 수도 있습니다.

TLS/SSL 인증서에서 공개 키 .cer 파일(프라이빗 키 제외)을 내보냅니다. 다음 단계는 인증서에 대한 Base-64로 인코딩된 X.509(.CER) 형식의 .cer 파일을 내보내는 데 도움이 됩니다.

1. 인증서에서 .cer 파일을 가져오려면 **사용자 인증서 관리** 를 엽니다. 일반적으로 'Certificates - Current User\Personal\Certificates'에서 인증서를 찾아 마우스 오른쪽 단추로 클릭합니다. **모든 태스크** 를 클릭한 다음 **내보내기** 를 클릭합니다. 이렇게 하면 **인증서 내보내기 마법사** 가 열립니다. Current User\Personal\Certificates에서 인증서를 찾을 수 없는 경우 “인증서 -현재 사용자” 대신 실수로 “인증서 - 로컬 컴퓨터”를 열었을 수 있습니다. PowerShell을 사용하여 현재 사용자 범위에서 인증서 관리자를 열려는 경우 콘솔 창에 *certmgr* 을 입력합니다.

   ![인증서가 선택된 인증서 관리자와 모든 작업, 내보내기가 선택된 상황에 맞는 메뉴를 보여 주는 스크린샷.](./media/certificates-for-backend-authentication/export.png)

2. 마법사에서 **다음** 을 클릭합니다.

   ![인증서 내보내기](./media/certificates-for-backend-authentication/exportwizard.png)

3. **아니요, 프라이빗를 내보내지 않습니다.** 를 선택한 후, **다음** 을 클릭합니다.

   ![아니요, 프라이빗 키를 내보내지 않습니다](./media/certificates-for-backend-authentication/notprivatekey.png)

4. **내보내기 파일 형식** 페이지에서 **Base 64로 인코딩된 X.509(.CER)** 를 선택한 후 **다음** 을 클릭합니다.

   ![Base-64로 인코딩됨](./media/certificates-for-backend-authentication/base64.png)

5. **내보낼 파일** 에서 인증서를 내보내려는 위치를 **찾습니다**. **파일 이름** 에는 인증서 파일의 이름을 입력합니다. 그런 후 **다음** 을 클릭합니다.

   ![내보낼 파일을 지정하는 인증서 내보내기 마법사를 보여 주는 스크린샷.](./media/certificates-for-backend-authentication/browse.png)

6. **마침** 을 클릭하여 인증서를 내보냅니다.

   ![파일 내보내기 완료 후 인증서 내보내기 마법사를 보여 주는 스크린샷.](./media/certificates-for-backend-authentication/finish-screen.png)

7. 인증서가 성공적으로 내보내졌습니다.

   ![성공 메시지와 함께 인증서 내보내기 마법사를 보여 주는 스크린샷](./media/certificates-for-backend-authentication/success.png)

   내보낸 인증서는 다음과 비슷합니다.

   ![인증서 기호를 표시하는 스크린샷.](./media/certificates-for-backend-authentication/exported.png)

8. 메모장을 사용하여 내보낸 인증서를 열면 이 예제와 비슷한 내용이 표시됩니다. 파란색 섹션에는 Application Gateway에 업로드되는 정보가 포함되어 있습니다. 메모장에서 인증서를 열었지만 이와 비슷하게 표시되지 않으면, 일반적으로 Base-64로 인코딩된 X.509(.CER) 형식을 사용하여 인증서를 내보내지 않았음을 의미합니다. 또한 다른 텍스트 편집기를 사용하려면 일부 편집자가 백그라운드에서 의도하지 않은 형식을 도입할 수도 있음을 이해해야 합니다. 이 경우 인증서의 텍스트를 Azure로 업로드할 때 문제가 발생할 수 있습니다.

   ![메모장에서 열기](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>신뢰할 수 있는 루트 인증서 내보내기(v2 SKU용)

애플리케이션 게이트웨이 v2 SKU에서 백 엔드 인스턴스를 허용하려면 신뢰할 수 있는 루트 인증서가 필요합니다. 루트 인증서는 백 엔드 서버 인증서의 Base-64로 인코딩된 X.509(.CER) 형식 루트 인증서입니다. 이 예에서는 백 엔드 인증서에 TLS/SSL 인증서를 사용하고, 공개 키를 내보낸 다음 신뢰할 수 있는 CA의 루트 인증서를 공개 키에서 base64 인코딩 형식으로 내보내 신뢰할 수 있는 루트 인증서를 가져옵니다. 중간 인증서를 서버 인증서와 함께 사용하여 백 엔드 서버에 설치해야 합니다.

다음 단계는 인증서에 대한 .cer 파일을 내보내는 데 도움이 됩니다.

1. 이전 섹션 [인증 인증서 내보내기(v1 SKU용)](#export-authentication-certificate-for-v1-sku)에서 언급한 1~8단계를 사용하여 백 엔드 인증서에서 공개 키를 내보냅니다.

2. 공개 키를 내보낸 후 파일을 엽니다.

   ![Open authorization 인증서](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![인증서 정보](./media/certificates-for-backend-authentication/general.png)

3. 인증 기관을 보려면 인증 경로 보기로 이동합니다.

   ![인증서 세부 정보](./media/certificates-for-backend-authentication/certdetails.png)

4. 루트 인증서를 선택하고 **인증서 보기** 를 클릭합니다.

   ![인증서 경로](./media/certificates-for-backend-authentication/rootcert.png)

   루트 인증서 세부 정보가 표시됩니다.

   ![인증서 정보](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. **세부 정보** 보기로 이동하고 **파일에 복사...** 를 클릭합니다.

   ![루트 인증서 복사](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. 이 시점에서는 백 엔드 인증서에서 루트 인증서의 세부 정보가 이미 추출된 상태입니다. **인증서 내보내기 마법사** 가 표시됩니다. 이제 위의 **백 엔드 인증서에서 인증 인증서 내보내기(v1 SKU용)** 섹션에 언급된 2~9단계를 사용하여 신뢰할 수 있는 루트 인증서를 Base-64로 인코딩된 X.509(.CER) 형식으로 내보냅니다.

## <a name="next-steps"></a>다음 단계

이제 Base-64로 인코딩된 X.509(.CER) 형식의 인증 인증서/신뢰할 수 있는 루트 인증서가 있습니다. 이를 애플리케이션 게이트웨이에 추가하여 엔드투엔드 TLS 암호화에 백 엔드 서버를 허용할 수 있습니다. [PowerShell과 함께 Application Gateway를 사용하여 엔드투엔드 TLS 구성](./application-gateway-end-to-end-ssl-powershell.md)을 참조하세요.