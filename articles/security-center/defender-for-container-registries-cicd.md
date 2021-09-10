---
title: CI/CD 워크플로의 컨테이너 이미지에 대한 Azure Defender의 취약성 검사기
description: 컨테이너 레지스트리에 Azure Defender를 사용하여 CI/CD 워크플로에서 컨테이너 이미지를 검색하는 방법에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 05/25/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: eb7309f067c350eac0d9455767b137377caf588b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536111"
---
# <a name="identify-vulnerable-container-images-in-your-cicd-workflows"></a>CI/CD 워크플로에서 취약한 컨테이너 이미지 식별

이 페이지에서는 GitHub 워크플로의 일부로 Azure Container Registry 기반 컨테이너 이미지가 빌드될 때 통합된 취약성 검사기를 사용하여 해당 이미지를 검사하는 방법을 설명합니다.

검사기를 설정하려면 **컨테이너 레지스트리를 위한 Azure Defender** 와 CI/CD 통합을 사용하도록 설정해야 합니다. CI/CD 워크플로가 레지스트리에 이미지를 푸시하면 레지스트리 검사 결과와 CI/CD 검사 결과의 요약을 볼 수 있습니다.

CI/CD 검사의 결과는 Qualys의 기존 레지스트리 검사 결과를 보강한 결과입니다. Azure Defender의 CI/CD 검사 기능은 [Aqua Trivy](https://github.com/aquasecurity/trivy)에서 제공합니다.

GitHub 워크플로와 GitHub 실행 URL과 같은 추적 가능성 정보를 통해 취약한 이미지를 생성하는 워크플로를 식별할 수 있습니다.

> [!TIP]
> 레지스트리 검사에서 식별된 취약성은 CI/CD 검사의 결과와 다를 수 있습니다. 이러한 차이가 발생하는 이유 중 하나는 레지스트리 검사가 [연속적](defender-for-container-registries-introduction.md#when-are-images-scanned)인 반면, CI/CD 검사는 워크플로가 이미지를 레지스트리에 푸시하기 직전에 실행되기 때문입니다.

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:| **이러한 CI/CD 통합은 현재 미리 보기 상태입니다.**<br>비프로덕션 워크플로에서만 실험하는 것이 좋습니다.<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
|가격 책정:|**컨테이너 레지스터리용 Azure Defender** 의 요금은 [Security Center 가격 책정](https://azure.microsoft.com/pricing/details/security-center/)페이지의 정보에 따라 청구됩니다.|
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/no-icon.png"::: 국가/지역(Azure Government, Azure China 21Vianet)|
|||

## <a name="prerequisites"></a>사전 요구 사항

CI/CD 워크플로가 레지스트리에 이미지를 푸시할 때 이미지를 검색하려면 구독에서 사용하도록 설정된 **컨테이너 레지스트리용 Azure Defender** 가 있어야 합니다. 

## <a name="set-up-vulnerability-scanning-of-your-cicd-workflows"></a>CI/CD 워크플로의 취약성 검사 설정

GitHub 워크플로에서 이미지의 취약성 검사를 사용하도록 설정하려면 다음을 수행합니다.

[1단계. Security Center에서 CI/CD 통합 사용 설정](#step-1-enable-the-cicd-integration-in-security-center)

[2단계. GitHub 워크플로에 필요한 행 추가](#step-2-add-the-necessary-lines-to-your-github-workflow-and-perform-a-scan)

### <a name="step-1-enable-the-cicd-integration-in-security-center"></a>1단계. Security Center에서 CI/CD 통합 사용 설정

1. Security Center의 사이드바에서 **가격 책정 및 설정** 을 선택합니다.
1. 관련 구독을 선택합니다.
1. 해당 구독에 대한 설정 페이지의 사이드바에서 **통합** 을 선택합니다.
1. 창이 나타나면 워크플로에서 CI/CD 검색 결과를 푸시할 Application Insights 계정을 선택합니다.
1. 인증 토큰과 연결 문자열을 GitHub 워크플로에 복사합니다.

    :::image type="content" source="./media/defender-for-container-registries-cicd/enable-cicd-integration.png" alt-text="GitHub 워크플로에서 컨테이너 이미지의 취약성 검사에 대한 CI/CD 통합 사용 설정." lightbox="./media/defender-for-container-registries-cicd/enable-cicd-integration.png":::

    > [!IMPORTANT]
    > 인증 토큰과 연결 문자열은 수집된 보안 원격 분석과 구독의 리소스를 상호 연결하는 데 사용됩니다. 이러한 매개 변수에 잘못된 값을 사용하는 경우 원격 분석이 중단됩니다.

### <a name="step-2-add-the-necessary-lines-to-your-github-workflow-and-perform-a-scan"></a>2단계. GitHub 워크플로에 필요한 행을 추가하고 검사를 수행합니다.

1. GitHub 워크플로에서 다음과 같이 CI/CD 검사를 사용하도록 설정합니다.

    > [!TIP]
    > 아래와 같이 YAML 파일에서 참조할 수 있도록 리포지토리에 두 개의 비밀을 만드는 것이 좋습니다. 자신만의 명명 규칙에 따라 비밀의 이름을 지정할 수 있습니다. 이 예시에서는 비밀이 **AZ_APPINSIGHTS_CONNECTION_STRING** 및 **AZ_SUBSCRIPTION_TOKEN** 으로 참조됩니다.


    ```yml
    - run: |
      echo "github.sha=$GITHUB_SHA"
      docker build -t githubdemo1.azurecr.io/k8sdemo:${{ github.sha }}
    
    - uses: Azure/container-scan@v0 
      name: Scan image for vulnerabilities
      id: container-scan
      continue-on-error: true
      with:
        image-name: githubdemo1.azurecr.io/k8sdemo:${{ github.sha }} 
    
    - name: Push Docker image - githubdemo1.azurecr.io/k8sdemo:${{ github.sha }}
      run: |
      docker push githubdemo1.azurecr.io/k8sdemo:${{ github.sha }}
    
    - name: Post logs to appinsights
      uses: Azure/publish-security-assessments@v0
      with: 
        scan-results-path: ${{ steps.container-scan.outputs.scan-report-path }}
        connection-string: ${{ secrets.AZ_APPINSIGHTS_CONNECTION_STRING }}
        subscription-token: ${{ secrets.AZ_SUBSCRIPTION_TOKEN }} 
    ```

1. 선택한 컨테이너 레지스트리에 이미지를 푸시할 워크플로를 실행합니다. 이미지가 레지스트리로 푸시되면 레지스트리 검사가 실행되고 Azure Security Center 내 레지스트리 검사 결과와 함께 CI/CD 검사 결과를 확인할 수 있습니다.

1. [CI/CD 검사 결과를 확인](#view-cicd-scan-results)합니다.

## <a name="view-cicd-scan-results"></a>CI/CD 검사 결과 확인

1. 결과를 보려면 **권장 사항** 페이지로 이동합니다. 문제가 발견되면 **Azure Container Registry 이미지의 취약성 문제를 해결** 하기 위한 권장 사항을 확인할 수 있습니다.

    ![문제를 수정하기 위한 권장 사항.](media/monitor-container-security/acr-finding.png)

1. 권장 사항을 선택합니다. 

    추가 정보를 포함하는 권장 사항 세부 정보 페이지가 열립니다. 이 정보에는 취약한 이미지를 포함하는 레지스트리 목록(‘영향을 받는 리소스’)과 수정 문제 해결 단계가 포함됩니다. 

1. **영향을 받는 리소스** 목록을 열고 비정상 레지스트리를 선택하여 취약한 이미지를 포함하는 레지스트리 내 리포지토리를 확인합니다.

    :::image type="content" source="media/defender-for-container-registries-cicd/select-registry.png" alt-text="비정상 레지스트리 선택.":::

    영향을 받는 리포지토리 목록과 함께 레지스트리 세부 정보 페이지가 열립니다.

1. 특정 리포지토리를 선택하여 취약한 이미지가 있는 리포지토리를 확인합니다.

    :::image type="content" source="media/defender-for-container-registries-cicd/select-repository.png" alt-text="비정상 리포지토리 선택.":::

    리포지토리 세부 정보가 열립니다. 결과의 심각도 평가와 함께 취약한 이미지가 나열됩니다.

1. 취약성을 확인할 이미지를 선택합니다.

    :::image type="content" source="media/defender-for-container-registries-cicd/select-image.png" alt-text="비정상 이미지 선택.":::

    선택한 이미지에 대한 결과 목록이 열립니다.

    :::image type="content" source="media/defender-for-container-registries-cicd/cicd-scan-results.png" alt-text="이미지 검사 결과.":::

1. 취약한 이미지를 푸시하는 GitHub 워크플로에 대한 자세한 내용을 보려면 해당 정보 버블을 선택합니다.

    :::image type="content" source="media/defender-for-container-registries-cicd/cicd-findings.png" alt-text="특정 GitHub 분기 및 커밋에 대한 CI/CD 결과.":::



## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Defender](azure-defender.md)에 대해 자세히 알아보세요.
