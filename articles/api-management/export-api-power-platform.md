---
title: Azure API Management에서 Microsoft Power Platform으로 API 내보내기 | Microsoft Docs
description: Microsoft Power Platform의 Power Apps 및 Power Automate에 대한 사용자 지정 커넥터로서 API Management에서 API를 내보내는 방법을 알아봅니다.
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: how-to
ms.date: 07/27/2021
ms.author: apimpm
ms.openlocfilehash: 6560995ba770e01c910c2b8fc923c9fcbad6f3d3
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122529706"
---
# <a name="export-apis-from-azure-api-management-to-the-power-platform"></a>Azure API Management에서 Power Platform으로 API 내보내기 

Microsoft [Power Platform](https://powerplatform.microsoft.com)을 사용하는 시민 개발자는 전문 개발자가 개발하고 Azure에 배포하는 비즈니스 기능에 도달해야 하는 경우가 많습니다. 전문 개발자는 [Azure API Management](https://aka.ms/apimrocks)를 통해 백 엔드 서비스를 API로서 게시하고, 이러한 API를 시민 개발자가 발견하고 사용할 수 있도록 사용자 지정 커넥터로서 Power Platform([Power Apps](/powerapps/powerapps-overview) 및 [Power Automate](/power-automate/getting-started))으로 쉽게 내보낼 수 있습니다. 

이 문서에서는 API Management에서 API에 대한 사용자 지정 Power Platform 커넥터를 만들기 위한 Azure Portal의 단계를 안내합니다. 시민 개발자는 이 기능과 함께 Power Platform을 사용하여 API Management에서 관리하는 내부 및 외부 API를 기반으로 하는 앱을 만들고 배포할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

+ 다음 빠른 시작을 완료합니다. [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)
+ API Management 인스턴스에 Power Platform으로 내보내려고 하는 API가 있는지 확인합니다.
+ Power Apps 또는 Power Automate [환경](/powerapps/powerapps-overview#power-apps-for-admins)을 보유하고 있는지 확인합니다. 

## <a name="create-a-custom-connector-to-an-api"></a>API에 대한 사용자 지정 커넥터 만들기

1. Azure Portal에서 API Management 서비스로 이동합니다.
1. 메뉴의 **API** 아래에서 **Power Platform** 을 선택합니다.
1. **커넥터 만들기** 를 선택합니다.
1. **커넥터 만들기** 창에서 다음을 수행합니다.
    1. Power Platform에 게시할 API를 선택합니다.
    1. API를 게시할 Power Platform 환경을 선택합니다. 
    1. 사용자 지정 커넥터의 이름으로 사용될 표시 이름을 입력합니다.  
    1. 선택적으로, API가 [OAuth 2.0 서버로 보호](api-management-howto-protect-backend-with-aad.md)되는 경우 **클라이언트 ID**, **클라이언트 비밀**, **권한 부여 URL**, **토큰 URL**, **새로 고침 URL** 을 비롯한 세부 정보를 제공합니다.  
1. **만들기** 를 선택합니다. 

    :::image type="content" source="media/export-api-power-platform/create-custom-connector.png" alt-text="API Management에서 API에 대한 사용자 지정 커넥터 만들기":::

커넥터를 만들었으면 [Power Apps](https://make.powerapps.com) 또는 [Power Automate](https://flow.microsoft.com) 환경으로 이동합니다. **데이터 > 사용자 지정 커넥터** 아래에 API가 나열되는 것을 볼 수 있습니다.

:::image type="content" source="media/export-api-power-platform/custom-connector-power-app.png" alt-text="Power Platform의 사용자 지정 커넥터":::

## <a name="next-steps"></a>다음 단계

* [Power Platform에 대한 자세한 정보](https://powerplatform.microsoft.com/)
* [사용자 지정 커넥터 만들기 및 사용에 대한 자세한 정보](/connectors/custom-connectors/)
* [자습서에 따라 API Management에서 일반적인 작업 알아보기](./import-and-publish.md)
