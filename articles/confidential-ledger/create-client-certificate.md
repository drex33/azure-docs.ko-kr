---
title: Microsoft Azure 기밀 원장을 사용하여 클라이언트 인증서 만들기
description: Microsoft Azure 기밀 원장을 사용하여 클라이언트 인증서 만들기
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 216a626bfaaf86a8c2237b5d871ab7895d921127
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476398"
---
# <a name="creating-a-client-certificate"></a>클라이언트 인증서 만들기

Azure 기밀 원장 API에는 클라이언트 인증서 기반 인증이 필요합니다. 원장 생성 또는 원장 업데이트 중에 허용 목록에 추가된 인증서만 기밀 원장 기능 API를 호출하는 데 사용할 수 있습니다.

PEM 형식의 인증서가 필요합니다. 둘 이상의 인증서를 만들고 Ledger Update API를 사용하여 인증서를 추가하거나 삭제할 수 있습니다.

## <a name="openssl"></a>OpenSSL

OpenSSL을 사용하여 인증서를 생성하는 것이 좋습니다. git을 설치한 경우 git 셸에서 OpenSSL을 실행할 수 있습니다. 그렇지 않으면 OS에 OpenSSL을 설치할 수 있습니다.

- **Windows**: [Windows용 chocolatey](https://chocolatey.org/install)를 설치하고, 관리자 모드에서 PowerShell 터미널 창을 열고, `choco install openssl`을 실행합니다. 또는 [여기](http://gnuwin32.sourceforge.net/packages/openssl.htm)에서 Windows용 OpenSSL을 직접 설치할 수 있습니다.
- **Linux**: `sudo apt-get install openssl` 실행

그런 다음, Bash 또는 PowerShell 터미널 창에서 `openssl`을 실행하여 인증서를 생성할 수 있습니다.

```bash
openssl ecparam -out "privkey_name.pem" -name "secp384r1" -genkey
openssl req -new -key "privkey_name.pem" -x509 -nodes -days 365 -out "cert.pem" -"sha384" -subj=/CN="ACL Client Cert"
```

## <a name="next-steps"></a>다음 단계

- [Microsoft Azure 기밀 원장 개요](overview.md)