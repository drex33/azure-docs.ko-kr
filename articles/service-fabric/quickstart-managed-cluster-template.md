---
title: Azure Resource Manager를 사용하여 Service Fabric 관리형 클러스터 배포
description: Azure Resource Manager 템플릿을 통해 Service Fabric 관리형 클러스터의 만드는 방법을 알아봅니다.
ms.topic: quickstart
ms.date: 5/10/2021
ms.custom: mode-other
ms.openlocfilehash: 7dd09d35c8a1ff7b9e3d17dfcdc9eaf0f37ec1e7
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133043442"
---
# <a name="quickstart-deploy-a-service-fabric-managed-cluster-with-an-azure-resource-manager-template"></a>빠른 시작: Azure Resource Manager 템플릿을 통해 Service Fabric 관리형 클러스터 배포

Service Fabric 관리형 클러스터는 배포 및 클러스터 관리 환경을 간소화하는 Azure Service Fabric 클러스터 리소스 모델의 진화입니다. Service Fabric 관리형 클러스터는 Service Fabric 클러스터를 구성하는 모든 기본 리소스를 배포하지 않고 단일 Service Fabric 클러스터 리소스를 배포할 수 있는 완전히 캡슐화된 리소스입니다. 이 문서에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure에서 테스트용 Service Fabric 관리형 클러스터를 배포하는 방법을 설명합니다.

이 자습서에서 배포된 3 노드 기본 SKU 클러스터는 프로덕션 워크로드 대신 교육용으로 사용하기 위한 목적으로만 사용됩니다. 자세한 내용은 [Service Fabric 관리형 클러스터 SKU](overview-managed-cluster.md#service-fabric-managed-cluster-skus)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 시작하기 전에

* Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 샘플 - Service Fabric 클러스터 템플릿](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Basic-SKU-1-NT)에서 나온 것입니다.

## <a name="create-a-client-certificate"></a>클라이언트 인증서 만들기

Service Fabric 관리형 클러스터는 클라이언트 인증서를 액세스 제어를 위한 키로 사용합니다. 클러스터에 대한 액세스 제어에 사용할 클라이언트 인증서가 이미 있는 경우 이 단계를 건너뛸 수 있습니다.

새 클라이언트 인증서를 만들어야 하는 경우 [Azure Key Vault에서 인증서 설정 및 검색](../key-vault/certificates/quick-create-portal.md)의 단계를 수행합니다.

다음 단계에서 템플릿을 배포하는 데 필요하므로 인증서 지문을 기록해 둡니다.

## <a name="deploy-the-template"></a>템플릿 배포

1. 다음 이미지를 선택하고 Azure에 로그인하여 템플릿을 엽니다.

      [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fservice-fabric-cluster-templates%2Fmaster%2FSF-Managed-Basic-SKU-1-NT%2Fazuredeploy.json)

2. 다음 값을 선택 또는 입력

    이 빠른 시작에서는 다음 템플릿 매개 변수에 대한 사용자 고유의 값을 제공합니다.

    * **구독**: Azure 구독을 선택합니다.
    * **리소스 그룹**: **새로 만들기** 를 선택합니다. 리소스 그룹에 대한 고유 이름(예: *myResourceGroup*)을 입력한 다음, **확인** 을 선택합니다.
    * **위치**: 위치를 선택합니다.
    * **클러스터 이름**: 클러스터에 대한 고유 이름(예: *mysfcluster*)을 입력합니다.
    * **관리자 사용자 이름**: 클러스터의 기본 VM에서 RDP에 사용할 관리자에 대한 이름을 입력합니다.
    * **관리자 암호**: 클러스터의 기본 VM에서 RDP에 사용할 관리자에 대한 암호를 입력합니다.
    * **클라이언트 인증서 지문**: 클러스터에 액세스하는 데 사용할 클라이언트 인증서의 지문을 제공합니다. 인증서가 없는 경우 [인증서 설정 및 검색](../key-vault/certificates/quick-create-portal.md)을 수행하여 자체 서명된 인증서를 만듭니다.
    * **노드 유형 이름**: *nt1* 과 같이 노드 형식에 대한 고유한 이름을 입력합니다.
    * **위에 명시된 사용 약관에 동의함**: 동의하려면 이 확인란을 선택합니다. 

3. **구매** 를 선택합니다.

4. 관리형 Service Fabric 클러스터를 배포하는 데 몇 분 정도 걸립니다. 다음 단계로 이동하기 전에 배포가 성공적으로 완료될 때까지 기다립니다.

## <a name="validate-the-deployment"></a>배포 유효성 검사

### <a name="review-deployed-resources"></a>배포된 리소스 검토

배포가 완료되면 출력에서 Service Fabric Explorer 값을 찾고 웹 브라우저에서 주소를 열어 Service Fabric Explorer에서 클러스터를 확인합니다. 인증서를 요청하는 메시지가 표시되면 템플릿에서 클라이언트 지문이 제공된 인증서를 사용합니다.

> [!NOTE]
> Azure Portal의 리소스 그룹 배포 탭에서 배포의 출력을 찾을 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 하지 않은 경우 Service Fabric 관리형 클러스터에 대한 리소스 그룹을 삭제합니다. 포털을 통해 리소스 그룹을 삭제하려면:

1. 포털 맨 위에 있는 *검색* 상자에 리소스 그룹의 이름을 입력합니다. 검색 결과에 이 빠른 시작에서 사용된 리소스 그룹이 표시되면 선택합니다.
2. **리소스 그룹 삭제** 를 선택합니다.
3. **리소스 그룹 이름 입력** 상자에 리소스 그룹 이름을 입력하고 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 관리형 Service Fabric 클러스터를 배포했습니다. 클러스터의 크기를 조정하는 방법을 자세히 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [Service Fabric 관리형 클러스터 확장 방법](tutorial-managed-cluster-scale.md)
