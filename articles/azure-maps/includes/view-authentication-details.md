---
title: Microsoft Azure Maps의 인증 세부 정보 보기
description: Azure Portal을 사용하여 Azure Maps의 인증 세부 정보를 볼 수 있습니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/21/2021
ms.topic: include
ms.service: azure-maps
services: azure-maps
custom.ms: subject-rbac-steps
ms.openlocfilehash: ccc0e8c43001f161648032be7fbb83aec43cc49f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801206"
---
Azure Portal에서 Azure Maps 계정 인증 세부 정보를 보려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure Portal 메뉴로 이동합니다. **모든 리소스** 를 선택한 다음, Azure Maps 계정을 선택합니다.

3. 왼쪽 창의 **설정** 에서 **인증** 을 선택합니다.

    :::image type="content" border="true" source="../media/how-to-manage-authentication/view-authentication-keys.png" alt-text="인증 세부 정보":::

Azure Maps 계정을 만들 때 세 가지 값이 생성됩니다. 해당 값은 Azure Maps의 두 가지 인증 유형을 지원하는 데 사용됩니다.
- **Azure Active Directory 인증**: `Client ID`는 REST API 요청에 사용되는 계정을 나타냅니다. `Client ID` 값은 애플리케이션 구성에 저장된 후, Azure AD 인증을 사용하는 Azure Maps HTTP 요청을 수행하기 전에 검색되어야 합니다.
- **공유 키 인증**: `Primary Key`와 `Secondary Key`가 공유 키 인증의 구독 키로 사용됩니다. 공유 키 인증은 각 요청과 함께 Azure Maps 계정에서 생성된 키를 Azure Maps에 전달합니다. 키를 정기적으로 다시 생성하는 것이 좋습니다. 다시 생성하는 동안 현재 연결을 유지하기 위해 두 개의 키가 제공됩니다. 하나의 키를 다시 생성하는 동안 다른 키를 사용할 수 있습니다. 키를 다시 생성하는 경우 이 계정에 액세스하는 모든 애플리케이션이 새 키를 사용하도록 업데이트해야 합니다. 자세한 내용은 [Azure Maps 인증](../azure-maps-authentication.md)을 참조하세요.
