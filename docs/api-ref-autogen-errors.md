# API Reference - Error Handling

When a low-level transport error occurs, or a request is malformed at the HTTP
or RPC level, the server may send an HTTP 500 error response, or the client
may simulate the same. The client must be prepared to handle these errors,
though they may be treated as fatal.

On the wire, these are transmitted in a form similar to this when using the
XML-RPC protocol:

```
$curl -D - -X POST https://server -H 'Content-Type: application/xml' \
> -d '<?xml version="1.0"?>
> <methodCall>
>   <methodName>session.logout</methodName>
> </methodCall>'
HTTP/1.1 500 Internal Error
content-length: 297
content-type:text/html
connection:close
cache-control:no-cache, no-store

<html><body><h1>HTTP 500 internal server error</h1>An unexpected error occurred;
 please wait a while and try again. If the problem persists, please contact your
 support representative.<h1> Additional information </h1>Xmlrpc.Parse_error(&quo
t;close_tag&quot;, &quot;open_tag&quot;, _)</body></html>
```

When using the JSON-RPC protocol:

```
$curl -D - -X POST https://server/jsonrpc -H 'Content-Type: application/json' \
> -d '{
>     "jsonrpc": "2.0",
>     "method": "session.login_with_password",
>     "id": 0
> }'
HTTP/1.1 500 Internal Error
content-length: 308
content-type:text/html
connection:close
cache-control:no-cache, no-store

<html><body><h1>HTTP 500 internal server error</h1>An unexpected error occurred;
 please wait a while and try again. If the problem persists, please contact your
 support representative.<h1> Additional information </h1>Jsonrpc.Malformed_metho
d_request(&quot;{jsonrpc=...,method=...,id=...}&quot;)</body></html>
```

All other failures are reported with a more structured error response, to
allow better automatic response to failures, proper internationalisation of
any error message, and easier debugging.

On the wire, these are transmitted like this when using the XML-RPC protocol:

```xml
    <struct>
      <member>
        <name>Status</name>
        <value>Failure</value>
      </member>
      <member>
        <name>ErrorDescription</name>
        <value>
          <array>
            <data>
              <value>MAP_DUPLICATE_KEY</value>
              <value>Customer</value>
              <value>eSpiel Inc.</value>
              <value>eSpiel Incorporated</value>
            </data>
          </array>
        </value>
      </member>
    </struct>
```

Note that `ErrorDescription` value is an array of string values. The
first element of the array is an error code; the remainder of the array are
strings representing error parameters relating to that code.  In this case,
the client has attempted to add the mapping _Customer &#45;&gt;
eSpiel Incorporated_ to a Map, but it already contains the mapping
_Customer &#45;&gt; eSpiel Inc._, and so the request has failed.

When using the JSON-RPC protocol v2.0, the above error is transmitted as:

```json
{
    "jsonrpc": "2.0",
    "error": {
        "code": 1,
        "message": "MAP_DUPLICATE_KEY",
        "data": [
            "Customer","eSpiel Inc.","eSpiel Incorporated"
        ]
    },
    "id": 3
  }
```

Finally, when using the JSON-RPC protocol v1.0:

```json
{
  "result": null,
  "error": [
      "MAP_DUPLICATE_KEY","Customer","eSpiel Inc.","eSpiel Incorporated"
  ],
  "id": "xyz"
}
```

Each possible error code is documented in the following section.

## Error Codes

### ACTIVATION&#95;WHILE&#95;NOT&#95;FREE

An activation key can only be applied when the edition is set to 'free'.

No parameters.

### ADDRESS&#95;VIOLATES&#95;LOCKING&#95;CONSTRAINT

The specified IP address violates the VIF locking configuration.

_Signature:_

```
ADDRESS_VIOLATES_LOCKING_CONSTRAINT(address)
```

### AUTH&#95;ALREADY&#95;ENABLED

External authentication for this host is already enabled.

_Signature:_

```
AUTH_ALREADY_ENABLED(current auth_type, current service_name)
```

### AUTH&#95;DISABLE&#95;FAILED

The host failed to disable external authentication.

_Signature:_

```
AUTH_DISABLE_FAILED(message)
```

### AUTH&#95;DISABLE&#95;FAILED&#95;PERMISSION&#95;DENIED

The host failed to disable external authentication.

_Signature:_

```
AUTH_DISABLE_FAILED_PERMISSION_DENIED(message)
```

### AUTH&#95;DISABLE&#95;FAILED&#95;WRONG&#95;CREDENTIALS

The host failed to disable external authentication.

_Signature:_

```
AUTH_DISABLE_FAILED_WRONG_CREDENTIALS(message)
```

### AUTH&#95;ENABLE&#95;FAILED

The host failed to enable external authentication.

_Signature:_

```
AUTH_ENABLE_FAILED(message)
```

### AUTH&#95;ENABLE&#95;FAILED&#95;DOMAIN&#95;LOOKUP&#95;FAILED

The host failed to enable external authentication.

_Signature:_

```
AUTH_ENABLE_FAILED_DOMAIN_LOOKUP_FAILED(message)
```

### AUTH&#95;ENABLE&#95;FAILED&#95;INVALID&#95;ACCOUNT

The host failed to enable external authentication.

_Signature:_

```
AUTH_ENABLE_FAILED_INVALID_ACCOUNT(message)
```

### AUTH&#95;ENABLE&#95;FAILED&#95;INVALID&#95;OU

The host failed to enable external authentication.

_Signature:_

```
AUTH_ENABLE_FAILED_INVALID_OU(message)
```

### AUTH&#95;ENABLE&#95;FAILED&#95;PERMISSION&#95;DENIED

The host failed to enable external authentication.

_Signature:_

```
AUTH_ENABLE_FAILED_PERMISSION_DENIED(message)
```

### AUTH&#95;ENABLE&#95;FAILED&#95;UNAVAILABLE

The host failed to enable external authentication.

_Signature:_

```
AUTH_ENABLE_FAILED_UNAVAILABLE(message)
```

### AUTH&#95;ENABLE&#95;FAILED&#95;WRONG&#95;CREDENTIALS

The host failed to enable external authentication.

_Signature:_

```
AUTH_ENABLE_FAILED_WRONG_CREDENTIALS(message)
```

### AUTH&#95;IS&#95;DISABLED

External authentication is disabled, unable to resolve subject name.

No parameters.

### AUTH&#95;SERVICE&#95;ERROR

Error querying the external directory service.

_Signature:_

```
AUTH_SERVICE_ERROR(message)
```

### AUTH&#95;UNKNOWN&#95;TYPE

Unknown type of external authentication.

_Signature:_

```
AUTH_UNKNOWN_TYPE(type)
```

### BACKUP&#95;SCRIPT&#95;FAILED

The backup could not be performed because the backup script failed.

_Signature:_

```
BACKUP_SCRIPT_FAILED(log)
```

### BALLOONING&#95;TIMEOUT&#95;BEFORE&#95;MIGRATION

Timeout trying to balloon down memory before VM migration. If the error occurs repeatedly, consider increasing the memory&#45;dynamic&#45;min value.

_Signature:_

```
BALLOONING_TIMEOUT_BEFORE_MIGRATION(vm)
```

### BOOTLOADER&#95;FAILED

The bootloader returned an error

_Signature:_

```
BOOTLOADER_FAILED(vm, msg)
```

### BRIDGE&#95;NAME&#95;EXISTS

The specified bridge already exists.

_Signature:_

```
BRIDGE_NAME_EXISTS(bridge)
```

### BRIDGE&#95;NOT&#95;AVAILABLE

Could not find bridge required by VM.

_Signature:_

```
BRIDGE_NOT_AVAILABLE(bridge)
```

### CANNOT&#95;ADD&#95;TUNNEL&#95;TO&#95;BOND&#95;SLAVE

This PIF is a bond slave and cannot have a tunnel on it.

_Signature:_

```
CANNOT_ADD_TUNNEL_TO_BOND_SLAVE(PIF)
```

### CANNOT&#95;ADD&#95;TUNNEL&#95;TO&#95;SRIOV&#95;LOGICAL

This is a network SR&#45;IOV logical PIF and cannot have a tunnel on it.

_Signature:_

```
CANNOT_ADD_TUNNEL_TO_SRIOV_LOGICAL(PIF)
```

### CANNOT&#95;ADD&#95;TUNNEL&#95;TO&#95;VLAN&#95;ON&#95;SRIOV&#95;LOGICAL

This is a vlan PIF on network SR&#45;IOV and cannot have a tunnel on it.

_Signature:_

```
CANNOT_ADD_TUNNEL_TO_VLAN_ON_SRIOV_LOGICAL(PIF)
```

### CANNOT&#95;ADD&#95;VLAN&#95;TO&#95;BOND&#95;SLAVE

This PIF is a bond slave and cannot have a VLAN on it.

_Signature:_

```
CANNOT_ADD_VLAN_TO_BOND_SLAVE(PIF)
```

### CANNOT&#95;CHANGE&#95;PIF&#95;PROPERTIES

This properties of this PIF cannot be changed. Only the properties of non&#45;bonded physical PIFs, or bond masters can be changed.

_Signature:_

```
CANNOT_CHANGE_PIF_PROPERTIES(PIF)
```

### CANNOT&#95;CONTACT&#95;HOST

Cannot forward messages because the host cannot be contacted.  The host may be switched off or there may be network connectivity problems.

_Signature:_

```
CANNOT_CONTACT_HOST(host)
```

### CANNOT&#95;CREATE&#95;STATE&#95;FILE

An HA statefile could not be created, perhaps because no SR with the appropriate capability was found.

No parameters.

### CANNOT&#95;DESTROY&#95;DISASTER&#95;RECOVERY&#95;TASK

The disaster recovery task could not be cleanly destroyed.

_Signature:_

```
CANNOT_DESTROY_DISASTER_RECOVERY_TASK(reason)
```

### CANNOT&#95;DESTROY&#95;SYSTEM&#95;NETWORK

You tried to destroy a system network: these cannot be destroyed.

_Signature:_

```
CANNOT_DESTROY_SYSTEM_NETWORK(network)
```

### CANNOT&#95;ENABLE&#95;REDO&#95;LOG

Could not enable redo log.

_Signature:_

```
CANNOT_ENABLE_REDO_LOG(reason)
```

### CANNOT&#95;EVACUATE&#95;HOST

This host cannot be evacuated.

_Signature:_

```
CANNOT_EVACUATE_HOST(errors)
```

### CANNOT&#95;FETCH&#95;PATCH

The requested update could to be obtained from the master.

_Signature:_

```
CANNOT_FETCH_PATCH(uuid)
```

### CANNOT&#95;FIND&#95;OEM&#95;BACKUP&#95;PARTITION

The backup partition to stream the updat to cannot be found

No parameters.

### CANNOT&#95;FIND&#95;PATCH

The requested update could not be found.  This can occur when you designate a new master or xe patch&#45;clean.  Please upload the update again

No parameters.

### CANNOT&#95;FIND&#95;STATE&#95;PARTITION

This operation could not be performed because the state partition could not be found

No parameters.

### CANNOT&#95;FIND&#95;UPDATE

The requested update could not be found. Please upload the update again. This can occur when you run xe update&#45;pool&#45;clean before xe update&#45;apply. 

No parameters.

### CANNOT&#95;FORGET&#95;SRIOV&#95;LOGICAL

This is a network SR&#45;IOV logical PIF and cannot do forget on it

_Signature:_

```
CANNOT_FORGET_SRIOV_LOGICAL(PIF)
```

### CANNOT&#95;PLUG&#95;BOND&#95;SLAVE

This PIF is a bond slave and cannot be plugged.

_Signature:_

```
CANNOT_PLUG_BOND_SLAVE(PIF)
```

### CANNOT&#95;PLUG&#95;VIF

Cannot plug VIF

_Signature:_

```
CANNOT_PLUG_VIF(VIF)
```

### CANNOT&#95;RESET&#95;CONTROL&#95;DOMAIN

The power&#45;state of a control domain cannot be reset.

_Signature:_

```
CANNOT_RESET_CONTROL_DOMAIN(vm)
```

### CERTIFICATE&#95;ALREADY&#95;EXISTS

A certificate already exists with the specified name.

_Signature:_

```
CERTIFICATE_ALREADY_EXISTS(name)
```

### CERTIFICATE&#95;CORRUPT

The specified certificate is corrupt or unreadable.

_Signature:_

```
CERTIFICATE_CORRUPT(name)
```

### CERTIFICATE&#95;DOES&#95;NOT&#95;EXIST

The specified certificate does not exist.

_Signature:_

```
CERTIFICATE_DOES_NOT_EXIST(name)
```

### CERTIFICATE&#95;LIBRARY&#95;CORRUPT

The certificate library is corrupt or unreadable.

No parameters.

### CERTIFICATE&#95;NAME&#95;INVALID

The specified certificate name is invalid.

_Signature:_

```
CERTIFICATE_NAME_INVALID(name)
```

### CHANGE&#95;PASSWORD&#95;REJECTED

The system rejected the password change request; perhaps the new password was too short?

_Signature:_

```
CHANGE_PASSWORD_REJECTED(msg)
```

### CLUSTERED&#95;SR&#95;DEGRADED

An SR is using clustered local storage. It is not safe to reboot a host at the moment.

_Signature:_

```
CLUSTERED_SR_DEGRADED(sr)
```

### CLUSTERING&#95;DISABLED

An operation was attempted while clustering was disabled on the cluster&#95;host.

_Signature:_

```
CLUSTERING_DISABLED(cluster_host)
```

### CLUSTERING&#95;ENABLED

An operation was attempted while clustering was enabled on the cluster&#95;host.

_Signature:_

```
CLUSTERING_ENABLED(cluster_host)
```

### CLUSTER&#95;ALREADY&#95;EXISTS

A cluster already exists in the pool.

No parameters.

### CLUSTER&#95;CREATE&#95;IN&#95;PROGRESS

The operation could not be performed because cluster creation is in progress.

No parameters.

### CLUSTER&#95;DOES&#95;NOT&#95;HAVE&#95;ONE&#95;NODE

An operation failed as it expected the cluster to have only one node but found multiple cluster&#95;hosts.

_Signature:_

```
CLUSTER_DOES_NOT_HAVE_ONE_NODE(number_of_nodes)
```

### CLUSTER&#95;FORCE&#95;DESTROY&#95;FAILED

Force destroy failed on a Cluster&#95;host while force destroying the cluster.

_Signature:_

```
CLUSTER_FORCE_DESTROY_FAILED(cluster)
```

### CLUSTER&#95;HOST&#95;IS&#95;LAST

The last cluster host cannot be destroyed. Destroy the cluster instead

_Signature:_

```
CLUSTER_HOST_IS_LAST(cluster_host)
```

### CLUSTER&#95;HOST&#95;NOT&#95;JOINED

Cluster&#95;host operation failed as the cluster&#95;host has not joined the cluster.

_Signature:_

```
CLUSTER_HOST_NOT_JOINED(cluster_host)
```

### CLUSTER&#95;STACK&#95;IN&#95;USE

The cluster stack is already in use.

_Signature:_

```
CLUSTER_STACK_IN_USE(cluster_stack)
```

### COULD&#95;NOT&#95;FIND&#95;NETWORK&#95;INTERFACE&#95;WITH&#95;SPECIFIED&#95;DEVICE&#95;NAME&#95;AND&#95;MAC&#95;ADDRESS

Could not find a network interface with the specified device name and MAC address.

_Signature:_

```
COULD_NOT_FIND_NETWORK_INTERFACE_WITH_SPECIFIED_DEVICE_NAME_AND_MAC_ADDRESS(device, mac)
```

### COULD&#95;NOT&#95;IMPORT&#95;DATABASE

An error occurred while attempting to import a database from a metadata VDI

_Signature:_

```
COULD_NOT_IMPORT_DATABASE(reason)
```

### COULD&#95;NOT&#95;UPDATE&#95;IGMP&#95;SNOOPING&#95;EVERYWHERE

The IGMP Snooping setting cannot be applied for some of the host, network&#40;s&#41;.

No parameters.

### CPU&#95;FEATURE&#95;MASKING&#95;NOT&#95;SUPPORTED

The CPU does not support masking of features.

_Signature:_

```
CPU_FEATURE_MASKING_NOT_SUPPORTED(details)
```

### CRL&#95;ALREADY&#95;EXISTS

A CRL already exists with the specified name.

_Signature:_

```
CRL_ALREADY_EXISTS(name)
```

### CRL&#95;CORRUPT

The specified CRL is corrupt or unreadable.

_Signature:_

```
CRL_CORRUPT(name)
```

### CRL&#95;DOES&#95;NOT&#95;EXIST

The specified CRL does not exist.

_Signature:_

```
CRL_DOES_NOT_EXIST(name)
```

### CRL&#95;NAME&#95;INVALID

The specified CRL name is invalid.

_Signature:_

```
CRL_NAME_INVALID(name)
```

### DB&#95;UNIQUENESS&#95;CONSTRAINT&#95;VIOLATION

You attempted an operation which would have resulted in duplicate keys in the database.

_Signature:_

```
DB_UNIQUENESS_CONSTRAINT_VIOLATION(table, field, value)
```

### DEFAULT&#95;SR&#95;NOT&#95;FOUND

The default SR reference does not point to a valid SR

_Signature:_

```
DEFAULT_SR_NOT_FOUND(sr)
```

### DEVICE&#95;ALREADY&#95;ATTACHED

The device is already attached to a VM

_Signature:_

```
DEVICE_ALREADY_ATTACHED(device)
```

### DEVICE&#95;ALREADY&#95;DETACHED

The device is not currently attached

_Signature:_

```
DEVICE_ALREADY_DETACHED(device)
```

### DEVICE&#95;ALREADY&#95;EXISTS

A device with the name given already exists on the selected VM

_Signature:_

```
DEVICE_ALREADY_EXISTS(device)
```

### DEVICE&#95;ATTACH&#95;TIMEOUT

A timeout happened while attempting to attach a device to a VM.

_Signature:_

```
DEVICE_ATTACH_TIMEOUT(type, ref)
```

### DEVICE&#95;DETACH&#95;REJECTED

The VM rejected the attempt to detach the device.

_Signature:_

```
DEVICE_DETACH_REJECTED(type, ref, msg)
```

### DEVICE&#95;DETACH&#95;TIMEOUT

A timeout happened while attempting to detach a device from a VM.

_Signature:_

```
DEVICE_DETACH_TIMEOUT(type, ref)
```

### DEVICE&#95;NOT&#95;ATTACHED

The operation could not be performed because the VBD was not connected to the VM.

_Signature:_

```
DEVICE_NOT_ATTACHED(VBD)
```

### DISK&#95;VBD&#95;MUST&#95;BE&#95;READWRITE&#95;FOR&#95;HVM

All VBDs of type 'disk' must be read/write for HVM guests

_Signature:_

```
DISK_VBD_MUST_BE_READWRITE_FOR_HVM(vbd)
```

### DOMAIN&#95;BUILDER&#95;ERROR

An internal error generated by the domain builder.

_Signature:_

```
DOMAIN_BUILDER_ERROR(function, code, message)
```

### DOMAIN&#95;EXISTS

The operation could not be performed because a domain still exists for the specified VM.

_Signature:_

```
DOMAIN_EXISTS(vm, domid)
```

### DUPLICATE&#95;MAC&#95;SEED

This MAC seed is already in use by a VM in the pool

_Signature:_

```
DUPLICATE_MAC_SEED(seed)
```

### DUPLICATE&#95;PIF&#95;DEVICE&#95;NAME

A PIF with this specified device name already exists.

_Signature:_

```
DUPLICATE_PIF_DEVICE_NAME(device)
```

### DUPLICATE&#95;VM

Cannot restore this VM because it would create a duplicate

_Signature:_

```
DUPLICATE_VM(vm)
```

### EVENTS&#95;LOST

Some events have been lost from the queue and cannot be retrieved.

No parameters.

### EVENT&#95;FROM&#95;TOKEN&#95;PARSE&#95;FAILURE

The event.from token could not be parsed. Valid values include: '', and a value returned from a previous event.from call.

_Signature:_

```
EVENT_FROM_TOKEN_PARSE_FAILURE(token)
```

### EVENT&#95;SUBSCRIPTION&#95;PARSE&#95;FAILURE

The server failed to parse your event subscription. Valid values include: &#42;, class&#45;name, class&#45;name/object&#45;reference.

_Signature:_

```
EVENT_SUBSCRIPTION_PARSE_FAILURE(subscription)
```

### FAILED&#95;TO&#95;START&#95;EMULATOR

An emulator required to run this VM failed to start

_Signature:_

```
FAILED_TO_START_EMULATOR(vm, name, msg)
```

### FEATURE&#95;REQUIRES&#95;HVM

The VM is set up to use a feature that requires it to boot as HVM.

_Signature:_

```
FEATURE_REQUIRES_HVM(details)
```

### FEATURE&#95;RESTRICTED

The use of this feature is restricted.

No parameters.

### FIELD&#95;TYPE&#95;ERROR

The value specified is of the wrong type

_Signature:_

```
FIELD_TYPE_ERROR(field)
```

### GPU&#95;GROUP&#95;CONTAINS&#95;NO&#95;PGPUS

The GPU group does not contain any PGPUs.

_Signature:_

```
GPU_GROUP_CONTAINS_NO_PGPUS(gpu_group)
```

### GPU&#95;GROUP&#95;CONTAINS&#95;PGPU

The GPU group contains active PGPUs and cannot be deleted.

_Signature:_

```
GPU_GROUP_CONTAINS_PGPU(pgpus)
```

### GPU&#95;GROUP&#95;CONTAINS&#95;VGPU

The GPU group contains active VGPUs and cannot be deleted.

_Signature:_

```
GPU_GROUP_CONTAINS_VGPU(vgpus)
```

### HANDLE&#95;INVALID

You gave an invalid object reference.  The object may have recently been deleted.  The class parameter gives the type of reference given, and the handle parameter echoes the bad value given.

_Signature:_

```
HANDLE_INVALID(class, handle)
```

### HA&#95;ABORT&#95;NEW&#95;MASTER

This host cannot accept the proposed new master setting at this time.

_Signature:_

```
HA_ABORT_NEW_MASTER(reason)
```

### HA&#95;CANNOT&#95;CHANGE&#95;BOND&#95;STATUS&#95;OF&#95;MGMT&#95;IFACE

This operation cannot be performed because creating or deleting a bond involving the management interface is not allowed while HA is on. In order to do that, disable HA, create or delete the bond then re&#45;enable HA.

No parameters.

### HA&#95;CONSTRAINT&#95;VIOLATION&#95;NETWORK&#95;NOT&#95;SHARED

This operation cannot be performed because the referenced network is not properly shared. The network must either be entirely virtual or must be physically present via a currently&#95;attached PIF on every host.

_Signature:_

```
HA_CONSTRAINT_VIOLATION_NETWORK_NOT_SHARED(network)
```

### HA&#95;CONSTRAINT&#95;VIOLATION&#95;SR&#95;NOT&#95;SHARED

This operation cannot be performed because the referenced SR is not properly shared. The SR must both be marked as shared and a currently&#95;attached PBD must exist for each host.

_Signature:_

```
HA_CONSTRAINT_VIOLATION_SR_NOT_SHARED(SR)
```

### HA&#95;DISABLE&#95;IN&#95;PROGRESS

The operation could not be performed because HA disable is in progress

No parameters.

### HA&#95;ENABLE&#95;IN&#95;PROGRESS

The operation could not be performed because HA enable is in progress

No parameters.

### HA&#95;FAILED&#95;TO&#95;FORM&#95;LIVESET

HA could not be enabled on the Pool because a liveset could not be formed: check storage and network heartbeat paths.

No parameters.

### HA&#95;HEARTBEAT&#95;DAEMON&#95;STARTUP&#95;FAILED

The host could not join the liveset because the HA daemon failed to start.

No parameters.

### HA&#95;HOST&#95;CANNOT&#95;ACCESS&#95;STATEFILE

The host could not join the liveset because the HA daemon could not access the heartbeat disk.

No parameters.

### HA&#95;HOST&#95;CANNOT&#95;SEE&#95;PEERS

The operation failed because the HA software on the specified host could not see a subset of other hosts. Check your network connectivity.

_Signature:_

```
HA_HOST_CANNOT_SEE_PEERS(host, all, subset)
```

### HA&#95;HOST&#95;IS&#95;ARMED

The operation could not be performed while the host is still armed; it must be disarmed first

_Signature:_

```
HA_HOST_IS_ARMED(host)
```

### HA&#95;IS&#95;ENABLED

The operation could not be performed because HA is enabled on the Pool

No parameters.

### HA&#95;LOST&#95;STATEFILE

This host lost access to the HA statefile.

No parameters.

### HA&#95;NOT&#95;ENABLED

The operation could not be performed because HA is not enabled on the Pool

No parameters.

### HA&#95;NOT&#95;INSTALLED

The operation could not be performed because the HA software is not installed on this host.

_Signature:_

```
HA_NOT_INSTALLED(host)
```

### HA&#95;NO&#95;PLAN

Cannot find a plan for placement of VMs as there are no other hosts available.

No parameters.

### HA&#95;OPERATION&#95;WOULD&#95;BREAK&#95;FAILOVER&#95;PLAN

This operation cannot be performed because it would invalidate VM failover planning such that the system would be unable to guarantee to restart protected VMs after a Host failure.

No parameters.

### HA&#95;POOL&#95;IS&#95;ENABLED&#95;BUT&#95;HOST&#95;IS&#95;DISABLED

This host cannot join the pool because the pool has HA enabled but this host has HA disabled.

No parameters.

### HA&#95;SHOULD&#95;BE&#95;FENCED

Host cannot rejoin pool because it should have fenced &#40;it is not in the master's partition&#41;

_Signature:_

```
HA_SHOULD_BE_FENCED(host)
```

### HA&#95;TOO&#95;FEW&#95;HOSTS

HA can only be enabled for 2 hosts or more. Note that 2 hosts requires a pre&#45;configured quorum tiebreak script.

No parameters.

### HOSTS&#95;NOT&#95;COMPATIBLE

The hosts in this pool are not compatible.

No parameters.

### HOSTS&#95;NOT&#95;HOMOGENEOUS

The hosts in this pool are not homogeneous.

_Signature:_

```
HOSTS_NOT_HOMOGENEOUS(reason)
```

### HOST&#95;BROKEN

This host failed in the middle of an automatic failover operation and needs to retry the failover action

No parameters.

### HOST&#95;CANNOT&#95;ATTACH&#95;NETWORK

Host cannot attach network &#40;in the case of NIC bonding, this may be because attaching the network on this host would require other networks &#91;that are currently active&#93; to be taken down&#41;.

_Signature:_

```
HOST_CANNOT_ATTACH_NETWORK(host, network)
```

### HOST&#95;CANNOT&#95;DESTROY&#95;SELF

The pool master host cannot be removed.

_Signature:_

```
HOST_CANNOT_DESTROY_SELF(host)
```

### HOST&#95;CANNOT&#95;READ&#95;METRICS

The metrics of this host could not be read.

No parameters.

### HOST&#95;CD&#95;DRIVE&#95;EMPTY

The host CDROM drive does not contain a valid CD

No parameters.

### HOST&#95;DISABLED

The specified host is disabled.

_Signature:_

```
HOST_DISABLED(host)
```

### HOST&#95;DISABLED&#95;UNTIL&#95;REBOOT

The specified host is disabled and cannot be re&#45;enabled until after it has rebooted.

_Signature:_

```
HOST_DISABLED_UNTIL_REBOOT(host)
```

### HOST&#95;EVACUATE&#95;IN&#95;PROGRESS

This host is being evacuated.

_Signature:_

```
HOST_EVACUATE_IN_PROGRESS(host)
```

### HOST&#95;HAS&#95;NO&#95;MANAGEMENT&#95;IP

The host failed to acquire an IP address on its management interface and therefore cannot contact the master.

No parameters.

### HOST&#95;HAS&#95;RESIDENT&#95;VMS

This host cannot be forgotten because there are some user VMs still running

_Signature:_

```
HOST_HAS_RESIDENT_VMS(host)
```

### HOST&#95;IN&#95;EMERGENCY&#95;MODE

Cannot perform operation as the host is running in emergency mode.

No parameters.

### HOST&#95;IN&#95;USE

This operation cannot be completed as the host is in use by &#40;at least&#41; the object of type and ref echoed below.

_Signature:_

```
HOST_IN_USE(host, type, ref)
```

### HOST&#95;IS&#95;LIVE

This operation cannot be completed as the host is still live.

_Signature:_

```
HOST_IS_LIVE(host)
```

### HOST&#95;IS&#95;SLAVE

You cannot make regular API calls directly on a slave. Please pass API calls via the master host.

_Signature:_

```
HOST_IS_SLAVE(Master IP address)
```

### HOST&#95;ITS&#95;OWN&#95;SLAVE

The host is its own slave. Please use pool&#45;emergency&#45;transition&#45;to&#45;master or pool&#45;emergency&#45;reset&#45;master.

No parameters.

### HOST&#95;MASTER&#95;CANNOT&#95;TALK&#95;BACK

The master reports that it cannot talk back to the slave on the supplied management IP address.

_Signature:_

```
HOST_MASTER_CANNOT_TALK_BACK(ip)
```

### HOST&#95;NAME&#95;INVALID

The host name is invalid.

_Signature:_

```
HOST_NAME_INVALID(reason)
```

### HOST&#95;NOT&#95;DISABLED

This operation cannot be performed because the host is not disabled. Please disable the host and then try again.

No parameters.

### HOST&#95;NOT&#95;ENOUGH&#95;FREE&#95;MEMORY

Not enough host memory is available to perform this operation

_Signature:_

```
HOST_NOT_ENOUGH_FREE_MEMORY(needed, available)
```

### HOST&#95;NOT&#95;ENOUGH&#95;PCPUS

The host does not have enough pCPUs to run the VM. It needs at least as many as the VM has vCPUs.

_Signature:_

```
HOST_NOT_ENOUGH_PCPUS(vcpus, pcpus)
```

### HOST&#95;NOT&#95;LIVE

This operation cannot be completed as the host is not live.

No parameters.

### HOST&#95;OFFLINE

You attempted an operation which involves a host which could not be contacted.

_Signature:_

```
HOST_OFFLINE(host)
```

### HOST&#95;POWER&#95;ON&#95;MODE&#95;DISABLED

This operation cannot be completed as the host power on mode is disabled.

No parameters.

### HOST&#95;STILL&#95;BOOTING

The host toolstack is still initialising. Please wait.

No parameters.

### HOST&#95;UNKNOWN&#95;TO&#95;MASTER

The master says the host is not known to it. Perhaps the Host was deleted from the master's database? Perhaps the slave is pointing to the wrong master?

_Signature:_

```
HOST_UNKNOWN_TO_MASTER(host)
```

### ILLEGAL&#95;VBD&#95;DEVICE

The specified VBD device is not recognized: please use a non&#45;negative integer

_Signature:_

```
ILLEGAL_VBD_DEVICE(vbd, device)
```

### IMPORT&#95;ERROR

The VM could not be imported.

_Signature:_

```
IMPORT_ERROR(msg)
```

### IMPORT&#95;ERROR&#95;ATTACHED&#95;DISKS&#95;NOT&#95;FOUND

The VM could not be imported because attached disks could not be found.

No parameters.

### IMPORT&#95;ERROR&#95;CANNOT&#95;HANDLE&#95;CHUNKED

Cannot import VM using chunked encoding.

No parameters.

### IMPORT&#95;ERROR&#95;FAILED&#95;TO&#95;FIND&#95;OBJECT

The VM could not be imported because a required object could not be found.

_Signature:_

```
IMPORT_ERROR_FAILED_TO_FIND_OBJECT(id)
```

### IMPORT&#95;ERROR&#95;PREMATURE&#95;EOF

The VM could not be imported; the end of the file was reached prematurely.

No parameters.

### IMPORT&#95;ERROR&#95;SOME&#95;CHECKSUMS&#95;FAILED

Some data checksums were incorrect; the VM may be corrupt.

No parameters.

### IMPORT&#95;ERROR&#95;UNEXPECTED&#95;FILE

The VM could not be imported because the XVA file is invalid: an unexpected file was encountered.

_Signature:_

```
IMPORT_ERROR_UNEXPECTED_FILE(filename_expected, filename_found)
```

### IMPORT&#95;INCOMPATIBLE&#95;VERSION

The import failed because this export has been created by a different &#40;incompatible&#41; product version

No parameters.

### INCOMPATIBLE&#95;CLUSTER&#95;STACK&#95;ACTIVE

This operation cannot be performed, because it is incompatible with the currently active HA cluster stack.

_Signature:_

```
INCOMPATIBLE_CLUSTER_STACK_ACTIVE(cluster_stack)
```

### INCOMPATIBLE&#95;PIF&#95;PROPERTIES

These PIFs cannot be bonded, because their properties are different.

No parameters.

### INCOMPATIBLE&#95;STATEFILE&#95;SR

The specified SR is incompatible with the selected HA cluster stack.

_Signature:_

```
INCOMPATIBLE_STATEFILE_SR(SR type)
```

### INTERFACE&#95;HAS&#95;NO&#95;IP

The specified interface cannot be used because it has no IP address

_Signature:_

```
INTERFACE_HAS_NO_IP(interface)
```

### INTERNAL&#95;ERROR

The server failed to handle your request, due to an internal error.  The given message may give details useful for debugging the problem.

_Signature:_

```
INTERNAL_ERROR(message)
```

### INVALID&#95;CIDR&#95;ADDRESS&#95;SPECIFIED

A required parameter contained an invalid CIDR address &#40;&lt;addr&gt;/&lt;prefix length&gt;&#41;

_Signature:_

```
INVALID_CIDR_ADDRESS_SPECIFIED(parameter)
```

### INVALID&#95;CLUSTER&#95;STACK

The cluster stack provided is not supported.

_Signature:_

```
INVALID_CLUSTER_STACK(cluster_stack)
```

### INVALID&#95;DEVICE

The device name is invalid

_Signature:_

```
INVALID_DEVICE(device)
```

### INVALID&#95;EDITION

The edition you supplied is invalid.

_Signature:_

```
INVALID_EDITION(edition)
```

### INVALID&#95;FEATURE&#95;STRING

The given feature string is not valid.

_Signature:_

```
INVALID_FEATURE_STRING(details)
```

### INVALID&#95;IP&#95;ADDRESS&#95;SPECIFIED

A required parameter contained an invalid IP address

_Signature:_

```
INVALID_IP_ADDRESS_SPECIFIED(parameter)
```

### INVALID&#95;PATCH

The uploaded patch file is invalid

No parameters.

### INVALID&#95;PATCH&#95;WITH&#95;LOG

The uploaded patch file is invalid.  See attached log for more details.

_Signature:_

```
INVALID_PATCH_WITH_LOG(log)
```

### INVALID&#95;UPDATE

The uploaded update package is invalid.

_Signature:_

```
INVALID_UPDATE(info)
```

### INVALID&#95;VALUE

The value given is invalid

_Signature:_

```
INVALID_VALUE(field, value)
```

### IS&#95;TUNNEL&#95;ACCESS&#95;PIF

You tried to create a VLAN or tunnel on top of a tunnel access PIF &#45; use the underlying transport PIF instead.

_Signature:_

```
IS_TUNNEL_ACCESS_PIF(PIF)
```

### JOINING&#95;HOST&#95;CANNOT&#95;BE&#95;MASTER&#95;OF&#95;OTHER&#95;HOSTS

The host joining the pool cannot already be a master of another pool.

No parameters.

### JOINING&#95;HOST&#95;CANNOT&#95;CONTAIN&#95;SHARED&#95;SRS

The host joining the pool cannot contain any shared storage.

No parameters.

### JOINING&#95;HOST&#95;CANNOT&#95;HAVE&#95;RUNNING&#95;OR&#95;SUSPENDED&#95;VMS

The host joining the pool cannot have any running or suspended VMs.

No parameters.

### JOINING&#95;HOST&#95;CANNOT&#95;HAVE&#95;RUNNING&#95;VMS

The host joining the pool cannot have any running VMs.

No parameters.

### JOINING&#95;HOST&#95;CANNOT&#95;HAVE&#95;VMS&#95;WITH&#95;CURRENT&#95;OPERATIONS

The host joining the pool cannot have any VMs with active tasks.

No parameters.

### JOINING&#95;HOST&#95;CONNECTION&#95;FAILED

There was an error connecting to the host while joining it in the pool.

No parameters.

### JOINING&#95;HOST&#95;SERVICE&#95;FAILED

There was an error connecting to the host. the service contacted didn't reply properly.

No parameters.

### LICENCE&#95;RESTRICTION

This operation is not allowed because your license lacks a needed feature.  Please contact your support representative.

_Signature:_

```
LICENCE_RESTRICTION(feature)
```

### LICENSE&#95;CANNOT&#95;DOWNGRADE&#95;WHILE&#95;IN&#95;POOL

Cannot downgrade license while in pool. Please disband the pool first, then downgrade licenses on hosts separately.

No parameters.

### LICENSE&#95;CHECKOUT&#95;ERROR

The license for the edition you requested is not available.

_Signature:_

```
LICENSE_CHECKOUT_ERROR(reason)
```

### LICENSE&#95;DOES&#95;NOT&#95;SUPPORT&#95;POOLING

This host cannot join a pool because its license does not support pooling.

No parameters.

### LICENSE&#95;DOES&#95;NOT&#95;SUPPORT&#95;XHA

XHA cannot be enabled because this host's license does not allow it.

No parameters.

### LICENSE&#95;EXPIRED

Your license has expired.  Please contact your support representative.

No parameters.

### LICENSE&#95;FILE&#95;DEPRECATED

This license file is no longer accepted. Please upgrade to the new licensing system.

No parameters.

### LICENSE&#95;HOST&#95;POOL&#95;MISMATCH

Host and pool have incompatible licenses &#40;editions&#41;.

No parameters.

### LICENSE&#95;PROCESSING&#95;ERROR

There was an error processing your license.  Please contact your support representative.

No parameters.

### LOCATION&#95;NOT&#95;UNIQUE

A VDI with the specified location already exists within the SR

_Signature:_

```
LOCATION_NOT_UNIQUE(SR, location)
```

### MAC&#95;DOES&#95;NOT&#95;EXIST

The MAC address specified doesn't exist on this host.

_Signature:_

```
MAC_DOES_NOT_EXIST(MAC)
```

### MAC&#95;INVALID

The MAC address specified is not valid.

_Signature:_

```
MAC_INVALID(MAC)
```

### MAC&#95;STILL&#95;EXISTS

The MAC address specified still exists on this host.

_Signature:_

```
MAC_STILL_EXISTS(MAC)
```

### MAP&#95;DUPLICATE&#95;KEY

You tried to add a key&#45;value pair to a map, but that key is already there.

_Signature:_

```
MAP_DUPLICATE_KEY(type, param_name, uuid, key)
```

### MEMORY&#95;CONSTRAINT&#95;VIOLATION

The dynamic memory range does not satisfy the following constraint.

_Signature:_

```
MEMORY_CONSTRAINT_VIOLATION(constraint)
```

### MESSAGE&#95;DEPRECATED

This message has been deprecated.

No parameters.

### MESSAGE&#95;METHOD&#95;UNKNOWN

You tried to call a method that does not exist.  The method name that you used is echoed.

_Signature:_

```
MESSAGE_METHOD_UNKNOWN(method)
```

### MESSAGE&#95;PARAMETER&#95;COUNT&#95;MISMATCH

You tried to call a method with the incorrect number of parameters.  The fully&#45;qualified method name that you used, and the number of received and expected parameters are returned.

_Signature:_

```
MESSAGE_PARAMETER_COUNT_MISMATCH(method, expected, received)
```

### MESSAGE&#95;REMOVED

This function is no longer available.

No parameters.

### MIRROR&#95;FAILED

The VDI mirroring cannot be performed

_Signature:_

```
MIRROR_FAILED(vdi)
```

### MISSING&#95;CONNECTION&#95;DETAILS

The license&#45;server connection details &#40;address or port&#41; were missing or incomplete.

No parameters.

### NETWORK&#95;ALREADY&#95;CONNECTED

You tried to create a PIF, but the network you tried to attach it to is already attached to some other PIF, and so the creation failed.

_Signature:_

```
NETWORK_ALREADY_CONNECTED(network, connected PIF)
```

### NETWORK&#95;CONTAINS&#95;PIF

The network contains active PIFs and cannot be deleted.

_Signature:_

```
NETWORK_CONTAINS_PIF(pifs)
```

### NETWORK&#95;CONTAINS&#95;VIF

The network contains active VIFs and cannot be deleted.

_Signature:_

```
NETWORK_CONTAINS_VIF(vifs)
```

### NETWORK&#95;HAS&#95;INCOMPATIBLE&#95;SRIOV&#95;PIFS

The PIF is not compatible with the selected SR&#45;IOV network

_Signature:_

```
NETWORK_HAS_INCOMPATIBLE_SRIOV_PIFS(PIF, network)
```

### NETWORK&#95;HAS&#95;INCOMPATIBLE&#95;VLAN&#95;ON&#95;SRIOV&#95;PIFS

VLAN on the PIF is not compatible with the selected SR&#45;IOV VLAN network

_Signature:_

```
NETWORK_HAS_INCOMPATIBLE_VLAN_ON_SRIOV_PIFS(PIF, network)
```

### NETWORK&#95;INCOMPATIBLE&#95;PURPOSES

You tried to add a purpose to a network but the new purpose is not compatible with an existing purpose of the network or other networks.

_Signature:_

```
NETWORK_INCOMPATIBLE_PURPOSES(new_purpose, conflicting_purpose)
```

### NETWORK&#95;INCOMPATIBLE&#95;WITH&#95;BOND

The network is incompatible with bond

_Signature:_

```
NETWORK_INCOMPATIBLE_WITH_BOND(network)
```

### NETWORK&#95;INCOMPATIBLE&#95;WITH&#95;SRIOV

The network is incompatible with sriov

_Signature:_

```
NETWORK_INCOMPATIBLE_WITH_SRIOV(network)
```

### NETWORK&#95;INCOMPATIBLE&#95;WITH&#95;TUNNEL

The network is incompatible with tunnel

_Signature:_

```
NETWORK_INCOMPATIBLE_WITH_TUNNEL(network)
```

### NETWORK&#95;INCOMPATIBLE&#95;WITH&#95;VLAN&#95;ON&#95;BRIDGE

The network is incompatible with vlan on bridge

_Signature:_

```
NETWORK_INCOMPATIBLE_WITH_VLAN_ON_BRIDGE(network)
```

### NETWORK&#95;INCOMPATIBLE&#95;WITH&#95;VLAN&#95;ON&#95;SRIOV

The network is incompatible with vlan on sriov

_Signature:_

```
NETWORK_INCOMPATIBLE_WITH_VLAN_ON_SRIOV(network)
```

### NETWORK&#95;SRIOV&#95;ALREADY&#95;ENABLED

The PIF selected for the SR&#45;IOV network is already enabled

_Signature:_

```
NETWORK_SRIOV_ALREADY_ENABLED(PIF)
```

### NETWORK&#95;SRIOV&#95;DISABLE&#95;FAILED

Failed to disable SR&#45;IOV on PIF

_Signature:_

```
NETWORK_SRIOV_DISABLE_FAILED(PIF, msg)
```

### NETWORK&#95;SRIOV&#95;ENABLE&#95;FAILED

Failed to enable SR&#45;IOV on PIF

_Signature:_

```
NETWORK_SRIOV_ENABLE_FAILED(PIF, msg)
```

### NETWORK&#95;SRIOV&#95;INSUFFICIENT&#95;CAPACITY

There is insufficient capacity for VF reservation

_Signature:_

```
NETWORK_SRIOV_INSUFFICIENT_CAPACITY(network)
```

### NETWORK&#95;UNMANAGED

The network is not managed by xapi.

_Signature:_

```
NETWORK_UNMANAGED(network)
```

### NOT&#95;ALLOWED&#95;ON&#95;OEM&#95;EDITION

This command is not allowed on the OEM edition.

_Signature:_

```
NOT_ALLOWED_ON_OEM_EDITION(command)
```

### NOT&#95;IMPLEMENTED

The function is not implemented

_Signature:_

```
NOT_IMPLEMENTED(function)
```

### NOT&#95;IN&#95;EMERGENCY&#95;MODE

This pool is not in emergency mode.

No parameters.

### NOT&#95;SUPPORTED&#95;DURING&#95;UPGRADE

This operation is not supported during an upgrade.

No parameters.

### NOT&#95;SYSTEM&#95;DOMAIN

The given VM is not registered as a system domain. This operation can only be performed on a registered system domain.

_Signature:_

```
NOT_SYSTEM_DOMAIN(vm)
```

### NO&#95;CLUSTER&#95;HOSTS&#95;REACHABLE

No other cluster host was reachable when joining

_Signature:_

```
NO_CLUSTER_HOSTS_REACHABLE(cluster)
```

### NO&#95;COMPATIBLE&#95;CLUSTER&#95;HOST

The host does not have a Cluster&#95;host with a compatible cluster stack.

_Signature:_

```
NO_COMPATIBLE_CLUSTER_HOST(host)
```

### NO&#95;HOSTS&#95;AVAILABLE

There were no hosts available to complete the specified operation.

No parameters.

### NO&#95;MORE&#95;REDO&#95;LOGS&#95;ALLOWED

The upper limit of active redo log instances was reached.

No parameters.

### NVIDIA&#95;TOOLS&#95;ERROR

Nvidia tools error. Please ensure that the latest Nvidia tools are installed

_Signature:_

```
NVIDIA_TOOLS_ERROR(host)
```

### OBJECT&#95;NOLONGER&#95;EXISTS

The specified object no longer exists.

No parameters.

### ONLY&#95;ALLOWED&#95;ON&#95;OEM&#95;EDITION

This command is only allowed on the OEM edition.

_Signature:_

```
ONLY_ALLOWED_ON_OEM_EDITION(command)
```

### OPENVSWITCH&#95;NOT&#95;ACTIVE

This operation needs the OpenVSwitch networking backend to be enabled on all hosts in the pool.

No parameters.

### OPERATION&#95;BLOCKED

You attempted an operation that was explicitly blocked &#40;see the blocked&#95;operations field of the given object&#41;.

_Signature:_

```
OPERATION_BLOCKED(ref, code)
```

### OPERATION&#95;NOT&#95;ALLOWED

You attempted an operation that was not allowed.

_Signature:_

```
OPERATION_NOT_ALLOWED(reason)
```

### OPERATION&#95;PARTIALLY&#95;FAILED

Some VMs belonging to the appliance threw an exception while carrying out the specified operation

_Signature:_

```
OPERATION_PARTIALLY_FAILED(operation)
```

### OTHER&#95;OPERATION&#95;IN&#95;PROGRESS

Another operation involving the object is currently in progress

_Signature:_

```
OTHER_OPERATION_IN_PROGRESS(class, object)
```

### OUT&#95;OF&#95;SPACE

There is not enough space to upload the update

_Signature:_

```
OUT_OF_SPACE(location)
```

### PASSTHROUGH&#95;NOT&#95;ENABLED

The passthrough&#95;enabled must be true before passthrough usb to vm.

_Signature:_

```
PASSTHROUGH_NOT_ENABLED(PUSB)
```

### PATCH&#95;ALREADY&#95;APPLIED

This patch has already been applied

_Signature:_

```
PATCH_ALREADY_APPLIED(patch)
```

### PATCH&#95;ALREADY&#95;EXISTS

The uploaded patch file already exists

_Signature:_

```
PATCH_ALREADY_EXISTS(uuid)
```

### PATCH&#95;APPLY&#95;FAILED

The patch apply failed.  Please see attached output.

_Signature:_

```
PATCH_APPLY_FAILED(output)
```

### PATCH&#95;APPLY&#95;FAILED&#95;BACKUP&#95;FILES&#95;EXIST

The patch apply failed: there are backup files created while applying patch. Please remove these backup files before applying patch again.

_Signature:_

```
PATCH_APPLY_FAILED_BACKUP_FILES_EXIST(output)
```

### PATCH&#95;IS&#95;APPLIED

The specified patch is applied and cannot be destroyed.

No parameters.

### PATCH&#95;PRECHECK&#95;FAILED&#95;ISO&#95;MOUNTED

Tools ISO must be ejected from all running VMs.

_Signature:_

```
PATCH_PRECHECK_FAILED_ISO_MOUNTED(patch)
```

### PATCH&#95;PRECHECK&#95;FAILED&#95;OUT&#95;OF&#95;SPACE

The patch precheck stage failed: the server does not have enough space.

_Signature:_

```
PATCH_PRECHECK_FAILED_OUT_OF_SPACE(patch, found_space, required_required)
```

### PATCH&#95;PRECHECK&#95;FAILED&#95;PREREQUISITE&#95;MISSING

The patch precheck stage failed: prerequisite patches are missing.

_Signature:_

```
PATCH_PRECHECK_FAILED_PREREQUISITE_MISSING(patch, prerequisite_patch_uuid_list)
```

### PATCH&#95;PRECHECK&#95;FAILED&#95;UNKNOWN&#95;ERROR

The patch precheck stage failed with an unknown error.  See attached info for more details.

_Signature:_

```
PATCH_PRECHECK_FAILED_UNKNOWN_ERROR(patch, info)
```

### PATCH&#95;PRECHECK&#95;FAILED&#95;VM&#95;RUNNING

The patch precheck stage failed: there are one or more VMs still running on the server.  All VMs must be suspended before the patch can be applied.

_Signature:_

```
PATCH_PRECHECK_FAILED_VM_RUNNING(patch)
```

### PATCH&#95;PRECHECK&#95;FAILED&#95;WRONG&#95;SERVER&#95;BUILD

The patch precheck stage failed: the server is of an incorrect build.

_Signature:_

```
PATCH_PRECHECK_FAILED_WRONG_SERVER_BUILD(patch, found_build, required_build)
```

### PATCH&#95;PRECHECK&#95;FAILED&#95;WRONG&#95;SERVER&#95;VERSION

The patch precheck stage failed: the server is of an incorrect version.

_Signature:_

```
PATCH_PRECHECK_FAILED_WRONG_SERVER_VERSION(patch, found_version, required_version)
```

### PBD&#95;EXISTS

A PBD already exists connecting the SR to the host

_Signature:_

```
PBD_EXISTS(sr, host, pbd)
```

### PERMISSION&#95;DENIED

Caller not allowed to perform this operation.

_Signature:_

```
PERMISSION_DENIED(message)
```

### PGPU&#95;INSUFFICIENT&#95;CAPACITY&#95;FOR&#95;VGPU

There is insufficient capacity on this PGPU to run the VGPU.

_Signature:_

```
PGPU_INSUFFICIENT_CAPACITY_FOR_VGPU(pgpu, vgpu_type)
```

### PGPU&#95;IN&#95;USE&#95;BY&#95;VM

This PGPU is currently in use by running VMs.

_Signature:_

```
PGPU_IN_USE_BY_VM(VMs)
```

### PGPU&#95;NOT&#95;COMPATIBLE&#95;WITH&#95;GPU&#95;GROUP

PGPU type not compatible with destination group.

_Signature:_

```
PGPU_NOT_COMPATIBLE_WITH_GPU_GROUP(type, group_types)
```

### PIF&#95;ALLOWS&#95;UNPLUG

The operation you requested cannot be performed because the specified PIF allows unplug.

_Signature:_

```
PIF_ALLOWS_UNPLUG(PIF)
```

### PIF&#95;ALREADY&#95;BONDED

This operation cannot be performed because the pif is bonded.

_Signature:_

```
PIF_ALREADY_BONDED(PIF)
```

### PIF&#95;BOND&#95;MORE&#95;THAN&#95;ONE&#95;IP

Only one PIF on a bond is allowed to have an IP configuration.

No parameters.

### PIF&#95;BOND&#95;NEEDS&#95;MORE&#95;MEMBERS

A bond must consist of at least two member interfaces

No parameters.

### PIF&#95;CANNOT&#95;BOND&#95;CROSS&#95;HOST

You cannot bond interfaces across different hosts.

No parameters.

### PIF&#95;CONFIGURATION&#95;ERROR

An unknown error occurred while attempting to configure an interface.

_Signature:_

```
PIF_CONFIGURATION_ERROR(PIF, msg)
```

### PIF&#95;DEVICE&#95;NOT&#95;FOUND

The specified device was not found.

No parameters.

### PIF&#95;DOES&#95;NOT&#95;ALLOW&#95;UNPLUG

The operation you requested cannot be performed because the specified PIF does not allow unplug.

_Signature:_

```
PIF_DOES_NOT_ALLOW_UNPLUG(PIF)
```

### PIF&#95;HAS&#95;FCOE&#95;SR&#95;IN&#95;USE

The operation you requested cannot be performed because the specified PIF has FCoE SR in use.

_Signature:_

```
PIF_HAS_FCOE_SR_IN_USE(PIF, SR)
```

### PIF&#95;HAS&#95;NO&#95;NETWORK&#95;CONFIGURATION

PIF has no IP configuration &#40;mode currently set to 'none'&#41;

_Signature:_

```
PIF_HAS_NO_NETWORK_CONFIGURATION(PIF)
```

### PIF&#95;HAS&#95;NO&#95;V6&#95;NETWORK&#95;CONFIGURATION

PIF has no IPv6 configuration &#40;mode currently set to 'none'&#41;

_Signature:_

```
PIF_HAS_NO_V6_NETWORK_CONFIGURATION(PIF)
```

### PIF&#95;INCOMPATIBLE&#95;PRIMARY&#95;ADDRESS&#95;TYPE

The primary address types are not compatible

_Signature:_

```
PIF_INCOMPATIBLE_PRIMARY_ADDRESS_TYPE(PIF)
```

### PIF&#95;IS&#95;MANAGEMENT&#95;INTERFACE

The operation you requested cannot be performed because the specified PIF is the management interface.

_Signature:_

```
PIF_IS_MANAGEMENT_INTERFACE(PIF)
```

### PIF&#95;IS&#95;NOT&#95;PHYSICAL

You tried to perform an operation which is only available on physical PIF

_Signature:_

```
PIF_IS_NOT_PHYSICAL(PIF)
```

### PIF&#95;IS&#95;NOT&#95;SRIOV&#95;CAPABLE

The selected PIF is not capable of network SR&#45;IOV

_Signature:_

```
PIF_IS_NOT_SRIOV_CAPABLE(PIF)
```

### PIF&#95;IS&#95;PHYSICAL

You tried to destroy a PIF, but it represents an aspect of the physical host configuration, and so cannot be destroyed.  The parameter echoes the PIF handle you gave.

_Signature:_

```
PIF_IS_PHYSICAL(PIF)
```

### PIF&#95;IS&#95;SRIOV&#95;LOGICAL

You tried to create a bond on top of a network SR&#45;IOV logical PIF &#45; use the underlying physical PIF instead

_Signature:_

```
PIF_IS_SRIOV_LOGICAL(PIF)
```

### PIF&#95;IS&#95;VLAN

You tried to create a VLAN on top of another VLAN &#45; use the underlying physical PIF/bond instead

_Signature:_

```
PIF_IS_VLAN(PIF)
```

### PIF&#95;NOT&#95;ATTACHED&#95;TO&#95;HOST

Cluster&#95;host creation failed as the PIF provided is not attached to the host.

_Signature:_

```
PIF_NOT_ATTACHED_TO_HOST(pif, host)
```

### PIF&#95;NOT&#95;PRESENT

This host has no PIF on the given network.

_Signature:_

```
PIF_NOT_PRESENT(host, network)
```

### PIF&#95;SRIOV&#95;STILL&#95;EXISTS

The PIF is still related with a network SR&#45;IOV

_Signature:_

```
PIF_SRIOV_STILL_EXISTS(PIF)
```

### PIF&#95;TUNNEL&#95;STILL&#95;EXISTS

Operation cannot proceed while a tunnel exists on this interface.

_Signature:_

```
PIF_TUNNEL_STILL_EXISTS(PIF)
```

### PIF&#95;UNMANAGED

The operation you requested cannot be performed because the specified PIF is not managed by xapi.

_Signature:_

```
PIF_UNMANAGED(PIF)
```

### PIF&#95;VLAN&#95;EXISTS

You tried to create a PIF, but it already exists.

_Signature:_

```
PIF_VLAN_EXISTS(PIF)
```

### PIF&#95;VLAN&#95;STILL&#95;EXISTS

Operation cannot proceed while a VLAN exists on this interface.

_Signature:_

```
PIF_VLAN_STILL_EXISTS(PIF)
```

### POOL&#95;AUTH&#95;ALREADY&#95;ENABLED

External authentication in this pool is already enabled for at least one host.

_Signature:_

```
POOL_AUTH_ALREADY_ENABLED(host)
```

### POOL&#95;AUTH&#95;DISABLE&#95;FAILED

The pool failed to disable the external authentication of at least one host.

_Signature:_

```
POOL_AUTH_DISABLE_FAILED(host, message)
```

### POOL&#95;AUTH&#95;DISABLE&#95;FAILED&#95;INVALID&#95;ACCOUNT

External authentication has been disabled with errors: Some AD machine accounts were not disabled on the AD server due to invalid account.

_Signature:_

```
POOL_AUTH_DISABLE_FAILED_INVALID_ACCOUNT(host, message)
```

### POOL&#95;AUTH&#95;DISABLE&#95;FAILED&#95;PERMISSION&#95;DENIED

External authentication has been disabled with errors: Your AD machine account was not disabled on the AD server as permission was denied.

_Signature:_

```
POOL_AUTH_DISABLE_FAILED_PERMISSION_DENIED(host, message)
```

### POOL&#95;AUTH&#95;DISABLE&#95;FAILED&#95;WRONG&#95;CREDENTIALS

External authentication has been disabled with errors: Some AD machine accounts were not disabled on the AD server due to invalid credentials.

_Signature:_

```
POOL_AUTH_DISABLE_FAILED_WRONG_CREDENTIALS(host, message)
```

### POOL&#95;AUTH&#95;ENABLE&#95;FAILED

The pool failed to enable external authentication.

_Signature:_

```
POOL_AUTH_ENABLE_FAILED(host, message)
```

### POOL&#95;AUTH&#95;ENABLE&#95;FAILED&#95;DOMAIN&#95;LOOKUP&#95;FAILED

The pool failed to enable external authentication.

_Signature:_

```
POOL_AUTH_ENABLE_FAILED_DOMAIN_LOOKUP_FAILED(host, message)
```

### POOL&#95;AUTH&#95;ENABLE&#95;FAILED&#95;DUPLICATE&#95;HOSTNAME

The pool failed to enable external authentication.

_Signature:_

```
POOL_AUTH_ENABLE_FAILED_DUPLICATE_HOSTNAME(host, message)
```

### POOL&#95;AUTH&#95;ENABLE&#95;FAILED&#95;INVALID&#95;ACCOUNT

The pool failed to enable external authentication.

_Signature:_

```
POOL_AUTH_ENABLE_FAILED_INVALID_ACCOUNT(host, message)
```

### POOL&#95;AUTH&#95;ENABLE&#95;FAILED&#95;INVALID&#95;OU

The pool failed to enable external authentication.

_Signature:_

```
POOL_AUTH_ENABLE_FAILED_INVALID_OU(host, message)
```

### POOL&#95;AUTH&#95;ENABLE&#95;FAILED&#95;PERMISSION&#95;DENIED

The pool failed to enable external authentication.

_Signature:_

```
POOL_AUTH_ENABLE_FAILED_PERMISSION_DENIED(host, message)
```

### POOL&#95;AUTH&#95;ENABLE&#95;FAILED&#95;UNAVAILABLE

The pool failed to enable external authentication.

_Signature:_

```
POOL_AUTH_ENABLE_FAILED_UNAVAILABLE(host, message)
```

### POOL&#95;AUTH&#95;ENABLE&#95;FAILED&#95;WRONG&#95;CREDENTIALS

The pool failed to enable external authentication.

_Signature:_

```
POOL_AUTH_ENABLE_FAILED_WRONG_CREDENTIALS(host, message)
```

### POOL&#95;JOINING&#95;EXTERNAL&#95;AUTH&#95;MISMATCH

Cannot join pool whose external authentication configuration is different.

No parameters.

### POOL&#95;JOINING&#95;HOST&#95;HAS&#95;BONDS

The host joining the pool must not have any bonds.

No parameters.

### POOL&#95;JOINING&#95;HOST&#95;HAS&#95;NETWORK&#95;SRIOVS

The host joining the pool must not have any network SR&#45;IOVs.

No parameters.

### POOL&#95;JOINING&#95;HOST&#95;HAS&#95;NON&#95;MANAGEMENT&#95;VLANS

The host joining the pool must not have any non&#45;management vlans.

No parameters.

### POOL&#95;JOINING&#95;HOST&#95;HAS&#95;TUNNELS

The host joining the pool must not have any tunnels.

No parameters.

### POOL&#95;JOINING&#95;HOST&#95;MANAGEMENT&#95;VLAN&#95;DOES&#95;NOT&#95;MATCH

The host joining the pool must have the same management vlan.

_Signature:_

```
POOL_JOINING_HOST_MANAGEMENT_VLAN_DOES_NOT_MATCH(local, remote)
```

### POOL&#95;JOINING&#95;HOST&#95;MUST&#95;HAVE&#95;PHYSICAL&#95;MANAGEMENT&#95;NIC

The host joining the pool must have a physical management NIC &#40;i.e. the management NIC must not be on a VLAN or bonded PIF&#41;.

No parameters.

### POOL&#95;JOINING&#95;HOST&#95;MUST&#95;HAVE&#95;SAME&#95;API&#95;VERSION

The host joining the pool must have the same API version as the pool master.

_Signature:_

```
POOL_JOINING_HOST_MUST_HAVE_SAME_API_VERSION(host_api_version, master_api_version)
```

### POOL&#95;JOINING&#95;HOST&#95;MUST&#95;HAVE&#95;SAME&#95;DB&#95;SCHEMA

The host joining the pool must have the same database schema as the pool master.

_Signature:_

```
POOL_JOINING_HOST_MUST_HAVE_SAME_DB_SCHEMA(host_db_schema, master_db_schema)
```

### POOL&#95;JOINING&#95;HOST&#95;MUST&#95;HAVE&#95;SAME&#95;PRODUCT&#95;VERSION

The host joining the pool must have the same product version as the pool master.

No parameters.

### POOL&#95;JOINING&#95;HOST&#95;MUST&#95;ONLY&#95;HAVE&#95;PHYSICAL&#95;PIFS

The host joining the pool must not have any bonds, VLANs or tunnels.

No parameters.

### PROVISION&#95;FAILED&#95;OUT&#95;OF&#95;SPACE

The provision call failed because it ran out of space.

No parameters.

### PROVISION&#95;ONLY&#95;ALLOWED&#95;ON&#95;TEMPLATE

The provision call can only be invoked on templates, not regular VMs.

No parameters.

### PUSB&#95;VDI&#95;CONFLICT

The VDI corresponding to this PUSB has existing VBDs.

_Signature:_

```
PUSB_VDI_CONFLICT(PUSB, VDI)
```

### PVS&#95;CACHE&#95;STORAGE&#95;ALREADY&#95;PRESENT

The PVS site already has cache storage configured for the host.

_Signature:_

```
PVS_CACHE_STORAGE_ALREADY_PRESENT(site, host)
```

### PVS&#95;CACHE&#95;STORAGE&#95;IS&#95;IN&#95;USE

The PVS cache storage is in use by the site and cannot be removed.

_Signature:_

```
PVS_CACHE_STORAGE_IS_IN_USE(PVS_cache_storage)
```

### PVS&#95;PROXY&#95;ALREADY&#95;PRESENT

The VIF is already associated with a PVS proxy

_Signature:_

```
PVS_PROXY_ALREADY_PRESENT(proxies)
```

### PVS&#95;SERVER&#95;ADDRESS&#95;IN&#95;USE

The address specified is already in use by an existing PVS&#95;server object

_Signature:_

```
PVS_SERVER_ADDRESS_IN_USE(address)
```

### PVS&#95;SITE&#95;CONTAINS&#95;RUNNING&#95;PROXIES

The PVS site contains running proxies.

_Signature:_

```
PVS_SITE_CONTAINS_RUNNING_PROXIES(proxies)
```

### PVS&#95;SITE&#95;CONTAINS&#95;SERVERS

The PVS site contains servers and cannot be forgotten.

_Signature:_

```
PVS_SITE_CONTAINS_SERVERS(servers)
```

### RBAC&#95;PERMISSION&#95;DENIED

RBAC permission denied.

_Signature:_

```
RBAC_PERMISSION_DENIED(permission, message)
```

### REDO&#95;LOG&#95;IS&#95;ENABLED

The operation could not be performed because a redo log is enabled on the Pool.

No parameters.

### REQUIRED&#95;PIF&#95;IS&#95;UNPLUGGED

The operation you requested cannot be performed because the specified PIF is currently unplugged.

_Signature:_

```
REQUIRED_PIF_IS_UNPLUGGED(PIF)
```

### RESTORE&#95;INCOMPATIBLE&#95;VERSION

The restore could not be performed because this backup has been created by a different &#40;incompatible&#41; product version

No parameters.

### RESTORE&#95;SCRIPT&#95;FAILED

The restore could not be performed because the restore script failed.  Is the file corrupt?

_Signature:_

```
RESTORE_SCRIPT_FAILED(log)
```

### RESTORE&#95;TARGET&#95;MGMT&#95;IF&#95;NOT&#95;IN&#95;BACKUP

The restore could not be performed because the host's current management interface is not in the backup. The interfaces mentioned in the backup are:

No parameters.

### RESTORE&#95;TARGET&#95;MISSING&#95;DEVICE

The restore could not be performed because a network interface is missing

_Signature:_

```
RESTORE_TARGET_MISSING_DEVICE(device)
```

### ROLE&#95;ALREADY&#95;EXISTS

Role already exists.

No parameters.

### ROLE&#95;NOT&#95;FOUND

Role cannot be found.

No parameters.

### SESSION&#95;AUTHENTICATION&#95;FAILED

The credentials given by the user are incorrect, so access has been denied, and you have not been issued a session handle.

No parameters.

### SESSION&#95;INVALID

You gave an invalid session reference.  It may have been invalidated by a server restart, or timed out.  You should get a new session handle, using one of the session.login&#95; calls.  This error does not invalidate the current connection.  The handle parameter echoes the bad value given.

_Signature:_

```
SESSION_INVALID(handle)
```

### SESSION&#95;NOT&#95;REGISTERED

This session is not registered to receive events.  You must call event.register before event.next.  The session handle you are using is echoed.

_Signature:_

```
SESSION_NOT_REGISTERED(handle)
```

### SLAVE&#95;REQUIRES&#95;MANAGEMENT&#95;INTERFACE

The management interface on a slave cannot be disabled because the slave would enter emergency mode.

No parameters.

### SM&#95;PLUGIN&#95;COMMUNICATION&#95;FAILURE

The SM plugin did not respond to a query.

_Signature:_

```
SM_PLUGIN_COMMUNICATION_FAILURE(sm)
```

### SR&#95;ATTACH&#95;FAILED

Attaching this SR failed.

_Signature:_

```
SR_ATTACH_FAILED(sr)
```

### SR&#95;BACKEND&#95;FAILURE

There was an SR backend failure.

_Signature:_

```
SR_BACKEND_FAILURE(status, stdout, stderr)
```

### SR&#95;DEVICE&#95;IN&#95;USE

The SR operation cannot be performed because a device underlying the SR is in use by the host.

No parameters.

### SR&#95;DOES&#95;NOT&#95;SUPPORT&#95;MIGRATION

You attempted to migrate a VDI to or from an SR which doesn't support migration

_Signature:_

```
SR_DOES_NOT_SUPPORT_MIGRATION(sr)
```

### SR&#95;FULL

The SR is full. Requested new size exceeds the maximum size

_Signature:_

```
SR_FULL(requested, maximum)
```

### SR&#95;HAS&#95;MULTIPLE&#95;PBDS

The SR.shared flag cannot be set to false while the SR remains connected to multiple hosts

_Signature:_

```
SR_HAS_MULTIPLE_PBDS(PBD)
```

### SR&#95;HAS&#95;NO&#95;PBDS

The SR has no attached PBDs

_Signature:_

```
SR_HAS_NO_PBDS(sr)
```

### SR&#95;HAS&#95;PBD

The SR is still connected to a host via a PBD. It cannot be destroyed or forgotten.

_Signature:_

```
SR_HAS_PBD(sr)
```

### SR&#95;INDESTRUCTIBLE

The SR could not be destroyed, as the 'indestructible' flag was set on it.

_Signature:_

```
SR_INDESTRUCTIBLE(sr)
```

### SR&#95;IS&#95;CACHE&#95;SR

The SR is currently being used as a local cache SR.

_Signature:_

```
SR_IS_CACHE_SR(host)
```

### SR&#95;NOT&#95;ATTACHED

The SR is not attached.

_Signature:_

```
SR_NOT_ATTACHED(sr)
```

### SR&#95;NOT&#95;EMPTY

The SR operation cannot be performed because the SR is not empty.

No parameters.

### SR&#95;NOT&#95;SHARABLE

The PBD could not be plugged because the SR is in use by another host and is not marked as sharable.

_Signature:_

```
SR_NOT_SHARABLE(sr, host)
```

### SR&#95;OPERATION&#95;NOT&#95;SUPPORTED

The SR backend does not support the operation &#40;check the SR's allowed operations&#41;

_Signature:_

```
SR_OPERATION_NOT_SUPPORTED(sr)
```

### SR&#95;REQUIRES&#95;UPGRADE

The operation cannot be performed until the SR has been upgraded

_Signature:_

```
SR_REQUIRES_UPGRADE(SR)
```

### SR&#95;SOURCE&#95;SPACE&#95;INSUFFICIENT

The source SR does not have sufficient temporary space available to proceed the operation.

_Signature:_

```
SR_SOURCE_SPACE_INSUFFICIENT(sr)
```

### SR&#95;UNKNOWN&#95;DRIVER

The SR could not be connected because the driver was not recognised.

_Signature:_

```
SR_UNKNOWN_DRIVER(driver)
```

### SR&#95;UUID&#95;EXISTS

An SR with that uuid already exists.

_Signature:_

```
SR_UUID_EXISTS(uuid)
```

### SR&#95;VDI&#95;LOCKING&#95;FAILED

The operation could not proceed because necessary VDIs were already locked at the storage level.

No parameters.

### SSL&#95;VERIFY&#95;ERROR

The remote system's SSL certificate failed to verify against our certificate library.

_Signature:_

```
SSL_VERIFY_ERROR(reason)
```

### SUBJECT&#95;ALREADY&#95;EXISTS

Subject already exists.

No parameters.

### SUBJECT&#95;CANNOT&#95;BE&#95;RESOLVED

Subject cannot be resolved by the external directory service.

No parameters.

### SUSPEND&#95;IMAGE&#95;NOT&#95;ACCESSIBLE

The suspend image of a checkpoint is not accessible from the host on which the VM is running

_Signature:_

```
SUSPEND_IMAGE_NOT_ACCESSIBLE(vdi)
```

### SYSTEM&#95;STATUS&#95;MUST&#95;USE&#95;TAR&#95;ON&#95;OEM

You must use tar output to retrieve system status from an OEM host.

No parameters.

### SYSTEM&#95;STATUS&#95;RETRIEVAL&#95;FAILED

Retrieving system status from the host failed.  A diagnostic reason suitable for support organisations is also returned.

_Signature:_

```
SYSTEM_STATUS_RETRIEVAL_FAILED(reason)
```

### TASK&#95;CANCELLED

The request was asynchronously cancelled.

_Signature:_

```
TASK_CANCELLED(task)
```

### TLS&#95;CONNECTION&#95;FAILED

Cannot contact the other host using TLS on the specified address and port

_Signature:_

```
TLS_CONNECTION_FAILED(address, port)
```

### TOO&#95;BUSY

The request was rejected because the server is too busy.

No parameters.

### TOO&#95;MANY&#95;PENDING&#95;TASKS

The request was rejected because there are too many pending tasks on the server.

No parameters.

### TOO&#95;MANY&#95;STORAGE&#95;MIGRATES

You reached the maximal number of concurrently migrating VMs.

_Signature:_

```
TOO_MANY_STORAGE_MIGRATES(number)
```

### TOO&#95;MANY&#95;VUSBS

The VM has too many VUSBs.

_Signature:_

```
TOO_MANY_VUSBS(number)
```

### TRANSPORT&#95;PIF&#95;NOT&#95;CONFIGURED

The tunnel transport PIF has no IP configuration set.

_Signature:_

```
TRANSPORT_PIF_NOT_CONFIGURED(PIF)
```

### UNIMPLEMENTED&#95;IN&#95;SM&#95;BACKEND

You have attempted a function which is not implemented

_Signature:_

```
UNIMPLEMENTED_IN_SM_BACKEND(message)
```

### UNKNOWN&#95;BOOTLOADER

The requested bootloader is unknown

_Signature:_

```
UNKNOWN_BOOTLOADER(vm, bootloader)
```

### UPDATE&#95;ALREADY&#95;APPLIED

This update has already been applied.

_Signature:_

```
UPDATE_ALREADY_APPLIED(update)
```

### UPDATE&#95;ALREADY&#95;APPLIED&#95;IN&#95;POOL

This update has already been applied to all hosts in the pool.

_Signature:_

```
UPDATE_ALREADY_APPLIED_IN_POOL(update)
```

### UPDATE&#95;ALREADY&#95;EXISTS

The uploaded update already exists

_Signature:_

```
UPDATE_ALREADY_EXISTS(uuid)
```

### UPDATE&#95;APPLY&#95;FAILED

The update failed to apply. Please see attached output.

_Signature:_

```
UPDATE_APPLY_FAILED(output)
```

### UPDATE&#95;IS&#95;APPLIED

The specified update has been applied and cannot be destroyed.

No parameters.

### UPDATE&#95;POOL&#95;APPLY&#95;FAILED

The update cannot be applied for the following host&#40;s&#41;.

_Signature:_

```
UPDATE_POOL_APPLY_FAILED(hosts)
```

### UPDATE&#95;PRECHECK&#95;FAILED&#95;CONFLICT&#95;PRESENT

The update precheck stage failed: conflicting update&#40;s&#41; are present.

_Signature:_

```
UPDATE_PRECHECK_FAILED_CONFLICT_PRESENT(update, conflict_update)
```

### UPDATE&#95;PRECHECK&#95;FAILED&#95;GPGKEY&#95;NOT&#95;IMPORTED

The update precheck stage failed: RPM package validation requires a GPG key that is not present on the host.

_Signature:_

```
UPDATE_PRECHECK_FAILED_GPGKEY_NOT_IMPORTED(update)
```

### UPDATE&#95;PRECHECK&#95;FAILED&#95;OUT&#95;OF&#95;SPACE

The update precheck stage failed: the server does not have enough space.

_Signature:_

```
UPDATE_PRECHECK_FAILED_OUT_OF_SPACE(update, available_space, required_space )
```

### UPDATE&#95;PRECHECK&#95;FAILED&#95;PREREQUISITE&#95;MISSING

The update precheck stage failed: prerequisite update&#40;s&#41; are missing.

_Signature:_

```
UPDATE_PRECHECK_FAILED_PREREQUISITE_MISSING(update, prerequisite_update)
```

### UPDATE&#95;PRECHECK&#95;FAILED&#95;UNKNOWN&#95;ERROR

The update precheck stage failed with an unknown error.

_Signature:_

```
UPDATE_PRECHECK_FAILED_UNKNOWN_ERROR(update, info)
```

### UPDATE&#95;PRECHECK&#95;FAILED&#95;WRONG&#95;SERVER&#95;VERSION

The update precheck stage failed: the server is of an incorrect version.

_Signature:_

```
UPDATE_PRECHECK_FAILED_WRONG_SERVER_VERSION(update, installed_version, required_version )
```

### USB&#95;ALREADY&#95;ATTACHED

The USB device is currently attached to a VM.

_Signature:_

```
USB_ALREADY_ATTACHED(PUSB, VM)
```

### USB&#95;GROUP&#95;CONFLICT

USB&#95;groups are currently restricted to contain no more than one VUSB.

_Signature:_

```
USB_GROUP_CONFLICT(USB_group)
```

### USB&#95;GROUP&#95;CONTAINS&#95;NO&#95;PUSBS

The USB group does not contain any PUSBs.

_Signature:_

```
USB_GROUP_CONTAINS_NO_PUSBS(usb_group)
```

### USB&#95;GROUP&#95;CONTAINS&#95;PUSB

The USB group contains active PUSBs and cannot be deleted.

_Signature:_

```
USB_GROUP_CONTAINS_PUSB(pusbs)
```

### USB&#95;GROUP&#95;CONTAINS&#95;VUSB

The USB group contains active VUSBs and cannot be deleted.

_Signature:_

```
USB_GROUP_CONTAINS_VUSB(vusbs)
```

### USER&#95;IS&#95;NOT&#95;LOCAL&#95;SUPERUSER

Only the local superuser can execute this operation

_Signature:_

```
USER_IS_NOT_LOCAL_SUPERUSER(msg)
```

### UUID&#95;INVALID

The uuid you supplied was invalid.

_Signature:_

```
UUID_INVALID(type, uuid)
```

### V6D&#95;FAILURE

There was a problem with the license daemon &#40;v6d&#41;.

No parameters.

### VALUE&#95;NOT&#95;SUPPORTED

You attempted to set a value that is not supported by this implementation.  The fully&#45;qualified field name and the value that you tried to set are returned.  Also returned is a developer&#45;only diagnostic reason.

_Signature:_

```
VALUE_NOT_SUPPORTED(field, value, reason)
```

### VBD&#95;CDS&#95;MUST&#95;BE&#95;READONLY

Read/write CDs are not supported

No parameters.

### VBD&#95;IS&#95;EMPTY

Operation could not be performed because the drive is empty

_Signature:_

```
VBD_IS_EMPTY(vbd)
```

### VBD&#95;NOT&#95;EMPTY

Operation could not be performed because the drive is not empty

_Signature:_

```
VBD_NOT_EMPTY(vbd)
```

### VBD&#95;NOT&#95;REMOVABLE&#95;MEDIA

Media could not be ejected because it is not removable

_Signature:_

```
VBD_NOT_REMOVABLE_MEDIA(vbd)
```

### VBD&#95;NOT&#95;UNPLUGGABLE

Drive could not be hot&#45;unplugged because it is not marked as unpluggable

_Signature:_

```
VBD_NOT_UNPLUGGABLE(vbd)
```

### VBD&#95;TRAY&#95;LOCKED

This VM has locked the DVD drive tray, so the disk cannot be ejected

_Signature:_

```
VBD_TRAY_LOCKED(vbd)
```

### VDI&#95;CBT&#95;ENABLED

The requested operation is not allowed for VDIs with CBT enabled or VMs having such VDIs, and CBT is enabled for the specified VDI.

_Signature:_

```
VDI_CBT_ENABLED(vdi)
```

### VDI&#95;CONTAINS&#95;METADATA&#95;OF&#95;THIS&#95;POOL

The VDI could not be opened for metadata recovery as it contains the current pool's metadata.

_Signature:_

```
VDI_CONTAINS_METADATA_OF_THIS_POOL(vdi, pool)
```

### VDI&#95;COPY&#95;FAILED

The VDI copy action has failed

No parameters.

### VDI&#95;HAS&#95;RRDS

The operation cannot be performed because this VDI has rrd stats

_Signature:_

```
VDI_HAS_RRDS(vdi)
```

### VDI&#95;INCOMPATIBLE&#95;TYPE

This operation cannot be performed because the specified VDI is of an incompatible type &#40;eg: an HA statefile cannot be attached to a guest&#41;

_Signature:_

```
VDI_INCOMPATIBLE_TYPE(vdi, type)
```

### VDI&#95;IN&#95;USE

This operation cannot be performed because this VDI is in use by some other operation

_Signature:_

```
VDI_IN_USE(vdi, operation)
```

### VDI&#95;IS&#95;A&#95;PHYSICAL&#95;DEVICE

The operation cannot be performed on physical device

_Signature:_

```
VDI_IS_A_PHYSICAL_DEVICE(vdi)
```

### VDI&#95;IS&#95;NOT&#95;ISO

This operation can only be performed on CD VDIs &#40;iso files or CDROM drives&#41;

_Signature:_

```
VDI_IS_NOT_ISO(vdi, type)
```

### VDI&#95;LOCATION&#95;MISSING

This operation cannot be performed because the specified VDI could not be found in the specified SR

_Signature:_

```
VDI_LOCATION_MISSING(sr, location)
```

### VDI&#95;MISSING

This operation cannot be performed because the specified VDI could not be found on the storage substrate

_Signature:_

```
VDI_MISSING(sr, vdi)
```

### VDI&#95;NEEDS&#95;VM&#95;FOR&#95;MIGRATE

You attempted to migrate a VDI which is not attached to a running VM.

_Signature:_

```
VDI_NEEDS_VM_FOR_MIGRATE(vdi)
```

### VDI&#95;NOT&#95;AVAILABLE

This operation cannot be performed because this VDI could not be properly attached to the VM.

_Signature:_

```
VDI_NOT_AVAILABLE(vdi)
```

### VDI&#95;NOT&#95;IN&#95;MAP

This VDI was not mapped to a destination SR in VM.migrate&#95;send operation

_Signature:_

```
VDI_NOT_IN_MAP(vdi)
```

### VDI&#95;NOT&#95;MANAGED

This operation cannot be performed because the system does not manage this VDI

_Signature:_

```
VDI_NOT_MANAGED(vdi)
```

### VDI&#95;NOT&#95;SPARSE

The VDI is not stored using a sparse format. It is not possible to query and manipulate only the changed blocks &#40;or 'block differences' or 'disk deltas'&#41; between two VDIs. Please select a VDI which uses a sparse&#45;aware technology such as VHD.

_Signature:_

```
VDI_NOT_SPARSE(vdi)
```

### VDI&#95;NO&#95;CBT&#95;METADATA

The requested operation is not allowed because the specified VDI does not have changed block tracking metadata.

_Signature:_

```
VDI_NO_CBT_METADATA(vdi)
```

### VDI&#95;ON&#95;BOOT&#95;MODE&#95;INCOMPATIBLE&#95;WITH&#95;OPERATION

This operation is not permitted on VDIs in the 'on&#45;boot=reset' mode, or on VMs having such VDIs.

No parameters.

### VDI&#95;READONLY

The operation required write access but this VDI is read&#45;only

_Signature:_

```
VDI_READONLY(vdi)
```

### VDI&#95;TOO&#95;SMALL

The VDI is too small. Please resize it to at least the minimum size.

_Signature:_

```
VDI_TOO_SMALL(vdi, minimum size)
```

### VGPU&#95;DESTINATION&#95;INCOMPATIBLE

The VGPU is not compatible with any PGPU in the destination.

_Signature:_

```
VGPU_DESTINATION_INCOMPATIBLE(reason, vgpu, host)
```

### VGPU&#95;TYPE&#95;NOT&#95;COMPATIBLE&#95;WITH&#95;RUNNING&#95;TYPE

VGPU type is not compatible with one or more of the VGPU types currently running on this PGPU

_Signature:_

```
VGPU_TYPE_NOT_COMPATIBLE_WITH_RUNNING_TYPE(pgpu, type, running_type)
```

### VGPU&#95;TYPE&#95;NOT&#95;ENABLED

VGPU type is not one of the PGPU's enabled types.

_Signature:_

```
VGPU_TYPE_NOT_ENABLED(type, enabled_types)
```

### VGPU&#95;TYPE&#95;NOT&#95;SUPPORTED

VGPU type is not one of the PGPU's supported types.

_Signature:_

```
VGPU_TYPE_NOT_SUPPORTED(type, supported_types)
```

### VIF&#95;IN&#95;USE

Network has active VIFs

_Signature:_

```
VIF_IN_USE(network, VIF)
```

### VIF&#95;NOT&#95;IN&#95;MAP

This VIF was not mapped to a destination Network in VM.migrate&#95;send operation

_Signature:_

```
VIF_NOT_IN_MAP(vif)
```

### VLAN&#95;IN&#95;USE

Operation cannot be performed because this VLAN is already in use. Please check your network configuration.

_Signature:_

```
VLAN_IN_USE(device, vlan)
```

### VLAN&#95;TAG&#95;INVALID

You tried to create a VLAN, but the tag you gave was invalid &#45;&#45; it must be between 0 and 4094.  The parameter echoes the VLAN tag you gave.

_Signature:_

```
VLAN_TAG_INVALID(VLAN)
```

### VMPP&#95;ARCHIVE&#95;MORE&#95;FREQUENT&#95;THAN&#95;BACKUP

Archive more frequent than backup.

No parameters.

### VMPP&#95;HAS&#95;VM

There is at least one VM assigned to this protection policy.

No parameters.

### VMSS&#95;HAS&#95;VM

There is at least one VM assigned to snapshot schedule.

No parameters.

### VMS&#95;FAILED&#95;TO&#95;COOPERATE

The given VMs failed to release memory when instructed to do so

No parameters.

### VM&#95;ASSIGNED&#95;TO&#95;PROTECTION&#95;POLICY

This VM is assigned to a protection policy.

_Signature:_

```
VM_ASSIGNED_TO_PROTECTION_POLICY(vm, vmpp)
```

### VM&#95;ASSIGNED&#95;TO&#95;SNAPSHOT&#95;SCHEDULE

This VM is assigned to a snapshot schedule.

_Signature:_

```
VM_ASSIGNED_TO_SNAPSHOT_SCHEDULE(vm, vmss)
```

### VM&#95;ATTACHED&#95;TO&#95;MORE&#95;THAN&#95;ONE&#95;VDI&#95;WITH&#95;TIMEOFFSET&#95;MARKED&#95;AS&#95;RESET&#95;ON&#95;BOOT

You attempted to start a VM that's attached to more than one VDI with a timeoffset marked as reset&#45;on&#45;boot.

_Signature:_

```
VM_ATTACHED_TO_MORE_THAN_ONE_VDI_WITH_TIMEOFFSET_MARKED_AS_RESET_ON_BOOT(vm)
```

### VM&#95;BAD&#95;POWER&#95;STATE

You attempted an operation on a VM that was not in an appropriate power state at the time; for example, you attempted to start a VM that was already running.  The parameters returned are the VM's handle, and the expected and actual VM state at the time of the call.

_Signature:_

```
VM_BAD_POWER_STATE(vm, expected, actual)
```

### VM&#95;BIOS&#95;STRINGS&#95;ALREADY&#95;SET

The BIOS strings for this VM have already been set and cannot be changed.

No parameters.

### VM&#95;CALL&#95;PLUGIN&#95;RATE&#95;LIMIT

There is a minimal interval required between consecutive plugin calls made on the same VM, please wait before retry.

_Signature:_

```
VM_CALL_PLUGIN_RATE_LIMIT(VM, interval, wait)
```

### VM&#95;CANNOT&#95;DELETE&#95;DEFAULT&#95;TEMPLATE

You cannot delete the specified default template.

_Signature:_

```
VM_CANNOT_DELETE_DEFAULT_TEMPLATE(vm)
```

### VM&#95;CHECKPOINT&#95;RESUME&#95;FAILED

An error occured while restoring the memory image of the specified virtual machine

_Signature:_

```
VM_CHECKPOINT_RESUME_FAILED(vm)
```

### VM&#95;CHECKPOINT&#95;SUSPEND&#95;FAILED

An error occured while saving the memory image of the specified virtual machine

_Signature:_

```
VM_CHECKPOINT_SUSPEND_FAILED(vm)
```

### VM&#95;CRASHED

The VM crashed

_Signature:_

```
VM_CRASHED(vm)
```

### VM&#95;DUPLICATE&#95;VBD&#95;DEVICE

The specified VM has a duplicate VBD device and cannot be started.

_Signature:_

```
VM_DUPLICATE_VBD_DEVICE(vm, vbd, device)
```

### VM&#95;FAILED&#95;SHUTDOWN&#95;ACKNOWLEDGMENT

VM didn't acknowledge the need to shutdown.

_Signature:_

```
VM_FAILED_SHUTDOWN_ACKNOWLEDGMENT(vm)
```

### VM&#95;HALTED

The VM unexpectedly halted

_Signature:_

```
VM_HALTED(vm)
```

### VM&#95;HAS&#95;CHECKPOINT

You attempted to migrate a VM which has a checkpoint.

_Signature:_

```
VM_HAS_CHECKPOINT(vm)
```

### VM&#95;HAS&#95;NO&#95;SUSPEND&#95;VDI

VM cannot be resumed because it has no suspend VDI

_Signature:_

```
VM_HAS_NO_SUSPEND_VDI(vm)
```

### VM&#95;HAS&#95;PCI&#95;ATTACHED

This operation could not be performed, because the VM has one or more PCI devices passed through.

_Signature:_

```
VM_HAS_PCI_ATTACHED(vm)
```

### VM&#95;HAS&#95;SRIOV&#95;VIF

This operation could not be performed, because the VM has one or more SR&#45;IOV VIFs.

_Signature:_

```
VM_HAS_SRIOV_VIF(vm)
```

### VM&#95;HAS&#95;TOO&#95;MANY&#95;SNAPSHOTS

You attempted to migrate a VM with more than one snapshot.

_Signature:_

```
VM_HAS_TOO_MANY_SNAPSHOTS(vm)
```

### VM&#95;HAS&#95;VGPU

This operation could not be performed, because the VM has one or more virtual GPUs.

_Signature:_

```
VM_HAS_VGPU(vm)
```

### VM&#95;HAS&#95;VUSBS

The operation is not allowed when the VM has VUSBs.

_Signature:_

```
VM_HAS_VUSBS(VM)
```

### VM&#95;HOST&#95;INCOMPATIBLE&#95;VERSION

This VM operation cannot be performed on an older&#45;versioned host during an upgrade.

_Signature:_

```
VM_HOST_INCOMPATIBLE_VERSION(host, vm)
```

### VM&#95;HOST&#95;INCOMPATIBLE&#95;VERSION&#95;MIGRATE

You attempted to migrate a VM to a destination host which is older than the source host.

_Signature:_

```
VM_HOST_INCOMPATIBLE_VERSION_MIGRATE(host, vm)
```

### VM&#95;HOST&#95;INCOMPATIBLE&#95;VIRTUAL&#95;HARDWARE&#95;PLATFORM&#95;VERSION

You attempted to run a VM on a host that cannot provide the VM's required Virtual Hardware Platform version.

_Signature:_

```
VM_HOST_INCOMPATIBLE_VIRTUAL_HARDWARE_PLATFORM_VERSION(host, host_versions, vm, vm_version)
```

### VM&#95;HVM&#95;REQUIRED

HVM is required for this operation

_Signature:_

```
VM_HVM_REQUIRED(vm)
```

### VM&#95;INCOMPATIBLE&#95;WITH&#95;THIS&#95;HOST

The VM is incompatible with the CPU features of this host.

_Signature:_

```
VM_INCOMPATIBLE_WITH_THIS_HOST(vm, host, reason)
```

### VM&#95;IS&#95;IMMOBILE

The VM is configured in a way that prevents it from being mobile.

_Signature:_

```
VM_IS_IMMOBILE(VM)
```

### VM&#95;IS&#95;PART&#95;OF&#95;AN&#95;APPLIANCE

This operation is not allowed as the VM is part of an appliance.

_Signature:_

```
VM_IS_PART_OF_AN_APPLIANCE(vm, appliance)
```

### VM&#95;IS&#95;PROTECTED

This operation cannot be performed because the specified VM is protected by xHA

_Signature:_

```
VM_IS_PROTECTED(vm)
```

### VM&#95;IS&#95;TEMPLATE

The operation attempted is not valid for a template VM

_Signature:_

```
VM_IS_TEMPLATE(vm)
```

### VM&#95;IS&#95;USING&#95;NESTED&#95;VIRT

This operation is illegal because the VM is using nested virtualisation.

_Signature:_

```
VM_IS_USING_NESTED_VIRT(VM)
```

### VM&#95;LACKS&#95;FEATURE

You attempted an operation on a VM which lacks the feature.

_Signature:_

```
VM_LACKS_FEATURE(vm)
```

### VM&#95;LACKS&#95;FEATURE&#95;SHUTDOWN

You attempted an operation which needs the cooperative shutdown feature on a VM which lacks it.

_Signature:_

```
VM_LACKS_FEATURE_SHUTDOWN(vm)
```

### VM&#95;LACKS&#95;FEATURE&#95;STATIC&#95;IP&#95;SETTING

You attempted an operation which needs the VM static&#45;ip&#45;setting feature on a VM which lacks it.

_Signature:_

```
VM_LACKS_FEATURE_STATIC_IP_SETTING(vm)
```

### VM&#95;LACKS&#95;FEATURE&#95;SUSPEND

You attempted an operation which needs the VM cooperative suspend feature on a VM which lacks it.

_Signature:_

```
VM_LACKS_FEATURE_SUSPEND(vm)
```

### VM&#95;LACKS&#95;FEATURE&#95;VCPU&#95;HOTPLUG

You attempted an operation which needs the VM hotplug&#45;vcpu feature on a VM which lacks it.

_Signature:_

```
VM_LACKS_FEATURE_VCPU_HOTPLUG(vm)
```

### VM&#95;MEMORY&#95;SIZE&#95;TOO&#95;LOW

The specified VM has too little memory to be started.

_Signature:_

```
VM_MEMORY_SIZE_TOO_LOW(vm)
```

### VM&#95;MIGRATE&#95;CONTACT&#95;REMOTE&#95;SERVICE&#95;FAILED

Failed to contact service on the destination host.

No parameters.

### VM&#95;MIGRATE&#95;FAILED

An error occurred during the migration process.

_Signature:_

```
VM_MIGRATE_FAILED(vm, source, destination, msg)
```

### VM&#95;MISSING&#95;PV&#95;DRIVERS

You attempted an operation on a VM which requires PV drivers to be installed but the drivers were not detected.

_Signature:_

```
VM_MISSING_PV_DRIVERS(vm)
```

### VM&#95;NOT&#95;RESIDENT&#95;HERE

The specified VM is not currently resident on the specified host.

_Signature:_

```
VM_NOT_RESIDENT_HERE(vm, host)
```

### VM&#95;NO&#95;CRASHDUMP&#95;SR

This VM does not have a crashdump SR specified.

_Signature:_

```
VM_NO_CRASHDUMP_SR(vm)
```

### VM&#95;NO&#95;EMPTY&#95;CD&#95;VBD

The VM has no empty CD drive &#40;VBD&#41;.

_Signature:_

```
VM_NO_EMPTY_CD_VBD(vm)
```

### VM&#95;NO&#95;SUSPEND&#95;SR

This VM does not have a suspend SR specified.

_Signature:_

```
VM_NO_SUSPEND_SR(vm)
```

### VM&#95;NO&#95;VCPUS

You need at least 1 VCPU to start a VM

_Signature:_

```
VM_NO_VCPUS(vm)
```

### VM&#95;OLD&#95;PV&#95;DRIVERS

You attempted an operation on a VM which requires a more recent version of the PV drivers. Please upgrade your PV drivers.

_Signature:_

```
VM_OLD_PV_DRIVERS(vm, major, minor)
```

### VM&#95;PV&#95;DRIVERS&#95;IN&#95;USE

VM PV drivers still in use

_Signature:_

```
VM_PV_DRIVERS_IN_USE(vm)
```

### VM&#95;REBOOTED

The VM unexpectedly rebooted

_Signature:_

```
VM_REBOOTED(vm)
```

### VM&#95;REQUIRES&#95;GPU

You attempted to run a VM on a host which doesn't have a pGPU available in the GPU group needed by the VM. The VM has a vGPU attached to this GPU group.

_Signature:_

```
VM_REQUIRES_GPU(vm, GPU_group)
```

### VM&#95;REQUIRES&#95;IOMMU

You attempted to run a VM on a host which doesn't have I/O virtualization &#40;IOMMU/VT&#45;d&#41; enabled, which is needed by the VM.

_Signature:_

```
VM_REQUIRES_IOMMU(host)
```

### VM&#95;REQUIRES&#95;NETWORK

You attempted to run a VM on a host which doesn't have a PIF on a Network needed by the VM. The VM has at least one VIF attached to the Network.

_Signature:_

```
VM_REQUIRES_NETWORK(vm, network)
```

### VM&#95;REQUIRES&#95;SR

You attempted to run a VM on a host which doesn't have access to an SR needed by the VM. The VM has at least one VBD attached to a VDI in the SR.

_Signature:_

```
VM_REQUIRES_SR(vm, sr)
```

### VM&#95;REQUIRES&#95;VDI

VM cannot be started because it requires a VDI which cannot be attached

_Signature:_

```
VM_REQUIRES_VDI(vm, vdi)
```

### VM&#95;REQUIRES&#95;VGPU

You attempted to run a VM on a host on which the vGPU required by the VM cannot be allocated on any pGPUs in the GPU&#95;group needed by the VM.

_Signature:_

```
VM_REQUIRES_VGPU(vm, GPU_group, vGPU_type)
```

### VM&#95;REQUIRES&#95;VUSB

You attempted to run a VM on a host on which the VUSB required by the VM cannot be allocated on any PUSBs in the USB&#95;group needed by the VM.

_Signature:_

```
VM_REQUIRES_VUSB(vm, USB_group)
```

### VM&#95;REVERT&#95;FAILED

An error occured while reverting the specified virtual machine to the specified snapshot

_Signature:_

```
VM_REVERT_FAILED(vm, snapshot)
```

### VM&#95;SHUTDOWN&#95;TIMEOUT

VM failed to shutdown before the timeout expired

_Signature:_

```
VM_SHUTDOWN_TIMEOUT(vm, timeout)
```

### VM&#95;SNAPSHOT&#95;WITH&#95;QUIESCE&#95;FAILED

The quiesced&#45;snapshot operation failed for an unexpected reason

_Signature:_

```
VM_SNAPSHOT_WITH_QUIESCE_FAILED(vm)
```

### VM&#95;SNAPSHOT&#95;WITH&#95;QUIESCE&#95;NOT&#95;SUPPORTED

The VSS plug&#45;in is not installed on this virtual machine

_Signature:_

```
VM_SNAPSHOT_WITH_QUIESCE_NOT_SUPPORTED(vm, error)
```

### VM&#95;SNAPSHOT&#95;WITH&#95;QUIESCE&#95;PLUGIN&#95;DEOS&#95;NOT&#95;RESPOND

The VSS plug&#45;in cannot be contacted

_Signature:_

```
VM_SNAPSHOT_WITH_QUIESCE_PLUGIN_DEOS_NOT_RESPOND(vm)
```

### VM&#95;SNAPSHOT&#95;WITH&#95;QUIESCE&#95;TIMEOUT

The VSS plug&#45;in has timed out

_Signature:_

```
VM_SNAPSHOT_WITH_QUIESCE_TIMEOUT(vm)
```

### VM&#95;TOO&#95;MANY&#95;VCPUS

Too many VCPUs to start this VM

_Signature:_

```
VM_TOO_MANY_VCPUS(vm)
```

### VM&#95;TO&#95;IMPORT&#95;IS&#95;NOT&#95;NEWER&#95;VERSION

The VM cannot be imported unforced because it is either the same version or an older version of an existing VM.

_Signature:_

```
VM_TO_IMPORT_IS_NOT_NEWER_VERSION(vm, existing_version, version_to_import)
```

### VM&#95;UNSAFE&#95;BOOT

You attempted an operation on a VM that was judged to be unsafe by the server. This can happen if the VM would run on a CPU that has a potentially incompatible set of feature flags to those the VM requires. If you want to override this warning then use the 'force' option.

_Signature:_

```
VM_UNSAFE_BOOT(vm)
```

### WLB&#95;AUTHENTICATION&#95;FAILED

WLB rejected our configured authentication details.

No parameters.

### WLB&#95;CONNECTION&#95;REFUSED

WLB refused a connection to the server.

No parameters.

### WLB&#95;CONNECTION&#95;RESET

The connection to the WLB server was reset.

No parameters.

### WLB&#95;DISABLED

This pool has wlb&#45;enabled set to false.

No parameters.

### WLB&#95;INTERNAL&#95;ERROR

WLB reported an internal error.

No parameters.

### WLB&#95;MALFORMED&#95;REQUEST

WLB rejected the server's request as malformed.

No parameters.

### WLB&#95;MALFORMED&#95;RESPONSE

WLB said something that the server wasn't expecting or didn't understand.  The method called on WLB, a diagnostic reason, and the response from WLB are returned.

_Signature:_

```
WLB_MALFORMED_RESPONSE(method, reason, response)
```

### WLB&#95;NOT&#95;INITIALIZED

No WLB connection is configured.

No parameters.

### WLB&#95;TIMEOUT

The communication with the WLB server timed out.

_Signature:_

```
WLB_TIMEOUT(configured_timeout)
```

### WLB&#95;UNKNOWN&#95;HOST

The configured WLB server name failed to resolve in DNS.

No parameters.

### WLB&#95;URL&#95;INVALID

The WLB URL is invalid. Ensure it is in format: &lt;ipaddress&gt;:&lt;port&gt;.  The configured/given URL is returned.

_Signature:_

```
WLB_URL_INVALID(url)
```

### WLB&#95;XENSERVER&#95;AUTHENTICATION&#95;FAILED

WLB reported that the server rejected its configured authentication details.

No parameters.

### WLB&#95;XENSERVER&#95;CONNECTION&#95;REFUSED

WLB reported that the server refused it a connection &#40;even though we're connecting perfectly fine in the other direction&#41;.

No parameters.

### WLB&#95;XENSERVER&#95;MALFORMED&#95;RESPONSE

WLB reported that the server said something to it that WLB wasn't expecting or didn't understand.

No parameters.

### WLB&#95;XENSERVER&#95;TIMEOUT

WLB reported that communication with the server timed out.

No parameters.

### WLB&#95;XENSERVER&#95;UNKNOWN&#95;HOST

WLB reported that its configured server name for this server instance failed to resolve in DNS.

No parameters.

### XAPI&#95;HOOK&#95;FAILED

3rd party xapi hook failed

_Signature:_

```
XAPI_HOOK_FAILED(hook_name, reason, stdout, exit_code)
```

### XENAPI&#95;MISSING&#95;PLUGIN

The requested plugin could not be found.

_Signature:_

```
XENAPI_MISSING_PLUGIN(name)
```

### XENAPI&#95;PLUGIN&#95;FAILURE

There was a failure communicating with the plugin.

_Signature:_

```
XENAPI_PLUGIN_FAILURE(status, stdout, stderr)
```

### XEN&#95;VSS&#95;REQ&#95;ERROR&#95;ADDING&#95;VOLUME&#95;TO&#95;SNAPSET&#95;FAILED

Some volumes to be snapshot could not be added to the VSS snapshot set

_Signature:_

```
XEN_VSS_REQ_ERROR_ADDING_VOLUME_TO_SNAPSET_FAILED(vm, error_code)
```

### XEN&#95;VSS&#95;REQ&#95;ERROR&#95;CREATING&#95;SNAPSHOT

An attempt to create the snapshots failed

_Signature:_

```
XEN_VSS_REQ_ERROR_CREATING_SNAPSHOT(vm, error_code)
```

### XEN&#95;VSS&#95;REQ&#95;ERROR&#95;CREATING&#95;SNAPSHOT&#95;XML&#95;STRING

Could not create the XML string generated by the transportable snapshot

_Signature:_

```
XEN_VSS_REQ_ERROR_CREATING_SNAPSHOT_XML_STRING(vm, error_code)
```

### XEN&#95;VSS&#95;REQ&#95;ERROR&#95;INIT&#95;FAILED

Initialization of the VSS requester failed

_Signature:_

```
XEN_VSS_REQ_ERROR_INIT_FAILED(vm, error_code)
```

### XEN&#95;VSS&#95;REQ&#95;ERROR&#95;NO&#95;VOLUMES&#95;SUPPORTED

Could not find any volumes supported by the Vss Provider

_Signature:_

```
XEN_VSS_REQ_ERROR_NO_VOLUMES_SUPPORTED(vm, error_code)
```

### XEN&#95;VSS&#95;REQ&#95;ERROR&#95;PREPARING&#95;WRITERS

An attempt to prepare VSS writers for the snapshot failed

_Signature:_

```
XEN_VSS_REQ_ERROR_PREPARING_WRITERS(vm, error_code)
```

### XEN&#95;VSS&#95;REQ&#95;ERROR&#95;PROV&#95;NOT&#95;LOADED

The Vss Provider is not loaded

_Signature:_

```
XEN_VSS_REQ_ERROR_PROV_NOT_LOADED(vm, error_code)
```

### XEN&#95;VSS&#95;REQ&#95;ERROR&#95;START&#95;SNAPSHOT&#95;SET&#95;FAILED

An attempt to start a new VSS snapshot failed

_Signature:_

```
XEN_VSS_REQ_ERROR_START_SNAPSHOT_SET_FAILED(vm, error_code)
```

### XMLRPC&#95;UNMARSHAL&#95;FAILURE

The server failed to unmarshal the XMLRPC message; it was expecting one element and received something else.

_Signature:_

```
XMLRPC_UNMARSHAL_FAILURE(expected, received)
```

