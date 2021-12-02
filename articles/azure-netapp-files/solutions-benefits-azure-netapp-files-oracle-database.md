---
title: Oracle Database에서 Azure NetApp Files를 사용할 경우의 이점 | Microsoft Docs
description: 기술에 대해 설명하고 Oracle dNFS(Direct NFS)와 기존 NFS 클라이언트 간 성능 비교를 제공합니다. Azure NetApp Files에서 dNFS를 사용할 경우의 장점을 보여 줍니다.
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
ms.date: 04/23/2020
ms.author: b-hchen
ms.openlocfilehash: c33b113b97dc203783059028a3c97a3c9a467810
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133484812"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>Oracle Database에서 Azure NetApp Files를 사용할 경우의 이점

Oracle dNFS(Direct NFS)를 사용하면 운영 체제의 자체 NFS 드라이버보다 성능을 높일 수 있습니다. 이 문서에서는 기술에 대해 설명하고 dNFS와 기존 NFS 클라이언트(커널 NFS) 간 성능 비교를 제공합니다. 또한 Azure NetApp Files에서 dNFS를 사용할 경우의 장점과 편의성을 보여 줍니다.  

## <a name="how-oracle-direct-nfs-works"></a>Oracle Direct NFS의 작동 방식

다음 요약에서는 Oracle Direct NFS의 작동 방식을 개괄적으로 설명합니다.

* Oracle Direct NFS는 운영 체제 버퍼 캐시를 무시합니다. 데이터가 사용자 공간에 한 번만 캐시되므로 메모리 복사본의 오버헤드를 없앨 수 있습니다.  

* 기존 NFS 클라이언트는 아래와 같이 단일 네트워크 흐름을 사용합니다.    

    ![단일 네트워크 흐름을 사용하는 기존 NFS 클라이언트](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

    Oracle Direct NFS는 여러 네트워크 흐름에 네트워크 트래픽을 부하 분산하여 성능을 향상시킵니다. 아래와 같이 테스트된 650개의 개별 네트워크 연결이 Oracle Database에서 동적으로 설정되었습니다.  

    ![Oracle Direct NFS 성능 향상](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

[Direct NFS에 대한 Oracle FAQ](http://www.orafaq.com/wiki/Direct_NFS)는 Oracle dNFS가 최적화된 NFS 클라이언트임을 보여 줍니다. TCP/IP를 통해 액세스할 수 있는 NAS 스토리지 디바이스에 있는 NFS 스토리지에 대한 빠르고 스케일링 가능한 액세스를 제공합니다. dNFS는 ASM과 마찬가지로 주로 DAS 또는 SAN 스토리지에서 사용되는 데이터베이스 커널에 빌드됩니다. 따라서 ‘NAS 스토리지를 구현할 때는 dNFS를 사용하고 SAN 스토리지를 구현할 때는 ASM을 사용’합니다.

dNFS는 Oracle 18c의 기본 옵션입니다.

dNFS는 Oracle Database 11g부터 사용할 수 있습니다. 아래 다이어그램은 dNFS와 네이티브 NFS를 비교한 것입니다. dNFS를 사용하는 경우 Azure 가상 머신에서 실행되는 Oracle 데이터베이스는 네이티브 NFS 클라이언트보다 많은 I/O를 처리할 수 있습니다.

![Oracle과 Azure NetApp Files의 dNFS 및 네이티브 NFS 비교](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

아래 두 명령을 실행하고 데이터베이스를 다시 시작하면 dNFS를 사용하거나 사용하지 않도록 설정할 수 있습니다.

사용하도록 설정하려면:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

사용하지 않도록 설정하려면:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>Oracle Direct NFS와 결합된 Azure NetApp Files

Azure NetApp Files 서비스를 사용하여 Oracle dNFS의 성능을 향상시킬 수 있습니다. 이 서비스를 통해 애플리케이션 성능을 완전히 제어할 수 있습니다. Azure NetApp Files 서비스는 매우 까다로운 애플리케이션을 충족할 수 있습니다. Azure NetApp Files와 Oracle dNFS를 결합하면 워크로드에 매우 유용합니다.

## <a name="next-steps"></a>다음 단계

- [Azure NetApp Files를 사용하는 솔루션 아키텍처](azure-netapp-files-solution-architectures.md)
- [Azure의 Oracle 애플리케이션 및 솔루션 개요](../virtual-machines/workloads/oracle/oracle-overview.md)