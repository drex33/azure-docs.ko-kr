---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/20/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c9fa1f92a75e013e4b8661fea9bb2e51ae0e9d01
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122605167"
---
|Name |Description |정책 |버전 |
|---|---|---|---|
|[안전하지 않은 암호 보안 설정이 있는 머신 감사](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettingsAINE.json) |이 이니셔티브는 정책 요구 사항을 배포하고 안전하지 않은 암호 보안 설정이 있는 머신을 감사합니다. 게스트 구성 정책에 대한 자세한 내용은 [https://aka.ms/gcpol](../../../../articles/governance/policy/concepts/guest-configuration.md)을 방문하세요. |9 |1.0.0 |
|[필수 구성 요소를 배포하여 가상 머신에서 게스트 구성 정책을 사용하도록 설정](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |이 이니셔티브는 시스템 할당 관리 ID를 추가하고 게스트 구성 정책에서 모니터링할 수 있는 가상 머신에 플랫폼에 적합한 게스트 구성 확장을 배포합니다. 이는 모든 게스트 구성 정책의 필수 구성 요소이며 게스트 구성 정책을 사용하기 전에 정책 할당 범위에 할당해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../../../../articles/governance/policy/concepts/guest-configuration.md)을 방문하세요. |4 |1.0.0 |
|[\[미리 보기\]: Windows 머신은 Azure 컴퓨팅 보안 기준의 요구 사항을 충족해야 함](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |이 이니셔티브는 Azure 컴퓨팅 보안 기준을 충족하지 않는 설정으로 Windows 머신을 감사합니다. 자세한 내용은 [https://aka.ms/gcpol](../../../../articles/governance/policy/concepts/guest-configuration.md)을 참조하세요. |29 |2.0.1-preview |