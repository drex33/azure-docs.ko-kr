---
title: Azure Storage에서 정적 웹 사이트 호스팅
description: 최신 웹 애플리케이션을 호스팅하는 비용 효율적이고 확장 가능한 솔루션을 제공하는 정적 웹 사이트 호스팅입니다.
author: normesta
ms.service: storage
ms.topic: how-to
ms.author: normesta
ms.reviewer: dineshm
ms.date: 09/04/2020
ms.subservice: blobs
ms.custom: devx-track-js
ms.openlocfilehash: ffab1f466de9352d1ea6784cbacb7cb9672ecaa7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467382"
---
# <a name="static-website-hosting-in-azure-storage"></a>Azure Storage에서 정적 웹 사이트 호스팅

*$web* 이라는 스토리지 컨테이너에서 직접 정적 콘텐츠(HTML, CSS, JavaScript 및 이미지 파일)를 사용할 수 있습니다. Azure Storage에서 콘텐츠를 호스팅하면 [Azure Functions](../../azure-functions/functions-overview.md) 및 기타 PaaS(Platform as a service) 서비스를 포함하는 서버리스 아키텍처를 사용할 수 있습니다. Azure Storage 고정적인 웹 사이트 호스팅은 웹 서버에서 콘텐츠를 렌더링하지 않아도 되는 경우에 적합한 옵션입니다.

[App Service Static Web Apps](https://azure.microsoft.com/services/app-service/static/)는 Azure Storage 고정적인 웹 사이트 호스팅의 훌륭한 대안이며 웹 서버에서 콘텐츠를 렌더링하지 않아도 되는 경우에도 적합합니다. App Service Static Web Apps는 GitHub 소스부터 전역 배포까지 완전 관리형 연속 통합 및 지속적인 업데이트(CI/CD) 워크플로를 제공합니다.

웹 서버에서 콘텐츠를 렌더링해야 하는 경우에는 [Azure App Service](https://azure.microsoft.com/services/app-service/)를 사용할 수 있습니다.

## <a name="setting-up-a-static-website"></a>정적 웹 사이트 설정

정적 웹 사이트 호스팅은 스토리지 계정에서 사용하도록 설정해야 하는 기능입니다.

정적 웹 사이트 호스팅을 사용하도록 설정하려면 기본 파일의 이름을 선택한 다음 필요에 따라 사용자 지정 404 페이지의 경로를 제공합니다. 계정에 **$web** 이라는 Blob 스토리지 컨테이너가 아직 없는 경우 하나 만듭니다. 이 컨테이너에 사이트의 파일을 추가합니다.

단계별 지침은 [Azure Storage에서 정적 웹 사이트 호스트](storage-blob-static-website-how-to.md)를 참조하세요.

![Azure Storage 정적 웹 사이트 메트릭](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

**$web** 컨테이너의 파일은 대/소문자를 구분하고 익명 액세스 요청을 통해 제공되며 읽기 작업을 통해서만 사용할 수 있습니다.

## <a name="uploading-content"></a>콘텐츠 업로드

다음 도구 중 하나를 사용하여 **$web** 컨테이너에 콘텐츠를 업로드할 수 있습니다.

> [!div class="checklist"]
> - [Azure CLI](storage-blob-static-website-how-to.md?tabs=azure-cli)
> - [Azure PowerShell 모듈](storage-blob-static-website-how-to.md?tabs=azure-powershell)
> - [AZCopy](../common/storage-use-azcopy-v10.md)
> - [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
> - [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> - [Visual Studio Code 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) 및 [Channel 9 비디오 데모](https://channel9.msdn.com/Shows/Docs-Azure/Deploy-static-website-to-Azure-from-Visual-Studio-Code/player)

## <a name="viewing-content"></a>콘텐츠 보기

사용자는 웹 사이트의 공용 URL을 사용하여 브라우저에서 사이트 콘텐츠를 볼 수 있습니다. Azure Portal, Azure CLI 또는 PowerShell을 사용하여 URL을 찾을 수 있습니다. [웹 사이트 URL 찾기](storage-blob-static-website-how-to.md#portal-find-url)를 참조하세요.

정적 웹 사이트 호스팅을 사용하도록 설정할 때 지정한 인덱스 문서는 사용자가 사이트를 열고 특정 파일을 지정하지 않을 때 표시됩니다(예: `https://contosoblobaccount.z22.web.core.windows.net`).

서버에서 404 오류를 반환하는 경우, 웹 사이트를 사용하도록 설정할 때 오류 문서를 지정하지 않았으면, 사용자에게 기본 404 페이지가 반환됩니다.

> [!NOTE]
> 고정적인 웹사이트에서는 [Azure Storage에 대한 CORS(원본 간 리소스 공유)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)가 지원되지 않습니다.

### <a name="secondary-endpoints"></a>보조 엔드포인트

[보조 지역의 중복성](../common/storage-redundancy.md#redundancy-in-a-secondary-region)을 설정한 경우 보조 엔드포인트를 사용하여 웹 사이트 콘텐츠에 액세스할 수도 있습니다. 데이터는 보조 지역에 비동기적으로 복제되므로 보조 엔드포인트에서 사용할 수 있는 파일이 기본 엔드포인트에서 사용할 수 있는 파일과 항상 동기화되지는 않습니다.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>웹 컨테이너의 공용 액세스 수준을 설정하는 경우의 영향

**$web** 컨테이너의 공용 액세스 수준을 수정할 수 있습니다. 하지만 이러한 파일은 익명 액세스 요청을 통해 제공되므로 기본 정적 웹 사이트 엔드포인트에는 영향을 주지 않습니다. 이는 모든 파일에 대한 공용(읽기 전용) 액세스를 의미합니다.

다음 스크린샷은 Azure Portal의 공용 액세스 수준 설정을 보여 줍니다.

![포털에서 공용 액세스 수준을 설정하는 방법을 보여 주는 스크린샷](./media/anonymous-read-access-configure/configure-public-access-container.png)

기본 정적 웹 사이트 엔드포인트에는 영향을 주지 않지만 공용 액세스 수준 변경은 주 Blob 서비스 엔드포인트에는 영향을 줍니다.

예를 들어 **$web** 컨테이너의 공용 액세스 수준을 **개인(익명 액세스 없음)** 에서 **Blob(Blob만 익명 읽기 액세스)** 으로 변경하는 경우 기본 정적 웹 사이트 엔드포인트 `https://contosoblobaccount.z22.web.core.windows.net/index.html`에 대한 공용 액세스 수준은 변경되지 않습니다.

그러나 기본 Blob 서비스 엔드포인트 `https://contosoblobaccount.blob.core.windows.net/$web/index.html`에 대한 공용 액세스가 개인에서 공용으로 변경됩니다. 이제 사용자는 이들 두 엔드포인트 중 하나를 사용하여 해당 파일을 열 수 있습니다.

스토리지 계정에 대한 퍼블릭 액세스를 사용 중지해도 스토리지 계정에서 호스트되는 고정적인 웹 사이트에는 영향을 주지 않습니다. 자세한 내용은 [컨테이너 및 BLOB에 대한 익명 퍼블릭 읽기 권한 구성](anonymous-read-access-configure.md)을 참조하세요.

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>정적 웹 사이트 URL에 사용자 지정 도메인 매핑

사용자 지정 도메인을 통해 정적 웹 사이트를 사용할 수 있도록 설정할 수 있습니다.

Azure Storage에서 기본적으로 지원하므로 사용자 지정 도메인에 대해 HTTP 액세스를 사용하는 것이 더 쉽습니다. HTTPS를 사용하려면 Azure CDN을 사용해야 합니다. Azure Storage는 기본적으로 사용자 지정 도메인에 대해 HTTPS를 지원하지 않습니다. 단계별 지침은 [Azure Blob Storage 엔드포인트 사용자 지정 도메인 매핑](storage-custom-domain-name.md)을 참조하세요.

스토리지 계정이 HTTPS를 통한 [보안 전송을 요구](../common/storage-require-secure-transfer.md)하도록 구성된 경우 사용자는 HTTPS 엔드포인트를 사용해야 합니다.

> [!TIP]
> Azure에서 도메인을 호스트하는 것이 좋습니다. 자세한 내용은 [Azure DNS에서 도메인 호스트](../../dns/dns-delegate-domain-azure-dns.md)를 참조하세요.

## <a name="adding-http-headers"></a>HTTP 헤더 추가

정적 웹 사이트 기능의 일부로 헤더를 구성할 수 있는 방법은 없습니다. 그러나 Azure CDN을 사용하여 헤더를 추가하고 헤더 값을 추가(또는 덮어쓰기)할 수 있습니다. [Azure CDN에 대한 표준 규칙 엔진 참조](../../cdn/cdn-standard-rules-engine-reference.md)를 참조하세요.

캐싱을 제어하는 데 헤더를 사용하려는 경우 [캐싱 규칙을 사용하여 Azure CDN 캐싱 동작 제어](../../cdn/cdn-caching-rules.md)를 참조하세요.

## <a name="multi-region-website-hosting"></a>다중 지역 웹 사이트 호스팅

여러 지역에서 웹 사이트를 호스트하려면 지역 캐싱에 [Content Delivery Network](../../cdn/index.yml)를 사용하는 것이 좋습니다. 지역마다 다른 콘텐츠를 제공하려면 [Azure Front Door](../../frontdoor/index.yml)를 사용합니다. 장애 조치(failover) 기능도 제공됩니다. 사용자 지정 도메인을 사용하려면 [Azure Traffic Manager](../../traffic-manager/index.yml)를 사용하지 않는 것이 좋습니다. Azure Storage에서 사용자 지정 도메인 이름을 확인하는 방식으로 인해 문제가 발생할 수 있습니다.

## <a name="permissions"></a>사용 권한

정적 웹 사이트를 사용하도록 설정할 수 있는 권한은 Microsoft.Storage/storageAccounts/blobServices/write 또는 공유 키입니다.  이 액세스를 제공하는 기본 제공 역할에는 스토리지 계정 기여자가 포함됩니다.

## <a name="pricing"></a>가격 책정

정적 웹 사이트 호스팅은 무료로 사용할 수 있습니다. 사이트에서 활용하는 Blob 스토리지 및 운영 비용에 대해서만 요금이 청구됩니다. Azure Blob Storage에 대한 가격의 자세한 내용은 [Azure Blob Storage 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/blobs/)를 참조하세요.

## <a name="metrics"></a>메트릭

정적 웹 사이트 페이지에서 메트릭을 사용할 수 있습니다. 메트릭을 사용하도록 설정하면 **$web** 컨테이너의 파일에 대한 트래픽 통계가 메트릭 대시보드에 보고됩니다.

정적 웹 사이트 페이지에서 메트릭을 사용하도록 설정하려면 [정적 웹 사이트 페이지에서 메트릭 사용](storage-blob-static-website-how-to.md#metrics)을 참조하세요.

## <a name="feature-support"></a>기능 지원

이 표에서는 사용자 계정에서 이 기능이 지원되는 방법과 특정 기능을 활성화할 때 지원에 미치는 영향을 보여 줍니다.

| Storage 계정 유형                | Blob Storage(기본 지원)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| 표준 범용 v2 | ![예](../media/icons/yes-icon.png) |![예](../media/icons/yes-icon.png)              | ![예](../media/icons/yes-icon.png) |
| Premium 블록 Blob          | ![예](../media/icons/yes-icon.png)|![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) |

<sup>1</sup> Data Lake Storage Gen2와 NFS(네트워크 파일 시스템) 3.0 프로토콜 모두에는 계층 구조 네임스페이스를 사용하는 스토리지 계정이 필요합니다.

## <a name="faq"></a>FAQ

##### <a name="does-the-azure-storage-firewall-work-with-a-static-website"></a>Azure Storage 방화벽이 정적 웹 사이트에서 작동하나요?

예. IP 기반 및 VNET 방화벽을 포함한 스토리지 계정 [네트워크 보안 규칙](../common/storage-network-security.md)은 정적 웹 사이트 엔드포인트에 대해 지원되며 웹 사이트를 보호하는 데 사용될 수 있습니다.

##### <a name="do-static-websites-support-azure-active-directory-azure-ad"></a>정적 웹 사이트에서 Azure AD(Azure Active Directory)를 지원하나요?

아니요. 정적 웹 사이트는 **$web** 컨테이너의 파일에 대한 익명 퍼블릭 읽기 권한만 지원합니다.

##### <a name="how-do-i-use-a-custom-domain-with-a-static-website"></a>정적 웹 사이트에서 사용자 지정 도메인을 사용하려면 어떻게 해야 하나요?

[Azure CDN(Azure Content Delivery Network)](./storage-custom-domain-name.md#map-a-custom-domain-with-https-enabled)을 사용하여 정적 웹 사이트로 [사용자 지정 도메인](./static-website-content-delivery-network.md)을 구성할 수 있습니다. Azure CDN은 전 세계 어디에서나 웹 사이트에 대해 일관되게 낮은 대기 시간을 제공합니다.

##### <a name="how-do-i-use-a-custom-ssl-certificate-with-a-static-website"></a>정적 웹 사이트에서 사용자 지정 SSL 인증서를 사용하는 방법

[Azure CDN](./storage-custom-domain-name.md#map-a-custom-domain-with-https-enabled)을 사용하여 정적 웹 사이트에서 [사용자 지정 SSL](./static-website-content-delivery-network.md) 인증서를 구성할 수 있습니다. Azure CDN은 전 세계 어디에서나 웹 사이트에 대해 일관되게 낮은 대기 시간을 제공합니다.

##### <a name="how-do-i-add-custom-headers-and-rules-with-a-static-website"></a>정적 웹 사이트에서 사용자 지정 헤더 및 규칙을 추가하는 방법

[Azure CDN - Verizon Premium](../../cdn/cdn-verizon-premium-rules-engine.md)을 사용하여 정적 웹 사이트의 호스트 헤더를 구성할 수 있습니다. [여기](https://feedback.azure.com/d365community/idea/694b08ef-3525-ec11-b6e6-000d3a4f0f84)에서 의견을 보내 주시기 바랍니다.

##### <a name="why-am-i-getting-an-http-404-error-from-a-static-website"></a>정적 웹 사이트에서 HTTP 404 오류가 발생하는 이유는 무엇인가요?

대소문자를 틀리게 사용하여 파일 이름을 참조하는 경우 발생할 수 있습니다. 예를 들어 `index.html` 대신 `Index.html`을 사용합니다. 정적 웹 사이트의 URL에서 파일 이름과 확장명은 HTTP를 통해 제공되더라도 대/소문자를 구분합니다. Azure CDN 엔드포인트가 아직 프로비저닝되지 않은 경우에도 발생할 수 있습니다. 전파가 완료될 때까지 새 Azure CDN을 프로비저닝한 후 최대 90분 동안 기다립니다.

##### <a name="why-isnt-the-root-directory-of-the-website-not-redirecting-to-the-default-index-page"></a>웹 사이트의 루트 디렉터리가 기본 인덱스 페이지로 리디렉션되지 않는 이유는 무엇인가요?

Azure Portal에서 계정의 정적 웹 사이트 구성 페이지를 열고 **인덱스 문서 이름** 필드에 설정된 이름과 확장명을 찾습니다. 이 이름이 스토리지 계정의 **$web** 컨테이너에 있는 파일 이름과 똑같은지 확인합니다. 정적 웹 사이트의 URL에서 파일 이름과 확장명은 HTTP를 통해 제공되더라도 대/소문자를 구분합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Storage에서 정적 웹 사이트 호스트](storage-blob-static-website-how-to.md)
- [Azure Blob Storage 엔드포인트에 사용자 지정 도메인 매핑](storage-custom-domain-name.md)
- [Azure Functions](../../azure-functions/functions-overview.md)
- [Azure App Service](../../app-service/overview.md)
- [첫 번째 서버 없는 웹앱 빌드](/azure/functions/tutorial-static-website-serverless-api-with-database)
- [자습서: Azure DNS에서 도메인 호스트](../../dns/dns-delegate-domain-azure-dns.md)
