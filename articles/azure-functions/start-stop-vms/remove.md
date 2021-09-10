---
title: VM v2 시작/중지(미리 보기) 제거 개요
description: 이 문서에서는 VM v2 시작/중지(미리 보기) 기능을 제거하는 방법을 설명합니다.
services: azure-functions
ms.subservice: start-stop-vms
ms.date: 06/25/2021
ms.topic: conceptual
ms.openlocfilehash: 1a22faba480eb9c130cc7c883abfc3e53ef58fe5
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112970160"
---
# <a name="how-to-remove-startstop-vms-v2-preview"></a>VM v2 시작/중지(미리 보기)를 제거하는 방법

Azure VM의 실행 상태를 관리하기 위해 VM v2 시작/중지(미리 보기) 기능을 사용하도록 설정한 후에도 사용을 중지할 수 있습니다. VM v2 시작/중지(미리 보기)를 지원하기 위해 다음 리소스를 저장하는 전용 리소스 그룹을 삭제하여 이 기능을 제거할 수 있습니다.

- Azure Functions 애플리케이션
- Azure Logic Apps의 일정
- Application Insights 인스턴스
- Azure Storage 계정

> [!NOTE]
> 배포 중에 문제가 발생하거나 VM 시작/중지 v2(미리 보기)를 사용할 때 문제가 발생하거나 관련 질문이 있는 경우 [GitHub](https://github.com/microsoft/startstopv2-deployments/issues)에서 문제를 제출할 수 있습니다. 이 미리 보기 버전에서는 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)에서 Azure 지원 인시던트를 제출할 수 없습니다. 

## <a name="delete-the-dedicated-resource-group"></a>전용 리소스 그룹 삭제

리소스 그룹을 삭제하려면 [Azure Resource Manager 리소스 그룹 및 리소스 삭제](../../azure-resource-manager/management/delete-resource-group.md) 문서에 설명된 단계를 따르세요.

## <a name="next-steps"></a>다음 단계

이 기능을 다시 배포하려면 [v2 시작/중지 배포](deploy.md)(미리 보기)를 참조하세요.