# Duckiebot Initialization {#setup-duckiebot status=ready}

This page is for the `DB18` configuration used in classes in 2019. For last year's instructions see [here](https://docs.duckietown.org/DT18/).

<div class='requirements' markdown="1">

Requires: An SD card of size at least 16 GB.

Requires: A computer with a **Ubuntu OS** (for flashing the SD card), an internet connection, an SD card reader, and 16 GB of free space.

Requires: Duckietown Shell, Docker, etc, as configured in [](#laptop-setup).

Requires: Duckietown Token set up as in [](#dt-account).

Results: A correctly configured Duckiebot SD card in configuration `DB18`. After assembling the Duckiebot, this will allow you to start it, connect to the internet, and get going.


</div>


## Burn the SD card {#burn-sd-card}

Warning: this currently only works on Ubuntu. Mac is not supported.

Plug the SD card in the computer using the card reader.

Then initalize it by running the command:

    laptop $ dts init_sd_card [options]

The important options are:

    --hostname         default: duckiebot
    --linux-username   default: duckie
    --linux-password   default: quackquack
    --wifi             default: duckietown:quackquack
    --country          default: US
    --aido             default: False # loads only the things needed for an AI-DO submission

For a full list of the options, run

    laptop $ dts init_sd_card --help

If you plan on connecting with the Duckiebot over different networks (e.g. at home and in class), you can list them like that (note there should be no space after the commas):

    laptop $ dts init_sd_card --wifi duckietown:quackquack,myhomenetwork:myhomepassword,myuninetwork:myunipassword

If you are using a 16GB SD card, also add the `--compress` option.

Make sure to set your country correctly with the `--country` option. (Ex. CA for Canada, CH for Switzerland)

If you want to add additional networks later and you have to edit  the `/etc/wpa_supplicant/wpa_supplicant.conf` file in the `root` drive.

Note: **(For ETH Zurich students only)** To connect to the netoworks on campus use the `--ethz-username` and `--ethz-password` options. Keep in mind that the ETH network prevents hostname resolution so, in general, you won't be able to connect to your Duckiebot despite it being connected to the internet.

After you run the  `dts init_sd_card` command with your options follow the instructions that appear on screen:

- You will then have to enter your laptop's `sudo` password to run Etcher.

- Select the drive with the correct size (usually `/dev/mmcblk0`) by pressing <kbd>Enter</kbd>.

- When asked "Are you sure?" select <kbd>y</kbd>.

When the SD card is completely written, you should arrive at `Press any key to continue`. Do so and the script will exit.

Note: on Ubuntu 16, you need to remove and re-insert the SD card. On Ubuntu 18 this is not necessary.

If the procedure fails with errors about directories not mounted, be patient and do it again, this time leaving the SD card in.

On successful end of the procedure, you can eject (safe remove) the drives and remove the SD card from your laptop.

## Booting the Duckiebot {#duckiebot-boot}

Now insert the SD card into the Raspberry PI and push the button on the battery to power things up.

Warning: Don't charge the battery while you are doing the initialization (or in general when the Duckiebot is turned on). The external power supply might not be able to provide sufficient current and the Raspberry Pi will reboot. Should that happen during the initialization procedure, you will likely have to burn the SD card again.

You should immediately see the **green** LED of the Raspberry Pi next to where the SD card was inserted start to blink with activity.

If not, stop, as there is a problem with the SD card initialization (or possibly the Raspberry Pi, but this is unlikely).

After some time, the **red** and the **green** LEDs of the Raspberry Pi will start to blink alternately. This means that the necessary Docker containers are being extracted. When the process is finished the **red** LED will be off and the **green** will be on.

Warning: Allow the robot time to boot. Wait until only the green Raspberry Pi LED is on, while the red is off. This might take a lot of time.

Do not power the robot off (by holding the battery button) while this is in process.re


You know that your Pi has successfully booted when you are able to ping your robot with the command below or with some method in [](#duckiebot-network):

```
laptop $ ping ![hostname].local
```

Note that you should be connected to the same network as the robot in order to do that. If you are using a virtual machine you should use Bridged connection (typically NAT is used by default).

You should see output similar to the following:​    

```
PING ![hostname].local (![X.X.X.X]): 56 data bytes
64 bytes from ![X.X.X.X]: icmp_seq=0 ttl=64 time=2.164 ms
64 bytes from ![X.X.X.X]: icmp_seq=1 ttl=64 time=2.303 ms
![...]
```
### Troubleshooting

Symptom: The LEDs light up in a variety of colors when the battery is plugged in.

Resolution: The LEDs of the Duckiebot should light up in white as soon as you power the Duckiebot. If the LEDs turn on and shine in any different color than white, probably the code on the microcontroller is corrupted. You can reflash it using the procedure in [](#setup-troubleshooting-boot).


## SSH to the Duckiebot {#setup-duckiebot-ssh}

Next, try to log in using SSH, using

    laptop $ ssh ![hostname]

This should succeed without password.

If it doesn't work, check that `~/.ssh/config` contains something like:

    Host ![hostname]
        User duckie
        Hostname ![hostname].local
        IdentityFile ~/.ssh/DT18_key_00

This configuration was added by the `init_sd_card` command.


## Securing your Duckiebot

By default your Duckiebot uses an SSH key that is the same for all Duckiebots. That means that anyone can access your Duckiebot. If you want to prevent this (in particular if you have your university internet credentials on the Duckiebot), then remove this key by running

    laptop $ ssh ![hostname] rm .ssh/authorized_keys

After this you will be prompted for your password every time you connect to your Duckiebot. If the password which you set in the SD card initialization process was not strong enough, or you kept the default password, we recommend you change it now.

## Rebooting the PI {#setup-duckiebot-reboot}

Warning: Do not test these commands now if you just booted up your duckiebot for the first time. It is likely not finished initializing and shutting down the duckiebot or disconnecting its internet access could interrupt the process and require you to re-flash the SD card.

To reboot:

    laptop $ ssh ![hostname] sudo reboot

## Turn off the PI {#setup-duckiebot-poweroff}

To turn off the Duckiebot, use:

    laptop $ ssh ![hostname] sudo poweroff

Then wait 30 seconds.


Warning: If you disconnect the power before shutting down properly using `shutdown`,
the system might get corrupted.


Then disconnect the USB cable (from the large connector next to the battery).

Warning: If you disconnect frequently the cable at the Raspberry Pi's end, you might damage the port.
