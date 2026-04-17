# Vlan

### 1. Создаем бридж и сразу включаем фильтрацию VLAN
/interface bridge
add name=bridge1 vlan-filtering=yes frame-types=admit-only-vlan-tagged

### 2. Добавляем порты в бридж
/interface bridge port
add bridge=bridge1 interface=ether1 frame-types=admit-only-vlan-tagged
add bridge=bridge1 interface=ether2 pvid=10 frame-types=admit-only-untagged-and-priority-tagged

### 3. Настраиваем VLAN на бридже (тегированный на ether1, нетегированный на ether2)
/interface bridge vlan
add bridge=bridge1 tagged=ether1 untagged=ether2 vlan-ids=10

### 4. Создаем VLAN-интерфейс для Layer-3 маршрутизации
/interface vlan
add interface=bridge1 name=vlan10 vlan-id=10

### 5. Назначаем IP-адрес на VLAN-интерфейс
/ip address
add address=192.168.10.1/24 interface=vlan10

### 6. (Опционально) Настройка DHCP-сервера для этой сети
/ip pool
add name=dhcp_pool10 ranges=192.168.10.2-192.168.10.254
/ip dhcp-server
add address-pool=dhcp_pool10 interface=vlan10 name=dhcp10
/ip dhcp-server network
add address=192.168.10.0/24 gateway=192.168.10.1 dns-server=8.8.8.8
