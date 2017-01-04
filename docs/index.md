# Citrix XenServer Management API

# Overview

## XenAPI Classes

Click on a class to view the associated fields and messages.

![Alt text](img/classes.png)

## Classes, Fields and Messages

Classes have both fields and messages. Messages are either implicit or explicit where an implicit message is one of:

- a constructor (usually called "create");
- a destructor (usually called "destroy");
- "get_by_name_label";
- "get_by_uuid"
- "get_record"; and
- "get_all".

Explicit messages include all the rest, more class-specific messages (e.g. "VM.start", "VM.clone")

Every field has at least one accessor depending both on its type and whether it is read-only or read-write. Accessors for a field named "X" would be a proper subset of:

- set_X: change the value of field X (only if it is read-write);
- get_X: retrieve the value of field X;
- add_to_X: add a key/value pair (only if field has type set or map); and
- remove_from_X: remove a key (only if a field has type set or map).

#Auth

## Class: auth

Management of remote authentication services

### Fields

None.

### Messages

Messages

```no-highlight
string set get_group_membership (string)

This calls queries the external directory service to obtain the transitively-closed set of groups that the the subject_identifier is member of.

Parameters:	string subject_identifier	A string containing the subject_identifier, unique in the external directory service

Minimum role:	read-only

Result:	set of subject_identifiers that provides the group membership of subject_identifier passed as argument, it contains, recursively, all groups a subject_identifier is member of.

Published in:	XenServer 5.5	
```

```no-highlight
string get_subject_identifier (string)

This call queries the external directory service to obtain the subject_identifier as a string from the human-readable subject_name

Parameters:	string subject_name	The human-readable subject_name, such as a username or a groupname

Minimum role:	read-only

Result:	the subject_identifier obtained from the external directory service

Published in:	XenServer 5.5
```

```no-highlight
(string → string) map get_subject_information_from_identifier (string)

This call queries the external directory service to obtain the user information (e.g. username, organization etc) from the specified subject_identifier

Parameters:	string subject_identifier	A string containing the subject_identifier, unique in the external directory service

Minimum role:	read-only

Result:	key-value pairs containing at least a key called subject_name

Published in:	XenServer 5.5	
```

#blob

## Class: blob

A placeholder for a binary blob

### Fields

```no-highlight
datetime last_updated [read-only]

Time at which the data in the blob was last updated

Published in:	XenServer 4.0	Time at which the data in the blob was last updated
```

```no-highlight
string mime_type [read-only]

The mime type associated with this object. Defaults to 'application/octet-stream' if the empty string is supplied

Published in:	XenServer 4.0	The mime type associated with this object. Defaults to 'application/octet-stream' if the empty string is supplied
```

```no-highlight
string name_description [read/write]

a notes field containing human-readable description

Default value:	""

Published in:	XenServer 4.0	a notes field containing human-readable description
```

```no-highlight
string name_label [read/write]

a human-readable name

Default value:	""

Published in:	XenServer 4.0	a human-readable name
```

```no-highlight
bool public [read/write]

True if the blob is publicly accessible

Default value:	false

Published in:	XenServer 6.1	True if the blob is publicly accessible
```

```no-highlight
int size [read-only]

Size of the binary data, in bytes

Published in:	XenServer 4.0	Size of the binary data, in bytes
```

```no-highlight
string uuid [read-only]

Unique identifier/object reference

Published in:	XenServer 4.0	Unique identifier/object reference
```

### Messages

```no-highlight
blob ref create (string, bool)

Create a placeholder for a binary blob

Parameters:	string mime_type	The mime-type of the blob. Defaults to 'application/octet-stream' if the empty string is supplied

bool public	True if the blob should be publicly available

Minimum role:	pool-operator

Result:	The reference to the created blob

Published in:	XenServer 5.0	Create a placeholder for a binary blob
```

```no-highlight
void destroy (blob ref)

Parameters:	blob ref self	The reference of the blob to destroy

Minimum role:	pool-operator

Published in:	XenServer 5.0
```

#Bond

## Class: Bond

### Enums

```no-highlight
bond_mode

Values:	balance-slb	Source-level balancing

active-backup	Active/passive bonding: only one NIC is carrying traffic

lacp	Link aggregation control protocol
```

### Fields

```no-highlight
int links_up [read-only]

Number of links up in this bond

Default value:	0

Published in:	XenServer 6.1	Number of links up in this bond

PIF ref master [read-only]

The bonded interface

Default value:	Null

Published in:	XenServer 4.1	The bonded interface
```

```no-highlight
bond_mode mode [read-only]

The algorithm used to distribute traffic among the bonded NICs

Default value:	balance-slb

Published in:	XenServer 6.0	
```

```no-highlight
(string → string) map other_config [read/write]

additional configuration

Default value:	{}

Published in:	XenServer 4.1	additional configuration

PIF ref primary_slave [read-only]

The PIF of which the IP configuration and MAC were copied to the bond, and which will receive all configuration/VLANs/VIFs on the bond if the bond is destroyed

Default value:	OpaqueRef:NULL

Published in:	XenServer 6.0	
```

```no-highlight
(string → string) map properties [read-only]

Additional configuration properties specific to the bond mode.

Default value:	{}

Published in:	XenServer 6.1	Additional configuration properties specific to the bond mode.

PIF ref set slaves [read-only]

The interfaces which are part of this bond

Published in:	XenServer 4.1	The interfaces which are part of this bond
```

```no-highlight
string uuid [read-only]

Unique identifier/object reference

Published in:	XenServer 4.0	Unique identifier/object reference
```

### Messages

```no-highlight
Bond ref create (network ref, PIF ref set, string, bond_mode, (string → string) map)

Create an interface bond

Parameters:	network ref network	Network to add the bonded PIF to

PIF ref set members	PIFs to add to this bond

string MAC	The MAC address to use on the bond itself. If this parameter is the empty string then the bond will inherit its MAC address from the primary slave.

bond_mode mode	Bonding mode to use for the new bond

(string → string) map properties	Additional configuration parameters specific to the bond mode

Minimum role:	pool-operator

Result:	The reference of the created Bond object

Published in:	XenServer 4.1	Create an interface bond
```

```no-highlight
void destroy (Bond ref)

Destroy an interface bond

Parameters:	Bond ref self	Bond to destroy

Minimum role:	pool-operator

Published in:	XenServer 4.1	Destroy an interface bond

void set_mode (Bond ref, bond_mode)

Change the bond mode

Parameters:	Bond ref self	The bond

bond_mode value	The new bond mode

Minimum role:	pool-operator

Published in:	XenServer 6.0	
```

```no-highlight
void set_property (Bond ref, string, string)

Set the value of a property of the bond

Parameters:	Bond ref self	The bond

string name	The property name

string value	The property value

Minimum role:	pool-operator

Published in:	XenServer 6.1	Set the value of a property of the bond
```

#console

## Class: console

A console

### Enums

```no-highlight

console_protocol

Values:	vt100	VT100 terminal

rfb	Remote FrameBuffer protocol (as used in VNC)

rdp	Remote Desktop Protocol
```

### Fields

```no-highlight
string location [read-only]

URI for the console service

Published in:	XenServer 4.0	URI for the console service
```

```no-highlight
(string → string) map other_config [read/write]

additional configuration

Published in:	XenServer 4.0	additional configuration
```

```no-highlight
console_protocol protocol [read-only]

the protocol used by this console

Published in:	XenServer 4.0	the protocol used by this console
```

```no-highlight
string uuid [read-only]

Unique identifier/object reference

Published in:	XenServer 4.0	Unique identifier/object reference
```

```no-highlight
VM ref VM [read-only]

VM to which this console is attached

Published in:	XenServer 4.0	VM to which this console is attached
```

### Messages

None.

#crashdump

## Class: crashdump

A VM crashdump

### Fields

```no-highlight
(string → string) map other_config [read/write]

additional configuration

Default value:	{}

Published in:	XenServer 4.1	additional configuration
```

```no-highlight
string uuid [read-only]

Unique identifier/object reference

Published in:	XenServer 4.0	Unique identifier/object reference
```

```no-highlight
VDI ref VDI [read-only]

the virtual disk

Published in:	XenServer 4.0	the virtual disk
```

```no-highlight
VM ref VM [read-only]

the virtual machine

Published in:	XenServer 4.0	the virtual machine
```

### Messages

```no-highlight
void destroy (crashdump ref)
Destroy the specified crashdump
Parameters:	crashdump ref self	The crashdump to destroy
Minimum role:	pool-operator
Published in:	XenServer 4.0	Destroy the specified crashdump
```

#data_source

## Class: data_source

Data sources for logging in RRDs

### Fields

```no-highlight
bool enabled [read-only]

true if the data source is being logged

Published in:	XenServer 4.0	true if the data source is being logged
```

```no-highlight
float max [read-only]

the maximum value of the data source

Published in:	XenServer 4.0	the maximum value of the data source
```

```no-highlight
float min [read-only]

the minimum value of the data source

Published in:	XenServer 4.0	the minimum value of the data source
```

```no-highlight
string name_description [read-only]

a notes field containing human-readable description

Default value:	""

Published in:	XenServer 4.0	a notes field containing human-readable description
```

```no-highlight
string name_label [read-only]

a human-readable name

Default value:	""

Published in:	XenServer 4.0	a human-readable name
```

```no-highlight
bool standard [read-only]

true if the data source is enabled by default. Non-default data sources cannot be disabled

Published in:	XenServer 4.0	true if the data source is enabled by default. Non-default data sources cannot be disabled
```
```no-highlight
string units [read-only]

the units of the value

Published in:	XenServer 4.0	the units of the value
```

```no-highlight
float value [read-only]

current value of the data source

Published in:	XenServer 4.0	current value of the data source
```

### Messages

None.

#DR_task

## Class: DR_task

DR task

### Fields

```no-highlight
SR ref set introduced_SRs [read-only]

All SRs introduced by this appliance

Published in:	XenServer 4.0	All SRs introduced by this appliance
```

```no-highlight
string uuid [read-only]

Unique identifier/object reference

Published in:	XenServer 4.0	Unique identifier/object reference
```

### Messages

```no-highlight
DR_task ref create (string, (string → string) map, string set)

Create a disaster recovery task which will query the supplied list of devices

Parameters:	string type	The SR driver type of the SRs to introduce

(string → string) map device_config	The device configuration of the SRs to introduce

string set whitelist	The devices to use for disaster recovery

Minimum role:	pool-operator

Result:	The reference to the created task

Published in:	XenServer 6.0	Create a disaster recovery task which will query the supplied list of devices
```

```no-highlight
void destroy (DR_task ref)

Destroy the disaster recovery task, detaching and forgetting any SRs introduced which are no longer required

Parameters:	DR_task ref self	The disaster recovery task to destroy

Minimum role:	pool-operator

Published in:	XenServer 6.0	Destroy the disaster recovery task, detaching and forgetting any SRs introduced which are no longer required
```
