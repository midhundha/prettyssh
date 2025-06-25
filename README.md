# prettyssh

A useful wrapper for the [OpenSSH](https://github.com/openssh/openssh-portable) ssh utility to conveniently pass a custom bash configuration file to the SSH host and display client information in the bash prompt. This wrapper takes advantage of the fact that environment variables that begin with `LC_` are automatically configured to be passed in an SSH session (for more details, have a look at `SendEnv` and `AcceptEnv` lines in `ssh_config` and `sshd_config` files, respectively).

It does the following:
1. Takes username and hostname from the client machine and exports an environment variable `LC_SSH_CLIENT` containing client information: `clientusername@clienthostname <==`.
2. Copies a bash configuration file ([`.ssh_bash_profile`](./.prettyssh-init/.ssh_bash_profile)). This config file:
    * modifies the `PS1` Bash string to include the client information contained in `LC_SSH_CLIENT`.
    * adds the prettyssh utility to the user's `PATH` variable.
    * exports a new/updated `LC_SSH_CLIENT` variable that contains both original client information as well as current host information - to pass to any nested SSH hosts. For example, on an SSH host, the variable after update could be `clientusername@clienthostname <== hostusername@hosthostname <==`.
3. Modifies the `.bashrc` to source ([`.ssh_bash_profile`](./.prettyssh-init/.ssh_bash_profile)). Specifically, the following line is appended to the end of the file:  `source <filepath>/.ssh_bash_profile`

This should work for nested SSH connections as well. 

As an example, the below screenshot shows how the PS1 string in Bash would look in a nested SSH session (client -> host #1 -> host #2).

![Bash prompt with prettyssh](./images/prettyssh.png "prettyssh")

### Note
Since prettyssh copies over files (using scp) and establishes another ssh session to modify `.bashrc` file, it is recommended to use ssh keys to avoid having to enter passwords multiple times.

## Known Issues
Does not behave well on Mac devices (possibly an issue with home bash config files are sourced in the ssh shell). This needs to be investigated further.

## To-do
1. Smarter copying of files (copy only if files do not exist on the host already).
2. Docstrings.
