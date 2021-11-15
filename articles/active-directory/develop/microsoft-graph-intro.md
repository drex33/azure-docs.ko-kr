---
title: Microsoft Graph API
description: Microsoft Graph API는 Microsoft 클라우드 서비스 리소스에 액세스할 수 있도록 하는 RESTful 웹 API입니다.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/08/2021
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: a0159f1c633806c26bda39ffe0c8a295bec46982
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129993705"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph API

Microsoft Graph API는 Microsoft 클라우드 서비스 리소스에 액세스할 수 있도록 하는 RESTful 웹 API입니다. 앱을 등록하고 사용자 또는 서비스에 대한 인증 토큰을 가져온 후 Microsoft Graph API에 대한 요청을 수행할 수 있습니다. 자세한 내용은 [Microsoft Graph 개요](/graph/overview)를 참조하세요.

Microsoft Graph는 REST API 및 클라이언트 라이브러리를 노출하여 다음 Microsoft 클라우드 서비스의 데이터에 액세스합니다.

- Microsoft 365 서비스: Delve, Excel, Microsoft Bookings, Microsoft Teams, OneDrive, OneNote, Outlook/Exchange, Planner 및 SharePoint
- Enterprise Mobility 및 Security 서비스: Advanced Threat Analytics, Advanced Threat Protection, Azure Active Directory, Identity Manager 및 Intune
- Windows 10 서비스: 작업, 디바이스, 알림
- Dynamics 365 Business Central

## <a name="versions"></a>버전

현재 사용 가능한 Microsoft Graph API 버전은 다음과 같습니다.

- **베타 버전**: 베타 버전에는 현재 미리 보기 상태이며 `https://graph.microsoft.com/beta` 엔드포인트에서 액세스할 수 있는 API가 포함되어 있습니다. 베타 API 사용을 시작하려면 [Microsoft Graph 베타 엔드포인트 참조](/graph/api/overview?view=graph-rest-beta&preserve-view=true)를 참조하세요.
- **v1.0 버전**: v1.0 버전에는 일반적으로 사용 가능하고 프로덕션용으로 준비된 API가 포함되어 있습니다. v1.0 버전은 `https://graph.microsoft.com/v1.0` 엔드포인트에서 액세스할 수 있습니다. v1.0 API 사용을 시작하려면 [Microsoft Graph REST API v1.0 참조](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)를 참조하세요.

Microsoft Graph API 버전에 대한 자세한 내용은 [Microsoft Graph에 대한 버전 관리, 지원 및 호환성이 손상되는 변경 정책](/graph/versioning-and-support)을 참조하세요.


## <a name="get-started"></a>시작

사용자 또는 이메일 메시지와 같은 리소스에서 읽거나 쓰려면 다음 패턴과 같은 요청을 구성합니다.

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

구성된 요청의 요소에 대한 자세한 내용은 [Microsoft Graph API 사용](/graph/use-the-api)을 참조하세요.

Microsoft Graph API의 기능에 액세스하는 방법을 보여주는 빠른 시작 샘플을 사용할 수 있습니다. 샘플은 하나의 인증으로 Microsoft 계정과 Outlook의 두 서비스에 액세스할 수 있습니다. 각각의 빠른 시작은 Microsoft 계정 사용자 프로필의 정보에 액세스하여 달력에 이벤트를 표시합니다.
빠른 시작에는 다음의 네 단계가 포함됩니다:

- 플랫폼 선택
- 앱 ID 가져오기(클라이언트 ID)
- 샘플 빌드
- 로그인 및 달력의 이벤트 보기

빠른 시작을 완료하면 실행할 준비가 된 앱이 있습니다. 자세한 내용은 [Microsoft Graph 빠른 시작 FAQ](/graph/quick-start-faq)를 참조하세요. 샘플을 시작하려면 [Microsoft Graph 빠른 시작](https://developer.microsoft.com/graph/quick-start)을 참조하세요.

## <a name="tools"></a>도구

**Microsoft Graph Explorer** 는 Microsoft Graph API에 대한 요청을 빌드하고 테스트하는데 사용할 수 있는 웹 기반 도구입니다. https://developer.microsoft.com/graph/graph-explorer 에서 Microsoft Graph Explorer에 액세스합니다.

**Postman** 은 Microsoft Graph API에 대한 요청을 수행하는 데 사용할 수 있는 또 다른 도구입니다. https://www.getpostman.com 에서 Postman을 다운로드할 수 있습니다. Postman에서 Microsoft Graph와 상호 작용하려면 [Microsoft Graph Postman 컬렉션](/graph/use-postman)을 사용합니다.

## <a name="next-steps"></a>다음 단계

사용 정보 및 자습서를 포함하여 Microsoft Graph에 대한 자세한 내용은 다음을 참조하세요.

- [Microsoft Graph API 사용](/graph/use-the-api)
- [Microsoft Graph 자습서](/graph/tutorials)
