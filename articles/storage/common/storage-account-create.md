---
title: 스토리지 계정 만들기
titleSuffix: Azure Storage
description: Blob, 파일, 큐, 테이블을 저장할 스토리지 계정을 만드는 방법에 대해 알아봅니다. Azure 스토리지 계정은 데이터를 읽고 쓰기 위해 Microsoft Azure에 고유한 네임스페이스를 제공합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/18/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 3be79a2af3dfb05b289a0111c9e1f9fcf17bab02
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555549"
---
# <a name="create-a-storage-account"></a>스토리지 계정 만들기

Azure Storage 계정에는 Blob, 파일, 큐, 테이블 및 디스크 등, 모든 Azure Storage 데이터 개체가 포함됩니다. 스토리지 계정은 Azure Storage 데이터에 고유한 네임스페이스를 제공하여 전 세계 어디서나 HTTP 또는 HTTPS를 통해 액세스할 수 있도록 합니다. Azure Storage 계정에 대한 자세한 내용은 [스토리지 계정 개요](storage-account-overview.md)를 참조하세요.

이 방법 문서에서는 [Azure Portal](https://portal.azure.com/), [Azure PowerShell](/powershell/azure/), [Azure CLI](/cli/azure) 또는 [Azure Resource Manager 템플릿](../../azure-resource-manager/management/overview.md)을 사용하여 스토리지 계정을 만드는 방법에 대해 알아봅니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

없음

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 Azure Storage 계정을 만들려면 [Az PowerShell 모듈](https://www.powershellgallery.com/packages/Az) 버전 0.7 이상을 설치했는지 확인합니다. 자세한 내용은 [Azure PowerShell Az 모듈 소개](/powershell/azure/new-azureps-module-az)를 참조하세요.

현재 버전을 확인하려면 다음 명령을 실행합니다.

```powershell
Get-InstalledModule -Name "Az"
```

Azure PowerShell을 설치 또는 업그레이드하려면 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure에 로그인하고 다음 두 방법 중 하나로 Azure CLI 명령을 실행할 수 있습니다.

- Azure Portal 내에서 Azure Cloud Shell을 사용하여 CLI 명령을 실행합니다.
- CLI를 설치하고 로컬로 CLI를 실행합니다.

### <a name="use-azure-cloud-shell"></a>Azure Cloud Shell 사용

Azure Cloud Shell은 Azure Portal에서 직접 실행할 수 있는 평가판 Bash 셸입니다. Azure CLI가 사전 설치되어 계정에서 사용하도록 구성됩니다. Azure Portal의 오른쪽 위 섹션에 있는 메뉴에서 **Cloud Shell** 단추를 클릭합니다.

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

이 단추는 이 방법 문서에 간략히 설명된 단계를 실행하는 데 사용할 수 있는 대화형 셸을 시작합니다.

[![포털에서 Cloud Shell 창을 보여 주는 스크린샷](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>로컬에서 CLI 설치

Azure CLI를 로컬에서 설치하여 사용할 수도 있습니다. 이 문서의 예제에는 Azure CLI 버전 2.0.4 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

# <a name="template"></a>[템플릿](#tab/template)

없음

---

그런 다음, Azure에 로그인합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

[Azure Portal](https://portal.azure.com)에 로그인합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

`Connect-AzAccount` 명령으로 Azure 구독에 로그인하고 화면의 지시에 따라 인증합니다.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure Cloud Shell을 시작하려면 [Azure Portal](https://portal.azure.com)에 로그인합니다.

CLI의 로컬 설치에 로그인하려면 [az login](/cli/azure/reference-index#az_login) 명령을 실행합니다.

```azurecli-interactive
az login
```

# <a name="template"></a>[템플릿](#tab/template)

해당 없음

---

## <a name="create-a-storage-account"></a>스토리지 계정 만들기

스토리지 계정은 Azure Resource Manager 리소스입니다. Resource Manager는 Azure용 배포 및 관리 서비스입니다. 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/management/overview.md)를 참조하세요.

Azure Storage 계정을 포함한 모든 Resource Manager 리소스는 Azure 리소스 그룹에 속해야 합니다. 리소스 그룹은 Azure 리소스를 그룹화하기 위한 논리적 컨테이너입니다. 스토리지 계정을 만들 때 새 리소스 그룹을 만들거나 기존 리소스 그룹을 사용할 수 있는 옵션이 있습니다. 이 방법에서는 새 리소스 그룹을 만드는 방법을 보여 줍니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 Azure Storage 계정을 만들려면 다음 단계를 수행합니다.

1. 왼쪽 포털 메뉴에서 **스토리지 계정** 을 선택하여 스토리지 계정 목록을 표시합니다.
1. **스토리지 계정** 페이지에서 **생성하기** 를 선택합니다.

새 스토리지 계정에 대한 옵션은 **스토리지 계정 만들기** 페이지에 탭으로 구성됩니다. 다음 섹션에서는 각 탭 및 해당 옵션에 대해 설명합니다.

### <a name="basics-tab"></a>기본 사항 탭

**기본 사항** 탭에서 스토리지 계정에 대한 필수 정보를 제공합니다. **기본 사항** 탭을 완료한 후 다른 탭에서 옵션을 설정하여 새 스토리지 계정을 추가로 사용자 지정하도록 선택하거나 **검토 + 만들기** 를 선택하여 기본 옵션을 적용하고 계정이 유효한지 검사한 후 만들 수 있습니다.

다음 표에서는 **기본 사항** 탭의 필드에 대해 설명합니다.

| 섹션 | 필드 | 필수 또는 선택 | Description |
|--|--|--|--|
| 프로젝트 세부 정보 | Subscription | 필수 | 새 스토리지 계정의 구독을 선택합니다. |
| 프로젝트 세부 정보 | Resource group | 필수 | 이 스토리지 계정에 대한 새 리소스 그룹을 만들거나 기존 리소스 그룹을 선택합니다. 자세한 내용은 [리소스 그룹](../../azure-resource-manager/management/overview.md#resource-groups)을 참조하세요. |
| 인스턴스 세부 정보 | 스토리지 계정 이름 | 필수 | 스토리지 계정의 고유한 이름을 선택합니다. Storage 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다. |
| 인스턴스 세부 정보 | 지역 | 필수 | 스토리지 계정에 적합한 지역을 선택합니다. 자세한 내용은 [Azure의 지역 및 가용성 영역](../../availability-zones/az-overview.md)을 참조하세요.<br /><br />모든 유형의 스토리지 계정 또는 중복 구성이 모든 지역에 대해 지원되는 것은 아닙니다. 자세한 내용은 [Azure Storage 중복성](storage-redundancy.md)을 참조하세요.<br /><br />지역 선택은 요금 청구에 영향을 줄 수 있습니다. 자세한 내용은 [스토리지 계정 청구](storage-account-overview.md#storage-account-billing)를 참조하세요. |
| 인스턴스 세부 정보 | 성능 | 필수 | 범용 v2 스토리지 계정의 **표준** 성능(기본값)을 선택합니다. 이 유형의 계정은 대부분의 시나리오에서 권장됩니다. 자세한 정보는 [스토리지 계정 유형](storage-account-overview.md#types-of-storage-accounts)을 참조하세요.<br /><br />짧은 대기 시간이 필요한 시나리오의 경우 **프리미엄** 을 선택합니다. **프리미엄** 을 선택한 후 만들 프리미엄 스토리지 계정의 유형을 선택합니다. 다음 유형의 프리미엄 스토리지 계정을 사용할 수 있습니다. <ul><li>[블록 Blob](./storage-account-overview.md)</li><li>[파일 공유](../files/storage-files-planning.md#management-concepts)</li><li>[페이지 Blob](../blobs/storage-blob-pageblob-overview.md)</li></ul> |
| 인스턴스 세부 정보 | 중복 | 필수 | 원하는 중복성 구성을 선택합니다. 모든 지역에서 모든 유형의 스토리지 계정에 대해 모든 중복성 옵션을 사용할 수 있는 것은 아닙니다. 중복성 구성에 대한 자세한 내용은 [Azure Storage 중복성](storage-redundancy.md)을 참조하세요.<br /><br />지역 중복 구성(GRS 또는 GZRS)을 선택하는 경우 데이터가 다른 지역의 데이터 센터에 복제됩니다. 보조 지역의 데이터에 대한 읽기 액세스의 경우 **지역 사용 불가 시 데이터에 대한 읽기 액세스를 사용하도록 지정** 을 선택합니다. |

다음 이미지는 새 스토리지 계정에 대한 표준 구성을 보여 줍니다.

:::image type="content" source="media/storage-account-create/create-account-basics-tab.png" alt-text="새 스토리지 계정에 대한 표준 구성 - 기본 사항 탭을 보여 주는 스크린샷":::

### <a name="advanced-tab"></a>고급 탭

**고급** 탭에서 추가 옵션을 구성하고 새 스토리지 계정에 대한 기본 설정을 수정할 수 있습니다. 이러한 옵션 중 일부는 스토리지 계정을 만든 후에 구성할 수도 있고 만들 때 구성해야 하는 경우도 있습니다.

다음 표에서는 **고급** 탭의 필드에 대해 설명합니다.

| 섹션 | 필드 | 필수 또는 선택 | 설명 |
|--|--|--|--|
| 보안 | 보안 전송 사용 | 선택 사항 | 이 스토리지 계정에 들어오는 요청이 HTTPS(기본값)를 통해서만 수행되도록 하려면 보안 전송을 사용하도록 설정합니다. 최적의 보안을 위해 권장됩니다. 자세한 내용은 [보안 연결을 위해 보안 전송 필요](storage-require-secure-transfer.md)를 참조하세요. |
| 보안 | 인프라 암호화 사용 | 선택 사항 | 기본적으로 인프라 암호화는 사용되지 않습니다. 서비스 수준 및 인프라 수준 둘 다에서 데이터를 암호화하려면 인프라 암호화를 사용하도록 설정합니다. 자세한 내용은 [데이터의 이중 암호화를 위해 인프라 암호화가 사용하도록 설정된 스토리지 계정 만들기](infrastructure-encryption-enable.md)를 참조하세요. |
| 보안 | Blob 퍼블릭 액세스 사용 | 선택 사항 | 이 설정을 사용하도록 지정하면 적절한 권한이 있는 사용자가 스토리지 계정의 컨테이너에 대한 익명 퍼블릭 액세스를 사용하도록 설정할 수 있습니다(기본값). 이 설정을 사용하지 않도록 지정하면 스토리지 계정에 대한 모든 익명 액세스가 허용되지 않습니다. 자세한 내용은 [컨테이너 및 Blob에 대한 익명 퍼블릭 읽기 권한 방지](../blobs/anonymous-read-access-prevent.md)를 참조하세요.<br> <br> Blob 퍼블릭 액세스를 사용하도록 설정해도 사용자가 컨테이너의 퍼블릭 액세스 설정을 명시적으로 구성하는 추가 단계를 수행하지 않는 한, Blob 데이터를 퍼블릭으로 액세스할 수 없습니다. |
| 보안 | 스토리지 계정 키 액세스 사용(미리 보기) | 선택 사항 | 이 설정을 사용하도록 지정하면 클라이언트가 계정 액세스 키 또는 Azure AD(Azure Active Directory) 계정(기본값)을 사용하여 스토리지 계정에 요청 권한을 부여할 수 있습니다. 이 설정을 사용하지 않도록 지정하면 계정 액세스 키를 사용하여 권한을 부여할 수 없습니다. 자세한 내용은 [Azure Storage 계정에 대한 공유 키 권한 부여 방지](shared-key-authorization-prevent.md)를 참조하세요. |
| 보안 | 최소 TLS 버전 | 필수 | 스토리지 계정으로 들어오는 요청에 대해 최소 버전의 TLS(전송 계층 보안)를 선택합니다. 기본값은 TLS 버전 1.2입니다. 기본값으로 설정하면 TLS 1.0 또는 TLS 1.1을 사용하여 만든 들어오는 요청이 거부됩니다. 자세한 내용은 [스토리지 계정에 대한 요청에 대해 필요한 최소 버전의 TLS(전송 계층 보안) 적용](transport-layer-security-configure-minimum-version.md)을 참조하세요. |
| Data Lake Storage Gen2 | 계층 구조 네임스페이스 사용 | 선택 사항 | Azure Data Lake Storage Gen2 워크로드에 이 스토리지 계정을 사용하려면 계층 구조 네임스페이스를 구성합니다. 자세한 내용은 [Azure Data Lake Storage Gen2 소개](../blobs/data-lake-storage-introduction.md)를 참조하세요. |
| SFTP (Secure 파일 전송 프로토콜) | SFTP 사용 | 선택 사항 | SFTP (Secure 파일 전송 프로토콜)를 사용 하 여 인터넷을 통해 데이터를 안전 하 게 전송할 수 있습니다. 자세한 내용은 [Azure Blob Storage에서 SFTP (Secure File Transfer) 프로토콜 지원](../blobs/secure-file-transfer-protocol-support.md)을 참조 하세요. |
| Blob Storage | 네트워크 파일 공유(NFS) v3 사용 | 선택 사항 | NFS v3는 개체 스토리지 스케일로 Linux 파일 시스템 호환성을 제공하고, Linux 클라이언트가 Azure VM(가상 머신) 또는 온-프레미스 컴퓨터에서 Blob Storage에 컨테이너를 탑재할 수 있게 해줍니다. 자세한 내용은 [Azure Blob Storage에서 네트워크 파일 시스템(NFS) 3.0 프로토콜 지원](../blobs/network-file-system-protocol-support.md)을 참조하세요. |
| Blob Storage | 액세스 계층 | 필수 | Blob 액세스 계층을 사용하여 사용량에 따라 가장 비용 효율적인 방식으로 Blob 데이터를 저장할 수 있습니다. 자주 액세스하는 데이터에 대해 핫 계층(기본값)을 선택합니다. 자주 액세스하지 않는 데이터에 대해 쿨 계층을 선택합니다. 자세한 내용은 [blob 데이터에 대 한 핫, 쿨 및 보관 액세스 계층](../blobs/access-tiers-overview.md)을 참조 하세요. |
| Azure Files | 대량 파일 공유 사용 | 선택 사항 | LRS 또는 ZRS 중복을 사용하는 표준 파일 공유에만 사용할 수 있습니다. |
| 테이블 및 큐 | 고객 관리형 키에 대한 지원을 사용하도록 설정 | 선택 사항 | 테이블 및 큐에 대한 고객 관리형 키 지원을 사용하도록 설정하려면 스토리지 계정을 만들 때 이 설정을 선택해야 합니다. 자세한 내용은 [테이블 및 큐에 대한 고객 관리형 키를 지원하는 계정 만들기](account-encryption-key-create.md)를 참조하세요. |

### <a name="networking-tab"></a>네트워킹 탭

**네트워킹** 탭에서 새 스토리지 계정에 대한 네트워크 연결 및 라우팅 기본 설정을 구성할 수 있습니다. 스토리지 계정을 만든 후에도 이러한 옵션을 구성할 수 있습니다.

다음 표에서는 **네트워킹** 탭의 필드에 대해 설명합니다.

| 섹션 | 필드 | 필수 또는 선택 | Description |
|--|--|--|--|
| 네트워크 연결 | 연결 방법 | 필수 | 기본적으로 들어오는 네트워크 트래픽은 스토리지 계정의 퍼블릭 엔드포인트로 라우팅됩니다. Azure 가상 네트워크를 통해 트래픽을 퍼블릭 엔드포인트로 라우팅해야 하도록 지정할 수 있습니다. 스토리지 계정에 대한 프라이빗 엔드포인트를 구성할 수도 있습니다. 자세한 내용은 [Azure Storage에 프라이빗 엔드포인트 사용](storage-private-endpoints.md)을 참조하세요. |
| 네트워크 라우팅 | 라우팅 기본 설정 | 필수 | 네트워크 라우팅 기본 설정에서 네트워크 트래픽이 인터넷을 통해 클라이언트에서 스토리지 계정의 퍼블릭 엔드포인트로 라우팅되는 방식을 지정합니다. 기본적으로 새 스토리지 계정은 Microsoft 네트워크 라우팅을 사용합니다. 스토리지 계정에 가장 가까운 POP를 통과해서 네트워크 트래픽을 라우팅하도록 선택할 수도 있습니다. 그러면 네트워킹 비용이 절감될 수 있습니다. 자세한 내용은 [Azure Storage에 대한 네트워크 라우팅 기본 설정](network-routing-preference.md)을 참조하세요. |

### <a name="data-protection-tab"></a>데이터 보호 탭

**데이터 보호** 탭에서 새 스토리지 계정의 Blob 데이터에 대한 데이터 보호 옵션을 구성할 수 있습니다.  스토리지 계정을 만든 후에도 이러한 옵션을 구성할 수 있습니다. Azure Storage의 데이터 보호 옵션에 대한 개요는 [데이터 보호 개요](../blobs/data-protection-overview.md)를 참조하세요.

다음 표에서는 **데이터 보호** 탭의 필드에 대해 설명합니다.

| 섹션 | 필드 | 필수 또는 선택 | Description |
|--|--|--|--|
| 복구 | 컨테이너에 특정 시점 복원 사용 | 선택 사항 | 특정 시점 복원을 사용하면 블록 BLOB 데이터를 이전 상태로 복원하여 실수로 인한 삭제 또는 손상을 방지할 수 있습니다. 자세한 내용은 [블록 Blob에 대한 특정 시점 복원](../blobs/point-in-time-restore-overview.md)을 참조하세요.<br /><br />또한 특정 시점 복원을 사용하도록 설정하면 Blob 버전, Blob 일시 삭제 및 Blob 변경 피드도 사용할 수 있습니다. 이러한 필수 요소 기능은 비용에 영향을 미칠 수 있습니다. 자세한 내용은 특정 시점 복원에 대한 [가격 책정 및 청구](../blobs/point-in-time-restore-overview.md#pricing-and-billing)를 참조하세요. |
| 복구 | Blob에 일시 삭제를 사용하도록 설정 | 선택 사항 | Blob 일시 삭제는 지정된 보존 기간 동안 시스템에 삭제된 데이터를 유지하여 실수로 인한 삭제 또는 덮어쓰기로부터 개별 Blob, 스냅샷 또는 버전을 보호합니다. 보존 기간 중 일시 삭제된 개체를 삭제된 시점의 상태로 복원할 수 있습니다. 자세한 내용은 [Blob에 대한 일시 삭제](../blobs/soft-delete-blob-overview.md)를 참조하세요.<br /><br />스토리지 계정에 대해 Blob 일시 삭제를 사용하도록 설정하고 최소 보존 기간을 7일로 설정하는 것이 좋습니다. |
| 복구 | 컨테이너에 대한 일시 삭제 사용(미리 보기) | 선택 사항 | 컨테이너 일시 삭제는 지정된 보존 기간 동안 삭제된 데이터를 시스템에서 유지 관리하여 컨테이너 및 해당 내용이 실수로 삭제되지 않도록 보호합니다. 보존 기간 동안 일시 삭제된 컨테이너를 삭제된 시점의 상태로 복원할 수 있습니다. 자세한 내용은 [컨테이너의 일시 삭제(미리 보기)](../blobs/soft-delete-container-overview.md)를 참조하세요.<br /><br />스토리지 계정에 대해 컨테이너 일시 삭제를 사용하도록 설정하고 최소 보존 기간을 7일로 설정하는 것이 좋습니다. |
| 복구 | 파일 공유에 일시 삭제 사용 | 선택 사항 | 파일 공유의 일시 삭제는 지정된 보존 기간 동안 삭제된 데이터를 시스템에서 유지 관리하여 파일 공유 및 해당 내용이 실수로 삭제되지 않도록 보호합니다. 보존 기간 중 일시 삭제된 파일 공유를 삭제된 시점의 상태로 복원할 수 있습니다. 자세한 내용은 [실수로 인한 Azure 파일 공유 삭제 방지](../files/storage-files-prevent-file-share-deletion.md)를 참조하세요.<br /><br />Microsoft는 Azure Files 워크로드의 파일 공유에 대해 일시 삭제를 사용하도록 설정하고 최소 보존 기간을 7일로 설정하도록 권장합니다. |
| 추적 | Blob에 버전 관리 사용 | 선택 사항 | Blob 버전 관리 기능은 Blob을 덮어쓸 때 이전 버전의 Blob 상태를 자동으로 저장합니다. 자세한 내용은 [Blob 버전 관리](../blobs/versioning-overview.md)를 참조하세요.<br /><br />스토리지 계정에 대한 최적의 데이터 보호를 위해 Blob 버전 관리를 사용하도록 설정하는 것이 좋습니다. |
| 추적 | Blob 변경 피드 사용 | 선택 사항 | Blob 변경 피드는 스토리지 계정의 모든 Blob 및 해당 메타데이터에 대한 모든 변경 내용의 트랜잭션 로그를 제공합니다. 자세한 내용은 [Azure Blob Storage 변경 피드 지원](../blobs/storage-blob-change-feed.md)을 참조하세요. |

### <a name="tags-tab"></a>태그 탭

**태그** 탭에서 Resource Manager 태그를 지정하여 Azure 리소스를 편리하게 구성할 수 있습니다. 자세한 내용은 [논리적 조직의 리소스, 리소스 그룹 및 구독에 태그 지정](../../azure-resource-manager/management/tag-resources.md)을 참조하세요.

### <a name="review--create-tab"></a>리뷰 + 만들기 탭

**검토 + 만들기** 탭으로 이동하면 Azure는 선택한 스토리지 계정 설정에 대해 유효성 검사를 실행합니다. 유효성 검사를 통과하면 스토리지 계정 만들기를 진행할 수 있습니다.

유효성 검사에 실패하면 Portal에 수정해야 하는 설정이 표시됩니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 범용 v2 스토리지 계정을 만들려면 먼저 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 명령을 호출하여 새 리소스 그룹을 만듭니다.

```azurepowershell-interactive
$resourceGroup = "<resource-group>"
$location = "<location>"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

`-Location` 매개 변수에 어떤 지역을 지정할지 확실하지 않으면 [Get-AzLocation](/powershell/module/az.resources/get-azlocation) 명령을 사용하여 해당 구독에 대해 지원되는 지역 목록을 검색할 수 있습니다.

```azurepowershell-interactive
Get-AzLocation | select Location
```

그런 다음 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) 명령을 사용하여 읽기 액세스 지역 중복 스토리지(RA-GRS)를 사용하는 표준 범용 v2 스토리지 계정을 만듭니다. 스토리지 계정의 이름은 Azure 전체에서 고유해야 하므로, 괄호 안의 자리 표시자 값을 사용자 고유의 값으로 바꿉니다.

```azurepowershell-interactive
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

스토리지 계정에서 [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)를 사용하도록 계층 구조 네임스페이스를 설정하려면 **New-AzStorageAccount** 명령에 대한 호출에서 `EnableHierarchicalNamespace' parameter to `$True'를 설정합니다.

다음 표에서는 원하는 중복성 구성을 사용하여 특정 유형의 스토리지 계정을 만들기 위해 `SkuName` 및 `Kind` 매개 변수에 사용할 값을 보여 줍니다.

| 스토리지 계정의 유형 | 지원되는 중복성 구성 | Kind 매개 변수에 대해 지원되는 값 | SkuName 매개 변수에 대해 지원되는 값 | 계층 구조 네임스페이스 지원 |
|--|--|--|--|--|
| 표준 범용 v2 | LRS / GRS / RA-GRS / ZRS / GZRS / RA-GZRS | StorageV2 | Standard_LRS / Standard_GRS / Standard_RAGRS/ Standard_ZRS / Standard_GZRS / Standard_RAGZRS | 예 |
| Premium 블록 Blob | LRS / ZRS | BlockBlobStorage | Premium_LRS / Premium_ZRS | 예 |
| 프리미엄 파일 공유 | LRS / ZRS | FileStorage | Premium_LRS / Premium_ZRS | 아니요 |
| 프리미엄 페이지 Blob | LRS | StorageV2 | Premium_LRS | 아니요 |
| 레거시 표준 범용 v1 | LRS / GRS / RA-GRS | 스토리지 | Standard_LRS / Standard_GRS / Standard_RAGRS | 아니요 |
| 레거시 Blob Storage | LRS / GRS / RA-GRS | BlobStorage | Standard_LRS / Standard_GRS / Standard_RAGRS | 아니요 |

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 범용 v2 스토리지 계정을 만들려면 먼저 [az group create](/cli/azure/group#az_group_create) 명령을 호출하여 새 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create \
  --name storage-resource-group \
  --location westus
```

`--location` 매개 변수에 어떤 지역을 지정할지 확실하지 않으면 [az account list-locations](/cli/azure/account#az_account_list) 명령을 사용하여 해당 구독에 대해 지원되는 지역을 검색할 수 있습니다.

```azurecli-interactive
az account list-locations \
  --query "[].{Region:name}" \
  --out table
```

그런 다음, [az storage account create](/cli/azure/storage/account#az_storage_account_create) 명령을 사용하여 읽기 액세스 지역 중복 스토리지를 사용하는 표준 범용 v2 스토리지 계정을 만듭니다. 스토리지 계정의 이름은 Azure 전체에서 고유해야 하므로, 괄호 안의 자리 표시자 값을 사용자 고유의 값으로 바꿉니다.

```azurecli-interactive
az storage account create \
  --name <account-name> \
  --resource-group storage-resource-group \
  --location westus \
  --sku Standard_RAGRS \
  --kind StorageV2
```

스토리지 계정에서 [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)를 사용하도록 계층 구조 네임스페이스를 설정하려면 **az storage account create** 명령에 대한 호출에서 `enable-hierarchical-namespace` 매개 변수를 `true`로 설정합니다. 계층 구조 네임스페이스를 만들려면 Azure CLI 버전 2.0.79 이상이 필요합니다.

다음 표에서는 원하는 중복성 구성을 사용하여 특정 유형의 스토리지 계정을 만들기 위해 `sku` 및 `kind` 매개 변수에 사용할 값을 보여 줍니다.

| 스토리지 계정의 유형 | 지원되는 중복성 구성 | Kind 매개 변수에 대해 지원되는 값 | sku 매개 변수에 대해 지원되는 값 | 계층 구조 네임스페이스 지원 |
|--|--|--|--|--|
| 표준 범용 v2 | LRS / GRS / RA-GRS / ZRS / GZRS / RA-GZRS | StorageV2 | Standard_LRS / Standard_GRS / Standard_RAGRS/ Standard_ZRS / Standard_GZRS / Standard_RAGZRS | 예 |
| Premium 블록 Blob | LRS / ZRS | BlockBlobStorage | Premium_LRS / Premium_ZRS | 예 |
| 프리미엄 파일 공유 | LRS / ZRS | FileStorage | Premium_LRS / Premium_ZRS | 아니요 |
| 프리미엄 페이지 Blob | LRS | StorageV2 | Premium_LRS | 아니요 |
| 레거시 표준 범용 v1 | LRS / GRS / RA-GRS | 스토리지 | Standard_LRS / Standard_GRS / Standard_RAGRS | 아니요 |
| 레거시 Blob Storage | LRS / GRS / RA-GRS | BlobStorage | Standard_LRS / Standard_GRS / Standard_RAGRS | 아니요 |

# <a name="template"></a>[템플릿](#tab/template)

Azure PowerShell 또는 Azure CLI를 사용하여 스토리지 계정을 만드는 Resource Manager 템플릿을 배포할 수 있습니다. 이 방법 문서에 사용된 템플릿은 [Azure Resource Manager 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/storage-account-create/)에서 가져온 것입니다. 스크립트를 실행하려면 **사용해 보세요.** 를 선택하여 Azure Cloud Shell을 엽니다. 스크립트를 붙여넣으려면 셸을 마우스 오른쪽 단추로 클릭하고 **붙여넣기** 를 선택합니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.storage/storage-account-create/azuredeploy.json"
```

> [!NOTE]
> 이 템플릿은 예제로만 사용됩니다. 이 템플릿의 일부로 구성되지 않은 스토리지 계정 설정이 많이 있습니다. 예를 들어 [Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)를 사용하려는 경우 `StorageAccountPropertiesCreateParameters` 개체의 `isHnsEnabledad` 속성을 `true`로 선택하여 이 템플릿을 수정합니다.

이 템플릿을 수정하거나 새 템플릿을 만드는 방법에 대해 알아보려면 다음을 참조하세요.

- [Azure Resource Manager 설명서](../../azure-resource-manager/index.yml)
- [스토리지 계정 템플릿 참조](/azure/templates/microsoft.storage/allversions).
- [추가 스토리지 계정 템플릿 샘플](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)

---

## <a name="delete-a-storage-account"></a>스토리지 계정 삭제

스토리지 계정 삭제는 계정의 모든 데이터를 포함한 전체 계정을 삭제합니다. 계정을 삭제하기 전에 저장할 모든 데이터를 백업했는지 확인합니다.

특정 상황에서는 삭제된 스토리지 계정이 복구될 수 있지만 반드시 복구된다고 보장할 수는 없습니다. 자세한 내용은 [삭제된 스토리지 계정 복구](storage-account-recover.md)를 참조하세요.

Azure 가상 머신과 연결된 스토리지 계정을 삭제하려고 하는 경우 아직 사용 중인 스토리지 계정에 대한 오류가 발생할 수 있습니다. 이 오류의 문제를 해결하는 도움말은 [스토리지 계정 삭제 시 발생하는 오류 문제 해결](/troubleshoot/azure/virtual-machines/storage-resource-deletion-errors)을 참조하세요.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.
1. **삭제** 를 클릭합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

스토리지 계정을 삭제하려면 [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) 명령을 사용합니다.

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

스토리지 계정을 삭제하려면 [az storage account delete](/cli/azure/storage/account#az_storage_account_delete) 명령을 사용합니다.

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[템플릿](#tab/template)

스토리지 계정을 삭제하려면 Azure PowerShell 또는 Azure CLI를 사용합니다.

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

또는 리소스 그룹을 삭제하여 해당 리소스 그룹에 있는 스토리지 계정과 기타 리소스를 삭제할 수 있습니다. 리소스 그룹을 삭제하는 방법에 대한 자세한 내용은 [리소스 그룹 및 리소스 삭제](../../azure-resource-manager/management/delete-resource-group.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Storage 계정 개요](storage-account-overview.md)
- [범용 v2 스토리지 계정으로 업그레이드](storage-account-upgrade.md)
- [스토리지 계정을 다른 지역으로 이동](storage-account-move.md)
- [삭제된 스토리지 계정 복구](storage-account-recover.md)