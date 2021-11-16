---
title: 'Azure AD Connect 동기화: 디렉터리 확장 | Microsoft Docs'
description: 이 항목에서는 Azure AD Connect의 디렉터리 확장 기능을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/09/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ab5eba7cebe03ff59cfa442c454da855788ebad
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124768613"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect 동기화: 디렉터리 확장
디렉터리 확장을 사용하면 온-프레미스 Active Directory의 사용자 고유 특성을 사용하여 Azure AD(Active Directory)에서 스키마를 확장할 수 있습니다. 이 기능을 통해 온-프레미스를 계속 관리하는 특성을 이용하는 LOB 앱을 빌드할 수 있습니다. 해당 특성은 [확장](/graph/extensibility-overview
)을 통해 사용할 수 있습니다. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)를 사용하여 사용 가능한 특성을 확인할 수 있습니다. 이 기능을 사용하여 Azure AD에서 동적 그룹을 만들 수도 있습니다.

현재 해당 특성을 사용하는 Microsoft 365 워크로드는 없습니다.

>[!IMPORTANT]
>디렉터리 확장 특성을 동기화하는 데 사용되는 사용자 지정 규칙이 포함된 구성을 내보낸 경우 Azure AD Connect의 새 설치나 기존 설치로 이 규칙을 가져오려고 하면 가져오는 동안 규칙이 생성되지만 디렉터리 확장 특성은 매핑되지 않습니다.  이 문제를 해결하려면 디렉터리 확장 특성을 다시 선택하고 규칙과 다시 연결하거나 규칙을 완전히 다시 만들어야 합니다.

## <a name="customize-which-attributes-to-synchronize-with-azure-ad"></a>Azure AD와 동기화할 특성 사용자 지정

설치 마법사의 사용자 지정 설정 경로에서 동기화할 추가 속성을 구성합니다.

> [!NOTE]
> 1\.2.65.0 이전 버전의 Azure AD Connect에서는 **사용 가능한 특성** 에 대한 검색 상자가 대/소문자를 구분합니다.

![스키마 확장 마법사](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

설치 시 다음과 같은 특성이 표시됩니다. 이러한 특성은 유효한 후보입니다.

* 사용자 및 그룹 개체 유형
* 단일 값 특성: 문자열, 부울, 정수, 이진
* 다중 값 특성: 문자열, 이진

> [!NOTE]
> Azure Active Directory의 모든 기능이 다중값 확장 특성을 지원하는 것은 아닙니다. 이러한 특성을 사용할 계획인 기능의 설명서를 참조하여 지원되는지 확인하세요.

특성 목록은 Azure AD Connect 설치 도중에 만들어진 스키마 캐시에서 읽힙니다. 추가 특성을 사용하여 Active Directory 스키마를 확장한 경우 [스키마를 새로 고쳐야](how-to-connect-installation-wizard.md#refresh-directory-schema) 이러한 새 특성을 볼 수 있습니다.

Azure AD의 개체에는 최대 100개의 디렉터리 확장 특성이 있을 수 있습니다. 최대 길이는 250자입니다. 특성 값이 더 긴 경우 동기화 엔진에서 잘립니다.

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>마법사에서 만든 Azure AD에서의 구성 변경

Azure AD Connect를 설치하는 동안 이러한 특성을 사용할 수 있는 애플리케이션이 등록됩니다. Azure Portal에서 다음 애플리케이션을 볼 수 있습니다. 해당 이름은 항상 **테넌트 스키마 확장 앱** 입니다.

![스키마 확장 앱](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

이 앱을 보려면 **모든 애플리케이션** 을 선택해야 합니다.

특성에는 **확장명 \_{ApplicationId}\_** 가 접두사로 추가됩니다. ApplicationId는 Azure AD 테넌트의 모든 특성에 대해 동일한 값을 갖습니다. 이 토픽의 다른 모든 시나리오에 이 값이 필요합니다.

## <a name="viewing-attributes-using-the-microsoft-graph-api"></a>Microsoft Graph API를 사용하여 특성 보기

이제 Microsoft Graph API를 통해 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer#)를 사용하여 해당 특성을 확인할 수 있습니다.

>[!NOTE]
> Microsoft Graph API에서 반환할 특성을 요청해야 합니다. `https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division`과 같이 속성을 명시적으로 선택합니다.
>
> 자세한 내용은 [Microsoft Graph: 쿼리 매개 변수 사용](/graph/query-parameters#select-parameter)을 참조하세요.

>[!NOTE]
> AADConnect의 특성 값을 AADConnect에서 만들지 않은 확장 특성과 동기화하는 작업은 지원되지 않습니다. 이렇게 하면 성능 문제와 예기치 않은 결과가 발생할 수 있습니다. 위와 같이 생성된 확장 특성만 동기화를 지원합니다.

## <a name="use-the-attributes-in-dynamic-groups"></a>동적 그룹의 특성 사용

더 유용한 시나리오 중 하나는 동적 보안 또는 Microsoft 365 그룹을 통해 해당 특성을 사용하는 것입니다.

1. Azure AD에 그룹을 만듭니다. 적절한 이름을 지정하고 **멤버 자격 유형** 을 **동적 사용자** 로 합니다.

   ![새 그룹이 있는 스크린샷](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. **동적 쿼리 추가** 를 선택합니다. 속성을 살펴보면 해당 확장 특성이 표시되지 않으므로 먼저 이를 수동으로 추가해야 합니다. **사용자 지정 확장 속성 가져오기** 를 클릭하고 애플리케이션 ID를 입력한 다음, **속성 새로 고침** 을 클릭합니다.

   ![디렉터리 확장이 추가된 스크린샷](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. 속성 드롭다운을 열고 추가한 특성이 이제 표시되는지 확인합니다.

   ![UI에 새 특성이 표시된 스크린샷](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   요구 사항에 맞게 식을 작성합니다. 이 예제에서 규칙은 **(user.extension_9d98ed114c4840d298fad781915f27e4_division -eq "Sales and marketing")** 으로 설정됩니다.

4. 그룹을 만든 후에는 Azure AD에 구성원을 채운 후 구성원을 검토할 수 있는 시간을 제공합니다.

   ![동적 그룹의 구성원이 있는 스크린샷](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>다음 단계
[Azure AD Connect 동기화](how-to-connect-sync-whatis.md) 구성에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
