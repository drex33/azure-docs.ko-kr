---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/04/2021
ms.author: tamram
ms.custom: include file, references_regions
ms.openlocfilehash: 8a50cc2f09d9094e8a7f327f0088988ca776a2c6
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129713918"
---
다음 표에서는 Azure GPv2(범용 v2), 범용 v1(GPv1) 및 Blob Storage 계정에 대한 기본 제한에 대해 설명합니다. *수신* 제한은 스토리지 계정에 보내는 모든 데이터를 나타냅니다. *송신* 제한은 스토리지 계정에서 받는 모든 데이터를 나타냅니다.

대부분의 시나리오에서는 GPv2 스토리지 계정을 사용하는 것이 좋습니다. 가동 중지 시간 없이 데이터를 복사할 필요 없이 GPv1 또는 Blob Storage 계정을 GPv2 계정으로 쉽게 업그레이드할 수 있습니다. 자세한 내용은 [GPv2 스토리지 계정으로 업그레이드를 참조하세요.](../articles/storage/common/storage-account-upgrade.md)

> [!NOTE]
> 더 높은 용량과 수신 한도를 요청할 수 있습니다. 한도 증가를 요청하려면 [Azure 지원](https://azure.microsoft.com/support/faq/)에 문의하세요.

| 리소스 | 제한 |
|--|--|
| 표준 및 프리미엄 스토리지 계정을 포함하는 구독당 지역별 스토리지 계정 수입니다. | 250 |
| 기본 최대 스토리지 계정 용량 | 5PiB<sup>1</sup> |
| 스토리지 계정당 Blob 컨테이너, Blob, 파일 공유, 테이블, 큐, 엔터티 또는 메시지의 최대 수입니다. | 제한 없음 |
| 스토리지 계정당 기본 최대 요청 속도 | 초당 20,000개 요청<sup>1</sup> |
| 다음 지역(LRS/GRS)의 범용 v2 및 Blob Storage 계정당 기본 최대 수신 수입니다.<br /><ul><li>오스트레일리아 동부</li><li>미국 중부</li><li>동아시아</li><li>미국 동부 2</li><li>프랑스 중부</li><li>일본 동부</li><li>한국 중부</li><li>북유럽</li><li>미국 중남부</li><li>동남아시아</li><li>영국 남부</li><li>서유럽</li><li>미국 서부</li></ul> | 60Gbps<sup>1</sup> |
| 다음 지역(ZRS)의 범용 v2 및 Blob Storage 계정당 기본 최대 수신 수:<br /><ul><li>오스트레일리아 동부</li><li>미국 중부</li><li>미국 동부</li><li>미국 동부 2</li><li>프랑스 중부</li><li>일본 동부</li><li>북유럽</li><li>미국 중남부</li><li>동남아시아</li><li>영국 남부</li><li>서유럽</li><li>미국 서부 2</li></ul> | 60Gbps<sup>1</sup> |
| 이전 행에 나열되지 않은 지역의 범용 v2 및 Blob Storage 계정당 기본 최대 수신입니다. | 25Gbps<sup>1</sup> |
| 범용 v1 스토리지 계정에 대한 기본 최대 수신(모든 지역) | 10Gbps<sup>1</sup> |
| 다음 지역(LRS/GRS)의 범용 v2 및 Blob Storage 계정에 대한 기본 최대 송신:<br /><ul><li>오스트레일리아 동부</li><li>미국 중부</li><li>동아시아</li><li>미국 동부 2</li><li>프랑스 중부</li><li>일본 동부</li><li>한국 중부</li><li>북유럽</li><li>미국 중남부</li><li>동남아시아</li><li>영국 남부</li><li>서유럽</li><li>미국 서부</li></ul> | 120Gbps<sup>1</sup> |
| 다음 지역(ZRS)의 범용 v2 및 Blob Storage 계정에 대한 기본 최대 송신: <ul><li>오스트레일리아 동부</li><li>미국 중부</li><li>미국 동부</li><li>미국 동부 2</li><li>프랑스 중부</li><li>일본 동부</li><li>북유럽</li><li>미국 중남부</li><li>동남아시아</li><li>영국 남부</li><li>서유럽</li><li>미국 서부 2</li></ul> | 120Gbps<sup>1</sup> |
| 이전 행에 나열되지 않은 지역의 범용 v2 및 Blob Storage 계정에 대한 기본 최대 송신입니다. | 50Gbps<sup>1</sup> |
| 범용 v1 스토리지 계정에 대한 최대 송신(미국 지역) | RA-GRS/GRS를 사용하도록 설정한 경우 20Gbps, LRS/ZRS의 경우 30Gbps<sup>2</sup> |
| 범용 v1 스토리지 계정에 대한 최대 송신(미국 이외의 지역) | RA-GRS/GRS를 사용하도록 설정한 경우 10Gbps, LRS/ZRS의 경우 15Gbps<sup>2</sup> |
| 스토리지 계정당 최대 IP 주소 규칙 수 | 200 |
| 스토리지 계정당 최대 가상 네트워크 규칙 수 | 200 |
| 스토리지 계정당 최대 리소스 인스턴스 규칙 수 | 200 |
| 스토리지 계정당 최대 프라이빗 엔드포인트 수 | 200 |

<sup>1</sup> Azure Storage 표준 계정은 요청별 수신 및 송신에 대해 더 높은 용량 제한 및 더 높은 제한을 지원합니다. 계정 제한을 늘리려면 [Azure 지원](https://azure.microsoft.com/support/faq/)에 문의하세요.

<sup>2</sup> 스토리지 계정에 RA-GRS(지역 중복 스토리지) 또는 RA-GZRS(지역 영역 중복 스토리지)를 사용하여 읽기 액세스가 설정된 경우 보조 위치의 송신 대상은 기본 위치의 송신 대상과 동일합니다. 자세한 내용은 [Azure Storage 복제](../articles/storage/common/storage-redundancy.md)를 참조하세요.
