---
title: Azure Blob Storage(미리 보기) | SFTP의 알려진 문제 Microsoft Docs
description: Azure Blob Storage SFTP(SSH 파일 전송 프로토콜) 지원의 제한 사항 및 알려진 문제에 대해 알아봅니다.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 11/22/2021
ms.author: normesta
ms.reviewer: ylunagaria
ms.openlocfilehash: 337b4e8a2946c2a9025411dbb842aaf21e568a51
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132940054"
---
# <a name="known-issues-with-ssh-file-transfer-protocol-sftp-support-for-azure-blob-storage-preview"></a>Azure Blob Storage(미리 보기)에 대한 SFTP(SSH 파일 전송 프로토콜) 지원과 관련된 알려진 문제

이 문서에서는 Azure Blob Storage SFTP 지원의 제한 사항 및 알려진 문제에 대해 설명합니다.

> [!IMPORTANT]
> SFTP 지원은 현재 미리 보기로 제공되며 이러한 지역에서 사용할 수 [있습니다.](secure-file-transfer-protocol-support.md#regional-availability)
> 
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>
> 미리 보기에 등록하려면 [이 양식](https://forms.office.com/r/gZguN0j65Y)을 참조하세요.

## <a name="data-redundancy-options"></a>데이터 중복 옵션

- GRS(지역 중복 스토리지) 및 GZRS(지역 영역 중복 스토리지)는 SFTP를 사용하도록 설정된 계정에서 지원되지 않습니다.

## <a name="authorization"></a>권한 부여

- 로컬 사용자는 현재 SFTP 엔드포인트에 대해 지원되는 유일한 형태의 ID 관리입니다.

- SFTP 엔드포인트에는 Azure Active Directory(Azure AD), SAS(공유 액세스 서명) 및 계정 키 권한 부여가 지원되지 않습니다.

- SFTP 엔드포인트에는 POSIX와 같은 ACL(액세스 제어 목록)이 지원되지 않습니다.

  > [!NOTE]
  > 데이터가 Azure Storage 수집되면 전체 Azure Storage 보안 설정을 사용할 수 있습니다. RBAC(역할 기반 액세스 제어) 및 액세스 제어 목록과 같은 권한 부여 메커니즘은 연결하는 SFTP 클라이언트에 권한을 부여하는 수단으로 지원되지 않지만 Azure SDKS 및 Azure REST API뿐만 아니라 Azure 도구(예: Azure Portal, Azure CLI, Azure PowerShell 명령 및 AzCopy)를 통해 액세스 권한을 부여하는 데 사용할 수 있습니다. 

- 컨테이너 나열, 배치/얻기, 컨테이너 만들기/삭제와 같은 계정 수준 작업은 지원되지 않습니다.
 
## <a name="networking"></a>네트워킹

- 분할된 DNS 엔드포인트는 지원되지 않습니다.

- SFTP를 사용하여 스토리지 계정에 액세스하려면 네트워크에서 포트 22의 트래픽을 허용해야 합니다.

- 방화벽이 구성되면 허용되지 않는 IP의 연결이 예상대로 거부되지 않습니다. 그러나 인증된 사용자에 대한 연결이 성공하면 모든 데이터 평면 작업이 거부됩니다.

## <a name="supported-algorithms"></a>지원되는 알고리즘

| 호스트 키 | 키 교환 | 암호화/암호화 | 무결성/MAC | 공개 키 |
|----------|--------------|--------------------|---------------|------------|
| rsa-sha2-256 | ecdh-sha2-nistp384 | aes128-gcm@openssh.com | hmac-sha2-256 | ssh-rsa |
| rsa-sha2-512 | ecdh-sha2-nistp256 | aes256-gcm@openssh.com | hmac-sha2-512 | ecdsa-sha2-nistp256 |
| ecdsa-sha2-nistp256 | diffie-hellman-group14-sha256 | aes128-cbc| | ecdsa-sha2-nistp384 |
| ecdsa-sha2-nistp384| diffie-hellman-group16-sha512 | aes256-cbc |  | 
||| aes192-cbc ||

## <a name="security"></a>보안

- 호스트 키는 [여기에](secure-file-transfer-protocol-host-keys.md)게시됩니다. 공개 미리 보기 동안 호스트 키는 한 달에 한 번까지 회전합니다.

- "원격 호스트 식별이 변경되었습니다." 경고에 대한 몇 가지 다른 이유가 있습니다.

  - 원격 호스트 키가 업데이트되었습니다(호스트 키는 주기적으로 순환됨).
  
  - 클라이언트가 로컬 ssh "known_hosts" 파일에 저장된 알고리즘과 다른 호스트 키 알고리즘을 선택했습니다. OpenSSH는 알고리즘이 반드시 일치하지 않는 경우에도 호스트(account.blob.core.windows.net)가 일치하는 경우 이미 신뢰할 수 있는 키를 사용합니다.
  
  - 스토리지 계정이 다른 지역으로 장애 조치되었습니다.
  
  - 원격 호스트(account.blob.core.windows.net)가 가짜입니다.

## <a name="integrations"></a>통합

- 변경 피드는 지원되지 않습니다.

- 트랜잭션 및 용량과 같은 계정 메트릭을 사용할 수 있습니다. 작업별로 로그를 필터링하여 SFTP 작업을 확인합니다.

- NFS(네트워크 파일 시스템) 3.0 및 SFTP는 동일한 스토리지 계정에서 사용하도록 설정할 수 없습니다.

## <a name="performance"></a>성능

- 일부 클라이언트에 대한 기본 설정을 사용하여 업로드 성능이 느려질 수 있습니다. 이 중 일부는 SFTP가 수다스러운 프로토콜이며 작은 메시지 요청을 보내기 때문에 예상됩니다. 버퍼 크기를 늘리고 여러 동시 연결을 사용하면 속도가 크게 향상될 수 있습니다. 

  - WinSCP의 경우 최대 9개의 동시 연결을 사용하여 여러 파일을 업로드할 수 있습니다. 

  - Windows OpenSSH의 경우 버퍼 크기를 100000으로 늘릴 수 있습니다. sftp -B 100000testaccount.user1@testaccount.blob.core.windows.net 

  - Linux에서 OpenSSH의 경우 버퍼 크기를 262000으로 늘릴 수 있습니다. sftp -B 262000 -R 32 testaccount.user1@testaccount.blob.core.windows.net 

- 유휴 또는 비활성 연결에는 4분의 시간 초과가 있습니다. OpenSSH는 응답을 중지한 다음 연결을 끊는 것처럼 보입니다. 일부 클라이언트는 자동으로 다시 연결됩니다. 

- 최대 파일 크기 업로드는 클라이언트 메시지 크기에 따라 제한됩니다. 아래의 몇 가지 예는 다음과 같습니다. 

  - 32k 메시지(OpenSSH 기본값) * 50k 블록 = 1.52GB 

  - 100k 메시지(OpenSSH Windows max) * 50k 블록 = 4.77GB 

  - 256k 메시지(OpenSSH Linux 최대) * 50k 블록 = 12.20GB 

## <a name="other"></a>기타

- 기호 링크는 지원되지 않습니다.

- PowerShell 및 Azure CLI 지원되지 않습니다. 공개 미리 보기에 포털 및 ARM 템플릿을 활용할 수 있습니다.

## <a name="see-also"></a>참조

- [Azure Blob Storage SFTP(SSH 파일 전송) 프로토콜 지원](secure-file-transfer-protocol-support.md)
