---
title: Oracle용 BareMetal Infrastructure란?
description: BareMetal Infrastructure가 Oracle 워크로드에 대해 제공하는 기능에 관해 알아봅니다.
ms.topic: conceptual
ms.subservice: baremetal-oracle
ms.date: 09/03/2021
ms.openlocfilehash: e74d1a3e3b6cb1b2cd0b99927a57a88117715e7b
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123542443"
---
# <a name="what-is-baremetal-infrastructure-for-oracle"></a>Oracle용 BareMetal Infrastructure란?

이 문서에서는 Oracle 워크로드에 대해 BareMetal Infrastructure가 제공하는 기능에 대한 개요를 제공합니다.

Oracle용 BareMetal Infrastructure는 Oracle 인증 UCS(Unified Computing System) 및 FLexPod를 기반으로 합니다. FlexPod 플랫폼은 미리 유효성이 검사된 스토리지, 네트워킹 및 서버 기술을 제공합니다. NetApp NFS(네트워크 파일 시스템) 스토리지를 제공하여 DirectNFS 프로토콜을 사용하여 통합을 제공합니다. BareMetal 서버는 BareMetal 인스턴스에 하이퍼바이저가 없는 전용 서버입니다. 

이 인스턴스는 Oracle 워크로드가 필요한 중요 업무용 애플리케이션을 실행하는 데 사용됩니다. BareMetal 인스턴스는 Azure VM(가상 머신)에서 실행되는 애플리케이션에 대해 짧은 대기 시간(0.35ms)을 제공합니다. BareMetal은 공유 스토리지를 제공합니다. 또한 전용 프라이빗 상호 연결 네트워크와의 노드 간 통신에 필요한 다중 캐스팅을 지원합니다. 

Oracle용 BareMetal Infrastructure의 다른 기능은 다음과 같습니다.

- Oracle 인증 UCS 블레이드 - UCSB200-M5, UCSB460-M4, UCSB480-M5
- 프라이빗 VLAN(가상 LAN)을 사용한 Oracle RAC(Real Application Clusters) 노드 간(다중 캐스트) 통신 -40Gb.
- Microsoft 관리형 하드웨어
  - 중복 스토리지, 네트워크, 전원, 관리
  - 인프라, 복구 및 바꾸기를 위한 모니터링
  - 고객의 도메인 컨트롤러에 대한 Azure ExpressRoute 포함
  - 보호된 물리적 및 네트워크 보안을 통해 모든 Azure 클라우드 서비스에 액세스할 수 있음

### <a name="supported-protocols"></a>지원되는 프로토콜

다음 프로토콜은 Oracle 워크로드를 위한 BareMetal 서버 내에서 여러 가지 탑재 지점에 사용됩니다.

- OS 탑재 – iSCSI
- 데이터/로그 – NFSv3
- 백업/보관 – NFSv4

### <a name="licensing"></a>라이선싱

- 자체 온-프레미스 운영 체제 및 Oracle 라이선스를 가져옵니다.

### <a name="operating-system"></a>운영 체제

서버는 운영 체제 RHEL 7.6과 함께 미리 로드됩니다.

## <a name="next-steps"></a>다음 단계

Oracle BareMetal 워크로드의 SKU에 관해 알아봅니다.

> [!div class="nextstepaction"]
> [Oracle 워크로드의 BareMetal SKU](oracle-baremetal-skus.md)
