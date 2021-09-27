---
title: API Management 레거시 개발자 포털에서 페이지 스타일 사용자 지정
titleSuffix: Azure API Management
description: 이 빠른 시작의 단계에 따라 Azure API Management 개발자 포털에 있는 요소의 스타일을 사용자 지정합니다.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: article
ms.date: 11/04/2019
ms.author: danlep
ms.openlocfilehash: ebdb3e9d4cd2d0c4e1e418fd962f6f32edd2f749
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659118"
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>개발자 포털 페이지의 스타일 사용자 지정

Azure API Management에는 개발자 포털을 사용자 지정하는 가장 기본적인 세 가지 방법이 있습니다.
 
* [페이지 레이아웃 요소 및 정적 페이지의 콘텐츠 편집](api-management-modify-content-layout.md)
* 개발자 포털 전반의 페이지 요소에 사용된 스타일 업데이트(이 가이드에 설명되어 있음)
* [포털에서 생성된 페이지에 사용된 템플릿 수정](api-management-developer-portal-templates.md)(예: API 문서, 제품, 사용자 인증)

이 문서에서는 레거시 **개발자** 포털의 페이지에서 요소 스타일을 사용자 지정하고 변경 내용을 확인하는 방법에 대해 알아봅니다.

![레거시 개발자 포털에서 설정을 변경하는 위치를 보여주는 스크린샷.](./media/modify-developer-portal-style/developer_portal.png)

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>사전 요구 사항

+ [Azure API Management 용어](api-management-terminology.md)를 익힙니다.
+ 다음 빠른 시작을 완료합니다. [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)
+ 또한, 다음 자습서 [첫 번째 API 가져오기 및 게시](import-and-publish.md)를 완료합니다.

## <a name="customize-the-developer-portal"></a>개발자 포털 사용자 지정

1. **개요** 를 선택합니다.
2. **개요** 창 위쪽의 **개발자 포털(레거시)** 단추를 클릭합니다.
3. 화면 왼쪽 위에는 두 개의 페인트 브러시로 구성된 아이콘이 표시됩니다. 이 아이콘 위로 마우스를 가져가 포털 사용자 지정 메뉴를 엽니다.

    ![두 개의 페인트 브러시로 아이콘을 강조 표시하는 스크린샷.](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. 메뉴에서 **스타일** 을 선택하여 스타일 사용자 지정 창을 엽니다.

    **스타일** 을 사용하여 사용자 지정할 수 있는 모든 요소가 페이지에 표시됩니다.
5. **변수 값을 변경하여 개발자 포털 모양 사용자 지정:** 필드에 "headings-color"를 입력합니다.

    **\@headings-color** 요소가 페이지에 나타납니다. 이 변수는 텍스트의 색을 제어합니다.

    ![스타일 사용자 지정](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. **\@headings-color** 변수의 필드를 클릭합니다. 
    
    색 선택 드롭다운이 열립니다.
7. 색 선택 드롭다운에서 새 색을 선택합니다.

    > [!TIP]
    > 실시간 미리 보기는 모든 변경 내용에 사용할 수 있습니다. 사용자 지정 창 맨 위에 있는 진행률 표시기가 나타납니다. 몇 초 후에 헤더 텍스트가 새로 선택한 색상으로 변경됩니다.

8. 사용자 지정 창 메뉴의 왼쪽 아래에서 **게시** 를 선택합니다.
9. **사용자 지정 게시** 를 선택하여 변경 내용을 공개적으로 사용할 수 있게 제공합니다.

## <a name="view-your-change"></a>변경 내용 보기

1. 개발자 포털로 이동합니다.
2. 변경한 내용을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

[템플릿을 사용하여 Azure API Management 개발자 포털을 사용자 지정하는 방법](api-management-developer-portal-templates.md)을 알아보는 데 관심이 있을 수도 있습니다.