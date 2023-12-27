Hey! I had a lot of issues using my laptop from beginning. It was waking up on it's own, discharging a lot during sleep, even doing BIOS update in backpack (yeah crazy). I have found that there's way to enable S3 Sleep (old one), but that was not stable for me so I went with S0 Low power idle, a.k.a. Modern Standby that microsoft is forcing us to use.

I was able to fix all crap, here are steps:

a. Enable all power plan settings: ... Open Powershell as administrator and paste this line:

(gci 'HKLM:\SYSTEM\CurrentControlSet\Control\Power\PowerSettings' -Recurse).Name -notmatch '\bDefaultPowerSchemeValues|(\[0-9]|\b255)$' | % {sp $_.Replace('HKEY_LOCAL_MACHINE','HKLM:') -Name 'Attributes' -Value 2 -Force}

b. Open Control Panel, go to Power and change these power plan settings:

1. Device Idle Policy (battery): Power Savings
    
2. Disconnected standby mode (both): Aggressive
    
3. Network connectivity in standby (both): Disable
    
4. Sleep, Allow away mode policy (both): No
    
5. Sleep, Allow system required policy (both): No
    
6. Sleep, Wake timers (both): Disable
    
7. Sleep, Allow sleep with remote opens (both): On
    
8. USB, USB3 Link power management (battery): Maximum power savings
    
9. Idle Resilency, Deep sleep (both): Enabled
    
10. Processor power management, Minimum processor state (both): 0%
    
11. Graphics settings, GPU preference policy (battery): Low Power
    
12. Presence aware power behavior, User presence prediction mode (both): Disabled
    
13. AMD power slider, Overlay (battery): Battery saver
    
14. AMD power slider, Overlay (ac): Better performance
    
15. AMD power slider, Energy saver settings, Energy saver policy (battery): Aggressive
    
16. Switchable dynamic graphics, Global settings (battery): Force power-saving graphics
    
17. Switchable dynamic graphics, Global settings (ac): Optimize performance
    
18. Battery, Critical battery action (both): Hibernate
    
19. Presence aware power behavior, Standby budget percent (battery): xx%
    

In 19, you are configuring battery % that can be consumed during sleep.

In 2. Aggressive means more power savings/less wakeups (may be not needed in this setup. 3rd is critical, this will decrease heat and battery usage by a lot. In 6, you are basically blocking any wake up to install updates or other scheduled tasks. 13 and 16 is depending on your usage. I am not really doing anything GPU intensive on the battery so.. (this does not mean you are not able to use GPU, it works normally). 18. is kinda risky, since hibernation is somewhat broken with bitlocker (if you don't know what it is, you are safe). Other settings are more related to battery savings but also help with heat during sleep and some to actually allow sleep in certain scenarios.

Since this, I have consistent sleep every time I close lid, works flawlessly 99% time. You may notice up to 1 minute delay before LED actualy start blinking like it's in sleep, but it is even before just system not yet triggered that effect. It's because there's not actual sleep, it's just in really low power mode. Benefit: When you plug in USB C dock, it will wake up automatically. On disconnect, auto sleep when there's lid closed. I can go trough full week without even opening lid, dock at home and in work :)