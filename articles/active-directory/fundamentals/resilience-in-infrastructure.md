---
title: Azure Active Directory를 사용하여 IAM 인프라에서 복원력 빌드
description: IAM 인프라 중단에 대비해서 복원력을 빌드하려는 설계자 및 IT 관리자를 위한 가이드입니다.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fc12433a187f030a0349247680c731c04a76274
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129402814"
---
# <a name="build-resilience-in-your-identity-and-access-management-infrastructure"></a>ID 및 액세스 관리 인프라의 복원력 빌드

Azure Active Directory는 조직의 리소스에 대한 인증 및 권한 부여와 같은 중요한 서비스를 제공하는 글로벌 클라우드 ID 및 액세스 관리 시스템입니다. 이 문서에서는 Azure AD(Azure Active Directory)를 사용하는 리소스에 대한 인증 또는 권한 부여 서비스의 중단 위험을 이해하고, 방지하고, 완화할 수 있는 지침을 제공 합니다. 

문서 세트는 다음을 위해 설계되었습니다.

* ID 설계자

* ID 서비스 소유자

* ID 작업 팀

[애플리케이션 개발자](./resilience-app-development-overview.md)와 [Azure AD B2C 시스템](resilience-b2c.md)에 대한 설명서도 참조하세요.

## <a name="what-is-resilience"></a>복원력이란?

ID 인프라의 컨텍스트에서 복원력은 비즈니스, 사용자 및 운영에 미치는 영향을 최소화하거나 없애면서 인증 및 권한 부여, 기타 구성 요소 실패 등과 같은 서비스 중단을 버티는 기능입니다. 중단이 발생할 경우 심각한 영향을 받을 수 있으며, 복원력을 유지하려면 철저한 계획이 필요합니다.

## <a name="why-worry-about-disruption"></a>중단을 우려하는 이유는 무엇일까요?

호출의 구성 요소 중 하나라도 실패하면 인증 시스템에 대한 모든 호출이 중단될 수 있습니다. 기본 구성 요소 오류로 인해 인증이 중단되면 사용자는 애플리케이션에 액세스하지 못하게 됩니다. 따라서 인증 호출 수와 해당 호출에 대한 종속성 수를 줄이는 것이 복원력에 중요합니다. 애플리케이션 개발자는 토큰을 요청하는 빈도에 대한 일부 제어를 어설션할 수 있습니다. 예를 들어, 가능한 경우 개발자와 협력하면서 애플리케이션에 Azure AD 관리 ID를 사용하도록 하는 것이 중요합니다. 

Azure AD와 같은 토큰 기반 인증 시스템에서 사용자의 애플리케이션(클라이언트)은 애플리케이션 또는 기타 리소스에 액세스하기 위해 먼저 ID 시스템에서 보안 토큰을 획득해야 합니다. 유효 기간 동안 클라이언트는 애플리케이션에 액세스하기 위해 동일한 토큰을 여러 번 제공할 수 있습니다.

애플리케이션에 제공된 토큰이 만료되면 애플리케이션은 토큰을 거부하고 클라이언트는 Azure AD에서 새 토큰을 획득해야 합니다. 새 토큰을 획득하려면 자격 증명 프롬프트, 인증 시스템의 다른 요구 사항 충족과 같은 사용자 조작이 필요합니다. 수명이 긴 토큰을 사용하여 인증 호출의 빈도를 줄이면 불필요한 상호 작용이 감소합니다. 그러나 토큰 수명과 정책 평가를 줄임으로써 야기되는 위험을 적절히 고려해야 합니다. 토큰 수명을 관리하는 방법에 대한 자세한 내용은 [재인증 프롬프트 최적화](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)에 대한 이 문서를 참조하세요.

## <a name="ways-to-increase-resilience"></a>복원력을 높이는 방법
다음 다이어그램에서는 복원력을 높일 수 있는 6가지 구체적인 방법을 보여 줍니다. 각 방법은 이 문서의 다음 단계에 링크된 문서에 자세히 설명되어 있습니다.
  
![관리 복원력 개요를 보여 주는 다이어그램](./media/resilience-in-infrastructure/admin-resilience-overview.png)

## <a name="next-steps"></a>다음 단계
관리자 및 설계자를 위한 복원력 리소스
 
* [자격 증명 관리를 사용하여 복원력 빌드](resilience-in-credentials.md)

* [장치 상태를 사용하여 복원력 빌드](resilience-with-device-states.md)

* [CAE(연속 액세스 평가)를 사용하여 복원력 빌드](resilience-with-continuous-access-evaluation.md)

* [외부 사용자 인증에서 복원력 빌드](resilience-b2b-authentication.md)

* [하이브리드 인증에서 복원력 빌드](resilience-in-hybrid.md)

* [응용 프로그램 프록시를 사용하여 응용 프로그램 액세스에서 복원력 빌드](resilience-on-premises-access.md)

개발자를 위한 복원력 리소스

* [애플리케이션에서 IAM 복원력 빌드](resilience-app-development-overview.md)

* [CIAM 시스템에서 복원력 빌드](resilience-b2c.md)
