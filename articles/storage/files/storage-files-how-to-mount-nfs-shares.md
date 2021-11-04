---
title: Azure NFS 파일 공유 탑재(미리 보기) - Azure Files
description: 네트워크 파일 시스템 공유를 탑재하는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/25/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 04d3174900ce5aa788f3793b39fa4d6c06222001
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131036792"
---
# <a name="how-to-mount-an-nfs-file-share-preview"></a>NFS 파일 공유를 마운트하는 방법(미리 보기)

[Azure Files](storage-files-introduction.md)는 사용하기 쉬운 Microsoft 클라우드 파일 시스템입니다. Azure 파일 공유는 SMB(서버 메시지 블록 프로토콜) 또는 NFS(네트워크 파일 시스템) 프로토콜(미리 보기)을 사용하여 Linux 배포에 탑재할 수 있습니다. 이 문서에서는 NFS를 사용하여 탑재하는 방법을 설명합니다. SMB를 사용하여 탑재하는 방법은 [Linux에서 Azure Files 사용](storage-how-to-use-files-linux.md)을 참조하세요. 각 프로토콜에 대한 자세한 내용은 [Azure 파일 공유 프로토콜](storage-files-planning.md#available-protocols)을 참조하세요.

## <a name="limitations"></a>제한 사항

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>국가별 가용성

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>필수 구성 요소

- [NFS 공유를 만듭니다](storage-files-how-to-create-nfs-shares.md).

    > [!IMPORTANT]
    > NFS 공유는 신뢰할 수 있는 네트워크에서만 액세스할 수 있습니다. NFS 공유에 대한 연결은 다음 원본 중 하나에서 시작해야 합니다.

- 다음 네트워킹 솔루션 중 하나를 사용합니다.
    - [프라이빗 엔드포인트 만들기](storage-files-networking-endpoints.md#create-a-private-endpoint)(권장) 또는 [퍼블릭 엔드포인트에 대한 액세스 제한](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Azure Files에서 사용할 P2S(지점 및 사이트 간) VPN을 Linux에 구성](storage-files-configure-p2s-vpn-linux.md).
    - [Azure Files에서 사용할 사이트 간 VPN 구성](storage-files-configure-s2s-vpn.md).
    - [ExpressRoute](../../expressroute/expressroute-introduction.md) 구성.

## <a name="disable-secure-transfer"></a>보안 전송을 사용하지 않도록 설정

1. [Azure Portal](https://portal.azure.com/) 에 로그인 하 여 만든 NFS 공유를 포함 하는 저장소 계정에 액세스 합니다.
1. **Configuration(구성)** 을 선택합니다.
1. **보안 전송 필요** 로 **사용 안 함** 을 선택합니다.
1. **저장** 을 선택합니다.

    :::image type="content" source="media/storage-files-how-to-mount-nfs-shares/disable-secure-transfer.png" alt-text="보안 전송이 사용하지 않도록 설정된 스토리지 계정 구성 화면 스크린샷." lightbox="media/storage-files-how-to-mount-nfs-shares/disable-secure-transfer.png":::

## <a name="mount-an-nfs-share"></a>NFS 공유 탑재

1. 파일 공유를 만든 후에 해당 공유를 선택하고 **Linux에서 연결** 을 선택합니다.
1. 사용하려는 탑재 경로를 입력한 다음 스크립트를 복사합니다.
1. 클라이언트 연결하고, 제공된 탑재 스크립트를 사용합니다.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/mount-nfs-file-share-script.png" alt-text="파일 공유 연결 블레이드의 스크린샷.":::

이제 NFS 공유가 탑재되었습니다.

### <a name="validate-connectivity"></a>연결 유효성 검사

탑재에 실패한 경우 프라이빗 엔드포인트가 올바르게 설정되지 않았거나 액세스 가능하지 않은 상태일 수 있습니다. 연결을 확인하는 방법은 네트워킹 엔드포인트 문서의 [연결 확인](storage-files-networking-endpoints.md#verify-connectivity) 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Files 배포에 대한 계획](storage-files-planning.md) 문서에서 Azure Files에 대해 자세히 알아봅니다.
- 문제가 발생하면 [Azure NFS 파일 공유 문제 해결](storage-troubleshooting-files-nfs.md)을 참조하세요.