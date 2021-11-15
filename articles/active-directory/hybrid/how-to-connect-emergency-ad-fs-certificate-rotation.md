---
title: AD FS 인증서의 긴급 회전 | Microsoft Docs
description: 이 문서에서는 AD FS 인증서를 즉시 철회 및 업데이트하는 방법을 설명합니다.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 3b03dc507a76254f8568989af27f76aa75ad4e20
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130222441"
---
# <a name="emergency-rotation-of-the-ad-fs-certificates"></a>AD FS 인증서의 긴급 회전
AD FS 인증서를 즉시 회전해야 하는 경우 이 섹션의 아래에 설명된 단계를 수행할 수 있습니다.

> [!IMPORTANT]
> AD FS 환경에서 아래 단계를 수행하면 이전 인증서가 즉시 해지됩니다.  이 작업은 즉시 수행되기 때문에 페더레이션 파트너가 새 인증서를 사용하는 데 일반적으로 허용되는 시간은 고려하지 않습니다. 새 인증서를 사용하기 위한 트러스트 업데이트로 인해 서비스가 중단될 수 있습니다.  모든 페더레이션 파트너에 새 인증서가 있는 경우 이 문제를 해결해야 합니다.

> [!NOTE]
> Microsoft는 HSM(하드웨어 보안 모듈)을 사용하여 인증서를 보호하는 것이 좋습니다.
> 자세한 내용은 AD FS 보안 설정에 대한 모범 사례에서 [하드웨어 보안 모듈](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#hardware-security-module-hsm)을 참조하세요.

## <a name="determine-your-token-signing-certificate-thumbprint"></a>토큰 서명 인증서 지문 확인
현재 사용 중인 AD FS 이전 토큰 서명 인증서를 철회하려면 토큰 서명 인증서의 지문을 확인해야 합니다.  이렇게 하려면 다음 단계를 따릅니다.

 1.    Microsoft 온라인 서비스 `PS C:\>Connect-MsolService`에 연결합니다.
 2.    온-프레미스 및 클라우드 토큰 서명 인증서 지문 및 만료 날짜를 모두 문서화합니다.
`PS C:\>Get-MsolFederationProperty -DomainName <domain>` 
 3.  지문을 복사합니다.  나중에 기존 인증서를 제거하는 데 사용됩니다.

AD FS 관리를 사용하고, 서비스/인증서로 이동하고, 인증서를 마우스 오른쪽 단추로 클릭하고, 인증서 보기를 선택한 후 세부 정보를 선택하여 지문을 가져올 수도 있습니다. 

## <a name="determine-whether-ad-fs-renews-the-certificates-automatically"></a>AD FS에서 인증서를 자동으로 갱신할지 여부 결정
기본적으로 AD FS는 초기 구성 시 및 인증서의 만료 날짜가 도달하는 경우 토큰 서명 및 토큰 암호 해독 인증서를 자동으로 생성하도록 구성되어 있습니다.

`PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*`의 AD FS에서 Windows PowerShell 명령을 실행합니다.

AutoCertificateRollover 속성은 AD FS가 토큰 서명 및 토큰 암호 해독 인증서를 자동으로 갱신하도록 구성되었는지 여부를 설명합니다.  AutoCertificateRollover가 TRUE로 설정된 경우 아래 [AutoCertificateRollover가 TRUE로 설정된 경우 새 자체 서명된 인증서 생성](#generating-new-self-signed-certificate-if-autocertificaterollover-is-set-to-true)에서 설명된 지침을 따르세요.  AutoCertificateRollover가 FALSE로 설정된 경우 아래 [AutoCertificateRollover가 FALSE로 설정된 경우 새 인증서를 수동으로 생성](#generating-new-certificates-manually-if-autocertificaterollover-is-set-to-false)에서 설명된 지침을 따르세요.


## <a name="generating-new-self-signed-certificate-if-autocertificaterollover-is-set-to-true"></a>AutoCertificateRollover이 TRUE로 설정된 경우 자체 서명된 새 인증서를 생성합니다.
이 섹션에서는 **두 개의** 토큰 서명 인증서를 만듭니다.  첫 번째는 **-urgent** 플래그를 사용하여 현재 기본 인증서를 즉시 바꿉니다.  두 번째는 보조 인증서에 사용됩니다.  

>[!IMPORTANT]
>두 개의 인증서를 만드는 이유는 Azure가 이전 인증서와 관련된 정보를 계속 보유하기 때문입니다.  두 번째를 만들면 Azure는 이전 인증서에 대한 정보를 해제하고 두 번째 인증서에 대한 정보로 대체하게 됩니다.
>
>두 번째 인증서를 만들지 않고 Azure를 업데이트하는 경우 이전 토큰 서명 인증서를 사용하여 사용자를 인증할 수 있습니다.

다음 단계에 따라 새 토큰 서명 인증서를 생성할 수 있습니다.

 1. 기본 AD FS 서버에 로그온했는지 확인합니다.
 2. 관리자 권한으로 Windows PowerShell을 엽니다. 
 3. AutoCertificateRollover가 True로 설정되어 있는지 확인합니다.
`PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*`
 4. 새 토큰 서명 인증서 `Update-ADFSCertificate –CertificateType token-signing -Urgent`를 생성하려면:
 5. `Get-ADFSCertificate –CertificateType token-signing` 명령을 다시 실행하여 업데이트를 확인합니다.
 6. 이제 두 번째 토큰 서명 인증서 `Update-ADFSCertificate –CertificateType token-signing`를 생성합니다.
 7. `Get-ADFSCertificate –CertificateType token-signing` 명령을 다시 실행하여 업데이트를 확인할 수 있습니다.


## <a name="generating-new-certificates-manually-if-autocertificaterollover-is-set-to-false"></a>AutoCertificateRollover이 FALSE로 설정된 경우 새 인증서를 수동으로 생성
자동으로 생성되었으며 기본 자체 서명된 토큰 서명 및 토큰 암호 해독 인증서를 사용하지 않는 경우 이러한 인증서를 수동으로 갱신하고 구성해야 합니다.  여기에는 두 개의 새 토큰 서명 인증서를 만들고 가져오는 작업이 포함됩니다.  그런 다음, 하나를 주로 승격하고 이전 인증서를 철회한 후 두 번째 인증서를 보조 인증서로 구성합니다.

먼저, 인증 기관에서 두 개의 새 인증서를 얻어 각 페더레이션 서버의 로컬 컴퓨터 개인 인증서 스토리지로 가져와야 합니다. 관련 지침은 [인증서 가져오기](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754489(v=ws.11)) 문서를 참조하세요.

>[!IMPORTANT]
>두 개의 인증서를 만드는 이유는 Azure가 이전 인증서와 관련된 정보를 계속 보유하기 때문입니다.  두 번째를 만들면 Azure는 이전 인증서에 대한 정보를 해제하고 두 번째 인증서에 대한 정보로 대체하게 됩니다.
>
>두 번째 인증서를 만들지 않고 Azure를 업데이트하는 경우 이전 토큰 서명 인증서를 사용하여 사용자를 인증할 수 있습니다.

### <a name="to-configure-a-new-certificate-as-a-secondary-certificate"></a>새 인증서를 보조 인증서로 구성하려면
그런 다음 인증서 하나를 보조 AD FS 토큰 서명 또는 암호 해독 인증서로 구성하고 주 인증서로 승격합니다.

1. 인증서를 가져온 다음 **AD FS 관리** 콘솔을 엽니다.
2. **서비스** 를 확장하고 **인증서** 를 선택합니다.
3. 작업 창에서 **토큰 서명 인증서 추가** 를 클릭합니다.
4. 표시된 인증서 목록에서 새 인증서를 선택하고 확인을 클릭합니다.

### <a name="to-promote-the-new-certificate-from-secondary-to-primary"></a>새 인증서의 수준을 보조 인증서에서 기본 인증서로 올리려면
AD FS에서 새 인증서를 가져온 후 구성했으므로 이제 기본 인증서로 설정해야 합니다.
1. **AD FS 관리** 콘솔을 엽니다.
2. **서비스** 를 확장하고 **인증서** 를 선택합니다.
3. 보조 토큰 서명 인증서를 클릭합니다.
4. **작업** 창에서 **기본으로 설정** 을 클릭합니다. 확인 메시지가 표시되면 예를 클릭합니다.
5. 새 인증서를 기본 인증서로 승격한 후에는 계속 사용될 수 있는 이전 인증서를 제거해야 합니다. 아래의 [이전 인증서 제거](#remove-your-old-certificates) 섹션을 참조하세요.  

### <a name="to-configure-the-second-certificate-as-a-secondary-certificate"></a>새 인증서를 보조 인증서로 구성하려면
첫 번째 인증서를 추가하고 기본 인증서로 설정한 후 이전 인증서를 제거했으므로 이제 두 번째 인증서를 가져옵니다.  그런 다음 인증서를 보조 AD FS 토큰 서명 인증서로 구성해야 합니다.

1. 인증서를 가져온 다음 **AD FS 관리** 콘솔을 엽니다.
2. **서비스** 를 확장하고 **인증서** 를 선택합니다.
3. 작업 창에서 **토큰 서명 인증서 추가** 를 클릭합니다.
4. 표시된 인증서 목록에서 새 인증서를 선택하고 확인을 클릭합니다.

## <a name="update-azure-ad-with-the-new-token-signing-certificate"></a>새 토큰 서명 인증서를 사용하여 Azure AD 업데이트
Windows PowerShell용 Microsoft Azure Active Directory 모듈을 엽니다. 또는 Windows PowerShell을 열고 `Import-Module msonline` 명령을 실행합니다.

명령 `Connect-MsolService`를 실행하여 Azure AD에 연결한 다음, 전역 관리자 자격 증명을 입력합니다.

>[!Note]
> 기본 페더레이션 서버가 아닌 컴퓨터에서 이러한 명령을 실행하는 경우 먼저 `Set-MsolADFSContext –Computer <servername>` 명령을 입력합니다. \<servername\>을 AD FS 서버의 이름으로 바꿉니다. 그런 다음 메시지가 표시되면 서버의 관리자 자격 증명을 입력합니다.

원하는 경우 Azure AD에서 현재 인증서 정보를 확인하여 업데이트가 필요한지를 확인합니다. 이렇게 하려면 `Get-MsolFederationProperty` 명령을 실행합니다. 메시지가 표시되면 페더레이션 도메인의 이름을 입력합니다.

Azure AD에서 인증서 정보를 업데이트하려면 명령 `Update-MsolFederatedDomain`을 실행하고 메시지가 표시되면 도메인 이름을 입력합니다.

>[!Note]
> 이 명령을 실행할 때 오류가 표시되면 명령 `Update-MsolFederatedDomain –SupportMultipleDomain`을 실행하고 메시지가 표시되면 도메인 이름을 입력합니다.

## <a name="replace-ssl-certificates"></a>SSL 인증서 유효성을 바꿉니다.
손상으로 인해 토큰 서명 인증서를 교체해야 하는 경우 AD FS 및 WAP 서버에 대한 SSL 인증서를 철회하고 바꾸어야 합니다.  

SSL 인증서 해지는 인증서를 발급한 CA(인증 기관)에서 수행해야 합니다.  이러한 인증서는 GoDaddy와 같은 타사 공급자가 발급하는 경우가 많습니다.  예를 보려면 (인증서 철회 | SSL 인증서 - GoDaddy 도움말 US)를 참조하세요.  자세한 내용은 [인증서를 해지하는 방법](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee619754(v=ws.10))을 참조하세요.

이전 SSL 인증서가 해지되고 새 인증서가 발급되면 SSL 인증서를 바꿀 수 있습니다. 자세한 내용은 [AD FS에 대한 SSL 인증서 바꾸기](/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs)를 참조하세요.

## <a name="remove-your-old-certificates"></a>이전 인증서 제거
이전 인증서는 바꾼 후에도 계속 사용될 수 있기 때문에 이전 인증서를 제거해야 합니다. 이렇게 하려면 아래 단계를 수행합니다.

1. 기본 AD FS 서버에 로그온했는지 확인합니다.
2. 관리자 권한으로 Windows PowerShell을 엽니다. 
4. 이전 토큰 서명 인증서 `Remove-ADFSCertificate –CertificateType token-signing -thumbprint <thumbprint>`를 제거하려면:

## <a name="updating-federation-partners-who-can-consume-federation-metadata"></a>페더레이션 메타데이터를 사용할 수 있는 페더레이션 파트너 업데이트
새 토큰 서명 또는 토큰 암호 해독 인증서를 갱신하고 구성한 경우에는 모든 페더레이션 파트너(신뢰 당사자 트러스트와 클레임 공급자 트러스트를 통해 AD FS에 표시되는 리소스 조직 또는 계정 조직 파트너)가 새 인증서를 선택했는지 확인해야 합니다.

## <a name="updating-federation-partners-who-can-not-consume-federation-metadata"></a>페더레이션 메타데이터를 사용할 수 없는 페더레이션 파트너 업데이트
페더레이션 파트너가 페더레이션 메타데이터를 사용할 수 없는 경우 새 토큰 서명/토큰 암호 해독 인증서의 공개 키를 수동으로 전송해야 합니다. 모든 리소스 조직 또는 계정 조직 파트너(신뢰 당사자 트러스트와 클레임 공급자 트러스트를 통해 AD FS에 표시)에 새 인증서 공개 키(전체 체인을 포함하려는 경우에는 .p7b 또는 .cer 파일)를 보냅니다. 파트너가 변경 내용을 구현하고 새 인증서를 신뢰하도록 합니다.



## <a name="revoke-refresh-tokens-via-powershell"></a>PowerShell을 통해 새로 고침 토큰 철회
이제 사용자에 대한 새로 고침 토큰을 철회하고 다시 로그온하여 새 토큰을 가져오는 데 사용할 수 있습니다.  그러면 토큰 및 새로 고침 토큰을 사용하는 다른 항목과 함께 사용자의 휴대폰, 현재 웹 메일 세션에서 로그아웃합니다.  [여기](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?preserve-view=true&view=azureadps-2.0)에서 정보를 찾을 수 있으며 [Azure Active Directory에서 사용자 액세스를 철회](../../active-directory/enterprise-users/users-revoke-access.md)하는 방법을 참조할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- [Windows Server 2016의 AD FS 및 WAP에서 SSL 인증서 관리](/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs)
- [AD FS에 대한 토큰 암호 해독 인증서 및 토큰 서명 가져오기 및 구성 ](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn781426(v=ws.11)#updating-federation-partners)
- [Microsoft 365 및 Azure Active Directory에 대한 페더레이션 인증서 갱신](how-to-connect-fed-o365-certs.md)
