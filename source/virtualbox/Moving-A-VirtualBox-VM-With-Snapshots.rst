======================================
Moving A VirtualBox VM With Snapshots
======================================


这个页面用google浏览器打开时, 会重定向掉, 所以请使用 safari 浏览器打开(具体原因,我也不清楚)

https://ehikioya.com/moving-virtualbox-vm-snapshots/

env
======================================

machine A: virtualbox 192.168.31.184(ubuntu desktop)
machine B: virtualbox 192.168.31.193(ubuntu desktop)

step
======================================


Cloning A VirtualBox VM
--------------------------------------

machine A

Step 1:
Shut down the virtual machine you want to clone. A virtual machine in a saved or running state cannot be cloned.

Step 2:
To start the cloning process, right click the virtual machine and select “Clone”.

Step 3:
The wizard that opens will first ask you to enter a name for new machine. Enter your desired name and press “Next”. The name you choose must be unique. No other vm with that name should exist on the host machine.

Step 4:
On the next screen, you need to select the type of clone you want. Since we want to move our vm to a new host, select “Full clone”.

Step 5:
The final screen requires that you choose if you want only the current machine state or if you want to clone the snapshots too. We want the snapshots so choose the “Everything” option. In my experience, cloning with snapshots takes a significantly longer time.

Step 6:
Press “Clone” and the process begins.

Add A New VM Using The Clone
--------------------------------------

machine B

Step 7:
Now you need to copy the entire folder containing the cloned VM over to the new host. In my case, this folder is found in “C:\Users\Ehi Kioya\VirtualBox VMs”. The folder name will be the machine name you entered in Step 3 above. Make sure this folder contains a “VirtualBox Machine Definition” file (.vbox). Copy this folder to an external or shared drive and then copy it onto the new host (the location doesn’t matter).

Step 8:
Launch VirtualBox on the new host and press “Machine” on the menu. Select “Add” on the dropdown. A Windows explorer browser window will open. Browse to the folder you newly copied over and select the “VirtualBox Machine Definition” file within it. If your new host machine does not have any existing VMs, you should be fine. A new virtual machine containing all the snapshots from the original host will be created. That’s it. You’re done.

However, if your new host machine already has existing VMs, and assuming you always install “VirtualBox Guest Additions” accross all your VMs (which is very likely), then you should get an error like this:

We’ll fix this error in the next post.

https://ehikioya.com/attached-media-conflicts-virtualbox/



