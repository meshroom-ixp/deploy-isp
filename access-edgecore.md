Configuration edgecore 3528M:

1. Configure ports:
  interface ethernet 1/1-24
    description "subscriber connection port"          # Описание клиентского порта
    switchport native vlan ${vlanid}                  # Номер влана клиента
    switchport mode access                            # Тип порта
    no spanning-tree loopback-detection               # Не отправлять клиенту пакеты stp loopback-detection
    no spanning-tree port-bpdu-flooding               # Не отправлять клиенту пакеты bpdu
    spanning-tree spanning-disabled                   # Отключить STP
    spanning-tree bpdu-filter                         # Фильровать bpdu от клиента
    no lldp notification                              # Не отправлять клиенту информационные пакеты LLDP  
    lldp admin-status rx-only                         # Принимать пакеты LLDP от клиента.
    loopback-detection                                # Отправлять клиенту пакеты определения петель loopback-datection.
    snmp-server enable port-traps mac-notification    # Отправлять SNMP трап при изучении нового mac на порту.

  interface ethernet 1/25
    description "uplink connection port"
    ip dhcp snooping trust                             # Деактивировать фильтри dhcp, mac на порту.
    switchport mode trunk
    switchport acceptable-frame-types tagged           # Фильтровать входящие пакеты без тегов.
    switchport allowed vlan add ${vlanids} tagged

  interface ethernet 1/26-28
    description "downlink connection port"
    switchport mode trunk
    switchport acceptable-frame-types tagged
    switchport allowed vlan add ${vlanids} tagged

  
2. Configure global options:

Конфигурация сервера времени:
  sntp server 10.50.0.5
  sntp client

Включить определение петель на клиентском порту:
  loopback-detection mode port-based 
  loopback-detection

Добавлять в пакеты dhcp информацию о порте подключения:
  ip dhcp snooping vlan ${vlanid}
  ip dhcp snooping information option
  ip dhcp snooping

Включить логирование:
  logging host 10.50.0.5
  logging on

Отключить встроеный веб сервер:
  no ip domain-lookup
  no ip http server
  no ip http secure-server
