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
ms.openlocfilehash: 0e0d3c131f5cd53684580d0035c8836a2537d780
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130070828"
---
현재 울트라 디스크에서 사용할 수 있는 인프라 중복 옵션은 가용성 영역뿐입니다. 다른 중복 옵션을 사용하는 VM은 울트라 디스크를 연결할 수 없습니다.

다음 표에서는에서 사용 가능한 ultra 디스크와 해당 가용성 옵션을 간략하게 설명 합니다.

> [!NOTE]
> 다음 목록의 영역에 ultra disks를 지 원하는 가용성 영역이 없는 경우 ultra disk를 연결 하기 위해 인프라 중복성 없이 해당 지역의 VM을 배포 해야 합니다.

| 중복 옵션 | 영역 |
|--------------------|---------|
| **단일 Vm** | 오스트레일리아 중부<br/>브라질 남부<br/>인도 중부<br/>동아시아<br/>독일 중서부<br/>한국 중부<br/>미국 중 북부, 미국 중부, 미국 서 부<br/>US Gov 애리조나, US Gov 텍사스, US Gov 버지니아<br/> |
| **두 개의 가용성 영역** | 오스트레일리아 동부<br/>캐나다 중부<br/>북유럽, 서유럽<br/>일본 동부<br/>동남아시아<br/>영국 남부<br/>미국 중부, 미국 동부, 미국 동부 2, 미국 서 부 2|
| **세 개의 가용성 영역** | 프랑스 중부 |

모든 VM 크기를 울트라 디스크가 있는 지원되는 모든 지역에서 사용할 수 있는 것은 아닙니다. 다음 표에서는 ultra disks와 호환 되는 VM 시리즈를 나열 합니다.

|VM 유형     |크기    |설명  |
|------------|---------|-------------|
| 범용|[DSv3 시리즈](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series), [Ddsv4 시리즈](../articles/virtual-machines/ddv4-ddsv4-series.md#ddsv4-series), [Dsv4 시리즈](../articles/virtual-machines/dv4-dsv4-series.md#dsv4-series), [Dasv4 시리즈](../articles/virtual-machines/dav4-dasv4-series.md#dasv4-series)| CPU 대 메모리 비율이 적당합니다. 테스트 및 개발, 중소 규모 데이터베이스 및 트래픽이 적거나 중간 정도인 웹 서버에 적합합니다.|
| 컴퓨팅 최적화|[FSv2 시리즈](../articles/virtual-machines/fsv2-series.md)| CPU 대 메모리 비율이 높습니다. 트래픽이 중간 정도인 웹 서버, 네트워크 어플라이언스, 일괄 처리 프로세스 및 애플리케이션 서버에 적합합니다.|
| 메모리에 최적화|[ESv3 시리즈](../articles/virtual-machines/ev3-esv3-series.md#esv3-series), [Easv4 시리즈](../articles/virtual-machines/eav4-easv4-series.md#easv4-series), [Edsv4 시리즈](../articles/virtual-machines/edv4-edsv4-series.md#edsv4-series), [Esv4 시리즈](../articles/virtual-machines/ev4-esv4-series.md#esv4-series), [M 시리즈](../articles/virtual-machines/m-series.md), [Mv2 시리즈](../articles/virtual-machines/mv2-series.md)|메모리 대 CPU 비율이 높습니다. 관계형 데이터베이스 서버, 중대형 캐시 및 메모리 내 분석에 적합합니다.
| 스토리지 최적화|[LSv2 시리즈](../articles/virtual-machines/lsv2-series.md)|빅 데이터, SQL, NoSQL 데이터베이스, 데이터 웨어하우징, 대형 트랜잭션 데이터베이스에 이상적인 높은 디스크 처리량과 IO.|
| GPU에 최적화|[NCv2 시리즈](../articles/virtual-machines/ncv2-series.md), [NCv3 시리즈](../articles/virtual-machines/ncv3-series.md), [NCasT4_v3 시리즈](../articles/virtual-machines/nct4-v3-series.md), [ND 시리즈](../articles/virtual-machines/nd-series.md), [NDv2 시리즈](../articles/virtual-machines/ndv2-series.md), [NVv3 시리즈](../articles/virtual-machines/nvv3-series.md), [NVv4 시리즈](../articles/virtual-machines/nvv4-series.md)| 고급 그래픽 렌더링 및 비디오 편집뿐만 아니라 딥 러닝을 통한 모델 학습 및 추론(ND)도 목표로 하는 특수화된 가상 머신입니다. 한 개 이상의 GPU를 사용할 수 있습니다.|
| <nobr>성능 최적화</nobr> |[Hb 시리즈](../articles/virtual-machines/hb-series.md), [HC 시리즈](../articles/virtual-machines/hc-series.md), [HBv2 시리즈](../articles/virtual-machines/hbv2-series.md)|선택적 처리량이 높은 네트워크 인터페이스 (RDMA)를 사용 하는 가장 빠르고 강력한 CPU 가상 컴퓨터입니다.|

디스크 스냅숏, 디스크 내보내기, 디스크 유형 변경, VM 이미지, 가용성 집합, Azure 전용 호스트 또는 Azure disk encryption을 비롯 한 일부 기능과 기능에는 울트라 디스크를 사용할 수 없습니다. Azure Backup 및 Azure Site Recovery는 울트라 디스크를 지원 하지 않습니다. 또한 캐시 되지 않은 읽기와 캐시 되지 않은 쓰기만 지원 됩니다.

Ultra disks는 기본적으로 4k 물리적 섹터 크기를 지원 합니다. 512E 섹터 크기는 등록을 요구 하지 않는 일반 공급 제품으로 사용할 수 있습니다. 대부분의 응용 프로그램은 4k 섹터 크기와 호환 되지만 일부는 512 바이트 섹터 크기를 요구 합니다. 예를 들어 Oracle Database는 4k 기본 디스크를 지원 하기 위해 릴리스 12.2 이상이 필요 합니다. 이전 버전의 Oracle DB의 경우 512바이트 섹터 크기가 필요합니다.
