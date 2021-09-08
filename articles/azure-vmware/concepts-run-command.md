---
title: 개념 - Azure VMware Solution에서 실행 명령
description: Azure VMware Solution에서 실행 명령을 사용하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 08/31/2021
ms.openlocfilehash: 28cbf76dd035ce9b04909a61e3001063aa151162
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123310675"
---
# <a name="run-commands-in-azure-vmware-solution"></a>Azure VMware Solution의 실행 명령

Azure VMware Solution에서는 CloudAdmin 역할을 사용하여 vCenter에 액세스할 수 있습니다. Azure VMware Solution 프라이빗 클라우드 vCenter의 Azure VMware Solution CloudAdmin 역할에 [부여된 권한을 볼 수 있습니다](concepts-identity.md#view-the-vcenter-privileges). 실행 명령은 상승된 권한이 필요한 vCenter에서 특정 작업을 수행하는 PowerShell cmdlet 모음입니다. 

Azure VMware Solution은 다음 작업을 지원합니다.

- [JetStream DR 솔루션 설치 및 제거](deploy-disaster-recovery-using-jetstream.md)

- [외부 ID 원본 구성](configure-identity-source-vcenter.md)

- [스토리지 정책 보기 및 수정](configure-storage-policy.md) 


>[!NOTE]
>실행 명령은 제출된 순서대로 한 번에 하나씩 실행됩니다.

## <a name="view-the-status-of-an-execution"></a>실행 상태 보기

출력, 오류, 경고 및 정보를 포함하여 실행된 모든 실행 명령의 상태를 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **실행 명령** > **실행 상태** 를 선택합니다.

   실행 이름, 패키지 이름, 패키지 버전, 명령 이름, 시작 시간, 종료 시간 및 상태별로 정렬할 수 있습니다.  

   :::image type="content" source="media/run-command/run-execution-status.png" alt-text="실행 상태 탭을 보여 주는 스크린샷." lightbox="media/run-command/run-execution-status.png":::

1. 보고자 하는 실행을 선택합니다.

   :::image type="content" source="media/run-command/run-execution-status-example.png" alt-text="실행의 예를 보여 주는 스크린샷.":::

   출력, 오류, 경고 및 정보를 포함하여 실행에 대한 자세한 내용을 볼 수 있습니다.

   - **세부 정보** - 이름, 상태, 패키지, 실행된 명령 이름과 같은 실행 세부 정보 요약입니다. 

   - **출력** - cmdlet이 성공적으로 실행되었을 때의 메시지입니다. 모든 cmdlet에 출력이 있는 것은 아닙니다.

      :::image type="content" source="media/run-command/run-execution-status-example-output.png" alt-text="실행의 출력을 보여 주는 스크린샷.":::

   - **오류** - cmdlet 실행을 중지한 종료 예외입니다.    

      :::image type="content" source="media/run-command/run-execution-status-example-error.png" alt-text="실행의 실행 중에 감지된 오류를 보여 주는 스크린샷.":::

   - **경고** - cmdlet을 실행하는 동안 종료되지 않는 예외가 발생했습니다. 

      :::image type="content" source="media/run-command/run-execution-status-example-warning.png" alt-text="실행의 실행 중에 감지된 경고를 보여 주는 스크린샷.":::

   - **정보** - cmdlet 실행 중의 진행 메시지입니다. 

      :::image type="content" source="media/run-command/run-execution-status-example-information.png" alt-text="실행되는 cmdlet의 전체 실시간 진행 상황을 보여 주는 스크린샷.":::



## <a name="cancel-or-delete-a-job"></a>작업 취소 또는 삭제



### <a name="method-1"></a>방법 1

>[!NOTE]
>방법 1은 되돌릴 수 없습니다.

1. **실행 명령** > **실행 상태** 를 선택한 다음 취소할 작업을 선택합니다.

   :::image type="content" source="media/run-command/run-execution-cancel-delete-job-method-1.png" alt-text="실행 명령을 취소하고 삭제하는 방법을 보여 주는 스크린샷.":::

2. 모든 사용자의 작업을 취소하고 제거하려면 **예** 를 선택합니다.



### <a name="method-2"></a>방법 2

1. **실행 명령** > **패키지** > **실행 상태** 를 선택합니다.

2. 취소 및 삭제하려는 작업에 대해 **기타**(...)를 선택합니다.

   :::image type="content" source="media/run-command/run-execution-cancel-delete-job-method-2.png" alt-text="줄임표를 사용하여 실행 명령을 취소하고 삭제하는 방법을 보여 주는 스크린샷":::

3. 모든 사용자의 작업을 취소하고 제거하려면 **예** 를 선택합니다.



## <a name="next-steps"></a>다음 단계

이제 실행 명령 개념에 대해 배웠으므로 실행 명령 기능을 사용하여 다음을 수행할 수 있습니다.

- [스토리지 정책 구성](configure-storage-policy.md) - vSAN 데이터 저장소에 배포된 각 VM에는 하나 이상의 VM 스토리지 정책이 할당됩니다. VM의 초기 배포에서 또는 복제 또는 마이그레이션과 같은 다른 VM 작업을 수행할 때 VM 스토리지 정책을 할당할 수 있습니다.

- [vCenter에 대한 외부 ID 원본 구성](configure-identity-source-vcenter.md) - vCenter에는 cloudadmin이라는 기본 제공 로컬 사용자가 있으며 CloudAdmin 역할에 할당됩니다. 로컬 cloudadmin 사용자는 AD(Active Directory)에서 사용자를 설정하는 데 사용 됩니다. 실행 명령 기능을 사용하면 vCenter에 대한 LDAP 또는 LDAPS를 통한 Active Directory를 외부 ID 원본으로 구성할 수 있습니다.

- [JetStream을 사용하여 재해 복구 배포](deploy-disaster-recovery-using-jetstream.md) - vSAN의 복구 클러스터에 직접 데이터를 저장합니다. 데이터는 vSphere 내에서 실행되는 I/O 필터를 통해 캡처됩니다. 기본 데이터 저장소는 VMFS, VSAN, vVol 또는 모든 HCI 플랫폼일 수 있습니다. 
