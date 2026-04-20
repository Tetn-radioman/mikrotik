# mikrotik

### чистка

\/ip dhcp-server lease remove [find dynamic]

\/ip firewall connection remove [find]

\/ip arp remove [find mac-address=XX:XX:XX:XX:XX:XX]

```markdown
## Мониторинг и статус (смотреть, что происходит)

```
### Железо
/system resource print     # Загрузка CPU, свободная память, аптайм
/system health print       # Температура и вольтаж (если есть сенсоры)

### Интерфейсы
/interface monitor-traffic ether1  # Смотреть трафик на порту в реальном времени (Ctrl+C для выхода)
/interface print stats             # Счетчики пакетов и ошибок на портах

### Кто подключен
/ip arp print             # Таблица MAC-адресов и IP в локалке
/tool torch ether1        # Показывает, КТО и КУДА качает прямо сейчас (очень наглядно)

### Логи
/log print follow         # Прямой эфир событий (полезно при диагностике Wi-Fi или VPN)

### Wi-Fi (если есть)
/interface wireless registration-table print  # Список подключенных клиентов и уровень сигнала
```
```
