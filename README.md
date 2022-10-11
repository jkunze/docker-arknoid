
# SYNOPSIS
arknoid - tool to create ARK (Archival Resource Key) identifiers

# QUICK START
If your organization doesn't have a NAAN, request one here:

    https://n2t.net/e/naan_request

After you install docker on your host, build the arknoid container:

    $ docker run -it -d --rm --name arknoid jakkbl/arknoid

Initialize the system with your organization's 5-digit NAAN:

    $ docker exec -it arknoid arknoid init 12345

Mint one ARK string with

    $ docker exec -it arknoid arknoid mint 1
	ark:12345/h74x54g19

Mint enough unique ARK strings to assign to your 35865 objects with

    $ docker exec -it arknoid arknoid mint 36000 > MyFirstARKs

You can mint over 70 million ARKs with one minter, and make as many minters
as you want whenever you want. Your ARKs will be unique across all minters.

# USAGE
    arknoid [options] init NAAN
    arknoid [options] mkminter [ NAAN[/Shoulder] ]
    arknoid [options] mint [ Count [ FQShoulder ] ]
    arknoid [options] testmint [ Count ]
    arknoid [options] lsminter [ FQShoulder ... ]
    arknoid [options] rmminter FQShoulder ...
    arknoid [options] test
    arknoid help

# DESCRIPTION
Archival Resource Keys (ARKs) are free, flexible, persistable identifiers.
This script is used to generate globally unique, opaque, random-looking
ARK strings that are suitable as persistent identifiers (PIDs). To create
an ARK is to publicize the assignment to a thing (eg, for reference
purposes) of a minted (generated) string. The less that that assignment is
publicized, the easier it is to "undo" that act of ARK creation.

To ensure global uniqueness, the ARK namespace is divided into NAANs
(Name Assigning Authority Numbers). NAANs can be divided into Shoulders,
which are useful for delegating responsibility within each NAAN namespace.
The arknoid script creates each minter on its own Shoulder, which looks
like YOUR_NAAN/ED, where E is letter and D is a digit. Unless you specify
it when making a minter, a random Shoulder will be created.

To run this script you will need a NAAN that will appear at the beginning
of your ARKs. You may request a NAAN for your organization using the link

    https://n2t.net/e/naan_request

Use the "init" command to initialize the system once and for all with the
NAAN (usually a 5-digit number) that you reserved for your institution or
with the test NAAN, 99999. For example,

    arknoid init 12345

The "mkminter" command creates a minter of unique opaque strings consisting
of digits, letters (betanumerics actually) and a final check character.
With no arugment, it creates a random shoulder using your NAAN, but you may
also specify a fully qualified shoulder of your choice. A Shoulder string
must start with one or more betanumeric letters (bcdfghjkmnpqrstvwxz) and
end in a digit. The NAAN should be one that you have been assigned (eg,
12345) via the global ARK NAAN registry. Each minter created with this
command can generate 70,728,100 unique ARKs. As an example, to create a
minter on the shoulder, 12345/r4, and then mint 6 ARKs, you could run

    arknoid mkminter 12345/r4
    arknoid mint 6 12345/r4

The "mint" command generates Count (default 1) strings suitable for ARK
assignments from the "fully qualified" minter name, FQShoulder, which
consists of the NAAN, a '/', and the Shoulder string. The "testmint"
command is simple shorthand to generate ARKs beginning with 99999,
which recipients understand to be generally impersistent, untrustworthy,
and for test purposes only.

Use the "lsminter" command with FQShoulder arguments to check for the
existence of one or more minters, or use it with no arguments to list all
minters available to you. Use the "rmminter" command to remove minters.
The "test" command verifies whether the software is working correctly and
the "help" command (the default command) outputs documentation.

Most commands exit with zero status on success and non-zero on error.
It is possible to run init on more than one NAAN. This script uses the
Noid (v0.424) software and adds several features such as default minter
and shoulder choices, as well as default templates that conform to best
practices for creating betanumeric ARKs with primordinal shoulders (that
follow the "first digit convention") and check characters.

# ENVIRONMENT
This script was designed to run within its own docker container, although
it should run on any system where the Noid software is installed.

From a terminal window on a computer connected to the dockerhub ecosystem,
a running container can be downloaded and built with

    $ docker run -it -d --rm --name arknoid jakkbl/arknoid

This command puts the container through a set of tests:

    $ docker run -it jakkbl/arknoid arknoid test

To use a minter requires a one-time initialization of your NAAN, as in,

    $ docker exec -it arknoid arknoid init 12345

The next command generates 3 strings suitable for assignment as ARKs:

    $ docker exec -it arknoid arknoid mint 3

The next command opens an interactive shell into the container:

    $ docker exec -it arknoid /bin/bash

Minter state is preserved in the "./minters/" directory using a private
docker volume when the container is stopped and restarted, but state is
purged when a container is rebuilt. You may be able to explore or export
minter state using the interactive shell.

# DEVELOPER
The arknoid code is maintained at github.com/jkunze/docker-arknoid.
From within a cloned git repo, you can make changes, run tests,
and open a shell with commands such as these.

    $ docker compose build --no-cache arknoid
    $ docker compose run arknoid arknoid test
    $ docker compose run arknoid

# LIMITATIONS
In being a simplified interface to the Noid software, this script uses
a fixed identifier template. As fo any Noid template, the algorithm runs
out of identifier strings at a certain point. Newer, unlimited algorithms
are under development and will be released to the ARK community, but there
are no known plans to fundamentally change either this script or the Noid
software.

# OPTIONS
    -c on init, clear out any previous NAAN data
    -f force run even if not inside a docker container
    -h show help information
    -v be more verbose

# FILES
    ./minters/ark/<NAAN>/<Shoulder>   keeps current minters states

