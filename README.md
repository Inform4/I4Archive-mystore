![Inform4 Archive](https://i4archive.com/wp-content/uploads/2020/02/mystore-terminal-2.jpg)

# [Inform4 Archive](https://i4archive.com/)

## mystore - Versioned Storage

# [*mystore* – The Application](https://i4archive.com/?p=17)

The application *mystore* stores files and folder in versions. Every
version can be restored as a whole or in parts. A storage-folder can
freely be selected for a compact store. Unlike a backup, complete
partition content can’t be stored, but the application focus on regular
files and directories. Unlike a version control system, there is no
special controlled folder, but the storage is open for all directories
and files equally. Up to 1 terabyte and more of data can be managed with
*mystore* in one storage folder, duplicates not counted.

**Download *mystore* at [i4archive.com](https://i4archive.com)**

Please note that the application *mystore* in version 0.9.7-rc isn’t
released for production usage. There are too few tests and user stories.
*mystore* is available for current Linux and Windows operating systems
(tested with Windows 10 and Ubuntu 18.04LTS).

# [*mystore* – The License](https://i4archive.com/?p=15)

The application *mystore* is free for private use and evaluation. For
regular use in the non-private area (from the 3rd backup or after 6
months), a fee-based license is required, which is available at
[Inform4(Andreas Menke)](mailto:license@i4archive.com).

The application *mystore* is offered without assuring special
properties. Any application examples shown may not be suitable for
special situations, nor in any liability it is to be assumed that
examples are functional at all. As far as legally possible, liability
for the application *mystore* is excluded.

# [*mystore* – Use Cases](https://i4archive.com/?p=13)

### *mystore* – Storing

Storing with *mystore* can be initiated from the command line with

    mystore —-store <Pfad-zum-Verzeichnis-oder-Datei>mystore —-store <path-to-file-or-folder>

The data is stored into the default folder *./.store*. If
`<path-to-file-or-folder>` specifies an absolute path, the root of the
path is removed. Storing of an entire partition without specifying a
(sub) directory or a file is not supported. With

    mystore —-storage <Pfad-zum-Storage> —-store <Pfad-zum-Verzeichnis-oder-Datei>mystore —-storage <path-to-storage> —-store <path-to-file-or-folder>

the data is stored in `<path-to-storage>`.

For *mystore* a file *filename* in the current directory */A/B* is seen
different from *B/filename* viewed from the current directory */A*. This
behavior is different from version control systems like git and Co.
*mystore* has no reference directory (eg */* or */A/*). For *mystore*
there are only the names *filename* and *B/filename*, which are
different and cannot be converted into one another. A search for
*filename* yields no results for *B/filename* and vice versa.

### *mystore* – Informing

With *mystore*, the content of a store can be searched using the
switches `--show` and `--browse`. The command

    mystore --show-ee

shows the names used for storing (*end entity*) and the number of
versions for this name. End entity (EE) are those files and folders that
were explicitly specified during a store.

    $ ./mystore19UMTS.exe --storage mystore-vm --show-ee
    Files in storage 'mystore-vm'
     EE   1 version  'Ubuntu18LTS'
     EE   1 version  'W10ITunes'

In contrast to explicit names (EE) all names are shown with the switch
`--show-all`.

    $ ./mystore19UMTS.exe --storage mystore-vm --show-all
    Files in storage 'mystore-vm'
     EE   1 version  'Ubuntu18LTS'
          1 version  'Ubuntu18LTS/ExtralDisk1.vdi'
          1 version  'Ubuntu18LTS/Logs'
          1 version  'Ubuntu18LTS/Logs/VBox.log'
          1 version  'Ubuntu18LTS/Logs/VBox.log.1'
          1 version  'Ubuntu18LTS/Logs/VBox.log.2'
          1 version  'Ubuntu18LTS/Logs/VBox.log.3'
    ...

Information about the versions of a name (file or directory) is
available with the switch `--show <name>`.

    $ ./mystore19UMTS.exe --storage mystore-vm --show Ubuntu18LTS/ExtralDisk1.vdi
    Versions for file 'Ubuntu18LTS/ExtralDisk1.vdi' in storage 'mystore-vm'
    Version   0      21664628736 byte '2020-01-26T17.31.29Z'

Versions are counted from 0 (zero) and each version receives a time
stamp (computer time) in UTC / Zulu time. To each version a freely
selectable character string can be added with the switch
`--version-string <string>`. The character set of the `<string>` is very
restrictive. Only ASCII 7-bit letters and numbers and only a few special
characters are allowed. The storage directory can be set with the switch
`--storage <storage>`.

Details about a name is shown via the switch `--browse <name>`.

    $ ./mystore19UMTS.exe --storage mystore-vm --browse W10ITunes
    Folder 'W10ITunes' in version '2020-01-26T14.29.01Z
     (folder)      'W10ITunes/Logs'
     (folder)      'W10ITunes/Snapshots'
    29847715840 byte 'W10ITunes/W10ITunes-disk1.vdi'
         9634 byte 'W10ITunes/W10ITunes.vbox'
         9634 byte 'W10ITunes/W10ITunes.vbox-prev'

### *mystore* – Restoring

Restoring of files and folder is as easy as storing

    mystore --restore <name>

Above command restores the last (newest) version of `<name>` into the
folder *./.restored*. The command is equivalent to

    mystore --version -1 --restore <name>

Version -1 denotes the last and newest version and 0 (zero) denotes the
oldest and first version of a name. The second-newest version is denoted
with -2 and the second-oldest version is denoted by 1 etc. To restore
the data into another folder, the switch `--restore-folder <folder>` is
used.

    mystore —-restore-folder <verzeichnis> —-restore <name>mystore —-restore-folder <folder> —-restore <name>

In order to restore all versions at once, there is the switch
`--restore-all [<name>]`. This command restores all versions of `<name>`
or all versions based on the EE names in their own subdirectories. Each
subdirectory is named with the version time together with the specified
version string and is unique for a storage.

A check for errors in the storage can be initiated with the switches
`--test <name>` or `--test-all [<name>]`. The tests perform internally
the same actions as the corresponding restoration, but with the
difference that the data is not written to the file system. The switch
` --test-all [<name>]  ` causes all versions to be tested either all
data based on all EE names or all data based on the specified `<name>`.

# [How it works](https://i4archive.com/?p=11)

Storing with *mystore* is based on the principle of the cyclic hash. Put
simply, the files to be stored are divided into fragments and saved. The
original file data is restored using index data. The division into
fragments has the advantage that same fragments do not have to be saved
twice. This advantage occurs automatically with completely identical
files. If, however, in a larger file little has been changed, then there
is a chance that fragments will be formed over the cyclic hash in such a
way that some fragments are the same and only a few fragments are
different. In fact, this effect is only slight for a ’normal‘ store,
since the fragment size is quite high by default with 20 bits. The
`--break-bits N` switch is available for your own experiments and for
adjustment in special cases. The approximate size of the fragments is
given as a bit value.

Each fragment is hashed using the SHA-1 algorithm and compressed using
the deflate algorithm and saved with the hash value as it’s name in the
file system. The SHA-1 algorithm is suitable for this application
because it is only used to distinguish two fragments. The hash value
does not have to withstand an attack against fake duplicates. To be the
growing number of fragments remains clear, they are stored in
directories named with the first byte of the hash value. To absolutely
limit the number of files used, all fragments with hash values that
begin with two identical bytes are packed into one file. With a storage
depth of 1 the absolute number of files is limited to 2 ^ 16 = 64 \*
1024. For experiments and special cases there is the switch
`--store-depth N` specifying the memory depth. By default, this value is
set to the smallest possible value 1. The switch `--no-pack` is
available for experiments and special cases. It’s meaning is that
fragments are not packed, but instead saved in separate files. In case
of the switch `--no-pack`, there is no limit to the number of files
created by a store. This switch must be handled with care, since a
normal file system such as NTFS on a PC with e.g. more than 100,000
files in a directory tree may take a long time to delete the files
alone. The file system itself or programs that work on the file system
can make a damage on the huge amount of file. Take care\! With the
default value of 1 for `--store-depth` the backup directory of *mystore*
is able to manage up to approx. 1 terabyte of data.

### More Storing Alternatives

Storings using *mystore* can be flexibly set, but only a few settings
are really useful or even necessary, and then only in a few special
cases. The default settings are almost always not only sufficient, but
also the best.

A useful option for storing is the import and export of *tar* archives.
When importing a *tar* archive, *mystore* interprets the *tar*
structure. The files and directories contained in the *tar* archive are
saved as if *mystore* had read the data itself from the file system.
Importing a *tar* archive makes sense in case of, for example, reading
from a remote file system. The *mystore* storage directory may not be
accessible via the `--storage <folder>` switch, but storing can still be
done by forwarding the data as a *tar* archive. Using the switch `--tar`
instead of `--store <file-or-folder>` the application *mystore* expects
a *tar* archive via *stdin*.

    cat my.tar | mystore --tar
    cat my.tgz | gzip -d | mystore --tar

There is no separate switch for exporting a *tar* archive. Instead, a
*tar* archive is implicitly packed if the restoration directory –
(*dash*) is specified as `<restore-folder>` for the switch
`--restore-folder <restore-folder>`. The *tar* archive is then output
via *stdout* and can either be saved as a file or forwarded via pipe
(‚|‘).

    mystore --restore-folder - --restore myfolder > my.tar
    mystore --restore-folder - --restore myfolder | gzip > my.tgz

For the special case that *stdin* should not be interpreted as a *tar*
archive, but the data should be saved as an independent file, *mystore*
has the switch `--name <name>`. This peculiarity does not exist for
output. Instead, an output via *stdout* always creates a *tar* archive.

# [FAQ – Frequently Asked Questions](https://i4archive.com/?p=7)

#### How do I work with *mystore*?

In a terminal. Switch to the folder you want to work with and type
`mystore .` to start storing. Getting back the latest stored version
with `mystore --restore .`, `mystore --show .` shows up all stored
versions. Per default *mystore* does not change any sources but only
creates and fills two folder for storing (default *./.store*) and
restoring (default *./.restored*).

#### Can I remove some content from a store? Does *mystore* have a delete function?

No. No delete nor remove are implemented.

#### Does *mystore* has the ability to encrypt it’s output?

No. If you have confidential data please use OS specific functionality
(read settings) or some tool for storage encryption.

#### Why *mystore* does not support hard and soft links?

At now there was no demand for it.

#### Is *mystore* sufficiant for IT administration? Is *mystore* a replacement for a regular backup?

In general no, for both questions. For IT administration ownership
handling isn’t implemented. Regular backup secures for the damage of a
disk. For both *mystore* isn’t designed for.

#### Can I get *mystore* for MAC or an other OS / CPU?

The application already is young, please ask.

#### Please note

All published names and trademarks on this website are the property of
their respective companies.

In case of interest have look at [Inform4 – Personal
Information](https://inform4.com)
