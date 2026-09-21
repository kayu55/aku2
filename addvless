#!/bin/bash

NC='\033[0;37m' 
MYIP=$(curl -sS ipv4.icanhazip.com)
clear
source /var/lib/scrz-prem/ipvps.conf
if [[ "$IP" = "" ]]; then
domain=$(cat /etc/xray/domain)
else
domain=$IP
fi
#tls="$(cat ~/log-install.txt | grep -w "Vless TLS" | cut -d: -f2|sed 's/ //g')"
#none="$(cat ~/log-install.txt | grep -w "Vless None TLS" | cut -d: -f2|sed 's/ //g')"
until [[ $user =~ ^[a-zA-Z0-9_]+$ && ${CLIENT_EXISTS} == '0' ]]; do
echo -e "\033[0;34m┌─────────────────────────────────────────────────┐\033[0m"
echo -e "\033[0;34m│\E[42;1;37m            Create Xray/Vless Account            \033[0;34m│"
echo -e "\033[0;34m└─────────────────────────────────────────────────┘\033[0m"

		read -rp "User: " -e user
		CLIENT_EXISTS=$(grep -w $user /etc/xray/config.json | wc -l)

		if [[ ${CLIENT_EXISTS} == '1' ]]; then
clear
		echo -e "\033[0;34m┌─────────────────────────────────────────────────┐\033[0m"
		echo -e "\033[0;34m│\E[42;1;37m            Create Xray/Vless Account            \033[0;34m│"
		echo -e "\033[0;34m└─────────────────────────────────────────────────┘\033[0m"
			echo ""
			echo "A client with the specified name was already created, please choose another name."
			echo ""
			read -n 1 -s -r -p "Press any key to back on menu"
			v2ray-menu
		fi
	done

uuid=$(cat /proc/sys/kernel/random/uuid)
read -p "Expired (days): " masaaktif
hariini=`date -d "0 days" +"%Y-%m-%d"`
exp=`date -d "$masaaktif days" +"%Y-%m-%d"`
sed -i '/#vless$/a\### '"$user $exp"'\
},{"id": "'""$uuid""'","email": "'""$user""'"' /etc/xray/config.json
sed -i '/#vlessgrpc$/a\### '"$user $exp"'\
},{"id": "'""$uuid""'","email": "'""$user""'"' /etc/xray/config.json
vlesslink1="vless://${uuid}@bug.com:443?path=/vless&security=tls&host=${domain}&encryption=none&type=ws&sni=${domain}#${user}"
vlesslink2="vless://${uuid}@${domain}:80?path=/vless&encryption=none&type=ws&host=${domain}#${user}"
vlesslink3="vless://${uuid}@${domain}:443?mode=gun&security=tls&encryption=none&type=grpc&serviceName=vless-grpc&sni=bug.com#${user}"

clear
mkdir -p /detail/vless/
#Simpan Detail Akun User
cat > /detail/vless/$user.txt <<-END
-----------------------------------------
Xray/Vless Account
-----------------------------------------
Remarks     : ${user}
Domain      : ${domain}
User Ip     : 2 IP
Port Non TLS: 80, 8080, 8880, 2082, 2086, 2052, 2095
Port TLS    : 443, 8443, 2087, 2096, 2053, 2083
User ID     : ${uuid}
Encryption  : none
Path TLS    : /vless
ServiceName : vless-grpc
-----------------------------------------
Link TLS    : ${vlesslink1}
-----------------------------------------
Link NTLS   : ${vlesslink2}
-----------------------------------------
Link GRPC   : ${vlesslink3}
-----------------------------------------
Aktif Selama     : $masaaktif Hari
Dibuat Pada      : $hariini
Berakhir Pada    : $exp
-----------------------------------------

END

cat >/var/www/html/vless-$user.txt <<-END

-----------------------------------------
Format Open Clash
-----------------------------------------
- name: Vless-$user-WS TLS
  server: ${domain}
  port: 443
  type: vless
  uuid: ${uuid}
  cipher: auto
  tls: true
  skip-cert-verify: true
  servername: ${domain}
  network: ws
  ws-opts:
    path: /vless
    headers:
      Host: ${domain}

- name: Vless-$user-WS (CDN) Non TLS
  server: ${domain}
  port: 80
  type: vless
  uuid: ${uuid}
  cipher: auto
  tls: false
  skip-cert-verify: false
  servername: ${domain}
  network: ws
  ws-opts:
    path: /vless
    headers:
      Host: ${domain}
  udp: true

- name: Vless-$user-gRPC (SNI)
  server: ${domain}
  port: 443
  type: vless
  uuid: ${uuid}
  cipher: auto
  tls: true
  skip-cert-verify: true
  servername: ${domain}
  network: grpc
  grpc-opts:
  grpc-mode: gun
    grpc-service-name: vless-grpc

-----------------------------------------
Link Akun Vless 
-----------------------------------------
Link TLS      : 
${vlesslink1}
-----------------------------------------
Link none TLS : 
${vlesslink2}
-----------------------------------------
Link GRPC     : 
${vlesslink3}
-----------------------------------------
Expired          : $expe
-----------------------------------------
END

function notif_vl() {
    CHATID="-1001911868043"
KEY="7876561487:AAHCpsZnu1LGx6w95fhqFMNSCT7fEGdv2vo"
    export TIME="10"
    export URL="https://api.telegram.org/bot$KEY/sendMessage"
    sensor=$(echo "$user" | sed 's/\(.\{3\}\).*/\1xxx/')
    ISP=$(curl -s ipinfo.io/org | cut -d " " -f 2-10 )
    TEXT="
<b>-----------------------------------------</b>
<b>TRANSACTION SUCCESSFUL</b>
<b>-----------------------------------------</b>
<b>» Produk : Vless</b>
<b>» ISP :</b> <code>${ISP}</code>
<b>» Limit Quota :</b> <code>${Quota} GB</code>
<b>» Limit Login :</b> <code>${iplimit} Hp</code>
<b>» Username :</b> <code>$sensor</code>
<b>» Duration :</b> <code>${masaaktif} Days</code>
<b>-----------------------------------------</b>
<i>Automatic Notification From Server</i>
<b>-----------------------------------------</b>
"
    curl -s --max-time $TIME -d "chat_id=$CHATID&disable_web_page_preview=1&text=$TEXT&parse_mode=html" $URL >/dev/null
}

systemctl restart xray
clear
echo -e "\033[0;34m═══════════\033[0;33mXRAY/VLESS\033[0;34m═══════════${NC}"
echo -e "\033[0;34m════════════════════════════════${NC}"
echo -e "Remarks       : ${user}" 
echo -e "Created       : $hariini"
echo -e "Expired On    : $exp 👍" 
echo -e "Domain        : ${domain}" 
echo -e "port none TLS : 80, 8080, 8880, 2082, 2086, 2052, 2095"
echo -e "port TLS      : 443, 8443, 2087, 2096, 2053, 2083"
echo -e "port GRPC     : 443 "
echo -e "id            : ${uuid}"
echo -e "Encryption    : none" 
echo -e "Network       : ws" 
echo -e "Path          : /vless" 
echo -e "Path          : vless-grpc"  
echo -e "\033[0;34m════════════════════════════════${NC}"
echo -e "Link TLS :"
echo -e "${vlesslink1}" 
echo -e "\033[0;34m════════════════════════════════${NC}"   
echo -e "Link none TLS : "
echo -e "${vlesslink2}" 
echo -e "\033[0;34m════════════════════════════════${NC}"
echo -e "Link GRPC : "
echo -e "${vlesslink3}" 
echo -e "\033[0;34m════════════════════════════════${NC}" 
echo -e ""
echo -e "${GREEN} Sc By Arya Blitar ${NC}" 
echo -e ""
read -n 1 -s -r -p "Press any key to back on menu"

menu-vless
