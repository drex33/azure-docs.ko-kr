---
title: 빠른 시작 - Azure CDN 프로필 및 엔드포인트 만들기
description: 이 빠른 시작에서는 새로운 CDN 프로필 및 엔드포인트를 만들어서 Azure CDN을 활성화하는 방법을 설명합니다.
author: duongau
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: azure-cdn
ms.topic: quickstart
ms.date: 04/30/2020
ms.author: duau
ms.custom: mvc, mode-other
ms.openlocfilehash: 37b46337acdc16997483ec5b2a863d93269ebe16
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133040055"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint"></a>빠른 시작: Azure CDN 프로필 및 엔드포인트 만들기

이 빠른 시작에서는 하나 이상의 CDN 엔드포인트 컬렉션인 CDN 프로필을 새로 만들어서 Azure CDN(Content Delivery Network)을 사용하도록 설정합니다. 프로필과 엔드포인트를 만든 후에 고객에게 콘텐츠를 제공하기 시작할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- 원본 호스트 이름에 사용하는 *cdnstorageacct123* 이라는 Azure Storage 계정. 이 요구 사항을 완료하려면 [Azure CDN과 Azure Storage 계정 통합](cdn-create-a-storage-account-with-cdn.md)을 참조하세요.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com) 에 로그인합니다.

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>새 CDN 엔드포인트 만들기

CDN 프로필을 만든 후에 엔드포인트를 만드는 데 사용할 수 있습니다.

1. Azure Portal에서 만든 대시보드에서 CDN 프로필을 선택합니다. 찾을 수 없는 경우 해당 프로필을 만든 리소스 그룹을 열거나, 포털 맨 위에 있는 검색 창을 사용하고, 프로필 이름을 입력하고, 결과에서 프로필을 선택할 수 있습니다.
   
1. CDN 프로필 페이지에서 **+ 엔드포인트** 를 선택합니다.
   
    ![CDN 프로필](./media/cdn-create-new-endpoint/cdn-select-endpoint.png)
   
    **엔드포인트 추가** 창이 나타납니다.

3. 다음 설정 값을 입력합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **이름** | 엔드포인트 호스트 이름에 *cdn-endpoint-123* 을 입력합니다. 이 이름은 Azure에서 전역적으로 고유해야 합니다. 이미 사용 중인 경우 다른 이름을 입력합니다. 이 이름은 _&lt;endpoint-name&gt;_ .azureedge.net 도메인에서 캐시된 리소스에 액세스하기 위해 사용됩니다.|
    | **원본 형식** | **스토리지** 를 선택합니다. | 
    | **원본 호스트 이름** | 드롭다운 목록에서 사용 중인 Azure Storage 계정의 호스트 이름(예: *cdnstorageacct123.blob.core.windows.net*)을 선택합니다. |
    | **원본 경로** | 비워 둡니다. |
    | **원본 호스트 헤더** | 기본값(원본 호스트 이름)을 그대로 유지합니다. |  
    | **프로토콜** | 기본값 **HTTP** 및 **HTTPS** 옵션을 선택해 둡니다. |
    | **원본 포트** | 포트 기본값을 그대로 둡니다. | 
    | **최적화 기준** | 기본 선택 영역 **일반 웹 배달** 을 그대로 둡니다. |

    ![엔드포인트 추가 창](./media/cdn-create-new-endpoint/cdn-add-endpoint.png)

3. 새 엔드포인트를 만들려면 **추가** 를 선택합니다. 엔드포인트가 만들어진 후 프로필에 대한 엔드포인트 목록에 표시됩니다.
    
   ![CDN 엔드포인트](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
   엔드포인트를 전파하는 데 걸리는 시간은 프로필을 만들 때 선택한 가격 책정 계층에 따라 달라집니다. **표준 Akamai** 는 일반적으로 1분 이내에 완료되고, **표준 Microsoft** 는 10분 이내에, **표준 Verizon** 및 **프리미엄 Verizon** 은 최대 30분 이내에 완료됩니다.

> [!NOTE]
> *Verizon CDN 엔드포인트* 의 경우 어떤 이유로든 엔드포인트를 **사용하지 않도록 설정** 하거나 **중지** 하면 Verizon 보조 포털을 통해 구성된 모든 리소스가 정리됩니다. 엔드포인트를 다시 시작하여 이러한 구성을 자동으로 복원할 수 없습니다. 이러한 구성을 다시 변경해야 합니다.

## <a name="clean-up-resources"></a>리소스 정리

이전 단계에서 리소스 그룹에 CDN 프로필 및 엔드포인트를 만들었습니다. [다음 단계](#next-steps)로 이동하려는 경우 이러한 리소스를 저장하고, 엔드포인트에 사용자 지정 도메인을 추가하는 방법에 대해 알아봅니다. 하지만 이러한 리소스를 나중에 사용하지 않을 경우 리소스 그룹을 삭제한 다음, 추가 요금을 방치하여 해당 리소스를 삭제할 수 있습니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택한 다음, **CDNQuickstart-rg** 를 선택합니다.

2. **리소스 그룹** 페이지에서 **리소스 그룹 삭제** 를 선택하고, 텍스트 상자에 *CDNQuickstart-rg* 를 입력한 다음, **삭제** 를 선택합니다. 그러면 이 빠른 시작에서 만든 리소스 그룹, 프로필 및 엔드포인트가 삭제됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: CDN을 사용하여 웹앱에서 정적 콘텐츠 적용](cdn-add-to-web-app.md)
