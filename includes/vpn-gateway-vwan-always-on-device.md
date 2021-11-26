---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/26/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0c901fcada708f800c84ec20fb03bcfda852d765
ms.sourcegitcommit: 8178cd2d9a47a67bb324483bd0879a57591706a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2021
ms.locfileid: "133110424"
---
디바이스 터널을 성공적으로 설정하려면 다음 요구 사항이 충족되어야 합니다.

* 디바이스는 Windows 10 Enterprise 또는 Education 버전 1809 이상을 실행하는 도메인 가입 컴퓨터여야 합니다.
* 터널은 Windows 기본 제공 VPN 솔루션에 대해서만 구성할 수 있으며 컴퓨터 인증서 인증과 함께 IKEv2를 사용하여 설정됩니다.
* 디바이스마다 디바이스 터널을 하나만 구성할 수 있습니다.

1. [지점 및 사이트 간 VPN 클라이언트](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md) 문서를 사용하여 Windows 10 클라이언트에 클라이언트 인증서를 설치합니다. 로컬 컴퓨터 저장소에 인증서를 저장해야 합니다.
1. VPN 프로필을 만들고 [이러한 지침](/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration)을 사용하여 LOCAL SYSTEM 계정의 컨텍스트에서 디바이스 터널을 구성합니다.

### <a name="configuration-example-for-device-tunnel"></a>디바이스 터널에 대한 구성 예

가상 네트워크 게이트웨이를 구성하고 Windows 10 클라이언트의 로컬 머신 저장소에 클라이언트 인증서를 설치한 후 다음 예제를 사용하여 클라이언트 디바이스 터널을 구성합니다.

1. 다음 텍스트를 복사하고 ***devicecert.ps1*** 로 저장합니다.

   ```
   Param(
   [string]$xmlFilePath,
   [string]$ProfileName
   )

   $a = Test-Path $xmlFilePath
   echo $a

   $ProfileXML = Get-Content $xmlFilePath

   echo $XML

   $ProfileNameEscaped = $ProfileName -replace ' ', '%20'

   $Version = 201606090004

   $ProfileXML = $ProfileXML -replace '<', '&lt;'
   $ProfileXML = $ProfileXML -replace '>', '&gt;'
   $ProfileXML = $ProfileXML -replace '"', '&quot;'

   $nodeCSPURI = './Vendor/MSFT/VPNv2'
   $namespaceName = "root\cimv2\mdm\dmmap"
   $className = "MDM_VPNv2_01"

   $session = New-CimSession

   try
   {
   $newInstance = New-Object Microsoft.Management.Infrastructure.CimInstance $className, $namespaceName
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ParentID", "$nodeCSPURI", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("InstanceID", "$ProfileNameEscaped", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ProfileXML", "$ProfileXML", 'String', 'Property')
   $newInstance.CimInstanceProperties.Add($property)

   $session.CreateInstance($namespaceName, $newInstance)
   $Message = "Created $ProfileName profile."
   Write-Host "$Message"
   }
   catch [Exception]
   {
   $Message = "Unable to create $ProfileName profile: $_"
   Write-Host "$Message"
   exit
   }
   $Message = "Complete."
   Write-Host "$Message"
   ```
1. 다음 텍스트를 복사하고 _*devicecert.ps1**과 동일한 폴더에 ***VPNProfile.xml** _로 저장합니다. 사용자 환경에 맞게 다음 텍스트를 편집합니다.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers> <= Can be found in the VpnSettings.xml in the downloaded profile zip file`
   * `<Address>192.168.3.5</Address> <= IP of resource in the vnet or the vnet address space`
   * `<Address>192.168.3.4</Address> <= IP of resource in the vnet or the vnet address space`

   ```
   <VPNProfile>  
     <NativeProfile>  
   <Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  
   <NativeProtocolType>IKEv2</NativeProtocolType>  
   <Authentication>  
     <MachineMethod>Certificate</MachineMethod>  
   </Authentication>  
   <RoutingPolicyType>SplitTunnel</RoutingPolicyType>  
    <!-- disable the addition of a class based route for the assigned IP address on the VPN interface -->
   <DisableClassBasedDefaultRoute>true</DisableClassBasedDefaultRoute>  
     </NativeProfile> 
     <!-- use host routes(/32) to prevent routing conflicts -->  
     <Route>  
   <Address>192.168.3.5</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
     <Route>  
   <Address>192.168.3.4</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
   <!-- need to specify always on = true --> 
     <AlwaysOn>true</AlwaysOn> 
   <!-- new node to specify that this is a device tunnel -->  
    <DeviceTunnel>true</DeviceTunnel>
   <!--new node to register client IP address in DNS to enable manage out -->
   <RegisterDNS>true</RegisterDNS>
   </VPNProfile>
   ```
1. [Sysinternals](/sysinternals/downloads/psexec)에서 **PsExec** 를 다운로드하고 **C:\PSTools** 에 파일을 추출합니다.
1. 관리자 CMD 프롬프트에서 다음을 실행하여 PowerShell을 시작합니다.
   
   32비트 Windows:
   
   ```
   PsExec.exe -s -i powershell
   ```
   
   64비트 Windows:
   
   ```
   PsExec64.exe -s -i powershell
   ```

   ![스크린샷에서는 64비트 버전의 PowerShell을 시작하는 명령이 포함된 명령 프롬프트 창을 보여줍니다.](./media/vpn-gateway-vwan-always-on-device/powershell.png)
1. PowerShell에서 **devicecert.ps1** 과 **VPNProfile.xml** 이 있는 폴더로 전환하고 다음 명령을 실행합니다.

   ```powershell
   .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![스크린샷에서는 devicesert 스크립트를 사용하여 MachineCertTest를 실행한 PowerShell 창을 보여줍니다.](./media/vpn-gateway-vwan-always-on-device/machinecerttest.png)
1. **rasphone** 을 실행합니다.

   ![스크린샷에서는 rasphone이 선택된 실행 대화 상자를 보여줍니다.](./media/vpn-gateway-vwan-always-on-device/rasphone.png)
1. **MachineCertTest** 항목을 찾아 **연결** 을 클릭합니다.

   ![스크린샷에서는 MachineCertTest가 선택되고 연결 단추가 있는 네트워크 연결 대화 상자를 보여줍니다.](./media/vpn-gateway-vwan-always-on-device/connect.png)
1. 연결에 성공하면 컴퓨터를 다시 부팅합니다. 터널이 자동으로 연결됩니다.
