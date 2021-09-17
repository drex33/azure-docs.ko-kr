---
title: 포함 파일
description: 포함 파일
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ba77d7f580a2a5fe69d575d2727e42ed12c68019
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123646757"
---
사용자 지정 IPsec 정책으로 작업하는 경우 다음 요구 사항을 염두에 두어야 합니다.

* **IKE** - IKE의 경우 IKE 암호화의 매개 변수와 IKE 무결성의 매개 변수 및 DH 그룹의 매개 변수를 선택할 수 있습니다.
* **IPsec** - IPsec의 경우 IPsec 암호화의 매개 변수와 IPsec 무결성의 매개 변수 및 PFS의 매개 변수를 선택할 수 있습니다. IPsec 암호화 또는 IPsec 무결성에 대한 매개 변수가 GCM인 경우 두 설정 모두에 대한 매개 변수는 GCM이어야 합니다.

>[!NOTE]
> 사용자 지정 IPsec 정책에는 기본 IPsec 정책과 달리 응답자와 개시자의 개념이 없습니다. 양쪽(온-프레미스 및 Azure VPN 게이트웨이) 모두는 IKE 단계 1 및 IKE 단계 2에 대해 동일한 설정을 사용합니다. IKEv1 및 IKEv2 프로토콜이 모두 지원됩니다. Azure는 응답자 전용으로 지원되지 않습니다.
>

**사용 가능한 설정 및 매개 변수**

| 설정 | 매개 변수 |
|--- |--- |
| IKE 암호화 | GCMAES256, GCMAES128, AES256, AES128 |
| IKE 무결성 | SHA384, SHA256 |
| DH 그룹 | ECP384, ECP256, DHGroup24, DHGroup14 |
| IPsec 암호화 | GCMAES256, GCMAES128, AES256, AES128, 없음 |
| IPsec 무결성 | GCMAES256, GCMAES128, SHA256 |
| PFS 그룹 | ECP384, ECP256, PFS24, PFS14, 없음 |
| SA 수명 |값 최소 300/기본 3600 초 |
