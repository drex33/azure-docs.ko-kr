---
title: 빠른 시작 - Azure API Management 인스턴스 만들기
description: Azure Portal을 사용하여 새 Azure API Management 서비스 인스턴스를 만듭니다.
author: dlepow
ms.service: api-management
ms.topic: quickstart
ms.custom: mvc, mode-portal
ms.date: 09/08/2020
ms.author: danlep
ms.openlocfilehash: 4f2e0ea9b9d45fc25f8cb0fc238e563076e69301
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133054757"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 새 Azure API Management 서비스 인스턴스 만들기

Azure APIM(API Management)을 사용하여 조직은 외부, 파트너 및 내부의 개발자에게 API를 게시하여 데이터 및 서비스의 잠재성을 활용할 수 있습니다. API Management는 개발자 참여, 비즈니스 통찰력, 분석, 보안과 보호 등을 통해 성공적인 API 프로그램을 보장하는 핵심적인 역량을 제공합니다. APIM을 사용하면 어디서든 호스팅되는 기존 백 엔드 서비스를 위한 최신 API 게이트웨이를 만들고 관리할 수 있습니다. 자세한 내용은 [개요](api-management-key-concepts.md)를 참조하세요.

이 빠른 시작에서는 Azure Portal을 사용하여 새 API Management 인스턴스를 만드는 단계를 설명합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

API Management 인스턴스

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-a-new-service"></a>새 서비스 만들기

1. Azure Portal 메뉴에서 **리소스 만들기** 를 선택합니다. Azure **홈** 페이지에서 **리소스 만들기** 를 선택할 수도 있습니다. 
   
   :::image type="content" source="media/get-started-create-service-instance/00-CreateResource-01.png" alt-text="리소스 만들기"::: 선택

   
1. **신규** 페이지에서 **통합** > **API Management** 를 선택합니다.

   새 Azure API Management 인스턴스
   
1. **API Management 서비스** 페이지에서 설정을 입력합니다.

   :::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-create-new.png" alt-text="새 인스턴스":::
   
   | 설정                 | Description   |                                                                     
   |-------------------------|-----------------------------------------------|
   | 이름 - **                | API Management 서비스에 대한 고유한 이름입니다. 이 이름은 나중에 변경할 수 없습니다. 서비스 이름은 서비스와 해당 Azure 리소스를 모두 나타냅니다. <br/> 서비스 이름은 기본 도메인 이름 *\<name\>.azure-api.net* 을 생성하는 데 사용됩니다. 사용자 지정 도메인 이름을 사용하려는 경우 [사용자 지정 도메인 구성](configure-custom-domain.md)을 참조하세요. |
   | **구독**:          | 이 새 서비스 인스턴스가 만들어질 구독입니다.   |
   | **리소스 그룹**      |  신규 또는 기존 리소스 그룹을 선택합니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. |
   | **위치**          | 사용 가능한 API Management 서비스 위치에서 지리적으로 가까운 지역을 선택합니다. | 
   | **조직 이름**   | 조직의 이름입니다. 이 이름은 여러 개발자 포털 제목 및 알림 전자 메일의 보낸 사람을 비롯하여 여러 위치에 사용됩니다. |                                                         
   | **관리자 전자 메일** | **API Management** 에서 모든 알림이 전송될 이메일 주소입니다.   |  
   | **가격 책정 계층**        | 서비스를 평가할 **개발자** 계층을 선택합니다. 이 계층은 프로덕션 용도가 아닙니다. API Management 계층 크기 조정에 대한 자세한 내용은 [업그레이드 및 크기 조정](upgrade-and-scale.md)을 참조하세요. |

3. **만들기** 를 선택합니다.

    > [!TIP]
    > 이 계층에서 API Management 서비스를 만들고 활성화하는 데 30~40분 정도 걸릴 수 있습니다. **대시보드에 고정** 을 선택하면 새로 생성된 서비스를 보다 쉽게 찾을 수 있습니다.

[!INCLUDE [api-management-navigate-to-instance](../../includes/api-management-navigate-to-instance.md)]

**개요** 페이지에서 서비스의 속성을 검토합니다.

   API Management 인스턴스

API Management 서비스 인스턴스가 온라인 상태이면 사용할 준비가 된 것입니다. 자습서를 시작하여 [첫 번째 API를 가져오고 게시](import-and-publish.md)하세요.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 다음 단계에 따라 리소스 그룹 및 모든 관련된 리소스를 제거할 수 있습니다.

1. Azure Portal에서 **리소스 그룹** 을 검색하고 선택합니다. **홈** 페이지에서 **리소스 그룹** 을 선택할 수도 있습니다. 

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-01.png" alt-text="리소스 그룹 탐색":::

1. **리소스 그룹** 페이지에서 리소스 그룹을 선택합니다.

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-02.png" alt-text="리소스 그룹":::을 선택합니다.

1. 리소스 그룹 페이지에서 **리소스 그룹 삭제** 를 선택합니다. 
   
1. 리소스 그룹의 이름을 입력하고 **삭제** 를 선택합니다.

   리소스 그룹 삭제

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [첫 번째 API 가져오기 및 게시](import-and-publish.md)
