---
title: Azure AD Domain Services를 사용하여 SMB를 통해 파일 데이터에 대한 액세스 권한 부여
description: Azure Active Directory Domain Services를 통해 Azure Files에 대해 SMB(서버 메시지 블록)를 통한 ID 기반 인증을 사용하도록 설정하는 방법을 알아봅니다. 도메인 조인 Windows VM(가상 머신)은 Azure AD 자격 증명을 사용하여 Azure 파일 공유에 액세스할 수 있습니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/22/2021
ms.author: rogarana
ms.subservice: files
ms.custom: contperf-fy21q1, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: f995b4b17330f6469f05c5399c2129fa7abc33bf
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114462577"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Azure Files에서 Azure Active Directory Domain Services 인증 사용

[Azure Files](storage-files-introduction.md) 는 두 가지 도메인 서비스인 온-프레미스 AD DS(Active Directory Domain Services) 및 Azure AD DS(Azure Active Directory Domain Services)를 사용하여 SMB(서버 메시지 블록)를 통한 ID 기반 인증을 지원합니다. 인증에 적합한 도메인 서비스를 선택하려면 [작동 방식 섹션](./storage-files-active-directory-overview.md#how-it-works)을 검토하는 것이 좋습니다. 설정은 선택한 도메인 서비스에 따라 달라집니다. 이 문서에서는 Azure 파일 공유에서 인증을 위해 Azure AD DS를 사용하도록 설정하고 구성하는 방법을 중점적으로 설명합니다.

Azure 파일 공유를 처음 사용하는 경우 다음 일련의 문서를 읽기 전에 [계획 가이드](storage-files-planning.md)를 읽어 보는 것이 좋습니다.

> [!NOTE]
> Azure Files는 RC4-HMAC 및 AES-256 암호화를 사용하여 Azure AD DS를 통한 Kerberos 인증을 지원합니다.
>
> Azure Files는 Azure AD와 전체 동기화를 사용하여 Azure AD DS 인증을 지원합니다. Azure AD에서 제한된 ID 세트만 동기화하는 Azure AD DS의 범위가 지정된 동기화를 사용하도록 설정한 경우에는 인증 및 권한 부여가 지원되지 않습니다.

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |

## <a name="prerequisites"></a>사전 요구 사항

Azure 파일 공유에 대해 SMB를 통한 Azure AD를 사용하도록 설정하기 전에 다음 필수 조건을 완료했는지 확인합니다.

1.  **Azure AD 테넌트를 선택하거나 만듭니다.**

    SMB를 통한 Azure AD 인증에는 새 테넌트나 기존 테넌트를 사용할 수 있습니다. 액세스하려는 파일 공유와 테넌트는 같은 구독과 연결되어 있어야 합니다.

    새 Azure AD 테넌트를 만들려는 경우 [Azure AD 테넌트 및 Azure AD 구독을 추가](/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription)하면 됩니다. 기존 Azure AD 테넌트가 있지만 Azure 파일 공유에서 사용할 새 테넌트를 만들려는 경우에는 [Azure Active Directory 테넌트 만들기](/rest/api/datacatalog/create-an-azure-active-directory-tenant)를 참조하세요.

1.  **Azure AD 테넌트에서 Azure AD Domain Services를 사용하도록 설정합니다.**

    Azure AD 자격 증명을 사용한 인증을 지원하려면 Azure AD 테넌트에 대해 Azure AD Domain Services를 사용하도록 설정해야 합니다. Azure AD 테넌트의 관리자가 아니라면 관리자에게 문의하고 [Azure Portal을 사용하여 Azure Active Directory Domain Services 활성화](../../active-directory-domain-services/tutorial-create-instance.md)를 위한 단계별 지침을 따르세요.

    일반적으로 Azure AD DS 배포를 완료하는 데 15분 정도 걸립니다. 다음 단계로 진행하기 전에 Azure AD DS의 성능 상태가 **실행 중** 으로 표시되고 암호 해시 동기화가 사용하도록 설정되어 있는지 확인합니다.

1.  **Azure AD DS를 사용하여 Azure VM을 도메인 조인합니다.**

    VM에서 Azure AD 자격 증명을 사용하여 파일 공유에 액세스하려면 VM이 Azure AD DS에 도메인 조인되어 있어야 합니다. VM을 도메인 조인하는 방법에 대한 자세한 내용은 [Windows Server 가상 머신을 관리되는 도메인에 조인](../../active-directory-domain-services/join-windows-vm.md)을 참조하세요.

    > [!NOTE]
    > Azure 파일 공유에 대한 SMB를 통한 Azure AD 인증은 Windows 7 또는 Windows Server 2008 R2 이상의 OS 버전에서 실행되는 Azure VMs에서만 지원됩니다.

1.  **Azure 파일 공유를 선택하거나 만듭니다.**

    Azure AD 테넌트와 같은 구독에 연결된 신규 또는 기존 파일 공유를 선택합니다. 새 파일 공유를 만드는 방법에 대한 자세한 내용은 [Azure Files에 파일 공유 만들기](storage-how-to-create-file-share.md)를 참조하세요.
    성능을 최적화하려면 해당 공유에 액세스하려는 VM과 같은 지역에 파일 공유를 배치하는 것이 좋습니다.

1.  **스토리지 계정 키를 사용하여 Azure 파일 공유를 탑재해 Azure Files 연결을 확인합니다.**

    VM 및 파일 공유가 적절하게 구성되었는지 확인하려면 스토리지 계정 키를 사용하여 파일 공유를 탑재해 봅니다. 자세한 내용은 [Azure 파일 공유를 탑재하고 Windows에서 공유에 액세스](storage-how-to-use-files-windows.md)를 참조하세요.

## <a name="regional-availability"></a>지역별 가용성

Azure AD DS를 사용한 Azure Files 인증은 [모든 Azure 공용, Gov 및 중국 지역](https://azure.microsoft.com/global-infrastructure/locations/)에서 사용할 수 있습니다.

## <a name="overview-of-the-workflow"></a>워크플로의 개요

Azure 파일 공유에 대해 SMB를 통한 Azure AD DS 인증을 사용하도록 설정하기 전에 Azure AD 및 Azure Storage 환경이 제대로 구성되어 있는지 확인합니다. [필수 조건](#prerequisites)을 검토하여 필요한 모든 단계를 완료했는지 확인하는 것이 좋습니다.

다음으로, 다음 작업을 수행하여 Azure AD 자격 증명을 사용하여 Azure Files 리소스에 대한 액세스 권한을 부여합니다.

1. 스토리지 계정에 대해 SMB를 통한 Azure AD DS 인증을 사용하도록 설정하여 연결된 Azure AD DS 배포에 스토리지 계정을 등록합니다.
2. Azure AD ID(사용자, 그룹 또는 서비스 주체)에 공유 액세스 권한을 할당합니다.
3. 디렉터리와 파일에 대해 SMB를 통한 NTFS 권한을 구성합니다.
4. 도메인 조인 VM에서 Azure 파일 공유를 탑재합니다.

다음 다이어그램에서는 Azure Files에 대해 SMB를 통한 Azure AD DS 인증을 사용하도록 설정하는 엔드투엔드 워크플로를 보여 줍니다.

![Azure Files용 SMB를 통한 Azure AD 워크플로를 보여 주는 다이어그램](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>계정에 대해 Azure AD DS 인증 사용

Azure Files에 대해 SMB를 통한 Azure AD DS 인증을 사용하려면 Azure Portal, Azure PowerShell 또는 Azure CLI를 사용하여 스토리지 계정에서 속성을 설정하면 됩니다. 이 속성을 설정하면 연결된 Azure AD DS 배포에 스토리지 계정이 암시적으로 “도메인 조인”됩니다. 그러면 스토리지 계정의 모든 신규 및 기존 파일 공유에 대해 SMB를 통한 Azure AD DS 인증이 사용하도록 설정됩니다.

Azure AD DS를 Azure AD 테넌트에 정상적으로 배포해야 SMB를 통한 Azure AD DS 인증을 사용하도록 설정할 수 있습니다. 자세한 내용은 [필수 조건](#prerequisites)을 참조하세요.

# <a name="portal"></a>[포털](#tab/azure-portal)

[Azure Portal](https://portal.azure.com)을 사용하여 SMB를 통한 Azure AD DS 인증을 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal에서 기존 스토리지 계정으로 이동하거나 [스토리지 계정을 만듭니다](../common/storage-account-create.md).
1. **파일 공유** 섹션에서 **Active Directory: 구성되지 않음** 을 선택합니다.

    :::image type="content" source="media/storage-files-active-directory-enable/files-azure-ad-enable-storage-account-identity.png" alt-text="Active Directory가 강조 표시된 스토리지 계정의 파일 공유 창 스크린샷" lightbox="media/storage-files-active-directory-enable/files-azure-ad-enable-storage-account-identity.png":::

1. **Azure Active Directory Domain Services** 를 선택한 후 토글을 **사용** 으로 전환합니다.
1. **저장** 을 선택합니다.

    :::image type="content" source="media/storage-files-active-directory-enable/files-azure-ad-highlight.png" alt-text="Azure Active Directory Domain Services가 사용하도록 설정된 Active Directory 창 스크린샷" lightbox="media/storage-files-active-directory-enable/files-azure-ad-highlight.png":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell에서 SMB를 통한 Azure AD DS 인증을 사용하려면 최신 Az 모듈(2.4 이상) 또는 Az.Storage 모듈(1.5 이상)을 설치합니다. PowerShell을 설치하는 방법에 관한 자세한 내용은 [PowerShellGet을 사용하여 Windows에 Azure PowerShell 설치](/powershell/azure/install-Az-ps)를 참조하세요.

새 스토리지 계정을 만들려면 [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount)를 호출한 다음, **EnableAzureActiveDirectoryDomainServicesForFile** 매개 변수를 **true** 로 설정합니다. 다음 예제에서는 자리 표시자 값을 고유한 값으로 바꿔야 합니다. (이전 미리 보기 모듈을 사용하는 경우 기능을 사용하도록 설정하는 매개 변수는 **EnableAzureFilesAadIntegrationForSMB** 입니다.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

기존 스토리지 계정에서 이 기능을 사용하도록 설정하려면 다음 명령을 사용합니다.

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 SMB를 통한 Azure AD 인증을 사용하도록 설정하려면 최신 CLI 버전(버전 2.0.70 이상)을 설치합니다. Azure CLI를 설치하는 방법에 관한 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

새 스토리지 계정을 만들려면 [az storage account create](/cli/azure/storage/account#az_storage_account_create)를 호출하고 `--enable-files-aadds` 속성을 **true** 로 설정합니다. 다음 예제에서는 자리 표시자 값을 고유한 값으로 바꿔야 합니다. (이전 미리 보기 모듈을 사용하는 경우 기능을 사용하도록 설정하는 매개 변수는 **file-aad** 입니다.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

기존 스토리지 계정에서 이 기능을 사용하도록 설정하려면 다음 명령을 사용합니다.

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```
---

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

이제 SMB를 통한 Azure AD DS 인증을 사용하도록 설정하고 Azure AD ID를 사용하여 Azure 파일 공유에 대한 액세스를 제공하는 사용자 지정 역할을 할당했습니다. 파일 공유에 대한 액세스 권한을 추가 사용자에게 부여하려면 [액세스 권한 할당](#assign-access-permissions-to-an-identity)의 지침에 따라 ID를 사용하고 [SMB를 통한 NTFS 권한 섹션을 구성](#configure-ntfs-permissions-over-smb)합니다.

## <a name="next-steps"></a>다음 단계

Azure Files 및 SMB를 통한 Azure AD를 사용하는 방법에 관한 자세한 내용은 다음 리소스를 참조하세요.

- [SMB 액세스에 대한 Azure Files ID 기반 인증 지원 개요](storage-files-active-directory-overview.md)
- [FAQ](storage-files-faq.md)
