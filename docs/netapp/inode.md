## modify volume inode size
```
volume show –vserver <vserver name> -volume <volume name> -fields files
volume modify –vserver <vserver name> -volume <volume name> -files <number of files>