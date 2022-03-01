# MDT books

## SCCM notes

SCCM Lab Architecture:
---
Domain Controller(Windows Server)
SCCM Primary Site Server(Windows Server)
SQL Server database(Windows Server)
Client Computer(Windows 10)


MECM Lab Setup In Azure:
------
create a Resource group,virtual network and 4 VMS in portal.azure.com

west us --> california, is the cheaper

https://tinyurl.com/rdcmapp

on Domain machine:
install AD and DS service role/feature

promote this server to a domain controller link on manage link on server manger.
Add new forest, domain name: lab.com
Ignore Create DNS delegation screen
type in without .com in Net bios name screen

On servers - DB server,MECM server and Domain machine:
uncheck ipv6 adapter
ie enhanced security
cmd> sconfig , change update setting to manual

--
Point all the VMs to internal DNS server in azure.
VNets--open vnet linekd to these machines.--click DNS servers.--select custom DNS server(local IP address of the Domain server we created)

login to the machines after restart using..
on DOmain machine:
lab\username

join all the individual machines from workgroup to lab.com domain.

users:
servermanager on domain machine, active directory users and computers.
create a folder/OU(MECMLAB) and create users inside it.
user: sccmadmin,sqladmin,user1

select all and add to group (domain admins)


sccm prereqs:
-----
on sql server machine:
----
dotnet 3.5(from server manager) on DB server
sqlserver and management studio on db server(sql server 2016 evaluation version:database engine,reporting services features)
sccm server(object types- computers) needs to be the local admin of DB server
configure sql memory to reserve memory for database transaction

domain computer--tools--group policy management--forests--lab.com--right click..create a GPO
SCCM Frewall Policy(as name)--right click and edit.--polcies--windows settings--security settings--windows firewal--inbound rule--predefined--file and printer sharing, Windows Management Instrumentation.
Outbound rule--predefined--file and printer sharing
inbound rule--port--1433(SQL PORT 1433),4022(SQL PORT 4022)

gpupdate /force --> it will fetch group policies from AD and we can check whether these applied.

Instead of above command, restart also works.

on sccm machine:
----
web server IIS
Windows server update services(instead of WD,select sql server connectivity with DB server name we created and select folder as c:\WSUS_content)
.net 3.5(include all sub features)
.net 4.7(include all sub features)
BITS
Remote differential compression
Windows AUthentication
asp .net 3.5
iis 6 wmi compatibility
iis management scripts and tools

install wadk and winpe mecmps server:
wadk: https://tinyurl.com/WADK1903  -- select only Deployment tools,USMT
WINPE: https://tinyurl.com/Winpe1903

set WSUS service to automatic mode.

server manager--exclamation icon--post confiuration continue.

Launch tools--iis manager.--application pools--cehck WSUS pool exist
Sites--wsus administration(website to administer wsus)
on database server--sql management studio--check SUSDB database is created

on AD machine:
run schema extender: https://tinyurl.com/extadsch
create System Management Container in AD
MECMPS server needs full control on that container to write data inside it.

after running schema
server manager--tools--adsi edit--right click and connect to.--right click on CN=System and create object -container-System Management
This will create entry in AD users and computers-(view - ADvanced featuers)--system--System Management.(right click -deletegate control-object type computer-MECMPS(sccm machine)--create a custom task to delegate--full control)

create a backup/snapshot of disks in azure so that we can restore back if required.
check in snapshots section once they are created.

Restart SCCM server and SQL database server machines.

https://tinyurl.com/mecm2002

sms provider--communication bridge between MECM and DB
MP--management point--MECM and client machine bridge
DP -distribution point--content storage
SCP-service connection point--to update SCCM version later time.

during installation, select "configure the communication method on each site system role"
--
.OVerview of MECM console
.DIsplay resources from AD into MECM
.Create boundary in MECM
.create boundary group and assign MECM site on Boundary





