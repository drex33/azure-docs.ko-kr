---
title: 스트리밍 엔드포인트에 대한 Private Link 만들기
description: 이 문서에서는 스트리밍 엔드포인트와 함께 프라이빗 링크를 사용하는 방법을 보여줍니다. 가상 네트워크와 스트리밍 엔드포인트 간의 링크인 프라이빗 엔드포인트 리소스를 만듭니다. 이 배포는 가상 네트워크 내에 네트워크 인터페이스 IP 주소를 만듭니다. 프라이빗 링크를 사용하면 프라이빗 네트워크의 네트워크 인터페이스를 Media Services 계정의 스트리밍 엔드포인트에 연결할 수 있습니다. 또한 개인 IP 주소를 전달하는 DNS 영역을 만듭니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 10/22/2021
ms.author: inhenkel
ms.openlocfilehash: 3175925fe8d5273ec5f9bb0220b439a01df5ef11
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102998"
---
# <a name="create-a-private-link-for-a-streaming-endpoint"></a>스트리밍 엔드포인트에 대한 Private Link 만들기

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서에서는 스트리밍 엔드포인트와 함께 프라이빗 링크를 사용하는 방법을 보여줍니다. [Azure 리소스 그룹,](/azure-resource-manager/management/manage-resource-groups-portal) [Media Services 계정](account-create-how-to.md)및 Azure 가상 [네트워크를](/virtual-network/quick-create-portal)만드는 방법을 이미 알고 있다고 가정합니다.

가상 네트워크와 스트리밍 엔드포인트 간의 링크인 프라이빗 엔드포인트 리소스를 만듭니다. 이 배포는 가상 네트워크 내에 네트워크 인터페이스 IP 주소를 만듭니다. 프라이빗 링크를 사용하면 프라이빗 네트워크의 네트워크 인터페이스를 Media Services 계정의 스트리밍 엔드포인트에 연결할 수 있습니다. 또한 개인 IP 주소를 전달하는 DNS 영역을 만듭니다.

이 워크스트를 위해 만든 가상 네트워크는 예제를 지원하기 위한 것입니다.  프로덕션에 사용할 기존 가상 네트워크가 있다고 가정합니다.

> [!NOTE]
> 단계를 수행하면서 비슷한 용도로 쉽게 이해할 수 있도록 리소스의 이름을 비슷하게 지정합니다.  예를 들어 스토리지 계정의 *경우 privatelink1stor,* 관리 ID의 *경우 privatelink1mi입니다.*

## <a name="create-a-resource-group-and-a-media-services-account"></a>리소스 그룹 및 Media Services 계정 만들기

1. Azure 리소스 그룹 만들기
1. Media Services 계정 만들기  계정을 만들 때 기본 스트리밍 엔드포인트가 만들어집니다. 설치 프로세스 중에 관리 ID를 만들어야 합니다.
1. 기본 설정을 사용하여 Azure 가상 네트워크를 만듭니다.

이 시점에서 가상 네트워크에는 Media Services 계정에 인터넷 연결 스트리밍 엔드포인트, 키 배달 및 라이브 이벤트를 포함하는 인터넷 연결 엔드포인트가 없습니다.  다음 단계에서는 스트리밍 엔드포인트를 프라이빗으로 만듭니다.

## <a name="start-the-streaming-endpoint"></a>스트리밍 엔드포인트 시작

1. 만든 Media Services 계정으로 이동합니다.  
1. 메뉴에서 **스트리밍 엔드포인트를** 선택합니다. 스트리밍 엔드포인트 화면이 나타납니다.
1. Media Services 계정을 설정할 때 만든 기본 스트리밍 엔드포인트를 선택합니다.  기본 스트리밍 엔드포인트 화면이 표시됩니다.
1. **시작** 을 선택합니다. 시작 옵션이 표시됩니다.
1. CDN 가격 책정 계층 드롭다운 목록에서 **없음을** 선택합니다.
1. **시작** 을 선택합니다.  스트리밍 엔드포인트 실행이 시작됩니다. 엔드포인트는 여전히 인터넷에 연결되어 있습니다.

## <a name="create-a-private-endpoint"></a>프라이빗 엔드포인트 만들기

1. Media Services 계정으로 다시 이동합니다.
1. 메뉴에서 **네트워킹을** 선택합니다.
1. 프라이빗 **엔드포인트 연결 탭을** 선택합니다.  프라이빗 엔드포인트 연결 화면이 표시됩니다.
1. **프라이빗 엔드포인트 추가를** 선택합니다. 프라이빗 엔드포인트 만들기 화면이 나타납니다.
1. **이름** 필드에서 프라이빗 엔드포인트에 *privatelinkpe* 와 같은 이름을 지정합니다.
1. **지역** 드롭다운 목록에서 *미국 서부 2와* 같은 지역을 선택합니다.
1. 완료되면 **다음: 리소스** 를 선택합니다. 리소스 화면이 표시됩니다.

## <a name="assign-the-private-endpoint-to-a-resource"></a>리소스에 프라이빗 엔드포인트 할당

1. 연결 **방법** 라디오 단추에서 내 디렉터리 라디오 *단추에서 Azure 리소스에* 대한 커넥트 선택합니다.
1. 리소스 **종류** 드롭다운 목록에서 *Microsoft.Media/mediaservices 를* 선택합니다.
1. **리소스** 드롭다운 목록에서 만든 Media Services 계정을 선택합니다.
1. 대상 **하위 리소스** 드롭다운 목록에서 만든 스트리밍 엔드포인트를 선택합니다.

## <a name="deploy-the-private-endpoint-to-the-virtual-network"></a>가상 네트워크에 프라이빗 엔드포인트 배포

1. 가상 **네트워크** 드롭다운 목록에서 만든 가상 네트워크를 선택합니다.
1. **서브넷** 드롭다운 목록에서 작업할 서브넷을 선택합니다.
1. 이 화면을 계속 진행합니다.

## <a name="create-dns-zones-to-use-with-the-private-endpoint"></a>프라이빗 엔드포인트와 함께 사용할 DNS 영역 만들기

가상 네트워크 내에서 스트리밍 엔드포인트를 사용하려면 프라이빗 DNS 영역을 만듭니다. 동일한 DNS 이름을 사용하고 스트리밍 엔드포인트의 개인 IP 주소를 다시 얻을 수 있습니다.

1. 동일한 화면의 **media-azure-net** 구성에 대해 리소스 그룹 드롭다운 목록에서 만든 **리소스 그룹을** 선택합니다.
1. **privatelink-media-azure-net** 구성의 경우 리소스 그룹 드롭다운 목록에서 동일한 **리소스 그룹을** 선택합니다.
1. 완료되면 **다음: 태그** 를 선택합니다. 리소스에 태그를 추가하려면 여기에서 태그를 추가합니다.
1. 완료되면 **다음: 리뷰 + 만들기** 를 클릭합니다. 검토 + 만들기 화면이 나타납니다.
1. 설정을 검토하고 올바른지 확인합니다.
1. **만들기** 를 선택합니다. 프라이빗 엔드포인트 배포 화면이 나타납니다.

배포가 진행되는 동안 [ARM(Azure Resource Manager) 템플릿도](/azure-resource-manager/templates/overview)만듭니다. ARM 템플릿을 사용하여 배포를 자동화할 수 있습니다. 템플릿을 보려면 메뉴에서 **템플릿을** 선택합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 연습에서 만든 리소스를 사용할 계획이 없는 경우 리소스 그룹을 삭제하기만 하면 됩니다. 리소스를 삭제하지 않으면 요금이 청구됩니다.