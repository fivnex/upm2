# UPM2
U Package Manager, the U stands for:

- Ultra
- Universal
- Unix/Unix-like
- United
- Useful
- Unamity
- Union
- Umpteen

----

The UPM system is on two feilds:\
A front-end to every other package management system (with some 
enabling/disabling functionality) including RPM, DPKG, APT, Snap,
Flatpak, Git, and even a custom UPM backend. The core backends
will be introduced as Common System Managers (DPKG, APT, and RPM
are in this category), Container Based Managers (Snap, Flatpak),
Configured Managers (any management system put in the configuration
file for UPM), and Misc Managrs (Git, AppImage, and UPM).

----

# Common System Managers
The custom system managers count as package managers that are common on several systems
of which can be classified as a form of "Upstream." The minimum for this is two. The two
upstreams, for them to count, must: \
A - Include custom repository links that are managed by the distribution. \
B - Does not include repository links to any other upstream. \
C - Own 100% of the repository (No split ownership).

The rules set are to weed out several managers that will cause system bloat and bugs for UPM2.
The package managers we will include to be set by a simple config are RPM and DPKG/APT while
others will need too be added by config. Pacman should be appearing as well, once we get a better
hold on how the package manager works. 

----

# Container Based Managers
Container based systems are package managers or software that run in a containerized system. While
many believe AppImage is one of these systems, and they technically are, they are NOT installable
packages, as is the whole point of AppImage. To distribute them takes a different method of management
which will me one of the misc systems. The major parts of this feild is Flatpak and Snap. To run these
in UPM2, there will be a layer of enabability. This will be done in the UPM2 config file, which will
be explained further in this document, but for a quick bit of information they can be enable by custom
values in the config files.

```
enableSnapPackages=True
enableFlatpakPackages=True
```

The reason for them having to be enabled by the user is the user needs to install the system themselves,
as it will require some setup and notice. Once working, however, packages can be installed by either
the command "upm --snap" or "upm --flatpak" to get software. Shortened versions of the command will 
not exist.

----

# Configured Managers
This is for package management systems, and requires the UPM2 config file. The further explanation of the UPM
config file will be explained later, but the specific setup of a package manager follows as such. In the config
file, the following information needs to be placed:

- Installation command
- Removal command
- Update repos command
- Search command
- Default / Alternative
  - If default, nothing else is needed
  - If alternative, will require "altName"

To set up, let's say we are running an alternative package manager (like PacAPT on Ubuntu for example).
The configuration file will look something like this (placement of items DOES NOT MATTER):

> To use an alternative manager, the config file should use the item of "Alt", capital A, no other letters.

```
packageManager(Alt, pacapt) {
	installPackage="-S"
	removePackage="-R"
	updateRepos="-Syy"
	searchRepos="-Ss"
}
```

run `upm reload` and `upm --alt pacapt -Syy` to enable and test the pacapt alternative. It should go without
saying that any alternative package manager MUST be installed and configured on your system.

----

# Misc Managers
There are three misc managers: AppImage, Git, and UPM. UPM packages still need a lot of work to be done in order
to work, so for now they will be ignored. The Git and AppImage systems will be fairly similar, with the major
difference being in the form of source management. With Git installs, the options are to use a normal Git
repo link, GitHub user/repo, and GitLab user/repo. For AppImage there is an option in UPM to set a package
repo, similar to any other manager. To add an AppImage source, you will need to add the following to the UPM2
config:

```
appImageSource(1)="https://example.com"
```

For appImageSource, the (1) puts an ID so you can add multiple links later on.

To install an AppImage from your source, `upm reload` to reload your system's
UPM2 config links and source files that do things in the background, then run
the following command to install your AppImage.

```
upm --appImage exactImageName.AppImage
```

This will add the image to a AppImage directory, found in your home folder. To remove an AppImage:

```
upm --removeAppImage exactImageName.AppImage
```

For Git it is a little less configurable. Due to the nature of Git and how repos
can share names, UPM2 will require a set of CLI arguments to run from a Git command.
The repo must also include an install script (`install.upm`) or one must be provided. 
The name of the install script must be `install.upm` whether in the repo or provided
by the user.

Let's say you have an app, you have your own UPM install script (as one is not included),
you have a thing to install (we will be using the repo example/test from Github). How
will it be done?

`upm --git --github example/test --externalInstallFile ./install.upm`

To do repos from GitLab instead, replace `--github` with `--gitlab`, and all other repos
can be done with a `--repo` and repo link after.

----

# The UPM2 Config File
The file `config.upm` needs to be created in your home directory, and will include tools,
links, settings, etc, for use when running UPM. A base config.upm has at least one argument
in the system. 

```
systemManager=apt
```

This should reflect the command of the package manager you use, with exact casing. The other
options in a UPM2 config file are going to reflect mirror setup, manager setup (of which some
has been covered in this document) and more. Some of the more notable arguments will include:
- altInstallArgument: Defines an alternate name to the install argument in either default or
alternative managers.
- Other alt arguments, similar to altInstallArgument (altUpdateArgument/altRemoveArgument, etc)
- packageManager(): Partially touched on when dealing with alternate managers, this is a function
that can add package managers to the system. Not touched on earlier was how to add a default manager
using this, and using the same syntax as pacapt, to add it as default, do `packageManager(Default, pacapt)`
and change `systemManager` to pacapt (`systemManager=pacapt`).
- shell: defines what shell you are using, the default is bash! To change it, for example, to TCSH, you can
add `shell=tcsh` to the script. Run it like you would run the shell otherwise.

----

# More:
Documentation needs to be added as UPM2 is worked on.
