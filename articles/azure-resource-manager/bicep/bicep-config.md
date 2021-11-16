---
title: Bicep 구성 파일
description: Bicep 배포에 대 한 구성 파일을 설명 합니다.
ms.topic: conceptual
ms.date: 11/16/2021
ms.openlocfilehash: 8679daf65402c939f9eea7651b57b1198970fb8d
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548143"
---
# <a name="configure-your-bicep-environment"></a>Bicep 환경 구성

Bicep는 **bicepconfig** 이라는 구성 파일을 지원 합니다. 이 파일 내에서 Bicep 개발 환경을 사용자 지정 하는 값을 추가할 수 있습니다. 이 파일을 추가 하지 않으면 Bicep에서 기본값을 사용 합니다.

값을 사용자 지정 하려면 Bicep 파일을 저장 하는 디렉터리에이 파일을 만듭니다. 여러 디렉터리에 bicepconfig 파일을 추가할 수 있습니다. 디렉터리 계층 구조에서 가장 가까운 구성 파일이 사용 됩니다.

## <a name="available-settings"></a>사용 가능한 설정

[모듈을 사용할](modules.md)때 모듈 경로에 대 한 별칭을 추가할 수 있습니다. 이러한 별칭은 복잡 한 경로를 반복할 필요가 없기 때문에 Bicep 파일을 단순화 합니다. 레지스트리에 대 한 인증을 위해 자격 증명 우선 순위를 구성할 수도 있습니다. 자격 증명은 로컬 캐시로 외부 모듈을 복원 하는 데 사용 됩니다. 자세한 내용은 [Bicep config에 모듈 설정 추가](bicep-config-modules.md)를 참조 하세요.

[Bicep linter](linter.md)를 사용 하는 경우 Bicep 파일 유효성 검사에 대 한 기본 설정을 재정의할 수 있습니다. 자세한 내용은 [Bicep config에 linter 설정 추가](bicep-config-linter.md)를 참조 하세요.

## <a name="intellisense"></a>IntelliSense

Visual Studio Code의 Bicep 확장은 **bicepconfig** 파일에 대 한 intellisense를 지원 합니다. Intellisense를 사용 하 여 사용 가능한 속성 및 값을 검색 합니다.

:::image type="content" source="./media/bicep-config/bicep-linter-configure-intellisense.png" alt-text="bicepconfig.json 구성 시 Intellisense 지원.":::

## <a name="next-steps"></a>다음 단계

* [Bicep config에서 모듈 설정 추가](bicep-config-modules.md)
* [Bicep config에 lecsettings 추가](bicep-config-linter.md)
* [Bicep linter](linter.md) 에 대해 알아보기
