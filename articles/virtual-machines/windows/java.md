---
title: Java를 사용하여 Azure Virtual Machine 만들기 및 관리
description: Java 및 Azure Resource Manager를 사용하여 가상 컴퓨터 및 모든 지원 리소스를 배포합니다.
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure
ms.topic: how-to
ms.date: 07/17/2017
ms.custom: devx-track-java
ms.author: cynthn
ms.openlocfilehash: ec738d2f2765cd97c518b79081675c197ead6466
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697207"
---
# <a name="create-and-manage-windows-vms-in-azure-using-java"></a>Java를 사용하여 Azure에서 Windows VM 만들기 및 관리

**적용 대상:** :heavy_check_mark: Windows VM 

[Azure VM(Virtual Machine)](overview.md)에 몇 가지 지원 Azure 리소스가 필요합니다. 이 문서에서는 Java를 사용하여 VM 리소스 만들기, 관리 및 삭제에 대해 설명합니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * Maven 프로젝트 만들기
> * 종속성 추가
> * 자격 증명 만들기
> * 리소스 만들기
> * 관리 작업 수행
> * 리소스 삭제
> * 애플리케이션 실행

이러한 단계를 수행하려면 약 20분이 걸립니다.

## <a name="create-a-maven-project"></a>Maven 프로젝트 만들기

1. 아직 수행하지 않았다면 [Java](/azure/developer/java/fundamentals/java-support-on-azure)를 설치합니다.
2. [Maven](https://maven.apache.org/download.cgi)을 설치합니다.
3. 새 폴더와 프로젝트를 만듭니다.
    
    ```
    mkdir java-azure-test
    cd java-azure-test
    
    mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

## <a name="add-dependencies"></a>종속성 추가

1. `testAzureApp` 폴더에서 `pom.xml` 파일을 열고 빌드 구성을 &lt;프로젝트&gt;에 추가하여 애플리케이션의 빌드를 사용하도록 설정합니다.

    ```xml
    <build>
      <plugins>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <configuration>
                <mainClass>com.fabrikam.testAzureApp.App</mainClass>
            </configuration>
        </plugin>
      </plugins>
    </build>
    ```

2. Azure Java SDK에 액세스하는 데 필요한 종속성을 추가합니다.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-compute</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-resources</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-network</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.squareup.okio</groupId>
      <artifactId>okio</artifactId>
      <version>1.13.0</version>
    </dependency>
    <dependency>
      <groupId>com.nimbusds</groupId>
      <artifactId>nimbus-jose-jwt</artifactId>
      <version>3.6</version>
    </dependency>
    <dependency>
      <groupId>net.minidev</groupId>
      <artifactId>json-smart</artifactId>
      <version>1.0.6.3</version>
    </dependency>
    <dependency>
      <groupId>javax.mail</groupId>
      <artifactId>mail</artifactId>
      <version>1.4.5</version>
    </dependency>
    ```

3. 파일을 저장합니다.

## <a name="create-credentials"></a>자격 증명 만들기

이 단계를 시작하기 전에 [Active Directory 서비스 주체](../../active-directory/develop/howto-create-service-principal-portal.md)에 액세스할 수 있는지 확인합니다. 또한 이후 단계에서 필요한 애플리케이션 ID, 인증 키 및 테넌트 ID를 기록해 두어야 합니다.

### <a name="create-the-authorization-file"></a>권한 부여 파일 만들기

1. `azureauth.properties`라는 파일을 만들고 이러한 속성을 추가합니다.

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.microsoft.com/
    ```

    **&lt;subscription-id&gt;** 를 구독 식별자, **&lt;application-id&gt;** 를 Active Directory 애플리케이션 식별자, **&lt;authentication-key&gt;** 를 애플리케이션 키, **&lt;tenant-id&gt;** 를 테넌트 식별자로 바꿉니다.

2. 파일을 저장합니다.
3. 인증 파일에 전체 경로로 프로그램 셸의 AZURE_AUTH_LOCATION이라는 환경 변수를 설정합니다.

### <a name="create-the-management-client"></a>관리 클라이언트 만들기

1. `src\main\java\com\fabrikam`에 있는 `App.java` 파일을 열고 이 패키지 문이 위쪽에 있는지 확인합니다.

    ```java
    package com.fabrikam.testAzureApp;
    ```

2. 패키지 문에서 다음과 같은 import 문을 추가합니다.
   
    ```java
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.compute.AvailabilitySet;
    import com.microsoft.azure.management.compute.AvailabilitySetSkuTypes;
    import com.microsoft.azure.management.compute.CachingTypes;
    import com.microsoft.azure.management.compute.InstanceViewStatus;
    import com.microsoft.azure.management.compute.DiskInstanceView;
    import com.microsoft.azure.management.compute.VirtualMachine;
    import com.microsoft.azure.management.compute.VirtualMachineSizeTypes;
    import com.microsoft.azure.management.network.PublicIPAddress;
    import com.microsoft.azure.management.network.Network;
    import com.microsoft.azure.management.network.NetworkInterface;
    import com.microsoft.azure.management.resources.ResourceGroup;
    import com.microsoft.azure.management.resources.fluentcore.arm.Region;
    import com.microsoft.azure.management.resources.fluentcore.model.Creatable;
    import com.microsoft.rest.LogLevel;
    import java.io.File;
    import java.util.Scanner;
    ```

2. 요청을 해야 하는 Active Directory 자격 증명을 만들려면 App 클래스의 Main 메서드에 이 코드를 추가합니다.
   
    ```java
    try {
        final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));
        Azure azure = Azure.configure()
            .withLogLevel(LogLevel.BASIC)
            .authenticate(credFile)
            .withDefaultSubscription();
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }

    ```

## <a name="create-resources"></a>리소스 만들기

### <a name="create-the-resource-group"></a>리소스 그룹 만들기

모든 리소스는 [리소스 그룹](../../azure-resource-manager/management/overview.md)에 포함되어야 합니다.

애플리케이션의 값을 지정하고 리소스 그룹을 만들려면 Main 메서드의 try 블록에 이 코드를 추가합니다.

```java
System.out.println("Creating resource group...");
ResourceGroup resourceGroup = azure.resourceGroups()
    .define("myResourceGroup")
    .withRegion(Region.US_EAST)
    .create();
```

### <a name="create-the-availability-set"></a>가용성 집합 만들기

[가용성 집합](tutorial-availability-sets.md)은 애플리케이션에서 사용되는 가상 머신을 쉽게 유지 관리할 수 있도록 합니다.

가용성 집합을 만들려면 Main 메서드의 try 블록에 이 코드를 추가합니다.

```java
System.out.println("Creating availability set...");
AvailabilitySet availabilitySet = azure.availabilitySets()
    .define("myAvailabilitySet")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withSku(AvailabilitySetSkuTypes.MANAGED)
    .create();
```
### <a name="create-the-public-ip-address"></a>공용 IP 주소 만들기

[공용 IP 주소](../../virtual-network/public-ip-addresses.md)는 가상 머신과 통신해야 합니다.

가상 머신의 공용 IP 주소를 만들려면 Main 메서드의 try 블록에 이 코드를 추가합니다.

```java
System.out.println("Creating public IP address...");
PublicIPAddress publicIPAddress = azure.publicIPAddresses()
    .define("myPublicIP")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withDynamicIP()
    .create();
```

### <a name="create-the-virtual-network"></a>가상 네트워크 만들기

가상 머신은 [가상 네트워크](../../virtual-network/virtual-networks-overview.md)의 서브넷에 있어야 합니다.

서브넷 및 가상 네트워크를 만들려면 Main 메서드의 try 블록에 이 코드를 추가합니다.

```java
System.out.println("Creating virtual network...");
Network network = azure.networks()
    .define("myVN")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withAddressSpace("10.0.0.0/16")
    .withSubnet("mySubnet","10.0.0.0/24")
    .create();
```

### <a name="create-the-network-interface"></a>네트워크 인터페이스 만들기

가상 머신은 가상 네트워크에서 통신하기 위해 네트워크 인터페이스가 필요합니다.

네트워크 인터페이스를 만들려면 Main 메서드의 try 블록에 이 코드를 추가합니다.

```java
System.out.println("Creating network interface...");
NetworkInterface networkInterface = azure.networkInterfaces()
    .define("myNIC")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetwork(network)
    .withSubnet("mySubnet")
    .withPrimaryPrivateIPAddressDynamic()
    .withExistingPrimaryPublicIPAddress(publicIPAddress)
    .create();
```

### <a name="create-the-virtual-machine"></a>가상 머신 만들기

모든 지원 리소스를 만들었으므로 가상 머신을 만들 수 있습니다.

가상 머신을 만들려면 Main 메서드의 try 블록에 이 코드를 추가합니다.

```java
System.out.println("Creating virtual machine...");
VirtualMachine virtualMachine = azure.virtualMachines()
    .define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .withAdminUsername("azureuser")
    .withAdminPassword("Azure12345678")
    .withComputerName("myVM")
    .withExistingAvailabilitySet(availabilitySet)
    .withSize("Standard_DS1")
    .create();
Scanner input = new Scanner(System.in);
System.out.println("Press enter to get information about the VM...");
input.nextLine();
```

> [!NOTE]
> 이 자습서는 Windows Server 운영 체제의 버전을 실행하는 가상 머신을 만듭니다. 기타 이미지 선택에 대해 자세히 알아보려면 [Windows PowerShell 및 Azure CLI를 사용하여 Azure 가상 머신 탐색 및 선택](../linux/cli-ps-findimage.md)을 참조하세요.
> 
>

마켓플레이스 이미지 대신 기존 디스크를 사용하려면 다음 코드를 사용합니다. 

```java
ManagedDisk managedDisk = azure.disks.define("myosdisk")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .withSizeInGB(128)
    .withSku(DiskSkuTypes.PremiumLRS)
    .create();

azure.virtualMachines.define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .withExistingAvailabilitySet(availabilitySet)
    .withSize(VirtualMachineSizeTypes.StandardDS1)
    .create();
```

## <a name="perform-management-tasks"></a>관리 작업 수행

가상 머신의 수명 주기 동안 가상 머신 시작, 중지 또는 삭제 등의 관리 작업을 실행하려고 할 수 있습니다. 또한 반복적이거나 복잡한 작업을 자동화하는 코드를 만들 수도 있습니다.

VM에서 작업을 수행해야 하는 경우 VM의 인스턴스를 가져와야 합니다. Main 메서드의 try 블록에 이 코드를 추가합니다.

```java
VirtualMachine vm = azure.virtualMachines().getByResourceGroup("myResourceGroup", "myVM");
```

### <a name="get-information-about-the-vm"></a>VM 관련 정보 가져오기

가상 머신에 대한 정보를 가져오려면 Main 메서드의 try 블록에 이 코드를 추가합니다.

```java
System.out.println("hardwareProfile");
System.out.println("    vmSize: " + vm.size());
System.out.println("storageProfile");
System.out.println("  imageReference");
System.out.println("    publisher: " + vm.storageProfile().imageReference().publisher());
System.out.println("    offer: " + vm.storageProfile().imageReference().offer());
System.out.println("    sku: " + vm.storageProfile().imageReference().sku());
System.out.println("    version: " + vm.storageProfile().imageReference().version());
System.out.println("  osDisk");
System.out.println("    osType: " + vm.storageProfile().osDisk().osType());
System.out.println("    name: " + vm.storageProfile().osDisk().name());
System.out.println("    createOption: " + vm.storageProfile().osDisk().createOption());
System.out.println("    caching: " + vm.storageProfile().osDisk().caching());
System.out.println("osProfile");
System.out.println("    computerName: " + vm.osProfile().computerName());
System.out.println("    adminUserName: " + vm.osProfile().adminUsername());
System.out.println("    provisionVMAgent: " + vm.osProfile().windowsConfiguration().provisionVMAgent());
System.out.println("    enableAutomaticUpdates: " + vm.osProfile().windowsConfiguration().enableAutomaticUpdates());
System.out.println("networkProfile");
System.out.println("    networkInterface: " + vm.primaryNetworkInterfaceId());
System.out.println("vmAgent");
System.out.println("  vmAgentVersion: " + vm.instanceView().vmAgent().vmAgentVersion());
System.out.println("    statuses");
for(InstanceViewStatus status : vm.instanceView().vmAgent().statuses()) {
    System.out.println("    code: " + status.code());
    System.out.println("    displayStatus: " + status.displayStatus());
    System.out.println("    message: " + status.message());
    System.out.println("    time: " + status.time());
}
System.out.println("disks");
for(DiskInstanceView disk : vm.instanceView().disks()) {
    System.out.println("  name: " + disk.name());
    System.out.println("  statuses");
    for(InstanceViewStatus status : disk.statuses()) {
        System.out.println("    code: " + status.code());
        System.out.println("    displayStatus: " + status.displayStatus());
        System.out.println("    time: " + status.time());
    }
}
System.out.println("VM general status");
System.out.println("  provisioningStatus: " + vm.provisioningState());
System.out.println("  id: " + vm.id());
System.out.println("  name: " + vm.name());
System.out.println("  type: " + vm.type());
System.out.println("VM instance status");
for(InstanceViewStatus status : vm.instanceView().statuses()) {
    System.out.println("  code: " + status.code());
    System.out.println("  displayStatus: " + status.displayStatus());
}
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="stop-the-vm"></a>VM을 중지합니다.

가상 머신을 중지하고 해당 설정을 모두 그대로 유지하면 계속 요금이 청구될 수 있습니다. 그렇지 않으려면 가상 머신을 중지하고 할당을 해제합니다. 가상 머신을 할당을 해제하면 연결된 모든 리소스의 할당이 취소되고 대금 청구가 끝납니다.

할당을 취소하지 않고 가상 머신을 중지하려면 Main 메서드의 try 블록에 이 코드를 추가합니다.

```java
System.out.println("Stopping vm...");
vm.powerOff();
System.out.println("Press enter to continue...");
input.nextLine();
```

가상 머신의 할당을 취소하려는 경우 PowerOff 호출을 이 코드로 변경합니다.

```java
vm.deallocate();
```

### <a name="start-the-vm"></a>VM 시작

가상 머신을 시작하려면 Main 메서드의 try 블록에 이 코드를 추가합니다.

```java
System.out.println("Starting vm...");
vm.start();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="resize-the-vm"></a>VM 크기 조정

가상 머신의 크기를 결정할 때 배포의 여러 측면을 고려해야 합니다. 자세한 내용은 [VM 크기](../sizes.md)를 참조하세요.  

가상 머신의 크기를 변경하려면 Main 메서드의 try 블록에 이 코드를 추가합니다.

```java
System.out.println("Resizing vm...");
vm.update()
    .withSize(VirtualMachineSizeTypes.STANDARD_DS2)
    .apply();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>VM에 데이터 디스크 추가

가상 컴퓨터에 크기가 2GB이고 LUN이 0이며 캐싱 형식이 읽기/쓰기인 데이터 디스크를 추가하려면 Main 메서드의 try 블록에 이 코드를 추가합니다.

```java
System.out.println("Adding data disk...");
vm.update()
    .withNewDataDisk(2, 0, CachingTypes.READ_WRITE)
    .apply();
System.out.println("Press enter to delete resources...");
input.nextLine();
```

## <a name="delete-resources"></a>리소스 삭제

Azure에서 사용되는 리소스에 대한 요금이 부과되기 때문에, 더 이상 필요하지 않은 리소스를 항상 삭제하는 것이 좋습니다. 가상 머신 및 모든 지원 리소스를 삭제하려는 경우, 리소스 그룹을 삭제해야 합니다.

1. 리소스 그룹을 삭제하려면 Main 메서드의 try 블록에 이 코드를 추가합니다.
   
    ```java
    System.out.println("Deleting resources...");
    azure.resourceGroups().deleteByName("myResourceGroup");
    ```

2. App.java 파일을 저장합니다.

## <a name="run-the-application"></a>애플리케이션 실행

이 콘솔 애플리케이션을 처음부터 끝까지 완전히 실행하려면 약 5분이 필요합니다.

1. 애플리케이션을 실행하려면 이 Maven 명령을 사용합니다.

    ```
    mvn compile exec:java
    ```

2. **Enter** 키를 눌러 리소스를 삭제하기 전에 Azure Portal에서 리소스 만들기를 확인하는 데에 몇 분이 걸릴 수 있습니다. 배포에 대한 정보를 보려면 배포 상태를 클릭합니다.


## <a name="next-steps"></a>다음 단계
* [Java용 Azure 라이브러리](/java/azure/java-sdk-azure-overview) 사용에 대해 자세히 알아보세요.