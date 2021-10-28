---
title: 무료 Azure Active Directory 개발자 테넌트를 만드는 방법
description: 이 문서에서는 개발자 계정을 만드는 방법을 보여줍니다.
services: active-directory
author: barclayn
manager: karenh444
ms.service: active-directory
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: fbdd59b38a9e699640b50e8f5402b60559990e8d
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130006853"
---
# <a name="how-to-create-a-free-azure-active-directory-developer-tenant"></a>무료 Azure Active Directory 개발자 테넌트를 만드는 방법

> [!IMPORTANT]
> Azure Active Directory 확인 가능한 자격 증명은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

> [!NOTE]
> 미리 보기에서는 P2 라이선스가 필요합니다. 

P2 평가판 라이선스를 사용하여 무료 Azure Active Directory를 만드는 두 가지 간단한 방법으로 확인 가능한 자격 증명 발급자 서비스를 설치하고 확인 가능한 자격 증명의 생성 및 유효성 검사를 테스트할 수 있습니다.

- 무료 Microsoft 365 개발자 프로그램에 [조인](https://aka.ms/o365devprogram)하여 P2 라이선스를 사용하는 Azure Active Directory와 같은 무료 샌드박스, 도구 및 기타 리소스를 받으세요. 구성된 사용자, 그룹, 사서함 등
- 새 [테넌트](../develop/quickstart-create-new-tenant.md)를 만들고 새 테넌트에서 Azure AD Premium P1 또는 P2의 [평가판](https://azure.microsoft.com/trial/get-started-active-directory/)을 활성화합니다.

무료 Microsoft 365 개발자 프로그램에 등록하려는 경우 몇 가지 간단한 단계를 수행해야 합니다.

1. 화면에서 **지금 조인** 단추를 클릭합니다.

2. 새 Microsoft 계정으로 로그인하거나 이미 있는 기존(작업) 계정을 사용하세요.

3. 등록 페이지에서 해당 지역을 선택하고 회사 이름을 입력하고 **다음** 을 클릭하기 전에 프로그램의 사용 약관에 동의합니다.

4. **구독 설정** 을 클릭합니다. 새 테넌트를 만들 지역을 지정하고, 사용자 이름과 도메인을 만들고, 암호를 입력합니다. 그러면 새 테넌트와 테넌트의 첫 번째 관리자가 생성됩니다.

5. 새 테넌트의 관리자 계정을 보호하는 데 필요한 보안 정보를 입력합니다. 그러면 계정에 대한 MFA 인증이 설정됩니다.


이 시점에서 25개의 E5 사용자 라이선스가 있는 테넌트를 만들었습니다. E5 라이선스에는 Azure AD P2 라이선스가 포함됩니다. 필요에 따라 사용자, 그룹, 메일 및 SharePoint와 함께 샘플 데이터 팩을 추가하여 개발 환경에서 데스트할 수 있습니다. 확인 가능한 자격 증명 발급 서비스의 경우에는 필요하지 않습니다.

사용자 편의를 위해 새로 만든 테넌트에 자신의 회사 계정을 [게스트](../external-identities/b2b-quickstart-add-guest-users-portal.md)로 추가하고 해당 계정을 사용하여 테넌트를 관리할 수 있습니다. 게스트 계정이 확인 가능한 자격 증명 서비스를 관리할 수 있도록 하려면 해당 사용자에게 '전역 관리자' 역할을 할당해야 합니다.

## <a name="next-steps"></a>다음 단계

이제 개발자 계정이 있으므로 확인 가능한 자격 증명에 대해 자세히 알아보려면 [첫 번째 자습서](get-started-verifiable-credentials.md)를 사용해 보세요.