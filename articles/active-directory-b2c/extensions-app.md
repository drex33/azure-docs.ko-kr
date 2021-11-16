---
title: Azure Active Directory B2C의 확장 앱
description: b2c-extensions-app 복원
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 11/02/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: f01a8a51f467d9a090847aeea5da0426fe66e163
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424404"
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: 확장 앱 

Azure AD B2C 디렉터리를 만든 경우 **b2c-extensions-app** 이라는 앱이 새 디렉터리 안에 자동으로 만들어집니다. 이 앱은 *앱 등록* 에 표시됩니다. 이 앱은 사용자 및 사용자 지정 특성에 대한 정보를 저장하기 위해 Azure AD B2C 서비스에 의해 사용됩니다. 앱을 삭제하는 경우 Azure AD B2C가 제대로 작동하지 않으며 프로덕션 환경에 영향을 주게 됩니다.

> [!IMPORTANT]
> 테넌트를 즉시 삭제할 계획이 아니라면 **b2c-extensions-app** 을 삭제하지 마세요. 앱이 30일 넘도록 삭제된 상태로 유지되면 사용자 정보는 영구적으로 손실됩니다.

## <a name="verifying-that-the-extensions-app-is-present"></a>확장 앱이 있는지 확인

b2c-extensions-app이 있는지 확인하려면

1. Azure AD B2C 테넌트 내의 왼쪽 탐색 메뉴에서 **모든 서비스** 를 클릭합니다.
1. **앱 등록** 을 검색하여 엽니다.
1. **b2c-extensions-app** 으로 시작하는 앱을 찾습니다.

## <a name="recover-the-extensions-app"></a>확장 앱 복구

b2c-extensions-app을 실수로 삭제한 경우 30일 이내에는 복구할 수 있습니다. Graph API를 사용하여 앱을 복원할 수 있습니다.

1. [https://developer.microsoft.com/en-us/graph/graph-explorer](https://developer.microsoft.com/en-us/graph/graph-explorer) 으로 이동합니다.
1. 삭제된 앱을 복원하려는 Azure AD B2C 디렉터리에 대한 전역 관리자로 사이트에 로그인합니다. 이 전역 관리자는 다음과 비슷한 이메일 주소가 있어야 합니다. `username@{yourTenant}.onmicrosoft.com`
1. URL `https://graph.microsoft.com/beta/directory/deleteditems/microsoft.graph.application`에 대해 HTTP GET을 실행합니다. 이 작업은 지난 30일 내에서 삭제된 모든 애플리케이션을 나열합니다.
1. 목록에서 이름이 ‘b2c-extension-app’으로 시작하는 애플리케이션을 찾고 해당 `objectid` 속성 값을 복사합니다.
1. URL `https://graph.microsoft.com/beta/directory/deleteditems/{id}/restore`에 대해 HTTP POST를 실행합니다. URL의 `{id}` 부분을 이전 단계의 `objectid`로 바꿉니다.

이제 Azure Portal에서 [복원된 앱을 볼 수 있습니다](#verifying-that-the-extensions-app-is-present).

> [!NOTE]
> 애플리케이션이 지난 30일 이내 삭제된 경우에만 복원 가능합니다. 30일이 지난 경우 데이터는 영구적으로 손실됩니다. 추가 지원이 필요한 경우 지원 티켓을 파일로 저장합니다.
