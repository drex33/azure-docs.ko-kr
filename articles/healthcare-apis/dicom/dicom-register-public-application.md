---
title: Azure Active Directory 공용 클라이언트 애플리케이션 등록 - DICOM용 Azure Healthcare API
description: 이 문서에서는 Azure Active Directory 공용 애플리케이션을 등록하는 방법을 설명합니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/13/2021
ms.author: aersoy
ms.openlocfilehash: f8a720508221d1a9a3308d64649863d11c6b57c7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122435342"
---
# <a name="register-a-public-client-application"></a>공용 클라이언트 애플리케이션 등록

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 문서에서는 azure AD(Azure Active Directory)에 공용 애플리케이션을 등록하는 방법을 알아봅니다.

클라이언트 애플리케이션 등록은 사용자를 대신하여 API 권한을 인증하고 요청할 수 있는 애플리케이션의 Azure AD 표현입니다. 공용 클라이언트는 비밀을 기밀로 유지할 수 없는 모바일 애플리케이션 및 단일 페이지 JavaScript 애플리케이션과 같은 애플리케이션입니다. 이 절차는 [기밀 클라이언트 애플리케이션을 등록하는](dicom-register-confidential-client-application.md)것과 유사하지만 퍼블릭 클라이언트가 애플리케이션 비밀을 보유하도록 신뢰할 수 없으므로 추가할 필요가 없습니다.

이 빠른 시작 가이드에서는 [Microsoft ID 플랫폼 애플리케이션을](.././../active-directory/develop/quickstart-register-app.md)등록하는 방법에 대한 일반 정보를 제공합니다.

## <a name="application-registrations-in-the-azure-portal"></a>Azure Portal에서 애플리케이션 등록

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 를 선택합니다.
2. **Azure Active Directory** 블레이드에서 **앱 등록** 선택합니다.
3. **새 등록** 을 선택합니다.

   [Azure Portal DICOM 애플리케이션 ![ ](media/dicom-azure-app-registrations.png) 등록 ](media/dicom-azure-app-registrations.png#lightbox)

## <a name="application-registration-overview"></a>애플리케이션 등록 개요

1. DICOM 서비스 애플리케이션에 사용자용 표시 이름을 제공합니다.

   [![Azure에서 애플리케이션을 등록합니다. ](media/dicom-registration-application-name.png) ](media/dicom-registration-application-name.png#lightbox)

2. **지원되는 계정 유형에** 대해 애플리케이션을 사용하거나 API에 액세스할 수 있는 사람을 선택합니다.
3. (**선택 사항)** 리디렉션 **URI 를** 제공합니다. 이러한 세부 정보는 나중에 변경할 수 있지만 애플리케이션의 회신 URL을 알고 있는 경우 입력합니다.
4. **등록** 을 선택합니다.

## <a name="configuring-an-application-as-a-public-application"></a>애플리케이션을 공용 애플리케이션으로 구성

[데스크톱,](.././../active-directory/develop/scenario-desktop-app-registration.md) [모바일](.././../active-directory/develop/scenario-mobile-app-registration.md) 또는 [단일 페이지](.././../active-directory/develop/scenario-mobile-app-registration.md) 애플리케이션을 공용 애플리케이션으로 구성하려면 아래 단계를 참조하세요.

1. [Azure Portal](https://portal.azure.com)에서 **앱 등록** 블레이드를 선택한 다음 인증 **블레이드를** 선택합니다.

   [![Azure 공용 애플리케이션 등록. ](media/dicom-authentication.png) ](media/dicom-authentication.png#lightbox)

2. 고급 **설정에서** **예를** 선택합니다.

   단일 페이지 애플리케이션의 경우 **액세스 토큰 및** ID **토큰을** 선택하여 암시적 흐름을 사용하도록 설정합니다.
   * 애플리케이션에서 사용자를 로그인하는 경우 **ID 토큰** 을 선택합니다.
   * 애플리케이션에서 보호된 웹 API를 호출해야 하는 경우에는 **액세스 토큰** 을 선택합니다.

3. **저장** 을 선택합니다.

## <a name="api-permissions"></a>API 사용 권한

기밀 클라이언트 [애플리케이션과](dicom-register-confidential-client-application.md)마찬가지로 이 애플리케이션이 사용자를 대신하여 요청해야 하는 API 권한을 선택해야 합니다.

DICOM 서비스를 사용하는 경우 조직에서 사용하는 API에서 Azure Healthcare API를 검색하여 DICOM용 Azure API에 대한 권한을 추가합니다. Microsoft.HealthcareAPIs 리소스 공급자를 이미 등록한 경우에만 찾을 수 있습니다.

[![내 API 권한. ](media/dicom-request-my-api-permissions.png) ](media/dicom-request-my-api-permissions.png#lightbox)

DICOM 서비스 애플리케이션에서 요청할 권한을 선택합니다.

[![API 사용 권한을 요청합니다. ](media/dicom-request-api-permissions.png) ](media/dicom-request-api-permissions.png#lightbox)

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Active Directory 공용 클라이언트 애플리케이션을 등록하는 방법을 배웠습니다.  

>[!div class="nextstepaction"]
>[DICOM 서비스 개요](dicom-services-overview.md)










