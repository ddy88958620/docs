#### Virtual Box安装

		yum install VirtualBox-4.2
#### 启动图形界面

		Virtual Box		

#### VBoxManage命令

		VBoxManage --help		
+ 命令

		VBoxManager list vms
		VBoxManager startvm <name> --type headless
		VBoxManager controlvm <name> poweroff		


#### Vncserver安装

		yum install tigervnc tigervnc-server -y 
		yum install fontforge -y 
		yum groupinstall Desktop -y
#### 启动Vncserver
	
		vncserver -name vnc_1		
