---
layout: post
title: proxmox - physical to virtual migration
---

<h1>{{ page.title }}</h1>

<p class="meta">May 24 2025</p>

<p>this guide will describe how to migrate a physical machine to a proxmox virtual machine.</p>

<h2>create disk image of the physical machine</h2>

<ol>
<li>boot the machine using a linux live cd. I'm using Knoppix 7.2 because it works with the Dual Pentium III machine.</li>

<li>find the source disk</li>
</ol>

<pre><code class="language-shell">lsblk

# assuming the output as follows

# /dev/sda      <-- destination
# /dev/sdb      <-- source
</code></pre>

<ol start="3">
<li>mount destination disk (previously formatted as ext3 for knoppix compatibility)</li>
</ol>

<pre><code class="language-shell">mkdir -p /mnt/destination
mount -t ext3 /dev/sda1 /mnt/destination
</code></pre>

<ol start="4">
<li>create disk image</li>
</ol>

<pre><code class="language-shell">dd if=/dev/sdb of=/mnt/destination/server.img bs=8M conv=noerror,sync
</code></pre>

<h2>import image into virtual machine</h2>

<ol>
<li>create a new proxmox virtual machine without a disk. note the vmid, i'll use vmid 109 as an example.</li>

<li>on the proxmox instance, mount the destination disk.</li>

<li>convert disk image to qcow2</li>
</ol>

<pre><code class="language-shell">qemu-img convert -f raw -O qcow2 server.img server.qcow2
</code></pre>

<ol start="4">
<li>import disk into virtual machine</li>
</ol>

<pre><code class="language-shell"># vm-storage is an example, this would be the storage volume that you store proxmox vm disks.
qm disk import 109 server.qcow2 vm-storage
</code></pre>
