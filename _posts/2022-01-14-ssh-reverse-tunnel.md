---
layout: single
title:  "SSH into a server without a public IP address"
date:   2022-01-13 17:56:35 +0000
categories: linux networking
excerpt: "By setting up a self-healing reverse SSH tunnel"
header:
  # Enter post address in https://cards-dev.twitter.com/validator if title/image is out-of-date when sharing on twitter
  # https://www.linkedin.com/post-inspector/ for linkedin
  image: "/assets/images/common_headers/networking.jpg"
  overlay_image: "/assets/images/common_headers/networking.jpg"
  overlay_filter: rgba(0, 0, 0, 0.5)
  caption: "[Hellfire81 / Wikimedia Commons / Public Domain](https://en.wikipedia.org/wiki/File:Small_business_(SOHO)_network_cabinet_with_equipment.jpg)"

---

This is the scenario: From *B*, you want to SSH into a remote server *A* which does not have a public IP address. However, you have another server *C* which does have a public IP address and is accesible from both *A* and *B*.

![scenario](/assets/images/2022-01-14-ssh-reverse-tunnel/ssh-rev-tunnel.png){:class="img-responsive"}

We will first setup a *reverse tunnel* from *A* to *C* so that you can connect to *C* and "go through the tunnel", so to speak, and connect to *A*.

First, we will need to enable tunneling in *C*. In *C*, edit `/etc/ssh/sshd_config` and change `GatewayPorts` to `yes`:
<div class="language-plaintext highlighter-rouge" style="color: rgb(255,255,255); background-color: rgb(50, 50, 80);"><pre class="highlight">
GatewayPorts yes
</pre></div>
And re-start ssh
<div class="language-plaintext highlighter-rouge" style="color: rgb(255,255,255); background-color: rgb(50, 50, 80);"><pre class="highlight">
sudo systemctl restart sshd
</pre></div>

You might also want to open a port, say `2222` in *C*'s firewall:
<div class="language-plaintext highlighter-rouge" style="color: rgb(255,255,255); background-color: rgb(50, 50, 80);"><pre class="highlight">
sudo ufw allow 2222/tcp
sudo ufw reload
</pre></div>

We will now setup the reverse tunnel from *A* to *C*. On *A*, run
<div class="language-plaintext highlighter-rouge" style="color: rgb(255,255,255); background-color: rgb(80, 50, 50);"><pre class="highlight">
ssh -R 2222:localhost:22 C-USER-NAME@C-IP-ADDRESS
</pre></div>

You can now SSH into *A* from *B* by connecting to *C* on port `2222` with *A*'s username and password. On *B*,
<div class="language-plaintext highlighter-rouge" style="color: rgb(255,255,255); background-color: rgb(50, 80, 50);"><pre class="highlight">
ssh -p 2222 A-USER-NAME@C-IP-ADDRESS
</pre></div>

If you just wanted to connect to *A* once, you're done. But what if you want to connect to it frequently, but you won't always have physical access to *A* to establish the reverse tunnel?

We will write and execute a simple bash script on *A* which will re-establish the reverse tunnel if the connection drops. On *A*, create a file, say `~/revtunnel.sh` with the following contents:
<div class="language-plaintext highlighter-rouge" style="color: rgb(255,255,255); background-color: rgb(80, 50, 50);"><pre class="highlight">
for((;;))
do
	ssh -N -R 2222:localhost:22 C-USER-NAME@C-IP-ADDRESS
done
</pre></div>

Mark the script as executable and run it:
<div class="language-plaintext highlighter-rouge" style="color: rgb(255,255,255); background-color: rgb(80, 50, 50);"><pre class="highlight">
chmod +x ~/revtunnel.sh
~/revtunnel.sh
</pre></div>

There's a problem though. Every time the connection drops, the script will try to re-establish the reverse tunnel and ask us to enter *C*'s password. To fix this, let's add *A*'s public key to *C*'s list of authorized keys.

First, create a public-private key pair on *A* if it doesn't exist.  Hit Enter for all the questions.
<div class="language-plaintext highlighter-rouge" style="color: rgb(255,255,255); background-color: rgb(80, 50, 50);"><pre class="highlight">
ssh-keygen
</pre></div>

Now get the generated public key:
<div class="language-plaintext highlighter-rouge" style="color: rgb(255,255,255); background-color: rgb(80, 50, 50);"><pre class="highlight">
cat ~/.ssh/id_rsa.pub
</pre></div>

And add it to `~/.ssh/authorized_keys` on *C*. Create this file if it doesn't exist.

Now when you run the script on *A*, it doesn't ask you to enter *C*'s password.

You might also want to make sure that this script is executed every time *A* restarts by adding a cron job. On *A*, run
<div class="language-plaintext highlighter-rouge" style="color: rgb(255,255,255); background-color: rgb(80, 50, 50);"><pre class="highlight">
crontab -e
</pre></div>

And add the following:
<div class="language-plaintext highlighter-rouge" style="color: rgb(255,255,255); background-color: rgb(80, 50, 50);"><pre class="highlight">
@reboot su A-USER-NAME -c "/home/A-USER-NAME/revtunnel.sh"
</pre></div>

Now, every time *A* starts up, the script will be automatically run and the reverse tunnel will be re-established.

That's it for now. Stay tuned for more linux and networking tips!