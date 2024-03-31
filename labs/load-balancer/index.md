# Exercise - Create a load balancer, and VMs

Get started with Azure Load Balancer by using the Azure portal to create a public load balancer for a backend pool with two virtual machines. Other resources include Azure Bastion, NAT Gateway, a virtual network, and the required subnets.

![Diagram of resources deployed for a standard load balancer implement.](/Users/jruels/Library/CloudStorage/Dropbox-IIS/Training/Azure Fundamentals/classpage/labs/load-balancer/images/public-load-balancer-overview.png)



## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).



## Create NAT gateway

In this section, you'll create a NAT gateway for outbound internet access for resources in the virtual network. 

1. Sign in to the [Azure portal](https://portal.azure.com/).

2. In the search box at the top of the portal, enter **NAT gateway**. Select **NAT gateways** in the search results.

3. Select **+ Create**.

4. In the **Basics** tab of **Create network address translation (NAT) gateway** enter or select the following information:

   | Setting                | Value                                                        |
   | :--------------------- | :----------------------------------------------------------- |
   | **Project details**    |                                                              |
   | Subscription           | Select your subscription.                                    |
   | Resource group         | Select **Create new**. Enter **load-balancer-rg** in Name. Select **OK**. |
   | **Instance details**   |                                                              |
   | NAT gateway name       | Enter **lb-nat-gateway**.                                    |
   | Region                 | Select **East US**.                                          |
   | Availability zone      | Select **None**.                                             |
   | Idle timeout (minutes) | Enter **15**.                                                |

   ![Screenshot of Create network address translation gateway window in the Azure portal.](/Users/jruels/Library/CloudStorage/Dropbox-IIS/Training/Azure Fundamentals/classpage/labs/load-balancer/images/create-nat-gateway.png)

5. Select the **Outbound IP** tab or select the **Next: Outbound IP** button at the bottom of the page.

6. Select **Create a new public IP address** under **Public IP addresses**.

7. Enter **nat-gw-public-ip** in **Name** in **Add a public IP address**.

8. Select **OK**.

9. Select the blue **Review + create** button at the bottom of the page, or select the **Review + create** tab.

10. Select **Create**.



## Create a virtual network and bastion host

In this section, you creates a virtual network with a resource subnet, an Azure Bastion subnet, and an Azure Bastion host.

**Important**

Hourly pricing starts from the moment that Bastion is deployed, regardless of outbound data usage. 

1. In the portal, search for and select **Virtual networks**.

2. On the **Virtual networks** page, select **+ Create**.

3. On the **Basics** tab of **Create virtual network**, enter or select the following information:

   | Setting              | Value                                                        |
   | :------------------- | :----------------------------------------------------------- |
   | **Project details**  |                                                              |
   | Subscription         | Select your subscription.                                    |
   | Resource group       | Select **Create new**. Enter **load-balancer-rg** in Name. Select **OK**. |
   | **Instance details** |                                                              |
   | Name                 | Enter **lb-vnet**.                                           |
   | Region               | Select **East US**.                                          |

   ![Screenshot of Basics tab of Create virtual network in the Azure portal.](/Users/jruels/Library/CloudStorage/Dropbox-IIS/Training/Azure Fundamentals/classpage/labs/load-balancer/images/create-virtual-network-basics.png)

4. Select the **Security** tab or **Next** button at the bottom of the page.

5. Under **Azure Bastion**, enter or select the following information:

   | Setting                         | Value                                                        |
   | :------------------------------ | :----------------------------------------------------------- |
   | **Azure Bastion**               |                                                              |
   | Enable Azure Bastion            | Select checkbox.                                             |
   | Azure Bastion host name         | Enter **lb-bastion**.                                        |
   | Azure Bastion public IP address | Select **Create new**. Enter **lb-bastion-ip** in Name. Select **OK**. |

6. Select the **IP addresses** tab, or **Next** at the bottom of the page.

7. On **Create virtual network** page, enter or select the following information:

   | Setting                    | Value                                       |
   | :------------------------- | :------------------------------------------ |
   | **Add IPv4 address space** |                                             |
   | IPv4 address space         | Enter **10.0.0.0/16 (65,356 addresses)**.   |
   | **Subnets**                | Select the **default** subnet link to edit. |
   | Subnet template            | Leave the default **Default**.              |
   | Name                       | Enter **backend-subnet**.                   |
   | Starting address           | Enter **10.0.0.0**.                         |
   | Subnet size                | Enter **/24(256 addresses)**.               |
   | **Security**               |                                             |
   | NAT Gateway                | Select **lb-nat-gateway**.                  |

   ![Screenshot of default subnet rename and configuration.](/Users/jruels/Library/CloudStorage/Dropbox-IIS/Training/Azure Fundamentals/classpage/labs/load-balancer/images/edit-subnet-window.png)

8. Select **Save**.

9. Select **Review + create** at the bottom of the screen, and when validation passes, select **Create**.



## Create load balancer

In this section, you create a zone redundant load balancer that load balances virtual machines. With zone-redundancy, one or more availability zones can fail and the data path survives as long as one zone in the region remains healthy.

During the creation of the load balancer, you configure:

- Frontend IP address
- Backend pool
- Inbound load-balancing rules
- Health probe

1. In the search box at the top of the portal, enter **Load balancer**. Select **Load balancers** in the search results.

2. In the **Load balancer** page, select **+ Create**.

3. In the **Basics** tab of the **Create load balancer** page, enter or select the following information:

   | Setting              | Value                          |
   | :------------------- | :----------------------------- |
   | **Project details**  |                                |
   | Subscription         | Select your subscription       |
   | Resource group       | Select **load-balancer-rg**    |
   | **Instance details** |                                |
   | Name                 | Enter **load-balancer**        |
   | Region               | Select **East US**             |
   | SKU                  | Leave the default **Standard** |
   | Type                 | Select **Public**              |
   | Tier                 | Leave the default **Regional** |

   ![Screenshot of create standard load balancer basics tab.](/Users/jruels/Library/CloudStorage/Dropbox-IIS/Training/Azure Fundamentals/classpage/labs/load-balancer/images/create-standard-load-balancer.png)

4. Select **Next: Frontend IP configuration** at the bottom of the page.

5. In **Frontend IP configuration**, select **+ Add a frontend IP configuration**.

6. Enter **lb-frontend** in **Name**.

7. Select **IPv4** for the **IP version**.

8. Select **IP address** for the **IP type**.

9. Select **Create new** in **Public IP address**.

10. In **Add a public IP address**, enter **lb-frontend-ip** for **Name**.

11. Select **Zone-redundant** in **Availability zone**.

     **Note**

    In regions with Availability Zones, you have the option to select no-zone (default option), a specific zone, or zone-redundant. The choice will depend on your specific domain failure requirements. In regions without Availability Zones, this field won't appear.

12. Leave the default of **Microsoft Network** for **Routing preference**.

13. Select **OK**.

14. Select **Add**.

15. Select **Next: Backend pools** at the bottom of the page.

16. In the **Backend pools** tab, select **+ Add a backend pool**.

17. Enter **lb-backend-pool** for **Name** in **Add backend pool**.

18. Select **lb-vnet** in **Virtual network**.

19. Select **IP Address** for **Backend Pool Configuration**.

20. Select **Save**.

21. Select **Next: Inbound rules** at the bottom of the page.

22. Under **Load balancing rule** in the **Inbound rules** tab, select **+ Add a load balancing rule**.

23. In **Add load balancing rule**, enter or select the following information:

    | Setting                                            | Value                                                        |
    | :------------------------------------------------- | :----------------------------------------------------------- |
    | Name                                               | Enter **lb-HTTP-rule**                                       |
    | IP Version                                         | Select **IPv4** or **IPv6** depending on your requirements   |
    | Frontend IP address                                | Select **lb-frontend (To be created)**                       |
    | Backend pool                                       | Select **lb-backend-pool**                                   |
    | Protocol                                           | Select **TCP**                                               |
    | Port                                               | Enter **80**                                                 |
    | Backend port                                       | Enter **80**                                                 |
    | Health probe                                       | Select **Create new**. In **Name**, enter **lb-health-probe**. Select **HTTP** in **Protocol**. Leave the rest of the defaults, and select **Save**. |
    | Session persistence                                | Select **None**.                                             |
    | Idle timeout (minutes)                             | Enter or select **15**                                       |
    | Enable TCP reset                                   | Select checkbox                                              |
    | Enable Floating IP                                 | Leave unchecked                                              |
    | Outbound source network address translation (SNAT) | Leave the default of **(Recommended) Use outbound rules to provide backend pool members access to the internet.** |

24. Select **Save**.

25. Select the blue **Review + create** button at the bottom of the page.

26. Select **Create**.

     **Note**

    In this example we'll create a NAT gateway to provide outbound Internet access. The outbound rules tab in the configuration is bypassed as it's optional and isn't needed with the NAT gateway.



## Create virtual machines

In this section, you create two VMs (**lb-vm1** and **lb-VM2**) in two different zones (**Zone 1** and **Zone 2**).

These VMs are added to the backend pool of the load balancer that was created earlier.

1. In the search box at the top of the portal, enter **Virtual machine**. Select **Virtual machines** in the search results.

2. In **Virtual machines**, select **+ Create** > **Azure virtual machine**.

3. In **Create a virtual machine**, enter or select the following values in the **Basics** tab:

   | Setting                   | Value                                                        |
   | :------------------------ | :----------------------------------------------------------- |
   | **Project Details**       |                                                              |
   | Subscription              | Select your Azure subscription                               |
   | Resource Group            | Select **load-balancer-rg**                                  |
   | **Instance details**      |                                                              |
   | Virtual machine name      | Enter **lb-VM1**                                             |
   | Region                    | Select **((US) East US)**                                    |
   | Availability Options      | Select **Availability zones**                                |
   | Availability zone         | Select **Zone 1**                                            |
   | Security type             | Select **Standard**.                                         |
   | Image                     | Select **Windows Server 2022 Datacenter: Azure Edition - Gen2** |
   | Azure Spot instance       | Leave the default of unchecked.                              |
   | Size                      | Choose VM size or take default setting                       |
   | **Administrator account** |                                                              |
   | Username                  | Enter a username                                             |
   | Password                  | Enter a password                                             |
   | Confirm password          | Reenter password                                             |
   | **Inbound port rules**    |                                                              |
   | Public inbound ports      | Select **None**                                              |

4. Select the **Networking** tab, or select **Next: Disks**, then **Next: Networking**.

5. In the Networking tab, select or enter the following information:

   | Setting                          | Value                                                        |
   | :------------------------------- | :----------------------------------------------------------- |
   | **Network interface**            |                                                              |
   | Virtual network                  | Select **lb-vnet**                                           |
   | Subnet                           | Select **backend-subnet**                                    |
   | Public IP                        | Select **None**.                                             |
   | NIC network security group       | Select **Advanced**                                          |
   | Configure network security group | Skip this setting until the rest of the settings are completed. Complete after **Select a backend pool**. |
   | Delete NIC when VM is deleted    | Leave the default of **unselected**.                         |
   | Accelerated networking           | Leave the default of **selected**.                           |
   | **Load balancing**               |                                                              |
   | **Load balancing options**       |                                                              |
   | Load-balancing options           | Select **Azure load balancer**                               |
   | Select a load balancer           | Select **load-balancer**                                     |
   | Select a backend pool            | Select **lb-backend-pool**                                   |
   | Configure network security group | Select **Create new**. In the **Create network security group**, enter **lb-NSG** in **Name**. Under **Inbound rules**, select **+Add an inbound rule**. In **Service**, select **HTTP**. Under **Priority**, enter **100**. In **Name**, enter **lb-NSG-Rule** Select **Add** Select **OK** |

6. Select **Review + create**.

7. Review the settings, and then select **Create**.

8. Follow the steps 1 through 7 to create another VM with the following values and all the other settings the same as **lb-VM1**:

   | Setting                | VM 2                           |
   | :--------------------- | :----------------------------- |
   | Name                   | **lb-VM2**                     |
   | Availability zone      | **Zone 2**                     |
   | Network security group | Select the existing **lb-NSG** |

 

**Note**

Azure provides a default outbound access IP for VMs that either aren't assigned a public IP address or are in the backend pool of an internal basic Azure load balancer. The default outbound access IP mechanism provides an outbound IP address that isn't configurable.

The default outbound access IP is disabled when one of the following events happens:

- A public IP address is assigned to the VM.
- The VM is placed in the backend pool of a standard load balancer, with or without outbound rules.
- An Azure Virtual Network NAT gateway resource is assigned to the subnet of the VM.

VMs that you create by using virtual machine scale sets in flexible orchestration mode don't have default outbound access.



## Install IIS

1. In the search box at the top of the portal, enter **Virtual machine**. Select **Virtual machines** in the search results.

2. Select **myVM1**.

3. On the **Overview** page, select **Connect**, then **Bastion**.

4. Enter the username and password entered during VM creation.

5. Select **Connect**.

6. On the server desktop, navigate to **Start** > **Windows PowerShell** > **Windows PowerShell**.

7. In the PowerShell Window, run the following commands to:

   - Install the IIS server.
   - Remove the default iisstart.htm file.
   - Add a new iisstart.htm file that displays the name of the VM:

   ```powershell
    # Install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
   
    # Remove default htm file
    Remove-Item  C:\inetpub\wwwroot\iisstart.htm
   
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```

8. Close the Bastion session with **myVM1**.

9. Repeat steps 1 to 8 to install IIS and the updated iisstart.htm file on **myVM2**.



## Test the load balancer

1. In the search box at the top of the page, enter **Public IP**. Select **Public IP addresses** in the search results.

2. In **Public IP addresses**, select **frontend-ip**.

3. Copy the item in **IP address**. Paste the public IP into the address bar of your browser. The custom VM page of the IIS Web server is displayed in the browser.

   ![Screenshot of load balancer test](/Users/jruels/Library/CloudStorage/Dropbox-IIS/Training/Azure Fundamentals/classpage/labs/load-balancer/images/load-balancer-test.png)



## Clean up resources

When no longer needed, delete the resource group, load balancer, and all related resources. To do so, select the resource group **load-balancer-rg** that contains the resources and then select **Delete**.