

在第一台机器上：
拷admin.s
& docker cp admin.sh neutron_server:/
进入容器
docker exec -it -u 0 neutron_server bash
 source admin.sh
 neutron port-create --name octavia-health-manager-standalone-listen-port --security-group lb-mgmt-sec-grp --device-owner Octavia:health-mgr --binding:host_id=test8 lb-mgmt-net
 exit
进入容器
 docker exec -it -u 0 openvswitch_db bash
填写上面加粗部分获取的Mac和ID：
  ovs-vsctl  --may-exist add-port br-int o-hm0 -- set Interface o-hm0 type=internal -- set Interface o-hm0 external-ids:iface-status=active -- set Interface o-hm0 external-ids:attached-mac=fa:16:3e:5a:c1:73 -- set Interface o-hm0 external-ids:iface-id=8f4fda73-df40-4f7f-b1bf-6d4a31c6d37e
 ip a
 ovs-vsctl show

  exit
服务器上（加粗部分的Mac地址）
 ip link set dev o-hm0 address fa:16:3e:5a:c1:73
 ip a
 dhclient -v o-hm0 
 ip a


修改配置信息：
 vim /etc/kolla/octavia-api/octavia.conf

   cp /etc/kolla/octavia-api/config.json /etc/kolla/octavia-worker/
   cp /etc/kolla/octavia-api/config.json /etc/kolla/octavia-health-manager/
   cp /etc/kolla/octavia-api/config.json /etc/kolla/octavia-housekeeping/
 docker restart octavia_api octavia_worker octavia_health_manager octavia_housekeeping
 docker ps -a | grep octavia
 netstat -ntpl | grep 9876

