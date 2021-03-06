## Azure 포털에서 클래식 VNet을 만드는 방법
위의 시나리오에 따라 클래식 VNet을 만들려면 다음 단계를 수행합니다.

1. 브라우저에서 http://portal.azure.com으로 이동하고 필요한 경우 Azure 계정으로 로그인합니다.
2. 아래 그림과 같이 **새로 만들기** > **네트워킹** > **가상 네트워크**를 클릭하고 **배포 모델 선택** 목록에 **클래식**이 이미 표시되는지 확인한 후 **만들기**를 클릭합니다.
   
    ![Azure 포털에서 VNet 만들기](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
3. **가상 네트워크** 블레이드에서 VNet의 **이름**을 입력하고 **주소 공간**을 클릭합니다. VNet 및 첫 번째 서브넷에 대한 주소 공간 설정을 구성하고 **확인**을 클릭합니다. 아래 그림에서는 이 시나리오에 대한 CIDR 블록 설정을 보여 줍니다.
   
    ![주소 공간 블레이드](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
4. **리소스 그룹**을 클릭하고 VNet을 추가할 리소스 그룹을 클릭하거나 **새 리소스 그룹 만들기**를 클릭하여 새 리소스 그룹에 VNet을 추가합니다. 아래 그림은 **TestRG**라는 새 리소스 그룹에 대한 리소스 그룹 설정을 보여 줍니다. 리소스 그룹에 대한 자세한 내용은 [Azure 리소스 관리자 개요](../articles/resource-group-overview.md#resource-groups)를 참조하세요.
   
    ![리소스 그룹 만들기 블레이드](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
5. 필요한 경우 VNet에 대한 **구독** 및 **위치** 설정을 변경합니다.
6. VNet을 **시작 보드**에 타일로 표시하지 않으려면 **시작 보드에 고정**을 사용하지 않도록 설정합니다.
7. **만들기**를 클릭하고 아래 그림과 같이 **가상 네트워크를 만드는 중** 타일이 표시되는지 확인합니다.
   
    ![포털에서 VNet 만들기](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
8. VNet이 만들어질 때까지 기다렸다가 아래 타일이 표시되면 클릭하여 서브넷을 더 추가합니다.
   
    ![포털에서 VNet 만들기](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
9. 아래와 같이 VNet에 대한 **구성**이 표시되어야 합니다.
   
    ![포털에서 VNet 만들기](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
10. **서브넷** > **추가**를 클릭하고 서브넷의 **이름**을 입력하고 **주소 범위(CIDR 블록)**를 지정한 후 **확인**을 클릭합니다. 아래 그림에서는 현재 시나리오에 대한 설정을 보여 줍니다.
    
    ![Azure 포털에서 VNet 만들기](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

<!---HONumber=AcomDC_0810_2016-->