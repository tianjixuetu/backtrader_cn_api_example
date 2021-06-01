免费提供基于backtrader的中国期货ctp行情以及交易接口。

https://blog.csdn.net/BackTraderCN/article/details/117473909
https://github.com/backtradercn/backtrader_cn_api_example

本安装包默认包含window和Linux版的ctp行情以及交易功能。

# backtrader-cn 接口，已经接入了高频系统(参考课程 https://edu.csdn.net/course/detail/24668) 的md/td模块，可以直接使用ctp，飞马，xtp，及一些非法币交易所。默认安装包仅提供ctp接入，其他安装包请联系课程主管。
# 行情以及交易接口已经改造为power-save模式，通过增加延迟减少cpu耗费。如需原始高频模式，请联系课程主管。
# 内置接口已经可以直接读取收集中国期货ctp的tick数据和分钟线（参考期货tick自动下载和分钟线处理环境 https://blog.csdn.net/BackTraderCN/article/details/116597823）


免责声明：详见《RISK_DISCLOSURE_AGREEMENT.txt》









#安装
#win


python -m easy_install backtradercn-1.0-py3.7-win-amd64.egg
cd c:\anaconda3\lib\site-packages\backtradercn-1.0-py3.7-win-amd64.egg
c:
copy pystrategy.pyd  backtradercn\

#linux 

mkdir /home/bt_docker_share_folder
sudo docker pull backtradercn/backtradercn-py3-runtime:1.0
sudo docker run --name backtradercn-py3 -e TZ="Asia/Shanghai" -v /etc/localtime:/etc/localtime  -v /home/bt_docker_share_folder:/home/bt_docker_share_folder --privileged --ulimit memlock=-1 --net=host -td backtradercn/backtradercn-py3-runtime:1.0 /bin/bash

copy "backtradercn-1.0-py3.7-linux-x86_64.egg" to the share folder: /home/bt_docker_share_folder
sudo docker exec -it backtradercn-py3 bash
cd /home/bt_docker_share_folder
python -m easy_install backtradercn-1.0-py3.7-linux-x86_64.egg 
cd /usr/local/python3/lib/python3.7/site-packages/backtradercn-1.0-py3.7-linux-x86_64.egg
cp *.so  /usr/lib64/
cp pystrategy.so backtradercn/






### 配置定时启动关闭md,td
copy files 
start_ctp.md.sh
start_ctp.td.sh
start_register_center.sh

################
# cat /home/bt_docker_share_folder/start_daemon.sh 
#!/bin/bash

./stop_daemon.sh

cd /home/bt_docker_share_folder/

echo `date "+%Y-%m-%d %H:%M:%S"`  start_register_center...
./start_register_center.sh
echo start_register_center: `date "+%Y-%m-%d %H:%M:%S"` >> ./start.log

sleep 2s
echo `date "+%Y-%m-%d %H:%M:%S"`  start_ctp.md.sh..
./start_ctp.md.sh
echo start_ctp.md.sh: `date "+%Y-%m-%d %H:%M:%S"` >> ./start.log


sleep 8s
echo `date "+%Y-%m-%d %H:%M:%S"`  start_ctp.td.sh..
./start_ctp.td.sh
echo start_ctp.td.sh: `date "+%Y-%m-%d %H:%M:%S"` >> ./start.log




# cat /home/bt_docker_share_folder/stop_daemon.sh
#!/bin/bash
#yum install psmisc
killall RegistryCenterServer
echo stop register_center: `date "+%Y-%m-%d %H:%M:%S"` >> ./start.log
killall ctp_md_daemon
echo stop ctp_md_daemon: `date "+%Y-%m-%d %H:%M:%S"` >> ./start.log
killall ctp_td_daemon
echo stop ctp_td_daemon: `date "+%Y-%m-%d %H:%M:%S"` >> ./start.log



#定时 crontab
#crontab -e

55 8 * * 1-5 cd /home/bt_docker_share_folder/;/home/bt_docker_share_folder/start_daemon.sh
37 11 * * 1-5 cd /home/bt_docker_share_folder/;/home/bt_docker_share_folder/stop_daemon.sh
#week 1-5, 12:55 PM to 15:15PM
55 12 * * 1-5 cd /home/bt_docker_share_folder/;/home/bt_docker_share_folder/start_daemon.sh
20 15 * * 1-5 cd /home/bt_docker_share_folder/;/home/bt_docker_share_folder/stop_daemon.sh
#week 1-6, 20:50 PM to 03:10AM
55 20 * * 1-5 cd /home/bt_docker_share_folder/;/home/bt_docker_share_folder/start_daemon.sh
20 03 * * 1-6 cd /home/bt_docker_share_folder/;/home/bt_docker_share_folder/stop_daemon.sh

54 16 * * 1-5 cd /home/bt_docker_share_folder/;/home/bt_docker_share_folder/stop_daemon.sh



1 04 * * 1-6  /home/bt_docker_share_folder/zip_and_mv.sh &





