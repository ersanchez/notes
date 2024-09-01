# `syncthing` Notes

## Synchronization Conflicts

**NOTE**: Take action on synchronization conflicts as soon as you detect them. Fixing this problem becomes more difficult the longer you wait!

If `syncthing` detects the same file has been changed at the same time on two different devices it will create a `sync-conflict` file.

Here is a situation where I have unintentionally triggered this synchronization conflict functionality:

1. Take computer-A (laptop) on an airplane (_**without**_ network connectivity)
2. On computer-A, make updates to a `example-file.txt` that is being synchronized with another computer using `syncthing`
3. Get home - but leave computer-A off in computer bag by the front door because I was too tired to unpack it
4. On computer-B (desktop), make update to `example-file.txt`
5. Unpack computer and connect it to my home network
6. computer A attempts to synchronize `example-file.txt` but it detects a conflict between the versions on computer-A and computer-B
7. `syncthing` creates a `sync-conflict` file to save one of the differing versions

Now I have a problem: The version of `example-file.txt` that is on computer-B does not have the edits I made on the airplane to the same version of this file on computer-A

I was not able to find a recommended solution for this scenario. I think the reason for no official guidance is that the needed actions will be different in almost every case.

