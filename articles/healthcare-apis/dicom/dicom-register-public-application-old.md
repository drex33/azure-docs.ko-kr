---
title: Azure Active Directory에서 공용 클라이언트 응용 프로그램 등록-DICOM 용 Azure 의료 api
description: 이 문서에서는 Azure Active Directory에서 공용 응용 프로그램을 등록 하는 방법을 설명 합니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/13/2021
ms.author: aersoy
ms.openlocfilehash: f8a720508221d1a9a3308d64649863d11c6b57c7
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814672"
---
# <a name="register-a-public-client-application"></a>공용 클라이언트 애플리케이션 등록

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 문서에서는 Azure Active Directory (Azure AD)에서 공용 응용 프로그램을 등록 하는 방법에 대해 알아봅니다.

클라이언트 응용 프로그램 등록은 사용자를 대신 하 여 API 권한을 인증 하 고 요청할 수 있는 응용 프로그램의 Azure AD 표현입니다. 공용 클라이언트는 비밀을 비밀으로 유지할 수 없는 모바일 응용 프로그램 및 단일 페이지 JavaScript 응용 프로그램과 같은 응용 프로그램입니다. 이 절차는 [기밀 클라이언트 응용 프로그램을 등록](dicom-register-confidential-client-application.md)하는 것과 유사 하지만 공용 클라이언트는 응용 프로그램 암호를 저장 하기 위해 신뢰할 수 없기 때문에 추가할 필요가 없습니다.

이 빠른 시작 가이드에서는 [Microsoft ID 플랫폼에 응용 프로그램](.././../active-directory/develop/quickstart-register-app.md)을 등록 하는 방법에 대 한 일반적인 정보를 제공 합니다.

## <a name="application-registrations-in-the-azure-portal"></a>Azure Portal에서 애플리케이션 등록

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 를 선택합니다.
2. **Azure Active Directory** 블레이드에서 **앱 등록** 를 선택 합니다.
3. **새 등록** 을 선택합니다.

   [![Azure Portal ](media/dicom-azure-app-registrations.png) 의 DICOM 응용 프로그램 등록 ](media/dicom-azure-app-registrations.png#lightbox)

## <a name="application-registration-overview"></a>응용 프로그램 등록 개요

1. 사용자 측 표시 이름을 사용 하 여 DICOM 서비스 응용 프로그램을 제공 합니다.

   [![Azure는 응용 프로그램을 등록 합니다. ](media/dicom-registration-application-name.png) ](media/dicom-registration-application-name.png#lightbox)

2. **지원 되는 계정 유형** 으로 응용 프로그램을 사용 하거나 API에 액세스할 수 있는 사용자를 선택 합니다.
3. (**선택 사항**) **리디렉션 URI** 를 제공 합니다. 이러한 세부 정보는 나중에 변경할 수 있지만 응용 프로그램의 회신 URL을 알고 있는 경우 입력 합니다.
4. **등록** 을 선택합니다.

## <a name="configuring-an-application-as-a-public-application"></a>응용 프로그램을 공용 응용 프로그램으로 구성

[데스크톱](.././../active-directory/develop/scenario-desktop-app-registration.md), [모바일](.././../active-directory/develop/scenario-mobile-app-registration.md) 또는 [단일 페이지](.././../active-directory/develop/scenario-mobile-app-registration.md) 응용 프로그램을 공용 응용 프로그램으로 구성 하려면 다음 단계를 참조 하세요.

1. [Azure Portal](https://portal.azure.com)에서 **앱 등록** 블레이드를 선택 하 고 **인증** 블레이드를 선택 합니다.

   [![Azure public 응용 프로그램 등록 ](media/dicom-authentication.png) ](media/dicom-authentication.png#lightbox)

2. **고급 설정** 에서 **예** 를 선택 합니다.

   단일 페이지 응용 프로그램의 경우 **액세스 토큰** 및 **ID 토큰** 을 선택 하 여 암시적 흐름을 사용 하도록 설정 합니다.
   * 애플리케이션에서 사용자를 로그인하는 경우 **ID 토큰** 을 선택합니다.
   * 애플리케이션에서 보호된 웹 API를 호출해야 하는 경우에는 **액세스 토큰** 을 선택합니다.

3. **저장** 을 선택합니다.

## <a name="api-permissions"></a>API 사용 권한

[기밀 클라이언트 응용 프로그램과](dicom-register-confidential-client-application.md)마찬가지로이 응용 프로그램이 사용자를 대신해 서 요청 해야 하는 API 권한을 선택 해야 합니다.

DICOM 서비스를 사용 하는 경우 내 조직에서 사용 하는 Api에서 Azure 의료 Api를 검색 하 여 DICOM 용 Azure API에 대 한 사용 권한을 추가 합니다. HealthcareAPIs 리소스 공급자를 이미 등록 한 경우에만이를 찾을 수 있습니다.

[![내 API 권한. ](media/dicom-request-my-api-permissions.png) ](media/dicom-request-my-api-permissions.png#lightbox)

DICOM 서비스 응용 프로그램에서 요청할 권한을 선택 합니다.

[![API 권한 ](media/dicom-request-api-permissions.png) 요청 ](media/dicom-request-api-permissions.png#lightbox)

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Active Directory에 공용 클라이언트 응용 프로그램을 등록 하는 방법에 대해 알아보았습니다.  

>[!div class="nextstepaction"]
>[DICOM 서비스 개요](dicom-services-overview.md)










