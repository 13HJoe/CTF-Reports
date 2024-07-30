# Hack Park
- Scanning and Enumeration
  logseq.order-list-type:: number
	- `nmap` scan results
	  logseq.order-list-type:: number
		- Fast Scan
			- ![Screenshot 2024-07-30 142229.png](../assets/Screenshot_2024-07-30_142229_1722337819926_0.png)
		- In-Depth Scan [run parallelly]
			- ![Screenshot 2024-07-30 143001.png](../assets/Screenshot_2024-07-30_143001_1722337809815_0.png)
	- Brute-Force **admin** console login using `hydra`
	  logseq.order-list-type:: number
		- `burpsuite` - intercepted login request
			- ![image.png](../assets/image_1722339053676_0.png)
		- ![Screenshot 2024-07-30 144127.png](../assets/Screenshot_2024-07-30_144127_1722337897814_0.png)
- Initial Access
  logseq.order-list-type:: number
	- Server -> BlogEngine.NET,  version -> `3.3.6`
	  logseq.order-list-type:: number
		- Vulnerability - `CVE-2019-6714` https://nvd.nist.gov/vuln/detail/CVE-2019-6714
			- **description** - A path traversal and Local File Inclusion vulnerability in PostList.ascx.cs can cause unauthenticated users to load a PostView.ascx component from a potentially untrusted location on the local filesystem. <u>This is especially dangerous if an authenticated user uploads a PostView.ascx file using the file manager utility, which is currently allowed. This results in remote code execution for an authenticated user.</u>
		- Exploit - https://www.exploit-db.com/exploits/46353
		- On uploading the payload `ascx` script and navigating to `<ip>/?theme=../../App_Data/files` we get a reverse shell
			- ![image.png](../assets/image_1722339993976_0.png)
			- ![image.png](../assets/image_1722340057844_0.png)
			- ![Screenshot 2024-07-30 172046.png](../assets/Screenshot_2024-07-30_172046_1722349308569_0.png)
	- To switch to a more stable shell [`meterpreter`]
	  logseq.order-list-type:: number
		- Navigate to a <u>universally writeable directory</u> 
		  logseq.order-list-type:: number
			- ![Screenshot 2024-07-30 172442.png](../assets/Screenshot_2024-07-30_172442_1722349413583_0.png)
		- Generate payload
		  logseq.order-list-type:: number
			- ![Screenshot 2024-07-30 172452.png](../assets/Screenshot_2024-07-30_172452_1722349466135_0.png)
		- Use `Invoke-WebRequest` to obtain `shell.exe`
		  logseq.order-list-type:: number
			- ![Screenshot 2024-07-30 172921.png](../assets/Screenshot_2024-07-30_172921_1722349512924_0.png)
			- ![Screenshot 2024-07-30 172931.png](../assets/Screenshot_2024-07-30_172931_1722349521904_0.png)
		- Configure a listener on `msfconsole` and execute the shell
		  logseq.order-list-type:: number
			- ![Screenshot 2024-07-30 173422.png](../assets/Screenshot_2024-07-30_173422_1722349585156_0.png)
			- ![Screenshot 2024-07-30 173415.png](../assets/Screenshot_2024-07-30_173415_1722349591332_0.png)
			- ![Screenshot 2024-07-30 174118.png](../assets/Screenshot_2024-07-30_174118_1722351197979_0.png)
- Privilege Escalation
  logseq.order-list-type:: number
	- Obtain `winPEASx64` from the attack machine to enumerate **privesc** vectors
	  logseq.order-list-type:: number
		- ![Screenshot 2024-07-30 174053.png](../assets/Screenshot_2024-07-30_174053_1722351158470_0.png)
		- ![Screenshot 2024-07-30 174058.png](../assets/Screenshot_2024-07-30_174058_1722351165452_0.png){:height 114, :width 608}
	- Execute `winPEASx64`
	  logseq.order-list-type:: number
		- Users
		  logseq.order-list-type:: number
			- ![Screenshot 2024-07-30 175314.png](../assets/Screenshot_2024-07-30_175314_1722351258679_0.png)
		- Env. variables
		  logseq.order-list-type:: number
			- ![Screenshot 2024-07-30 175350.png](../assets/Screenshot_2024-07-30_175350_1722351277561_0.png)
			- ![Screenshot 2024-07-30 175358.png](../assets/Screenshot_2024-07-30_175358_1722351288089_0.png)
		- Active TCP
		  logseq.order-list-type:: number
			- ![Screenshot 2024-07-30 175454.png](../assets/Screenshot_2024-07-30_175454_1722351320461_0.png)
		- Services
		  logseq.order-list-type:: number
			- ![Screenshot 2024-07-30 175234.png](../assets/Screenshot_2024-07-30_175234_1722351340085_0.png)
			- **possible vector** - `WindowsScheduler` is running and the folder in the execution path has Write/Create permissions
	- Navigate to the `SystemScheduler` directory and view the logs
	  logseq.order-list-type:: number
		- ![Screenshot 2024-07-30 175840.png](../assets/Screenshot_2024-07-30_175840_1722351527105_0.png)
		- `Message.exe` is being executed by the scheduler with **root** permissions
	- Change the name of `Message.exe` and replace it with `shell.exe` [our `meterpreter` payload from earlier]
	  logseq.order-list-type:: number
		- ![Screenshot 2024-07-30 180250.png](../assets/Screenshot_2024-07-30_180250_1722351662102_0.png)
		- ![Screenshot 2024-07-30 180659.png](../assets/Screenshot_2024-07-30_180659_1722351670920_0.png)
	- Configure another `metasploit` listener to receive the connection
	  logseq.order-list-type:: number
		- ![Screenshot 2024-07-30 180645.png](../assets/Screenshot_2024-07-30_180645_1722351759556_0.png)
- Flag Proof
  logseq.order-list-type:: number
	- User - ![Screenshot 2024-07-30 181038.png](../assets/Screenshot_2024-07-30_181038_1722351797950_0.png)
	- Root - ![Screenshot 2024-07-30 181045.png](../assets/Screenshot_2024-07-30_181045_1722351811800_0.png)
