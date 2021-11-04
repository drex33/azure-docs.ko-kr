---
title: 포함 파일
description: 포함 파일
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 10/22/2021
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 777af1f15441ab4849292e40b3a27863276f3fce
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473353"
---
| 리소스 | Basic | Standard | Premium |
|---|---|---|---|
| 포함된 스토리지<sup>1</sup>(GiB) | 10 | 100 | 500 |
| 스토리지 한도(TiB) | 20| 20 | 20 |
| 최대 이미지 레이어 크기(GiB) | 200 | 200 | 200 |
| 최대 매니페스트 크기(MiB) | 4 | 4 | 4 |
| 분당 ReadOps<sup>2, 3</sup> | 1,000 | 3,000 | 10000 |
| 분당 WriteOps<sup>2, 4</sup> | 100 | 500 | 2,000 |
| 다운로드 대역폭<sup>2</sup>(Mbps) | 30 | 60 | 100 |
| 업로드 대역폭<sup>2</sup>(Mbps) | 10 | 20 | 50 |
| Webhook | 2 | 10 | 500 |
| 지역에서 복제 | 해당 없음 | 해당 없음 | [지원됨][geo-replication] |
| 가용성 영역 | 해당 없음 | 해당 없음 | [미리 보기][zones] |
| 콘텐츠 신뢰 | 해당 없음 | 해당 없음 | [지원됨][content-trust] |
| 프라이빗 엔드포인트가 있는 프라이빗 링크 | 해당 없음 | 해당 없음 | [지원됨][plink] |
| &bull; 프라이빗 엔드포인트 | 해당 없음 | N/A | 200 |
| 공용 IP 네트워크 규칙 | 해당 없음 | 해당 없음 | 100 |
| 서비스 엔드포인트 VNet 액세스 | 해당 없음 | 해당 없음 | [미리 보기][vnet] |
| &bull; 가상 네트워크 규칙 | N/A | 해당 없음 | 100 |
| 고객 관리형 키 | 해당 없음 | 해당 없음 | [지원됨][cmk] |
| 리포지토리 범위 권한 | 해당 없음 | 해당 없음 | [미리 보기][token]|
| &bull; 토큰 | 해당 없음 | 해당 없음 | 20,000 |
| &bull; 범위 맵 | 해당 없음 | 해당 없음 | 20,000 |
| &bull; 범위 맵당 리포지토리 | 해당 없음 | 해당 없음 | 500 |


<sup>1</sup> 각 계층의 일별 요금에 포함된 스토리지입니다. 추가 스토리지는 GiB당 일일 추가 요금으로 레지스트리 스토리지 제한까지 사용할 수 있습니다. 요금 정보는 [Azure Container Registry 가격 책정][pricing]을 참조하세요. 레지스트리 스토리지 제한을 초과하는 스토리지가 필요한 경우 Azure 지원에 문의하세요.

<sup>2</sup>*ReadOps*, *WriteOps* 및 *Bandwidth* 는 최소 추정치입니다. Azure Container Registry는 필요한 사용량에 따라 성능을 개선하려고 합니다.

<sup>3</sup> [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image)은 이미지의 레이어 수와 매니페스트 검색을 더한 값에 따라 여러 읽기 작업으로 변환됩니다.

<sup>4</sup> [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image)는 푸시해야 하는 레이어 수에 따라 여러 쓰기 작업으로 변환됩니다. `docker push`에는 기존 이미지에 대해 매니페스트를 검색할 *ReadOps* 가 포함되어 있습니다.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md
[zones]: ../articles/container-registry/zone-redundancy.md
