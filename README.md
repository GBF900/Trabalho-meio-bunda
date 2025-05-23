# TRABALHOS PRÁTICOS

**Primeiro Trabalho Prático:**

a)
```
def ipdefine(d):
 ip = socket.gethostbyname(d)
 if ip == None  :
    print("O domínio não existe ou é inválido") 
 else:
    print("O IP do domínio %s é %s" %(d,ip))     
    main()

def main():
 dominio= input ("DIGITE UM DOMÍNIO (FORMATO WWW. ****.COM): ")   
 ipdefine(dominio)
```

b)
```
import os

def ping(h):
 if h == None:
     print("Servidor não existe ou é inválido")
     main()
 else:   
  os.system("ping -n 4 %s"%h)  

def main():
 host = input("DIGITE UM NÚMERO DE HOST: ") 
 ping(host)
 main()
```
c)

```
import socket
import struct
import time
import os

def checksum(source_string):
    sum = 0
    max_cont = (len(source_string) // 2) * 2
    cont = 0

    while cont < max_cont:
        val = source_string[cont + 1] * 256 + source_string[cont]
        sum = sum + val
        sum = sum & 0xffffffff
        cont = cont + 2

    if max_cont < len(source_string):
        sum = sum + source_string[-1]
        sum = sum & 0xffffffff

    sum = (sum >> 16) + (sum & 0xffff)
    sum = sum + (sum >> 16)
    result = ~sum
    result = result & 0xffff
    result = result >> 8 | (result << 8 & 0xff00)
    return result

def send_icmp_echo(h):

    try:
        sock = socket.socket(socket.AF_INET, socket.SOCK_RAW, socket.IPPROTO_ICMP)
    except PermissionError:
        print("Permissão negada: execute como administrador/root.")
        return

    pid = os.getpid() & 0xFFFF
    icmp_type = 8  # Echo Request
    code = 0
    checksum_placeholder = 0
    identifier = pid
    sequence = 1

    header = struct.pack("!BBHHH", icmp_type, code, checksum_placeholder, identifier, sequence)
    data = b'ping_test'
    checksum_val = checksum(header + data)

    header = struct.pack("!BBHHH", icmp_type, code, checksum_val, identifier, sequence)
    packet = header + data

    start_time = time.time()
    sock.sendto(packet, (h, 10))
    sock.settimeout(1)

    try:
        response, addr = sock.recvfrom(1024)
        elapsed = time.time() - start_time
        print("Resposta de %s em : %.2f ms" %(addr[0],elapsed*1000))
    except socket.timeout:
        print("Tempo esgotado para o destino.")
    finally:
        sock.close()

def main():
 host= input("Digite um endereço ip ou domínio: ")
 send_icmp_echo(host)

main()
```

**Segunda Atividade Prática**

a)
```
import ipaddress

def ipverif(ip, red):
 ip = ipaddress.IPv4Address(ip)
 rede = ipaddress.IPv4Network(red, strict=False)

 if ip in rede:
    print("O IP %s pertence à rede %s"%(ip, rede))
 else:
    print("O IP %s NÃO pertence à rede %s"%(ip, rede))

def main():
    ip = input("Digite um ip: ")
    rede = input( "Digite uma rede (Formato X.X.X.0/YY): ")
    ipverif(ip, rede)
main()
```

b)

```
import ipaddress

def ip_list(ip):
    
 entrada = ip
 interface = ipaddress.IPv4Interface(entrada)
 rede = interface.network

 print("IP de rede:     %s"%rede.network_address)
 print("Broadcast:      %s"%rede.broadcast_address)
 print("Máscara:        %s"%rede.netmask)
 print("Hosts válidos:")
 for ip in rede.hosts():
    print(ip)

def main():
    ip = input ("DIGITE UM IP: ")
    ip_list(ip)

main()
```

c)

```
import ipaddress

def verificar_rede(entrada):
    try:
        rede = ipaddress.IPv4Network(entrada, strict=False)
        
        print("\nAnálise da rede: %s" %entrada)
        print("IP de rede:     %s" %rede.network_address)
        print("Broadcast:      %s "%rede.broadcast_address)
        print("Máscara:         %s " %rede.netmask)
        print("Hosts válidos:")
        for host in rede.hosts():
            print("  %s" %host)
    except ValueError:
        print("❌ Entrada inválida. Use formato IP/Máscara, ex: 192.168.0.10/24")

def main():
 entrada = input("Digite o IP com máscara (ex: 192.168.1.10/24): ")
 verificar_rede(entrada)
main()
```

**Terceira Atividade Prática**

a)

```
import socket
import threading

def is_host_up(ip):
    try:
        socket.create_connection((ip, 80), timeout=0.5)
        print(f'{ip} está ativo')
    except:
        return -1

threads = []

for i in range(1, 255):
    ip = f'192.168.1.{i}'
    t = threading.Thread(target=is_host_up, args=(ip,))
    threads.append(t)
    t.start()


for t in threads:
    t.join()

def main():
    ip= input( "DIGITE UM IP COM A MASCARA DE REDE: ")
    is_host_up(ip)
main()
```


**Quarta Atividade Prática** 

```
import socket
import ipaddress
import threading
import datetime

def verificar_ip(ip):
    resultado = socket_testar_conexao(str(ip), 80)
    if resultado:
        timestamp = datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S')
        print(f"[{timestamp}] {ip} está ativo")


def socket_testar_conexao(ip, porta):
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.settimeout(0.5)
    retorno = s.connect_ex((ip, porta))  # 0 = sucesso
    s.close()
    return retorno == 0

def main():
    entrada = input("Digite a rede no formato CIDR (ex: 192.168.0.0/28): ")


    rede = ipaddress.IPv4Network(entrada, strict=False)

    print(f"\nVerificando IPs ativos na rede {rede}...\n")

    threads = []

    for ip in rede.hosts():
     t = threading.Thread(target=verificar_ip, args=(ip,))
     threads.append(t)
     t.start()

    for t in threads:
     t.join()

```

