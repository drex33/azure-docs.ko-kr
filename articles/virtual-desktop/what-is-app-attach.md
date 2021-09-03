---
title: Azure Virtual Desktop MSIX 앱 연결 개요 - Azure
description: MSIX 앱 연결이란? 이 문서에서 확인하세요.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: d55308876db0928bef4fdf3b89ace10305f7c47d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528295"
---
# <a name="what-is-msix-app-attach"></a>MSIX 앱 연결이란?

MSIX는 모든 Windows 앱의 패키징 환경을 개선하는 데 필요한 많은 기능을 제공하는 새로운 패키징 형식입니다. MSIX에 대해 자세히 알아보려면 [MSIX 개요](/windows/msix/overview)를 참조하세요.

MSIX 앱 연결은 물리적 머신과 가상 머신 모두에 MSIX 애플리케이션을 제공하는 방법입니다. 그러나 MSIX 앱 연결은 Azure Virtual Desktop용으로 만들어졌기 때문에 일반 MSIX와는 다릅니다. 이 문서에서는 MSIX 앱 연결의 정의와 사용자를 위해 수행할 수 있는 작업을 설명합니다.

## <a name="application-delivery-options-in-azure-virtual-desktop"></a>Azure Virtual Desktop의 애플리케이션 제공 옵션

다음 메서드 중 하나를 통해 Azure Virtual Desktop에서 앱을 제공할 수 있습니다.

- 마스터 이미지에 앱 배치
- 중앙 관리를 위해 SCCM 또는 Intune과 같은 도구 사용
- 동적 앱 프로비저닝(AppV, VMware AppVolumes 또는 Citrix AppLayering)
- Microsoft 및 타사 도구를 사용하여 사용자 지정 도구 또는 스크립트 만들기

## <a name="what-does-msix-app-attach-do"></a>MSIX 앱 연결은 어떤 기능을 하나요?

Azure Virtual Desktop 배포에서 MSIX 앱 연결은 다음을 수행할 수 있습니다.

- [MSIX 컨테이너](/windows/msix/msix-container)를 사용하여 사용자 데이터, OS 및 앱을 구분합니다.
- 애플리케이션을 동적으로 제공할 때 재패키징할 필요가 없습니다.
- 사용자가 로그인하는 데 걸리는 시간을 줄입니다.
- 인프라 요구 사항 및 비용을 줄입니다.

MSIX 앱 연결은 VDI 또는 SBC 외부에서 적용할 수 있어야 합니다.

## <a name="traditional-app-layering-compared-to-msix-app-attach"></a>MSIX 앱 연결과 비교한 기존 앱 계층화

다음 표에서는 MSIX 앱 연결 및 앱 계층화의 주요 기능을 비교합니다.

| 기능 | 기존 앱 계층화  | MSIX 앱 연결  |
|-----|-----------------------------|--------------------|
| 형식               | 다른 앱 계층화 기술에는 서로 다른 독자적인 형식이 필요합니다. | 네이티브 MSIX 패키징 형식으로 작동합니다.        |
| 재패키징 오버헤드 | 독자적 형식에는 업데이트마다 시퀀싱 및 재패키징이 필요합니다.         | MSIX로 게시된 앱은 재패키징할 필요가 없습니다. 그러나 MSIX 패키지를 사용할 수 없는 경우 재패키징 오버헤드가 계속 적용됩니다. |
| 에코시스템            | 해당 없음(예: 공급 업체는 App-V를 제공하지 않음)  | MSIX는 핵심 ISV 파트너 및 Office와 같은 사내 앱을 채택하는 Microsoft의 기본 기술입니다. 가상 데스크톱과 물리적 Windows 컴퓨터 모두에서 MSIX를 사용할 수 있습니다. |
| 인프라       | 추가 인프라 필요(서버, 클라이언트 등) | 스토리지 전용   |
| 관리       | 유지 관리 및 업데이트 필요   | 앱 업데이트 간소화 |
| 사용자 환경      | 사용자 로그인 시간에 영향을 줍니다. 경계는 OS 상태, 앱 상태 및 사용자 데이터 사이에 존재합니다.  | 제공된 앱은 로컬로 설치된 애플리케이션과 구분할 수 없습니다. |

## <a name="next-steps"></a>다음 단계

MSIX 앱 연결에 관해 자세히 알아보려면 [용어집](app-attach-glossary.md) 및 [FAQ](app-attach-faq.yml)를 확인하세요. 그러지 않으면 [앱 연결 설정](app-attach.md)을 시작합니다.
