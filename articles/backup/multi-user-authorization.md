---
title: Resource Guard를 사용한 다중 사용자 권한 부여
description: 리소스 가드를 사용 하는 다중 사용자 권한 부여에 대 한 개요입니다.
ms.topic: how-to
ms.date: 10/20/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 02380588d4c238fe293027423969460aa0c62738
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132707645"
---
# <a name="multi-user-authorization-using-resource-guard-preview"></a>리소스 가드 (미리 보기)를 사용 하는 다중 사용자 권한 부여

Azure Backup에 대 한 MUA (다중 사용자 권한 부여)를 사용 하면 Recovery Services 자격 증명 모음에서 중요 한 작업에 대 한 추가 보호 계층을 추가할 수 있습니다. MUA의 경우, Azure Backup는 리소스 가드 라는 다른 Azure 리소스를 사용 하 여 중요 한 작업이 적용 가능한 권한 부여로만 수행 되도록 합니다.

이 문서에는 다음이 포함 되어 있습니다.

- 리소스 가드 MUA를 사용 하는 방법
- 시작하기 전에
- 테스트 시나리오
- 리소스 가드 만들기
- Recovery Services 자격 증명 모음에서 MUA 사용
- 자격 증명 모음에 대 한 무단 작업 으로부터 보호
- 자격 증명 모음에 대 한 중요 한 작업 권한 부여
- Recovery Services 자격 증명 모음에서 MUA 사용 안 함

>[!NOTE]
>- 백업에 대 한 다중 사용자 권한 부여는 현재 미리 보기 상태 이며 모든 공용 Azure 지역에서 사용할 수 있습니다.
>- 2021 년 11 월 2 일 이전에 preview를 사용 하는 경우 시작 하려면 Azure Portal의 **Microsoft RecoveryServices** 공급자에서 미리 **보기 기능을** 사용 하 여 구독을 등록 합니다.

## <a name="how-does-mua-for-backup-work"></a>백업에 대 한 MUA는 어떻게 작동 하나요?

Azure Backup는 리소스 가드를 Recovery Services 자격 증명 모음에 대 한 권한 부여 서비스로 사용 합니다. 따라서 중요 한 작업 (아래 설명)을 수행 하려면 연결 된 리소스 보호에 대 한 충분 한 권한이 있어야 합니다.

> [!Important]
> 의도 한 대로 작동 하려면 다른 사용자가 리소스 가드를 소유 해야 하 고 자격 증명 모음 관리자에 게 참가자 권한이 없어야 합니다. Recovery Services 자격 증명 모음을 포함 하는 것과 다른 구독 또는 테 넌 트에 리소스 가드를 추가 하 여 더 나은 보호를 제공할 수 있습니다.

### <a name="critical-operations"></a>중요 한 작업

다음 표에서는 중요 한 작업으로 정의 되 고 리소스 가드로 보호할 수 있는 작업을 보여 줍니다. 자격 증명 모음을 연결 하는 경우 리소스 가드를 사용 하 여 특정 작업을 보호 하지 않도록 선택할 수 있습니다. 필수로 표시 된 작업은 연결 된 자격 증명 모음에 대 한 리소스 가드를 사용 하 여 보호 되지 않도록 제외할 수 없습니다. 또한 제외 된 중요 작업은 리소스 보호와 연결 된 모든 자격 증명 모음에 적용 됩니다.

**연산** | **필수/선택 사항**
--- | ---
일시 삭제 사용 안 함 | 필수
MUA 보호 사용 안 함 | 필수
백업 정책 수정 | 선택 사항: 제외 가능
보호 수정 | 선택 사항: 제외 가능
보호 중지 | 선택 사항: 제외 가능
MARS 보안 PIN 변경 | 선택 사항: 제외 가능

### <a name="concepts"></a>개념
백업에 MUA를 사용 하는 경우와 관련 된 개념과 프로세스는 아래에 설명 되어 있습니다.

프로세스 및 책임을 명확 하 게 이해 하기 위해 다음과 같은 두 사용자를 살펴보겠습니다. 이러한 두 역할은이 문서 전체에서 참조 됩니다.

**Backup admin**: Recovery Services 자격 증명 모음의 소유자 이며 자격 증명 모음에 대 한 관리 작업을 수행 합니다. 시작 하려면 백업 관리자에 게 리소스 보호에 대 한 권한이 없어야 합니다.

**보안 관리자**: 리소스 가드의 소유자 이며 자격 증명 모음에 대 한 중요 한 작업의 게이트 키퍼 역할을 합니다. 따라서 보안 관리자는 백업 관리자가 자격 증명 모음에서 중요 한 작업을 수행 하는 데 필요한 권한을 제어 합니다.

다음은 리소스 가드를 사용 하 여 구성 된 MUA 자격 증명 모음에서 중요 한 작업을 수행 하기 위한 다이어그램 표현입니다.

:::image type="content" source="./media/multi-user-authorization/configure-mua-using-resource-card-diagram.png" alt-text="리소스 가드를 사용 하 여 MUA를 구성 하는 방법에 대 한 다이어그램 표현입니다.":::
 

다음은 일반적인 시나리오에서 발생 하는 이벤트의 흐름입니다.

1. 백업 관리자는 Recovery Services 자격 증명 모음을 만듭니다.
1. 보안 관리자가 리소스 가드를 만듭니다. 리소스 가드는 다른 구독 또는 다른 테 넌 트 인 Recovery Services 자격 증명 모음에 있을 수 있습니다. 백업 관리자에 게 리소스 보호에 대 한 참가자 권한이 없는지 확인 해야 합니다.
1. 보안 관리자는 리소스 보호 (또는 관련 범위)에 대 한 백업 관리자에 게 **읽기 권한자** 역할을 부여 합니다. 백업 관리자는 자격 증명 모음에 대해 MUA를 사용 하도록 설정 하려면 읽기 권한자 역할이 필요 합니다.
1. 이제 백업 관리자는 리소스 가드를 통해 MUA로 보호 되도록 Recovery Services 자격 증명 모음을 구성 합니다.
1. 이제 백업 관리자가 자격 증명 모음에서 중요 한 작업을 수행 하려는 경우 리소스 보호에 대 한 액세스를 요청 해야 합니다. 이러한 작업을 수행 하기 위한 액세스 권한을 얻는 방법에 대 한 자세한 내용은 백업 관리자가 보안 관리자에 게 문의할 수 있습니다. Privileged Identity Management (PIM) 또는 조직에서 제공한 다른 프로세스를 사용 하 여이 작업을 수행할 수 있습니다.
1. 보안 관리자는 리소스 보호에 대 한 **참가자** 역할을 일시적으로 백업 관리자에 게 부여 하 여 중요 한 작업을 수행 합니다.
1. 이제 백업 관리자가 중요 한 작업을 시작 합니다.
1. Azure Resource Manager는 백업 관리자에 게 충분 한 권한이 있는지 여부를 확인 합니다. 이제 백업 관리자에 게 리소스 보호에 대 한 참가자 역할이 있으므로 요청이 완료 됩니다.
   - 백업 관리자에 필요한 사용 권한/역할이 없는 경우 요청이 실패 합니다.
1. 보안 관리자는 권한이 부여 된 작업이 수행 된 후 또는 정의 된 기간 이후에 중요 한 작업을 수행 하는 권한이 취소 되도록 합니다. JIT 도구 [Azure Active Directory Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) 를 사용 하면이를 확인 하는 데 유용할 수 있습니다.

>[!NOTE]
>- MUA는 Recovery Services 자격 증명 모음에만 수행 되는 위의 나열 된 작업에 대 한 보호를 제공 합니다. 데이터 원본에서 직접 수행 하는 모든 작업 (즉, 보호 된 Azure 리소스/작업)은 리소스 보호 범위를 벗어났습니다. 
>- 이 기능은 현재 Azure Portal 통해서만 사용할 수 있습니다.
>- 이 기능은 현재 Recovery Services 자격 증명 모음 에서만 지원 되며 백업 자격 증명 모음에 사용할 수 없습니다.

## <a name="before-you-start"></a>시작하기 전에

-  리소스 가드와 Recovery Services 자격 증명 모음은 동일한 Azure 지역에 있어야 합니다.
-  이전 섹션에서 설명한 대로 Backup 관리자 **에 게 리소스** 보호에 대 한 **참가자** 권한이 없는지 확인 합니다. 동일한 디렉터리 또는 다른 디렉터리의 다른 구독에서 리소스를 보호 하도록 선택 하 여 최대 격리를 보장할 수 있습니다.

## <a name="usage-scenarios"></a>사용 시나리오

다음 표에서는 리소스 가드 및 Recovery Services 자격 증명 모음 (RS 자격 증명 모음)을 만드는 시나리오와 각각에서 제공 하는 상대 보호를 보여 줍니다.

>[!Important]
> 모든 시나리오에서 백업 관리자는 리소스 보호에 대 한 참가자 권한이 없어야 합니다.

**사용 시나리오** | **MUA로 인 한 보호** | **구현 용이성** | **참고**
--- | --- |--- |--- |
RS 자격 증명 모음과 리소스 가드가 **동일한 구독에 있습니다.** </br> 백업 관리자에 게 리소스 보호에 대 한 액세스 권한이 없습니다. | 백업 관리자와 보안 관리자 간의 격리를 최소화 합니다. | 하나의 구독만 필요 하므로 비교적 쉽게 구현할 수 있습니다. | 리소스 수준 사용 권한/역할을 올바르게 지정 해야 합니다.
RS 자격 증명 모음 및 리소스 보호는 **서로 다른 구독에 있지만 동일한 테 넌 트에 있습니다.** </br> 백업 관리자는 리소스 가드 또는 해당 구독에 액세스할 수 없습니다. | 백업 관리자와 보안 관리자 간의 보통 격리 | 두 개의 구독 (하나는 단일 테 넌 트)이 필요 하므로 비교적 보통 수준의 구현이 필요 합니다. | 리소스 또는 구독에 대 한 사용 권한/역할이 올바르게 할당 되었는지 확인 합니다.
RS 자격 증명 모음 및 리소스 보호는 **다른 테 넌 트에 있습니다.** </br> 백업 관리자는 리소스 보호, 해당 구독 또는 해당 테 넌 트에 액세스할 수 없습니다.| 백업 관리자와 보안 관리자 간의 최대 격리. 따라서 최대 보안입니다. | 에서 테스트 하는 데 두 개의 테 넌 트 또는 디렉터리가 필요 하므로 비교적 테스트 하기가 어렵습니다. | 리소스, 구독 또는 디렉터리에 대 한 사용 권한/역할이 올바르게 할당 되었는지 확인 합니다.

 >[!NOTE]
 > 이 문서에서는 최대 보호를 제공 하는 다른 테 넌 트에서 리소스 가드를 만드는 방법을 설명 합니다. 이 문서에서는 중요 한 작업을 수행 하기 위한 요청 요청 및 승인 측면에서 리소스 가드를 소유 하는 테 넌 트에 [Azure Active Directory Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) 를 사용 하는 것을 보여 줍니다. 필요에 따라 다른 메커니즘을 사용하여 설정에 따라 Resource Guard에 대한 JIT 권한을 관리할 수 있습니다.

## <a name="creating-a-resource-guard"></a>Resource Guard 만들기

**보안 관리자는** Resource Guard를 만듭니다. **다른 구독** 또는 다른 **테넌트에서** 자격 증명 모음으로 만드는 것이 좋습니다. 그러나 자격 증명 모음과 **동일한 지역에** 있어야 합니다. Backup 관리자는 Resource Guard 또는 해당 리소스가 포함된 구독에 대한 *기여자* 액세스 권한이 **없어야** 합니다.

다음 예제에서는 자격 증명 모음 테넌트와 다른 테넌트에서 Resource Guard를 만듭니다.
1. Azure Portal Resource Guard를 만들려는 디렉터리로 이동합니다.
   
   :::image type="content" source="./media/multi-user-authorization/portal-settings-directories-subscriptions.png" alt-text="포털 설정을 보여주는 스크린샷.":::
     
1. 검색 표시줄에서 **리소스 가드를** 검색하고 드롭다운에서 해당 항목을 선택합니다.
    
   :::image type="content" source="./media/multi-user-authorization/resource-guards-preview-inline.png" alt-text="미리 보기의 리소스 가드를 보여주는 스크린샷." lightbox="./media/multi-user-authorization/resource-guards-preview-expanded.png":::
    
   - **만들기를** 클릭하여 Resource Guard 만들기를 시작합니다.
   - 만들기 블레이드에서 이 Resource Guard에 필요한 세부 정보를 입력합니다.
       - Resource Guard가 Recovery Services 자격 증명 모음과 동일한 Azure 지역에 있는지 확인합니다.
       - 또한 필요한 경우 연결된 자격 증명 모음에서 작업을 수행하기 위해 액세스를 얻거나 요청하는 방법에 대한 설명을 추가하는 것이 좋습니다. 이 설명은 백업 관리자에게 필요한 권한을 얻는 방법을 안내하기 위해 연결된 자격 증명 모음에도 표시됩니다. 필요한 경우 나중에 설명을 편집할 수 있지만 항상 잘 정의된 설명을 갖는 것이 좋습니다.
       
        :::image type="content" source="./media/multi-user-authorization/create-resource-guard.png" alt-text="Resource Guard를 만드는 방법을 보여주는 스크린샷.":::
                
1. 필요에 따라 요구 사항에 따라 Resource Guard에 태그를 추가합니다.
1. **검토 + 만들기** 클릭
1. Resource Guard의 상태 및 성공적인 생성에 대한 알림을 따릅니다.

### <a name="select-operations-to-protect-using-resource-guard"></a>Resource Guard를 사용하여 보호할 작업 선택

지원되는 모든 중요 작업 중에서 Resource Guard를 사용하여 보호하려는 작업을 선택합니다. 기본적으로 지원되는 모든 중요 작업이 사용됩니다. 그러나 Resource Guard를 사용하여 MUA의 관점에서 특정 작업을 제외할 수 있습니다. 보안 관리자는 다음 단계를 수행할 수 있습니다.

1. 위에서 만든 Resource Guard에서 **속성** 으로 이동합니다.
2. Resource Guard를 사용하여 권한 부여에서 제외하려는 작업에 대해 **사용 안 함을** 선택합니다. 일시 **삭제 사용 안 함** 및 **MUA 보호 제거** 작업은 사용하지 않도록 설정할 수 없습니다.
3. 필요에 따라 이 블레이드를 사용하여 Resource Guard에 대한 설명을 업데이트할 수도 있습니다. 
4. **저장** 을 클릭합니다.

   :::image type="content" source="./media/multi-user-authorization/demo-resource-guard-properties.png" alt-text="데모 Resource Guard 속성을 보여주는 스크린샷.":::

## <a name="assign-permissions-to-the-backup-admin-on-the-resource-guard-to-enable-mua"></a>RESOURCE Guard의 Backup 관리자에게 MUA를 사용하도록 설정하기 위한 권한 할당

자격 증명 모음에서 MUA를 사용하도록 설정하려면 자격 증명 모음의 관리자가 Resource Guard 또는 Resource Guard를 포함하는 구독에 대한 **읽기** 역할이어야 합니다. Resource Guard에 **판독기** 역할을 할당하려면 다음을 수행합니다.

1. 위에서 만든 Resource Guard에서 Access Control(IAM) 블레이드로 이동한 다음 역할 **할당 추가로** 이동합니다.

   :::image type="content" source="./media/multi-user-authorization/demo-resource-guard-access-control.png" alt-text="데모 리소스 보호 액세스 제어를 보여주는 스크린샷.":::
    
1. 기본 제공 역할 목록에서 **읽기** 프로그램을 선택하고 화면 아래쪽에서 **다음을** 클릭합니다.

   :::image type="content" source="./media/multi-user-authorization/demo-resource-guard-add-role-assignment-inline.png" alt-text="데모 resource guard-add 역할 할당을 보여주는 스크린샷." lightbox="./media/multi-user-authorization/demo-resource-guard-add-role-assignment-expanded.png":::

1. **구성원 선택을** 클릭하고 Backup 관리자의 이메일 ID를 추가하여 읽기 **프로그램** 으로 추가합니다. 이 경우 Backup 관리자는 다른 테넌트에서 Resource Guard를 포함하는 테넌트에서 게스트로 추가됩니다.

1. **선택을** 클릭한 다음 **검토 + 할당으로** 진행하여 역할 할당을 완료합니다.

   :::image type="content" source="./media/multi-user-authorization/demo-resource-guard-select-members-inline.png" alt-text="데모 리소스 가드 선택 멤버를 보여주는 스크린샷." lightbox="./media/multi-user-authorization/demo-resource-guard-select-members-expanded.png":::

## <a name="enable-mua-on-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음에서 MUA 사용

이제 Backup 관리자가 Resource Guard에 대한 읽기 권한자 역할을 하게 되었으며, 관리되는 자격 증명 모음에서 다중 사용자 권한 부여를 쉽게 사용하도록 설정할 수 있습니다. 다음 단계는 **Backup 관리자가** 수행합니다.

1. Recovery Services 자격 증명 모음으로 이동합니다. 왼쪽 탐색 패널에서 **속성으로** 이동한 다음 **다중 사용자 권한 부여로** 이동하여 **업데이트를** 클릭합니다.

1. 이제 MUA를 사용하도록 설정하고 다음 방법 중 하나를 사용하여 Resource Guard를 선택하는 옵션이 제공됩니다.

   1. Resource Guard의 URI를 지정하고 판독기 **액세스** 권한이 있고 자격 증명 모음과 동일한 지역인 Resource Guard의 URI를 지정해야 합니다. **개요** 화면에서 Resource Guard의 URI(Resource Guard ID)를 찾을 수 있습니다.

      :::image type="content" source="./media/multi-user-authorization/resource-guard-rg-inline.png" alt-text="Resource Guard를 보여주는 스크린샷." lightbox="./media/multi-user-authorization/resource-guard-rg-expanded.png":::
    
   1. 또는 **읽기** 권한자 액세스 권한이 있는 리소스 가드 목록과 해당 지역에서 사용할 수 있는 리소스 가드 목록에서 Resource Guard를 선택할 수 있습니다.

      1. **Resource Guard 선택을** 클릭합니다.
      1. 드롭다운을 클릭하고 Resource Guard가 있는 디렉터리를 선택합니다.
      1. **인증을** 클릭하여 ID 및 액세스의 유효성을 검사합니다.
      1. 인증 후 표시된 목록에서 **Resource Guard를** 선택합니다.

      :::image type="content" source="./media/multi-user-authorization/testvault1-multi-user-authorization-inline.png" alt-text="다중 사용자 권한 부여를 보여주는 스크린샷." lightbox="./media/multi-user-authorization/testvault1-multi-user-authorization-expanded.png" :::

1. 한 번 **저장을** 클릭하여 MUA를 사용하도록 설정합니다.

   :::image type="content" source="./media/multi-user-authorization/testvault1-enable-mua.png" alt-text="다중 사용자 인증을 사용하도록 설정하는 방법을 보여주는 스크린샷":::

## <a name="protect-against-unauthorized-protected-operations"></a>권한 없음(보호된) 작업으로부터 보호

MUA를 사용하도록 설정하면 Backup 관리자가 Resource Guard에 필요한 역할(즉, 기여자 역할)을 갖지 않고 해당 작업을 수행하려고 하면 범위의 작업이 자격 증명 모음에서 제한됩니다.

 >[!NOTE]
 >MUA를 사용하도록 설정한 후 설정을 테스트하여 보호된 작업이 예상대로 차단되었는지 확인하고 MUA가 올바르게 구성되었는지 확인하는 것이 좋습니다.

아래는 Backup 관리자가 이러한 보호된 작업을 수행하려고 할 때 발생하는 작업을 보여주는 예입니다(예: 일시 삭제 사용 안 함에 대한 설명은 여기에 설명되어 있습니다. 다른 보호된 작업에도 비슷한 환경이 있습니다.) 다음 단계는 필요한 권한 없이 Backup 관리자가 수행합니다.

1. 일시 삭제를 사용하지 않도록 설정하려면 Recovery Services 자격 증명 모음 > 속성 > 보안 설정 이동하고 **업데이트** 를 클릭하여 보안 설정 표시합니다.
1. 슬라이더를 사용하여 일시 삭제를 사용하지 않도록 설정합니다. 보호된 작업이라는 정보를 받고 Resource Guard에 대한 액세스를 확인해야 합니다.
1. Resource Guard 및 인증이 포함된 디렉터리를 선택합니다. Resource Guard가 자격 증명 모음과 동일한 디렉터리에 있는 경우 이 단계가 필요하지 않을 수 있습니다.
1. 계속하여 **저장을** 클릭합니다. 이 작업을 수행할 수 있도록 Resource Guard에 대한 충분한 권한이 없다는 오류와 함께 요청이 실패합니다.

## <a name="authorize-critical-protected-operations-using-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management 사용하여 중요한(보호된) 작업 권한 부여

다음 하위 섹션에서는 PIM을 사용하여 이러한 요청에 권한을 부여하는 것에 대해 설명합니다. 백업에서 중요한 작업을 수행해야 하는 경우가 있으며 MUA는 올바른 승인 또는 권한이 있는 경우에만 수행되도록 하는 데 도움이 될 수 있습니다. 앞에서 설명한 대로 Backup 관리자는 Resource Guard 범위에 있는 중요한 작업을 수행하기 위해 Resource Guard에 대한 기여자 역할이 있어야 합니다. 이러한 작업에 Just-In-Time을 허용하는 방법 중 하나는 [azure AD(Azure Active Directory) Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)사용하는 것입니다.

>[!NOTE]
> Azure AD PIM을 사용하는 것이 권장되는 방법이지만 수동 또는 사용자 지정 방법을 사용하여 Resource Guard의 Backup 관리자에 대한 액세스를 관리할 수 있습니다. Resource Guard에 대한 액세스를 수동으로 관리하려면 Resource Guard의 왼쪽 탐색 모음에서 '액세스 제어(IAM)' 설정을 사용하고 Backup 관리자에게 **기여자** 역할을 부여합니다.

### <a name="create-an-eligible-assignment-for-the-backup-admin-if-using-azure-ad-privileged-identity-management"></a>Backup 관리자에 대한 적격 할당 만들기(Azure AD Privileged Identity Management 사용하는 경우)

보안 관리자는 PIM을 사용하여 Resource Guard에 대한 기여자로 Backup 관리자에 대한 적격 할당을 만들 수 있습니다. 이렇게 하면 Backup 관리자가 보호된 작업을 수행해야 할 때 기여자 역할에 대한 요청을 발생시킬 수 있습니다. 이렇게 하려면 **보안 관리자가** 다음을 수행합니다.

1. 보안 테넌트(Resource Guard 포함)에서 **Privileged Identity Management(Azure Portal** 검색 창에서 검색)로 이동한 **다음, Azure 리소스(왼쪽** 메뉴의 **관리** 아래)로 이동합니다.
1. **기여자** 역할을 할당할 리소스(Resource Guard 또는 포함하는 구독/RG)를 선택합니다.

   1. 리소스 목록에 해당 리소스가 표시되지 않으면 PIM에서 관리될 포함하는 구독을 추가해야 합니다.

1. 선택한 리소스에서 할당(왼쪽 **메뉴의** **관리** 아래)으로 이동하고 **할당 추가로 이동합니다.**

    :::image type="content" source="./media/multi-user-authorization/add-assignments.png" alt-text="할당을 추가하는 방법을 보여주는 스크린샷":::

1. 할당 추가에서
   1. 역할을 기여자로 선택합니다.
   1. 구성원 선택으로 이동하여 Backup 관리자의 사용자 이름(또는 전자 메일 ID)을 추가합니다.
   1. 다음을 클릭합니다.

   :::image type="content" source="./media/multi-user-authorization/add-assignments-membership.png" alt-text="assignments-membership을 추가하는 방법을 보여주는 스크린샷":::

1. 다음 화면에서
   1. 할당 유형에서 **적격** 를 선택합니다.
   1. 적격 사용 권한이 유효한 기간을 지정합니다.
   1. **할당을** 클릭하여 적격 할당 만들기를 완료합니다.

   :::image type="content" source="./media/multi-user-authorization/add-assignments-setting.png" alt-text="할당 설정을 추가하는 방법을 보여주는 스크린샷.":::

### <a name="set-up-approvers-for-activating-contributor-role"></a>기여자 역할을 활성화하기 위한 승인자 설정

기본적으로 위의 설정에는 PIM에서 구성 된 승인자 (및 승인 흐름 요구 사항)가 없을 수 있습니다. 승인 된 요청만 통과 하도록 허용 하기 위해 승인자가 필요한 지 확인 하려면 보안 관리자가 다음 단계를 수행 해야 합니다.
참고이 구성 되지 않은 경우 모든 요청은 보안 관리자 또는 지정 된 승인자의 검토를 거치지 않고 자동으로 승인 됩니다. 이에 대 한 자세한 내용은 여기를 참조 [하세요](../active-directory/privileged-identity-management/pim-resource-roles-configure-role-settings.md) .

1. Azure AD PIM의 왼쪽 탐색 모음에서 **Azure 리소스** 를 선택 하 고 리소스 가드를 선택 합니다.

1. **설정** 로 이동한 다음 **참가자** 역할로 이동 합니다.

   :::image type="content" source="./media/multi-user-authorization/add-contributor.png" alt-text="참가자를 추가 하는 방법을 보여 주는 스크린샷":::

1. **승인자** 라는 설정에 없음이 표시 되거나 잘못 된 승인자가 표시 되 면 **편집** 을 클릭 하 여 참가자 역할에 대 한 활성화 요청을 검토 하 고 승인 해야 하는 검토자를 추가 합니다.

1. **활성화** 탭에서 **승인 필요를** 선택 하 고 각 요청을 승인 해야 하는 승인자를 추가 합니다. 또한 MFA 및 재조정 티켓 옵션을 사용 하 여 참가자 역할을 활성화 하는 등의 다른 보안 옵션을 선택할 수 있습니다. 필요에 따라 사용자 요구 사항에 따라 **할당** 및 **알림** 탭에서 관련 설정을 선택 합니다.

   :::image type="content" source="./media/multi-user-authorization/edit-role-settings.png" alt-text="역할 설정을 편집 하는 방법을 보여 주는 스크린샷":::

1. 완료 되 면 **업데이트** 를 클릭 합니다.

### <a name="request-activation-of-an-eligible-assignment-to-perform-critical-operations"></a>적격 할당을 활성화 하 여 중요 한 작업 수행

보안 관리자가 적격 할당을 만든 후에는 보호 된 작업을 수행할 수 있도록 백업 관리자가 참가자 역할에 대 한 할당을 활성화 해야 합니다. 다음 작업은 **백업 관리자** 가 역할 할당을 활성화 하는 데 수행 됩니다.

1. [AZURE AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)로 이동 합니다. 리소스 가드가 다른 디렉터리에 있는 경우 해당 디렉터리로 전환 하 고 [AZURE AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)로 이동 합니다.
1. 왼쪽 메뉴에서 내 역할 > Azure 리소스로 이동 합니다.
1. 백업 관리자는 참가자 역할에 대 한 적격 할당을 볼 수 있습니다. 활성화 **를 클릭** 하 여 활성화 합니다.
1. 백업 관리자는 승인을 위해 요청을 보내는 포털 알림을 통해 알림을 받습니다.

   :::image type="content" source="./media/multi-user-authorization/identity-management-myroles-inline.png" alt-text="적격 할당을 활성화 하는 것을 보여 주는 스크린샷" lightbox="./media/multi-user-authorization/identity-management-myroles-expanded.png":::

### <a name="approve-activation-of-requests-to-perform-critical-operations"></a>중요 한 작업을 수행 하기 위해 요청 활성화 승인

백업 관리자가 참가자 역할 활성화 요청을 발생 시킨 후에는 **보안 관리자** 가 요청을 검토 하 고 승인 해야 합니다.
1. 보안 테 넌 트에서 [AZURE AD Privileged Identity Management로 이동 합니다.](../active-directory/privileged-identity-management/pim-configure.md)
1. **요청 승인** 으로 이동 합니다.
1. **Azure 리소스** 에서 **참가자로** 활성화를 요청 하는 백업 관리자에 의해 발생 한 요청이 표시 될 수 있습니다.
1. 요청을 검토 합니다. 정품 인 경우 요청을 선택 하 고 **승인** 을 클릭 하 여 승인 합니다.
1. Backup 관리자는 요청이 승인 된 전자 메일 또는 다른 조직 경고 메커니즘을 통해 알림을 받습니다.
1. 승인 되 면 백업 관리자는 요청 된 기간 동안 보호 된 작업을 수행할 수 있습니다.

## <a name="performing-a-protected-operation-after-approval"></a>승인 후 보호 된 작업 수행

리소스 보호에 대 한 참가자 역할에 대 한 백업 관리자의 요청이 승인 되 면 연결 된 자격 증명 모음에서 보호 된 작업을 수행할 수 있습니다. 다른 디렉터리에 리소스 보호가 있는 경우에는 백업 관리자가 자신을 인증 해야 합니다.

>[!NOTE]
> JIT 메커니즘을 사용 하 여 액세스 권한이 할당 된 경우 승인 된 기간이 끝날 때 참가자 역할이 취소 됩니다. 또는 보안 관리자는 백업 관리자에 할당 된 **참가자** 역할을 수동으로 제거 하 여 중요 한 작업을 수행 합니다.

## <a name="disable-mua-on-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음에서 MUA 사용 안 함

MUA를 사용 하지 않도록 설정 하는 것은 보호 된 작업 이므로 MUA를 사용 하 여 보호 됩니다. 즉, 백업 관리자에 게 리소스 보호에 필요한 참가자 역할이 있어야 합니다. 이 역할을 얻는 방법에 대 한 자세한 내용은 여기에 설명 되어 있습니다. 다음은 자격 증명 모음에서 MUA을 사용 하지 않도록 설정 하는 단계에 대 한 요약입니다.
1. 백업 관리자는 리소스 보호에 대 한 **참가자** 역할에 대 한 보안 관리자를 요청 합니다. [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md), 다른 내부 도구 및 절차와 같은 조직에서 승인 하는 메서드를 사용 하도록 요청할 수 있습니다. 
1. 보안 관리자는 요청을 승인 하 고 (승인 여부를 확인 하는 경우) 백업 관리자에 게 알립니다. 이제 백업 관리자에 게 리소스 보호에 대 한 ' 참가자 ' 역할이 있습니다.
1. 백업 관리자는 자격 증명 모음 > 속성 > 다중 사용자 권한 부여로 이동 합니다.
1. **업데이트** 를 클릭합니다.
   1. 리소스 보호로 보호 확인란의 선택을 취소 합니다.
   1. 리소스 보호를 포함 하는 디렉터리를 선택 하 고 인증 단추 (해당 하는 경우)를 사용 하 여 액세스를 확인 합니다.
   1. **인증** 후 **저장** 을 클릭 합니다. 올바른 액세스 권한으로 요청이 성공적으로 완료 되어야 합니다.