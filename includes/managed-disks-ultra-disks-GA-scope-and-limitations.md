---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/17/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: d0bed89f56d41c6613080e84d743d245243bb507
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122336032"
---
현재 울트라 디스크에는 다음과 같은 추가 제한 사항이 있습니다.

현재 울트라 디스크에서 사용할 수 있는 인프라 중복 옵션은 가용성 영역뿐입니다. 다른 중복 옵션을 사용하는 VM은 울트라 디스크를 연결할 수 없습니다.

다음 표에는 울트라 디스크를 사용할 수 있는 지역과 해당 가용성 옵션이 요약되어 있습니다.

> [!NOTE]
> 다음 목록의 지역에 울트라 디스크 지원 가용성 영역이 없는 경우, 울트라 디스크를 연결하려면 해당 지역의 VM을 인프라 중복 옵션 없이 배포해야 합니다.

|영역  |중복 옵션  |
|---------|---------|
|브라질 남부     |단일 VM 전용(가용성 집합 및 가상 머신 확장 집합은 지원되지 않음)|
|인도 중부     |단일 VM 전용(가용성 집합 및 가상 머신 확장 집합은 지원되지 않음)|
|동아시아     |단일 VM 전용(가용성 집합 및 가상 머신 확장 집합은 지원되지 않음)|
|독일 중서부     |단일 VM 전용(가용성 집합 및 가상 머신 확장 집합은 지원되지 않음)|
|한국 중부     |단일 VM 전용(가용성 집합 및 가상 머신 확장 집합은 지원되지 않음)|
|미국 중북부    |단일 VM 전용(가용성 집합 및 가상 머신 확장 집합은 지원되지 않음)|
|미국 중남부    |단일 VM 전용(가용성 집합 및 가상 머신 확장 집합은 지원되지 않음)|
|US Gov 애리조나     |단일 VM 전용(가용성 집합 및 가상 머신 확장 집합은 지원되지 않음)|
|US Gov 버지니아     |단일 VM 전용(가용성 집합 및 가상 머신 확장 집합은 지원되지 않음)|
|US Gov 텍사스     |단일 VM 전용(가용성 집합 및 가상 머신 확장 집합은 지원되지 않음)|
|미국 서부     |단일 VM 전용(가용성 집합 및 가상 머신 확장 집합은 지원되지 않음)        |
|오스트레일리아 중부    |단일 VM 전용(가용성 집합 및 가상 머신 확장 집합은 지원되지 않음)|
|오스트레일리아 동부     |세 개의 가용성 영역         |
|동남 아시아    |세 개의 가용성 영역        |
|캐나다 중부     |세 개의 가용성 영역          |
|미국 중부     |세 개의 가용성 영역          |
|미국 동부     |세 개의 가용성 영역          |
|미국 동부 2     |세 개의 가용성 영역         |
|프랑스 중부    |두 개의 가용성 영역        |
|일본 동부    |세 개의 가용성 영역        |
|북유럽    |세 개의 가용성 영역        |
|영국 남부    |세 개의 가용성 영역        |
|서유럽    | 세 개의 가용성 영역|
|미국 서부 2    |세 개의 가용성 영역|

- 다음 VM 시리즈에서만 지원됩니다.
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [Easv4](../articles/virtual-machines/eav4-easv4-series.md#easv4-series)
    - [Edsv4](../articles/virtual-machines/edv4-edsv4-series.md#edsv4-series)
    - [Esv4](../articles/virtual-machines/ev4-esv4-series.md#esv4-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [Dasv4](../articles/virtual-machines/dav4-dasv4-series.md#dasv4-series)
    - [Ddsv4](../articles/virtual-machines/ddv4-ddsv4-series.md#ddsv4-series)
    - [Dsv4](../articles/virtual-machines/dv4-dsv4-series.md#dsv4-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [M](../articles/virtual-machines/m-series.md)
    - [Mv2](../articles/virtual-machines/mv2-series.md)
    - [HBv2](../articles/virtual-machines/hbv2-series.md)
    - [HB](../articles/virtual-machines/hb-series.md)
    - [HC](../articles/virtual-machines/hc-series.md)
    - [NDv2](../articles/virtual-machines/ndv2-series.md)
    - [ND](../articles/virtual-machines/nd-series.md)
    - [NC_T4_v3](../articles/virtual-machines/nct4-v3-series.md)
    - [NCv2](../articles/virtual-machines/ncv2-series.md)
    - [NCv3](../articles/virtual-machines/ncv3-series.md)
    - [NVv3](../articles/virtual-machines/nvv3-series.md)
    - [NVv4](../articles/virtual-machines/nvv4-series.md)
    
- 모든 VM 크기를 울트라 디스크가 있는 지원되는 모든 지역에서 사용할 수 있는 것은 아닙니다.
- 데이터 디스크로만 사용할 수 있습니다. 
- 기본적으로 4k 물리적 섹터 크기를 지원합니다. 512E 섹터 크기는 일반적으로 사용할 수 있는 제품(등록 필요 없음)으로 제공됩니다. 대부분의 애플리케이션은 4k 섹터 크기와 호환되지만 일부는 512바이트 섹터 크기가 필요합니다. 한 가지 예는 4k 기본 디스크를 지원하기 위해 릴리스 12.2 이상이 필요한 Oracle Database입니다. 이전 버전의 Oracle DB의 경우 512바이트 섹터 크기가 필요합니다.
- 빈 디스크로만 만들 수 있습니다.
- 현재 디스크 스냅샷, 디스크 내보내기, 디스크 형식 변경, VM 이미지, 가용성 집합, Azure 전용 호스트 또는 Azure 디스크 암호화를 지원하지 않습니다.
- 현재 Azure Backup 또는 Azure Site Recovery와의 통합을 지원하지 않습니다.
- 캐시되지 않은 읽기와 캐시되지 않은 쓰기만 지원합니다.
- 현재 일반 공급 크기의 단일 VM에 대한 IOPS의 최대 한도는 80,000입니다. IOPS가 더 큰 울트라 디스크를 공유 디스크로 사용하여 여러 VM을 지원할 수 있습니다.

Azure 울트라 디스크는 기본적으로 구독당 지역별 최대 32TiB를 제공하지만, 울트라 디스크는 요청에 따라 더 많은 용량을 지원합니다. 용량 증가를 요청하려면 할당량 증가를 요청하거나 Azure 지원에 문의하세요.
