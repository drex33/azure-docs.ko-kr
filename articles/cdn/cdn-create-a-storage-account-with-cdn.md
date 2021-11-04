---
title: '빠른 시작: Azure CDN과 Azure Storage 계정 통합'
description: 이 빠른 시작에서는 Azure Storage에서 Blob을 캐시하여 고대역폭 콘텐츠를 제공하는 Azure CDN(Content Delivery Network)을 사용하는 방법을 알아봅니다.
services: cdn
author: duongau
ms.service: azure-cdn
ms.topic: quickstart
ms.date: 04/30/2020
ms.author: duau
ms.custom: mvc
ms.openlocfilehash: a420432e52e584fbcac85d8801d025ac1fee80e0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439332"
---
# <a name="quickstart-integrate-an-azure-storage-account-with-azure-cdn"></a>빠른 시작: Azure CDN과 Azure Storage 계정 통합

이 빠른 시작에서는 [Azure CDN(Content Delivery Network)](cdn-overview.md)이 Azure Storage에서 콘텐츠를 캐시하도록 활성화합니다. Azure CDN은 개발자에게 고대역폭 콘텐츠를 배달하기 위한 전역 솔루션을 제공합니다. 미국, 유럽, 아시아, 오스트레일리아 및 남아메리카의 물리적 노드에 컴퓨팅 인스턴스의 Blob 및 정적 콘텐츠를 캐시할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com) 에 로그인합니다.

## <a name="create-a-storage-account"></a>스토리지 계정 만들기

스토리지 계정을 통해 Azure Storage 서비스에 액세스할 수 있습니다. 스토리지 계정은 Azure Blob, Queue Storage, Table Storage와 같은 각 Azure Storage 서비스 구성 요소에 액세스하기 위한 가장 높은 수준의 네임스페이스를 나타냅니다. 자세한 내용은 [Microsoft Azure Storage 소개](../storage/common/storage-introduction.md) 를 참조하세요.

스토리지 계정을 만들려면 관련 구독에 대한 서비스 관리자 또는 공동 관리자여야 합니다.

1. Azure Portal의 왼쪽 위에서 **리소스 만들기** 를 선택합니다. **새로 만들기** 창이 나타납니다.

1. **스토리지 계정** 을 검색하고, 드롭다운 목록에서 **스토리지 계정 - BLOB, 파일, 테이블, 큐** 를 선택합니다. **만들기** 를 선택합니다.
    
    ![스토리지 리소스 선택](./media/cdn-create-a-storage-account-with-cdn/cdn-select-new-storage-account.png)

1. **스토리지 계정 만들기** 창에서 다음 정보를 입력합니다.

    | 설정 | 값 | 
    | --- | --- |
    | 프로젝트 세부 정보 > 리소스 그룹 | **새로 만들기** 를 선택하고, 이름으로 *CDNQuickstart-rg* 를 사용합니다. 원한다면 기존 리소스 그룹을 사용할 수도 있습니다. |
    | 인스턴스 세부 정보 > 스토리지 계정 이름 | 소문자와 숫자만 사용하여 계정 이름을 3-24자 사이에서 입력합니다. 이름은 Azure에서 고유해야 하며 구독에 대한 BLOB, 큐 또는 테이블 리소스의 주소를 지정하는 데 사용되는 URL 내의 호스트 이름이 됩니다. Blob 스토리지에서 컨테이너 리소스의 주소를 지정하려면 http:// *&lt;storageaccountname&gt;* .blob.core.windows.net/ *&lt;container-name&gt;* 형식의 URI를 사용합니다.
    | 인스턴스 세부 정보 > 위치 | 드롭다운 목록에서 가까운 Azure 지역을 선택합니다. |
    
    나머지 정보는 기본값으로 두고 **검토 + 만들기** 를 선택합니다.

1. 스토리지 계정을 만드는 데 몇 분 정도 걸릴 수 있습니다. 만들기가 완료되면 **리소스로 이동** 을 선택하여 다음 단계에 사용할 스토리지 계정 페이지를 엽니다.

## <a name="enable-azure-cdn-for-the-storage-account"></a>스토리지 계정에 대해 Azure CDN 활성화

1. 스토리지 계정에 대한 페이지의 왼쪽 메뉴에서 **Blob service** > **Azure CDN** 을 선택합니다. **Azure CDN** 페이지가 나타납니다.

    ![CDN 엔드포인트 만들기](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-endpoint-configuration.png)
    
1. **새 엔드포인트** 섹션에서 다음 정보를 입력합니다.

    | 설정  | 값 |
    | -------- | ----- |
    | **CDN 프로필** | **새로 만들기** 를 선택하고 프로필 이름을 입력합니다(예: *cdn-profile-123*). 프로필은 엔드포인트의 컬렉션입니다. |
    | **가격 책정 계층** | **표준 Microsoft** 와 같은 **표준** 옵션 중 하나를 선택합니다. |
    | **CDN 엔드포인트 이름** | 엔드포인트 호스트 이름을 입력합니다(예: *cdn-endpoint-123*). 이 이름은 URL _&lt;endpoint-name&gt;_ .azureedge.net의 캐시된 리소스에 액세스하는 데 사용되므로 Azure에서 전역적으로 고유해야 합니다. |
    | **원본 호스트 이름** | 기본적으로 새 CDN 엔드포인트는 스토리지 계정의 호스트 이름을 원본 서버로 사용합니다. |

1. **만들기** 를 선택합니다. 만든 엔드포인트는 엔드포인트 목록에 나타납니다.

    ![스토리지 새 CDN 엔드포인트](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-list.png)

> [!TIP]
> [대형 파일 다운로드 최적화](cdn-optimization-overview.md#large-file-download)처럼 CDN 엔드포인트에 대한 고급 구성 설정을 지정하려면 [Azure CDN 확장](cdn-create-new-endpoint.md)을 대신 사용하여 CDN 프로필 및 엔드포인트를 만들면 됩니다.


## <a name="enable-additional-cdn-features"></a>추가 CDN 기능 사용

스토리지 계정의 **Azure CDN** 페이지에서 목록에 있는 CDN 엔드포인트를 선택하여 CDN 엔드포인트 구성 창을 엽니다.

이 페이지에서 전송에 대해 [압축](cdn-improve-performance.md), [쿼리 문자열 캐싱](cdn-query-string.md) 및 [지역 필터링](cdn-restrict-access-by-country-region.md) 등과 같은 추가 CDN 기능을 사용하도록 설정할 수 있습니다. 
    
## <a name="enable-sas"></a>SAS 사용

프라이빗 스토리지 컨테이너에 제한된 액세스를 부여하려는 경우 Azure Storage 계정의 SAS(공유 액세스 서명) 기능을 사용할 수 있습니다. SAS는 계정 키를 노출하지 않고 Azure Storage 리소스에 대한 제한된 액세스 권한을 부여하는 URI입니다. 자세한 내용은 [SAS에 Azure CDN 사용](cdn-sas-storage-support.md)을 참조하세요.

## <a name="access-cdn-content"></a>CDN 콘텐츠 액세스

CDN에 캐시된 콘텐츠에 액세스하려면 포털에 제공된 CDN URL을 사용합니다. 캐시된 Blob에 대한 주소는 다음 형식을 갖습니다.

http://<*endpoint-name*\>.azureedge.net/<*myPublicContainer*\>/<*BlobName*\>

> [!NOTE]
> 스토리지 계정에 대한 Azure CDN 액세스를 사용하도록 설정하면 공개적으로 사용 가능한 모든 개체가 CDN POP 캐싱에 적합합니다. 현재 CDN에 캐시된 개체를 수정하는 경우 캐시된 콘텐츠 TTL(Time-to-Live) 기간이 만료된 후에 Azure CDN에서 해당 콘텐츠를 새로 고쳐야 Azure CDN을 통해 새 콘텐츠를 사용할 수 있습니다.

## <a name="remove-content-from-azure-cdn"></a>Azure CDN에서 콘텐츠 제거

더 이상 Azure CDN에 개체를 캐시하지 않으려면 다음 단계 중 하나를 수행할 수 있습니다.

- 컨테이너를 공용 대신 프라이빗으로 설정합니다. 자세한 내용은 [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](../storage/blobs/anonymous-read-access-configure.md)를 참조하세요.
- Azure Portal을 사용하여 CDN 엔드포인트를 사용하지 않도록 설정하거나 삭제합니다.
- 더 이상 개체 요청에 응답하지 않도록 호스티드 서비스를 수정합니다.

Azure CDN에 이미 캐시된 개체는 개체의 TTL(Time-to-Live) 기간이 만료되거나 엔드포인트가 [삭제](cdn-purge-endpoint.md)될 때까지 캐시된 상태로 유지됩니다. TTL(Time-to-Live)이 만료되면 Azure CDN에서 CDN 엔드포인트가 여전히 유효하며 개체에 익명으로 액세스할 수 있는지 확인합니다. 그렇지 않으면 개체가 더 이상 캐시되지 않습니다.

## <a name="clean-up-resources"></a>리소스 정리

이전 단계에서 리소스 그룹에 CDN 프로필 및 엔드포인트를 만들었습니다. [다음 단계](#next-steps)로 이동하려는 경우 이러한 리소스를 저장하고, 엔드포인트에 사용자 지정 도메인을 추가하는 방법에 대해 알아봅니다. 하지만 이러한 리소스를 나중에 사용하지 않을 경우 리소스 그룹을 삭제한 다음, 추가 요금을 방치하여 해당 리소스를 삭제할 수 있습니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택한 다음, *CDNQuickstart-rg*\* 를 선택합니다.

2. **리소스 그룹** 페이지에서 **리소스 그룹 삭제** 를 선택하고, 텍스트 상자에 *CDNQuickstart-rg* 를 입력한 다음, **삭제** 를 선택합니다.

    그러면 이 빠른 시작에서 만든 리소스 그룹, 프로필 및 엔드포인트를 삭제합니다.

3. 스토리지 계정을 삭제하려면 대시보드에서 선택한 다음, 상단 메뉴에서 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: CDN을 사용하여 웹앱에서 정적 콘텐츠 적용](cdn-add-to-web-app.md)
