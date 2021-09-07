---
title: Logz.io와의 Azure 통합을 위한 Single Sign-On - Azure 파트너 솔루션
description: Logz.io와의 Azure 통합을 위해 Single Sign-On을 설정하는 방법을 알아봅니다.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 08/17/2021
ms.author: tomfitz
ms.openlocfilehash: 04a2616e46f526e9601e102c8ecb4104b6595164
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122568118"
---
# <a name="set-up-logzio-single-sign-on"></a>Logz.io Single Sign-On 설정

이 문서에서는 Azure Active Directory에서 SSO(Single Sign-On)를 설정하는 방법을 설명합니다. Logz.io 통합을 위한 SSO는 선택 사항입니다.

## <a name="configure-single-sign-on"></a>Single Sign-On 구성

Logz.io 리소스 내에서 SAML(Security Assertion Markup Language) SSO 기능을 사용하려면 엔터프라이즈 애플리케이션을 설정해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Portal 메뉴에서 **Azure Active Directory** 를 선택하거나 _Azure Active Directory_ 를 검색합니다.
1. **관리** > **엔터프라이즈 애플리케이션** 으로 이동한 후 **새 애플리케이션** 단추를 선택합니다.
1. _Logz.io_ 를 검색하고 **Logz.io - Azure AD Integration** 이라는 SAML 애플리케이션을 선택한 다음, **만들기** 를 선택합니다.

   :::image type="content" source="./media/sso-setup/gallery.png" alt-text="Azure Active Directory 갤러리 찾아보기":::

1. **개요** 에서 SSO 애플리케이션의 **애플리케이션 ID** 를 복사합니다.

   이후 단계에서 SAML을 구성하는 데 사용하므로 _애플리케이션 ID_ 를 기록해 둡니다.

   :::image type="content" source="./media/sso-setup/app-id.png" alt-text="애플리케이션 ID 복사":::

1. **2. Single Sign On 설정** 타일을 선택합니다.

   :::image type="content" source="./media/sso-setup/setup.png" alt-text="Single Sign-On 설정":::

1. Single Sign-On 방법으로 **SAML** 프로토콜을 선택합니다.

   :::image type="content" source="./media/sso-setup/saml.png" alt-text="SAML Single Sign On":::

1. SAML 구성에서 기존 값을 선택하거나 텍스트 상자를 사용하여 필수 필드 **식별자** 및 **회신 URL** 을 입력합니다. 새 항목을 만들려면 이전 단계에서 복사한 _애플리케이션 ID_ 를 사용합니다.

   다음 패턴을 사용하여 새 값을 추가합니다.

   - **식별자**: `urn:auth0:logzio:<Application ID>`
   - **회신 URL**: `https://logzio.auth0.com/login/callback?connection=<Application ID>`

   :::image type="content" source="./media/sso-setup/saml-config.png" alt-text="기본 SAML 구성":::

1. **Single Sign-On 설정 저장** 프롬프트에서 **예** 를 선택하고 **저장** 을 선택합니다.

   SSO 프롬프트에서 **아니요, 나중에 테스트하겠습니다.** 를 선택합니다.

1. SAML 구성이 저장되면 **관리** > **속성** 으로 이동한 후 **사용자 할당 필요** 를 **아니요** 로 설정합니다. **저장** 을 선택합니다.

   :::image type="content" source="./media/sso-setup/properties.png" alt-text="Single Sign-On 속성":::

## <a name="next-steps"></a>다음 단계

- SSO 문제를 해결하려면 [문제 해결](troubleshoot.md)을 참조하세요.
- Logz.io 통합을 만들려면 [빠른 시작: Azure Portal에서 Logz.io 리소스 만들기](create.md)를 참조하세요.
