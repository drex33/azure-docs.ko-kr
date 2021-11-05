---
title: Azure 기밀 원장 노드 인증
description: Azure 기밀 원장 노드 인증
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 20e5c46ea174807efea21cf4812eef47ee8c0257
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450099"
---
# <a name="authenticating-azure-confidential-ledger-nodes"></a>Azure 기밀 원장 노드 인증

Azure 기밀 원장 노드는 사용자가 코드 샘플을 사용하여 인증할 수 있습니다.

## <a name="code-samples"></a>코드 샘플

초기화하면 코드 샘플이 ID 서비스를 쿼리하여 노드 인증서를 가져옵니다. 노드 인증서를 검색하면 코드 샘플이 Ledger 네트워크를 쿼리하여 견적을 가져옵니다. 이후 호스트 확인 이진 파일을 사용하여 그 유효성을 검사합니다. 확인에 성공하면 코드 샘플이 Ledger 작업으로 진행됩니다.

## <a name="users"></a>사용자

사용자는 Azure 기밀 원장 노드의 신뢰성을 확인하여 실제로 Ledger의 enclave와 인터페이스로 연결되어 있는지 확인할 수 있습니다. 여러 방법으로 Azure 기밀 원장 노드에서 신뢰를 구축할 수 있습니다. 노드는 서로 적층되어 전체 신뢰 수준을 높일 수 있습니다. 따라서 1~2단계는 기능 워크플로 내에서 초기 TLS 핸드셰이크 및 인증의 일부로 Azure 기밀 원장 enclave에 대한 신뢰도를 구축하는 데 도움이 됩니다. 그 외에 사용자의 클라이언트와 기밀 원장 간에 영구 클라이언트 연결이 유지됩니다.

- **기밀 원장 노드 유효성 검사**: Microsoft에서 호스트하는 ID 서비스를 쿼리하여 검사합니다. 이러면 네트워크 인증서를 제공하여 Ledger 노드가 해당 특정 인스턴스에 대해 네트워크 인증서로 보증/서명된 인증서를 제공하는지 확인하는 데 도움이 됩니다. PKI 기반 HTTPS처럼 서버의 인증서는 잘 알려진 CA(인증 기관) 또는 중간 CA에서 서명합니다. Azure 기밀 원장의 경우 CA 인증서는 ID 서비스에서 네트워크 인증서 형식으로 반환합니다. 이는 기밀 원장 사용자를 위한 중요한 신뢰 구축 조치입니다. 이 노드 인증서에 반환된 네트워크 인증서 서명이 없는 경우 클라이언트 연결에 실패합니다(샘플 코드에서 구현됨).
- **기밀 원장 enclave 유효성 검사**: 기밀 원장이 해당 enclave 내에서 생성된 데이터 Blob인 Quote로 표현되는 Intel® SGX enclave에서 실행됩니다. 다른 엔터티에서 이를 사용하여 Intel® SGX 보호를 실행하는 애플리케이션에서 견적이 생성되었는지 확인할 수 있습니다. 견적은 쉽게 확인할 수 있는 방식으로 구성됩니다. 여기에는 Enclave 및 실행 중인 애플리케이션의 다양한 속성을 식별하는 데 도움이 되는 클레임이 포함되어 있습니다. 이는 기밀 원장 사용자를 위한 중요한 신뢰 구축 메커니즘입니다. 이를 위해서는 함수 워크플로 API를 호출하여 enclave 견적을 가져와야 합니다. 견적이 유효하지 않으면 클라이언트 연결에 실패합니다. 이 경우 검색된 견적을 open_enclaves Host_Verify 도구로 그 유효성을 검사할 수 있습니다. 이에 대한 자세한 내용은 여기에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Microsoft Azure 기밀 원장 개요](overview.md)
- [Azure 기밀 원장 아키텍처](architecture.md)
