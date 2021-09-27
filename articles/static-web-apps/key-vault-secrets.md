---
title: Azure Key Vault에서 인증 비밀 보호
description: Azure Key Vault에서 관리 ID를 사용하여 인증 비밀을 보호합니다.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/17/2021
ms.author: cshoe
ms.openlocfilehash: 1d6aa4d6e8a04a65366d6f109fd755be4f075d6b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124777149"
---
# <a name="securing-authentication-secrets-in-azure-key-vault"></a>Azure Key Vault에서 인증 비밀 보호

사용자 지정 인증 공급자를 구성하는 경우 Azure Key Vault에 연결 비밀을 저장하는 것이 좋습니다. 이 문서에서는 관리 ID를 사용하여 사용자 지정 인증 비밀에 대한 키 자격 증명 모음에 대한 액세스 권한을 Azure Static Web Apps에 부여하는 방법을 설명합니다.

보안 비밀을 적용하려면 다음 항목을 준비해야 합니다.

- 정적 웹앱 인스턴스에서 시스템 할당 ID를 만듭니다.
- ID에 대한 액세스 키 자격 증명 모음 비밀 액세스를 부여합니다.
- 정적 웹앱 애플리케이션 설정에서 키 자격 증명 모음 비밀을 참조하세요.

이 문서에서는 [BYOF(Bring Your Own Functions) 애플리케이션](./functions-bring-your-own.md)용 프로덕션에서 위의 각 항목을 설정하는 방법을 보여 줍니다.

Key Vault 통합은 다음에 사용할 수 없습니다.

- [정적 웹앱의 스테이징 버전](./review-publish-pull-requests.md). Key Vault 통합은 프로덕션 환경에서만 지원됩니다.
- [관리형 함수를 사용하는 정적 웹앱](./apis.md).

> [!NOTE]
> 관리 ID 사용은 Azure Static Web Apps 표준 계획에서만 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- [BYOF(Bring Your Own Functions)](./functions-bring-your-own.md)를 사용하는 기존의 Azure Static Web Apps 사이트
- 비밀 값이 있는 기존 Key Vault 리소스

## <a name="create-identity"></a>ID 만들기

1. Azure Portal의 Static Web App 사이트를 엽니다.

1. _설정_ 메뉴에서 **ID** 를 선택합니다.

1. **할당된 시스템** 탭을 선택합니다.

1. _상태_ 레이블에서 **켜기** 단추를 선택합니다.

1. **저장** 단추를 선택합니다.

    :::image type="content" source="media/key-vault-secrets/azure-static-web-apps-enable-managed-identity.png" alt-text="시스템이 할당한 ID 추가":::

1. 확인 대화 상자가 나타나면 **예** 단추를 선택합니다.

    :::image type="content" source="media/key-vault-secrets/azure-static-web-apps-enable-managed-identity-confirmation.png" alt-text="ID 할당을 확인합니다.":::

이제 정적 웹앱이 키 자격 증명 모음 비밀을 읽도록 허용하는 액세스 정책을 추가할 수 있습니다.

## <a name="add-a-key-vault-access-policy"></a>Key Vault 액세스 정책 추가

1. Azure Portal에서 Key Vault 리소스를 엽니다.

1. _설정_ 메뉴에서 **액세스 정책** 을 선택합니다.

1. **액세스 정책 추가** 링크를 선택합니다.

1. _비밀 권한_ 드롭다운에서 **받기** 를 선택합니다.

1. _주체 선택_ 레이블 옆에 있는 **선택하지 않음** 링크를 선택합니다.

1. 검색 상자에서 고정 웹앱 애플리케이션 이름을 검색합니다.

1. 애플리케이션 이름과 일치하는 목록 항목을 선택합니다.

1. **선택** 단추를 누릅니다.

1. **추가** 단추를 선택합니다.

1. **저장** 단추를 선택합니다.

    :::image type="content" source="media/key-vault-secrets/azure-static-web-apps-key-vault-save-policy.png" alt-text="Key Vault 액세스 정책 저장":::

이제 액세스 정책이 키 자격 증명 모음에 저장됩니다. 다음으로, 정적 웹앱을 키 자격 증명 모음 리소스에 연결하기 위해 사용할 비밀의 URI에 액세스합니다.

1. _설정_ 메뉴에서 **비밀** 을 선택합니다.

1. 목록에서 원하는 비밀을 선택합니다.

1. 목록에서 원하는 비밀 버전을 선택합니다.

1. _비밀 식별자_ 텍스트 상자 끝에 있는 **복사 단추** 을 선택하여 비밀 URI 값을 클립보드에 복사합니다.

1. 나중에 사용할 수 있도록 이 값을 텍스트 편집기에 붙여넣습니다.

## <a name="add-application-setting"></a>애플리케이션 설정 추가

1. Azure Portal의 Static Web App 사이트를 엽니다.

1. _설정_ 메뉴에서 **구성** 을 선택합니다.

1. _애플리케이션 설정_ 섹션에서 **추가** 단추를 선택합니다.

1. _이름_ 필드의 텍스트 상자에 이름을 입력합니다.

1. _값_ 필드의 텍스트 상자에서 비밀 값을 확인합니다.

    비밀 값은 몇 가지 다른 값의 복합 값입니다. 다음 템플릿에서는 최종 문자열이 빌드되는 방법을 보여 줄 수 있습니다.

    ```text
    @Microsoft.KeyVault(SecretUri=<YOUR-KEY-VAULT-SECRET-URI>)
    ```

    다음 단계를 사용하여 전체 비밀 값을 빌드합니다.

1. 위에서 템플릿을 복사하여 텍스트 편집기에 붙여넣습니다.

1. `<YOUR-KEY-VAULT-SECRET-URI>`를 이전에 따로 설정한 키 자격 증명 모음 URI 값으로 바꿉니다.

1. 새 전체 문자열 값을 복사합니다.

1. _값_ 필드의 텍스트 상자에 값을 붙여넣습니다.

1. **확인** 단추를 선택합니다.

1. _애플리케이션 설정_ 도구 모음의 위쪽에서 **저장** 단추를 선택합니다.

    :::image type="content" source="media/key-vault-secrets/azure-static-web-apps-application-settings-save.png" alt-text="애플리케이션 설정 저장":::

이제 사용자 지정 인증 구성이 새로 만든 애플리케이션 설정을 참조하면 고정 웹앱의 ID를 사용하여 Azure Key Vault 값이 추출됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 인증](./authentication-custom.md)
