---
title: Azure Stream Analytics에 대한 지속적인 통합 및 배포
description: 이 문서에서는 Azure Stream Analytics에 대한 CI/CD(지속적인 통합 및 배포) 파이프라인의 개요를 제공합니다.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: aede2f01b7a38637767c6278e913b9bb2cf1a950
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113586134"
---
# <a name="continuous-integration-and-deployment-cicd-for-azure-stream-analytics"></a>Azure Stream Analytics에 대한 CI/CD(지속적인 통합 및 배포)

소스 제어 통합을 사용하여 Azure Stream Analytics 작업을 지속적으로 배포할 수 있습니다. 소스 제어 통합을 사용하면 코드 업데이트가 Azure에 리소스 배포를 트리거하는 워크플로를 사용할 수 있습니다. 이 문서에서는 CI/CD(지속적인 통합 및 배포) 파이프라인을 만들기 위한 기본 단계에 대해 간략하게 설명합니다.

Azure Stream Analytics를 처음 접하는 경우 [Azure Stream Analytics 빠른 시작](stream-analytics-quick-create-portal.md)을 시작합니다.

## <a name="create-a-cicd-pipeline"></a>CI/CD 파이프라인 만들기

이 안내선의 단계를 수행하여 Stream Analytics에 대한 CI/CD 파이프라인을 만듭니다.

1. Azure Stream Analytics 쿼리를 개발합니다.

   [Visual Studio Code](./quick-create-visual-studio-code.md) 또는 [Visual Studio](stream-analytics-quick-create-vs.md)용 Azure Stream Analytics 도구를 사용하여 [쿼리를 로컬로 개발하고 테스트](develop-locally.md)합니다. 로컬 프로젝트로 [기존 작업을 내보낼](visual-studio-code-explore-jobs.md#export-a-job-to-a-local-project) 수도 있습니다.

2. Git 리포지토리와 같은 소스 제어 시스템에 Azure Stream Analytics 프로젝트를 커밋합니다.

3. [Azure Stream Analytics CI/CD 도구](cicd-tools.md)를 사용하여 프로젝트를 빌드하고 배포를 위한 Azure Resource Manager 템플릿을 생성합니다.

4. 품질 회귀를 위해 [자동화된 스크립트 테스트](cicd-tools.md#automated-test)를 실행합니다.

5. Azure에 자동으로 [작업을 배포](cicd-tools.md#deploy-to-azure)합니다.

## <a name="auto-build-test-and-deploy"></a>자동 빌드, 테스트 및 배포

명령줄 및 [Azure Stream Analytics CI/CD 도구](cicd-tools.md)를 사용하여 자동 빌드, 테스트 및 배포할 수 있습니다. [Azure Pipelines](set-up-cicd-pipeline.md)에서 CI/CD 파이프라인을 설정할 수도 있습니다. 파이프라인 관리, 시각화 및 트리거와 같은 고급 기능을 지원하는 Azure Pipelines입니다.

## <a name="next-steps"></a>다음 단계

* [CI/CD 도구를 사용하여 Azure Stream Analytics 작업의 빌드, 테스트 및 배포 자동화](cicd-tools.md)
* [Azure Pipelines를 사용하여 Stream Analytics 작업에 대한 CI/CD 파이프라인 설정](set-up-cicd-pipeline.md)
