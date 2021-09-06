---
title: Azure NetApp Files의 Linux 직접 I/O 모범 사례 | Microsoft Docs
description: Linux 직접 I/O 및 Azure NetApp Files에 대한 모범 사례를 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: b-juche
ms.openlocfilehash: 6497d91169c7530251a473e8e06c26ce411e0e6a
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113233518"
---
# <a name="linux-direct-io-best-practices-for-azure-netapp-files"></a>Azure NetApp Files에 대한 Linux 직접 I/O 모범 사례

이 문서는 Azure NetApp Files의 직접 I/O 모범 사례를 이해하는 데 도움이 됩니다.  

## <a name="direct-io"></a>직접 I/O

 스토리지 성능 벤치마킹에서 가장 일반적으로 사용되는 매개 변수는 직접 I/O입니다. FIO 및 Vdbench에서 지원됩니다. DISKSPD는 유사한 메모리 매핑 I/O 구조를 지원합니다. 직접 I/O를 사용하면 파일 시스템 캐시가 바이패스되고 직접 메모리 액세스 복사를 위한 작업이 방지되며 스토리지 테스트가 빠르고 간단해집니다.  

직접 I/O 매개 변수를 사용하면 스토리지 테스트가 쉬워집니다. 클라이언트의 파일 시스템 캐시에서 읽은 데이터가 없습니다. 따라서 테스트는 메모리 액세스 속도가 아니라 스토리지 프로토콜과 서비스 자체에 큰 부담을 주고 있습니다. 또한 DMA 메모리 복사본이 없으면 읽기 및 쓰기 작업이 처리 측면에서 효율적입니다. 

예제 워크로드로 Linux `dd` 명령을 사용합니다. 옵션 `odirect` 플래그가 없으면 `dd`에서 생성된 모든 I/O가 Linux 버퍼 캐시에서 처리됩니다. 블록이 이미 메모리에 있는 읽기는 스토리지에서 검색되지 않습니다. 버퍼 캐시 누락이 발생하는 읽기는 NFS 미리 읽기를 사용하여 스토리지에서 읽게 되고 탑재 `rsize` 및 클라이언트 미리 읽기 튜닝 가능 요인에 따라 결과가 달라집니다. 버퍼 캐시를 통해 쓰기가 전송될 때 쓰기 후 메커니즘을 사용합니다. 이 메커니즘은 튜닝되지 않고 상당한 양의 병렬 처리를 사용하여 데이터를 스토리지 디바이스로 보냅니다. 읽기용 `dd` 1개 및 쓰기용 `dd` 1개 등 두 개의 독립적인 I/O 스트림을 실행하려고 할 수 있습니다. 그러나 실제로 튜닝되지 않은 운영 체제는 읽기보다 쓰기를 우선하고 더 많은 병렬 처리를 사용합니다.

데이터베이스 외에는 일부 애플리케이션에서 직접 I/O를 사용합니다. 대신 반복 읽기의 경우 대용량 메모리 캐시의 이점을 활용하고 비동기 쓰기의 경우 나중 쓰기(write behind) 캐시를 활용합니다. 간단히 말해, *만약* 통합되는 애플리케이션이 파일 시스템 캐시를 사용하는 경우 직접 I/O를 사용하면 테스트가 마이크로 벤치마크로 바뀝니다.  

다음은 직접 I/O를 지원하는 몇 가지 데이터베이스입니다. 

* Oracle 
* SAP HANA
* MySQL(InnoDB 스토리지 엔진)
* RocksDB
* PostgreSQL
* Teradata

## <a name="best-practices"></a>모범 사례 

`directio`를 사용하여 테스트하는 것은 스토리지 서비스 및 클라이언트의 한계를 이해하는 데 매우 좋은 방법입니다. 애플리케이션 자체의 동작을 더 잘 이해하려면(애플리케이션에서 `directio`를 사용하지 않는 경우) 파일 시스템 캐시를 통해 테스트를 실행해야 합니다.

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files에 대한 Linux 파일 시스템 캐시 모범 사례](performance-linux-filesystem-cache.md)
* [Azure NetApp Files에 대한 Linux NFS 탑재 옵션 모범 사례](performance-linux-mount-options.md)
* [Azure NetApp Files를 위한 Linux 동시성 모범 사례](performance-linux-concurrency-session-slots.md)
* [Linux NFS 미리 읽기 모범 사례](performance-linux-nfs-read-ahead.md)
* [Azure 가상 머신 SKU 모범 사례](performance-virtual-machine-sku.md) 
* [Linux용 성능 벤치마크](performance-benchmarks-linux.md) 
