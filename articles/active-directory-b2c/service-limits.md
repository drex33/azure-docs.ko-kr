---
title: Azure AD B2C 서비스 한도 및 제한 사항
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 서비스에 대한 서비스 한도 및 제한 사항에 대한 참조입니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/02/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 8e3ceab429f92340a080a6a42afd095375ce51b1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130266090"
---
# <a name="azure-active-directory-b2c-service-limits-and-restrictions"></a>Azure Active Directory B2C 서비스 한도 및 제한 사항

이 문서에는 Azure AD B2C(Azure Active Directory B2C) 서비스에 대한 사용 제약 조건 및 기타 서비스 한도가 포함됩니다.

## <a name="end-userconsumption-related-limits"></a>최종 사용자/사용량 관련 한도

다음 최종 사용자 관련 서비스 한도는 SAML, Open ID Connect, OAuth2 및 ROPC를 포함하여 Azure AD B2C에서 지원하는 모든 인증 및 권한 부여 프로토콜에 적용됩니다.

|범주 |제한    |
|---------|---------|
|Azure AD B2C 테넌트당 IP 주소당 요청 수       |6,000/5분          |
|Azure AD B2C 테넌트당 총 요청 수     |12,000/분          |

요청 수는 Azure AD B2C 사용자 경험 중에 발생하는 디렉터리 읽기 및 쓰기 수에 따라 달라질 수 있습니다. 예를 들어 디렉터리에서 읽는 간단한 로그인 경험은 요청 1개로 구성됩니다. 로그인 경험에서 디렉터리도 업데이트해야 하는 경우 이 작업은 추가 요청으로 계산됩니다.

## <a name="azure-ad-b2c-configuration-limits"></a>Azure AD B2C 구성 한도

다음 표에는 Azure AD B2C 서비스의 관리 구성 한도가 나열되어 있습니다.

|범주  |제한  |
|---------|---------|
|애플리케이션당 범위 수        |1000          |
|사용자 <sup>1</sup>당 [사용자 지정 특성](user-profile-attributes.md#extension-attributes)  수       |100         |
|애플리케이션당 리디렉션 URL 수       |100         |
|애플리케이션당 로그아웃 URL 수        |1          |
|특성당 문자열 제한      |250개 문자          |
|구독당 B2C 테넌트 수      |20         |
|사용자 지정 정책의 [상속](custom-policy-overview.md#inheritance-model) 수준     |10         |
|Azure AD B2C 테넌트당 정책 수(사용자 흐름 + 사용자 지정 정책)     |200          |
|최대 정책 파일 크기      |1024KB          |

<sup>1</sup> 또한 [Azure AD 서비스 한도 및 제한 사항](../active-directory/enterprise-users/directory-service-limits-restrictions.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- Microsoft Graph [제한 지침](/graph/throttling)에 대해 알아보기 
- [Azure AD B2C 애플리케이션의 유효성 검사 차이점](../active-directory/develop/supported-accounts-validation.md)에 대해 알아보기
