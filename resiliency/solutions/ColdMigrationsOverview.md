**MIGRATE “AS IS”**

<li>Backup and Move AIX and IBM I / AS400 Workloads unchanged

<li>Like for like architecture ensures applications run in Azure without changes

<li>Quickly exit the datacenter

<li>Eliminate dependencies on hardware contracts and support costs  

**Protect**
<li>Offer heterogeneous protection for any workload, now running on Azure
<li>Protect at every phase: On-Prem, Hybrid, Pure Azure and Cross Cloud
<li>Backup to object storage, save costs with consumption based spend
 <li>Disaster Recovery for AIX / IBM I in Azure

**Innovate and Scale**
<li>Leverage Azure native services in region (Data Lake, AKS, Global Reach, etc…)
<li>Modernize the POWER workloads over time based on initial migration and protection footprint
<li>Optimize file system, shorten recovery time, utilize cost effective storage solutions in Azure


### Overview: Importing VMs and vApps into Skytap | Skytap help and documentation


  <p><a href=".">Home</a> &gt; <a href="user-guide.html">User Guide</a></p>
</div>

<ul class="nav" id="mysidebar">
  <li><a href="skytap-overview.html">Skytap overview</a>
    <ul>
      <li><a href="skytap-on-azure-overview.html">Skytap on Azure overview</a></li>
      <li><a href="skytap-on-ibm-cloud-overview.html">Skytap on IBM Cloud overview</a></li>
      <li><a href="overview-features-by-region.html">Skytap features by region</a></li>
      <li><a href="overview-service-limits.html">Skytap service limits</a></li>
    </ul>
  </li>
  <li><a href="vm-access-requirements.html">Access requirements</a></li>
  <li><a href="creating-an-environment.html">Creating environments</a>
    <ul>
      <li><a href="creating-an-environment-from-a-template.html">Creating an environment from a template</a>
        <ul>
          <li><a href="using-public-templates.html">Skytap public templates</a>
            <ul>
              <li><a href="ansible-getting-started.html">Ansible dynamic inventory</a></li>
              <li><a href="kb-configure-ubuntu-firstboot-name-server.html">Configuring an Ubuntu Server VM created from a Skytap public template</a></li>
            </ul>
          </li>
        </ul>
      </li>
      <li><a href="copy-environment.html">Copying environments</a></li>
      <li><a href="copy-environment-with-running-vms.html">Copying environments with running VMs (live clone)</a>
        <ul>
          <li><a href="quiescing-vm-activity.html">Quiescing VM activity</a></li>
        </ul>
      </li>
      <li><a href="building-vms-from-empty-image-isos.html">Building a VM from an ISO</a></li>
    </ul>
  </li>
  <li><a href="importing-vms-overview.html">Importing VMs into Skytap</a>
    <ul>
      <li><a href="importing-vms-preparing-vms-for-import.html">Preparing x86 VMs and vApps for import</a></li>
      <li><a href="imports-using-the-vm-imports-page.html">Importing VMs using the VM Imports page</a>
        <ul>
          <li><a href="imports-exporting-power-lpar-to-ovf.html">Preparing Power LPARs for import into Skytap</a>
            <ul>
              <li><a href="imports-md5.html">Creating MD5 hash values for VM imports</a></li>
            </ul>
          </li>
          <li><a href="imports-retrying.html">Retrying a failed import</a></li>
        </ul>
      </li>
      <li><a href="importing-vms-shipping-physical-media.html">Shipping VMs to Skytap with the Advanced Import Service</a></li>
      <li><a href="kb-additional-ways-to-import-power-vms.html">Additional ways to import Power LPARs into Skytap</a>
        <ul>
          <li><a href="importing-power-ibmi-using-icc.html">Importing IBM i workloads into Skytap using IBM Cloud Storage Solutions for i (ICC)</a></li>
          <li><a href="importing-power-ibmi-using-direct-transfer.html">Importing IBM i workloads into Skytap using direct transfer</a></li>
          <li><a href="kb-aix-creating-skytap-vm-from-mksysb.html">Creating a Skytap AIX VM from a mksysb image</a></li>
          <li><a href="kb-aix-creating-mksysb-from-skytap-vm.html">Creating a mksysb from an AIX VM in Skytap</a></li>
          <li><a href="importing-power-vms.html">Importing Power LPARs into Skytap</a></li>
        </ul>
      </li>
      <li><a href="kb-download-ibm-power-vms.html">Downloading IBM-hosted AIX VMs and importing them into Skytap</a></li>
      <li><a href="importing-vms-testing-vms-after-import.html">Testing imported VMs</a></li>
      <li><a href="imports-errors.html">Resolving import errors</a></li>
    </ul>
  </li>
  <li><a href="running-and-stopping-vms-and-environments.html">Running and stopping VMs</a></li>
  <li><a href="accessing-vms-overview.html">Accessing VMs</a>
    <ul>
      <li><a href="accessing-vms-with-a-browser.html">Accessing VM desktops from a browser</a>
        <ul>
          <li><a href="connectivity-checker.html">Testing access requirements with Connectivity Checker</a></li>
          <li><a href="audio-using-vm-audio.html">Using audio with a VM</a>
            <ul>
              <li><a href="audio-troubleshooting.html">Troubleshooting VM audio</a></li>
            </ul>
          </li>
          <li><a href="ssh-sra-using.html">Using an SSH connection in a browser client session</a></li>
          <li><a href="copy-and-paste-text-between-your-local-computer-and-a-vm.html">Copy and paste text between the local machine and a VM</a></li>
          <li><a href="insert-text-in-power-sra.html">Insert text into a Power VM</a></li>
          <li><a href="keyboard-shortcuts-for-sra-and-smartrdp.html">Keyboard shortcuts for browser client views</a></li>
          <li><a href="improving-browser-client-performance.html">Improving browser client performance</a></li>
          <li><a href="sra-url-parameters.html">Optional URL parameters for sharing an SRA browser client view</a></li>
        </ul>
      </li>
      <li><a href="accessing-vms-with-smartrdp.html">Accessing VMs with SmartRDP</a></li>
    </ul>
  </li>
  <li><a href="transferring-files-to-a-vm.html">Transferring files to a VM</a>
    <ul>
      <li><a href="sharing-files-with-the-assets-page.html">Adding and sharing files with the Assets page</a></li>
      <li><a href="sharing-files-with-the-shared-drive.html">Adding and sharing files with the shared drive</a>
        <ul>
          <li><a href="using-hared-drive-with-custom-dns.html">Using the shared drive with a custom DNS server or static IP addresses</a></li>
          <li><a href="troubleshooting-shared-drive.html">Troubleshooting issues with the shared drive</a></li>
        </ul>
      </li>
      <li><a href="using-iso-files.html">Using ISO files</a></li>
      <li><a href="sharing-files-with-the-assets-page.html">Using Skytap public assets</a></li>
    </ul>
  </li>
  <li><a href="editing-environment-and-networks.html">Editing environments and networks</a>
    <ul>
      <li><a href="environment-lock.html">Locking environments</a></li>
      <li><a href="adding-vms.html">Adding VMs to an environment</a></li>
      <li><a href="editing-vm-settings.html">Editing VMs</a>
        <ul>
          <li><a href="vm-hardware-overview.html">VM hardware and guest OS settings</a>
            <ul>
              <li><a href="editing-vm-cpus-ram.html">Editing VM CPUs and RAM</a></li>
              <li><a href="kb-enabling-nested-virtualization.html">Enabling nested virtualization</a></li>
              <li><a href="upgrading-vm-hardware-version.html">Upgrading VM hardware versions</a></li>
              <li><a href="using-international-keyboards-with-vms.html">Using an international keyboard layout with a VM</a></li>
              <li><a href="editing-vm-bios-time-sync.html">Editing VM BIOS clock sync settings</a></li>
              <li><a href="adding-and-extending-vm-disks.html">Adding or extending a virtual disk</a></li>
              <li><a href="deleting-virtual-disk.html">Deleting a virtual disk</a></li>
              <li><a href="audio-configuring-guest-os.html">Enabling audio for a VM</a></li>
              <li><a href="ssh-sra-configuring-OS.html">Enabling SSH on a VM for use in SRA browser client sessions</a></li>
              <li><a href="pwr-ibmi-lpps.html">IBM i default license program packages (LPPs)</a></li>
              <li><a href="pwr-ibmi-optional-lpps.html">IBM i optional license program packages (LPPs)</a></li>
            </ul>
          </li>
          <li><a href="vm-network-settings.html">VM network settings</a>
            <ul>
              <li><a href="attaching-vms-to-networks.html">Attaching VMs to networks</a></li>
              <li><a href="adding-secondary-ip-addresses.html">Adding secondary IP addresses</a></li>
              <li><a href="editing-vm-mac-address.html">Editing VM MAC address</a></li>
              <li><a href="editing-vm-hostname-or-ip-address.html">Editing VM hostname or primary IP address</a></li>
              <li><a href="enabling-promiscuous-mode.html">Enabling promiscuous mode</a></li>
              <li><a href="using-static-ip-addresses.html">Using static IP addresses</a></li>
              <li><a href="ensuring-unique-hostnames-for-windows-vms.html">Ensuring unique hostnames for Windows VMs</a></li>
            </ul>
          </li>
          <li><a href="vm-setting-credentials.html">Storing VM credentials</a></li>
          <li><a href="licensing-guest-os.html">Licensing the guest OS</a></li>
          <li><a href="installing-and-upgrading-vmware-tools-on-linux-vms.html">Installing and upgrading VMware Tools - Linux</a></li>
          <li><a href="installing-and-upgrading-vmware-tools-on-windows-vms.html">Installing and upgrading VMware Tools - Windows</a></li>
          <li><a href="improving-os-and-app-performance-for-vms.html">Improving operating system or application performance within the VM</a></li>
        </ul>
      </li>
      <li><a href="containers-overview.html">Adding containers and container hosts</a>
        <ul>
          <li><a href="containers-getting-started.html">Getting started with containers</a></li>
          <li><a href="about-the-container-host.html">About the container host</a></li>
          <li><a href="containers-enabling-a-vm-as-a-container-host.html">Creating a container host</a>
            <ul>
              <li><a href="kb-enable-ip-forwarding-for-container-host.html">Enable IP address forwarding for a container host</a></li>
            </ul>
          </li>
          <li><a href="adding-containers-to-a-container-host.html">Adding containers to a container host</a></li>
          <li><a href="managing-containers.html">Managing individual containers</a></li>
          <li><a href="containers-known-issues.html">Containers: Known issues</a></li>
          <li><a href="troubleshooting-vmagent-troubleshooting.html">Troubleshooting the VM Agent</a></li>
        </ul>
      </li>
      <li><a href="managing-networks.html">Managing networks</a>
        <ul>
          <li><a href="network-overview.html">Network overview</a>
            <ul>
              <li><a href="default-host-naming-behavior.html">Default host-naming behavior</a></li>
              <li><a href="networks-comparison.html">What is the difference between automatic and manual networks?</a></li>
            </ul>
          </li>
          <li><a href="network-topology-view.html">Viewing the network topology for an environment</a></li>
          <li><a href="using-multiple-networks-in-an-environment.html">Using multiple networks in an environment</a>
            <ul>
              <li><a href="creating-automatic-networks.html">Creating an automatic network</a></li>
              <li><a href="creating-a-manual-network.html">Creating a manual network</a></li>
              <li><a href="routing-between-networks-in-the-same-environment.html">Routing between networks in the same environment</a></li>
              <li><a href="avoiding-restricted-subnets-and-ip-addresses.html">Avoiding restricted subnets and IP addresses</a></li>
            </ul>
          </li>
          <li><a href="connecting-multiple-environments.html">Networking between environments</a>
            <ul>
              <li><a href="using-network-address-transalation-to-avoid-ip-adddress-conflicts.html">Using NAT to avoid IP address conflicts</a></li>
            </ul>
          </li>
          <li><a href="editing-automatic-networks.html">Editing an automatic network</a>
            <ul>
              <li><a href="editing-network-nat-subnet.html">Editing the network NAT subnet</a></li>
            </ul>
          </li>
          <li><a href="wan-connecting-environments-to-vpn-or-pnc.html">Connecting an environment network to a VPN or Private Network Connection</a></li>
          <li><a href="manually-configuring-domain-name-servers.html">Manually configuring domain name servers</a></li>
          <li><a href="controlling-public-internet-access-to-vms.html">Exposing and blocking public Internet access to VMs</a>
            <ul>
              <li><a href="accessing-vms-with-published-services.html">Accessing VMs with published services</a>
                <ul>
                  <li><a href="published-services-add.html">Adding published services</a></li>
                  <li><a href="published-services-connect.html">Connecting to a published service</a></li>
                  <li><a href="published-services-troubleshoot.html">Troubleshooting my connection to a published service</a></li>
                  <li><a href="published-services-remove.html">Removing a published service</a></li>
                  <li><a href="accessing-vms-with-direct-rdp.html">Example - Accessing VMs with RDP</a></li>
                  <li><a href="connect-to-a-linux-vm-with-ssh.html">Example - Accessing VMs with SSH</a></li>
                </ul>
              </li>
              <li><a href="using-public-ip-addresses.html">Using public IP addresses</a>
                <ul>
                  <li><a href="attaching-static-public-ip-addresses-to-vms.html">Attaching a static public IP address to a VM</a></li>
                  <li><a href="adding-automatic-dns-names-and-public-ip-address-to-vms.html">Adding a managed DNS name and public IP address to a VM</a></li>
                  <li><a href="changing-skytap-dns-names.html">Editing a managed VM DNS name</a></li>
                  <li><a href="faq-how-does-skytap-generate-vm-dns-names.html">How does Skytap generate the VM DNS name?</a></li>
                  <li><a href="detaching-public-ip-addresses-from-vms.html">Detaching a static public IP address or  DNS name from a VM</a></li>
                  <li><a href="comparing-static-and-dynamic-public-ip-addresses.html">What is the difference between static public IPs and dynamic public IPs with DNS?</a></li>
                  <li><a href="troubleshooting-public-ip-addresses.html">Troubleshooting issues connecting to a public IP address or Skytap-managed DNS name</a></li>
                </ul>
              </li>
              <li><a href="kb-no-internet-from-vm.html">Issue - I can’t access the public Internet from my VM.</a></li>
            </ul>
          </li>
          <li><a href="deleting-networks.html">Deleting a network</a></li>
        </ul>
      </li>
      <li><a href="kb-move-a-vm-to-a-different-environment.html">Moving a VM to a different environment</a></li>
      <li><a href="deleting-vms.html">Deleting VMs</a></li>
    </ul>
  </li>
  <li><a href="sharing-portals.html">Sharing VMs and environments with sharing portals</a>
    <ul>
      <li><a href="sharing-portals-create.html">Creating a sharing portal</a>
        <ul>
          <li><a href="sharing-portals-options.html">Overview of sharing portal options</a></li>
          <li><a href="sharing-portals-custom-content-how-to.html">How to create custom HTML content for a sharing portal</a></li>
          <li><a href="sharing-portals-custom-content-templates.html">Custom content tab – starter templates</a></li>
          <li><a href="sharing-portals-custom-content-allowed-html.html">Custom content tab - allowed HTML</a></li>
        </ul>
      </li>
      <li><a href="sharing-portals-viewing.html">Using a VM or environment from a sharing portal URL</a></li>
      <li><a href="sharing-portals-find-share-url.html">Finding and sharing the URL for a sharing portal</a></li>
      <li><a href="sharing-portals-csv.html">Exporting sharing portal information and URL(s)</a></li>
      <li><a href="sharing-portals-disable-delete.html">Disabling or deleting a sharing portal</a></li>
    </ul>
  </li>
  <li><a href="sharing-resources-with-projects.html">Sharing resources with projects</a>
    <ul>
      <li><a href="projects-creating-and-deleting.html">Creating and deleting projects</a></li>
      <li><a href="projects-adding-and-removing-environments.html">Adding and removing environments</a></li>
      <li><a href="projects-adding-and-removing-templates.html">Adding and removing templates</a></li>
      <li><a href="projects-adding-and-removing-assets.html">Adding and removing assets</a></li>
      <li><a href="projects-adding-and-removing-users.html">Adding and removing users</a></li>
      <li><a href="projects-adding-and-removing-groups.html">Adding and removing groups</a></li>
      <li><a href="projects-understanding-roles.html">Understanding project roles</a></li>
      <li><a href="projects-finding-project-resources.html">Finding project resources</a></li>
    </ul>
  </li>
  <li><a href="adding-shared-templates-to-your-account.html">Adding a shared template to your account</a></li>
  <li><a href="saving-an-environment-as-a-template.html">Saving an environment as a template</a>
    <ul>
      <li><a href="copy-template.html">Copying a template</a></li>
      <li><a href="deleting-templates.html">Deleting templates</a></li>
    </ul>
  </li>
  <li><a href="understanding-regions.html">Understanding regions</a>
    <ul>
      <li><a href="copy-environment-to-region.html">Copying an environment to another region</a></li>
      <li><a href="copy-template-to-region.html">Copying a template to another region</a></li>
      <li><a href="copying-to-region.html">What to expect when you copy an environment or template</a></li>
    </ul>
  </li>
  <li><a href="account-settings-management.html">Managing your account settings</a>
    <ul>
      <li><a href="kb-convert-non-vmware-image.html">Changing your default region</a></li>
      <li><a href="kb-account-marketing-emails.html">Changing your marketing email preferences</a></li>
      <li><a href="kb-account-change-password.html">Changing your password</a></li>
      <li><a href="kb-account-change-time-zone.html">Changing your user account time zone</a></li>
      <li><a href="kb-account-find-ftp-creds.html">Finding your shared drive credentials</a></li>
      <li><a href="kb-find-primary-admin.html">Finding your primary administrator</a></li>
      <li><a href="kb-account-find-user-role.html">Finding your Skytap user role</a></li>
      <li><a href="kb-find-api-token.html">Finding your user name and API token</a></li>
      <li><a href="kb-account-reset-apitoken.html">Resetting your API token</a></li>
    </ul>
  </li>
  <li><a href="viewing-usage-limits.html">Viewing your current usage and usage limits</a>
    <ul>
      <li><a href="environment-activity.html">Viewing recent activity for an environment</a></li>
    </ul>
  </li>
  <li><a href="best-practices.html">Best practices</a>
    <ul>
      <li><a href="automating-vms-and-environments.html">Automating VMs and environments</a>
        <ul>
          <li><a href="auto-shutdown.html">Automatically suspend or shut down inactive environments</a>
            <ul>
              <li><a href="auto-suspend-keep-alive.html">Sending keep-alive messages from a VM</a></li>
              <li><a href="troubleshooting-automatic-shutdown.html">Troubleshooting automatic shutdown</a></li>
            </ul>
          </li>
          <li><a href="automating-actions-with-schedules.html">Automating actions with schedules</a>
            <ul>
              <li><a href="schedules-add-new.html">Adding a new schedule to an environment or template</a></li>
              <li><a href="schedules-edit-schedule.html">Editing an existing schedule</a></li>
              <li><a href="schedules-delete-schedule.html">Deleting a schedule</a></li>
            </ul>
          </li>
          <li><a href="vm-sequencing.html">Controlling the order of VM startup with VM sequencing</a></li>
          <li><a href="accessing-vm-metadata-service-from-within-a-vm.html">Accessing the VM metadata service</a></li>
        </ul>
      </li>
      <li><a href="reference-architecture-ci-cd.html">CI/CD reference architectures</a>
        <ul>
          <li><a href="reference-architecture-ci-cd-build.html">Jenkins build farm</a></li>
          <li><a href="reference-architecture-ci-cd-functional-test.html">Functional test environments</a></li>
          <li><a href="reference-architecture-ci-cd-systems-test.html">Systems integration test environments</a></li>
        </ul>
      </li>
      <li><a href="reference-architecture-ibmi-data-protection-and-resiliency-overview.html">IBM i Data Protection and Resiliency Solutions</a>
        <ul>
          <li><a href="reference-architecture-ibmi-backup-and-restore.html">IBM i backup and restore</a></li>
          <li><a href="reference-architecture-ibmi-disaster-recovery-and-high-availability.html">IBM i disaster recovery and high availability</a></li>
        </ul>
      </li>
      <li><a href="env-duplication-best-practices.html">Creating recovery copies of VMs and environments</a></li>
      <li><a href="reference-architecture-disaster-recovery.html">Disaster recovery reference architectures</a>
        <ul>
          <li><a href="reference-architecture-dr-offsite-cold.html">On-premises data center to Skytap – cold recovery</a></li>
          <li><a href="reference-architecture-dr-offsite-warm.html">On-premises data center to Skytap – warm recovery</a></li>
          <li><a href="reference-architecture-dr-region-to-region-cold.html">Skytap region-to-region – cold recovery</a></li>
          <li><a href="reference-architecture-dr-region-to-region-warm.html">Skytap region-to-region – warm recovery</a></li>
        </ul>
      </li>
      <li><a href="notifications-user.html">Monitoring your usage with email notifications</a></li>
      <li><a href="using-tags-to-organize-and-search.html">Organizing resources with labels and tags</a>
        <ul>
          <li><a href="adding-usage-labels-to-environments.html">Adding and removing labels for environments</a></li>
          <li><a href="adding-usage-labels-to-templates.html">Adding and removing labels for templates</a></li>
          <li><a href="adding-usage-labels-to-resources.html">Adding and removing labels for VMs and other resources</a></li>
          <li><a href="adding-tags-to-environments.html">Adding and removing tags for environments</a></li>
          <li><a href="adding-tags-to-templates.html">Adding and removing tags for templates</a></li>
          <li><a href="csh-label-tag-error.html">Error messages for tags and labels</a></li>
        </ul>
      </li>
      <li><a href="security-best-practices.html">Security best practices</a></li>
      <li><a href="demo-overview.html">Using Skytap for demos</a></li>
      <li><a href="virtual-training.html">Using Skytap for training classes</a>
        <ul>
          <li><a href="training-options.html">Choosing an environment architecture and remote connection method</a></li>
          <li><a href="training-create-class-labs.html">Creating class lab environments</a></li>
          <li><a href="training-env-best-practices.html">Best practices for preparing student VMs</a></li>
          <li><a href="delivering-a-training-class.html">Preparing for and delivering a training class</a></li>
        </ul>
      </li>
    </ul>
  </li>
  <li><a href="knowledge-base.html">Knowledge base</a>
    <ul>
      <li><a href="kb-eol-notices.html">Skytap security improvements: Deprecation and end of life (EOL) notices</a></li>
      <li><a href="kb-adding-notes-to-vms-environments-templates-and-assets.html">How to add notes to VMs, environments, templates, and assets</a></li>
      <li><a href="kb-change-vm-name.html">How to change the name of a VM</a></li>
      <li><a href="kb-change-environment-name-desc.html">How to change the name or description of an environment</a></li>
      <li><a href="kb-checking-vm-hardware-version.html">How to check VM hardware version</a></li>
      <li><a href="kb-connecting-to-a-vm-from-an-ios-device.html">How to connect to a VM from an iPad or iPhone</a></li>
      <li><a href="kb-private-wan-connections.html">How to connect to Skytap using a private WAN connection</a></li>
      <li><a href="kb-deleting-environments.html">How to delete environments environments</a></li>
      <li><a href="determining-shared-drive-usage.html">How to determine the usage of the shared drive</a></li>
      <li><a href="kb-exporting-resource-lists.html">How to export a list of environments, templates, or assets</a></li>
      <li><a href="kb-exporting-vms-from-skytap.html">How to export VMs</a></li>
      <li><a href="kb-account-find-sftp-credentials.html">How to find the SFTP host address and credentials for the shared drive</a></li>
      <li><a href="kb-find-id-for-vm-environment-or-template.html">How to find the ID for a VM, VPN, environment, or template</a></li>
      <li><a href="kb-find-instance-url-for-a-skytap-on-azure-account.html">How to find the Instance URL for a Skytap on Azure account</a></li>
      <li><a href="kb-find-vm-ip-addresses.html">How to find the IP addresses for a VM</a></li>
      <li><a href="kb-find-network-gateway-ip.html">How to find the network gateway IP address</a></li>
      <li><a href="kb-find-api-token.html">How to find your user name and API security token</a></li>
      <li><a href="kb-generate-api-token.html">How to generate an API security token for your account</a></li>
      <li><a href="kb-dashboard-guide.html">Guide to the Dashboard</a></li>
      <li><a href="kb-securing-vms-and-remediating-compromised-vms.html">Protecting a VM that is exposed to the internet or compromised</a></li>
      <li><a href="kb-time-zones-and-utc-offsets.html">Time zones and UTC offsets</a></li>
      <li><a href="kb-storage.html">Understanding storage in Skytap</a></li>
      <li><a href="kb-using-search.html">Using search</a></li>
      <li><a href="kb-working-with-windows.html">Using Windows in Skytap</a>
        <ul>
          <li><a href="kb-resolving-duplicate-sid-errors.html">Fixing duplicate SID errors for Windows networking</a></li>
          <li><a href="kb-using-skytap-helper-for-windows-vms.html">Managing Windows hostnames with Skytap Helper</a></li>
          <li><a href="kb-resolving-windows-license-reactivation.html">Resolving prompts to reactivate a Windows license</a></li>
          <li><a href="kb-windows-domains-in-skytap.html">Running a Windows domain</a></li>
        </ul>
      </li>
      <li><a href="kb-using-power-vms.html">Using Power VMs in Skytap</a>
        <ul>
          <li><a href="kb-aix-maintenance-mode-boot.html">Booting an AIX VM into maintenance mode</a></li>
          <li><a href="kb-manually-configure-tcpip-for-aix-vms.html">Configuring TCP/IP network settings for an AIX VM</a></li>
          <li><a href="kb-aix-using-xwindows-with-aix.html">Connecting to an AIX VM with X Windows</a></li>
          <li><a href="kb-differences-between-power-and-x86-vms.html">Differences between Power and x86 VMs</a></li>
          <li><a href="kb-additional-ways-to-import-power-vms.html">Importing Power VMs into Skytap</a></li>
          <li><a href="kb-known-issues-for-power-vms.html">Known issues for Power VMs in Skytap</a></li>
          <li><a href="faq-ibmi.html">IBM i FAQ</a></li>
          <li><a href="kb-ibmi-installing-licenses.html">Installing IBM i licenses in Skytap</a></li>
          <li><a href="kb-ibmi-system-reference-codes.html">Viewing system reference codes for an IBM i VM in Skytap</a></li>
        </ul>
      </li>
      <li><a href="kb-externally-edit-vms.html">Editing VMs outside of Skytap</a>
        <ul>
          <li><a href="kb-using-ovf-converter-to-create-ova-files.html">Compressing VMX files to OVA for import</a></li>
          <li><a href="kb-convert-p2v.html">Converting a physical machine to a VM</a></li>
          <li><a href="kb-convert-non-vmware-image.html">Converting non-VMware-based VMs for import</a></li>
          <li><a href="kb-converting-ovf-to-vmx.html">Converting OVF files to VMX for use with VMware Converter</a></li>
          <li><a href="kb-shrinking-virtual-disks.html">Shrinking VM disks</a></li>
        </ul>
      </li>
      <li><a href="kb-unable-to-connect-via-remote-desktop-gateway.html">Troubleshooting connections to a Skytap VM from a Windows 10 computer?</a></li>
    </ul>
  </li>
  <li><a href="troubleshooting-skytap.html">Troubleshooting</a></li>
</ul>

          <script src="js/set-toc-li-to-active.js" type="text/javascript"></script>
          </div>
          
        
        <div class="col-md-9" id="tg-sb-content">
            <div class="post-header"></div>
            <h1 id="overview-importing-vms-lpars-and-vapps-into-skytap">Overview: Importing VMs, LPARs, and vApps into Skytap</h1>

<p>Skytap has several options for importing preconfigured VMs, vApps, and LPARs into your Skytap account. With each import job, you can transform isolated VM and LPAR files or an entire vApp into a new Skytap virtual environment.  During the import process, Skytap retains the operating system and application settings for each VM and LPAR; Skytap also retains the network settings for vApp imports.</p>

<p>Each import job is transformed into a new Skytap virtual environment. An import job can contain:</p>

<ul>
  <li>One or more VMs, LPARs, or vApps.</li>
  <li>A mix of supported file types (OVA files, OVF packages, and VMX images, etc.)</li>
</ul>

<div id="contents">
  <h6 class="minitoc">Contents</h6>
</div>

<h2 id="overview-of-the-import-process">Overview of the import process</h2>

<p>Importing VMs is a multi-step process:
<br /></p>

<ol style="margin-left: 40px;">
  <li>
    <p><span class="ion-ios-checkmark-outline" style="font-size:36px;padding-bottom: 5px;position: absolute;margin-left: -60px; margin-top:-10px;"></span><strong>Validate the import file(s)</strong></p>

    <p>Check that your VM and/or LPAR files meet the Skytap import requirements (for file size, file type, etc.).</p>

    <p>For more information, see:</p>
    <ul>
      <li><a href="importing-vms-preparing-vms-for-import.html">Import requirements for x86 VMware VMs and vApps</a></li>
      <li><a href="importing-power-vms.html#required">Import requirements for Power LPARs</a></li>
    </ul>

    <p>To convert non-VMware-based VMs, see <a href="kb-convert-non-vmware-image.html">Converting non-VMware-based VMs for import</a>.
 <br /></p>
  </li>
  <li>
    <p><span class="ion-ios-cloud-upload-outline" style="font-size:36px;padding-bottom: 5px;position: absolute;margin-left: -60px; margin-top:-10px;"></span><strong>Transfer the files</strong></p>

    <p>Upload or transfer the VM, vApp, and/or LPAR file(s) to Skytap</p>

    <p>This can be done in one of the following ways:</p>
    <ul>
      <li><a href="imports-using-the-vm-imports-page.html">Using the basic <strong>VM Imports</strong> tab</a></li>
      <li><a href="importing-vms-shipping-physical-media.html">Using the Advanced Import Service</a></li>
    </ul>

    <p>For more information about these options, see <a href="#tools">Overview of the import tools</a> below.
 <br /></p>
  </li>
  <li>
    <p><span class="ion-ios-gear-outline" style="font-size:36px;padding-bottom: 5px;position: absolute;margin-left: -60px; margin-top:-10px;"></span> <strong>Process the import</strong></p>

    <p>Skytap processes the import job and creates a Skytap virtual environment containing the VMs and/or LPARs.</p>

    <p><a href="importing-vms-imported-settings.html">Learn more about what happens during this step.</a></p>
  </li>
</ol>

<p><a name="tools"></a></p>

<h2 id="overview-of-the-import-tools">Overview of the import tools</h2>

<p>Skytap provides these import tools:</p>

<ul>
  <li>The <strong>VM Imports</strong> page in Skytap, which lets you manually manage basic import jobs.</li>
  <li>The <strong>Advanced Import Service</strong>, which supports shipping VMs to Skytap for processing.</li>
</ul>

<p class="tip">You can import VMs entirely through the self-service <strong>VM Imports</strong> page or you can ship the VMs to Skytap using the <strong>Advanced Import Service</strong>.</p>

<p>The following table contains more information about each tool:</p>

<div class="container-fluid table-container"><div class="row table-row table-header"><div class="col-md-3 table-cell">

      <p>Tool</p>

    </div><div class="col-md-9 table-cell">

      <p>Overview</p>

    </div></div><div class="row table-row"><div class="col-md-3 table-cell">

      <p><strong>VM Imports tab in Skytap</strong></p>

      <p>(Basic)</p>

    </div><div class="col-md-9 table-cell">

      <p>Use this self-service option to manually create import jobs and manage the import process.</p>

      <p>Best for:</p>

      <ul class="checklist">
        <li>Lightweight import files that have been manually validated (for example, a single VM)</li>
        <li>Individual large x86 VMs or Power LPARs that you upload to IBM Cloud Object Storage or Microsoft Azure Blob Storage.</li>
      </ul>

      <p>To get started, access the <strong>Imports</strong> tab in the <strong>Environments</strong> section of your Skytap account.</p>

      <p>For instructions, see <a href="imports-using-the-vm-imports-page.html">Importing x86 VMs using the VM Imports page</a>.</p>

      <p class="info">If you’re a user, an administrator must give you permission to import VMs.</p>

    </div></div><div class="row table-row"><div class="col-md-3 table-cell">

      <p><strong>Advanced Import Service</strong></p>

      <p>(Advanced)</p>

    </div><div class="col-md-9 table-cell">

      <p>Use this Skytap-assisted process to mail VMs and LPARs for import.</p>

      <p>Best for:</p>

      <ul class="checklist">
        <li>Bulk import of VMs from an on-premises data center (where physical shipment is faster than network transfer)</li>
      </ul>

      <p>To get started, coordinate with your Skytap sales representative.</p>

      <p>For more information, see <a href="importing-vms-shipping-physical-media.html">Using the Advanced Import Service</a>.</p>

      <p class="info">The VMs must meet the Skytap <a href="importing-vms-preparing-vms-for-import.html">import requirements</a> before you ship them to Skytap.</p>

    </div></div></div>

            
              <div id="feedback"><iframe style="border:0;" title="feedback" src="https://www.surveygizmo.com/s3/2240145/DocFeedbackv2?page=importing-vms-overview.html" width="100%" height="360" style="overflow:hidden"></iframe></div>
            
            <div class="docs-footer">
<p>Copyright &copy; 2021 Skytap, Inc. &vert; <a href="https://www.skytap.com/legal/privacy-policy/" target="_blank">Privacy Policy</a> &vert; <a href="https://www.skytap.com/legal/terms-of-service/" target="_blank">Terms of Service</a> &vert; <a href="https://www.skytap.com/support" target="_blank">Support</a> </p>
</div>
        </div>
      </div>
    </div>
  </div>
</div>