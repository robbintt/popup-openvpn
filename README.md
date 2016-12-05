
### Digital Ocean Ubuntu 16.04 LTS OpenVPN + Stouts.openvpn

This playbook will quickly pop up an (optionally disposable) OpenVPN VPN service.

If you can 'kind of' run a server that should be enough to manage this.

**When you run this playbook for the first time, you will need to restart the server before you can connect.**


#### Installation Guide

1. [Make an account and get a $10 free credit at digital ocean](https://m.do.co/c/a4d54c9e5004)

2. Add your `SSH key` to `Digital Ocean`
    1. Enter the digital ocean web interface
    2. go to `settings->security->SSH keys`
    3. Paste your key, found by default `$HOME/.ssh/id_rsa.pub`
        - tip: make a key with `ssh-keygen`
        - danger: `ssh-keygen` WILL overwrite your current key without warning
    4. On server creation, the key will be added in server's `root` user's `authorized_keys`
    5. `ansible` reads from the default `id_rsa.pub` location
        - you can manually set a key as a command line argument in `manage.sh`

3. Create a new server on Digital Ocean
    1. Choose the `Ubuntu 16.04.1 x64` base image 
    2. Use the $5 option
    3. Choose a datacenter near you for low latency.
    4. Check your preferred `ssh key` under `Add your SSH keys`
    5. Choose a hostname like `popup-openvpn`
    6. Do not check `use IPv6`
    7. Notes
        - You will have 1 terabyte of monthly transfer
        - They call a `server` a `droplet`

4. Configure this repo for your users
    1. Clone this repository on your local computer
        - `git clone https://github.com/robbintt/popup-openvpn.git`
    1. Configuration files: 
        - `hosts` 
        - `group_vars/all`
    2. Add the IP address of the server to your `hosts` file
        - The `IP address` is available in your digital ocean dashboard
        - In the `[vpnservers]` section, replace the default 1.2.3.4 with your server's IP address
    3. Add each of the following to your `group_vars/all`
        - You need at least one user and password
        - `user` 
            - any username is fine in user
        - `password`
            - use a strong password
        - `openvpn_client` 
            - any name is fine -- the default is `myvpn1` etc.
        - **Replace or delete the example users and passwords entirely**
    4. You can add as many openvpn_client sections and user/passwords as you wish
    5. IMPORTANT: Delete any extra user lines and unnecessary example certificates, e.g. `myvpn3`, `myvpn4`

5. It's time to automatically set things up!!
    1. Install `ansible` locally
        - Optional: do this using your preferred method
        - `todo` (pip method is cross platform)
    2. execute: `manage.sh`
        - The ansible script should run without any errors
        - (The restarter role fails if it doesn't need to restart or something)
    3. At the end of the script your server needs restarted. I don't know why
        - todo - just restart the server every time the playbook is run or something
    4. To reconfigure or update your server you may run `manage.sh` again

6. email the .ovpn files to all your users
    - The ansible playbook transferred the .ovpn files to the computer you ran ansible from
    - Each ovpn file is stored in `./openvpn_files/` (inside the `popup-openvpn` project directory)
    - Each user only needs the ovpn file generated for them. They also need their username and password
    - You will open this file in your `OpenVPN Client`

7. Install a client on each of your devices.  You will need: `user`, `password`, `.ovpn file`
    1. Client Recommendations
        - macos: [Tunnelblick](https://tunnelblick.net/)
            - option: use `brew cask` to install
        - iphone app store: `OpenVPN` by OpenVPN Technologies
        - android app store: `OpenVPN Connect` by OpenVPN Technologies
    2. You will need to follow the client specific instructions
        - TODO: Assemble links for client instructions
    3. All your users need to do this step for all their devices

8. Maintaining your server
    - TODO


#### Requirements

- Server: Digital Ocean Ubuntu 16.04.1 Release
- Local `~/.ssh/id_rsa.pub` private key deployed to remote root `authorized_keys`
    - This is done through Digital Ocean on droplet deployment


#### Future

1. Use ansible vault to encrypt group_vars/all
2. Make a workflow for adding a new user and client certificate "for a friend"
3. Add an automatic updates tool. 
4. Move Stouts.openvpn default to 2048 bit certificate.
    - Add variables in `group_vars/all`
    - needs testing
5. Test this guide from very beginning to very end.
    - Beginning: Create a Digital Ocean Account
    - End: Install the ovpn cert and put the password in on all your devices
    - Very End: Maintaining your server
6. Deploy to the world at large (reddit? digital ocean article?)


#### Stouts.openvpn

The [Stouts.openvpn](https://github.com/Stouts/Stouts.openvpn) repo is included as a subrepo in `/roles/Stoutsopenvpn`. 


### References

Some useful links and additions.


#### Using Ansible Vault

You might use this to encrypt your `group_vars/all`

- Encrypt a file: `ansible-vault encrypt foo.yml bar.yml baz.yml`
- Decrypt a file: `ansible-vault decrypt foo.yml bar.yml baz.yml`
- View file contents: `ansible-vault view foo.yml`
- Run Playbook w/ Vault password: `ansible-playbook site.yml --ask-vault-pass`
    - This is managed in `manage.sh` in this script


##### Links

- [Manually building an ovpn file with certs and keys inside](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-openvpn-server-on-ubuntu-14-04)

