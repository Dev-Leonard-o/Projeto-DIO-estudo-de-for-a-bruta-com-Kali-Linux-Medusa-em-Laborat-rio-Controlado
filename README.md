# Projeto DIO estudo de força bruta com kali-linux + enumeração + medusa + enum4linux (Laboratório Controlado)

<br>
<br>
<h1> Introdução </h1>
  <p> 
  Em um cenário de cibersegurança, a compreensão das táticas ofensivas é fundamental para a construção de defesas robustas. Este relatório detalha um <strong>projeto</strong> de estudo focado na simulação de um ataque de <strong>força bruta</strong>, uma técnica que     explora a repetição sistemática para quebrar credenciais de acesso. A iniciativa foi realizada em um <strong>laboratório controlado e ético</strong>, garantindo um ambiente seguro para a experimentação, o projeto faz parte do Bootcamp da Digital Innovation One (DIO).

  A simulação empregou o sistema operacional **Kali Linux**, uma distribuição especializada em testes de penetração, e a ferramenta **Medusa** para automatizar e executar os ataques de força bruta. Para enriquecer a fase de reconhecimento, o projeto também utilizou técnicas de **enumeração** e as ferramentas: <strong>Nmap</strong>, **enum4linux**, que são cruciais para coletar informações valiosas sobre os sistemas e usuários-alvo antes do ataque propriamente dito. Assim como a criação de wordlists para obter um melhor desempenho das ferramentas. Este exercício prático teve como objetivo principal demonstrar a eficácia dessas ferramentas e a importância crítica de políticas de senha fortes e monitoramento contínuo da rede. </p>

<br>

<h2> Índice </h2>

  <ol>
    <li>1. Objetivos</li>
    <li> Aviso de Ética e Uso </li>
    <li> Ambiente (infra)</li>
    <li> Cenários Testados </li>
            <ul>
               <li> FTP (Força bruta com Medusa)</li>
               <li> Enumeração em Web Forms (DVWA)</li>
               <li> SMB (password spraying + enumeração)</li>
            </ul>
    </li>
    <li> Wordlists utlizadas</li>
    <li> Comandos (exemplos) </li>
    <li> Experimento SSH: Invasão a máquina via conexão SSH
             <ul>
               <li> "Unable to negotiate", Causa e solução</li>
               <li> Conexão não autorizada a máquina via SSH</li>
               <li> Criação de um arquivo .txt "Hello World" remoto</li>
             </ul>
    </li>
    <li> Evidências e validação </li>
    <li> Recomendações de mitigação</li>
    <li> Referências e materiais de estudo</li>
    <li> Encerramento e agradecimento.</li>
  </ol>

<br>

<h3>1. Objetivos</h3>
  <ul>
    <li> Demonstrar ataques de força bruta em </li>
    <li> Praticar uso das ferramentas <strong>nmap</strong>, <strong>Medusa</strong> <strong>Enum4linux</strong></li>
    <li> Gerar Documentação técnica clara e objetiva, com comandos, wordlists, e sugestões de mitigação</li>
    <li> Registrar uma atividade extra como experimento adicional realizado via SSH(Invasão, navegação e criação de arquivo hello_world.txt, assim como registrar um problema comum observado (Unable to negotiate).</li>
    <li> Aprender a validar resultados e gerar evidências para um relatório técnico profissional</li>
  </ul>

  <br>

  
<h3>2. Aviso de Ética e Uso</h3>
  <ul>
    <li> Todos os testes foram realizados em <strong>ambiente isolado</strong> (VMs locais) e autorizados (Ambiente com fins educacionais) </li>
    <li> É expressamente <strong>PROIBIDO</strong> executar testes fora de ambientes controlados e autorizados</li>
    <li>A finalidade deste projeto tem cunho educativo, tal qual é: entender vulnerabilidades e propor contramedidas.</li>
  </ul>

  <br>

 <h3>3. Ambiente (Infra)</h3>
  <ul>
    <li>Virtual box com duas VM'S em <strong> host-only / internal network</strong>, sendo: 
        <ul>
          <li><strong>Kali Linux</strong> (atacante) - Ferramentas instaladas (Medusa, enum4linux, nmap) /li>
          <li><strong>Metasploitable 2</strong>(alvo vulnerável) - Serviços: FTP (vsftpd), SMB (Samba), HTTP (DVWA instalado/servindo).</li>
        </ul>
    </li>
     <li> Endereços IPs de exemplo (rede host-only):
          <ul>
            <li> Kali: <strong>192.168.56.102</strong></li>
            <li> Metasploitable: <strong>192.168.56.101</strong></li>
          </ul>
     </li>
  </ul>
 
 <br>
 
 <h3>4. Cenários testados</h3>
     <h4> &nbsp &nbsp &nbsp &nbsp Cenário A → FTP (Força bruta)</h4>
         <ul>
           <li>Objetivo: testar força bruta de credenciais FTP (vsftpd).</li>
           <li>Ferramenta principal: <strong>Medusa</strong> (-M ftp).</li>
         </ul>
      <h4> &nbsp &nbsp &nbsp &nbsp Cenário B → Formulário Web (DVWA)</h4>
         <ul>
           <li>Objetivo: Tentativas de login automatizadas na tela de formulário de login da DVWA.</li>
           <li>Ferramenta principal: <strong>Medusa</strong> (-M + wordlists users + passwords).</li>
         </ul>
      <h4> &nbsp &nbsp &nbsp &nbsp Cenário C → SMB (Password Spraying / Enumeração)</h4>
         <ul>
           <li>Objetivo: Enumeração múltia de usuários (via enum4linux, smbclient) e aplicação de passowrd-spraying.</li>
           <li>Ferramenta principal: enum4linux, smbclient, Medusa (-M smbnt / -M smb).</li>
         </ul>
         
  <br>
  
  <h3>5. Wordlists utilizadas</h3>
      <p>Arquivos em /wordlists/ (exemplos):</p>
         <ul>
           <li>user.txt - admin, user, test</li>
           <li>pass.txt - 123456, password, admin123, Welcome123</li>
         </ul>

  <br>
    
<h3>6. Comandos utilizados no terminal </h3>
<h3>6.1 Medusa (força bruta) </h3>

      medusa -h 192.168.56.101 -U users.txt -P pass.txt -M ftp -t 6

<h3>6.2Web Form (DVWA) - automação </h3>

      $ medusa -h 192.168.56.101 -U users.txt -P pass.txt -M http \
      -m PAGE: '/dvwa/login.php' \
      -m Form: 'username="USER"&PASSWORD="PASS"&login=Login' \
      -m 'FAIL=Login failed' -t 6
<br>

<h3>6.3 SMB → Enumeração + password spraying</h3>

      //enumeração + organização de wordlists.
      $ less enum4linux_output.txt

<h3>7. Invasão a máquina via SSH: problema comum, solução do problema, conexão, criação de arquivos e evidência</h3>
  <p> Inspirado no projeto que a instrutora Isadora Ferrão aplicou, tomei a inciativa de ir um pouco mais a fundo: realizar uma invasão à maquina vulnerável e deixar um documento de saudação de "Hello World" </p>
  
  <h4>Objetivo:</h4>
 &nbsp &nbsp &nbsp &nbsp <p> Estabelecer conexão SSH a partir do Kali linux para a máquina vulnerável metasploitable 2, navegar dentre as pastas e deixar um arquivo de registro "Hello_World.txt", com o autor metasploitable e data como evidência de invasão.</p>

 <h5>"Unable to negotiate" a causa comum e sua solução </h5>
 &nbsp &nbsp &nbsp <p> Ao inicializar, me deparo com o seguinte erro: 
   "Unable to negotiate with 192.168.56.102 port 22: no matching key exchange method found. Their offer: diffie-hellman-group1-sha1". Que de momento, precisei realizar algumas pesquisas para conseguir encontrar a solução.</p>

   <ul>
     <h4>Causa</h4>
      <li>Essa causa é bem comum em vm's, significa tanto o servidor, quanto clientes possuem protocolos ssl, tls, ssh
       incompatíveis, justamente pelo metasploitable oferecer serviços antigos ou obsoletos que ocorre esse erro, não há interseção entre as listas de algoritmos</li>
     <h4>Solução</h4>
     <li>A solução encontrada, abre uma sessão ssh para o host <strong>192.168.56.101</strong> usando o usuário msfadmin (coletado através da etapa de enumeração e armazenada em uma wordlist), forçando o cliente SSH a aceitar/usar o algoritmo ssh-rsa tanto para a chave do host quanto para tipos de chave pública do cliente. Abaixo o código utilizado:</li>

                $ -o HostkeyAlgorithms=+ssh-rsa -o PubKeyAcceptedKeyTypes=+ssh-rsa msfadmin@192.168.56.101

  (Figuras e imagens armazenadas na pasta "Imagens")
      <h4>Conexão</h4>
        <li>Após a correção do erro, agora inicializa-se a etapa da conexão, uma vez capturadas as informações graças ao <strong>Nmap, Medusa e enum4linux</strong>, consigo executar a conexão via ssh com a máquina alvo, uma vez que as combinações foram testadas e arnazenadas na etapa de enumeração. </li>
         <h4>Navegação, evidência.</h4>
         <li>Conexão bem sucedida, hora de dar um alô, navego até a pasta "vulnerabilities" com os comandos ensinados no BOOTCAMP "ls", "cd.." e "cd" e finalizo com o comando: $ echo "HelloWorld - Criado por $(whoami) em $(date)" > hello_world.txt</li>
         <li>Em seguida, confirmo o arquivo tanto no kali linux, quanto na máquina metasploitable, utilizando os comandos no kali: "ls" e "cat" </li>
   </ul>

   
   
