### Ontap Toolkit Debug Logging
You may find an instance where you either need debug loggin on or it has been turned on and you want it off. To accomplish either tasks you need the following command:

```powershell
# Turn on debugging
PS > Set-NaToolkitConfiguration -LogLevel debug

# Turn off debugging
PS > Set-NaToolkitConfiguration -LogLevel off
```
