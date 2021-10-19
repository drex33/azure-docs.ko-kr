---
title: Azure Active Directory에서 기밀 클라이언트 응용 프로그램 등록-DICOM 용 Azure 의료 api
description: 이 문서에서는 Azure Active Directory에서 기밀 클라이언트 응용 프로그램을 등록 하는 방법을 설명 합니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/13/2021
ms.author: aersoy
ms.openlocfilehash: f5dd5e5136ac2cd47b637d7b95cb7ba95c3ddaaa
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122435361"
---
# <a name="register-a-confidential-client-application"></a>기밀 클라이언트 애플리케이션 등록

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 자습서에서는 Azure AD (Azure Active Directory)에서 기밀 클라이언트 응용 프로그램을 등록 하는 방법에 대해 알아봅니다.

## <a name="register-a-new-application"></a>새 애플리케이션 등록

클라이언트 응용 프로그램 등록은 사용자를 대신 하 여 인증 하 고 리소스 응용 프로그램에 대 한 액세스를 요청 하는 데 사용할 수 있는 응용 프로그램의 Azure AD 표현입니다. 기밀 클라이언트 응용 프로그램은 암호를 저장 하 고 액세스 토큰을 요청할 때 해당 비밀을 제공할 수 있는 신뢰할 수 있는 응용 프로그램입니다. 기밀 응용 프로그램의 예로는 서버 쪽 응용 프로그램이 있습니다.

새 기밀 클라이언트 응용 프로그램을 등록 하려면 다음 단계를 참조 하세요.

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 를 선택합니다.
2. **앱 등록** 블레이드를 선택 합니다.
3. **새 등록** 을 선택합니다.

   [![등록을 Azure 앱 합니다. ](media/dicom-azure-app-registrations.png) ](media/dicom-azure-app-registrations.png#lightbox)

4. 응용 프로그램에 대 한 사용자 연결 표시 이름을 입력 합니다.

   [![Azure는 응용 프로그램을 등록 합니다. ](media/dicom-registration-application-name.png) ](media/dicom-registration-application-name.png#lightbox)

5. **지원 되는 계정 유형** 으로 응용 프로그램을 사용 하거나 API에 액세스할 수 있는 사용자를 선택 합니다.
6. (**선택 사항**) **리디렉션 URI** 를 제공 합니다. 이러한 세부 정보는 나중에 변경할 수 있지만 응용 프로그램의 회신 URL을 알고 있는 경우 입력 합니다.
7. **등록** 을 선택합니다.

## <a name="api-permissions"></a>API 사용 권한

응용 프로그램을 등록 했으므로 이제이 응용 프로그램이 사용자를 대신 하 여 요청 해야 하는 API 권한을 선택 해야 합니다.

1. **API 사용 권한** 블레이드를 선택합니다.

   [![API 권한 ](media/dicom-add-api-permissions.png) 추가 ](media/dicom-add-api-permissions.png#lightbox)

2. **사용 권한 추가** 를 선택합니다.

   Azure 의료 Api를 사용 하는 경우 **내 조직** 에서 사용 하는 API에서 **DICOM 용 Azure API** 를 검색 하 여 dicom 서비스에 대 한 사용 권한을 추가 합니다. 

   [![API 사용 권한 ](media/dicom-search-apis-permissions.png) 검색 ](media/dicom-search-apis-permissions.png#lightbox)

   DICOM 용 Azure API에 대 한 검색 결과는 작업 영역에 이미 DICOM 서비스를 배포한 경우에만 반환 됩니다.

   다른 리소스 응용 프로그램을 참조 하는 경우 이전에 **api 내 조직** 에서 만든 DICOM API 리소스 응용 프로그램 등록을 선택 합니다.

3. 기밀 클라이언트 응용 프로그램에서 사용자를 대신 하 여 요청 하는 범위 (권한)를 선택 합니다. **user_impersonation** 을 선택한 다음, **사용 권한 추가** 를 선택합니다.

   [![사용 권한 범위를 선택 합니다. ](media/dicom-select-scopes.png) ](media/dicom-select-scopes.png#lightbox)

## <a name="application-secret"></a>애플리케이션 비밀

1. **인증서 및 암호** 를 선택하고 **새 클라이언트 암호** 를 선택합니다.

   [![인증서 및 비밀. ](media/dicom-new-client-secret.png) ](media/dicom-new-client-secret.png#lightbox)

2. 클라이언트 암호에 대한 **설명** 을 입력합니다. 만료 시간 프레임을 선택 하려면 **만료** 드롭다운 메뉴를 선택 하 고 **추가** 를 클릭 합니다.

   [![클라이언트 암호 설명입니다. ](media/dicom-client-secret-description.png) ](media/dicom-client-secret-description.png#lightbox)

3. 클라이언트 암호 문자열을 만든 후에는 해당 **값** 과 **ID** 를 복사 하 여 원하는 보안 위치에 저장 합니다.

   [![클라이언트 암호 값 ID입니다. ](media/dicom-client-secret-value-id.png) ](media/dicom-client-secret-value-id.png#lightbox)

   > [!NOTE]
   > 클라이언트 암호 문자열은 Azure Portal에서 한 번만 표시 됩니다. 인증서 & 비밀 웹 페이지에서 다른 곳으로 이동 하 여 다시 돌아오면 값 문자열이 마스킹 됩니다. 생성 된 직후에 클라이언트 암호 문자열을 복사 하는 것이 중요 합니다. 클라이언트 암호의 백업 복사본이 없는 경우에는 위의 단계를 반복 하 여 다시 생성 해야 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure AD에서 기밀 클라이언트 응용 프로그램을 등록 하는 방법의 단계를 안내 했습니다. 또한 Azure API for DICOM에 API 권한을 추가 하는 방법의 단계를 안내 했습니다. 마지막으로, 응용 프로그램 암호를 만드는 방법을 살펴보았습니다. 

>[!div class="nextstepaction"]
>[DICOM 서비스 개요](dicom-services-overview.md)



