#### ⚠ This page is just for your information and inspiration.

Grsecurity
----------

`GRKERNSEC`\

    If you say Y here, you will be able to configure many features
    that will enhance the security of your system.  It is highly
    recommended that you say Y here and read through the help
    for each option so that you fully understand the features and
    can evaluate their usefulness for your machine.

Configuration Method
--------------------

    Choose between automatic or custom configuration here.

### Automatic

`GRKERNSEC_CONFIG_AUTO`\

    If you choose this configuration method, you'll be able to answer a small
    number of simple questions about how you plan to use this kernel.
    The settings of grsecurity and PaX will be automatically configured for
    the highest commonly-used settings within the provided constraints.

    If you require additional configuration, custom changes can still be made
    from the "custom configuration" menu.

### Custom

`GRKERNSEC_CONFIG_CUSTOM`\

    If you choose this configuration method, you'll be able to configure all
    grsecurity and PaX settings manually.  Via this method, no options are
    automatically enabled.

    Take note that if menuconfig is exited with this configuration method
    chosen, you will not be able to use the automatic configuration methods
    without starting again with a kernel configuration with no grsecurity
    or PaX options specified inside.

Usage Type
----------

    Choose between server or desktop here.

### Server

`GRKERNSEC_CONFIG_SERVER`\

    Choose this option if you plan to use this kernel on a server.

### Desktop

`GRKERNSEC_CONFIG_DESKTOP`\

    Choose this option if you plan to use this kernel on a desktop.

Virtualization Type
-------------------

    Choose between no, guest, or host virtualization here.

### None

`GRKERNSEC_CONFIG_VIRT_NONE`\

    Choose this option if this kernel will be run on bare metal.

### Guest

`GRKERNSEC_CONFIG_VIRT_GUEST`\

    Choose this option if this kernel will be run as a VM guest.

### Host

`GRKERNSEC_CONFIG_VIRT_HOST`\

    Choose this option if this kernel will be run as a VM host.

Virtualization Hardware
-----------------------

    Choose between no or EPT/RVI hardware virtualization support here.

### EPT/RVI Processor Support

`GRKERNSEC_CONFIG_VIRT_EPT`\

    Choose this option if your CPU supports the EPT or RVI features of 2nd-gen
    hardware virtualization.  This allows for additional kernel hardening protections
    to operate without additional performance impact.

    To see if your Intel processor supports EPT, see:
    http://ark.intel.com/Products/VirtualizationTechnology
    (Most Core i3/5/7 support EPT)

    To see if your AMD processor supports RVI, see:
    http://support.amd.com/us/kbarticles/Pages/GPU120AMDRVICPUsHyperVWin8.aspx

### First-gen/No Hardware Virtualization

`GRKERNSEC_CONFIG_VIRT_SOFT`\

    Choose this option if you use an Atom/Pentium/Core 2 processor that either doesn't
    support hardware virtualization or doesn't support the EPT/RVI extensions.

Virtualization Software
-----------------------

    Choose between Xen, Xen PV, VMware, KVM, VirtualBox, or HyperV virtualization here.

### Paravirtualized Xen

`GRKERNSEC_CONFIG_VIRT_XEN_PV`\

    Choose this option if this kernel is running as a Paravirtualized Xen domU or dom0.
    KERNEXEC on both i386 and x86_64 and UDEREF on x86_64, as well as RANDKSTACK will
    not be enabled.

### PVHVM/HVM Xen

`GRKERNSEC_CONFIG_VIRT_XEN`\

    Choose this option if this kernel is running as a non-PV Xen domU or dom0.

### VMWare

`GRKERNSEC_CONFIG_VIRT_VMWARE`\

    Choose this option if this kernel is running as a VMWare guest or host.

### KVM

`GRKERNSEC_CONFIG_VIRT_KVM`\

    Choose this option if this kernel is running as a KVM guest or host.

### VirtualBox

`GRKERNSEC_CONFIG_VIRT_VIRTUALBOX`\

    Choose this option if this kernel is running as a VirtualBox guest or host.

### Hyper-V

`GRKERNSEC_CONFIG_VIRT_HYPERV`\

    Choose this option if this kernel is running as a Hyper-V guest.

Required Priorities
-------------------

    Choose between performance or security priorities here.

### Performance

`GRKERNSEC_CONFIG_PRIORITY_PERF`\

    Choose this option if performance is of highest priority for this deployment
    of grsecurity.  Features like kernel stack clearing, clearing of structures
    intended for userland, and freed memory sanitizing will be disabled.

### Security

`GRKERNSEC_CONFIG_PRIORITY_SECURITY`\

    Choose this option if security is of highest priority for this deployment of
    grsecurity.  Kernel stack clearing, clearing of structures intended for
    userland, and freed memory sanitizing will be enabled for this kernel.
    In a worst-case scenario, these features can introduce a 10% performance hit.

Default Special Groups
----------------------

### GID exempted from /proc restrictions

`GRKERNSEC_PROC_GID`\

    Setting this GID determines which group will be exempted from
    grsecurity's /proc restrictions, allowing users of the specified
    group  to view network statistics and the existence of other users'
    processes on the system.  This GID may also be chosen at boot time
    via "grsec_proc_gid=" on the kernel commandline.

### GID for TPE-untrusted users

`GRKERNSEC_TPE_UNTRUSTED_GID`\

    Setting this GID determines what group TPE restrictions will be
    *enabled* for.  If the sysctl option is enabled, a sysctl option
    with name "tpe_gid" is created.

### GID for TPE-trusted users

`GRKERNSEC_TPE_TRUSTED_GID`\

    Setting this GID determines what group TPE restrictions will be
    *disabled* for.  If the sysctl option is enabled, a sysctl option
    with name "tpe_gid" is created.

### GID for users with kernel-enforced SymlinksIfOwnerMatch

`GRKERNSEC_SYMLINKOWN_GID`\

    Setting this GID determines what group kernel-enforced
    SymlinksIfOwnerMatch will be enabled for.  If the sysctl option
    is enabled, a sysctl option with name "symlinkown_gid" is created.

Customize Configuration
-----------------------

### PaX

#### Enable various PaX features

`PAX`\

    This allows you to enable various PaX features.  PaX adds
    intrusion prevention mechanisms to the kernel that reduce
    the risks posed by exploitable memory corruption bugs.

#### PaX Control

##### Support soft mode

`PAX_SOFTMODE`\

    Enabling this option will allow you to run PaX in soft mode, that
    is, PaX features will not be enforced by default, only on executables
    marked explicitly.  You must also enable PT_PAX_FLAGS or XATTR_PAX_FLAGS
    support as they are the only way to mark executables for soft mode use.

    Soft mode can be activated by using the "pax_softmode=1" kernel command
    line option on boot.  Furthermore you can control various PaX features
    at runtime via the entries in /proc/sys/kernel/pax.

##### Use legacy ELF header marking

`PAX_EI_PAX`\

    Enabling this option will allow you to control PaX features on
    a per executable basis via the 'chpax' utility available at
    http://pax.grsecurity.net/.  The control flags will be read from
    an otherwise reserved part of the ELF header.  This marking has
    numerous drawbacks (no support for soft-mode, toolchain does not
    know about the non-standard use of the ELF header) therefore it
    has been deprecated in favour of PT_PAX_FLAGS and XATTR_PAX_FLAGS
    support.

    Note that if you enable PT_PAX_FLAGS or XATTR_PAX_FLAGS marking
    support as well, they will override the legacy EI_PAX marks.

    If you enable none of the marking options then all applications
    will run with PaX enabled on them by default.

##### Use ELF program header marking

`PAX_PT_PAX_FLAGS`\

    Enabling this option will allow you to control PaX features on
    a per executable basis via the 'paxctl' utility available at
    http://pax.grsecurity.net/.  The control flags will be read from
    a PaX specific ELF program header (PT_PAX_FLAGS).  This marking
    has the benefits of supporting both soft mode and being fully
    integrated into the toolchain (the binutils patch is available
    from http://pax.grsecurity.net).

    Note that if you enable the legacy EI_PAX marking support as well,
    the EI_PAX marks will be overridden by the PT_PAX_FLAGS marks.

    If you enable both PT_PAX_FLAGS and XATTR_PAX_FLAGS support then you
    must make sure that the marks are the same if a binary has both marks.

    If you enable none of the marking options then all applications
    will run with PaX enabled on them by default.

##### Use filesystem extended attributes marking

`PAX_XATTR_PAX_FLAGS`\

    Enabling this option will allow you to control PaX features on
    a per executable basis via the 'setfattr' utility.  The control
    flags will be read from the user.pax.flags extended attribute of
    the file.  This marking has the benefit of supporting binary-only
    applications that self-check themselves (e.g., skype) and would
    not tolerate chpax/paxctl changes.  The main drawback is that
    extended attributes are not supported by some filesystems (e.g.,
    isofs, udf, vfat) so copying files through such filesystems will
    lose the extended attributes and these PaX markings.

    Note that if you enable the legacy EI_PAX marking support as well,
    the EI_PAX marks will be overridden by the XATTR_PAX_FLAGS marks.

    If you enable both PT_PAX_FLAGS and XATTR_PAX_FLAGS support then you
    must make sure that the marks are the same if a binary has both marks.

    If you enable none of the marking options then all applications
    will run with PaX enabled on them by default.

##### MAC system integration

    Mandatory Access Control systems have the option of controlling
    PaX flags on a per executable basis, choose the method supported
    by your particular system.

    - "none": if your MAC system does not interact with PaX,
    - "direct": if your MAC system defines pax_set_initial_flags() itself,
    - "hook": if your MAC system uses the pax_set_initial_flags_func callback.

    NOTE: this option is for developers/integrators only.

###### none

`PAX_NO_ACL_FLAGS`\

###### direct

`PAX_HAVE_ACL_FLAGS`\

###### hook

`PAX_HOOK_ACL_FLAGS`\

#### Non-executable pages

##### Enforce non-executable pages

`PAX_NOEXEC`\

    By design some architectures do not allow for protecting memory
    pages against execution or even if they do, Linux does not make
    use of this feature.  In practice this means that if a page is
    readable (such as the stack or heap) it is also executable.

    There is a well known exploit technique that makes use of this
    fact and a common programming mistake where an attacker can
    introduce code of his choice somewhere in the attacked program's
    memory (typically the stack or the heap) and then execute it.

    If the attacked program was running with different (typically
    higher) privileges than that of the attacker, then he can elevate
    his own privilege level (e.g. get a root shell, write to files for
    which he does not have write access to, etc).

    Enabling this option will let you choose from various features
    that prevent the injection and execution of 'foreign' code in
    a program.

    This will also break programs that rely on the old behaviour and
    expect that dynamically allocated memory via the malloc() family
    of functions is executable (which it is not).  Notable examples
    are the XFree86 4.x server, the java runtime and wine.

##### Paging based non-executable pages

`PAX_PAGEEXEC`\

    This implementation is based on the paging feature of the CPU.

    On alpha, avr32, ia64, parisc, sparc, sparc64, x86_64 and i386
    with hardware non-executable bit support there is no performance
    impact, on ppc the impact is negligible.

    Note that several architectures require various emulations due to
    badly designed userland ABIs, this will cause a performance impact
    but will disappear as soon as userland is fixed.  For example, ppc
    userland MUST have been built with secure-plt by a recent toolchain.

##### Segmentation based non-executable pages

`PAX_SEGMEXEC`\

    This implementation is based on the segmentation feature of the
    CPU and has a very small performance impact, however applications
    will be limited to a 1.5 GB address space instead of the normal
    3 GB.

##### Emulate trampolines

`PAX_EMUTRAMP`\

    There are some programs and libraries that for one reason or
    another attempt to execute special small code snippets from
    non-executable memory pages.  Most notable examples are the
    signal handler return code generated by the kernel itself and
    the GCC trampolines.

    If you enabled CONFIG_PAX_PAGEEXEC or CONFIG_PAX_SEGMEXEC then
    such programs will no longer work under your kernel.

    As a remedy you can say Y here and use the 'chpax' or 'paxctl'
    utilities to enable trampoline emulation for the affected programs
    yet still have the protection provided by the non-executable pages.

    On parisc you MUST enable this option and EMUSIGRT as well, otherwise
    your system will not even boot.

    Alternatively you can say N here and use the 'chpax' or 'paxctl'
    utilities to disable CONFIG_PAX_PAGEEXEC and CONFIG_PAX_SEGMEXEC
    for the affected files.

    NOTE: enabling this feature *may* open up a loophole in the
    protection provided by non-executable pages that an attacker
    could abuse.  Therefore the best solution is to not have any
    files on your system that would require this option.  This can
    be achieved by not using libc5 (which relies on the kernel
    signal handler return code) and not using or rewriting programs
    that make use of the nested function implementation of GCC.
    Skilled users can just fix GCC itself so that it implements
    nested function calls in a way that does not interfere with PaX.

##### Automatically emulate sigreturn trampolines

`PAX_EMUSIGRT`\

    Enabling this option will have the kernel automatically detect
    and emulate signal return trampolines executing on the stack
    that would otherwise lead to task termination.

    This solution is intended as a temporary one for users with
    legacy versions of libc (libc5, glibc 2.0, uClibc before 0.9.17,
    Modula-3 runtime, etc) or executables linked to such, basically
    everything that does not specify its own SA_RESTORER function in
    normal executable memory like glibc 2.1+ does.

    On parisc you MUST enable this option, otherwise your system will
    not even boot.

    NOTE: this feature cannot be disabled on a per executable basis
    and since it *does* open up a loophole in the protection provided
    by non-executable pages, the best solution is to not have any
    files on your system that would require this option.

##### Restrict mprotect()

`PAX_MPROTECT`\

    Enabling this option will prevent programs from
     - changing the executable status of memory pages that were
       not originally created as executable,
     - making read-only executable pages writable again,
     - creating executable pages from anonymous memory,
     - making read-only-after-relocations (RELRO) data pages writable again.

    You should say Y here to complete the protection provided by
    the enforcement of non-executable pages.

    NOTE: you can use the 'chpax' or 'paxctl' utilities to control
    this feature on a per file basis.

##### Use legacy/compat protection demoting (read help)

`PAX_MPROTECT_COMPAT`\

    The current implementation of PAX_MPROTECT denies RWX allocations/mprotects
    by sending the proper error code to the application.  For some older
    userland, this can cause problems with applications that assume such
    allocations will not be prevented by PaX or SELinux and other access
    control systems and have no fallback mechanisms.  For modern distros,
    this option should generally be set to 'N'.

##### Allow ELF text relocations (read help)

`PAX_ELFRELOCS`\

    Non-executable pages and mprotect() restrictions are effective
    in preventing the introduction of new executable code into an
    attacked task's address space.  There remain only two venues
    for this kind of attack: if the attacker can execute already
    existing code in the attacked task then he can either have it
    create and mmap() a file containing his code or have it mmap()
    an already existing ELF library that does not have position
    independent code in it and use mprotect() on it to make it
    writable and copy his code there.  While protecting against
    the former approach is beyond PaX, the latter can be prevented
    by having only PIC ELF libraries on one's system (which do not
    need to relocate their code).  If you are sure this is your case,
    as is the case with all modern Linux distributions, then leave
    this option disabled.  You should say 'n' here.

##### Allow ELF ET\_EXEC text relocations

`PAX_ETEXECRELOCS`\

    On some architectures there are incorrectly created applications
    that require text relocations and would not work without enabling
    this option.  If you are an alpha, ia64 or parisc user, you should
    enable this option and disable it once you have made sure that
    none of your applications need it.

##### Automatically emulate ELF PLT

`PAX_EMUPLT`\

    Enabling this option will have the kernel automatically detect
    and emulate the Procedure Linkage Table entries in ELF files.
    On some architectures such entries are in writable memory, and
    become non-executable leading to task termination.  Therefore
    it is mandatory that you enable this option on alpha, parisc,
    sparc and sparc64, otherwise your system would not even boot.

    NOTE: this feature *does* open up a loophole in the protection
    provided by the non-executable pages, therefore the proper
    solution is to modify the toolchain to produce a PLT that does
    not need to be writable.

##### Emulate old glibc resolver stub

`PAX_DLRESOLVE`\

    This option is needed if userland has an old glibc (before 2.4)
    that puts a 'save' instruction into the runtime generated resolver
    stub that needs special emulation.

##### Enforce non-executable kernel pages

`PAX_KERNEXEC`\

    This is the kernel land equivalent of PAGEEXEC and MPROTECT,
    that is, enabling this option will make it harder to inject
    and execute 'foreign' code in kernel memory itself.

    Note that on amd64, CONFIG_EFI enabled with "efi=old_map" on
    the kernel command-line will result in an RWX physical map.

    Likewise, the EFI runtime services are necessarily mapped as
    RWX.  If CONFIG_EFI is enabled on an EFI-capable system, it
    is recommended that you boot with "noefi" on the kernel
    command-line if possible to eliminate the mapping.

##### Code Pointer Instrumentation Method

    KERNEXEC on amd64 is not as secure as its i386 variant due to the
    lack of certain processor features.  This option can bring back some
    of the security by forcing all code pointers at runtime to fall into
    kernel memory.  This is achieved via compile time instrumentation of
    all code pointer dereferences (indirect calls and function returns).

    While there are alternative mechanisms (SMEP, UDEREF) that can achieve
    the same or even more, they also have their own drawbacks in terms of
    performance impact and/or being processor dependent so this feature
    offers a choice by having a low performance impact and being processor
    independent.

    If you enabled RAP (see PAX_RAP) and have an amd64 processor that does
    not support SMEP then you must also enable a KERNEXEC code pointer
    instrumentation method.

    Note that binary modules cannot be instrumented by this approach.

    Note that the implementation requires a gcc with plugin support,
    i.e., gcc 4.5 or newer.  You may need to install the supporting
    headers explicitly in addition to the normal gcc package.

###### none

`PAX_KERNEXEC_PLUGIN_METHOD_NONE`\

    In case your processor supports Supervisor Mode Execution
    Prevention (SMEP) you should choose this option to disable
    compile time instrumentation.

    Note that the kernel will refuse to boot if SMEP is not
    supported by the processor or is disabled on the kernel
    command line.

###### bts

`PAX_KERNEXEC_PLUGIN_METHOD_BTS`\

    This method is compatible with binary only modules but has
    a higher runtime overhead.

##### Minimum amount of memory reserved for module code

`PAX_KERNEXEC_MODULE_TEXT`\

    Due to implementation details the kernel must reserve a fixed
    amount of memory for runtime allocated code (such as modules)
    at compile time that cannot be changed at runtime.  Here you
    can specify the minimum amount in MB that will be reserved.
    Due to the same implementation details this size will always
    be rounded up to the next 2/4 MB boundary (depends on PAE) so
    the actually available memory for runtime allocated code will
    usually be more than this minimum.

    The default 4 MB should be enough for most users but if you have
    an excessive number of modules (e.g., most distribution configs
    compile many drivers as modules) or use huge modules such as
    nvidia's kernel driver, you will need to adjust this amount.
    A good rule of thumb is to look at your currently loaded kernel
    modules and add up their sizes.

#### Address Space Layout Randomization

##### Address Space Layout Randomization

`PAX_ASLR`\

    Many if not most exploit techniques rely on the knowledge of
    certain addresses in the attacked program.  The following options
    will allow the kernel to apply a certain amount of randomization
    to specific parts of the program thereby forcing an attacker to
    guess them in most cases.  Any failed guess will most likely crash
    the attacked program which allows the kernel to detect such attempts
    and react on them.  PaX itself provides no reaction mechanisms,
    instead it is strongly encouraged that you make use of grsecurity's
    (http://www.grsecurity.net/) built-in crash detection features or
    develop one yourself.

    By saying Y here you can choose to randomize the following areas:
     - top of the task's kernel stack
     - top of the task's userland stack
     - base address for mmap() requests that do not specify one
       (this includes all libraries)
     - base address of the main executable

    It is strongly recommended to say Y here as address space layout
    randomization has negligible impact on performance yet it provides
    a very effective protection.

    NOTE: you can use the 'chpax' or 'paxctl' utilities to control
    this feature on a per file basis.

##### Randomize kernel stack base

`PAX_RANDKSTACK`\

    By saying Y here the kernel will randomize every task's kernel
    stack on every system call.  This will not only force an attacker
    to guess it but also prevent him from making use of possible
    leaked information about it.

    Since the kernel stack is a rather scarce resource, randomization
    may cause unexpected stack overflows, therefore you should very
    carefully test your system.  Note that once enabled in the kernel
    configuration, this feature cannot be disabled on a per file basis.

##### Randomize user stack and mmap() bases

`PAX_RANDMMAP`\

    By saying Y here the kernel will randomize every task's userland
    stack and use a randomized base address for mmap() requests that
    do not specify one themselves.

    The stack randomization is done in two steps where the second
    one may apply a big amount of shift to the top of the stack and
    cause problems for programs that want to use lots of memory (more
    than 2.5 GB if SEGMEXEC is not active, or 1.25 GB when it is).

    As a result of mmap randomization all dynamically loaded libraries
    will appear at random addresses and therefore be harder to exploit
    by a technique where an attacker attempts to execute library code
    for his purposes (e.g. spawn a shell from an exploited program that
    is running at an elevated privilege level).

    Furthermore, if a program is relinked as a dynamic ELF file, its
    base address will be randomized as well, completing the full
    randomization of the address space layout.  Attacking such programs
    becomes a guess game.  You can find an example of doing this at
    http://pax.grsecurity.net/et_dyn.tar.gz and practical samples at
    http://www.grsecurity.net/grsec-gcc-specs.tar.gz .

    NOTE: you can use the 'chpax' or 'paxctl' utilities to control this
    feature on a per file basis.

#### Miscellaneous hardening features

##### Sanitize all freed memory

`PAX_MEMORY_SANITIZE`\

    By saying Y here the kernel will erase memory pages and slab objects
    as soon as they are freed.  This in turn reduces the lifetime of data
    stored in them, making it less likely that sensitive information such
    as passwords, cryptographic secrets, etc stay in memory for too long.

    This is especially useful for programs whose runtime is short, long
    lived processes and the kernel itself benefit from this as long as
    they ensure timely freeing of memory that may hold sensitive
    information.

    A nice side effect of the sanitization of slab objects is the
    reduction of possible info leaks caused by padding bytes within the
    leaky structures.  Use-after-free bugs for structures containing
    pointers can also be detected as dereferencing the sanitized pointer
    will generate an access violation.

    The tradeoff is performance impact, on a single CPU system kernel
    compilation sees a 3% slowdown, other systems and workloads may vary
    and you are advised to test this feature on your expected workload
    before deploying it.

    The slab sanitization feature excludes a few slab caches per default
    for performance reasons.  To extend the feature to cover those as
    well, pass "pax_sanitize_slab=full" as kernel command line parameter.

    To reduce the performance penalty by sanitizing pages only, albeit
    limiting the effectiveness of this feature at the same time, slab
    sanitization can be disabled with the kernel command line parameter
    "pax_sanitize_slab=off".

    Note that this feature does not protect data stored in live pages,
    e.g., process memory swapped to disk may stay there for a long time.

##### Sanitize kernel stack

`PAX_MEMORY_STACKLEAK`\

    By saying Y here the kernel will erase the kernel stack before it
    returns from a system call.  This in turn reduces the information
    that a kernel stack leak bug can reveal.

    Note that such a bug can still leak information that was put on
    the stack by the current system call (the one eventually triggering
    the bug) but traces of earlier system calls on the kernel stack
    cannot leak anymore.

    The tradeoff is performance impact: on a single CPU system kernel
    compilation sees a 1% slowdown, other systems and workloads may vary
    and you are advised to test this feature on your expected workload
    before deploying it.

    Note that the full feature requires a gcc with plugin support,
    i.e., gcc 4.5 or newer.  You may need to install the supporting
    headers explicitly in addition to the normal gcc package.  Using
    older gcc versions means that functions with large enough stack
    frames may leave uninitialized memory behind that may be exposed
    to a later syscall leaking the stack.

##### Forcibly initialize local variables copied to userland

`PAX_MEMORY_STRUCTLEAK`\

    By saying Y here the kernel will zero initialize some local
    variables that are going to be copied to userland.  This in
    turn prevents unintended information leakage from the kernel
    stack should later code forget to explicitly set all parts of
    the copied variable.

    The tradeoff is less performance impact than PAX_MEMORY_STACKLEAK
    at a much smaller coverage.

    Note that the implementation requires a gcc with plugin support,
    i.e., gcc 4.5 or newer.  You may need to install the supporting
    headers explicitly in addition to the normal gcc package.

##### Prevent invalid userland pointer dereference

`PAX_MEMORY_UDEREF`\

    By saying Y here the kernel will be prevented from dereferencing
    userland pointers in contexts where the kernel expects only kernel
    pointers.  This is both a useful runtime debugging feature and a
    security measure that prevents exploiting a class of kernel bugs.

    Another purpose of this feature is to prevent userland from accessing
    kernel memory during speculative execution (CVE-2017-5754, Meltdown).
    For best performance on i386 all userland should be recompiled with
    -mno-tls-direct-seg-refs and libc should be a 'nosegneg' variant.

    Note that when enabling this feature on guest kernels running on old
    CPUs without hardware virtualization support there may be a huge
    slowdown therefore you should not enable this feature for kernels
    meant to run in such environments.

    On X86_64 the kernel will make use of PCID support when available
    (Intel's Westmere, Sandy Bridge, etc).

##### Prevent various kernel object reference counter overflows

`PAX_REFCOUNT`\

    By saying Y here the kernel will detect and prevent overflowing
    various (but not all) kinds of object reference counters.  Such
    overflows can normally occur due to bugs only and are often, if
    not always, exploitable.

    The tradeoff is that data structures protected by an overflowed
    refcount will never be freed and therefore will leak memory.  Note
    that this leak also happens even without this protection but in
    that case the overflow can eventually trigger the freeing of the
    data structure while it is still being used elsewhere, resulting
    in the exploitable situation that this feature prevents.

    Since this has a negligible performance impact, you should enable
    this feature.

##### Harden memory copies between kernel and userland

`PAX_USERCOPY`\

    By saying Y here the kernel will enforce the size of heap objects
    when they are copied in either direction between the kernel and
    userland, even if only a part of the heap object is copied.

    Specifically, this checking prevents information leaking from the
    kernel heap during kernel to userland copies (if the kernel heap
    object is otherwise fully initialized) and prevents kernel heap
    overflows during userland to kernel copies.

    Note that the current implementation provides the strictest bounds
    checks for the SLUB allocator.

    Enabling this option also enables per-slab cache protection against
    data in a given cache being copied into/out of via userland
    accessors.  Though the whitelist of regions will be reduced over
    time, it notably protects important data structures like task structs.

    If frame pointers are enabled on x86, this option will also restrict
    copies into and out of the kernel stack to local variables within a
    single frame.

    Since this has a negligible performance impact, you should enable
    this feature.

##### Automatically constify eligible structures

`PAX_CONSTIFY_PLUGIN`\

    By saying Y here the compiler will automatically constify a class
    of types that contain only function pointers.  This reduces the
    kernel's attack surface and also produces a better memory layout.

    Note that the implementation requires a gcc with plugin support,
    i.e., gcc 4.5 or newer.  You may need to install the supporting
    headers explicitly in addition to the normal gcc package.

    Note that if some code really has to modify constified variables
    then the source code will have to be patched to allow it.  Examples
    can be found in PaX itself (the no_const attribute) and for some
    out-of-tree modules at http://www.grsecurity.net/~paxguy1/ .

##### Report code regions instrumented for writing to constified data

`PAX_CONSTIFY_PLUGIN_VERBOSE`\

    Print out the start and end of code regions that are enabled to
    modify otherwise read-only memory.

##### Prevent various integer overflows in function size parameters

`PAX_SIZE_OVERFLOW`\

    By saying Y here the kernel recomputes expressions of function
    arguments marked by a size_overflow attribute with double integer
    precision (DImode/TImode for 32/64 bit integer types).

    If the recomputed argument does not fit the original type then the
    event is logged.  If the pax_size_overflow_report_only parameter is
    NOT passed on the kernel command line at boot then the triggering
    process will also be killed.  Alternatively, the pax_so_report_only
    parameter provides finer grained control over whether all events
    should cause the triggering process to be killed or only those that
    were not detected by the extra instrumentation (for more details
    see PAX_SIZE_OVERFLOW_EXTRA).

    Homepage: https://github.com/ephox-gcc-plugins/size_overflow
    Blog: http://forums.grsecurity.net/viewtopic.php?f=7&t=3043

    Note that the implementation requires a gcc with plugin support,
    i.e., gcc 4.5 or newer.  You may need to install the supporting
    headers explicitly in addition to the normal gcc package.

##### Increase coverage of size overflow checking

`PAX_SIZE_OVERFLOW_EXTRA`\

    By saying Y here the kernel will instrument more size argument
    calculations that off-line static analysis tracked back through
    indirect function calls, structure fields and global variables.

    This greatly increases coverage and thus security however there
    are also more false positives and the performance impact is higher
    as well.

##### Log missing size overflow hash table entries

`PAX_SIZE_OVERFLOW_HASHGEN`\

    By saying Y here the size overflow gcc plugin will perform data
    flow analysis during the kernel build process to detect new code
    that affects size calculations.  New discoveries are logged to
    to stderr for later integration into the size overflow plugin's
    metadata hash tables.  Unless you are developing this plugin or
    are otherwise interested in instrumenting new code, you should
    say N here.

##### Free more kernel memory after init

`PAX_INITIFY`\

    The kernel has a mechanism to free up code and data memory that is
    only used during kernel or module initialization.  Enabling this
    feature will teach the compiler to find more such code and data
    that can be freed after initialization.

##### Free more kernel memory after init (verbose mode)

`PAX_INITIFY_VERBOSE`\

    Print all initified strings and all functions which should be
    __init/__exit.

    Note that the candidates identified for __init/__exit markings
    depend on the current kernel configuration and thus should be
    verified manually before the source code is patched.

##### Generate some entropy during boot and runtime

`PAX_LATENT_ENTROPY`\

    By saying Y here the kernel will instrument some kernel code to
    extract some entropy from both original and artificially created
    program state.  This will help especially embedded systems where
    there is little 'natural' source of entropy normally.  The cost
    is some slowdown of the boot process (about 0.5%) and fork and
    irq processing.

    When pax_extra_latent_entropy is passed on the kernel command line,
    entropy will be extracted from up to the first 4GB of RAM while the
    runtime memory allocator is being initialized.  This costs even more
    slowdown of the boot process.

    Note that the implementation requires a gcc with plugin support,
    i.e., gcc 4.5 or newer.  You may need to install the supporting
    headers explicitly in addition to the normal gcc package.

    Note that entropy extracted this way is not cryptographically
    secure!

##### Prevent code reuse attacks

`PAX_RAP`\

    By saying Y here the kernel will check indirect control transfers
    in order to detect and prevent attacks that try to hijack control
    flow by overwriting code pointers.

    If you have an amd64 processor that does not support SMEP then you
    must also enable a KERNEXEC code pointer instrumentation method
    (see PAX_KERNEXEC_PLUGIN).

    Note that binary modules cannot be instrumented by this approach.

    Note that the implementation requires a gcc with plugin support,
    i.e., gcc 4.5 or newer.  You may need to install the supporting
    headers explicitly in addition to the normal gcc package.

##### Forward edge defense (deterministic)

`PAX_RAP_CALL`\

##### Forward edge defense instrumentation method

    Some processors are susceptible to certain speculative execution
    based attacks.  This option selects the instrumentation kind for
    the forward edge defense with different tradeoffs between security,
    performance and information about control flow violations.

    Note that each instrumentation method for the forward edge defense
    provides the same security level for normal (non-speculative)
    execution, they differ only under speculative execution on affected
    processors.

    Kernels built to run purely on AMD systems with an updated
    microcode and CONFIG_RETPOLINE enabled should enable the "callabort"
    method for improved performance and reporting ability.  Such systems
    are not able to perform speculative ROP attacks in the kernel.

###### callabort

`PAX_RAP_CALL_ABORT`\

    This instrumentation method provides the best performance
    and is able to provide full details for control flow
    violations but it may be susceptible to speculative
    execution based attacks under certain circumstances.

###### callnospec

`PAX_RAP_CALL_NOSPEC`\

    This instrumentation is safe from speculation based attacks
    but has a worse performance impact and provides only partial
    details for control flow violations (the unintended, possibly
    hijacked, control flow target may not be discoverable).

    Kernels built to run purely on AMD systems with an updated
    microcode and CONFIG_RETPOLINE enabled should not enable this
    option to benefit from improved performance and reporting
    ability.  Such systems are not able to speculatively perform
    ROP attacks in the kernel.

    Note that this feature requires gcc 4.8 or newer.

##### Backward edge defense (deterministic)

`PAX_RAP_RET`\

##### Backward edge defense (probabilistic)

`PAX_RAP_XOR`\

##### Automatically protect kernel code vulnerable to Spectre v1

`PAX_RESPECTRE_PLUGIN`\

    By saying Y here the compiler will automatically find and protect
    kernel code that may be vulnerable to CVE-2017-5753 (Spectre v1).
    The defense ensures that speculative array accesses use either
    a properly bounded or non-speculated index.  There are two defense
    types: index masking and fencing.  The former is preferred due to
    its negligible performance impact but it is not always feasible to
    use it so fencing is used in such cases.

    While the performance impact of the instrumentation should be mostly
    negligible (fencing is rarely used and even then its impact is quite
    processor dependent), you should measure it on your particular
    workload before deployment.

    Note that the implementation requires a gcc with plugin support,
    i.e., gcc 4.5 or newer.  You may need to install the supporting
    headers explicitly in addition to the normal gcc package.

##### Protect more kernel code vulnerable to Spectre v1

`PAX_RESPECTRE_PLUGIN_LOOPINDEX`\

    By saying Y here the compiler will protect more kernel code from
    Spectre v1 attacks where the potentially vulnerable instances depend
    on a loop index variable and speculative loop overexecution.  In such
    cases the processor can speculatively execute a loop more times than
    the intended loop count which can result in speculative uses of
    out-of-bound pointers and other invalid data.

    As the number of such cases is over 5 times of the 'normal' Spectre
    v1 instrumentation, the performance impact needs to be carefully
    measured before enabling this option.

##### Automatically protect kernel code vulnerable to Spectre v4

`PAX_RESPECTRE_PLUGIN_SSB`\

    By saying Y here the compiler will automatically find and protect
    kernel code that may be vulnerable to CVE-2018-3639 (Spectre v4).
    The defense is fencing that ensures that speculative loads of an
    array index variable do not bypass earlier stores to the index.

    Note that this defense does not apply to user programs therefore
    if you want to protect them as well you must enable some form of
    the SSBD defense instead, see the kernel documentation about
    'spec_store_bypass_disable'.

    Note that in order to reduce performance impact, the static analysis
    is specifically tailored to finding problematic stores and loads
    inside the same function only.  Nevertheless, you should measure its
    performance impact before deployment and also compare it to that of
    the full SSBD defense which also protects the kernel.

##### Protect more kernel code vulnerable to Spectre v4

`PAX_RESPECTRE_PLUGIN_SSB_ALL`\

    By saying Y here the compiler will protect more kernel code from
    Spectre v4 attacks where the potentially vulnerable instances can
    cross function boundaries.

    As the number of such cases is about 25 times of the 'normal' Spectre
    v4 instrumentation, the performance impact needs to be carefully
    measured before enabling this option.

##### Report code found to be potentially vulnerable to Spectre v1/v4

`PAX_RESPECTRE_PLUGIN_VERBOSE`\

    Print out statements deemed vulnerable to Spectre v1/v4 along with
    the chosen defense type during compilation.

##### Convert k\*alloc allocations into their own slabs

`PAX_AUTOSLAB_PLUGIN`\

    Convert generic memory allocations of candidate types into type
    specific slab allocations.  This can help reduce memory fragmentation
    and will stop the exploit technique that relies on type confusion
    between objects of different types that would otherwise be allocated
    from the same generic slab.

    Note that autoslabs with live objects created by a kernel module
    will not be destroyed when unloading the module, resulting in a small
    memory leak on such module unloads.  Live objects at module unload
    time will be logged; in most instances, this will point to real memory
    leak bugs in the kernel.

    Note that this feature requires gcc 4.6 or newer.

##### Convert small k\*alloc allocations to local variables

`PAX_AUTOSLAB_PLUGIN_AUTOSTACK`\

    Some generic memory allocations are equivalent to and thus can be
    converted to a local variable when they meet certain criteria.

##### Report autoslab decisions

`PAX_AUTOSLAB_PLUGIN_VERBOSE`\

    Print out when an autoslab conversion took place or why it did not.

##### Report some potential NULL pointer dereferences

`PAX_REPORT_NULL_DEREF`\

    Report some NULL pointer dereferences that gcc was unable to eliminate
    during optimization.  Note that these may or may not be real bugs but
    are worth a look regardless as the code should at least be refactored.

### Memory Protections

#### Deny reading/writing to /dev/kmem, /dev/mem, and /dev/port

`GRKERNSEC_KMEM`\

    If you say Y here, /dev/kmem and /dev/mem won't be allowed to
    be written to or read from to modify or leak the contents of the running
    kernel.  /dev/port will also not be allowed to be opened, writing to
    /dev/cpu/*/msr will be prevented, and support for kexec will be removed.
    If you have module support disabled, enabling this will close up several
    ways that are currently used to insert malicious code into the running
    kernel.

    Even with this feature enabled, we still highly recommend that
    you use the RBAC system, as it is still possible for an attacker to
    modify the running kernel through other more obscure methods.

    Enabling this feature will prevent the "cpupower" and "powertop" tools
    from working and excludes debugfs from being compiled into the kernel.

    It is highly recommended that you say Y here if you meet all the
    conditions above.

#### Restrict VM86 mode

`GRKERNSEC_VM86`\

    If you say Y here, only processes with CAP_SYS_RAWIO will be able to
    make use of a special execution mode on 32bit x86 processors called
    Virtual 8086 (VM86) mode.  XFree86 may need vm86 mode for certain
    video cards and will still work with this option enabled.  The purpose
    of the option is to prevent exploitation of emulation errors in
    virtualization of vm86 mode like the one discovered in VMWare in 2009.
    Nearly all users should be able to enable this option.

#### Disable privileged I/O

`GRKERNSEC_IO`\
Related sysctl variables:\
:`kernel.grsecurity.disable_priv_io`

    If you say Y here, all ioperm and iopl calls will return an error.
    Ioperm and iopl can be used to modify the running kernel.
    Unfortunately, some programs need this access to operate properly,
    the most notable of which are XFree86 and hwclock.  hwclock can be
    remedied by having RTC support in the kernel, so real-time 
    clock support is enabled if this option is enabled, to ensure 
    that hwclock operates correctly.  If hwclock still does not work,
    either update udev or symlink /dev/rtc to /dev/rtc0.

    If you're using XFree86 or a version of Xorg from 2012 or earlier,
    you may not be able to boot into a graphical environment with this
    option enabled.  In this case, you should use the RBAC system instead.

#### Harden BPF interpreter

`GRKERNSEC_BPF_HARDEN`\

    Enabling this option will automatically configure the kernel's BPF
    JIT with the most secure settings.

    If you're using KERNEXEC, it's recommended that you enable this option
    to supplement the hardening of the kernel.

#### Disable unprivileged PERF\_EVENTS usage by default

`GRKERNSEC_PERF_HARDEN`\

    If you say Y here, the range of acceptable values for the
    /proc/sys/kernel/perf_event_paranoid sysctl will be expanded to allow and
    default to a new value: 3.  When the sysctl is set to this value, no
    unprivileged use of the PERF_EVENTS syscall interface will be permitted.

    Though PERF_EVENTS can be used legitimately for performance monitoring
    and low-level application profiling, it is forced on regardless of
    configuration, has been at fault for several vulnerabilities, and
    creates new opportunities for side channels and other information leaks.

    This feature puts PERF_EVENTS into a secure default state and permits
    the administrator to change out of it temporarily if unprivileged
    application profiling is needed.

#### Insert random gaps between thread stacks

`GRKERNSEC_RAND_THREADSTACK`\

    If you say Y here, a random-sized gap will be enforced between allocated
    thread stacks.  Glibc's NPTL and other threading libraries that
    pass MAP_STACK to the kernel for thread stack allocation are supported.
    The implementation currently provides 8 bits of entropy for the gap.

    Many distributions do not compile threaded remote services with the
    -fstack-check argument to GCC, causing the variable-sized stack-based
    allocator, alloca(), to not probe the stack on allocation.  This
    permits an unbounded alloca() to skip over any guard page and potentially
    modify another thread's stack reliably.  An enforced random gap
    reduces the reliability of such an attack and increases the chance
    that such a read/write to another thread's stack instead lands in
    an unmapped area, causing a crash and triggering grsecurity's
    anti-bruteforcing logic.

#### Harden ASLR against information leaks and entropy reduction

`GRKERNSEC_PROC_MEMMAP`\

    If you say Y here, the /proc/<pid>/maps and /proc/<pid>/stat files will
    give no information about the addresses of its mappings if
    PaX features that rely on random addresses are enabled on the task.
    In addition to sanitizing this information and disabling other
    dangerous sources of information, this option causes reads of sensitive
    /proc/<pid> entries where the file descriptor was opened in a different
    task than the one performing the read.  Such attempts are logged.
    This option also limits argv/env strings for suid/sgid binaries
    to 512KB to prevent a complete exhaustion of the stack entropy provided
    by ASLR.  Finally, it places an 8MB stack resource limit on suid/sgid
    binaries to prevent alternative mmap layouts from being abused.

    If you use PaX it is essential that you say Y here as it closes up
    several holes that make full ASLR useless locally.

#### Prevent kernel stack overflows

`GRKERNSEC_KSTACKOVERFLOW`\

    If you say Y here, the kernel's process stacks will be allocated
    with vmalloc instead of the kernel's default allocator.  This
    introduces guard pages that in combination with the alloca checking
    of the STACKLEAK feature and removal of thread_info from the kernel
    stack prevents all forms of kernel process stack overflow abuse.
    Note that this is different from kernel stack buffer overflows.

#### Deter exploit bruteforcing

`GRKERNSEC_BRUTE`\
Related sysctl variables:\
:`kernel.grsecurity.deter_bruteforce`

    If you say Y here, attempts to bruteforce exploits against forking
    daemons such as apache or sshd, as well as against suid/sgid binaries
    will be deterred.  When a child of a forking daemon is killed by PaX
    or crashes due to an illegal instruction or other suspicious signal,
    the parent process will be delayed 30 seconds upon every subsequent
    fork until the administrator is able to assess the situation and
    restart the daemon.
    In the suid/sgid case, the attempt is logged, the user has all their
    existing instances of the suid/sgid binary terminated and will
    be unable to execute any suid/sgid binaries for 15 minutes.

    It is recommended that you also enable signal logging in the auditing
    section so that logs are generated when a process triggers a suspicious
    signal.
    If the sysctl option is enabled, a sysctl option with name
    "deter_bruteforce" is created.

#### Harden module auto-loading

`GRKERNSEC_MODHARDEN`\

    If you say Y here, module auto-loading in response to use of some
    feature implemented by an unloaded module will be restricted to
    root users.  Enabling this option helps defend against attacks 
    by unprivileged users who abuse the auto-loading behavior to 
    cause a vulnerable module to load that is then exploited.

    If this option prevents a legitimate use of auto-loading for a 
    non-root user, the administrator can execute modprobe manually 
    with the exact name of the module mentioned in the alert log.
    Alternatively, the administrator can add the module to the list
    of modules loaded at boot by modifying init scripts.

    Modification of init scripts will most likely be needed on 
    Ubuntu servers with encrypted home directory support enabled,
    as the first non-root user logging in will cause the ecb(aes),
    ecb(aes)-all, cbc(aes), and cbc(aes)-all  modules to be loaded.

#### Hide kernel symbols

`GRKERNSEC_HIDESYM`\

    If you say Y here, getting information on loaded modules, and
    displaying all kernel symbols through a syscall will be restricted
    to users with CAP_SYS_MODULE.  For software compatibility reasons,
    /proc/kallsyms will be restricted to the root user.  The RBAC
    system can hide that entry even from root.

    This option also prevents leaking of kernel addresses through
    several /proc entries.

    Note that this option is only effective provided the following
    conditions are met:
    1) The kernel using grsecurity is not precompiled by some distribution
    2) You have also enabled GRKERNSEC_DMESG
    3) You are using the RBAC system and hiding other files such as your
       kernel image and System.map.  Alternatively, enabling this option
       causes the permissions on /boot, /lib/modules, and the kernel
       source directory to change at compile time to prevent 
       reading by non-root users.
    If the above conditions are met, this option will aid in providing a
    useful protection against local kernel exploitation of overflows
    and arbitrary read/write vulnerabilities.

    It is highly recommended that you enable GRKERNSEC_PERF_HARDEN
    in addition to this feature.

#### Randomize layout of sensitive kernel structures

`GRKERNSEC_RANDSTRUCT`\

    If you say Y here, the layouts of a number of sensitive kernel
    structures (task, fs, cred, etc) and all structures composed entirely
    of function pointers (aka "ops" structs) will be randomized at compile-time.
    This can introduce the requirement of an additional infoleak
    vulnerability for exploits targeting these structure types.

    Enabling this feature will introduce some performance impact, slightly
    increase memory usage, and prevent the use of forensic tools like
    Volatility against the system (unless the kernel source tree isn't
    cleaned after kernel installation).

    The seed used for compilation is located at scripts/gcc-plugins/randomize_layout_seed.h.
    It remains after a make clean to allow for external modules to be compiled
    with the existing seed and will be removed by a make mrproper or
    make distclean.

    Note that the implementation requires gcc 4.6.4. or newer.  You may need
    to install the supporting headers explicitly in addition to the normal
    gcc package.

#### Use cacheline-aware structure randomization

`GRKERNSEC_RANDSTRUCT_PERFORMANCE`\

    If you say Y here, the RANDSTRUCT randomization will make a best effort
    at restricting randomization to cacheline-sized groups of elements.
    This reduces the performance hit of RANDSTRUCT at the cost of weakened
    randomization.

#### Active kernel exploit response

`GRKERNSEC_KERN_LOCKOUT`\

    If you say Y here, when a PaX alert is triggered due to suspicious
    activity in the kernel (from KERNEXEC/UDEREF/USERCOPY)
    or an OOPS occurs due to bad memory accesses, instead of just
    terminating the offending process (and potentially allowing
    a subsequent exploit from the same user), we will take one of two
    actions:
     If the user was root, we will panic the system
     If the user was non-root, we will log the attempt, terminate
     all processes owned by the user, then prevent them from creating
     any new processes until the system is restarted
    This deters repeated kernel exploitation/bruteforcing attempts
    and is useful for later forensics.

#### Old ARM userland compatibility

`GRKERNSEC_OLD_ARM_USERLAND`\

    If you say Y here, stubs of executable code to perform such operations
    as "compare-exchange" will be placed at fixed locations in the ARM vector
    table.  This is unfortunately needed for old ARM userland meant to run
    across a wide range of processors.  Without this option enabled,
    the get_tls and data memory barrier stubs will be emulated by the kernel,
    which is enough for Linaro userlands or other userlands designed for v6
    and newer ARM CPUs.  It's recommended that you try without this option enabled
    first, and only enable it if your userland does not boot (it will likely fail
    at init time).

### Role Based Access Control Options

#### Disable RBAC system

`GRKERNSEC_NO_RBAC`\

    If you say Y here, the /dev/grsec device will be removed from the kernel,
    preventing the RBAC system from being enabled.  You should only say Y
    here if you have no intention of using the RBAC system, so as to prevent
    an attacker with root access from misusing the RBAC system to hide files
    and processes when loadable module support and /dev/[k]mem have been
    locked down.

#### Hide kernel processes

`GRKERNSEC_ACL_HIDEKERN`\

    If you say Y here, all kernel threads will be hidden to all
    processes but those whose subject has the "view hidden processes"
    flag.

#### Maximum tries before password lockout

`GRKERNSEC_ACL_MAXTRIES`\

    This option enforces the maximum number of times a user can attempt
    to authorize themselves with the grsecurity RBAC system before being
    denied the ability to attempt authorization again for a specified time.
    The lower the number, the harder it will be to brute-force a password.

#### Time to wait after max password tries, in seconds

`GRKERNSEC_ACL_TIMEOUT`\

    This option specifies the time the user must wait after attempting to
    authorize to the RBAC system with the maximum number of invalid
    passwords.  The higher the number, the harder it will be to brute-force
    a password.

### Filesystem Protections

#### Proc restrictions

`GRKERNSEC_PROC`\

    If you say Y here, the permissions of the /proc filesystem
    will be altered to enhance system security and privacy.  You MUST
    choose either a user only restriction or a user and group restriction.
    Depending upon the option you choose, you can either restrict users to
    see only the processes they themselves run, or choose a group that can
    view all processes and files normally restricted to root if you choose
    the "restrict to user only" option.  NOTE: If you're running identd or
    ntpd as a non-root user, you will have to run it as the group you
    specify here.

#### Restrict /proc to user only

`GRKERNSEC_PROC_USER`\

    If you say Y here, non-root users will only be able to view their own
    processes, and restricts them from viewing network-related information,
    and viewing kernel symbol and module information.

#### Allow special group

`GRKERNSEC_PROC_USERGROUP`\

    If you say Y here, you will be able to select a group that will be
    able to view all processes and network-related information.  If you've
    enabled GRKERNSEC_HIDESYM, kernel and symbol information may still
    remain hidden.  This option is useful if you want to run identd as
    a non-root user.  The group you select may also be chosen at boot time
    via "grsec_proc_gid=" on the kernel commandline.

#### GID exempted from /proc restrictions

`GRKERNSEC_PROC_GID`\

    Setting this GID determines which group will be exempted from
    grsecurity's /proc restrictions, allowing users of the specified
    group  to view network statistics and the existence of other users'
    processes on the system.  This GID may also be chosen at boot time
    via "grsec_proc_gid=" on the kernel commandline.

#### Additional restrictions

`GRKERNSEC_PROC_ADD`\

    If you say Y here, additional restrictions will be placed on
    /proc that keep normal users from viewing device information and 
    slabinfo information that could be useful for exploits.

#### Linking restrictions

`GRKERNSEC_LINK`\
Related sysctl variables:\
:`kernel.grsecurity.linking_restrictions`

    If you say Y here, /tmp race exploits will be prevented, since users
    will no longer be able to follow symlinks owned by other users in
    world-writable +t directories (e.g. /tmp), unless the owner of the
    symlink is the owner of the directory. users will also not be
    able to hardlink to files they do not own.  If the sysctl option is
    enabled, a sysctl option with name "linking_restrictions" is created.

#### Kernel-enforced SymlinksIfOwnerMatch

`GRKERNSEC_SYMLINKOWN`\
Related sysctl variables:\
:`kernel.grsecurity.enforce_symlinksifowner`

:   `kernel.grsecurity.symlinkown_gid`

<!-- -->

    Apache's SymlinksIfOwnerMatch option has an inherent race condition
    that prevents it from being used as a security feature.  As Apache
    verifies the symlink by performing a stat() against the target of
    the symlink before it is followed, an attacker can setup a symlink
    to point to a same-owned file, then replace the symlink with one
    that targets another user's file just after Apache "validates" the
    symlink -- a classic TOCTOU race.  If you say Y here, a complete,
    race-free replacement for Apache's "SymlinksIfOwnerMatch" option
    will be in place for the group you specify. If the sysctl option
    is enabled, a sysctl option with name "enforce_symlinksifowner" is
    created.

#### GID for users with kernel-enforced SymlinksIfOwnerMatch

`GRKERNSEC_SYMLINKOWN_GID`\

    Setting this GID determines what group kernel-enforced
    SymlinksIfOwnerMatch will be enabled for.  If the sysctl option
    is enabled, a sysctl option with name "symlinkown_gid" is created.

#### FIFO restrictions

`GRKERNSEC_FIFO`\
Related sysctl variables:\
:`kernel.grsecurity.fifo_restrictions`

    If you say Y here, users will not be able to write to FIFOs they don't
    own in world-writable +t directories (e.g. /tmp), unless the owner of
    the FIFO is the same owner of the directory it's held in.  If the sysctl
    option is enabled, a sysctl option with name "fifo_restrictions" is
    created.

#### Sysfs/debugfs restriction

`GRKERNSEC_SYSFS_RESTRICT`\
Related sysctl variables:\
:`kernel.grsecurity.enforce_sysfs_restrict`

    If you say Y here, sysfs (the pseudo-filesystem mounted at /sys) and
    any filesystem normally mounted under it (e.g. debugfs) will be
    mostly accessible only by root.  These filesystems generally provide access
    to hardware and debug information that isn't appropriate for unprivileged
    users of the system.  Sysfs and debugfs have also become a large source
    of new vulnerabilities, ranging from infoleaks to local compromise.
    There has been very little oversight with an eye toward security involved
    in adding new exporters of information to these filesystems, so their
    use is discouraged.
    For reasons of compatibility, a few directories have been whitelisted
    for access by non-root users:
    /sys/fs/selinux
    /sys/fs/fuse
    /sys/devices/system/cpu

    If the sysctl option is enabled, a sysctl option with name
    "enforce_sysfs_restrict" is created to control the sysfs side
    of this protection at runtime.

    At kernel boot time, the grsec_sysfs_restrict=0 option can be
    used on the kernel commandline to disable both the sysfs and
    debugfs aspects of this feature.

    Please note that for backward compatibility reasons, the enforce_sysfs_restrict
    option will default to on regardless of any other grsecurity settings.

#### Allow special group to bypass sysfs restrictions

`GRKERNSEC_SYSFS_RESTRICT_GROUP`\
Related sysctl variables:\
:`kernel.grsecurity.sysfs_restrict_bypass_gid`

    If you say Y here, you will be able to select a group that will be
    able to bypass the sysfs restrictions of GRKERNSEC_SYSFS_RESTRICT.
    While we aim to whitelist obviously safe entries for compatibility
    purposes, this may not always be possible, so this additional group
    can be used to fill that compatibility need if it arises.

#### GID for users that bypass sysfs restrictions

`GRKERNSEC_SYSFS_RESTRICT_GID`\

    Setting this GID determines what group bypassing of sysfs restrictions
    will be permitted for. If the sysctl option is enabled, a sysctl option
    with name "sysfs_restrict_bypass_gid" is created.

#### Runtime read-only mount protection

`GRKERNSEC_ROFS`\
Related sysctl variables:\
:`kernel.grsecurity.romount_protect`

    If you say Y here, a sysctl option with name "romount_protect" will
    be created.  By setting this option to 1 at runtime, filesystems
    will be protected in the following ways:
    * No new writable mounts will be allowed
    * Existing read-only mounts won't be able to be remounted read/write
    * Write operations will be denied on all block devices
    This option acts independently of grsec_lock: once it is set to 1,
    it cannot be turned off.  Therefore, please be mindful of the resulting
    behavior if this option is enabled in an init script on a read-only
    filesystem.
    Also be aware that as with other root-focused features, GRKERNSEC_KMEM
    and GRKERNSEC_IO should be enabled and module loading disabled via
    config or at runtime.
    This feature is mainly intended for secure embedded systems.

#### Eliminate stat/notify-based device sidechannels

`GRKERNSEC_DEVICE_SIDECHANNEL`\

    If you say Y here, timing analyses on block or character
    devices like /dev/ptmx using stat or inotify/dnotify/fanotify
    will be thwarted for unprivileged users.  If a process without
    CAP_MKNOD stats such a device, the last access and last modify times
    will match the device's create time.  No access or modify events
    will be triggered through inotify/dnotify/fanotify for such devices.
    This feature will prevent attacks that may at a minimum
    allow an attacker to determine the administrator's password length.

#### Chroot jail restrictions

`GRKERNSEC_CHROOT`\

    If you say Y here, you will be able to choose several options that will
    make breaking out of a chrooted jail much more difficult.  If you
    encounter no software incompatibilities with the following options, it
    is recommended that you enable each one.

    Note that the chroot restrictions are not intended to apply to "chroots"
    to directories that are simple bind mounts of the global root filesystem.
    For several other reasons, a user shouldn't expect any significant
    security by performing such a chroot.

#### Deny mounts

`GRKERNSEC_CHROOT_MOUNT`\
Related sysctl variables:\
:`kernel.grsecurity.chroot_deny_mount`

    If you say Y here, processes inside a chroot will not be able to
    mount or remount filesystems.  If the sysctl option is enabled, a
    sysctl option with name "chroot_deny_mount" is created.

#### Deny double-chroots

`GRKERNSEC_CHROOT_DOUBLE`\
Related sysctl variables:\
:`kernel.grsecurity.chroot_deny_chroot`

    If you say Y here, processes inside a chroot will not be able to chroot
    again outside the chroot.  This is a widely used method of breaking
    out of a chroot jail and should not be allowed.  If the sysctl 
    option is enabled, a sysctl option with name 
    "chroot_deny_chroot" is created.

#### Deny pivot\_root in chroot

`GRKERNSEC_CHROOT_PIVOT`\
Related sysctl variables:\
:`kernel.grsecurity.chroot_deny_pivot`

    If you say Y here, processes inside a chroot will not be able to use
    a function called pivot_root() that was introduced in Linux 2.3.41.  It
    works similar to chroot in that it changes the root filesystem.  This
    function could be misused in a chrooted process to attempt to break out
    of the chroot, and therefore should not be allowed.  If the sysctl
    option is enabled, a sysctl option with name "chroot_deny_pivot" is
    created.

#### Enforce chdir(

`GRKERNSEC_CHROOT_CHDIR`\
Related sysctl variables:\
:`kernel.grsecurity.chroot_enforce_chdir`

    If you say Y here, the current working directory of all newly-chrooted
    applications will be set to the the root directory of the chroot.
    The man page on chroot(2) states:
    Note that this call does not change  the  current  working
    directory,  so  that `.' can be outside the tree rooted at
    `/'.  In particular, the  super-user  can  escape  from  a
    `chroot jail' by doing `mkdir foo; chroot foo; cd ..'.

    It is recommended that you say Y here, since it's not known to break
    any software.  If the sysctl option is enabled, a sysctl option with
    name "chroot_enforce_chdir" is created.

#### Deny (f)chmod +s

`GRKERNSEC_CHROOT_CHMOD`\
Related sysctl variables:\
:`kernel.grsecurity.chroot_deny_chmod`

    If you say Y here, processes inside a chroot will not be able to chmod
    or fchmod files to make them have suid or sgid bits.  This protects
    against another published method of breaking a chroot.  If the sysctl
    option is enabled, a sysctl option with name "chroot_deny_chmod" is
    created.

#### Deny fchdir and fhandle out of chroot

`GRKERNSEC_CHROOT_FCHDIR`\
Related sysctl variables:\
:`kernel.grsecurity.chroot_deny_fchdir`

    If you say Y here, a well-known method of breaking chroots by fchdir'ing
    to a file descriptor of the chrooting process that points to a directory
    outside the filesystem will be stopped.  This option also prevents use of
    the recently-created syscall for opening files by a guessable "file handle"
    inside a chroot, as well as accessing relative paths outside of a
    directory passed in via file descriptor with openat and similar syscalls.
    If the sysctl option is enabled, a sysctl option with name "chroot_deny_fchdir"
    is created.

#### Deny mknod

`GRKERNSEC_CHROOT_MKNOD`\
Related sysctl variables:\
:`kernel.grsecurity.chroot_deny_mknod`

    If you say Y here, processes inside a chroot will not be allowed to
    mknod.  The problem with using mknod inside a chroot is that it
    would allow an attacker to create a device entry that is the same
    as one on the physical root of your system, which could range from
    anything from the console device to a device for your harddrive (which
    they could then use to wipe the drive or steal data).  It is recommended
    that you say Y here, unless you run into software incompatibilities.
    If the sysctl option is enabled, a sysctl option with name
    "chroot_deny_mknod" is created.

#### Deny shmat() out of chroot

`GRKERNSEC_CHROOT_SHMAT`\
Related sysctl variables:\
:`kernel.grsecurity.chroot_deny_shmat`

    If you say Y here, processes inside a chroot will not be able to attach
    to shared memory segments that were created outside of the chroot jail.
    It is recommended that you say Y here.  If the sysctl option is enabled,
    a sysctl option with name "chroot_deny_shmat" is created.

#### Deny access to abstract AF\_UNIX sockets out of chroot

`GRKERNSEC_CHROOT_UNIX`\
Related sysctl variables:\
:`kernel.grsecurity.chroot_deny_unix`

    If you say Y here, processes inside a chroot will not be able to
    connect to abstract (meaning not belonging to a filesystem) Unix
    domain sockets that were bound outside of a chroot.  It is recommended
    that you say Y here.  If the sysctl option is enabled, a sysctl option
    with name "chroot_deny_unix" is created.

#### Protect outside processes

`GRKERNSEC_CHROOT_FINDTASK`\
Related sysctl variables:\
:`kernel.grsecurity.chroot_findtask`

    If you say Y here, processes inside a chroot will not be able to
    kill, send signals with fcntl, ptrace, capget, getpgid, setpgid, 
    getsid, or view any process outside of the chroot.  If the sysctl
    option is enabled, a sysctl option with name "chroot_findtask" is
    created.

#### Restrict priority changes

`GRKERNSEC_CHROOT_NICE`\
Related sysctl variables:\
:`kernel.grsecurity.chroot_restrict_nice`

    If you say Y here, processes inside a chroot will not be able to raise
    the priority of processes in the chroot, or alter the priority of
    processes outside the chroot.  This provides more security than simply
    removing CAP_SYS_NICE from the process' capability set.  If the
    sysctl option is enabled, a sysctl option with name "chroot_restrict_nice"
    is created.

#### Deny sysctl writes

`GRKERNSEC_CHROOT_SYSCTL`\
Related sysctl variables:\
:`kernel.grsecurity.chroot_deny_sysctl`

    If you say Y here, an attacker in a chroot will not be able to
    write to sysctl entries, either by sysctl(2) or through a /proc
    interface.  It is strongly recommended that you say Y here. If the
    sysctl option is enabled, a sysctl option with name
    "chroot_deny_sysctl" is created.

#### Deny bad renames

`GRKERNSEC_CHROOT_RENAME`\
Related sysctl variables:\
:`kernel.grsecurity.chroot_deny_bad_rename`

    If you say Y here, an attacker in a chroot will not be able to
    abuse the ability to create double chroots to break out of the
    chroot by exploiting a race condition between a rename of a directory
    within a chroot against an open of a symlink with relative path
    components.  This feature will likewise prevent an accomplice outside
    a chroot from enabling a user inside the chroot to break out and make
    use of their credentials on the global filesystem.  Enabling this
    feature is essential to prevent root users from breaking out of a
    chroot. If the sysctl option is enabled, a sysctl option with name
    "chroot_deny_bad_rename" is created.

#### Capability restrictions

`GRKERNSEC_CHROOT_CAPS`\
Related sysctl variables:\
:`kernel.grsecurity.chroot_caps`

    If you say Y here, the capabilities on all processes within a
    chroot jail will be lowered to stop module insertion, raw i/o,
    system and net admin tasks, rebooting the system, modifying immutable
    files, modifying IPC owned by another, and changing the system time.
    This is left an option because it can break some apps.  Disable this
    if your chrooted apps are having problems performing those kinds of
    tasks.  If the sysctl option is enabled, a sysctl option with
    name "chroot_caps" is created.

#### Exempt initrd tasks from restrictions

`GRKERNSEC_CHROOT_INITRD`\

    If you say Y here, tasks started prior to init will be exempted from
    grsecurity's chroot restrictions.  This option is mainly meant to
    resolve Plymouth's performing privileged operations unnecessarily
    in a chroot.

### Kernel Auditing

#### Single group for auditing

`GRKERNSEC_AUDIT_GROUP`\
Related sysctl variables:\
:`kernel.grsecurity.audit_gid`

:   `kernel.grsecurity.audit_group`

<!-- -->

    If you say Y here, the exec and chdir logging features will only operate
    on a group you specify.  This option is recommended if you only want to
    watch certain users instead of having a large amount of logs from the
    entire system.  If the sysctl option is enabled, a sysctl option with
    name "audit_group" is created.

#### GID for auditing

`GRKERNSEC_AUDIT_GID`\

#### Exec logging

`GRKERNSEC_EXECLOG`\
Related sysctl variables:\
:`kernel.grsecurity.exec_logging`

    If you say Y here, all execve() calls will be logged (since the
    other exec*() calls are frontends to execve(), all execution
    will be logged).  Useful for shell-servers that like to keep track
    of their users.  If the sysctl option is enabled, a sysctl option with
    name "exec_logging" is created.
    WARNING: This option when enabled will produce a LOT of logs, especially
    on an active system.

#### Resource logging

`GRKERNSEC_RESLOG`\
Related sysctl variables:\
:`kernel.grsecurity.resource_logging`

    If you say Y here, all attempts to overstep resource limits will
    be logged with the resource name, the requested size, and the current
    limit.  It is highly recommended that you say Y here.  If the sysctl
    option is enabled, a sysctl option with name "resource_logging" is
    created.  If the RBAC system is enabled, the sysctl value is ignored.

#### Log execs within chroot

`GRKERNSEC_CHROOT_EXECLOG`\
Related sysctl variables:\
:`kernel.grsecurity.chroot_execlog`

    If you say Y here, all executions inside a chroot jail will be logged
    to syslog.  This can cause a large amount of logs if certain
    applications (eg. djb's daemontools) are installed on the system, and
    is therefore left as an option.  If the sysctl option is enabled, a
    sysctl option with name "chroot_execlog" is created.

#### Ptrace logging

`GRKERNSEC_AUDIT_PTRACE`\
Related sysctl variables:\
:`kernel.grsecurity.audit_ptrace`

    If you say Y here, all attempts to attach to a process via ptrace
    will be logged.  If the sysctl option is enabled, a sysctl option
    with name "audit_ptrace" is created.

#### Chdir logging

`GRKERNSEC_AUDIT_CHDIR`\
Related sysctl variables:\
:`kernel.grsecurity.audit_chdir`

    If you say Y here, all chdir() calls will be logged.  If the sysctl
    option is enabled, a sysctl option with name "audit_chdir" is created.

#### (Un)Mount logging

`GRKERNSEC_AUDIT_MOUNT`\
Related sysctl variables:\
:`kernel.grsecurity.audit_mount`

    If you say Y here, all mounts and unmounts will be logged.  If the
    sysctl option is enabled, a sysctl option with name "audit_mount" is
    created.

#### Only log (un)mounts for the initial mount namespace

`GRKERNSEC_AUDIT_MOUNT_INITNS_ONLY`\

    If you say Y here, GRKERNSEC_AUDIT_MOUNT's behavior will be modified such
    that only mounts/unmounts in the initial mount namespace will be logged.
    This will eliminate noise from systemd and other facilities that create
    mounts (generally for security purposes) within private mount namespaces
    for specific services that have opted in.

#### Signal logging

`GRKERNSEC_SIGNAL`\
Related sysctl variables:\
:`kernel.grsecurity.signal_logging`

    If you say Y here, certain important signals will be logged, such as
    SIGSEGV, which will as a result inform you of when a error in a program
    occurred, which in some cases could mean a possible exploit attempt.
    If the sysctl option is enabled, a sysctl option with name
    "signal_logging" is created.

#### Fork failure logging

`GRKERNSEC_FORKFAIL`\
Related sysctl variables:\
:`kernel.grsecurity.forkfail_logging`

    If you say Y here, all failed fork() attempts will be logged.
    This could suggest a fork bomb, or someone attempting to overstep
    their process limit.  If the sysctl option is enabled, a sysctl option
    with name "forkfail_logging" is created.

#### Time change logging

`GRKERNSEC_TIME`\
Related sysctl variables:\
:`kernel.grsecurity.timechange_logging`

    If you say Y here, any changes of the system clock will be logged.
    If the sysctl option is enabled, a sysctl option with name
    "timechange_logging" is created.

#### /proc/<pid>/ipaddr support

`GRKERNSEC_PROC_IPADDR`\

    If you say Y here, a new entry will be added to each /proc/<pid>
    directory that contains the IP address of the person using the task.
    The IP is carried across local TCP and AF_UNIX stream sockets.
    This information can be useful for IDS/IPSes to perform remote response
    to a local attack.  The entry is readable by only the owner of the
    process (and root if he has CAP_DAC_OVERRIDE, which can be removed via
    the RBAC system), and thus does not create privacy concerns.

#### Denied RWX mmap/mprotect logging

`GRKERNSEC_RWXMAP_LOG`\
Related sysctl variables:\
:`kernel.grsecurity.rwxmap_logging`

    If you say Y here, calls to mmap() and mprotect() with explicit
    usage of PROT_WRITE and PROT_EXEC together will be logged when
    denied by the PAX_MPROTECT feature.  This feature will also
    log other problematic scenarios that can occur when PAX_MPROTECT
    is enabled on a binary, like textrels and PT_GNU_STACK.  If the 
    sysctl option is enabled, a sysctl option with name "rwxmap_logging"
    is created.

### Executable Protections

#### Dmesg(8) restriction

`GRKERNSEC_DMESG`\
Related sysctl variables:\
:`kernel.grsecurity.dmesg`

    If you say Y here, non-root users will not be able to use dmesg(8)
    to view the contents of the kernel's circular log buffer.
    The kernel's log buffer often contains kernel addresses and other
    identifying information useful to an attacker in fingerprinting a
    system for a targeted exploit.
    If the sysctl option is enabled, a sysctl option with name "dmesg" is
    created.

#### Deter ptrace-based process snooping

`GRKERNSEC_HARDEN_PTRACE`\
Related sysctl variables:\
:`kernel.grsecurity.harden_ptrace`

    If you say Y here, TTY sniffers and other malicious monitoring
    programs implemented through ptrace will be defeated.  If you
    have been using the RBAC system, this option has already been
    enabled for several years for all users, with the ability to make
    fine-grained exceptions.

    This option only affects the ability of non-root users to ptrace
    processes that are not a descendent of the ptracing process.
    This means that strace ./binary and gdb ./binary will still work,
    but attaching to arbitrary processes will not.  If the sysctl
    option is enabled, a sysctl option with name "harden_ptrace" is
    created.

#### Require read access to ptrace sensitive binaries

`GRKERNSEC_PTRACE_READEXEC`\
Related sysctl variables:\
:`kernel.grsecurity.ptrace_readexec`

    If you say Y here, unprivileged users will not be able to ptrace unreadable
    binaries.  This option is useful in environments that
    remove the read bits (e.g. file mode 4711) from suid binaries to
    prevent infoleaking of their contents.  This option adds
    consistency to the use of that file mode, as the binary could normally
    be read out when run without privileges while ptracing.

    If the sysctl option is enabled, a sysctl option with name "ptrace_readexec"
    is created.

#### Enforce consistent multithreaded privileges

`GRKERNSEC_SETXID`\
Related sysctl variables:\
:`kernel.grsecurity.consistent_setxid`

    If you say Y here, a change from a root uid to a non-root uid
    in a multithreaded application will cause the resulting uids,
    gids, supplementary groups, and capabilities in that thread
    to be propagated to the other threads of the process.  In most
    cases this is unnecessary, as glibc will emulate this behavior
    on behalf of the application.  Other libcs do not act in the
    same way, allowing the other threads of the process to continue
    running with root privileges.  If the sysctl option is enabled,
    a sysctl option with name "consistent_setxid" is created.

#### Disallow access to overly-permissive IPC objects

`GRKERNSEC_HARDEN_IPC`\
Related sysctl variables:\
:`kernel.grsecurity.harden_ipc`

    If you say Y here, access to overly-permissive IPC objects (shared
    memory, message queues, and semaphores) will be denied for processes
    given the following criteria beyond normal permission checks:
    1) If the IPC object is world-accessible and the euid doesn't match
       that of the creator or current uid for the IPC object
    2) If the IPC object is group-accessible and the egid doesn't
       match that of the creator or current gid for the IPC object
    It's a common error to grant too much permission to these objects,
    with impact ranging from denial of service and information leaking to
    privilege escalation.  This feature was developed in response to
    research by Tim Brown:
    http://labs.portcullis.co.uk/whitepapers/memory-squatting-attacks-on-system-v-shared-memory/
    who found hundreds of such insecure usages.  Processes with
    CAP_IPC_OWNER are still permitted to access these IPC objects.
    If the sysctl option is enabled, a sysctl option with name
    "harden_ipc" is created.

#### Disallow unprivileged use of command injection

`GRKERNSEC_HARDEN_TTY`\
Related sysctl variables:\
:`kernel.grsecurity.harden_tty`

    If you say Y here, the ability to use the TIOCSTI ioctl for
    terminal command injection will be denied for unprivileged users.
    There are very few legitimate uses for this functionality and it
    has made vulnerabilities in several 'su'-like programs possible in
    the past.  Even without these vulnerabilities, it provides an
    attacker with an easy mechanism to move laterally among other
    processes within the same user's compromised session.
    By default, Linux allows unprivileged use of command injection as
    long as the injection is being performed into the same tty session.
    This feature makes that case the same as attempting to inject into
    another session, making any TIOCSTI use require CAP_SYS_ADMIN.
    If the sysctl option is enabled, a sysctl option with name
    "harden_tty" is created.

#### Disable ability of suid root apps to execute unsafe files

`GRKERNSEC_SUID_NO_UNPRIV_EXEC`\

    If you say Y here, suid root applications will not be able to mmap
    executable or execute files world-writable or not owned by root.
    This addresses both an exploitation technique (e.g. the struct
    service_user overwrite in Qualys' CVE-2021-3156 Sudo exploit) as
    well as a vulnerability class that has appeared in suid root
    executables in the past (e.g. CVE-2017-4915).  If the sysctl
    option is enabled, a sysctl option with name "suid_no_unpriv_exec"
    is created.

#### Trusted Path Execution (TPE)

`GRKERNSEC_TPE`\
Related sysctl variables:\
:`kernel.grsecurity.tpe`

:   `kernel.grsecurity.tpe_gid`

<!-- -->

    If you say Y here, you will be able to choose a gid to add to the
    supplementary groups of users you want to mark as "untrusted."
    These users will not be able to execute any files that are not in
    root-owned directories writable only by root.  If the sysctl option
    is enabled, a sysctl option with name "tpe" is created.

#### Partially restrict all non-root users

`GRKERNSEC_TPE_ALL`\
Related sysctl variables:\
:`kernel.grsecurity.tpe_restrict_all`

    If you say Y here, all non-root users will be covered under
    a weaker TPE restriction.  This is separate from, and in addition to,
    the main TPE options that you have selected elsewhere.  Thus, if a
    "trusted" GID is chosen, this restriction applies to even that GID.
    Under this restriction, all non-root users will only be allowed to
    execute files in directories they own that are not group or
    world-writable, or in directories owned by root and writable only by
    root.  If the sysctl option is enabled, a sysctl option with name
    "tpe_restrict_all" is created.

#### Invert GID option

`GRKERNSEC_TPE_INVERT`\
Related sysctl variables:\
:`kernel.grsecurity.tpe_invert`

    If you say Y here, the group you specify in the TPE configuration will
    decide what group TPE restrictions will be *disabled* for.  This
    option is useful if you want TPE restrictions to be applied to most
    users on the system.  If the sysctl option is enabled, a sysctl option
    with name "tpe_invert" is created.  Unlike other sysctl options, this
    entry will default to on for backward-compatibility.

#### GID for TPE-untrusted users

`GRKERNSEC_TPE_UNTRUSTED_GID`\

    Setting this GID determines what group TPE restrictions will be
    *enabled* for.  If the sysctl option is enabled, a sysctl option
    with name "tpe_gid" is created.

#### GID for TPE-trusted users

`GRKERNSEC_TPE_TRUSTED_GID`\

    Setting this GID determines what group TPE restrictions will be
    *disabled* for.  If the sysctl option is enabled, a sysctl option
    with name "tpe_gid" is created.

### Network Protections

#### TCP/UDP blackhole and LAST\_ACK DoS prevention

`GRKERNSEC_BLACKHOLE`\
Related sysctl variables:\
:`kernel.grsecurity.ip_blackhole`

:   `kernel.grsecurity.lastack_retries`

<!-- -->

    If you say Y here, neither TCP resets nor ICMP
    destination-unreachable packets will be sent in response to packets
    sent to ports for which no associated listening process exists.
    It will also prevent the sending of ICMP protocol unreachable packets
    in response to packets with unknown protocols.
    This feature supports both IPV4 and IPV6 and exempts the 
    loopback interface from blackholing.  Enabling this feature 
    makes a host more resilient to DoS attacks and reduces network
    visibility against scanners.

    The blackhole feature as-implemented is equivalent to the FreeBSD
    blackhole feature, as it prevents RST responses to all packets, not
    just SYNs.  Under most application behavior this causes no
    problems, but applications (like haproxy) may not close certain
    connections in a way that cleanly terminates them on the remote
    end, leaving the remote host in LAST_ACK state.  Because of this
    side-effect and to prevent intentional LAST_ACK DoSes, this
    feature also adds automatic mitigation against such attacks.
    The mitigation drastically reduces the amount of time a socket
    can spend in LAST_ACK state.  If you're using haproxy and not
    all servers it connects to have this option enabled, consider
    disabling this feature on the haproxy host.

    If the sysctl option is enabled, two sysctl options with names
    "ip_blackhole" and "lastack_retries" will be created.
    While "ip_blackhole" takes the standard zero/non-zero on/off
    toggle, "lastack_retries" uses the same kinds of values as
    "tcp_retries1" and "tcp_retries2".  The default value of 4
    prevents a socket from lasting more than 45 seconds in LAST_ACK
    state.

#### Disable TCP Simultaneous Connect

`GRKERNSEC_NO_SIMULT_CONNECT`\

    If you say Y here, a feature by Willy Tarreau will be enabled that
    removes a weakness in Linux's strict implementation of TCP that
    allows two clients to connect to each other without either entering
    a listening state.  The weakness allows an attacker to easily prevent
    a client from connecting to a known server provided the source port
    for the connection is guessed correctly.

    As the weakness could be used to prevent an antivirus or IPS from
    fetching updates, or prevent an SSL gateway from fetching a CRL,
    it should be eliminated by enabling this option.  Though Linux is
    one of few operating systems supporting simultaneous connect, it
    has no legitimate use in practice and is rarely supported by firewalls.

#### Socket restrictions

`GRKERNSEC_SOCKET`\

    If you say Y here, you will be able to choose from several options.
    If you assign a GID on your system and add it to the supplementary
    groups of users you want to restrict socket access to, this patch
    will perform up to three things, based on the option(s) you choose.

#### Deny any sockets to group

`GRKERNSEC_SOCKET_ALL`\
Related sysctl variables:\
:`kernel.grsecurity.socket_all`

:   `kernel.grsecurity.socket_all_gid`

<!-- -->

    If you say Y here, you will be able to choose a GID of whose users will
    be unable to connect to other hosts from your machine or run server
    applications from your machine.  If the sysctl option is enabled, a
    sysctl option with name "socket_all" is created.

#### GID to deny all sockets for

`GRKERNSEC_SOCKET_ALL_GID`\

    Here you can choose the GID to disable socket access for. Remember to
    add the users you want socket access disabled for to the GID
    specified here.  If the sysctl option is enabled, a sysctl option
    with name "socket_all_gid" is created.

#### Deny client sockets to group

`GRKERNSEC_SOCKET_CLIENT`\
Related sysctl variables:\
:`kernel.grsecurity.socket_client`

:   `kernel.grsecurity.socket_client_gid`

<!-- -->

    If you say Y here, you will be able to choose a GID of whose users will
    be unable to connect to other hosts from your machine, but will be
    able to run servers.  If this option is enabled, all users in the group
    you specify will have to use passive mode when initiating ftp transfers
    from the shell on your machine.  If the sysctl option is enabled, a
    sysctl option with name "socket_client" is created.

#### GID to deny client sockets for

`GRKERNSEC_SOCKET_CLIENT_GID`\

    Here you can choose the GID to disable client socket access for.
    Remember to add the users you want client socket access disabled for to
    the GID specified here.  If the sysctl option is enabled, a sysctl
    option with name "socket_client_gid" is created.

#### Deny server sockets to group

`GRKERNSEC_SOCKET_SERVER`\
Related sysctl variables:\
:`kernel.grsecurity.socket_server`

:   `kernel.grsecurity.socket_server_gid`

<!-- -->

    If you say Y here, you will be able to choose a GID of whose users will
    be unable to run server applications from your machine.  If the sysctl
    option is enabled, a sysctl option with name "socket_server" is created.

#### GID to deny server sockets for

`GRKERNSEC_SOCKET_SERVER_GID`\

    Here you can choose the GID to disable server socket access for.
    Remember to add the users you want server socket access disabled for to
    the GID specified here.  If the sysctl option is enabled, a sysctl
    option with name "socket_server_gid" is created.

### Physical Protections

#### Deny new USB connections after toggle

`GRKERNSEC_DENYUSB`\

    If you say Y here, a new sysctl option with name "deny_new_usb"
    will be created.  Setting its value to 1 will prevent any new
    USB devices from being recognized by the OS.  Any attempted USB
    device insertion will be logged.  This option is intended to be
    used against custom USB devices designed to exploit vulnerabilities
    in various USB device drivers.

    For greatest effectiveness, this sysctl should be set after any
    relevant init scripts.  This option is safe to enable in distros
    as each user can choose whether or not to toggle the sysctl.

#### Reject all USB devices not connected at boot

`GRKERNSEC_DENYUSB_FORCE`\

    If you say Y here, a variant of GRKERNSEC_DENYUSB will be enabled
    that doesn't involve a sysctl entry.  This option should only be
    enabled if you're sure you want to deny all new USB connections
    at runtime and don't want to modify init scripts.  This should not
    be enabled by distros.  It forces the core USB code to be built
    into the kernel image so that all devices connected at boot time
    can be recognized and new USB device connections can be prevented
    prior to init running.

### Sysctl Support

#### Sysctl support

`GRKERNSEC_SYSCTL`\

    If you say Y here, you will be able to change the options that
    grsecurity runs with at bootup, without having to recompile your
    kernel.  You can echo values to files in /proc/sys/kernel/grsecurity
    to enable (1) or disable (0) various features.  All the sysctl entries
    are mutable until the "grsec_lock" entry is set to a non-zero value.
    All features enabled in the kernel configuration are disabled at boot
    if you do not say Y to the "Turn on features by default" option.
    All options should be set at startup, and the grsec_lock entry should
    be set to a non-zero value after all the options are set.
    *THIS IS EXTREMELY IMPORTANT*

#### Extra sysctl support for distro makers (READ HELP)

`GRKERNSEC_SYSCTL_DISTRO`\

    If you say Y here, additional sysctl options will be created
    for features that affect processes running as root.  Therefore,
    it is critical when using this option that the grsec_lock entry be
    enabled after boot.  Only distros with prebuilt kernel packages
    with this option enabled that can ensure grsec_lock is enabled
    after boot should use this option.
    *Failure to set grsec_lock after boot makes all grsec features
    this option covers useless*

    Currently this option creates the following sysctl entries:
    "Disable Privileged I/O": "disable_priv_io"

#### Turn on features by default

`GRKERNSEC_SYSCTL_ON`\

    If you say Y here, instead of having all features enabled in the
    kernel configuration disabled at boot time, the features will be
    enabled at boot time.  It is recommended you say Y here unless
    there is some reason you would want all sysctl-tunable features to
    be disabled by default.  As mentioned elsewhere, it is important
    to enable the grsec_lock entry once you have finished modifying
    the sysctl entries.

### Logging Options

#### Seconds in between log messages (minimum)

`GRKERNSEC_FLOODTIME`\

    This option allows you to enforce the number of seconds between
    grsecurity log messages.  The default should be suitable for most
    people, however, if you choose to change it, choose a value small enough
    to allow informative logs to be produced, but large enough to
    prevent flooding.

    Setting both this value and GRKERNSEC_FLOODBURST to 0 will disable
    any rate limiting on grsecurity log messages.

#### Number of messages in a burst (maximum)

`GRKERNSEC_FLOODBURST`\

    This option allows you to choose the maximum number of messages allowed
    within the flood time interval you chose in a separate option.  The
    default should be suitable for most people, however if you find that
    many of your logs are being interpreted as flooding, you may want to
    raise this value.

    Setting both this value and GRKERNSEC_FLOODTIME to 0 will disable
    any rate limiting on grsecurity log messages.