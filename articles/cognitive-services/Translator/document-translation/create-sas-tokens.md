---
title: Microsoft Storage Explorer를 사용 하 여 컨테이너 및 blob에 대 한 SAS (공유 액세스 서명) 토큰 생성
description: Microsoft Storage Explorer 및 Azure Portal를 사용 하 여 컨테이너 및 blob에 대 한 SAS (공유 액세스 서명 토큰)를 생성 하는 방법입니다.
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 09/16/2021
ms.openlocfilehash: ecc62d7b45bfb3b0b108057d5e3e25b189297165
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128653308"
---
# <a name="generate-sas-tokens-for-your-storage-containers"></a>저장소 컨테이너에 대 한 SAS 토큰 생성

이 문서에서는 Azure Storage Explorer 또는 Azure Portal을 사용하여 공유 액세스 서명(SAS) 토큰을 만드는 방법을 알아봅니다. SAS 토큰은 Azure 스토리지 계정의 리소스에 대한 안전한 위임된 액세스를 제공합니다.

## <a name="create-your-sas-tokens-with-azure-storage-explorer"></a>Azure Storage Explorer를 사용하여 SAS 토큰 만들기

### <a name="prerequisites"></a>필수 조건

* Windows, macOS 또는 Linux 개발 환경에 [**Azure Storage Explorer**](../../../vs-azure-tools-storage-manage-with-storage-explorer.md) 앱이 설치되어 있어야 합니다. Azure Storage Explorer는 Azure 클라우드 스토리지 리소스를 쉽게 관리할 수 있도록 지원하는 무료 도구입니다.
* Azure Storage Explorer 앱이 설치된 이후 문서 번역에 사용하는 [스토리지 계정에 연결](../../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#connect-to-a-storage-account-or-service)합니다.

### <a name="create-your-tokens"></a>토큰 만들기

### <a name="sas-tokens-for-containers"></a>[컨테이너에 대한 SAS 토큰](#tab/Containers)

1. 로컬 컴퓨터에서 Azure Storage Explorer 앱을 열고 연결된 **스토리지 계정** 으로 이동합니다.
1. 스토리지 계정 노드를 확장하고 **BLOB 컨테이너** 를 선택합니다.
1. BLOB 컨테이너 노드를 확장하고 스토리지 **컨테이너** 노드를 마우스 오른쪽 단추로 클릭하거나 옵션 메뉴를 표시합니다.
1. 옵션 메뉴에서 **공유 액세스 서명 가져오기...** 를 선택합니다.
1. **공유 액세스 서명** 창에서 다음을 선택합니다.
    * **액세스 정책**(기본값은 없음)을 선택합니다.
    * 서명된 키 **시작** 및 **만료** 날짜와 시간을 지정합니다. 생성된 후에는 SAS를 철회할 수 없으므로 짧은 수명이 권장됩니다.
    * 시작 및 만료 날짜와 시간에 대한 **표준 시간대**(기본값은 로컬)를 선택합니다.
    * 적절한 확인란을 선택하거나 선택 취소하여 컨테이너 **권한** 을 정의합니다.
    * 검토하고 **만들기** 를 선택합니다.

1. **컨테이너** 이름, **URI** 및 컨테이너에 대한 **쿼리 문자열** 과 함께 새 창이 표시됩니다.  
1. **한 번만 표시되며 창을 닫은 후에는 검색할 수 없으므로 컨테이너, URI 및 쿼리 문자열 값을 복사하여 안전한 위치에 붙여넣습니다.**
1. SAS URL을 구성하려면 스토리지 서비스의 URL에 SAS 토큰(URI)을 추가합니다.

### <a name="sas-tokens-for-blobs"></a>[BLOB에 대한 SAS 토큰](#tab/blobs)

1. 로컬 컴퓨터에서 Azure Storage Explorer 앱을 열고 연결된 **스토리지 계정** 으로 이동합니다.
1. 스토리지 노드를 확장하고 **BLOB 컨테이너** 를 선택합니다.
1. BLOB 컨테이너 노드를 확장하고 기본 창에 콘텐츠를 표시할 **컨테이너** 노드를 선택합니다.
1. SAS 액세스를 위임하려는 BLOB를 선택하고 마우스 오른쪽 단추를 클릭하여 옵션 메뉴를 표시합니다.
1. 옵션 메뉴에서 **공유 액세스 서명 가져오기...** 를 선택합니다.
1. **공유 액세스 서명** 창에서 다음을 선택합니다.
    * **액세스 정책**(기본값은 없음)을 선택합니다.
    * 서명된 키 **시작** 및 **만료** 날짜와 시간을 지정합니다. 생성된 후에는 SAS를 철회할 수 없으므로 짧은 수명이 권장됩니다.
    * 시작 및 만료 날짜와 시간에 대한 **표준 시간대**(기본값은 로컬)를 선택합니다.
    * 적절한 확인란을 선택하거나 선택 취소하여 컨테이너 **권한** 을 정의합니다.
    * 검토하고 **만들기** 를 선택합니다.
1. **BLOB** 이름, **URI** 및 BLOB에 대한 **쿼리 문자열** 과 함께 새 창이 표시됩니다.  
1. **한 번만 표시되며 창을 닫은 후에는 검색할 수 없으므로 BLOB, URI 및 쿼리 문자열 값을 복사하여 안전한 위치에 붙여넣습니다.**
1. SAS URL을 구성하려면 스토리지 서비스의 URL에 SAS 토큰(URI)을 추가합니다.

---

## <a name="create-sas-tokens-for-blobs-in-the-azure-portal"></a>Azure Portal에서 BLOB에 대한 SAS 토큰 만들기

<!-- markdownlint-disable MD024 -->
### <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음이 필요합니다.

* 활성 [**Azure 계정**](https://azure.microsoft.com/free/cognitive-services/).  계정이 없는 경우 [**체험 계정을 만들 수 있습니다**](https://azure.microsoft.com/free/).
* [**Translator**](https://ms.portal.azure.com/#create/Microsoft) 서비스 리소스(Cognitive Services 다중 서비스 리소스 **아님**).  [새 Azure 리소스 만들기](../../cognitive-services-apis-create-account.md#create-a-new-azure-cognitive-services-resource)를 참조하세요.  
* [**Azure Blob Storage 계정**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) 스토리지 계정 내에서 BLOB 데이터를 저장하고 구성하는 컨테이너를 만듭니다.

### <a name="create-your-tokens"></a>토큰 만들기

[Azure Portal](https://ms.portal.azure.com/#home)로 이동하고 다음과 같이 이동합니다.  

 **스토리지 계정** → **컨테이너** → **내 컨테이너** → **내 BLOB**

1. 페이지 상단 근처의 메뉴에서 **SAS 생성** 을 선택합니다.

1. **서명 방법** → **사용자 위임 키** 를 선택합니다.

1. 적절한 확인란을 선택하거나 선택 취소하여 **권한** 을 정의합니다.

1. 서명된 키 **시작** 및 **만료** 시간을 지정합니다.

1. **허용되는 IP 주소** 필드는 선택 사항으로 요청을 수락할 IP 주소 또는 IP 주소 범위를 지정합니다. 요청 IP 주소가 SAS 토큰에 지정된 IP 주소 또는 주소 범위와 일치하지 않으면 권한이 부여되지 않습니다.

1. **허용되는 프로토콜** 필드는 선택 사항으로 SAS로 만든 요청에 대해 허용되는 프로토콜을 지정합니다. 기본값은 HTTPS입니다.

1. 검토 후 **SAS 토큰 및 URL 생성** 을 선택합니다.

1. **BLOB SAS 토큰** 쿼리 문자열 및 **BLOB SAS URL** 이 창의 아래쪽 영역에 표시됩니다.  

1. **한 번만 표시되며 창을 닫은 후에는 검색할 수 없으므로 BLOB SAS 토큰 및 URL 값을 복사하여 안전한 위치에 붙여넣습니다.**

1. SAS URL을 구성하려면 스토리지 서비스의 URL에 SAS 토큰(URI)을 추가합니다.

## <a name="learn-more"></a>자세한 정보

* [프로그래밍 방식으로 BLOB 또는 컨테이너에 대한 SAS 토큰 만들기](../../../storage/blobs/sas-service-create.md)
* [디렉터리, 컨테이너 또는 BLOB에 대한 권한](/rest/api/storageservices/create-service-sas#permissions-for-a-directory-container-or-blob)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [문서 번역 시작](get-started-with-document-translation.md)
>
>
