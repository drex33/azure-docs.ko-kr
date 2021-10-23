---
title: Azure NetApp Files에 대한 리소스 제한 | Microsoft Docs
description: Azure NetApp Files 리소스에 대한 제한과 리소스 제한을 늘리는 요청 방법을 설명합니다.
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
ms.date: 09/27/2021
ms.author: b-juche
ms.openlocfilehash: 9404d6052093880ff25ded0492e4a3fb87202f09
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252500"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp Files에 대한 리소스 제한

Azure NetApp Files에 대한 리소스 제한을 이해하면 볼륨을 관리하는 데 도움이 됩니다.

## <a name="resource-limits"></a>리소스 제한

다음 표에서는 Azure NetApp Files에 대한 리소스 제한을 설명합니다.

|  리소스  |  기본 제한  |  지원 요청을 통해 조정 가능  |
|----------------|---------------------|--------------------------------------|
|  [구독당 지역 용량 할당량](regional-capacity-quota.md)   |  25TiB  |  Yes  |
|  구독당 Azure 지역별 NetApp 계정 수  |  10    |  예   |
|  NetApp 계정당 용량 풀 수   |    25     |   예   |
|  구독당 볼륨 수   |    500     |   Yes   |
|  용량 풀당 볼륨 수     |    500   |    Yes     |
|  볼륨당 스냅샷 수       |    255     |    No        |
|  Azure Virtual Network당 Azure NetApp Files(Microsoft.NetApp/볼륨)에 위임된 서브넷 수    |   1   |    아니요    |
|  Azure NetApp Files를 사용하는 VNet(즉시 피어링된 VNet 포함)의 사용된 IP 수   |    1000   |    No   |
|  단일 용량 풀의 최소 크기   |  4TiB     |    No  |
|  단일 용량 풀의 최대 크기    |  500TiB   |   No   |
|  단일 볼륨의 최소 크기    |    100GiB    |    아니요    |
|  단일 볼륨의 최대 크기     |    100TiB    |    No    |
|  단일 파일의 최대 크기     |    16TiB    |    No    |    
|  단일 디렉터리에서 디렉터리 메타데이터의 최대 크기      |    320MB    |    No    |    
|  단일 디렉터리의 최대 파일 수  | *약* 4백만입니다. <br> [디렉터리가 제한 크기에 도달하고 있는지 확인](#directory-limit)을 참조하세요.  |    No    |   
|  볼륨당 최대 파일 수([maxfiles](#maxfiles))     |    1억    |    Yes    |    
|  볼륨당 최대 내보내기 정책 규칙 수     |    5  |    No    | 
|  수동 QoS 볼륨에 할당된 최소 처리량     |    1MiB/s   |    No    |    
|  수동 QoS 볼륨에 할당된 최대 처리량     |    4,500MiB/s    |    No    |    
|  지역 간 복제 데이터 보호 볼륨(대상 볼륨) 수     |    10    |    예    |     
|  볼륨당 최대 정책 기반(예약된) 백업 수  | <ul><li> 일일 보존 수: 1(최소) ~ 1019(최대) </li> <li> 주간 보존 수: 1(최소) ~ 1019(최대) </li> <li> 월간 보존 수: 1(최소) ~ 1019(최대) </ol></li> <br> 결합된 최대 일일, 주별 및  월별 백업 보존 수는 1019개입니다.  |  N  |
|  보호된 볼륨의 최대 크기  |  100TiB  |  N  |
|  구독당 백업할 수 있는 최대 볼륨 수   |  5  |  지원  |
|  일별 볼륨당 최대 수동 백업 수 |  5  |  지원  |

자세한 내용은 [용량 관리 FAQ](faq-capacity-management.md)를 참조하세요.

## <a name="determine-if-a-directory-is-approaching-the-limit-size"></a>디렉터리가 제한 크기 <a name="directory-limit"></a>에 도달하고 있는지 확인  

클라이언트에서 `stat` 명령을 사용하여 디렉터리가 디렉터리 메타데이터의 최대 크기 제한(320MB)에 도달하는지 여부를 확인할 수 있습니다.   

320MB 디렉터리의 경우 블록 수는 655360개이고, 각 블록 크기는 512바이트입니다.  (즉, 320x1024x1024/512입니다.) 이 숫자는 320MB 디렉터리에 대해 최대 약 400만 개의 파일로 변환됩니다. 그러나 디렉터리에서 비 ASCII 문자가 포함된 파일 수와 같은 요인에 따라 실제 최대 파일 수는 더 적을 수 있습니다. 따라서 다음과 같이 `stat` 명령을 사용하여 디렉터리가 제한에 도달하고 있는지 여부를 확인해야 합니다.  

예:

```console
[makam@cycrh6rtp07 ~]$ stat bin
File: 'bin'
Size: 4096            Blocks: 8          IO Block: 65536  directory

[makam@cycrh6rtp07 ~]$ stat tmp
File: 'tmp'
Size: 12288           Blocks: 24         IO Block: 65536  directory
 
[makam@cycrh6rtp07 ~]$ stat tmp1
File: 'tmp1'
Size: 4096            Blocks: 8          IO Block: 65536  directory
```

## <a name="maxfiles-limits"></a>Maxfiles 한도 <a name="maxfiles"></a> 

Azure NetApp Files 볼륨에는 *maxfiles* 라는 한도가 있습니다. Maxfiles 한도는 볼륨에 포함할 수 있는 파일 수입니다. Linux 파일 시스템은 *inode* 로 제한을 나타냅니다. Azure NetApp Files 볼륨의 maxfiles 한도는 볼륨의 크기(할당량)를 기준으로 인덱싱됩니다. 볼륨에 대한 maxfiles 한도는 프로비저닝된 볼륨 크기의 TiB당 2천만 개 파일의 비율로 증가하거나 감소합니다. 

서비스는 프로비저닝된 크기를 기준으로 볼륨의 maxfiles 한도를 동적으로 조정합니다. 예를 들어 처음에 1TiB 크기로 구성된 볼륨의 maxfiles 한도는 2천만 개입니다. 이후에 볼륨 크기를 변경하면 다음 규칙에 따라 maxfiles 한도가 자동으로 재조정됩니다. 

|    볼륨 크기(할당량)     |  maxfiles 한도의 자동 재조정    |
|----------------------------|-------------------|
|    <= 1TiB                |    2천만     |
|    > 1TiB <= 2TiB    |    4천만     |
|    > 2TiB <= 3TiB    |    6천만     |
|    > 3TiB <= 4TiB    |    8천만     |
|    > 4TiB                 |    1억    |

볼륨에 이미 4TiB 이상의 할당량을 할당한 경우 [지원 요청](#request-limit-increase)을 시작하여 maxfiles(inodes) 한도를 1억 개 이상으로 늘릴 수 있습니다. 1억 개 파일(또는 그 일부)마다 해당 볼륨 할당량을 4TiB씩 늘려야 합니다.  예를 들어 maxfiles 한도를 1억 개에서 2억 개 파일(또는 그 사이에 있는 모든 수)로 늘리면 볼륨 할당량을 4TiB에서 8TiB로 늘려야 하는 것입니다.

볼륨 할당량이 20TiB 이상인 경우 maxfiles 한도를 5억 개로 늘릴 수 있습니다. <!-- ANF-11854 --> 

## <a name="request-limit-increase"></a>제한 증가 요청

Azure 지원 요청을 만들어 [리소스 제한](#resource-limits) 표에서 조정 가능한 한도를 늘릴 수 있습니다. 

1. **지원 + 문제 해결** 아래의 **새 지원 요청** 으로 이동합니다.
1. **문제 설명** 탭에서 요청된 정보를 입력합니다.
1. **추가 세부 정보** 탭에서 요청 정보 필드에 **세부 정보 입력** 을 클릭합니다.  

    ![세부 정보 탭과 세부 정보 입력 필드를 보여주는 스크린샷.](../media/azure-netapp-files/quota-additional-details.png)

1. 표시되는 할당량 세부 정보 창에서:  

    1. 할당량 유형에서 증가시킬 리소스의 유형을 선택합니다.  
        예를 들면 다음과 같습니다.  
        * 구독당 지역 용량 할당량(TiB)
        * *구독당 Azure 지역별 NetApp 계정 수*
        * *구독당 볼륨 수*

    1. 요청된 지역에서 지역을 선택합니다.   
        현재 및 기본 크기는 할당량 상태 아래에 표시됩니다.
    1. 지정한 할당량 유형의 증가를 요청하는 값을 입력합니다.
    
    ![지역별 할당량에 대한 증가를 표시하고 요청하는 방법을 보여주는 스크린샷.](../media/azure-netapp-files/quota-details-regional-request.png)

1. **다음** 과 **검토 + 만들기** 를 클릭하여 요청을 만듭니다.

## <a name="next-steps"></a>다음 단계  

- [Azure NetApp Files의 스토리지 계층 구조 이해](azure-netapp-files-understand-storage-hierarchy.md)
- [Azure NetApp Files 비용 모델](azure-netapp-files-cost-model.md)
- [Azure NetApp Files에 대 한 지역 용량 할당량](regional-capacity-quota.md)
