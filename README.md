# Jenkins CI/CD

Este repositório é baseado na documentação: [Build a Node.js and React app with npm](https://jenkins.io/doc/tutorials/build-a-node-js-and-react-app-with-npm/)

![alt tag](https://github.com/FabianoCarneiro/pipelines/blob/master/img-src/jenkins.png)

**Parte 1**

O repositório contém um aplicativo Node.js e React simples que gera
uma página com o conteúdo "Bem-vindo ao React" e com um teste para
verificar se o aplicativo é renderizado conforme esperado crianda nosso primeiro pipeline.

01. [Requisitos](#1)
02. [Início](#2)
03. [Desbloqueio](#3)
04. [Pipeline](#4)
05. [Sessões](#5)
06. [Running](#6)
07. [Test](#7)
08. [Delivery](#8)
09. [ProjetosMultiBranch](#9)
10. [MultiBranchPipeline](#10)
11. [MultiBranchDeploy](#11)

---


**Parte 2**

[Integração com o Repo (GitHub Flow)](https://github.com/FiapFullStack/pipelines/blob/master/flows/GITHUBFLOW.md)

---

## 1. Requisitos <a name="1"></a>

Utilizaremos a arquitetura baseada em containers para instanciar a primeira versão do Jenkins e demonstrar a configuração de um pipeline utilizando o plugin Blue Ocean, este laboratório é baseado na documentação [Build a Node.js and React app with npm ](https://www.jenkins.io/doc/tutorials/build-a-node-js-and-react-app-with-npm/#setup-wizard);

1.1 Instalação do Docker CE: Para execução deste laboratório é necessário a instalação do serviço docker, existem versões para Windows, Linux e Mac, a documentação de apoio está disponível neste endereço: [Install Docker Engine](https://docs.docker.com/engine/install/);

1.2 Instalação do Docker Compose, embora não seja um pré-requisito a infraestrutura de containers construida na documentação foi automatizada para este laboratório utilizando [Overview of Docker Compose](https://docs.docker.com/compose/) este recurso automatiza algumas etapas da configuração com a inicialização dos dcontainers e configuração de rede e volumes, por isso para seguir o passo-a-passo abaixo sua instalação é necessária e [esta detalhada nesse documentação](https://docs.docker.com/compose/install/);

    ## Alternativa:

    Você também pode executar este laboratório utilizando uma instância em núvem ou uma IDE como o Cloud9, opção utilizada neste cenário com base [neste template e instruções de instalação](https://github.com/FiapDevOps/cloud9);

## 2. Início <a name="2"></a>

2.1 Como executarmeos algumas alterações durante o laboratório é necessário que você execute um [Fork deste repositório](https://help.github.com/pt/github/getting-started-with-github/fork-a-repo) para sua conta local no GitHub, utilize um repositório aberto para evitar a configurações de credenciais de acesso no CI.

2.2 Acesse o servidor disponibilizado para o laboratório e caso ainda não esteja disponível execute um clone deste repositório, em seguida inicie o stack do Jenkins a partir da raiz do projeto

```sh
docker-compose up -d
```

[![asciicast](https://asciinema.org/a/335882.svg)](https://asciinema.org/a/335882)

## 3. Desbloqueio <a name="3"></a>

**Liberando o jenkins para uso;**

3.1 Como esta é a primeira vez que o serviço pe inicializado será criada uma credencial provisória para acesso administrativo, essa informação será publicada ao final do processo de inicialização dos containers conforme abaixo.

Obs.: Se estiver utilizando um segundo terminal ou iniciou o container em background será possível recuperar as credenciais utilizando o comando abaixo:

```sh
docker logs jenkins-blueocean
```

3.1.1 A informação que aparece ao final da tela no formato de chave no campo com a descrição "Please use the following password to proceed to installation" será utilizada para desboquear o CI, para isso acesse a URL do servidor na porta 80 e coloque a chave obtida:

![alt tag](https://github.com/FabianoCarneiro/pipelines/blob/master/img-src/01.PNG)

3.2 Depois de [desbloquear o Jenkins](https://www.jenkins.io/doc/tutorials/build-a-node-js-and-react-app-with-npm/#unlocking-jenkins), a página **Customize Jenkins** será exibida

3.3 Nesta página, clique em **"Install suggested plugins."**

> O assistente de instalação mostra a progressão na instalação dos plug-ins sugeridos. Esse processo pode demorar alguns minutos.

3.4 Em seguida o Jenkins solicitará que você crie seu primeiro usuário administrador, especifique os detalhes nos respectivos campos, anote a senha utilizada e clique em Salvar e concluir.

3.5 Quando a página Jenkins estiver pronta aparecer, clique em **"Start using Jenkins"**

---

## 4. Pipeline <a name="4"></a>

**Criando o projeto e a integração**

4.1 Crie seu projeto de pipeline no Jenkins, para isso na página **"Welcome to Jenkins!"** clique em criar **"Create New Job"**

4.2 No campo que surgir digite um nome para o seu novo projeto de Pipeline (por exemplo, simple-node-js-react-npm-app).

4.3 Em seguida logo abaixo escolha a opção **"Pipeline"**, depois clique em OK no final da página.

4.4 Na parte superior da página clique na Guia Pipeline para rolar até a sessão de configuração do nosso Pipeline;

4.5 No campo **"Definition"**, escolha a opção Pipeline script from SCM. 

Esta opção instrui Jenkins a obter seu Pipeline do Source Control Management (SCM), que será seu repositório Git clonado localmente, **essa será nossa primeira ação prática rumo a integração contínua;**

4.6 No campo SCM, escolha Git.

4.7 No campo URL do Repositório, especifique o repositório clonado, por exemplo: https://github.com/FabianoCarneiro/pipelines.git

**Importante:** Tome o cuidado de utilizar o seu repositório para que tenha o acesso necessário para fazer alterações no futuro envolvendo credenciais para execução de webhooks;

4.8 Ao final da tela no campo "Script Path" utilize a path **Jenkinsfile** indicando o Jenkinsfile dentro do diretório pipelines;

4.8 Clique em Salvar para salvar seu novo Pipeline. Agora você está pronto para começar a criar seu [Jenkinsfile](https://www.jenkins.io/doc/book/pipeline/jenkinsfile/);

**Vantagens no uso de um arquivo declarativo como o Jenkinsfile**

* Utilizando este método é possível estabelecer estratégias de Code Review para o próprio Pipeline de Integração Contínua;
* Também é possível auditar as etapas e literalmente "rastrear" o fluxo percorrido pelo produto sendo entregue via Pipeline; 
* Também conseguiremos um "Single source of truth" para o Pipeline, ou seja, ele poderá ser visualizado e editado por vários membros do projeto.


No Jenkins estes pipelines podem ser escritos utilizando [uma linguagem declarativa](https://www.jenkins.io/doc/book/pipeline/syntax/) ou a linguagem de programação [Groovy](http://groovy-lang.org/syntax.html);

---

## Exemplo <a name="Pipeline"></a> 

**Criando o primeiro pipeline**

O conteúdo base necessário para o nosso primeiro pipeline está na raiz do repositório no arquivo JenkinsFile conforme abaixo:

```sh
pipeline {
    agent {
        docker {
            image 'node:lts-bullseye-slim' 
            args '-p 3000:3000' 
        }
    }
    environment {
        CI = 'true' 
    }
    stages {
        stage('Build') { 
            steps {
                sh 'npm install' 
            }
        }
    }
}
```

Este fluxo possui um único **stage** chamado Build, e um único agente chamado **docker** ambos os conceitos explicados abaixo:

---

## 5. Sessões <a name="5"></a>

**A composição bruta de um pipeline em Jenkins**

5.1 Um pipeline declarativo em Jenkins será sempre dividido em sessões, cada sessão possui uma função dentro do fluxo do pipeline.

5.2 A sessão **"agent"** especifica onde o Pipeline, ou um estágio específico dele será executado, essa seção deve ser obrigatoriamente declarada ao menos uma vez no nível superior de dentro do bloco de pipeline, também é possível declarar em niveis inferiores alternando entre ambientes mas este uso é opicional.

> Neste exemplo para a sessão agent utilizamos o tipo Docker o que provisionará um container que será o hospedeiro do ambiente responsável por executar a etapa seguinte, essa tática tem sido muito utilizada por desenvolvedores a fim de isolar dependências dentro do Pipeline em relação ao servidor hospedando a aplicação que será construída.

5.3 A sessão **"stage"** contém o ponto mais importante deste estudo, o core do pipeline, uma sequência de uma ou mais diretivas com as funções que constroem os estágios é onde a maior parte do "trabalho" descrito por um Pipeline será localizado a partir de ações como compilar, testar e implementar, etc.

---

## 6. Running <a name="6"></a> 

**Execute o primeiro pipeline do laboratório**

6.1 Após verificarmos a esturtura por trás do pipeline faremos a primeira execução utilizando o plugin blue ocean, para isso volte para Jenkins novamente, faça login se necessário e clique em **"Open Blue Ocean"** no painél à esquerda para acessar a interface do plugin., nesta interface você provavelmente verá uma mensagem **"This job has not been run"**, clique em **"Run"**:

![alt tag](https://github.com/FabianoCarneiro/pipelines/blob/master/img-src/02.PNG)

6.2 Em seguida, clique rapidamente no link **"OPEN"** que aparece brevemente no canto inferior direito para ver Jenkins construindo seu projeto:

![alt tag](https://github.com/FabianoCarneiro/pipelines/blob/master/img-src/03.PNG)

6.3 Se não foi possível clicar no link, clique na linha que aparece interface principal do Blue Ocean para acessar esse recurso.

> O Blue Ocean é um plugin extremamente popular que trás como proposta repensar a experiência do usuário no CI. Ele basicamente projeta o Pipeline e cria um modelo visual para os estágios, o que reduz a desordem e aumenta a clareza para todos os membros da equipe, vale apena uma lida na documentação do projeto para os interessados em maiores detalhes;

[jenkins.io: What is Blue Ocean?](https://www.jenkins.io/doc/book/blueocean/)

6.4 Ao final da execução a primeira versão do pipeline estará completa:

![alt tag](https://github.com/FabianoCarneiro/pipelines/blob/master/img-src/04.PNG)

---

## 7. Test <a name="7"></a> 

**"Adicionando uma etapa de testes"**

7.1 Utilizando uma versão local ou o editor ntivo do GitHub na internet abra o arquivo jenkinsfile para edição;

7.2 Em seguida copie e cole o bloco abaixo no seu Pipeline imediatamente sob a seção **"agent"** do arquivo Jenkinsfile:

```sh
    environment {
        CI = 'true'
    }
```

7.3 Além disso, adicione o seguinte bloco imediatamente após o estágio Build:

```sh
        stage('Test') {
            steps {
                sh 'chmod +x ./jenkins/scripts/test.sh'                        
                sh './jenkins/scripts/test.sh'
            }
        }
```

7.4 O Conteúdo final do pipeline ficará assim:

```sh
pipeline {
    agent {
        docker {
            image 'node:lts-bullseye-slim' 
            args '-p 3000:3000'
        }
    }
    environment {
        CI = 'true' 
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') { 
            steps {
                sh 'chmod +x ./jenkins/scripts/test.sh'
                sh './jenkins/scripts/test.sh' 
            }
        }
    }
}
```

---

## 8. Delivery <a name="8"></a> 

**"Adicionando uma etapa final para entrega"**

8.1 Abra novamente o arquivo Jenkinsfile para edição e adicione um novo estágio:

```sh
       stage('Deliver') {
            steps {
                sh 'chmod -R +x ./jenkins/scripts'
                sh './jenkins/scripts/deliver.sh'
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh './jenkins/scripts/kill.sh'
            }
        }
```

Esta etapa será responsável por acionar um script de entrega da aplicação em um ambiente temporário.

> O input ao final é naturalmente opicional e adiciona apenas uma etapa de pausa para facilitar a verificação do fluxo antes que o pipeline seja totalmente finalizado e apresenta uma das várias possibilidades para integrações em casos onde o objetivo é o delivery contínuo com alguma decisão manual antes ou depois da entrega;

8.2 Verifique o modelo final do Jenkinsfile:

```sh
pipeline {
    agent {
        docker {
            image 'node:lts-bullseye-slim' 
            args '-p 3000:3000'
        }
    }
    environment { 
        CI = 'true'
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh 'chmod +x ./jenkins/scripts/test.sh'            
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('Deliver') { 
            steps {
                sh 'chmod -R +x ./jenkins/scripts'
                sh './jenkins/scripts/deliver.sh'
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh './jenkins/scripts/kill.sh'
            }
        }
    }
}
```

8.3 Durante a entrega, enquanto não clicarmos na opção Proceed a página ficará acessível na URL do CI na porta 3000;

---

# 9. Multibranch Pipeline <a name="9"></a>

Nesta etapa ampliaremos a configuração do projeto para um modelo com multi branchs no processo de desenvolvimento;

Trabalhar com multiplas brancs permite a implantação de fluxos mais complexos baseados em workflows como [GitFlow](https://nvie.com/posts/a-successful-git-branching-model/) e [GitlabFlow](https://about.gitlab.com/blog/2016/10/25/gitlab-workflow-an-overview/);


## ProjetosMultiBranch <a name="ProjetosMultiBranch"></a>

**Crie o seu pipeline utilizando o Blue Ocean**

9.3 Após a criação das branchs volte a solução de CI, clique em **'Open Blue Ocean'** no painel a esquerda;

9.4 Na sessão **'Welcome to Jenkins'**, no centro da interface do Blue Ocean, clique em **'Create a new Pipeline'** para iniciar o assistente;

9.5 Na opção **'Where do you store your code?'** selecione **'Git'** (Não utilize a opção GitHub);

![alt tag](https://github.com/FabianoCarneiro/pipelines/blob/master/img-src/05.PNG)


9.6 No campo **'Repository URL'**, especifique o repositório remoto na sua conta Git;

9.7 Clique em **'Save'** ao finalizar o processo;

9.8 Por padrão ao criar pipelines utilizando o blue ocean ele automaticamente interpreta que o modelo de implantação pode ser executado com multiplas branchs, isso ocorrerá a partir da existência de novas branchs com arquivos de Jenkinsfile;

![alt tag](https://github.com/FabianoCarneiro/pipelines/blob/master/img-src/05.PNG)

> Se for necessário excluir uma branch do fluxo basta que ela não possua um arquivo Jenkinsfile ou que ele seja removido, utilizando recursos como o arquivo .gitignore, Se ao criar um projeto de Pipeline no Blue Ocean o Jenkinsfile não existir mas for adicionado posteriormente basta utilizar o recurso **'Scan Multibranch Pipeline Now'** na home do projeto; 

---

## 10. MultiBranchPipeline <a name="10"></a> 

**"Adicione etapas de entrega e implantação do seu Pipeline"**

10.1 Usando a sua IDE ou a interface do GitHub edite o seu arquivo Jenkinsfile **na branch master** alterando o stage de execução do pipeline:

```sh
    agent {
        docker {
            image 'node:lts-bullseye-slim' 
            args '-p 3000:3000 -p 5000:5000'
        }
    }
    environment { 
        CI = 'true'
    }
```

> Esta alteração consiste em adicionar a porta de produção do node no fluxo (Internamente é a porta 5000) que será utilizada nas etapas a seguir;

10.2 Substitua o antigo stage Deliver pelo modelo abaixo:

```sh
        stage('Deliver for development') {
            when {
                branch 'development'
            }
            steps {
                sh 'chmod +x ./jenkins/scripts/deliver.sh'
                sh './jenkins/scripts/deliver.sh'
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh 'chmod +x ./jenkins/scripts/kill.sh'
                sh './jenkins/scripts/kill.sh'
            }
        }
```

No modelo de multiplas branchs a alteração permitirá criar um estágio cuja execução é condicionada a commits na branch development;

> Se o valor da condição definida na função when no início do stage corresponder ao nome da branch na qual o Jenkins está executando a compilação, o estágio declarado será executado, essa abordagem permite estruturar tarefas distintas para o ambiente de desenvolvimento e de produção.


10.3 A versão atual do seu arquivo de pipeline possuirá um layout similar ao modelo abaixo:

```sh
pipeline {
    agent {
        docker {
            image 'node:lts-bullseye-slim' 
            args '-p 3000:3000 -p 5000:5000'
        }
    }
    environment { 
        CI = 'true'
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh 'chmod +x ./jenkins/scripts/test.sh'            
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('Deliver for development') {
            when {
                branch 'development'
            }
            steps {
                sh 'chmod +x ./jenkins/scripts/deliver.sh'
                sh './jenkins/scripts/deliver.sh'
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh 'chmod +x ./jenkins/scripts/kill.sh'
                sh './jenkins/scripts/kill.sh'
            }
        }        
    }
}
```

10.4 Agora crie uma nova branch de desenvolvimento para que possamos atuar com multiplas branchs e testar a primeira condição:

Na execução pelo terminal:

```sh
git branch development
```

10.5 Para que o plugin identifique a nova branch volte a página principal do CI, clique sobre o Job criado e no painel no canto esquerdo da tela escolha a opção **'"Scan Multibranch Pipeline Now"'**

É possível confirmar se o CI identificou a nova branch verificando o log no botão abaixo da opção utilizada:


![alt tag](https://github.com/FabianoCarneiro/pipelines/blob/master/img-src/06.PNG)

10.6 Após a alteração volte ao Blue Ocean e execute um novo Job na branch master, para isso clique em **'Branchs'** no canto superior direito para acessar a lista das branchs do seu projeto de Pipeline, existirá um job em execução relativo a branch development.

10.5 Para testar a configuração inicie o job a partir da branch master clicando na opção **'Run'** na linha da branch master no Pipeline;

![alt tag](https://github.com/FabianoCarneiro/pipelines/blob/master/img-src/07.PNG)


---

## 11. MultiBranchDeploy <a name="11"></a> 

**"Etapa final com o deploy da aplicação"**

Será necessário adicionar o novo script para deploy em produção no diretório jenkins/scripts no seu repositório Git:

11.1 Crie o arquivo jenkins/scripts/deployment.sh com o conteúdo abaixo:

```sh
#!/usr/bin/env sh
set -x
npm run build
set +x

echo 'The following "npm" command downloads and installs the npm serve module'
set -x
npm install serve
set +x

echo 'The "serve" command has a trailing ampersand so that the command runs'
echo 'as a background process (i.e. asynchronously). Otherwise, this command'
echo 'can pause running builds of CI/CD applications indefinitely.'

set -x
./node_modules/serve/bin/serve.js -c 0 -s build &
sleep 1
echo $! > .pidfile
set +x

echo 'Now...'
echo 'Visit http://localhost:5000 to see your Node.js/React application in action.'
echo '(This is why you specified the "args ''-p 5000:5000''" parameter)'
```

11.2 Além da alteração anterior adicione um segundo estágio que será utilizado para a entrega em produção, essa configuração será aplicada a branch master e em seguida adicionaremos o mesm oconteúdo nas branchs development e production

```sh
        stage('Deploy for production') {
            when {
                branch 'production'
            }
            steps {
                sh 'chmod +x ./jenkins/scripts/deployment.sh'
                sh './jenkins/scripts/deployment.sh'
                input message: 'Finished with your production version? (Click "Proceed" to continue)'
                sh 'chmod +x ./jenkins/scripts/kill.sh'
                sh './jenkins/scripts/kill.sh'                
            }
        } 
```

11.3 A versão final do seu arquivo de pipeline possuirá um layout similar ao modelo abaixo:

```sh
pipeline {
    agent {
        docker {
            image 'node:lts-bullseye-slim' 
            args '-p 3000:3000 -p 5000:5000'
        }
    }
    environment { 
        CI = 'true'
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test') {
            steps {
                sh 'chmod +x ./jenkins/scripts/test.sh'            
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('Deliver for development') {
            when {
                branch 'development'
            }
            steps {
                sh 'chmod +x ./jenkins/scripts'
                sh './jenkins/scripts/deliver.sh'
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh './jenkins/scripts/kill.sh'
            }
        }
        stage('Deploy for production') {
            when {
                branch 'production'
            }
            steps {
                sh 'chmod +x ./jenkins/scripts/deployment.sh'
                sh './jenkins/scripts/deployment.sh'
                input message: 'Finished with your production version? (Click "Proceed" to continue)'
                sh 'chmod +x ./jenkins/scripts/kill.sh'
                sh './jenkins/scripts/kill.sh'                
            }
        }        
    }
}

```


11.4 Voltando ao repositório Git faça atualize os arquivos Jenkinfile de acordo com as mudanças que executamos no arquivo disponível no repositório principal.

```sh
git checkout development
git merge master
#
git checkout production
git push
```

11.5 Testando o modelo completo, nesta etapa execute o job na branch de desenvolvimento:

> Como você está construindo a aplicação em uma branch diferente, a etapa de instalação do npm exigirá alguns minutos para que o npm faça o download das dependências necessárias para executar o  Node.js e React;

11.6 Ao final do processo acesse a página do CI na porta 3000 para verificar a aplicação Node rodando em modo de desenvolvimento e finalize a aplicação pleo CI clicando na opção "Proceed";

![alt tag](https://github.com/FabianoCarneiro/pipelines/blob/master/img-src/08.PNG)

11.7 Finalmente faça um novo teste iniciando o Job a partir da branch "Production";

---

**Free Software, Hell Yeah!**
