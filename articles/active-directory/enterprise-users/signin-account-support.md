---
title: 내 Azure AD 로그인 페이지에서 Microsoft 계정을 수락하나요? | Microsoft Docs
description: 로그인 중에 화면 메시지에서 사용자 이름 조회를 반영하는 방법
services: active-directory
author: curtand
manager: KarenH444
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: overview
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93bd814075bf4fc2603bc4dd55caec1b7e5543f5
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129985488"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Azure Active Directory의 Microsoft 계정에 대한 로그인 옵션

Azure AD(Azure Active Directory)용 Microsoft 365 로그인 페이지는 회사 또는 학교 계정과 Microsoft 계정을 지원하지만 사용자의 상황에 따라 하나 또는 둘 다 지원할 수 있습니다. 예를 들어 Azure AD 로그인 페이지는 다음을 지원합니다.

* 두 가지 유형의 계정 모두에서 로그인을 허용하는 앱
* 게스트를 수락하는 조직

## <a name="identification"></a>Identification(식별)
사용자 이름 필드의 힌트 텍스트를 살펴보면 조직에서 사용하는 페이지가 Microsoft 계정을 지원하는지 알 수 있습니다. 힌트 텍스트에 "이메일, 휴대폰 또는 Skype"가 표시되면 로그인 페이지는 Microsoft 계정을 지원합니다.

![계정 로그인 페이지의 차이점](./media/signin-account-support/ui-prompt.png)

[추가 로그인 옵션은 개인 Microsoft 계정에 대해서만 작동](https://azure.microsoft.com/updates/microsoft-account-signin-options/ )하지만 회사 또는 학교 계정 리소스에 로그인 하는 데 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

[로그인 브랜딩 사용자 지정](../fundamentals/add-custom-domain.md)