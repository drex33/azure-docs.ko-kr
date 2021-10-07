---
title: '빠른 시작: 엔터프라이즈 애플리케이션 속성 구성'
titleSuffix: Azure AD
description: Azure Active Directory에서 엔터프라이즈 애플리케이션의 속성을 구성합니다.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: f6325dfceaa0ee6cedc60e5555b177e1f7a25d6c
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058272"
---
# <a name="quickstart-configure-enterprise-application-properties-in-azure-active-directory"></a>빠른 시작: Azure Active Directory에서 엔터프라이즈 애플리케이션 속성 구성

이 빠른 시작에서는 Azure Active Directory 관리 센터를 사용하여 이전에 Azure AD(Azure Active Directory) 테넌트에 추가한 엔터프라이즈 애플리케이션의 속성을 구성합니다.

다음과 같은 엔터프라이즈 애플리케이션의 일반적인 속성을 구성할 수 있습니다.

- **사용자가 로그인할 수 있습니까?** - 애플리케이션에 할당된 사용자가 로그인할 수 있는지 여부를 결정합니다.
- **사용자 할당이 필요합니까?** - 애플리케이션에 할당되지 않은 사용자가 로그인할 수 있는지 여부를 결정합니다.
- **사용자가 볼 수 있습니까?** - 애플리케이션에 할당된 사용자가 내 앱 및 Microsoft 365 앱 시작 관리자에서 해당 애플리케이션을 볼 수 있는지 여부를 결정합니다. (Microsoft 365 웹 사이트의 왼쪽 위 모서리에 있는 와플 메뉴를 참조하세요.)
- **로고** - 애플리케이션을 나타내는 로고를 결정합니다.
- **참고** - 애플리케이션에 적용되는 메모를 추가할 수 있는 위치를 제공합니다.

이 빠른 시작의 단계를 테스트하려면 비 프로덕션 환경을 사용하는 것이 좋습니다.

## <a name="prerequisites"></a>필수 조건

엔터프라이즈 애플리케이션의 속성을 구성하려면 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 다음 역할 중 하나: 전역 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 관리자 또는 서비스 주체의 소유자.
- [빠른 시작: 엔터프라이즈 애플리케이션 추가](add-application-portal.md) 단계의 완료

## <a name="configure-application-properties"></a>애플리케이션 속성 구성

애플리케이션 속성은 애플리케이션이 표시되는 방법과 애플리케이션에 액세스하는 방법을 제어합니다.

애플리케이션 속성을 편집하려면:

1. [Azure Active Directory 관리 센터](https://aad.portal.azure.com)로 이동하고, 필수 구성 요소에 나열된 역할 중 하나를 사용하여 로그인합니다.
1. 왼쪽 메뉴에서 **엔터프라이즈 애플리케이션** 을 선택합니다. **모든 애플리케이션** 창이 열리고, Azure AD 테넌트의 애플리케이션 목록이 표시됩니다. 사용하려는 애플리케이션을 검색하여 선택합니다. 예를 들어 **Azure AD SAML Toolkit 1** 입니다.
1. **관리** 섹션에서 **속성** 을 선택하여 편집할 **속성** 창을 엽니다.
1. **예** 또는 **아니요** 를 선택하여 사용자가 로그인하는 데 애플리케이션을 사용하도록 설정할지 여부를 결정합니다.
1. **예** 또는 **아니요** 를 선택하여 애플리케이션에 할당된 사용자 계정만 로그인할 수 있는지 여부를 결정합니다.
1. **예** 또는 **아니요** 를 애플리케이션에 할당된 사용자가 내 앱 및 Microsoft 365 포털에서 해당 애플리케이션을 볼 수 있는지 여부를 결정합니다. 

    :::image type="content" source="media/add-application-portal-configure/configure-properties.png" alt-text="엔터프라이즈 애플리케이션의 속성 구성":::

## <a name="use-a-custom-logo"></a>사용자 지정 로고 사용

애플리케이션 로고는 내 앱 및 Microsoft 365 포털에 표시되며, 관리자가 엔터프라이즈 애플리케이션 갤러리에서 이 애플리케이션을 볼 때 표시됩니다. 사용자 지정 로고의 크기가 정확히 215x215 픽셀이어야 하고 PNG 형식이어야 합니다. 사용자에게 가장 잘 표시되도록 애플리케이션 로고에 투명도가 없는 단색 배경을 사용하는 것이 좋습니다.

사용자 지정 로고를 사용하려면:

1. 215 x 215 픽셀 로고를 만들어 .png 형식으로 저장합니다.
1. **파일 선택** 에서 아이콘을 선택하여 해당 로고를 업로드합니다.
1. 완료되면 **저장** 을 선택합니다.

로고 썸네일은 즉시 업데이트되지 않습니다. **속성** 창을 닫았다가 다시 열면 업데이트된 썸네일을 볼 수 있습니다.

## <a name="add-notes"></a>메모 추가

**메모** 속성을 사용하여 Azure AD에서 애플리케이션 관리와 관련된 모든 정보를 추가할 수 있습니다. **메모** 속성은 최대 1,024자 크기의 자유 텍스트 필드입니다.

애플리케이션에 대한 메모를 입력하려면,

1. 애플리케이션에서 유지하려는 메모를 입력합니다.
1. **저장** 을 선택합니다.

## <a name="clean-up-resources"></a>리소스 정리

다음 빠른 시작을 완료하려는 경우 여기서 만든 엔터프라이즈 애플리케이션을 유지합니다. 그렇지 않으면 해당 애플리케이션을 삭제하여 테넌트를 정리하는 것이 좋습니다. 자세한 내용은 [애플리케이션 삭제](delete-application-portal.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure AD 테넌트에서 애플리케이션을 검색하고 보는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [애플리케이션 보기](view-applications-portal.md)
