---
title: AKS 가상 머신 호스트의 보안 강화
description: AKS VM 호스트 OS의 보안 강화에 대해 알아보기
services: container-service
author: georgewallace
ms.topic: article
ms.date: 03/29/2021
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 9c39d67fbf52295fd6b31d417c7780cf7e240304
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132055439"
---
# <a name="security-hardening-for-aks-agent-node-host-os"></a>AKS 에이전트 노드 호스트 OS의 보안 강화

보안 서비스인 AKS(Azure Kubernetes Service)는 SOC, ISO, PCI DSS 및 HIPAA 표준을 준수합니다. 이 문서에서는 AKS VM(가상 머신) 호스트에 적용되는 보안 강화에 대해 설명합니다. AKS 보안에 대한 자세한 내용은 [AKS(Azure Kubernetes Service)의 애플리케이션 및 클러스터에 대한 보안 개념](./concepts-security.md)을 참조하세요.

> [!Note]
> 이 문서의 범위는 AKS의 Linux 에이전트로 한정됩니다.

AKS 클러스터는 AKS에서 실행되는 컨테이너에 사용되는 보안에 최적화된 OS를 실행하는 호스트 VM에 배포됩니다. 이 호스트 OS는 [보안 강화](#security-hardening-features) 및 최적화가 더 적용된 **Ubuntu 18.04.5 LTS** 이미지를 기반으로 합니다.

보안 강화 호스트 OS의 목표는 공격 노출 영역을 줄이고 안전하게 컨테이너의 배포를 최적화하는 것입니다.

> [!Important]
> 보안 강화 OS는 CIS 벤치마크를 거치지 **않았습니다**. CIS 벤치마크와 겹치는 부분이 있지만 목표는 CIS를 준수하지 않는 것입니다. 호스트 OS 강화의 목적은 Microsoft의 자체적인 내부 호스트 보안 표준과 일치하는 보안 수준으로 수렴하는 것입니다.

## <a name="security-hardening-features"></a>보안 강화 기능

* AKS는 기본적으로 보안 최적화 호스트 OS를 제공하지만 대체 운영 체제를 선택할 수 있는 옵션은 없습니다.

* Azure는 AKS 가상 머신 호스트에 매일 패치를 적용합니다(보안 패치 포함). 
    * 이러한 패치 중 일부는 다시 부팅해야 적용됩니다. 
    * 필요에 따라 AKS VM 호스트 다시 부팅을 예약해야 합니다. 
    * AKS 패치를 자동화하는 방법에 대한 지침은 [AKS 노드 패치](./node-updates-kured.md)를 참조하세요.

## <a name="what-is-configured"></a>구성되는 항목

| CIS  | 감사 설명|
|---|---|
| 1.1.1.1 |cramfs filesystems 탑재를 사용하지 않도록 설정했는지 확인|
| 1.1.1.2 |freevxfs filesystems 탑재를 사용하지 않도록 설정했는지 확인|
| 1.1.1.3 |jffs2 filesystems 탑재를 사용하지 않도록 설정했는지 확인|
| 1.1.1.4 |HFS filesystems 탑재를 사용하지 않도록 설정했는지 확인|
| 1.1.1.5 |HFS Plus filesystems 탑재를 사용하지 않도록 설정했는지 확인|
|1.4.3 |단일 사용자 모드에 대한 인증이 필요한지 확인 |
|1.7.1.2 |로컬 로그인 경고 배너가 올바르게 구성되어 있는지 확인 |
|1.7.1.3 |원격 로그인 경고 배너가 올바르게 구성되어 있는지 확인 |
|1.7.1.5 |/etc/issue에 대한 권한이 구성되어 있는지 확인 |
|1.7.1.6 |/etc/issue.net에 대한 권한이 구성되어 있는지 확인 |
|2.1.5 |--streaming-connection-idle-timeout이 0으로 설정되지 않았는지 확인 |
|3.1.2 |패킷 리디렉션 보내기를 사용하지 않도록 설정되었는지 확인 |
|3.2.1 |원본 라우팅 패키지가 수락되지 않았는지 확인 |
|3.2.2 |ICMP 리디렉션이 수락되지 않았는지 확인 |
|3.2.3 |보안 ICMP 리디렉션이 수락되지 않았는지 확인 |
|3.2.4 |의심스러운 패킷이 기록되었는지 확인 |
|3.3.1 |IPv6 라우터 보급 알림이 수락되지 않았는지 확인 |
|3.5.1 |DCCP가 사용하지 않도록 설정되었는지 확인 |
|3.5.2 |SCTP가 사용하지 않도록 설정되었는지 확인 |
|3.5.3 |RDS가 사용하지 않도록 설정되었는지 확인 |
|3.5.4 |TIPC가 사용하지 않도록 설정되었는지 확인 |
|4.2.1.2 |로깅이 구성되어 있는지 확인 |
|5.1.2 |/etc/crontab에 대한 권한이 구성되어 있는지 확인 |
|5.2.4 |SSH X11 전달이 사용하지 않도록 설정되어 있는지 확인 |
|5.2.5 |SSH MaxAuthTries가 4 이하로 설정되어 있는지 확인 |
|5.2.8 |SSH 루트 로그인이 사용하지 않도록 설정되어 있는지 확인 |
|5.2.10 |SSH PermitUserEnvironment가 사용하지 않도록 설정되어 있는지 확인 |
|5.2.11 |승인된 MAX 알고리즘만 사용되는지 확인 |
|5.2.12 |SSH Idle Timeout Interval이 구성되어 있는지 확인 |
|5.2.13 |SSH LoginGraceTime이 1분 미만으로 설정되어 있는지 확인 |
|5.2.15 |SSH 경고 배너가 구성되어 있는지 확인 |
|5.3.1 |암호 만들기 요구 사항이 구성되어 있는지 확인 |
|5.4.1.1 |암호 만료가 90일 이하인지 확인 |
|5.4.1.4 |비활성 암호 잠금이 30일 이하인지 확인 |
|5.4.4 |default user umask가 027 이상으로 제한되어 있는지 확인 |
|5.6 |su 명령 액세스가 제한되어 있는지 확인|

## <a name="additional-notes"></a>추가적인 참고 사항
 
* 공격 노출 영역을 더 줄이기 위해 일부 불필요한 커널 모듈 드라이버는 OS에서 사용하지 않도록 설정되었습니다.

* 보안 강화 OS는 AKS에 대해 특별히 구축 및 유지 관리되며 AKS 플랫폼 밖에서는 지원되지 **않습니다**.

## <a name="next-steps"></a>다음 단계  

AKS 보안에 대한 자세한 내용은 다음 문서를 참조하세요. 

* [AKS(Azure Kubernetes Service)](./intro-kubernetes.md)
* [AKS 보안 고려 사항 ](./concepts-security.md)
* [AKS 모범 사례 ](./best-practices.md)
