# VM Fleet Commander (Deploy and manage Azure compute resources)
Implement an infrastructure-as-code approach to provision and manage virtual machines in Azure, using ARM templates and Bicep. The aim is to gain hands-on experience in automating the deployment of Azure resources and organizing them efficiently.

- **Programming required?**: ✅ (Knowledge of JSON for ARM templates and Bicep language syntax is essential.)
- **Azure Services Used:**
  - Azure Virtual Machines
  - Azure Resource Manager (ARM)
  - Bicep
  
- **Steps**:

   1. **Initial Setup**:
      - Ensure you have Azure CLI installed with Bicep support.
      - Set up a version control system (e.g., Git) to track changes in your Bicep and ARM templates.

   2. **Bicep Basics**:
      - Start with learning the basics of Bicep syntax and structure.
      - Convert a basic ARM template (like one that deploys a single VM) to Bicep to understand the differences.

   3. **Resource Group and Naming Conventions**:
      - Define a Bicep file to create an Azure Resource Group for your VMs.
      - Implement naming conventions for your resources using Bicep's string functions.

   4. **Virtual Machine Provisioning**:
      - Create a Bicep module for deploying Azure VMs, allowing for parameterized input like VM size, name, and region.
      - Use loops in Bicep to deploy multiple VM instances based on a specified count.

   5. **Network Resources**:
      - Design Bicep modules for associated networking resources like Virtual Network, Subnet, and Network Security Groups.
      - Ensure your VMs are provisioned within the designated VNet and have the necessary security rules applied.

   6. **Parameter Files and Validation**:
      - Create separate parameter files for your Bicep templates, allowing for different environment deployments (e.g., dev, test, prod).
      - Use the Azure CLI to validate your Bicep files before deploying, catching any structural errors.

   7. **Deployment**:
      - Use the Azure CLI to deploy your Bicep templates, creating all designated resources.
      - Test the reproducibility by deploying the same infrastructure to a different region or resource group.

   8. **Maintenance & Updates**:
      - Make changes to your Bicep files (e.g., VM size or count) and redeploy. Observe how Azure handles updates and maintains state.
      - Regularly pull updates to the Bicep language and Azure CLI to stay updated with new features and improvements.

   9. **Documentation & Cleanup**:
      - Document your Bicep modules, their purpose, and any parameters required.
      - After testing, ensure to delete resources or resource groups to avoid incurring unnecessary costs.
     

                             +------------------------------------------------------+
                             |                    Azure Environment                  |
                             +------------------------------------------------------+
                                                |                         |
                                                |                         |
                            +-------------------+                         +-------------------+
                            |                                        |                      |
                            v                                        v                      v
            +------------------------------+       +------------------------------+       +------------------------------+
            |          Office 1             |       |          VPN Gateway         |       |          Office 2             |
            |       (Location 1)            |       +------------------------------+       |       (Location 2)            |
            |  VNet: Office1VNet (10.1.0.0/16)|<---->|     VPN Gateway Connection   |<----->|  VNet: Office2VNet (10.2.0.0/16)|
            +------------------------------+       | (VPN: Site-to-Site between    |       +------------------------------+
                |          |          |            |    Office1VNet & Office2VNet) |            |          |          |
                v          v          v            +------------------------------+            v          v          v
    +---------------+ +---------------+ +---------------+                                      +---------------+ +---------------+ +---------------+
    | DevSubnet     | | UATSubnet1    | | UATSubnet2    |                                      | DevSubnet     | | UATSubnet1    | | UATSubnet2    |
    | (10.1.1.0/24) | | (10.1.2.0/24) | | (10.1.3.0/24) |                                      | (10.2.1.0/24) | | (10.2.2.0/24) | | (10.2.3.0/24) |
    +---------------+ +---------------+ +---------------+                                      +---------------+ +---------------+ +---------------+
          |               |               |                                                       |               |               |
          v               v               v                                                       v               v               v
    +------------+   +------------+   +------------+                                         +------------+   +------------+   +------------+
    | Dev-VM-1   |   | UAT1-VM-1  |   | UAT2-VM-1  |                                         | Dev-VM-2   |   | UAT1-VM-2  |   | UAT2-VM-2  |
    +------------+   +------------+   +------------+                                         +------------+   +------------+   +------------+
      (10.1.1.4)       (10.1.2.4)       (10.1.3.4)                                             (10.2.1.4)       (10.2.2.4)       (10.2.3.4)

                +-------------------------------+                                    +-------------------------------+
                | FileShareSubnet                |                                    | FileShareSubnet                |
                | (10.1.5.0/24)                  |                                    | (10.2.5.0/24)                  |
                +-------------------------------+                                    +-------------------------------+
                                |                                                                  |
                                v                                                                  v
                    +----------------+                                                    +----------------+
                    | FileShare-VM-1 |                                                    | FileShare-VM-2 |
                    +----------------+                                                    +----------------+
                      (10.1.5.4)                                                            (10.2.5.4)


                             +-------------------------+             +-------------------------+
                             |    Network Security     |             |    Network Security     |
                             |    NSG DevSubnet        |             |    NSG UATSubnet1       |
                             +-------------------------+             +-------------------------+
                                 |            |                         |            |
                                 v            v                         v            v
                               Rule 1       Rule 2                    Rule 1       Rule 2
                               Allow        Deny                      Allow        Deny

                             +-------------------------+             +-------------------------+
                             |    Network Security     |             |    Network Security     |
                             |    NSG UATSubnet2       |             |    NSG FileShareSubnet  |
                             +-------------------------+             +-------------------------+
                                 |            |                         |            |
                                 v            v                         v            v
                               Rule 1       Rule 2                    Rule 1       Rule 2
                               Allow        Deny                      Allow        Deny

                           +-----------------------------------------------------+
                           |        Role-Based Access Control (RBAC)            |
                           +-----------------------------------------------------+
                               |                |                |
                               v                v                v
                           System Dept      Dev Dept         HR Dept
                            (Full Access)   (VM Access)      (FileShare Access)
