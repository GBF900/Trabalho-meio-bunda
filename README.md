#TRABALHOS PRÁTICOS 

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
