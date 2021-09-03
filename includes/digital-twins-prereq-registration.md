---
author: baanders
description: Azure Digital Twins에 대한 파일 포함 - 앱 등록을 설정하기 위한 필수 구성 요소
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 128aa82762b80958f02cf22e9e7017bb334fde0c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437853"
---
이 문서에 사용된 모든 리소스를 인증하려면 **[Azure AD(Azure Active Directory)](../articles/active-directory/fundamentals/active-directory-whatis.md) 앱 등록을 설정** 해야 합니다. [앱 등록 만들기](../articles/digital-twins/how-to-create-app-registration-portal.md)의 지침에 따라 설정합니다. 

앱을 등록하면 등록의 **애플리케이션(클라이언트) ID**, **디렉터리(테넌트) ID** 및 **클라이언트 암호**([Azure Portal에서 찾기](../articles/digital-twins/how-to-create-app-registration-portal.md#collect-important-values))가 필요합니다. 나중에 Azure Digital Twins API에 액세스 권한을 부여하는 데 사용할 수 있도록 이러한 값을 기록해 둡니다.