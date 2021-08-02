---
title: Azure FXT Edge Filer 포트 목록
description: FXT 클러스터 환경에서 사용되는 TCP/UDP 포트 목록
author: ekpgh
ms.author: v-erkel
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 05/20/2021
ms.openlocfilehash: 5067e60a45025ae6c37af63df4e1392e9ae52709
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111358520"
---
# <a name="required-network-ports"></a>필수 네트워크 포트

이 목록에는 FXT 클러스터 환경에 필요한 TCP/UDP 포트가 표시됩니다. 해당 포트에 액세스할 수 있도록 사용 중인 방화벽을 구성합니다.

시스템의 특정 요구 사항은 사용하는 백 엔드 스토리지의 종류에 따라 다릅니다.

자세한 내용은 Microsoft 서비스 및 지원 센터에 문의하세요.

## <a name="api-ports"></a>API 포트

| Direction | Type | 포트 번호 | 프로토콜 |
|-----------|------|-------------|----------|
| 인바운드   | TCP  | 22          | SSH      |
| 아웃바운드  | TCP  | 80          | HTTP     |
| 인바운드/아웃바운드  | TCP  | 443         | HTTPS    |

## <a name="nfs-ports"></a>NFS 포트

인바운드 NFS 포트:

| Type    | 포트 번호 | 서비스  |
|---------|-------------|----------|
| TCP/UDP | 111         | RPCBIND  |
| TCP/UDP | 2049        | NFS      |
| TCP/UDP | 4045        | NLOCKMGR |
| TCP/UDP | 4046        | MOUNTD   |
| TCP/UDP | 4047        | 상태   |

아웃바운드 NFS 포트:

| Type    | 포트 번호 | 서비스  |
|---------|-------------|----------|
| TCP/UDP | 111         | RPCBIND  |
| TCP/UDP | 2049        | NFS      |

아웃바운드 NFS 포트 트래픽은 코어 파일러로 사용되는 스토리지의 종류에 따라 달라집니다. (포트 2049는 여기에 나열될 만큼 일반적이지만 일부 시스템에는 사용되지 않습니다. 모든 클러스터에는 포트 111에 대한 액세스 권한이 있어야 합니다.) 스토리지 시스템에 대한 설명서를 확인하거나 아래의 [추가 포트 요구 사항](#additional-port-requirements)에 설명된 쿼리 기술을 사용하세요.

FXT 노드에서 나오는 일부 아웃바운드 NFS 트래픽은 임시 포트를 사용합니다. 잘 알려진 위 포트의 아웃바운드 FXT 트래픽에 대해 전송 수준에서 제한이 있어서는 안 됩니다.

## <a name="smb-ports"></a>SMB 포트

| Direction | Type | 포트 번호 | 프로토콜 |
|-----------|------|-------------|----------|
| 인바운드/아웃바운드  | UDP  | 137         | NetBIOS  |
| 인바운드   | UDP  | 138         | NetBIOS  |
| 인바운드/아웃바운드  | TCP  | 139         | SMB      |
| 인바운드/아웃바운드  | TCP  | 445         | SMB      |

<!--| Outbound  | UDP  | 137         | NetBIOS  | 
| Outbound  | TCP  | 139         | SMB      |
| Outbound  | TCP  | 445         | SMB      |
-->

## <a name="general-traffic-ports"></a>일반 트래픽 포트

| Direction | Type    | 포트 번호 | 프로토콜 |
|-----------|---------|-------------|----------|
| 아웃바운드  | TCP/UDP | 53          | DNS      |
| 아웃바운드  | TCP/UDP | 88          | Kerberos |
| 아웃바운드  | UDP     | 123         | NTP      |
| 아웃바운드  | TCP/UDP | 389         | LDAP     |
| 아웃바운드  | TCP     | 686         | LDAPS    |

## <a name="additional-port-requirements"></a>추가 포트 요구 사항

코어 파일러에 추가 포트에 대한 액세스 권한이 필요할 수 있습니다. 이 요구 사항은 사용되는 스토리지 유형에 따라 달라집니다.

`rpcinfo` 명령을 사용하여 특정 서버에 사용되는 포트를 확인할 수 있습니다. 이 명령은 방화벽이 설정되지 않은 클라이언트 시스템에서 실행하세요.

`rpcinfo -p <server_IP_address>`
