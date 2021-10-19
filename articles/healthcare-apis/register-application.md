---
title: Azure Healthcare API에 대한 Azure Active Directory 클라이언트 애플리케이션 등록
description: Azure AD에서 클라이언트 애플리케이션을 등록하는 방법 및 Azure Healthcare API에 비밀 및 API 권한을 추가하는 방법
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 08/25/2021
ms.author: ginle
ms.openlocfilehash: b1ac54f71c9c49af5bb8656bdbe05d639b8bca25
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278051"
---
# <a name="register-a-client-application-in-azure-active-directory"></a>Azure Active Directory 클라이언트 애플리케이션 등록

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 문서에서는 의료 API에 액세스하기 위해 Azure AD(Azure Active Directory)에 클라이언트 애플리케이션을 등록하는 방법을 알아봅니다. [Microsoft ID 플랫폼 애플리케이션 등록에](../active-directory/develop/quickstart-register-app.md)대한 자세한 내용을 확인할 수 있습니다.

## <a name="register-a-new-application"></a>새 애플리케이션 등록

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 를 선택합니다.
2. **앱 등록** 을 선택합니다.
[![새 앱 등록 창의 스크린샷. ](media/register-application-one.png) ](media/register-application-one.png#lightbox)
3. **새 등록** 을 선택합니다.
4. 지원되는 계정 **유형에 대해 이 조직 디렉터리에 있는 계정만을** 선택합니다. 다른 옵션은 그대로 둡니다.
[![새 등록 계정 옵션의 스크린샷. ](media/register-application-two.png) ](media/register-application-two.png#lightbox)
5. **등록** 을 선택합니다.

## <a name="application-id-client-id"></a>애플리케이션 ID(클라이언트 ID)

새 애플리케이션을 등록한 후 개요 메뉴 옵션에서 애플리케이션(클라이언트) ID 및 디렉터리(테넌트) ID를 찾을 수 있습니다. 나중에 사용할 값을 기록해 둡다.

[![클라이언트 ID 개요 패널의 스크린샷. ](media/register-application-three.png) ](media/register-application-three.png#lightbox)

[![클라이언트 ID의 ](media/register-application-four.png) 스크린샷 ](media/register-application-four.png#lightbox)

## <a name="authentication-setting-confidential-vs-public"></a>인증 설정: 기밀 및 공용

**인증을** 클릭하여 설정을 검토합니다. 공용 클라이언트 **흐름 허용의** 기본값은 "아니요"입니다.

이 기본값을 유지하는 경우 애플리케이션 등록은 **기밀 클라이언트 애플리케이션이며** 인증서 또는 비밀이 필요합니다.

[![기밀 클라이언트 애플리케이션의 스크린샷. ](media/register-application-five.png) ](media/register-application-five.png#lightbox)

기본값을 "예"로 변경하면 애플리케이션 등록이 **공용 클라이언트 애플리케이션이며** 인증서 또는 비밀이 필요하지 않습니다. "예" 값은 비밀을 저장하지 않으려는 모바일 앱 또는 JavaScript 앱에서 클라이언트 애플리케이션을 사용하려는 경우에 유용합니다.

리디렉션 URL이 필요한 도구의 경우 **플랫폼 추가를** 선택하여 플랫폼을 구성합니다.

[![플랫폼 추가 스크린샷. ](media/register-application-five-alpha.png) ](media/register-application-five-alpha.png#lightbox)

Postman에서 **모바일 및 데스크톱 애플리케이션을** 선택합니다. 사용자 https://www.getpostman.com/oauth2/callback 지정 리디렉션 **URIS 섹션에서 ""를 입력합니다.** **구성** 단추를 선택하여 설정을 저장합니다.

[![다른 서비스 구성의 스크린샷. ](media/register-application-five-bravo.png) ](media/register-application-five-bravo.png#lightbox)

## <a name="certificates--secrets"></a>인증서 & 비밀

**인증서 & 비밀을** 선택하고 새 클라이언트 **암호** 를 선택합니다. 만료 필드에서 **권장 6개월을** 선택합니다.  이 새 비밀은 6개월 동안 유효합니다. 다음과 같은 다른 값을 선택할 수도 있습니다.
 
* 03개월
* 12개월
* 24개월
* 사용자 지정 시작 날짜 및 종료 날짜입니다.

>[!NOTE]
>비밀 ID가 아니라 비밀 값을 저장하는 것이 중요합니다.

[![인증서 및 비밀의 스크린샷. ](media/register-application-six.png) ](media/register-application-six.png#lightbox)

필요에 따라 인증서(공개 키)를 업로드하고 인증서와 연결된 GUID 값인 인증서 ID를 사용할 수 있습니다. 테스트를 위해 PowerShell 명령줄, 등의 도구를 사용하여 자체 서명된 `New-SelfSignedCertificate` 인증서를 만든 다음 인증서 저장소에서 인증서를 내보낼 수 있습니다.

## <a name="api-permissions"></a>API 사용 권한

의료 API에 대한 권한은 RBAC를 통해 관리됩니다. 자세한 내용은 [FHIR 서비스에 대한 Azure RBAC 구성을](./fhir/configure-azure-rbac-for-fhir.md)방문하세요.

>[!NOTE]
>Postman 또는 Rest Client와 같은 도구를 사용하여 FHIR 서비스에 대한 액세스 토큰을 획득하려고 할 때 client_credentials grant_type 사용합니다. 자세한 내용은 [postman을 사용하여 액세스](use-postman.md) 및 [Visual Studio Code REST 클라이언트 확장을 사용하여 의료 API 액세스를 방문하세요.](using-rest-client.md)

이제 애플리케이션 등록이 완료되었습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure AD에서 클라이언트 애플리케이션을 등록하는 방법을 배웠습니다. 또한 Azure Healthcare API에 비밀 및 API 권한을 추가하는 방법을 배웠습니다. Azure Healthcare API에 대한 자세한 내용은 다음을 참조하세요.

>[!div class="nextstepaction"]
>[Azure Healthcare API 개요](healthcare-apis-overview.md)
