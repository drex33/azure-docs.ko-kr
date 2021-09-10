---
title: Azure Security Center에서 Just-In-Time 가상 머신 액세스 | Microsoft Docs
description: 이 문서에서는 Azure Security Center에서 JIT(Just-In-Time VM) 액세스가 Azure 가상 머신에 대한 액세스를 제어하는 데 어떻게 도움이 되는지 보여 줍니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: dff90bc02ef9ee20284adb895eb05e765bdfba72
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528646"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Just-In-Time 액세스를 사용하여 관리 포트 보호

Azure Security Center의 JIT(Just-In-Time) 가상 머신(VM) 액세스 기능을 사용하여 Azure 가상 머신에 대한 인바운드 트래픽을 잠급니다. 이렇게 하면 VM에 연결해야 할 때 쉽게 액세스할 수 있도록 하면서 공격에 대한 노출을 줄일 수 있습니다.

JIT 작동 방식 및 기본 논리에 대한 전체 설명은 [Just-In-Time 설명이 포함된 문서](just-in-time-explained.md)를 참조하세요.

이 페이지에서는 보안 프로그램에 JIT를 포함하는 방법을 설명합니다. 이 문서에서 배울 내용은 다음과 같습니다. 

- **VM에서 JIT 사용** - Security Center, PowerShell 또는 REST API를 사용하여 하나 이상의 VM에 대한 사용자 지정 옵션으로 JIT을 사용하도록 설정할 수 있습니다. 또는 Azure 가상 머신에서 하드 코딩된 기본 매개 변수를 사용하여 JIT를 사용하도록 설정할 수 있습니다. JIT를 사용하도록 설정하면 네트워크 보안 그룹에 규칙을 만들어 Azure VM에 대한 인바운드 트래픽을 잠급니다.
- **JIT를 사용하도록 설정한 VM에 대한 액세스 요청** - JIT의 목표는 인바운드 트래픽이 잠기더라도 필요한 경우 Security Center에서 VM에 연결할 수 있게 쉽게 액세스하도록 하는 것입니다. Security Center, Azure 가상 머신, PowerShell 또는 REST API에서 JIT 지원 VM에 대한 액세스를 요청할 수 있습니다.
- **활동 감사** - VM의 보안을 적절하게 유지하려면 일반 보안 검사의 일부로 JIT 지원 VM에 대한 액세스를 검토합니다.   



## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|[서버용 Azure Defender](defender-for-servers-introduction.md) 필요|
|지원되는 VM:|:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Resource Manager를 통해 배포된 VM<br>:::image type="icon" source="./media/icons/no-icon.png"::: 클래식 배포 모델을 사용하여 배포된 VM [이러한 배포 모델에 대한 자세한 정보](../azure-resource-manager/management/deployment-models.md)<br>:::image type="icon" source="./media/icons/no-icon.png"::: [Azure Firewall Manager](../firewall-manager/overview.md)에서 제어하는 Azure Firewall로 보호되는 VM|
|필요한 역할 및 권한:|**읽기 권한자** 및 **보안 읽기 권한자** 역할은 둘 다 JIT 상태와 매개 변수를 볼 수 있습니다.<br>JIT를 사용할 수 있는 사용자 지정 역할을 만들려면 [JIT를 구성하고 사용하는 데 필요한 권한은 무엇인가요?](just-in-time-explained.md#what-permissions-are-needed-to-configure-and-use-jit)를 참조하세요.<br>VM에 대한 JIT 액세스를 요청해야 하는 사용자에게 최소 권한 역할을 만들고 다른 JIT 작업을 수행하지 않으려면 Security Center GitHub 커뮤니티 페이지에서 [Set-JitLeastPrivilegedRole 스크립트](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role)를 사용합니다.|
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 국가/소버린(Azure Government, Azure 중국 21Vianet)|
|||


## <a name="enable-jit-vm-access"></a>JIT VM 액세스 사용 <a name="jit-configure"></a>

Security Center를 사용하거나 프로그래밍 방식으로 하나 이상의 VM에 대한 사용자 지정 옵션을 사용하여 JIT VM 액세스를 사용하도록 설정할 수 있습니다. 

또는 Azure 가상 머신에서 하드 코딩된 기본 매개 변수를 사용하여 JIT를 사용하도록 설정할 수 있습니다.

이러한 각 옵션은 아래 별도의 탭에 설명되어 있습니다.

### <a name="azure-security-center"></a>[**Azure Security Center**](#tab/jit-config-asc)

### <a name="enable-jit-on-your-vms-from-azure-security-center"></a>Azure Security Center에서 VM에 대해 JIT 사용 <a name="jit-asc"></a>

:::image type="content" source="./media/security-center-just-in-time/jit-config-security-center.gif" alt-text="Azure Security Center에서 JIT VM 액세스 구성":::

Security Center에서 JIT VM 액세스를 사용하도록 설정하고 구성할 수 있습니다.

1. Azure Defender 대시보드를 열고 고급 보호 영역에서 **Just-In-Time VM 액세스** 를 선택합니다.

    VM이 다음 탭으로 그룹화되어 **Just-In-Time VM 액세스** 페이지가 열립니다.

    - **구성됨** - Just-In-Time VM 액세스를 지원하도록 이미 구성된 VM입니다. 각 VM에 대해 구성됨 탭에 다음이 표시됩니다.
        - 지난 7일간 승인된 JIT 요청 수
        - 마지막으로 파일에 액세스한 날짜 및 시간
        - 구성된 연결 세부 정보
        - 마지막 사용자
    - **구성되지 않음** -JIT를 사용하도록 설정하지 않았으나 JIT를 지원할 수 있는 VM입니다. 이러한 VM에 대해 JIT를 사용하도록 설정하는 것이 좋습니다.
    - **지원되지 않음** - JIT를 사용하도록 설정하지 않았으며 해당 기능을 지원하지 않는 VM입니다. 다음과 같은 이유로 VM이 이 탭에 포함될 수 있습니다.
      - 누락된 NSG(네트워크 보안 그룹) - JIT를 구성하려면 NSG가 필요합니다.
      - 클래식 VM - JIT는 '클래식 배포'가 아닌 Azure Resource Manager를 통해 배포되는 VM을 지원합니다. [클래식 및 Azure Resource Manager 배포 모델에 대해 자세히 알아봅니다](../azure-resource-manager/management/deployment-models.md).
      - 기타 - JIT 솔루션을 구독 또는 리소스 그룹의 보안 정책에서 사용하지 않도록 설정한 경우에는 VM이 이 탭에 포함될 수 있습니다.

1. **구성되지 않음** 탭에서 VM을 JIT로 보호하도록 표시하고 **VM에 대해 JIT 사용** 을 선택합니다. 

    JIT VM 액세스 페이지가 열리고 Security Center에서 보호를 권장하는 포트가 나열됩니다.
    - 22 - SSH
    - 3389 - RDP
    - 5985 - WinRM 
    - 5986 - WinRM

    기본 설정을 적용하려면 **저장** 을 선택합니다.

1. JIT 옵션을 사용자 지정하려면

    - **추가** 단추를 사용하여 사용자 지정 포트를 추가합니다. 
    - 목록에서 선택하여 기본 포트 중 하나를 수정합니다.

    각 포트(사용자 지정 및 기본)에 대해 **포트 구성 추가** 창에 다음 옵션이 제공됩니다.

    - **프로토콜** - 요청이 승인될 때 이 포트에서 허용되는 프로토콜입니다.
    - **허용된 원본 IP** - 요청이 승인될 때 이 포트에서 허용되는 IP 범위입니다.
    - **최대 요청 시간** - 특정 포트를 열 수 있는 최대 기간입니다.

     1. 포트 보안을 필요에 맞게 설정합니다.

     1. **확인** 을 선택합니다.

1. **저장** 을 선택합니다.



### <a name="edit-the-jit-configuration-on-a-jit-enabled-vm-using-security-center"></a>Security Center를 사용하여 JIT 지원 VM에서 JIT 구성 편집 <a name="jit-modify"></a>

해당 VM에 대해 보호할 새 포트를 추가 및 구성하거나 이미 보호된 포트와 관련된 다른 설정을 변경하여 VM의 Just-In-Time 구성을 수정할 수 있습니다.

VM에 대한 기존 JIT 규칙을 편집하려면

1. Azure Defender 대시보드를 열고 고급 보호 영역에서 **Just-In-Time VM 액세스** 를 선택합니다.

1. **구성됨** 탭에서 포트를 추가하려는 VM을 마우스 오른쪽 단추로 클릭하고 편집을 선택합니다. 

    ![Azure Security Center에서 JIT VM 액세스 구성 편집](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

1. **JIT VM 액세스 구성** 에서 포트를 클릭하여 이미 보호된 포트의 기존 설정을 편집하거나 새로운 사용자 지정 포트를 추가할 수 있습니다.

1. 포트 편집이 완료되면 **저장** 을 선택합니다.
 


### <a name="azure-virtual-machines"></a>[**Azure 가상 머신**](#tab/jit-config-avm)

### <a name="enable-jit-on-your-vms-from-azure-virtual-machines"></a>Azure 가상 머신에서 VM에 대해 JIT를 사용하도록 설정

Azure Portal의 Azure 가상 머신 페이지에서 VM에 대해 JIT를 사용하도록 설정할 수 있습니다.

![Azure Virtual Machines에서 JIT VM 액세스 구성](./media/security-center-just-in-time/jit-config-virtual-machines.gif)

> [!TIP]
> VM에 이미 Just-In-Time이 사용 설정된 경우 구성 페이지로 이동하면 Just-In-Time이 사용하도록 설정되었으며 링크를 사용해 Security Center에서 Just-In-Time VM 액세스 페이지를 연 후 설정을 보고 변경할 수 있음을 확인할 수 있습니다.

1. [Azure Portal](https://ms.portal.azure.com)에서 **가상 머신** 을 검색하여 선택합니다. 

1. JIT로 보호하려는 가상 머신을 선택합니다.

1. 메뉴에서 **구성** 을 선택합니다.

1. **Just-in-time 액세스** 에서 **Just-In-Time 사용** 을 선택합니다. 

    이렇게 하면 다음 설정을 사용하여 VM에 대한 Just-In-Time 액세스가 사용하도록 설정됩니다.

    - Windows 머신:
        - RDP 포트 3389
        - 허용되는 최대 액세스: 3시간
        - 허용된 원본 IP 주소는 임의로 설정됨
    - Linux 머신:
        - SSH 포트 22
        - 허용되는 최대 액세스: 3시간
        - 허용된 원본 IP 주소는 임의로 설정됨

1. 이러한 값을 편집하거나 JIT 구성에 포트를 더 추가하려면 Azure Security Center의 Just-In-Time 페이지를 사용합니다.

    1. Security Center의 메뉴에서 **Just-In-Time VM 액세스** 를 선택합니다.

    1. **구성됨** 탭에서 포트를 추가하려는 VM을 마우스 오른쪽 단추로 클릭하고 편집을 선택합니다. 

        ![Azure Security Center에서 JIT VM 액세스 구성 편집](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

    1. **JIT VM 액세스 구성** 에서 포트를 클릭하여 이미 보호된 포트의 기존 설정을 편집하거나 새로운 사용자 지정 포트를 추가할 수 있습니다.

    1. 포트 편집이 완료되면 **저장** 을 선택합니다.


### <a name="powershell"></a>[**PowerShell**](#tab/jit-config-powershell)

### <a name="enable-jit-on-your-vms-using-powershell"></a>PowerShell을 사용하여 VM에서 JIT 사용

PowerShell에서 Just-In-Time VM 액세스를 사용하도록 설정하려면 공식 Azure Security Center PowerShell cmdlet `Set-AzJitNetworkAccessPolicy`를 사용합니다.

**예** - 다음 규칙을 사용하여 특정 VM에서 Just-In-Time VM 액세스를 사용하도록 설정합니다.

* 포트 22 및 3389를 닫습니다.
* 승인된 요청에 대해 열 수 있도록 최대 기간을 3시간으로 설정합니다.
* 액세스를 요청하는 사용자가 소스 IP 주소를 제어할 수 있도록 허용합니다.
* 액세스를 요청하는 사용자가 승인된 Just-In-Time 액세스 요청 시 성공적인 세션을 설정할 수 있도록 허용합니다.

다음 PowerShell 명령은 이 JIT 구성을 만듭니다.

1. VM에 대한 Just-In-Time VM 액세스 규칙을 보유하는 변수를 할당합니다.

    ```azurepowershell
    $JitPolicy = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
             number=22;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"})})
    ```

1. VM Just-In-Time VM 액세스 규칙을 배열에 삽입합니다.
    
    ```azurepowershell
    $JitPolicyArr=@($JitPolicy)
    ```

1. 선택한 VM에서 Just-In-Time VM 액세스 규칙을 구성합니다.
    
    ```azurepowershell
    Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr
    ```

    -Name 매개 변수를 사용하여 VM을 지정합니다. 예를 들어, VM1 및 VM2의 서로 다른 두 VM에 대해 JIT 구성을 설정하려면 ```Set-AzJitNetworkAccessPolicy -Name VM1``` 및 ```Set-AzJitNetworkAccessPolicy -Name VM2```를 사용합니다.


### <a name="rest-api"></a>[**REST API**](#tab/jit-config-api)

### <a name="enable-jit-on-your-vms-using-the-rest-api"></a>REST API를 사용하여 VM에서 JIT 사용

Just-In-Time VM 액세스 기능은 Azure Security Center API를 통해 사용할 수 있습니다. 이 API를 사용하여 구성된 VM에 대한 정보를 가져오고 새로 추가하고 VM에 대한 액세스를 요청하는 등을 할 수 있습니다. 

[JIT 네트워크 액세스 정책](/rest/api/securitycenter/jitnetworkaccesspolicies)에 대해 자세히 알아보세요.


--- 










## <a name="request-access-to-a-jit-enabled-vm"></a>JIT 사용 VM에 대한 액세스 요청

Azure Portal(Security Center 또는 Azure 가상 머신)에서 또는 프로그래밍 방식으로 JIT 지원 VM에 대한 액세스를 요청할 수 있습니다.

이러한 각 옵션은 아래 별도의 탭에 설명되어 있습니다.

### <a name="azure-security-center"></a>[**Azure Security Center**](#tab/jit-request-asc)

### <a name="request-access-to-a-jit-enabled-vm-from-azure-security-center"></a>Azure Security Center에서 JIT 지원 VM에 대한 액세스 요청 

VM에서 JIT를 사용하도록 설정한 경우 연결에 대한 액세스를 요청해야 합니다. JIT를 사용하도록 설정하는 방법에 관계 없이 지원되는 방법 중 하나를 사용하여 액세스를 요청할 수 있습니다.

:::image type="content" source="./media/security-center-just-in-time/jit-request-security-center.gif" alt-text="Security Center에서 JIT 액세스 요청":::

1. **Just-In-Time VM 액세스** 페이지에서 **구성됨** 탭을 선택합니다.

1. 액세스하려는 VM에 표시합니다.

    - **연결 세부 정보** 열의 아이콘은 네트워크 보안 그룹 또는 방화벽에서 JIT가 사용하도록 설정되었는지를 나타냅니다. 둘 다에서 사용하도록 설정된 경우 방화벽 아이콘만 표시됩니다.

    - **연결 세부 정보** 열은 VM 및 열려 있는 포트에 연결하는 데 필요한 정보를 제공합니다.

1. **액세스 요청** 을 선택합니다. 그러면 **액세스 요청** 창이 열립니다.

1. **액세스 요청** 아래에서 각 VM에 대해 열려는 포트, 포트가 열려 있는 원본 IP 주소 및 포트를 열어 둘 기간을 구성합니다. 구성된 포트에 대해서만 액세스를 요청할 수 있습니다. 각 포트에는 만든 JIT 구성에서 파생된 최대 허용 시간이 있습니다.

1. **포트 열기** 를 선택합니다.

> [!NOTE]
> 액세스를 요청하는 사용자가 프록시 뒤에 있으면 **내 IP** 옵션이 작동하지 않습니다. 조직의 전체 IP 주소 범위를 정의해야 할 수 있습니다.



### <a name="azure-virtual-machines"></a>[**Azure 가상 머신**](#tab/jit-request-avm)

### <a name="request-access-to-a-jit-enabled-vm-from-the-azure-virtual-machines-connect-page"></a>Azure 가상 머신의 연결 페이지에서 JIT 지원 VM에 대한 액세스 요청

VM에서 JIT를 사용하도록 설정한 경우 연결에 대한 액세스를 요청해야 합니다. JIT를 사용하도록 설정하는 방법에 관계 없이 지원되는 방법 중 하나를 사용하여 액세스를 요청할 수 있습니다.

  >![JIT(Just-In-Time) 요청](./media/security-center-just-in-time/jit-request-vm.png)


Azure 가상 머신에서 액세스를 요청하려면

1. Azure Portal에서 가상 머신 페이지를 엽니다.

1. 연결하려는 VM을 선택하고 **연결** 페이지를 엽니다.

    Azure는 해당 VM에서 JIT가 사용하도록 설정되어 있는지 확인합니다.

    - VM에 대해 JIT를 사용하도록 설정하지 않은 경우에는 사용하도록 설정하라는 메시지가 표시됩니다.

    - JIT를 사용하도록 설정한 경우 **액세스 요청** 을 선택하여 요청 IP, 시간 범위 및 해당 VM에 대해 구성된 포트를 사용하여 액세스 요청을 전달합니다.

> [!NOTE]
> Azure Firewall로 보호되는 VM에 대한 요청이 승인되면 Security Center는 사용자에게 VM에 연결하는 데 사용할 적절한 연결 세부 정보(DNAT 테이블에서의 포트 매핑)를 제공합니다.



### <a name="powershell"></a>[**PowerShell**](#tab/jit-request-powershell)

### <a name="request-access-to-a-jit-enabled-vm-using-powershell"></a>PowerShell을 사용하여 JIT 사용 VM에 대한 액세스 요청

다음 예제에서는 포트 22를 특정 IP 주소 및 특정 시간 동안 열도록 요청된 특정 VM에 대한 Just-In-Time VM 액세스 요청을 확인할 수 있습니다.

PowerShell에서 다음 내용을 실행하세요.

1. VM 요청 액세스 속성을 구성합니다.

    ```azurepowershell
    $JitPolicyVm1 = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
           number=22;
           endTimeUtc="2020-07-15T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
    ```

1. VM 액세스 요청 매개 변수를 배열에 삽입합니다.

    ```azurepowershell
    $JitPolicyArr=@($JitPolicyVm1)
    ```
        
1. 액세스 요청을 보냅니다(1단계의 리소스 ID 사용).

    ```azurepowershell
    Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr
    ```

[PowerShell cmdlet 설명서](/powershell/scripting/developer/cmdlet/cmdlet-overview)에서 자세히 알아보세요.



### <a name="rest-api"></a>[**REST API**](#tab/jit-request-api)

### <a name="request-access-to-a-jit-enabled-vms-using-the-rest-api"></a>REST API를 사용하여 JIT 지원 >VM에 대한 액세스 요청

Just-In-Time VM 액세스 기능은 Azure Security Center API를 통해 사용할 수 있습니다. 이 API를 사용하여 구성된 VM에 대한 정보를 가져오고 새로 추가하고 VM에 대한 액세스를 요청하는 등을 할 수 있습니다. 

[JIT 네트워크 액세스 정책](/rest/api/securitycenter/jitnetworkaccesspolicies)에 대해 자세히 알아보세요.

---








## <a name="audit-jit-access-activity-in-security-center"></a>Security Center에서 JIT 액세스 작업 감사

로그 검색을 사용하여 VM 활동에 대한 정보를 얻을 수 있습니다. 로그를 보려면:

1. **Just-In-Time VM 액세스** 에서 **구성됨** 탭을 선택합니다.

1. 감사하려는 VM의 경우 행의 끝에 있는 줄임표 메뉴를 엽니다.
 
1. 메뉴에서 **활동 로그** 를 선택합니다.

   ![JIT(Just-in-time) 활동 로그 선택](./media/security-center-just-in-time/jit-select-activity-log.png)

   활동 로그는 시간, 날짜 및 구독과 함께 해당 VM에 대한 이전 작업의 필터링된 보기를 제공합니다.

1. 로그 정보를 다운로드하려면 **CSV로 다운로드** 를 선택합니다.








## <a name="next-steps"></a>다음 단계

이 문서에서는 Just-In-Time VM 액세스를 구성하고 사용하는 _방법_ 을 알아보았습니다. JIT를 사용해야 하는 _이유_ 를 알아보려면 JIT가 방어하는 위협을 설명하는 개념 문서를 참조하세요.

> [!div class="nextstepaction"]
> [JIT 설명이 포함된 문서](just-in-time-explained.md)