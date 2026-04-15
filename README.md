# mikrotik
## 2 версия
### вот моя итоговая схема :

#### IPv4

/ip firewall filter 

add chain=forward action=fasttrack-connection connection-state=established,related comment="!!! FASTTRACK TURBO !!!" place-before=0 

special dummy rule to show fasttrack counters(forward passthrough)

add chain=input action=accept connection-state=established,related comment="input: allow established,related"
add chain=input action=drop connection-state=invalid comment="input: drop invalid"
add chain=input action=accept in-interface-list=LAN comment="input: allow from LAN"
add chain=input action=accept protocol=icmp in-interface-list=WAN comment="input: allow ICMP from WAN" 
add chain=input action=drop in-interface-list=WAN comment="input: drop all other from WAN"

### --- FORWARD (Межсетевой экран) ---
### Шаг 1: Пропуск уже установленных соединений (ускоряет работу)
add chain=forward action=accept connection-state=established,related comment="forward: allow established,related"
add chain=forward action=drop connection-state=invalid comment="forward: drop invalid"

### Шаг 2: БЕЗОПАСНОСТЬ VLAN (ПЕРЕНЕСЕНО ВЫШЕ, ДО РАЗРЕШЕНИЯ ИНТЕРНЕТА)
### Разрешить Админу ходить в любые VLAN (включая Servers и Home)
add chain=forward action=accept src-address=192.168.20.0/24 comment="allow Admin to all VLANs"

### Запретить Home и Servers ходить друг к другу
add chain=forward action=drop src-address=192.168.40.0/24 dst-address=192.168.30.0/24 comment="isolate Home from Servers"
add chain=forward action=drop src-address=192.168.30.0/24 dst-address=192.168.40.0/24 comment="isolate Servers from Home"

### Шаг 3: Разрешить ВСЕМ (кто не попал под запрет выше) выходить в Интернет (WAN)
add chain=forward action=accept in-interface-list=LAN out-interface-list=WAN comment="forward: allow LAN to WAN (internet)"

### Шаг 4: Защита от вторжений с WAN (новые подключения извне)
add chain=forward action=drop in-interface-list=WAN connection-state=new comment="forward: drop new from WAN"

### Шаг 5: Финальный запрет для всего, что не подошло (страховка)
add chain=forward action=drop comment="forward: drop the rest"



####IPv6

/ipv6 firewall filter
# --- INPUT (Защита роутера) ---
add chain=input action=accept connection-state=established,related comment="ipv6 input: allow established,related"
add chain=input action=drop connection-state=invalid comment="ipv6 input: drop invalid"
add chain=input action=accept in-interface-list=LAN comment="ipv6 input: allow from LAN"
add chain=input action=accept protocol=icmpv6 in-interface-list=WAN comment="ipv6 input: allow ICMPv6 from WAN (необходим для работы IPv6)"
add chain=input action=drop in-interface-list=WAN comment="ipv6 input: drop all other from WAN"

# --- FORWARD ---
add chain=forward action=accept connection-state=established,related comment="ipv6 forward: allow established,related"
add chain=forward action=drop connection-state=invalid comment="ipv6 forward: drop invalid"

# Аналогично ваши VLAN правила (если используете IPv6 в VLAN)
add chain=forward action=accept src-address=2001:db8:20::/64 comment="allow Admin to all VLANs (IPv6)"
# ... и так далее

add chain=forward action=accept in-interface-list=LAN out-interface-list=WAN comment="ipv6 forward: allow LAN to WAN"
add chain=forward action=drop in-interface-list=WAN connection-state=new comment="ipv6 forward: drop new from WAN"
add chain=forward action=drop comment="ipv6 forward: drop the rest"
