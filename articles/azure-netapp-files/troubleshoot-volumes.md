---
title: Azure NetApp Files에 대 한 볼륨 오류 문제 해결 | Microsoft Docs
description: Azure NetApp Files 볼륨 문제를 해결 하는 데 도움이 될 수 있는 오류 메시지 및 해결 방법에 대해 설명 합니다.
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
ms.topic: troubleshooting
ms.date: 10/04/2021
ms.author: b-juche
ms.openlocfilehash: a9df7577e1426fb355995cd4486338003a753c86
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129539027"
---
# <a name="troubleshoot-volume-errors-for-azure-netapp-files"></a>Azure NetApp Files에 대 한 볼륨 오류 문제 해결

이 문서에서는 Azure NetApp Files 볼륨 문제를 해결 하는 데 도움이 될 수 있는 오류 메시지 및 해결 방법을 설명 합니다. 

## <a name="errors-for-volume-allocation"></a>볼륨 할당에 대 한 오류 

Azure NetApp Files에서 새 볼륨을 만들거나 기존 볼륨의 크기를 조정 하는 경우 Microsoft Azure는 구독에 저장소 및 네트워킹 리소스를 할당 합니다. 특정 지역에서 Azure 서비스에 대 한 수요가 증가 함에 따라 리소스 할당 오류가 발생할 수 있습니다.

이 섹션에서는 일반적인 할당 오류의 원인을 설명 하 고 가능한 해결 방법을 제안 합니다.

|     오류 조건    |     해결 방법    |
|-|-|
|새 볼륨을 만들거나 기존 볼륨의 크기를 조정 하는 동안 오류가 발생 했습니다. <br> 오류 메시지: `There was a problem locating [or extending] storage  for the volume. Please retry the operation. If the problem persists, contact Support.` | 오류는이 요청에 대 한 리소스를 할당 하려고 할 때 서비스에서 오류가 발생 했음을 나타냅니다. <br> 잠시 후 작업을 다시 시도하세요. 문제가 계속 되 면 지원 담당자에 게 문의 하세요.|
|일반 볼륨의 지역에서 저장소 또는 네트워킹 용량이 부족 합니다. <br> 오류 메시지: `There are currently insufficient resources available to create [or extend] a volume in this region. Please retry the operation. If the problem persists, contact Support.` | 이 오류는 영역에서 볼륨을 만들거나 크기를 조정 하는 데 사용할 수 있는 리소스가 부족 함을 나타냅니다. <br> 다음 해결 방법 중 하나를 시도해 보세요. <ul><li>새 VNet에서 볼륨을 만듭니다. 이렇게 하면 네트워킹 관련 리소스 제한에 도달 하지 않습니다.</li> <li>잠시 후 다시 시도 하세요. 리소스가 일시적으로 클러스터, 지역 또는 영역에서 해제 되었을 수 있습니다.</li></ul> |
|네트워크 기능이로 설정 된 볼륨을 만들 때 저장소 용량이 부족 `Standard` 합니다. <br> 오류 메시지: `No storage available with Standard network features, for the provided VNet.` | 이 오류는 네트워킹 기능을 사용 하 여 볼륨을 만들기 위해 지역에 사용할 수 있는 리소스가 부족 함을 나타냅니다 `Standard` . <br> 다음 해결 방법 중 하나를 시도해 보세요. <ul><li>`Standard`네트워크 기능이 필요 하지 않은 경우 네트워크 기능을 사용 하 여 볼륨을 만듭니다 `Basic` .</li> <li>새 VNet에서 볼륨을 만들어 보세요. 이렇게 하면 네트워킹 관련 리소스 제한에 도달 하지 않습니다.</li><li>잠시 후 다시 시도 하세요.  리소스가 일시적으로 클러스터, 지역 또는 영역에서 해제 되었을 수 있습니다.</li></ul> |

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files에 대한 NFS 볼륨 만들기](azure-netapp-files-create-volumes.md)
* [Azure NetApp Files에 대한 SMB 볼륨 만들기](azure-netapp-files-create-volumes-smb.md) 
* [Azure NetApp Files에 대한 이중 프로토콜 볼륨 만들기](create-volumes-dual-protocol.md) 
* [볼륨의 네트워크 기능 구성](configure-network-features.md)
