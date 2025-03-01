<h1 align="center">TRABALHO FINAL SSORC</h1>
<h2 align="center">CONFIGURAÇÃO DE DOIS COMPARTILHAMENTOS NO SAMBA</h2>

<p align="center">
<b>Repositório Utilizado Somente Para a Aplicação da Atividade Do Projeto Final da Cadeira de Segurança em Sistemas Operacionais e Redes de Computadores!</b>
</p>

<h2> 📌 Objetivo Geral</h2>
O objetivo deste projeto é configurar dois compartilhamentos no samba, um público e um privado entre sistemas Windows e Linux. Para isso, será utilizada a distribuição Debian, com configuração de usuários, restrições de acesso e segurança reforçada.

<h2> 📌 Objetivos Específicos</h2>

* Configurar dois compartilhamentos no Samba: um público e um privado em um ambiente Linux (Debian) virtual.
* Configurar o cenário de testes com pelo menos 2 máquinas (virtuais/físicas).
* Definir e implementar as restrições de segurança necessárias para a autenticação de usuários.
* Propor uma atividade para que seja desenvolvida pela turma SSORC, considerando as configurações necessárias do servidor Samba.

<h2> ✔️ Ferramentas Necessárias</h2>

Para a realização dessa atividade, será necessário algumas ferramentas:

- VirtualBox: VirtualBox 7.1.6 
- Debian: Debian 12.09 
- Windows 11 (máquina nativa): Sistema operacional principal
- Samba: Versão 4.17.12

<h2>  ✔️ Configuração de Rede e Atribuição de IPs </h2>
Para garantir que os sistemas Debian e Linux estejam na mesma sub-rede e possam se comunicar corretamente via Samba, utilizamos uma máquina virtual no VirtualBox. A configuração de rede foi ajustada para o modo Bridge, permitindo que a máquina virtual obtivesse um endereço IP diretamente do roteador da rede principal, como se fosse um dispositivo físico conectado.

<h4> 📍Ativação do modo Bridge na máquina virtual: </h4>

* No VirtualBox, acessamos as configurações da máquina virtual.
* Na aba Rede, selecionamos o Adaptador 1 e alteramos o Modo de Conexão de NAT para Bridge (Ponte).
* Escolhemos uma interface de rede correta (Wi-Fi ou Ethernet) para garantir que a máquina virtual possa se conectar corretamente.
* Salvamos as configurações e iniciamos a máquina virtual.
  
<h4> 📍Verificação dos Endereços IP: </h4>

* Para verificarmos o endereço IP designado pelo roteador utilizamos o comando: 

```bash
  ip a
```

<h2> ✔️ Instalação do Ambiente </h2>

<h4> 📍Criando um Usuário Administrador no Debian (Modo Gráfico) </h4>

1. Acesse Configurações (menu de engrenagem).
2. Navegue até a opção Usuários.
3. Clique em Desbloquear e insira a senha de administrador.
4. Adicione um novo usuário e defina como Administrador.

<h4> 📍Instalação do Samba </h4>

Atualizar os pacotes: 
```bash
sudo apt update && sudo apt upgrade
```
Instalar o samba:
```bash
sudo apt install samba
```
Verificar a instalação:
```bash
smbd --version 
```

<h4> 📍Configuração do Samba</h4>

* Configurando Compartilhamento Público com Restrições de arquivos
  
Abra o arquivo de configuração:
```bash
sudo nano /etc/samba/smb.conf
```
No final do arquivo, adicione estas configurações:
```bash
[publico]
path = srv/samba/publico
browseable = yes
writable = yes
guest ok = yes
create mask = 0777
directory mask = 0777
force user = nobody
veto files = /*.exe/*.bat/*.mp4/*.mp3
delete veto files = yes
```
Agora, crie o diretório no servidor:
```bash
sudo mkdir -p /srv/samba/publico
```
Configure as permissões 
```bash
chmod 777  /srv/samba/publico
```
Após configurar tudo, reinicie o serviço: 
```bash
sudo systemctl restart smbd
```
Verifique se o samba está rodando corretamente:
```bash
sudo systemctl status samba
```
 <h5>Se aparecer **active (running)**, significa que está rodando!✅</h5>

* Configurando Compartilhamento Privado sem Restrições de arquivos
  
Abra o arquivo de configuração: 
```bash
sudo nano /etc/samba/smb.conf
```
No final do arquivo, adicione estas configurações:
```bash
[privado]
path = srv/samba/privado
browseable = yes
writable = yes
guest ok = no
valid users = usuario_samba
create mask = 0700
directory mask = 0700
```
Agora, crie o diretório no servidor:
```bash
sudo mkdir -p /srv/samba/privado
```
Crie um usuário no sistema: 
```bash
sudo adduser usuario_samba
```
<h5>(Defina uma senha quando for solicitado)</h5>

Adicione o usuário ao Samba:
```bash
sudo smbpasswd -a usuario_samba
```
<h5>(Defina a mesma senha do usuário do sistema)</h5>

Configure as permissões 
```bash
chmod 700 /srv/samba/privado
sudo chown usuario_samba:usuario_samba /srv/samba/privado
```
Após configurar tudo, reinicie o serviço: 
```bash
sudo systemctl restart smbd
```
Verifique se o samba está rodando corretamente:
```bash
sudo systemctl status samba
```
<h5>Se aparecer **active (running)**, significa que está rodando!✅</h5>

<h2> ✔️ Testando no Windows </h2>

1. Para acessar o compartilhamento publico

* No Windows, abra o gerenciador de arquivos 
* Digite na barra de pesquisa `\\IP do debian\publico` e pressione Enter.
* Para ver o ip da máquina debian digite o comando:
```bash
ip a 
```
2. Para acessar o compartilhamento privado
   
* No Windows, abra o gerenciador de arquivos 
* Digite na barra de pesquisa `\\IP do debian\privado` e pressione Enter
* Agora o Windows pedirá usuário e senha
* Usuario: usuario_samba
* Senha: (a que você definiu no smbpasswd)
* Para ver o ip da máquina debian digite o comando:
```bash
ip a 
```
<h2> ✔️ Testando no Linux - Ubuntu ou Debian </h2>

1. Para acessar o compartilhamento publico
   
* No Ubuntu ou Debian, abra o gerenciador de arquivos e procure por `+ outros locais` 
* Digite na barra de pesquisa `smb:\\IP do debian\publico` e pressione conectar
* Aperte acessar `anonimamente` e pronto
* Para ver o ip da máquina debian digite o comando: 
```bash
ip a 
```

2. Para acessar o compartilhamento privado

* No Ubuntu ou Debian, abra o gerenciador de arquivos e procure por `+ outros locais`,  
* Digite na barra de pesquisa `smb:\\IP do debian\privado` e pressione conectar
* Aperte acessar com `senha` e pronto
* Agora o Ubuntu ou Debian pedirá usuário e senha
* Usuario: usuario_samba
* Senha: (a que você definiu no smbpasswd)
* Para ver o ip da máquina debian digite o comando: 
```bash
ip a 
```

<h2> ✔️ Medidas de Segurança: Tipos de Documentos Compartilhados</h2>

<h4>Para garantir um compartilhamento seguro e eficiente, serão permitidos os seguintes tipos de documentos: </h4>

* Documentos de texto: .txt, .docx, .pdf 
* Arquivos executáveis (.exe, .bat) e multimídia (.mp4, .mp3) serão bloqueados por padrão para evitar riscos de segurança. 

<h2> ✔️ Estrutura das Máquinas e Funções</h2>

<h4>Debian (Máquina Virtual)</h4>

* Instalação e configuração do Servidor Samba
* Configuração de compartilhamento público e privado
* Definição de permissões de usuários
* Implementação de segurança - restrições de arquivos

<h4>Windows 11 (Máquina Nativa)</h4>

* Configuração como cliente Samba
* Teste de acesso ao compartilhamento público
* Teste de autenticação e acesso ao compartilhamento privado

<h2> ✔️ Conclusão </h2>

Com a configuração realizada, os objetivos do projeto foram alcançados com sucesso. Foram criados dois compartilhamentos no Samba: um público, acessível sem autenticação, porém com restrições de arquivos, e um privado, acessível apenas por um usuário autenticado. 
A implementação foi feita em um ambiente Linux Debian, garantindo compatibilidade com sistemas Windows e Linux.
Além disso, foi estruturado um cenário de testes com pelo menos duas máquinas, permitindo a validação da comunicação entre os sistemas. As restrições de segurança foram aplicadas de forma a garantir controle de acesso e proteção dos dados compartilhados.
Por fim, o projeto possibilitou a proposição de uma atividade prática para a turma, proporcionando um aprendizado efetivo sobre a configuração e administração do Samba em ambientes de rede. 

<h2 id="colab">🤝 Colaboradores</h2>

Colaboradores do Projeto

<table>
  <tr>
    <td align="center">
      <a href="https://github.com/helen-brandao">
        <img src="https://avatars.githubusercontent.com/u/174743042?v=4" width="100px;" alt="Helen Brandão"/><br>
        <sub>
          <b>Helen Brandão</b>
        </sub>
      </a>
    </td>
    <td align="center">
      <a href="https://github.com/wi2lgit/git-will">
        <img src="https://avatars.githubusercontent.com/u/148277586?v=4" width="100px;" alt="Willian Mota"/><br>
        <sub>
          <b>Willian Mota</b>
        </sub>
      </a>
    </td>
  </tr>
</table>
