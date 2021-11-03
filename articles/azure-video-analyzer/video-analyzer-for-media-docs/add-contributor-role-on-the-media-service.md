---
title: Media Services에 참가자 역할 추가
description: 이 항목에서는 Media Services에 참가자 역할을 추가 하는 방법에 대해 설명 합니다.
ms.author: itnorman
ms.topic: how-to
ms.date: 10/13/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 294587e9f502a7f927bb905fbd354ab588955a85
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103069"
---
# <a name="add-contributor-role-on-the-media-services"></a>Media Services에 참가자 역할 추가

이 문서에서는 Media Services에 참가자 역할을 할당 하는 방법을 설명 합니다.

> [!NOTE]
> Azure Portal UI를 통해 미디어 용 Azure 비디오 분석기를 만드는 경우 선택한 관리 id는 선택한 미디어 서비스 계정에 대 한 참가자 권한으로 자동으로 할당 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

1. AMS(Azure Media Services)
2. 사용자 할당 관리 ID
> [!NOTE]
> Azure Media Services 및 사용자 할당 관리 id에 대 한 [참가자][docs-role-contributor] 역할 및 [사용자 액세스 관리자][docs-role-administrator] 역할 모두에 액세스할 수 있는 Azure 구독이 필요 합니다. 적절한 권한이 없는 경우 계정 관리자에게 권한을 부여해 달라고 요청하세요. 연결된 Azure Media Services는 Video Analyzer for Media 계정과 동일한 지역에 있어야 합니다.


## <a name="add-contributor-role-on-the-media-services"></a>Media Services에 참가자 역할 추가
### <a name="azure-portal"></a>[Azure Portal](#tab/portal/)

### <a name="add-contributor-role-on-the-media-services-in-the-azure-portal"></a>Azure Portal의 Media Services에 대 한 참가자 역할 추가

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
    * 위쪽의 검색 표시줄을 사용 하 여 **Media Services** 를 입력 합니다.
    * 미디어 서비스 리소스를 찾아 선택 합니다.
1. 왼쪽 창에서 **액세스 제어 (IAM)** 를 클릭 합니다.
    * **추가** > **역할 할당 추가** 를 클릭합니다. 역할을 할당할 권한이 없는 경우 **역할 할당 추가** 옵션을 사용할 수 없습니다.
1. 역할 목록에서 [참가자][docs-role-contributor] 역할을 선택 하 고 **다음** 을 클릭 합니다.
1. **액세스 할당** 대상에서 *관리 id* 라디오 단추를 선택 합니다.
    * **+ 구성원 선택** 단추를 클릭 하 고 **관리 되는 Id** 창을 선택 합니다.
1. 다음을 **선택** 합니다.
    * **구독** 에서 관리 되는 id가 있는 구독입니다.
    * **관리 id** 에서 *사용자 할당 관리 id* 를 선택 합니다.
    * **선택** 섹션에서 Media services 리소스에 대 한 참가자 권한을 부여 하려는 관리 되는 id를 검색 합니다.    
1. 보안 주체를 찾았으면 클릭하여 선택합니다.
1. 역할을 할당 하려면 **검토 + 할당** 을 클릭 합니다.

<!-- links -->
[docs-role-contributor]: ../../role-based-access-control/built-in-roles.md#contributor
[docs-role-administrator]: ../../role-based-access-control/built-in-roles.md#user-access-administrator
