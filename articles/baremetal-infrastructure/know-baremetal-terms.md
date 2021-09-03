---
title: Azure BareMetal 인프라의 약관 파악
description: Azure BareMetal 인프라의 약관을 파악합니다.
ms.topic: conceptual
ms.date: 07/13/2021
ms.openlocfilehash: a4bf8b24bc412215f595e12128c0a63f40e8fbfd
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113767110"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>BareMetal 인프라의 약관 파악

이 문서에서는 BareMetal 인프라와 관련된 몇 가지 중요한 용어를 알아봅니다.

- **수정 버전**: BareMetal 인프라(HANA 대규모 인스턴스) 스탬프에 대한 두 가지 스탬프 수정 버전이 있습니다. 이러한 수정 버전은 아키텍처와 Azure 가상 머신 호스트에 대한 근접성이 다릅니다.
    - "수정 버전 3"(Rev 3): 원래 디자인은 2016년 중간에 배포되었습니다.
    - "수정 버전 4.2"(Rev 4.2): Azure VM과 HANA 대규모 인스턴스 간의 네트워크 대기 시간이 매우 짧아 Azure 가상 머신 호스트에 대한 근접성을 높인 새로운 디자인입니다. Azure Portal의 리소스를 "BareMetal 인프라"라고 하며 고객은 Azure Portal의 BareMetal 인스턴스로 리소스에 액세스할 수 있습니다.

- **스탬프**: BareMetal 인스턴스의 Microsoft 내부 배포 크기를 정의합니다. 인스턴스 단위를 배포하기 전에 컴퓨팅, 네트워크, 저장소 랙으로 구성된 BareMetal 인스턴스 스탬프를 데이터 센터 위치에 배포해야 합니다. 이러한 배포를 BareMetal 인스턴스 스탬프라고 합니다.

- **테넌트**: BareMetal 인스턴스 스탬프에 배포된 고객은 *테넌트* 로 격리됩니다. 테넌트는 네트워킹, 스토리지 및 컴퓨팅 계층에서 다른 테넌트로부터 격리됩니다. 다른 테넌트에 할당된 저장소 및 컴퓨팅 단위는 BareMetal 인스턴스 스탬프 수준에서 서로 확인하거나 통신할 수 없습니다. 고객은 다양한 테넌트에 배포하도록 선택할 수 있습니다. 이 경우에도 BareMetal 인스턴스 스탬프 수준에서 테넌트 간 통신은 없습니다.

## <a name="next-steps"></a>다음 단계

BareMetal Infrastructure에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [BareMetal Infrastructure](concepts-baremetal-infrastructure-overview.md)
