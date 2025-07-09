# monitoring.sh

apt install bc

#VARIABLES

USEDMEM=$(free -m | grep "Mem:" | awk '{print$3}')
TOTALMEM=$(free -m | grep "Mem:" | awk '{print$2}')
MEMPERCENTAGE=$(echo "scale=2; $USEDMEM/$TOTALMEM*100" | bc)

#STORAGE

STORAGESIZE=$(df -h | awk '{val=$2; if(val~/G/){gsub(/G/,"",val); sum+=val*1024} else if(val~/M/){gsub(/M/,"",val); sum+=val}} END{print sum}')
STORAGEUSEDTEXT=$(df -h | awk '{val=$3; if(val~/G/){gsub(/G/,"",val); sum+=val*1024} else if(val~/M/){gsub(/M/,"",val); sum+=val}} END{printf "%d", sum}')
STORAGEUSEDCALC=$(df -h | awk '{val=$3; if(val~/G/){gsub(/G/,"",val); sum+=val*1024} else if(val~/M/){gsub(/M/,"",val); sum+=val}} END{print sum}')


STORAGESIZEGB=$(echo "scale=0; $STORAGESIZE/1024" | bc)
BARKGB=$(echo "Gb")
STORAGEPERC=$(echo "scale=0; $STORAGEUSEDCALC*100/$STORAGESIZE" | bc)

#STORAGE

CPUUSEPERC=$(top -bn1 | grep "%Cpu(s)" | awk -F',' '{print$4}' | awk '{print$1}')
CPUUSE=$(echo "100-$CPUUSEPERC" | bc)

LASTBOOT=$(who -b | awk '{print$3}')
LASTBOOTCLOCK=$(who -b | awk '{print$4}')

LVMUSE=$(lsblk -o TYPE | wrep -qw lvm && echo "yes" || echo "no")

TCP=$(echo "$(ss -tan state established | tail -n +2 | wc -l) ESTABLISHED")
USERLOG=$(who | wc -l)
IPV4=$(hostname -I | awk '{print$1}')
MAC=$(ip link show | grep "link/ether" | awk '{print$2}')
SUDO=$(sudo grep "COMMAND" /var/log/sudo/sudo.log | wc -l)

#SCRIPT

echo    "#Architecture : $(uname -a)"
echo    "#CPU physical : $(lscpu | grep "Socket(s):" | awk '{print$2}')"
echo    "#vCPU : $(nproc)"
echo    "#Memory Usage : $USEDMEM/$TOTALMEM MB ($MEMPERCENTAGE%)"
echo    "#Disk Usage : $STORAGEUSEDTEXT/$STORAGESIZEGB$BARKGB ($STORAGEPERC%)"
echo    "#CPU load: $CPUUSE%"
echo    "#Last boot: $LASTBOOT $LASTBOOTCLOCK"
echo    "#LVM use: $LVMUSE"
echo    "#Connections TCP : $TCP"
echo    "#User log: $USERLOG"
echo    "#Network: IP $IPV4 $MAC"
echo    "#Sudo : $SUDO cmd"
