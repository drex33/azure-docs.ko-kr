---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/07/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: f3d3f66f01ce3245fa56712d06867bc35a4114ff
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124778879"
---
다음 표에서는 Azure 범용 v1, v2, Blob 스토리지 및 블록 Blob 스토리지 계정의 기본 제한에 대해 설명합니다. *수신* 제한은 스토리지 계정에 보내는 모든 데이터를 나타냅니다. *송신* 제한은 스토리지 계정에서 받는 모든 데이터를 나타냅니다.

> [!NOTE]
> 더 높은 용량과 수신 한도를 요청할 수 있습니다. 한도 증가를 요청하려면 [Azure 지원](https://azure.microsoft.com/support/faq/)에 문의하세요.

| 리소스 | 제한 |
| --- | --- |
| 표준 및 프리미엄 스토리지 계정을 포함하는 구독당 지역별 스토리지 계정 수입니다.| 250 |
| 최대 스토리지 계정 용량 | 5PiB<sup>1</sup>|
| 스토리지 계정당 Blob 컨테이너, Blob, 파일 공유, 테이블, 큐, 엔터티 또는 메시지의 최대 수 | 제한 없음 |
| 스토리지 계정당 최대 요청 속도<sup>1</sup> | 초당 20,000회 요청 |
| 스토리지 계정당 최대 수신<sup>1</sup>(미국, 유럽 지역) | 10Gbps |
| 스토리지 계정당 최대 수신<sup>1</sup>(미국 및 유럽 이외의 지역) | RA-GRS/GRS를 사용하도록 설정한 경우 5Gbps, LRS/ZRS의 경우 10Gbps<sup>2</sup> |
| 범용 v2 및 Blob 스토리지 계정에 대한 최대 송신(모든 지역) | 50Gbps |
| 범용 v1 스토리지 계정에 대한 최대 송신(미국 지역) | RA-GRS/GRS를 사용하도록 설정한 경우 20Gbps, LRS/ZRS의 경우 30Gbps<sup>2</sup> |
| 범용 v1 스토리지 계정에 대한 최대 송신(미국 이외의 지역) | RA-GRS/GRS를 사용하도록 설정한 경우 10Gbps, LRS/ZRS의 경우 15Gbps<sup>2</sup> |
| 스토리지 계정당 최대 가상 네트워크 규칙 수 | 200 |
| 스토리지 계정당 최대 IP 주소 규칙 수 | 200 |
| 스토리지 계정당 최대 리소스 인스턴스 규칙 수 | 200 |
| 스토리지 계정당 최대 프라이빗 엔드포인트 수 | 200 |

<sup>1</sup> Azure Storage 표준 계정은 더 높은 용량 제한과 요청별 수신에 대한 더 높은 제한을 지원합니다. 계정 제한을 늘리려면 [Azure 지원](https://azure.microsoft.com/support/faq/)에 문의하세요.

<sup>2</sup> 스토리지 계정에서 지리적 중복 스토리지(RA-GRS) 또는 지리적 영역 중복 스토리지(RA-GZRS)를 사용하여 읽기 액세스를 사용하도록 설정한 경우 보조 위치의 송신 대상은 기본 위치의 송신 대상과 동일합니다. 자세한 내용은 [Azure Storage 복제](../articles/storage/common/storage-redundancy.md)를 참조하세요.

> [!NOTE]
> 대부분의 시나리오에서는 범용 v2 스토리지 계정을 사용하는 것이 좋습니다. 가동 중지 시간이 없고 데이터를 복사할 필요 없이 범용 v1 또는 Azure Blob 스토리지 계정을 범용 v2 계정으로 쉽게 업그레이드할 수 있습니다. 자세한 내용은 [범용 v2 스토리지 계정으로 업그레이드](../articles/storage/common/storage-account-upgrade.md)를 참조하세요.

모든 스토리지 계정은 만들어진 시기와 관계없이 플랫 네트워크 토폴로지에서 실행됩니다. Azure Storage 플랫 네트워크 아키텍처 및 확장성에 대한 자세한 내용은 [Microsoft Azure Storage: 강력한 일관성과 함께 항상 사용 가능한 클라우드 스토리지 서비스](/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets) 문서를 참조하세요.

