---
title: Azure AD 조건부 액세스의 사용자 지정 컨트롤
description: Azure Active Directory 조건부 액세스의 사용자 지정 컨트롤의 작동 방식에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b42df2aeb70d9dcdc9ef77ed224d50a352097ec
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128592834"
---
# <a name="custom-controls-preview"></a>사용자 지정 컨트롤(미리 보기)

사용자 지정 컨트롤은 Azure Active Directory의 미리 보기 기능입니다. 사용자 지정 컨트롤을 사용하는 경우 Azure Active Directory 외부의 인증 요구 사항을 충족하기 위해 호환 가능한 서비스로 리디렉션됩니다. 이 컨트롤을 충족하려면 사용자의 브라우저가 외부 서비스로 리디렉션되고 필요한 인증을 수행한 다음, Azure Active Directory로 다시 리디렉션됩니다. Azure Active Directory가 응답을 확인합니다. 성공적으로 인증되거나 유효성이 확인된 사용자는 조건부 액세스 흐름을 계속합니다.

> [!NOTE]
> 사용자 지정 컨트롤 기능에 계획 중인 변경 사항에 대한 자세한 내용은 2020년 2월 [새로운 기능 아카이브](../fundamentals/whats-new-archive.md#upcoming-changes-to-custom-controls)를 참조하세요.

## <a name="creating-custom-controls"></a>사용자 지정 컨트롤 만들기

사용자 지정 컨트롤은 일부 승인된 인증 공급자와 함께 작동합니다. 사용자 지정 컨트롤을 만들려면 먼저 사용할 공급자에게 문의해야 합니다. Microsoft가 아닌 각 공급자에게는 가입, 구독 또는 기타 서비스의 일부가 되고 조건부 액세스와 통합할 의사가 있음을 나타내기 위한 자체 프로세스 및 요구 사항이 있습니다. 이 시점에서 공급자는 JSON 형식의 데이터 블록을 제공합니다. 이 데이터는 테넌트가 공급자 및 조건부 액세스와 함께 작동할 수 있게 하고, 새 컨트롤을 만들고, 사용자가 공급자와의 검증을 성공적으로 수행했는지를 조건부 액세스에서 확인하는 방법을 정의합니다.

JSON 데이터를 복사한 다음 관련 텍스트 상자에 붙여넣습니다. 변경 사항을 명시적으로 이해하지 못한 경우 JSON을 변경하지 마세요. 변경하면 공급자와 Microsoft 간의 연결이 끊어져 잠재적으로 사용자가 사용자 계정 밖에서 잠길 수 있습니다.

사용자 지정 컨트롤을 만드는 옵션은 **조건부 액세스** 페이지의 **관리** 섹션에 있습니다.

![조건부 액세스의 사용자 지정 컨트롤 인터페이스](./media/controls/custom-controls-conditional-access.png)

**새 사용자 지정 컨트롤** 을 클릭하면 컨트롤의 JSON 데이터에 대한 텍스트 상자가 있는 블레이드가 열립니다.  

![새 사용자 지정 컨트롤](./media/controls/new-custom-controls-conditional-access.png)

## <a name="deleting-custom-controls"></a>사용자 지정 컨트롤 삭제

사용자 지정 컨트롤을 삭제하려면 먼저 조건부 액세스 정책에서 사용되지 않는지 확인해야 합니다. 작업이 완료되면 다음을 수행합니다.

1. 사용자 지정 컨트롤 목록으로 이동합니다.
1. …를 클릭합니다.  
1. **삭제** 를 선택합니다.

## <a name="editing-custom-controls"></a>사용자 지정 컨트롤 편집

사용자 지정 컨트롤을 편집하려면 현재 컨트롤을 삭제하고 업데이트된 정보로 새 컨트롤을 만들어야 합니다.

## <a name="known-limitations"></a>알려진 제한 사항

사용자 지정 컨트롤은 Intune 디바이스 등록의 일부 또는 Azure AD에 디바이스를 조인하는 경우 Azure AD Multi-Factor Authentication, Azure AD SSPR(셀프 서비스 암호 재설정), 다단계 인증 클레임 요구 사항 충족, PIM(Privileged Identity Manager)의 역할 승격이 필요한 ID 보호 자동화와 함께 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [조건부 액세스 일반 정책](concept-conditional-access-policy-common.md)

- [보고서 전용 모드](concept-conditional-access-report-only.md)

- [조건부 액세스 What If 도구를 사용하여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)
