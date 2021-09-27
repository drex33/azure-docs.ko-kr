---
title: 고정적 웹 사이트를 Azure CDN와 통합 - Azure Storage
description: Azure Storage 계정에서 Azure Content Delivery Network(CDN)를 사용하여 고정적 웹 사이트 콘텐츠를 캐시하는 방법을 알아봅니다.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.author: normesta
ms.date: 04/07/2020
ms.openlocfilehash: fcc79c6b1828d4feb33a73f04e98f92abbf7384e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597021"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>Azure CDN와 고정적 웹 사이트 통합

[Azure Content Delivery Network(CDN)](../../cdn/cdn-overview.md)를 사용하도록 설정하여 Azure Storage 계정에 호스팅된 [고정적 웹 사이트](storage-blob-static-website.md)에서 콘텐츠를 캐시할 수 있습니다. Azure CDN를 사용하여 고정적 웹 사이트의 사용자 지정 도메인 엔드포인트를 구성하고, 사용자 지정 TLS/SSL 인증서를 프로비전하고, 사용자 지정 다시 쓰기 규칙을 구성할 수 있습니다. Azure CDN을 구성하면 추가 비용이 발생하지만 전세계 어디에서나 웹 사이트에 대해 일관되게 낮은 대기 시간을 제공합니다. 또한 Azure CDN은 고유한 인증서를 사용하여 TLS 암호화를 제공합니다.

Azure CDN 가격 책정에 대한 정보는 [Azure CDN 가격 책정](https://azure.microsoft.com/pricing/details/cdn/)을 참조하세요.

## <a name="enable-azure-cdn-for-your-static-website"></a>고정적 웹 사이트에 Azure CDN 사용

스토리지 계정에서 직접 고정적 웹 사이트에 대해 Azure CDN을 사용하도록 설정할 수 있습니다. [대형 파일 다운로드 최적화](../../cdn/cdn-optimization-overview.md#large-file-download)처럼 CDN 엔드포인트에 대한 고급 구성 설정을 지정하려면 [Azure CDN 확장](../../cdn/cdn-create-new-endpoint.md)을 대신 사용하여 CDN 프로필 및 엔드포인트를 만들면 됩니다.

1. Azure Portal에서 스토리지 계정을 찾아 계정 개요를 표시합니다.

1. **보안 + 네트워킹** 메뉴에서 **Azure CDN** 을 선택하여 **Azure CDN** 페이지를 엽니다.

    ![CDN 엔드포인트 만들기](media/storage-blob-static-website-custom-domain/cdn-storage-new.png)

1. **CDN 프로필** 섹션에서 새 CDN 프로필을 만들지 또는 기존 계정을 사용할지 지정합니다. CDN 프로필은 가격 책정 계층 및 공급자를 공유 하는 CDN 엔드포인트 컬렉션입니다. 그 후 구독 내에서 고유한 CDN의 이름을 입력합니다.

1. CDN 엔드포인트에 대한 가격 책정 계층을 지정합니다. 가격 책정에 대해 자세히 알아보려면 [Content Delivery Network 가격 책정](https://azure.microsoft.com/pricing/details/cdn/)을 참조하세요. 각 계층에서 사용 가능한 기능에 대한 자세한 내용은 [Azure CDN 제품 기능 비교](../../cdn/cdn-features.md)를 참조하세요.

1. **CDN 엔드포인트 이름** 필드에서 CDN 엔드포인트에 대한 이름을 지정합니다. CDN 엔드포인트는 Azure에서 고유해야 하며 엔드포인트 URL의 첫 부분을 제공합니다. 형식은 엔드포인트 이름이 고유한지 유효성을 검사합니다.

1. **기존 호스트 이름** 필드에서 고정적 웹 사이트 엔드포인트를 지정합니다.

   정적 웹 사이트 엔드포인트를 찾으려면 스토리지 계정에 대한 **정적 웹 사이트** 설정으로 이동합니다.  기본 엔드포인트를 복사하여 CDN 구성에 붙여넣습니다.

   > [!IMPORTANT]
   > URL에서 프로토콜 식별자(*예:* HTTPS)와 후행 슬래시를 제거해야 합니다. 예를 들어 고정적 웹 사이트 엔드포인트가 `https://mystorageaccount.z5.web.core.windows.net/`인 경우 **원본 호스트 이름** 필드에 `mystorageaccount.z5.web.core.windows.net`를 지정합니다.

   다음 이미지에서는 엔드포인트 구성의 예를 보여 줍니다.

   ![CDN 엔드포인트 구성 샘플을 보여 주는 스크린샷](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. **만들기** 를 선택하고 CDN이 프로비전하는 것을 기다립니다. 만든 엔드포인트는 엔드포인트 목록에 나타납니다. (양식에 오류가 있으면 해당 필드 옆에 느낌표가 표시됩니다.)

1. CDN 엔드포인트가 올바르게 구성되었는지 확인하려면 엔드포인트를 클릭하여 해당 설정으로 이동합니다. 스토리지 계정에 대한 CDN 개요에서 다음 이미지와 같이 엔드포인트 호스트 이름을 찾아 해당 엔드포인트로 이동합니다. CDN 엔드포인트의 형식은 `https://staticwebsitesamples.azureedge.net`과 비슷합니다.

    ![CDN 엔드포인트의 개요를 보여 주는 스크린샷](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

1. CDN 엔드포인트가 프로비전된 후 CDN 엔드포인트로 이동하면 이전에 고정적 웹 사이트에 업로드 한 index.html 파일의 내용이 표시됩니다.

1. CDN 엔드포인트에 대한 원본 설정을 검토하려면 CDN 엔드포인트에 대한 **설정** 섹션 아래에서 **원본** 으로 이동합니다. **원본 형식** 필드가 *사용자 지정 원본* 으로 설정되어 있고 **원본 호스트 이름** 필드에 정적 웹 사이트 엔드포인트가 표시됩니다.

    ![CDN 엔드포인트에 대한 원본 설정을 보여 주는 스크린샷](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="remove-content-from-azure-cdn"></a>Azure CDN에서 콘텐츠 제거

더 이상 Azure CDN에 개체를 캐시하지 않으려면 다음 단계 중 하나를 수행할 수 있습니다.

- 컨테이너를 공용 대신 프라이빗으로 설정합니다. 자세한 내용은 [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](./anonymous-read-access-configure.md)를 참조하세요.
- Azure Portal을 사용하여 CDN 엔드포인트를 사용하지 않도록 설정하거나 삭제합니다.
- 더 이상 개체 요청에 응답하지 않도록 호스티드 서비스를 수정합니다.

Azure CDN에 이미 캐시된 개체는 개체의 TTL(Time-to-Live) 기간이 만료되거나 엔드포인트가 [삭제](../../cdn/cdn-purge-endpoint.md)될 때까지 캐시된 상태로 유지됩니다. TTL(Time-to-Live)이 만료되면 Azure CDN에서 CDN 엔드포인트가 여전히 유효하며 개체에 익명으로 액세스할 수 있는지 확인합니다. 그렇지 않으면 개체가 더 이상 캐시되지 않습니다.

## <a name="next-steps"></a>다음 단계

(선택 사항) Azure CDN 엔드포인트에 사용자 지정 도메인 추가 [자습서: Azure CDN 엔드포인트에 사용자 지정 도메인 추가](../../cdn/cdn-map-content-to-custom-domain.md)를 참조하세요.
