---
title: 포함 파일
description: 포함 파일
services: active-directory
author: daveba
ms.service: active-directory
ms.subservice: msi
ms.topic: include
ms.date: 07/13/2021
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: 7ea469c4d549d968745f08a5eff341bbc650854e
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "114200994"
---
- 각 관리 ID는 [Azure AD 서비스 한도 및 제한 사항](../articles/active-directory/enterprise-users/directory-service-limits-restrictions.md)에 설명된 대로 Azure AD 테넌트의 개체 할당량 한도 계산에 포함됩니다.
-   관리 ID를 만들 수 있는 속도에는 다음과 같은 한도가 있습니다.

    1. Azure 지역별 Azure AD 테넌트당: 20초당 작업 400개 만들기
    2. Azure 지역별 Azure 구독당: 20초당 작업 80개 만들기

-   사용자가 할당한 관리 ID가 Azure 리소스와 함께 할당될 수 있는 속도입니다.

    1. Azure 지역별 Azure AD 테넌트당: 20초당 작업 400개 할당
    2. Azure 지역별 Azure 구독당: 20초당 작업 300개 할당




