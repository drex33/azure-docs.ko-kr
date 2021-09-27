---
title: Azure Storage Explorer 보안 가이드 | Microsoft Docs
description: Azure Storage Explorer에 대한 보안 지침
services: storage
author: cralvord
ms.service: storage
ms.topic: best-practice
ms.date: 07/30/2020
ms.author: cralvord
ms.openlocfilehash: b82071c393cea168fccb5e06db85e71852bbd4f8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636765"
---
# <a name="azure-storage-explorer-security-guide"></a>Azure Storage Explorer 보안 가이드

Microsoft Azure Storage Explorer를 사용하면 Windows, macOS 및 Linux에서 Azure Storage 데이터로 쉽고 안전하게 작업할 수 있습니다. 다음 지침에 따라 데이터를 보호된 상태로 유지할 수 있습니다.

## <a name="general"></a>일반

- **항상 최신 Storage Explorer 버전 사용.** Storage Explorer 릴리스에는 보안 업데이트가 포함될 수 있습니다. 최신 상태를 유지하면 일반적인 보안을 보장할 수 있습니다.
- **신뢰하는 리소스에만 연결.** 신뢰할 수 없는 소스에서 다운로드한 데이터는 악성 데이터일 수 있으며, 신뢰할 수 없는 소스에 데이터를 업로드하면 데이터가 손실되거나 데이터를 도난 당할 수 있습니다.
- **가능하면 항상 HTTPS 사용.** Storage Explorer는 기본적으로 HTTPS를 사용합니다. 일부 시나리오에서는 HTTP를 사용할 수 있지만 HTTP는 최후의 수단으로만 사용해야 합니다.
- **필요한 사용자에게 필요한 권한만 부여.** 사용자에게 리소스에 대한 액세스 권한을 부여할 때 과도하게 권한을 부여하지 않도록 합니다.
- **중요한 작업을 실행하는 경우 주의.** 삭제 및 덮어쓰기와 같은 특정 작업은 되돌릴 수 없으며 이로 인해 데이터가 손실될 수 있습니다. 이러한 작업을 실행하기 전에 올바른 리소스로 작업하고 있는지 확인합니다.

## <a name="choosing-the-right-authentication-method"></a>올바른 인증 방법 선택

Storage Explorer는 Azure Storage 리소스에 액세스하는 다양한 방법을 제공합니다. 어떤 방법을 선택하든 권장 사항은 다음과 같습니다.

### <a name="azure-ad-authentication"></a>Azure AD 인증

Azure Storage 리소스에 가장 쉽고 안전하게 액세스하는 방법은 Azure 계정으로 로그인하는 것입니다. 로그인할 때 Azure AD 인증을 사용하면 다음 작업을 수행할 수 있습니다.

- 특정 사용자 및 그룹에게 액세스 권한 부여
- 특정 사용자 및 그룹에게서 언제든지 액세스 권한 철회
- 다단계 인증 필요처럼 액세스 조건 적용

가능하면 항상 Azure AD 인증을 사용하는 것이 좋습니다.

이 섹션에서는 스토리지 리소스를 보호하는 데 사용할 수 있는 두 가지 Azure AD 기반 기술을 설명합니다.

#### <a name="azure-role-based-access-control-azure-rbac"></a>Azure RBAC(Azure 역할 기반 액세스 제어)

[Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/overview.md)는 Azure 리소스에 대한 세부적인 액세스 제어를 제공합니다. Azure 역할 및 사용 권한은 Azure Portal에서 관리할 수 있습니다.

Storage Explorer는 스토리지 계정, Blob 및 큐에 대한 Azure RBAC 액세스를 지원합니다. 파일 공유 또는 테이블에 액세스해야 하는 경우 스토리지 계정 키를 나열하는 권한을 부여하는 Azure 역할을 할당해야 합니다.

#### <a name="access-control-lists-acls"></a>ACL(액세스 제어 목록)

[ACL(액세스 제어 목록)](../blobs/data-lake-storage-access-control.md)을 사용하면 ADLS Gen2 Blob 컨테이너에서 파일 및 폴더 수준 액세스를 제어할 수 있습니다. Storage Explorer를 사용하여 ACL을 관리할 수 있습니다.

### <a name="shared-access-signatures-sas"></a>SAS(공유 액세스 서명)

Azure AD 인증을 사용할 수 없는 경우 공유 액세스 서명을 사용하는 것이 좋습니다. 공유 액세스 서명을 사용하면 다음 작업을 수행할 수 있습니다.

- 보안 리소스에 대한 제한된 익명 액세스를 제공합니다.
- SAP(공유 액세스 정책)에서 생성된 경우 즉시 SAS를 철회합니다.

그러나 공유 액세스 서명을 사용하면 다음 작업을 수행할 수 없습니다.

- SAS를 사용할 수 있는 사용자를 제한합니다. 유효한 SAS는 해당 서명을 가진 모든 사용자가 사용할 수 있습니다.
- SAP(공유 액세스 정책)에서 생성되지 않은 경우 SAS를 해지합니다.

Storage Explorer에서 SAS를 사용하는 경우 다음 지침을 따르는 것이 좋습니다.

- **SAS 토큰과 URI의 배포 제한.** 신뢰할 수 있는 개인에게만 SAS 토큰과 URI를 배포합니다. SAS 배포를 제한하면 SAS가 잘못 사용될 가능성이 줄어듭니다.
- **신뢰하는 엔터티의 SAS 토큰 및 URI만 사용**
- **가능한 경우 SAS 토큰 및 URI를 생성할 때 SAP(공유 액세스 정책) 사용.** SAS는 SAP를 삭제하여 철회할 수 있기 때문에 공유 액세스 정책을 기반으로 하는 SAS는 일반 SAS보다 더 안전합니다.
- **최소 리소스 액세스 및 사용 권한으로 토큰 생성.** 최소 사용 권한은 SAS가 잘못 사용되는 경우 수행할 수 있는 잠재적 손상을 제한합니다.
- **필요한 기간에만 유효한 토큰 생성.** SAS는 생성되면 철회할 방법이 없기 때문에 기본 SAS의 수명은 특히 짧아야 합니다.

> [!IMPORTANT]
> 서비스 요청 또는 버그 보고서 등 문제 해결을 위해 SAS 토큰과 URI를 공유하는 경우 항상 SAS의 서명 부분을 하나 이상 편집합니다.

### <a name="storage-account-keys"></a>Storage 계정 키

스토리지 계정 키는 스토리지 계정 내에서 서비스 및 리소스에 무제한으로 액세스할 수 있는 권한을 부여합니다. 따라서 Storage Explorer에서 리소스에 액세스할 때 키 사용을 제한하는 것이 좋습니다. 대신 Azure RBAC 기능 또는 SAS를 사용하여 액세스 권한을 제공합니다.

일부 Azure 역할은 스토리지 계정 키를 검색할 수 있는 권한을 부여합니다. 이러한 역할을 가진 개인은 Azure RBAC에서 부여하거나 거부한 권한을 효과적으로 우회할 수 있습니다. 필요한 경우가 아니면 이 사용 권한을 부여하지 않는 것이 좋습니다.

Storage Explorer는 스토리지 계정 키(사용 가능한 경우)를 사용하여 요청을 인증하려고 합니다. 이 기능은 설정(**서비스 > 스토리지 계정 > 키 사용 안 함**)에서 사용하지 않도록 설정할 수 있습니다. 일부 기능은 클래식 스토리지 계정으로 작업하는 등의 Azure RBAC를 지원하지 않습니다. 해당 기능은 키가 필요하지만 이 설정의 영향을 받지 않습니다.

키를 사용하여 스토리지 리소스에 액세스해야 하는 경우 다음 지침을 따르는 것이 좋습니다.

- **누구와도 계정 키를 공유하지 않음**
- **스토리지 계정 키를 암호와 같이 취급.** 키에 액세스할 수 있도록 설정해야 하는 경우 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)와 같은 보안 스토리지 솔루션을 사용합니다.

> [!NOTE]
> 스토리지 계정 키가 실수로 공유 또는 배포되었다고 생각되는 경우 Azure Portal에서 스토리지 계정에 대해 새 키를 생성할 수 있습니다.

### <a name="public-access-to-blob-containers"></a>Blob 컨테이너에 대한 퍼블릭 액세스

Storage Explorer를 사용하면 Azure Blob Storage 컨테이너의 액세스 수준을 수정할 수 있습니다. 프라이빗이 아닌 Blob 컨테이너는 누구나 컨테이너 내 데이터에 대한 익명 읽기 액세스를 허용합니다.

Blob 컨테이너에 퍼블릭 액세스를 사용하도록 설정하는 경우 다음 지침을 따르는 것이 좋습니다.

- **잠재적으로 중요한 데이터가 있을 수 있는 Blob 컨테이너에 대해 퍼블릭 액세스를 사용하도록 설정하지 않음.** Blob 컨테이너에 프라이빗 데이터가 아예 없는지 확인합니다.
- **Blob 또는 컨테이너 액세스 권한을 사용하여 잠재적으로 중요한 데이터를 Blob 컨테이너에 업로드하지 않음**

## <a name="next-steps"></a>다음 단계

- [보안 권장 사항](../blobs/security-recommendations.md)
