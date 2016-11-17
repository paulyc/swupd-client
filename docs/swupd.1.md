swupd(1) -- OS software update program
==================================


## SYNOPSIS

`swupd [subcommand] <flags>`


## DESCRIPTION

`swupd(1)` is an OS-level software update program that applies updates
to system software.

The updates are fetched from a central software update server. If a
valid update is found on the server, it can be downloaded and applied.

The `swupd` tool can also install and remove bundles, check for updates
without applying them, and perform system-level verification of the
system software.

A *version url* server provides version information. This server notifies
the program of available updates.

A *content url* server (can be the same as *version url* server) provides
the file and metadata content for all versions. The content url server
provides metadata in the form of manifests. These Manifest files list and
describe file contents, symlinks, directories. Additionally, the actual
content is provided to clients in the form of archive files.


## OPTIONS

The following options are applicable to most subcommands, and can be used
to modify the core behavior and resources that swupd uses.

 * `-h, --help`

   Display general help information. If put after a subcommand, it will
   display help specific to that subcommand.

 * `-v, --version`

   Displays the version information of the swupd program, and exit. It
   also displays compile options and copyright information.

 * `-u, --url={url}`

    Specify an RFC-3986 encoded url. The url will be used to download
    version information and file content downloads.

 * `-c, --contenturl={url}`

    Specify an RFC-3986 encoded url. The url will be used for file content
    downloads only.

 * `-v, --versionurl={url}`

    Specify an RFC-3986 encoded url. The url will be used to download
    version information.

 * `-P, --port={port}`

    Specify the port number of the server to connect to. Applies to both
    version and file content url server connections.

 * `-p, --path={path}`

    Specify the path to use for operations. This can be used to point to
    a chroot installation of the OS or a custom mount.

 * `-F, --format={formatstring}`

    Specify the format suffix for version file downloads. Is usually one
    of `1`, `2`, `3`, etc. or `staging`. Software update formats may change
    regularly and normally you should consult the swupd server data for
    the appropriate latest version available. If that version is not
    supported by your version of `swupd`, you should subtract `1` from the
    number and try again until it succeeds.

 *  `-f, --force`

    Forces completion of swupd beyond critical failures. This may ignore
    filesystem errors, configuration errors and other errors which are
    considered fatal, and could damage an installation if not addressed
    properly.

 *  `-S, --statedir={path}`

    Specify an alternate swupd state directory. Normally `swupd` uses
    `/var/lib/swupd`.


## SUBCOMMANDS

`bundle-add {bundles}`

    Installs new software bundles. Bundles available can be listed with
    the `--list` option. Any bundle name listed after `bundle-add` will
    be downloaded and installed.

    *  `-l, --list`

        Lists all available software bundles, either installed or not, that
        are available.

`bundle-remove {bundles}`

    Removes software bundles. Any bundle name listed after `bundle-remove`
    will be removed from the system, as well as all bundles that required
    the listed bundles, either directly or indirectly.

`check-update`

    Checks whether an update is available and prints out the information
    if so. Does not download update content.

`hashdump {path}`

    Calculates and print the Manifest hash for a specific file on disk.

    * `-n --no-xattrs`

        Ignore extended attributes when calculating hash.

    * `-p, --path={path}`

        Specify the path to use for operations. This can be used to
        point to a chroot installation of the OS or a custom mount.

`search {string}`

    Search for matching paths in manifest data. The specified {string}
    is matched in any part of the path listed in manifests, and all
    matches are printed, including the name of the bundle in which the
    match was found.

    If manifest data is not present in the state folder, it is
    downloaded from the content url.

    Because this search consults all manifests, it normally requires to
    download all manifests for bundles that are not installed, and may
    result in the download of several mega bytes of manifest data.

    * `-l, --library`

        Restrict search to designated dynamic shared library paths.

    * `-b, --binary`

        Restrict search to designated program binary paths.

    * `-i, --init`

        Perform collection and download of all required manifest
        resources needed to perform the search, then exit.

    * `-d, --display-files`

        Do not search for any particular string, instead, print out all
        files, paths, etc. listed in any manifest, and exit.

    * `-s, --scope={b|o}`

        Restrict search to only list the first match found in *b*undle
        or *o*s.

`update`

    Performs a system software update.

    The program will contact the version server at the version url, and
    check to see if a system software update is available. If an update
    is available, the update content will be downloaded from the content
    url and stored in the `/var/lib/swupd` state path. Once all content
    is downloaded and verified, the update is applied to the system.

    In case any problem arises during a software update, the program
    attempts to correct the issue, possibly by performing a `swupd verify --fix`
    operation, which corrects broken or missing files and other issues.

    After the update is applied, the system performs an array of
    post-update actions. These actions are triggered through `systemd(1)`
    and reside in the `update-triggers.target(4)` system target.

    * `-s, --status`

        Do not perform an update, instead display whether an update is
        available on the version url server, and what version number is
        available.

    * `-d, --download`

        Do not perform an update, instead download all resources needed
        to perform the update, and exit.

`verify`

    Perform system software installation verification. The program will
    obtain all the manifests needed from version url and content url to
    establish whether the system software is correctly installed and not
    overwritten, modified, missing or otherwise incorrect (permissions, etc.).

    After obtaining the proper resources, all files that are under
    control of the software update program are verified according to the
    manifest data

    * `-f, --fix`

        Correct any issues found. This will overwrite incorrect file
        content, add missing files and do additional corrections, permissions
        etc.

    * `-i, --install`

        Install all files into {path} as specified by the `--path={path}`
        option. Useful to generate a new system root, or verify side
        by side.

    * `-q, --quick`

        Omit checking hash values. Instead only corrects missing files
        and directories and/or symlinks.


## EXIT STATUS

On success, 0 is returned. A non-zero return code signals a failure.

If the subcommand `check-update` was specified, the program returns `0`
if an update is available, `1` if no update available, and a return value
higher than `1` signals a failure.


## COPYRIGHT

 * Copyright (C) 2016 Intel Corporation, License: CC-BY-SA-3.0


## SEE ALSO

`check-update.service(4)`, `check-update.timer(4)`,
`swupd-update.service(4)`, `swupd-update.timer(4)`,
`update-triggers.target(4)`

https://github.com/clearlinux/swupd-client/

https://clearlinux.org/documentation/


## NOTES

Creative Commons Attribution-ShareAlike 3.0 Unported

 * http://creativecommons.org/licenses/by-sa/3.0/