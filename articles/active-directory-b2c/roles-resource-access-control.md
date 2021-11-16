---
title: 역할 및 리소스 액세스 제어
titleSuffix: Azure AD B2C
description: 역할을 사용하여 리소스 액세스를 제어하는 방법을 알아봅니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/08/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 672df647bb655210ab759278a69e63a38abc1cf4
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130035299"
---
# <a name="roles-and-resource-access-control"></a>역할 및 리소스 액세스 제어

액세스 제어 전략을 계획하는 경우 리소스에 액세스하는 데 필요한 최소한의 권한 있는 역할을 사용자에게 할당하는 것이 가장 좋습니다. 다음 표에서는 Azure AD B2C 테넌트의 기본 리소스와 해당 리소스를 관리하는 사용자에게 가장 적합한 관리 역할에 대해 설명합니다.

|리소스  |설명  |역할  |
|---------|---------|---------|
|[애플리케이션 등록](tutorial-register-applications.md) | Azure AD B2C 내에서 웹, 모바일 및 네이티브 애플리케이션 등록의 모든 측면을 만들고 관리합니다.|[애플리케이션 관리자](../active-directory/roles/permissions-reference.md#application-administrator)|
|[ID 공급자](add-identity-provider.md)| [로컬 ID 공급자](identity-provider-local.md)와 외부 소셜 또는 엔터프라이즈 ID 공급자를 구성합니다. | [외부 ID 공급자 관리자](../active-directory/roles/permissions-reference.md#external-identity-provider-administrator)|
|[API 커넥터](add-api-connector.md)| 사용자 흐름을 웹 API와 통합하여 사용자 환경을 사용자 지정하고 외부 시스템과 통합합니다.|[외부 ID 사용자 흐름 관리자](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[회사 브랜딩](customize-ui.md#configure-company-branding)| 사용자 흐름 페이지를 사용자 지정합니다.| [전역 관리자](../active-directory/roles/permissions-reference.md#global-administrator)|
|[사용자 특성](user-flow-custom-attributes.md)| 모든 사용자 흐름에 사용할 수있는 사용자 지정 특성을 추가하거나 삭제합니다.| [외부 ID 사용자 흐름 특성 관리자](../active-directory/roles/permissions-reference.md#external-id-user-flow-attribute-administrator)|
|사용자 관리| 이 문서에서 설명한 대로 [소비자 계정](manage-users-portal.md) 및 관리 계정을 관리합니다.| [사용자 관리자](../active-directory/roles/permissions-reference.md#user-administrator)|
|역할 및 관리자| Azure AD B2C 디렉터리에서 역할 할당을 관리합니다. Azure AD B2C 역할에 할당할 수 있는 그룹을 만들고 관리합니다. |[전역 관리자](../active-directory/roles/permissions-reference.md#global-administrator), [권한 있는 역할 관리자](../active-directory/roles/permissions-reference.md#privileged-role-administrator)|
|[사용자 흐름](user-flow-overview.md)|가입, 로그인 및 프로필 편집과 같은 일반적인 ID 작업을 빠르게 구성하고 사용하도록 설정합니다.| [외부 ID 사용자 흐름 관리자](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[사용자 지정 정책](user-flow-overview.md)| Azure AD B2C에서 모든 사용자 지정 정책을 만들고, 읽고, 업데이트하고, 삭제합니다.| [B2C IEF 정책 관리자](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)|
|[정책 키](policy-keys-overview.md)|사용자 지정 정책에 사용되는 토큰, 클라이언트 암호, 인증서 및 암호에 서명하고 유효성을 검사하기 위한 암호화 키를 추가하고 관리합니다.|[B2C IEF 키 세트 관리자](../active-directory/roles/permissions-reference.md#b2c-ief-keyset-administrator)|