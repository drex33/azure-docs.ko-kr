---
title: Linux용 Azure NetApp Files 성능 벤치마크 | Microsoft Docs
description: Linux용으로 제공되는 Azure NetApp Files의 성능 벤치마크를 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-hchen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/29/2021
ms.author: b-hchen
ms.openlocfilehash: ef7ca4e238e6d787ab5f6c7b95ecc7fd2a90445c
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133483368"
---
# <a name="azure-netapp-files-performance-benchmarks-for-linux"></a>Linux용 Azure NetApp Files 성능 벤치마크

이 문서에서는 Linux용으로 제공되는 Azure NetApp Files의 성능 벤치마크를 설명합니다.

## <a name="linux-scale-out"></a>Linux 스케일 아웃

이 섹션에서는 Linux 워크로드 처리량 및 워크로드 IOPS의 성능 벤치마크를 설명합니다.

### <a name="linux-workload-throughput"></a>Linux 워크로드 처리량  

아래 그래프는 KiB (64-kibibyte) 순차적 작업 및 1 TiB 작업 집합을 나타냅니다. 그래프는 단일 Azure NetApp Files 볼륨이 1,600MiB/s 미만 순수 순차 쓰기와 4,500MiB/s 미만 순수 순차 읽기 사이에서 처리할 수 있음을 보여 줍니다.  

이 그래프는 순수 읽기에서 순수 쓰기로 10% 감소하는 것을 보여 줍니다. 또한 다양한 읽기/쓰기 비율(100%:0%, 90%:10%, 80%:20% 등)을 사용할 때 예측할 수 있는 항목을 보여 줍니다.

![Linux 워크로드 처리량](../media/azure-netapp-files/performance-benchmarks-linux-workload-throughput.png)  

### <a name="linux-workload-iops"></a>Linux 워크로드 IOPS  

다음 그래프는 kibibyte (KiB) 임의 작업 및 1 개의 TiB 작업 집합을 나타냅니다. 이 그래프는 Azure NetApp Files 볼륨이 130,000 미만인 순수 임의 쓰기 및 460,000 미만인 순수 임의 읽기 사이에서 처리할 수 있음을 보여 줍니다.  

이 그래프는 순수 읽기에서 순수 쓰기로 10% 감소하는 것을 보여 줍니다. 또한 다양한 읽기/쓰기 비율(100%:0%, 90%:10%, 80%:20% 등)을 사용할 때 예측할 수 있는 항목을 보여 줍니다.

![Linux 워크로드 IOPS](../media/azure-netapp-files/performance-benchmarks-linux-workload-iops.png)  

## <a name="linux-scale-up"></a>Linux 확장  

이 섹션의 그래프는 NFSv3을 사용한 클라이언트 측 탑재 옵션에 대한 유효성 검사 테스트 결과를 보여 줍니다. 자세한 내용은 [ `nconnect` Linux 탑재 옵션 섹션](performance-linux-mount-options.md#nconnect)을 참조 하세요.

그래프는의 장점과 `nconnect` 탑재 되지 않은 볼륨을 비교 합니다 `connected` . 그래프에서 FIO는 64-KiB 순차 워크 로드를 사용 하 여 미국 west2 Azure 지역의 단일 D32s_v4 인스턴스에서 작업을 생성 했습니다. 여기서는 테스트 당시에 Azure NetApp Files에서 지원 되는 가장 큰 i/o 크기를 나타냅니다. Azure NetApp Files는 이제 더 큰 i/o 크기를 지원 합니다. 자세한 내용은 [ `rsize` `wsize` Linux 탑재 옵션의 및 섹션](performance-linux-mount-options.md#rsize-and-wsize)을 참조 하세요.

### <a name="linux-read-throughput"></a>Linux 읽기 처리량  

다음 그래프는 64-KiB 3500의 순차적 읽기 ( `nconnect` 약 2.3 x가 아닌)를 사용 하 여 읽기를 표시 합니다 `nconnect` .

![Linux 읽기 처리량](../media/azure-netapp-files/performance-benchmarks-linux-read-throughput.png)  

### <a name="linux-write-throughput"></a>Linux 쓰기 처리량  

다음 그래프에서는 순차 쓰기를 보여 줍니다. 그래프는 `nconnect`에 순차 쓰기에 대해 눈에 띄는 이점이 없음을 나타냅니다. 1500 MiB/s는 거의 순차적 쓰기 볼륨 상한 및 D32s_v4 인스턴스 송신 한도입니다.

![Linux 쓰기 처리량](../media/azure-netapp-files/performance-benchmarks-linux-write-throughput.png)  

### <a name="linux-read-iops"></a>Linux 읽기 IOPS  

다음 그래프는 ~ 20만 읽기 IOPS의 4-KiB 임의 읽기와 약 3, 0003을 보여 줍니다 `nconnect` `nconnect` .

![Linux 읽기 IOPS](../media/azure-netapp-files/performance-benchmarks-linux-read-iops.png)  

### <a name="linux-write-iops"></a>Linux 쓰기 IOPS  

다음 그래프는 ~ 135000 write IOPS의 KiB 임의 쓰기 `nconnect` 를, 약 3, 0003을 보여 줍니다 `nconnect` .

![Linux 쓰기 IOPS](../media/azure-netapp-files/performance-benchmarks-linux-write-iops.png)  

## <a name="next-steps"></a>다음 단계

- [Azure NetApp Files: 클라우드 스토리지를 최대한 활용](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf?hsCtaTracking=f2f560e9-9d13-4814-852d-cfc9bf736c6a%7C764e9d9c-9e6b-4549-97ec-af930247f22f)
