---
title: NFS 3.0 프로토콜을 사용하여 Azure Blob Storage 탑재 | Microsoft Docs
description: NFS 3.0 프로토콜을 사용하여 온-프레미스에서 실행되는 Azure VM(가상 머신) 또는 클라이언트에서 Blob Storage에 컨테이너를 탑재하는 방법을 알아봅니다.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7173477e01faddb3c8aa3c18dc2781ef21dee012
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536328"
---
# <a name="mount-blob-storage-by-using-the-network-file-system-nfs-30-protocol"></a>NFS(네트워크 파일 시스템) 3.0 프로토콜을 사용하여 Blob Storage 탑재

NFS 3.0 프로토콜을 사용하여 온-프레미스에서 실행되는 Linux 기반 Azure VM(가상 머신) 또는 Linux 시스템에서 Blob Storage에 컨테이너를 탑재할 수 있습니다. 이 문서에서는 단계별 지침을 제공합니다. Blob 스토리지에서 NFS 3.0 프로토콜 지원에 대한 자세한 내용은 [Azure Blob Storage에서 NFS(네트워크 파일 시스템) 3.0 프로토콜 지원](network-file-system-protocol-support.md)을 참조하세요.

## <a name="step-1-create-an-azure-virtual-network-vnet"></a>1단계: Azure VNet(Virtual Network) 만들기

스토리지 계정은 VNet 내에 포함되어야 합니다. 클라이언트는 VNet을 통해 스토리지 계정에 안전하게 연결할 수 있습니다. VNet에 대한 자세한 내용과 VNet을 만드는 방법은 [Virtual Network 설명서](../../virtual-network/index.yml)를 참조하세요.

> [!NOTE]
> 동일한 VNet의 클라이언트는 계정에 컨테이너를 탑재할 수 있습니다. 온-프레미스 네트워크에서 실행되는 클라이언트에서 컨테이너를 탑재할 수도 있지만 먼저 온-프레미스 네트워크를 VNet에 연결해야 합니다. [지원되는 네트워크 연결](network-file-system-protocol-support.md#supported-network-connections)을 참조하세요.

## <a name="step-2-configure-network-security"></a>2단계: 네트워크 보안 구성

계정에서 데이터를 보호하는 유일한 방법은 VNet 및 기타 네트워크 보안 설정을 사용하는 것입니다. 계정 키 인증, Azure AD(Azure Active Directory) 보안 및 ACL(액세스 제어 목록)을 포함하여 데이터를 보호하는 데 사용되는 다른 모든 도구는 NFS 3.0 프로토콜 지원이 설정된 계정에서 아직 지원되지 않습니다.

계정에서 데이터를 보호하려면 권장 사항: [Blob Storage에 대한 네트워크 보안 권장 사항](security-recommendations.md#networking)을 참조하세요.

## <a name="step-3-create-and-configure-a-storage-account"></a>3단계: 스토리지 계정 만들기 및 구성

NFS 3.0을 사용하여 컨테이너를 탑재하려면 스토리지 계정을 만들어야 합니다. 기존 계정을 사용할 수 없습니다.

표준 범용 v2 스토리지 계정 및 프리미엄 블록 Blob 스토리지 계정에 대해 NFS 3.0 프로토콜이 지원됩니다. 이러한 스토리지 계정 유형에 대한 자세한 내용은 [스토리지 계정 개요](../common/storage-account-overview.md)를 참조하세요.

계정을 구성하는 경우 다음 값을 선택합니다.

|설정 | 프리미엄 성능 | 표준 성능  
|----|---|---|
|위치|사용 가능한 모든 지역 |사용 가능한 모든 지역    
|성능|Premium| Standard
|계정 종류|BlockBlobStorage| 범용 V2
|복제|LRS(로컬 중복 스토리지), ZRS(영역 중복 스토리지)| LRS(로컬 중복 스토리지), ZRS(영역 중복 스토리지)
|연결 방법|퍼블릭 엔드포인트(선택한 네트워크) 또는 프라이빗 엔드포인트 |퍼블릭 엔드포인트(선택한 네트워크) 또는 프라이빗 엔드포인트
|계층 구조 네임스페이스|사용|사용
|NFS V3|사용 |사용 

다른 모든 설정에 대한 기본값을 수락할 수 있습니다. 

## <a name="step-4-create-a-container"></a>4단계: 컨테이너 만들기

다음 도구 또는 SDK 중 하나를 사용하여 스토리지 계정에 컨테이너를 만듭니다.

|도구|SDK|
|---|---|
|[Azure Portal](https://portal.azure.com)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AZCopy](../common/storage-use-azcopy-v10.md#transfer-data)|[Java](data-lake-storage-directory-file-acl-java.md)|
|[PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[Azure CLI](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
||[REST (영문)](/rest/api/storageservices/create-container)|

> [!NOTE]
> 기본적으로 새 컨테이너의 루트 squash 옵션은 `no root squash`입니다. 하지만 `root squash` 또는 `all squash`로 변경할 수 있습니다. 이러한 squash 옵션에 대한 정보는 운영 체제 설명서를 참조하세요.

다음 이미지는 Azure Portal에서 표시되는 squash 옵션을 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![Azure Portal의 squash 옵션](./media/network-file-system-protocol-how-to/squash-options-azure-portal.png)

## <a name="step-5-mount-the-container"></a>5단계: 컨테이너 탑재

Linux 시스템에서 디렉터리를 만든 다음, 컨테이너를 스토리지 계정에 탑재합니다.

1. Linux 시스템에서 디렉터리를 만듭니다.

   ```
   mkdir -p /mnt/test
   ```

2. 다음 명령을 사용하여 컨테이너를 탑재합니다.

   ```
   mount -o sec=sys,vers=3,nolock,proto=tcp <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name>  /mnt/test
   ```

   - 이 명령에 표시되는 `<storage-account-name>` 자리 표시자를 스토리지 계정의 이름으로 바꿉니다.  

   - `<container-name>` 자리 표시자를 컨테이너의 이름으로 바꿉니다.

---

## <a name="resolve-common-errors"></a>일반적인 오류 해결

|오류 | 원인/해결 방법|
|---|---|
|`Access denied by server while mounting`|지원되는 서브넷 내에서 클라이언트가 실행되고 있는지 확인합니다. [지원되는 네트워크 위치](network-file-system-protocol-support.md#supported-network-connections)를 참조하세요.|
|`No such file or directory`| 탑재 명령과 해당 매개 변수를 터미널에 직접 입력해야 합니다. 이 명령의 일부를 복사하여 다른 애플리케이션의 터미널에 붙여넣는 경우 붙여넣은 정보에 숨겨진 문자가 있으면 이 오류가 발생할 수 있습니다.|
|`Permision denied`| 새로 만든 NFS v3 컨테이너의 기본 모드는 0750입니다. 루트가 아닌 사용자에게는 볼륨에 대한 액세스 권한이 없습니다. 루트가 아닌 사용자가 액세스해야 하는 경우 루트 사용자는 모드를 0755로 변경해야 합니다. 샘플 명령: `sudo chmod 0755 /mnt/<newcontainer>`|
|`EINVAL ("Invalid argument"`) |이 오류는 클라이언트에서 다음을 시도할 때 나타날 수 있습니다.<li>Blob 엔드포인트에서 만든 Blob에 씁니다.<li>스냅샷이 있거나 활성 WORM(한 번 쓰기, 여러 번 읽기) 정책을 포함하는 컨테이너에 있는 Blob을 삭제합니다.|
|`EROFS ("Read-only file system"`) |이 오류는 클라이언트에서 다음을 시도할 때 나타날 수 있습니다.<li>Blob에 쓰거나 활성 임대가 있는 Blob을 삭제합니다.<li>활성 WORM(한 번 쓰기, 여러 번 읽기) 정책이 있는 컨테이너에서 Blob에 쓰거나 Blob을 삭제합니다. |
|`NFS3ERR_IO/EIO ("Input/output error"`) |이 오류는 클라이언트에서 보관 액세스 계층에 저장된 Blob에 대한 특성을 읽거나 쓰거나 설정하려고 할 때 나타날 수 있습니다. |
|`OperationNotSupportedOnSymLink`개 오류| 이 오류는 Blob 또는 Azure Data Lake Storage Gen2 API를 통해 쓰기 작업을 수행하는 동안 반환될 수 있습니다. 이러한 API를 사용하여 NFS 3.0을 사용하여 만든 바로 가기 링크를 작성하거나 삭제하는 것은 허용되지 않습니다. 바로 가기 링크로 작업하려면 NFS v3 엔드포인트를 사용해야 합니다. |

## <a name="see-also"></a>참고 항목

- [Azure Blob Storage의 NFS(네트워크 파일 시스템) 3.0 프로토콜 지원](network-file-system-protocol-support.md)
- [Azure Blob Storage의 NFS(네트워크 파일 시스템) 3.0 프로토콜 지원의 알려진 문제](network-file-system-protocol-known-issues.md)
