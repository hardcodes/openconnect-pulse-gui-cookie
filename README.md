*Forked from [utknoxville/openconnect-pulse-gui](https://github.com/utknoxville/openconnect-pulse-gui)*.

Status: **WIP**

# openconnect-pulse-gui

This script provides a browser login through a WebKitGTK2 window. The authentication cookie is printed to the console so that it can be used to call [OpenConnect](https://www.infradead.org/openconnect/) with parameter `--cookie=<AUTH-COOKIE>` afterwards. This allows OpenConnect to be compatible with web-based authentication mechanisms, such as SAML.

In contrast to the original project the browser can run as regular user account. Only `openconnect` has to called with `sudo` as user `root` to be able to add the tunneling network device. If `openconnect` is started with parameter `--setuid=${USER}` it will drop the root privileges to your user account after the connection has been established.


## Requirements

The script can be used with python2 or python3, however python3 is recommended.  The following packages are also required:

 - python-gi or python-gobject
 - webkit2gtk
 - openconnect

Instruction for specific distros can be found below.


### Debian/Ubuntu

    sudo apt install python3-gi gir1.2-webkit2-4.0 openconnect


### Fedora

    sudo yum install python-gi webkit2gtk3 openconnect


### Arch

    sudo pacman -S python-gobject webkit2gtk openconnect


## Installation

**Version A**

This repo can be downloaded with `git clone https://github.com/hardcodes/openconnect-pulse-gui-cookie.git` or via the GitHub webpage.

- Copy the `openconnect_pulse_gui.py` script to a directory of your liking (it should be in your `$PATH` if you want to call it from your shell).

**Version B**

- Copy (`openconnect_pulse_gui.py`)(https://raw.githubusercontent.com/hardcodes/openconnect-pulse-gui-cookie/refs/heads/master/openconnect_pulse_gui/openconnect_pulse_gui.py) to a directory of your liking (it should be in your `$PATH` if you want to call it from your shell).


## Usage

Simply call the script with the sign-in link / server URL as only required argument.

```bash
python3 openconnect_pulse_gui.py <URL>
```

Other arguments can be found by using

```bash
python3 openconnect-pulse-gui.py -h
```

Note that this script will **not** run openconnect, it will only print the command with the correct arguments to stdout.

If you want to capture the output in a variable before invoking `openconnect`, you can call it this way:

```bash
SSO_LOGIN_COOKIE=$(python3 openconnect_pulse_gui.py <URL>)
sudo -p "sudo (openconnect): " openconnect --background --protocol=nc --user=<VPN user account> --setuid=${USER} --cookie="${SSO_LOGIN_COOKIE}" <URL>
```


## Login process

Anybody wishing to recreate this functionality either manually or using another library can with the following steps:

1. Send the user to the sign-in URL.  This will either give them the ability to log in directly or redirect them to an external authentication server.
1. Wait for a `Set-Cookie` header that contains the `DSID` cookie.  This is the authentication cookie used by Pulse Secure.
1. Pass the cookie to `openconnect` using `--protocol nc` and `-C 'DSID=<cookie-value>'`.  Note that some workflows may work with `--protocol pulse`, but at this time SAML-based logins do not.


