---
title: Azure Blob Storage의 SFTP 관련 알려진 문제 (미리 보기) | Microsoft Docs
description: Azure Blob Storage에서 파일 전송 프로토콜 SFTP (SSH) 지원의 제한 사항 및 알려진 문제에 대해 알아봅니다.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 11/22/2021
ms.author: normesta
ms.reviewer: ylunagaria
ms.openlocfilehash: 5da11038e812bca6fe53114f8bc2edb12fcdbc05
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133271330"
---
# <a name="known-issues-with-ssh-file-transfer-protocol-sftp-support-for-azure-blob-storage-preview"></a>Azure Blob Storage (미리 보기)에 대 한 SFTP (SSH 파일 전송 프로토콜) 지원의 알려진 문제

이 문서에서는 Azure Blob Storage에서 SFTP 지원의 제한 사항 및 알려진 문제에 대해 설명 합니다.

> [!IMPORTANT]
> SFTP 지원은 현재 미리 보기로 제공 되며 [이러한 지역](secure-file-transfer-protocol-support.md#regional-availability)에서 사용할 수 있습니다.
> 
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>
> 미리 보기에 등록 하려면 [이 양식을](https://forms.office.com/r/gZguN0j65Y) 작성 하 고 Azure Portal의 ' 미리 보기 기능 '을 통해 참여 하도록 요청 하세요.

## <a name="data-redundancy-options"></a>데이터 중복성 옵션

- GRS (지역 중복 저장소) 및 GZRS (지역 중복 저장소)는 SFTP를 사용 하는 계정에서 지원 되지 않습니다.

## <a name="authorization"></a>권한 부여

- 로컬 사용자는 현재 SFTP 끝점에 대해 지원 되는 id 관리의 유일한 형태입니다.

- Azure AD (Azure Active Directory), SAS (공유 액세스 서명) 및 계정 키 인증은 SFTP 끝점에 대해 지원 되지 않습니다.

- POSIX와 유사한 Acl (access control 목록)은 SFTP 끝점에 대해 지원 되지 않습니다.

  > [!NOTE]
  > 데이터가 Azure Storage 수집 되 면 Azure Storage 보안 설정의 전체 범위를 사용할 수 있습니다. RBAC (역할 기반 액세스 제어) 및 액세스 제어 목록과 같은 권한 부여 메커니즘은 연결 된 SFTP 클라이언트를 인증 하는 수단으로 지원 되지 않지만, azure 도구 (Azure Portal, Azure CLI, Azure PowerShell 명령 및 AzCopy) 뿐만 아니라 azure sdk 및 azure REST api를 통해 액세스 권한을 부여 하는 데 사용할 수 있습니다. 

- 컨테이너 나열/가져오기, 만들기/삭제와 같은 계정 수준 작업은 지원 되지 않습니다.
 
## <a name="networking"></a>네트워킹

- 분할 된 DNS 끝점은 지원 되지 않습니다.

- SFTP를 사용 하 여 저장소 계정에 액세스 하려면 네트워크가 22 포트에서 트래픽을 허용 해야 합니다.

- 방화벽이 구성 된 경우 허용 되지 않는 Ip의 연결이 예상 대로 거부 되지 않습니다. 그러나 인증 된 사용자에 대 한 연결이 성공 하면 모든 데이터 평면 작업이 거부 됩니다.

## <a name="supported-algorithms"></a>지원되는 알고리즘

| 호스트 키 | 키 교환 | 암호화/암호화 | 무결성/MAC | 공개 키 |
|----------|--------------|--------------------|---------------|------------|
| rsa-sha2-256 | ecdh-sha2-nistp384 | aes128-gcm@openssh.com | hmac-sha2-256 | ssh-rsa |
| rsa-sha2-512 | ecdh-sha2-nistp256 | aes256-gcm@openssh.com | hmac-sha2-512 | ecdsa-sha2-nistp256 |
| ecdsa-sha2-nistp256 | diffie-hellman-group14-sha256 | aes128-cbc| | ecdsa-sha2-nistp384 |
| ecdsa-sha2-nistp384| diffie-hellman-group16-sha512 | aes256-cbc |  | 
||| aes192-cbc ||

## <a name="security"></a>보안

- [여기](secure-file-transfer-protocol-host-keys.md)에 호스트 키가 게시 됩니다. 공개 미리 보기 중에 호스트 키는 매달 한 번까지 회전 합니다.

- "원격 호스트 id가 변경 되었습니다." 경고에 대 한 몇 가지 다른 이유가 있습니다.

  - 원격 호스트 키가 업데이트 되었습니다 (호스트 키는 정기적으로 순환 됨).
  
  - 클라이언트에서 로컬 ssh "known_hosts" 파일에 저장 된 것과 다른 호스트 키 알고리즘을 선택 했습니다. OpenSSH는 알고리즘이 일치 하지 않을 경우에도 호스트 (account.blob.core.windows.net)가 일치 하는 경우 이미 신뢰할 수 있는 키를 사용 합니다.
  
  - 저장소 계정이 다른 지역으로 장애 조치 (failover) 되었습니다.
  
  - 원격 호스트 (account.blob.core.windows.net)를 가짜 하 고 있습니다.

## <a name="integrations"></a>통합

- 변경 피드가 지원 되지 않습니다.

- 트랜잭션 및 용량과 같은 계정 메트릭을 사용할 수 있습니다. 작업 별로 로그를 필터링 하 여 SFTP 활동을 확인 합니다.

- 동일한 저장소 계정에서 NFS (네트워크 파일 시스템) 3.0 및 SFTP를 사용 하도록 설정할 수 없습니다.

## <a name="performance"></a>성능

- 일부 클라이언트의 기본 설정을 사용 하는 업로드 성능은 느릴 수 있습니다. SFTP가 번잡 프로토콜 이며 작은 메시지 요청을 보내기 때문에이 중 일부가 필요 합니다. 버퍼 크기를 늘리고 여러 동시 연결을 사용 하면 속도를 크게 향상 시킬 수 있습니다. 

  - WinSCP의 경우 최대 9 개의 동시 연결을 사용 하 여 여러 파일을 업로드할 수 있습니다. 

  - Windows OpenSSH의 경우 버퍼 크기를 10만: sftp-B 10만로 늘릴 수 있습니다.testaccount.user1@testaccount.blob.core.windows.net 

  - Linux에서 OpenSSH의 경우 버퍼 크기를 262000으로 늘릴 수 있습니다 32 262000. testaccount.user1@testaccount.blob.core.windows.net 

- 유휴 또는 비활성 연결에 대 한 시간 제한은 4 분입니다. OpenSSH 응답을 중지 한 다음 연결을 해제 하는 것으로 나타납니다. 일부 클라이언트는 자동으로 다시 연결 됩니다. 

- 최대 파일 크기 업로드는 클라이언트 메시지 크기로 제한 됩니다. 아래 몇 가지 예가 있습니다. 

  - 32KB message (OpenSSH default) * 50k 블록 = 1.52 GB 

  - 100KB message (OpenSSH Windows max) * 50k 블록 = 4.77 gb 

  - 256KB 메시지 (OpenSSH Linux max) * 50k 블록 = 12.20 GB 

## <a name="other"></a>기타

- 기호 링크는 지원되지 않습니다.

- PowerShell 및 Azure CLI 지원 되지 않습니다. 공개 미리 보기용 포털 및 ARM 템플릿을 활용할 수 있습니다.

## <a name="see-also"></a>참고 항목

- [Azure Blob Storage에서 파일 전송 프로토콜 SFTP (SSH) 지원](secure-file-transfer-protocol-support.md)
