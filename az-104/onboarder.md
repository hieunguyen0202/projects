## Onboard Automator (Manage Azure identities and governance)
Streamline and automate the process of onboarding a new employee into Azure AD and assigning necessary Azure resources.

- **Programming required?**: ❌
- **Azure Services Used:**
  - Azure AD
  - Azure Logic Apps
  - Azure Email Service (part of Logic Apps connector)
  - Azure Resource Manager
  
- **Steps**:
   1. **Azure AD Setup**:
        - Set up a new Azure AD instance (if not already present) using the Azure portal.
   
   2. **Logic App Workflow Design**:
        - Design a Logic App workflow triggered by an event (like an entry in a SharePoint list or an email to a specific mailbox) indicating a new employee hire.
   
   3. **Azure AD User Creation**:
        - Use the Azure AD connector in Logic Apps to automatically create a new user in Azure AD based on the trigger event's details.
   
   4. **Role and Group Assignment**:
        - Assign predefined roles and groups to the new user based on the job position or department indicated in the trigger.
   
   5. **Resource Provisioning**:
        - Use the Azure Resource Manager connector in Logic Apps to provision any necessary Azure resources for the user (like VMs or specific permissions).
   
   6. **Welcome Email**:
        - Leverage the Email connector in Logic Apps to send a welcome email to the new hire with instructions and necessary access details.
   
   7. **Monitoring and Review**:
        - Monitor and review the onboarding process through Logic Apps runs history and Azure AD logs to ensure smooth operations.
     
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
