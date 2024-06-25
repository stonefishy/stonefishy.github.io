---
title: Write a Command line tool to install additional msi components.
date: 2023-05-25 17:35:28
Category: C#
tags: [C#, Msi]
---

Constants.cs file
``` csharp
public class Constants {
    public static string PRODUCT_REGISTRY_KEY = @"HKEY_LOCAL_MACHINE\Software\MyCompany\MyProduct";

    public static string MSI_PATH_ARG= "MSI_PATH";

    public static string MSI_COMPONETS_ARG = "MSI_COMPONETS";

    public static string MSI_ADD_LOCAL_PROPERTY = "ADDLOCAL";
    
}
```

MsiCommand.cs file
``` csharp
using System;
using System.Diagnostics;

public class MsiCommand 
{
    private static string COMMAND_AGENT = "msiexec.exe";
    private static string SILENT_MODE = "/quiet";

    private string msiPath;

    private string msiArgs;

    private string commandStr;

    public MsiCommand(string msiPath, string msiArgs, bool isSilentMode = true)
    {
        this.msiPath = msiPath;
        this.msiArgs = msiArgs;
        
        if (isSilentMode) { 
            this.commandStr = $"{COMMAND_AGENT} {SILENT_MODE} /i \"{this.msiPath}\" {this.msiArgs}";
        } else {
            this.commandStr = $"{COMMAND_AGENT} /i \"{this.msiPath}\" {this.msiArgs}";
        }
    }

    public void Install() {
        ProcessStartInfo processStartInfo = new ProcessStartInfo
        {
            FileName = "cmd.exe",
            Arguments = $"/C {this.commandStr}",
            RedirectStandardOutput = true,
            UseShellExecute = false,
            CreateNoWindow = true,
            WindowStyle = ProcessWindowStyle.Hidden
        };

        // Create and start the process
        Process process = new Process { StartInfo = processStartInfo };
        process.Start();
        process.WaitForExit();
    }
}
```

RegistryHelper.cs file
``` csharp
using Microsoft.Win32;
using System;

public sealed class RegistryHelper 
{
    private RegistryHelper() {}

    public static bool IsHKLMRegistryKeyExists(string keyPath) 
    {
        if (String.IsNullOrEmpty(keyPath)) 
        {
            return false;
        }

        RegistryKey key = Registry.LocalMachine.OpenSubKey(keyPath);

        if (key != null) 
        {
            key.Close();
            return true;
        }
        
        return false;
    }
}
```

Program.cs file
``` csharp
internal class Program
{
    private static Dictionary<string, string> Components = new Dictionary<string, string> {
        { "Agent", "IS_AGENT" },
        { "Console", "IS_CONSOLE" },
        { "ImageExplorer", "IS_IMAGE_EXPLORER" },
        { "TaskTray", "IS_TASKTRAY" },
    };

    private static Dictionary<string, string> CommandArgDict = new Dictionary<string, string>();

    /*
    * args[0] - msi installer file path, e.g. MSI_PATH="C:\Program Files\MyCompany\MyProduct.msi"
    * args[1] - components name, split by comma, e.g. MSI_COMPONENTS="Agent,Console"
    */
    private static void Main(string[] args)
    {
        for (int i = 0; i < args.Length; i++) {
            string[] argArr = args[i].Split("=");
            if (argArr.Length != 2) {
                throw new Exception("Invalid argument format, please use 'argName=argValue'");
            }
            CommandArgDict.Add(args[0], args[1]);
        }

        string msiFilePath = CommandArgDict["MSI_PATH"];
        if (String.IsNullOrEmpty(msiFilePath)) {
            throw new Exception("Please specify the msi installer file path!");
        }

        string[] components = CommandArgDict["MSI_COMPONENTS"].Split(",");
    }
}
```


