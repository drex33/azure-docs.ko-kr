---
title: Azure NetApp Files에 대해 ADDS LDAP over TLS 구성 | Microsoft Docs
description: 루트 CA 인증서 관리를 포함하여 Azure NetApp Files에 대해 ADDS LDAP over TLS를 구성하는 방법을 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-hchen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/05/2021
ms.author: b-hchen
ms.openlocfilehash: 0241bae39feb350adaa1578d8dff5db0b752873c
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133484185"
---
# <a name="configure-adds-ldap-over-tls-for-azure-netapp-files"></a>Azure NetApp Files에 대해 ADDS LDAP over TLS 구성

LDAP over TLS를 사용하여 Azure NetApp Files 볼륨과 Active Directory LDAP 서버 간의 통신을 보호할 수 있습니다.  Azure NetApp Files의 NFS, SMB 및 이중 프로토콜 볼륨에 LDAP over TLS를 사용할 수 있습니다.  

## <a name="considerations"></a>고려 사항

* AADDS(Azure Active Directory Domain Services)를 사용하는 경우 LDAP over TLS를 사용하면 안 됩니다. AADDS는 LDAP over TLS(포트 389) 대신 LDAPS(포트 636)를 사용하여 LDAP 트래픽을 보호합니다.  

## <a name="register-the-ldap-over-tls-feature"></a>LDAP over TLS 기능 등록 

LDAP over TLS 기능은 현재 미리 보기로 제공됩니다. 이 기능을 처음 사용하는 경우 사용하기 전에 기능을 등록해야 합니다.

1.  기능을 등록합니다.

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapOverTls
    ```

2. 기능 등록 상태를 확인합니다. 

    > [!NOTE]
    > **RegistrationState** 는 `Registered`로 변경되기 전까지 최대 60분 동안 `Registering` 상태일 수 있습니다. 상태가 `Registered`가 될 때까지 기다린 후 계속합니다.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapOverTls
    ```
[Azure CLI 명령](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` 및 `az feature show`를 사용하여 기능을 등록하고 등록 상태를 표시할 수도 있습니다. 

## <a name="generate-and-export-root-ca-certificate"></a>루트 CA 인증서 생성 및 내보내기 

루트 CA 인증서가 없는 경우 하나 생성하고 LDAP over TLS 인증에 사용할 수 있게 내보내야 합니다. 

1. ADDS 인증 기관을 설치하고 구성하려면 [인증 기관 설치](/windows-server/networking/core-network-guide/cncg/server-certs/install-the-certification-authority)를 따릅니다. 

2. MMC 스냅인과 인증서 관리자 도구를 사용하려면 [MMC 스냅인으로 인증서 보기](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in)를 따릅니다.  
    인증서 관리자 스냅인을 사용하여 로컬 디바이스의 루트 또는 발급 인증서를 찾습니다. 다음 설정 중 하나에서 인증서 관리 스냅인 명령을 실행해야 합니다.  
    * 도메인에 가입하고 루트 인증서가 설치된 Windows 기반 클라이언트 
    * 루트 인증서를 포함하는 도메인의 다른 컴퓨터  

3. 루트 CA 인증서를 내보냅니다.  
    루트 CA 인증서는 다음 예와 같이 개인 또는 신뢰할 수 있는 루트 인증 기관 디렉터리에서 내보낼 수 있습니다.   
    ![개인 인증서를 보여주는 스크린샷](../media/azure-netapp-files/personal-certificates.png)   
    ![신뢰할 수 있는 루트 인증 기관을 보여주는 스크린샷](../media/azure-netapp-files/trusted-root-certification-authorities.png)    

    인증서가 Base-64로 인코딩된 X.509(.CER) 형식으로 내보내졌는지 확인합니다. 

    ![인증서 내보내기 마법사](../media/azure-netapp-files/certificate-export-wizard.png)

## <a name="enable-ldap-over-tls-and-upload-root-ca-certificate"></a>LDAP over TLS 사용 및 루트 CA 인증서 업로드 

1. 볼륨에 사용되는 NetApp 계정으로 이동하고 **Active Directory 연결** 을 클릭합니다. 그런 다음, **조인** 을 클릭하여 새 AD 연결을 만들거나 **편집** 을 클릭하여 기존 AD 연결을 편집합니다.  

2. **Active Directory 조인** 또는 **Edit Active Directory(Active Directory 편집)** 창이 나타나면 **LDAP over TLS(TLS를 통한 LDAP)** 확인란을 선택하여 볼륨에 LDAP over TLS를 사용하도록 설정합니다. 그런 다음, **Server root CA Certificate(서버 루트 CA 인증서)** 를 클릭하고 LDAP over TLS에 사용할 [생성된 루트 CA 인증서](#generate-and-export-root-ca-certificate)를 업로드합니다.  

    ![LDAP over TLS 옵션을 보여주는 스크린샷](../media/azure-netapp-files/ldap-over-tls-option.png)

    DNS에서 인증 기관 이름을 확인할 수 있는지 확인합니다. 이 이름은 인증서의 "발급자" 필드입니다.  

    ![인증서 정보를 보여주는 스크린샷](../media/azure-netapp-files/certificate-information.png)

잘못된 인증서를 업로드했고 기존 AD 구성, SMB 볼륨 또는 Kerberos 볼륨이 있는 경우 다음과 유사한 오류가 발생합니다.

`Error updating Active Directory settings The LDAP client configuration "ldapUserMappingConfig" for Vservers is an invalid configuration.`

오류 조건을 확인하려면 LDAP 인증을 위해 Windows Active Directory LDAP 서버에서 요구하는 대로 유효한 루트 CA 인증서를 NetApp 계정에 업로드합니다.

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files에 대한 NFS 볼륨 만들기](azure-netapp-files-create-volumes.md)
* [Azure NetApp Files에 대한 SMB 볼륨 만들기](azure-netapp-files-create-volumes-smb.md) 
* [Azure NetApp Files에 대한 이중 프로토콜 볼륨 만들기](create-volumes-dual-protocol.md)

