---
title: SFTP 프로토콜 (미리 보기)을 사용 하 여 Azure Blob Storage에 커넥트 | Microsoft Docs
description: sftp 클라이언트를 사용 하 여 Azure Storage 계정에 직접 연결할 수 있도록 Azure Blob Storage 계정에 대해 sftp 지원을 사용 하도록 설정 하는 방법을 알아봅니다.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 11/15/2021
ms.author: normesta
ms.reviewer: ylunagaria
ms.openlocfilehash: b5249fa06dad80cb675723f724b12cf638c452fe
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556917"
---
# <a name="connect-to-azure-blob-storage-by-using-the-secure-file-transfer-sftp-protocol-preview"></a>SFTP (Secure File Transfer) 프로토콜 (미리 보기)을 사용 하 여 Azure Blob Storage 커넥트

SFTP 클라이언트를 사용 하 여 Azure Storage 계정의 Blob Storage 끝점에 안전 하 게 연결 하 고 파일을 업로드 및 다운로드할 수 있습니다. 이 문서에서는 sftp 프로토콜 지원을 사용 하도록 설정한 다음 sftp 클라이언트를 사용 하 여 Blob Storage에 연결 하는 방법을 보여 줍니다. 

Azure Blob Storage에서 sftp 프로토콜 지원에 대 한 자세한 내용은 [Azure Blob Storage의 sftp (Secure File Transfer) 프로토콜 지원](secure-file-transfer-protocol-support.md)을 참조 하세요.

> [!IMPORTANT]
> SFTP 프로토콜 지원은 현재 미리 보기 상태 이며 [이러한 지역](secure-file-transfer-protocol-support.md#regional-availability)에서 사용할 수 있습니다.
>
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>
> 미리 보기에 등록하려면 [이 양식](https://forms.office.com/r/gZguN0j65Y)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

- 표준 범용 v2 또는 premium 블록 blob 저장소 계정입니다. 이러한 스토리지 계정 유형에 대한 자세한 내용은 [스토리지 계정 개요](../common/storage-account-overview.md)를 참조하세요.

- 저장소 계정의 계정 중복성 옵션은 LRS (로컬 중복 저장소) 또는 ZRS (영역 중복 저장소)로 설정 됩니다.

- 계정의 계층 구조 네임 스페이스 기능을 사용 하도록 설정 해야 합니다. 계층 구조 네임 스페이스 기능을 사용 하도록 설정 하려면 [Azure Blob Storage Azure Data Lake Storage Gen2 기능으로 업그레이드](upgrade-to-data-lake-storage-gen2-how-to.md)를 참조 하세요.

- 온-프레미스 네트워크에서 연결 하는 경우 클라이언트에서 포트 22를 통한 나가는 통신을 허용 하는지 확인 합니다. SFTP 프로토콜은 해당 포트를 사용 합니다.

## <a name="register-the-feature"></a>기능 등록

SFTP 지원을 사용 하려면 먼저 구독에 SFTP 기능을 등록 해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 구독의 구성 페이지를 엽니다.

3. **설정** 아래에서 **미리 보기 기능** 을 선택 합니다.

   > [!div class="mx-imgBorder"]
   > ![미리 보기 설정](./media/secure-file-transfer-protocol-support-how-to/preview-features-setting.png)

4. **미리 보기 기능** 페이지에서 **allowsftp** 기능을 선택 하 고 **등록** 을 선택 합니다.

### <a name="verify-feature-registration"></a>기능 등록 확인

이 문서의 다른 단계를 계속 하기 전에 기능이 등록 되어 있는지 확인 합니다. 

1. 구독의 **미리 보기 기능** 페이지를 엽니다. 

2. **Allowsftp** 기능을 찾아 **등록** 된이 **상태** 열에 표시 되는지 확인 합니다.

## <a name="enable-sftp-support"></a>SFTP 지원 사용

1. [Azure Portal](https://portal.azure.com/)에서 스토리지 계정으로 이동합니다.

2. **설정** 에서 **SFTP** 를 선택 합니다.

   > [!NOTE]
   > 이 옵션은 계정의 계층 구조 네임 스페이스 기능을 사용 하도록 설정한 경우에만 표시 됩니다. 계층 구조 네임 스페이스 기능을 사용 하도록 설정 하려면 [Azure Blob Storage Azure Data Lake Storage Gen2 기능으로 업그레이드](upgrade-to-data-lake-storage-gen2-how-to.md)를 참조 하세요.

3. **SFTP 사용** 을 선택 합니다. 

   > [!div class="mx-imgBorder"]
   > ![SFTP 사용 단추](./media/secure-file-transfer-protocol-support-how-to/sftp-enable-option.png)

   >[!NOTE]
   > SFTP 구성 페이지에 로컬 사용자가 표시 되지 않으면 해당 사용자를 하나 이상 추가 해야 합니다. 로컬 사용자를 추가 하려면 다음 섹션을 참조 하세요.

## <a name="configure-permissions"></a>권한 구성

Azure Storage는 SFTP 끝점에 액세스 하는 데 사용할 수 있는 SAS (공유 액세스 서명) 또는 azure AD (azure Active directory) 인증을 지원 하지 않습니다. 대신, Azure 생성 암호나 SSH (보안 셸) 키 쌍을 사용 하 여 보안을 설정할 수 있는 로컬 사용자 라는 id를 사용 해야 합니다. 연결 하는 클라이언트에 대 한 액세스 권한을 부여 하려면 저장소 계정에 암호 또는 키 쌍과 연결 된 id가 있어야 합니다. 해당 id를 *로컬 사용자* 라고 합니다. 

이 섹션에서는 로컬 사용자를 만들고 인증 방법을 선택한 다음 해당 로컬 사용자에 대 한 사용 권한을 할당 하는 방법에 대해 알아봅니다. 

SFTP 권한 모델에 대해 자세히 알아보려면 [Sftp 권한 모델](secure-file-transfer-protocol-support.md#sftp-permissions-model)을 참조 하세요. 

1. [Azure Portal](https://portal.azure.com/)에서 스토리지 계정으로 이동합니다.

2. **설정** 에서 **SFTP** 를 선택 하 고 **로컬 사용자 추가** 를 선택 합니다. 

   > [!div class="mx-imgBorder"]
   > ![로컬 사용자 추가 단추](./media/secure-file-transfer-protocol-support-how-to/sftp-local-user.png)

3. **로컬 사용자 구성 추가** 창에서 사용자의 이름을 추가 하 고이 로컬 사용자와 연결할 인증 방법을 선택 합니다. 암호 및/또는 SSH 키를 연결할 수 있습니다. 

   > [!IMPORTANT]
   > 두 가지 형태의 인증을 모두 사용 하도록 설정할 수 있지만 SFTP 클라이언트는 둘 중 하나만 사용 하 여 연결할 수 있습니다. 성공적인 인증을 위해 유효한 암호와 유효한 공개 및 개인 키 쌍이 모두 필요한 다단계 인증을 지원 하지 않습니다.

   **암호를 사용 하 여 보안** 을 선택 하는 경우 **로컬 사용자 구성 추가** 창의 모든 단계를 완료 하면 암호가 표시 됩니다.

   **SSH 공개 키를 사용 하 여 보안** 을 선택 하는 경우 키 **원본 추가** 를 선택 하 여 키 원본을 지정 합니다. 

   > [!div class="mx-imgBorder"]
   > ![로컬 사용자 구성 창](./media/secure-file-transfer-protocol-support-how-to/add-local-user-config-page.png)

   다음 표에서는 각 키 원본 옵션을 설명 합니다.

   | 옵션 | 지침 |
   |----|----|
   | 새 키 쌍 생성 | 새 공개/개인 키 쌍을 만들려면이 옵션을 사용 합니다. 공개 키는 사용자가 제공 하는 키 이름을 사용 하 여 Azure에 저장 됩니다. 로컬 사용자가 성공적으로 추가 된 후 개인 키를 다운로드할 수 있습니다. |
   | Azure에 저장 된 기존 키 사용 | Azure에 이미 저장 되어 있는 공개 키를 사용 하려면이 옵션을 사용 합니다. Azure에서 기존 키를 찾으려면 [키 나열](/azure/virtual-machines/ssh-keys-portal#list-keys)을 참조 하세요. SFTP 클라이언트가 Azure Blob Storage에 연결할 때 해당 클라이언트는이 공개 키와 연결 된 개인 키를 제공 해야 합니다. |
   | 기존 퍼블릭 키 사용 | Azure 외부에 저장 된 공개 키를 업로드 하려는 경우이 옵션을 사용 합니다. 공개 키가 없지만 Azure 외부에서 하나를 생성 하려는 경우 [ssh-ssh-keygen로 키 생성](/azure/virtual-machines/linux/create-ssh-keys-detailed#generate-keys-with-ssh-keygen)을 참조 하세요. |

4. **다음** 을 선택 하 여 구성 창의 **컨테이너 권한** 탭을 엽니다.

5. **컨테이너 권한** 탭에서이 로컬 사용자가 사용할 수 있도록 설정할 컨테이너를 선택 합니다. 그런 다음이 로컬 사용자가 수행 하도록 설정할 작업 유형을 선택 합니다.

   > [!div class="mx-imgBorder"]
   > ![컨테이너 권한 탭](./media/secure-file-transfer-protocol-support-how-to/container-perm-tab.png)

6. **홈 디렉터리** 편집 상자에이 로컬 사용자와 연결 된 기본 위치로 사용할 컨테이너 이름 또는 컨테이너 이름을 포함 하는 디렉터리 경로를 입력 합니다. 

   홈 디렉터리에 대 한 자세한 내용은 [홈 디렉터리](secure-file-transfer-protocol-support.md#home-directory)를 참조 하십시오.

7. **추가 단추** 를 선택 하 여 로컬 사용자를 추가 합니다.

   암호 인증을 사용 하도록 설정한 경우 로컬 사용자가 추가 된 후에 Azure에서 생성 된 암호가 대화 상자에 나타납니다. 

   > [!IMPORTANT]
   > 나중에이 암호를 검색할 수 없으므로 암호를 복사한 다음 찾을 수 있는 위치에 저장 해야 합니다.

   새 키 쌍을 생성 하도록 선택한 경우 로컬 사용자가 추가 된 후 해당 키 쌍의 개인 키를 다운로드 하 라는 메시지가 표시 됩니다.

## <a name="connect-an-sftp-client"></a>SFTP 클라이언트 커넥트

모든 SFTP 클라이언트를 사용 하 여 안전 하 게 연결 하 고 파일을 전송할 수 있습니다. 다음 스크린샷은 [오픈 SSH](/windows-server/administration/openssh/openssh_overview) 및 암호 인증을 사용 하 여 연결 하 고 라는 파일을 업로드 하는 Windows PowerShell 세션을 보여 줍니다 `logfile.txt` .  

> [!div class="mx-imgBorder"]
> ![SSH를 사용 하 여 커넥트](./media/secure-file-transfer-protocol-support-how-to/ssh-connect-and-transfer.png)

> [!NOTE]
> 호스트 키를 신뢰 하 라는 메시지가 표시 될 수 있습니다. 공개 미리 보기 중에는 유효한 호스트 키가 [여기](secure-file-transfer-protocol-host-keys.md)에 게시 됩니다.  

전송이 완료 되 면 Azure Portal에서 파일을 보고 관리할 수 있습니다. 

> [!div class="mx-imgBorder"]
> ![업로드 된 파일이 저장소 계정에 표시 됨](./media/secure-file-transfer-protocol-support-how-to/uploaded-file-in-storage-account.png)

> [!NOTE]
> Azure Portal는 Blob REST API 및 Data Lake Storage Gen2 REST API를 사용 합니다. Azure Portal에서 업로드 된 파일과 상호 작용할 수 있으면 SFTP와 REST 간의 상호 운용성을 보여 줍니다.

파일을 연결 하 고 전송 하는 방법에 대 한 지침은 SFTP 클라이언트 설명서를 참조 하세요.

## <a name="see-also"></a>추가 정보

- [Azure Blob Storage의 SFTP (Secure File Transfer) 프로토콜 지원](secure-file-transfer-protocol-support.md)
- [Azure Blob Storage의 SFTP (Secure File Transfer) 프로토콜 지원에 대 한 알려진 문제](secure-file-transfer-protocol-known-issues.md)
