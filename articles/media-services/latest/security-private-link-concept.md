---
title: Azure Media Services 프라이빗 링크 사용 개요
description: 이 문서에서는 Azure Media Services 프라이빗 링크 사용에 대한 개요를 제공합니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: inhenkel
ms.openlocfilehash: 10d27003cfa5300e097c2328deb14c0d9c1f7e93
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053535"
---
# <a name="overview-of-using-azure-private-link-with-azure-media-services"></a>Azure Media Services Azure Private Link 사용 개요

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서에서는 Azure Media Services 프라이빗 링크 사용에 대한 개요를 제공합니다.

## <a name="when-to-use-private-link-with-media-services"></a>Media Services Private Link 사용하는 경우

Private Link 사용하면 프라이빗 네트워크에서 Media Services 액세스할 수 있습니다. Media Services 제공하는 네트워크 액세스 제어와 함께 사용하는 경우 프라이빗 링크를 사용하면 엔드포인트를 공용 인터넷에 노출하지 않고도 Media Services 사용할 수 있습니다.

## <a name="azure-private-endpoint-and-azure-private-link"></a>Azure 프라이빗 엔드포인트 및 Azure Private Link

[Azure 프라이빗 엔드포인트는](/private-link/private-endpoint-overview) 가상 네트워크의 개인 IP 주소를 사용하는 네트워크 인터페이스입니다.  이 네트워크 인터페이스는 Azure Private Link 통해 개인적이고 안전하게 서비스에 연결합니다.

Media Services 엔드포인트는 프라이빗 엔드포인트를 사용하여 가상 네트워크에서 액세스할 수 있습니다. 프라이빗 엔드포인트는 피어된 가상 네트워크 또는 Express Route 또는 VPN을 사용하여 가상 네트워크에 연결된 다른 네트워크에서 액세스할 수도 있습니다.

[Azure Private Links를](/private-link/) 사용하면 공용 인터넷에 노출하지 않고 가상 네트워크의 Media Services 프라이빗 엔드포인트에 액세스할 수 있습니다. Microsoft 백본 네트워크를 통해 트래픽을 라우팅합니다.

## <a name="restricting-access"></a>액세스 제한

> [!Important]
> 프라이빗 엔드포인트를 만들면 해당 엔드포인트에 대한 인터넷 액세스를 암시적으로 사용하지 **않도록 설정되지 않습니다.**

Media Services 계정의 엔드포인트에 대한 인터넷 액세스는 다음 두 가지 방법 중 하나로 제한할 수 있습니다.

- Media Services 계정 내의 모든 리소스에 대한 액세스 제한
- IP 허용 목록을 사용하여 각 리소스에 대해 개별적으로 액세스를 제한합니다.

## <a name="media-services-endpoints"></a>Media Services 엔드포인트

| 엔드포인트                    | 설명                                                               | 프라이빗 링크 지원 | 인터넷 액세스 제어 |
| --------------------------- | ------------------------------------------------------------------------- | --------------------- | ----------------------- |
| 스트리밍 엔드포인트          | 비디오를 스트리밍하고 미디어를 HLS 및 DASH로 포맷하기 위한 원본 서버 | Yes                   | IP 허용 목록            |
| CDN 있는 스트리밍 엔드포인트 | 많은 뷰어로 미디어 스트리밍                                              | 아니요                    | CDN 관리          |
| 키 배달                | 미디어 뷰어에게 미디어 콘텐츠 키 및 DRM 라이선스 제공             | Yes                   | IP 허용 목록            |
| 라이브 이벤트                  | 라이브 스트리밍을 위한 미디어 콘텐츠 검색                                  | Yes                   | IP 허용 목록            |

> [!NOTE]
> 2020-05-01 이전의 API 버전으로 만든 Media Services 계정에는 레거시 RESTv2 API 엔드포인트에 대한 엔드포인트도 있습니다(사용 중단 보류 중).  이 엔드포인트는 프라이빗 링크를 지원하지 않습니다.

## <a name="other-private-link-enabled-azure-services"></a>기타 Private Link 사용하도록 설정된 Azure 서비스

| 서비스                | Media Services 통합                      | 프라이빗 링크 설명서 |
| ---------------------- | ----------------------------------------------- | -------------------------- |
| Azure Storage          | 미디어를 저장하는 데 사용                             | [Azure Storage에 프라이빗 엔드포인트 사용](/storage/common/storage-private-endpoints) |
| Azure Key Vault        | [고객 관리형 키를](security-customer-managed-keys-portal-tutorial.md) 저장하는 데 사용             | [Azure Key Vault 네트워킹 설정 구성](/key-vault/general/how-to-azure-key-vault-network-security) |
| Azure Resource Manager | Media Services API에 대한 액세스 제공          | [REST API를 사용하여 Azure 리소스를 관리하기 위한 프라이빗 링크 만들기](/azure-resource-manager/management/create-private-link-access-rest) |
| Event Grid             | [Media Services 이벤트에 대한 알림을](./monitoring/job-state-events-cli-how-to.md) 제공합니다. | [Azure Event Grid 토픽 또는 도메인의 프라이빗 엔드포인트 구성](/event-grid/configure-private-endpoints)  |

## <a name="private-endpoints-are-created-on-the-media-services-account"></a>프라이빗 엔드포인트는 Media Services 계정에 생성됩니다.

키 배달, 스트리밍 엔드포인트 및 라이브 이벤트에 대한 프라이빗 엔드포인트는 개별적으로 만드는 대신 Media Services 계정에 만들어집니다.

Media Services 프라이빗 엔드포인트 리소스를 만들 때 Media Services 계정의 각 스트리밍 엔드포인트 또는 라이브 이벤트에 대한 개인 IP 주소가 만들어집니다. 예를 들어 스트리밍 엔드포인트를 두 개 시작한 경우 두 스트리밍 엔드포인트를 가상 네트워크에 연결하기 위해 단일 프라이빗 엔드포인트를 만들어야 합니다. 리소스는 동시에 여러 가상 네트워크에 연결할 수 있습니다.

계정 내의 모든 리소스에 대해 또는 각 리소스에 대해 개별적으로 Media Services 계정에 대한 인터넷 액세스를 제한해야 합니다.

## <a name="private-link-pricing"></a>Private Link 가격 책정
가격 책정에 대한 자세한 내용은 [Azure Private Link 가격 책정을 참조하세요.](https://azure.microsoft.com/pricing/details/private-link)

## <a name="private-link-how-tos-and-faqs"></a>Private Link 방법 및 FAQ

- [Azure 리소스 관리 템플릿을 사용하여 Private Link Media Services 및 Storage 계정 만들기](security-private-link-arm-how-to.md)
- [스트리밍 엔드포인트에 대한 Private Link 만들기](security-private-link-streaming-endpoint-how-to.md)