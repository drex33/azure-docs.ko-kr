---
title: Azure Blob Storage NFS 3.0의 알려진 문제
description: Azure Blob Storage에서 NFS(네트워크 파일 시스템) 3.0 프로토콜 지원의 제한 사항과 알려진 문제에 관해 알아봅니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 09/08/2021
ms.author: normesta
ms.reviewer: yzheng
ms.openlocfilehash: 6e5949f22000c930f101ae36042587d97a5293a0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664868"
---
# <a name="known-issues-with-network-file-system-nfs-30-protocol-support-in-azure-blob-storage"></a>Azure Blob Storage의 NFS(네트워크 파일 시스템) 3.0 프로토콜 지원의 알려진 문제

이 문서에서는 Azure Blob Storage에서 NFS(네트워크 파일 시스템) 3.0 프로토콜 지원의 제한 사항과 알려진 문제를 설명합니다.

> [!IMPORTANT]
> NFS 3.0을 사용하려면 계정의 계층 구조 네임스페이스 기능을 사용하도록 설정해야 하므로 [Azure Data Lake Storage Gen2의 알려진 문제](data-lake-storage-known-issues.md) 문서에 설명된 알려진 모든 문제도 계정에 적용됩니다.

## <a name="nfs-30-support"></a>NFS 3.0 지원

- NFS 3.0 지원은 기존 스토리지 계정에서 사용하도록 설정할 수 없습니다.

- NFS 3.0 지원을 사용하도록 설정한 후에는 스토리지 계정에서 사용하지 않도록 설정할 수 없습니다.

## <a name="nfs-30-features"></a>NFS 3.0 기능

다음 NFS 3.0 기능은 아직 지원되지 않습니다.

- UDP를 통한 NFS 3.0. TCP를 통한 NFS 3.0만 지원됩니다.

- NLM(네트워크 잠금 관리자)를 사용한 파일 잠금. 탑재 명령은 `-o nolock` 매개 변수를 포함해야 합니다.

- 하위 디렉터리 탑재. 루트 디렉터리(컨테이너)만 탑재할 수 있습니다.

- 탑재 나열(예: `showmount -a` 명령 사용)

- 내보내기 나열(예: `showmount -e` 명령 사용)

- 하드 링크

- 컨테이너를 읽기 전용으로 내보내기

## <a name="nfs-30-clients"></a>NFS 3.0 클라이언트

NFS용 Windows 클라이언트는 아직 지원되지 않습니다.

## <a name="blob-storage-features"></a>Blob Storage 기능

NFS 3.0 프로토콜 지원을 사용하도록 설정하면 일부 Blob Storage 기능이 완전히 지원되지만 일부 기능은 미리 보기 수준에서만 지원되거나 아직 지원되지 않을 수 있습니다.

NFS 3.0 지원을 사용하도록 설정된 계정에서 각 Blob Storage 기능이 지원되는 방식을 보려면 [Azure Storage 계정의 Blob Storage 기능 지원을 참조하세요.](storage-feature-support-in-storage-accounts.md)

## <a name="see-also"></a>참고 항목

- [Azure Blob Storage의 NFS(네트워크 파일 시스템) 3.0 프로토콜 지원](network-file-system-protocol-support.md)
- [NFS(네트워크 파일 시스템) 3.0 프로토콜을 사용하여 Blob Storage 탑재](network-file-system-protocol-support-how-to.md)
