---
title: AzCopy & Azure Active Directory로 Blob 액세스 권한 부여 | Microsoft Docs
description: Azure AD(Active Directory)를 사용하여 AzCopy 작업에 대한 권한 부여 자격 증명을 제공할 수 있습니다.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/01/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: d5aced875863f892ec9dda0022f4571ce15349c4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128638238"
---
# <a name="authorize-access-to-blobs-with-azcopy-and-azure-active-directory-azure-ad"></a>AzCopy & Azure AD(Active Directory)로 Blob 액세스 권한 부여

Azure AD를 사용하여 AzCopy에 권한 부여 자격 증명을 제공할 수 있습니다. 이렇게 하면 명령마다 SAS(공유 액세스 서명)를 추가하지 않아도 됩니다.

먼저 역할 할당을 확인합니다. 그런 다음, 권한을 부여할 *보안 주체* 의 유형을 선택합니다. [사용자 ID](../../active-directory/fundamentals/add-users-azure-active-directory.md), [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md) 및 [서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md)는 각 보안 주체의 유형입니다.

사용자 ID는 Azure AD에 ID가 있는 사용자입니다. 권한 부여를 위한 가장 쉬운 보안 주체입니다. 관리 ID 및 서비스 주체는 사용자 개입 없이 실행되는 스크립트 내에서 AzCopy를 사용하려는 경우에 유용한 옵션입니다. 관리 ID는 Azure VM(Virtual Machine)에서 실행되는 스크립트에, 서비스 주체는 온-프레미스에서 실행되는 스크립트에 더 적합합니다.

AzCopy에 대한 자세한 내용은 [AzCopy 시작](storage-use-azcopy-v10.md)을 참조하세요.

## <a name="verify-role-assignments"></a>역할 할당 확인

필요한 권한 부여 수준은 파일을 업로드할지 또는 다운로드만 할지 여부에 따라 정해집니다.

파일을 다운로드만 할 경우 [Storage Blob 데이터 독자](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) 역할이 사용자 ID, 관리 ID 또는 서비스 주체에게 할당됩니다.

파일을 업로드하려면 다음 역할 중 하나가 보안 주체에 할당되었는지 확인합니다.

- [Storage Blob 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- [Storage Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

이 모든 범위에서 보안 주체에 이러한 역할을 할당할 수 있습니다.

- 컨테이너(파일 시스템)
- 스토리지 계정
- Resource group
- Subscription

역할을 확인하고 할당하는 방법을 알아보려면 [Blob 데이터에 액세스하기 위한 Azure 역할 할당](../blobs/assign-azure-role-data-access.md)을 참조하세요.

> [!NOTE]
> Azure 역할 할당을 전파하는 데 최대 5분이 걸릴 수 있습니다.

보안 주체를 대상 컨테이너나 디렉터리의 ACL(액세스 제어 목록)에 추가하는 경우, 보안 주체에 이런 역할을 할당하지 않아도 됩니다. ACL에서 보안 주체에는 대상 디렉터리에 대한 쓰기 권한과, 컨테이너 및 각 부모 디렉터리에 대한 실행 권한이 필요합니다.

자세한 정보는 [Azure Data Lake Storage Gen2의 액세스 제어 모델](../blobs/data-lake-storage-access-control-model.md)을 참조하세요.

## <a name="authorize-a-user-identity"></a>사용자 ID 권한 부여

사용자 ID에 필요한 권한 부여 수준이 제공되었는지 확인한 후에는 명령 프롬프트를 열어 다음 명령을 입력하고 ENTER 키를 누릅니다.

```azcopy
azcopy login
```

오류가 나타날 경우 스토리지 계정이 속한 조직의 테넌트 ID를 포함해 봅니다.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

`<tenant-id>` 자리 표시자를 스토리지 계정이 속한 조직의 테넌트 ID로 바꿉니다. 테넌트 ID를 찾으려면 Azure Portal에서 **Azure Active Directory > 속성 > 디렉터리 ID** 를 선택합니다.

이 명령은 웹 사이트의 인증 코드와 URL을 반환합니다. 웹 사이트를 열고, 코드를 입력하고, **다음** 단추를 선택합니다.

![컨테이너 만들기](media/storage-use-azcopy-v10/azcopy-login.png)

로그인 창이 나타납니다. 이 창에서 Azure 계정 자격 증명을 사용하여 Azure 계정에 로그인합니다. 로그인에 성공하면 브라우저 창을 닫고 AzCopy를 사용할 수 있습니다.

<a id="managed-identity"></a>

## <a name="authorize-a-managed-identity"></a>관리 ID 권한 부여

사용자 개입 없이 실행되는 스크립트 안에서 AzCopy를 사용하고 스크립트가 Azure VM(Virtual Machine)에서 실행될 경우 이 옵션을 사용하는 것이 좋습니다. 이 옵션을 사용할 때는 VM에 자격 증명을 저장할 필요가 없습니다.

VM에서 사용하도록 설정한 시스템 차원의 관리 ID를 사용하거나, VM에 할당한 사용자가 할당한 관리 ID의 클라이언트 ID, 개체 ID 또는 리소스 ID를 사용하여 계정에 로그인할 수 있습니다.

시스템 차원의 관리 ID를 사용하도록 설정하거나, 사용자가 할당한 관리 ID를 만드는 방법은 [Azure Portal을 사용하여 VM에서 Azure 리소스에 대한 관리 ID 구성](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)을 참조하세요.

#### <a name="authorize-by-using-a-system-wide-managed-identity"></a>시스템 차원의 관리 ID를 사용하여 권한 부여

먼저 VM에서 시스템 차원의 관리 ID를 사용하도록 설정했는지 확인합니다. [시스템이 할당한 관리 ID 사용](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)을 참조하세요.

그런 다음, 명령 콘솔에서 다음 명령을 입력하고 ENTER 키를 누릅니다.

```azcopy
azcopy login --identity
```

#### <a name="authorize-by-using-a-user-assigned-managed-identity"></a>사용자가 할당한 관리 ID를 사용하여 권한 부여

먼저 VM에서 사용자가 할당한 관리 ID를 사용하도록 설정했는지 확인합니다. [사용자가 할당한 관리 ID](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity)를 참조하세요.

그런 다음, 명령 콘솔에서 다음 명령 중 하나를 입력하고 ENTER 키를 누릅니다.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

`<client-id>` 자리 표시자를 사용자가 할당한 관리 ID의 클라이언트 ID로 바꿉니다.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

`<object-id>` 자리 표시자를 사용자가 할당한 관리 ID의 개체 ID로 바꿉니다.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

`<resource-id>` 자리 표시자를 사용자가 할당한 관리 ID의 리소스 ID로 바꿉니다.

<a id="service-principal"></a>

## <a name="authorize-a-service-principal"></a>서비스 주체 권한 부여

이 옵션은 특히 온-프레미스에서 실행되는 경우 사용자 개입 없이 실행되는 스크립트 안에서 AzCopy를 사용하고자 할 때 유용합니다. Azure에서 실행되는 VM에서 AzCopy를 실행하려는 경우 관리 서비스 ID가 더 관리하기 쉽습니다. 자세한 정보는 이 문서의 [관리 ID 권한 부여](#authorize-a-managed-identity) 섹션을 참조하세요.

스크립트를 실행하기 전에 AzCopy에 서비스 주체의 자격 증명을 제공할 수 있게 대화형으로 한 번 이상 로그인해야 합니다.  이러한 자격 증명은 스크립트가 중요 정보를 제공할 필요가 없게 보호 및 암호화되는 파일에 저장됩니다.

클라이언트 암호나 서비스 주체의 앱 등록과 연결된 인증서의 암호를 사용하여 계정에 로그인할 수 있습니다.

서비스 주체 만들기에 대한 자세한 정보는 [방법: 포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md)를 참조하세요.

서비스 주체에 대한 일반적인 내용은 [Azure Active Directory의 애플리케이션 및 서비스 주체 개체](../../active-directory/develop/app-objects-and-service-principals.md)를 참조하세요.

#### <a name="authorize-a-service-principal-by-using-a-client-secret"></a>클라이언트 암호를 사용하여 서비스 주체 권한 부여

먼저 `AZCOPY_SPA_CLIENT_SECRET` 환경 변수를 서비스 주체의 앱 등록에 대한 클라이언트 암호로 설정합니다.

> [!NOTE]
> 운영 체제의 환경 변수 설정이 아니라 명령 프롬프트에서 이 값을 설정해야 합니다. 이렇게 하면 현재 세션에만 값을 사용할 수 있습니다.

이 예제에서는 PowerShell에서 이 작업을 수행하는 방법을 보여줍니다.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> 이 예제에 나와 있는 것처럼 프롬프트를 사용하는 것이 좋습니다. 이렇게 하면 암호가 콘솔의 명령 기록에 표시되지 않습니다.

이제 다음 명령을 입력하고 ENTER 키를 누릅니다.

```azcopy
azcopy login --service-principal  --application-id application-id --tenant-id=tenant-id
```

`<application-id>` 자리 표시자를 서비스 주체 앱 등록의 애플리케이션 ID로 바꿉니다. `<tenant-id>` 자리 표시자를 스토리지 계정이 속한 조직의 테넌트 ID로 바꿉니다. 테넌트 ID를 찾으려면 Azure Portal에서 **Azure Active Directory > 속성 > 디렉터리 ID** 를 선택합니다.

#### <a name="authorize-a-service-principal-by-using-a-certificate"></a>인증서를 사용하여 서비스 주체 권한 부여

권한 부여에 자체 자격 증명을 사용하려는 경우 인증서를 앱 등록에 업로드한 다음, 해당 인증서를 사용하여 로그인할 수 있습니다.

앱 등록에 인증서를 업로드하는 것과 함께 AzCopy가 실행될 컴퓨터나 VM에 인증서 사본을 저장해야 합니다. 이 인증서 사본은 .PFX 또는 .PEM 형식이면서, 프라이빗 키를 포함해야 합니다. 프라이빗 키는 암호로 보호되어야 합니다. Windows를 사용하며 인증서가 인증서 저장소에만 있는 경우 이 인증서를 PFX 파일(프라이빗 키 포함)로 내보내야 합니다. 지침은 [Export-PfxCertificate](/powershell/module/pki/export-pfxcertificate)를 참조하세요.

다음으로 `AZCOPY_SPA_CERT_PASSWORD` 환경 변수를 인증서 암호로 설정합니다.

> [!NOTE]
> 운영 체제의 환경 변수 설정이 아니라 명령 프롬프트에서 이 값을 설정해야 합니다. 이렇게 하면 현재 세션에만 값을 사용할 수 있습니다.

이 예제에서는 PowerShell에서 이 작업을 수행하는 방법을 보여줍니다.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

이제 다음 명령을 입력하고 ENTER 키를 누릅니다.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

`<path-to-certificate-file>` 자리 표시자를 인증서 파일의 상대 경로 또는 정규화된 경로로 바꿉니다. AzCopy는 이 인증서 경로를 저장하지만 인증서 사본을 저장하지는 않으므로 인증서를 그대로 유지해야 합니다. `<tenant-id>` 자리 표시자를 스토리지 계정이 속한 조직의 테넌트 ID로 바꿉니다. 테넌트 ID를 찾으려면 Azure Portal에서 **Azure Active Directory > 속성 > 디렉터리 ID** 를 선택합니다.

> [!NOTE]
> 이 예제에 나와 있는 것처럼 프롬프트를 사용하는 것이 좋습니다. 이렇게 하면 암호가 콘솔의 명령 기록에 표시되지 않습니다.

## <a name="authorize-without-a-secret-store"></a>비밀 저장소 없이 권한 부여

`azcopy login` 명령은 OAuth 토큰을 검색한 다음, 해당 토큰을 시스템의 비밀 저장소에 배치합니다. 운영 체제에 Linux *인증 키* 와 같은 비밀 저장소가 없는 경우 토큰을 배치할 수 없기 때문에 `azcopy login` 명령이 작동하지 않습니다.

`azcopy login` 명령을 사용하는 대신, 메모리 내 환경 변수를 설정할 수 있습니다. 그런 다음, AzCopy 명령을 실행합니다. AzCopy는 작업 완료에 필요한 인증 토큰을 검색합니다. 작업 완료 후에는 토큰이 메모리에서 사라집니다.

### <a name="authorize-a-user-identity"></a>사용자 ID 권한 부여

사용자 ID에 필요한 권한 부여 수준이 제공되었는지 확인한 후에는 다음 명령을 입력하고 ENTER 키를 누릅니다.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=DEVICE
```

그런 다음, azcopy 명령을 실행합니다(예: `azcopy list https://contoso.blob.core.windows.net`).

이 명령은 웹 사이트의 인증 코드와 URL을 반환합니다. 웹 사이트를 열고, 코드를 입력하고, **다음** 단추를 선택합니다.

![컨테이너 만들기](media/storage-use-azcopy-v10/azcopy-login.png)

로그인 창이 나타납니다. 이 창에서 Azure 계정 자격 증명을 사용하여 Azure 계정에 로그인합니다. 로그인한 후 작업을 완료할 수 있습니다.

### <a name="authorize-by-using-a-system-wide-managed-identity"></a>시스템 차원의 관리 ID를 사용하여 권한 부여

먼저 VM에서 시스템 차원의 관리 ID를 사용하도록 설정했는지 확인합니다. [시스템이 할당한 관리 ID 사용](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)을 참조하세요.

다음 명령을 입력하고 ENTER 키를 누릅니다.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=MSI
```

그런 다음, azcopy 명령을 실행합니다(예: `azcopy list https://contoso.blob.core.windows.net`).

### <a name="authorize-by-using-a-user-assigned-managed-identity"></a>사용자가 할당한 관리 ID를 사용하여 권한 부여

먼저 VM에서 사용자가 할당한 관리 ID를 사용하도록 설정했는지 확인합니다. [사용자가 할당한 관리 ID](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity)를 참조하세요.

다음 명령을 입력하고 ENTER 키를 누릅니다.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=MSI
```

이제 다음 명령 중 하나를 입력하고 ENTER 키를 누릅니다.

```bash
export AZCOPY_MSI_CLIENT_ID=<client-id>
```

`<client-id>` 자리 표시자를 사용자가 할당한 관리 ID의 클라이언트 ID로 바꿉니다.

```bash
export AZCOPY_MSI_OBJECT_ID=<object-id>
```

`<object-id>` 자리 표시자를 사용자가 할당한 관리 ID의 개체 ID로 바꿉니다.

```bash
export AZCOPY_MSI_RESOURCE_STRING=<resource-id>
```

`<resource-id>` 자리 표시자를 사용자가 할당한 관리 ID의 리소스 ID로 바꿉니다.

이러한 변수를 설정한 후에는 모든 azcopy 명령을 실행할 수 있습니다(예: `azcopy list https://contoso.blob.core.windows.net`).

### <a name="authorize-a-service-principal"></a>서비스 주체 권한 부여

클라이언트 암호나 서비스 주체의 앱 등록과 연결된 인증서의 암호를 사용하여 계정에 로그인할 수 있습니다.

#### <a name="authorize-a-service-principal-by-using-a-client-secret"></a>클라이언트 암호를 사용하여 서비스 주체 권한 부여

다음 명령을 입력하고 ENTER 키를 누릅니다.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=SPN
export AZCOPY_SPA_APPLICATION_ID=<application-id>
export AZCOPY_SPA_CLIENT_SECRET=<client-secret>
export AZCOPY_TENANT_ID=<tenant-id>
```

`<application-id>` 자리 표시자를 서비스 주체 앱 등록의 애플리케이션 ID로 바꿉니다. `<client-secret>` 자리 표시자를 클라이언트 암호로 바꿉니다. `<tenant-id>` 자리 표시자를 스토리지 계정이 속한 조직의 테넌트 ID로 바꿉니다. 테넌트 ID를 찾으려면 Azure Portal에서 **Azure Active Directory > 속성 > 디렉터리 ID** 를 선택합니다.

> [!NOTE]
> 메시지를 표시하여 사용자에게서 암호를 수집하는 것이 좋습니다. 이렇게 하면 암호가 명령 기록에 표시되지 않습니다.

그런 다음, azcopy 명령을 실행합니다(예: `azcopy list https://contoso.blob.core.windows.net`).

#### <a name="authorize-a-service-principal-by-using-a-certificate"></a>인증서를 사용하여 서비스 주체 권한 부여

권한 부여에 자체 자격 증명을 사용하려는 경우 인증서를 앱 등록에 업로드한 다음, 해당 인증서를 사용하여 로그인할 수 있습니다.

앱 등록에 인증서를 업로드하는 것과 함께 AzCopy가 실행될 컴퓨터나 VM에 인증서 사본을 저장해야 합니다. 이 인증서 사본은 .PFX 또는 .PEM 형식이면서, 프라이빗 키를 포함해야 합니다. 프라이빗 키는 암호로 보호되어야 합니다.

다음 명령을 입력하고 ENTER 키를 누릅니다.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=SPN
export AZCOPY_SPA_CERT_PATH=<path-to-certificate-file>
export AZCOPY_SPA_CERT_PASSWORD=<certificate-password>
export AZCOPY_TENANT_ID=<tenant-id>
```

`<path-to-certificate-file>` 자리 표시자를 인증서 파일의 상대 경로 또는 정규화된 경로로 바꿉니다. AzCopy는 이 인증서 경로를 저장하지만 인증서 사본을 저장하지는 않으므로 인증서를 그대로 유지해야 합니다. `<certificate-password>` 자리 표시자를 인증서 암호로 바꿉니다. `<tenant-id>` 자리 표시자를 스토리지 계정이 속한 조직의 테넌트 ID로 바꿉니다. 테넌트 ID를 찾으려면 Azure Portal에서 **Azure Active Directory > 속성 > 디렉터리 ID** 를 선택합니다.

> [!NOTE]
> 메시지를 표시하여 사용자에게서 암호를 수집하는 것이 좋습니다. 이렇게 하면 암호가 명령 기록에 표시되지 않습니다.

그런 다음, azcopy 명령을 실행합니다(예: `azcopy list https://contoso.blob.core.windows.net`).

## <a name="next-steps"></a>다음 단계

- AzCopy에 대한 자세한 내용은 [AzCopy 시작](storage-use-azcopy-v10.md)을 참조하세요.

- 질문, 문제 또는 일반 피드백이 있는 경우 [GitHub](https://github.com/Azure/azure-storage-azcopy) 페이지에서 제출하세요.
