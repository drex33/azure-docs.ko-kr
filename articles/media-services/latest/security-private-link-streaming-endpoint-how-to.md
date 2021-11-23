---
title: 스트리밍 끝점에 대 한 개인 링크 만들기
description: 이 문서에서는 스트리밍 끝점에 개인 링크를 사용 하는 방법을 보여 줍니다. 가상 네트워크와 스트리밍 끝점 간의 링크인 개인 끝점 리소스를 만듭니다. 이 배포는 가상 네트워크 내에 네트워크 인터페이스 IP 주소를 만듭니다. 개인 링크를 사용 하 여 개인 네트워크의 네트워크 인터페이스를 Media Services 계정의 스트리밍 끝점에 연결할 수 있습니다. 또한 개인 IP 주소를 전달 하는 DNS 영역을 만듭니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 10/22/2021
ms.author: inhenkel
ms.openlocfilehash: eeb81fa7814924c4bc85b2ae537ff9f722be01f0
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132937189"
---
# <a name="create-a-private-link-for-a-streaming-endpoint"></a>스트리밍 끝점에 대 한 개인 링크 만들기

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서에서는 스트리밍 끝점에 개인 링크를 사용 하는 방법을 보여 줍니다. [azure 리소스 그룹](/azure/azure-resource-manager/management/manage-resource-groups-portal), [Media Services 계정](account-create-how-to.md)및 [azure 가상 네트워크](/azure/virtual-network/quick-create-portal)를 만드는 방법을 이미 알고 있다고 가정 합니다.

가상 네트워크와 스트리밍 끝점 간의 링크인 개인 끝점 리소스를 만듭니다. 이 배포는 가상 네트워크 내에 네트워크 인터페이스 IP 주소를 만듭니다. 개인 링크를 사용 하 여 개인 네트워크의 네트워크 인터페이스를 Media Services 계정의 스트리밍 끝점에 연결할 수 있습니다. 또한 개인 IP 주소를 전달 하는 DNS 영역을 만듭니다.

이 연습을 위해 만든 가상 네트워크는 예제를 지원 하기 위한 것입니다.  프로덕션에 사용할 기존 가상 네트워크가 있다고 가정 합니다.

> [!NOTE]
> 단계를 따라 수행 하는 것 처럼 비슷한 용도로 쉽게 이해할 수 있도록 리소스 이름을 비슷하게 지정할 수 있습니다.  예를 들어 저장소 계정에 대 한 *Privatelink1stor* 관리 id의 *privatelink1mi* 입니다.

## <a name="create-a-resource-group-and-a-media-services-account"></a>리소스 그룹 및 Media Services 계정 만들기

1. Azure 리소스 그룹 만들기
1. Media Services 계정 만들기  기본 스트리밍 끝점은 계정을 만들 때 생성 됩니다. 설치 프로세스를 진행 하는 동안 관리 되는 Id를 만드는 작업이 필요 합니다.
1. 기본 설정을 사용 하 여 Azure 가상 네트워크를 만듭니다.

이제 가상 네트워크에는 아무 것도 없습니다. 인터넷 연결 스트리밍 끝점, 키 배달 및 라이브 이벤트를 포함 하는 인터넷 연결 끝점은 Media Services 계정에 있습니다.  다음 단계에서는 스트리밍 끝점을 비공개로 설정 합니다.

## <a name="start-the-streaming-endpoint"></a>스트리밍 엔드포인트 시작

1. 만든 Media Services 계정으로 이동 합니다.  
1. 메뉴에서 **스트리밍 끝점** 을 선택 합니다. 스트리밍 끝점 화면이 표시 됩니다.
1. Media Services 계정을 설정할 때 만든 기본 스트리밍 끝점을 선택 합니다.  기본 스트리밍 끝점 화면이 표시 됩니다.
1. **시작** 을 선택합니다. 시작 옵션이 표시 됩니다.
1. CDN 가격 책정 계층 드롭다운 목록에서 **없음** 을 선택 합니다.
1. **시작** 을 선택합니다.  스트리밍 끝점이 실행 되기 시작 합니다. 끝점이 인터넷에 연결 되어 있습니다.

## <a name="create-a-private-endpoint"></a>프라이빗 엔드포인트 만들기

1. Media Services 계정으로 다시 이동 합니다.
1. 메뉴에서 **네트워킹** 을 선택 합니다.
1. **개인 끝점 연결** 탭을 선택 합니다.  개인 끝점 연결 화면이 표시 됩니다.
1. **개인 끝점 추가를** 선택 합니다. 개인 끝점 만들기 화면이 표시 됩니다.
1. **이름** 필드에서 개인 끝점에 *privatelinkpe* 와 같은 이름을 지정 합니다.
1. **지역** 드롭다운 목록에서 *미국 서 부 2* 와 같은 지역을 선택 합니다.
1. 완료되면 **다음: 리소스** 를 선택합니다. 리소스 화면이 표시 됩니다.

## <a name="assign-the-private-endpoint-to-a-resource"></a>리소스에 개인 끝점 할당

1. **연결 방법** 라디오 단추에서 *내 디렉터리의 Azure 리소스에 커넥트* 라디오 단추를 선택 합니다.
1. **리소스 종류** 드롭다운 목록에서 *windowsazure.mediaservices/* 를 선택 합니다.
1. **리소스** 드롭다운 목록에서 사용자가 만든 Media Services 계정을 선택 합니다.
1. **대상 하위 리소스** 드롭다운 목록에서 사용자가 만든 스트리밍 끝점을 선택 합니다.

## <a name="deploy-the-private-endpoint-to-the-virtual-network"></a>가상 네트워크에 개인 끝점 배포

1. **가상 네트워크** 드롭다운 목록에서 사용자가 만든 가상 네트워크를 선택 합니다.
1. **서브넷** 드롭다운 목록에서 작업 하려는 서브넷을 선택 합니다.
1. 이 화면을 계속 진행 합니다.

## <a name="create-dns-zones-to-use-with-the-private-endpoint"></a>개인 끝점과 함께 사용할 DNS 영역을 만듭니다.

가상 네트워크 내에서 스트리밍 끝점을 사용 하려면 개인 DNS 영역을 만듭니다. 동일한 DNS 이름을 사용 하 고 스트리밍 끝점의 개인 IP 주소를 다시 가져올 수 있습니다.

1. 동일한 화면에서 **미디어-azure-net** 구성의 경우 **리소스 그룹** 드롭다운 목록에서 만든 리소스 그룹을 선택 합니다.
1. **Privatelink** 구성의 경우 **리소스 그룹** 드롭다운 목록에서 동일한 리소스 그룹을 선택 합니다.
1. 완료되면 **다음: 태그** 를 선택합니다. 리소스에 태그를 추가 하려면 여기에서 작업을 수행 합니다.
1. 완료되면 **다음: 리뷰 + 만들기** 를 클릭합니다. 검토 + 만들기 화면이 표시 됩니다.
1. 설정을 검토 하 고 올바른지 확인 합니다.
1. **만들기** 를 선택합니다. 개인 끝점 배포 화면이 나타납니다.

배포가 진행 되는 동안에는 [ARM (Azure Resource Manager) 템플릿도](/azure/azure-resource-manager/templates/overview)생성 됩니다. ARM 템플릿을 사용 하 여 배포를 자동화할 수 있습니다. 템플릿을 보려면 메뉴에서 **템플릿** 을 선택 합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 연습에서 만든 리소스를 사용 하지 않으려는 경우에는 리소스 그룹을 삭제 하기만 하면 됩니다. 리소스를 삭제 하지 않으면 해당 리소스에 대 한 요금이 청구 됩니다.
