---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/27/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 971c56497823050933bd6cc48b7aad8e631936bd
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123109683"
---
|Name |설명 |정책 |버전 |
|---|---|---|---|
|[\[미리 보기\]: 가상 머신에서 Azure Defender for SQL 에이전트 구성](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_AzureDefenderForSql.json) |Azure Monitor 에이전트가 설치된 위치에 Azure Defender for SQL 에이전트를 자동으로 설치하도록 가상 머신을 구성합니다. Security Center는 에이전트에서 이벤트를 수집하고 이를 사용하여 보안 경고 및 맞춤형 강화 작업(권장 사항)을 제공합니다. 머신과 동일한 지역에 리소스 그룹 및 Log Analytics 작업 영역을 만듭니다. 이 정책은 일부 지역의 VM에만 적용됩니다. |2 |1.0.0-preview |
|[Linux 가상 머신에 Azure Monitor 에이전트를 구성하고 데이터 수집 규칙에 연결](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AMCS_LinuxPlatform_EnableDCR.json) |가상 머신 이미지(OS) 및 위치가 정의된 목록에 있고 에이전트가 설치되지 않은 경우 Linux 가상 머신용 Azure Monitor 에이전트를 배포합니다.  그런 다음, 연결을 배포하여 가상 머신을 지정된 데이터 수집 규칙에 연결합니다. OS 이미지 목록은 향후 지원이 증가됨에 따라 업데이트됩니다. |2 |1.0.0 |
|[Windows 가상 머신에 Azure Monitor 에이전트를 구성하고 데이터 수집 규칙에 연결](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AMCS_WindowsPlatform_EnableDCR.json) |가상 머신 이미지(OS) 및 위치가 정의된 목록에 있고 에이전트가 설치되지 않은 경우 Windows 가상 머신용 Azure Monitor 에이전트를 배포합니다.  그런 다음, 연결을 배포하여 가상 머신을 지정된 데이터 수집 규칙에 연결합니다. OS 이미지 목록은 향후 지원이 증가됨에 따라 업데이트됩니다. |2 |1.0.0 |
|[\[미리 보기\]: 가상 머신에 Azure Monitor 및 Azure Security 에이전트를 자동으로 설치하도록 머신 구성](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Azure Monitor 및 Azure Security 에이전트를 자동으로 설치하도록 머신을 구성합니다. Security Center는 에이전트에서 이벤트를 수집하고 이를 사용하여 보안 경고 및 맞춤형 강화 작업(권장 사항)을 제공합니다. 감사 레코드를 저장할 머신과 동일한 지역에 리소스 그룹 및 Log Analytics 작업 영역을 만듭니다. 이 정책은 일부 지역의 VM에만 적용됩니다. |6 |2.0.0-preview |
|[Virtual Machine Scale Sets에 Azure Monitor 사용](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |지정된 범위(관리 그룹, 구독 또는 리소스 그룹)에서 Virtual Machine Scale Sets에 대해 Azure Monitor를 사용하도록 설정합니다. Log Analytics 작업 영역을 매개 변수로 사용합니다. 참고: 확장 집합 upgradePolicy를 수동으로 설정한 경우 이에 대한 업그레이드를 호출하여 집합의 모든 VM에 확장을 적용해야 합니다. CLI에서 이는 az vmss update-instances입니다. |6 |1.0.1 |
|[VM용 Azure Monitor 사용](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |지정된 범위(관리 그룹, 구독 또는 리소스 그룹)에서 VM(가상 머신)에 대해 Azure Monitor를 사용하도록 설정합니다. Log Analytics 작업 영역을 매개 변수로 사용합니다. |10 |2.0.0 |
