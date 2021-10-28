---
title: Azure Active Directory B2C에서 사용자 데이터 관리
description: Azure AD B2C에서 사용자 데이터를 삭제하거나 내보내는 방법을 알아봅니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/06/2018
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 03b21f0e7a9f3035a5bfad6494d49f69ba3b5fef
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130041769"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 데이터 관리

 이 문서에서는 [Microsoft Graph API](/graph/use-the-api)에서 제공하는 작업을 사용하여 Azure AD(Azure Active Directory) B2C에서 사용자 데이터를 관리할 수 있는 방법을 설명합니다. 사용자 데이터를 관리하는 작업에는 감사 로그에서 데이터를 삭제하거나 내보내는 작업이 포함됩니다.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>사용자 데이터 삭제

사용자 데이터는 Azure AD B2C 디렉터리와 감사 로그에 저장됩니다. 모든 사용자 감사 데이터는 Azure AD B2C에서 7일간 유지됩니다. 7일 내에 사용자 데이터를 삭제하려면 [사용자 삭제](/graph/api/user-delete) 작업을 사용하면 됩니다. 데이터가 상주할 수도 있는 Azure AD B2C 테넌트마다 삭제 작업이 필요합니다.

Azure AD B2C의 모든 사용자에게는 개체 ID가 할당됩니다. 개체 ID는 Azure AD B2C에서 사용자 데이터를 삭제하는 데 사용할 수 있는 명확한 식별자를 제공합니다. 아키텍처에 따라 개체 ID는 재무, 마케팅 및 고객 관계 관리 데이터베이스와 같은 기타 서비스 전반에서 유용한 상관 관계 식별자가 될 수 있습니다.

사용자에 대한 개체에 ID를 확보하는 가장 정확한 방법은 Azure AD B2C 인증 과정에서 확보하는 것입니다. 다른 방법을 사용하여 사용자로부터 유효한 데이터 요청을 수신하는 경우 사용자를 찾고 연결된 개체 ID를 기록하는 데 고객 서비스 지원 에이전트 검색 등의 오프라인 프로세스가 필요할 수 있습니다.

다음 예제에는 가능한 데이터 삭제 흐름을 보여줍니다.

1. 사용자가 로그인하여 **내 데이터 삭제** 를 선택합니다.
2. 애플리케이션이 애플리케이션의 관리 섹션 내에 데이터를 삭제하는 옵션을 제공합니다.
3. 애플리케이션이 Azure AD B2C에 인증을 강제로 수행합니다. Azure AD B2C가 사용자의 개체 ID가 포함된 토큰을 애플리케이션에 제공합니다.
4. 애플리케이션이 토큰을 수신하고 개체 ID가 Microsoft Graph API에 대한 호출을 통해 사용자 데이터를 삭제하는 데 사용됩니다. Microsoft Graph API가 사용자 데이터를 삭제하고 200 OK 상태 코드를 반환합니다.
5. 애플리케이션이 개체 ID나 기타 식별자를 사용하여 필요에 따라 다른 조직 시스템에서 사용자 데이터 삭제를 조정합니다.
6. 애플리케이션이 데이터 삭제를 확인하고 사용자에게 다음 단계를 제공합니다.

## <a name="export-customer-data"></a>고객 데이터 내보내기

Azure AD B2C에서 고객 데이터를 내보내는 프로세스는 삭제 프로세스와 비슷합니다.

Azure AD B2C 사용자 데이터는 다음 항목으로 제한됩니다.

- **Azure Active Directory에 저장된 데이터**: 개체 ID나 이메일 또는 사용자 이름과 같은 로그인 이름을 사용하여 Azure AD B2C 인증 사용자 경험에서 데이터를 검색할 수 있습니다.
- **사용자별 감사 이벤트 보고서**: 개체 ID를 사용하여 데이터를 인덱싱할 수 있습니다.

다음과 같은 데이터 내보내기 흐름 예제에서 애플리케이션이 수행하는 것으로 언급된 단계는 백 엔드 프로세스 또는 디렉터리의 관리자 역할이 있는 사용자에 의해 수행될 수도 있습니다.

1. 사용자가 애플리케이션에 로그인합니다. Azure AD B2C는 필요한 경우 Azure AD 다단계 인증을 사용하여 인증을 적용합니다.
2. 애플리케이션은 사용자 자격 증명을 사용하여 사용자 특성을 검색하는 Microsoft Graph API 작업을 호출합니다. Microsoft Graph API는 속성 데이터를 JSON 형식으로 제공합니다. 스키마에 따라 사용자에 대한 모든 개인 데이터를 포함하도록 ID 토큰 콘텐츠를 설정할 수 있습니다.
3. 애플리케이션이 사용자 감사 활동을 검색합니다. Microsoft Graph API가 이벤트 데이터를 애플리케이션에 제공합니다.
4. 애플리케이션이 사용자가 사용할 수 있도록 데이터를 집계합니다.

## <a name="next-steps"></a>다음 단계

사용자가 애플리케이션에 액세스하는 방법을 관리하는 방법에 대해 알아보려면 [사용자 액세스 관리](manage-user-access.md)를 참조하세요.