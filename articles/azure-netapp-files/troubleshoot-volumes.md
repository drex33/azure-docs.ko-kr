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
ms.openlocfilehash: f46b6f0c0648dcc354f170548a4273188c4871e6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130219738"
---
# <a name="troubleshoot-volume-errors-for-azure-netapp-files"></a>Azure NetApp Files에 대 한 볼륨 오류 문제 해결

이 문서에서는 Azure NetApp Files 볼륨 문제를 해결 하는 데 도움이 될 수 있는 오류 메시지 및 해결 방법을 설명 합니다. 

## <a name="errors-for-smb-and-dual-protocol-volumes"></a>SMB 및 이중 프로토콜 볼륨에 대 한 오류

|     오류 조건    |     해결 방법    |
|-|-|
| SMB 또는 이중 프로토콜 볼륨 만들기는 다음 오류로 인해 실패합니다. <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available."}]}` | 이 오류는 DNS에 연결할 수 없음을 나타냅니다. <br> 다음과 같은 해결 방법을 고려해 보십시오. <ul><li>ADDS와 볼륨이 동일한 지역에 배포되고 있는지 확인합니다.</li> <li>ADDS와 볼륨이 동일한 VNet에 배포되고 있는지 확인합니다. 서로 다른 VNET를 사용하는 경우 두 VNet이 서로 피어링하는지 확인하십시오. [Azure NetApp Files 네트워크 계획 지침](azure-netapp-files-network-topologies.md)을 참조하십시오. </li> <li>DNS 서버에 NSG(네트워크 보안 그룹)가 적용되어 있을 수 있습니다.  따라서 트래픽 흐름이 허용되지 않습니다. 이 경우 NSG를 DNS 또는 AD에 개방해 여러 포트에 연결합니다. 포트 요구 사항은 [Active Directory 연결에 대한 요구 사항](create-active-directory-connections.md#requirements-for-active-directory-connections)을 참조하십시오. </li></ul> <br>Azure ADDS에 동일한 솔루션이 적용됩니다. Azure ADDS는 동일한 지역에 배포되어야 합니다. VNet은 동일한 지역에 위치하거나 볼륨에서 사용하는 VNet과 피어링되어야 합니다. | 
| SMB 또는 이중 프로토콜 볼륨 만들기는 다음 오류로 인해 실패합니다. <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-C1C8\". Reason: Kerberos Error: Invalid credentials were given Details: Error: Machine account creation procedure failed\n [ 563] Loaded the preliminary configuration.\n**[ 670] FAILURE: Could not authenticate as 'test@contoso.com':\n** Unknown user (KRB5KDC_ERR_C_PRINCIPAL_UNKNOWN)\n. "}]}`  |  <ul><li>사용자 이름을 정확하게 입력해야 합니다. </li> <li>사용자가 컴퓨터 계정을 만들 수 있는 권한이 있는 관리자 그룹의 일부인지 확인합니다. </li> <li> Azure ADDS를 사용하는 경우 사용자가 Azure AD 그룹 `Azure AD DC Administrators`의 일부인지 확인합니다. </li></ul> | 
| SMB 또는 이중 프로토콜 볼륨 만들기가 다음 오류로 인해 실패합니다. <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-A452\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\n [ 567] Loaded the preliminary configuration.\n [ 671] Successfully connected to ip 10.x.x.x, port 88 using TCP\n**[ 1099] FAILURE: Could not authenticate as\n** 'user@contoso.com': CIFS server account password does\n** not match password stored in Active Directory\n** (KRB5KDC_ERR_PREAUTH_FAILED)\n. "}]}` | AD 연결에 참가하기 위해 입력한 암호가 정확한지 확인합니다. |
| SMB 또는 이중 프로토콜 볼륨 만들기가 다음 오류로 인해 실패합니다. <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError","message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-D9A2\". Reason: SecD Error: ou not found Details: Error: Machine account creation procedure failed\n [ 561] Loaded the preliminary configuration.\n [ 665] Successfully connected to ip 10.x.x.x, port 88 using TCP\n [ 1039] Successfully connected to ip 10.x.x.x, port 389 using TCP\n**[ 1147] FAILURE: Specifed OU 'OU=AADDC Com' does not exist in\n** contoso.com\n. "}]}` | AD 연결에 참가하기 위해 지정한 OU 경로가 올바른지 확인합니다. Azure ADDS를 사용하는 경우 조직 구성 단위 경로가 `OU=AADDC Computers`인지 확인합니다. |
| SMB 또는 이중 프로토콜 볼륨 만들기가 다음 오류로 인해 실패합니다. <br> `Failed to create the Active Directory machine account \"SMB-ANF-VOL. Reason: LDAP Error: Local error occurred Details: Error: Machine account creation procedure failed. [nnn] Loaded the preliminary configuration. [nnn] Successfully connected to ip 10.x.x.x, port 88 using TCP [nnn] Successfully connected to ip 10.x.x.x, port 389 using [nnn] Entry for host-address: 10.x.x.x not found in the current source: FILES. Ignoring and trying next available source [nnn] Source: DNS unavailable. Entry for host-address:10.x.x.x found in any of the available sources\n*[nnn] FAILURE: Unable to SASL bind to LDAP server using GSSAPI: local error [nnn] Additional info: SASL(-1): generic failure: GSSAPI Error: Unspecified GSS failure. Minor code may provide more information (Cannot determine realm for numeric host address) [nnn] Unable to connect to LDAP (Active Directory) service on contoso.com (Error: Local error) [nnn] Unable to make a connection (LDAP (Active Directory):contosa.com, result: 7643. `  | DNS 서버에 AD 호스트 컴퓨터의 포인터(PTR) 레코드가 존재하지 않을 가능성이 있습니다. DNS 서버에 역방향 조회 영역을 만든 다음 해당 역방향 조회 영역에 AD 호스트 컴퓨터의 PTR 레코드를 추가해야 합니다. <br> 예를 들어 AD 컴퓨터의 IP 주소가 `10.x.x.x`이고, `hostname` 명령을 사용하여 발견된 AD 컴퓨터의 호스트 이름이 `AD1`이며, 도메인 이름이 `contoso.com`라고 가정합니다.  역방향 조회 영역에 추가된 PTR 레코드는 `10.x.x.x` -> `contoso.com`이어야 합니다.   | 
| SMB 또는 이중 프로토콜 볼륨 만들기가 다음 오류로 인해 실패합니다. <br> `Failed to create the Active Directory machine account \"SMB-ANF-VOL\". Reason: Kerberos Error: KDC has no support for encryption type Details: Error: Machine account creation procedure failed [nnn]Loaded the preliminary configuration. [nnn]Successfully connected to ip 10.x.x.x, port 88 using TCP [nnn]FAILURE: Could not authenticate as 'contosa.com': KDC has no support for encryption type  (KRB5KDC_ERR_ETYPE_NOSUPP) ` | Active Directory 연결 및 서비스 계정 모두에서 [AES 암호화](./create-active-directory-connections.md#create-an-active-directory-connection)를 사용하도록 설정했는지 확인합니다. |
| SMB 또는 이중 프로토콜 볼륨 만들기가 다음 오류로 인해 실패합니다. <br> `Failed to create the Active Directory machine account \"SMB-NTAP-VOL\". Reason: LDAP Error: Strong authentication is required Details: Error: Machine account creation procedure failed\n [ 338] Loaded the preliminary configuration.\n [ nnn] Successfully connected to ip 10.x.x.x, port 88 using TCP\n [ nnn ] Successfully connected to ip 10.x.x.x, port 389 using TCP\n [ 765] Unable to connect to LDAP (Active Directory) service on\n dc51.area51.com (Error: Strong(er) authentication\n required)\n*[ nnn] FAILURE: Unable to make a connection (LDAP (Active\n* Directory):contoso.com), result: 7609\n. "` | LDAP 서명 옵션은 선택되지 않지만 AD 클라이언트에는 LDAP 서명이 있습니다.  [LDAP 서명 사용](create-active-directory-connections.md#create-an-active-directory-connection)으로 설정하고 다시 시도하십시오. | 

## <a name="errors-for-dual-protocol-volumes"></a>이중 프로토콜 볼륨에 대한 오류

|     오류 조건    |     해결 방법    |
|-|-|
| TLS를 통한 LDAP를 사용하도록 설정했고, 오류 `This Active Directory has no Server root CA Certificate`이 발생하여 이중 프로토콜 볼륨 만들기가 실패합니다.    |     이중 프로토콜 볼륨을 만들 때 이 오류가 발생하는 경우에는 NetApp 계정에 루트 CA 인증서가 업로드되었는지 확인합니다.    |
| 이중 프로토콜 볼륨 만들기가 오류 `Failed to validate LDAP configuration, try again after correcting LDAP configuration`로 인해 실패합니다.    |  DNS 서버에 AD 호스트 컴퓨터의 포인터(PTR) 레코드가 없을 수 있습니다. DNS 서버에 역방향 조회 영역을 만든 다음 해당 역방향 조회 영역에 AD 호스트 컴퓨터의 PTR 레코드를 추가해야 합니다. <br> 예를 들어 AD 컴퓨터의 IP 주소가 `10.x.x.x`이고, `hostname` 명령을 사용하여 발견된 AD 컴퓨터의 호스트 이름이 `AD1`이며, 도메인 이름이 `contoso.com`라고 가정합니다.  역방향 조회 영역에 추가된 PTR 레코드는 `10.x.x.x` -> `contoso.com`이어야 합니다.   |
| 이중 프로토콜 볼륨 만들기가 오류 `Failed to create the Active Directory machine account \\\"TESTAD-C8DD\\\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\\n [ 434] Loaded the preliminary configuration.\\n [ 537] Successfully connected to ip 10.x.x.x, port 88 using TCP\\n**[ 950] FAILURE`로 인해 실패합니다. |     이 오류는 Active Directory가 NetApp 계정에 가입할 때 AD 암호가 잘못되었음을 나타냅니다. 올바른 암호를 사용하여 AD 연결을 업데이트하고 다시 시도하십시오. |
| 이중 프로토콜 볼륨 만들기가 오류 `Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available`로 인해 실패합니다. |   이 오류는 DNS에 연결할 수 없음을 나타냅니다. 잘못된 DNS IP 또는 네트워킹 문제가 원인일 수 있습니다. AD 연결에 입력된 DNS IP를 확인하고 IP가 정확한지 확인하십시오. <br> 또한 AD와 볼륨이 동일한 지역 및 동일한 VNet에 위치해야 합니다. AD와 볼륨이 서로 다른 VNET에 위치한 경우 두 VNet 간에 VNet 피어링이 설정되어 있는지 확인합니다. <br> 자세한 내용은 [Azure NetApp Files 네트워크 계획 지침](azure-netapp-files-network-topologies.md#azure-native-environments)을 참조하세요. |
| 이중 프로토콜 볼륨을 탑재할 때 사용 권한 거부 오류가 발생했습니다. | 이중 프로토콜 볼륨은 NFS 프로토콜과 SMB 프로토콜을 모두 지원합니다.  UNIX 시스템의 탑재된 볼륨에 액세스하려고 할 때 시스템은 사용자가 사용하는 UNIX 사용자와 Windows 사용자의 매핑을 시도합니다. 매핑이 발견되지 않으면 "사용 권한 거부됨" 오류가 발생합니다. <br> 이 상황은 액세스 시 'root' 사용자를 사용하는 경우에도 적용됩니다. <br> "사용 권한 거부됨" 문제를 방지하려면 탑재 지점에 액세스하기 전에 Windows Active Directory에 `pcuser`가 포함되어 있는지 확인하십시오. "사용 권한 거부됨" 문제 발생 후 `pcuser`를 추가하는 경우, 액세스를 다시 시도하기 전에 삭제할 캐시 항목을 24 시간 동안 기다립니다. |

## <a name="errors-for-nfsv41-kerberos-volumes"></a>NFSv 4.1 Kerberos 볼륨에 대 한 오류

|     오류 조건    |     해결 방법    |
|-|-|
|`Error allocating volume - Export policy rules does not match kerberosEnabled flag` | Azure NetApp Files는 NFSv3 볼륨에 대해 Kerberos를 지원하지 않습니다. Kerberos는 NFSv4.1 프로토콜에 대해서만 지원됩니다.  |
|`This NetApp account has no configured Active Directory   connections`  |  **KDC IP** 및 **AD 서버 이름** 필드를 사용하여 NetApp 계정에 대한 Active Directory를 구성합니다. 지침은 [Azure Portal 구성](configure-kerberos-encryption.md#configure-the-azure-portal)을 참조하세요. |
|`Mismatch between KerberosEnabled flag value and ExportPolicyRule's access type parameter values.`  | Azure NetApp Files는 일반 NFSv4.1 볼륨을 Kerberos NFSv4.1 볼륨으로, 또는 그 반대로 변환하는 것을 지원하지 않습니다. |
|`mount.nfs: access denied by server when mounting volume <SMB_SERVER_NAME-XXX.DOMAIN_NAME>/<VOLUME_NAME>` <br>  예: `smb-test-64d9.contoso.com:/nfs41-vol101` | <ol><li> A/PTR 레코드가 올바르게 설정되고 서버 이름 `smb-test-64d9.contoso.com`에 대해 Active Directory에 존재하는지 확인합니다. <br> NFS 클라이언트에서 `smb-test-64d9.contoso.com`의 `nslookup`이 IP 주소 IP1(즉, `10.1.1.68`)로 확인되면 IP1의 `nslookup`은 하나의 레코드(즉, `smb-test-64d9.contoso.com`)로만 확인되어야 합니다. IP1의 `nslookup`은 *절대* 여러 이름으로 확인되어서는 안 됩니다. </li>  <li>PowerShell 또는 UI를 사용하여 AD에서 유형 `NFS-<Smb NETBIOS NAME>-<few random characters>`의 NFS 컴퓨터 계정에 대해 AES-256을 설정합니다. <br> 명령 예: <ul><li>`Set-ADComputer <NFS_MACHINE_ACCOUNT_NAME> -KerberosEncryptionType AES256` </li><li>`Set-ADComputer NFS-SMB-TEST-64 -KerberosEncryptionType AES256` </li></ul> </li> <li>NFS 클라이언트, AD 및 Azure NetApp Files 스토리지 소프트웨어의 시간이 서로 동기화되고 5분 오차 범위 내에 있는지 확인합니다. </li>  <li>`kinit <administrator>` 명령을 사용하여 NFS 클라이언트에서 Kerberos 티켓을 가져옵니다.</li> <li>NFS 클라이언트 호스트 이름을 15자 미만으로 줄이고 영역 결합을 다시 수행합니다. </li><li>다음과 같이 NFS 클라이언트와 `rpcgssd` 서비스를 다시 시작합니다. 명령어는 OS에 따라 다를 수 있습니다.<br> RHEL 7: <br> `service nfs restart` <br> `service rpcgssd restart` <br> CentOS 8: <br> `systemctl enable nfs-client.target && systemctl start nfs-client.target` <br> Ubuntu: <br> (`rpc-gssd` 서비스를 다시 시작합니다.) <br> `sudo systemctl start rpc-gssd.service` </ul>| 
|`mount.nfs: an incorrect mount option was specified`   | 이 문제는 NFS 클라이언트 문제와 관련이 있을 수 있습니다. NFS 클라이언트를 다시 부팅합니다.    |
|`Hostname lookup failed`   | DNS 서버에 역방향 조회 영역을 만든 다음 해당 역방향 조회 영역에 AD 호스트 컴퓨터의 PTR 레코드를 추가해야 합니다. <br> 예를 들어, AD 시스템의 IP 주소가 `10.1.1.4`이고 AD 시스템의 호스트 이름(hostname 명령을 사용하여 찾은 대로)이 `AD1`이고 도메인 이름이 `contoso.com`이라고 가정합니다. 역방향 조회 영역에 추가된 PTR 레코드는 `10.1.1.4 -> AD1.contoso.com`이어야 합니다. |
|`Volume creation fails due to unreachable DNS server`  | 사용 가능한 솔루션은 두 가지입니다. <br> <ul><li> 이 오류는 DNS에 연결할 수 없음을 나타냅니다. 잘못된 DNS IP 또는 네트워킹 문제 때문일 수 있습니다. AD 연결에서 입력한 DNS IP를 확인하고 IP가 맞는지 확인하세요. </li> <li> AD와 볼륨이 동일한 지역 및 동일한 VNet에 있는지 확인합니다. 서로 다른 VNet에 있는 경우 두 VNet 간에 VNet 피어링이 설정되어 있는지 확인합니다. </li></ul> |
|NFSv4.1 Kerberos 볼륨 만들기가 다음 예와 유사한 오류로 인해 실패합니다. <br> `Failed to enable NFS Kerberos on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". Failed to bind service principal name on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". SecD Error: server create fail join user auth.` |KDC IP가 잘못되었으며 Kerberos 볼륨이 만들어졌습니다. KDC IP를 올바른 주소로 업데이트합니다. <br> KDC IP를 업데이트한 후에도 오류가 사라지지 않습니다. 볼륨을 다시 만들어야 합니다. |

## <a name="errors-for-ldap-volumes"></a>LDAP 볼륨에 대한 오류

|     오류 조건    |     해결 방법    |
|-|-|
| ldapEnabled가 true로 설정된 SMB 볼륨을 만드는 동안 오류가 발생했습니다. <br> `Error Message: ldapEnabled option is only supported with NFS protocol volume. ` | LDAP를 사용하도록 설정된 SMB 볼륨을 만들 수 없습니다. <br> LDAP를 사용하지 않도록 설정한 SMB 볼륨을 만듭니다. |
| 기존 볼륨에 대한 ldapEnabled 매개 변수 값을 업데이트하는 동안 오류가 발생했습니다. <br> `Error Message: ldapEnabled parameter is not allowed to update` |  볼륨을 만든 후에는 LDAP 옵션 설정을 수정할 수 없습니다. <br> 만든 볼륨에서 LDAP 옵션 설정을 업데이트하지 마세요. 자세한 내용은 [NFS 볼륨 액세스에 대한 확장 그룹을 사용하여 ADDS LDAP 구성](configure-ldap-extended-groups.md)을 참조하세요. |
| LDAP 지원 NFS 볼륨을 만드는 동안 오류가 발생했습니다. <br> `Could not query DNS server` <br> `Sample error message:` <br> `"log": time="2020-10-21 05:04:04.300" level=info msg=Res method=GET url=/v2/Volumes/070d0d72-d82c-c893-8ce3-17894e56cea3  x-correlation-id=9bb9e9fe-abb6-4eb5-a1e4-9e5fbb838813 x-request-id=c8032cb4-2453-05a9-6d61-31ca4a922d85 xresp="200:  {\"created\":\"2020-10-21T05:02:55.000Z\",\"lifeCycleState\":\"error\",\"lifeCycleStateDetails\":\"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available.\",\"name\":\"smb1\",\"ownerId\ \":\"8c925a51-b913-11e9-b0de-9af5941b8ed0\",\"region\":\"westus2stage\",\"volumeId\":\"070d0d72-d82c-c893-8ce3-` |  이 오류는 DNS에 연결할 수 없기 때문에 발생합니다. <br> <ul><li> Azure NetApp Files에 대해 올바른 사이트(사이트 범위 지정)를 구성했는지 확인합니다. </li><li> DNS에 연결할 수 없는 이유는 잘못된 DNS IP 주소 또는 네트워킹 문제일 수 있습니다. AD 연결에 입력한 DNS IP 주소가 올바른지 확인합니다. </li><li> AD와 볼륨이 동일한 지역 및 동일한 VNet에 있는지 확인합니다. 서로 다른 VNet에 위치한 경우 두 VNet 간에 VNet 피어링이 설정되어 있는지 확인합니다.</li></ul> |
| 스냅샷에서 볼륨을 만드는 동안 오류가 발생했습니다. <br> `Aggregate does not exist` | Azure NetApp Files는 LDAP가 비활성화된 볼륨에 속한 스냅샷에서 새로운 LDAP 지원 볼륨을 프로비전하는 기능을 지원하지 않습니다. <br> 지정된 스냅샷에서 LDAP 사용하지 않도록 설정된 새 볼륨을 만들어 보세요. |

## <a name="errors-for-volume-allocation"></a>볼륨 할당 오류 

새 볼륨을 만들거나 Azure NetApp Files 기존 볼륨의 크기를 조정하면 Microsoft Azure 구독에 스토리지 및 네트워킹 리소스를 할당합니다. 특정 지역의 Azure 서비스에 대한 수요가 1000억 달러 증가하므로 리소스 할당 오류가 발생할 수 있습니다.

이 섹션에서는 몇 가지 일반적인 할당 오류의 원인을 설명하고 가능한 방안을 제안합니다.

|     오류 조건    |     해결 방법    |
|-|-|
|새 볼륨을 만들거나 기존 볼륨의 크기를 조정하는 동안 오류가 발생했습니다. <br> 오류 메시지: `There was a problem locating [or extending] storage  for the volume. Please retry the operation. If the problem persists, contact Support.` | 이 오류는 이 요청에 대한 리소스를 할당하려고 할 때 서비스가 오류가 발생했음을 나타냅니다. <br> 잠시 후 작업을 다시 시도하세요. 문제가 지속되면 지원에 문의합니다.|
|일반 볼륨에 대한 지역의 스토리지 또는 네트워킹 용량이 부족합니다. <br> 오류 메시지: `There are currently insufficient resources available to create [or extend] a volume in this region. Please retry the operation. If the problem persists, contact Support.` | 이 오류는 지역에서 볼륨을 만들거나 크기를 조정하는 데 사용할 수 있는 리소스가 부족함을 나타냅니다. <br> 다음 해결 방법 중 하나를 시도해 보세요. <ul><li>새 VNet에서 볼륨을 만듭니다. 이렇게 하면 네트워킹 관련 리소스 제한에 도달하지 않습니다.</li> <li>잠시 후 다시 시도합니다. 중간에 클러스터, 지역 또는 영역에서 리소스가 해제되었을 수 있습니다.</li></ul> |
|네트워크 기능이 로 설정된 볼륨을 만들 때 스토리지 용량이 `Standard` 부족합니다. <br> 오류 메시지: `No storage available with Standard network features, for the provided VNet.` | 이 오류는 지역에서 사용할 수 있는 리소스가 부족하여 네트워킹 기능이 있는 볼륨을 만들 수 없다는 `Standard` 것을 나타냅니다. <br> 다음 해결 방법 중 하나를 시도해 보세요. <ul><li>네트워크 기능이 필요하지 않은 경우 `Standard` 네트워크 기능을 사용하여 볼륨을 만듭니다. `Basic`</li> <li>새 VNet에서 볼륨을 만들어 보세요. 이렇게 하면 네트워킹 관련 리소스 제한에 도달하지 않습니다.</li><li>잠시 후 다시 시도합니다.  중간에 클러스터, 지역 또는 영역에서 리소스가 해제되었을 수 있습니다.</li></ul> |

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files에 대한 NFS 볼륨 만들기](azure-netapp-files-create-volumes.md)
* [Azure NetApp Files에 대한 SMB 볼륨 만들기](azure-netapp-files-create-volumes-smb.md) 
* [Azure NetApp Files에 대한 이중 프로토콜 볼륨 만들기](create-volumes-dual-protocol.md) 
* [볼륨에 대한 네트워크 기능 구성](configure-network-features.md)
