---
title: SFTP 프로토콜(미리 보기) | 사용하여 Azure Blob Storage 커넥트 Microsoft Docs
description: SFTP 클라이언트를 사용하여 Azure Storage 계정에 직접 연결할 수 있도록 Azure Blob Storage 계정에 SFTP 지원을 사용하도록 설정하는 방법을 알아봅니다.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 11/15/2021
ms.author: normesta
ms.reviewer: ylunagaria
ms.openlocfilehash: 9a5e70bbb653faea4bcea6d64d62b125ae8cd54e
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132751292"
---
# <a name="connect-to-azure-blob-storage-by-using-the-secure-file-transfer-sftp-protocol-preview"></a>SFTP(보안 파일 전송) 프로토콜(미리 보기)을 사용하여 Azure Blob Storage 커넥트

SFTP 클라이언트를 사용하여 Azure Storage 계정의 Blob Storage 엔드포인트에 안전하게 연결한 다음 파일을 업로드하고 다운로드할 수 있습니다. 이 문서에서는 SFTP 프로토콜 지원을 사용하도록 설정한 다음 SFTP 클라이언트를 사용하여 Blob Storage 연결하는 방법을 보여 줍니다. 

Azure Blob Storage SFTP 프로토콜 지원에 대한 자세한 내용은 Azure Blob Storage [SFTP(보안 파일 전송) 프로토콜 지원을](secure-file-transfer-protocol-support.md)참조하세요.

> [!IMPORTANT]
> SFTP 프로토콜 지원은 현재 미리 보기로 제공되며 이러한 지역에서 사용할 수 [있습니다.](secure-file-transfer-protocol-support.md#regional-availability)
>
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>
> 미리 보기에 등록하려면 [이 양식](https://forms.office.com/r/gZguN0j65Y)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- 표준 범용 v2 또는 프리미엄 블록 Blob Storage 계정. 계정을 만들 때 SFTP를 사용하도록 설정할 수도 있습니다. 이러한 스토리지 계정 유형에 대한 자세한 내용은 [스토리지 계정 개요](../common/storage-account-overview.md)를 참조하세요.

- 스토리지 계정의 계정 중복 옵션은 LRS(로컬 중복 스토리지) 또는 ZRS(영역 중복 스토리지)로 설정됩니다.

- 계정의 계층 구조 네임스페이스 기능을 사용하도록 설정해야 합니다. 계층 구조 네임스페이스 기능을 사용하도록 설정하려면 Azure Data Lake Storage Gen2 기능을 사용하여 [Azure Blob Storage 업그레이드를 참조하세요.](upgrade-to-data-lake-storage-gen2-how-to.md)

- 온-프레미스 네트워크에서 연결하는 경우 클라이언트가 포트 22를 통해 나가는 통신을 허용하는지 확인합니다. SFTP 프로토콜은 해당 포트를 사용합니다.

## <a name="register-the-feature"></a>기능 등록

SFTP 지원을 사용하도록 설정하려면 먼저 구독에 SFTP 기능을 등록해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 구독의 구성 페이지를 엽니다.

3. **설정** 아래에서 **미리 보기 기능을** 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![미리 보기 설정](./media/secure-file-transfer-protocol-support-how-to/preview-features-setting.png)

4. 미리 **보기 기능** 페이지에서 **AllowSFTP** 기능을 선택한 다음, **등록을** 선택합니다.

### <a name="verify-feature-registration"></a>기능 등록 확인

이 문서의 다른 단계를 계속하기 전에 기능이 등록되었는지 확인합니다. 

1. 구독의 **미리 보기 기능** 페이지를 엽니다. 

2. **AllowSFTP** 기능을 **찾아서 상태** 열에 **등록이** 표시되는지 확인합니다.

## <a name="enable-sftp-support"></a>SFTP 지원 사용

1. [Azure Portal](https://portal.azure.com/)에서 스토리지 계정으로 이동합니다.

2. **설정** 아래에서 **SFTP** 를 선택합니다.

   > [!NOTE]
   > 이 옵션은 계정의 계층 구조 네임스페이스 기능을 사용하도록 설정한 경우에만 나타납니다. 계층 구조 네임스페이스 기능을 사용하도록 설정하려면 Azure Data Lake Storage Gen2 기능을 사용하여 [Azure Blob Storage 업그레이드를 참조하세요.](upgrade-to-data-lake-storage-gen2-how-to.md)

3. **SFTP 사용을** 선택합니다. 

   > [!div class="mx-imgBorder"]
   > ![SFTP 사용 단추](./media/secure-file-transfer-protocol-support-how-to/sftp-enable-option.png)

   >[!NOTE]
   > SFTP 구성 페이지에 로컬 사용자가 표시되지 않으면 그 중 하나 이상을 추가해야 합니다. 로컬 사용자를 추가하려면 다음 섹션을 참조하세요.

## <a name="configure-permissions"></a>권한 구성

Azure Storage SFTP 엔드포인트에 액세스하기 위한 SAS(공유 액세스 서명) 또는 Azure AD(Azure Active Directory) 인증을 지원하지 않습니다. 대신 Azure 생성 암호 또는 SSH(보안 셸) 키 쌍으로 보호할 수 있는 로컬 사용자라는 ID를 사용해야 합니다. 연결하는 클라이언트에 대한 액세스 권한을 부여하려면 스토리지 계정에 암호 또는 키 쌍과 연결된 ID가 있어야 합니다. 이 ID를 *로컬 사용자라고* 부립니다. 

이 섹션에서는 로컬 사용자를 만들고, 인증 방법을 선택한 다음, 해당 로컬 사용자에 대한 권한을 할당하는 방법을 알아봅니다. 

SFTP 권한 모델에 대한 자세한 내용은 [SFTP 사용 권한 모델 을 참조하세요.](secure-file-transfer-protocol-support.md#sftp-permissions-model) 

1. [Azure Portal](https://portal.azure.com/)에서 스토리지 계정으로 이동합니다.

2. **설정** 아래에서 **SFTP** 를 선택한 **다음, 로컬 사용자 추가를** 선택합니다. 

   > [!div class="mx-imgBorder"]
   > ![로컬 사용자 추가 단추](./media/secure-file-transfer-protocol-support-how-to/sftp-local-user.png)

3. 로컬 **사용자** 구성 추가 창에서 사용자의 이름을 추가한 다음, 이 로컬 사용자와 연결할 인증 방법을 선택합니다. 암호 및/또는 SSH 키를 연결할 수 있습니다. 

   > [!IMPORTANT]
   > 두 가지 형태의 인증을 모두 사용하도록 설정할 수 있지만 SFTP 클라이언트는 둘 중 하나만 사용하여 연결할 수 있습니다. 다단계 인증은 성공적인 인증을 위해 유효한 암호와 유효한 퍼블릭 및 프라이빗 키 쌍이 모두 필요하지 않습니다.

   **암호로 보안을** 선택하면 **로컬 사용자** 구성 추가 창의 모든 단계를 완료하면 암호가 표시됩니다.

   **SSH 공개 키를** 사용하여 보안을 선택한 경우 키 원본 추가를 선택하여 키 **원본을** 지정합니다. 

   > [!div class="mx-imgBorder"]
   > ![로컬 사용자 구성 창](./media/secure-file-transfer-protocol-support-how-to/add-local-user-config-page.png)

   다음 표에서는 각 키 원본 옵션에 대해 설명합니다.

   | 옵션 | 지침 |
   |----|----|
   | 새 키 쌍 생성 | 새 퍼블릭/프라이빗 키 쌍을 만들려면 이 옵션을 사용합니다. 공개 키는 제공한 키 이름으로 Azure에 저장됩니다. 로컬 사용자가 성공적으로 추가된 후에 프라이빗 키를 다운로드할 수 있습니다. |
   | Azure에 저장된 기존 키 사용 | Azure에 이미 저장된 공개 키를 사용하려면 이 옵션을 사용합니다. Azure에서 기존 키를 찾으려면 [키 나열을 참조하세요.](../../virtual-machines/ssh-keys-portal.md#list-keys) SFTP 클라이언트가 Azure Blob Storage 연결할 때 해당 클라이언트는 이 공개 키와 연결된 프라이빗 키를 제공해야 합니다. |
   | 기존 퍼블릭 키 사용 | Azure 외부에 저장된 공개 키를 업로드하려면 이 옵션을 사용합니다. 공개 키가 없지만 Azure 외부에서 생성하려는 경우 [ssh-keygen을 사용하여 키 생성을](../../virtual-machines/linux/create-ssh-keys-detailed.md#generate-keys-with-ssh-keygen)참조하세요. |

4. **다음을** 선택하여 구성 창의 **컨테이너 권한** 탭을 엽니다.

5. 컨테이너 **권한** 탭에서 이 로컬 사용자가 사용할 수 있도록 하려는 컨테이너를 선택합니다. 그런 다음 이 로컬 사용자가 수행할 작업 유형을 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![컨테이너 권한 탭](./media/secure-file-transfer-protocol-support-how-to/container-perm-tab.png)

6. 홈 **디렉터리** 편집 상자에 이 로컬 사용자와 연결된 기본 위치가 될 컨테이너 또는 디렉터리 경로(컨테이너 이름 포함)의 이름을 입력합니다. 

   홈 디렉터리에 대한 자세한 내용은 홈 디렉터리 를 [참조하세요.](secure-file-transfer-protocol-support.md#home-directory)

7. 추가 **단추를** 선택하여 로컬 사용자를 추가합니다.

   암호 인증을 사용하도록 설정한 경우 로컬 사용자가 추가된 후 Azure에서 생성된 암호가 대화 상자에 나타납니다. 

   > [!IMPORTANT]
   > 나중에 이 암호를 검색할 수 없으므로 암호를 복사한 다음 찾을 수 있는 위치에 저장해야 합니다.

   새 키 쌍을 생성하도록 선택한 경우 로컬 사용자가 추가된 후 해당 키 쌍의 프라이빗 키를 다운로드하라는 메시지가 표시됩니다.

## <a name="connect-an-sftp-client"></a>SFTP 클라이언트 커넥트

SFTP 클라이언트를 사용하여 안전하게 연결한 다음 파일을 전송할 수 있습니다. 다음 스크린샷은 [SSH 열기](/windows-server/administration/openssh/openssh_overview) 및 암호 인증을 사용하여 연결한 다음 라는 파일을 업로드하는 Windows PowerShell 세션을 `logfile.txt` 보여줍니다.  

> [!div class="mx-imgBorder"]
> ![Open SSH를 커넥트](./media/secure-file-transfer-protocol-support-how-to/ssh-connect-and-transfer.png)

> [!NOTE]
> 호스트 키를 신뢰하라는 메시지가 표시될 수 있습니다. 공개 미리 보기 동안 유효한 호스트 키는 [여기에](secure-file-transfer-protocol-host-keys.md)게시됩니다.  

전송이 완료되면 Azure Portal 파일을 보고 관리할 수 있습니다. 

> [!div class="mx-imgBorder"]
> ![업로드된 파일이 스토리지 계정에 표시](./media/secure-file-transfer-protocol-support-how-to/uploaded-file-in-storage-account.png)

> [!NOTE]
> Azure Portal Blob REST API 및 Data Lake Storage Gen2 REST API 사용합니다. Azure Portal 업로드된 파일과 상호 작용할 수 있다는 것은 SFTP와 REST 간의 상호 운용성을 보여줍니다.

파일을 연결하고 전송하는 방법에 대한 지침은 SFTP 클라이언트의 설명서를 참조하세요.

## <a name="see-also"></a>추가 정보

- [Azure Blob Storage SFTP(SSH 파일 전송 프로토콜) 지원](secure-file-transfer-protocol-support.md)
- [Azure Blob Storage SFTP(SSH 파일 전송 프로토콜) 지원과 관련된 알려진 문제](secure-file-transfer-protocol-known-issues.md)
