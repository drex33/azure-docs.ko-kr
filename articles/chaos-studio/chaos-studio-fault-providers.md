---
title: 비정상 스튜디오에 대해 지원 되는 리소스 유형 및 역할 할당
description: 지원 되는 리소스 유형 목록 및 실험에서 해당 리소스 유형에 대 한 오류를 실행 하도록 설정 하는 데 필요한 역할 할당을 이해 합니다.
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: 10bb9b826d5c749b283dd38caa77ae03b7695b8e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053762"
---
# <a name="supported-resource-types-and-role-assignments-for-chaos-studio"></a>비정상 스튜디오에 대해 지원 되는 리소스 유형 및 역할 할당

다음은 오류에 대해 지원 되는 리소스 유형, 대상 유형 및 해당 유형의 리소스에 실험 권한을 제공할 때 사용할 제안 역할입니다.

| 리소스 종류 | 대상 이름 | 제안 된 역할 할당 |
| - | - | - |
| VirtualMachines/(에이전트 기반) | Microsoft-Agent | 판독기 |
| VirtualMachineScaleSets/(에이전트 기반) | Microsoft-Agent | 판독기 |
| Microsoft. Compute/virtualMachines (서비스-직접) | Microsoft-VirtualMachine | 가상 머신 참가자 |
| Microsoft. Compute/virtualMachineScaleSets (서비스-직접) | Microsoft-VirtualMachineScaleSet | 가상 머신 참가자 |
| Microsoft DocumentDb/databaseAccounts (CosmosDB, service-direct) | Microsoft-CosmosDB | Cosmos DB 운영자 |
| ContainerService/managedClusters (서비스 직접) | Microsoft-AzureKubernetesServiceChaosMesh | Azure Kubernetes Service 클러스터 사용자 역할 |
| Microsoft. Network/networkSecurityGroups (서비스-직접) | Microsoft-NetworkSecurityGroup | 네트워크 참가자 |
| Microsoft. Cache/Redis (서비스-직접) | Microsoft-AzureCacheForRedis | Redis Cache 참가자 |
