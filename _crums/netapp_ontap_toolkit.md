---
title: "PowerShell: NetApp Toolkit Customization"
last_modified_at: 2021-05-23T05:28:36-05:00
categories:
  - crums
tags:
  - NetApp
  - PowerShell
---
### Ontap Toolkit Debug Logging
You may find an instance where you either need debug logging on or it has been turned on and you want it off. To accomplish either tasks you want to use the following command:

```powershell
# Turn on debugging
PS > Set-NaToolkitConfiguration -LogLevel debug

# Turn off debugging
PS > Set-NaToolkitConfiguration -LogLevel off
```
