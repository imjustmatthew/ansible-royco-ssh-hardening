SSHD Hardening
=========

This role hardens an SSH server based on the [Mozilla InfoSec guidelines](https://infosec.mozilla.org/guidelines/openssh.html)

This role alone is probably not good enough for an internet-facing server. This is meant to be a "secure by default" configuration you can just apply and not think any more about for internal servers that are reasonably low risk.

Implementation notes:

 - This assumes SSHD is already installed
 - This does not configure firewalling or Allow/Deny lists that you may want for high-risk systems
 - By default this disables SSH agent forwarding since it is risky. [This is the reccommmended alternative](https://infosec.mozilla.org/guidelines/openssh.html#recommended-safer-alternatives-to-ssh-agent-forwarding)

Requirements
------------

Tested only on Ubuntu 24.04, though it probably works with all modern Ubuntu.

Role Variables
--------------

See [argument_specs.yml](meta/argument_specs.yml) for a list of variables in this role.

All variables have a default set in [defaults/main.yml](defaults/main.yml)

Dependencies
------------

None

Example Playbook
----------------

Basic, secure-by-default implementation:

    - hosts: servers
      roles:
         - { role: ansible-royco-ssh-hardening }

Specify authentications methods to require key+password or key+keyboard-interactive:

    - name: Setup SSH on servers
      hosts: servers
      roles:
        - role: ../ansible-royco-ntp
          vars:
            - ansible_royco_ssh_hardening_authmethods: "publickey,password publickey,keyboard-interactive"

You can find more details about how to use `AuthenticationMethods` in [man sshd_config(5)](https://www.man7.org/linux/man-pages/man5/sshd_config.5.html)

[Go read this if you are trying to use things like PublicKey + Duo or other "2FA" solutions.](https://infosec.mozilla.org/guidelines/openssh.html#multi-factor-authentication-openssh-63) You can easily accidentally allow password auth to work without realizing it.


Skip "extras" for servers that might need things like X11 forwarding or TCP forwarding:

    - name: Setup SSH on servers that need to Port forward
      hosts: servers
      roles:
        - role: ../ansible-royco-ntp
          vars:
            - ansible_royco_ssh_hardening_extras: false

See [the sshd_config template](templates/00-ssh-hardening.conf.j2) to see what this controls.


License
-------

[GNU GENERAL PUBLIC LICENSE Version 3](LICENSE)

Author Information
------------------

[Matthew Roy](https://matthew.royhousehold.net)
