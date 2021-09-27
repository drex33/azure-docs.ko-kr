---
title: 'ExpressRoute: 보급 경로에 대한 사용자 지정 경고를 구성하는 방법'
description: 이 문서에서는 Azure Automation 및 Logic Apps를 사용하여 1000 경로 제한에 도달하지 않도록 ExpressRoute 게이트웨이에서 온-프레미스 네트워크로 보급된 경로 수를 모니터링하는 방법을 설명합니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 05/29/2020
ms.author: duau
ms.openlocfilehash: 90ce78bfab0cacbaea871402394fe8963775844c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124824678"
---
# <a name="configure-custom-alerts-to-monitor-advertised-routes"></a>보급 경로를 모니터링하기 위해 사용자 지정 경고 구성

이 문서는 Azure Automation 및 Logic Apps를 사용하여 ExpressRoute 게이트웨이에서 온-프레미스 네트워크로 보급된 경로 수를 지속적으로 모니터링하는 데 유용합니다. 모니터링을 통해 1000 경로 제한에 도달하는 것을 방지할 수 있습니다](expressroute-faqs.md#how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering).

**Azure Automation** 을 사용하여 *Runbook* 에 저장된 사용자 지정 PowerShell 스크립트의 실행을 자동화할 수 있습니다. 이 문서의 구성을 사용하는 경우 Runbook에는 하나 이상의 ExpressRoute 게이트웨이를 쿼리하는 PowerShell 스크립트가 포함됩니다. 리소스 그룹, ExpressRoute 게이트웨이 이름 및 온-프레미스에 보급된 네트워크 접두사 수가 포함된 데이터 집합을 수집합니다.

**Azure Logic Apps** 는 Azure Automation Runbook을 호출하는 사용자 지정 워크플로를 예약합니다. Runbook 실행은 작업을 통해 수행됩니다. 데이터 수집이 실행된 후 Azure Logic Apps 워크플로는 데이터를 분류하고 미리 정의할 수 있는 임계값 이상인 네트워크 접두사 수에 대한 일치 조건에 따라 정보를 대상 메일 주소로 보냅니다.

### <a name="workflow"></a><a name="workflow"></a>워크플로

사용자 지정 경고는 다음과 같은 세 가지 주요 단계를 기반으로 설정됩니다.

1. “실행” 계정 및 사용 권한을 사용하여 Automation 계정을 만듭니다.

2. Runbook을 만들고 구성합니다.

3. Automation 계정을 실행할 논리 앱을 만들고 숫자가 임계값(예: 160)보다 큰 경우 경고 메일을 보냅니다.

## <a name="before-you-begin"></a><a name="before"></a>시작하기 전에

구성을 시작하기 전에 다음 기준을 충족하는지 확인합니다.

* 배포에 최소 하나의 ExpressRoute 게이트웨이가 포함됩니다.

* Azure Automation의 [실행 계정](../automation/manage-runas-account.md)에 대한 기본적인 지식이 있습니다.

* [Azure Logic Apps](../logic-apps/logic-apps-overview.md)에 익숙합니다.

* Azure PowerShell 사용에 익숙합니다. ExpressRoute 게이트웨이에서 네트워크 접두사를 수집하려면 Azure PowerShell이 필요합니다. Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

### <a name="notes-and-limitations"></a><a name="limitations"></a>참고 사항 및 제한 사항

* 이 문서에서 설명하는 사용자 지정 경고는 더 나은 작업과 제어를 위한 추가 기능입니다. ExpressRoute에서 기본 경고를 대체하는 수단이 아닙니다.
* ExpressRoute 게이트웨이에 대한 데이터 수집은 백그라운드에서 실행됩니다. 런타임이 예상보다 길어질 수 있습니다. 작업 큐를 방지하려면 워크플로 되풀이를 올바르게 설정해야 합니다.
* 스크립트나 ARM 템플릿에서 배포하면 사용자 지정 경보 트리거보다 빠르게 수행될 수 있습니다. 이로 인해 1000 경로 제한을 초과하는 ExpressRoute 게이트웨이의 네트워크 접두사 수가 증가할 수 있습니다.

## <a name="create-and-configure-accounts"></a><a name="accounts"></a>계정 만들고 구성하기

Azure Portal에서 Automation 계정을 만드는 경우 [실행](../automation/automation-security-overview.md#run-as-accounts) 계정이 자동으로 만들어집니다. 이 계정은 다음 작업을 수행합니다.

* 자체 서명된 인증서를 사용하여 Azure AD(Azure Active Directory) 애플리케이션을 만듭니다. 실행 계정에는 기본적으로 매년 갱신해야 하는 인증서가 있습니다.

* Azure AD에서 애플리케이션에 대한 서비스 주체 계정을 만듭니다.

* 사용 중인 Azure 구독에 대한 참가자 역할(Azure RBAC)을 자신에게 할당합니다. 이 역할은 Runbook을 사용하여 Azure Resource Manager 리소스를 관리합니다.

Automation 계정을 만들려면 권한과 사용 권한이 필요합니다. 자세한 내용은 [Automation 계정을 만드는 데 필요한 권한](../automation/automation-create-standalone-account.md#permissions-required-to-create-an-automation-account)을 참조하세요.

### <a name="1-create-an-automation-account"></a><a name="about"></a>1. Automation 계정 만들기

실행 권한을 사용하여 Azure Automation 계정을 만듭니다. 자세한 내용은 [Azure Automation 계정 만들기](../automation/quickstarts/create-account-portal.md)를 참조하세요.

:::image type="content" source="./media/custom-route-alert-portal/create-account.png" alt-text="Automation 계정 추가" lightbox="./media/custom-route-alert-portal/create-account-expand.png":::

### <a name="2-assign-the-run-as-account-a-role"></a><a name="about"></a>2. 실행 계정에 역할 할당

기본적으로 **참가자** 역할은 **실행** 계정에서 사용하는 서비스 주체에 할당됩니다. 기본 역할을 서비스 주체에 할당된 상태로 유지하거나 [기본 제공 역할](../role-based-access-control/built-in-roles.md)(예: 읽기 권한자) 또는 [사용자 지정 역할](../active-directory/roles/custom-create.md)을 할당하여 권한을 제한할 수 있습니다.

 다음 단계를 사용하여 실행 계정에서 사용하는 서비스 주체에 할당된 역할을 확인합니다.

1. Automation 계정으로 이동합니다. **계정 설정** 으로 이동하여 **실행 계정** 을 선택합니다.

2. **역할** 을 선택하여 사용 중인 역할 정의를 찾습니다.

   :::image type="content" source="./media/custom-route-alert-portal/run-as-account-permissions.png" alt-text="역할 할당":::

## <a name="create-and-configure-runbooks"></a><a name="runbooks"></a>Runbook 만들기 및 구성

### <a name="1-install-modules"></a><a name="install-modules"></a>1. 모듈 설치

Azure Automation Runbook에서 PowerShell cmdlet을 실행하려면 몇 가지 추가적인 Azure PowerShell Az 모듈을 설치해야 합니다. 다음 단계에 따라 모듈을 설치합니다.

1. Azure Automation 계정을 열고 **모듈** 로 이동합니다.

   :::image type="content" source="./media/custom-route-alert-portal/navigate-modules.png" alt-text="모듈에 이동":::

2. 갤러리를 검색해 다음 모듈을 가져옵니다. **Az.Accounts**, **Az.Network**, **Az.Automation**, **Az.Profile**.

   :::image type="content" source="./media/custom-route-alert-portal/import-modules.png" alt-text="모듈 검색 및 가져오기" lightbox="./media/custom-route-alert-portal/import-modules-expand.png":::
  
### <a name="2-create-a-runbook"></a><a name="create"></a>2. Runbook 만들기

1. PowerShell Runbook을 만들려면 Automation 계정으로 이동합니다. **프로세스 자동화** 에서 **Runbook** 타일을 선택한 다음, **Runbook 만들기** 를 선택합니다.

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook.png" alt-text="Runbook을 만듭니다.":::

2. **만들기** 를 선택하여 Runbook을 만듭니다.

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook-2.png" alt-text="만들기를 선택합니다.":::

3. 새로 만든 Runbook을 선택한 후 **편집** 을 선택합니다.

   :::image type="content" source="./media/custom-route-alert-portal/edit-runbook.png" alt-text="Runbook 편집":::

4. **편집** 에서 PowerShell 스크립트를 붙여넣습니다. [예제 스크립트](#script)를 수정하여 하나 이상의 리소스 그룹에서 ExpressRoute 게이트웨이를 모니터링하는 데 사용할 수 있습니다.

   예제 스크립트에서 다음 설정을 확인합니다.

    * **$rgList** 배열에는 ExpressRoute 게이트웨이를 사용하는 리소스 그룹 목록이 포함됩니다. 목록 기반 ExpressRoute 게이트웨이를 사용자 지정할 수 있습니다.
    * **$thresholdNumRoutes** 변수는 ExpressRoute 게이트웨이에서 온-프레미스 네트워크로 보급된 네트워크 접두사 수에 대한 임계값을 정의합니다.

#### <a name="example-script"></a><a name="script"></a>예제 스크립트

```powershell
  ################# Input parameters #################
# Resource Group Name where the ExR GWs resides in
$rgList= @('ASH-Cust10-02','ASH-Cust30')  
$thresholdNumRoutes = 160
###################################################

# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process | Out-Null

Try {

   $conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
   while(!($connectionResult) -And ($logonAttempt -le 5))
   {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =  Connect-AzAccount `
                               -ServicePrincipal `
                               -ApplicationId $conn.ApplicationId `
                               -Tenant $conn.TenantId `
                               -CertificateThumbprint $conn.CertificateThumbprint `
                               -Subscription $conn.SubscriptionId `
                               -Environment AzureCloud 
                               
        Start-Sleep -Seconds 10
    }
} Catch {
    if (!$conn)
    {
        $ErrorMessage = "Service principal not found."
        throw $ErrorMessage
    } 
    else
    {
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Get the name of the Azure subscription
$subscriptionName=(Get-AzSubscription -SubscriptionId $conn.SubscriptionId).Name

#write-Output "<br>$(Get-Date) - selection of the Azure subscription: $subscriptionName" 
Select-AzSubscription -SubscriptionId $conn.SubscriptionId | Out-Null


$GtwList = @()
$results= @()

foreach ($rgName in $rgList)
{
## Collect all the ExpressRoute gateways in a Resource Group
$GtwList=Get-AzVirtualNetworkGateway -ResourceGroupName $rgName 

## For each ExpressRoute gateway, get the IP addresses of the BGP peers and collect the number of routes advertised 
foreach ($gw in $GtwList) {
  
  $peers = Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName


 if ($peers[0].State -eq 'Connected') {
   $routes1=$null
   $routes1 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[0].Neighbor
 }
  if ($peers[1].State -eq 'Connected') {
  
   $routes2=$null
   $routes2 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[1].Neighbor
 }
 
  $sampleTime=(Get-Date).ToString("dd-MM-yyyy HH:mm:ss")
  if ($routes1.Count -eq $routes2.Count)
  {
     
     if ($routes1.Count -lt $thresholdNumRoutes){
       $status='OK'
       $alertMsg='number of routes below threshold'
     } 
     else {
       $status='ALERT'
       $alertMsg='number of routes above threshold'
     }
  } 
  else
  {
     $status='WARNING'
     $alertMsg='check ER Gateway'
  }
  
  $obj = [psCustomObject]@{
            resourceGroup =$rgName
            nameGtw  = $gw.Name
            peer1 = $peers[0].Neighbor
            peer2 = $peers[1].Neighbor
            numRoutesPeer1=  $routes1.Count
            numRoutesPeer2=  $routes2.Count
            time=$sampleTime
            status=$status
            alertMessage = $alertMsg
        }
  $results += $obj
} ### end foreach gateways in each resource group
} ### end foreach resource group
$jsonResults= ConvertTo-Json $results -Depth 100
Write-Output  $jsonResults
 
 ```

### <a name="3-save-and-publish-the-runbook"></a><a name="publish"></a>3. Runbook 저장 및 게시

1. **저장** 을 선택해 Runbook의 초안 복사본을 저장합니다.
2. **게시** 를 선택하여 이 Runbook을 Automation 계정의 공식적인 Runbook 버전으로 게시합니다.

   :::image type="content" source="./media/custom-route-alert-portal/save-publish-runbook.png" alt-text="Runbook을 저장하고 게시합니다.":::

PowerShell 스크립트를 실행하면 다음에 해당하는 값이 수집됩니다.
 
* Resource group

* ExpressRoute 게이트웨이 이름

* 첫 번째 BGP 피어의 IP 주소(peer1)

* 두 번째 BGP 피어의 IP 주소(peer2)

* ExpressRoute 게이트웨이에서 첫 번째 BGP 피어(peer1)로 보급된 네트워크 접두사 수

* ExpressRoute 게이트웨이에서 두 번째 BGP 피어(peer2)로 보급된 네트워크 접두사 수

* 타임스탬프

* 다음으로 분류된 상태

  * 경로 수가 임계값보다 작은 경우 ‘OK’
  * 경로 수가 임계값을 초과하는 경우 ‘ALERT’
  * 두 BGP 피어에 보급된 네트워크 접두사 수가 다른 경우 ‘WARNING’

* 상태(OK, ALERT, WARNING)에 대해 자세히 설명하는 경고 메시지

PowerShell 스크립트는 수집된 정보를 JSON 출력으로 변환합니다. Runbook은 출력 스트리밍으로 PowerShell cmdlet [Write-Output](/powershell/module/Microsoft.PowerShell.Utility/Write-Output)을 사용하여 정보를 클라이언트에 전달합니다.

### <a name="4-validate-the-runbook"></a><a name="validate"></a>4. Runbook의 유효성 검사

Runbook을 만든 후에는 유효성 검사를 해야 합니다. **시작** 을 선택하고 다른 작업 스트림에 대한 출력 및 오류를 확인합니다.

:::image type="content" source="./media/custom-route-alert-portal/validate-runbook.png" alt-text="Runbook의 유효성 검사" lightbox="./media/custom-route-alert-portal/validate-runbook-expand.png":::

## <a name="create-and-configure-a-logic-app"></a><a name="logic"></a>논리 앱을 만들고 구성

Azure Logic Apps는 모든 컬렉션 및 작업 프로세스의 오케스트레이터입니다. 다음 섹션에서는 논리 앱을 사용하여 워크플로를 빌드합니다.

### <a name="workflow"></a>워크플로

이 논리 앱의 경우 ExpressRoute 게이트웨이를 정기적으로 모니터링하는 워크플로를 빌드합니다. 새 항목이 있으면 워크플로에서 각 항목에 대한 이메일을 보냅니다. 여기까지 모두 마치면 워크플로가 고수준의 이 예제와 비슷하게 보입니다.

:::image type="content" source="./media/custom-route-alert-portal/logic-apps-workflow.png" alt-text="Logic Apps 워크플로":::

### <a name="1-create-a-logic-app"></a>1. 논리 앱 만들기

**논리 앱 디자이너** 에서 **빈 논리 앱** 템플릿을 사용하여 논리 앱을 만듭니다. 단계의 경우 [첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

:::image type="content" source="./media/custom-route-alert-portal/blank-template.png" alt-text="새 템플릿":::

### <a name="2-add-a-trigger"></a>2. 트리거 추가

모든 워크플로는 트리거로 시작합니다. 특정 이벤트가 발생하거나 특정 조건이 충족될 때 트리거가 발생합니다. 트리거가 작동할 때마다 Azure Logic Apps는 새 워크플로 인스턴스를 만들고 실행합니다.

미리 정의된 시간 일정을 기반으로 하는 워크플로를 정기적으로 실행하려면 기본 제공 **되풀이** 트리거를 워크플로에 추가합니다. 검색 상자에 **일정** 을 입력합니다. **일정** 아이콘을 선택합니다. 트리거 목록에서 **되풀이** 를 선택합니다.

:::image type="content" source="./media/custom-route-alert-portal/schedule.png" alt-text="되풀이: 일정":::

되풀이 트리거에서 표준 시간대와 해당 워크플로를 반복하는 되풀이를 설정할 수 있습니다. 간격과 빈도는 워크플로의 트리거에 대한 일정을 정의합니다. 적절한 최소 되풀이 빈도를 설정하려면 다음 요소를 고려하세요.

* Automation Runbook의 PowerShell 스크립트를 완료하는 데는 시간이 걸립니다. 런타임은 모니터링할 ExpressRoute 게이트웨이의 수에 따라 달라집니다. 되풀이 빈도가 너무 짧으면 작업 큐가 발생합니다.

* PowerShell 스크립트는 백그라운드에서 작업으로 실행됩니다. 이는 즉시 시작되지 않고, 변수 연기 후에 실행됩니다.

* 반복 빈도가 너무 짧으면 Azure ExpressRoute 게이트웨이에서 불필요한 부하를 생성합니다.

워크플로 구성이 끝나면 워크플로를 몇 번 실행하여 되풀이 빈도의 일관성을 확인한 다음 **실행 기록** 에서 결과를 확인할 수 있습니다.

:::image type="content" source="./media/custom-route-alert-portal/recurrence.png" alt-text="되풀이 간격 및 빈도 값을 보여 주는 스크린샷." lightbox="./media/custom-route-alert-portal/recurrence-expand.png":::

### <a name="3-create-a-job"></a><a name="job">3. 작업 만들기</a>

논리 앱 워크플로는 커넥터를 통해 다른 앱, 서비스 및 플랫폼에 액세스합니다. 다음 단계는 커넥터를 선택하여 이전에 정의된 Azure Automation 계정에 액세스하는 것입니다.

1. **Logic Apps 디자이너** 의 **되풀이** 에서 **새 단계** 를 선택합니다. **작업 선택** 및 검색 상자 에서 **모두** 를 선택합니다.
2. 검색 상자에 **Azure Automation** 을 입력하고 검색합니다. **작업 만들기** 를 선택합니다. **작업 만들기** 를 사용해 앞에서 만든 Automation Runbook을 실행합니다.

   :::image type="content" source="./media/custom-route-alert-portal/create-job.png" alt-text="작업 만들기":::

3. 서비스 주체를 사용하여 로그인합니다. 기존 서비스 주체를 사용하거나 새 서비스 주체를 만들 수 있습니다. 새 서비스 주체를 만들려면 [포털을 이용해 리소스에 액세스할 수 있는 Azure AD 서비스 주체를 만드는 방법](../active-directory/develop/howto-create-service-principal-portal.md)을 참조하세요. **서비스 주체와 연결** 을 선택합니다.

   :::image type="content" source="./media/custom-route-alert-portal/sign-in.png" alt-text="‘서비스 주체와 연결’ 작업이 강조 표시된 ‘되풀이’ 섹션을 보여 주는 스크린샷.":::

4. **연결 이름** 을 입력하고 **클라이언트 ID**(애플리케이션 ID), **클라이언트 암호** 및 **테넌트 ID** 를 추가합니다. 그런 다음 **만들기** 를 선택합니다.

   :::image type="content" source="./media/custom-route-alert-portal/connect-service-principal.png" alt-text="서비스 주체를 사용하여 연결":::

5. **작업 만들기** 페이지에서 서비스 주체에 Automation 계정을 호스트하는 **리소스 그룹** 에 대한 “읽기 권한자” 역할 및 **Automation 계정** 에 대한 “Automation 작업 연산자”가 있어야 합니다. 또한 **Runbook 이름** 을 새 매개 변수로 추가했는지 확인합니다.

   :::image type="content" source="./media/custom-route-alert-portal/roles.png" alt-text="Runbook 이름을 확인할 수 있는 되풀이 작업 값 만들기를 보여 주는 스크린샷." lightbox="./media/custom-route-alert-portal/roles-expand.png":::

### <a name="4-get-the-job-output"></a><a name="output"></a>4. 작업 출력 가져오기

1. **새 단계** 를 선택합니다. “Azure Automation”을 검색합니다. **작업** 목록에서 **작업 출력 가져오기** 를 선택합니다.

   :::image type="content" source="./media/custom-route-alert-portal/get-output.png" alt-text="작업 출력 가져오기":::

2. **작업 출력 가져오기** 페이지에서 Automation 계정에 액세스하는 데 필요한 정보를 지정합니다. 사용하려는 **구독, 리소스 그룹** 과 **Automation 계정을** 선택합니다. **작업 ID** 상자 내부를 클릭합니다. **동적 콘텐츠** 목록이 표시되면 **작업 ID** 를 선택합니다.

   :::image type="content" source="./media/custom-route-alert-portal/job-id.png" alt-text="작업 ID" lightbox="./media/custom-route-alert-portal/job-id-expand.png":::

### <a name="5-parse-the-json"></a><a name="parse"></a>5. JSON 구문 분석

‘Azure Automation 작업 만들기 작업’(이전 단계)의 출력에 포함된 정보는 JSON 개체를 생성합니다. 기본 제공 **JSON 구문 분석** 동작은 JSON 콘텐츠의 속성 및 해당 값에서 사용자에게 친숙한 토큰을 만듭니다. 그 다음 워크플로에서 해당 속성을 사용할 수 있습니다.

1. 작업을 추가합니다. **작업 출력 가져오기 ->작업** 아래에서 **새 단계** 를 선택합니다.
2. **작업 선택** 검색 상자에 “JSON 구문 분석”을 입력하여 이 작업을 제안하는 커넥터를 검색합니다. **작업** 목록에서 사용하려는 데이터 작업에 대한 **JSON 구문 분석** 작업을 선택합니다.

   :::image type="content" source="./media/custom-route-alert-portal/parse-json.png" alt-text="Parse JSON":::

3. **콘텐츠** 상자 내부를 클릭합니다. 동적 콘텐츠 목록이 나타나면 **콘텐츠** 를 선택합니다.

   :::image type="content" source="./media/custom-route-alert-portal/content.png" alt-text="콘텐츠가 선택된 JSON 구문 분석 대화 상자를 보여 주는 스크린샷." lightbox="./media/custom-route-alert-portal/content-expand.png":::

4. JSON을 구문 분석하려면 스키마가 필요합니다. 자동화 Runbook의 출력으로 스키마를 생성할 수 있습니다. 새 웹 브라우저 세션을 열고, Automation Runbook을 실행하여 출력을 가져옵니다. **Logic Apps JSON 데이터 구문 분석** 작업으로 돌아갑니다. 페이지 맨 아래에서 **샘플 페이로드를 사용하여 스키마 생성** 을 선택합니다.

   :::image type="content" source="./media/custom-route-alert-portal/sample-payload.png" alt-text="샘플 페이로드를 사용하여 스키마 생성":::

5. **샘플 JSON 페이로드를 입력하거나 붙여넣으려면** Automation Runbook의 출력을 붙여넣고 **완료** 를 선택합니다.

   :::image type="content" source="./media/custom-route-alert-portal/paste-payload.png" alt-text="샘플 페이로드 붙여넣기" lightbox="./media/custom-route-alert-portal/paste-payload-expand.png":::

6. JSON 입력 페이로드를 구문 분석하여 스키마가 자동으로 생성됩니다.

   :::image type="content" source="./media/custom-route-alert-portal/generate-schema.png" alt-text="스키마 생성" lightbox="./media/custom-route-alert-portal/generate-schema-expand.png":::

### <a name="6-define-and-initialize-a-variable"></a><a name="define-variable"></a>6. 변수의 정의 및 초기화

워크플로의 이 단계에서는 메일로 경보를 전송하는 조건을 만듭니다. 메일 본문 메시지에 유연한 사용자 지정 서식 지정을 가능케 하기 위해 워크플로에 보조 변수가 도입되었습니다.

1. **작업 출력 가져오기 작업** 아래에서 **새 단계** 를 선택합니다. 검색 상자에서 **변수** 를 찾아 선택합니다.

   :::image type="content" source="./media/custom-route-alert-portal/variables.png" alt-text="검색 상자에 변수가 있고 변수가 선택된 작업 선택 대화 상자를 보여 주는 스크린샷.":::

2. **작업** 목록에서 **변수 초기화** 작업을 선택합니다.

   :::image type="content" source="./media/custom-route-alert-portal/initialize-variables.png" alt-text="변수 초기화":::

3. 변수의 이름을 지정합니다. **형식** 에서 **문자열** 을 선택합니다. 변수의 **값** 은 나중에 워크플로에서 할당됩니다.

   :::image type="content" source="./media/custom-route-alert-portal/string.png" alt-text="이름, 형식 및 값을 입력할 수 있는 초기화 변수와 연결된 JSON 구문 분석을 보여 주는 스크린샷." lightbox="./media/custom-route-alert-portal/string-expand.png":::

### <a name="7-create-a-for-each-action"></a><a name="cycles-json"></a>7. “For each” 작업 만들기

JSON이 구문 분석되면 **JSON 데이터 구문 분석** 작업은 *본문* 출력 콘텐츠를 저장합니다. 출력을 처리하기 위해 배열의 각 항목에 하나 이상의 작업을 반복하는 “For each” 루프를 만들 수 있습니다.

1. **변수 초기화** 에서 **작업 추가** 를 선택합니다. 검색 상자에서 필터로 “for each”를 입력합니다.

   :::image type="content" source="./media/custom-route-alert-portal/control.png" alt-text="검색 상자에 for each가 입력됐고 컨트롤이 선택된 작업 선택 대화 상자를 보여 주는 스크린샷.":::

2. **작업** 목록에서 **for each - 컨트롤** 을 선택합니다.

   :::image type="content" source="./media/custom-route-alert-portal/for-each.png" alt-text="For each - 컨트롤":::

3. **이전 단계의 출력 선택** 텍스트 상자를 클릭합니다. **동적 콘텐츠** 목록이 표시되면 구문 분석된 JSON에서 출력된 **본문** 을 선택합니다.

   :::image type="content" source="./media/custom-route-alert-portal/body.png" alt-text="이전 단계의 출력 선택 텍스트 상자를 포함하는, For each에 연결된 초기화된 변수를 보여 주는 스크린샷.":::

4. JSON 본문의 각 요소에 조건을 설정하려고 합니다. 작업 그룹에서 **제어** 를 선택합니다.

   :::image type="content" source="./media/custom-route-alert-portal/condition-control.png" alt-text="제어":::

5. **작업** 목록에서 **조건-제어** 를 선택합니다. 이 조건-제어는 워크플로의 데이터를 특정한 값이나 필드와 비교하는 컨트롤 구조체입니다. 그런 다음, 데이터가 조건을 충족하는지 여부에 따라 실행되는 다양한 작업을 지정할 수 있습니다.

   :::image type="content" source="./media/custom-route-alert-portal/condition.png" alt-text="조건 제어":::

6. **조건** 동작의 루트에서 논리 작업을 **또는** 으로 변경합니다.

   :::image type="content" source="./media/custom-route-alert-portal/condition-or.png" alt-text="Or" lightbox="./media/custom-route-alert-portal/condition-or-expand.png":::

7. ExpressRoute 게이트웨이가 두 BGP 피어에 보급하는 네트워크 접두사 수의 값을 확인합니다. 경로 수는 **동적 콘텐츠** 의 “numRoutePeer1” 및 “numRoutePeer2”로 확인할 수 있습니다. 값 상자에 **numRoutePeer1** 의 값을 입력합니다.

   :::image type="content" source="./media/custom-route-alert-portal/peer-1.png" alt-text="numRoutesPeer1":::

8. 조건에 다른 행을 추가하려면 **추가 -> 행 추가** 를 선택합니다. 두 번째 상자의 **동적 콘텐츠** 에서 **numRoutePeer2** 를 선택합니다.

   :::image type="content" source="./media/custom-route-alert-portal/peer-2.png" alt-text="numRoutesPeer2":::

9. 논리 조건은 두 개의 동적 변수 numRoute1 또는 numRoute2 중 하나가 임계값보다 크면 true입니다. 이 예제에서 임계값은 800(1000 경로의 최댓값의 80%)로 고정됩니다. 요구 사항에 맞게 임계값을 변경할 수 있습니다. 이 값은 일관성을 위해 Runbook PowerShell 스크립트에 사용된 값과 같아야 합니다.

   :::image type="content" source="./media/custom-route-alert-portal/logic-condition.png" alt-text="논리 조건":::

10. **true인 경우** 아래에서 메일을 통해 경고를 보낼 작업의 형식을 지정하고 작업을 만듭니다. **작업 선택에서 **변수** 를 검색한 다음 선택합니다.

    :::image type="content" source="./media/custom-route-alert-portal/condition-if-true.png" alt-text="true인 경우":::

11. 변수에서 **작업 추가** 를 선택합니다. **작업** 목록에서 **변수 설정** 을 선택합니다.

    :::image type="content" source="./media/custom-route-alert-portal/condition-set-variable.png" alt-text="‘작업’ 탭을 선택하고 ‘변수 설정’이 강조 표시된 ‘변수’ 섹션의 스크린샷.":::

12. **이름** 에서 이전에 만든 **EmailBody** 라는 변수를 선택합니다. **값** 에는 경고 메일의 서식을 지정하는 데 필요한 HTML 스크립트를 붙여넣습니다. JSON 본문의 값을 포함하려면 **동적 콘텐츠** 를 사용합니다. 해당 설정을 구성한 후에는 변수 **Emailbody** 가 경고와 관련된 모든 정보를 HTML 형식으로 포함합니다.

    :::image type="content" source="./media/custom-route-alert-portal/paste-script.png" alt-text="변수 설정":::

### <a name="8-add-the-email-connector"></a><a name="email"></a>8. 메일 커넥터 추가

Logic Apps에서는 여러 메일 커넥터를 쓸 수 있습니다. 이 예제에서는 메일을 통해 경고를 보내도록 Outlook 커넥터를 추가합니다. **변수 설정** 에서 **작업 추가** 를 선택합니다. **작업 선택** 에서 검색 상자에 “메일 보내기”를 입력합니다.

1. **Office 365 Outlook** 을 선택합니다.

   :::image type="content" source="./media/custom-route-alert-portal/email.png" alt-text="이메일 보내기":::

2. **작업** 목록에서 **메일 보내기(V2)** 를 선택합니다.

   :::image type="content" source="./media/custom-route-alert-portal/email-v2.png" alt-text="메일 보내기(V2)":::

3. 로그인하여 Office 365 Outlook에 대한 연결을 만듭니다.

   :::image type="content" source="./media/custom-route-alert-portal/office-365.png" alt-text="로그인":::

4. **본문** 필드에서 **동적 콘텐츠 추가** 를 클릭합니다. 동적 콘텐츠 패널에서 **Emailbody** 변수를 추가합니다. **제목** 및 **받는 주소** 필드의 내용을 입력합니다.

   :::image type="content" source="./media/custom-route-alert-portal/emailbody.png" alt-text="본문":::

5. **메일 보내기(v2)** 작업으로 워크플로 설정이 완료됩니다.

   :::image type="content" source="./media/custom-route-alert-portal/send-email-v2.png" alt-text="메일 보내기 v2" lightbox="./media/custom-route-alert-portal/send-email-v2-expand.png":::

### <a name="9-workflow-validation"></a><a name="validation"></a>9. 워크플로 유효성 검사

최종 단계는 워크플로의 유효성 검사입니다. **Logic Apps 개요** 에서 **트리거 실행** 을 선택합니다. **되풀이** 를 선택합니다. **실행 기록** 에서 워크플로를 모니터링하고 확인할 수 있습니다.

:::image type="content" source="./media/custom-route-alert-portal/trigger.png" alt-text="트리거 실행":::

## <a name="next-steps"></a>다음 단계

워크플로를 사용자 지정하는 방법에 대한 자세한 내용은 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)를 참조하세요.