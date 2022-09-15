
# SYNOPSIS                          (./arknoid)
    arknoid - tool to mint ARK (Archival Resource Key) identifiers

# USAGE
    arknoid [options] init
    arknoid [options] mkminter [ NAAN[/Shoulder] ]
    arknoid [options] mint [ Count [ FQShoulder ] ]
    arknoid [options] testmint [ Count ]
    arknoid [options] lsminter [ FQShoulder ... ]
    arknoid [options] rmminter FQShoulder ...
    arknoid [options] test
    arknoid [ help ]

# DESCRIPTION
    This script is used to mint globally unique, opaque, random-looking ARK
    (Archival Resource Key) identifiers. Here, "to mint" means to generate a
    string of letters and digits suitable for use as an ARK. To create an ARK
    is to publicize your assignment of a minted string to a thing (eg, for
    reference purposes). The less that you publicize your ARK assignment, the
    easier it is to "undo" the act of ARK creation.

    To ensure ARK global uniqueness, the ARK namespace is divided into NAANs
    (Name Assigning Authority Numbers). NAANs are further (sub)divided into
    Shoulders, which are useful for delegating responsibility within NAAN
    namespaces. The arknoid script creates each minter on its own Shoulder,
    which looks like YOUR_NAAN/ED, where E is letter and D is a digit. Unless
    you specify it when making a minter, a random Shoulder will be created.

    To run this script you will need a NAAN that will appear at the beginning
    of your ARKs. You may request a NAAN for your organization using the link

        https://n2t.net/e/naan_request

    Use the "init" command to initialize the system with the NAAN reserved
    for you (usually a 5-digit number), or with the test NAAN, 99999.

    The "mkminter" command creates a minter of unique opaque strings consisting
    of digits, letters (betanumerics actually) and a final check character.
    With no arugment, it creates a random shoulder using your NAAN, but you may
    also specify a fully qualified shoulder of your choice. A Shoulder string
    must start with one or more betanumeric letters (bcdfghjkmnpqrstvwxz) and
    end in a digit. The NAAN should be one that you have been assigned (eg,
    12345) via the global ARK NAAN registry. Each minter created with this
    command can generate 70,728,100 unique ARKs.

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
    It is possible to run init on more than one NAAN. This script relies on
    the Noid (v0.424) software.

# ENVIRONMENT
    This script was designed to run within its own docker container, although
    it may well run on any system in which the "noid" software is installed.

    From a terminal window on a computer connected to the dockerhub ecosystem,
    a running container can be downloaded and built with

        $ docker run -it -d --rm --name arknoid jakkbl/arknoid

    To use a minter requires a one-time initialization of your NAAN, as in,

        $ docker exec -it arknoid arknoid init 12345

    The next command generates 3 strings suitable for assignment as ARKs:

        $ docker exec -it arknoid arknoid mint 3

    The next command opens an interactive shell into the container:

        $ docker exec -it arknoid /bin/bash

# DEVELOPER
    The arknoid code is maintained at github.com/jkunze/docker-arknoid.
    From within a cloned git repo, you can make changes, run tests,
    and open a shell with commands such as these.

        $ docker-compose build --no-cache arknoid
        $ docker-compose run arknoid arknoid test
        $ docker-compose run arknoid

# OPTIONS
    -f force run even if not inside a docker container
    -c on init, clear out any previous NAAN data

# FILES
    ./minters/ark/<NAAN>/<Shoulder>

