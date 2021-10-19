---
title: Azure Active Directory 서비스 클라이언트 애플리케이션 등록 - DICOM용 Azure Healthcare API
description: 이 문서에서는 Azure Active Directory 서비스 클라이언트 애플리케이션을 등록하는 방법을 설명합니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/13/2021
ms.author: aersoy
ms.openlocfilehash: 8408d1e2e670083f1e5bb1c30723a3c4ad83b8f2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122435302"
---
# <a name="register-a-service-client-application"></a>서비스 클라이언트 애플리케이션 등록

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 문서에서는 Azure AD(Azure Active Directory)에서 서비스 클라이언트 애플리케이션을 등록하는 방법을 알아봅니다.

## <a name="application-registrations-in-the-azure-portal"></a>Azure Portal에서 애플리케이션 등록

클라이언트 애플리케이션 등록은 토큰을 인증하고 가져오는 데 사용할 수 있는 애플리케이션의 Azure AD 표현입니다. 서비스 클라이언트는 애플리케이션에서 사용자의 대화형 인증 없이 액세스 토큰을 가져오는 데 사용됩니다. 특정 애플리케이션 권한을 가지며 액세스 토큰을 가져올 때 애플리케이션 비밀(암호)을 사용합니다.

새 서비스 클라이언트를 만들려면 아래 단계를 참조하세요.

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 를 선택합니다.
2. **Azure Active Directory** 블레이드에서 **앱 등록** 선택합니다.
3. **새 등록** 을 선택합니다.

   [![등록을 Azure 앱. ](media/dicom-azure-app-registrations.png) ](media/dicom-azure-app-registrations.png#lightbox)

4. DICOM 서비스 애플리케이션에 사용자용 표시 이름을 제공합니다. 서비스 클라이언트 애플리케이션은 일반적으로 회신 URL을 사용하지 않습니다.

   [![Azure에서 애플리케이션을 등록합니다. ](media/dicom-registration-application-name.png) ](media/dicom-registration-application-name.png#lightbox)

5. **등록** 을 선택합니다.

## <a name="api-permissions"></a>API 사용 권한

이제 DICOM 서비스 애플리케이션을 등록했으므로 이 애플리케이션이 사용자를 대신하여 요청해야 하는 API 권한을 선택해야 합니다.

1. **API 사용 권한** 블레이드를 선택합니다.

   [![API 사용 권한을 추가합니다. ](media/dicom-add-api-permissions.png) ](media/dicom-add-api-permissions.png#lightbox)

2. **사용 권한 추가** 를 선택합니다.

   DICOM을 사용하는 경우 **조직에서 사용하는** API에서 **Azure Healthcare** API를 검색하여 DICOM용 Azure API에 대한 권한을 추가합니다. 리소스 공급자를 이미 등록한 경우에만 찾을 수 `Microsoft.HealthcareAPIs` 있습니다.

   [![내 API 권한. ](media/dicom-request-my-api-permissions.png) ](media/dicom-request-my-api-permissions.png#lightbox)


   [![API 사용 권한을 요청합니다. ](media/dicom-request-api-permissions.png) ](media/dicom-request-api-permissions.png#lightbox)

3. 기밀 클라이언트 애플리케이션이 사용자를 대신하여 요청할 범위(권한)를 선택합니다.

   [![사용 권한 범위를 선택합니다. ](media/dicom-select-scopes.png) ](media/dicom-select-scopes.png#lightbox)

4. 애플리케이션에 동의를 부여합니다. 필요한 권한이 없는 경우 ADD 관리자에게 문의하세요.


## <a name="application-secret"></a>애플리케이션 비밀

토큰을 얻으려면 서비스 클라이언트에 비밀(암호)이 필요합니다.

1. **인증서 및 암호** 를 선택하고 **새 클라이언트 암호** 를 선택합니다.

   [![인증서 및 비밀. ](media/dicom-new-client-secret.png) ](media/dicom-new-client-secret.png#lightbox)

2. 클라이언트 암호에 대한 **설명** 을 입력합니다. **만료** 드롭다운 메뉴를 선택하여 만료 시간 프레임을 선택한 다음, **추가를** 클릭합니다.

   [![클라이언트 비밀 설명입니다. ](media/dicom-client-secret-description.png) ](media/dicom-client-secret-description.png#lightbox)

3. 클라이언트 비밀 문자열을 만든 후 **해당 값** 및 **ID** 를 복사하여 선택한 안전한 위치에 저장합니다.

   [![클라이언트 비밀 값 ID입니다. ](media/dicom-client-secret-value-id.png) ](media/dicom-client-secret-value-id.png#lightbox)

4. [추가]를 선택합니다.

> [!NOTE]
> 클라이언트 비밀 문자열은 Azure Portal 한 번만 표시됩니다. 인증서 & 비밀 웹 페이지에서 이동한 다음 다시 돌아가면 값 문자열이 마스킹됩니다. 클라이언트 비밀 문자열을 생성한 직후에 복사하는 것이 중요합니다. 클라이언트 비밀의 백업 복사본이 없는 경우 위의 단계를 반복하여 다시 생성해야 합니다.


## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure AD에서 서비스 클라이언트 애플리케이션을 등록하는 방법을 배웠습니다. 

>[!div class="nextstepaction"]
>[DICOM 서비스 개요](dicom-services-overview.md)




