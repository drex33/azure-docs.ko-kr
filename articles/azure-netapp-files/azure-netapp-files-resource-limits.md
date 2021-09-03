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
ms.date: 07/28/2021
ms.author: b-juche
ms.openlocfilehash: 06be68fb1de224bbbcad13e71e7f4069e44f8309
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566330"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp Files에 대한 리소스 제한

Azure NetApp Files에 대한 리소스 제한을 이해하면 볼륨을 관리하는 데 도움이 됩니다.

## <a name="resource-limits"></a>리소스 제한

다음 표에서는 Azure NetApp Files에 대한 리소스 제한을 설명합니다.

|  리소스  |  기본 제한  |  지원 요청을 통해 조정 가능  |
|----------------|---------------------|--------------------------------------|
|  [구독당 지역 용량 할당량](#regional-capacity-quota)   |  25TiB  |  Yes  |
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
|  지역 간 복제 데이터 보호 볼륨(대상 볼륨) 수     |    5    |    예    |     

자세한 내용은 [용량 관리 FAQ](azure-netapp-files-faqs.md#capacity-management-faqs)를 참조하세요.

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

볼륨에 이미 4TiB 이상의 할당량을 할당한 경우 [지원 요청](#limit_increase)을 시작하여 maxfiles(inodes) 한도를 1억 개 이상으로 늘릴 수 있습니다. 1억 개 파일(또는 그 일부)마다 해당 볼륨 할당량을 4TiB씩 늘려야 합니다.  예를 들어 maxfiles 한도를 1억 개에서 2억 개 파일(또는 그 사이에 있는 모든 수)로 늘리면 볼륨 할당량을 4TiB에서 8TiB로 늘려야 하는 것입니다.

볼륨 할당량이 20TiB 이상인 경우 maxfiles 한도를 5억 개로 늘릴 수 있습니다. <!-- ANF-11854 --> 

## <a name="regional-capacity-quota"></a>지역 용량 할당량

Azure NetApp Files에는 용량에 따라 지역 제한이 있습니다. 각 구독에 대한 표준 용량 제한은 모든 서비스 수준에서 지역당 25TiB입니다.   

다음과 같이 특정 **서비스 및 구독 한도(할당량)** 지원 티켓을 제출하여 용량 증가를 요청할 수 있습니다.

1. 포털에서 **지원 + 문제 해결** 로 이동하여 지원 요청 프로세스를 시작합니다.  

    ![지원 문제 해결 메뉴를 보여 주는 스크린샷.](../media/azure-netapp-files/support-troubleshoot-menu.png)   

2.  **서비스 및 구독 한도(할당량)** 문제 유형을 선택하고 모든 관련 세부 정보를 입력합니다.

    ![서비스 및 구독 제한 메뉴를 보여 주는 스크린샷.](../media/azure-netapp-files/service-subscription-limits-menu.png)   

3. 세부 정보 탭에서 **세부 정보 입력** 링크를 클릭한 다음 **구독당 TiB** 할당량 유형을 선택합니다.   

    ![세부 정보 탭의 세부 정보 입력 링크를 보여 주는 스크린샷.](../media/azure-netapp-files/support-details.png)   

    ![할당량 세부 정보 창을 보여 주는 스크린샷.](../media/azure-netapp-files/support-quota-details.png)   

4.  지원 방법 페이지에서 **심각도 수준 B - 보통 영향** 을 선택해야 합니다.  

    ![지원 방법 창을 보여 주는 스크린샷.](../media/azure-netapp-files/support-method-severity.png)   

5. 요청 프로세스를 완료하여 요청을 발행합니다. 
 
티켓이 제출되면 처리를 위해 요청이 Azure 용량 관리 팀으로 전송됩니다. 일반적으로 영업일 기준 2일 이내에 응답을 받게 됩니다. Azure 용량 관리 팀에서 대규모 요청을 처리하기 위해 연락할 수 있습니다.
 
지역 용량 할당량 증가는 청구 증가를 발생시키지 않습니다. 요금은 여전히 프로비저닝된 용량 풀을 기반으로 합니다.

## <a name="request-limit-increase"></a>요청 한도 증가 <a name="limit_increase"></a> 

Azure 지원 요청을 만들어 [리소스 제한](#resource-limits) 표에서 조정 가능한 한도를 늘릴 수 있습니다. 

Azure Portal 탐색 화면에서: 

1. **도움말 + 지원** 을 클릭합니다.
2. **+ 새 지원 요청** 을 클릭합니다.
3. 기본 탭에서 다음 정보를 제공합니다. 
    1. 문제 유형: **서비스 및 구독 한도(할당량)** 를 선택합니다.
    2. 구독: 할당량을 늘려야 하는 리소스의 구독을 선택합니다.
    3. 할당량 유형: **스토리지: Azure NetApp Files 한도** 를 선택합니다.
    4. **다음: 솔루션** 을 클릭합니다.
4. 세부 정보 탭에서:
    1. 아래 표에서 해당 리소스 종류에 대한 다음 정보를 제공합니다.

        |  리소스  |    부모 리소스      |    요청된 새 제한     |    할당량 증가 이유       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  계정 |  *구독 ID*   |  요청된 새 최대 **계정** 수    |  어떤 시나리오 또는 사용 사례를 계기로 요청하게 되었나요?  |
        |  풀    |  구독 ID, NetApp 계정 URI  |  요청된 새 최대 **풀** 수   |  어떤 시나리오 또는 사용 사례를 계기로 요청하게 되었나요?  |
        |  볼륨  |  구독 ID, NetApp 계정 URI, 용량 풀 URI   |  요청된 새 최대 **볼륨** 수     |  어떤 시나리오 또는 사용 사례를 계기로 요청하게 되었나요?  |
        |  Maxfiles  |  구독 ID, NetApp 계정 URI, 용량 풀 URI, 볼륨 URI   |  요청된 새 최대 **maxfiles** 수     |  어떤 시나리오 또는 사용 사례를 계기로 요청하게 되었나요?  |    
        |  지역 간 복제 데이터 보호 볼륨  |  구독 ID, 대상 NetApp 계정 URI, 대상 용량 풀 URI, 원본 NetApp 계정 URI, 원본 용량 풀 URI, 원본 볼륨 URI   |  *요청된 새 최대 **지역 간 복제 데이터 보호 볼륨(대상 볼륨)** 수_     |  _어떤 시나리오 또는 사용 사례를 계기로 요청하게 되었나요?*  |    

    2. 적절한 지원 방법을 지정하고 계약 정보를 제공합니다.

    3. **다음: 검토 + 만들기** 를 클릭하여 요청을 만듭니다. 


## <a name="next-steps"></a>다음 단계  

- [Azure NetApp Files의 스토리지 계층 구조 이해](azure-netapp-files-understand-storage-hierarchy.md)
- [Azure NetApp Files 비용 모델](azure-netapp-files-cost-model.md)
