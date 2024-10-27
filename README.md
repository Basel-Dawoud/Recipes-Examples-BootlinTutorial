# Recipes Examples
### Explaining examples of recipes of the bootlin yocto tutorial

```bash
SUMMARY = "Hello world program"
DESCRIPTION = "Hello world program"
HOMEPAGE = "http://example.net/hello/"
SECTION = "examples"
LICENSE = "GPL-2.0-or-later"
SRC_URI = "git://git.example.com/hello;protocol=https;branch=master"
SRCREV = "2d47b4eb66e705458a17622c2e09367300a7b118"
S = "${WORKDIR}/git"
LIC_FILES_CHKSUM = "file://hello.c;beginline=3;endline=21;md5=58e..."
do_compile() {
oe_runmake
}
do_install() {
install -d ${D}${bindir}
install -m 0755 hello ${D}${bindir}
}
```

## `SUMMARY`

- **Purpose**: To give a concise overview of the package.
- **Length**: Should be 72 characters or fewer for clarity.
- **Fallback**: Used as the `DESCRIPTION` if not specified separately.

## `DESCRIPTION`

The package description used by package managers. If not set, [DESCRIPTION](https://docs.yoctoproject.org/ref-manual/variables.html#term-DESCRIPTION) takes the value of the [SUMMARY](https://docs.yoctoproject.org/ref-manual/variables.html#term-SUMMARY) variable.

## `HOMEPAGE`

Website where more information about the software the recipe is building can be found.

## `SECTION`

- **Organization**: Helps maintain a clean and organized repository of software.
- **Discoverability**: Enhances the ability of users to discover relevant software packages.
- **Dependency Management**: Some package management systems may use sections to manage dependencies more effectively, ensuring that related packages are available together.

### Typical Sections

The specific values for the `SECTION` variable can vary by distribution and package management system, but common sections include:

- **Base**: Fundamental system packages.
- **Development**: Tools and libraries for software development (e.g., compilers, IDEs).
- **Utilities**: General-purpose tools that assist with various tasks.
- **Networking**: Packages related to network services and tools.
- **Libraries**: Shared libraries needed by applications.
- **Games**: Packages related to gaming.

## `LICENCE`

The list of source licenses for the recipe. Follow these rules:

• Do not use spaces within individual license names.
• Separate license names using | (pipe) when there is a choice between licenses.
• Separate license names using & (ampersand) when there are multiple licenses for different parts of the source.
• You can use spaces between license names.
• For standard licenses, use the names of the files in `meta/files/common-licenses/` or the [SPDXLICENSEMAP](https://docs.yoctoproject.org/ref-manual/variables.html#term-SPDXLICENSEMAP) flag names defined in `meta/conf/licenses.conf`.

Here are some examples:

`LICENSE = "LGPL-2.1-only | GPL-3.0-only"
LICENSE = "MPL-1.0 & LGPL-2.1-only"
LICENSE = "GPL-2.0-or-later"`

The first example is from the recipes for Qt, which the user may choose to distribute under either the LGPL version 2.1 or GPL version 3.

The second example is from Cairo where two licenses cover different parts of the source code.

The final example is from `sysstat`, which presents a single license.

You can also specify licenses on a per-package basis to handle situations where components of the output have different licenses. For example, a piece of software whose code is licensed under GPLv2 but has accompanying documentation licensed under the GNU Free Documentation License 1.2 could be specified as follows:

`LICENSE = "GFDL-1.2 & GPL-2.0-only"
LICENSE:${PN} = "GPL-2.0.only"
LICENSE:${PN}-doc = "GFDL-1.2"` 

## [`SRC_URI`](https://docs.yoctoproject.org/bitbake/2.8/bitbake-user-manual/bitbake-user-manual-ref-variables.html#term-SRC_URI)

The list of source files — local or remote. This variable tells BitBake which bits to pull for the build and how to pull them. For example, if the recipe or append file needs to fetch a single tarball from the Internet, the recipe or append file uses a [SRC_URI](https://docs.yoctoproject.org/bitbake/2.8/bitbake-user-manual/bitbake-user-manual-ref-variables.html#term-SRC_URI) entry that specifies that tarball. On the other hand, if the recipe or append file needs to fetch a tarball, apply two patches, and include a custom file, the recipe or append file needs an [SRC_URI](https://docs.yoctoproject.org/bitbake/2.8/bitbake-user-manual/bitbake-user-manual-ref-variables.html#term-SRC_URI) variable that specifies all those sources.

### Example

Here’s an example of how `SRC_URI` might look in a recipe:

```bash
SRC_URI = "http://example.com/mysoftware-1.0.tar.gz \
            file://myconfig.conf \
            file://fix-bug.patch"
```

In this example:

- The main source code is fetched from a tarball.
- A custom configuration file and a bug fix patch are included in the build process.

## [`SRCREV`](https://docs.yoctoproject.org/bitbake/2.8/bitbake-user-manual/bitbake-user-manual-ref-variables.html#term-SRCREV)

The revision of the source code used to build the package. This variable applies only when using Subversion, Git, Mercurial and Bazaar. If you want to build a fixed revision and you want to avoid
performing a query on the remote repository every time BitBake parses your recipe, you should specify a [SRCREV](https://docs.yoctoproject.org/bitbake/2.8/bitbake-user-manual/bitbake-user-manual-ref-variables.html#term-SRCREV) that is a full revision identifier and not just a tag.

### When to Use `SRCREV`

- **Fixed Revisions**: It’s best to specify `SRCREV` when you want to work with a known stable commit or specific feature set, rather than the latest version in a branch.
- **Development vs. Stable Builds**: In a development setting, you might want to point to the latest commit in a branch for active development. In contrast, for production builds, it’s prudent to specify a known good revision.
- Example: `SRCREV = "2d47b4eb66e705458a17622c2e09367300a7b118"`

## [`S`](https://docs.yoctoproject.org/ref-manual/variables.html#term-S)

The `S` variable in a BitBake recipe specifies the location of the unpacked source code within the build directory.

### Purpose of `S`

1. **Source Code Location**:
    - `S` defines the directory where the source code for the recipe can be found after it has been fetched and unpacked. This allows the build system to locate the source files during the build process.
2. **Default Behavior**:
    - By default, `S` is set to `${WORKDIR}/${BPN}-${PV}`, where:
        - `${WORKDIR}` is the temporary working directory for the build.
        - `${BPN}` is the base name of the recipe (without version).
        - `${PV}` is the version of the recipe.
    - This default path works well when the source code is unpacked from a tarball or similar source that extracts to a directory named according to the recipe's name and version.
3. **Custom Paths**:
    - When the unpacked source code resides in a directory with a different naming convention, or when the source is fetched from a source control management (SCM) system (like Git or Subversion), you must explicitly set `S` to point to the correct location.

### When to Set `S`

1. **Non-standard Directory Names**:
    - If the source tarball extracts into a directory that does not match the `${BPN}-${PV}` format, you need to define `S` to point to the correct directory.
2. **Source Control Management (SCM) Systems**:ere the 
    - For Git repositories or other SCMs, the default unpacked location may differ. For instance, Git repositories are typically cloned into `${WORKDIR}/git`. In such cases, you must set `S` accordingly.

### Example Usage

### Example 1: Default Behavior

For a recipe named `db` with version `5.1.19`, the default `S` would look like this:

```bash
S = "${WORKDIR}/db-5.1.19"
```

### Example 2: Custom Directory for Tarball

If the tarball extracts to a directory named `database-5.1.19`, you would specify:

```bash
S = "${WORKDIR}/database-5.1.19"
```

### Example 3: Using Git

When fetching from a Git repository, you might specify:

```bash
SRC_URI = "git://path/to/repo.git;branch=main"
S = "${WORKDIR}/git"
```

### Summary

The `S` variable is crucial for directing the build system to the correct location of the unpacked source code. Properly setting `S` ensures that the build process can locate and compile the source files effectively. By understanding how and when to set this variable, you can manage various source formats and control the build environment more accurately.

## [`LIC_FILES_CHKSUM`](https://docs.yoctoproject.org/ref-manual/variables.html#term-LIC_FILES_CHKSUM)

Checksums of the license text in the recipe source code.

This variable tracks changes in license text of the source code files. If the license text is changed, it will trigger a build failure, which gives the developer an opportunity to review any license change.

This variable must be defined for all recipes (unless [`LICENSE](https://docs.yoctoproject.org/ref-manual/variables.html#term-LICENSE)` is set to “`CLOSED`”).

Setting the `LICENSE` variable to "`CLOSED`" in a BitBake recipe indicates that the software is proprietary and does not require tracking of license changes via `LIC_FILES_CHKSUM`. 

## `do_compile`[](https://docs.yoctoproject.org/ref-manual/tasks.html#do-compile)

Compiles the source code. This task runs with the current working directory set to `${`[B](https://docs.yoctoproject.org/ref-manual/variables.html#term-B)`}`.

The default behavior of this task is to run the `oe_runmake` function if a makefile (`Makefile`, `makefile`, or `GNUmakefile`) is found.

If no such file is found, the [do_compile](https://docs.yoctoproject.org/ref-manual/tasks.html#ref-tasks-compile) task does nothing.

## `oe_runmake`function

used to run make.

- passes EXTRA_OEMAKE settings to make
- displays the make command
- checks for errors generated via the call.

## `do_install`[](https://docs.yoctoproject.org/ref-manual/tasks.html#do-install)

Copies files that are to be packaged into the holding area `${`[D](https://docs.yoctoproject.org/ref-manual/variables.html#term-D)`}`. This task runs with the current working directory set to `${`[B](https://docs.yoctoproject.org/ref-manual/variables.html#term-B)`}`, which is the compilation directory. The [do_install](https://docs.yoctoproject.org/ref-manual/tasks.html#ref-tasks-install) task, as well as other tasks that either directly or indirectly depend on the installed files (e.g. [do_package](https://docs.yoctoproject.org/ref-manual/tasks.html#ref-tasks-package), [do_package_write_*](https://docs.yoctoproject.org/ref-manual/tasks.html#ref-tasks-package-write-deb), and [do_rootfs](https://docs.yoctoproject.org/ref-manual/tasks.html#ref-tasks-rootfs)), run under [fakeroot](https://docs.yoctoproject.org/overview-manual/concepts.html#fakeroot-and-pseudo).

<aside>
💡

Note

When installing files, be careful not to set the owner and group IDs of the installed files to unintended values. Some methods of copying files, notably when using the recursive `cp` command, can preserve the UID and/or GID of the original file, which is usually not what you want.

The `host-user-contaminated` QA check checks for files that probably have the wrong ownership.

Safe methods for installing files include the following:

- The `install` utility. This utility is the preferred method.
- The `cp` command with the `-no-preserve=ownership` option.
- The `tar` command with the `-no-same-owner` option.
</aside>

**In summary**, this `do_install` function does the following:

- Creates the necessary directory for **executable binaries** in the target file system.
- Installs the compiled `hello` binary into that directory with the appropriate permissions.

# version-agnostic part

A "version-agnostic part" of a recipe refers to sections of the recipe that do not depend on a specific version of the software being built. This can include shared configurations, common build steps, or patches that apply to multiple versions of the software.

Parts of the recipe such as `do_install`, `do_configure`, and even some variables can be written in a way that they apply regardless of the version. This means that only the version-specific elements (like the source URL or version numbers) need to be updated when a new version is released.

### Example:

A typical use case might be a recipe for a library that has different versions but has the same build process and install steps. You might define:

```bash
inherit autotools

# Version-specific settings
PV = "1.0.0"
SRC_URI = "http://example.com/library-${PV}.tar.gz"

do_configure() {
    # Common configure steps
    ./configure --prefix=${prefix}
}

do_install() {
    # Common install steps
    install -d ${D}${libdir}
    install -m 0755 libexample.so ${D}${libdir}
}
```

In this example, the `do_configure` and `do_install` functions can be reused across multiple versions by simply changing the `PV` and `SRC_URI` variables when new versions are released.

### Example:

```bash
SUMMARY = "GNU file archiving program"
HOMEPAGE = "https://www.gnu.org/software/tar/"
SECTION = "base"
SRC_URI = "${GNU_MIRROR}/tar/tar-${PV}.tar.bz2"
do_configure() { ... }
do_compile() { ... }
do_install() { ... }
```

1. **`SUMMARY`**:
    
    ```bash
    SUMMARY = "GNU file archiving program"
    ```
    
    - This variable provides a brief description of what the recipe is about. In this case, it describes `tar` as a file archiving program.

2. **`HOMEPAGE`**:
    
    ```bash
    HOMEPAGE = "<https://www.gnu.org/software/tar/>"
    ```
    
    - This variable specifies the URL of the official homepage for the `tar` software. It's useful for reference and for users to find more information about the software.

3. **`SECTION`**:
    
    ```bash
    SECTION = "base"
    ```
    
    - This categorizes the recipe within the overall package structure. Here, `tar` is categorized under "base," indicating that it's a fundamental utility.

4. **`SRC_URI`**:
    
    ```bash
    SRC_URI = "${GNU_MIRROR}/tar/tar-${PV}.tar.bz2"
    ```
    
    - This variable defines the location of the source code to be fetched.
    - `${GNU_MIRROR}` is likely a variable set elsewhere in the environment that points to the official GNU mirrors, ensuring that the recipe can fetch the correct source archive for the specified version.
    - `tar-${PV}.tar.bz2` indicates that the source archive is in the Bzip2 format, and `${PV}` is another variable (not shown here) that would hold the version number of the `tar` package.

5. **`do_configure()`**:
    
    ```bash
    do_configure() { ... }
    ```
    
    - This function defines the steps necessary to configure the build environment before compilation. The actual implementation would typically include commands to run the `./configure` script, which sets up the build according to the specified options and environment.

6. **`do_compile()`**:
    
    ```bash
    do_compile() { ... }
    ```
    
    - This function contains the commands to compile the source code into binaries. The implementation might use commands like `make` to build the project based on the configured settings.

7. **`do_install()`**:
    
    ```bash
    do_install() { ... }
    ```
    
    - This function specifies the steps to install the compiled binaries and other necessary files into the appropriate directories in the target file system. This often includes creating directories and copying files with the appropriate permissions.


### Summary

This recipe provides the necessary metadata and structure for building the GNU `tar` program within the Yocto Project. It includes essential information about the software, the source location, and placeholders for the configure, compile, and install steps. The actual implementation details for `do_configure`, `do_compile`, and `do_install` would be needed to complete the recipe and ensure the software builds and installs correctly.

```bash
require tar.inc
LICENSE = "GPL-2.0-only"
LIC_FILES_CHKSUM = \
"file://COPYING;md5=59530bdf33659b29e73d4adb9f9f6552"
SRC_URI += "file://avoid_heap_overflow.patch"
SRC_URI[md5sum] = "c6c4f1c075dbf0f75c29737faa58f290"
```

### Breakdown of the Recipe

1. **`require tar.inc`**:
    
    ```bash
    require tar.inc
    
    ```
    
    - This line indicates that the recipe is including the contents of another file, `tar.inc`. This is typically done to share common configuration, variables, or functions across multiple recipes.
    - By using `require`, the recipe can inherit settings defined in `tar.inc`, allowing for DRY (Don't Repeat Yourself) principles in recipe management.

2. **`LICENSE`**:
    
    ```bash
    LICENSE = "GPL-2.0-only"
    
    ```
    
    - This variable specifies the licensing terms under which the software is distributed. In this case, it indicates that the software is licensed under the GNU General Public License version 2.0, with no later versions allowed.
    - This is important for compliance and informing users about the usage rights associated with the software.

3. **`LIC_FILES_CHKSUM`**:
    
    ```bash
    LIC_FILES_CHKSUM = \\
    "file://COPYING;md5=59530bdf33659b29e73d4adb9f9f6552"
    
    ```
    
    - This variable provides a checksum for the license file included in the source.
    - The `file://COPYING` part indicates the location of the license file within the source package, and the `md5` checksum ensures that the file matches the expected contents. This is a way to verify that the correct license is present and has not been altered.

4. **`SRC_URI`**:
    
    ```bash
    SRC_URI += "file://avoid_heap_overflow.patch"
    
    ```
    
    - This line adds a patch file (`avoid_heap_overflow.patch`) to the source URI.
    - The `+=` operator appends this file to the existing list of sources defined in the `SRC_URI` variable (potentially defined in `tar.inc`).
    - The patch likely addresses a specific issue or vulnerability related to heap overflow, which may enhance the security or stability of the `tar` program.

5. **`SRC_URI[md5sum]`**:
    
    ```bash
    SRC_URI[md5sum] = "c6c4f1c075db0f75c29737faa58f290"
    
    ```
    
    - This line provides a checksum for the patch file added to `SRC_URI`.
    - The `md5sum` attribute specifies the expected MD5 checksum of the `avoid_heap_overflow.patch` file. This is used to verify that the patch has been downloaded correctly and has not been tampered with.


### Summary

This recipe extends the functionality and metadata of the base `tar` recipe by including specific licensing information, a license checksum for verification, and a patch to address a security issue. It leverages the `require` directive to include shared settings from `tar.inc`, ensuring consistency and maintainability in the Yocto project. The inclusion of checksums provides an additional layer of integrity checking for both the license file and the patch.

```bash
require tar.inc
LICENSE = "GPL-2.0-only"
LIC_FILES_CHKSUM = \
"file://COPYING;md5=59530bdf33659b29e73d4adb9f9f6552"
SRC_URI += "file://avoid_heap_overflow.patch"
SRC_URI[md5sum] = "c6c4f1c075dbf0f75c29737faa58f290"
```

1. **`require tar.inc`**:
    
    ```bash
    require tar.inc
    
    ```
    
    - This line includes the contents of `tar.inc`, allowing the current recipe to inherit variables and functions defined in that file. This is useful for sharing common configurations among multiple recipes.

2. **`LICENSE`**:
    
    ```bash
    LICENSE = "GPL-3.0-only"
    
    ```
    
    - This specifies the licensing terms for the software. In this case, it indicates that the software is licensed under the GNU General Public License version 3.0, with no later versions allowed. This is a more permissive license compared to GPL-2.0, providing broader rights for users.

3. **`LIC_FILES_CHKSUM`**:
    
    ```bash
    LIC_FILES_CHKSUM = \\
    "file://COPYING;md5=d32239bcb673463ab874e80d47fae504"
    
    ```
    
    - This variable provides a checksum for the license file that accompanies the software. The `file://COPYING` part points to the license file included in the source package, while the `md5` checksum is used to verify the integrity of the file. It ensures that the correct license is present and unchanged.

4. **`SRC_URI[md5sum]`**:
    
    ```bash
    SRC_URI[md5sum] = "2cee42a2ff4f1cd4f9298eeeb2264519"
    
    ```
    
    - This line provides an MD5 checksum for the entire source archive specified in `SRC_URI`. This is crucial for verifying that the source code downloaded matches the expected content and has not been tampered with.

### Summary

This recipe builds upon the base `tar` functionality defined in `tar.inc` and includes specific information related to licensing. The license is now set to GPL-3.0-only, indicating a different set of rights and responsibilities for users compared to the previous version that used GPL-2.0-only. The recipe also incorporates checksums for both the license file and the source archive to ensure the integrity and authenticity of the files being used. This helps maintain compliance and security in the software distribution process.

```bash
require tar.inc
LICENSE = "GPL-3.0-only"
LIC_FILES_CHKSUM = \
"file://COPYING;md5=d32239bcb673463ab874e80d47fae504"
SRC_URI[md5sum] = "2cee42a2ff4f1cd4f9298eeeb2264519"ile listed in the [SRC_URI](https://docs.yoctoproject.org/ref-manual/variables.html#term-SRC_URI)
variable. Additionally, you need to manually write the [do_compile](https://docs.yoctoproject.org/ref-manual/tasks.html#ref-tasks-compile)
and [do_install](https://docs.yoctoproject.org/ref-manual/tasks.html#ref-tasks-install) tasks. T
```

### Breakdown of the Recipe

1. **`require tar.inc`**:
    
    ```bash
    require tar.inc
    
    ```
    
    - This line includes the contents of another recipe or configuration file named `tar.inc`. This is a way to inherit common variables, settings, or functions defined in `tar.inc`, promoting reuse and maintainability across recipes.

2. **`LICENSE`**:
    
    ```bash
    LICENSE = "GPL-3.0-only"
    
    ```
    
    - This specifies the licensing for the software. In this case, it indicates that the software is licensed under the GNU General Public License version 3.0, with no allowance for later versions. This means that users have certain rights, such as the freedom to use, modify, and distribute the software, but they must do so under the same license.

3. **`LIC_FILES_CHKSUM`**:
    
    ```bash
    LIC_FILES_CHKSUM = \\
    "file://COPYING;md5=d32239bcb673463ab874e80d47fae504"
    
    ```
    
    - This variable provides a checksum for the license file that comes with the source code. The `file://COPYING` points to the license file (commonly named `COPYING` or `LICENSE` in source packages).
    - The `md5` checksum ensures the integrity of the license file, verifying that it matches the expected content and hasn’t been altered.

4. **`SRC_URI[md5sum]`**:
    
    ```bash
    SRC_URI[md5sum] = "2cee42a2ff4f1cd4f9298eeeb2264519"
    ```
    
    - This specifies an MD5 checksum for the source archive defined in the `SRC_URI` variable. This is critical for ensuring that the source code downloaded is the correct version and has not been tampered with during the download process.

### Summary

This recipe establishes the foundational elements for building a version of `tar` by including shared configurations from `tar.inc`. It specifies that the software is licensed under GPL-3.0-only, which governs how it can be used and distributed. The checksums for both the license file and the source archive ensure that the integrity and authenticity of the files are maintained, contributing to a secure and compliant software build process. This recipe is a good example of best practices in Yocto for managing licenses and verifying file integrity.
