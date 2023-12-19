## HPN-SSH Installation

The process of installing HPN-SSH from source is a relatively painless process
but does have some nuances. This document will go through the process step by 
step to help you get the most from your installation. If you find any errors 
please contact us at hpnssh@psc.edu.


### Step 1: Get the source code

The official repository for HPN-SSH is found at 
https://github.com/rapier1/hpn-ssh. Get a copy with<br /> 
`git clone https://github.com/rapier1/hpn-ssh`. 


### Step 2: Install dependencies

* What you need to install is dependent on your distribution but will include:
* OpenSSL development package
	* Debian: libssl-dev
    * Fedora: openssl-devel
	* Alternatively you can use LibreSSL
		* However, in this case we suggest compiling and installing libressl manually as there are few maintained linux packages for LibreSSL. 
		* Also, LibreSSL v3.5 and v3.6 do not support the threaded AES-CTR cipher. If that’s important to you then you should use OpenSSL. 
* Z compression library
    * Debian: zlib1g-dev
    * Fedora: zlib-devel
* Autoconf
* Automake


### Step 3: Install optional dependencies

This optional libraries will extend the functionality of HPN-SSH to allow the use of PAM 
authentication, Kerberos, graphical password tools, etc. 

* PAM
* Kerberos
* GTK


### Step 4: Build the configure file

> `generate ./configure with “autoreconf -f -i"`



### Step 5: Configuration

Configure the installation. You can get detailed information on how to do this by 
issuing `./configure --help`. However, commonly you will want to change the default installation location of the binaries. This can be done with `--prefix=/[desired_path]`. For example, if you want the binaries installed into `/usr/bin` as opposed to the default of /usr/local/bin you’d use `./configure --prefix=/usr`. Other common options would be to 
incorporate pam, kerberos, alternative SSL libraries, and so forth. However, for most users 
either no additional configuration options or modifying the prefix will suffice. 


### Step 6: Make

Make the application with `make -j[<em>num cores</em>]`. So if you have an 8 core system 
you’d use `make -j8`


### Step 7: Installation

After HPN-SSH successfully builds, install it with `sudo make install`. This will install the binaries, configuration files, and generate the unique host keys used. At this point you can make changes to the ssh client and server default configuration. These files are 
found, generally, in /etc/hpnssh/ssh_config and sshd_config respectively. You may want to 
change the default port from 2222 to some other value. You may also want to enable the 
NoneCipher and NoneMac options. For more information use `man hpnsshd_config` and 
`man hpnssh_config`. Note: The hpnssh client expects the server to be on port 2222 but will 
fallback to 22 if it’s not found there. So if you do change the default port, you’ll need to 
make sure the clients point at the correct port. 


### Step 8: Set up the hpnsshd user

This user is part of the privilege separation routines used in the 
pre-authentication sandbox. I suggest using the following command: <br />
<span style="whitespace:nowrap">
`sudo useradd --system --shell /usr/sbin/nologin --comment="Privilege separated HPNSSH User" --home=/run/hpnsshd hpnsshd`
</span><br />
Alternatively, you can use `vipw` to add the user manually. 


### Step 9: Finishing up

At this point you can start hpnsshd manually by running `sudo /usr/sbin/hpnsshd` or whatever the full path to the hpnsshd binary might be. However, this won’t restart automatically on reboot. To do this you’ll need to install an appropriate systemd configuration file. If that seems like a good idea to you then following steps may be of help. Otherwise, you are done. Enjoy!


### Step 10: Installing a systemd startup file

The correct systemd startup file depends on the distribution you are using. For system 
using systemd (you start a service with systemctl) create a file at `/lib/systemd/system/hpnssh.service`  with the following contents. NB: you may need to update the paths to match your installation: 

```
[Unit]
Description=HPN/OpenBSD Secure Shell server
Documentation=man:hpnsshd(8) man:hpnsshd_config(5)
After=network.target auditd.service
ConditionPathExists=!/etc/hpnssh/sshd_not_to_be_run

[Service]
EnvironmentFile=-/etc/default/hpnssh
ExecStartPre=/usr/sbin/hpnsshd -t
ExecStart=/usr/sbin/hpnsshd -D $SSHD_OPTS
ExecReload=/usr/sbin/hpnsshd -t
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process
Restart=on-failure
RestartPreventExitStatus=255
Type=notify
RuntimeDirectory=hpnsshd
RuntimeDirectoryMode=0755

[Install]
WantedBy=multi-user.target
Alias=hpnsshd.service
```

Then create the defaults file at `/etc/defaults/hpnssh` with the following content:

```
# Default settings for openssh-server. 
# Options to pass to sshd
SSHD_OPTS= 
```

Enter any runtime options you want on the SSHD_OPTS line. If you can’t think of any, simply 
leave it blank.

You must then reload the systemd service to make it aware of this new service with 
sudo systemctl daemon-reload

If you are using an init.d (you start a service with `system`) then you need to install an 
init.d. Create the file `/etc/init.d/hpnssh` and copy the following into it. NB: The following is for where hpnsshd is found at `/usr/sbin/hpnsshd`. If it is not in that location you’ll need to update the paths.  

```
#! /bin/sh

### BEGIN INIT INFO
# Provides:                hpnsshd
# Required-Start:        $remote_fs $syslog
# Required-Stop:        $remote_fs $syslog
# Default-Start:        2 3 4 5
# Default-Stop:                
# Short-Description:        OpenBSD Secure Shell server with HPN
### END INIT INFO

set -e

# /etc/init.d/hpnssh: start and stop the OpenBSD "secure shell(tm)" daemon

test -x /usr/sbin/hpnsshd || exit 0
( /usr/sbin/hpnsshd -\? 2>&1 | grep -q OpenSSH ) 2>/dev/null || exit 0

umask 022

if test -f /etc/default/hpnssh; then
    . /etc/default/hpnssh
fi

. /lib/lsb/init-functions


if [ -n "$2" ]; then
    SSHD_OPTS="$SSHD_OPTS $2"
fi

# Are we running from init?
run_by_init() {
    ([ "$previous" ] && [ "$runlevel" ]) || [ "$runlevel" = S ]
}

check_for_no_start() {
    # forget it if we're trying to start, and /etc/hpnssh/sshd_not_to_be_run exists
    if [ -e /etc/hpnssh/sshd_not_to_be_run ]; then 
        if [ "$1" = log_end_msg ]; then
            log_end_msg 0 || true
        fi
        if ! run_by_init; then
            log_action_msg "HPN/OpenBSD Secure Shell server not in use (/etc/hpnssh/sshd_not_to_be_run)" || true
        fi
        exit 0
    fi
}

check_dev_null() {
    if [ ! -c /dev/null ]; then
        if [ "$1" = log_end_msg ]; then
            log_end_msg 1 || true
        fi
        if ! run_by_init; then
            log_action_msg "/dev/null is not a character device!" || true
        fi
        exit 1
    fi
}

check_privsep_dir() {
    # Create the PrivSep empty dir if necessary
    if [ ! -d /run/hpnsshd ]; then
        mkdir /run/hpnsshd
        chmod 0755 /run/hpnsshd
    fi
}

check_config() {
    if [ ! -e /etc/hpnssh/sshd_not_to_be_run ]; then
        # shellcheck disable=SC2086
        /usr/sbin/hpnsshd $SSHD_OPTS -t || exit 1
    fi
}

export PATH="${PATH:+$PATH:}/usr/sbin:/sbin"

case "$1" in
  start)
        check_privsep_dir
        check_for_no_start
        check_dev_null
        log_daemon_msg "Starting HPN/OpenBSD Secure Shell server" "hpnsshd" || true
        # shellcheck disable=SC2086
        if start-stop-daemon --start --quiet --oknodo --chuid 0:0 --pidfile /run/hpnsshd.pid --exec /usr/sbin/hpnsshd -- $SSHD_OPTS; then
            log_end_msg 0 || true
        else
            log_end_msg 1 || true
        fi
        ;;
  stop)
        log_daemon_msg "Stopping HPN/OpenBSD Secure Shell server" "hpnsshd" || true
        if start-stop-daemon --stop --quiet --oknodo --pidfile /run/hpnsshd.pid --exec /usr/sbin/hpnsshd; then
            log_end_msg 0 || true
        else
            log_end_msg 1 || true
        fi
        ;;


  reload|force-reload)
        check_for_no_start
        check_config
        log_daemon_msg "Reloading HPN/OpenBSD Secure Shell server's configuration" "hpnsshd" || true
        if start-stop-daemon --stop --signal 1 --quiet --oknodo --pidfile /run/hpnsshd.pid --exec /usr/sbin/hpnsshd; then
            log_end_msg 0 || true
        else
            log_end_msg 1 || true
        fi
        ;;


  restart)
        check_privsep_dir
        check_config
        log_daemon_msg "Restarting HPN/OpenBSD Secure Shell server" "hpnsshd" || true
        start-stop-daemon --stop --quiet --oknodo --retry 30 --pidfile /run/hpnsshd.pid --exec /usr/sbin/hpnsshd
        check_for_no_start log_end_msg
        check_dev_null log_end_msg
        # shellcheck disable=SC2086
        if start-stop-daemon --start --quiet --oknodo --chuid 0:0 --pidfile /run/hpnsshd.pid --exec /usr/sbin/hpnsshd -- $SSHD_OPTS; then
            log_end_msg 0 || true
        else
            log_end_msg 1 || true
        fi
        ;;


  try-restart)
        check_privsep_dir
        check_config
        log_daemon_msg "Restarting HPN/OpenBSD Secure Shell server" "hpnsshd" || true
        RET=0
        start-stop-daemon --stop --quiet --retry 30 --pidfile /run/hpnsshd.pid --exec /usr/sbin/hpnsshd || RET="$?"
        case $RET in
            0)
                # old daemon stopped
                check_for_no_start log_end_msg
                check_dev_null log_end_msg
                # shellcheck disable=SC2086
                if start-stop-daemon --start --quiet --oknodo --chuid 0:0 --pidfile /run/hpnsshd.pid --exec /usr/sbin/hpnsshd -- $SSHD_OPTS; then
                    log_end_msg 0 || true
                else
                    log_end_msg 1 || true
                fi
                ;;
            1)
                # daemon not running
                log_progress_msg "(not running)" || true
                log_end_msg 0 || true
                ;;
            *)
                # failed to stop
                log_progress_msg "(failed to stop)" || true
                log_end_msg 1 || true
                ;;
        esac
        ;;


  status)
        status_of_proc -p /run/hpnsshd.pid /usr/sbin/hpnsshd hpnsshd && exit 0 || exit $?
        ;;


  *)
        log_action_msg "Usage: /etc/init.d/hpnssh {start|stop|reload|force-reload|restart|try-restart|status}" || true
        exit 1
esac

exit 0
```


### Step 10: Working with SELinux

If you are using SELinux you’ll need to run a few more commands in order to grant hpnssh the necessary exceptions to open sockets, files, read keys, and so forth. Run the following commands to allow this. Note: I’m not sure every single one of these is needed so if someone knows better please let me know. Again, double check the paths of the files being updated. 

```
semanage fcontext -a -f f -t sshd_key_t  /etc/hpnssh/ssh_host_dsa_key  
semanage fcontext -a -f f -t sshd_key_t  /etc/hpnssh/ssh_host_rsa_key
semanage fcontext -a -f f -t sshd_key_t  /etc/hpnssh/ssh_host_ecdsa_key 
semanage fcontext -a -f f -t sshd_key_t  /etc/hpnssh/ssh_host_ed25519_key 
semanage fcontext -a -f f -t sshd_key_t /etc/hpnssh/ssh_host_dsa_key.pub 
semanage fcontext -a -f f -t sshd_key_t /etc/hpnssh/ssh_host_rsa_key.pub 
semanage fcontext -a -f f -t sshd_key_t  /etc/hpnssh/ssh_host_ecdsa_key.pub 
semanage fcontext -a -f f -t sshd_key_t  /etc/hpnssh/ssh_host_ed25519_key.pub 
semanage fcontext -a -f f -t sshd_exec_t /usr/sbin/hpnsshd 
semanage fcontext -a -f f -t sshd_keygen_exec_t /usr/libexec/hpnssh/hpnsshd-keygen 
semanage fcontext -a -f f -t bin_t /usr/libexec/hpnssh/hpnsftp-server 
semanage fcontext -a -f f -t ssh_exec_t /usr/bin/hpnssh 
semanage fcontext -a -f f -t ssh_agent_exec_t /usr/bin/hpnssh-agent 
semanage fcontext -a -f f -t ssh_keygen_exec_t /usr/bin/hpnssh-keygen 
semanage fcontext -a -f f -t etc_t /etc/pam.d/hpnsshd 
semanage port -a -t ssh_port_t -p tcp 2222 
restorecon /usr/sbin/hpnsshd
restorecon /etc/hpnssh/ssh*_key
restorecon /etc/hpnssh/ssh*_key\.pub
restorecon /usr/libexec/hpnssh/hpnsshd-keygen
restorecon /usr/libexec/hpnssh/hpnsftp-server
restorecon /usr/bin/hpnssh
restorecon /usr/bin/hpnssh-agent
restorecon /usr/bin/hpnssh-keygen
restorecon /etc/pam.d/hpnsshd
```
