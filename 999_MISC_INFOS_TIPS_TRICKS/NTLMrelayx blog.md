# Relaying credentials everywhere with ntlmrelayx

Source:

~ blog.fox-it.com/author/dirkjanm/ "Posts by dirkjanm" ~
~blog.fox-it.com~

Insecurities in NTLM Authentication have been known about
 for over 15 years. The protocol can be abused to hijack a victim’s
 session through a process called “relaying”, which abuses a victim’s
 credentials by forwarding them to a different service than intended. NTLM authentication is still supported and enabled by default in many cases, even though it has been replaced as default authentication method by the more secure Kerberos.

## NTLM Relaying explained

NTLM Authentication is a challenge-response based protocol. Challenge-response protocols use a commonly shared secret, in this case the user password, to authenticate the client. The server sends a challenge, and the client replies with the response on this challenge. If the challenge matches the one calculated by the server, the authentication is accepted. The NTLM Authentication is a complex protocol, and how it is explained here is a simplification.
A very good and detailed description can be found at http://davenport.sourceforge.net/ntlm.html

## NTLM Authentication flow

There are 3 steps in the NTLM Authentication protocol:

1.  **Negotiate authentication**: The first step of NTLM authentication is the negotiation of the protocol, and which features are supported by the client. In this stage, the client sends the request for authentication to the server, including the NTLM versions accepted by the client.
2.  **Server challenge**: The server responds with its own message, indicating which NTLM versions it accepts and which features it wants to use. This message also includes a “challenge” value, which is important in the authentication.
3.  **Authentication response**: The client sends back the response based on the challenge, and includes the username and domain to which the password belongs.

After the 3 messages are exchanged, the server replies with either a message indicating that the authentication was successful, or that the authentication failed. Depending on which protocol is used, the session the client has with the server is now authenticated. This process is displayed in the figure below:

![ntlm_auth](../../_resources/ntlm_auth_b446715f635d4a09b131093db4ae25e3.png)

## Abusing NTLM

As an attacker, this process can be abused if a client can be convinced to connect to an attacker. How this can be done is explained in the next section. Once an attacker has a connected client willing to authenticate, they can easily forward the 3 messages to the server between client and the server until the challenge-response cycle is complete.

![relay_ntlm](../../_resources/relay_ntlm_5e4b2433a1384b1d89b9c3352dcd21d7.png)At the point where the connection is authenticated, the attacker can simply send an error message to the client, or drop the connection. Afterwards the attacker can use the session to interact with the server from the context of the user from which the authentication was relayed.

## Cross protocol relaying

The NTLM authentication is encapsulated within other protocols, but the messages are the same regardless of the overlying protocol. This allows for using the NTLM messages in other protocols. For example a client that authenticates using HTTP sends the NTLM authentication messages within the “Authorization” header. An attacker can take these messages out of the HTTP header and use them in other protocols, such as SMB.

NTLM is supported in several protocols, for example SMB, HTTP(S), LDAP, IMAP, SMTP, POP3 and MSSQL.

## Obtaining traffic

A point which has not been explained yet, is how to get clients to connect with the attacker instead of with the real server. There are several ways to obtain traffic which can be relayed:

- Traffic to hosts for which the IP is resolved in an insecure manner
- Traffic resulting from the abuse of AutoDiscovery protocols
- Traffic which is obtained through a man-in-the-middle attack

## Insecure name resolution protocols

It happens quite regularly that Fox-IT encounters name resolution traffic using insecure protocols. Workstations or servers are often configured to contact hosts which do not exist (anymore) in the network, and/or whose hostnames cannot be resolved using DNS. When this happens, Windows workstations fall back to name resolution protocols such as NBNS and LLMNR, which rely on broadcast traffic to ask hosts in the same network to resolve a hostname to an IP address. Because this traffic can be viewed by all hosts in the same network segment (depending on firewall configuration), any host can reply to the requests. This gives an attacker the opportunity to spoof the address for the requested name. This process is illustrated below.

![NBNS_spoof](../../_resources/nbns_spoof_6eb31caa05e14f838a8b7c2a86a6d076.png)

## Auto Discovery protocols

Perhaps the most infamous feature among hackers for the past years was the Windows Proxy Auto Detection (WPAD) feature. This feature would essentially look for a hostname called WPAD, via DNS and if not successful via LLMNR and NBNS as described above, and then connect to the first host it could find. Abusing this feature was made even easier because when prompted for authentication, workstations would automatically attempt to authenticate with NTLM Authentication, which could then be relayed by an attacker. Several aspects of this have been patched by Microsoft in June 2016, but at times Fox-IT still encounters this in networks.

## Man-in-the-middle attack

Man-in-the-middle attacks, where an attacker takes over the traffic of a victim, are often quite disruptive in enterprise networks, especially when using techniques such as ARP spoofing. However, when corporate devices are connected to untrusted networks (such as public WiFi networks), an attacker can attack the victim and intercept traffic which is not secured with TLS, redirecting this to a location trusted by the victim’s workstation. The victim will then automatically authenticate if Automatic Intranet Detection is enabled (which is the default).

![autologon](../../_resources/autologon_806dc3f31c2348fc97ed55c043783960.png)

## Using ntlmrelayx to relay NTLM everywhere

There are several tools available which abuse NTLM Authentication. One of those is smbrelayx, part of Core Security’s [impacket library](https://github.com/CoreSecurity/impacket). Ntlmrelayx is an extension and partial rewrite of the smbrelayx tool, developed by Fox-IT. It features relaying to a wide range of protocols. The tool accepts multiple targets, cycling through each to find systems to authenticate to. The tool features an SMB and HTTP server, from which it can relay NTLM authentication to SMB, HTTP(s), IMAP, LDAP and MSSQL.

## Relaying to SMB

Relaying to SMB is the classic attack, which was already part of smbrelayx. If you are unfamiliar with this attack, relaying to SMB allows attackers to execute files on hosts that have SMB signing disabled if the user being relayed has administrative privileges on the box. For non-administrative users, ntlmrelayx adds the option to launch a smbclient shell, which allows attackers to interact with shares, for example to download or upload files. This attack can be done with the interactive flag (-i), which will spawn a local TCP shell, which can be connected to with for example netcat.

![interactive_smb](../../_resources/interactive_smb_7881189cd8164444a40544f1fce257ae.png)![interactive_smb_1](../../_resources/interactive_smb_1_a0582e79d7d8469e949871b068e5bdf1.png)

## Relaying to LDAP

Relaying to LDAP is a new addition in ntlmrelayx. LDAP is an interesting protocol because it is used to directly query the directory, which contains a lot of interesting information for an attacker. Even more interesting is that most of this information is by default readable by all accounts in the domain (including computer accounts). This is where ntlmrelayx integrates with [ldapdomaindump](https://github.com/dirkjanm/ldapdomaindump), another Fox-IT developed tool. This tool tries to gather as much information as possible from the domain, including users, their group memberships, domain computers and the domain policy.

![ldd_clean](../../_resources/ldd_clean_8cabef1cd956459e812f9db4e4fc0576.png)Aside from gathering information, it is also possible to write to the directory via LDAP. If ntlmrelayx encounters a user with Domain Administrator privileges, it will create a new Domain Admin account, which instantly gives the attacker full control over the domain:

![da_add](../../_resources/da_add_0d74452c38c2439eaa66748620a18aba.png)

## Relaying to IMAP

While not enabled by default in modern versions of Exchange, many organizations support NTLM authentication via IMAP on their exchange server. This allows for relaying to IMAP, giving an attacker direct access to emails of the victim. When relaying to IMAP, ntlmrelayx has the option to search for keywords in emails, or to just download all the latest emails in a specified inbox of the user.

![imap_relay](../../_resources/imap_relay_1d9d85c5fcb64d54a34347b869dd0cbf.png)![password](../../_resources/password_6118a3acc4a14e9e921bd67d988f2f1f.png)

## Relaying to MSSQL

Relaying to MSSQL currently only exists as a proof-of-concept, but it is possible to specify queries on the command line, which will be executed in the context of the victim on the database.

## Mitigations

So what can organizations do against these attacks? All the attacks above abuse the NLTM authentication protocol, so the only complete solution to this is [disabling NTLM entirely](https://technet.microsoft.com/en-us/library/jj865668%28v=ws.10%29.aspx) and switching to Kerberos. Many organizations however have legacy products or operating systems that do not support Kerberos authentication, and thus disabling NTLM would have a considerate business impact. As a mitigating factor, there are several settings that can be enabled to minimize the risk of relaying.

- **Enable SMB signing**: SMB signing will prevent relaying to SMB by requiring all traffic to be signed. Signing requires the user password to authenticate the messages, and thus an attacker relaying the connection cannot send any traffic that will be accepted by the server, since the attacker does not possess the victim’s password.
- **Enable LDAP signing**: Similar to SMB signing, LDAP signing prevents unsigned connections to LDAP. It should be noted that connections to LDAP that happen over TLS are considered signed, so this setting will not prevent relay attacks to LDAP over TLS.
- **Enable extended protection for authentication**: [Extended protection for authentication](https://msdn.microsoft.com/en-us/library/dd767318%28v=vs.90%29.aspx) helps prevent some relaying attacks by ensuring that the TLS channel used for the connection to the server is the same that the client uses when authenticating. This setting mainly applies to IIS.
- **Enable SPN target name validation**: [SPN target name validation](https://technet.microsoft.com/en-us/library/jj852272%28v=ws.11%29.aspx) is another measure which prevents relaying to SMB by validating the target name to which the client thinks it is authenticating. If the name does not match with the server, the authentication is refused.
- **Ensure internal websites use HTTPS**: When internal websites are visited over the insecure HTTP protocol, there is no possible way for users to validate the authenticity of the connection. By enforcing all internal websites to only function over HTTPS, relaying becomes much less effective.

## General hardening to prevent relaying

Aside from these specific server-side measurements, the following general hardening can prevent NTLM relaying:

- **Disable automatic intranet detection**: If NTLM authentication is required in the domain, make sure that browsers (mainly Internet Explorer) only automatically authenticate to trusted websites. Via Group Policy it is possible to disable automatic intranet detection and only automatically authenticate to a whitelist of internal websites to which automatic authentication should apply. As mentioned previously, it is strongly recommended to only use HTTPS websites here.
- **Disable Windows Proxy Auto Detection**: While the security issues of WPAD have been mostly addressed by the Microsoft [MS16-077](https://support.microsoft.com/en-us/help/3165191/ms16-077-security-update-for-wpad-june-14,-2016) security update, it is still recommended to disable WPAD in general via Group Policy.
- **Disable LLMNR/NBNS**: These insecure name resolution protocols are often not required in well configured networks. Disabling them gives an attacker fewer possibilities for name resolution spoofing, which in turn makes it harder for attackers to trick victims in connecting to the attackers server.

## Obtaining ntlmrelayx

Ntlmrelayx has been committed back to the Impacket repository, and is available in the Impacket [examples directory](https://github.com/CoreSecurity/impacket/blob/master/examples/ntlmrelayx.py).

## More resources

Much of our research in trying to understand NTLM was with the help from the following resources:

- http://davenport.sourceforge.net/ntlm.html (clear explanation, some parts are outdated, based on reverse engineering the protocol before it was open sourced by Microsoft)
- http://ubiqx.org/cifs/SMB.html#SMB.8.5 (similar to the one above)
- https://msdn.microsoft.com/en-us/library/cc236621.aspx (Official protocol documentation, very technical)
- https://technet.microsoft.com/en-us/library/2006.08.securitywatch.aspx (LMCompatibilityLevel explained)