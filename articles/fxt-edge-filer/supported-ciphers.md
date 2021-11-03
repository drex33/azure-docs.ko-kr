---
title: Azure FXT Edge Filer에 지원되는 암호화 암호
description: FXT Edge Filer 클러스터에 사용되는 암호화 표준 목록
author: femila
ms.author: femila
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 05/20/2021
ms.openlocfilehash: 0c05e821ab19c4a617a8a6e13bd7f1c37331a661
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131033003"
---
# <a name="supported-encryption-standards-for-azure-fxt-edge-filer"></a>Azure FXT Edge Filer에 지원되는 암호화 표준

이 문서에서는 Azure FXT Edge Filer에 필요한 암호화 표준에 대해 설명합니다. 해당 표준은 운영 체제 버전 5.1.1.2에서 구현됩니다.

해당 표준은 Azure FXT Edge Filer 및 [Avere vFXT for Azure](../avere-vfxt/index.yml)에 적용됩니다.

Azure FXT Edge Filer 캐시 또는 개별 노드에 연결되는 모든 관리 또는 인프라 시스템은 해당 표준을 충족해야 합니다.

(클라이언트 머신은 NFS를 사용하여 캐시를 탑재하므로 해당 암호화 요구 사항은 적용되지 않습니다. 다른 적절한 방법으로 클라이언트 머신의 보안을 유지하세요.)

## <a name="tls-standard"></a>TLS 표준

* TLS 1.2를 사용하도록 설정해야 함
* SSL V2 및 V3을 사용하지 않도록 설정해야 함

TLS 1.0 및 TLS 1.1은 프라이빗 개체 저장소와의 호환성을 위해 반드시 필요한 경우에 사용할 수 있지만 프라이빗 스토리지를 최신 보안 표준으로 업그레이드하는 것이 더 좋습니다. 자세한 내용은 Microsoft 고객 서비스 및 지원 센터에 문의하세요.

## <a name="permitted-cipher-suites"></a>허용되는 암호 그룹

Azure FXT Edge Filer는 다음과 같은 TLS 암호 그룹을 협상할 수 있도록 허용합니다.

* ECDHE-ECDSA-AES128-GCM-SHA256
* ECDHE-ECDSA-AES256-GCM-SHA384
* ECDHE-RSA-AES128-GCM-SHA256
* ECDHE-RSA-AES256-GCM-SHA384
* ECDHE-ECDSA-AES128-SHA256
* ECDHE-ECDSA-AES256-SHA384
* ECDHE-RSA-AES128-SHA256
* ECDHE-RSA-AES256-SHA384

클러스터 관리 HTTPS 인터페이스(제어판 웹 GUI 및 관리 RPC 연결에 사용됨)는 위의 암호 그룹 및 TLS 1.2만 지원합니다. 이 외의 프로토콜 또는 암호 그룹은 관리 인터페이스 연결 시 지원되지 않습니다.

## <a name="ssh-server-access"></a>SSH 서버 액세스

해당 표준은 해당 제품에 포함된 SSH 서버에 적용됩니다.

SSH 서버는 원격 로그인을 슈퍼 사용자 “루트”로 허용하지 않습니다. Microsoft 고객 서비스 및 지원 센터의 지침에 따라 원격 SSH 액세스가 필요한 경우 셸에 제한이 있는 SSH “관리자” 사용자로 로그인합니다.

다음 SSH 암호 그룹은 클러스터 SSH 서버에서 사용할 수 있습니다. 클러스터에 연결하는 데 SSH를 사용하는 클라이언트에는 해당 표준을 충족하는 최신 소프트웨어가 있어야 합니다.

### <a name="ssh-encryption-standards"></a>SSH 암호화 표준

| Type | 지원되는 값 |
|--|--|
| Ciphers | aes256-gcm@openssh.com</br> aes128-gcm@openssh.com</br> aes256-ctr</br> aes128-ctr |
| MAC | hmac-sha2-512-etm@openssh.com</br> hmac-sha2-256-etm@openssh.com</br> hmac-sha2-512</br> hmac-sha2-256 |
| KEX 알고리즘 | ecdh-sha2-nistp521</br> ecdh-sha2-nistp384</br> ecdh-sha2-nistp256</br> diffie-hellman-group-exchange-sha256 |

## <a name="next-steps"></a>다음 단계

* Azure FXT Edge Filer 클러스터에 백 엔드 [스토리지를 추가](add-storage.md)하는 방법
* [제어판에 연결](cluster-create.md#open-the-settings-pages)하여 클러스터 관리
* [클라이언트를 탑재](mount-clients.md)하여 클러스터의 데이터에 액세스
* 암호화 표준에 대한 자세한 내용은 [지원 서비스에 문의](support-ticket.md)하세요.
