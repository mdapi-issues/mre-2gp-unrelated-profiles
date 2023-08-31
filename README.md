# MRE: 2GP Unrelated Profiles

> `sf package version create` includes `*.profile-meta.xml` files which are not in the selected package directory

## Issue description

Although we choose a package directory explictly using `--path` or implicitly using `--package`, profile files from other unrelated directories inside the current project are processed and included in the package version.

## Reproduction

The sfdx project in this repo contains a package directory with a Profile (`Dummy Profile`), Layout and CustomField.
Additionally it contains an unrelated directory with two Profiles (`Dummy Profile` and `Unrelated Profile`).

The package version should only process and include the `Dummy Profile` from the packaging directory.
But it processes and includes all of them.

Create an unlocked package first:

```console
sf package create -t Unlocked --no-namespace --path force-app -n "2GP Unrelated Profiles"
```

Now create a package version:

```console
export SF_LOG_LEVEL="debug"
sf package version create -x -d force-app -w 20
```

See output in `~/.sf/sf-$(date +%F).log` (e.g. `~/.sf/sf-2023-08-31.log`):

```text
Building ComponentSet from sourcepath: /path/to/project/force-app
Matching metadata files (3):
{"level":20,"time":1693499233557,"name":"sf:componentSetBuilder","msg":"/path/to/project/force-app/main/default/layouts/Account-Account Layout.layout-meta.xml"}
{"level":20,"time":1693499233557,"name":"sf:componentSetBuilder","msg":"/path/to/project/force-app/main/default/objects/Account/fields/Dummy__c.field-meta.xml"}
{"level":20,"time":1693499233557,"name":"sf:componentSetBuilder","msg":"/path/to/project/force-app/main/default/profiles/Dummy Profile.profile-meta.xml"}
...
The profile "Dummy Profile" from the "/path/to/project/force-app/main/default/profiles/Dummy Profile.profile-meta.xml" directory was added to this package version.
The profile "Dummy Profile" from the "/path/to/project/unrelated-stuff/profiles/Dummy Profile.profile-meta.xml" directory was added to this package version.
The profile "Unrelated Profile" from the "/path/to/project/unrelated-stuff/profiles/Unrelated Profile.profile-meta.xml" directory was added to this package version.
```
