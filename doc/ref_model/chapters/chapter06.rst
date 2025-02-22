External Interfaces
===================

Introduction
------------

In this document’s earlier chapters, the various resources and capabilities of the Cloud Infrastructure have been
catalogued and the workloads have been profiled with respect to those capabilities. The intent behind this chapter and
an “API Layer” is to similarly provide a single place to catalogue and thereby codify, a common set of open APIs to
access (i.e. request, consume, control, etc.) the aforementioned resources, be them directly exposed to the workloads,
or purely internal to the Cloud Infrastructure.

It is a further intent of this chapter and this document to ensure the APIs adopted for the Cloud Infrastructure
implementations are open and not proprietary, in support of compatibility, component substitution, and ability to
realize maximum value from existing and future test heads and harnesses.

While it is the intent of this chapter to catalogue the APIs, it is not the intent of this chapter to reprint the APIs,
as this would make maintenance of the chapter impractical and the length of the chapter disproportionate within the
Reference Model document. Instead, the APIs selected for the Cloud Infrastructure implementations and specified in this
chapter, will be incorporated by reference and URLs for the latest, authoritative versions of the APIs, provided in the
References section of this document.

Although the document does not attempt to reprint the APIs themselves, where appropriate and generally where the mapping
of resources and capabilities within the Cloud Infrastructure to objects in APIs would be otherwise ambiguous, this
chapter shall provide explicit identification and mapping.

In addition to the raw or base-level Cloud Infrastructure functionality to API and object mapping, it is further the
intent to specify an explicit, normalized set of APIs and mappings to control the logical interconnections and
relationships between these objects, notably, but not limited to, support of SFC (Service Function Chaining) and other
networking and network management functionality.

This chapter specifies the abstract interfaces (API, CLI, etc.) supported by the Cloud Infrastructure Reference Model.
The purpose of this chapter is to define and catalogue a common set of open (not proprietary) APIs, of the following
types:

- Cloud Infrastructure APIs: These APIs are provided to the workloads (i.e. exposed), by the infrastructure in order for
  workloads to access (i.e. request, consume, control, etc.) Cloud Infrastructure resources.
- Intra-Cloud Infrastructure APIs: These APIs are provided and consumed directly by the infrastructure. These APIs are
  purely internal to the Cloud Infrastructure and are not exposed to the workloads.
- Enabler Services APIs: These APIs are provided by non-Cloud Infrastructure services and provide capabilities that are
  required for a majority of workloads, e.g. DHCP, DNS, NTP, DBaaS, etc.

Cloud Infrastructure APIs
-------------------------

The Cloud Infrastructure APIs consist of set of APIs that are externally and internally visible. The externally visible
APIs are made available for orchestration and management of the execution environments that host workloads while the
internally visible APIs support actions on the hypervisor and the physical resources. The ETSI NFV Reference MANO
Architecture (:numref:`ETSI NFV architecture mapping`) shows a number of Interface points where specific or sets of APIs
are supported. For the scope of the reference model the relevant interface points are shown in **Table 6-1**.

.. figure:: ../figures/ch09-etsi-nfv-architecture-mapping.png
   :name: ETSI NFV architecture mapping
   :alt: ETSI NFV architecture mapping

   ETSI NFV architecture mapping


+-----------+----------------+---------------------------------------+-------------------------------------------------+
| Interface | Cloud          | Interface Between                     | Description                                     |
| Point     | Infrastructure |                                       |                                                 |
|           | Exposure       |                                       |                                                 |
+===========+================+=======================================+=================================================+
| Vi-Ha     | Internal NFVI  | Software Layer and Hardware Resources | 1. Discover/collect resources and their         |
|           |                |                                       | configuration information, 2. Create execution  |
|           |                |                                       | environment (e.g., VM) for workloads (VNF)      |
+-----------+----------------+---------------------------------------+-------------------------------------------------+
| Vn-Nf     | External       | NFVI and VM (VNF)                     | Here VNF represents the execution environment.  |
|           |                |                                       | The interface is used to specify interactions   |
|           |                |                                       | between the VNF and abstract NFVI accelerators. |
|           |                |                                       | The interfaces can be used to discover,         |
|           |                |                                       | configure, and manage these acceleartors and    |
|           |                |                                       | for the VNF to register/deregister for          |
|           |                |                                       | receiving accelerator events and data.          |
+-----------+----------------+---------------------------------------+-------------------------------------------------+
| NF-Vi     | External       | NFVI and VIM                          | 1. Discover/collect physical/virtual resources  |
|           |                |                                       | and their configuration information, 2. Manage  |
|           |                |                                       | (create, resize, (un) suspend, reboot, etc.)    |
|           |                |                                       | physical/virtualised resources, 3.              |
|           |                |                                       | Physical/Virtual resources configuration        |
|           |                |                                       | changes, 4. Physical/Virtual resource           |
|           |                |                                       | configuration.                                  |
+-----------+----------------+---------------------------------------+-------------------------------------------------+
| Or-Vi     | External       | VNF Orchestrator and VIM              | See below                                       |
+-----------+----------------+---------------------------------------+-------------------------------------------------+
| Vi-Vnfm   | External       | VNF Manager and VIM                   | See below                                       |
+-----------+----------------+---------------------------------------+-------------------------------------------------+

**Table 6-1:** NFVI and VIM Interfaces with Other System Components in the ETSI NFV architecture

The Or-Vi and Vi-Vnfm are both specifying interfaces provided by the VIM and therefore are related. The Or-Vi reference
point is used for exchanges between NFV Orchestrator and VIM, and supports the following interfaces; virtualised
resources refers to virtualised compute, storage, and network resources:

-  Software Image Management
-  Virtualised Resources Information Management
-  Virtualised Resources Capacity Management (only VNF Orchestrator and VIM (Or-Vi))
-  Virtualised Resources Management
-  Virtualised Resources Change Management
-  Virtualised Resources Reservation Management
-  Virtualised Resources Quota Management
-  Virtualised Resources Performance Management
-  Virtualised Resources Fault Management
-  Policy Management
-  Network Forwarding Path (NFP) Management (only VNF Orchestrator and VIM (Or-Vi))

Tenant Level APIs
~~~~~~~~~~~~~~~~~

In the abstraction model of the Cloud Infrastructure (**Chapter 3**) a conceptual model of a Tenant represents the slice
of a cloud zone dedicated to a workload. This slice, the Tenant, is composed of virtual resources being utilized by
workloads within that Tenant. The Tenant has an assigned quota of virtual resources, a set of users can perform
operations as per their assigned roles, and the Tenant exists within a Cloud Zone. The APIs will specify the allowed
operations on the Tenant including its component virtual resources and the different APIs can only be executed by users
with the appropriate roles. For example, a Tenant may only be allowed to be created and deleted by Cloud Zone
administrators while virtual compute resources could be allowed to be created and deleted by Tenant administrators.

For a workload to be created in a Tenant also requires APIs for the management (creation, deletion, and operation) of
the Tenant, software flavours (Chapter 5), Operating System and workload images (“Images”), Identity and Authorization
(“Identity”), virtual resources, security, and the workload application (“stack”).

A virtual compute resource is created as per the flavour template (specifies the compute, memory, and local storage
capacity) and is launched using an image with access and security credentials; once launched, it is referred to as a
virtual compute instance or just “Instance”). Instances can be launched by specifying the compute, memory, and local
storage capacity parameters instead of an existing flavour; reference to flavours covers the situation where the
capacity parameters are specified. IP addresses and storage volumes can be attached to a running Instance.

+--------------+------+----+------+------+------+----------------------------------------------------------------------+
| Resource     |Create|List|Attach|Detach|Delete| Notes                                                                |
+==============+======+====+======+======+======+======================================================================+
| Flavour      | +    | +  |      |      | +    |                                                                      |
+--------------+------+----+------+------+------+----------------------------------------------------------------------+
| Image        | +    | +  |      |      | +    | Create/delete by appropriate administrators                          |
+--------------+------+----+------+------+------+----------------------------------------------------------------------+
| Key pairs    | +    | +  |      |      | +    |                                                                      |
+--------------+------+----+------+------+------+----------------------------------------------------------------------+
| Privileges   |      |    |      |      |      | Created and managed by Cloud Service Provider(CSP) administrators    |
+--------------+------+----+------+------+------+----------------------------------------------------------------------+
| Role         | +    | +  |      |      | +    | Create/delete by authorized administrators where roles are assigned  |
|              |      |    |      |      |      | privileges and mapped to users in scope                              |
+--------------+------+----+------+------+------+----------------------------------------------------------------------+
| Security     | +    | +  |      |      | +    | Create and delete only by VDC administrators                         |
| Groups       |      |    |      |      |      |                                                                      |
+--------------+------+----+------+------+------+----------------------------------------------------------------------+
| Stack        | +    | +  |      |      | +    | Create/delete by VDC users with appropriate role                     |
+--------------+------+----+------+------+------+----------------------------------------------------------------------+
| Virtual      | +    | +  | +    | +    | +    | Create/delete by VDC users with appropriate role                     |
| Storage      |      |    |      |      |      |                                                                      |
+--------------+------+----+------+------+------+----------------------------------------------------------------------+
| User         | +    | +  |      | +    | +    | Create/delete only by VDC administrators                             |
+--------------+------+----+------+------+------+----------------------------------------------------------------------+
| Tenant       | +    | +  |      | +    | +    | Create/delete only by Cloud Zone administrators                      |
+--------------+------+----+------+------+------+----------------------------------------------------------------------+
| Virtual      | +    | +  |      | +    | +    | Create/delete by VDC users with appropriate role. Additional         |
| compute      |      |    |      |      |      | operations would include suspend/unsuspend                           |
+--------------+------+----+------+------+------+----------------------------------------------------------------------+
| Virtual      | +    | +  | +    | +    | +    | Create/delete by VDC users with appropriate role                     |
| network      |      |    |      |      |      |                                                                      |
+--------------+------+----+------+------+------+----------------------------------------------------------------------+

**Table 6-2:** API types for a minimal set of resources.

**Table 6-2** specifies a minimal set of operations for a minimal set of resources that are needed to orchestrate
workloads. The actual APIs for the listed operations will be specified in the Reference Architectures; each listed
operation could have a number of associated APIs with a different set of parameters. For example, create virtual
resource using an image or a device.

Hardware Acceleration Interfaces
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Acceleration Interface Specifications**
ETSI GS NFV-IFA 002 :cite:p:`etsigsnfvifa002` defines a technology and implementation independent virtual accelerator, the accelerator
interface requirements and specifications that would allow a workload to leverage a Virtual Accelerator. The virtual
accelerator is modelled on extensible para-virtualised devices (EDP). ETSI GS NFV-IFA 002 :cite:p:`etsigsnfvifa002` specifies the
architectural model in Chapter 4 and the abstract interfaces for management, configuration, monitoring, and Data
exchange in Chapter 7.

ETSI NFV-IFA 019 3.1.1 :cite:p:`etsinfvifa019` has defined a set of technology independent interfaces for acceleration resource life cycle
management. These operations allow: allocation, release, and querying of acceleration resource, get and reset
statistics, subscribe/unsubscribe (terminate) to fault notifications, notify (only used by NFVI), and get alarm
information.

These acceleration interfaces are summarized here in Table 6.3 only for convenience.



+-----------------------+------------------------+-------+--------+---------------+------------------------------------+
| Request               | Response               | From, | Type   | Parameter     | Description                        |
|                       |                        | To    |        |               |                                    |
+=======================+========================+=======+========+===============+====================================+
| InitAccRequest        | InitAccResponse        | VNF → | Input  | accFilter     | the accelarator sub-system(s) to   |
|                       |                        | NFVI  |        |               | initialize and retrieve their      |
|                       |                        |       |        |               | capabilities.                      |
|                       |                        |       +--------+---------------+------------------------------------+
|                       |                        |       | Filter | accAttributeS | attribute names of accelerator     |
|                       |                        |       |        | elector       | capabilities                       |
|                       |                        |       +--------+---------------+------------------------------------+
|                       |                        |       | Output | accCapabiliti | acceleration sub-system            |
|                       |                        |       |        | es            | capabilities                       |
+-----------------------+------------------------+-------+--------+---------------+------------------------------------+
| RegisterForAccEventRe | RegisterForAccEventRes | VNF → | Input  | accEvent      | event the VNF is interested in     |
| quest                 | ponse                  | NFVI  +--------+---------------+------------------------------------+
|                       |                        |       | Input  | vnfEventHandl | the handler for NFVI to use when   |
|                       |                        |       |        | erId          | notifying the VNF of the event     |
+-----------------------+------------------------+-------+--------+---------------+------------------------------------+
| AccEventNotificationR | AccEventNotificationRe | NFVI  | Input  | vnfEventHandl | Handler used by VNF registering    |
| equest                | sponse                 | → VNF |        | erId          | for this event                     |
|                       |                        |       +--------+---------------+------------------------------------+
|                       |                        |       | Input  | accEventMetaD |                                    |
|                       |                        |       |        | ata           |                                    |
+-----------------------+------------------------+-------+--------+---------------+------------------------------------+
| DeRegisterForAccEvent | DeRegisterForAccEventR | VNF → | Input  | accEvent      | Event VNF is deregistering from    |
| Request               | esponse                | NFVI  |        |               |                                    |
+-----------------------+------------------------+-------+--------+---------------+------------------------------------+
| ReleaseAccRequest     | ReleaseAccResponse     | VNF → |        |               |                                    |
|                       |                        | NFVI  |        |               |                                    |
+-----------------------+------------------------+-------+--------+---------------+------------------------------------+
|                       |                        | VNF → | Input  | accConfigurat | Config data for accelerator        |
|                       |                        | NFVI  |        | ionData       |                                    |
| ModifyAccConfiguratio | ModifyAccConfiguration |       +--------+---------------+------------------------------------+
| nRequest              | Response               |       | Input  | accSubSysConf | Config data for accelerator        |
|                       |                        |       |        | igurationData | sub-system                         |
+-----------------------+------------------------+-------+--------+---------------+------------------------------------+
|                       |                        |       | Input  | accFilter     | Filter for subsystems from which   |
|                       |                        |       |        |               | config data requested              |
|                       |                        |       +--------+---------------+------------------------------------+
| GetAccConfigsRequest  | GetAccConfigsResponse  | VNF → | Input  | accConfigSele | attributes of config types         |
|                       |                        |       |        | ctor          |                                    |
|                       |                        | NFVI  +--------+---------------+------------------------------------+
|                       |                        |       | Output | accConfigs    | Config info (only for the          |
|                       |                        |       |        |               | specified attributes) for          |
|                       |                        |       |        |               | specified subsystems               |
+-----------------------+------------------------+-------+--------+---------------+------------------------------------+
|                       |                        |       | Input  | accFilter     | Filter for subsystems for which    |
|                       |                        | VNF → |        |               | config is to be reset              |
| ResetAccConfigsReque  | ResetAccConfigsRespon  | NFVI  +--------+---------------+------------------------------------+
| st                    | se                     |       | Input  | accConfigSele | attributes of config types whose   |
|                       |                        |       |        | ctor          | values will be reset               |
+-----------------------+------------------------+-------+--------+---------------+------------------------------------+
|                       |                        |       | Input  | accData       | Data (metadata) sent to            |
|                       |                        |       |        |               | accelerator                        |
|                       |                        |       +--------+---------------+------------------------------------+
| AccDataRequest        | AccDataResponse        | VNF → | Input  | accChannel    | Channel data is to be sent to      |
|                       |                        | NFVI  +--------+---------------+------------------------------------+
|                       |                        |       | Output | accData       | Data from accelerator              |
+-----------------------+------------------------+-------+--------+---------------+------------------------------------+
| AccSendDataRequest    | AccSendDataResponse    | VNF → | Input  | accData       | Data (metadata) sent too           |
|                       |                        | NFVI  |        |               | accelerator                        |
|                       |                        |       +--------+---------------+------------------------------------+
|                       |                        |       | Input  | accChannel    | Channel data is to be sent to      |
+-----------------------+------------------------+-------+--------+---------------+------------------------------------+
|                       |                        |       | Input  | maxNumberOfDa | Max number of data items to be     |
|                       |                        |       |        | taItems       | received                           |
|                       |                        |       +--------+---------------+------------------------------------+
| AccReceiveDataRequest | AccReceiveDataResponse | VNF → | Input  | accChannel    | Channel data is requested from     |
|                       |                        | NFVI  +--------+---------------+------------------------------------+
|                       |                        |       | Output | accData       | Data received form Accelerator     |
+-----------------------+------------------------+-------+--------+---------------+------------------------------------+
| RegisterForAccDataAva | RegisterForAccDataAvai | VNF → | Input  | regHandlerId  | Registration Identifier            |
| ilableEventRequest    | lableEventResponse     | NFVI  +--------+---------------+------------------------------------+
|                       |                        |       | Input  | accChannel    | Channel where event is requested   |
|                       |                        |       |        |               | for                                |
+-----------------------+------------------------+-------+--------+---------------+------------------------------------+
| AccDataAvailableEvent | AccDataAvailableEventN | NFVI  | Input  | regHandlerId  | Reference used by VNF when         |
| NotificationRequest   | otificationResponse    | → VNF |        |               | registering for the event          |
+-----------------------+------------------------+-------+--------+---------------+------------------------------------+
| DeRegisterForAccDataA | DeRegisterForAccDataAv | VNF → | Input  | accChannel    | Channel related to the event       |
| vailableEventRequest  | ailableEventResponse   | NFVI  |        |               |                                    |
+-----------------------+------------------------+-------+--------+---------------+------------------------------------+
|                       |                        |       | Input  | attachTargetI | the resource the accelerator is to |
|                       |                        |       |        | nfo           | be attached to (e.g., VM)          |
|                       |                        |       +--------+---------------+------------------------------------+
| AllocateAccResourceRe | AllocateAccResourceRes | VIM → | Input  | accResourceI  | Accelerator Information            |
| quest                 | ponse                  | NFVI  |        | nfo           |                                    |
|                       |                        |       +--------+---------------+------------------------------------+
|                       |                        |       | Output | accResourceId | Id if successful                   |
+-----------------------+------------------------+-------+--------+---------------+------------------------------------+
| ReleaseAccResourceReq | ReleaseAccResourceResp | VIM → | Input  | accResourceId | Id of resource to be released      |
| uest                  | onse                   | NFVI  |        |               |                                    |
+-----------------------+------------------------+-------+--------+---------------+------------------------------------+
|                       |                        |       | Input  | hostId        | Id of specified host               |
|                       |                        |       +--------+---------------+------------------------------------+
| QueryAccResourceReque | QueryAccResourceRespon | VIM → | Input  | Filter        | Specifies the accelerators for     |
| st                    | se                     | NFVI  |        |               | which query applies                |
|                       |                        |       +--------+---------------+------------------------------------+
|                       |                        |       | Output | accQueryResu  | Details of the accelerators        |
|                       |                        |       |        | lt            | matching the input filter located  |
|                       |                        |       |        |               | in the selected host.              |
+-----------------------+------------------------+-------+--------+---------------+------------------------------------+
|                       |                        |       | Input  | accFilter     | Accelerator subsystems from which  |
|                       |                        |       |        |               | data is requested                  |
|                       |                        |       +--------+---------------+------------------------------------+
| GetAccStatisticsReque | GetAccStatisticsRespon | VIM → | Input  | accStatSelect | attributes of AccStatistics whose  |
| st                    | se                     | NFVI  |        | or            | data will be returned              |
|                       |                        |       +--------+---------------+------------------------------------+
|                       |                        |       | Output | accStatistics | Statistics data of the             |
|                       |                        |       |        |               | accelerators matching the input    |
|                       |                        |       |        |               | filter located in the selected     |
|                       |                        |       |        |               | host.                              |
+-----------------------+------------------------+-------+--------+---------------+------------------------------------+
| ResetAccStatisticsReq | ResetAccStatisticsResp | VIM → | Input  | accFilter     | Accelerator subsystems for which   |
| uest                  | onse                   | NFVI  |        |               | data is to be reset                |
|                       |                        |       +--------+---------------+------------------------------------+
|                       |                        |       | Input  | accStatSelect | attributes of AccStatistics whose  |
|                       |                        |       |        | or            | data will be reset                 |
+-----------------------+------------------------+-------+--------+---------------+------------------------------------+
|                       |                        |       | Input  | hostId        | Id of specified host               |
|                       |                        |       +--------+---------------+------------------------------------+
| SubscribeRequest      | SubscribeResponse      | VIM → | Input  | Filter        | Specifies the accelerators and     |
|                       |                        | NFVI  |        |               | related alarms The filter could    |
|                       |                        |       |        |               | include accelerator information,   |
|                       |                        |       |        |               | severity of the alarm, etc.        |
|                       |                        |       +--------+---------------+------------------------------------+
|                       |                        |       | Output | Subscriptio   | Identifier of the successfully     |
|                       |                        |       |        | nId           | created subscription.              |
+-----------------------+------------------------+-------+--------+---------------+------------------------------------+
| UnsubscribeRequest    | UnsubscribeResponse    | VIM → | Input  | hostId        | Id of specified host               |
|                       |                        | NFVI  +--------+---------------+------------------------------------+
|                       |                        |       | Input  | Subscription  | Identifier of the subscription to  |
|                       |                        |       |        | Id            | be unsubscribed.                   |
+-----------------------+------------------------+-------+--------+---------------+------------------------------------+
| Notify                |                        | NFVI  |        |               | NFVI notifies an alarm to VIM      |
|                       |                        | → VIM |        |               |                                    |
+-----------------------+------------------------+-------+--------+---------------+------------------------------------+
|                       |                        |       | Input  | hostId        | Id of specified host               |
|                       |                        |       +--------+---------------+------------------------------------+
| GetAlarmInfoRequest   | GetAlarmInfoResponse   | VIM → | Input  | Filter        | Specifies the accelerators and     |
|                       |                        | NFVI  |        |               | related alarms The filter could    |
|                       |                        |       |        |               | include accelerator information,   |
|                       |                        |       |        |               | severity of the alarm, etc.        |
|                       |                        |       +--------+---------------+------------------------------------+
|                       |                        |       | Output | Alarm         | Information about the alarms if    |
|                       |                        |       |        |               | filter matches an alarm.           |
+-----------------------+------------------------+-------+--------+---------------+------------------------------------+
| AccResourcesDiscovery | AccResourcesDiscoveryR | VIM → | Input  | hostId        | Id of specified host               |
| Request               | esponse                | NFVI  +--------+---------------+------------------------------------+
|                       |                        |       | Output | discoveredAcc | Information on the acceleration    |
|                       |                        |       |        | ResourceInfo  | resources discovered within the    |
|                       |                        |       |        |               | NFVI.                              |
+-----------------------+------------------------+-------+--------+---------------+------------------------------------+
|                       |                        |       | Input  | accResourceId | Identifier of the chosen           |
|                       |                        |       |        |               | accelerator in the NFVI.           |
|                       |                        |       +--------+---------------+------------------------------------+
| OnloadAccImageRequest | OnloadAccImageResponse | VIM → | Input  | accImageInfo  | Information about the acceleration |
|                       |                        | NFVI  |        |               | image.                             |
|                       |                        |       +--------+---------------+------------------------------------+
|                       |                        |       | Input  | accImage      | The binary file of acceleration    |
|                       |                        |       |        |               | image.                             |
+-----------------------+------------------------+-------+--------+---------------+------------------------------------+

**Table 6-3:** Hardware Acceleration Interfaces in the ETSI NFV architecture

Intra-Cloud Infrastructure Interfaces
-------------------------------------

Hypervisor Hardware Interface
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Table 6-1 lists a number of NFVI and VIM interfaces, including the internal VI-Ha interface. The VI-Ha interface allows
the hypervisor to control the physical infrastructure; the hypervisor acts under VIM control. The VIM issues all
requests and responses using the NF-VI interface; requests and responses include commands, configuration requests,
policies, updates, alerts, and response to infrastructure results. The hypervisor also provides information about the
health of the physical infrastructure resources to the VM. All these activities, on behalf of the VIM, are performed by
the hypervisor using the VI-Ha interface. While no abstract APIs have yet been defined for this internal VI-Ha
interface, ETSI GS NFV-INF 004 :cite:p:`etsigsnfvinf004` defines a set of requirements and details of the information that is required by the
VIM from the physical infrastructure resources. Hypervisors utilize various programs to get this data including BIOS,
IPMI, PCI, I/O Adapters/Drivers, etc.

Enabler Services Interfaces
---------------------------

An operational cloud needs a set of standard services to function. Services such as NTP for time synchronization, DHCP
for IP address allocation, DNS for obtaining IP addresses for domain names, and LBaaS (version 2) to distribute incoming
requests amongst a pool of designated resources.
