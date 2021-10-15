---
title: 네트워크 라우팅 기본 설정
titleSuffix: Azure Storage
description: 네트워크 라우팅 기본 설정을 사용하면 네트워크 트래픽이 인터넷을 통해 클라이언트에서 계정으로 라우팅되는 방식을 지정할 수 있습니다.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.custom: references_regions
ms.openlocfilehash: 0a80fe8bc2cf2cfa86b5b8b1a9c78684d718e68f
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130038368"
---
# <a name="network-routing-preference-for-azure-storage"></a>Azure Storage에 대한 네트워크 라우팅 기본 설정

Azure 스토리지 계정의 네트워크 [라우팅 기본 설정](../../virtual-network/routing-preference-overview.md)을 구성하여 네트워크 트래픽이 인터넷을 통해 클라이언트에서 계정으로 라우팅되는 방식을 지정할 수 있습니다. 기본적으로 인터넷에서 들어오는 트래픽은 [Microsoft 글로벌 네트워크](../../networking/microsoft-global-network.md)를 통해 스토리지 계정의 퍼블릭 엔드포인트로 라우팅됩니다. Azure Storage는 스토리지 계정에 트래픽이 라우팅되는 방식을 구성하기 위한 추가 옵션을 제공합니다.

라우팅 기본 설정을 구성하면 프리미엄 네트워크 성능 또는 비용에 맞게 트래픽을 자유롭게 최적화할 수 있습니다. 라우팅 기본 설정을 구성하는 경우 기본적으로 스토리지 계정의 퍼블릭 엔드포인트로 트래픽을 전송하는 방법을 지정합니다. 스토리지 계정의 경로별 엔드포인트를 게시할 수도 있습니다.

> [!NOTE]
> 이 기능은 ZRS(영역 중복 스토리지)를 사용하도록 구성된 Premium Performance Storage 계정 또는 계정에서 지원되지 않습니다.

## <a name="microsoft-global-network-versus-internet-routing"></a>Microsoft 글로벌 네트워크와 인터넷 라우팅 비교

기본적으로 Azure 환경 외부의 클라이언트는 Microsoft 글로벌 네트워크를 통해 스토리지 계정에 액세스합니다. Microsoft 글로벌 네트워크는 대기 시간이 짧은 경로를 선택하여 높은 안정성과 프리미엄 네트워크 성능을 제공하도록 최적화되었습니다. 인바운드 및 아웃바운드 트래픽은 모두 클라이언트에 가장 가까운 POP(상태 지점)를 통해 라우팅됩니다. 이 기본 라우팅 구성은 스토리지 계정에서 들어오고 나가는 트래픽의 이동 경로 중 상당수가 Microsoft 글로벌 네트워크를 통과하도록 하므로 네트워크 성능이 극대화됩니다.

스토리지 계정의 라우팅 구성을 변경하여 인바운드 및 아웃바운드 트래픽이 모두 스토리지 계정과 가장 가까운 POP를 통해 Azure 환경 외부의 클라이언트 간에 라우팅되도록 할 수 있습니다. 이 경로는 트래픽의 Microsoft 글로벌 네트워크 통과를 최소화하여 가장 빠른 기회에 전송 ISP에 전달합니다. 이 라우팅 구성을 활용하면 네트워킹 비용이 절감됩니다.

다음 다이어그램에서는 각 라우팅 기본 설정의 클라이언트와 스토리지 계정 간 트래픽 흐름을 보여 줍니다.

![Azure Storage의 라우팅 옵션 개요](media/network-routing-preference/routing-options-diagram.png)

Azure의 라우팅 기본 설정에 대한 자세한 내용은 [라우팅 기본 설정이란?](../../virtual-network/routing-preference-overview.md)을 참조하세요.

## <a name="routing-configuration"></a>라우팅 구성

라우팅 기본 설정 및 경로별 엔드포인트를 구성하는 방법을 보여주는 단계별 지침은 [Azure Storage의 네트워크 라우팅 기본 설정 구성](configure-network-routing-preference.md)을 참조하세요.

스토리지 계정의 퍼블릭 엔드포인트에 대한 기본 라우팅 기본 설정으로 Microsoft 글로벌 네트워크 또는 인터넷 라우팅을 선택할 수 있습니다. 기본 라우팅 기본 설정은 Azure 외부 클라이언트에서 들어오는 모든 트래픽에 적용되며, Azure Data Lake Storage Gen2, Blob Storage, Azure Files 및 정적 웹 사이트의 엔드포인트에 영향을 줍니다. Azure Queues 또는 Azure Tables에 대한 라우팅 기본 설정 구성은 지원되지 않습니다.

스토리지 계정의 경로별 엔드포인트를 게시할 수도 있습니다. 경로별 엔드포인트를 게시하는 경우 Azure Storage는 원하는 경로를 통해 트래픽을 라우팅하는 스토리지 계정의 새 퍼블릭 엔드포인트를 만듭니다. 이러한 유연성 덕분에 기본 라우팅 기본 설정을 변경하지 않고 특정 경로를 통해 스토리지 계정에 트래픽을 보낼 수 있습니다.

예를 들어 ‘StorageAccountA’의 인터넷 경로별 엔드포인트를 게시하면 스토리지 계정에 대해 다음 엔드포인트가 게시됩니다.

| 스토리지 서비스        | 경로별 엔드포인트                                  |
| :--------------------- | :------------------------------------------------------- |
| Blob service           | `StorageAccountA-internetrouting.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `StorageAccountA-internetrouting.dfs.core.windows.net`   |
| 파일 서비스           | `StorageAccountA-internetrouting.file.core.windows.net`  |
| 정적 웹 사이트        | `StorageAccountA-internetrouting.web.core.windows.net`   |

읽기 액세스 지역 중복 스토리지(RA-GRS) 또는 읽기 액세스 지역 영역 중복 스토리지(RA-GZRS) 계정이 있는 경우 경로별 엔드포인트를 게시하면 읽기 액세스를 위해 보조 지역에 해당 엔드포인트가 자동으로 생성됩니다.

| 스토리지 서비스        | 경로별 읽기 전용 보조 엔드포인트                        |
| :--------------------- | :----------------------------------------------------------------- |
| Blob service           | `StorageAccountA-internetrouting-secondary.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `StorageAccountA-internetrouting-secondary.dfs.core.windows.net`   |
| 파일 서비스           | `StorageAccountA-internetrouting-secondary.file.core.windows.net`  |
| 정적 웹 사이트        | `StorageAccountA-internetrouting-secondary.web.core.windows.net`   |

게시된 경로별 엔드포인트의 연결 문자열은 [Azure Portal](https://portal.azure.com)을 통해 복사할 수 있습니다. 이러한 연결 문자열은 기존의 모든 Azure Storage SDK 및 API를 사용한 공유 키 권한 부여에 사용할 수 있습니다.

## <a name="regional-availability"></a>국가별 가용성

Azure Storage의 라우팅 기본 설정은 다음 지역에서 제공됩니다.

- 미국 중부 
- 미국 중부 EUAP
- 미국 동부 
- 미국 동부 2
- 미국 동부 2 
- 미국 동부 2 EUAP
- 미국 중남부
- 미국 중서부
- 미국 서부 
- 미국 서부 2 
- 프랑스 중부 
- 프랑스 남부 
- 독일 북부 
- 독일 중서부 
- 미국 중북부
- 북유럽 
- 노르웨이 동부 
- 스위스 북부
- 스위스 서부
- 영국 남부 
- 영국 서부 
- 서유럽 
- 아랍에미리트 중부
- 동아시아 
- 동남아시아 
- 일본 동부 
- 일본 서부 
- 인도 서부
- 오스트레일리아 동부 
- 오스트레일리아 남동부 

다음과 같은 알려진 문제는 Azure Storage의 라우팅 기본 설정에 영향을 줍니다.

- Microsoft 글로벌 네트워크의 경로별 엔드포인트에 대한 액세스 요청이 HTTP 오류 404 또는 이와 동등한 오류와 함께 실패합니다. Microsoft 글로벌 네트워크를 통한 라우팅이 퍼블릭 엔드포인트에 대한 기본 라우팅 설정으로 지정된 경우에는 올바르게 작동합니다.

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

가격 책정 및 대금 청구에 대한 자세한 내용은 [라우팅 기본 설정이란?](../../virtual-network/routing-preference-overview.md#pricing)의 **가격 책정** 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [라우팅 기본 설정이란?](../../virtual-network/routing-preference-overview.md)
- [네트워크 라우팅 기본 설정 구성](configure-network-routing-preference.md)
- [Azure Storage 방화벽 및 가상 네트워크 구성](storage-network-security.md)
- [Blob Storage에 대한 보안 권장 사항](../blobs/security-recommendations.md)
