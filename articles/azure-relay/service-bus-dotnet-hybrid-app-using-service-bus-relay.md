---
title: Azure WCF(Windows Communication Foundation) 릴레이 하이브리드 온-프레미스/클라우드 애플리케이션(.NET) | Microsoft Docs
description: Azure Relay를 사용하여 클라우드의 웹 애플리케이션에 온-프레미스 WCF 서비스를 노출하는 방법을 알아봅니다.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 06/23/2021
ms.openlocfilehash: 2b332db84d3350b04a0ac4adfb94639c5f167c39
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2021
ms.locfileid: "114669810"
---
# <a name="expose-an-on-premises-wcf-service-to-a-web-application-in-the-cloud-by-using-azure-relay"></a>Azure Relay를 사용하여 클라우드의 웹 애플리케이션에 온-프레미스 WCF 서비스 노출

이 문서에서는 Microsoft Azure 및 Visual Studio로 하이브리드 클라우드 애플리케이션을 구축하는 방법을 보여줍니다. 클라우드의 여러 Azure 리소스를 사용하는 애플리케이션을 만듭니다. 이 자습서에서는 다음에 대해 알아볼 수 있습니다.

* 웹 서비스를 만들거나 기존 웹 서비스를 웹 솔루션에서 사용할 수 있도록 변경하는 방법
* Azure WCF(Windows Communication Foundation) 릴레이 서비스를 사용하여 Azure 애플리케이션 및 다른 위치에서 호스트된 웹 서비스 간에 데이터를 공유하는 방법

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
>
> * 이 자습서를 수행하기 위한 필수 조건 설치
> * 시나리오 검토
> * 네임스페이스 만들기
> * 온-프레미스 서버 만들기
> * ASP.NET 애플리케이션 만들기
> * 로컬로 앱 실행
> * Azure에 웹앱 배포
> * Azure에서 앱 실행

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

* Azure 구독 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).
* [Visual Studio 2015 이상](https://www.visualstudio.com) - 이 자습서의 예제에서는 Visual Studio 2019를 사용합니다.
* Azure SDK for .NET. [SDK 다운로드 페이지](https://azure.microsoft.com/downloads/)에서 설치합니다.

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>하이브리드 솔루션에 유용한 Azure 릴레이

비즈니스 솔루션은 일반적으로 사용자 지정 코드와 기존 기능의 조합으로 구성됩니다. 사용자 지정 코드는 새롭고 고유한 비즈니스 요구 사항을 해결합니다. 이미 구현된 솔루션과 시스템은 기존 기능을 제공합니다.

솔루션 설계자는 확장 요구 사항을 더 간편하게 처리하고 운영 비용을 낮추기 위해 클라우드를 사용하기 시작했습니다. 이 과정에서 솔루션의 구성 요소로 사용할 기존 서비스 자산이 회사 방화벽 내부에 있으므로 클라우드 솔루션이 쉽게 액세스할 수 없음을 발견합니다. 대부분의 내부 서비스는 회사 네트워크 에지에서 쉽게 노출될 수 있는 방식으로 빌드되거나 호스트되지 않습니다.

[Azure Relay](https://azure.microsoft.com/services/service-bus/)는 기존 WCF 웹 서비스를 사용하고 회사 네트워크 인프라에 대한 침입적인 변경 없이도 회사 경계 외부에 있는 솔루션에서 해당 서비스에 안전하게 액세스할 수 있도록 합니다. 이러한 릴레이 서비스는 기존 환경 내에 계속 호스트되지만 클라우드 호스트 릴레이 서비스에 들어오는 세션 및 요청에 대한 수신을 위임합니다. 또한 Azure Relay는 [SAS(공유 액세스 서명](../service-bus-messaging/service-bus-sas.md)) 인증을 사용하여 이러한 서비스에 무단으로 액세스하지 못하도록 보호합니다.

## <a name="review-the-scenario"></a>시나리오 검토

이 자습서에서는 제품 재고 페이지에 제품 목록을 표시할 수 있는 ASP.NET 웹 사이트를 만듭니다.

![시나리오][0]

이 자습서는 기존 온-프레미스 시스템에 제품 정보가 있으며 해당 시스템에 도달하는 데 Azure 릴레이를 사용하는 개발자를 대상으로 합니다. 간단한 콘솔 애플리케이션에서 실행되는 웹 서비스가 해당 상황을 시뮬레이트합니다. 이 서비스는 메모리 내 제품 집합을 포함합니다. 사용자 고유의 컴퓨터에서 이 콘솔 애플리케이션을 실행하고 Azure에 웹 역할을 배포할 수 있습니다. 이렇게 하면 Azure 데이터 센터에서 실행되는 웹 역할이 컴퓨터를 호출하는 방법을 확인할 수 있습니다. 이 호출은 컴퓨터가 하나 이상의 방화벽 및 NAT(Network Address Translation) 계층 뒤에 있는 것이 거의 확실한 경우에도 발생합니다.

## <a name="set-up-the-development-environment"></a>개발 환경 설정

Azure 애플리케이션 개발을 시작하려면 먼저 도구를 다운로드하고 개발 환경을 설정해야 합니다.

1. SDK [다운로드 페이지](https://azure.microsoft.com/downloads/)에서 .NET용 Azure SDK를 설치합니다.
1. **.NET** 열에서 사용 중인 [Visual Studio](https://www.visualstudio.com) 버전을 선택합니다. 이 자습서에서는 Visual Studio 2019를 사용합니다.
1. 설치 관리자를 실행할지 또는 저장할지를 묻는 메시지가 표시되면 **실행** 을 선택합니다.
1. **웹 플랫폼 설치 관리자** 대화 상자에서 **설치** 를 선택하고 설치를 계속 진행합니다.

설치가 완료되면 앱 개발을 시작하는 데 필요한 모든 것이 준비되었습니다. SDK에는 Visual Studio에서 Azure 애플리케이션을 쉽게 개발할 수 있는 도구가 포함되어 있습니다.

## <a name="create-a-namespace"></a>네임스페이스 만들기

첫 단계는 네임스페이스를 만들고 [SAS(공유 액세스 서명)](../service-bus-messaging/service-bus-sas.md) 키를 확보합니다. 네임스페이스는 릴레이 서비스를 통해 노출되는 각 애플리케이션에 대한 애플리케이션 경계를 제공합니다. SAS 키는 서비스 네임스페이스가 만들어질 때 시스템에 의해 자동으로 생성됩니다. 서비스 네임스페이스 및 SAS 키 조합은 Azure에 자격 증명을 제공하여 애플리케이션에 대한 액세스를 인증합니다.

[!INCLUDE [relay-create-namespace-portal](./includes/relay-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>온-프레미스 서버 만들기

먼저, 시뮬레이션된 온-프레미스 제품 카탈로그 시스템을 빌드합니다.  이 프로젝트는 Visual Studio 콘솔 애플리케이션으로, [Azure Service Bus NuGet 패키지](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)를 사용하여 Service Bus 라이브러리 및 구성 설정을 포함합니다. <a name="create-the-project"></a>

1. 관리자 권한으로 Microsoft Visual Studio를 시작합니다. 이렇게 하려면 Visual Studio 프로그램 아이콘을 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행** 을 선택합니다.
1. Visual Studio에서 **새 프로젝트 만들기** 를 선택합니다.
1. **새 프로젝트 만들기** 에서 C#용 **콘솔 앱(.NET Framework)** 을 선택하고 **다음** 을 선택합니다.
1. 프로젝트 이름을 *ProductsServer* 로 지정하고 **만들기** 를 선택합니다.

   ![새 프로젝트 구성][11]

1. **솔루션 탐색기** 에서 **ProductsServer** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리** 를 선택합니다.
1. **찾아보기** 를 선택한 다음, **WindowsAzure.ServiceBus** 를 검색하여 선택합니다. **설치** 를 선택하고 사용 약관에 동의합니다.

   ![NuGet 패키지 선택][13]

   필수 클라이언트 어셈블리가 이제 참조됩니다.

1. 제품 계약의 새 클래스를 추가합니다. **솔루션 탐색기** 에서 **ProductsServer** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스** 를 선택합니다.
1. **이름** 에 *ProductsContract.cs* 를 입력하고 **추가** 를 선택합니다.

솔루션의 코드를 다음과 같이 변경합니다.

1. *ProductsContract.cs* 에서 네임스페이스 정의를 다음 코드로 바꿉니다. 이 코드는 서비스의 계약을 정의합니다.

    ```csharp
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;

        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }

        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();

        }

        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```

1. *Program.cs* 에서 네임스페이스 정의를 다음 코드로 바꿉니다. 이 코드는 프로필 서비스 및 해당 호스트를 추가합니다.

    ```csharp
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;

        // Implement the IProducts interface.
        class ProductsService : IProducts
        {

            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };

            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }

        }

        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();

                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();

                sh.Close();
            }
        }
    }
    ```

1. **솔루션 탐색기** 에서 **App.config** 파일을 두 번 클릭하여 Visual Studio 편집기에서 엽니다. `<system.ServiceModel>` 요소의 맨 아래이지만 `<system.ServiceModel>` 내에 다음 XML 코드를 추가합니다. `yourServiceNamespace`를 해당 네임스페이스의 이름으로 바꾸고 `yourKey`를 앞서 포털에서 검색한 SAS 키로 바꿔야 합니다.

    ```xml
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```

    > [!NOTE]
    > `transportClientEndpointBehavior`에서 발생한 오류는 경고일 뿐이며 예제의 차단 이슈는 아닙니다.

1. 계속 *App.config* 의 `<appSettings>` 요소에서 연결 문자열 값을 앞서 포털에서 얻은 연결 문자열로 바꿉니다.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

1. Ctrl+Shift+B를 선택하거나 **빌드** > **솔루션 빌드** 를 선택하여 애플리케이션을 빌드하고 지금까지 진행한 작업의 정확도를 확인합니다.

## <a name="create-an-aspnet-application"></a>ASP.NET 애플리케이션 만들기

이 섹션에서는 제품 서비스에서 검색한 데이터를 표시하는 간단한 ASP.NET 애플리케이션을 빌드합니다.

### <a name="create-the-project"></a>프로젝트 만들기

1. 관리자 권한으로 Visual Studio를 실행하고 있는지 확인합니다.
1. Visual Studio에서 **새 프로젝트 만들기** 를 선택합니다.
1. **새 프로젝트 만들기** 에서 C#용 **ASP.NET 웹 애플리케이션(.NET Framework)** 을 선택하고 **다음** 을 선택합니다.
1. 프로젝트 이름을 *ProductsPortal* 로 지정하고 **만들기** 를 선택합니다.
1. **새 ASP.NET 웹 애플리케이션 만들기** 에서 **MVC** 를 선택한 다음 **인증** 에서 **변경** 을 선택합니다.

   ![ASP.NET 웹 애플리케이션 선택][16]

1. **인증 변경** 에서 **인증 없음** 을 선택한 다음 **확인** 을 선택합니다. 이 자습서에서는 사용자 로그인이 필요하지 않은 앱을 배포합니다.

    ![인증 지정][18]

1. **새 ASP.NET 웹 애플리케이션 만들기** 로 돌아가서 **만들기** 를 선택하여 MVC 앱을 만듭니다.
1. 새 웹앱의 Azure 리소스를 구성합니다. [웹앱 게시](../app-service/quickstart-dotnetcore.md?tabs=netframework48#publish-your-web-app)의 단계를 따릅니다. 그런 다음, 이 자습서로 돌아와서 다음 단계를 진행합니다.
1. **솔루션 탐색기** 에서 **모델** 을 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스** 를 선택합니다.
1. 클래스 이름을 *Product.cs* 로 지정하고 **추가** 를 선택합니다.

    ![제품 모델 만들기][17]

### <a name="modify-the-web-application"></a>웹 애플리케이션 수정

1. Visual Studio의 *Product.cs* 파일에서 기존 네임스페이스 정의를 다음 코드로 바꿉니다.

   ```csharp
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
       public class Product
       {
           public string Id { get; set; }
           public string Name { get; set; }
           public string Quantity { get; set; }
       }
    }
    ```

1. **솔루션 탐색기** 에서 **컨트롤러** 를 펼친 다음 **HomeController.cs** 를 두 번 클릭하여 Visual Studio에서 파일을 엽니다.
1. *HomeController.cs* 에서 기존 네임스페이스 정의를 다음 코드로 바꿉니다.

    ```csharp
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;

        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```

1. **솔루션 탐색기** 에서 **보기** > **공유** 를 펼친 다음 **_Layout.cshtml** 을 두 번 클릭하여 Visual Studio 편집기에서 파일을 엽니다.
1. `My ASP.NET Application`과 일치하는 모든 항목을 *Northwind Traders 제품* 으로 변경합니다.
1. `Home`, `About`, `Contact` 링크를 제거합니다. 다음 예제에서 강조 표시된 코드를 삭제합니다.

    ![생성된 목록 항목 삭제][41]

1. **솔루션 탐색기** 에서 **보기** > **홈** 을 펼친 다음 **Index.cshtml** 을 두 번 클릭하여 Visual Studio 편집기에서 파일을 엽니다. 파일의 전체 내용을 다음 코드로 바꿉니다.

   ```html
   @model IEnumerable<ProductsWeb.Models.Product>

   @{
            ViewBag.Title = "Index";
   }

   <h2>Prod Inventory</h2>

   <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.Name)
                 </th>
                 <th></th>
                 <th>
                     @Html.DisplayNameFor(model => model.Quantity)
                 </th>
             </tr>

   @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.Name)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Quantity)
                 </td>
             </tr>
   }

   </table>
   ```

1. 지금까지 진행한 작업의 정확도를 확인하려면 Ctrl+Shift+B를 선택하여 프로젝트를 빌드하면 됩니다.

### <a name="run-the-app-locally"></a>로컬에서 앱 실행하기

애플리케이션을 실행하여 작동하는지 확인합니다.

1. **ProductsPortal** 이 활성 프로젝트인지 확인합니다. **솔루션 탐색기** 에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정** 을 선택합니다.
1. Visual Studio에서 F5 키를 선택합니다.

애플리케이션이 브라우저에 실행되는 것으로 나타나야 합니다.

![URL이 강조 표시된 브라우저에서 실행되는 애플리케이션 예제 스크린샷][21]

## <a name="put-the-pieces-together"></a>부분 연결

다음 단계는 온-프레미스 제품 서버를 ASP.NET 애플리케이션과 연결하는 것입니다.

1. 아직 열려 있지 않은 경우 [ASP.NET 애플리케이션 만들기](#create-an-aspnet-application) 섹션에서 만든 **ProductsPortal** 프로젝트를 Visual Studio에서 엽니다.
1. [온-프레미스 서버 만들기](#create-an-on-premises-server) 섹션의 단계와 비슷하게 프로젝트 참조에 NuGet 패키지를 추가합니다. **솔루션 탐색기** 에서 **ProductsPortal** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리** 를 선택합니다.
1. *WindowsAzure.ServiceBus* 를 검색하고 **WindowsAzure.ServiceBus** 항목을 선택합니다. 그런 다음 설치를 완료하고 이 대화 상자를 닫습니다.
1. **솔루션 탐색기** 에서 **ProductsPortal** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **기존 항목** 을 선택합니다.
1. **ProductsServer** 콘솔 프로젝트에서 *ProductsContract.cs* 파일로 이동합니다. *ProductsContract.cs* 를 강조 표시합니다. **추가** 옆의 아래쪽 화살표를 선택한 다음 **링크로 추가** 를 선택합니다.

   ![링크로 추가][24]

1. 이제 Visual Studio 편집기에서 *HomeController.cs* 파일을 열고 네임스페이스 정의를 다음 코드로 바꿉니다. `yourServiceNamespace`를 해당 서비스 네임스페이스의 이름으로 바꾸고 `yourKey`를 해당 SAS 키로 바꿔야 합니다. 이 코드를 사용하면 클라이언트에서 온-프레미스 서비스를 호출하고 호출 결과를 반환할 수 있습니다.

   ```csharp
   namespace ProductsWeb.Controllers
   {
       using System.Linq;
       using System.ServiceModel;
       using System.Web.Mvc;
       using Microsoft.ServiceBus;
       using Models;
       using ProductsServer;

       public class HomeController : Controller
       {
           // Declare the channel factory.
           static ChannelFactory<IProductsChannel> channelFactory;

           static HomeController()
           {
               // Create shared access signature token credentials for authentication.
               channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                   "sb://yourServiceNamespace.servicebus.windows.net/products");
               channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                   TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                       "RootManageSharedAccessKey", "yourKey") });
           }

           public ActionResult Index()
           {
               using (IProductsChannel channel = channelFactory.CreateChannel())
               {
                   // Return a view of the products inventory.
                   return this.View(from prod in channel.GetProducts()
                                    select
                                        new Product { Id = prod.Id, Name = prod.Name,
                                            Quantity = prod.Quantity });
               }
           }
       }
   }
   ```

1. **솔루션 탐색기** 에서 **ProductsPortal** 솔루션을 마우스 오른쪽 단추로 클릭합니다. 프로젝트가 아닌 솔루션을 마우스 오른쪽 단추로 클릭해야 합니다. **추가** > **기존 프로젝트** 를 선택합니다.
1. **ProductsServer** 프로젝트로 이동한 후 *ProductsServer.csproj* 솔루션 파일을 두 번 클릭하여 추가합니다.
1. **ProductsPortal** 에 대한 데이터를 표시하려면 **ProductsServer** 가 실행되고 있어야 합니다. **솔루션 탐색기** 에서 **ProductsPortal** 솔루션을 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택하여 **속성 페이지** 를 표시합니다.
1. **공용 속성** > **시작 프로젝트**, **여러 시작 프로젝트** 를 차례로 선택합니다. **ProductsServer** 및 **ProductsPortal** 이 해당 순서대로 표시되고 둘 다의 **작업** 이 **시작** 인지 확인합니다.

      ![여러 개의 시작 프로젝트][25]

1. 왼쪽에서 **공용 속성** > **프로젝트 종속성** 을 선택합니다.
1. **프로젝트** 에서 **ProductsPortal** 을 선택합니다. **ProductsServer** 가 선택되어 있는지 확인합니다.

    ![프로젝트 종속성][26]

1. **프로젝트** 에서 **ProductsServer** 를 선택합니다. **ProductsPortal** 이 선택되어 있지 않은지 확인하고 **확인** 을 선택하여 변경 내용을 저장합니다.

## <a name="run-the-project-locally"></a>로컬로 프로젝트 실행

애플리케이션을 로컬로 테스트하려면 Visual Studio에서 F5 키를 선택합니다. 온-프레미스 서버(**ProductsServer**)가 먼저 시작된 후 브라우저 창에서 **ProductsPortal** 애플리케이션이 시작되어야 합니다. 이제 제품 서비스 온-프레미스 시스템에서 검색된 제품 재고 목록 데이터가 표시됩니다.

![웹 애플리케이션][10]

**ProductsPortal** 페이지에서 **새로 고침** 을 선택합니다. 페이지를 새로 고칠 때마다 **ProductsServer** 에서 `GetProducts()`가 호출되면 서버 앱에 메시지가 표시되는 것을 확인하게 됩니다.

다음 섹션을 진행하기 전에 애플리케이션을 둘 다 닫습니다.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Azure 웹앱에 ProductsPortal 프로젝트 배포

다음 단계는 **ProductsPortal** 프런트 엔드를 Azure 웹앱으로 다시 게시하는 것입니다.

1. **솔루션 탐색기** 에서 **ProductsPortal** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시** 를 선택합니다. **게시** 페이지에서 **게시** 를 선택합니다.

   > [!NOTE]
   > 배포 후 **ProductsPortal** 웹 프로젝트가 자동으로 시작되면 브라우저 창에 오류 메시지가 표시될 수 있습니다. 예상된 동작이며 **ProductsServer** 애플리케이션이 아직 실행되지 않기 때문에 발생합니다.
   >

1. 배포된 웹앱의 URL을 복사합니다. 이 URL은 나중에 필요합니다. Visual Studio의 **Azure App Service 작업** 창에서 이 URL을 가져올 수도 있습니다.

   ![배포된 앱의 URL][9]

1. 실행 중인 애플리케이션을 중지하려면 브라우저 창을 닫습니다.

<a name="set-productsportal-as-web-app"></a>클라우드에서 애플리케이션을 실행하기 전에 Visual Studio 내에서 **ProductsPortal** 이 웹앱으로 시작되었는지 확인해야 합니다.

1. Visual Studio에서 **ProductsPortal** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다.
1. **웹** 을 선택합니다. **시작 작업** 에서 **시작 URL** 을 선택합니다. 이전에 배포한 웹앱의 URL(예제에서는 `https://productsportal20190906122808.azurewebsites.net/`)을 입력합니다.

    ![시작 URL][27]

1. **파일** > **모두 저장** 을 선택합니다.
1. **빌드** > **솔루션 다시 빌드** 를 선택합니다.

## <a name="run-the-application"></a>애플리케이션 실행

F5 키를 선택하여 애플리케이션을 빌드하고 실행합니다. **ProductsServer** 콘솔 애플리케이션인 온-프레미스 서버가 먼저 시작된 후에 아래와 같이 **ProductsPortal** 애플리케이션이 브라우저 창에서 시작되어야 합니다.

   ![Azure에서 웹앱 실행][1]

제품 인벤토리에 제품 서비스 온-프레미스 시스템에서 검색된 데이터가 나열되며 해당 데이터가 웹앱에 표시됩니다. URL을 확인하여 **ProductsPortal** 이 클라우드에서 Azure 웹앱으로 실행 중인지 확인합니다.

   > [!IMPORTANT]
   > **ProductsServer** 콘솔 애플리케이션이 실행 중이어야 하며 **ProductsPortal** 애플리케이션에 데이터를 제공할 수 있어야 합니다. 브라우저에 오류가 표시되면 **ProductsServer** 가 다음 메시지를 로드하고 표시할 때까지 몇 초 정도 기다렸다가 브라우저를 새로 고칩니다.
   >

브라우저에서 **ProductsPortal** 페이지를 새로 고칩니다. 페이지를 새로 고칠 때마다 **ProductsServer** 에서 `GetProducts()`가 호출되면 서버 앱에 메시지가 표시되는 것을 확인하게 됩니다.

![업데이트된 출력][38]

## <a name="next-steps"></a>다음 단계

다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
>[네트워크 외부의 WCF 클라이언트에 온-프레미스 WCF 서비스 노출](service-bus-relay-tutorial.md)

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app.png
[NuGet]: https://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/configure-productsserver.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/install-nuget-service-bus-productsserver.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/choose-web-application-template.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-class-productsportal.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/change-authentication.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/web-publish-activity.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally-no-content.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-existing-item-link.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/launch-app-as-web-app.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png