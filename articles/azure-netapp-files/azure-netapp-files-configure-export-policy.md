---
title: Azure NetApp Files NFS 또는 이중 프로토콜 볼륨에 대한 내보내기 정책 구성 - Azure NetApp Files
description: Azure NetApp Files를 사용하여 NFS 볼륨에 대한 액세스를 제어하도록 내보내기 정책을 구성하는 방법을 설명합니다.
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 10/11/2021
ms.openlocfilehash: 2412673dd71c6fbe6a1f070451db24d748694a8d
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129811131"
---
# <a name="configure-export-policy-for-nfs-or-dual-protocol-volumes"></a>NFS 또는 이중 프로토콜 볼륨에 대한 내보내기 정책 구성

NFS 프로토콜(NFSv3 및 NFSv4.1) 또는 이중 프로토콜(NFSv3 및 SMB 또는 NFSv4.1 및 SMB)을 사용하는 Azure NetApp Files 볼륨에 대한 액세스를 제어하도록 내보내기 정책을 구성할 수 있습니다. 

최대 5개의 내보내기 정책 규칙을 만들 수 있습니다.

## <a name="configure-the-policy"></a>정책 구성 

1.  **볼륨** 페이지에서 내보내기 정책을 구성할 볼륨을 선택하고 **내보내기 정책** 을 선택합니다. 볼륨을 만드는 동안 내보내기 정책을 구성할 수도 있습니다.

2.  내보내기 정책 규칙을 만들려면 다음 정보를 지정합니다.   
    * **인덱스**: 규칙에 대한 인덱스 번호를 지정합니다.  
      
      내보내기 정책은 최대 5개의 규칙으로 구성할 수 있습니다. 규칙은 인덱스 번호 목록의 해당 순서에 따라 평가됩니다. 더 낮은 인덱스 번호의 규칙이 먼저 평가됩니다. 예를 들어 인덱스 번호가 1인 규칙은 인덱스 번호가 2인 규칙보다 먼저 계산됩니다. 

    * **허용된 클라이언트**: 다음 형식 중 하나에 값을 지정합니다.  
      * IPv4 주소. 예: `10.1.12.24`
      * 비트 수로 표현된 서브넷 마스크가 있는 IPv4 주소. 예: `10.1.12.10/4`
      * 쉼표로 구분된 IP 주소. 여러 호스트 IP를 쉼표로 구분하여 단일 규칙으로 입력할 수 있습니다. 길이 제한은 4096자입니다. 예: `10.1.12.25,10.1.12.28,10.1.12.29`

    * **액세스**: 다음 액세스 유형 중 하나를 선택합니다.  
      * 액세스 권한 없음 
      * 읽기 및 쓰기
      * 읽기 전용

    * **읽기 전용** 및 **읽기/쓰기**: NFSv4.1에서 Kerberos 암호화를 사용하는 경우 [NFSv4.1 Kerberos 암호화 구성](configure-kerberos-encryption.md)의 지침을 따릅니다.  Kerberos가 성능에 미치는 영향을 보려면 [Performance impact of Kerberos on NFSv4.1 volumes](performance-impact-kerberos.md)(NFSv4.1 볼륨에서 Kerberos가 성능에 미치는 영향)를 참조하세요. 

      ![Kerberos 보안 옵션](../media/azure-netapp-files/kerberos-security-options.png) 

    * **루트 액세스**: `root` 계정에서 볼륨에 액세스할 수 있는지를 지정합니다.  기본적으로 루트 액세스는 **설정** 으로 지정되고 `root` 계정에는 볼륨에 대한 액세스 권한이 있습니다.  NFSv 4.1 Kerberos 볼륨에는이 옵션을 사용할 수 없습니다.

      ![내보내기 정책](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 

    * **Chown 모드**: 필요에 따라 소유권 변경 모드를 수정하여 파일 및 디렉터리 소유권 관리 기능을 설정합니다.  2가지 옵션을 사용할 수 있습니다.   

      * `Restricted`(기본값) - 루트 사용자만 파일 및 디렉터리의 소유권을 변경할 수 있습니다.
      * `Unrestricted` - 루트가 아닌 사용자는 자신이 소유하는 파일 및 디렉터리에 대한 소유권을 변경할 수 있습니다.  

        등록 요구 사항 및 고려 사항은 **`Chown Mode`** 설정에 적용됩니다. [Unix 권한 구성 및 소유권 모드 변경](configure-unix-permissions-change-ownership-mode.md)의 지침을 따릅니다.  

      ![소유권 모드 변경 옵션을 보여주는 스크린샷.](../media/azure-netapp-files/chown-mode-export-policy.png) 

## <a name="next-steps"></a>다음 단계 
* [볼륨 탑재 또는 분리](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Unix 권한 구성 및 소유권 모드 변경](configure-unix-permissions-change-ownership-mode.md) 
* [스냅샷 관리](azure-netapp-files-manage-snapshots.md)
