# CVE-2022-0996

a user 
whose password was expired was still allowed to access the database as 
if the password was not expired.  Once a password is expired, and "grace 
logins" have been used up, the account is basically supposed to be 
locked out and should not be allowed to perform any privileged action.

In my testing I have only one ACI in the database that allows access to 
any user who is "authenticated".  After the error 49 (expired password) 
that connection should be considered "anonymous", but it looks like it's 
still being seen as "authenticated" in regards to access control (ACI).


[02/Mar/2022:15:05:22.218125170 -0500] conn=471 op=1 BIND 
dn="cn=mark,dc=example,dc=com" method=128 version=3
[02/Mar/2022:15:05:22.223049484 -0500] conn=471 op=1 *RESULT err=49* 
tag=97 nentries=0 wtime=0.000068907 optime=0.004932884 etime=0.005000319
[02/Mar/2022:15:05:22.223758357 -0500] conn=471 op=2 MOD 
dn="cn=mark,dc=example,dc=com"
[02/Mar/2022:15:05:22.390369884 -0500] conn=471 op=2 *RESULT err=0* 
tag=103 nentries=0 wtime=0.000074679 optime=0.166612685 etime=0.166681508
[02/Mar/2022:15:05:22.391383520 -0500] conn=471 op=3 SRCH 
base="cn=mark,dc=example,dc=com" scope=2 filter="(objectClass=*)" 
attrs="* + aci"
[02/Mar/2022:15:05:22.625998010 -0500] conn=471 op=3 RESULT err=0 
tag=101 *nentries=1* wtime=0.000164145 optime=0.234615089 
etime=0.234772592 notes=U details="Partially Unindexed Filter"
[02/Mar/2022:15:05:22.626797151 -0500] conn=471 op=4 EXT 
oid="1.3.6.1.4.1.4203.1.11.1" name="passwd_modify_plugin"
[02/Mar/2022:15:05:22.784152543 -0500] conn=471 op=4*RESULT err=0* 
tag=120 nentries=0 wtime=0.000090637 optime=0.157359229 etime=0.157444618
[02/Mar/2022:15:05:22.790619385 -0500] conn=471 op=5 UNBIND
[02/Mar/2022:15:05:22.793371023 -0500] conn=471 op=5 fd=64 Disconnect - 
Cleanly Closed Connection - U1

