---
title: DICOM 서비스에 대한 Azure Active Directory 애플리케이션 등록 - Azure Healthcare API
description: 이 문서에서는 DICOM 서비스에 대한 Azure Active Directory 애플리케이션 등록에 대해 설명합니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 07/10/2021
ms.author: aersoy
ms.openlocfilehash: 1db5ba11d4a3bd8380561ae493c69b809edf5135
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122435371"
---
# <a name="register-azure-active-directory-applications-for-the-dicom-service"></a>DICOM 서비스에 대한 Azure Active Directory 애플리케이션 등록

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

DICOM 서비스 또는 OSS(Azure용 FHIR 서버)를 설정하는 경우 선택할 수 있는 몇 가지 옵션이 있습니다. 오픈 소스의 경우 사용자 고유의 리소스 애플리케이션 등록을 만들어야 합니다. Azure API for FHIR의 경우 이 리소스 애플리케이션은 자동으로 생성됩니다.

## <a name="application-registrations"></a>애플리케이션 등록

애플리케이션이 Azure AD와 상호 작용하려면 등록해야 합니다. DICOM 서비스의 컨텍스트에서 논의할 클라이언트 애플리케이션 등록에는 세 가지 유형이 있습니다.

## <a name="client-applications"></a>클라이언트 애플리케이션

클라이언트 애플리케이션은 토큰을 요청하는 클라이언트의 등록입니다. OAuth 2.0에서는 세 가지 이상의 서로 다른 애플리케이션 유형을 구분하는 경우가 많습니다.

### <a name="confidential-clients"></a>기밀 클라이언트

기밀 클라이언트는 Azure AD에서 웹앱이라고도 합니다. 기밀 클라이언트는 [권한 부여 코드 흐름](../../active-directory/azuread-dev/v1-protocols-oauth-code.md)을 사용하여 유효한 자격 증명을 제시하는 로그인한 사용자를 대신하여 토큰을 가져오는 애플리케이션입니다. 비밀을 보유할 수 있고 토큰에 대한 인증 코드를 교환할 때 이 비밀을 Azure AD에 표시하기 때문에 기밀 클라이언트라고 합니다. 기밀 클라이언트는 클라이언트 비밀을 사용하여 자신을 인증할 수 있기 때문에 퍼블릭 클라이언트보다 더 신뢰할 수 있으며 수명이 긴 토큰을 가질 수 있으며 새로 고침 토큰을 부여받을 수 있습니다. 자세한 내용은 [Azure Active Directory 기밀 클라이언트 애플리케이션 등록을](dicom-register-confidential-client-application.md)참조하세요. 클라이언트가 인증 코드를 받을 회신 URL을 등록하는 것이 중요합니다.

### <a name="public-clients"></a>공용 클라이언트

비밀을 유지할 수 없는 클라이언트입니다. 일반적으로 사용자가 클라이언트의 비밀을 검색할 수 있는 모바일 디바이스 애플리케이션 또는 단일 페이지 JavaScript 애플리케이션입니다. 또한 퍼블릭 클라이언트는 권한 부여 코드 흐름을 사용하지만, 토큰을 가져올 때 비밀을 제공할 수 없으며 토큰 기한이 더 짧거나 새로 고침 토큰이 없을 수도 있습니다. Azure Active Directory 공용 클라이언트 애플리케이션을 등록하는 방법에 대한 세부 정보를 [읽어보십시오.](dicom-register-public-application.md)

### <a name="service-clients"></a>서비스 클라이언트

이러한 클라이언트는 [클라이언트 자격 증명 흐름](.././../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)을 사용하여 본인(사용자 대신이 아님)을 대신하여 토큰을 가져옵니다. 일반적으로 비대화형 방식으로 DICOM 서비스에 액세스하는 애플리케이션을 나타냅니다. 예를 들면 수집 프로세스가 있습니다. 서비스 클라이언트를 사용하는 경우 /authorize 엔드포인트를 호출하여 토큰을 얻는 프로세스를 시작할 필요가 없습니다. 서비스 클라이언트는 /token 엔드포인트로 바로 이동하여 클라이언트 ID 및 클라이언트 비밀을 표시하여 토큰을 가져올 수 있습니다. 자세한 내용은 [Azure Active Directory 서비스 클라이언트 애플리케이션 등록을](dicom-register-service-client-application.md)참조하세요.

## <a name="next-steps"></a>다음 단계

이 개요 문서에서는 리소스 및 클라이언트 애플리케이션이 DICOM 서비스와 함께 작동하도록 애플리케이션 등록 프로세스를 안내했습니다. 애플리케이션을 등록한 후 DICOM 서비스를 배포할 수 있습니다.

>[!div class="nextstepaction"]
>[Azure Active Directory 기밀 클라이언트 애플리케이션 등록](dicom-register-confidential-client-application.md)

>[!div class="nextstepaction"]
>[Azure Active Directory 공용 클라이언트 애플리케이션 등록](dicom-register-public-application.md)

>[!div class="nextstepaction"]
>[Azure Active Directory 서비스 클라이언트 애플리케이션 등록](dicom-register-service-client-application.md)