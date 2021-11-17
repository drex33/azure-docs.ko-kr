---
title: HR 프로비저닝과 관련된 특성 검색 문제 해결
description: HR 프로비저닝과 관련된 특성 검색 문제를 해결하는 방법을 알아봅니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: troubleshooting
ms.workload: identity
ms.date: 10/27/2021
ms.author: kenwith
ms.reviewer: chmutali
ms.openlocfilehash: f1c0cc4812a3a2770c4b4580d0755bf3766499dd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482834"
---
# <a name="troubleshoot-hr-attribute-retrieval-issues"></a>HR 특성 검색 문제 해결

## <a name="provisioning-app-is-not-fetching-all-workday-attributes"></a>프로비저닝 앱이 모든 Workday 특성을 가져오지 않습니다.
**적용 대상:**
* 온-프레미스 Active Directory 사용자 프로비저닝에 대한 Workday
* Azure Active Directory 사용자 프로비저닝에 대한 Workday

| | |
|-- | -- |
| **문제점** | Workday 인바운드 프로비저닝 앱을 설정하고 Workday 테넌트 URL에 성공적으로 연결했습니다. 테스트 동기화를 실행했는데 프로비저닝 앱이 Workday의 일부 특성을 검색하지 않는 것을 확인했습니다. 일부 특성만 읽고 대상에 프로비저닝됩니다. |
| **원인** | 기본적으로 Workday 프로비저닝 앱은 WWS(Workday Web Services) v21.1에서 작동하는 특성 매핑 및 XPATH 정의와 함께 제공됩니다. 프로비저닝 앱에서 Workday로의 연결을 구성할 때 WWS API 버전(예: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0`)을 명시적으로 지정한 경우 WWS API 버전과 XPATH 정의가 일치하지 않기 때문에 이 문제가 발생할 수 있습니다.  |
| **해결 방법** | * *옵션 1*: URL에서 WWS API 버전 정보를 제거하고 기본 WWS API 버전 v21.1을 사용합니다. <br> * *옵션 2*: XPATH API 식을 기본 설정된 WWS API 버전과 호환되도록 수동으로 업데이트합니다. [Workday 특성 참조](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30) 섹션을 참조하여 **특성 매핑 -> 고급 옵션 -> Workday의 특성 목록 편집** 아래에서 **XPATH API 식** 을 업데이트합니다.  |

## <a name="provisioning-app-is-not-fetching-workday-integration-system-attributes--calculated-fields"></a>프로비저닝 앱이 Workday 통합 시스템 특성/계산 필드를 페치하지 않음
**적용 대상:**
* 온-프레미스 Active Directory 사용자 프로비저닝에 대한 Workday
* Azure Active Directory 사용자 프로비저닝에 대한 Workday

| | |
|-- | -- |
| **문제점** | Workday 인바운드 프로비저닝 앱을 설정하고 Workday 테넌트 URL에 성공적으로 연결했습니다. Workday에 통합 시스템이 구성되어 있고 Workday 통합 시스템의 특성을 가리키는 XPATH를 구성했습니다. 그러나 Azure AD 프로비저닝 앱은 이러한 통합 시스템 특성 또는 계산 필드와 연결된 값을 가져오지 않습니다. |
| **원인** | 이 문제는 알려진 제한 사항입니다. Workday 프로비저닝 앱은 현재 계산된 필드/통합 시스템 특성 페치를 지원하지 않습니다.  |
| **해결 방법** | 이 제한 사항에 대한 해결 방법이 없습니다. |


## <a name="next-steps"></a>다음 단계

* [Azure AD와 Workday 통합 시나리오 및 웹 서비스 호출에 대한 자세한 정보](workday-integration-reference.md)
* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](check-status-user-account-provisioning.md).

