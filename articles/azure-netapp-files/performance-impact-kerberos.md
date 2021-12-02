---
title: Azure NetApp Files NFSv 4.1 볼륨에서 Kerberos의 성능 영향 | Microsoft Docs
description: Azure NetApp Files NFSv 4.1 볼륨에서 사용 가능한 보안 옵션, 테스트 된 성능 벡터 및 kerberos의 예상 성능 영향에 대해 설명 합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-hchen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: b-hchen
ms.openlocfilehash: 178aa5ed797e6b6fb5e8e3b4df5a902bdbc849c9
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133487248"
---
# <a name="performance-impact-of-kerberos-on-azure-netapp-files-nfsv41-volumes"></a>Azure NetApp Files NFSv 4.1 볼륨에서 Kerberos의 성능 영향

Azure NetApp Files는 256 암호화를 사용 하는 Kerberos 모드 (krb5.conf, krb5i 및 krb5p) [에서 NFS 클라이언트 암호화](configure-kerberos-encryption.md) 를 지원 합니다. 이 문서에서는 NFSv 4.1 볼륨의 Kerberos에 대 한 성능 영향을 설명 합니다. 

## <a name="available-security-options"></a>사용 가능한 보안 옵션 

NFSv 4.1 볼륨에 현재 사용할 수 있는 보안 옵션은 다음과 같습니다. 

* **sec = sys** 는 AUTH_SYS를 사용 하 여 NFS 작업을 인증 함으로써 로컬 UNIX uid 및 gid ds를 사용 합니다.
* **sec = krb5.conf** 는 로컬 UNIX uid 및 gid ds 대신 Kerberos V5를 사용 하 여 사용자를 인증 합니다.
* **sec = krb5i** 는 사용자 인증에 Kerberos V5를 사용 하 고 보안 체크섬을 사용 하 여 NFS 작업의 무결성 검사를 수행 하 여 데이터 변조를 방지 합니다.
* **sec = krb5p** 사용자 인증 및 무결성 검사에 Kerberos V5를 사용 합니다. 트래픽 스니핑을 방지 하기 위해 NFS 트래픽을 암호화 합니다. 이 옵션은 가장 안전한 설정 이지만 대부분의 성능 오버 헤드가 포함 됩니다.

## <a name="performance-vectors-tested"></a>테스트 된 성능 벡터

이 섹션에서는 다양 한 옵션의 단일 클라이언트 쪽 성능 영향에 대해 설명 합니다 `sec=*` .

* 성능 영향은 낮은 동시성 (낮은 부하) 및 높은 동시성 (i/o 및 처리량의 상한)의 두 가지 수준으로 테스트 되었습니다.  
* 테스트 된 세 가지 종류의 작업은 다음과 같습니다.  
    * 작은 작업 무작위 읽기/쓰기 (FIO 사용)
    * FIO를 사용 하는 대량 작업 순차 읽기/쓰기
    * Git와 같은 응용 프로그램에 의해 생성 된 메타 데이터 작업량이 많은 작업

## <a name="expected-performance-impact"></a>예상 성능 영향 

포커스에는 두 가지 영역, 즉 낮은 부하와 상한이 있습니다. 다음 목록에서는 보안 설정 및 시나리오 별로 성능 영향 보안 설정에 대해 설명 합니다. 모든 비교는 보안 매개 변수를 기준으로 수행 됩니다 `sec=sys` . 단일 클라이언트를 사용 하 여 단일 볼륨에서 테스트가 수행 되었습니다. 

Krb5.conf의 성능 영향:

* 낮은 동시성 (r/w):
    * 순차적 대기 시간이 0.3 밀리초로 증가 했습니다.
    * 임의 i/o 대기 시간이 0.2 밀리초로 증가 했습니다.
    * 메타 데이터 i/o 대기 시간이 0.2 밀리초로 증가 했습니다.
* 높은 동시성 (r/w): 
    * Krb5.conf의 최대 순차 처리량에 영향을 주지 않았습니다.
    * 작업 부하가 순수 쓰기로 전환 될 때 전체 영향을 0으로 줄이면 순수 읽기 작업의 경우 최대 임의 i/o가 30% 감소 합니다. 
    * 최대 메타 데이터 워크 로드가 30% 감소 했습니다.

Krb5i의 성능 영향: 

* 낮은 동시성 (r/w):
    * 순차적 대기 시간이 0.5 밀리초로 증가 했습니다.
    * 임의 i/o 대기 시간이 0.2 밀리초로 증가 했습니다.
    * 메타 데이터 i/o 대기 시간이 0.2 밀리초로 증가 했습니다.
* 높은 동시성 (r/w): 
    * 워크 로드 혼합에 상관 없이 최대 순차적 처리량이 70% 감소 했습니다.
    * 작업 부하가 순수한 write로 이동 하므로 전체 영향이 25%로 감소 하는 순수한 읽기 작업의 경우 최대 임의 i/o가 50% 감소 합니다. 
    * 최대 메타 데이터 워크 로드가 30% 감소 했습니다.

Krb5p의 성능 영향:

* 낮은 동시성 (r/w):
    * 순차적 대기 시간이 0.8 밀리초로 증가 했습니다.
    * 임의 i/o 대기 시간이 0.2 밀리초로 증가 했습니다.
    * 메타 데이터 i/o 대기 시간이 0.2 밀리초로 증가 했습니다.
* 높은 동시성 (r/w): 
    * 워크 로드 혼합에 상관 없이 최대 순차적 처리량이 85% 감소 했습니다. 
    * 작업 부하가 순수 쓰기로 전환 될 때 전체 영향이 43%로 감소 하는 순수한 읽기 작업의 경우 최대 무작위 i/o 수는 65% 감소 합니다. 
    * 최대 메타 데이터 워크 로드가 30% 감소 했습니다.

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files에 대한 NFSv4.1 Kerberos 암호화 구성](configure-kerberos-encryption.md) 
