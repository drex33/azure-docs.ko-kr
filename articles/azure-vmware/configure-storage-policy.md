---
title: 스토리지 정책 구성
description: Azure VMware Solution 가상 머신에 대한 스토리지 정책을 구성하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 08/31/2021
ms.openlocfilehash: df8f6931dbae377833d3f380e3fd5fcad8bd73ba
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431335"
---
# <a name="configure-storage-policy"></a>스토리지 정책 구성

vSAN 스토리지 정책은 VM(가상 머신)에 대한 스토리지 요구 사항을 정의합니다. 이러한 정책은 VM에 스토리지를 할당하는 방법을 결정하기 때문에 VM에 필요한 서비스 수준을 보장합니다. vSAN 데이터 저장소에 배포된 각 VM에는 하나 이상의 VM 스토리지 정책이 할당됩니다.

VM의 초기 배포에서 복제 또는 마이그레이션과 같은 다른 VM 작업을 수행할 때 VM 스토리지 정책을 할당할 수 있습니다. 배포 후 cloudadmin 사용자나 그와 동등한 역할은 VM에 대한 기본 스토리지 정책을 변경할 수 없습니다. 그러나 디스크 변경에 따라 **VM 스토리지 정책** 이 허용됩니다. 

실행 명령을 사용하면 권한 있는 사용자가 기본 또는 기존 VM 스토리지 정책을 VM 배포 후에 사용할 수 있는 정책으로 변경할 수 있습니다. 디스크 수준 VM 스토리지 정책에 대한 변경 사항은 없습니다. 요구 사항에 따라 언제든지 디스크 수준 VM 스토리지 정책을 변경할 수 있습니다.


>[!NOTE]
>실행 명령은 제출된 순서대로 한 번에 하나씩 실행됩니다.


이 방법에서는 다음을 수행하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 모든 스토리지 정책 나열
> * VM에 대한 스토리지 정책 설정
> * 클러스터에 대한 스토리지 정책 지정



## <a name="prerequisites"></a>필수 조건

[최소 호스트 수준이 충족](https://docs.vmware.com/en/VMware-Cloud-on-AWS/services/com.vmware.vsphere.vmc-aws-manage-data-center-vms.doc/GUID-EDBB551B-51B0-421B-9C44-6ECB66ED660B.html)되는지 확인합니다.

|  **RAID 구성** | **FTT(허용 실패)** | **필요한 최소 호스트** |
| --- | :---: | :---: |
| RAID-1(미러링) <br />기본 설정입니다.  | 1  | 3  |
| RAID-5(이레이져 코딩)  | 1  | 4  |
| RAID-1(미러링)  | 2  | 5  |
| RAID-6(이레이져 코딩)  | 2  | 6  |
| RAID-1(미러링)  | 3  | 7  |


 

## <a name="list-storage-policies"></a>스토리지 정책 나열

`Get-StoragePolicy` cmdlet을 실행하여 VM에서 설정할 수 있는 vSAN 기반 스토리지 정책을 나열합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **실행 명령** > **패키지** > **Get-StoragePolicies** 를 선택합니다.

   :::image type="content" source="media/run-command/run-command-overview-storage-policy.png" alt-text="사용 가능한 스토리지 정책 실행 명령을 액세스하는 방법을 보여주는 스크린샷입니다." lightbox="media/run-command/run-command-overview-storage-policy.png":::

1. 필요한 값을 제공하거나 기본값을 변경한 다음 **실행** 을 선택합니다.

   :::image type="content" source="media/run-command/run-command-get-storage-policy.png" alt-text="사용 가능한 스토리지 정책을 나열하는 방법을 보여주는 스크린샷입니다.":::
   
   | **필드** | **값** |
   | --- | --- |
   | **다음까지 유지**  | cmdlet 출력의 보존 기간입니다. 기본값은 60입니다.  |
   | **실행할 이름 지정**  | 영숫자 이름(예: **Get-StoragePolicies-Exec1**)입니다. |
   | **Timeout**  |  완료하는 데 너무 오래 걸릴 경우 cmdlet이 종료되는 기간입니다.  |

1. 진행률을 보려면 **알림** 을 선택합니다.




## <a name="set-storage-policy-on-vm"></a>VM에서 스토리지 정책 설정

`Set-AvsVMStoragePolicy` cmdlet을 실행하여 개별 VM에서 vSAN 기반 스토리지 정책을 수정합니다. 

>[!NOTE]
>vSphere 클라이언트를 사용하여 기본 스토리지 정책 또는 VM에 대한 기존 스토리지 정책을 변경할 수 없습니다. 

1. **실행 명령** > **패키지** > **Set-AvsVMStoragePolicy** 를 선택합니다.

1. 필요한 값을 제공하거나 기본값을 변경한 다음 **실행** 을 선택합니다.

   | **필드** | **값** |
   | --- | --- |
   | **VMName** | 대상 VM의 이름입니다. |
   | **StoragePolicyName** | 설정할 스토리지 정책의 이름입니다. 예를 들어, **RAID-FTT-1** 입니다. |
   | **다음까지 유지**  | cmdlet 출력의 보존 기간입니다. 기본값은 60입니다.  |
   | **실행할 이름 지정**  | 영숫자 이름(예: **changeVMStoragePolicy**)입니다.  |
   | **Timeout**  |  완료하는 데 너무 오래 걸릴 경우 cmdlet이 종료되는 기간입니다.  |

1. 진행률을 보려면 **알림** 을 선택합니다.


## <a name="specify-storage-policy-for-a-cluster"></a>클러스터에 대한 스토리지 정책 지정

`Set-ClusterDefaultStoragePolicy` cmdlet을 실행하여 클러스터에 대한 기본 스토리지 정책을 지정합니다.

>[!NOTE]
>기본 관리 클러스터의 스토리지 정책 변경은 허용되지 않습니다.

1. **실행 명령** > **패키지** > **Set-ClusterDefaultStoragePolicy** 를 선택합니다.

1. 필요한 값을 제공하거나 기본값을 변경한 다음 **실행** 을 선택합니다.

   | **필드** | **값** |
   | --- | --- |
   | **ClusterName** | 클러스터의 이름입니다. |
   | **StoragePolicyName** | 설정할 스토리지 정책의 이름입니다. 예를 들어, **RAID-FTT-1** 입니다. |
   | **다음까지 유지**  | cmdlet 출력의 보존 기간입니다. 기본값은 60입니다.  |
   | **실행할 이름 지정**  | 영숫자 이름(예: **Set-ClusterDefaultStoragePolicy-Exec1**)입니다.  |
   | **Timeout**  |  완료하는 데 너무 오래 걸릴 경우 cmdlet이 종료되는 기간입니다.  |

1. 진행률을 보려면 **알림** 을 선택합니다.



## <a name="next-steps"></a>다음 단계

이제 vSAN 스토리지 정책을 구성하는 방법을 알아보았으므로 다음에 대해 자세히 알아볼 수 있습니다.

- [Azure VMware Solution 호스트에 디스크 풀을 연결하는 방법(미리 보기)](attach-disk-pools-to-azure-vmware-solution-hosts.md) - 디스크를 Azure VMware Solution에 대한 영구적 저장소로 사용하여 최적의 비용 및 성능을 얻을 수 있습니다.

- [vCenter에 대한 외부 ID를 구성하는 방법](configure-identity-source-vcenter.md) - vCenter에는 cloudadmin이라는 기본 제공 로컬 사용자가 있으며 CloudAdmin 역할에 할당됩니다. 로컬 cloudadmin 사용자는 AD(Active Directory)에서 사용자를 설정하는 데 사용 됩니다. 실행 명령 기능을 사용하면 vCenter에 대한 LDAP 또는 LDAPS를 통한 Active Directory를 외부 ID 원본으로 구성할 수 있습니다.
