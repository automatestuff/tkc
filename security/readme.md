# Workload Management Privileges for vCenter and Avi

Configuring vCenter and NSX Advanced Load Balancer (Avi) users and roles.

*Table of Contents*
- [Workload Management Privileges for vCenter and Avi](#workload-management-privileges-for-vcenter-and-avi)
  - [Environments](#environments)
  - [Documentation](#documentation)
  - [vCenter SSO Users and Groups](#vcenter-sso-users-and-groups)
    - [Create a New Group and User](#create-a-new-group-and-user)
    - [Add New Group to Service Provider Users](#add-new-group-to-service-provider-users)
  - [vCenter Custom Roles](#vcenter-custom-roles)
    - [Permissions for Workload Management Administrator Custom Role](#permissions-for-workload-management-administrator-custom-role)
    - [Content Library Custom Role](#content-library-custom-role)
    - [Avi Service Custom Role](#avi-service-custom-role)

## Environments

**vCenter**

The vCenter Single Sign On account that you provide to Tanzu Kubernetes Grid when you deploy a management cluster must have the correct permissions in order to perform the required operations in vSphere.

It is not recommended to provide a vSphere administrator account to Tanzu Kubernetes Grid, because this provides Tanzu Kubernetes Grid with far greater permissions than it needs. The best way to assign permissions to Tanzu Kubernetes Grid is to create a custom role, a service (user) account, and a user group. Then to grant the user group custom role permissions on vSphere objects. Additionally, a custom role is necessary for global permission to Content Libraries for the new user group.

**NSX Advanced Load Balancer (Avi)**

Avi Vantage manages the lifecycle of the load balancer within each cloud. In VMware write access cloud, the Controller requires vCenter URL, username, and password to establish a connection with the vCenter portal. With this the Controller discovers the vCenter managed objects to build an internal relation graph. As a part of the load balancer lifecycle management, Avi Service Engine is created and port groups are added and (or) removed from the virtual machines.

On deploying vCenter cloud, Avi Vantage is not provided the root credentials for security reasons. On creating the cloud in Avi Vantage, the vCenter user is assigned certain roles that allow the Controller to manage the load balancer lifecycle. The user is mapped to two roles during the role configuration on vCenter. One of the roles is applied at the vCenter root level and another at the folder level where the Service Engines are created by the Avi Controller.

The following section discusses defining role privileges for two roles that are assigned to the vCenter user.

---

## Documentation

- [vSphere with Tanzu Required Ops Privileges](https://docs.vmware.com/en/VMware-Tanzu-Kubernetes-Grid/1.3/vmware-tanzu-kubernetes-grid-13/GUID-mgmt-clusters-vsphere.html) - Denoted by the **asterisk (*)** after permission names below.

- [Cloud Admin Privileges](https://docs.vmware.com/en/VMware-Cloud-on-AWS/services/com.vmware.vsphere.vmc-aws-manage-data-center-vms.doc/GUID-DFB3C048-5728-4DE9-9380-7240748875C3.html) - Denoted by the **ampersand (&)** after the permission names below.

- [VMware User Role for Avi Privileges](https://avinetworks.com/docs/latest/vmware-user-role/) - Denoted by the **dollar sign ($)** after the permission names below.

- [Velero Backup and Restore Privileges](https://code.vmware.com/docs/11750/virtual-disk-development-kit-programming-guide/GUID-8301C6CF-37C2-42CC-B4C5-BB1DD28F79C9.html)

---

## vCenter SSO Users and Groups

In vCenter, or an associated AD/LDAP, create the following user and group. And add the new group to the SSO service provider users group.

### Create a New Group and User

1. Go to Administration > Single Sign On > Users and Groups

2. Create a new user named `wcp-admin` as a service account for Avi.

3. Select the *Groups* tab and create new `wcp-admins` group. Add the new `wcp-admin` user and possible other WCP administrators.

### Add New Group to Service Provider Users

1. Go to Administration > Single Sign On > Users and Groups > Groups

2. Find the `ServiceProviderUsers` group, on the second page of the groups list, and add the new `wcp-admins` to the group to this group. This will give the `wcp-admins` visibility to the *Namespaces* resource pool in the *Hosts & Clusters* view.

---

## vCenter Custom Roles

In the vSphere Client, go to Administration > Access Control > Roles, and create a new role, for example `Workload Management Administrator`, with the following permissions.

### Permissions for Workload Management Administrator Custom Role

1. Under Administration > Access Control > Roles, create a new role `Workload Management Administrator` and define the following parameters for this role:

   ```yaml
   - Alarms
     - Acknowledge alarm &
     - Create alarm &
     - Disable alarm action &
     - Modify alarm &
     - Remove alarm &
     - Set alarm status &

   - Certificate Authority
     - Create/Delete (below Admins priv) &

   - Certificate Management
     - Create/Delete (below Admins priv) &

   - Cns
     - Searchable * &

   - Compute Policy
     - Create and Delete Compute Policy &

   - Datastore
     - Allocate space * & $
     - Browse datastore * &
     - Configure datastore &
     - Low level file operations * &
     - Remove file &
     - Rename datastore &
     - Update virtual machine files &
     - Update virtual machine metadata &

   - Extension
     - Register extension &
     - Unregister extension &
     - Update extension &

   - Folder
     - Create folder &
     - Delete folder &
     - Move folder &
     - Rename folder &

   - Global
     - Cancel task &
     - Disable methods * &
     - Enable methods * &
     - Global tag &
     - Health &
     - Licenses * &
     - Log event &
     - Manage custom attributes &
     - Service managers &
     - Set custom attribute &
     - System tag &

   - Host
     - Configuration
       - Network configuration $

   - Host profile
     - View &

   - Hybrid Linked Mode
     - Manage &

   - Namespaces
     - Modify cluster-wide configuration
     - Modify cluster-wide namespace self-service configuration
     - Modify namespace configuration

   - Network
     - Assign network * & $

   - Profile-driven storage
     - Profile-driven storage view *

   - Resource
     - Apply recommendation &
     - Assign vApp to resource pool * &
     - Assign virtual machine to resource pool &
     - Create resource pool &
     - Modify resource pool &
     - Move resource pool &
     - Query vMotion &
     - Remove resource pool &
     - Rename resource pool &

   - Scheduled task
     - Create tasks &
     - Modify task &
     - Remove task &
     - Run task &

   - Sessions
     - Message * &
     - Validate session * &

   - Storage views
     - View &

   - Supervisor Services
     - Manage Supervisor Services

   - Trusted Infrastructure administrator
     - Manage Trusted Infrastructure Hosts &

   - Virtual Machine Classes
     - Manage Virtual Machine Classes

   - Virtual machine
     - Change Configuration
       - Acquire disk lease &
       - Add existing disk * & $
       - Add new disk * &
       - Add or remove device * &
       - Advanced configuration * & $
       - Change CPU count * &
       - Change Memory * &
       - Change Settings * &
       - Change Swapfile placement &
       - Change Resource &
       - Configure Host USB device &
       - Configure Raw device * &
       - Configure managedBy &
       - Display connection settings &
       - Extend virtual disk * &
       - Modify device settings * &
       - Query Fault Tolerance compatibility &
       - Query unowned files &
       - Reload from path &
       - Remove disk * &
       - Rename &
       - Reset guest information &
       - Set annotation &
       - Toggle disk change tracking * &
       - Upgrade virtual machine compatibility &
     - Edit Inventory
       - Create from existing * &
       - Create new &
       - Move &
       - Remove * &
       - Register &
       - Unregister &
     - Guest operations
       - Guest operation alias modification &
       - Guest operation alias query &
       - Guest operation modifications &
       - Guest operation program execution &
       - Guest operation queries &
     - Interaction
       - Answer question &
       - Backup operation on virtual machine &
       - Configure CD media &
       - Configure floppy media &
       - Connect devices &
       - Console interaction &
       - Create screenshot &
       - Defragment all disks &
       - Drag and drop &
       - Guest operating system management by VIX API &
       - Inject USB HID scan codes &
       - Install VMware Tools &
       - Pause or Unpause &
       - Power off * &
       - Power on * &
       - Reset &
       - Suspend &
     - Provisioning
       - Allow disk access &
       - Allow file access &
       - Allow read-only disk access * &
       - Allow virtual machine download * &
       - Allow virtual machine files upload &
       - Clone template &
       - Clone virtual machine &
       - Create template from virtual machine &
       - Customize guest &
       - Deploy template * &
       - Mark as template &
       - Mark as virtual machine &
       - Modify customization specification &
       - Promote disks &
       - Read customization specifications &
     - Service configuration
       - Allow notifications &
       - Allow polling of global event notifications &
       - Manage service configurations &
       - Modify service configuration &
       - Query service configurations &
       - Read service configuration &
     - Snapshot management
       - Create snapshot * &
       - Remove snapshot * &
       - Rename snapshot &
       - Revert to snapshot &
     - vSphere Replication
       - Configure replication &
       - Manage replication &
       - Monitor replication &

   - vApp
     - Add virtual machine &
     - Assign resource pool &
     - Assign vApp &
     - Clone &
     - Create &
     - Delete &
     - Export &
     - Import * $
     - Move &
     - Power off &
     - Power on &
     - Rename &
     - Suspend &
     - Unregister &
     - View OVF environment &
     - vApp application configuration &
     - vApp instance configuration &
     - vApp managedBy configuration &
     - vApp resource configuration &

   - vSan Cluster
     - Cluster &
     - ShallowRekey &

   - vService
     - Create dependency &
     - Destroy dependency &
     - Reconfigure dependency configuration &
     - Update dependency &

   - vSphere Tagging
     - Assign or Unassign vSphere Tag &
     - Assign or Unassign vSphere Tag on Object &
     - Create vSphere Tag &
     - Create vSphere Tag Category &
     - Delete vSphere Tag &
     - Delete vSphere Tag Category &
     - Edit vSphere Tag &
     - Edit vSphere Tag Category &
     - Modify UsedBy Field For Category &
     - Modify UsedBy Field For Tag &
   ```

2. vCenter Object Permissions. In *Hosts and Clusters*, right-click the objects that your Tanzu Kubernetes Grid deployment will use, right-click the object and select **Add Permission**. Assign the new `wcp-admins` group with the new Custom Role `Workload Management Administrator` to each object.

   - **Hosts and Clusters**
     - The root vCenter Server object
     - The Datacenter and all of the Host and Cluster folders, from the Datacenter object down to the cluster that manages the Tanzu Kubernetes Grid deployment
     - Target cluster and use *Propogate to children* enabled (for hosts, etc.)
     - Target resource pools, with *Propagate to children* enabled
     
   - **VMs and Templates**
     - Any deployed Tanzu Kubernetes Grid base image templates. None for TKGS.
     - Target VM and Template folders, with *Propagate to children* enabled
     
   - **Storage**
     - Datastores and all storage folders, from the Datacenter object down to the datastores that will be used for Tanzu Kubernetes Grid deployments. No propogate to children.
     
   - **Networking**
     - Networks, Distributed switches and Distributed Port Groups to which clusters will be assigned

   > **NOTE**: If the `Avi Internal` network has been created by the Avi Controller in a past Workload Management deployment, ensure `wcp-admins` also has permissions.

### Content Library Custom Role

This role provides Content Library admninistration for the `wcp-admins` group.
  
1. Create a new role, `Workload Management Content Library Administrator` with the following permissions:

   ```yaml
   - Content Library
     - Add library item &
     - Check in a template &
     - Check out a template &
     - Create local library &
     - Create subscribed library &
     - Delete library item &
     - Delete local library &
     - Delete subscribed library &
     - Download files &
     - Evict library item &
     - Evict subscribed library &
     - Import storage &
     - Probe subscription information &
     - Read storage &
     - Sync library item &
     - Sync subscribed library &
     - Type introspection &
     - Update configuration settings &
     - Update library &
     - Update library item &
     - Update local library &
     - Update subscribed library &
     - View configuration settings &
   ```

2. Add Global Permission for `wcp-admins` to administer Content Libraries by adding a new permission using Administration > Access Control > Global Permissions, **Add Permission**.

3. Enter User/Group of `wcp-admins` and select the new role `Workload Management Content Library Administrator`.

4. Check **Propogate to Children** and select OK to create.

### Avi Service Custom Role

`Avi Service` role is applied at the folder level where the Service Engines are created by the Avi Controller. With this role, the user is restricted to perform all operations on a Service Engine within a particular folder and is not allowed to edit any resources outside the specific folder.

This role is required for the user to access the datastore, host, and networking settings that allow creating the Service Engine. This role will be configured in the Avi Controller Console for Avi to reach out to vCenter for Service Engines.

1. Under Administration > Access Control > Roles, create a new role `Avi Service` and define the following parameters for this role:

    ```yaml
    Datacenter
    - Network protocol profile configuration
    - Query IP pool allocation
    - Release IP allocation

    Datastore
    - Allocate space
    - Browse datastore
    - Configure datastore
    - Low level file operations
    - Remove file
    - Update virtual machine files
    - Update virtual machine metadata

    Distributed switch
    - Create
    - Host operation
    - Modify
    - Network I/O control operation
    - Policy operation
    - Port configuration operation
    - Port setting operation

    Enable all parameters under the following categories.
    - Network
    - Performance
    - Tasks
    - Virtual machine
    - dvPort group
    - vApp
    ```

2. Create a new folder, `AviSEFolder`, in vCenter > VMs & Folders underneath the cluster.
   
3. Assign `Avi Service` to the new `AviSEFolder` folder for the Service Engine creation by right-clicking the folder and selecting "Add Permission". Select the `Avi Service` role and the `wcp-admins` group.

4. In Avi Controller UI, navigate to Infrastructure > Service Engine Group and enter `AviSEFolder` for the folder name from the previous step under the *Service Engine Folder* field.

5. In the Avi Controller UI, Infrastructure > Cloud, configure the using this `wcp-admin` service account to access vCenter.


**Test Commands**

1. SSH to Avi Controller VM:

   ```bash
   $ ssh admin@avi.domain.com
   $ shell
   Login: admin
   Password: xxxxx
   ```

2. Run the following:
   
    ```bash
    [admin:controller]: > show vinfra vcenter 10.1.1.1-SantaClara datastores
    +---------------+------------------------------------------------+
    | Field         | Value                                          |
    +---------------+------------------------------------------------+
    | datacenter    | datacenter-2-cloud-81cxxxxx-5bxx-46xx-89xx-5fexxxxx |
    +---------------+------------------------------------------------+

    [admin:controller]: > show vinfra vcenter 10.1.1.1-SantaClara redis
    +-----------------------+----------------------------+----------+
    | Name                  | Inventory State            | Progress |
    +-----------------------+----------------------------+----------+
    | 10.10.2.11-SantaClara | VCENTER_DISCOVERY_COMPLETE | 100      |
    | 10.10.2.5-SantaClara  | VCENTER_DISCOVERY_COMPLETE | 100      |
    +-----------------------+----------------------------+----------+

    [admin:controller]: > show vinfra vcenter 10.1.1.1-SantaClara hostresources
    +---------------------+-------------------+------------+--------+---------+------------+
    | Name                | Managed Object Id | Host Scale | Num Se | Se Fail | Se Success |
    +---------------------+-------------------+------------+--------+---------+------------+
    | 10.160.5.23         | host-603          | 2558       | -      | -       | -          |
    | 10.160.5.24         | host-588          | 1217       | -      | -       | -          |
    | esx-9.avi.local     | host-5526         | 431        | -      | -       | -          |
    | esx-8.avi.local     | host-5513         | 543        | -      | -       | -          |
    ```
