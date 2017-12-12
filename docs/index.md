Citrix XenServer 7.1 LTSR Management API
========================================
API Revision 2.6
----------------

> Note: The offial PDF version of this API reference is provided on the 
> [docs.citrix.com](https://docs.citrix.com/content/dam/docs/en-us/xenserver/7-1/downloads/xenserver-7-1-management-api-guide.pdf)
> website. For XenServer 7.1 LTSR, this markdown version is provided as a convenience.

## Introduction

This document defines the XenServer Management API - an interface for remotely
configuring and controlling virtualized guests running on a Xen-enabled host.

The API is presented here as a set of Remote Procedure Calls (RPCs), with a wire
format based upon [XML-RPC](http://xmlrpc.scripting.com/spec.html). No specific
language bindings are prescribed, although examples will be given in the Python
programming language.

Although we adopt some terminology from object-oriented programming,
future client language bindings may or may not be object oriented.
The API reference uses the terminology _classes_ and _objects_.
For our purposes a _class_ is simply a hierarchical namespace;
an _object_ is an instance of a class with its fields set to
specific values. Objects are persistent and exist on the server-side.
Clients may obtain opaque references to these server-side objects and then
access their fields via get/set RPCs.

For each class we specify a list of fields along with their _types_ and
_qualifiers_. A qualifier is one of:

- `RO/runtime`: the field is Read Only. Furthermore, its value is
  automatically computed at runtime. For example, current CPU load and disk IO
  throughput.

- `RO/constructor`: the field must be manually set when a new object is
  created, but is then Read Only for the duration of the object's life.
  For example, the maximum memory addressable by a guest is set
  before the guest boots.

- `RW`: the field is Read/Write. For example, the name of a VM.

## Types

A full list of types is given in Chapter 2. However, there are three types that require explicit mention:

- `t ref`: Reference to an object of type `t`.
- `t set`: A set containing values of type `t`.
- `(k -> v) map`: Mapping from values of type `k` to values of type `v`.

Note that there are a number of cases where `ref`s are _doubly linked_.
For example, a `VM` has a field called `VIFs` of type `VIF ref set`;
this field lists the network interfaces attached to a particular VM.
Similarly, the `VIF` class has a field called `VM` of type `VM ref`
which references the VM to which the interface is connected.
These two fields are _bound together_, in the sense that
creating a new VIF causes the `VIFs` field of the corresponding
VM object to be updated automatically.

The API reference lists explicitly the fields that are
bound together in this way. It also contains a diagram that shows
relationships between classes. In this diagram an edge signifies the
existence of a pair of fields that are bound together, using standard
crows-foot notation to signify the type of relationship (e.g.
one-many, many-many).

## RPCs associated with fields

Each field, `f`, has an RPC accessor associated with it that returns `f`'s value:

- `get_f (r)`: takes a `ref`, `r` that refers to an object and returns the value
  of `f`.

Each field, `f`, with qualifier `RW` and whose outermost type is `set` has the
following additional RPCs associated with it:

- `add_f(r, v)`: adds a new element `v` to the set.
  Note that sets cannot contain duplicate values, hence this operation has
  no action in the case that `v` is already in the set.

- `remove_f(r, v)`: removes element `v` from the set.

Each field, `f`, with qualifier `RW` and whose outermost type is `map` has the
following additional RPCs associated with it:

- `add_to_f(r, k, v)`: adds new pair `k -> v` to the mapping stored in `f` in
  object`r`. Adding a new pair for duplicate key, `k`, overwrites any previous mapping for `k`.

- `remove_from_f(r, k)`: removes the pair with key `k`
  from the mapping stored in `f` in object `r`.

Each field whose outermost type is neither `set` nor `map`, but whose
qualifier is `RW` has an RPC accessor associated with it that sets its value:

- `set_f(r, v)`: sets the field `f` on object `r` to value `v`.

## RPCs associated with classes

* Each class has a _constructor_ RPC named `create` that
  takes as parameters all fields marked `RW` and `RO/constructor`. The result
  of this RPC is that a new _persistent_ object is created on the server-side
  with the specified field values.

* Each class has a `get_by_uuid(uuid)` RPC that returns the object
  of that class that has the specified `uuid`.

* Each class that has a `name_label` field has a
  `get_by_name_label(name_label)` RPC that returns a set of objects of that
  class that have the specified `name_label`.

* Each class has a `destroy(r)` RPC that explicitly deletes
  the persistent object specified by `r` from the system.  This is a
  non-cascading delete - if the object being removed is referenced by another
  object then the `destroy` call will fail.

### Additional RPCs

As well as the RPCs enumerated above, some classes have additional RPCs
associated with them. For example, the `VM` class has RPCs for cloning,
suspending, starting etc. Such additional RPCs are described explicitly
in the API reference.
