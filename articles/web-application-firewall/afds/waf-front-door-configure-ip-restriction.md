---
title: Azure Front Door에 대한 IP 제한 WAF 규칙 구성
description: 기존 Azure Front Door 엔드포인트에 대한 IP 주소를 제한하도록 Web Application Firewall 규칙을 구성하는 방법을 알아봅니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 12/22/2020
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e9022db23ac425cd958180954373ff4a924e9818
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131507135"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>Web Application Firewall에서 Azure Front Door에 대한 IP 제한 규칙 구성

이 문서에서는 Azure Portal, Azure CLI, Azure PowerShell 또는 Azure Resource Manager 템플릿을 사용하여 WAF(Web Application Firewall)에서 Azure Front Door에 대한 IP 제한 규칙을 구성하는 방법을 보여 줍니다.

IP 주소 기반 액세스 제어 규칙은 웹 애플리케이션에 대한 액세스를 제어할 수 있는 사용자 지정 WAF 규칙입니다. CIDR(Classless Interdomain Routing) 형식으로 IP 주소 또는 IP 주소 범위 목록을 지정하여 제어합니다. IP 주소 일치에는 두 가지 유형의 일치 변수, **RemoteAddr** 및 **SocketAddr** 이 있습니다. RemoteAddr은 일반적으로 X-Forwarded-For 요청 헤더를 통해 전송되는 원래 클라이언트 IP입니다. SocketAddr은 WAF에서 볼 수 있는 원본 IP 주소입니다. 사용자가 프록시 뒤에 있는 경우 SocketAddr은 프록시 서버 주소인 경우가 많습니다.

기본적으로 웹 애플리케이션은 인터넷에서 액세스할 수 있습니다. 알려진 IP 주소 또는 IP 주소 범위 목록에서 클라이언트에 대한 액세스를 제한하려면 IP 주소 목록이 일치 값으로 포함되어 있으며 연산자가 “Not”(부정이 true)으로 설정되고 동작이 **차단** 으로 설정된 IP 일치 규칙을 만들 수 있습니다. IP 제한 규칙이 적용되면 이 허용 목록 이외의 주소에서 발생한 요청은 403 Forbidden 응답을 수신합니다.

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>Azure Portal을 사용하여 WAF 정책 구성

### <a name="prerequisites"></a>필수 구성 요소

[빠른 시작: 고가용성 글로벌 웹 애플리케이션에 대한 Front Door 만들기](../../frontdoor/quickstart-create-front-door.md)에 설명된 지침에 따라 Azure Front Door 프로필을 만듭니다.

### <a name="create-a-waf-policy"></a>WAF 정책 만들기

1. Azure Portal에서 **리소스 만들기** 를 선택하고 검색 상자에 **웹 애플리케이션 방화벽** 을 입력한 다음, **WAF(Web Application Firewall)** 를 선택합니다.
2. **만들기** 를 선택합니다.
3. **WAF 정책 만들기** 페이지에서 다음 값을 사용하여 **기본 사항** 탭을 완료합니다.

   |설정  |값  |
   |---------|---------|
   |정책     |글로벌 WAF(Front Door)|
   |구독     |구독 선택|
   |Resource group     |Front Door가 있는 리소스 그룹 선택|
   |정책 이름     |정책 이름 입력|
   |정책 상태     |사용|

   **다음: 정책 설정** 을 선택합니다.

1. **정책 설정** 탭에서 **방지** 를 선택합니다. **차단 응답 본문** 에 *You've been blocked!* (차단되었습니다.)를 입력합니다. 이렇게 하면 사용자 지정 규칙이 적용되는 것을 볼 수 있습니다.
2. **다음: 관리형 규칙** 을 선택합니다.
3. **다음: 사용자 지정 규칙** 을 선택합니다.
4. **사용자 지정 규칙 추가** 를 선택합니다.
5. **사용자 지정 규칙 추가** 페이지에서 다음 테스트 값을 사용하여 사용자 지정 규칙을 만듭니다.

   |설정  |값  |
   |---------|---------|
   |사용자 지정 규칙 이름     |FdWafCustRule|
   |상태     |사용|
   |규칙 유형     |일치|
   |우선 순위    |100|
   |일치 유형     |IP 주소|
   |일치 변수|SocketAddr|
   |작업(Operation)|포함하지 않음|
   |IP 주소 또는 범위|10.10.10.0/24|
   |결과|트래픽 거부|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="사용자 지정 규칙":::

   **추가** 를 선택합니다.
6. **다음: 연결** 을 선택합니다.
7. **프런트 엔드 호스트 추가** 를 선택합니다.
8. **프런트 엔드 호스트** 에 대해 프런트 엔드 호스트를 선택하고 **추가** 를 선택합니다.
9. **검토 + 만들기** 를 선택합니다.
10. 정책 유효성 검사를 통과하면 **만들기** 를 선택합니다.

### <a name="test-your-waf-policy"></a>WAF 정책 테스트

1. WAF 정책 배포가 완료되면 Front Door 프런트 엔드 호스트 이름으로 이동합니다.
2. 사용자 지정 차단 메시지가 표시되어야 합니다.

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="WAF 규칙 테스트":::

   > [!NOTE]
   > 사용자 지정 규칙에서는 규칙이 트리거되도록 보장하기 위해 의도적으로 개인 IP 주소가 사용되었습니다. 실제 배포에서는 특정 상황에 대해 IP 주소를 사용하는 ‘허용’ 및 ‘거부’ 규칙을 만듭니다. 

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Azure CLI를 사용하여 WAF 정책 구성

### <a name="prerequisites"></a>필수 구성 요소
IP 제한 정책 구성을 시작하기 전에 CLI 환경을 설정하고 Azure Front Door 프로필을 만듭니다.

#### <a name="set-up-the-azure-cli-environment"></a>Azure CLI 환경 설정
1. [Azure CLI](/cli/azure/install-azure-cli)를 설치하거나 Azure Cloud Shell을 사용합니다. Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 Bash 셸입니다. Azure CLI가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 다음 CLI 명령에서 **체험해 보기** 단추를 선택한 다음, 열리는 Cloud Shell 세션에서 Azure 계정에 로그인합니다. 세션이 시작되면 `az extension add --name front-door`를 입력하여 Azure Front Door 확장을 추가합니다.
 2. Bash에서 로컬로 CLI를 사용하는 중인 경우 `az login`을 사용하여 Azure에 로그인합니다.

#### <a name="create-an-azure-front-door-profile"></a>Azure Front Door 프로필 만들기
[빠른 시작: 고가용성 글로벌 웹 애플리케이션에 대한 Front Door 만들기](../../frontdoor/quickstart-create-front-door.md)에 설명된 지침에 따라 Azure Front Door 프로필을 만듭니다.

### <a name="create-a-waf-policy"></a>WAF 정책 만들기

[az network front-door waf-policy create](/cli/azure/network/front-door/waf-policy#az_network_front_door_waf_policy_create) 명령을 사용하여 WAF 정책을 만듭니다.
아래 예제에서 정책 이름 *IPAllowPolicyExampleCLI* 를 고유한 정책 이름으로 바꿉니다.

```azurecli-interactive
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>사용자 지정 IP 액세스 제어 규칙 추가

[az network front-door waf-policy custom-rule create](/cli/azure/network/front-door/waf-policy/rule#az_network_front_door_waf_policy_rule_create) 명령을 사용하여 방금 만든 WAF 정책에 대한 사용자 지정 IP 액세스 제어 규칙을 추가합니다.

아래 예제에서 다음을 수행합니다.
-  *IPAllowPolicyExampleCLI* 를 앞에서 만든 고유한 정책으로 바꿉니다.
-  *ip-address-range-1*, *ip-address-range-2* 를 고유한 범위로 바꿉니다.

먼저, 이전 단계에서 만든 정책에 대해 IP 허용 규칙을 만듭니다.
> [!NOTE]
> 다음 단계에서 규칙에 일치 조건이 추가되어야 하므로 **--defer** 는 필수입니다.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
다음으로, 규칙에 일치 조건을 추가합니다.

```azurecli
az network front-door waf-policy rule match-condition add \
--match-variable SocketAddr \
--operator IPMatch \
--values "ip-address-range-1" "ip-address-range-2" \
--negate true \
--name IPAllowListRule \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
  ```

### <a name="find-the-id-of-a-waf-policy"></a>WAF 정책의 ID 찾기
[az network front-door waf-policy show](/cli/azure/network/front-door/waf-policy#az_network_front_door_waf_policy_show) 명령을 사용하여 WAF 정책의 ID를 찾습니다. 다음 예제의 *IPAllowPolicyExampleCLI* 를 앞에서 만든 고유한 정책으로 바꿉니다.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Azure Front Door 프런트 엔드 호스트에 WAF 정책 연결

[az network front-door update](/cli/azure/network/front-door#az_network_front_door_update) 명령을 사용하여 Azure Front Door *WebApplicationFirewallPolicyLink* ID를 정책 ID로 설정합니다. *IPAllowPolicyExampleCLI* 를 앞에서 만든 고유한 정책으로 바꿉니다.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
이 예제에서는 WAF 정책이 **FrontendEndpoints[0]** 에 적용됩니다. WAF 정책을 임의의 프런트 엔드에 연결할 수 있습니다.
> [!Note]
> WAF 정책을 Azure Front Door 프런트 엔드에 연결하려면 **WebApplicationFirewallPolicyLink** 속성을 한 번만 설정하면 됩니다. 이후의 정책 업데이트는 프런트 엔드에 자동으로 적용됩니다.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Azure PowerShell을 사용하여 WAF 정책 구성

### <a name="prerequisites"></a>필수 구성 요소
IP 제한 정책 구성을 시작하기 전에 PowerShell 환경을 설정하고 Azure Front Door 프로필을 만듭니다.

#### <a name="set-up-your-powershell-environment"></a>PowerShell 환경 설정
Azure PowerShell은 [Azure Resource Manager](../../azure-resource-manager/management/overview.md) 모델을 사용하여 Azure 리소스를 관리하는 cmdlet 세트를 제공합니다.

로컬 머신에 [Azure PowerShell](/powershell/azure/) 설치하고 모든 PowerShell 세션에서 사용할 수 있습니다. 페이지의 지침에 따라 Azure 자격 증명을 사용하여 PowerShell에 로그인하고 Az 모듈을 설치합니다.

1. 다음 명령을 사용하여 Azure에 연결하고 대화형 대화 상자를 사용하여 로그인합니다.
    ```
    Connect-AzAccount
    ```
 2. Azure Front Door 모듈을 설치하기 전에 현재 버전의 PowerShellGet 모듈이 설치되어 있는지 확인합니다. 다음 명령을 실행하고 PowerShell을 다시 엽니다.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ```

3. 다음 명령을 사용하여 Az.FrontDoor 모듈을 설치합니다.

    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>Azure Front Door 프로필 만들기
[빠른 시작: 고가용성 글로벌 웹 애플리케이션에 대한 Front Door 만들기](../../frontdoor/quickstart-create-front-door.md)에 설명된 지침에 따라 Azure Front Door 프로필을 만듭니다.

### <a name="define-an-ip-match-condition"></a>IP 일치 조건 정의
[New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) 명령을 사용하여 IP 일치 조건을 정의합니다.
다음 예제에서는 *ip-address-range-1*, *ip-address-range-2* 를 고유한 범위로 바꿉니다.
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  SocketAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```

### <a name="create-a-custom-ip-allow-rule"></a>사용자 지정 IP 허용 규칙 만들기

[New-AzFrontDoorWafCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) 명령을 사용하여 동작을 정의하고 우선 순위를 설정합니다. 다음 예제에서는 목록과 일치하는 클라이언트 IP에서 생성되지 않은 요청이 차단됩니다.

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>WAF 정책 구성
`Get-AzResourceGroup`을 사용하여 Azure Front Door 프로필이 포함된 리소스 그룹의 이름을 찾습니다. 그런 다음, [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)를 사용하여 IP 규칙으로 WAF 정책을 구성합니다.

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Azure Front Door 프런트 엔드 호스트에 WAF 정책 연결

WAF 정책 개체를 기존 프런트 엔드 호스트에 연결하고 Azure Front Door 속성을 업데이트합니다. 먼저 [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)를 사용하여 Azure Front Door 개체를 검색합니다. 그런 다음, [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) 명령을 사용하여 **WebApplicationFirewallPolicyLink** 속성을 이전 단계에서 만든 *$IPAllowPolicyExamplePS* 의 리소스 ID로 설정합니다.

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> 이 예제에서는 WAF 정책이 **FrontendEndpoints[0]** 에 적용됩니다. WAF 정책을 임의의 프런트 엔드에 연결할 수 있습니다. WAF 정책을 Azure Front Door 프런트 엔드에 연결하려면 **WebApplicationFirewallPolicyLink** 속성을 한 번만 설정하면 됩니다. 이후의 정책 업데이트는 프런트 엔드에 자동으로 적용됩니다.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Resource Manager 템플릿으로 WAF 정책 구성
사용자 지정 IP 제한 규칙을 사용하여 Azure Front Door 정책과 WAF 정책을 만드는 템플릿을 보려면 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-waf-clientip)로 이동합니다.


## <a name="next-steps"></a>다음 단계

- [Azure Front Door 프로필을 만드는](../../frontdoor/quickstart-create-front-door.md) 방법을 알아봅니다.
