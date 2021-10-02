---
title: Azure Automation FAQ
description: 이 문서에서는 Azure Automation에 대한 질문과 대답을 제공합니다.
services: automation
ms.subservice: ''
ms.topic: conceptual
ms.date: 08/25/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dfe04bb2fcb7022934ac699bb5c3bd90005bf3b3
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356606"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation 질문과 대답

이 Microsoft FAQ는 Azure Automation에 대한 일반적인 질문과 대답 목록입니다. 기능에 대한 다른 질문이 있으면 토론 포럼으로 이동하여 질문을 게시하세요. 자주 묻는 질문일 경우 빠르고 쉽게 찾을 수 있도록 이 문서에 추가합니다.

## <a name="can-update-management-prevent-unexpected-os-level-upgrades"></a>업데이트 관리 예기치 않은 OS 수준 업그레이드를 방지할 수 있나요?

예. 자세한 내용은 [업데이트 제외를 참조하세요.](./update-management/manage-updates-for-vm.md#exclude-updates)

## <a name="why-arent-criticalsecurity-updates-applied-to-a-linux-machine-with-update-management"></a>업데이트 관리 사용하여 Linux 머신에 중요/보안 업데이트가 적용되지 않는 이유는 무엇인가요?

Linux 컴퓨터에 업데이트를 배포할 때 업데이트 분류를 선택할 수 있습니다. 이 옵션은 지정된 조건에 맞는 업데이트를 필터링합니다. 자세한 내용은 [Linux 업데이트 분류를 참조하세요.](./update-management/manage-updates-for-vm.md#linux-update-classifications)

## <a name="can-update-management-deploy-updates-across-azure-tenants"></a>업데이트 관리 Azure 테넌트 간에 업데이트를 배포할 수 있나요?

예. 지침은 Azure [테넌트 간에 업데이트 배포를 참조하세요.](./update-management/deploy-updates.md#deploy-updates-across-azure-tenants)

## <a name="which-python-3-version-is-supported-in-azure-automation"></a>Azure Automation에서 지원되는 Python 3 버전은 무엇인가요?

클라우드 작업의 경우 Python 3.8이 지원됩니다. 서로 다른 버전에서 코드가 호환되는 경우에는 3.x 버전의 스크립트 및 패키지가 작동할 수 있습니다.

Windows Hybrid Runbook Worker의 하이브리드 작업의 경우 사용하려는 모든 3.x 버전을 설치하도록 선택할 수 있습니다. Linux Hybrid Runbook Workers의 하이브리드 작업의 경우 머신에 설치된 Python 3 버전에 따라 DSC OMSConfig 및 Linux Hybrid Worker를 실행합니다. 버전 3.6을 설치할 것을 권장하지만 Python 3 버전 간에 호환성이 손상되는 메서드 서명 또는 계약 변경이 없다면 다른 버전도 사용할 수 있습니다.

## <a name="can-python-2-and-python-3-runbooks-run-in-same-automation-account"></a>Python 2 및 Python 3 Runbook을 동일한 Automation 계정에서 실행할 수 있나요?

예. 동일한 Automation 계정에서 Python 2 및 Python 3 Runbook을 사용하는 데에는 제한이 없습니다.  

## <a name="what-is-the-plan-for-migrating-existing-python-2-runbooks-and-packages-to-python-3"></a>기존 Python 2 Runbook 및 패키지를 Python 3으로 마이그레이션하기 위한 계획은 무엇인가요?

Azure Automation은 Python 2 Runbook 및 패키지를 Python 3으로 마이그레이션할 계획이 없습니다. 이 마이그레이션을 직접 수행해야 합니다. 기존 및 새로운 Python 2 Runbook 및 패키지는 계속 작동합니다.

## <a name="what-packages-are-supported-by-default-in-python-3-environment"></a>Python 3 환경에서 기본적으로 지원되는 패키지는 무엇인가요?

Azure 패키지 4.0.0. 자세한 내용은 [Python 3 패키지 관리를 참조하세요.](python-3-packages.md)

## <a name="what-if-i-run-a-python-3-runbook-that-references-a-python-2-package-or-the-other-way-around"></a>Python 2 패키지를 참조하는 Python 3 Runbook을 실행하거나 그 반대로 실행하는 경우 어떻게 되나요?

Python 2와 Python 3에는 서로 다른 실행 환경이 있습니다. Python 2 Runbook을 실행하는 동안 Python 2 패키지만 가져올 수 있으며 Python 3의 경우도 이와 유사합니다.

## <a name="how-do-i-differentiate-between-python-2-and-python-3-runbooks-and-packages"></a>Python 2와 Python 3 Runbook 및 패키지를 어떻게 구분하나요?

Python 3은 Python 2와 Python 3 Runbook을 구분하는 새로운 Runbook 정의입니다. 마찬가지로 Python 3 패키지에 다른 패키지 종류가 도입되었습니다.

## <a name="how-does-a-hybrid-runbook-worker-know-which-version-of-python-to-run-when-both-python2-and-python3-are-installed"></a>Hybrid Runbook Worker는 Python 2 및 Python 3이 둘 다 설치된 경우 실행할 Python 버전을 어떻게 알 수 있나요?

Windows Runbook Worker의 경우 Python 2 Runbook을 실행할 때 먼저 환경 변수 `PYTHON_2_PATH`를 찾고 유효한 실행 파일을 가리키는지 유효성을 검사합니다. 예를 들어, 설치 폴더가 `C:\Python2`이면 `C:\Python2\python.exe`가 유효한 경로인지 확인합니다. 해당 환경 변수가 없으면 `PATH` 환경 변수를 찾아서 비슷한 확인을 수행합니다.

Python 3의 경우 먼저 `PYTHON_3_PATH` 환경 변수를 찾은 다음, `PATH` 환경 변수로 대체합니다.

[여러 Python 버전](automation-runbook-types.md#multiple-python-versions)을 참조하세요.

## <a name="how-does-a-hybrid-runbook-worker-locate-the-python-interpreter"></a>Hybrid Runbook Worker는 Python 인터프리터를 어떻게 찾나요?

Python 모듈 찾기는 앞에서 설명한 대로 환경 변수를 통해 제어합니다.

## <a name="is-python-3-supported-in-source-control"></a>원본 제어에서 Python 3이 지원되나요?

아니요. 원본 제어는 현재 Python 3에서 지원되지 않습니다. 기본적으로 Python Runbook은 Python 2 Runbook으로 동기화됩니다.

## <a name="how-can-a-runbook-author-know-what-python-packages-are-available-in-an-azure-sandbox"></a>Runbook 작성자는 Azure 샌드박스에서 사용할 수 있는 Python 패키지를 어떻게 알 수 있나요?

[샌드박스에서 사용 가능한 패키지 식별을](python-3-packages.md#identify-available-packages-in-sandbox)참조하세요.

## <a name="how-can-a-runbook-author-set-which-version-of-a-package-module-to-be-used-if-there-are-multiple-modules"></a>Runbook 작성자는 여러 모듈이 있는 경우 사용할 패키지 모듈의 버전을 어떻게 설정할 수 있나요?

[Python 3 패키지 관리를 참조하세요.](python-3-packages.md)

## <a name="next-steps"></a>다음 단계

질문에 대한 대답이 여기에 없으면 다음 출처에서 추가 질문 및 대답을 참조할 수 있습니다.

- [Azure Automation](/answers/topics/azure-automation.html)
- [피드백 포럼](https://feedback.azure.com/forums/905242-update-management)
