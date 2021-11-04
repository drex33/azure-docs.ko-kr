---
title: '자습서: 게이트웨이 부하 분산 장치 만들기 - Azure Portal'
titleSuffix: Azure Load Balancer
description: 이 자습서를 사용하여 Azure Portal에서 게이트웨이 부하 분산 장치를 만드는 방법을 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: template-tutorial, ignite-fall-2021
ms.openlocfilehash: 918f2da42a57752db2373e65665804d0a49ad31a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101005"
---
# <a name="tutorial-create-a-gateway-load-balancer-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 게이트웨이 부하 분산 장치 만들기

Azure Load Balancer는 표준, 기본 및 게이트웨이 SKU로 구성됩니다. 게이트웨이 부하 분산 장치는 NVA(네트워크 가상 어플라이언스)를 사용자가 인식하지 못하는 새 삽입하는 데 사용됩니다. NVA의 고성능 및 높은 확장성이 필요한 시나리오의 경우 게이트웨이 부하 분산 장치를 사용합니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 미리 보기 기능을 등록합니다.
> * 가상 네트워크를 만듭니다.
> * 네트워크 보안 그룹을 만듭니다.
> * 게이트웨이 부하 분산 장치를 만듭니다.
> * 부하 분산 장치 프런트 엔드를 게이트웨이 부하 분산 장치에 연결합니다.

> [!IMPORTANT]
> 게이트웨이 Azure Load Balancer는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 기존 공용 표준 SKU Azure Load Balancer 부하 분산 장치 만들기에 대한 자세한 내용은 **[Azure Portal을 사용하여 공용 부하 분산 장치 만들기](quickstart-load-balancer-standard-public-portal.md)** 를 참조하세요.
    - 이 자습서에서는 예제의 부하 분산 장치 이름을 **myLoadBalancer** 로 지정합니다.

## <a name="register-preview-feature"></a>미리 보기 기능 등록

공개 미리 보기의 일부로 공급자를 Azure 구독에 등록해야 합니다. 다음 PowerShell 또는 Azure CLI 예제를 사용하여 구독을 활성화합니다.

### <a name="powershell"></a>PowerShell

[Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature)를 사용하여 **AllowGatewayLoadBalancer** 공급자 기능을 등록합니다.

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowGatewayLoadBalancer

```

[Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider)를 사용하여 **Microsoft.Network** 리소스 공급자를 등록합니다.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Network

```

### <a name="azure-cli"></a>Azure CLI

[az feature register](/cli/azure/feature#az_feature_register)를 사용하여 **AllowGatewayLoadBalancer** 공급자 기능을 등록합니다.

```azurecli-interactive
  az feature register \
    --name AllowGatewayLoadBalancer \
    --namespace Microsoft.Network
```

[az provider register](/cli/azure/provider#az_provider_register)를 사용하여 **Microsoft.Network** 리소스 공급자를 등록합니다.

```azurecli-interactive
  az provider register \
    --namespace Microsoft.Network
```

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://preview.portal.azure.com](https://preview.portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="create-virtual-network"></a>가상 네트워크 만들기

가상 네트워크는 게이트웨이 부하 분산 장치의 백 엔드 풀에 있는 리소스에 필요합니다. 

1. 포털 맨 위에 있는 검색 상자에 **가상 네트워크** 를 입력합니다. 검색 결과에서 **가상 네트워크** 를 선택합니다.

2. **가상 네트워크** 에서 **+ 만들기** 를 선택합니다.

3. **가상 네트워크 만들기** 의 **기본** 탭에서 다음 정보를 입력하거나 선택합니다.

    | **설정**          | **값**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **프로젝트 세부 정보**  |                                                                 |
    | Subscription     | Azure 구독 선택                                  |
    | 리소스 그룹   | **새로 만들기** 를 선택합니다. </br> **이름** 에 **TutorGwLB-rg** 를 입력합니다. </br> **확인** 을 선택합니다. |
    | **인스턴스 세부 정보** |                                                                 |
    | 속성             | **myVNet** 입력                                    |
    | 지역           | **미국 동부** 를 선택합니다. |

4. **IP 주소** 탭을 선택하거나 페이지 하단의 **다음: IP 주소** 단추를 선택합니다.

5. **IP 주소** 탭에서 다음 정보를 입력합니다.

    | 설정            | 값                      |
    |--------------------|----------------------------|
    | IPv4 주소 공간 | **10.1.0.0/16** 입력 |

6. **서브넷 이름** 아래에서 **기본값** 이라는 단어를 선택합니다.

7. **서브넷 편집** 에서 다음 정보를 입력합니다.

    | 설정            | 값                      |
    |--------------------|----------------------------|
    | 서브넷 이름 | **myBackendSubnet** 입력 |
    | 서브넷 주소 범위 | **10.1.0.0/24** 입력 |

8. **저장** 을 선택합니다.

9. **보안** 탭을 선택합니다.

10. **BastionHost** 에서 **사용** 을 선택합니다. 다음 정보를 입력합니다.

    | 설정            | 값                      |
    |--------------------|----------------------------|
    | Bastion 이름 | **myBastionHost** 입력 |
    | AzureBastionSubnet 주소 공간 | **10.1.1.0/27** 입력 |
    | 공용 IP 주소 | **새로 만들기** 를 선택합니다. </br> **이름** 에 대해 **myBastionIP** 를 입력합니다. </br> **확인** 을 선택합니다. |


11. **검토 + 만들기** 탭을 선택하거나 **검토 + 만들기** 단추를 선택합니다.

12. **만들기** 를 선택합니다.

## <a name="create-nsg"></a>NSG 만들기

다음 예제를 사용하여 네트워크 보안 그룹을 만듭니다. 이전에 만든 가상 네트워크의 네트워크 트래픽에 필요한 NSG 규칙을 구성합니다.

1. 포털 맨 위에 있는 검색 상자에 **네트워크 보안** 을 입력합니다. 검색 결과에서 **네트워크 보안 그룹** 을 선택합니다.

2. **+ 만들기** 를 선택합니다.

3. **네트워크 보안 그룹 만들기** 의 **기본 사항** 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** |   |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **TutorGwLB-rg** 를 선택합니다. |
    | **인스턴스 세부 정보** |   |
    | 속성 | **myNSG** 를 입력합니다. |
    | 지역 | **미국 동부** 를 선택합니다. |

4. **검토 + 만들기** 탭을 선택하거나 **검토 + 만들기** 단추를 선택합니다.

5. **만들기** 를 선택합니다.

6. 포털 맨 위에 있는 검색 상자에 **네트워크 보안** 을 입력합니다. 검색 결과에서 **네트워크 보안 그룹** 을 선택합니다.

7. **myNSG** 를 선택합니다.

8. **myNSG** 의 **설정** 에서 **인바운드 보안 규칙** 을 선택합니다.

9. **+추가** 를 선택합니다.

10. **인바운드 보안 규칙 추가** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 원본 | **모두**(기본값)를 그대로 둡니다. |
    | 원본 포트 범위 | **'*'** (기본값)을 그대로 둡니다. |
    | 대상 | **모두**(기본값)를 그대로 둡니다. |
    | 서비스 | **사용자 지정**(기본값)을 그대로 둡니다. |
    | 대상 포트 범위 | **'*'** 를 입력합니다. |
    | 프로토콜 | **임의** 를 선택합니다. |
    | 작업 | **허용**(기본값)을 그대로 둡니다. |
    | 우선 순위 | **100** 을 입력합니다. | 
    | Name | **myNSGRule-AllowAll-All** 을 입력합니다. |

11. **추가** 를 선택합니다.

12. **설정** 에서 **아웃바운드 보안 규칙** 을 선택합니다.

13. **+추가** 를 선택합니다.

14. **아웃바운드 보안 규칙 추가** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 원본 | **모두**(기본값)를 그대로 둡니다. |
    | 원본 포트 범위 | **'*'** (기본값)을 그대로 둡니다. |
    | 대상 | **모두**(기본값)를 그대로 둡니다. |
    | 서비스 | **사용자 지정**(기본값)을 그대로 둡니다. |
    | 대상 포트 범위 | **'*'** 를 입력합니다. |
    | 프로토콜 | **TCP** 를 선택합니다. |
    | 작업 | **허용**(기본값)을 그대로 둡니다. |
    | 우선 순위 | **100** 을 입력합니다. | 
    | Name | **myNSGRule-AllowAll-TCP-Out** 을 입력합니다. |

15. **추가** 를 선택합니다.

배포에 대한 NVA를 만들 때 이 NSG를 선택합니다.

## <a name="create-gateway-load-balancer"></a>게이트웨이 부하 분산 장치 만들기

이 섹션에서는 구성을 만들고 게이트웨이 부하 분산 장치를 배포합니다. 

1. 포털 맨 위에 있는 검색 상자에 **부하 분산 장치** 를 입력합니다. 검색 결과에서 **부하 분산 장치** 를 선택합니다.

2. **부하 분산 장치** 페이지에서 **만들기** 를 선택합니다.

3. **부하 분산 장치 만들기** 페이지의 **기본 사항** 탭에서 다음 정보를 입력하거나 선택합니다. 

    | 설정                 | 값                                              |
    | ---                     | ---                                                |
    | **프로젝트 세부 정보** |   |
    | Subscription               | 구독을 선택합니다.    |    
    | Resource group         | **TutorGwLB-rg** 를 선택합니다. |
    | **인스턴스 세부 정보** |   |
    | Name                   | **myLoadBalancer-gw** 를 입력합니다.                                   |
    | 지역         | **(미국) 미국 동부** 를 선택합니다.                                        |
    | Type          | **내부** 를 선택합니다.                                        |
    | SKU           | **게이트웨이** 를 선택합니다. |

    :::image type="content" source="./media/tutorial-gateway-portal/create-load-balancer.png" alt-text="표준 부하 분산 장치 만들기 기본 사항 탭의 스크린샷" border="true":::

4. 페이지 하단에서 **다음: 프런트 엔드 IP 구성** 을 선택합니다.

5. **프런트 엔드 IP 구성** 에서 **+ 프런트 엔드 IP 추가** 를 선택합니다.

6. **이름** 에 **MyFrontEnd** 를 입력합니다.

7. **서브넷** 에서 **myBackendSubnet** 을 선택합니다.

8. **할당** 에서 **동적** 을 선택합니다.

9. **추가** 를 선택합니다.

10. 페이지 하단에서 **다음: 백 엔드 풀** 을 선택합니다.

11. **백 엔드 풀** 탭에서 **+ 백 엔드 풀 추가** 를 선택합니다.

12. **백 엔드 풀 추가** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 이름 | **myBackendPool** 을 입력합니다. |
    | 백 엔드 풀 구성 | **NIC** 를 선택합니다. |
    | IP 버전 | **IPv4** 를 선택합니다. |
    | **게이트웨이 부하 분산 장치 구성** |   |
    | 형식 | **내부 및 외부** 를 선택합니다. |
    | 내부 포트 | **10800**(기본값)을 그대로 둡니다. |
    | 내부 식별자 | **800**(기본값)을 그대로 둡니다. |
    | 외부 포트 | **10801**(기본값)을 그대로 둡니다. |
    | 외부 식별자 | **801**(기본값)을 그대로 둡니다. |

13. **추가** 를 선택합니다.

14. 페이지 하단에 있는 **다음: 인바운드 규칙** 단추를 선택합니다.

15. **인바운드 규칙** 탭의 **부하 분산 규칙** 에서 **+ 부하 분산 규칙 추가** 를 선택합니다.

16. **부하 분산 규칙 추가** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | Name | **myLBRule** 을 입력합니다. |
    | IP 버전 | 요구 사항에 따라 **IPv4** 또는 **IPv6** 를 선택합니다. |
    | 프런트 엔드 IP 주소 | **MyFrontend** 를 선택합니다. |
    | 백 엔드 풀 | **myBackendPool** 을 선택합니다. |
    | 상태 프로브 | **새로 만들기** 를 선택합니다. </br> **이름** 에 **myHealthProbe** 를 입력합니다. </br> **프로토콜** 에서 **HTTP** 를 선택합니다. </br> 나머지는 기본값으로 두고 **확인** 을 선택합니다. |
    | 세션 지속성 | **없음** 을 선택합니다. |

    :::image type="content" source="./media/tutorial-gateway-portal/add-load-balancing-rule.png" alt-text="부하 분산 규칙 만들기 스크린샷." border="true":::

17. **추가** 를 선택합니다.

18. 페이지 하단에 있는 파란색 **검토 + 만들기** 단추를 선택합니다.

19. **만들기** 를 선택합니다.

## <a name="add-network-virtual-appliances-to-the-gateway-load-balancer-backend-pool"></a>게이트웨이 부하 분산 장치 백 엔드 풀에 네트워크 가상 어플라이언스 추가
Azure Marketplace를 통해 NVA를 배포합니다. 배포가 완료되면 게이트웨이 부하 분산 장치의 백 엔드 풀 탭으로 이동하여 NVA 가상 머신을 백 엔드 풀에 추가합니다.

## <a name="chain-load-balancer-frontend-to-gateway-load-balancer"></a>부하 분산 장치 프런트 엔드를 게이트웨이 부하 분산 장치에 연결

이 예제에서는 표준 부하 분산 장치의 프런트 엔드를 게이트웨이 부하 분산 장치에 연결합니다. 

구독에 있는 기존 부하 분산 장치의 프런트 엔드 IP에 프런트 엔드를 추가합니다.

1. Azure Portal 검색 상자에 **부하 분산 장치** 를 입력합니다. 검색 결과에서 **부하 분산 장치** 를 선택합니다.

2. **부하 분산 장치** 에서 **myLoadBalancer** 또는 기존 부하 분산 장치 이름을 선택합니다.

3. 부하 분산 장치 페이지의 **설정** 에서 **프런트 엔드 IP 구성** 을 선택합니다.

4. 부하 분산 장치의 프런트 엔드 IP를 선택합니다. 이 예제에서는 프런트 엔드의 이름이 **myFrontendIP** 입니다.

    :::image type="content" source="./media/tutorial-gateway-portal/frontend-ip.png" alt-text="프런트 엔드 IP 구성 스크린샷." border="true":::

5. **게이트웨이 부하 분산 장치** 옆에 있는 드롭다운 상자에서 **myFrontendIP(10.1.0.4)** 를 선택합니다.

6. **저장** 을 선택합니다.

    :::image type="content" source="./media/tutorial-gateway-portal/select-gateway-load-balancer.png" alt-text="프런트 엔드 IP에 게이트웨이 부하 분산 장치를 추가하는 스크린샷." border="true":::


## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않으면 리소스 그룹, 부하 분산 장치 및 모든 관련 리소스를 삭제합니다. 이렇게 하려면 리소스가 포함된 리소스 그룹 **TutorGwLB-rg** 를 선택하고 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

Azure에서 네트워크 가상 어플라이언스를 만듭니다. 

NVA를 만들 때 이 자습서에서 만든 리소스를 선택합니다.

* 가상 네트워크

* 서브넷

* 네트워크 보안 그룹

* 게이트웨이 부하 분산 장치

영역 간 Azure Load Balancer를 만드는 방법을 알아보려면 다음 문서로 계속 진행하세요.
> [!div class="nextstepaction"]
> [영역 간 부하 분산 장치](tutorial-cross-region-powershell.md)
