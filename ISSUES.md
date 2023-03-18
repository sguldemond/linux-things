# Issue I run into running Linux

## Laptop won't sleep when after lid close when headset is connected

Date: 2023-03-17
Hardware: Thinkpad T480s, Sony WH-1000XM3 (headset)
Software: Ubuntu MATE 22.04.02, BlueZ 5.64 (5.64-0ubuntu1)

When I close the lid when bluetooth headset is still connected, the red Thinkpad light does not start pulsing, indicating it is not going into sleep mode.

When I open the laptop I cannot easily wake it, maybe by holding down the power button for a bit, but not too long so it turns off.

I could try to install a newer version of bluez that might fix the issue, but I don't want to risk breaking bluetooth all together.

For now I'll live with the quirke, hopefully a new version of bluez will be available for my setup in the future.

This is some of the log:
```
mrt 17 20:55:09 stan-t480s bluetoothd[911]: src/profile.c:ext_io_disconnected() Unable to get io data for Hands-Free Voice gateway: getpeername: Transport endpoint is not connected (107)
mrt 17 20:55:09 stan-t480s acpid[904]: input device has been disconnected, fd 19
mrt 17 20:55:15 stan-t480s kernel: Freezing user space processes ... (elapsed 0.005 seconds) done.
mrt 17 20:55:15 stan-t480s kernel: OOM killer disabled.
mrt 17 20:55:15 stan-t480s kernel: Freezing remaining freezable tasks ... (elapsed 0.001 seconds) done.
mrt 17 20:55:15 stan-t480s kernel: printk: Suspending console(s) (use no_console_suspend to debug)
mrt 17 20:55:15 stan-t480s kernel: e1000e: EEE TX LPI TIMER: 00000011
mrt 17 20:55:15 stan-t480s kernel: xhci_hcd 0000:00:14.0: PM: pci_pm_suspend(): hcd_pci_suspend+0x0/0x40 returns -16
mrt 17 20:55:15 stan-t480s kernel: xhci_hcd 0000:00:14.0: PM: dpm_run_callback(): pci_pm_suspend+0x0/0x1c0 returns -16
mrt 17 20:55:15 stan-t480s kernel: xhci_hcd 0000:00:14.0: PM: failed to suspend async: error -16
mrt 17 20:55:15 stan-t480s kernel: PM: Some devices failed to suspend, or early wake event detected
mrt 17 20:55:15 stan-t480s kernel: nvme nvme0: Shutdown timeout set to 8 seconds
mrt 17 20:55:15 stan-t480s kernel: nvme nvme0: 8/0/0 default/read/poll queues
mrt 17 20:55:15 stan-t480s kernel: e1000e 0000:00:1f.6 enp0s31f6: Failed to disable ULP
```

## Fixed issue (2023-03-18)

For now I fixed the issue, by removing the headset from the paired devices in "Bluetooth Devices" window, and then repairing the headset. When the headset is paired and I close the laptop, first the headset gets disconnected -- which I can hear through the headset -- and then the laptop starts its sleep mode -- which I can tell by the red pulsing Thinkpad light on the lid. 
