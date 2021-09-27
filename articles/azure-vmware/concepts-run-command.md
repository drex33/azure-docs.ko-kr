---
title: 개념 - Azure VMware Solution 명령 실행(미리 보기)
description: Azure VMware Solution에서 실행 명령을 사용하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 09/17/2021
ms.openlocfilehash: a4121f8479b22eb1c0666a1e7d7a23ece4fb3d20
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128668852"
---
# <a name="run-command-in-azure-vmware-solution-preview"></a>Azure VMware Solution 명령 실행(미리 보기)

Azure VMware Solution에서 vCenter는 cloudadmin이라는 기본 제공 로컬 사용자를 포함하고 *cloudadmin* 역할에 할당됩니다. CloudAdmin 역할에는 다른 VMware 클라우드 솔루션 및 온-프레미스 배포와 다른 vCenter [권한이](concepts-identity.md#view-the-vcenter-privileges) 있습니다. 실행 명령(미리 보기) 기능을 사용하면 일반적으로 PowerShell cmdlet 컬렉션을 통해 상승된 권한이 필요한 작업을 수행할 수 있습니다. 

Azure VMware Solution은 다음 작업을 지원합니다.

- [외부 ID 원본 구성](configure-identity-source-vcenter.md)

- [스토리지 정책 보기 및 설정](configure-storage-policy.md) 

- [JetStream을 사용하여 재해 복구 배포](deploy-disaster-recovery-using-jetstream.md)


>[!NOTE]
>실행 명령은 제출된 순서대로 한 번에 하나씩 실행됩니다.

## <a name="view-the-status-of-an-execution"></a>실행 상태 보기

cmdlet의 출력, 오류, 경고 및 정보 로그를 포함하여 실행된 실행 명령의 상태를 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **실행 명령** > **실행 상태** 를 선택합니다.

   열을 선택하여 다양한 열을 정렬할 수 있습니다.  

   :::image type="content" source="media/run-command/run-execution-status.png" alt-text="실행 상태 탭을 보여 주는 스크린샷." lightbox="media/run-command/run-execution-status.png":::

1. 보고자 하는 실행을 선택합니다. 실행에 대한 세부 정보 및 cmdlet에서 생성된 다양한 유형의 출력에 대한 기타 탭이 있는 창이 열립니다.

   :::image type="content" source="media/run-command/run-execution-status-example.png" alt-text="실행의 예를 보여 주는 스크린샷.":::

   출력, 오류, 경고 및 정보를 포함하여 실행에 대한 자세한 내용을 볼 수 있습니다.

   - **세부 정보** - 이름, 상태, 패키지, cmdlet 이름 및 명령이 실패한 경우 오류와 같은 실행 세부 정보의 요약입니다. 

   - **출력** - cmdlet에서 출력하는 메시지입니다. 진행률 또는 작업의 결과를 포함할 수 있습니다. 모든 cmdlet에 출력이 있는 것은 아닙니다.

      :::image type="content" source="media/run-command/run-execution-status-example-output.png" alt-text="실행의 출력을 보여 주는 스크린샷.":::

   - **오류** - cmdlet 실행에서 생성된 오류 메시지입니다. 세부 정보 창의 종료 오류 메시지에 추가됩니다.    

      :::image type="content" source="media/run-command/run-execution-status-example-error.png" alt-text="실행의 실행 중에 감지된 오류를 보여 주는 스크린샷.":::

   - **경고** - 실행 중에 생성된 경고 메시지입니다. 

      :::image type="content" source="media/run-command/run-execution-status-example-warning.png" alt-text="실행의 실행 중에 감지된 경고를 보여 주는 스크린샷.":::

   - **정보** - cmdlet을 실행 하는 동안 진행률 및 진단 생성 된 메시지입니다. 

      :::image type="content" source="media/run-command/run-execution-status-example-information.png" alt-text="실행되는 cmdlet의 전체 실시간 진행 상황을 보여 주는 스크린샷.":::



## <a name="cancel-or-delete-a-job"></a>작업 취소 또는 삭제



### <a name="method-1"></a>방법 1

이 메서드는 실행을 취소한 다음 완료 시 삭제합니다.

>[!IMPORTANT]
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

- [스토리지 정책 구성](configure-storage-policy.md) - vSAN 데이터 저장소에 배포된 각 VM에 vSAN 스토리지 정책이 할당됩니다. VM의 초기 배포에서 또는 복제 또는 마이그레이션과 같은 다른 VM 작업을 수행할 때 vSAN 스토리지 정책을 할당할 수 있습니다.

- [vCenter에 대한 외부 ID 원본 구성(명령 실행)](configure-identity-source-vcenter.md) - 외부 ID 원본을 Active Directory로 사용할 수 있도록 vCenter에 대해 LDAP 또는 LDAPS를 통해 Active Directory를 구성합니다. 그런 다음 외부 ID 원본의 그룹을 CloudAdmin 역할에 추가할 수 있습니다.

- [JetStream을 사용하여 재해 복구 배포](deploy-disaster-recovery-using-jetstream.md) - vSAN의 복구 클러스터에 직접 데이터를 저장합니다. 데이터는 vSphere 내에서 실행되는 I/O 필터를 통해 캡처됩니다. 기본 데이터 저장소는 VMFS, VSAN, vVol 또는 모든 HCI 플랫폼일 수 있습니다. 