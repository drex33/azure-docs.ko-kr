---
title: ARM 템플릿 및 Bicep 파일 문제 해결 개요
description: ARM 템플릿(Azure Resource Manager 템플릿) 및 Bicep 파일을 통해 Azure 리소스 배포에 대한 문제 해결을 설명합니다.
ms.topic: overview
ms.date: 10/26/2021
ms.custom: troubleshooting-overview
ms.openlocfilehash: 604ba2be3fc1adcf22df9774400237a1b07f93e1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101404"
---
# <a name="what-is-deployment-troubleshooting"></a>배포 문제 해결이란?

Bicep 파일 또는 ARM 템플릿(Azure Resource Manager 템플릿)을 배포하는 경우 오류가 발생할 수 있습니다. 이 설명서를 통해 오류에 대한 가능한 솔루션을 찾을 수 있습니다.

## <a name="error-types"></a>오류 유형

**유효성 검사 오류** 와 **배포 오류** 의 두 가지 유형의 오류가 발생할 수 있습니다.

배포가 시작되기 전에 유효성 검사 오류가 발생합니다. 이러한 오류는 현재 Azure 환경과 상호 작용하지 않고도 확인할 수 있습니다. 예를 들어 유효성 검사를 통해 배포를 시작하기 전에 함수에 대한 구문 오류 또는 누락된 인수를 인식할 수 있습니다.

배포 오류는 배포를 시도하고 Azure 환경과 상호 작용해야만 확인할 수 있습니다. 예를 들어 VM(가상 머신)에는 NIC(네트워크 인터페이스 카드)가 필요합니다. VM을 배포할 때 NIC가 없으면 배포 오류가 발생합니다.

## <a name="troubleshooting-tools"></a>문제 해결 도구

배포 전에 구문 오류를 식별하려면 최신 버전의 [Visual Studio Code](https://code.visualstudio.com)를 사용합니다. 다음 중 최신 버전을 설치합니다.

* [Bicep 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep)
* [Azure Resource Manager 도구 확장](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

배포 문제를 해결하려면 리소스 공급자의 속성 또는 API 버전에 대해 알아보는 것이 좋습니다. 자세한 내용은 [Bicep 및 ARM 템플릿을 사용하여 리소스 정의](/azure/templates)를 참조하세요.

템플릿 개발에 대한 모범 사례를 따르려면 다음 중 하나를 사용합니다.

* [Bicep Linter](../bicep/linter.md)
* [ARM 템플릿 테스트 도구 키트](../templates/test-toolkit.md)

배포할 때 리소스 그룹의 **배포** 또는 **활동 로그** 에서 Azure Portal 오류의 원인을 찾을 수 있습니다. Azure PowerShell을 사용하는 경우 [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation) 및 [Get-AzActivityLog](/powershell/module/az.monitor/get-azactivitylog)와 같은 명령을 사용합니다. Azure CLI의 경우 [az deployment operation group](/cli/azure/deployment/operation/group) 및 [az monitor activity-log list](/cli/azure/monitor/activity-log#az_monitor_activity_log_list)와 같은 명령을 사용합니다.

## <a name="next-steps"></a>다음 단계

- 오류 코드를 기반으로 하는 솔루션은 [일반적인 Azure 배포 오류 문제 해결](common-deployment-errors.md)을 참조하세요.
- 오류 코드 찾기에 대한 소개는 [빠른 시작: ARM 템플릿 배포 문제 해결](quickstart-troubleshoot-arm-deployment.md) 또는 [빠른 시작: Bicep 파일 배포 문제 해결](quickstart-troubleshoot-bicep-deployment.md)을 참조하세요.
