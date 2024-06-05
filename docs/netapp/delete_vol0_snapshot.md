## From the command line, drop to the nodeshell.
```
::> run -node <nodename>
```

## Delete all existing snapshots on the root volume and suppress confirmation request.
```
> snap delete -a -f vol0
```

## Disable the automatic snapshot schedule.
```
> snap sched vol0 0 0 0
```

## Enable snapshot autodelete.
```
> snap autodelete vol0 on
```

## Set the snapshot autodelete threshold to 35% for the volume free space. This guarantees the volume has a minimum of free space equal to 35% of its overall size.
```
> snap autodelete vol0 target_free_space 35
```

## Confirm the autodelete has been configured correctly.
```
> snap autodelete vol0
```