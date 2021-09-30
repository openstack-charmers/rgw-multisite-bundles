# Overview

This repository contains bundles for deployment for a four way
multisite replicated Ceph RADOS Gateway deployment.

Each bundle should be deployed in a separate Juju model (potentially
on different Juju controllers).

One module should them offer the 'master' endpoint from the ceph-radosgw
application - for example:

```
juju offer -m us-east-1 rgw-us-east-one:master
```

and then all other models should consume this offer and relate to the
master endpoint:

```
juju consume -m us-west-1 admin/us-east-1.rgw-us-east-one
juju add-relation -m rgw-us-west-one rgw-us-east-one
```

post deployment RADOS gateway should be operating in multi-master
replicated mode:

```
$ sudo radosgw-admin sync status
          realm 667c06f5-648e-40d1-adfe-638b5f0d52b6 (replicated)
      zonegroup 30fb055d-66bf-40b3-9e76-fa7f98212ef8 (us)
           zone 2430724a-4b03-494c-9d5b-864285ef41d6 (us-east-two)
  metadata sync syncing
                full sync: 0/64 shards
                incremental sync: 64/64 shards
                metadata is caught up with master
      data sync source: 3fdc0e1a-148d-4b26-b6ea-b67fcb014ec4 (us-west-two)
                        syncing
                        full sync: 0/128 shards
                        incremental sync: 128/128 shards
                        data is caught up with source
                source: 7fcd5b02-f65e-47e8-b8cc-8f5f0ee0e0dc (us-west-one)
                        syncing
                        full sync: 0/128 shards
                        incremental sync: 128/128 shards
                        data is caught up with source
                source: e9e5ba58-d23d-41f9-9b63-69250a40329d (us-east-one)
                        syncing
                        full sync: 0/128 shards
                        incremental sync: 128/128 shards
                        data is caught up with source
```

Metadata operations can only be performed on the current metadata master
zone - this can be migrated between models using the promote action on
the ceph-radosgw application.
