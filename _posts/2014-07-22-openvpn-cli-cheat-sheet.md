---
title: OpenVPN CLI Cheat Sheet
author: Evan Hoffman
layout: post
permalink: /2014/07/22/openvpn-cli-cheat-sheet/
dsq_thread_id:
  - 2960268580
categories:
  - Uncategorized
tags:
  - aws
  - cli
  - command
  - linux
  - openvpn
  - sacli
---
<h3 style="color: #333333;">
  Adding a regular user called testing
</h3>

<p style="color: #333333;">
  /usr/local/openvpn_as/scripts/sacli -u testing -k type -v user_connect UserPropPut
</p>

<h3 style="color: #000000;">
  Add an autologin user called knock
</h3>

<p style="color: #333333;">
  /usr/local/openvpn_as/scripts/sacli -u knock -k prop_autologin -v true UserPropPut
</p>

<h3 style="color: #000000;">
  Add an admin user called admin
</h3>

<p style="color: #333333;">
  /usr/local/openvpn_as/scripts/sacli -u admin -k prop_superuser -v true UserPropPut; /etc/init.d/openvpnas restart
</p>

<h3 style="color: #000000;">
  Allow user testing to networks 192.168.0.0/24 and 10.0.0.0/16 via NAT
</h3>

<p style="color: #333333;">
  /usr/local/openvpn_as/scripts/sacli -u testing -k access_to.0 -v +NAT:192.168.0.0/24 UserPropPut; /usr/local/openvpn_as/scripts/sacli -u testing -k access_to.1 -v +NAT:192.168.0.0/16 UserPropPut; /usr/local/openvpn_as/scripts/sacli start
</p>

<h3 style="color: #000000;">
  Allow user testing to networks 192.168.0.0/24 and 10.0.0.0/16 via ROUTE
</h3>

<p style="color: #333333;">
  /usr/local/openvpn_as/scripts/sacli -u testing -k access_to.0 -v +ROUTE:192.168.0.0/24 UserPropPut; /usr/local/openvpn_as/scripts/sacli -u testing -k access_to.1 -v +ROUTE:192.168.0.0/16 UserPropPut; /usr/local/openvpn_as/scripts/sacli start
</p>

<h3 style="color: #000000;">
  Remove access to network entry 0 and 1 for user testing
</h3>

<p style="color: #333333;">
  /usr/local/openvpn_as/scripts/sacli -u testing -k access_to.0 UserPropDel; /usr/local/openvpn_as/scripts/sacli -u testing -k access_to.1 UserPropDel; /usr/local/openvpn_as/scripts/sacli start
</p>

<h3 style="color: #000000;">
  Get installer with profile for user, in this case autologin
</h3>

<p style="color: #333333;">
  ./sacli &#8211;user testing AutoGenerateOnBehalfOf<br /> ./sacli &#8211;user testing &#8211;key prop_autologin &#8211;value true UserPropPut<br /> ./sacli &#8211;itype msi &#8211;autologin -u testing -o installer_testing/ GetInstallerEx
</p>

<h3 style="color: #000000;">
  Get separate certificate files for user, for open source applications
</h3>

<p style="color: #333333;">
  ./sacli -o ./targetfolder –cn test Get5
</p>

<h3 style="color: #000000;">
  Get unified (.ovpn file) for user, for Connect Client for example
</h3>

<p style="color: #333333;">
  ./sacli -o ./targetfolder –-cn test Get1
</p>

<h3 style="color: #000000;">
  Show all users in user database with all their properties
</h3>

<p style="color: #333333;">
  ./confdba -u -s
</p>

<h3 style="color: #000000;">
  Show only a specific user in user database with all properties
</h3>

<p style="color: #333333;">
  ./confdba -u &#8211;prof testuser -s
</p>

<h3 style="color: #000000;">
  Remove a user from the database, revoke his/her certificates, and then kick him/her off the server
</h3>

<p style="color: #333333;">
  ./confdba -u &#8211;prof testing &#8211;rm<br /> ./sacli &#8211;user testing RevokeUser<br /> ./sacli &#8211;user testing DisconnectUser
</p>

<h3 style="color: #000000;">
  Set a password on a user from the command line, when using LOCAL authentication mode:
</h3>

<p style="color: #333333;">
  ./sacli &#8211;user testing &#8211;new_pass passwordgoeshere SetLocalPassword
</p>

<h3 style="color: #000000;">
  Enable Google Authenticator for a user:
</h3>

<pre class="literal-block" style="margin-left: 2.0em;">./sacli --key vpn.server.google_auth.enable --value true ConfigPut
</pre>

&nbsp;