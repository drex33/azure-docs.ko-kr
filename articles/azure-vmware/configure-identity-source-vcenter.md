---
title: vCenter에 대한 외부 ID 원본 구성
description: vCenter에 대한 LDAP 또는 LDAPS를 통한 Active Directory를 외부 ID 원본으로 구성하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 08/31/2021
ms.openlocfilehash: 50a79c54c57649fd8dc565a7634823bf4a7a0f86
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123315925"
---
# <a name="configure-external-identity-source-for-vcenter"></a>vCenter에 대한 외부 ID 원본 구성



[!INCLUDE [vcenter-access-identity-description](includes/vcenter-access-identity-description.md)]

>[!NOTE]
>실행 명령은 제출된 순서대로 한 번에 하나씩 실행됩니다.

이 방법에서는 다음 방법을 배웁니다.

> [!div class="checklist"]
> * vCenter SSO와 통합된 모든 기존 외부 ID 원본 나열
> * SSL을 사용하거나 사용하지 않는 LDAP를 통한 Active Directory 추가 
> * 기존 AD 그룹을 cloudadmin 그룹에 추가
> * cloudadmin 역할에서 AD 그룹 제거
> * 기존 외부 ID 원본 제거



## <a name="prerequisites"></a>필수 조건

- 온-프레미스 네트워크에서 프라이빗 클라우드로의 연결을 설정합니다.

- SSL을 사용하는 AD가 있는 경우 AD 인증용 인증서를 다운로드하고 Azure Storage 계정에 Blob Storage로 업로드합니다. 그런 다음 [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 액세스 권한을 부여](../storage/common/storage-sas-overview.md)해야 합니다.  

- FQDN을 사용하는 경우 온-프레미스 AD에서 DNS 확인을 사용하도록 설정합니다.

 

## <a name="list-external-identity"></a>외부 ID 나열



`Get-ExternalIdentitySources` cmdlet을 실행하여 이미 vCenter SSO와 통합된 모든 외부 ID 원본을 나열합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **실행 명령** > **패키지** > **Get-ExternalIdentitySources** 를 선택합니다.

   :::image type="content" source="media/run-command/run-command-overview.png" alt-text="사용 가능한 실행 명령에 액세스하는 방법을 보여 주는 스크린샷" lightbox="media/run-command/run-command-overview.png":::

1. 필요한 값을 제공하거나 기본값을 변경한 다음 **실행** 을 선택합니다.

   :::image type="content" source="media/run-command/run-command-get-external-identity-sources.png" alt-text="외부 ID 원본을 나열하는 방법을 보여 주는 스크린샷":::
   
   | **필드** | **값** |
   | --- | --- |
   | **다음까지 유지**  |cmdlet 출력의 보존 기간입니다. 기본값은 60입니다.   |
   | **실행할 이름 지정**  | 영숫자 이름(예: **getExternalIdentity**)입니다.  |
   | **Timeout**  |  완료하는 데 너무 오래 걸릴 경우 cmdlet이 종료되는 기간입니다.  |

1. 진행 상황을 보려면 **알림** 을 확인합니다.


## <a name="add-active-directory-over-ldap"></a>LDAP를 통한 Active Directory 추가

`New-AvsLDAPIdentitySource` cmdlet을 실행하여 LDAP를 통한 AD를 vCenter에 SSO에서 사용할 외부 ID 원본으로 추가합니다. 

1. **실행 명령** > **패키지** > **New-AvsLDAPIdentitySource** 를 선택합니다.

1. 필요한 값을 제공하거나 기본값을 변경한 다음 **실행** 을 선택합니다.
   
   | **필드** | **값** |
   | --- | --- |
   | **이름**  | 사용자에게 친숙한 외부 ID 원본 이름(예: **avslap.local**)입니다.  |
   | **DomainName**  | 도메인의 FQDN입니다.    |
   | **DomainAlias**  | Active Directory ID 원본의 경우 도메인의 NetBIOS 이름입니다. SSPI 인증을 사용하는 경우 AD 도메인의 NetBIOS 이름을 ID 원본의 별칭으로 추가합니다.      |
   | **PrimaryUrl**  | 외부 ID 원본의 기본 URL(예: **ldap://yourserver:389**).  |
   | **SecondaryURL**  | 기본 오류가 있는 경우를 대비한 보조 대체 URL입니다.  |
   | **BaseDNUsers**  |  유효한 사용자를 찾을 위치(예: **CN=users,DC=yourserver,DC=internal**).  LDAP 인증을 사용하려면 기본 DN이 필요합니다.  |
   | **BaseDNGroups**  | 그룹을 찾을 위치(예: **CN=group1, DC=yourserver,DC= internal**). LDAP 인증을 사용하려면 기본 DN이 필요합니다.  |
   | **자격 증명**  | AD 원본(cloudadmin 아님)과의 인증에 사용되는 사용자 이름 및 암호입니다.  |
   | **GroupName**  | 클라우드 관리자에게 외부 ID 원본(예: **avs-admins**)에 대한 액세스 권한을 부여하는 그룹입니다.  |
   | **다음까지 유지**  | cmdlet 출력의 보존 기간입니다. 기본값은 60입니다.   |
   | **실행할 이름 지정**  | 영숫자 이름(예: **addexternalIdentity**)입니다.  |
   | **Timeout**  |  완료하는 데 너무 오래 걸릴 경우 cmdlet이 종료되는 기간입니다.  |

1. 진행 상황을 보려면 **알림** 을 확인합니다.



## <a name="add-active-directory-over-ldap-with-ssl"></a>SSL을 사용하는 LDAP를 통한 Active Directory 추가

`New-AvsLDAPSIdentitySource` cmdlet을 실행하여 SSL을 사용하는 LDAP를 통한 AD를 vCenter에 SSO에서 사용할 외부 ID 원본으로 추가합니다. 

1. AD 인증용 인증서를 다운로드하고 Azure Storage 계정에 Blob Storage로 업로드합니다.  

1. [SAS(공유 액세스 서명)을 사용하여 Azure Storage 리소스에 대한 액세스 권한을 부여합니다](../storage/common/storage-sas-overview.md).  
   
1. **실행 명령** > **패키지** > **New-AvsLDAPSIdentitySource** 를 선택합니다.

1. 필요한 값을 제공하거나 기본값을 변경한 다음 **실행** 을 선택합니다.

   | **필드** | **값** |
   | --- | --- |
   | **이름**  | 사용자에게 친숙한 외부 ID 원본 이름(예: **avslap.local**)입니다.  |
   | **DomainName**  | 도메인의 FQDN입니다.   |
   | **DomainAlias**  | Active Directory ID 원본의 경우 도메인의 NetBIOS 이름입니다. SSPI 인증을 사용하는 경우 AD 도메인의 NetBIOS 이름을 ID 원본의 별칭으로 추가합니다.     |
   | **PrimaryUrl**  | 외부 ID 원본의 기본 URL(예: **ldap://yourserver:389**).  |
   | **SecondaryURL**  | 기본 오류가 있는 경우를 대비한 보조 대체 URL입니다.  |
   | **BaseDNUsers**  |  유효한 사용자를 찾을 위치(예: **CN=users,DC=yourserver,DC=internal**).  LDAP 인증을 사용하려면 기본 DN이 필요합니다.  |
   | **BaseDNGroups**  | 그룹을 찾을 위치(예: **CN=group1, DC=yourserver,DC= internal**). LDAP 인증을 사용하려면 기본 DN이 필요합니다.  |
   | **자격 증명**  | AD 원본(cloudadmin 아님) 인증에 사용되는 사용자 이름과 암호입니다.  |
   | **CertificateSAS** | AD 원본에 대한 인증을 위한 인증서가 있는 SAS 문자열의 경로입니다.  |
   | **GroupName**  | 클라우드 관리자에게 외부 ID 원본(예: **avs-admins**)에 대한 액세스 권한을 부여하는 그룹입니다.  |
   | **다음까지 유지**  | cmdlet 출력의 보존 기간입니다. 기본값은 60입니다.   |
   | **실행할 이름 지정**  | 영숫자 이름(예: **addexternalIdentity**)입니다.  |
   | **Timeout**  |  완료하는 데 너무 오래 걸릴 경우 cmdlet이 종료되는 기간입니다.  |

1. 진행 상황을 보려면 **알림** 을 확인합니다.




## <a name="add-existing-ad-group-to-cloudadmin-group"></a>기존 AD 그룹을 cloudadmin 그룹에 추가

`Add-GroupToCloudAdmins` cmdlet을 실행하여 기존 AD 그룹을 cloudadmin 그룹에 추가합니다. 이 그룹의 사용자는 vCenter SSO에 정의된 cloudadmin(cloudadmin@vsphere.local) 역할과 동일한 권한을 갖습니다.

1. **실행 명령** > **패키지** > **Add-GroupToCloudAdmins** 를 선택합니다.

1. 필요한 값을 제공하거나 기본값을 변경한 다음 **실행** 을 선택합니다.

   | **필드** | **값** |
   | --- | --- |
   | **GroupName**  | 추가할 그룹의 이름(예: **VcAdminGroup**)입니다.  |
   | **다음까지 유지**  | cmdlet 출력의 보존 기간입니다. 기본값은 60입니다.   |
   | **실행할 이름 지정**  | 영숫자 이름(예: **addADgroup**)입니다.  |
   | **Timeout**  |  완료하는 데 너무 오래 걸릴 경우 cmdlet이 종료되는 기간입니다.  |

1. 진행 상황을 보려면 **알림** 을 확인합니다.




## <a name="remove-ad-group-from-the-cloudadmin-role"></a>cloudadmin 역할에서 AD 그룹 제거

`Remove-GroupFromCloudAdmins` cmdlet을 실행하여 cloudadmin 역할에서 지정된 AD 그룹을 제거합니다. 

1. **실행 명령** > **패키지** > **Remove-GroupFromCloudAdmins** 를 선택합니다.

1. 필요한 값을 제공하거나 기본값을 변경한 다음 **실행** 을 선택합니다.

   | **필드** | **값** |
   | --- | --- |
   | **GroupName**  | 제거할 그룹의 이름(예: **VcAdminGroup**)입니다.  |
   | **다음까지 유지**  | cmdlet 출력의 보존 기간입니다. 기본값은 60입니다.   |
   | **실행할 이름 지정**  | 영숫자 이름(예: **removeADgroup**)입니다.  |
   | **Timeout**  |  완료하는 데 너무 오래 걸릴 경우 cmdlet이 종료되는 기간입니다.  |

1. 진행 상황을 보려면 **알림** 을 확인합니다.






## <a name="remove-existing-external-identity-sources"></a>기존 외부 ID 원본 제거

`Remove-ExternalIdentitySources` cmdlet을 실행하여 기존의 모든 외부 ID 원본을 일괄적으로 제거합니다. 

1. **실행 명령** > **패키지** > **Remove-ExternalIdentitySources** 를 선택합니다.

1. 필요한 값을 제공하거나 기본값을 변경한 다음 **실행** 을 선택합니다.

   | **필드** | **값** |
   | --- | --- |
   | **다음까지 유지**  | cmdlet 출력의 보존 기간입니다. 기본값은 60입니다.   |
   | **실행할 이름 지정**  | 영숫자 이름(예: **remove_externalIdentity**)입니다.  |
   | **Timeout**  |  완료하는 데 너무 오래 걸릴 경우 cmdlet이 종료되는 기간입니다.  |

1. 진행 상황을 보려면 **알림** 을 확인합니다.


## <a name="next-steps"></a>다음 단계

이제 LDAP 및 LDAPS를 구성하는 방법을 배웠으므로 다음에 대해 자세히 알아볼 수 있습니다.

- [스토리지 정책 구성 방법](configure-storage-policy.md) - vSAN 데이터 저장소에 배포된 각 VM에는 하나 이상의 VM 스토리지 정책이 할당됩니다. VM의 초기 배포에서 복제 또는 마이그레이션과 같은 다른 VM 작업을 수행할 때 VM 스토리지 정책을 할당할 수 있습니다.

- [Azure VMware Solution ID 개념](concepts-identity.md) - vCenter를 사용하여 VM(가상 머신) 워크로드를 관리하고 NSX-T Manager를 사용하여 프라이빗 클라우드를 관리 및 확장합니다. 액세스 및 ID 관리는 vCenter에서 CloudAdmin 역할을, NSX-T Manager에서 제한된 관리자 권한을 사용합니다. 

 
