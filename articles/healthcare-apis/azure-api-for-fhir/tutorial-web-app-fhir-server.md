---
title: 웹앱 자습서 - Azure API for FHIR 설정
description: 이 자습서에서는 간단한 웹 애플리케이션을 배포하는 예제를 안내합니다. 이 첫 번째 자습서에서는 필수 구성 요소 및 Azure API for FHIR 배포에 대해 설명합니다.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.custom: devx-track-js
ms.openlocfilehash: a29321072c81974c3afc4ab7a1a9894d1a886b90
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785868"
---
# <a name="deploy-javascript-app-to-read-data-from-azure-api-for-fhir"></a>JavaScript 앱을 배포하여 Azure API for FHIR 데이터 읽기
이 자습서에서는 FHIR 서비스에서 데이터를 읽는 작은 JavaScript 앱을 배포합니다. 이 자습서의 단계는 다음과 같습니다.
1. FHIR 서버 배포
1. 공용 클라이언트 애플리케이션 등록
1. 애플리케이션에 대한 액세스 테스트
1. 이 FHIR 데이터를 읽는 웹 애플리케이션 만들기

## <a name="prerequisites"></a>필수 구성 요소
이 자습서 세트를 시작하기 전에 다음 항목이 필요합니다.
1. Azure 구독
1. Azure Active Directory 테넌트
1. [Postman](https://www.getpostman.com/) 설치

> [!NOTE]
> 이 자습서에서는 FHIR 서비스, Azure AD 애플리케이션 및 Azure AD 사용자가 모두 동일한 Azure AD 테넌트에 있습니다. 그렇지 않은 경우에도 이 자습서를 계속 진행할 수 있지만 추가 단계를 수행하려면 참조 문서 중 일부를 파악해야 할 수 있습니다.

## <a name="deploy-azure-api-for-fhir"></a>Azure API for FHIR 배포
자습서의 첫 번째 단계는 Azure API for FHIR을 올바르게 설정하는 것입니다.

1. 아직 없는 경우 [Azure API for FHIR](fhir-paas-portal-quickstart.md)을 배포합니다.
1. Azure API for FHIR을 배포한 후 Azure API for FHIR로 이동하고 CORS를 선택하여 [CORS](configure-cross-origin-resource-sharing.md) 설정을 구성합니다. 
    1. **원본** 을 *로 설정
    1. **헤더** 를 *로 설정
    1. **메서드** 에서 **모두 선택** 을 선택합니다.
    1. **최대 기간** 을 **600** 으로 설정

## <a name="next-steps"></a>다음 단계
Azure API for FHIR을 배포했으므로 이제 퍼블릭 클라이언트 애플리케이션을 등록할 준비가 되었습니다.

>[!div class="nextstepaction"]
>[퍼블릭 클라이언트 애플리케이션 등록](tutorial-web-app-public-app-reg.md)
