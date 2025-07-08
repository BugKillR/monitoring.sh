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

#SCRIPT

echo    "#Architecture : $(uname -a)"
echo    "#CPU physical : $(lscpu | grep "Socket(s):" | awk '{print$2}')"
echo    "#vCPU : $(nproc)"
echo    "#Memory Usage : $USEDMEM/$TOTALMEM MB ($MEMPERCENTAGE%)"
echo    "#Disk Usage : $STORAGEUSEDTEXT/$STORAGESIZEGB$BARKGB ($STORAGEPERC%)"
