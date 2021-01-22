#!/bin/bash
#厂长个人对接脚本 谢绝传播 
# Version：2.2 Bete 
# Updata time：2020-11-9 17:39:37
#check root
[ $(id -u) != "0" ] && { echo "错误: 您必须以root用户运行此脚本"; exit 1; }
unlink $0   #修复删除运行脚本错误
#常规变量设置
#fonts color
Green="\033[32m" 
Red="\033[31m" 
Yellow="\033[33m"
GreenBG="\033[42;37m"
RedBG="\033[41;37m"
Font="\033[0m"

#notification information
Info="${Green}[Info]${Font}"
OK="${Green}[OK]${Font}"
Error="${Red}[Error]${Font}"
Notification="${Yellow}[Notification]${Font}"

#IP and config
#IPAddress=`wget http://members.3322.org/dyndns/getip -O - -q ; echo`;
config="/root/shadowsocks/userapiconfig.py"
Github="https://github.com/tonydan6324/ssrmu.git"
Libsodiumr_file="/usr/local/lib/libsodium.so"
get_ip(){
	ip=$(curl -s https://ipinfo.io/ip)
	[[ -z $ip ]] && ip=$(curl -s https://api.ip.sb/ip)
	[[ -z $ip ]] && ip=$(curl -s https://api.ipify.org)
	[[ -z $ip ]] && ip=$(curl -s https://ip.seeip.org)
	[[ -z $ip ]] && ip=$(curl -s https://ifconfig.co/ip)
	[[ -z $ip ]] && ip=$(curl -s https://api.myip.com | grep -oE "([0-9]{1,3}\.){3}[0-9]{1,3}")
	[[ -z $ip ]] && ip=$(curl -s icanhazip.com)
	[[ -z $ip ]] && ip=$(curl -s myip.ipip.net | grep -oE "([0-9]{1,3}\.){3}[0-9]{1,3}")
	[[ -z $ip ]] && echo -e "\n 这小鸡鸡还是割了吧！\n" && exit
}
check_system(){
	clear
	if [[ -f /etc/redhat-release ]]; then
		release="centos"
	elif cat /etc/issue | grep -q -E -i "debian"; then
		release="debian"
	elif cat /etc/issue | grep -q -E -i "ubuntu"; then
		release="ubuntu"
	elif cat /etc/issue | grep -q -E -i "centos|red hat|redhat"; then
		release="centos"
	elif cat /proc/version | grep -q -E -i "debian"; then
		release="debian"
	elif cat /proc/version | grep -q -E -i "ubuntu"; then
		release="ubuntu"
	elif cat /proc/version | grep -q -E -i "centos|red hat|redhat"; then
		release="centos"
    fi
	bit=`uname -m`
	#res = $(cat /etc/redhat-release | awk '{print $4}')
	#if [[ ${release} == "centos" ]] && [[ ${bit} == "x86_64" ]] && [[ ${res} -ge 7 ]]; then
	if [[ ${release} == "centos" ]] && [[ ${res} -eq 6 ]]; then
	echo -e "你的系统为[${release} ${bit}],检测${Red} 不可以 ${Font}搭建。"
	echo -e "请选择${Yellow} Centos7.x / Debian / Ubuntu ${Font}搭建"
	exit 0;
	else
	echo -e "你的系统为[${release} ${bit}],检测${Green} 可以 ${Font}搭建。"
	fi
}
optimize(){
	clear
	echo "fs.file-max = 51200" > /etc/sysctl.conf
	echo "net.core.rmem_max = 67108864" >> /etc/sysctl.conf
	echo "net.core.wmem_max = 67108864" >> /etc/sysctl.conf
	echo "net.core.netdev_max_backlog = 250000" >> /etc/sysctl.conf
	echo "net.core.somaxconn = 65535" >> /etc/sysctl.conf
	echo "net.ipv4.tcp_syncookies = 1" >> /etc/sysctl.conf
	echo "net.ipv4.tcp_tw_reuse = 1" >> /etc/sysctl.conf
	echo "net.ipv4.tcp_tw_recycle = 0" >> /etc/sysctl.conf
	echo "net.ipv4.tcp_fin_timeout = 30" >> /etc/sysctl.conf
	echo "net.ipv4.tcp_keepalive_time = 1200" >> /etc/sysctl.conf
	echo "net.ipv4.ip_local_port_range = 10000 65000" >> /etc/sysctl.conf
	echo "net.ipv4.tcp_max_syn_backlog = 8192" >> /etc/sysctl.conf
	echo "net.ipv4.tcp_max_tw_buckets = 5000" >> /etc/sysctl.conf
	echo "net.ipv4.tcp_fastopen = 3" >> /etc/sysctl.conf
	echo "net.ipv4.tcp_rmem = 4096 87380 67108864" >> /etc/sysctl.conf
	echo "net.ipv4.tcp_wmem = 4096 65536 67108864" >> /etc/sysctl.conf
	echo "net.ipv4.tcp_mtu_probing = 1" >> /etc/sysctl.conf
	echo "net.ipv4.ip_forward = 1" >> /etc/sysctl.conf
	echo "net.ipv6.conf.all.disable_ipv6 = 1" >> /etc/sysctl.conf
	echo "net.ipv6.conf.default.disable_ipv6 = 1" >> /etc/sysctl.conf
	echo "* soft nofile 65535" > /etc/security/limits.conf
	echo "* hard nofile 65535" >> /etc/security/limits.conf
	echo "* soft nproc 65535" >> /etc/security/limits.conf
	echo "* hard nproc 65535" >> /etc/security/limits.conf
	sysctl -p
}
node_install_start_for_centos(){
	clear
	yum -y groupinstall "Development Tools"
	yum install unzip zip ntpdate git iptables -y
	yum update nss curl iptables -y
	wget --no-check-certificate https://download.libsodium.org/libsodium/releases/libsodium-1.0.18.tar.gz
	tar xf libsodium-1.0.18.tar.gz && cd libsodium-1.0.18
	./configure && make -j2 && make install
	echo /usr/local/lib > /etc/ld.so.conf.d/usr_local_lib.conf
	ldconfig
	clear
	[[ ! -e ${Libsodiumr_file} ]] && echo -e "${Error} libsodium 安装失败 !" && exit 1
	echo && echo -e "${Info} libsodium 安装成功 !" && echo
	cd /root
	yum -y install python-setuptools
	easy_install pip
	git clone ${Github} "/root/shadowsocks"
	cd shadowsocks
	pip install -r requirements.txt
	pip install cymysql
	cp apiconfig.py userapiconfig.py
	cp config.json user-config.json
}
node_install_start_for_debian(){
	clear
	apt-get update -y
	apt-get install git curl ntpdate iptables unzip zip build-essential -y
	wget --no-check-certificate https://download.libsodium.org/libsodium/releases/libsodium-1.0.18.tar.gz
	tar xf libsodium-1.0.18.tar.gz && cd libsodium-1.0.18
	./configure && make -j2 && make install
	ldconfig
	clear
	[[ ! -e ${Libsodiumr_file} ]] && echo -e "${Error} libsodium 安装失败 !" && exit 1
	echo && echo -e "${Info} libsodium 安装成功 !" && echo
	cd /root
	apt-get install python-pip -y
	git clone ${Github} "/root/shadowsocks"
	cd shadowsocks
	pip install -r requirements.txt
	pip install cymysql
	cp apiconfig.py userapiconfig.py
	cp config.json user-config.json
}
api_new(){
    clear
	echo -e "如果以下手动配置错误，请在${config}手动编辑修改"
	read -p "请输入你的对接域名或IP(例如:http://www.baidu.com 默认为本机对接): " WEBAPI_URL
	read -p "请输入muKey(在你的配置文件中 默认marisn):" WEBAPI_TOKEN
	read -p "请输入你的节点编号(回车默认为节点ID 3):  " NODE_ID
	read -p "请输入你的混淆参数[务必与配置文件中一致](回车默认为: microsoft.com):  " MU_SUFFIX
	read -p "是否自动安装定时重启服务(回车默认安装 Y/N):  " MONITOR
	MONITOR=${MONITOR:-"Y"} #默认开启监控
	if [[ ${release} == "centos" ]];then
	node_install_start_for_centos
	else
	node_install_start_for_debian
	fi
	cd /root/shadowsocks
	echo -e "modify Config.py...\n"
	get_ip
	WEBAPI_URL=${WEBAPI_URL:-"http://${ip}"}
	sed -i '/WEBAPI_URL/c \WEBAPI_URL = '\'${WEBAPI_URL}\''' ${config}
	#sed -i "s#https://zhaoj.in#${WEBAPI_URL}#" /root/shadowsocks/userapiconfig.py
	WEBAPI_TOKEN=${WEBAPI_TOKEN:-"marisn"}
	sed -i '/WEBAPI_TOKEN/c \WEBAPI_TOKEN = '\'${WEBAPI_TOKEN}\''' ${config}
	#sed -i "s#glzjin#${WEBAPI_TOKEN}#" /root/shadowsocks/userapiconfig.py
	NODE_ID=${NODE_ID:-"3"}
	sed -i '/NODE_ID/c \NODE_ID = '${NODE_ID}'' ${config}
	MU_SUFFIX=${MU_SUFFIX:-"microsoft.com"}
	sed -i '/MU_SUFFIX/c \MU_SUFFIX = '\'${MU_SUFFIX}\''' ${config}
	
	#监控服务
	if [[ ${MONITOR} == "Y" || ${MONITOR} == "y" ]];then
	crontab -l > crontab_monitor
	echo "30 4 * * * $(which systemctl) restart ssr" >> crontab_monitor
	crontab crontab_monitor
	rm -rf crontab_monitor
	fi
}
api_old(){
	clear
	echo -e "如果以下手动配置错误，请在${config}手动编辑修改"
	read -p "请输入新增节点目录名(如ssrmu,不可为纯数字): " NODE_LIST
	read -p "请输入你的对接域名或IP(例如:http://www.baidu.com 默认为本机对接): " WEBAPI_URL
	read -p "请输入muKey(在你的配置文件中 默认marisn):" WEBAPI_TOKEN
	read -p "请输入你的节点编号(回车默认为节点ID 3):  " NODE_ID
	read -p "请输入你的混淆参数[务必与配置文件中一致](回车默认为: microsoft.com):  " MU_SUFFIX
	read -p "是否自动安装定时重启服务(回车默认安装 Y/N):  " MONITOR
	NODE_LIST=${NODE_LIST:-"ssrmu"} #默认为ssrmu
	MONITOR=${MONITOR:-"Y"} #默认开启监控
	git clone ${Github} "/root/${NODE_LIST}"
	if [ ! -d "/root/${NODE_LIST}" ]; then
		echo -e "${Error} 下载资源失败，请检查是否安装Git"
		exit 1
	fi
	cd "/root/${NODE_LIST}"
	pip install -r requirements.txt
	pip install cymysql
	cp apiconfig.py userapiconfig.py
	cp config.json user-config.json
	cp ssr.service ${NODE_LIST}.service
	echo -e "modify Config.py...\n"
	get_ip
	#修复参数替换错误
	WEBAPI_URL=${WEBAPI_URL:-"http://${ip}"}
	sed -i '/WEBAPI_URL/c \WEBAPI_URL = '\'${WEBAPI_URL}\''' "/root/${NODE_LIST}/userapiconfig.py"
	WEBAPI_TOKEN=${WEBAPI_TOKEN:-"marisn"}
	sed -i '/WEBAPI_TOKEN/c \WEBAPI_TOKEN = '\'${WEBAPI_TOKEN}\''' "/root/${NODE_LIST}/userapiconfig.py"
	NODE_ID=${NODE_ID:-"3"}
	sed -i '/NODE_ID/c \NODE_ID = '${NODE_ID}'' "/root/${NODE_LIST}/userapiconfig.py"
	MU_SUFFIX=${MU_SUFFIX:-"microsoft.com"}
	sed -i '/MU_SUFFIX/c \MU_SUFFIX = '\'${MU_SUFFIX}\''' "/root/${NODE_LIST}/userapiconfig.py"
	#替换守护程序
	sed -i "s/ssr/${NODE_LIST}/" ${NODE_LIST}.service
	sed -i "s/shadowsocks/${NODE_LIST}/" ${NODE_LIST}.service
	
	#监控服务
	if [[ ${MONITOR} == "Y" || ${MONITOR} == "y" ]];then
	crontab -l > crontab_monitor
	echo "30 4 * * * $(which systemctl) restart ${NODE_LIST}" >> crontab_monitor
	crontab crontab_monitor
	rm -rf crontab_monitor
	fi
}
db_new(){
	clear
	echo -e "如果以下手动配置错误，请在${config}手动编辑修改"
	read -p "请输入你的对接数据库IP(例如:127.0.0.1 如果是本机请直接回车): " MYSQL_HOST
	read -p "请输入你的数据库名称(默认sspanel):" MYSQL_DB
	read -p "请输入你的数据库端口(默认3306):" MYSQL_PORT
	read -p "请输入你的数据库用户名(默认root):" MYSQL_USER
	read -p "请输入你的数据库密码(默认root):" MYSQL_PASS
	read -p "请输入你的节点编号(回车默认为节点ID 3):  " NODE_ID
	read -p "请输入你的混淆参数[务必与配置文件中一致](回车默认为: microsoft.com):  " MU_SUFFIX
	read -p "是否自动安装定时重启服务(回车默认安装 Y/N):  " MONITOR
	MONITOR=${MONITOR:-"Y"} #默认开启监控
	if [[ ${release} == "centos" ]];then
	node_install_start_for_centos
	else
	node_install_start_for_debian
	fi
	cd /root/shadowsocks
	echo -e "modify Config.py...\n"
	get_ip
	sed -i '/API_INTERFACE/c \API_INTERFACE = '\'glzjinmod\''' ${config}
	MYSQL_HOST=${MYSQL_HOST:-"${ip}"}
	sed -i '/MYSQL_HOST/c \MYSQL_HOST = '\'${MYSQL_HOST}\''' ${config}
	MYSQL_DB=${MYSQL_DB:-"sspanel"}
	sed -i '/MYSQL_DB/c \MYSQL_DB = '\'${MYSQL_DB}\''' ${config}
	MYSQL_USER=${MYSQL_USER:-"root"}
	sed -i '/MYSQL_USER/c \MYSQL_USER = '\'${MYSQL_USER}\''' ${config}
	MYSQL_PASS=${MYSQL_PASS:-"root"}
	sed -i '/MYSQL_PASS/c \MYSQL_PASS = '\'${MYSQL_PASS}\''' ${config}
	MYSQL_PORT=${MYSQL_PORT:-"3306"}
	sed -i '/MYSQL_PORT/c \MYSQL_PORT = '${MYSQL_PORT}'' ${config}
	NODE_ID=${NODE_ID:-"3"}
	sed -i '/NODE_ID/c \NODE_ID = '${NODE_ID}'' ${config}
	MU_SUFFIX=${MU_SUFFIX:-"microsoft.com"}
	sed -i '/MU_SUFFIX/c \MU_SUFFIX = '\'${MU_SUFFIX}\''' ${config}
	
	#监控服务
	if [[ ${MONITOR} == "Y" || ${MONITOR} == "y" ]];then
	crontab -l > crontab_monitor
	echo "30 4 * * * $(which systemctl) restart ssr" >> crontab_monitor
	crontab crontab_monitor
	rm -rf crontab_monitor
	fi
}
db_old(){
    clear
	echo -e "如果以下手动配置错误，请在${config}手动编辑修改"
	read -p "请输入新增节点目录名(如ssrmu,不可为纯数字): " NODE_LIST
	read -p "请输入你的对接数据库IP(例如:127.0.0.1 如果是本机请直接回车): " MYSQL_HOST
	read -p "请输入你的数据库名称(默认sspanel):" MYSQL_DB
	read -p "请输入你的数据库端口(默认3306):" MYSQL_PORT
	read -p "请输入你的数据库用户名(默认root):" MYSQL_USER
	read -p "请输入你的数据库密码(默认root):" MYSQL_PASS
	read -p "请输入你的节点编号(回车默认为节点ID 3):  " NODE_ID
	read -p "请输入你的混淆参数[务必与配置文件中一致](回车默认为: microsoft.com):  " MU_SUFFIX
	read -p "是否自动安装定时重启服务(回车默认安装 Y/N):  " MONITOR
	NODE_LIST=${NODE_LIST:-"ssrmu"} #默认为ssrmu
	MONITOR=${MONITOR:-"Y"} #默认开启监控
	git clone ${Github} "/root/${NODE_LIST}"
	if [ ! -d "/root/${NODE_LIST}" ]; then
		echo -e "${Error} 下载资源失败，请检查是否安装Git"
		exit 1
	fi
	cd "/root/${NODE_LIST}"
	pip install -r requirements.txt
	pip install cymysql
	cp apiconfig.py userapiconfig.py
	cp config.json user-config.json
	cp ssr.service ${NODE_LIST}.service
	echo -e "modify Config.py...\n"
	get_ip
	sed -i '/API_INTERFACE/c \API_INTERFACE = '\'glzjinmod\''' "/root/${NODE_LIST}/userapiconfig.py"
	MYSQL_HOST=${MYSQL_HOST:-"${ip}"}
	sed -i '/MYSQL_HOST/c \MYSQL_HOST = '\'${MYSQL_HOST}\''' "/root/${NODE_LIST}/userapiconfig.py"
	MYSQL_DB=${MYSQL_DB:-"sspanel"}
	sed -i '/MYSQL_DB/c \MYSQL_DB = '\'${MYSQL_DB}\''' "/root/${NODE_LIST}/userapiconfig.py"
	MYSQL_USER=${MYSQL_USER:-"root"}
	sed -i '/MYSQL_USER/c \MYSQL_USER = '\'${MYSQL_USER}\''' "/root/${NODE_LIST}/userapiconfig.py"
	MYSQL_PASS=${MYSQL_PASS:-"root"}
	sed -i '/MYSQL_PASS/c \MYSQL_PASS = '\'${MYSQL_PASS}\''' "/root/${NODE_LIST}/userapiconfig.py"
	MYSQL_PORT=${MYSQL_PORT:-"3306"}
	sed -i '/MYSQL_PORT/c \MYSQL_PORT = '${MYSQL_PORT}'' "/root/${NODE_LIST}/userapiconfig.py"
	NODE_ID=${NODE_ID:-"3"}
	sed -i '/NODE_ID/c \NODE_ID = '${NODE_ID}'' "/root/${NODE_LIST}/userapiconfig.py"
	MU_SUFFIX=${MU_SUFFIX:-"microsoft.com"}
	sed -i '/MU_SUFFIX/c \MU_SUFFIX = '\'${MU_SUFFIX}\''' "/root/${NODE_LIST}/userapiconfig.py"
	#替换守护程序
	sed -i "s/ssr/${NODE_LIST}/" ${NODE_LIST}.service
	sed -i "s/shadowsocks/${NODE_LIST}/" ${NODE_LIST}.service
	
	#监控服务
	if [[ ${MONITOR} == "Y" || ${MONITOR} == "y" ]];then
	crontab -l > crontab_monitor
	echo "30 4 * * * $(which systemctl) restart ${NODE_LIST}" >> crontab_monitor
	crontab crontab_monitor
	rm -rf crontab_monitor
	fi
}



complete_new()
{
	clear
	if [[ ${release} == "centos" ]];then
	#关闭CentOS7的防火墙
	systemctl stop firewalld.service
	systemctl disable firewalld.service
	#iptables
	iptables -F
	iptables -X  
	iptables -I INPUT -p tcp -m tcp --dport 22:65535 -j ACCEPT
	iptables -I INPUT -p udp -m udp --dport 22:65535 -j ACCEPT
	iptables-save >/etc/sysconfig/iptables
	echo 'iptables-restore /etc/sysconfig/iptables' >> /etc/rc.local
	chmod +x /etc/rc.d/rc.local && chmod +x /etc/rc.local
	fi
	#删除libsodium
	cd /root && rm -rf libsodium*
	cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime -r >/dev/null 2>&1
	timedatectl set-timezone Asia/Shanghai
	timedatectl
	ntpdate -u cn.pool.ntp.org
	clear
	echo -e "${GreenBG} 正在优化系统内核参数中...请稍后... ${Font}"
	optimize
	echo -e "${GreenBG} 将后端写入服务中中...请稍后... ${Font}"
	sleep 2
	if [[ ${release} == "centos" ]];then
	cp /root/shadowsocks/ssr.service /usr/lib/systemd/system/ssr.service
	else
	cp /root/shadowsocks/ssr.service /lib/systemd/system/ssr.service
	fi
	systemctl daemon-reload
	systemctl start ssr
	systemctl enable ssr
	if [[ `ps -ef | grep server.py |grep -v grep | wc -l` -ge 1 ]];then
		echo -e "${OK} ${GreenBG} 后端已启动 ${Font}"
	else
		echo -e "${OK} ${RedBG} 后端未启动 ${Font}"
		echo -e "请检查是否为Centos 7.x系统、检查配置文件是否正确、检查是否代码错误请反馈"
		exit 1
	fi
	stdout() {
		echo -e "\033[32m$1\033[0m"
	}
	stdout "启动命令：systemctl start ssr"
	stdout "停止命令：systemctl stop ssr"
	stdout "重启命令：systemctl restart ssr"
	stdout "开启自启：systemctl enable ssr"
	stdout "关闭自启：systemctl disable ssr"
	stdout "查看状态：systemctl status ssr"
	#监控服务
	if [[ ${MONITOR} == "Y" || ${MONITOR} == "y" ]];then
	stdout "监控任务已安装: crontab -l 查看"
	fi
}

complete_old()
{
	clear
	if [[ ${release} == "centos" ]];then
	cp /root/${NODE_LIST}/${NODE_LIST}.service /usr/lib/systemd/system/${NODE_LIST}.service
	else
	cp /root/${NODE_LIST}/${NODE_LIST}.service /lib/systemd/system/${NODE_LIST}.service
	fi
	systemctl daemon-reload
	systemctl start ${NODE_LIST}
	systemctl enable ${NODE_LIST}
	stdout() {
		echo -e "\033[32m$1\033[0m"
	}
	stdout "启动命令：systemctl start ${NODE_LIST}"
	stdout "停止命令：systemctl stop ${NODE_LIST}"
	stdout "重启命令：systemctl restart ${NODE_LIST}"
	stdout "开启自启：systemctl enable ${NODE_LIST}"
	stdout "关闭自启：systemctl disable ${NODE_LIST}"
	stdout "查看状态：systemctl status ${NODE_LIST}"
	#监控服务
	if [[ ${MONITOR} == "Y" || ${MONITOR} == "y" ]];then
	stdout "监控任务已安装: crontab -l 查看"
	fi
}

uninstall_node()
{
	clear
	#先检测系统
	if [[ -f /etc/redhat-release ]]; then
		release="centos"
	elif cat /etc/issue | grep -q -E -i "debian"; then
		release="debian"
	elif cat /etc/issue | grep -q -E -i "ubuntu"; then
		release="ubuntu"
	elif cat /etc/issue | grep -q -E -i "centos|red hat|redhat"; then
		release="centos"
	elif cat /proc/version | grep -q -E -i "debian"; then
		release="debian"
	elif cat /proc/version | grep -q -E -i "ubuntu"; then
		release="ubuntu"
	elif cat /proc/version | grep -q -E -i "centos|red hat|redhat"; then
		release="centos"
    fi
	read -p "请输入root文件夹下的ss目录名(默认shadowsocks):" CATALOGUE
	CATALOGUE=${CATALOGUE:-"shadowsocks"} #默认为shadowsocks
	if [ ! -d "/root/${CATALOGUE}" ]; then
		echo -e "${Error} 检测root文件夹下不存在此目录,请重试"
		exit 1
	fi
	if [[ ${CATALOGUE} == "shadowsocks" ]];then
		SERVICE="ssr"
	else
		SERVICE="${CATALOGUE}"
	fi
	systemctl stop ${SERVICE}
	systemctl disable ${SERVICE}
	rm -rf /root/${CATALOGUE}
	if [[ ${release} == "centos" ]];then
		rm -rf /usr/lib/systemd/system/${SERVICE}.service
	else
		rm -rf /lib/systemd/system/${SERVICE}.service
	fi
	clear
	#检查是否卸载干净
	if [ -d "/root/${CATALOGUE}" ]; then
		echo -e "${Notification} 检测文件仍然存在，卸载失败"
	else
		echo -e "${OK} 节点已卸载成功"
		echo -e "${Notification} 如果搭建时安装了定时重启任务，请手动执行crontab -e进行删除"
	fi
}

choose_mode()
{
	clear
	echo -e "\033[1;5;31m请选择对接类型：\033[0m"
	echo -e "1.新节点对接"
	echo -e "2.小鸡复用对接(必须使用本脚本已搭建过新节点才行)"
	echo -e "3.节点卸载"
	read -t 30 -p "选择：" MODE_MS
	case $MODE_MS in
			1)
				if [ -d "/root/shadowsocks" ]; then
					echo -e "${Error} 检测已安装节点，请选择小鸡复用对接"
					exit 1
				fi
				select_mode_new
				;;
			2)
				if [ ! -d "/root/shadowsocks" ]; then
					echo -e "${Error} 检测未安装节点，请选择新节点对接"
					exit 1
				fi
				select_mode_old
				;;
			3)
				uninstall_node
				;;
			*)
				echo -e "请选择正确对接类型"
				exit 1
				;;
	esac
}
select_mode_new()
{
	clear
	echo -e "\033[1;5;31m请选择对接模式：\033[0m"
	echo -e "1.API对接模式"
	echo -e "2.数据库对接模式"
	read -t 30 -p "选择：" NODE_MS_NEW
	case $NODE_MS_NEW in
			1)
				api_new
				complete_new
				;;
			2)
				db_new
				complete_new
				;;
			*)
				echo -e "请选择正确对接模式"
				exit 1
				;;
	esac
}

select_mode_old()
{
	clear
	echo -e "\033[1;5;31m请选择对接模式：\033[0m"
	echo -e "1.API对接模式"
	echo -e "2.数据库对接模式"
	read -t 30 -p "选择：" NODE_MS_OLD
	case $NODE_MS_OLD in
			1)
				api_old
				complete_old
				;;
			2)
				db_old
				complete_old
				;;
			*)
				echo -e "请选择正确对接模式"
				exit 1
				;;
	esac
}

main() {
	check_system
	choose_mode
}

main


