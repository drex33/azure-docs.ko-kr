---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 023f3413fbd7b642300de19ad479029c94e7c4d5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128705613"
---
## <a name="setting-up"></a>설치

### <a name="creating-the-visual-studio-project"></a>Visual Studio 프로젝트 만들기

Visual Studio 2019에서 새 `Blank App (Universal Windows)` 프로젝트를 만듭니다. 프로젝트 이름을 입력한 후 `10.0.17134`보다 높은 버전의 Windows SDK를 자유롭게 선택합니다. 

### <a name="install-the-package-and-dependencies-with-nuget-package-manager"></a>NuGet 패키지 관리자를 사용하여 패키지와 종속성 설치

Calling SDK API와 라이브러리는 NuGet 패키지를 통해 공개적으로 사용할 수 있습니다.
다음 단계는 Calling SDK NuGet 패키지를 찾고, 다운로드하고, 설치하는 방법을 안내합니다.

1. NuGet 패키지 관리자(`Tools` -> `NuGet Package Manager` -> `Manage NuGet Packages for Solution`)를 엽니다.
2. `Browse`를 클릭하고 검색 상자에 `Azure.Communication.Calling`을 입력합니다.
3. `Include prerelease` 확인란이 선택되어 있는지 확인합니다.
4. `Azure.Communication.Calling` 패키지를 클릭합니다.
5. 오른쪽 탭의 CS 프로젝트에 해당하는 확인란을 선택합니다.
6. `Install` 단추를 클릭합니다.