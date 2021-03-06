---
title: "Linux VM에 대한 포트 및 끝점 열기 | Microsoft Docs"
description: "Azure Resource Manager 배포 모드 및 Azure CLI를 사용하여 Linux VM에 대한 포트를 열고 끝점을 만드는 방법 알아보기"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: eef9842b-495a-46cf-99a6-74e49807e74e
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/27/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 5dd20630580f09049c88ffd9107f7fa8e8e43816
ms.openlocfilehash: 0e5e7b2c0637db3d20cbe2e6f00a23cb9d7fb51f


---
# <a name="opening-ports-and-endpoints-to-a-linux-vm-in-azure"></a>Azure에서 Linux VM에 대한 끝점 및 포트 열기
서브넷 또는 VM 네트워크 인터페이스에서 네트워크 필터를 만들어, Azure에서 VM(가상 컴퓨터)에 대한 포트를 열거나 끝점을 만듭니다. 인바운드 및 아웃바운드 트래픽을 모두 제어하는 이러한 필터를 트래픽을 수신하는 리소스에 연결된 네트워크 보안 그룹에 배치합니다. 포트 80에서 웹 트래픽의 일반적인 예제를 사용해 보겠습니다.

## <a name="quick-commands"></a>빠른 명령
네트워크 보안 그룹과 규칙을 만들려면 [Azure CLI](../xplat-cli-install.md)가 설치되어 있어야 하고 다음과 같은 Resource Manager 모드를 사용해야 합니다.

```azurecli
azure config mode arm
```

다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에 `myResourceGroup`, `myNetworkSecurityGroup` 및 `myVnet`가 포함됩니다.

고유한 이름 및 위치를 적절히 입력하여 네트워크 보안 그룹을 만듭니다. 다음 예제에서는 `WestUS` 위치에 `myNetworkSecurityGroup`이라는 네트워크 보안 그룹을 만듭니다.

```azurecli
azure network nsg create --resource-group myResourceGroup --location westus \
    --name myNetworkSecurityGroup
```

웹 서버에 대한 HTTP 트래픽을 허용하는 규칙을 추가하거나 SSH 액세스 또는 데이터베이스 연결 등 사용자 고유의 시나리오에 맞게 조정합니다. 다음 예제에서는 80 포트에서 TCP 트래픽을 허용하도록 `myNetworkSecurityGroupRule`이라는 규칙을 만듭니다.

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 80 --access allow
```

네트워크 보안 그룹을 VM의 네트워크 인터페이스(NIC)에 연결합니다. 다음 예제에서는 `myNic`라는 기존 NIC를 `myNetworkSecurityGroup`이라는 네트워크 보안 그룹에 연결합니다.

```azurecli
azure network nic set --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup --name myNic
```

또는 네트워크 보안 그룹을 단일 VM의 네트워크 인터페이스가 아니라 가상 네트워크 서브넷에 연결할 수 있습니다. 다음 예제에서는 `myVnet` 가상 네트워크에서 `mySubnet`이라는 기존 서브넷을 `myNetworkSecurityGroup`이라는 네트워크 보안 그룹에 연결합니다.

```azurecli
azure network vnet subnet set --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --vnet-name myVnet --name mySubnet
```

## <a name="more-information-on-network-security-groups"></a>네트워크 보안 그룹에 대한 자세한 정보
여기서 빠른 명령을 사용하면 VM으로 트래픽이 이동되도록 할 수 있습니다. 네트워크 보안 그룹은 리소스에 대한 액세스를 제어하는 많은 기능과 세분성을 제공합니다. [여기서 네트워크 보안 그룹 및 ACL 규칙 만들기](../virtual-network/virtual-networks-create-nsg-arm-cli.md)에 대해 자세히 읽어보세요.

네트워크 보안 그룹 및 ACL 규칙을 Azure Resource Manager 템플릿의 일부로 정의할 수도 있습니다. [템플릿을 사용하여 네트워크 보안 그룹 만들기](../virtual-network/virtual-networks-create-nsg-arm-template.md)에 대해 자세히 읽어보세요.

포트 전달을 사용하여 고유한 외부 포트를 VM의 내부 포트에 매핑해야 하는 경우 부하 분산 장치 및 NAT(네트워크 주소 변환) 규칙을 사용합니다. 예를 들어 TCP 포트 8080을 외부에 노출하고 트래픽이 VM의 TCP 포트 80으로 전달되도록 할 수 있습니다. [인터넷 연결 부하 분산 장치 만들기](../load-balancer/load-balancer-get-started-internet-arm-cli.md)에 대해 자세히 알아볼 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 예제에서는 HTTP 트래픽을 허용하는 간단한 규칙을 만들었습니다. 다음 문서에서 보다 자세한 환경을 만들기 위한 정보를 찾을 수 있습니다.

* [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md)
* [NSG(네트워크 보안 그룹)란?](../virtual-network/virtual-networks-nsg.md)
* [부하 분산 장치에 대한 Azure Resource Manager 개요](../load-balancer/load-balancer-arm.md)




<!--HONumber=Nov16_HO3-->


