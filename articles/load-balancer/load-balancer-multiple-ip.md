---
title: '자습서: 여러 IP 구성의 부하 분산 - Azure Portal'
titleSuffix: Azure Load Balancer
description: 이 문서에서는 Azure Portal을 사용하여 기본 NIC 구성과 보조 NIC 구성에 부하를 분산하는 방법을 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 08/08/2021
ms.custom: template-tutorial
ms.openlocfilehash: c0a5f441b27d379ff94db41fabfdf398a19ca667
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130254668"
---
# <a name="tutorial-load-balance-multiple-ip-configurations-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 여러 IP 구성의 부하 분산 

여러 웹 사이트를 호스트하려면 가상 머신과 연결된 또 다른 네트워크 인터페이스를 사용하면 됩니다. Azure Load Balancer는 부하 분산 솔루션을 배포하여 웹 사이트의 고가용성을 지원할 수 있습니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 가상 네트워크, 서브넷 및 NAT 게이트웨이를 만들고 구성
> * Windows Server 가상 머신 2개 만들기
> * 각 가상 머신에 대한 보조 NIC 및 네트워크 구성 만들기
> * 각 가상 머신에서 2개의 IIS(인터넷 정보 서버) 웹 사이트 만들기
> * 웹 사이트를 네트워크 구성에 바인딩
> * Azure Load Balancer를 만들고 구성
> * 부하 분산 장치 테스트

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-virtual-network"></a>가상 네트워크 만들기

이 섹션에서는 부하 분산 장치 및 가상 머신에 대한 가상 네트워크를 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 포털 맨 위에 있는 검색 상자에 **가상 네트워크** 를 입력합니다. 검색 결과에서 **가상 네트워크** 를 선택합니다.

3. **가상 네트워크** 에서 **+ 만들기** 를 선택합니다.

4. **가상 네트워크 만들기** 의 **기본** 탭에서 다음 정보를 입력하거나 선택합니다.

    | **설정**          | **값**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **프로젝트 세부 정보**  |                                                                 |
    | 구독     | Azure 구독 선택                                  |
    | 리소스 그룹   | **새로 만들기** 를 선택합니다. </br> **이름** 에 **TutorialLBIP-rg** 를 입력합니다. </br> **확인** 을 선택합니다. |
    | **인스턴스 세부 정보** |                                                                 |
    | 속성             | **myVNet** 입력                                    |
    | 지역           | **(유럽) 서유럽** 선택 |

5. **IP 주소** 탭을 선택하거나 페이지 하단의 **다음: IP 주소** 단추를 선택합니다.

6. **IP 주소** 탭에서 다음 정보를 입력합니다.

    | 설정            | 값                      |
    |--------------------|----------------------------|
    | IPv4 주소 공간 | **10.1.0.0/16** 입력 |

7. **서브넷 이름** 아래에서 **기본값** 이라는 단어를 선택합니다.

8. **서브넷 편집** 에서 다음 정보를 입력합니다.

    | 설정            | 값                      |
    |--------------------|----------------------------|
    | 서브넷 이름 | **myBackendSubnet** 입력 |
    | 서브넷 주소 범위 | **10.1.0.0/24** 입력 |

9. **저장** 을 선택합니다.

10. **보안** 탭을 선택합니다.

11. **BastionHost** 에서 **사용** 을 선택합니다. 다음 정보를 입력합니다.

    | 설정            | 값                      |
    |--------------------|----------------------------|
    | Bastion 이름 | **myBastionHost** 입력 |
    | AzureBastionSubnet 주소 공간 | **10.1.1.0/27** 입력 |
    | 공용 IP 주소 | **새로 만들기** 를 선택합니다. </br> **이름** 에 대해 **myBastionIP** 를 입력합니다. </br> **확인** 을 선택합니다. |


12. **검토 + 만들기** 탭을 선택하거나 **검토 + 만들기** 단추를 선택합니다.

13. **만들기** 를 선택합니다.

## <a name="create-nat-gateway"></a>NAT 게이트웨이 만들기

이 섹션에서는 가상 네트워크의 리소스에 대한 아웃바운드 인터넷 액세스를 위한 NAT 게이트웨이를 만듭니다. 

1. 포털 맨 위에 있는 검색 상자에 **NAT 게이트웨이** 를 입력합니다. 검색 결과에서 **NAT 게이트웨이** 를 선택합니다.

2. **NAT 게이트웨이** 에서 **+ 만들기** 를 선택합니다.

3. **NAT(Network Address Translation) 게이트웨이 만들기** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** |   |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **TutorialLBIP-rg** 를 선택합니다. |
    | **인스턴스 세부 정보** |    |
    | NAT 게이트웨이 이름 | **myNATgateway** 를 입력합니다. |
    | 가용성 영역 | **없음** 을 선택합니다. |
    | 유휴 제한 시간(분) | **15** 를 입력합니다. |

4. **아웃바운드 IP** 탭을 선택하거나 페이지 하단에 있는 **다음: 아웃바운드 IP** 단추를 선택합니다.

5. **아웃바운드 IP** 에서 **공용 IP 주소** 옆에 있는 **새 공용 IP 주소 만들기** 를 선택합니다.

6. **공용 IP 주소 추가** 에서 **이름** 에 **myNATgatewayIP** 를 입력합니다.

7. **확인** 을 선택합니다.

8. **서브넷** 탭을 선택하거나 페이지 하단에 있는 **다음: 서브넷** 단추를 선택합니다.

9. **가상 네트워크** 의 **서브넷** 탭에서 **myVNet** 을 선택합니다.

10. **서브넷 이름** 아래에서 **myBackendSubnet** 을 선택합니다.

11. 페이지 하단에서 파란색 **검토 + 만들기** 단추를 선택하거나 **검토 + 만들기** 탭을 선택합니다.

12. **만들기** 를 선택합니다.

## <a name="create-virtual-machines"></a>가상 머신 만들기

이 섹션에서는 IIS 웹 사이트를 호스트하는 2개의 가상 머신을 만듭니다.

1. 포털 맨 위에 있는 검색 상자에 **가상 머신** 을 입력합니다. 검색 결과에서 **가상 머신** 을 선택합니다.

2. **가상 머신** 에서 **+ 만들기** 를 선택한 다음, **+ 가상 머신** 을 선택합니다.

3. **가상 머신 만들기** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값                                          |
    |-----------------------|----------------------------------|
    | **프로젝트 세부 정보** |  |
    | 구독 | Azure 구독 선택 |
    | 리소스 그룹 | **TutorialLBIP-rg** 를 선택합니다. |
    | **인스턴스 세부 정보** |  |
    | 가상 머신 이름 | **myVM1** 을 입력합니다. |
    | 지역 | **(유럽) 서유럽** 선택 |
    | 가용성 옵션 | **가용성 영역** 을 선택합니다. |
    | 가용성 영역 | **1** 을 선택합니다. |
    | 이미지 | **Windows Server 2019 Datacenter - Gen1** 을 선택합니다. |
    | Azure Spot 인스턴스 | 선택 안 함(기본값)을 그대로 둡니다. |
    | 크기 | VM 크기를 선택하거나 기본 설정을 사용합니다. |
    | **관리자 계정** |  |
    | 사용자 이름 | 사용자 이름을 입력합니다. |
    | 암호 | 암호를 입력합니다. |
    | 암호 확인 | 암호를 다시 입력합니다. |
    | **인바운드 포트 규칙** |  |
    | 공용 인바운드 포트 | **없음** 을 선택합니다. |

3. **네트워킹** 탭을 선택하거나 **다음: 디스크**, **다음: 네트워킹** 을 차례로 선택합니다.
  
4. [네트워킹] 탭에서 다음을 선택하거나 입력합니다.

    | 설정 | 값 |
    |-|-|
    | **네트워크 인터페이스** |  |
    | 가상 네트워크 | **myVNet** |
    | 서브넷 | **myBackendSubnet** |
    | 공용 IP | **없음** 을 선택합니다. |
    | NIC 네트워크 보안 그룹 추가 | **고급** 을 선택합니다.|
    | 네트워크 보안 그룹 구성 | **새로 만들기** 를 선택합니다. </br> **네트워크 보안 그룹 만들기** 의 **이름** 에서 **myNSG** 를 입력합니다. </br> **인바운드 규칙** 아래에서 **+ 인바운드 규칙 추가** 를 선택합니다. </br> **서비스** 에서 **HTTP** 를 선택합니다. </br> **우선 순위** 아래에서 **100** 을 입력합니다. </br> **이름** 에 **myNSGrule** 을 입력합니다. </br> **추가** 를 선택합니다. </br> **확인** 을 선택합니다. |
   
7. **검토 + 만들기** 를 선택합니다. 
  
8. 설정을 검토한 다음, **만들기** 를 선택합니다.

9. 1~8단계에 따라 다음 값 및 **myVM1** 과 동일한 다른 모든 설정을 사용하여 또 다른 VM을 만듭니다.

    | 설정 | VM 2 |
    | ------- | ---- |
    | Name |  **myVM2** |
    | 가용성 영역 | **2** |
    | 네트워크 보안 그룹 | 기존 **myNSG** 를 선택합니다. |

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-secondary-network-configurations"></a>보조 네트워크 구성 만들기

이 섹션에서는 각 가상 머신의 기존 NIC 개인 IP 주소를 **고정** 으로 변경합니다. 다음으로, **고정** 개인 IP 주소 구성을 사용하여 각 가상 머신에 새 NIC 리소스를 추가합니다.

1. 포털 맨 위에 있는 검색 상자에 **가상 머신** 을 입력합니다. 검색 결과에서 **가상 머신** 을 선택합니다.

2. **myVM1** 을 선택합니다.

3. 가상 머신이 실행 중인 경우 가상 머신을 중지합니다. 

4. **설정** 에서 **네트워킹** 을 선택합니다.

5. **네트워킹** 에서 **네트워크 인터페이스** 옆에 있는 네트워크 인터페이스 이름을 선택합니다. 네트워크 인터페이스는 VM 이름으로 시작하며 임의의 숫자가 할당됩니다. 이 예제에서는 **myVM1266** 입니다.

    :::image type="content" source="./media/load-balancer-multiple-ip/myvm1-nic.png" alt-text="Azure Portal에서 myVM1 네트워킹을 구성하는 스크린샷":::

6. [네트워크 인터페이스] 페이지의 **설정** 에서 **IP 구성** 을 선택합니다.

7. **IP 구성** 에서 **ipconfig1** 을 선택합니다.

    :::image type="content" source="./media/load-balancer-multiple-ip/myvm1-ipconfig1.png" alt-text="myVM1 네트워크 인터페이스 구성의 스크린샷":::

8. **ipconfig1** 구성의 **할당** 에서 **고정** 을 선택합니다.

9. **저장** 을 선택합니다.

10. **myVM1** 의 **개요** 페이지로 돌아갑니다.

11. **설정** 에서 **네트워킹** 을 선택합니다.

12. **네트워킹** 페이지에서 **네트워크 인터페이스 연결** 을 선택합니다.

    :::image type="content" source="./media/load-balancer-multiple-ip/myvm1-attach-nic.png" alt-text="myVM1 네트워크 인터페이스 연결의 스크린샷":::

13. **네트워크 인터페이스 연결** 에서 **네트워크 인터페이스 만들기 및 연결** 을 선택합니다.

14. **네트워크 인터페이스 만들기** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** |   |
    | Resource group | **TutorialLBIP-rg** 를 선택합니다. |
    | **네트워크 인터페이스** |  |
    | Name | **myVM1NIC2** 를 입력합니다. |
    | 서브넷 | **myBackendSubnet(10.1.0.0/24)** 을 선택합니다. |
    | NIC 네트워크 보안 그룹 추가 | **고급** 을 선택합니다. |
    | 네트워크 보안 그룹 구성 | **myNSG** 를 선택합니다. |
    | 개인 IP 주소 할당 | **고정** 을 선택합니다. |
    | 개인 IP 주소 | **10.1.0.6** 을 입력합니다. |

15. **만들기** 를 선택합니다. 

16. 가상 머신을 시작합니다.

17. **myVM2** 에 대해 1~16단계를 반복하고, 다음 정보를 바꿉니다.

    | 설정 | myVM2 |
    | ------  | ----- |
    | Name | **myVM2NIC2** |
    | 개인 IP 주소 | **10.1.0.7** |

## <a name="configure-virtual-machines"></a>가상 머신 구성

이 섹션에서는 Azure Bastion을 사용하여 **myVM1** 및 **myVM2** 에 연결하고 보조 네트워크를 구성합니다. 보조 네트워크 구성에 대한 게이트웨이의 경로를 추가합니다. 그런 다음, 각 가상 머신에 IIS를 설치하고 가상 머신의 호스트 이름을 표시하도록 웹 사이트를 사용자 지정합니다.

1. 포털 맨 위에 있는 검색 상자에 **가상 머신** 을 입력합니다. 검색 결과에서 **가상 머신** 을 선택합니다.

2. **myVM1** 을 선택합니다.

3. **myVM1** 을 시작합니다.

4. **개요** 에서 **연결** 을 선택한 다음, **Bastion** 을 선택합니다.

5. **Bastion 사용** 을 선택합니다.

6. 가상 머신을 만들 때 입력한 사용자 이름 및 암호를 입력합니다.

7. Bastion에서 클립보드를 사용할 수 있도록 **허용** 을 선택합니다.

8. 서버 데스크톱에서 시작 > Windows 관리 도구 > Windows PowerShell > Windows PowerShell로 이동합니다.

9. PowerShell 창에서 `route print` 명령을 실행합니다. 그러면 2개의 네트워크 인터페이스가 연결된 가상 머신의 출력이 다음과 비슷한 형태로 반환됩니다.

    ```console
    ===========================================================================
    Interface List
      6...00 22 48 86 00 53 ......Microsoft Hyper-V Network Adapter #2
     13...00 22 48 83 0b da ......Microsoft Hyper-V Network Adapter #3
      1...........................Software Loopback Interface 1
    ===========================================================================
    ```
    이 예제에서는 **Microsoft Hyper-V Network Adapter #3**(인터페이스 13)이 기본 게이트웨이가 할당되지 않은 보조 네트워크 인터페이스입니다.

10. PowerShell 창에서 `ipconfig /all` 명령을 실행하여 보조 네트워크 인터페이스에 할당된 IP 주소를 확인합니다. 이 예제에서는 10.1.0.6이 인터페이스 13에 할당되었습니다. 보조 네트워크 인터페이스에 대해서는 기본 게이트웨이 주소가 반환되지 않습니다.

11. 서브넷 외부에 있는 모든 주소의 트래픽을 게이트웨이로 라우팅하려면 다음 명령을 실행합니다.

    ```console
    route -p add 0.0.0.0 MASK 0.0.0.0 10.1.0.1 METRIC 5015 IF 13
    ```

    이 예제에서 **10.1.0.1** 은 앞에서 만든 가상 네트워크의 기본 게이트웨이입니다.

12. PowerShell 창에서 다음 명령을 실행하여 IIS 및 테스트 웹 사이트를 설치하고 구성합니다.

    ```powershell
    ## Install IIS and the management tools. ##
    Install-WindowsFeature -Name Web-Server -IncludeManagementTools

    ## Set the binding for the Default website to 10.1.0.4:80. ##
    $para1 = @{
        Name = 'Default Web Site'
        BindingInformation = '10.1.0.4:80:'
        Protocol = 'http'
    }
    New-IISSiteBinding @para1

    ## Remove the default site binding. ##
    $para2 = @{
        Name = 'Default Web Site'
        BindingInformation = '*:80:'
    }
    Remove-IISSiteBinding @para2 -Force

    ## Remove the default htm file. ##
    Remove-Item c:\inetpub\wwwroot\iisstart.htm

    ## Add a new htm file that displays the Contoso website. ##
    $para3 = @{
        Path = 'c:\inetpub\wwwroot\iisstart.htm'
        Value = $("Hello World from www.contoso.com" + "-" + $env:computername)
    }
    Add-Content @para3

    ## Create folder to host website. ##
    $para4 = @{
        Path = 'c:\inetpub\'
        Name = 'fabrikam'
        Type = 'directory'
    }
    New-Item @para4

     ## Create a new website and site binding for the second IP address 10.1.0.6. ##
    $para5 = @{
        Name = 'Fabrikam'
        PhysicalPath = 'c:\inetpub\fabrikam'
        BindingInformation = '10.1.0.6:80:'
    }
    New-IISSite @para5

    ## Add a new htm file that displays the Fabrikam website. ##
    $para6 = @{
        Path = 'C:\inetpub\fabrikam\iisstart.htm'
        Value = $("Hello World from www.fabrikam.com" + "-" + $env:computername)

    }
    Add-Content @para6

    ```
13. **myVM1** 에 대한 Bastion 연결을 닫습니다.

14. **myVM2** 에 대해 1~13단계를 반복합니다. **myVM2** 에 대해 아래 PowerShell 코드를 사용하여 IIS를 설치합니다.

    ```powershell
    ## Install IIS and the management tools. ##
    Install-WindowsFeature -Name Web-Server -IncludeManagementTools

    ## Set the binding for the Default website to 10.1.0.5:80. ##
    $para1 = @{
        Name = 'Default Web Site'
        BindingInformation = '10.1.0.5:80:'
        Protocol = 'http'
    }
    New-IISSiteBinding @para1

    ## Remove the default site binding. ##
    $para2 = @{
        Name = 'Default Web Site'
        BindingInformation = '*:80:'
    }
    Remove-IISSiteBinding @para2

    ## Remove the default htm file. ##
    Remove-Item C:\inetpub\wwwroot\iisstart.htm

    ## Add a new htm file that displays the Contoso website. ##
    $para3 = @{
        Path = 'c:\inetpub\wwwroot\iisstart.htm'
        Value = $("Hello World from www.contoso.com" + "-" + $env:computername)
    }
    Add-Content @para3

    ## Create folder to host website. ##
    $para4 = @{
        Path = 'c:\inetpub\'
        Name = 'fabrikam'
        Type = 'directory'
    }
    New-Item @para4

    ## Create a new website and site binding for the second IP address 10.1.0.7. ##
    $para5 = @{
        Name = 'Fabrikam'
        PhysicalPath = 'c:\inetpub\fabrikam'
        BindingInformation = '10.1.0.7:80:'
    }
    New-IISSite @para5

    ## Add a new htm file that displays the Fabrikam website. ##
    $para6 = @{
        Path = 'C:\inetpub\fabrikam\iisstart.htm'
        Value = $("Hello World from www.fabrikam.com" + "-" + $env:computername)
    }
    Add-Content @para6

    ```

## <a name="create-load-balancer"></a>부하 분산 장치 만들기

이 섹션에서는 가상 머신의 부하를 분산하는 영역 중복 부하 분산 장치를 만듭니다. 

영역 중복에서 하나 이상의 가용성 영역이 실패할 수 있고 지역에서 한 영역이 정상으로 유지되는 한 데이터 경로는 유효합니다.

부하 분산 장치를 만드는 동안 다음을 구성합니다.

* 2개의 프런트 엔드 IP 주소(웹 사이트마다 하나씩)
* 백 엔드 풀
* 인바운드 부하 분산 규칙

1. 포털 맨 위에 있는 검색 상자에 **부하 분산 장치** 를 입력합니다. 검색 결과에서 **부하 분산 장치** 를 선택합니다.

2. **부하 분산 장치** 페이지에서 **만들기** 를 선택합니다.

3. **부하 분산 장치 만들기** 페이지의 **기본 사항** 탭에서 다음 정보를 입력하거나 선택합니다. 

    | 설정                 | 값                                              |
    | ---                     | ---                                                |
    | **프로젝트 세부 정보** |   |
    | Subscription               | 구독을 선택합니다.    |    
    | Resource group         | **TutorialLBIP-rg** 를 선택합니다. |
    | **인스턴스 세부 정보** |   |
    | 이름                   | **myLoadBalancer** 를 입력합니다.                                   |
    | 지역         | **(유럽) 서유럽** 을 선택합니다.                                        |
    | Type          | **공용** 을 선택합니다.                                        |
    | SKU           | 기본값인 **표준** 을 그대로 둡니다. |
    | 계층          | 기본 **지역** 을 그대로 둡니다. |

4. 페이지 하단에서 **다음: 프런트 엔드 IP 구성** 을 선택합니다.

5. **프런트 엔드 IP 구성** 에서 **+ 프런트 엔드 IP 추가** 를 선택합니다.

6. **이름** 에 **Frontend-contoso** 를 입력합니다.

7. **IP 버전** 으로 **IPv4** 를 선택합니다.

    > [!NOTE]
    > IPv6는 현재 라우팅 기본 설정 또는 지역 간 부하 분산(글로벌 계층)에서 지원되지 않습니다.

8. **IP 형식** 으로 **IP 주소** 를 선택합니다.

    > [!NOTE]
    > IP 접두사에 대한 자세한 내용은 [Azure 공용 IP 주소 접두사](../virtual-network/ip-services/public-ip-address-prefix.md)를 참조하세요.

9. **공용 IP 주소** 에서 **새로 만들기** 를 선택합니다.

10. **공용 IP 주소 추가** 에서 **이름** 에 **myPublicIP-contoso** 를 입력합니다.

11. **가용성 영역** 에서 **영역 중복** 을 선택합니다.

    > [!NOTE]
    > [가용성 영역](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)이 있는 지역에서는 영역 없음(기본 옵션), 특정 영역 또는 영역 중복을 선택하는 옵션이 있습니다. 선택은 특정 도메인 오류 요구 사항에 따라 달라집니다. 가용성 영역이 없는 지역에서는 이 필드가 표시되지 않습니다. </br> 가용성 영역에 대한 자세한 내용은 [가용성 영역 개요](../availability-zones/az-overview.md)를 참조하세요.

12. **라우팅 기본 설정** 에는 **Microsoft 네트워크** 의 기본값을 그대로 둡니다.

13. **확인** 을 선택합니다.

14. **추가** 를 선택합니다.

14. **+ 프런트 엔드 IP 추가** 를 선택합니다.

15. **이름** 에 **Frontend-fabrikam** 을 입력합니다.

7. **IP 버전** 으로 **IPv4** 를 선택합니다.

8. **IP 형식** 으로 **IP 주소** 를 선택합니다.

9. **공용 IP 주소** 에서 **새로 만들기** 를 선택합니다.

10. **공용 IP 주소 추가** 에서 **이름** 에 **myPublicIP-fabrikam** 을 입력합니다.

11. **가용성 영역** 에서 **영역 중복** 을 선택합니다.

14. **추가** 를 선택합니다.

15. 페이지 하단에서 **다음: 백 엔드 풀** 을 선택합니다.

16. **백 엔드 풀** 탭에서 **+ 백 엔드 풀 추가** 를 선택합니다.

17. **백 엔드 풀 추가** 의 **이름** 에 **myBackendPool-contoso** 를 입력합니다.

18. **가상 네트워크** 에서 **myVNet** 을 선택합니다.

19. **백 엔드 풀 구성** 에서 **NIC** 를 선택합니다.

20. **IP 버전** 으로 **IPv4** 를 선택합니다.

21. **가상 머신** 에서 **추가** 를 선택합니다.

22. **ipconfig1(10.1.0.4)** 및 **ipconfig1(10.1.0.5)** 에 부합하는 **myVM1** 및 **myVM2** 를 선택합니다.

23. **추가** 를 선택합니다.

21. **추가** 를 선택합니다.

22. **+ 백 엔드 추가** 를 선택합니다.

23. **백 엔드 풀 추가** 의 **이름** 에 **myBackendPool-fabrikam** 을 입력합니다.

24. **가상 네트워크** 에서 **myVNet** 을 선택합니다.

19. **백 엔드 풀 구성** 에서 **NIC** 를 선택합니다.

20. **IP 버전** 으로 **IPv4** 를 선택합니다.

21. **가상 머신** 에서 **추가** 를 선택합니다.

22. **ipconfig1(10.1.0.6)** 및 **ipconfig1(10.1.0.7)** 에 부합하는 **myVM1** 및 **myVM2** 를 선택합니다.

23. **추가** 를 선택합니다.

21. **추가** 를 선택합니다.

22. 페이지 하단에 있는 **다음: 인바운드 규칙** 단추를 선택합니다.

23. **인바운드 규칙** 탭의 **부하 분산 규칙** 에서 **+ 부하 분산 규칙 추가** 를 선택합니다.

24. **부하 분산 규칙 추가** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | Name | **myHTTPRule-contoso** 입력 |
    | IP 버전 | **IPv4** 를 선택합니다. |
    | 프런트 엔드 IP 주소 | **Frontend-contoso** 를 선택합니다. |
    | 프로토콜 | **TCP** 를 선택합니다. |
    | 포트 | **80** 을 입력합니다. |
    | 백 엔드 포트 | **80** 을 입력합니다. |
    | 백 엔드 풀 | **myBackendPool-contoso** 를 선택합니다. |
    | 상태 프로브 | **새로 만들기** 를 선택합니다. </br> **이름** 에 **myHealthProbe-contoso** 를 입력합니다. </br> **프로토콜** 에서 **HTTP** 를 선택합니다. </br> 나머지는 기본값으로 두고 **확인** 을 선택합니다. |
    | 세션 지속성 | **없음** 을 선택합니다. |
    | 유휴 제한 시간(분) | **15** 를 입력하거나 선택합니다. |
    | TCP 재설정 | **사용** 을 선택합니다. |
    | 부동 IP | **사용 안 함** 을 선택합니다. |
    | 아웃바운드 SNAT(Source Network Address Translation) | **(권장) 아웃바운드 규칙을 사용하여 백 엔드 풀 멤버에 인터넷 액세스 권한을 제공합니다.** (기본값)를 그대로 둡니다. |

25. **추가** 를 선택합니다.

26. **부하 분산 규칙 추가** 를 선택합니다.

27. **부하 분산 규칙 추가** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | Name | **myHTTPRule-fabrikam** 입력 |
    | IP 버전 | **IPv4** 를 선택합니다. |
    | 프런트 엔드 IP 주소 | **Frontend-fabrikam** 을 선택합니다. |
    | 프로토콜 | **TCP** 를 선택합니다. |
    | 포트 | **80** 을 입력합니다. |
    | 백 엔드 포트 | **80** 을 입력합니다. |
    | 백 엔드 풀 | **myBackendPool-fabrikam** 을 선택합니다. |
    | 상태 프로브 | **새로 만들기** 를 선택합니다. </br> **이름** 에 **myHealthProbe-fabrikam** 을 입력합니다. </br> **프로토콜** 에서 **HTTP** 를 선택합니다. </br> 나머지는 기본값으로 두고 **확인** 을 선택합니다. |
    | 세션 지속성 | **없음** 을 선택합니다. |
    | 유휴 제한 시간(분) | **15** 를 입력하거나 선택합니다. |
    | TCP 재설정 | **사용** 을 선택합니다. |
    | 부동 IP | **사용 안 함** 을 선택합니다. |
    | 아웃바운드 SNAT(Source Network Address Translation) | **(권장) 아웃바운드 규칙을 사용하여 백 엔드 풀 멤버에 인터넷 액세스 권한을 제공합니다.** (기본값)를 그대로 둡니다. |

25. **추가** 를 선택합니다.

26. 페이지 하단에 있는 파란색 **검토 + 만들기** 단추를 선택합니다.

27. **만들기** 를 선택합니다.

    > [!NOTE]
    > 이 예제에서는 아웃바운드 인터넷 액세스를 제공하는 NAT 게이트웨이를 만들었습니다. 구성의 아웃바운드 규칙 탭은 NAT 게이트웨이에서 필수가 아닌 선택 사항이므로 무시됩니다. Azure NAT 게이트웨이에 대한 자세한 내용은 [Azure Virtual Network NAT란?](../virtual-network/nat-gateway/nat-overview.md)을 참조하세요.
    > Azure의 아웃바운드 연결에 대한 자세한 내용은 [아웃바운드 연결에 대한 SNAT(Source Network Address Translation)](../load-balancer/load-balancer-outbound-connections.md)를 참조하세요.

## <a name="test-load-balancer"></a>부하 분산 장치 테스트

이 섹션에서는 각 웹 사이트의 공용 IP 주소를 검색합니다. 브라우저에 IP를 입력하여 앞에서 만든 웹 사이트를 테스트합니다.

1. 포털 맨 위에 있는 검색 상자에 **공용 IP** 를 입력합니다. 검색 결과에서 **공용 IP 주소** 를 선택합니다.

2. **myPublicIP-contoso** 를 선택합니다.

3. **myPublicIP-contoso** 의 [개요] 페이지에서 **IP 주소** 를 복사합니다.

    :::image type="content" source="./media/load-balancer-multiple-ip/public-ip-contoso.png" alt-text="myPublicIP-fabrikam 공용 IP 주소의 스크린샷":::

4. 웹 브라우저를 열고 공용 IP 주소를 주소 표시줄에 붙여넣습니다.

    :::image type="content" source="./media/load-balancer-multiple-ip/test-contoso.png" alt-text="웹 브라우저의 contoso 웹 사이트 스크린샷":::

5. **공용 IP 주소** 로 돌아갑니다. **myPublicIP-fabrikam** 을 선택합니다.

6. **myPublicIP-fabrikam** 의 [개요] 페이지에서 **IP 주소** 를 복사합니다.

    :::image type="content" source="./media/load-balancer-multiple-ip/public-ip-fabrikam.png" alt-text="myPublicIP-contoso 공용 IP 주소의 스크린샷":::

7. 웹 브라우저를 열고 공용 IP 주소를 주소 표시줄에 붙여넣습니다.

    :::image type="content" source="./media/load-balancer-multiple-ip/test-fabrikam.png" alt-text="웹 브라우저의 fabrikam 웹 사이트 스크린샷":::

8. 부하 분산 장치를 테스트하려면 브라우저를 새로 고치거나 가상 머신 중 하나를 종료합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 더 이상 사용하지 않으려면 다음 단계에 따라 가상 머신 및 부하 분산 장치를 삭제합니다.

1. 포털 맨 위에 있는 검색 상자에 **리소스 그룹** 을 입력합니다.  검색 결과에서 **리소스 그룹** 을 선택합니다.

2. **리소스 그룹** 에서 **TutorialLBIP-rg** 를 선택합니다.

3. **리소스 그룹 삭제** 를 선택합니다.

4. **리소스 그룹 이름 입력** 에 **TutorialLBIP-rg** 를 입력합니다. **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

지역 간 부하 분산 장치를 만드는 방법을 알아보려면 다음 문서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Portal을 사용하여 지역 간 부하 분산 장치 만들기](tutorial-cross-region-portal.md)