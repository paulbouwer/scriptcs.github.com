---
layout: post
title: "A new file system"
tagline: "Playing better with .NET solutions."
category: Releases
tags: [changes]
---
{% include JB/setup %}

With scriptcs version 0.13 we decided to make some changes to help scriptcs run more smoothly together with existing .NET solutions. A problem which existed until now was the occasional inability to run scriptcs from within the folder of a regular .NET solution due to problematic assemblies in `packages` or `bin` folders. These would be assemblies which were not installed in order to be used from scriptcs, but rather from the .NET solution, and cause errors or warnings when running scriptcs. In particular, developers and users of build systems based on scriptcs, e.g. [PVC](http://pvcbuild.com/) and [Bau](https://github.com/bau-build/bau), were often tripped up by this.

The problem occured because, until now, the file system conventions used by regular .NET and scriptcs overlapped. They both used `packages.config`, `packages/` and `nuget.config` for NuGet package management and scriptcs searched `bin/` for binaries, a folder often used for other purposes in a .NET solution.

Starting with version 0.13, we've changed our conventions to isolate our file system artifacts from interference with a regular .NET solution.

<pre>
.cache            ->   .scriptcs_cache
bin/              ->   scriptcs_bin/
packages/         ->   scriptcs_packages/
nuget.config      ->   scriptcs_nuget.config
packages.config   ->   scriptcs_packages.config
</pre>

### Automatic migration

When you run scriptcs 0.13, it will detect 'legacy' file system artifacts (those shared with regular .NET) and automatically migrate them. To ensure anything outside scriptcs keeps functioning (e.g. an existing .NET solution), the existing artifacts are left in place, and copies are made instead. The only artifact which is renamed is `.cache` (to `.scriptcs_cache`) since this was never shared with regular .NET, but has still had it's name changed for consistency.

E.g. if your folder looked like this *before* running scriptcs 0.13 (you may not have all of these artifacts):

<img src="/images/2015-02-02-before.png" />

you will see this output when running scriptcs 0.13:

<img src="/images/2015-02-02-during.png" />

and you your folder will end up looking like this:

<img src="/images/2015-02-02-after.png" />

If you don't need the original artifacts, feel free to delete them. It would be very difficult to automatically detect, with certainty, whether or not they're required so we thought we'd leave it to you to decide.

### Performance

Another nice effect of this change is a potential increase in performance. When scriptcs starts up, it scans `bin/` and `packages/` for modules and script packs. Previously, a large .NET solution may have many assemblies in these folders, not required for script execution, and this would result in a performance hit. With the new file system conventions, only those assemblies needed for script execution are scanned and that should speed things up in many cases.

### Hosting

If you are hosting scriptcs in your own application and you want to perform automatic migration, you will need to call `scriptServices.FileSystemMigrator.Migrate()` at an appropriate place in your application.

### Summary

Our hope is that these changes will smooth out any friction you may have running scriptcs in environments shared with regular .NET solutions and projects. In the majority of cases, the upgrade to scriptcs 0.13 should be seamless. Migration to the new conventions is automatic and the only burden left on you is to remove any unwanted 'legacy' artifacts. If you do have any problems, please don't hesitate to let us know by [raising an issue](https://github.com/scriptcs/scriptcs/issues).

By the way, the file system changes are not the only changes in scriptcs version 0.13. For full details of what is contained in this release, please see the [release notes](https://github.com/scriptcs/scriptcs/releases/tag/v0.13).
