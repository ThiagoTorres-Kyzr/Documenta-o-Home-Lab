Documentação de um projeto de home lab focado em simulação de ataques e detecção, usando o Wazuh como SIEM.
Arquitetura utilizada:
3 Máquinas Virtuais através do VirtualBox;
1-Kali Linux (Ataque)
2-Windows 10 (Vítima) -> Com programas de monitoramento entre outros. (Ex: Sysmon, Suricata, Wazuh Agent)
3-Ubuntu Server -> Com o Wazuh.
4-Host -> Utilizei de uma conexão SSH com o servidor Ubuntu para acessar o Wazuh-Dashboard através de um navegador. 
