Useful Hints
============

Running the model on a Microsoft Virtual Machine (VM)
-----------------------------------------------------

Since this program is very resource-intensive it can be useful to run this on a Microsoft VM to free up resources on the local machine. Since some corporate VM's will automatically time out when no activity is detected, which is defined as no movement by the mouse or keyboard, it will time out and close all programs that are currently running. This is, of course, problematic when the program is required to run overnight or a couple of days. This can be prevented by doing the following

#. Open ``Windows PowerShell``
#. Copy the following line in the command propmpt:

.. code-block:: console

    $wsh = New-Object -ComObject WScript.Shell; while (1) {$wsh.SendKeys('+{F15}'); Start-Sleep -seconds 59}

#. Keep the ``Windows PowerShell`` window open until the program has finished and you want to log off

Check the source for more information: `jamesfreeman959/keepawake.ps1 <https://gist.github.com/jamesfreeman959/231b068c3d1ed6557675f21c0e346a9c>`_