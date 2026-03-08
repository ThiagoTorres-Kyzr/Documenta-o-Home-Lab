# Documentação de um projeto de home lab focado em simulação de ataques e detecção, usando o Wazuh como SIEM.

# Arquitetura utilizada:
3 Máquinas Virtuais através do VirtualBox;
1-Kali Linux (Ataque)
2-Windows 10 (Vítima) -> Com programas de monitoramento entre outros. (Ex: Sysmon, Suricata, Wazuh Agent)
3-Ubuntu Server -> Com o Wazuh.
4-Host -> Utilizei de uma conexão SSH com o servidor Ubuntu para acessar o Wazuh-Dashboard através de um navegador. 

# Write-up para o Home Lab:
Foram simulados ataques a um computador com Windows 10, utilizando de uma VM com Kali Linux para utilizar de scripts com utilidades populares (como por exemplo: Nmap, Metasploit). O intuito foi testar o uso de um SIEM (Wazuh foi utilizado nessa instância, sendo colocado em um Servidor Ubuntu, com seu agente no Windows 10), e analisar logs para detecção de sinais de tentativas de ataque em uma máquina. 

Iniciei com o uso de reconhecimento, MITRE ATT&CK ID: T1595 (Active Scanning), o que não foi detectado inicialmente pela máquina com suas configurações de segurança padrão. Realizando mudanças nas regras de firewall e fechar portas desnecessárias ajudaram a reduzir os resultados dos escaneamentos, mas não é algo que tem como se prevenir totalmente quando se tem uma rede em contato com a internet externa. Mesmo com regras mais rigorosas e software extra (Suricata foi utilizado neste caso), tentativas de reconhecimento não foram identificadas e nenhum alerta foi criado pelo Wazuh. 

Em seguida utilizei de scripts do Metasploit para tentar fazer o uso de vulnerabilidades conhecidas, como por exemplo o EternalBlue CVE-2017-0144, MITRE ATT&CK ID: T1210. Uma técnica que tenta utilizar do protocolo SMB para execução remota de código em máquinas Windows. Em um sistema desatualizado, isso foi letal, inicialmente foi detectado pelo SIEM mas pouco depois controle da máquina foi perdido. Felizmente, aplicar a atualização de segurança criada para essa vulnerabilidade ajuda a prevenir a execução com sucesso desse ataque. Devido ao fato de não ser desatualizado o Windows não foi explorado e nenhum comportamento incomum foi detectado, novamente, sem alertas relevantes pelo Wazuh. 

Após realizar uma limpeza restaurando a máquina a uma baseline com snapshots antes dos ataques serem realizados, utilizei a ferramenta do Atomic Red Team para realizar testes que são diretamente relacionados com o framework MITRE ATT&CK. Poucos alertas de nível alto foram demonstrados pelo Wazuh. Os principais alertas foram relacionados as regras ID:60154, que se tratam de mudanças feitas no grupo de administradores, evasão de defesas e escalada de privilégios. Alguns ataques podem ser mapeados e foram detectados como por exemplo: MITRE ATT&CK  ID:T1136.001 (criação de conta local).

Surpreendentemente, utilizando um sistema com mais programas de monitoramento e segurança não demonstrou tanta diferença. Exceto casos onde o administrador da máquina propositalmente introduz regras ou funcionalidades não seguras ao ambiente há baixíssima chance de um ataque ser realizado com sucesso. 

O que aponta para o perigo que são ameaças internas. 
Simulando o comportamento de uma ameaça interna demonstra incrível facilidade de extrair dados, danificar, e causar caos na rede e máquinas conectadas. Realizando ataques como comprometimento de defesas, um teste que se destaca foi o MITRE ATT&CK ID: T1562.001 cujo propósito é comprometer defesas: desativar ou modificar ferramentas. Esse teste específicamente causou bastante dano à máquina, pois conseguiu remover totalmente Sysmon que me ajudou com a produção de logs mais precisos, conseguiu desabilitar o Suricata embora não tenha influenciado numa situação desse gênero, e desativou o windows defender, deixando a máquina totalmente vulnerável a ataques subsequentes. Nenhum alerta foi gerado pois o ataque conseguiu realizar o que necessitava rápido o suficiente e tomou controle da máquina.

Podem ser realizados controles que monitoram por comportamento irregular, como tentativas de interferir com antivírus ou outros sistemas de defesa e detecção, o que pode não garantir uma defesa perfeita mas ajuda a desacelerar o progresso do ataque o que por sua vez pode ocasionar em mais fácil detecção por parte dos programas de monitoramento, providenciando tempo o suficiente para uma resposta por parte do time resposta a incidentes. 

Ataques desse tipo podem ser evitados utilizando de diversas táticas diferentes, como prevenção de execução de ferramentas e scripts não confiados, controles de acesso mais rigorosos, e pela aplicação de arquiteturas como Zero Trust, que permite acesso somente ao que é absolutamente necessário para alguém realizar seu trabalho. Dessa forma, será reduzida significativamente as chances de uma ameaça interna completar um ataque, e caso ocorra será muito mais direto encontrar a origem.

Este experimento pelo fato de ser executado pelo administrador da máquina com o intuito de teste, foi um sucesso aos olhos de uma ameaça interna, mas não demonstra um cenário totalmente realista onde existem diversas camadas de segmentação e controles de acesso, porém ainda sendo algo que é possível acontecer quando não se aplica a defesa necessária.
