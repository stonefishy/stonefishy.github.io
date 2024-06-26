---
title: How to install additional msi components.
date: 2023-05-25 17:35:28
Category: Installer
tags: [Msi, Wix, Installer]
---

Recently, I had the opportunity to work on a project that requires to build a Msi Installer which contains multiple components. The project was to install these components on a Windows machine. To build this Msi Installer, I used `WiX toolset`. It's a powerful tool that allows you to create complex installers with a high degree of flexibility. But here I'm not going to go into the details of how to create a Msi Installer using WiX toolset. Instead, I'll focus on how to use `msiexec` tool to install additional components of a Windows MSI package.

## Understanding `msiexec`
`msiexec` is a command-line utility that allows you to install, modify, or uninstall software that comes in the form of an MSI package. MSI (Microsoft Installer) packages are commonly used for software distribution because they offer a standardized way to handle installations, including dependencies and custom actions.

## Windows Installer Properties
The Windows Installer contains several properties that can be set to customize the installation process. These properties is defined in list categories.
- Component Location Properties
- Configuration Properties
- Date, Time Properties
- Feature Installation Options Properties
- Hardware Properties
- Installation Status Properties
- Operating System Properties
- Product Information Properties
- Summary Information Update Properties
- System Folder Properties
- User Information Properties

For more details, please refer to the official documentation of Windows Installer. https://learn.microsoft.com/en-us/windows/win32/msi/property-reference

## The `ADDLOCAL` Option
One of the most useful options when using msiexec is `ADDLOCAL`. This option allows you to specify which features or components of the MSI package you want to install. Here’s the basic syntax:

``` shell
msiexec /i <path_to_msi_file> ADDLOCAL=<component1>[,<component2>,...]
```

This works in a scenario, some feature components of msi package already installed, and you can use this property to install additional component.

## Example Scenario
Let's say you have an MSI package named example.msi that contains several components, including FeatureA, FeatureB, and FeatureC. If you only need FeatureA and FeatureB, you can use the following command:

``` shell
msiexec /i "C:\path\to\example.msi" ADDLOCAL=FeatureA,FeatureB
```

This command will install only the specified components, saving you time and disk space.

## Combining with Other Options
msiexec offers a variety of other options that you can combine with `ADDLOCAL` to customize your installation further. For instance, you can use the `/passive` option for a passive installation, which means the installation will run with minimal user interaction:

``` shell
msiexec /i "C:\path\to\example.msi" ADDLOCAL=FeatureA /passive
```

This is particularly useful for automated installations or when you want to avoid user prompts.

### Important Considerations
1. `Component Names`: Ensure that the component names are exactly as specified in the MSI package. These names can usually be found in the documentation provided with the MSI package or by inspecting the package with a tool like `Orca`.

2. `All Components`: If you need to install all components, you can omit the `ADDLOCAL=ALL` option to install everything:

``` shell
msiexec /i "C:\path\to\example.msi" ADDLOCAL=ALL
```

## Summary
Using `msiexec` with the `ADDLOCAL` option is a powerful way to manage MSI installations, allowing you to install only the components you need. This not only saves time but also ensures that your system remains clutter-free with unnecessary software. Whether you're a system administrator, a developer, or simply someone who likes to keep their system lean, mastering msiexec is a valuable skill.

By following the examples and tips provided in this blog post, you'll be well on your way to becoming an expert in MSI installations. Happy installing!

## Useful Links
- Windows Instalelr: https://learn.microsoft.com/en-us/windows/win32/msi/windows-installer-portal
- Windows Installer Property Reference: https://docs.microsoft.com/en-us/windows/win32/msi/property-reference